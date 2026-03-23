# Testing Standards

## What to Test

| Layer | What to test | What NOT to test |
|-------|-------------|-----------------|
| **Business logic** | Validation, transformations, state transitions, calculations | Framework glue, constructor assignments |
| **Database operations** | SQL correctness, constraint behavior, query results | ORM/driver internals |
| **API endpoints** | Request/response contracts, auth enforcement, error shapes | HTTP framework routing mechanics |
| **Infrastructure** | Terraform `plan` shows expected changes | Terraform provider behavior |

## When to Use What

| Approach | Use when | Example |
|----------|----------|---------|
| **Unit test** | Pure functions, validation logic, data transformations. No external dependencies. | `validate_report()`, `checksum()`, `parse_config()` |
| **Integration test (testcontainers)** | Code that talks to Postgres. Need real SQL execution, constraint checking, transaction behavior. | Migration apply/rollback, upsert + query, advisory locking |
| **Integration test (mocked traits)** | Code that talks to S3/SSM/external APIs. Real service not needed to verify logic. | File listing with depth filtering, credential store read/write |
| **End-to-end** | Critical user paths through deployed infrastructure. Rare — prefer integration tests. | Smoke test after deploy |

## Test Organization

### TypeScript (vitest)

```
src/
  components/
    UserList.tsx
    UserList.test.tsx      # co-located with source
  data/
    api.ts
    api.test.ts
```

Co-locate test files with source. Use `.test.ts` / `.test.tsx` suffix. Never `.spec.ts`.

### Rust

```
src/
  lib.rs                   # pub mod for testable code
  main.rs                  # thin Lambda handler
  ops.rs                   # business logic
  storage.rs               # trait + impls
tests/
  integration.rs           # testcontainers tests
```

- Unit tests: inline `#[cfg(test)] mod tests` in the source file for small, focused tests
- Integration tests: `tests/` directory for anything that needs external services
- Extract business logic into `lib.rs` so integration tests can import it (binary crates can't be imported)

## Test Naming

Name tests for what they verify, not what they call:

```rust
// Good
test_migrate_applies_in_order
test_migrate_detects_checksum_mismatch
test_rollback_to_target_preserves_earlier_migrations
test_upsert_updates_on_conflict

// Bad
test_migrate
test_rollback
test_function_works
```

## Idempotency

Every test must be independent. No shared state between tests. Each integration test starts with a fresh database (testcontainers spins one per test). Never depend on test execution order.

## What Coverage Looks Like

There is no coverage percentage target. Instead:

- **Every public function** in a lib crate should have at least one test
- **Every error path** that returns a user-facing error should have a test
- **Every SQL operation** (insert, update, delete, query) should be tested against real Postgres
- **Checksum/hash verification** must be tested for both match and mismatch
- **Idempotency claims** must be tested (run twice, assert same result)

## Testcontainers (Rust + Postgres)

Standard setup pattern:

```rust
use testcontainers::runners::AsyncRunner;
use testcontainers_modules::postgres::Postgres;
use tokio_postgres::NoTls;

async fn setup() -> (tokio_postgres::Client, testcontainers::ContainerAsync<Postgres>) {
    let container = Postgres::default().start().await.unwrap();
    let host = container.get_host().await.unwrap();
    let port = container.get_host_port_ipv4(5432).await.unwrap();
    let connstr = format!(
        "host={host} port={port} user=postgres password=postgres dbname=postgres"
    );
    let (client, connection) = tokio_postgres::connect(&connstr, NoTls).await.unwrap();
    tokio::spawn(async move { connection.await.ok(); });
    // init your schema here
    (client, container)
}
```

The `_container` binding must be held for the test's lifetime — dropping it kills the Postgres instance.

## Trait-Based Test Doubles

For external services (S3, SSM), define a trait and provide an in-memory implementation:

```rust
#[async_trait]
pub trait FileStore: Send + Sync {
    async fn list_files(&self, prefix: &str) -> Result<Vec<File>, Error>;
    async fn read_file(&self, key: &str) -> Result<String, Error>;
}

// Production: S3FileStore
// Test: MemoryFileStore (HashMap<String, String>)
```

This avoids mocking libraries and keeps tests deterministic.

## CI

Tests run in GitHub Actions. The `lint-and-test` job gates deploy:

```yaml
- name: Rust tests
  working-directory: apps
  run: cargo test
```

Testcontainers requires Docker — GitHub Actions ubuntu runners have it pre-installed.
