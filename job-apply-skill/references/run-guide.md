# Run guide: `start` and `stop`

You are the **coordinator**. You search LinkedIn (through a search sub-agent), fill the queue, hand jobs one at a time to worker sub-agents, and report. You never fill an application yourself. `S` = this skill's `scripts/` folder.

There is **one browser**: the user's own Chrome, through the `jobapply-browser` tools (see [browser-setup.md](browser-setup.md)). Only one sub-agent uses it at a time, so search first, then apply one job after another.

**Once a run has started, keep going until it finishes.** Never end your turn to ask the user something mid-run, with one exception: a worker returned **help** (a human check, a login, an account, or a question only the user can answer) and `ask_for_help` is true in `config.yaml` (the default). Then you ask, wait for "go", and carry on (see "When a worker needs help"). Otherwise the run is fully automatic by the user's own choice. In particular:
- **Dry run or real** comes only from `dry_run` in `config.yaml`. Never ask "test mode or real?". If the user wants a test first, they set `dry_run: true` (or ask you to) *before* `start`.
- Anything else you'd want to ask (an unclear job, whether a job fits) goes into the manual list and the final report instead.
- Only stop early for `stop`, the daily cap, an empty queue, LinkedIn blocking (security check, logged out, Easy Apply limit), or the browser connection being lost.

## Start

1. `S/state.py get`. If the state is RUNNING or PAUSED, run `S/state.py run-lock status`:
   - `held: true` and `stale: false` → a run really is going: say so and offer `watch`. Stop here.
   - `stale: true` → an earlier run stopped without finishing (chat closed, turn ended). Tell the user in one line ("the last run stopped at <time> after <n> jobs; continuing from where it left off"), `S/state.py set READY`, and carry on. Step 4 picks up its unfinished jobs.
   - `held: false` (PAUSED after a LinkedIn block) → say in one line why it paused, `S/state.py set READY`, and carry on: step 2 checks LinkedIn again.
   Otherwise `S/state.py require READY`.
2. **Browser quick check** ([browser-setup.md](browser-setup.md), "Quick check"): tools loaded, Chrome connected, LinkedIn logged in. Fix anything with the user now, before they walk away. If the `linkedin_profile` checkpoint is missing, do setup-guide step 11 item 2 first.
3. `S/state.py run-lock acquire` (it replaces a stale lock by itself). If it still fails, a run is going: offer `watch`.
4. `S/tracker.py recover`. For each job in `verify_on_site`: open its URL in the browser, look for an "Applied" / "Application submitted" state, update to `applied` or back to `queued`. For each job in `verify_email`: tell the user to check their Sent folder for a message to that address near `updated_at`, then update to `applied` or `queued` from their answer. Never resend.
5. `S/state.py set RUNNING`. `S/live.py event --worker coordinator --stage run_started --platform linkedin`.
6. Read the user's extra words for this run only: `remote only` / `onsite` / `hybrid` → work mode filter; a role ("only Django jobs") → search only that role.
7. Tell the user in one short message: "Searching LinkedIn and applying now. Today's limit left: N (`S/tracker.py stats`). Keep Chrome open. Type `/job-apply watch` to see it live or `/job-apply stop` to end." Open the live page if they asked to watch.
8. **Search phase**, then **apply phase**, below.
9. When nothing is left: finish (below).

## Search phase

Start one **search sub-agent** with the Agent tool and wait for it. Prompt:

