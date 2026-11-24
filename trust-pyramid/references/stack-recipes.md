# Stack recipes

Starting points. Always confirm versions against the installed toolchain.

## TypeScript + React (Vite) + Node.js + Firebase + OpenAI-compatible APIs

**Layout:** `web/` (React), `server/` (Cloud Functions / Cloud Run), `shared/` (types).
Inside each: `domain/` pure, `services/` or `adapters/` for I/O.

**Tools:** `tsc --noEmit` strict; oxlint (with a JS plugin for project rules) or ESLint +
typescript-eslint if it supports the installed TypeScript major; `node --test` or Vitest; fast-check;
`@firebase/rules-unit-testing` against the emulator; Playwright for the verify CLI.

**Type-check the tests.** If `tsconfig.json` does not include the test files, add
`tsconfig.test.json` and run `tsc -p tsconfig.test.json` in the typecheck check:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": { "noEmit": true, "rootDir": ".", "allowImportingTsExtensions": true },
  "include": ["src", "test", "tests"]
}
```

`rootDir: "."` (or no `rootDir`) keeps the tests inside it; `allowImportingTsExtensions` is needed
when tests import `.ts` paths. Count the existing errors first; if there are any, ratchet them.

**First rules:**
- UI cannot import `firebase/*` or `firebase-admin`; only `web/src/services` talks to Firebase.
- No OpenAI-compatible base URLs, API keys or model SDK imports outside `server/src/adapters/ai`.
- The AI adapter sets timeout, max tokens and retry cap, and records token usage per call. Retries
  share one total deadline for user-facing requests; timeouts are not retried there.
- Model JSON (summaries, structured translations) must pass a schema (zod/valibot) before use;
  failures go through one fallback path, never rendered raw.
- Transcripts, prompts and model outputs never appear in logs.
- UI text through `t()`; `t()` never at module load (multilingual apps).
- Firestore: clients read only their own documents; all writes go through the server; tested in the emulator.
- Quota/billing: reserve first, settle after, refund unused; property tests for "never negative" and
  "never double-charged".

**Verification:** Firebase emulators via a dedicated `firebase.verify.json` with non-default ports;
`server` started with `OPENAI_BASE_URL=http://127.0.0.1:<port>/v1` pointing at `fake-openai.mjs`;
seed account through the Auth emulator; smoke includes language-leak and console-error checks.

**Streaming/realtime (WebSocket) apps:** draw the session state chart (connecting → live → ending
→ reconnecting → closed); test reconnect, duplicate end, upstream drop, and quota exhaustion
mid-stream with the fake server's `slow`/`timeout` faults. Consider TLA+ if multiple devices or
handoffs are involved.

## Next.js (App Router) + Postgres (Prisma/Drizzle)

- Boundaries: `app/` UI and route handlers; `server/` domain + db; client components cannot import
  `server/` or the DB client (`"server-only"` package plus an import rule).
- Postgres row-level security or a repository layer that always filters by owner; tests that a user
  cannot read another user's rows.
- Migrations checked in; a check that the schema and migrations are in sync.
- Verification: Docker Postgres on a non-default port, seeded via the app's own API.

## Python (FastAPI) + any frontend

Field-tested on a FastAPI + SQLite + vanilla JS app.

**Layout:** `app/domain` (pure, pydantic models), `app/adapters/{db,ai}.py`, `app/api` (routers:
parse, authorize, call domain), `app/config.py` (the only place that reads env vars),
`app/log.py`, `app/static/` (frontend). Use a venv in the repo (`.venv/`) and call tools by path.

**Tools:** ruff (lint + format), `mypy --strict`, pytest, Hypothesis, import-linter for layers.

**Boundaries (import-linter in `pyproject.toml`):**

```toml
[[tool.importlinter.contracts]]
name = "Layers"
type = "layers"
layers = ["app.main", "app.api", "app.adapters", "app.domain"]

[[tool.importlinter.contracts]]
name = "Domain does no I/O"
type = "forbidden"
source_modules = ["app.domain"]
forbidden_modules = ["sqlite3", "httpx", "os", "app.config", "app.log"]

[[tool.importlinter.contracts]]
name = "Only the AI adapter talks HTTP"
type = "forbidden"
source_modules = ["app.api", "app.domain", "app.adapters.db", "app.main"]
forbidden_modules = ["httpx", "openai", "requests"]
```

**Project rules:** start from `templates/lint/python/project_rules.py` (AST + comment checks: env vars
outside config, HTTP clients outside the AI adapter, `print()`, excuse comments) with its fixtures,
plus a pytest test that asserts each rule code and line on `violations.py` and nothing on
`compliant.py`. Frontend JS (even vanilla) gets the oxlint plugin in `lint/js/` with its own small
`package.json`.

**`scripts/check.sh` table:**

```bash
CHECKS=(
  "app/ tests/ lint/ pyproject.toml|python: ruff|.venv/bin/ruff check --quiet --output-format concise . && .venv/bin/ruff format --check --quiet ."
  "app/ tests/ pyproject.toml|python: mypy --strict|.venv/bin/mypy --no-error-summary"
  "app/ pyproject.toml|python: layer boundaries|.venv/bin/lint-imports --no-cache"
  "app/ lint/|python: project rules|.venv/bin/python -m lint.project_rules"
  "app/ tests/ lint/ pyproject.toml|python: tests|.venv/bin/pytest -q -rf --tb=short -p no:warnings"
  "app/static/ lint/js/|frontend: lint + rule fixtures|npm --prefix lint/js run --silent lint && npm --prefix lint/js run --silent test"
)
```

**Fake model server in pytest:** a session fixture starts `node verify/fake-openai.mjs` on a free
port (pass `FAKE_OPENAI_PORT`, optionally `FAKE_OPENAI_FIXTURES`), waits for `/health`, yields the
base URL, and terminates it. Point `Settings(openai_base_url=...)` at it; write one test per fault
(`x-fake-fault: timeout | 500 | 429 | bad-json`).

**`verify/verify.config.mjs` backend process:**

```js
{
  name: 'backend', port: 8765,
  command: ['bash', '-c', 'rm -f verify/.state/verify.db && exec .venv/bin/uvicorn --factory app.main:create_app --host 127.0.0.1 --port 8765'],
  env: { DATABASE_PATH: 'verify/.state/verify.db', OPENAI_BASE_URL: 'http://127.0.0.1:4010/v1', OPENAI_API_KEY: 'fake-key' },
  ready: { url: 'http://127.0.0.1:8765/health' },
}
```

The DB is recreated on every `up`; restart (`down`, `up`) after backend changes.

**`quality/review.config.json`:**

```json
{ "installCommand": "python3 -m venv .venv && .venv/bin/pip install -q -r requirements-dev.txt && npm ci --prefix verify --silent" }
```

**CI:** `actions/setup-python` + the same venv install, plus Node for `verify/`.

## Go services

- `go vet`, staticcheck, golangci-lint (depguard for boundaries), `go test -race`.
- `internal/domain` pure; `internal/adapters`; handlers thin.
- Property tests with `testing/quick` or rapid.

## Mobile (React Native / Flutter)

- Static layers as above for the language.
- Verification through the platform simulator tooling (screenshots, accessibility tree);
  keep the same CLI verbs (`up`, `click`, `snapshot`, `screenshot`, `errors`) so prompts transfer.

## Test runners that never exit

A test that leaves a socket, server or timer open keeps its process alive after it fails, and every
gate waits for it. `scripts/check.sh` stops a check at its time limit, but give the runner its own
limit too, so the failure names the test:

- Node: `node --test --test-timeout=30000`; close servers and sockets in `t.after()` (an upgraded
  WebSocket is not closed by `server.closeAllConnections()`; call `terminate()` on each client).
- Vitest / Jest: `testTimeout` and `hookTimeout` in the config; Jest `--detectOpenHandles` to find
  the leak.
- pytest: `pytest-timeout` (`--timeout=30`).
- Go: `go test -timeout 2m`.
