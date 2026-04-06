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
- Read all open issues and write a report

5/4/26:
- Offline: Páscoa

6/4/26:
- Wrap up corepc ci migration to cargo-rbmt
	- [ ] p1 - fix the tests failing on [my local branch](https://github.com/satsfy/corepc/pull/27)
- Push at least one client problem fix based on compare tool to support floresta
- Read up and arrive at a final conclusion about corepc client
	- Because Tobin has expressed he doesn't want corepc to be a production client [here](https://tobin.cc/blog/core-rpc-client/) explicitly, but [B10C](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4033595043), floresta and countless other projects want it, and yet [another dev is implementing it](https://github.com/rust-bitcoin/corepc/pull/505) for corepc 
	- [ ] p1 - Read up on that is missing
	- [ ] p2 - Write a design proposal (mostly to guide you in making a future contribution)
	- [ ] p3 - Consider codegen solution for it
- Do A PR review in corepc today
- Read all open issues and write a report
- Integrate notes on personal website

