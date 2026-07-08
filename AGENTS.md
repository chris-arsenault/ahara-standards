# Agent Guide

Standards library for the ahara umbrella — conventions, ESLint rules, Rust lint config, patterns, and ADRs consumed by every ahara project.

## Read first

| Topic | Link |
| ---- | ---- |
| Repository overview | [README.md](README.md) |
| Standards index | [standards/README.md](standards/README.md) |
| Architecture decisions | [adr/](adr/) |
| Review checklist | [checklists/REVIEW_CHECKLIST.md](checklists/REVIEW_CHECKLIST.md) |

## Critical rules

- Every standard is backed by an ADR. To change a standard, add or supersede an ADR in [adr/](adr/) — do not mutate an accepted ADR in place.
- Keep the three surfaces in sync: a standard in `standards/` names the ADR that justifies it, the pattern in `patterns/` that demonstrates it, and the rule in `rules/eslint/` that enforces it.
- New ADRs take the next sequential number (`NNN-title.md`, zero-padded to three digits) and are added to [subscriptions.json](subscriptions.json) awareness — projects opt out per ADR, never per standard.
- When you add or edit an ESLint rule, export it from [rules/eslint/index.js](rules/eslint/index.js) and register the artifact in [library.json](library.json) with its `source_project` and checksum.
- This repo holds conventions and rule code only. Application and infrastructure code live in their own repos; do not add them here.

## Code map

| Path | Purpose |
| ---- | ---- |
| `standards/` | Markdown convention docs, indexed by `standards/README.md` |
| `patterns/` | Reusable implementation patterns referenced by the standards |
| `adr/` | Numbered Architecture Decision Records — the only home for trade-offs |
| `rules/eslint/` | Custom ESLint rule sources, exported via `index.js` |
| `rules/rust/` | Shared `clippy.toml` and `rustfmt.toml` |
| `checklists/` | Drift-prevention review checklist keyed to ADRs |
| `library.json` | Registry of shared artifacts with source-project provenance |
| `subscriptions.json` | Per-project ADR opt-outs |

## Commands

| Command | Purpose |
| ---- | ---- |
| `npm install` | Install workspace dependencies |
| Import `@ahara/standards/eslint-rules` | Consume the ESLint rules from a project's flat config |
