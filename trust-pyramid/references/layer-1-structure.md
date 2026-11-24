# Layer 1 - Structure and APIs

The cheapest bug is the one that cannot be written. Design the repository so boundaries are
visible in paths and enforceable by tools.

## Directory layout

Separate at least these roles (names are examples; adapt to the stack):

| Role | Example path | May depend on | Must not |
|---|---|---|---|
| UI | `web/src/components`, `web/src/hooks` | client services, shared types | call DB/cloud SDKs, model APIs, read secrets |
| Client services | `web/src/services` | shared types, HTTP | import UI framework (keeps them unit-testable) |
| Pure domain logic | `web/src/domain`, `server/src/domain` | nothing with I/O | touch network, DB, env vars, clock (pass `now` in) |
| Server API | `server/src/api` | domain, adapters | contain business rules inline |
| Adapters | `server/src/adapters/{db,ai,payments}` | SDKs | leak SDK types into domain |
| Shared types | `shared/` or generated | nothing | contain implementation |

Frontend and backend never import each other's implementation; they agree on JSON shapes.

Naming is a constraint too. Example: files on a per-frame hot path end in `.hot.ts`, and a rule
forbids allocations and logging in them. Only add such conventions where they pay off.

## Single-entry APIs

Wrap anything that is easy to misuse so there is exactly one correct way to do it:

- **AI client** (`adapters/ai`): timeout, retry with cap, max tokens, rate limit, usage/cost
  logging, and schema validation of model output. Business code never imports a model SDK or
  fetches a model URL directly. **Give every user-facing call a total deadline across retries**
  (for example 20 seconds) and do not retry timeouts on interactive paths: a 15-second timeout
  with two retries made users wait ~46 seconds for an error in a benchmark.
- **Config**: read and validate environment variables in one module at startup; missing values
  fail fast. Secrets exist only on the server.
- **i18n**: one `t()` for user-visible text when the UI is multilingual.
- **Logger**: one function; it must not log user content (transcripts, prompts) or secrets.
- **UI primitives**: one modal, one form field, one error banner, one button system.
- **Time**: domain functions take `now` as a parameter.

## Remove footguns at the root

If an API needs expert judgment to use correctly (React `useEffect` is the classic example), ban
it in feature code and allow it only inside a small, reviewed framework layer. This removes a
whole class of review comments permanently.

## Existing projects

Do not reshuffle a large codebase in one go. Introduce the target layout for new code, add
boundary rules that only apply to the new folders, and migrate module by module.
