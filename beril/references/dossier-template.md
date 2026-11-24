# Dossier template

Use this structure when the built-in dossier is not enough, or when you need to hand the research to someone who will not run the scripts.

The rule for every section: a claim without a link is a note to yourself, not a finding.

## Header

```markdown
# <Target or topic> intelligence brief

Date: 2026-11-22
Author: <name>
Scope question: <one sentence>
Sources reached: <count and list>
Sources blocked: <list and why>
```

The scope question keeps the brief honest. "External attack surface for example.com" is a scope. "Everything about example.com" is a wish.

## 1. What changed

Three to six bullets. Only include a change you can support with a dated source.

- New tool or release that alters the workflow.
- New research that changes a known technique.
- A program scope change.
- A disclosed report that matches the target's stack.
- A dead source that the reader should stop using.

If nothing changed, write "No material change since <date>" and move on.

## 2. Coverage

| Layer | Rows | Sources | Gaps |
|---|---:|---|---|
| Threads and reports | | | |
| Experts | | | |
| Tools | | | |
| Methods | | | |

Name the gaps. "No public reports for the mobile API" is useful. A blank cell is not.

## 3. Signals

For each signal:

```markdown
### <Short title>

- Source: [title](url), <date>
- Confidence: A / B / C
- Why it matters: <one sentence>
- What to test: <one action>
```

Signals are leads, not findings. Keep them separate from confirmed behavior.

## 4. Expert queue

| Expert | Why this person | Read first | Time |
|---|---|---|---|
| | | | 20 min |

Cap the queue at five people. A reading list with 30 names is a library, not a queue.

## 5. Tool shelf

| Phase | Tool | Command | Why |
|---|---|---|---|
| Recon | subfinder | `subfinder -d target -silent` | Passive subdomain pass |

Only include tools you ran or verified against a current README. Mark the rest as "candidate".

## 6. Method cards

For each card:

```markdown
### <Method name>

When: <precondition>
Signal: <what you see>
Steps:
1. ...
2. ...
Proof: <the evidence>
Pitfalls: <the false positive>
Source: [title](url)
```

Six cards is a good brief. Two cards with detail beat ten cards with headings only.

## 7. Target notes

Free-form, but keep it structured:

- Stack and versions.
- Authentication model.
- Interesting endpoints or hosts.
- Known defenses: WAF, CDN, rate limits.
- Open questions.

## 8. Next actions

A short list with an owner and a time box.

```markdown
- [ ] Run the passive recon pass on the new acquisition (30 min)
- [ ] Read the two most recent disclosed reports for the API gateway (20 min)
- [ ] Test the password reset host header with a controlled domain (15 min)
```

## 9. Appendix

- Commands run.
- Raw file names.
- Snapshot hashes or paths.
- Access limits.

## Style rules

- Sentence case headings.
- No em dashes or en dashes.
- Straight quotes.
- Link the exact page, not the site root.
- Separate observation from inference.
- Put the date on every claim that can expire.
- Keep paragraphs under five sentences.
- Use a table when the reader will compare items. Use prose when the reader needs the reasoning.

## Quality check

Before sending:

1. Every URL opens.
2. Every method card has a proof line.
3. No row contains a credential, token, or private report.
4. The coverage table matches the JSONL counts.
5. The "what changed" section names dates.
6. The next actions fit in one day.