```
You search LinkedIn for jobs and report them. You never apply.
Browser: use only the jobapply-browser tools (the user's own Chrome, already logged in).
Never call browser_close. Never log in or out. Start with browser_navigate.
Hints (may be outdated): {contents of references/sites/linkedin.md}
Roles: {roles for this run}  Seniority: {search.seniority}  Locations: {search.locations}
Work modes: {mode filter or search.work_modes}  Exclude: {search.exclude_keywords}
Blocked companies: {search.blocklist_companies}

1. Search LinkedIn Jobs for each role. Remote: one search with the work-mode filter "Remote"
   (location: the user's country or "Worldwide"). On-site/hybrid: one search per location.
   Past week, newest first. Read pages with browser_snapshot; scroll the results list to load
   more. Skip "Applied" and "No longer accepting applications" cards.
2. Collect up to {limit} different jobs. Stop searching once you have them.
3. Open each job and read its description. Note whether it has "Easy Apply" or "Apply"
   (company website). For "Apply", click it once to learn the company site's URL, then close
   that new tab with browser_tabs and come back (don't fill anything). If the posting's only
   way to apply is an email address ("send your CV to ..."), record that address.
4. If LinkedIn shows a security check, CAPTCHA, "unusual activity" or the sign-in page:
   stop and report it.
5. Write a JSON array to {workspace}/queue/{date}-linkedin.json, one object per job:
   {"platform":"linkedin","url":"https://www.linkedin.com/jobs/view/<id>/","apply_url":"",
    "email":"","job_id":"<id>","company":"","title":"","location":"",
    "work_mode":"remote|hybrid|onsite","posted_at":"","description":"full text",
    "easy_apply":true|false}
   apply_url: the company site URL for "Apply" jobs, empty for Easy Apply.
   email: only for email-only postings.
6. Close any extra tabs you opened (browser_tabs), keeping one.
7. Return one line: "{count} jobs" or "blocked: <reason>".
After each job you open, run: python3 {S}/live.py event --worker search --stage searching --platform linkedin --note "{count} found"
```

Use `limit` = 3 × the daily cap left (at least 10). If the agent reports `blocked: <login or security check>` and `ask_for_help` is true: tell the user "LinkedIn is showing <what> in Chrome. Please sort it out there, then type **go**", wait, and on "go" start the search agent again. With `ask_for_help` false: `S/state.py set PAUSED`, tell the user what LinkedIn wants, and finish.

### Fit gate and queue

For each job in the queue file, first `S/tracker.py check --platform linkedin ...` (skip duplicates without reading further). For new ones, score fit **0–100** against the profile:

- Hard filters first (score 0, reason given): location or work mode the user can't do; a visa or citizenship the answers say the user lacks; seniority far off (e.g. "10+ years" for a 2-year profile); blocklisted company or keyword.
- Otherwise: share of the job's must-have skills that are `verified`/`user_attested` in the ledger (60%), years asked vs actual (20%), domain and role match (20%).

