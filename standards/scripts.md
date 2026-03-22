# Scripts and Deployment

## Deploy Scripts

**Location**: Always `scripts/deploy.sh` at the project root. Never `infrastructure/terraform/deploy.sh`.

**Must be parameterless.** All configuration is sourced from:
- Environment variables with baked-in defaults
- `platform.yml` manifest (for platform CLI tools)
- Terraform backend config passed via `-backend-config`

Users run: `source .env && scripts/deploy.sh` — no arguments.

**Standard structure:**
```bash
#!/usr/bin/env bash
set -euo pipefail

ROOT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
TF_DIR="${ROOT_DIR}/infrastructure/terraform"

STATE_BUCKET="${STATE_BUCKET:-tfstate-559098897826}"
STATE_REGION="${STATE_REGION:-us-east-1}"

# Build steps here (npm, cargo, etc.)

terraform -chdir="${TF_DIR}" init -reconfigure \
  -backend-config="bucket=${STATE_BUCKET}" \
  -backend-config="region=${STATE_REGION}" \
  -backend-config="use_lockfile=true"

terraform -chdir="${TF_DIR}" apply -auto-approve
```

**Do NOT:**
- Require command-line arguments
- Source `.env` automatically — the user does this manually
- Create state buckets (platform handles this)

## Makefile

Every project should have a `Makefile` with standard targets:

```makefile
.PHONY: lint lint-fix format format-check typecheck test deploy

lint:
	eslint .

lint-fix:
	eslint . --fix

format:
	prettier --write .

format-check:
	prettier --check .

typecheck:
	tsc --noEmit

test:
	vitest run

deploy:
	scripts/deploy.sh
```

Adjust commands per language (cargo clippy for Rust, ruff for Python, etc.) but keep the target names consistent.

## Environment Files

- `.env` — local secrets. Never committed. Always in `.gitignore`.
- `.env.example` — template showing required variables (no actual values). Committed.

## Version Pinning

- `.node-version` — exact Node version (e.g., `24.12.0`). Not `package.json` `engines`.
- `.python-version` — exact Python version.
- `package.json` `"packageManager"` field — exact pnpm version (e.g., `pnpm@10.29.3`).
