---
name: job-apply
description: Automatic job search and application on LinkedIn for one person on this laptop. Builds a verified profile and resume from the user's own project code and details, then uses the user's own Chrome (through the Playwright Extension) to find LinkedIn jobs that fit and apply to them with a tailored resume, through Easy Apply, the company's own website, or email. Use when the user types /job-apply, or asks in any language to set up job applications, connect the browser, start or stop applying, see what it is doing, update their profile, log in to LinkedIn again, change the daily limit, record an interview or rejection, change saved answers, or get a job application report.
---

# Job Apply

One user, one laptop, one job site: **LinkedIn**. Jobs are found on LinkedIn only, then applied to wherever LinkedIn's button leads: Easy Apply on LinkedIn, the company's own website, or an email address in the posting. The user can also hand over a single job link (LinkedIn or any company site) with `apply <link>`.

Code and references live in this skill folder. Everything personal lives in the workspace `~/.job-apply` (or `$JOB_APPLY_HOME`). Never put personal data in the skill folder.

Scripts are in `scripts/` next to this file. Run them as `python3 <this folder>/scripts/<name>.py ...`. They print JSON.

## The user may not be technical

- **Do the work yourself.** Run every script, open every page, fill every file. Ask the user only for what only they can do: click "Add to Chrome", paste the token, log in to LinkedIn, restart Claude Code once, answer questions about themselves, solve a human check in Chrome and type "go".
- **One short step at a time**, in plain words. Say exactly what to click and what to type back ("done"). No jargon (no "MCP", "token scope", "JSON", "API"); call things "the browser connection", "the code from the extension".
- **Never ask them to run a command, edit a file, or find a folder.** Suggest answers you can work out yourself and let them say "ok".

## Language

Talk to the user in the language they write in (for example Roman Urdu when they write Hindi or Urdu). Everything written to files, resumes, cover letters and forms is English.

## Every request starts here

1. Run `scripts/state.py init` (safe to repeat), then `scripts/state.py get`.
2. Work out which command the user means. They may type it (`/job-apply start remote only`) or say it in their own words ("start applying", "LinkedIn logged me out").
3. Check the gate in the table. If the state isn't allowed, say so plainly. Before setup is done, reply "Setup isn't finished yet" and offer to continue setup.
4. Read the guide listed for that command and follow it.

| Command | Allowed in state | Guide |
|---|---|---|
| `setup` (also `setup from <file>`) | any except RUNNING | [references/setup-guide.md](references/setup-guide.md) (starts with the browser) |
| `browser` (connect or fix the browser) | any except RUNNING | [references/browser-setup.md](references/browser-setup.md) |
| `start` (optional words: `remote only`, `onsite`, `hybrid`, a role such as `only Django jobs`) | READY (or a RUNNING run that has gone silent: see run-guide step 1) | [references/run-guide.md](references/run-guide.md) |
| `stop` | RUNNING | [references/run-guide.md](references/run-guide.md) |
| `apply <link>` (one job link: LinkedIn or any company site) | READY | [references/run-guide.md](references/run-guide.md), section "apply <link>" |
| `go`, `skip` (after the skill asked for help in Chrome) | RUNNING | [references/run-guide.md](references/run-guide.md), section "When a worker needs help" |
| `watch`, `status`, `report` | READY, RUNNING, PAUSED | [references/commands.md](references/commands.md) |
| `limit <n>` (applications per day) | READY, RUNNING, PAUSED | [references/commands.md](references/commands.md) |
| `login` (LinkedIn) | PREFERENCES, READY, RUNNING, PAUSED | [references/commands.md](references/commands.md) |
| `answers`, `mark <code> <status>` | READY, RUNNING, PAUSED | [references/commands.md](references/commands.md) |
| `update-profile` | READY (if RUNNING: say it will wait for the run to end) | [references/setup-guide.md](references/setup-guide.md), section "Updating the profile" |
| `reset` | any except RUNNING | [references/commands.md](references/commands.md) |

If the user asks for another job site (Indeed, Bayt, Rozee...), say plainly that this skill works with LinkedIn only, and that LinkedIn jobs which lead to a company website are applied to there too.

## Rules that always apply

- **No wrong data, ever.** Every value put in a form, resume or letter comes from the user's profile or saved answers. If you are not sure, do not guess: leave optional fields empty, and send the job to the manual list when a required field is unsure. Full rules: [references/accuracy-rules.md](references/accuracy-rules.md).
- **Emails follow one standard:** [references/email-writing.md](references/email-writing.md) (greeting by name or company, introduction, capabilities matched to the job, resume line, "Best regards" footer). No em dashes anywhere.
- **Real skills only.** A skill appears on a resume only if it is in the evidence ledger. Tailoring changes order and emphasis, never membership. See [references/writing-rules.md](references/writing-rules.md).
- **Never solve** a CAPTCHA, 2FA or any human check yourself, and never create accounts on job or company sites. When one appears, ask the user for help: they do it in Chrome and type **go**, and you carry on from that page (`ask_for_help: true`, the default). With `ask_for_help: false` those jobs go to the manual list.
- **Never read secrets** in the user's *project* folders being analysed (`.env`, keys, certificates, credential files) and never copy anything secret-looking into the workspace.
- **Never type or store a site's login password.** The user logs in to LinkedIn by hand in their own Chrome. Two things are stored, both put there by the user: the browser connection code from the Playwright Extension (`scripts/browser.py save-token`, never printed or repeated back), and optionally a Gmail App Password in the workspace `.env` for email applications (read only by `scripts/send_email.py`, see [references/email-guide.md](references/email-guide.md)).
- **One browser:** the `jobapply-browser` tools (`mcp__jobapply-browser__*`), which drive the user's own Chrome through the Playwright Extension. Only one sub-agent uses it at a time. Never call `browser_close` on it during a run (it disconnects from Chrome); close only the extra tabs you opened, with `browser_tabs`. It only reaches the tabs it opened, never the user's own tabs.
- **Duplicates and limits** are decided by `scripts/tracker.py`, never by memory.
