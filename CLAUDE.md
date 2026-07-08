@AGENTS.md

## Claude-specific

- Adding an ADR: create the next `adr/NNN-title.md`, then update the standard in `standards/` and the enforcing rule in `rules/eslint/` that reference it.
- When editing a standard, check `checklists/REVIEW_CHECKLIST.md` for a matching item and keep the ADR number in sync.
- This is a standards repo, not a product — there is no build or dev server to run; verification is reading the affected `standards/`, `adr/`, and `rules/` files for consistency.
