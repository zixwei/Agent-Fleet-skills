# Key concepts and terms: selection, provenance, marking, colour, consistency

Macros: `templates/preamble.tex` block B4b (`\concept{}`, one orange colour, switch `\showconcepts`). Lint: `scripts/check_paper.py`, sections `== concepts` and `== terms`. The term table and its provenance rule: §3. Evidence labels as in SKILL.md. All example terms are synthetic, taken from the invented running methods of `paper_types.md` (DIVA, PriorTilt, CaliLoss, SynthBonus).

## 1. What the source papers do (measured on the live chapters, comments excluded)

- **About four in five marks sit in the Introduction**; the rest mark a concept at its formal definition in the Preliminary or Methodology. **None** in the abstract, experiments, conclusion, appendix, captions, headings or math `[all]` (papers that use the macro).
- **The count fell over time**: about 20 marked uses per paper `[older]`, 5-11 `[current]`. Follow the current range.
- **Most marked terms are borrowed, not invented**:
  - field terms taken from prior work and cited in the same sentence (in the style of *label shift*, *curriculum learning*, *potential-based shaping*);
  - the paper's own names are composed from existing vocabulary (in the style of *diversity scorer*, built from "diversity" and the standard "scorer"; *ensemble penalty*; a pair of plain field words such as *target fit / prior fidelity*);
  - genuinely new coinages are rare: at most 2 per paper.
- **Marked at first mention**, re-marked only for a deliberate contrast of a pair or at the formal definition of the core object (e.g. the corrected prior $\boldsymbol{\pi}^{\star}$ when the Methodology defines it). Never on every occurrence.
- **Never combined with bold or italics** `[all]`: the colour is the only marker.
- Slips not to copy: adverbs or generic words marked as concepts (*solely*, *overall*, *measure their relevance*). A concept is a noun phrase the paper keeps using.

## 2. Rules

### 2.1 Budget
- **Core concepts** (the ideas the paper is built on): **2-5, never more than 6.** Typical set: the twin concepts or named components of the method (DIVA *difficulty scorer / diversity scorer*; PriorTilt *target fit / prior fidelity*), the key object (PriorTilt *corrected prior*), and the prior paradigm the paper builds on (DIVA *one-pass filtering*).
- **Framing concepts** (borrowed field terms needed to state the problem): **≤ 6.**
- **Total marked uses in the main text: target 8-15, hard cap 20. Distinct marked phrases ≤ 12.** Current papers sit at 5-11.
- Placement: the Introduction (most), plus the first formal definition in the Preliminary or Methodology. Nowhere else.

### 2.2 Borrow first, compose second, coin last (the most important rule)
For every concept, in this order:
1. **An established term exists** → use it verbatim as the literature does (spelling, hyphenation, abbreviation) and cite its source in the same sentence: `\concept{label shift} \citep{...}`, `\concept{potential-based shaping} \citep{...}`. If the field uses several names, pick the most common one and give the alias once (`\aka`).
2. **Only a composition is needed** → build the name from existing terms, preferably from the closest prior work (a "scorer" from the data-selection literature + "diversity" → *diversity scorer*), and say where each part comes from, with a citation key.
3. **Nothing fits** → coin a new term, at most 2 per paper, introduced once with "(termed X)" or a definitional apposition; plain words, no hype, no acronym unless it is used ≥ 3 times.
- "(termed X)" is reserved for the core concepts: its count in the main text should not exceed the number of core concepts (source papers: 1-3). A named pair of core concepts is introduced with ONE of three phrasings per paper: "(termed X)", "By X, we mean ..." or "X, meaning that ..." (`prose.md` §1 rule 10). `check_paper.py` prints the "(termed" count next to the core-concept count.
- Never invent a new name for something the literature already names: reviewers read it as either ignorance or inflation. Before coining, read the cited papers' full text for an existing term (§3); a reference corpus of house papers only says how the house writes, not what the field calls a thing.
- Record the provenance of every concept in the concept table (§2.5) with its evidence: citation key + a quote from that paper for borrowed terms, the citation key of each part for composed terms, or "coined". A provenance field without a key and quote is a claim, not a check (§3).

### 2.3 Consistency
- **One concept = one exact phrase = one abbreviation = one symbol** for the whole paper, including abstract, figures, captions, section titles and appendix (these are not *marked*, but they use the same words).
- After first use, never switch to a synonym ("corrected prior" stays "corrected prior", not "adjusted prior" / "new prior"); an abbreviation, once introduced, is used consistently afterwards.
- Figures label the concept with the same words.
- One thing = one name also holds for terms that are not marked: grep the draft for the synonyms of every term-table row (§3) before each review (e.g. quality classifier / quality scorer / quality model; decision accuracy / pairwise accuracy).
- The terminology-unification revision habit applies (`prose.md` §9 rule 11): if two names drift apart in a draft, pick one and replace all.

