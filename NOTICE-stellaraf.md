# Modification notice — Stellar (stellaraf/vrl)

This repository is a fork of [`vectordotdev/vrl`](https://github.com/vectordotdev/vrl),
licensed under the Mozilla Public License 2.0. The upstream `LICENSE` file is unmodified and
governs this fork in full; nothing here is relicensed, and no copyright notice has been
removed or altered.

MPL-2.0 is file-level copyleft: the obligation attaches to the Covered Software files that
are modified, and those files remain available under MPL-2.0 here. This file records which
they are, so a recipient of any Larger Work built from this fork can identify the changed
Source Code Form without diffing releases.

## Branch `threatlens/community-id-optional`

Base: upstream tag `v0.35.0` (commit `d7058406eecb3ead5f438dcf380f0373cac56ca2`).
Author: Stellar (stellaraf), 2026-09-05.

Modified files, both of which remain under MPL-2.0:

| File | Change |
|---|---|
| `Cargo.toml` | `dep:community-id` moves out of `stdlib-base` into a new `community-id` feature; `stdlib` enables that feature. |
| `src/stdlib/mod.rs` | `mod community_id;` and the `CommunityID` registration are gated on `#[cfg(feature = "community-id")]`. |

### What the change does, and what it deliberately does not do

The Community ID flow hash is specified as base64 of a SHA-1 digest, so the `community-id`
crate depends on RustCrypto `sha1` unconditionally. Because `stdlib-base` required
`community-id`, `sha1` was in the dependency graph of every build of the standard library,
including builds that never call `community_id`. An embedder operating under a policy that
bans SHA-1 *implementations* by crate name — a FIPS 140-3 boundary, in Stellar's case — had
no feature combination that removed it.

After this change, `--no-default-features --features stdlib-base` resolves no `sha1`, and
`community_id` is available by enabling `community-id` alongside it.

The change is behaviour-preserving for every existing consumer. `stdlib` enables
`community-id`, and `default` enables `stdlib`, so `default`, `stdlib`, and any feature set
reaching either compile and register `community_id` exactly as before. No function signature,
no runtime behaviour, and no other feature changes.

The `community-id` feature is named for the dependency rather than for a family (`enable_*_functions`)
because it gates one function, not a family.

### Upstream

This change is intended for upstream. It is kept on a branch here rather than merged to this
fork's `main` so that the fork tracks upstream cleanly, and Stellar consumes it by pinned
revision.
