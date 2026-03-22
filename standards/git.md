# Git Standards

## .gitignore

Every project must include at minimum:

```
.env
node_modules/
dist/
.terraform/
*.tfstate
*.tfstate.backup
.terraform.lock.hcl
.idea/
target/
```

## Branching

- `main` is the primary branch
- Feature branches for non-trivial changes
- Branch protection on main where possible (PRs required)

## Commits

- Use imperative mood: "Add feature" not "Added feature"
- First line under 72 characters
- Reference the change purpose, not the mechanics
- AI-assisted commits include `Co-Authored-By: Claude ...` trailer

## Never

- `git reset --hard` without explicit user approval
- `git push --force` to main
- `git checkout --` to discard work without user approval
- Commit `.env`, credentials, or secrets
- Skip pre-commit hooks (`--no-verify`)
