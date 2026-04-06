## References:
- [Tobin's blog post "Why you should write your own Bitcoin Core RPC client"](https://tobin.cc/blog/core-rpc-client/)

-----
## Problem:

Some downstream users would like to have an easy to use rust client for a bitcoin node (peer-observer, floresta...). They are using the existing client, made for integration test only, in production because it serves their needs well enough.

From my research, a production `corepc` client does not exist yet because, publishing one would force the project to standardize a large set of choices that are not actually universal—supported Core versions, RPC surface, optional arguments, transport, sync vs async model, dependency footprint, typing strategy, and failure semantics—while also taking on the maintenance and reliability burden of keeping those choices safe for downstream production users; so the blocker is not merely implementation effort, but the absence of a clearly bounded, broadly acceptable policy surface for what “the client” should be.

However, some corepc clients would gladly accept any sensible working solution for a client because otherwise they must re-implement on their own. This increases their dev time, limit what they can accomplish (to minimize the amount of client code they have to manage) and greatly raises risks of errors. Furthermore, any new rust bitcoin projects suffer additional friction to get started because there is not easy to use client available. 

---

## Proposal

Create an opinionated bitcoin facing client that rust projects can easily integrate without re-implementing anything. All the "default" choices could be made simply based on what .

Open questions: 
- Should the client be sync or async? 
	- For reference, [this PR from @jamillambert](https://github.com/rust-bitcoin/corepc/pull/505) is trying to implement async bdk client

MVP Features:
- Support all the methods of bitcoin core
- Support all previous versions corepc can

Follow up features:
- Codegen client from Bitcoin Core OpenRPC spec
	- This would enable support for current bitcoin core master branch types, which is a use case proposed [here in peer-observer](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853).
- Support optional arguments

Some default choices I mapped: