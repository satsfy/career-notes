## Generate version-specific `corepc-types` from Bitcoin Core OpenRPC

`corepc-types` is currently hand-maintained across Core versions. That works, but it is labor-intensive, and drift between Core’s actual RPC output and the Rust definitions is only caught once somebody notices.

Codegen would help with the part of the problem that is repetitive and mechanical: version-specific request/response structs, field additions/removals across Core versions, docs copied from Core metadata, faster support for new Core releases, and potentially support for unreleased versions and `master` without patching by hand first.

If Bitcoin Core lands a machine-readable OpenRPC export, `corepc` could generate per-version RPC request/response types from Core’s own source of truth. That would remove a lot of repetitive maintenance in `corepc-types`, make new-version bringup much faster, and reduce drift between Core’s RPC output and the Rust definitions. It looks like a strong path for keeping the shared type layer maintainable with less manual work.

Bitcoin Core issue [#29912](https://github.com/bitcoin/bitcoin/issues/29912) and Bitcoin Core PR [#34683](https://github.com/bitcoin/bitcoin/pull/34683). That PR generates an OpenRPC description from `RPCHelpMan` metadata, from the same structured source of truth that already backs RPC help output. A new RPC `getopenrpcinfo`. There is also a sample Bitcoin Core OpenRPC spec here: [sample Bitcoin Core v30 OpenRPC spec](https://github.com/satsfy/corepc/blob/integrate-codegen-compare/spec_compare/specs/v30_2_0_openrpc.json)

This conversation existed before. I have alluded to this in issue [using automatic rust code generation](https://github.com/rust-bitcoin/corepc/issues/4#issuecomment-3935551647) from OpenRPC specs. It generates Rust type definitions matching `corepc-types` conventions. I have successfully generated types for v24 through v30. I created a script that can use the spec to generate Rust source code resembling corepc. This solution delivers complete specs for recent versions of Bitcoin Core as OpenRPC, generates source code directly from the spec, and spots mismatches between spec and `corepc` implementation easily. Here is the PR draft in my fork: [satsfy#26](https://github.com/satsfy/corepc/pull/26)

What this would enable for `corepc` is more complete and easier to maintain version specific types, including optional fields, for any Core version that has a spec, plus much faster new-version support: generate spec, run codegen, refine if needed. It should also reduce review burden by moving repetitive fixes into the generator instead of hand-editing the generated output.

This is directly useful for projects like `peer-observer`, which currently patches `corepc` because releases do not keep up with Bitcoin Core `master`.


- [peer-observer uses patched corepc](https://github.com/peer-observer/peer-observer/blob/cbcc4c38d2682a113452b3fd9418c75b6c8d088f/shared/Cargo.toml#L25-L32)
- [peer-observer issue comment](https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853)

I think the scope here should stay narrow: improve `corepc-types`, not replace it, and not introduce a separate competing project. I also do not think this needs to imply generated client architecture. It seems plausible to generate the version-specific Rust `std` types first and leave `types::model` and `into_model()` conversions manual, at least initially. That boundary seems like the main friction point to resolve for introducing codegen safely.

A few design questions if this goes anywhere:
1. Should codegen belong inside `corepc`, specifically as a way to improve the current `corepc-types`?
2. Should generated code live directly inside `corepc-types`, or be produced by a generator checked into the repo?
3. How much patching/annotation should be allowed on top of the upstream spec for better Rust-facing types?
4. Should support for Core `master` be an explicit goal, or just a side effect?
5. Should the initial scope be limited to generating version-specific raw/request-response types, while keeping `types::model` and `into_model()` manual?