`score ≥ limits.min_fit_score` → `S/tracker.py add --platform linkedin --url "<LinkedIn job url>" --job-id <id> ... --status queued --fit-score N`. For an email-only posting use `--url "mailto:<email>"` (still with `--job-id <id>`): the tracker uses `mailto:` to know a job is applied to by email. Lower fit → `add ... --status skipped --fit-score N --reason "low fit: <short why>"`. Keep the queue file until the run ends (it holds each job's `apply_url`, `email` and `easy_apply`, which the tracker CSV does not).

Once, after queuing: if any queued job's queue-file entry has a non-empty `email`, run `S/send_email.py check`. If it reports not configured, `S/tracker.py update --id <id> --status manual --reason "email not configured: see references/email-guide.md"` for each of them (no worker spent on them) and remember the count for the final report.

## Apply phase

One job at a time (`workers.max_total` is 1: one browser).

1. `S/tracker.py claim --worker w1`. If `job` is null: daily cap or empty queue → stop handing out jobs (finish).
2. Make the job folder `applications/<YYYY-MM-DD>_<company-slug>_<title-slug>/`. Write `job.md`: title, company, LinkedIn URL, description, and one "Apply via:" line from the queue-file entry:
   - `easy_apply: true` → `Apply via: LinkedIn Easy Apply at <LinkedIn url>`
   - `apply_url` set → `Apply via: company website at <apply_url> (LinkedIn job: <url>)`
   - `email` set → `Apply via: email to <address>`
   Then `S/tracker.py update --id <id> --status claimed --folder <folder>`.
3. Start a worker sub-agent (Agent tool) with the prompt in [worker-guide.md](worker-guide.md), placeholders filled, and wait for it.
4. Read its one line. If it starts with `help`, follow "When a worker needs help" below. Otherwise pause `workers.pause_between_submits_sec` (random within the range) before the next LinkedIn job. Claim the next one.
5. If two jobs in a row return "linkedin blocked" (Easy Apply limit), or `login needed` while `ask_for_help` is false: stop handing out jobs, `S/state.py set PAUSED`, and finish with that reason.
6. Between workers, handle any user messages (see "While running").

The daily cap is enforced by `claim`. Never work around it.

### When a worker needs help

The worker returned `help <id>: <what>` and left the page open in Chrome.

1. Tell the user in one short message, plain words, in their language:
   - A check, login or account: "I need your help in Chrome: <what>, on <site> (<company>, <title>). Please do it there, then type **go**. Type **skip** to leave this job for later."
   - A question: "<company> asks: <exact question> <options>. What should I answer? (Type **skip** to leave this job for later.)"
   Then end your turn and wait. The job stays `claimed`.
2. On **go** (or an answer): for a question, save the answer to `profile/answers.json` (`source: "user"`, `scope: "generic"` unless it is clearly about this company only). Start a worker for the same job and folder with `{continue_note}` saying what the user did. Then carry on with the apply phase as normal.
3. On **skip**: `S/tracker.py update --id <id> --status manual --reason "<what was needed>"`, close the extra tabs, carry on with the next job.
4. If the user starts talking about something else instead, answer it, and remind them in one line that a job is waiting for **go** or **skip**.

## `apply <link>`: one job the user gives

The user pastes a job link: a LinkedIn job, or any company's careers or application page. State READY.

1. Browser quick check (start step 2), `S/state.py run-lock acquire`, `S/state.py set RUNNING`.
2. Start the search sub-agent with this prompt instead of the search prompt: "Open {link} with the jobapply-browser tools (never browser_close, never log in). Read the posting and write ONE job object, same fields as the search prompt step 5, to {workspace}/queue/{date}-direct.json. LinkedIn link → platform "linkedin" and its job id. Any other site → platform "direct", url = the link, apply_url = the application form's URL (click "Apply" once to find it, then come back; don't fill anything), job_id = the site's own id if the URL shows one. Return "1 job" or "blocked: <reason>"."
3. `S/tracker.py check`, then `add ... --status queued --fit-score 100 --reason "asked by user"` (no fit gate: the user chose it; still stop if `check` says it's a duplicate, and tell them when it was applied). For a "direct" job, job.md says `Apply via: company website at <apply_url or url>`.
4. Apply phase for this one job (claim gives it first because of its score), including help. Then finish as below.

## While running

The user may type commands while a worker runs. Handle them without disturbing it:
- `watch`, `status`: show, change nothing.
- `login`: see commands.md. Afterwards `S/tracker.py requeue --platform linkedin --reason-contains login`.
- `limit <n>`: update config; takes effect at the next claim.
- `answers`: save; then `S/tracker.py requeue --from-status manual --reason-contains "<question topic>"` for jobs that waited for it.
- `go`, `skip`, or an answer: only meaningful while a job waits for help (above).
- `update-profile`, `reset`, second `start`, `apply <link>`, `browser`: say they wait until the run ends.
- `stop`: below.

If the browser connection is lost (every browser tool errors), run browser-setup.md "Quick check" once. Still broken → finish with the reason, and tell the user to type `/job-apply browser`.

If the internet drops (navigation errors on every page), wait a minute and retry, up to 15 minutes, then finish.

## Stop and finish

`stop` (or the cap, an empty queue, or a block): give no new jobs, wait for the running worker to return, then:

1. Close extra browser tabs (`browser_tabs`), keeping one. Don't call `browser_close`.
2. `S/live.py event --worker coordinator --stage run_finished`.
3. `S/state.py set READY` (from RUNNING or PAUSED, unless you set PAUSED for a block: leave it), `S/state.py run-lock release`.
4. Report in chat, short: applied today (with codes), skipped by reason, the manual list (code, company, title, reason, link) and discarded count. Point to `job-apply-data/logs/My_Job_Applications.csv` (in the folder Claude runs in) for the full record: every job found, its status in plain words, reason, links and the files sent.