### 2.4 Echo: core concepts must come back
- A concept marked in the Introduction is only useful if the paper keeps using it. Every **core** concept is echoed later with the **same phrase, unmarked**:
  - in the Methodology: subsection titles or bold run-in heads carry the concept names (PriorTilt subsections "Target Fit via Expected Log-Likelihood" / "Prior Fidelity with a Divergence Budget"; DIVA run-ins `\textbf{Difficulty scorer.}` / `\textbf{Diversity scorer.}`);
  - in the Experiments: the RQ text or the finding sentences name the concept they test (e.g. "we isolate the diversity scorer ...");
  - in the Conclusion: the twin concepts are restated, often as "(\ie <concept>)" (`prose.md` §1 rule 10).
- Framing terms need no echo, but when they recur they keep the same wording.
- The lint prints an echo table (mentions per section) and flags a core concept that never reappears after the Introduction, or is missing from the Experiments or the Conclusion.

### 2.5 Concept table (write it before drafting the Introduction)
Put it in the main file right after `\input{preamble}` (or in the paper plan), one comment line per concept:

```latex
% concept: diversity scorer | core | composed: scorer (survey2024select "<quote that uses 'scorer'>") + diversity (plain) | sets a sampling quota per cluster
% concept: label shift | framing | borrowed: shift2019detect "<quote that uses 'label shift' in this sense>" | class priors differ between training and deployment
% concept: prior fidelity | core | coined | the corrected prior stays within a divergence budget of the training prior
% term: quality classifier | borrowed: filter2023quality "<quote>" | small model that scores each document | other senses: none
% term: run-to-run variation | borrowed: eval2025noise "<quote>" | standard deviation of the score across data-order seeds | other senses: "seed noise" = initialization seed
```
(Keys and angle-bracket quotes are placeholders; in a paper each is the real key from the `.bib` and a quote copied from that paper.)
- Fields: exact phrase | core or framing | provenance | one-line definition. Provenance is one of: `borrowed: citekey "quote <= 15 words"` (the cited paper uses the same phrase in the same sense), `composed: part (citekey "quote") + part (plain)` (every part borrowed or plain), `coined` (at most 2 per paper, concepts and terms together).
- `% term:` lines carry the terms that are not marked with `\concept{}` (metric names, recurring technical nouns, component names): phrase | provenance | definition, with an optional last field `other senses: ...`. Together with the concept lines they form the term table of §3.
- The lint reads these lines: every `\concept{}` must match a declared phrase, and every declared core concept must be marked once and echoed (`== concepts`); every row needs its citation key (looked up in the paper's `.bib`) and, when borrowed, a quote; every "(termed X)" needs a row; more than 2 coined rows warn (`== terms`).

### 2.6 Colour (default ON, one colour)
- All concepts use **one colour: orange** (`conceptcolor` = xcolor `orange`). Per-concept colours are not used: a single hue reads cleaner while drafting.
- `\showconceptstrue` (default) while drafting and reviewing; `\showconceptsfalse` for the submission and camera-ready PDF, which renders everything black, as in the published source papers `[all]`. Keep the wrappers in the source.
- Co-author edit macros (`\coA{}`, `\coB{}`) may also be coloured while drafting; if one of them is orange too, turn it off (black) while reviewing concepts.

## 3. Term table: every key term needs a source in the field literature `[skill choice]`

**Why.** In a draft audited while building this skill, five key terms were coined by the authors: the cited papers never used them, or used the words in another sense (a name for a test run built on "probe", a component named "X-aware", an update called "base", a decision called "admission"). Four earlier audits missed them, for four reasons this section removes: (1) provenance in the concept table was self-declared (a citation key next to a coined word passed as a source); (2) the coined terms sat on a protected "fixed terms" list, which exempted them from review; (3) wording was checked only against a corpus of house papers, never against the field literature; (4) a word attested in a different sense passed ("probe" exists in LLM papers, but means a linear probe).

### 3.1 The table (build it before drafting)
List **every key term**: the concept-table entries, every "(termed X)", method and component names, metric names, and the recurring technical nouns (anything used 3 or more times that a newcomer might look up). One row per term:

| Field | Content |
|---|---|
| exact phrase | as printed in the paper |
| meaning | one plain sentence |
| verdict | **borrowed** (a cited paper uses the same phrase in the same sense) / **composed** (every part is borrowed or plain, and the combination is clear to a newcomer) / **plain** (an ordinary phrase, clear without a definition) / **coined** (none of these) |
| source | a quote of at most 15 words + the citation key, from the **cited field literature**, in the **same sense**; for composed terms, the source of each borrowed part |
| other senses | what the word already means elsewhere in the field (e.g. probe = linear probe) |

Keep the table in the main file as `% concept:` lines (marked concepts) and `% term:` lines (all other rows) (§2.5), so `check_paper.py` (`== terms`) can read it; a longer audit may sit in a notes file, but the rows in the paper carry the key and quote.

### 3.2 Rules
1. **Prefer the terms the cited papers themselves use**, with their spelling and hyphenation. A private synonym for something they already name is a coinage.
2. **Coin only when nothing fits**, at most 1-2 per paper, and mark the row "coined".
3. **Fixed is not verified.** A "fixed terms" list only means a term is not reworded while polishing. It never exempts the term from this check: every fixed term gets a row with a source like any other.
4. **Check senses, not spellings.** A word the field already uses in another sense is a **sense clash**, worse than a coinage, because readers think they already know it. Known clashes (the lint flags the greppable ones in live text):

| Word | What it already means in ML / LLM papers | Write instead |
|---|---|---|
| probe / probing | a linear probe (a classifier on hidden states), or knowledge probing | run, test, proxy, method |
| base (base update, base delta) | the base model, in merging papers | name the data the update was trained on |
| pre-registration / pre-registered | a protocol time-stamped before any experiment | fixed in advance (unless a dated record exists) |
| seed noise | noise from the initialization seed | run-to-run variation, data-order noise |
| screening | filtering documents | say what is screened (datasets, candidates) |
| X-aware (e.g. share-aware) | awareness of sharing (HCI, systems) | say what the step does |
| ablation | removing a part | say what was added or varied; for data runs, the cited paper's term |

5. **One thing = one name.** Grep the draft for synonyms of every row and keep the table phrase (§2.3).
6. **Re-audit whenever terms are added or renamed** (a newcomer review, a rewrite, a rename pass): a new row needs its source before the term enters the text.
7. **Read the cited papers' actual text** (arXiv HTML or PDF), searching for the exact phrase and close variants. A house corpus shows how the house writes; only the field papers show whether the field uses a phrase, and in which sense. An abstract or a summary is not enough.

### 3.3 Procedure
1. From the outline, list the candidate key terms (the concept candidates plus the component, metric and recurring nouns).
2. For each, search the full text of the 3-6 closest cited papers for the phrase and its variants; copy the quote and the citation key; note other senses.
3. Assign the verdict. For a coined or clashing row, find the term those papers use (or a plain phrase) and rename before drafting.
4. Write the rows (`% concept:` / `% term:`) and run `check_paper.py`; the `== terms` block must show no missing keys or quotes, at most 2 coined rows, and no sense-clash words.
5. Repeat steps 2-4 for every term added later.

### 3.4 Worked example (synthetic): renaming the draft terms of DIVA
DIVA (`paper_types.md`, Type W) filters a pretraining pool with a difficulty scorer and a diversity scorer. Its first draft named things freely; the term table renamed them before the Introduction was written. Keys and quotes are placeholders for the ones copied from the cited papers.

| Draft term | Verdict | Renamed to | Source of the new term |
|---|---|---|---|
| quality probe (a small model that scores documents) | coined + sense clash (probe = linear probe) | quality classifier | filter2023quality "<quote using 'quality classifier'>" |
| base pool | composed, clashes with "base model" | unfiltered pool | filter2023quality "<quote using 'unfiltered pool'>" |
| cluster admission | plain word used as a coined label | keeping a cluster; inclusion rate | plain |
| seed noise (runs differ only in data order) | borrowed word, different sense | run-to-run variation | eval2025noise "<quote using 'run-to-run variance'>" |
| pre-registered thresholds | sense clash (overclaim) | thresholds fixed in advance | plain |
| diversity ablation (adding the diversity scorer) | wrong sense (ablation = removal) | with and without the diversity scorer | plain |
| pairwise accuracy / pairwise decision accuracy | synonym drift | decision accuracy | eval2025noise "<quote defining decision accuracy>" |

Kept after the check, each with a quote: difficulty scorer and diversity scorer (composed: "scorer" from the data-selection survey), one-pass filtering (borrowed).

## 4. Procedure
1. From the outline, list candidate concepts (≤ 12). Classify each as core or framing.
2. For each, run §2.2 (borrow → compose → coin) and §3 (read the cited papers, copy key + quote, check other senses); write the concept table and the `% term:` rows with their sources.
3. Draft; mark each concept at its first mention in the Introduction (or its first formal definition) with `\concept{}`; never mark it again.
4. Echo every core concept with the same phrase in the Methodology headings or text, the Experiments RQ text or findings, and the Conclusion.
5. Run `check_paper.py`: fix over-budget counts, undeclared or unused concepts, marks outside the allowed sections and missing echoes (`== concepts`), and missing sources, coined rows above 2, unlisted "(termed X)" and sense-clash words (`== terms`). Re-run §3 for any term added since.
6. Before submission: `\showconceptsfalse`, rebuild, confirm the PDF is black.
