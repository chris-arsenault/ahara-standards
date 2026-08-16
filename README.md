# Ahara Standards

Shared conventions, lint rules, and architectural decisions for every project under the ahara umbrella. AI agents and humans working in any ahara project follow what is recorded here.

MIT licensed. Published as `@ahara/standards`.

## Contents

| Path | Purpose |
|------|---------|
| [standards/](standards/README.md) | Language and project conventions — TypeScript, Rust, Terraform, observability, git, testing, documentation, project structure, scripts |
| [patterns/](patterns/) | Reusable implementation patterns — store access, API access, async error handling, CSS, view scaffold, expand/collapse, empty states, backend logging |
| [adr/](adr/) | Architecture Decision Records — the canonical home for every standards trade-off |
| [rules/eslint/](rules/eslint/) | Custom ESLint rules that enforce the standards, exported from `@ahara/standards/eslint-rules` |
| [rules/rust/](rules/rust/) | Shared `clippy.toml` and `rustfmt.toml` |
| [checklists/](checklists/REVIEW_CHECKLIST.md) | Drift-prevention review checklist, keyed to ADRs |
| [library.json](library.json) | Registry of shared artifacts with source-project provenance |
| [subscriptions.json](subscriptions.json) | Per-project ADR opt-outs |

## Consuming the standards

Read [standards/README.md](standards/README.md) first — it indexes every convention. Each standard links to the ADRs that justify it and, where applicable, the patterns and lint rules that enforce it.

The ESLint rules are exported from the package. A consuming project imports them into its flat config:

```js
import * as aharaRules from "@ahara/standards/eslint-rules";

export default [
  {
    plugins: { ahara: { rules: aharaRules } },
    rules: {
      "ahara/no-direct-fetch": "error",
      "ahara/no-inline-styles": "error",
      // ...
    },
  },
];
```

Rust projects reference the shared [`clippy.toml`](rules/rust/clippy.toml) and [`rustfmt.toml`](rules/rust/rustfmt.toml).

A project opts out of specific ADRs by listing them under its entry in [subscriptions.json](subscriptions.json).

## Documentation

| Topic | Link |
| ---- | ---- |
| Standards index | [standards/README.md](standards/README.md) |
| Architecture decisions | [adr/](adr/) |
| Review checklist | [checklists/REVIEW_CHECKLIST.md](checklists/REVIEW_CHECKLIST.md) |
| Agent guide | [AGENTS.md](AGENTS.md) |

## License

MIT — see [LICENSE](LICENSE).
