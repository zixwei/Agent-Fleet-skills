# Email writing standard

Every job-application email follows this exact shape, every time. It's short, but it is a complete, professional email that a hiring manager reads in under a minute and finishes knowing who the user is, what they can do for *this* job, and that the resume is attached.

Everything in it comes from the user's own data (`profile/profile.json`, `profile/evidence_ledger.json`, `profile/professional_summary.md`) and the job's own description (`job.md`). Nothing is invented. We only write to jobs that already passed the fit gate, so the real overlap is there: the email's job is to show it clearly.

## The shape

```
Subject: Application for <job title> - <full name>

Hi <hirer's first name | Company team | Hiring Team>,

<Paragraph 1: who the user is, professionally. 2 sentences.>

<Paragraph 2: what the user can do for THIS job, matched to its description. 2–3 sentences.>

<Paragraph 3: resume attached + one professional closing line. 1–2 sentences.>

Best regards,
<full name>
<email>
<phone>
```

Five blocks separated by blank lines: greeting, three paragraphs, footer. `lint_text.py --kind email_body` checks the length and that there are exactly five blocks.

## Greeting (dynamic)

Use the most specific thing the posting actually gives, in this order:

| The posting gives | Greeting |
|---|---|
| A named contact ("reach out to Sarah", "Hiring manager: Ahmed Khan") | `Hi Sarah,` / `Hi Ahmed,` (first name) |
| A company name, no person | `Hi <Company> team,` or `Hi <Company> Hiring Team,` |
| Neither | `Hi Hiring Team,` |

Never invent or guess a name. Never `Hi,` alone, and never plain `Hi team,` when the company name is known.

## Paragraph 1: who the user is (introduction)

Two sentences that tell the reader, technically, who is writing. Not personal details like address or age.

1. **Identity and experience:** name, professional identity (from `profile.json` headline), total experience (from `profile_tools.py years`), and the kind of systems they build.
2. **Where they are now:** current role and company, and what they own there, in one line. If the user has no current job, use their strongest recent project or role instead.

> I'm Sara Ahmed, a backend engineer with three years of experience building payment and billing systems. I currently work at Northwind Pay, where I own the Django services behind subscriptions and invoicing.

## Paragraph 2: what the user can do for this job (technical capabilities)

Two or three sentences, written for *this* posting. The reader should finish it thinking "this person can do what we need".

How to build it:
1. From `job.md`, pick the 3–4 things the job cares about most (must-haves, the first responsibilities listed, repeated keywords).
2. For each, find the user's real evidence in the ledger (`verified` or `user_attested` only). Drop any requirement the user doesn't have. Don't mention it, don't hedge around it.
3. Keep the 2–3 strongest matches. Lead with the job's own top priority.
4. Use the posting's own wording when it means the same thing ("LLM orchestration" if they say that, rather than "agent workflows").
5. Back at least one capability with something concrete the user built, and at most one number, only if the ledger has it.

> Your posting centres on reliable payment processing. I built Stripe billing where every payment is applied exactly once, and moved payment jobs onto their own Celery workers so long-running uploads never delay billing. I also own the tests around it, with 200+ on my current platform.

Never: list tools without saying what was done with them, copy sentences from the posting, claim a skill that isn't in the ledger, or repeat the resume's bullets word for word.

## Paragraph 3: resume and closing

One or two plain, professional sentences: the resume is attached, and the user would welcome a conversation.

> My resume is attached with the full details. I'd welcome the chance to discuss how I could contribute to your team, and I look forward to hearing from you.

Vary the wording slightly between emails, but keep it this simple. No begging, no "I believe I would be a great fit", no "thank you for your time and consideration" stacked on top.

## Footer

```
Best regards,
<full name>
<email>
<phone>
```

Each on its own line, copied exactly from `profile.identity`. No title, no quote, no links unless the user asks for them.

## Style rules (all checked by the lint)

- **No em dash (`—`) and no double hyphen (`--`) anywhere.** Use a comma, colon or full stop. This is one of the clearest signs of AI-written text.
- No exclamation marks.
- None of the AI-sounding phrases in `lint_text.py` (excited, thrilled, passionate about, leverage, proven track record, robust, seamless…).
- First person, plain English, short sentences. It should read like a capable engineer wrote it themselves in five minutes.
- Length: 90 to 180 words including greeting and footer.

## Subject line

`Application for <job title> - <full name>` with a plain hyphen. If the posting asks for a specific subject (a reference code, "Role - Your Name - Years of experience"), follow the posting exactly instead. Check it with `lint_text.py --kind field_short`.

## Every email is written fresh

Never reuse a previous email's text. Write each one for its own job, then reread it once and tighten it before linting: cut a weak word, make a capability more specific, check paragraph 2 really answers this posting. The examples above show the shape, not words to copy.

## Before sending

Run `lint_text.py --kind email_body` until it reports ok, then go through accuracy-rules.md "Email applications". Any doubt → don't send, manual list.
