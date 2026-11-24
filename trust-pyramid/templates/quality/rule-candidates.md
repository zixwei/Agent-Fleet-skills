# Rule candidates

Mistakes that are not a lint rule or a test yet. The agent keeps this ledger and turns its entries
into rules itself, as `quality/ADD_RULE.md` describes.

- Add a row the first time a mistake is corrected and nothing can catch it yet. Bump `Seen` (a
  number) each time it happens again.
- At `Seen` 2 or more, after the current task, ask the user whether you may turn it into a rule or
  a test, and on a yes do it and delete the row in that pull request. `bash scripts/hook-status.sh`
  lists such rows at the start of every session.
- If a real blocker comes first (existing violations must be fixed first), start "Why not a rule
  yet" with `Blocked until <what>`; the reminder then only counts the row. Remove it once that is done.
- "Why not a rule yet" must be a real reason: seen once so far; not visible in the source and no
  test can catch it; existing violations must be fixed first. "The file is protected" or "a
  maintainer has to add it" is never a reason: gate files change through a pull request from a
  separate worktree, which the agent opens.
- "Plan" is the next step the agent will take, not a request to someone else.

| Kind of mistake | Seen | Examples (file:line, date) | Why not a rule yet | Plan |
|---|---|---|---|---|
| | | | | |
