# Documentation Standards

## CLAUDE.md

Every project MUST have a `CLAUDE.md` at the root. This is the primary instruction file for AI agents.

**Required sections:**
1. **Critical rules** — absolute constraints (no destructive git, no unauthorized deploys, etc.)
2. **Project overview** — what the project does, in 2-3 sentences
3. **Code layout** — table mapping paths to purposes
4. **Architecture patterns** — key design decisions an agent needs to know
5. **Development commands** — how to build, lint, test, deploy

**Style:**
- Lead with constraints, not descriptions
- Use tables for structured data (paths, commands)
- Keep it under 200 lines — agents have context limits
- Do not duplicate information that's in README.md

## README.md

Every project MUST have a `README.md`. This is for humans.

**Required sections:**
1. What the project is
2. How to set up local development
3. How to deploy
4. Architecture overview (brief)

## Code Comments

- Do NOT add comments that restate what the code does
- DO add comments explaining WHY something is done when not obvious
- Do NOT add JSDoc/docstrings to every function — only for public APIs and non-obvious behavior
- Do NOT add `// removed`, `// deprecated`, or `// TODO` comments for deleted code — just delete it
