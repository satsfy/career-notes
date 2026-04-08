**Current goal:** Establish a grant-able foothold in bitcoin open source ecossystem
- **Subgoal:** Fix bitcoin node types situation in bitcoin core and corepc
 
---------

1/4/26:
- Diagnose what I think is most needed on corepc
	- [x] Explore the repo to get thae current codebase state

2/4/26:
- Prepare one PR today
	- [x] p1 - Prepare one simple back to action PR 
	- [x] p2 - Read  https://github.com/rust-bitcoin/corepc/pull/505
	- [x] p3 - Read and think about https://github.com/rust-bitcoin/corepc/pull/505
- Research the state of corepc client in 2 pomoros max
	- [x] p1 - Read https://tobin.cc/blog/core-rpc-client/

3/4/26:
- Offline: Sexta Feira da Paixão

4/4/26:
- Adress the corepc CI  to `cargo-rbmt`
	- [x] p1 - Read and implement v1 for request in https://github.com/rust-bitcoin/corepc/issues/538
- Consider codegen
- Do A PR review in corepc today
- Read all open issues and write a repor

5/4/26:
- Offline: Páscoa

6/4/26:
- Wrap up corepc ci migration to cargo-rbmt
	- [x] p1 - fix the tests failing on [my local branch](https://github.com/satsfy/corepc/pull/27)
	- [x] p2 - fix the tests failing on [my local branch](https://github.com/satsfy/corepc/pull/27)
	- [x] p3 - fix the tests failing on [my local branch](https://github.com/satsfy/corepc/pull/27)
	- [x] p4 - fix the tests failing on [my local branch](https://github.com/satsfy/corepc/pull/27)
	- CI finally passing
	- [x] p5 - shaving off the PR for corepc
	- [x] p6 - shaving off the PR for corepc
	- [x] p7 - shaving off the PR for corepc
	- [x] p8 - shaving off the PR for corepc
	- [x] p9 - shaving off the PR for corepc
	- [x] p10 - shaving off the PR for corepc
- Read up and arrive at a final conclusion about corepc client
	- For context, check out tasks/Corepc Client
	- [x] p1 - Read up on what are the existing problems
	- [x] p2 - Research what actually are the choices and tradeoffs
	- [ ] p3 - Check and review the entire repo to determine discussion status
	- [ ] p4 - check downstream implementations
	- [ ] p5 - Write a design proposal
	- [ ] p6 - Write an issue comment
	- [ ] p7 - Consider the follow up codegen
- Do A PR review in corepc today
	- [x] p1 - Reviewed https://github.com/rust-bitcoin/corepc/pull/539
- ~~Push at least one client problem fix based on compare tool to support floresta~~
	- The client situation must be resolved before I can do this
- corepc plans:
	- [x] Read corepc open issues and write a report

7/4/26:
- Wrap up corepc ci migration to cargo-rbmt
	- Maintainers receptive to the idea
	- [x] p1 - shaving off the PR
	- [x] p2 - shaving off the PR
	- [x] p3 - shaving off the PR
	- [x] p4 - shaving off the PR
	- [x] p5 - shaving off the PR
	- [x] p6 - shaving off the PR
	- [x] p7 - shaving off the PR
	- [x] p8 - shaving off the PR
	- This was a painful PR because of the constant CI errors
	- PR CREATED: https://github.com/rust-bitcoin/corepc/pull/540/
- Join the existing corepc production client creation effort
	- [x] p0 - research and gather up all the material for a concrete proposal
- Review a PR in rust-bitcoin to get familiarized with the project
	- [x] p1- Read what I can surrounding the project to understand what it is 

8/4/26
- Workshop Slow blocks
	- [x] Sync signet
	- [x] Receive live slow blocks
	- [x] Inspect reasons for slowness
- infra:
	- [x] Setup my own testnet full node
	- [ ] Integrate notes on personal website
- Join the existing corepc production client creation effort
	- [ ] p1 - figure out what this client should be by reading the BDK and LDK use cases, adn jamil implementation
	- [ ] p2 - find what can I provide and my entry point to aid
	- [ ] p3 - propose codegen client generation from openrpc specs
 - corepc ci migration to cargo-rbmt
	 - [ ] Address reviews
- Review a PR in rust-bitcoin to get familiarized with the project
	- [ ] p1- Read what I can surrounding the project to understand what it is 
	- [ ] p2 - Open codebase and identify general components
	- [ ] p3 - Look through existing issues and development
- bitcoin core openrpc elision elimination PR 
	[ ] Rebase bitcoin core pr https://github.com/bitcoin/bitcoin/pull/34764#issuecomment-4202258634
