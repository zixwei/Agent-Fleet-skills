# Setup guide

Setup runs once and can be resumed. Always start by reading `scripts/state.py get` and continue from the current state. Checkpoints in `state.json` record finished work, so never redo a finished step.

Workspace paths below are relative to `~/.job-apply`. `S` means this skill's `scripts/` folder.

| State | Step | Who |
|---|---|---|
| NOT_SETUP | 0 Connect the browser (the user's Chrome) | skill, user clicks |
| NOT_SETUP → INTAKE | 1–2 Collect and save raw details | user |
| INTAKE → CLARIFY | 3–4 Normalise, validate, ask what's missing | user |
| CLARIFY → ANALYZING | 5–6 Read projects, reconcile claims | skill, then user |
| ANALYZING → BUILDING | 7–8 Profile, summary, base resume | skill |
| BUILDING → USER_REVIEW | 9 User reviews | user |
| USER_REVIEW → PREFERENCES | 10–11 Preferences, LinkedIn ready | user, then skill |
| PREFERENCES → READY | done | |

Move between states only with `S/state.py set <STATE>`.

The user may not be technical: do everything you can yourself, suggest answers you can work out, and ask for one thing at a time in plain words.

## Step 0: connect the browser (before anything else)

If there is no `browser` checkpoint, follow [browser-setup.md](browser-setup.md) "Full setup" first. It ends with one Claude Code restart, so it goes first, before the long questions. When the connection test passes (checkpoint `browser` saved), continue with step 1. If the user wants to give their details first, that's fine: do step 0 before step 11.

## Steps 1–2: collect details (state INTAKE)

Set state INTAKE. Ask in the user's language, in one message, roughly:

> Let's build your profile. Paste everything you can about yourself: your projects (folder path, and in `[ ]` what you did there), jobs, certificates, skills, education, contact details and links. Any order, any language. Or give me a file, or your old resume as a PDF.

Accept any of:
- **One long message.** Take it as is.
- **Topic by topic.** If the user prefers, ask in this order: projects, work history, certificates and education, contact and links, photo (optional).
- **A file** (`setup from <path>`): read it with the Read tool (text, Markdown, PDF). An old resume is a starting point, not evidence: its claims still go through reconciliation.

Append every piece of user-provided text, untouched, to `intake/raw_input.md` under a timestamp heading. Never edit earlier entries.

### Projects

| User gives | Do |
|---|---|
| Exact folder: `/path/to/app [what I did]` | Use it. The `[ ]` text is the user's claim for that project. |
| A parent folder | List its subfolders that look like projects (contain `.git`, `package.json`, `pubspec.yaml`, `pyproject.toml`, `requirements.txt`, `go.mod`, `pom.xml`, `build.gradle`, `Cargo.toml`, `composer.json`, `*.sln`). Ask which are theirs and what they did in each. |
| A vague hint ("my recipe app is on the Desktop") | Search under the home folder by name with `find ~ -maxdepth 5 -type d -iname '*recipe*' -not -path '*/node_modules/*' -not -path '*/.*'`. Ask the user to confirm the match. |
| Public GitHub URL | `git clone --depth 1 <url> intake/repos/<name>` and treat that copy as read-only. Private repos: ask for a local path instead. |

Also ask which email address(es) the user commits with. Suggest what you find with `git -C <path> log --format='%ae' | sort | uniq -c | sort -rn | head`, and let the user confirm. These emails decide which code is theirs.

## Steps 3–4: normalise and validate (state CLARIFY)

Set state CLARIFY. Translate everything to English and write `intake/intake.json`:

```json
{
  "identity": {"full_name": "", "email": "", "phone": "", "city": "", "country": "", "photo_path": null},
  "links": {"linkedin": null, "github": null, "portfolio": null, "other": []},
  "git_emails": [],
  "projects": [{"slug": "recipe-app-flutter", "path": "", "source": "local|github", "url": null,
                "claims": ["Built the full frontend", "Riverpod state management"], "role_hint": "frontend|backend|full-stack|ai|mobile|other"}],
  "experience": [{"company": "", "title": "", "start": "YYYY-MM", "end": "YYYY-MM or null", "location": "", "claims": []}],
  "education": [{"institution": "", "degree": "", "field": "", "start": "YYYY", "end": "YYYY"}],
  "certifications": [{"name": "", "issuer": "", "year": "", "details": "", "url": null}],
  "skills_self_reported": [],
  "languages_spoken": []
}
```

Link every detail to the thing it belongs to (a certificate's details stay with that certificate, a project claim with that project). Keep the user's meaning. Don't upgrade it ("used" stays "used", not "led").

Validate, then ask about everything missing **in one batch**, numbered, short:
- Required: full name, email, phone, city and country, at least one project or one job, git emails for local projects.
- Every project path exists and is readable.
- Experience dates are real months, starts before ends, at most one current job unless the user confirms more.
- Links look like real URLs. Don't visit them.
- Anything ambiguous ("2 years at X" without dates, a claim that could belong to two projects).

Repeat until complete, saving answers to `raw_input.md` and `intake.json`. Then `state.py set ANALYZING`.

## Steps 5–6: read projects and reconcile (state ANALYZING)

For each project without a checkpoint `project:<slug>`, or whose checkpoint `head` differs from `git -C <path> rev-parse HEAD`:

1. Start one sub-agent per project with the Agent tool, up to 4 at a time, running in the background. Give it the prompt in [analysis-guide.md](analysis-guide.md) with the placeholders filled in.
2. When it finishes, check that `profile/projects/<slug>.md` and `profile/ledger/<slug>.json` exist and are valid JSON, then `state.py checkpoint project:<slug> '{"head": "<sha or none>"}'`.
3. Tell the user briefly how many projects are done ("2 of 3 projects read").

Then run `S/ledger.py merge`. It builds `profile/evidence_ledger.json` from every `profile/ledger/*.json` (format in analysis-guide) and lists `needs_question`: every skill still `said_not_seen` or `seen_not_said`. Ask the user about all of them in one batch:

> 1. You said Firebase Auth for recipe-app, but I couldn't find it in that code. Did you build it, maybe in another project? (yes / no)
> 2. shop-api has CI pipelines written by someone else. Did you work on them too? (yes / no)

Record each answer with `S/ledger.py decide "<skill>" yes|no`. Yes → `user_attested` (or `verified` for code the user did write); no → `rejected`, never used. Answers are saved in `profile/ledger_decisions.json`, so a later `ledger.py merge` (after `update-profile`) keeps them and never asks again. Then `state.py set BUILDING`.

## Steps 7–8: profile and base resume (state BUILDING)

Write, from `intake.json` and the ledger only:

- `profile/profile.json`: identity, links, experience, education, certifications, projects (summary, stack, what the user did, evidence-backed highlights), skills (from the ledger, with level and evidence count), total experience. Compute total experience with `S/profile_tools.py years`, never by hand.
- `profile/profile.summary.md`: the same in readable form, one section per project.
- `profile/professional_summary.md`: 3–4 plain sentences on what the user builds, their strongest verified skills and their years of experience. Follow [writing-rules.md](writing-rules.md).
- `resume/base_resume.json` in the schema in [writing-rules.md](writing-rules.md), section "Resume data". Then:
  - `S/profile_tools.py check-resume resume/base_resume.json` must report no problems. If it flags a skill the ledger has under another name ("S3" vs "AWS S3"), record that with `S/ledger.py alias "S3" "AWS S3"` instead of renaming things by hand. Names in brackets already match ("RAG" finds "Retrieval-Augmented Generation (RAG)").
  - `S/render_resume.py resume/base_resume.json resume/base_resume.pdf`.

Then `state.py set USER_REVIEW`.

## Step 9: user review (state USER_REVIEW)

Show a short summary in chat: total experience, top skills, projects and one line each, and the paths to `profile.summary.md` and `base_resume.pdf`. Open the PDF with `xdg-open` (or `open` on macOS). Ask the user to approve or say what to change. Changes go back through BUILDING (`state.py set BUILDING`, fix, rebuild, `set USER_REVIEW`). A change that adds a new claim goes into the ledger as `user_attested`. On approval: `state.py set PREFERENCES`.

## Step 10: job preferences (state PREFERENCES)

Ask in one message, suggesting answers from the profile:

1. Target roles (suggest 2–4 from the profile).
2. Seniority (suggest from total experience).
3. Work mode: remote, hybrid, on-site (several allowed).
4. Countries or cities, and regions (`global`, `gcc`, `sa`, `eu`, `uk`, `na`, `apac`).
5. Screening answers: work authorisation per country of interest, visa sponsorship needed, notice period, current salary (optional), expected salary with currency, willing to relocate, earliest start date.
6. Companies never to apply to (suggest the current employer).

Write roles, seniority, work modes, regions, locations and blocklist into `config.yaml` under `search`. Write screening answers to `profile/answers.json`:

```json
{"answers": [{"key": "notice_period", "question_examples": ["What is your notice period?"], "answer": "1 month",
              "scope": "generic", "source": "user", "updated_at": ""}]}
```

## Step 11: LinkedIn ready

Jobs are searched on LinkedIn only (`config.yaml` already has `platforms.enabled: [linkedin]`). Tell the user in one line that LinkedIn's terms don't allow automated use and the account could be restricted, so the skill applies at most `limits.daily_cap` (default 10) a day, one at a time, with pauses. They can change the number with `/job-apply limit <n>`.

Then, in the `jobapply-browser` tools (do [browser-setup.md](browser-setup.md) first if there is no `browser` checkpoint):

1. **Logged in?** Connection test from browser-setup.md. Logged out → the user logs in by hand in that Chrome tab; you never type a password.
2. **Set up the LinkedIn profile once, before any applying** (Easy Apply sends it with every application):
   - Copy `resume/base_resume.pdf` to `resume/<First>_<Last>_Resume.pdf` (names from `profile.identity.full_name`, spaces → underscores, ASCII), and upload that file at `https://www.linkedin.com/jobs/application-settings/` (resume section). Read back that it's listed.
   - Open `https://www.linkedin.com/in/me/`. Check the location shows the user's city and country from `profile.identity`. If it is empty or a different place, fix only the location in the intro edit dialog, save, read it back.
   - Don't change anything else on the profile (headline, about, skills, experience). If something looks clearly wrong against the profile, tell the user in one line instead.
3. `S/state.py checkpoint linkedin_profile '{"ok": true}'`, `S/state.py set READY`.

Tell the user they're ready, with the three commands they need: `/job-apply start`, `/job-apply watch`, `/job-apply stop`. Suggest a first test with `dry_run` (fills everything but doesn't press Submit) and offer to switch it on for them.

## Updating the profile (`update-profile`)

Only in state READY. If a run is going, say you'll do it after the run ends.

1. Ask what to add: a project, a job, a certificate, a correction.
2. Append to `raw_input.md`, update `intake.json`.
3. `state.py set ANALYZING`. Re-read only new projects and projects whose HEAD changed (step 5). Reconcile new items (step 6).
4. Rebuild profile and base resume (steps 7–8), show what changed, then go USER_REVIEW → PREFERENCES → READY (preferences stay as they are unless the user wants changes). Upload the new resume at LinkedIn's application settings again (step 11, item 2, first bullet).
