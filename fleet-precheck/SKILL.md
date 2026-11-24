---
name: fleet-precheck
description: Use before release to scan selected text for known sensitive terms or overstated claims, verify each hit and the final artifact, and route the release decision to the authorized person.
---

# Check before release

Run the project's term list against the selected release files. A reviewer checks each hit in context and inspects the final artifact. The person authorized to release it decides whether it goes out. If the check is delegated to a roster member, the project manager verifies the results.

Identify the files intended for this release, then ask the reader to supply or confirm their own term list. The project maintains rules for internal names, personal names, paths, amounts, and overstated claims. The [example list](terms.example.txt) demonstrates the format only; it cannot replace the project's real rules. Keep sensitive rules in the reader's own project and exclude them from public materials.

Each line has three fields separated by a single tab: severity (`ERROR` or `WARN`), a Python regular expression, and an explanation. Blank lines and comments starting with `#` are skipped. Matching is case-sensitive unless the expression uses `(?i)`. Explain how to handle a match without repeating sensitive text. Pipes inside an expression do not separate fields.

Run from the skill package root. Replace the final file argument with the explicitly selected release files; the script does not search directories recursively. The example checks an English project file; for a Chinese project, use its existing Chinese filename:

```bash
python -X utf8 skills/fleet-precheck/scripts/precheck.py --terms skills/fleet-precheck/terms.example.txt /path/to/my-project/fleet/annual-report-summary.md
```

Report each match's severity, file, line number, and explanation without echoing the source line. Markdown examples inside backtick or tilde code fences are skipped; inline code is checked. An unclosed fence makes the rest of the file count as code, so also inspect fence correctness manually. A human must check code intended for release too; do not use fences to hide ordinary prose.

Exit code 0 means no `ERROR` was found; `WARN` findings still need attention. Code 1 means there are errors. Code 2 means configuration or file reading failed and the check is invalid. Fix errors, resolve each warning or record why it is retained, and rerun after edits. Confirm that every release file was checked, then verify factual sources, quotations, authorization, and final rendering. No term-list matches does not establish correctness or release authorization. CLI diagnostics follow the detected project language; use `--lang en` or `--lang zh` to override detection. CLI help includes both languages.

Return the check command, output summary, resolved findings, and matters awaiting a human decision. Guide the reader through any blocker, one step at a time:

- No valid term list: explain that the example cannot establish this project's release rules, then ask here only for the existing list's project-relative path or “no list”. If none exists, prepare the list inside the chosen project and propose the first non-sensitive rule for confirmation. For a sensitive rule, give the actual local file path and a prepared line with only the sensitive part left to fill; ask the reader to fill that part in their own editor and reply “done”, never paste it here. Check the saved rule's format, then guide the next needed rule in the same way. For an invalid list, identify the first line to fix without echoing its contents, give the specific fix, ask for “fixed”, and check it again before running the check.
- Unreadable file: first check the selected path without changing it. If help is needed, give the exact path and ask the reader to open it in their own file manager and reply “opens” or “not found”, without pasting its contents. Retry reading after the reply. If it only opens on the reader's machine, give the full check command with the actual paths for their own terminal; ask for the exit code and diagnostics only, then verify those results cover the selected files.
- Insufficient authorization: explain the exact action and why it needs a human decision, then ask the decision maker here to reply with explicit approval or the permitted scope for that action. Verify the reply against the project's approval table and record it before continuing. Direction, external commitments, spending, and deletion require human approval; the external release itself follows that table.

Do not request keys or passwords. If a reply does not resolve the current blocker, give the next specific step; do not restart the questions.
