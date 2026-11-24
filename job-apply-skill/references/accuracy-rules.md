# No wrong data, ever

A wrong answer on an application is embarrassing and can't be taken back. These rules apply to every field a worker fills, and to every resume and letter.

## Where values come from

Only from:
1. `profile/profile.json` (identity, links, experience, education, certifications, projects, skills)
2. `profile/answers.json` (screening answers the user gave)
3. `profile/evidence_ledger.json` (skills and their evidence)
4. The tailored resume and cover letter made for this job

Never from memory, guesses, the job description, or what "most candidates" answer.

## Before filling a field

1. **Read the label, help text and options.** Work out exactly what is asked.
2. **Match it to one source value.** Know the difference between similar questions:
   - "Years of experience with React" → from the ledger evidence for React (projects and jobs that used it, with their dates). If it can't be worked out with confidence, it's unsure.
   - "Total years of experience" → `profile_tools.py years`.
   - "Current salary" and "Expected salary" are different answers. Currency matters.
   - "Are you authorised to work in X?" and "Do you need sponsorship?" are different answers, per country.
   - "Notice period" vs "Earliest start date".
3. **Format it the way the field asks.** Phone with or without country code, date format, URL with `https://`, numbers without text where a number is asked.
4. **Dropdowns, radio buttons, checkboxes:** choose an option only if it clearly matches the user's answer. "5+ years" matches 6 years. Nothing close enough → unsure.

## When unsure

| Field | Do |
|---|---|
| Optional and unsure | Leave it empty |
| Required and unsure | Don't guess. With `ask_for_help` on, ask the user the exact question (worker returns "help ... question: ...") and save their answer to answers.json; otherwise the job goes to the manual list with the exact question in the reason |
| Required, and the answer would be "No"/"I don't have it" for a real requirement | Answer honestly. Never tick "Yes" to pass a filter |
| Legal declarations ("I confirm the above is true", privacy consent) | Tick only the standard consent needed to submit. Anything else (marketing, talent pools) stays unticked |
| Demographic questions (gender, ethnicity, disability, veteran) | Use the user's saved answer if there is one; otherwise "Prefer not to say" or "Decline to self-identify" when offered; otherwise leave empty |

## Read-back check (before every Submit)

1. `browser_snapshot` the whole form, scrolling through every section and step.
2. For each filled field, compare what's on the page with the source value. Watch for autocomplete changing values, wrong dropdown choices, truncated text, and leftover text from the site's own autofill.
3. Check the uploaded file is this job's resume (`<First>_<Last>_Resume.pdf` from this job's folder) and the cover letter matches this company and role.
4. Check no required field is empty and there are no validation errors on the page.
5. Any mismatch you can't fix with certainty → don't submit, send to the manual list with what was wrong.

Write what was filled into `form_answers.json` in the job folder, as `[{"label": "", "value": "", "source": "profile.identity.phone"}]`.

## Email applications (before every send)

1. **Recipient:** the exact address the posting gave. Never guess one (`careers@company.com`, `firstname.lastname@company.com`) and never pattern-build one from a name or domain.
2. **Subject:** always set, matching the format the posting asked for if it asked for one (e.g. a reference code).
3. **Attachments:** exactly this job's tailored resume PDF, plus a cover letter PDF only if one was made for this job. Never attach another job's files.
4. **Sender:** always the address configured in `~/.job-apply/.env` (`GMAIL_ADDRESS`). Never any other account.
5. **Recipients:** only the address(es) the posting itself gave. No `--cc` unless the posting explicitly lists more than one recipient. Never guess a CC.
6. **Body:** passed `lint_text.py --kind email_body`, and says nothing the resume doesn't already say in more detail (see email-writing.md).
7. **Greeting:** a named hirer or "Hi <name>," only when the posting itself actually names someone. Never invent a name. No name given → the company name; no company name either → "Hi Hiring Team,".
8. **Footer:** "Best regards," then full name, email and phone copied exactly from `profile.identity`, same as any other field.
   **Paragraph 2:** every capability named is `verified` or `user_attested` in the ledger. Nothing the job asks for is claimed unless the user really has it.
9. Any doubt on any of the above → don't send, manual list with what was unsure.
