# Email applications: setup and how it works

Most jobs are applied to through a form in the browser. A few postings only give an email address ("send your resume to jobs@company.com"). This skill applies to those automatically too, by sending a real email through the user's own Gmail — but only once the user has set up a Gmail **App Password** for it. Until then, those jobs go to the manual list instead, with a reason pointing here.

## What the user needs to do (once)

Tell the user, in their language, short:

1. Go to their Google Account → **Security** → **2-Step Verification** (must be turned on first) → **App Passwords**.
2. Create one for "Mail" / "Other", name it something like "job-apply". Google shows a 16-character password once.
3. Create the file `~/.job-apply/.env` themselves, with a text editor or a terminal command, containing:
   ```
   GMAIL_ADDRESS=their_address@gmail.com
   GMAIL_APP_PASSWORD=the16characterpassword
   ```
4. Tell the skill "done". **Never ask the user to paste the app password into the chat** — have them put it directly into the file so it never appears in the conversation. If they do paste it anyway, treat it the same as any other secret: never echo it back, never put it in a resume, letter, log, or anywhere outside `.env`.
5. Confirm with `python3 S/send_email.py check` (ok: true means it's set). Don't open or print the file's contents yourself.

This app password is **not** the user's real Gmail password. It only works for sending mail through this one path, and the user can revoke it any time from the same Google Account page without touching their main password.

## What the skill does with it

- Read only by `scripts/send_email.py`, only from `~/.job-apply/.env`, via `common.load_env()`.
- Used only to send one message at a time over Gmail's SMTP server (`smtp.gmail.com:465`), with the tailored resume (and cover letter, if one was made) attached.
- Never logged, never printed by any script, never written into a resume, letter, `job.md`, or any CSV. `tracker.py`, `live.py` and every report only ever show the job's recipient address and subject, never the credential.
- `send_email.py check` only reports whether the two keys exist (true/false); it never prints their values.

## Where this fits in a run

- The search sub-agent marks a posting as email-apply when that's the only way to apply (see run-guide.md "Search phase").
- Before the apply phase spends a worker on an email-apply job, the coordinator runs `send_email.py check`; if not configured, that job goes straight to `manual` with reason `"email not configured: see references/email-guide.md"` — no worker, no repeated failures.
- A configured email-apply job is handled exactly like any other: fully automatic, same accuracy rules (accuracy-rules.md "Email applications"), same write-ahead log before sending (tracker status `submitting` before the send, so a crash mid-send is caught by `tracker.py recover`'s `verify_email` list rather than resent).
- `dry_run: true` in `config.yaml` makes `send_email.py` save a `.eml` file instead of sending — open it in any mail app to read exactly what would have been sent.

## If the user asks to change or remove it

- Change the password: generate a new app password in Google, replace the line in `.env`.
- Turn email applications off without losing anything else: delete or rename `.env`; those jobs go back to the manual list.
- `reset` deletes the whole workspace, including `.env` — warn about this specifically, since it also means removing the app password from the skill's storage (not from the user's Google Account; they still revoke it there if they want it gone for good).
