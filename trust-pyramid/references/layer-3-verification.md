# Layer 3 - Verification

Static checks prove the code is well-formed. Verification proves it does the right thing in the
running app. The human reviews the artifacts this layer produces.

## 1. One-command local stack

`node verify/app.mjs up` must start everything (emulators/test DB, backend, frontend, a headless
browser), seed a test account and sample data, and sign in. Requirements:

- **Isolated ports.** Use a dedicated config (for Firebase: a separate `firebase.verify.json`).
  Check ports before starting; developers often have other emulators running.
- **Deterministic browser.** Pin locale (`--lang`, `--accept-lang`) and window size.
- **Seed through the real API** where possible so seeding exercises production paths.
- **No real third-party calls.** Payments in test mode only; model calls go to the fake server.
- Test credentials come from seed/fixture files in the repo, never from the user's real accounts.
- **Every process declares its `port`.** `up` refuses to start if any is busy, otherwise another
  project's server can answer the readiness probe and the run tests the wrong app.
- **No hot reload.** After backend changes, `down` then `up`; stale servers produce confusing 404s.

## 2. Verify CLI

Copy `templates/verify/app.mjs` and fill in `templates/verify/verify.config.mjs`. Commands:

| Command | Purpose |
|---|---|
| `up` / `down` | start/stop the stack; `up` signs in |
| `goto <path>` | navigate |
| `click "<accessible name>"` | click button/link/tab/menuitem by its screen-reader name |
| `fill "<label>" "<text>"` | fill a labelled field |
| `press <key>` | keyboard |
| `snapshot` | print the accessibility tree - agents read this instead of guessing selectors |
| `screenshot <name>` | save to `verify/.state/artifacts/<name>.png`; agents must open and look at it |
| `errors` | console errors and uncaught exceptions since the last call |

The browser is started once and reused over CDP. Console errors are recorded by a long-lived
watcher process, not by scripts injected per connection (those die with the connection).

## 3. Feature map

`verify/feature-map.md`: one row per user-visible feature - how to reach it (using accessible
names), what "works" means, and whether the smoke flow covers it. Humans and agents use the same
names, so "Preferences in Settings" means one specific place.

## 4. Smoke flow

`verify/smoke.mjs` walks the feature map, screenshots each step, writes
`verify/.state/artifacts/smoke-result.json`, and exits non-zero on failure.

- **Reset to a fixed starting state first** via `reset()` in `verify.config.mjs` (data, language,
  local storage). A previous run must not poison the next one: leftover rows make selectors match
  several elements. Prefer a test-only reset endpoint that exists only against the emulator/test DB,
  and scope assertions to the item the step created.
- Screenshots are full-page; old `smoke-*` screenshots are deleted at the start of each run.
- Include "no console errors".
- Multilingual UI: switch to each language and scan visible text and `aria-label`/`title`/
  `placeholder` for untranslated strings, excluding seeded user content and `lang`-tagged elements.
- Every new user-visible feature adds a feature-map row and a smoke step in the same change.

## 5. Fake model server (apps that call AI APIs)

Use `templates/verify/fake-openai.mjs`: an OpenAI-compatible server (`/v1/chat/completions` with
and without streaming, `/v1/responses`, `/v1/audio/transcriptions`, `/v1/models`) returning
deterministic content. Point the server's base URL at it during verification. Fault injection via
the `x-fake-fault` request header or `FAKE_OPENAI_FAULT` env: `timeout`, `500`, `429`, `bad-json`,
`slow`. Write tests for each fault path in the AI adapter.

## 6. Property-based UI testing (optional)

Random input and interaction fuzzing (for example Antithesis Bombadil) finds UI bugs nobody wrote
a test for. Add once the smoke flow is stable.

## 7. State machines and formal methods (optional)

For stateful, concurrent flows (recording sessions, subscription states, quota settlement), draw
the state chart first and review it with the user. If the state space is large or concurrency is
involved, model it in TLA+ and model-check the invariants. Recommend this in the plan only when it
pays off; never block setup on it.
