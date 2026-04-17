## Generate version-specific `corepc-types` from Bitcoin Core OpenRPC

`corepc-types` is currently hand-maintained across Core versions. That works, but it is labor-intensive, and drift between Core’s actual RPC output and the Rust definitions is only caught once somebody notices.

Codegen would help with the part of the problem that is repetitive and mechanical: version-specific request/response structs, field additions/removals across Core versions, docs copied from Core metadata, faster support for new Core releases and support for unreleased versions and `master` without patching by hand first.

If Bitcoin Core lands a machine-readable OpenRPC export, `corepc` could generate per-version RPC request/response types from Core’s own source of truth. That would remove a lot of repetitive maintenance in `corepc-types`, make new-version bringup much faster, and reduce drift between Core’s RPC output and the Rust definitions. It looks like a strong path for keeping the production client shared type layer maintainable once we have one.

Bitcoin Core issue [#29912](https://github.com/bitcoin/bitcoin/issues/29912) and Bitcoin Core PR [#34683](https://github.com/bitcoin/bitcoin/pull/34683). That PR generates an OpenRPC description from `RPCHelpMan` metadata, from the same structured source of truth that already backs RPC help output. A new RPC `getopenrpcinfo`. There is also a sample Bitcoin Core OpenRPC spec here: [sample Bitcoin Core v30 OpenRPC spec](https://github.com/satsfy/corepc/blob/integrate-codegen-compare/spec_compare/specs/v30_2_0_openrpc.json)

This conversation existed before. I have alluded to this in issue [using automatic rust code generation](https://github.com/rust-bitcoin/corepc/issues/4#issuecomment-3935551647) from OpenRPC specs. It generates Rust type definitions matching `corepc-types` conventions. I have successfully generated types for v24 through v30. I created a script that can use the spec to generate Rust source code resembling corepc. This solution delivers a complete specs for most recent version of Bitcoin Core as OpenRPC, generate source code directly from spec and spot mismatches between spec and corepc implementation easily. Here is the PR draft in my fork: [satsfy#26](https://github.com/satsfy/corepc/pull/26)

What this would enable for `corepc` is correctness and completeness for types (even optional fields) for any Core version that has a spec, fast new-version support (generate spec, run codegen, refine if needed). Would reduced review burden and enable support for unreleased versions and `master`. 

This is directly useful for projects like `peer-observer`, which currently patches `corepc` because releases do not keep up with Bitcoin Core `master`. Furthermore, from a survey I did about the Rust Bitcoin Core RPC client landscape, it seems like more Rust applications could exist leveraging corepc if there was a complete client.

- [peer-observer uses patched corepc](https://github.com/peer-observer/peer-observer/blob/cbcc4c38d2682a113452b3fd9418c75b6c8d088f/shared/Cargo.toml#L25-L32)
- [peer-observer issue comment](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853)

Allowing client support to bitcoin core live master branch. `peer-observer`, for example, [finds value in the idea](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853).

Connecting the client to `into_model()` conversions seem to be friction point to be resolved for introducing codegen.

A few design questions if this goes anywhere:
1. Codegen should belong to corepc?
2. Should generated code live directly inside `corepc-types`, or be produced by a generator checked into the repo?
3. How much patching/annotation should be allowed on top of the upstream spec for better Rust-facing types?
4. Should support for Core `master` be an explicit goal, or just a side effect?
