# Smaller commands

`S` = this skill's `scripts/` folder. Answer the user in their language, short and plain.

## watch

`S/live.py rebuild`, then `S/live.py path` and open that file with `xdg-open` (`open` on macOS). Say: "The live page is open. It updates every 2 seconds; closing it doesn't stop anything."

## status

`S/tracker.py stats`. Reply in 3–5 lines: running or not, applied today, left today, in queue, manual list count.

## report

The full record is always in `~/.job-apply/logs/My_Job_Applications.csv` (also reachable as `job-apply-data/logs/...` from the project folder), rewritten after every change; `S/tracker.py export` rewrites it on demand. Mention it.

`S/tracker.py report --days N` (N from the user's words; default 7; "this month" = days since the 1st). Reply: applied (codes, company, title), skipped grouped by reason, manual list (code, company, title, reason, link), discarded count.

## limit <n>

Applications per day. Set `limits.daily_cap` in `~/.job-apply/config.yaml` to n (and `platforms.settings.linkedin.daily_cap` too, if that key exists, to the same n). Above 15, add one line: LinkedIn may restrict accounts that apply too much; 10 a day is safer. During a run it takes effect at the next job. Say: "Done, up to n applications a day."

## login (LinkedIn)

The user's own Chrome, through the `jobapply-browser` tools. Works during a run between jobs.

1. If the browser tools aren't loaded or connected: follow [browser-setup.md](browser-setup.md) "Quick check".
2. `browser_navigate` to `https://www.linkedin.com/feed/`. Tell the user: "LinkedIn is open in Chrome. Please log in there, then type **done**." Never ask for or type a password.
3. On "done": `browser_snapshot`, confirm a logged-in state (feed, "Me" menu). If not, ask them to try again.
4. If a run is going or jobs wait on it: `S/tracker.py requeue --platform linkedin --reason-contains login`, and say how many will be retried.

Chrome keeps the login, so this is rarely needed.

## browser

Connect or fix the browser connection: follow [browser-setup.md](browser-setup.md) "Full setup". Not during a run.

## answers

Read `~/.job-apply/profile/answers.json`. Show the saved answers as a short numbered list. Apply the user's change, set `source: "user"` and `updated_at`. If jobs in the manual list were waiting on that question (reason starts with "unsure:"), `S/tracker.py requeue --from-status manual --reason-contains "<a word from the question>"` and say how many will be retried.

## mark <code> <status>

Statuses: `interview`, `rejected`, `offer` (also accept "got a call", "they said no" and so on). `S/tracker.py update --id <code> --status <status>`. If the user names a company instead of a code, find it with `S/tracker.py list --status applied,interview` and confirm which one.

## reset

Refuse while a run is going. Otherwise warn that it deletes everything in `~/.job-apply` (profile, resumes, logs, the browser connection code) and ask for an explicit yes. On yes: delete the workspace folder, then `S/state.py init`. The skill folder, Chrome, the extension and the LinkedIn login are not touched; the next `setup` connects the browser again.
