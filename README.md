# pubky-app-specs

[![crates.io](https://img.shields.io/crates/v/pubky-app-specs)](https://crates.io/crates/pubky-app-specs)
[![docs.rs](https://img.shields.io/docsrs/pubky-app-specs)](https://docs.rs/pubky-app-specs)
[![npm](https://img.shields.io/npm/v/pubky-app-specs)](https://www.npmjs.com/package/pubky-app-specs)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Rust types, sanitization, and validation for [Pubky.app](https://pubky.app) data models. Use this crate to build JSON that matches what [Pubky indexers](https://github.com/pubky/pubky-nexus) expect.

> ⚠️ **Warning: Rapid Development Phase**  
> This specification is in an **early development phase** and is evolving quickly. Expect frequent changes and updates as the system matures. Consider this a **v0 draft**.
>
> When we reach the first stable, long-term support version of the schemas, paths will adopt the format: `pubky.app/v1/` to indicate compatibility and stability

## Installation

**Rust** ([crates.io](https://crates.io/crates/pubky-app-specs)):

```bash
cargo add pubky-app-specs
```

**JavaScript / TypeScript** ([npm](https://www.npmjs.com/package/pubky-app-specs)): see [`pkg/README.md`](https://github.com/pubky/pubky-app-specs/blob/main/pkg/README.md).

## Rust quick start

```rust
use pubky_app_specs::{
    traits::{HasPath, Validatable},
    PubkyAppUser,
};
use serde_json::to_vec;

// Create a user profile
let user = PubkyAppUser::new("Alice".into(), None, None, None, None);
let path = PubkyAppUser::create_path(); // /pub/pubky.app/profile.json
let json = to_vec(&user).unwrap();

// Parse and validate JSON from storage
let profile = PubkyAppUser::try_from(&json, "").unwrap();
```

For a full homeserver flow, see [`examples/create_user.rs`](https://github.com/pubky/pubky-app-specs/blob/main/examples/create_user.rs).

## Why use this crate

- **Validation consistency** — same sanitization and validation rules as Pubky indexers.
- **Auto IDs and paths** — generates IDs, paths, and URLs according to Pubky standards.
- **Single source of truth** — Rust models drive native apps, WASM bindings, and this spec.

## Features

| Feature   | Purpose                                                        |
| --------- | -------------------------------------------------------------- |
| `sdk`     | `PubkyId` conversions to and from the `pubky` SDK keys (default) |
| `openapi` | OpenAPI schemas via `utoipa`                                   |

```toml
pubky-app-specs = { version = "0.8", features = ["openapi"] }
```

`sdk` is the only feature that pulls in the `pubky` SDK. It backs `PubkyId::to_public_key`, the
`From<PublicKey>` and `From<Keypair>` conversions, and the Ed25519 curve-point check that native
`PubkyId` construction performs. A consumer pinned to another `pubky` release turns it off:

```toml
pubky-app-specs = { version = "0.8", default-features = false, features = ["openapi"] }
```

Without it the crate has no `pubky` dependency and `PubkyId` validates the z32 format only, which
is what the wasm32 build has always done. A consumer that needs a real public key, to verify a
signature or to address a homeserver, does the curve-point check itself.

- **MSRV:** 1.89 (see `rust-version` in `Cargo.toml`)
- **API docs:** [docs.rs/pubky-app-specs](https://docs.rs/pubky-app-specs)

## Models

| Rust type           | Purpose                                  |
| ------------------- | ---------------------------------------- |
| `PubkyAppUser`      | User profile information                 |
| `PubkyAppFile`      | Uploaded file metadata                   |
| `PubkyAppPost`      | Posts, replies, embeds, and collections  |
| `PubkyAppTag`       | Tags applied to Pubky URIs               |
| `PubkyAppBookmark`  | Bookmarks for Pubky URIs                 |
| `PubkyAppFollow`    | Follow relationships                     |
| `PubkyAppFeed`      | Feed configurations                      |
| `PubkyAppMute`      | Muted users                              |
| `PubkyAppBlob`      | Raw binary file data                     |
| `PubkyAppLastRead`  | Last-read notification timestamp         |

## Specification

See the [full data model specification](https://github.com/pubky/pubky-app-specs/blob/main/SPEC.md) for URI layout, examples, and validation rules.

## License

MIT
