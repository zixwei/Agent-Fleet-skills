# Self-training

A lab teaches only when it changes the next decision. This loop turns a finished exercise into a method note, a review prompt, and a better default.

## The loop

1. Finish a lab task and save the evidence.
2. Write the finding in one sentence.
3. Name the root cause, not the symptom.
4. Write the fix and the check that would have caught it.
5. Add it to the learned set:

```bash
./beril reflect --lab web --finding "Any user can read another profile" \
  --root-cause "object id comes from the query and is never compared with the session" \
  --evidence labs/web/evidence/idor.txt \
  --fix "scope the query by session uid and add an object-owner test"
```

6. Review three random notes twice a week:

```bash
./beril reflect --review
```

7. When a note survives three reviews, rewrite it as a method card in `references/techniques.md`.

## What makes a good note

| Bad | Good |
|---|---|
| "IDOR found" | "Profile read used the query id, not the session id" |
| "XSS" | "Search reflected raw into the HTML body, so a tag executes" |
| "SQLi in id" | "f-string built the query, `OR 1=1` returned the first row" |
| "Fixed it" | "Parameterized query plus a test with `' OR '1'='1`" |

## Root cause prompts

- Which check was missing, and where should it live?
- Did the code check a different value than the one used later?
- Did two parsers disagree?
- Was the state stale, shared, or trusted from the client?
- Was the size derived from user input?
- Was the boundary wrong: tenant, user, role, or object?

## Fix prompts

- What is the smallest fix at the right layer?
- What test fails before the fix and passes after?
- Does the fix cover the sibling endpoint?
- Does it break a legitimate flow?

## Spaced repetition

Keep 20 prompts, not 200. A prompt is a question, not a paragraph.

- Which cookie flag stops JavaScript from reading the session?
- Which parser disagreement enables request smuggling?
- What does full RELRO block?
- Why is a stack canary not a bounds check?

If the answer takes more than 20 seconds, reopen the lab.

## Feeding the catalog

A learned note becomes a catalog entry only when it has:

- a public or local source;
- a precondition;
- a minimal reproduction;
- an observable proof;
- a failure mode;
- one sentence on transfer.

Until then it stays in `data/learned_methods.jsonl`.

## Evidence

The evidence path is not decoration. A note without a request, crash log, or diff is a memory. Memories drift.

## Weekly review

1. Read the three random notes.
2. Run one of them again from memory.
3. Update the note if the behavior changed.
4. Delete a note that no longer applies.
5. Add one new drill from `references/exercises.md`.

The goal is a smaller, sharper set over time.
