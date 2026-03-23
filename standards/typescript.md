# TypeScript / React Standards

## Package Manager

Use **pnpm** for all TypeScript/JavaScript projects. Pin the version in `package.json`:

```json
{ "packageManager": "pnpm@10.29.3" }
```

## ESLint

Use ESLint v9+ flat config (`eslint.config.js`).

**Required plugins:**
- `@eslint/js` — base recommended
- `typescript-eslint` — TS type-aware rules
- `eslint-plugin-react` — React rules
- `eslint-plugin-react-hooks` — hooks rules
- `eslint-plugin-react-refresh` — HMR safety
- `eslint-plugin-react-perf` — performance anti-patterns
- `eslint-plugin-jsx-a11y` — accessibility
- `eslint-plugin-sonarjs` — semantic code quality
- `eslint-config-prettier` — disable formatting rules (must be last)

**Required custom rules from `@ahara/standards`:**

```bash
npm install -D github:chris-arsenault/ahara-standards
```

Import from `@ahara/standards/eslint-rules`. Each rule and its rationale:

| Rule | Severity | Rationale |
|------|----------|-----------|
| `max-jsx-props` | warn | Max 12 props per element. High prop counts mean the component is doing too much — refactor to Parameter Object pattern. |
| `no-inline-styles` | error | Inline style objects create new references on every render and bypass CSS architecture. Use CSS classes. |
| `no-direct-fetch` | error | Raw `fetch()` scatters auth handling and error formatting. All HTTP goes through a shared API wrapper. |
| `no-direct-store-import` | warn | Views should not couple to store internals. Access state through hooks or props. |
| `no-escape-hatches` | error | `getInternal*` methods expose implementation details. Config fallback defaults mask missing required values. Deprecated stubs should be deleted, not kept for compatibility. |
| `no-manual-async-state` | warn | Manual `loading`/`error`/`data` state is error-prone and inconsistent. Use shared async hooks. |
| `no-manual-view-header` | warn | View headers should use a shared component for consistent layout and behavior. |
| `no-manual-expand-state` | warn | Expand/collapse state should use a shared hook for consistent keyboard and accessibility support. |
| `no-raw-undefined-union` | warn | `\| undefined` and `?:` in property signatures hide WHY a value can be absent. Named aliases like `Optional<T>` force explicit intent. Catches LLM-added defensive optionality that should be reviewed. |
| `no-non-vitest-testing` | error | Vitest is the standard test runner. No jest, mocha, or other frameworks. |
| `no-js-file-extension` | error | TypeScript projects use `.ts`/`.tsx`. JavaScript files indicate missing migration or tooling issues. |

**Mandatory complexity limits (not negotiable):**
```js
complexity: ["error", 10],
"max-lines": ["error", { max: 400, skipBlankLines: true, skipComments: true }],
"max-lines-per-function": ["error", { max: 75, skipBlankLines: true, skipComments: true }],
"max-depth": ["warn", 4],
```

## Prettier

Standard config (`.prettierrc`):
```json
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "printWidth": 100,
  "trailingComma": "es5"
}
```

## TypeScript Config

Standard `tsconfig.json` base:
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "noEmit": true,
    "isolatedModules": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "jsx": "react-jsx"
  }
}
```

## Testing

Use **vitest** for all tests. The `no-non-vitest-testing` custom rule enforces this.

## File Extensions

Use `.ts` / `.tsx` only. The `no-js-file-extension` custom rule enforces this.
