# Layer 2 - Static analysis

Deterministic checks the agent cannot argue with. Everything here runs inside `scripts/check.sh`.

## 1. Pick tools that actually support the stack

- Check the linter's supported language versions before installing. Example: `typescript-eslint`
  declares a peer range on `typescript`; a newer TypeScript major may not be supported. If not,
  use a linter that does not depend on the TypeScript compiler API (for example oxlint with JS
  plugins) and say so in the plan.
- Pin exact versions. Record why each tool was chosen in `docs/QUALITY.md`.
- Python: ruff + mypy/pyright (strict). Go: `go vet`, staticcheck, golangci-lint. Rust: clippy with
  `-D warnings`. JVM: Error Prone / detekt.

## 2. Type checking

Strict mode everywhere (`strict: true`, mypy `--strict`, etc.). Add a `typecheck` script per package.

**Type-check the tests too.** A `tsconfig.json` often includes only `src/`, and tests run through
`tsx` or `node --import tsx`, which strip types without checking them. Test fakes then drift from the
interfaces they stand in for (a fake without a new method still passes) and "✓ typecheck" covers
less than it says. When the project's config leaves tests out, add a `tsconfig.test.json` and check it
too (TypeScript recipe in `stack-recipes.md`). If the tests already have type errors, record a
baseline (errors per file, which may only go down, core rule 6) instead of leaving tests unchecked.

## 3. Boundary rules

Turn the Layer 1 table into rules. In JS/TS use `no-restricted-imports` with per-directory
overrides (or dependency-cruiser); in Python use import-linter; in Go use package layout plus
`depguard`. Typical rules:

- UI may not import DB/cloud SDKs or model SDKs.
- Client services and domain may not import the UI framework.
- Frontend may not import backend implementation (and vice versa).
- Domain may not import I/O modules (type-only imports allowed).
- Feature code may not import the model SDK; only `adapters/ai` may.

Each restriction has a message that names the right place to put the code.

## 4. Project lint plugin

Keep project rules in the repo (`lint/project-plugin.mjs`, start from `templates/lint/`; `.mjs` loads
as ESM without a `"type": "module"` package.json). Run linters from the repo root: oxlint rejects
paths containing `..`. Run oxlint through `lint/oxlint.mjs` (`node lint/oxlint.mjs -c .oxlintrc.json src`,
or `runOxlint([...])` from a test): it starts oxlint with `node` itself, because spawning `npx` or a
`.cmd` shim without a shell fails on Windows. In tests, never spawn `python3` by name either: on
Windows it is often the Microsoft Store stub; try `python3`, `python`, then `py -3`. Non-JS stacks: write rules for the stack's own linter or as a small AST
script (see the Python recipe), with the same fixtures-plus-test discipline.
A rule is worth writing when a mistake (a) happened at least once, (b) can be detected from source
text or AST, and (c) has a clear correct alternative.

### Anatomy of a good rule

```
what:      UI text "Save settings" is not translated.
how:       Use {t('Save settings')} (const t = useT() from src/i18n) and add the English entry.
exception: Language names that must not be translated: add a lang attribute to the element.
```

- One rule, one concern. No false positives on the compliant fixture.
- Provide the sanctioned exception path, or agents will invent one.
- Fixtures: `lint/fixtures/violations.*` and `lint/fixtures/compliant.*` plus a test that asserts
  the exact rule IDs and lines reported. Use a separate config for fixtures so normal lint ignores them.

### Candidate rules (pick what fits; see `stack-recipes.md`)

- User-visible text must go through i18n; `t()` must not run at module load.
- Font sizes/colors only from design tokens.
- Modals/overlays only through the shared component.
- No timing patches: `setTimeout(fn, 0)`, `requestAnimationFrame` used to "wait for" state.
- No secrets-shaped strings or server env names in client code.
- No empty `catch`; no floating promises.
- Server code logs only through the logger; no user content in logs.
- Model output must pass a schema before use.
- No explanatory "excuse" comments (`// TODO hack`, `// temporary workaround`). Prefer banning
  comments that justify a workaround over banning all comments; tighten later if useful.

## 5. Tests

- Unit tests for every domain function.
- Property-based tests (fast-check, Hypothesis, `testing/quick`) for money, quota, permission and
  time logic. Examples: balance never negative; refund never exceeds charge; usage resets exactly
  once per cycle; any user can read only their own records.
- Database security rules (Firestore rules, Postgres RLS) tested against an emulator/test DB:
  other users' data unreadable, client writes rejected where the server owns writes.

## 6. Ratchet

Run every new rule on the whole repo first. If existing violations are few, fix them now. If many,
scope the rule to new paths or changed files, log the backlog in `quality/rule-candidates.md`, and
widen the scope as the backlog shrinks. Never leave a rule half-on without a recorded plan.
