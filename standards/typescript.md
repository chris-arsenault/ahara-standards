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

Import from `@ahara/standards/eslint-rules`. See `rules/eslint/` for the full list.

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
