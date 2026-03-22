# Rust Standards

## Cargo Workspace

Multi-binary projects use a Cargo workspace:
```toml
[workspace]
members = ["binary-a", "binary-b"]
resolver = "2"

[workspace.dependencies]
# pin shared versions here
```

## TLS

Use `rustls` with `ring` crypto provider. Do NOT use `native-tls` / `openssl`.

For AWS RDS connections, embed the RDS CA bundle at build time:
```rust
const RDS_CA_BUNDLE: &[u8] = include_bytes!("../../certs/rds-global-bundle.pem");
```

Download from: `https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem`

Do NOT use `webpki-roots` for RDS — AWS certs are not in the Mozilla trust store.
Do NOT disable certificate verification (`NoVerify`, `danger_*`).

Call `rustls::crypto::ring::default_provider().install_default()` at the top of `main()`.

## Clippy

Copy `rules/rust/clippy.toml` from ahara-standards to project root.

Run with strict warnings:
```bash
cargo clippy -- -D warnings
```

Thresholds:
- `cognitive-complexity-threshold = 10`
- `too-many-arguments-threshold = 6`
- `type-complexity-threshold = 200`

Never `#[allow(dead_code)]` — remove unused code instead.

## Formatting

Copy `rules/rust/rustfmt.toml` from ahara-standards to project root.

```bash
cargo fmt --check
```

## Lambda

- Runtime: `provided.al2023`
- Build: `cargo lambda build --release`
- Handler: `bootstrap`
- Extract business logic into a `lib.rs` for testability

## Testing

Use `testcontainers-rs` for integration tests with real Postgres. Use trait-based abstraction for external services (S3, SSM) to enable in-memory test implementations.
