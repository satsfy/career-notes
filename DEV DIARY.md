**Current goal:** Establish a grant-able foothold in bitcoin open source ecossystem
- **Subgoal:** Fix bitcoin node clients and types situation in rust bitcoin, corepc, bitcoin core 
 
---------

1/4/26:
- earlier work on:
	- https://github.com/bitcoin/bitcoin/pull/34799
	- https://github.com/bitcoin/bitcoin/pull/34683
	- https://github.com/bitcoin/bitcoin/pull/34764
	- https://github.com/bitcoin/bitcoin/pull/34867
	- https://github.com/stealth-bitcoin/stealth/
	- 8 corepc PRs: https://github.com/rust-bitcoin/corepc/pulls?q=is%3Apr%20author%3Asatsfy%20
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
- New task: Wrap up corepc ci migration to cargo-rbmt
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
- Do A PR review in corepc today
	- [x] p1 - Reviewed https://github.com/rust-bitcoin/corepc/pull/539
- ~~Push at least one client problem fix based on compare tool to support floresta~~
	- The client situation must be resolved before I can do this
- corepc plans:
	- [x] Read corepc open issues and write a report

7/4/26:
- Running task: Wrap up corepc ci migration to cargo-rbmt
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
- Join the existing corepc production client creation effort **(RESEARCH -> THINK -> PROPOSE)**
	- [x] p1 - Use Github Search to find [uses of corepc on real projects](https://github.com/search?q=corepc_types&type=code)
	- [x] p2 - Collect all client implementations for BDK, Alpen, LDK, floresta, MystenLabs/hashi, benthecarman/mempool-radar, peer-observer, jamil implementation, sunsetted rust-bitcoin/rust-bitcoincore-rpc, corepc client sync (obviously)...
	- [x] p3 - pool information based on found implementations about what truly the most necessary features for a client
	- [x] p4 - Write a research summary about this
	- [x] p5 - Turn this research into an issue comment
	- [x] p6 - Write a proposal client that is useful enough for a certain portions of the users
	- [x] p7 - propose codegen client generation from openrpc specs
		- Should I create a `bitcoin-types-codegen` package? 
	- [x] p8 - think strategy:
		- So I have 1. research about the rust bitcoin rpc client ecosystem 2. a proposal for the definitive version of this client for corepc 3. codegen proposal. I'm considering how to present these...
		- I have decided to separate codegen proposal into a separate issue for later
	- Postponed - I need more mind to think about this, will leave for later
 - Running task: corepc ci migration to cargo-rbmt
	 - [x] p1-  Address reviews

9/4/26:
 - Running task: corepc ci migration to cargo-rbmt
	 - [x] p1 -  Address reviews
	 - [x] p2 -  Address reviews
	 - [x] p3 -  Address reviews
	 - [x] p4 -  Address reviews
	 - [x] p5 -  Address reviews
- bitcoin core openrpc elision elimination PR 
	- [x] Rebase bitcoin core pr https://github.com/bitcoin/bitcoin/pull/34764#issuecomment-4202258634
- Review a PR in rust-bitcoin to get familiarized with the project
	- Strategically, the best PRs to review right now is rust-bitcoin because I dont really know the project. I need to acquire that context.
	- [Tobin proposed issue](https://github.com/rust-bitcoin/rust-bitcoin/issues/5969#issuecomment-4209664725) tagging me but someone already implemented it
	- [x] p1 - Review the PR for issue tobin proposed - https://github.com/rust-bitcoin/rust-bitcoin/pull/5975#pullrequestreview-4085256871
	- [x] p2 - Read what I can surrounding the project to understand what it is 
	- [x] p3 - Open codebase and identify general components

10/4/26:
- Education:
	- [x] Read BIPS and optech
	- [x] Read about rust-bitcoin
- Join the existing corepc production client creation effort **(RESEARCH -> THINK -> PROPOSE)**
	- [x] p1 - Re-reading carefully and cutting out noise
	- [x] p2 - Slicing off comments
	- I created a github gist [Survey of Rust Bitcoin Core RPC Client Landscape](https://gist.github.com/satsfy/0e2a3b36a9a00c9439087122dcfb6350) (this could become a blog post)
	- [x] p3 - Preparing the final proposal
	- [x] p4 - Preparing the final proposal
	- [x] p5 - Preparing the final proposal
	- [x] p6 - Preparing the final proposal
	- [x] p7 - Preparing the final proposal
	- At this point in time, it has become a well defensible design proposal

11/4/26:
- Offline

12/4/26:
- Offline: Domingo

13/4/26:
- Prepare for upcoming rust-bitcoin meeting
	- [x] p1 - Read up on rust-bitcoin repos ecossytem
	- [x] p2 - Read up on rust-bitcoin repos ecossytem
	- [x] p3 - Read up on rust-bitcoin repos ecossytem
	- [x] p4 - Read up on rust-bitcoin repos ecossytem
- corepc production client creation design proposal
	- [x] p1 - Preparing the final proposal
	- [x] p2 - Preparing the final proposal
	- [x] p3 - Preparing the final proposal
	- "its the integration of asybc into jsonrpc crate that is the hard part, and is dependent of other developers". additionally "corepc is not high priority"
	- Posted: https://github.com/rust-bitcoin/corepc/issues/507#issuecomment-4240780139
- ci rbmt migration
	- merged: https://github.com/rust-bitcoin/corepc/pull/540
- rust-bitcoin:
	- [x] p1 - Exploring repos and issues

14/4/26:
- Pick a project for today:
	- [x] p1 - Read email and figure out what is happening in rust-bitcoin
	- [x] p2 - Understand release candidates situation and project context
	- [x] p3 - Understand release candidates situation and project context
	- We are working to prepare a 1.0 release in six months.
	- We are 1.0-ing crates in this order: rust-bitcoin's consensus_encoding, units and primitives; then rust-miniscript; then corepc; then psbt.
	- We don't care about 2.0 at this point in time.
	- Tobin suggested do a rust bitcoin 0.33 beta rebase into rust-miniscript, but apoelstra suggested against new dev doing it because its too complex.
		- MAYBE I could do a portion of it.
	- I will stick to these issues here, labled 1.0: https://github.com/rust-bitcoin/rust-bitcoin/issues?q=is%3Aissue%20state%3Aopen%20label%3A1.0%2CC-units%2CC-primitives%2CC-consensus_encoding
	- Tobin personally suggested this other issue: https://github.com/rust-bitcoin/rust-bech32/issues/252
	- [x] p4 - explore open issues and triage one for solving today. 
	- Priority for today: easy wins
	- Today I pick this issue to work on: https://github.com/rust-bitcoin/rust-bech32/issues/252
- New task: [Fix all mutation test problems in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/issues/252)
	- [x] p1 - reproduce locally, find similar issues for reference like [1](https://github.com/rust-bitcoin/rust-bitcoin/pull/5193), [2](https://github.com/rust-bitcoin/rust-bitcoin/pull/5126), [3](https://github.com/rust-bitcoin/rust-bitcoin/pull/5000), [4](https://github.com/rust-bitcoin/rust-bitcoin/pull/5335/changes)
	- [x] p2 - explore how these are usually solve (how the unit tests are made)
	- [x] p3 - familiarize with lib
	- [x] p4 - create a manual test to fix 
	- [x] p5 - keep cutting down the list to 0
	- FTR there are hundreds of mutations test failing. Apparently this repo had mutation tests before, but then someone switched to `cargo-mutants` and hundreds of new errors appeared.
	- [x] p6 - keep cutting down the list to 0
	- The previous PR is going great, while a mutation test runs or fixes are being developed i have the bandwidth for another small task
- New task: [finish this cargo ci migration to rbmt in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/pull/234)
	- [x] p1 - Integrating the corepc changes to cut out line of code from current solution
	- [x] p2 - get all ci to green

15/4/26:
- Running task: [finish this cargo ci migration to rbmt in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/pull/234)
	- [x] p1 - Integrating the corepc changes to cut out line of code from current solution
	- [x] p2 - get all ci to green
	- [x] p3 - Verify that functionally does what its supposed to
	- [x] p4 - Cut down modification to bare mininmum
	- [x] p5 - Polishing
	- [x] p6 - Write up PR 
	- Open PR: https://github.com/rust-bitcoin/rust-bech32/pull/254
	- A follow up PR, if this is accepted, could be https://github.com/satsfy/rust-bech32/pull/3
- Running task: [Fix all mutation test problems in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/issues/252)
	- [x] p1 - keep cutting down the list to 0
	- [x] p2 - Carefully review test changes, reducing modifications to minimum
	- [x] p3 - I think I found a bug in bech32, investigating it carefully...
	- [x] p4 - Reducing loose exceptions in mutants rules
- New task: fix a off by one error detected in rust-bech32
	- [x] p1 - Isolating the chagne from mutation tests PR into its own PR.
	- [x] p2 - polish, commit history, write comment and title
	- Open the PR https://github.com/rust-bitcoin/rust-bech32/pull/253
- New task: [refactor encoder/decoder module in rust bitcoin](https://github.com/rust-bitcoin/rust-bitcoin/pull/5902)
	- [x] p1 - Learn more about the PR
	- [x] p2 - Pick tobin commits, format, consider how to implement them
	- [x] p3 - polish final version and 
	- [x] p4 - verify the change I did is really what is supposed to have happened

16/4/26:
- Running task: [Fix all mutation test problems in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/issues/252)
	- [x] p5 - Reviewing the 75 new tests and 1 modified test
	- [x] p6 - Cutting down the amount of tests
	- [x] p7 - Cutting down the amount of tests
	- [x] p8 - Running mutation tests and polishing the PR
	- [x] p9 - Running mutation tests and polishing the PR
	- [x] p10 - Running mutation tests and polishing the PR
	- [x] p11 - Running mutation tests and polishing the PR
	- [x] p12 - Running mutation tests and polishing the PR
	- [x] p13 - Running mutation tests and polishing the PR
	- [x] p14 - Running mutation tests and polishing the PR
	- [x] p15 - Verifying every mutant exclusion
	- [x] p16 - Verifying every mutant exclusion
	- [x] p17 - Polishing final version renaming what needs to change
	- Open PR: https://github.com/rust-bitcoin/rust-bech32/pull/255
- Running task: [finish this cargo ci migration to rbmt in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/pull/234)
	- [x] p7 - Address PR reviews

17/4/26:
- Running task: fix a off by one error detected in rust-bech32
	- Merged PR: https://github.com/rust-bitcoin/rust-bech32/pull/253#event-24608128810
- Writing codegen proposal
	- [x] p1 - Reserach and write a simple proposal to send via email before issue
	- [x] p2 - Looking for objections
- Review 4 PRs in corepc:
	- Reviewed: https://github.com/rust-bitcoin/corepc/pull/551
	- [x] p1 - Choose valuable target and read context
	- [x] p2 - Test the code
	- [x] p3 - Try to find any design flaws
	- Reviewed: https://github.com/rust-bitcoin/corepc/pull/558#pullrequestreview-4130370466
	- [x] p4 - Reviewing...
    - Reviewed: https://github.com/rust-bitcoin/corepc/pull/557
	- [x] p5 - Reviewing...
    - Reviewing: https://github.com/rust-bitcoin/corepc/pull/556
- Consider client design
	- [x] p1 - Read and thought about tradeoffs
	- Wrote issue comment https://github.com/rust-bitcoin/corepc/issues/555#issuecomment-4269391437
- New Task: https://github.com/rust-bitcoin/corepc/issues/58 (tip from Tobin)
	- [x] p1 - Acquire context and start
	- [x] p2 - identify and import bitcoin core tests
- New Task: https://github.com/rust-bitcoin/corepc/issues/553#issuecomment-4256506099
	- Someone did it first, I'll review it next
- New Review: https://github.com/rust-bitcoin/corepc/pull/556
	- [x] p1 - Acquire context and inspect the code
	- This one proved beyond my knowledge.
- New Task: https://github.com/rust-bitcoin/corepc/issues/552#issuecomment-4256454586
	- [x] p1 - Acquire context and start
	- [x] p2 - Learn more about features in rust  

18/4/26:
- Running task: [Fix all mutation test problems in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/issues/252)
	- [x] p1 - Address reviews
	- [x] p2 -  Address reviews
	- [x] p3 - Address reviews


19/4/26:
- Offline: sunday
- Merged: https://github.com/rust-bitcoin/rust-bech32/pull/255

20/4/26:
- Education:
	- [x] Read posts in Delving Bitcoin
- Running task: [finish this cargo ci migration to rbmt in rust-bech32](https://github.com/rust-bitcoin/rust-bech32/pull/234)
	- [x] p8 - Address review by grabbing latest version of rbmt
	- [x] p9 - Fixing MSRV version, evaluating reviewer suggestion
- Running Task: https://github.com/rust-bitcoin/corepc/issues/58
	- [x] p3 - inspect claude attempt to convert all these tests for approach
	- [ ] p4 - Investigating 7 test failures in corepc
- Running Task: https://github.com/rust-bitcoin/corepc/issues/552#issuecomment-4256454586
	- [x] p1 - Considering implementation
	- [x] p2 - Attempt first version
	- [x] p3 - Evaluate if we are on the right track
- Running task: [refactor encoder/decoder module in rust bitcoin](https://github.com/rust-bitcoin/rust-bitcoin/pull/5902)
	- [x] p5 - verify the change I did is really what is supposed to have happened
	- [x] p6 - write up the PR name + desc
	- [x] p7 - commit hist + write up the PR name + desc
- New task: https://git.rust-bitcoin.org/rust-bitcoin/rust-psbt/issues/66
	- [x] p1 - Reproduce the issue
	- [x] p2 - Investigate the codebase
	- [x] p3 - Work on solving each of the 62 prerelease problems

21/4/26:
- Merged PR: https://github.com/rust-bitcoin/rust-bech32/pull/254
- Running Task: https://github.com/rust-bitcoin/corepc/issues/58
	- [x] p3 - inspect claude attempt to convert all these tests for approach
	- [x] p4 - Increasing test surface so that more error appear
	- [x] p5 - Section off the changes into specific PRs
	- [x] p6 - Improving and testing my changes
	- [x] p7 - Section off the changes into specific PRs
- Running Task: https://github.com/rust-bitcoin/corepc/issues/552#issuecomment-4256454586
	- [x] p4 - Fix CI with acceptance criteria
	- [x] p5 - Polishing and cutting LoC from PR, Slicing off the formatting changes, 
- Running task: [refactor encoder/decoder module in rust bitcoin](https://github.com/rust-bitcoin/rust-bitcoin/pull/5902)
	- [x] p8 - triple check this change
	- Open PR: https://github.com/rust-bitcoin/rust-bitcoin/pull/6052
- Running task: https://git.rust-bitcoin.org/rust-bitcoin/rust-psbt/issues/66
	- [x] p4 - Work on solving each of the 62 prerelease problems
- Prepare for maintainer meeting:
	- [x] p1 - Write talking points:
	- [x] p3 - Writing codegen proposal and present to maintainer in person.
	- We got approval for codegen task!
- Codegen:
	-  [x] p1 - Patch up codegen repo for https://github.com/satsfy/rust-btc-codegen 

22/4/26:
- Running Task: https://github.com/rust-bitcoin/rust-bech32/pull/254#issuecomment-4289786340
	- [x] p1 - Migrate nightly update to rbmt 
	- [x] p2 - Unify embedded nightly version
	- [x] p3 - Test the solution
	- Open PR: https://github.com/rust-bitcoin/rust-bech32
- Running Task: https://github.com/rust-bitcoin/corepc/issues/552#issuecomment-4256454586
	- [x] p8 - Verify solution carefully
	- [x] p9 - Read about electrsd more carefully
	- [x] p10 - test electrsd
	- [x] p11 - test electrsd
	- [x] p12 - Review tobins work to ensure final shape
	- this is not being so easy
	- [x] p12 - Verify solution carefully
	- [x] p13- Cut down dramatically
	- [x] p14- Make CI pass
	- [x] p15 - Verify solution carefully

23/4/26:
- Merged PR: https://github.com/rust-bitcoin/rust-bitcoin/pull/6052
- Merged PR: https://github.com/rust-bitcoin/rust-bech32/pull/259
- Running Task: https://github.com/rust-bitcoin/corepc/issues/58
	- [x] p16 - Polishing final version
	- [x] p17 - Working on test feature flags
	- [x] p18 - Addressing testing errors
	- [x] p19 - Inspecting each version of electrsd 
	- [x] p20 - Inspecting each version of electrsd 
	- [x] p21 - Working on test feature flags
	- [x] p22- Iterating
	- [x] p23 - Iterating
	- [x] p24 - Iterating
	- [x] p25 - Iterating
	- [x] p26 - Iterating
	- Opened PR: https://github.com/rust-bitcoin/corepc/pull/570
- [x] Bitdevs

24/4/26:
- Running Task: https://github.com/rust-bitcoin/corepc/issues/58
	- [x] p27 - Redoing the PR based on feedack
	- [x] p28 - Redoing the PR based on feedack
	- [x] p29 - Redoing the PR based on feedack
	- [x] p30 - Redoing the PR based on feedack
	- [x] p31 - Redoing the PR based on feedack
	- [x] p32 - Redoing the PR based on feedack
	- [x] p33 - Redoing the PR based on feedack
	- [x] p34 - Redoing the PR based on feedack
	- [x] p35 - Redoing the PR based on feedack
	- [x] p36 - Redoing the PR based on feedack
	- [x] p37 - Redoing the PR based on feedack
25/4/26:
- New task: Read up issues in rust-bitcoin
	- [x] p1 - Read context for N issues...
	- [x] p2 - Read context for N issues...
	- [x] p3 - Read context for N issues...
	- [x] p4 - Read context for N issues...
	- So I have selected about 11 valuable issues to tackle for version 1.0 of rust-bitcoin

26/4/26:
- Offline: domingo

27/4/26:
- Running task: https://git.rust-bitcoin.org/rust-bitcoin/rust-psbt/issues/66
	- [x] p5 - learning PSBT repo
	- [x] p6 - Fixating knowledge about PSBTs
	- [x] p7 - Fixating knowledge about PSBTs
	- [x] p8 - Fixating knowledge about PSBTs
	- [x] p9- Fixating knowledge about PSBTs
	- [x] p10 - Reading PSBT repo
	- [ ] p11 - Reading PSBT repo
	- [ ] p12 - Reading PSBT repo
	- [ ] p - organized the changes into PRs
- Client: https://github.com/rust-bitcoin/corepc/pull/558#event-24779196166
	- I'm supposed to be the client guy now
	- [x] p1 - Research design ideas and solution developed by rust-bitcoin
	- [x] p2 - Collecting ideas for a production client
	- [ ] Put production client considerations (like validations) in issue 
	- Codegen: https://github.com/satsfy/rust-btc-codegen
- Running Task: https://github.com/rust-bitcoin/corepc/issues/58
	- [ ] p3 - Mapping out the included test
- New task: do a rust-bitcoin PR:
	- [ ] p1 - Select the best PR among my selection

28/7/26:
- Write report about this month's work:
	- [ ] p1 - copy template and fill up history