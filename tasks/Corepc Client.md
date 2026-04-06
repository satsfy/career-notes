## References:

- [Tobin's blog post "Why you should write your own Bitcoin Core RPC client"](https://tobin.cc/blog/core-rpc-client/)
- https://github.com/rust-bitcoin/corepc/issues/507

---

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
