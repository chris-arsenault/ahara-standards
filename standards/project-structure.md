# Project Structure

## Directory Layout

```
<project>/
  apps/                    # application code (multi-app projects)
    <app-name>/            # one directory per app
  backend/                 # or use this for single-backend projects
  frontend/                # or use this for single-frontend projects
  packages/                # shared libraries (monorepo only)
  infrastructure/
    terraform/             # all Terraform code
  scripts/
    deploy.sh              # primary deploy entry point
  db/
    migrations/            # SQL migration files (if using platform RDS)
  docs/                    # project documentation
  eslint-rules/            # custom ESLint rules (if any)
  platform.yml             # platform manifest (if integrated)
  CLAUDE.md                # AI agent instructions
  README.md
  Makefile                 # local dev commands
  .gitignore
  .env                     # local secrets (never committed)
```

## Naming Conventions

| What | Convention | Examples |
|------|-----------|---------|
| Directories | kebab-case | `shared-components`, `eslint-rules`, `world-schema` |
| TypeScript files | camelCase | `pipelineStore.ts`, `useAuth.ts` |
| Python files | snake_case | `stage_runner.py`, `bedrock_client.py` |
| Rust files | snake_case | `main.rs`, `storage.rs` |
| Shell scripts | kebab-case | `deploy.sh`, `build-lambda.sh` |
| Terraform files | kebab-case | `ci-ingest.tf`, `db-migrate.tf` |
| React components | PascalCase files | `UserEditor.tsx`, `Dashboard.tsx` |

## Required Files

Every project MUST have:
- `CLAUDE.md` — AI agent instructions
- `README.md` — human-readable project overview
- `.gitignore` — with `.env` at minimum
- `scripts/deploy.sh` — parameterless deploy (if deployable)
