# Writing rules: resumes, cover letters, form answers

Everything here is written in English, for a human recruiter who reads fast. Every piece passes `scripts/lint_text.py` before use, and every resume passes `scripts/profile_tools.py check-resume`.

## Truth first

- Use only skills with status `verified` or `user_attested` in `profile/evidence_ledger.json`.
- Use only highlights and numbers from the ledger or the user's own words. Never estimate, round up or invent a metric.
- Titles, companies and dates are copied exactly from `profile/profile.json`.
- If the job asks for something the user doesn't have, leave it out. Never add it, never imply it.

## Tailoring is judgment, not keyword matching

Tailoring changes **emphasis**, never **membership**:
- Keep the user's core skills on every resume, whether the job mentions them or not.
- Put what this job cares about first, and give the most relevant projects more bullets.
- Keep adjacent experience when it helps the reader. Backend work on a Flutter role shows the user understands the API side.
- Use the job's wording when it means the same thing ("RESTful services" for "REST APIs").
- Leave out only what adds nothing for this reader, such as trivial tools on a senior role.
- Adjust the summary's first sentence toward the role. Keep it true.

Never: add a skill, change a title or date, inflate years, invent results, or copy sentences from the job description.

## Length budgets

| Output | Budget | `lint_text.py --kind` |
|---|---|---|
| Cover letter (document or long text box) | 120–180 words, 3 short paragraphs | `cover_letter` |
| Email application body | 90–180 words: greeting, 3 paragraphs, footer ([email-writing.md](email-writing.md)) | `email_body` |
| "Why us", "About you", "Anything else?" field | 2–4 sentences, at most 90 words, no greeting, no sign-off | `field_long` |
| Short answer field | One line, at most 25 words | `field_short` |
| Resume summary | 2–3 sentences, at most 70 words | `summary` |
| Resume bullet | One line: action, what, result. At most 28 words | `bullet` |
| Any field with a character limit | At most 90% of the limit (`--max-chars`) | any |

## Cover letter shape

1. One or two sentences: the role, and the single strongest reason the user fits it.
2. One or two concrete facts from real projects or jobs that match what this job needs, with a number only if the ledger has one.
3. One closing line. No begging, no "I look forward to the opportunity to discuss how I can contribute".

## Email application body

Emails have their own standard: [email-writing.md](email-writing.md). Greeting by the hirer's name or the company, a paragraph on who the user is, a paragraph on what they can do for this job, a resume-and-closing line, then "Best regards," with name, email and phone. Read it before writing any email.

## Tone

Plain, direct, specific. Short sentences. First person. Write like a capable engineer writing a quick note, not like a brochure. The lint flags phrases such as "I am excited to", "thrilled", "passionate about", "delve", "leverage", "synergy", "proven track record", "fast-paced environment", "dynamic", "results-driven", "go-getter", "I believe I would be a great fit", "in today's ... world", more than one em dash, exclamation marks, and three-adjective lists. Fix every flag by rewriting, not by swapping in a synonym.

## Resume data (input to `render_resume.py`)

The layout is fixed and always the same (`assets/resume_template.html`, the user's chosen design): centred name, blue headline, contact line, blue section titles with thin rules, dates on the right, one column. Never change the template per job; only the content changes.

```json
{
  "name": "Full Name",
  "headline": "Python Developer and Full-Stack AI Engineer",
  "contact": {"location": "City, Country", "phone": "+92 300 0000000", "email": "",
              "links": [{"url": "https://linkedin.com/in/..."}, {"url": "https://github.com/..."}]},
  "photo": null,
  "summary": "3–4 sentences.",
  "experience": [{"title": "", "company": "", "location": "Remote (UAE)", "start": "May 2025", "end": "Present",
                  "bullets": [""]}],
  "projects": [{"name": "Project Name", "subtitle": "What it is in a few words", "start": "Apr 2026", "end": "Jun 2026",
                "description": "One line: what the project does.", "bullets": [""],
                "stack": ["Python", "FastAPI"], "link": "example.com"}],
  "skills": [{"group": "Python and Frameworks", "items": ["Python", "Django", "FastAPI"]}],
  "education": [{"degree": "Bachelor of Science in Computer Science", "detail": "Specialization in AI",
                 "institution": "University, Campus", "start": "2020", "end": "2024", "bullets": [""]}],
  "certifications": [{"name": "", "issuer": "", "year": ""}]
}
```

- **Experience line:** `Title` in bold, then `, Company, Location` in grey, dates on the right. `end` missing means "Present".
- **Project line:** `Name:` in bold, subtitle in grey (`"subtitle_join": " "` drops the colon, for subtitles like "for Event Photo Delivery"). Use `"date": "2026"` instead of `start`/`end` for a single year. `description` is the italic line. `stack` and `link` make the "Stack: … / link ↗" line; both optional.
- **Links** show without `https://` (`linkedin.com/in/...`). A link may be `{"url": "...", "display": "..."}` to show different text.
- **Education:** degree in bold, `detail` in grey, institution on the next line, optional bullets.
- `photo` is a file path, used only when `config.yaml` has `resume.include_photo: true`.
- **Section order:** summary, experience, projects, technical skills, education, certifications. Set `"projects_first": true` when the user has under a year of work.
- **Length:** one page for under 2 years of experience, at most two pages otherwise. Cut weaker bullets before shrinking anything. Experience and project entries never split across pages, so trimming a line inside an entry often doesn't reduce the page count: to save a page, drop or shorten a whole weaker entry. Check `pages` in `render_resume.py`'s output after each change.
- Tailored resumes are saved in the job's folder as `<First>_<Last>_Resume.pdf`. Never put "tailored" or the company name in the file name.

### Why this layout passes ATS parsers

Keep these properties when editing the template or the renderer:
- Real text only, one column, in reading order. No images of text, no text boxes, no tables for layout (the skills list is a definition list, read as "label, items").
- Nothing absolutely positioned: positioned elements are written to the PDF out of order and scramble the text an ATS extracts.
- The bundled TrueType font (Arimo) embeds as TrueType. Fonts Chrome turns into Type 3 can't be read by some ATS parsers.
- Standard section titles (Professional Summary, Experience, Projects, Technical Skills, Education, Certifications).
- Dates as "Mon YYYY – Mon YYYY", contact details as plain text, links as real hyperlinks.
- After any change to the template, check with `pdftotext -raw` that every bullet sits under its own job, and with `pdffonts` that no font is Type 3.
