The culmination of my efforts up until now is this issue comment: https://github.com/rust-bitcoin/corepc/issues/507#issuecomment-4240780139

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

## Introduction

I reviewed the current Rust Bitcoin Core RPC client landscape to understand what downstream projects actually need and whether `corepc` could support a shared production-oriented client implementation. A well-scoped minimal client could serve most downstream users and eliminate a large amount of duplicated effort.

The goal is to understand the current design space, how these clients are actually used and what users need.

The research reviews the Rust Bitcoin Core RPC client landscape through `corepc`, the discontinued `rust-bitcoincore-rpc`, `bdk-bitcoind-client`, Alpen Labs’ `bitcoind-async-client`, `peer-observer`, `ldk-node`, `rust-lightning`, MystenLabs’ `hashi`, `bitcoinresearchkit/brk`, `Floresta`, and `bdk-cli`, along with related issues, PRs, and maintainer notes.

Early clients `rust-bitcoincore-rpc` exposed a monolithic, sync-only, version-unaware interface. `corepc` has version-specific clients and a two-stage type conversion model. Downstream libraries diverge by use case: BDK with a minimal chain-reading surface, Alpen’s `bitcoind-async-client` with production features. Designs are shaped by architectural tradeoffs.

What stood out most from the survey:

- Most clients solve similar problems for their production clients, but there is no way of sharing work.
- The existing `corepc` sync client can be found used in production despite being intended for testing.
- Several downstream projects could benefit from shared typed request/response handling instead of building JSON-RPC calls by hand.
- Across projects, usage concentrates on a small subset of roughly 30 RPC methods, with a much smaller core read path used by nearly everyone. The core read path (8-10 methods) covers BDK, peer-observer, and most chain-reading applications. Adding fee estimation, transaction broadcast, and mempool queries covers LDK and broadcasting applications. Adding wallet operations covers Alpen.
- Alpen Labs’ `bitcoind-async-client` is the most fully developed production-oriented client I found in this survey, especially around async transport, retries, and connection reuse.

## Analysis by project

### rust-bitcoincore-rpc (discontinued predecessor)

The old `rust-bitcoincore-rpc` client was the original community Rust client for Bitcoin Core's RPC. Its architecture:

- Single monolithic `RpcApi` trait containing every RPC method (80+ methods) on one trait. All methods live at the same level regardless of whether they're blockchain queries, wallet operations, or node management.
- Sync only, built on the `jsonrpc` crate.
- No version awareness. A single set of types for all Bitcoin Core versions. This means fields that changed between versions require manual compat hacks (e.g., `get_blockchain_info` has a runtime check for `version < 190000` to handle the `softforks` field format change between v18 and v19).
- Returns rust-bitcoin types directly from deserialization. No intermediate layer. When serde impls in rust-bitcoin have bugs, the client breaks in ways that are hard to diagnose.
- Custom JSON types in `bitcoincore_rpc_json` hand-maintained.

The main problems that led to its discontinuation:

1. Types would silently break when Bitcoin Core changed its JSON output across versions.
2. No way to target a specific Core version.
3. `rust-bitcoin` serde bugs and Core data formatting issues surfaced as opaque deserialization failures with no way to distinguish the source of the problem.

### corepc

The successor to rust-bitcoincore-rpc. Designed with very different goals:

