## References:

- [Tobin's blog post "Why you should write your own Bitcoin Core RPC client"](https://tobin.cc/blog/core-rpc-client/)
- https://github.com/rust-bitcoin/corepc/issues/507

---

I surveyed the current Rust Bitcoin Core RPC client landscape to see what should a corepc client shared based look like, that leaves room for project specific layers on top.

Full survey here: [Survey of Rust Bitcoin Core RPC Client Landscape](https://gist.github.com/satsfy/0e2a3b36a9a00c9439087122dcfb6350).

I looked at `corepc`, the discontinued `rust-bitcoincore-rpc`, `bdk-bitcoind-client`, Alpen Labs' `bitcoind-async-client`, `peer-observer`, `ldk-node`, `rust-lightning`, MystenLabs' `hashi`, `bitcoinresearchkit/brk`, `getfloresta/floresta`, and `bdk-cli`.

## What I found

- The existing `corepc` sync client is used in production even though it was intended for testing, which suggests there is real demand for a reusable client layer.
- Most downstreams are solving very similar production problems today, but with little shared implementation.
- Across projects, usage clusters around a relatively small set of roughly 30 RPC methods. A blockchain read path covers BDK, peer-observer. Adding fee estimation, transaction broadcast, and mempool queries covers LDK and similar broadcasting use cases. Wallet RPCs add Alpen-like needs.
- `bitcoind-async-client` is the clearest production reference point I found. It shows the value of role-based traits, async transport, retries, and connection reuse.

## Proposal

I think corepc should have a bounded shared base client with explicit tradeoffs and non-goals. It is worth owning because the reusable middle layer is real and the cost of not owning it is high. Downstreams rebuild the same pieces, or fall back to the testing client, which increases duplicated effort and bug surface.

However, as soon as `corepc` owns a shared base, considerations such as transport, failure classification, or review surface come in. The 5 projects, with differing needs, patching one client into a pile of junk @tcharding said is real. So it must be minimal. 

A plausible solution could be:

- Async support, addressing integration to jsonrpc. (it can start out from PoC PR #505).
- Interface: the lowest friction solution is using the existing namespace-oriented api: `blockchain`, `network`, `mining`. Another possiblity is a role-based split like `reader` / `broadcaster` / `wallet` / `signer` such as `bitcoind-async-client`.
- Maintain testing client existing version split.
- Structured errors. Distinguish transport / auth / HTTP status / malformed JSON-RPC / Core RPC application error / version-method mismatch / type decode failure / model conversion failure. Every error should indicate whether it is retryable: connection/IO vs non-retryable: auth/protocol.
- Provide bitreq as optional client transport. Transport should not be left entirely downstream, as sans io solution suggests. The core should be runtime-agnostic, but it should still ship with one optional default HTTP transport. Otherwise every downstream ends up rebuilding it, which is reason enough for people to use the testing client in production. Projects with additional requirements swap in their own transport.

What corepc would own: typed request/response handling, a common method surface, and the operational pieces that multiple downstreams are already rebuilding anyway. It should prevent project-specific policy starts moving upstream.

## Conclusion

Feedback on scope would be especially useful: the concurrent splits (role based split and bitcoin-cli namespace based split), transport boundary. In particular, I would like to ask @tcharding what this layer should explicitly refuse to own so the scope stays maintainable.

If that general shape seems reasonable, I am happy to start a PoC on top of `#505` and use that to make the tradeoffs concrete.

# OLD, IGNORE:
--------
## Problem:

Projects such as peer-observer, floresta, BDK, Alpen, and LDK started or planned their own clients because the shared one was not positioned as a supported production client. Some are using the existing corepc client, made for integration test only, because it serves their needs well enough.

From my research, a production `corepc` client does not exist yet because publishing one would force the project to standardize a large set of choices that are not actually universal—supported Core versions, RPC surface, optional arguments, transport, sync vs async model, dependency footprint, typing strategy, and failure semantics—while also taking on the maintenance and reliability burden of keeping those choices safe for downstream production users. The blocker is therefore not merely implementation effort, but the absence of a clearly bounded, broadly acceptable policy surface for what “the client” should be. `corepc` does not want the crate to become the place where ecosystem-wide product decisions and support obligations accumulate.

However, some corepc clients would gladly accept any sensible working solution for a client because otherwise they must re-implement on their own. There is a substantial reusable middle layer that does not require solving every question perfectly. The current situation increases downstream development time, limits what projects can accomplish to minimize client-maintenance burden, and raises the risk of errors. Furthermore, new Rust Bitcoin projects face additional friction because there is no easy-to-use client available.

---

## Proposal

Create one sensible default client for a large middle of users, not a universal abstraction for every possible use case. Rust projects should be able to integrate it without re-implementing the client layer themselves. The default choices can be driven by what downstream users find valuable, starting from the existing integration-test client, which is already adopted by some projects.

Open questions:
- Should the client be sync or async? If both are needed, should one be primary and the other best-effort?
	- For reference, [this PR from @jamillambert](https://github.com/rust-bitcoin/corepc/pull/505) is trying to implement async bdk client
	- peer-observer and floresta are using sync implementation

MVP Features:
- Support all the methods of bitcoin core
- Support recent Bitcoin Core versions explicitly (say, down to 27), while treating older versions as best-effort

Follow up features:
- Codegen client from Bitcoin Core OpenRPC spec
	- This would enable support for current bitcoin core master branch types, which is a use case proposed [here in peer-observer](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853).
- Support optional arguments

Some sensible choices I propose:
- Treat older versions as best-effort: preserve partial compatibility where feasible, but avoid blocking progress on full legacy support.
- Keep a raw JSON-RPC escape hatch: let users call unsupported or newly added methods without waiting for a library release.
- Prefer debuggability over ergonomics: make it easy to tell whether failures come from Core output, deserialization, or type conversion.
- Choose one primary execution model: avoid doubling maintenance by trying to make sync and async equally first-class from day one.
- Keep transport and auth minimal: provide sensible defaults for HTTP JSON-RPC without baking in unnecessary policy or dependencies.
- Make version support explicit: document which Core versions and RPC behaviors are guaranteed instead of hiding compatibility drift behind a generic API.
- Design for production failure modes: expose structured errors around transport, RPC failure, unsupported versions, decode errors, and conversion errors.

Sensible follow up choices I propose:
- Use codegen for schemas, not for all client logic: generate types and method metadata, but keep transport, errors, and public API hand-curated.


---------

# issue comment
- https://github.com/rust-bitcoin/corepc/issues/507

A number of projects have have to re-implement solutions because there is no shared client. Some are also using the current `corepc-client` anyway because, despite its stated goals, it is still the furthest-along implementation in terms of coverage and test infrastructure. Effort gets duplicated and more surface area for subtle bugs in transport, typing, version handling, and error conversion. It also raises the barrier for newer Rust Bitcoin projects that need “a sensible client that works” more than they need a perfectly custom one.

I understand that creating a corepc production client would implicitly standardize choices that are not universal:
- which Core versions are supported
- transport/auth assumptions
- typing/conversion strategy
- failure semantics and compatibility guarantees...

I think the most promising path here to create a production-usable bounded default client with explicit tradeoffs for the large middle of downstream users. A useful shared base to avoid re-implementations and bugs in downstream clients. 

So the problem is really one of **policy surface**. If that surface is left open-ended, the client risks becoming the place where every downstream requirement accumulates.

Instead of aiming for a universal abstraction, define one sensible default client for a large middle of users:
- explicit about its tradeoffs
- production-usable within a documented scope
- easy to extend or fork outside that scope
- conservative about maintenance burden
## Possible shape of an MVP

I think an MVP could look something like:
- Async client
- structured errors that distinguish transport / RPC / decode / conversion / version mismatch
- a raw JSON-RPC escape hatch for unsupported or newly added methods

We may reduce maintenance burden by using code generation on the method layer from upstream OpenRPC schema information. It would make it possible to support to bitcoin core master branch coverage.

Future work may involve supporting optional arguments.
An open question is: what is the right target? A BDK-like client?

----- 

### Alternative comment

Keep the sync integration-test client as-is, define a bounded async/base client around the overlapping BDK/LDK use case first, and make the tradeoffs explicit rather than aiming for one client for all production software.

A number of projects have had to re-implement solutions because there is no shared client. Some are also using the current `corepc-client` anyway because, despite its stated goals, it is still the furthest-along implementation in terms of coverage and test infrastructure.

I do not think the conclusion from that is that `corepc` should try to provide a single client for all production software. The concern raised above about design tradeoffs accumulating into a pile of junk is real, and the existing sync client also has a legitimate role as something that can be patched freely for integration testing. What seems worth exploring instead is a bounded shared base with explicit scope and guarantees.

The thing that looks most promising to me is not “turn the current client into a universal production client”, but “define one default async client with a narrow, documented target, and keep the current sync client as the integration-test client”. That seems compatible with the direction already mentioned here: start from BDK, see whether the LDK overlap is favorable, and extend only where the overlap is real and does not introduce feature-gating or API churn. Alpen/PDK may well fit into that same overlap if their requirements are mostly the same.

That would still give downstream users something useful: a shared base that removes duplicated effort and reduces bugs in transport, typing, version handling, and error conversion, without pretending to solve every downstream requirement inside one crate.

## Possible shape of an MVP

I think an MVP could look something like:

- keep the existing sync `corepc-client` for integration testing and do not repurpose it
- add a separate async client intended as a bounded shared base
- scope it first around the intersection of BDK/LDK requirements, and only add Alpen/PDK requirements where they fit cleanly
- support a documented recent Core version window, with explicit guarantees inside that window
- expose structured errors that distinguish transport / RPC / decode / conversion / version mismatch
- keep a raw JSON-RPC escape hatch for unsupported or newly added methods
- keep transport/auth assumptions minimal and explicit

For coverage, I think there are really two layers:

- a method layer that aims for broad coverage over the supported Core versions
- a smaller ergonomic layer for the methods the initial downstream users actually need

That split matters because it avoids blocking on API polish for every method while still giving downstreams a shared base they can build on. It also seems like the right place to use code generation: generate the method/schema layer from upstream OpenRPC information where possible, but keep the public client API and error surface hand-curated. That should reduce churn, make recent-version coverage more realistic, and avoid turning the generated output itself into the public design.

I would also treat optional arguments as something that can be added incrementally on top of that base, rather than a prerequisite for getting the shared client shape right.

So the concrete question I would ask is not “should `corepc` provide the production client”, but rather: is there interest in maintaining a bounded async base client in this repo with explicit tradeoffs, while preserving the current sync integration-test client and avoiding any claim that one client should fit all production software?