# Writing guide: clear, plain, fast to scan

The reader wants to answer three questions in under a minute:
**Where are we? What changed since I last looked? Do I need to do anything?**

## 1. Structure: conclusion first

Every block follows **What → Why → Result → Next**.

| Bad | Good |
|---|---|
| "I explored the codebase, then looked at the loader, then tried several things…" | "Training is 2.3× faster: the data loader was the bottleneck; I switched to memory-mapped files." |

- Titles are outcomes, not activities: "Login works with SSO" not "Worked on auth".
- First sentence of the TL;DR answers "are we on track?".
- One idea per paragraph, ≤3 sentences. Use lists for anything enumerable.

## 2. Plain language

- **Define jargon on first use**, inline: "AUROC (how well scores separate normal vs. defective; 1.0 = perfect)".
- **Use an analogy for any non-trivial mechanism**, in one sentence:
  "The cache works like a bookmark: instead of re-reading the whole book, we jump to the last page we read."
- Prefer concrete over abstract: "3 of 48 tests fail, all in `parser/`" beats "some tests are failing".
- Numbers always come with a comparison: `0.912 → 0.947 (+3.5 pt)`, not just `0.947`.
- Say *why it matters*: "…so the nightly job now finishes before 8 am."

## 2b. The Method page: picture-first

The Method page is where the reader builds a mental model. **Test:** cover all the prose — can a smart
engineer from a different field still retell the approach from the figures alone? If not, a figure is missing.

Build it as a sequence of visuals, each introduced by an `h2` that states *the point*, with at most
two short lines of text between visuals:

| # | Visual | Component | Rule of thumb |
|---|---|---|---|
| 1 | **The idea in one line** | `.idea` (+ optional 2-line `.analogy`) | "X needs only Y." Bold headline + one muted sentence. |
| 2 | **What changed vs. the usual way** | two `.flow` rows; removed parts `.step.x`, new parts `.step.hl.new` | The reader sees the delta before the details. |
| 3 | **What the data looks like** | `.tokens` (sequence), `.kpis` (shapes), small `.figure` | Show tensor layout / input structure instead of describing it. |
| 4 | **The architecture** | `.diagram` (JSON → SVG) | 6–14 nodes. `kind`: `frozen` / `train` / `data` / `out` / `op`; `group` the repeated or frozen parts; image nodes (`"img"`) for real inputs/outputs; edge labels carry shapes. |
| 5 | **One real example, end to end** | `.imgflow` (real images/heatmaps + `.verdict`), or `ol.walk` when there are no images | Real file, real intermediate values, real final score. Pair a positive and a negative example when possible. |
| 6 | **The key formula** | `.formula` with `data-note` on each term | One equation, every term annotated in plain words. |
| 7 | **Why this design** | `.why` cards with `.ic` icon and `.rejected` line | Each card: choice → evidence number → rejected alternative. |
| 8 | **Limits** | `callout note` list | ≤3 bullets. |

Vividness rules:
- **A figure per idea.** If a paragraph explains a mechanism, replace it with a `.diagram` or `.flow`.
- **Real beats drawn.** Prefer actual outputs (run the project's own inference/plot code on 1–2 samples) over schematic boxes.
- **Numbers on the picture.** Put shapes on edges (`[1369×1024]`), scores in captions (`max 0.85`), thresholds in the verdict.
- **Same scale when comparing.** Images meant to be compared share one colour range; say so in a one-line caption.
- **Name the "aha"** in one bold sentence under the example ("only after summing 4 layers does the defect stand out").
- **Avoid:** paragraphs > 2 lines, a wall of module names, unexplained acronyms, marketing words.
- The validator counts prose outside components on the Method page and warns above ~450 CJK chars / ~225 words.

## 2c. The Alignment page: show what you believe, so it can be corrected

- **Restate, don't quote.** The goal card is your paraphrase; a verbatim copy hides misunderstandings.
- **Non-goals are mandatory.** "Not doing: real-robot tests, retraining the backbone" catches scope drift early.
- **One claim = one checkable sentence.** Bold title states the belief/decision; the muted line gives the
  reason, the rejected alternative, and the reversal cost. Bad: "Data handling choices". Good:
  "Evaluate on the 50-image subset, not the full 1,000 (full set was not on disk)".
- **Surface the ones that would hurt if wrong.** Prefer claims where a wrong answer wastes days
  (eval protocol, data split, what "done" means) over trivia.
- **Evidence tags are a promise.** `run` only if you executed it in this session or have the output file;
  otherwise `code`/`doc`/`guess`. Mixed evidence → use the weakest.
- **Diagram simplifications are decisions too.** If a figure omits or merges parts, add a claim saying so.

## 3. Honest status

- Use status colours consistently: ok = done/working, warn = partial/risky/unverified, bad = failed/blocked, info = neutral.
- Failures and dead ends get entries too (`data-tags="experiment" data-status="bad"`): what was tried, why it failed, what we learned.
- Separate **facts** (measured) from **guesses** (hypotheses). Mark guesses: "Hypothesis: …".
- Anything unverified gets `<span class="badge warn">unverified</span>`.

## 4. "Needs your decision" is sacred

If the user must decide something, put it in the Now block as a `callout decision`, with:
1. The question in one line.
2. 2–3 options, each with a one-line trade-off.
3. Your recommendation and why.
4. What you'll do by default if no answer.

Remove it once decided (record the decision as a `decision`-tagged timeline entry).

## 5. Visual-first, paged

Rule of thumb: if you wrote more than ~3 lines of prose, a visual is probably missing.
See `components.md` for the "what to show → which component" table.
One page = one question (Where are we? How does it work? Does it work? What happened?). When a page
grows past ~3 screens, move a topic to its own page instead of scrolling further.

## 6. Language

- Write in the language the user is currently using in the conversation. If they switch, write new content in the new language (don't translate old entries).
- Keep code, file paths, commands, metric/API names untranslated.
- Dates: `MM-DD HH:mm` for display, full ISO in `data-time`.
