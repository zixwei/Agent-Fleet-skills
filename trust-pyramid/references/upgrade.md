# Upgrade an installation

Use this when the user asks to upgrade Trust Pyramid in a project, or when
`node <skill>/tools/upgrade.mjs status` says an upgrade is available. `<skill>` is this skill's own
directory. The tool ships with the skill, so the newest skill always does the upgrade.

## How it works

Setup records `quality/trust-pyramid.lock.json`: the skill version, the templates that release
shipped, and for every installed file its template, its category, and the template text it was
installed from (base64, the merge base). `install-manifest.json` in the skill says where each
template goes and who owns it:

| Category | Examples | On upgrade |
|---|---|---|
| mechanism | `stop-gate.sh`, `trusted-check.sh`, `review-gate.mjs`, `pre-commit` | replaced; three-way merged if the project changed it |
| tailored | `check.sh`, workflows, hook settings, reviewer prompt, `AGENTS.md` | three-way merged |
| content | project lint rules, fixtures, ledgers, `smoke.mjs`, `feature-map.md` | never touched |
| append | `.gitignore`, `.gitattributes` | missing lines appended |

Three-way merge (`git merge-file`): the project's file, the base, and the new template. Changes on
different lines are combined. When both changed the same lines, the file gets conflict markers
(`<<<<<<< your project` ... `>>>>>>> trust-pyramid <version>`); nothing is chosen silently.

**Project sections.** Lines between `trust-pyramid:project-start <id>` and
`trust-pyramid:project-end <id>` (in a comment) belong to the project, such as the `CHECKS` table in
`scripts/check.sh`. Upgrades keep the project's copy of such a section as it is, so changed template
examples there never conflict. Keep the markers when editing these files.

## Steps

1. `node <skill>/tools/upgrade.mjs status --project .` and read the `CHANGELOG.md` entries between
   the installed version and this one (this skill's repository, "Upgrade notes"). Tell the user in
   plain language what changes. No lock file: see "Adopting an older installation".
2. Work in a separate worktree on a new branch, as in `add-rule.md`: gate files are protected
   (`permissions.deny`, CODEOWNERS, trusted CI), so the upgrade reaches the default branch only
   through a pull request the user approves.
   ```bash
   git worktree add ../<repo>-tp-upgrade -b chore/trust-pyramid-<version>
   ```
   Address files in the worktree by absolute path: permission rules check a relative path against
   the session's directory (the main checkout), whose deny list refuses gate files.
   A fresh worktree has no dependencies. Install them before running any check
   (`bash scripts/check.sh --setup` once `SETUP` lists them; before that, the project's own install
   commands, such as `npm ci --prefix lint`), or the checks fail on missing tools.
3. In the worktree: `node <skill>/tools/upgrade.mjs upgrade --project .` (add `--dry-run` first to
   show the user what will change). Exit code 3 means conflicts.
4. Resolve every conflict block. Keep the project's intent and the template's fix; when they truly
   disagree, prefer the project's version and say so. Then `upgrade.mjs check` must pass.
   Expect a conflict in every file where both the project and the release changed the same lines.
   A project that edited its templates, as setup asks, can get several (a field test from 0.3.0 to
   0.4.0 had eight, one block each). Each is a decision to explain, not an error.
5. Act on the rest of the report:
   - **New templates to install and adapt**: install them as in the matching setup phase, then
     `upgrade.mjs record --path <path>`.
   - **No longer shipped**: remove the file if nothing uses it (search first), else keep it.
   - **Missing**: the project moved or deleted a file; ask the user.
   - New or changed scripts: `git update-index --chmod=+x` for `*.sh` and `.githooks/*`.
6. If `quality/install/*` changed, tell the user to run `bash scripts/install-agent-hooks.sh`
   after merging (agents cannot write their own hook config), then start a new session.
7. Run every check (`bash scripts/check.sh --all`, the smoke flow) and `bash scripts/hook-status.sh`.
   Do not run `scripts/trusted-check.sh` in the worktree: it is the CI entry point and restores the
   base branch's gate files into the working tree, replacing the upgraded ones. CI runs it on the
   pull request. Commit, including `quality/trust-pyramid.lock.json`.
8. Open a pull request. In its description, in plain language: the version change, what was
   replaced, merged and added, and **each conflict: what the project had, what the new version
   has, and what you kept and why**. CI notes that protected files changed and checks with the
   base branch's copies; the new gates take effect after merge.

## Adopting an older installation

A project set up before upgrades were recorded (0.3.0 or earlier) has no lock file. Record it
against the release it was installed from, so the first upgrade is a real three-way merge:

```bash
node <skill>/tools/upgrade.mjs record --project . --from 0.3.0
```

`--from` reads that release's templates from the skill's git tag (`v0.3.0`), or downloads them from
GitHub when the skill is not a git checkout. To find the release: the project's setup commit or
`docs/QUALITY.md` often names it; otherwise compare a mechanism file such as
`scripts/trusted-check.sh` with `git show v<version>:skills/trust-pyramid/templates/scripts/trusted-check.sh`
for each tag and take the closest match. Then upgrade as above.

`record` leaves out files that sit at a template path but do not look like the template (fewer
than 10% of the template's lines remain): usually the project's own file, such as its own
`lint/oxlint.mjs`. Upgrades then never touch them. If one did come from Trust Pyramid, add it with
`record --path <path>`.

## For maintainers of this skill

- Bump `VERSION` and `.claude-plugin/plugin.json` together, add a dated `CHANGELOG.md` entry with
  "Upgrade notes" (anything the agent must do beyond the tool: renamed files, new secrets, new
  required checks), and tag the release `v<version>`.
- Every template must be in `install-manifest.json` (a test enforces it). Moving a template's
  install path is a breaking change: say so in the upgrade notes.