- Version-specific clients: Separate `Client` struct per Bitcoin Core version (v17 through v30). Each version module composes its method set via macros.
- Easy to extend version-by-version: a new version just re-exports the macros it shares and adds new ones.
- Two-layer type system: Version-specific types (`String`, `u64`) and version-agnostic model types (using rust-bitcoin types). `into_model()` function converts between them.
- Sync client is comprehensive (162 methods for v30, covering blockchain, wallet, mining, network, raw transactions, signer, util, zmq).
- WIP PoC Async client (PR #505) has 8 methods.
- Uses `bitreq` for HTTP.
- No retry logic, no connection pooling, no error classification. Just a testing client.

### bdk-bitcoind-client (BDK)

BDK devs started it to be used in production.

- Focused and minimal: 10 methods. Only blockchain read operations: `get_block`, `get_best_block_hash`, `get_block_count`, `get_block_hash`, `get_block_filter`, `get_block_header`, `get_block_header_verbose`, `get_block_verbose`, `get_raw_mempool`, `get_raw_transaction`.
- No wallet methods at all. BDK handles wallet operations internally.
- Sync only, built on the `jsonrpc` crate from corepc.
- Uses corepc-types with the `into_model()` pattern for safe type conversion.
- Feature-gated version support: `#[cfg(feature = "28_0")]` switches between v28 and v30 types.
- Pluggable transport via `with_transport()` method accepting any `jsonrpc::Transport` impl.
- Discussion of sans-io model (PR #27): would make the library only depend on corepc-types, with the user handling sync/async transport entirely. This is the leanest possible approach, the client becomes just request/response construction, no HTTP at all.
- Clean, readable code. No macros. Each method is written out explicitly.

What BDK needs from Bitcoin Core: block data, block headers, block filters (BIP-158), mempool state, raw transactions.

### bitcoind-async-client (Alpen Labs)

The most production-oriented client in the ecosystem.

- Fully async (tokio), using `bitreq` directly.
- Trait-based architecture with four clean role-based traits:
  - `Reader`: blockchain queries (17 methods: block headers, blocks, mempool, transactions, UTXO lookups, network info)
  - `Broadcaster`: sending transactions (3 methods: send_raw_transaction, test_mempool_accept, submit_package)
  - `Wallet`: watch-only wallet operations (11 methods: addresses, transactions, UTXOs, PSBTs, raw transaction creation)
  - `Signer`: signing operations (5 methods: sign_raw_transaction_with_wallet, get_xpriv, import_descriptors, wallet_process_psbt, psbt_bump_fee)
- Uses corepc-types with `into_model()` pattern.
- Built-in retry logic with configurable max retries, retry interval, and error classification. The `is_error_recoverable` method classifies bitreq errors as retryable (connection/IO/parsing) vs non-retryable (redirect/size/HTTPS).
- Connection pooling via `BitreqClient` with configurable capacity.
- Custom input types for complex RPC args: `CreateRawTransactionInput`, `PreviousTransactionOutput`, `ImportDescriptorInput`, `WalletCreateFundedPsbtOptions`, `ListUnspentQueryOptions`, `PsbtBumpFeeOptions`, `SighashType`.
- Feature-gated version support: `#[cfg(feature = "29_0")]`.
- Request ID tracking via atomic counter.
- Includes production concerns: retries, connection reuse, error classification, and a clean trait hierarchy.

### LDK (lightning-block-sync)

LDK has its own `RpcClient` in the `lightning-block-sync` crate.

- Uses raw JSON-RPC directly, building requests by hand with `serde_json`.
- Also supports Bitcoin Core's REST API alongside RPC.
- Focused entirely on block sync and chain monitoring, the `BlockSource` trait, `ChainPoller`, `SpvClient`, header cache.
- LDK's integration with Bitcoin Core is deeply tied to its own `Listen` trait pattern and fee estimation pipeline.
- The `BitcoindClient` wraps either RPC or REST, and handles chain sync, wallet polling, fee rate caching.
- `base64` auth directly, no shared auth abstraction.

LDK's needs: block headers, blocks, fee estimation, transaction broadcasting. Seems like the architecture is tightly coupled to LDK's internal patterns.

### mempool-radar

- Accesses mempool-specific methods: `get_raw_transaction`, `get_mempool_ancestors`, `get_mempool_descendants`, `get_mempool_entry`.
- Application using the testing-only client because nothing better exists.
- Uses `spawn_blocking` to bridge the sync client into async context.

### peer-observer (B10C)

- Uses corepc directly, with patches because corepc hasn't released recently and doesn't support changes in Bitcoin Core `master`.
- Runs Bitcoin Core release candidates and `master`, would benefit from [codegen](https://github.com/rust-bitcoin/corepc/issues/4#issuecomment-3935551647) for unreleased versions.
- Recently moved to version-agnostic (model) types where possible.
- Uses getpeerinfo, getnetworkinfo, and other network/peer-related RPCs.

### MystenLabs/hashi, bitcoinresearchkit/brk, Floresta

These projects wrap corepc or the old client in thin application-specific layers. They typically need only a handful of RPC methods and care most about type correctness. Floresta notably has its own RPC types for its own JSON-RPC server that mirrors Bitcoin Core's API.

---------
---
---
---
---

# Survey
## Observations

- It looks like all clients want to use the `into_model()` pattern deserializing version-specific raw type, convert to version-agnostic.

### What RPC methods do projects mostly use?

There's a clear clustering of usage:

Core read path (everyone needs these):

- `getbestblockhash`
- `getblock` (verbosity 0 and 1)
- `getblockcount`
- `getblockhash`
- `getblockheader` (raw and verbose)
- `getrawmempool`
- `getrawtransaction`
- `getblockchaininfo`

Extended read path (most need some of these):

- `getblockfilter` (BIP-158, needed by BDK)
- `gettxout`
- `getmempoolinfo`
- `getmempoolentry`
- `getmempoolancestors` / `getmempooldescendants`
- `estimatesmartfee`
- `getnetworkinfo`

Transaction submission (needed by active participants):

- `sendrawtransaction`
- `testmempoolaccept`
- `submitpackage`

Wallet operations (needed by wallet-integrated projects):

- `getnewaddress`
- `gettransaction`
- `listtransactions` / `listunspent`
- `createrawtransaction`
- `signrawtransactionwithwallet`
- `walletcreatefundedpsbt` / `walletprocesspsbt`
- `psbtbumpfee`
- `importdescriptors`
- `getaddressinfo`

Namespace most used:

- `wallet`
- `blockchain`
- `control`
- `network`

Testing/node management (only integration tests):

- `generatetoaddress` / `generate`
- `createwallet` / `loadwallet` / `unloadwallet`
- `stop`
- `invalidateblock` / `reconsiderblock`
- All the `hidden` RPCs

### The version problem

In practice, most projects only care about recent versions (v28+). peer-observer is the exception, needing unreleased versions too.

1. Version-specific client (corepc approach): Pick a version, get exact types. Safe but rigid.
2. Version-agnostic model types (what most consumers actually use): Use `into_model()` types that paper over version differences with `Option` fields. The user does not chose a version.
3. Latest-version only (BDK/Alpen approach): Target a specific recent version via feature flag, ignore older versions.

### The trait architecture question

Alpen's client splits into `Reader`, `Broadcaster`, `Wallet`, `Signer`. A chain-reading application (BDK) only needs `Reader`. A broadcasting application needs `Reader + Broadcaster`. A wallet application needs all four. Having separate traits means you can mock just the reader without stubbing 80 wallet methods.

### The transport question

There are three used approaches:

1. Hard-coded transport (Alpen): uses `bitreq` directly, builds HTTP requests internally.
2. Pluggable transport trait (BDK): `Transport` trait lets you swap HTTP implementations.
3. Sans-io (proposed in BDK PR #27): no transport at all. Client just produces request bytes and consumes response bytes.

## Async

Every production project is async, but BDK's sync client works only because they wrap it in `spawn_blocking`. mempool-radar does the same. The Alpen client is the only one that's natively async.

### Error handling

- corepc: Minimal. Errors are `jsonrpc::Error` or version-specific model conversion errors.
- BDK: Explicit error enum with variants for each failure mode (hex decode, model conversion, JSON, IO, etc.).
- Alpen: Most sophisticated. Classifies errors as retryable vs non-retryable. Has `is_tx_not_found()`, `is_block_not_found()`, `is_missing_or_invalid_input()` helper methods. Built-in retry loop.

</details>

---

## References

- [Tobin's blog post "Why you should write your own Bitcoin Core RPC client"](https://tobin.cc/blog/core-rpc-client/)
- https://github.com/peer-observer/peer-observer/issues/199#issuecomment-4046291853
- https://github.com/rust-bitcoin/corepc/issues/507
- https://github.com/rust-bitcoin/corepc/pull/505
- https://github.com/rust-bitcoin/corepc/pull/506
- https://github.com/satsfy/corepc/pull/26
- https://github.com/bitcoin/bitcoin/issues/29912
- https://github.com/bitcoin/bitcoin/pull/34683
- https://github.com/alpenlabs/bitcoind-async-client
- https://github.com/bitcoindevkit/bdk-bitcoind-client
- https://docs.rs/bdk_bitcoind_rpc/latest/src/bdk_bitcoind_rpc/lib.rs.html#1-476
- https://github.com/lightningdevkit/ldk-node/blob/fe692f3e397b311489aff8b2dc00761f7d10a69a/src/chain/bitcoind.rs#L690
- https://github.com/lightningdevkit/rust-lightning/blob/86dc928b915d00a17d3c95819101300d68a04b26/lightning-block-sync/src/rpc.rs#L84
- https://github.com/MystenLabs/hashi/blob/60e18ed3e32dd6b8d294d4223a36b55d3e24f2be/crates/hashi-monitor/src/rpc/btc.rs#L13
- https://github.com/bitcoinresearchkit/brk/blob/f022f62ccebe7f0dd544ca96a9cfd04ddb6daf9d/crates/brk_rpc/src/backend/corepc.rs#L11
- https://github.com/peer-observer/peer-observer/blob/c3114c868cd2c033678afb7bd2fc8abe817300b0/extractors/rpc/src/lib.rs#L3
- https://github.com/getfloresta/Floresta/blob/master/crates/floresta-node/src/json_rpc/blockchain.rs
- https://github.com/moisesPompilio/Floresta/blob/ebdd0859d81f683a1ede51cb120a06b4711f204e/test-rust/tests/common/bitcoind.rs
- https://github.com/moisesPompilio/Floresta/blob/ebdd0859d81f683a1ede51cb120a06b4711f204e/crates/floresta-rpc/src/rpc_types.rs
- https://github.com/bitcoindevkit/bdk-cli/blob/1dc41d0d9e1ff4feb6e9e8690cb1ea3380bb08e1/src/handlers.rs#L213
- https://docs.rs/bdk_bitcoind_rpc/latest/src/bdk_bitcoind_rpc/lib.rs.html#1-476
