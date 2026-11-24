# Structure: section blueprint, page budgets, main vs appendix

Evidence labels (`[all]`, `[most]`, `[some]`, `[venue: X]`, `[current]`, `[older]`, `[fix]`, `[skill choice]`) are defined in SKILL.md. `[some]` marks an option, not a rule. Where practice changed over time, `[current]` wins; a `[current]` rule that another current source contradicts is only a preference. Venue facts (limits, statements, kits) live in `venues.md`. All example titles and sentences below are synthetic.

## 1. Pick the arc first

| Paper type | Arc | Evidence |
|---|---|---|
| Method paper (default) | Abstract → 1 Introduction (teaser) → 2 Preliminary → 3 Methodology → 4 Experiments → 5 Limitations → 6 Conclusion; Related Works = Appendix A | `[all]` method papers |
| Findings then method | finding → method built on the finding → extension, one main-text section each with a descriptive title, RQs numbered across sections | `[some]` |
| Pure analysis (no method) | Claim-titled sections ("Forgotten Examples Are Predictable from Early-Epoch Losses"); Related Works stays in the main text as Sec 2 | `[some]` |

Use the findings/analysis arcs only when the paper really is a findings paper. A proposal that introduces a method becomes a method paper. Inside the method arc, the Methodology plan and intro P2-P4 depend on the method type (workflow W, objective + closed form T, loss + theory L, reward design R): see `paper_types.md`.

## 2. Main-text section skeleton (method paper)

| # | Exact title | Rule | Evidence |
|---|---|---|---|
| – | Abstract | one paragraph | `[all]` |
| 1 | `Introduction` | teaser float inside, panels referenced in text | `[all]` |
| 2 | `Preliminary` ("Preliminaries" `[some]`) | 2 subsections (task formulation; base paradigm with its equations); the base-paradigm subsection ends with the limitation the method removes (`[all]`; a later background subsection, if any, follows it); optional last subsection `Literature Review` (~0.6 pp; `[some]`) | `[most]` |
| 3 | `Methodology` | roadmap opener (`[all]` method papers); 3-5 subsections; first may be "Key Idea" (`[some]`); ends with the subsection that closes the loop: the assembled method (a subsection titled with the method's name), the joint optimization with its algorithm, or inference-time behaviour ("Inference"); pick the one the method needs | `[all]` method papers |
| 4 | `Experiments` | opener + RQ bullets → setup run-ins (under `\subsection{Experiment Setup}` `[some]`; directly under the section `[most]`) → one `(RQk) Title` subsection per RQ | `[all]` method papers |
| 5 | `Limitations` | numbered section, BEFORE the Conclusion | `[most]` |
| 6 | `Conclusion` | one paragraph | `[all]` |
| – | `\section*{Acknowledgments}` | camera-ready only, then `\clearpage` | `[most]` |

Title rules:
- All section and subsection titles in Title Case `[all]`.
- Method subsection titles are "Objective with/via Technique": "Coverage Preservation with Budgeted Sampling", "Scorer Training with Pairwise Feedback", "Scoring Novelty via Held-Out Agreement".
- Prefer the RQ tag as a PREFIX: "(RQ1) Reliability of the Difficulty Scores" `[current]`. The suffix "Coverage Gains (RQ1)" `[older]` is not wrong; use one form per paper.
- Analysis sections use claim-shaped titles: "Forgetting Analysis" → "Rare-Class Examples Are Forgotten First" (a typical revision `[some]`).

Paper title:
- "Verb-ing <Object> with <Key Ingredient>" (`[most]` method papers): "Filtering Training Data with Difficulty- and Diversity-Aware Scorers", "Correcting Label Shift with Budgeted Prior Tilting"; with the method name as a prefix: "PriorTilt: Training-Free Label-Shift Correction in Closed Form".
- Alternative: "<Hook>: <Descriptive Subtitle>" — "Less Is Enough: Curriculum Filtering for Data-Efficient Fine-Tuning" `[some]`.
- Titles become more sober in revision `[some]`: a playful hook ("Networks Know What They Will Forget") gives way to a descriptive title ("Predicting Example Forgetting from Early Training Dynamics"), and a question turns into a claim. Keep rejected candidates commented in the preamble `[some]`.

Method name:
- If the name is an acronym, expand it with bold initials at first mention: `\textbf{D}ifficulty- and d\textbf{IV}ersity-\textbf{A}ware filtering (DIVA)`, `\textbf{Cali}bration \textbf{Loss} (CaliLoss)`. Repeating the expansion verbatim in the abstract, intro P4 and the first Methodology sentence is `[some]`.
- A non-acronym name may be bold at first use or plain (`[some]` each); either is in style.
- Typed literally, never a `\method` macro `[all]`.

## 3. Project layout `[all]`

```
<venue>.tex                     % iclr2026_conference.tex / neurips_2026.tex / example_paper.tex
math_commands.tex               % ICLR only (the paper's own macros are appended here)
chapters/0_abstract.tex
chapters/1_introduction.tex     % teaser float at the top
chapters/2_preliminary.tex
chapters/3_methodology.tex
chapters/4_experiments.tex
chapters/5_related_work.tex     % \input commented out; RW lives in the appendix
chapters/6_conclusion.tex       % Limitations + Conclusion (+ Acknowledgments, \clearpage)
chapters/7_appendix.tex         % \appendix \clearpage; A = Related Works
chapters/8_ethics_reproducibility_statement.tex   % ICLR only; \input before \bibliography
checklist.tex                   % NeurIPS only; after the appendix, after \clearpage
figures/<topic>/...             % figures/framework/, figures/method/, figures/exp/<analysis>/
backup/*_v1.tex                 % superseded drafts are kept [some]
```
The ICML layout shifts the numbering by one (`1_abstract` ... `8_appendix`); same structure `[venue: ICML]`. `templates/main_skeleton.tex` + `templates/chapters/` reproduce the one-column layout; `templates/main_skeleton_icml.tex` + `templates/chapters_icml/` reproduce the two-column one, whose last file `8_appendix.tex` holds the whole appendix (`latex_source.md` §11).

Source-file conventions `[all]`:
- **One sentence per line** in the `.tex`: 80-97% of prose lines hold a single sentence; about 40% of the multi-sentence lines are captions, bullet bodies or bold run-in paragraphs. Write body prose one sentence per line; it keeps diffs and co-author edits per sentence.
- **Old wording is kept, not deleted**: the replaced sentence stays as a `%` line next to its successor (most often between the neighbouring sentences, then below, then above it), often with stacked alternatives; a rewritten paragraph stays as a commented block above the new one `[some]`, or inline in `\ignore{}` `[some]` ("Using\ignore{Applying} budgeted sampling"). Planning notes are comments too `[most]`.
- **Status macros mark edits in place**: `\revision{}`, `\rebuttal{}`, one initials macro per co-author (`\coA{}`, `\coB{}` in the template; rename them), `\concept{}` (coloured while drafting, black at submission; `templates/preamble.tex` B4). `\revise{}` and `\rebuttal{}` + `\rebuttalcaption{}` mark ICLR discussion-phase changes `[venue: ICLR]`; a venue-port marker (e.g. `\port{}`) wraps text added when moving a paper to another venue `[some]`. Labels, file names, bib and macro placement: `latex_source.md`.
- **Superseded files are kept** `[some]`: `backup/*_v1.tex`, `*_new.tex` next to the old file (e.g. `3_methodology_v2.tex`), `*_<initials>.tex` for a senior co-author's rewrite. Only the file `\input` by the main `.tex` is the paper; check that before quoting a source.

## 4. Capacity: words and pages

Prose words per main-text section (floats and display math excluded), range across the source set `[all]`:

| Section | Range (words) |
|---|---|
| Abstract | ≈120-290 (≤250 `[most]`) |
| Introduction | ≈600-850 |
| Preliminary | ≈400-700 |
| Methodology | ≈950-1,300 (method papers) |
| Experiments | ≈950-1,400 |
| Limitations | ≈35-60 |
| Conclusion | ≈80-140 |
| Total prose | ≈3,700-4,350 |
| Main floats (fig + tab + alg) | 5-13 |

- Papers that fill the limit hold about 3.75-3.9k prose words at a 9-10 pp one-column limit `[current]`. At ICML, about 3.65-3.9k prose words fill **8.0** two-column pages `[venue: ICML]`; the camera-ready page is optional (§5C, `venues.md` §2).
- Every paper fills the limit EXACTLY `[all]`: never a half-empty last page, never an overflow. A build under a different engine (e.g. tectonic/XeTeX without font expansion) can overflow by a line or two: fix such cases `[fix]`.
- Fit by moving material (RW to the appendix, delete the contribution list, extra models/datasets/ablations to the appendix) and by negative `\vspace` inside floats with commented `% \clearpage` / `% \vspace` toggles; never shrink body text (`\small`) `[all]`. Never put negative `\vspace` at headings (`[fix]`: some sources did) or touch `\baselinestretch`/section spacing: the kits forbid changing the format, ICML explicitly (`venues.md` §1.2).

A one-column page map follows from the blueprints below: title and abstract on p1, the Introduction to about the top of p3 with the teaser at the top of p2, the Methodology in the middle third, the Experiments in the last third, and Limitations + Conclusion ending exactly at the bottom of the limit page. At camera-ready the extra page goes mostly to the Experiments.

## 5. Blueprints (fix the word budget before drafting)

### A. ICLR, 9-page submission `[most]` (ICLR 2027 confirmed 9 / 10 pp)
ICLR and NeurIPS kits specify the same text block and paragraph rules; compiled, they hold the same words per page (`venues.md` §1.1).

| Block | Pages | Prose words | Floats / math |
|---|---|---|---|
| Title, authors, abstract | 0.35-0.4 | ≤250 | – |
| 1 Introduction | 1.9-2.1 | 650-750 | teaser `figure[t]`/`figure*[t]` 0.4-0.5 pp (lands top of p2) |
| 2 Preliminary | 0.6-0.9 | 350-550 | 2-4 numbered eqs; optional 2.x Literature Review 0.4-0.5 pp / 250-300 words |
| 3 Methodology | 2.4-2.8 | 900-1,100 | framework `figure*` 0.4-0.5 pp, 6-10 eqs, optional algorithm (~0.3 pp) or Definition/Lemma |
| 4 Experiments | 2.8-3.0 | 850-1,000 | 1-2 tables (0.35-0.5 pp each), 2-3 figures |
| 5 Limitations | 0.1 | 35-55 | – |
| 6 Conclusion | 0.15-0.2 | 80-120 | – |
| Total | 9.0 | ≈3,500-3,700 | 6-8 floats |
| After p9 (not counted) | – | – | AI Use Statement (2027, required) + Ethics + Reproducibility statements, References, Appendix (A Related Works …; 2026-style kits: last = Use of LLMs) |

At camera-ready (10 pp) the extra page goes back to the Experiments and Introduction `[some]`.

### B. NeurIPS, 9 pages `[some]`

| Block | Pages | Prose words | Notes |
|---|---|---|---|
| Title + abstract (+ optional page-1 results figure `[!h]` before `\section{Introduction}`) | 0.6-0.8 (0.35 without figure) | 120-200 (≤250) | |
| 1 Introduction | 1.6-2.0 | 650-750 | concept teaser on p2 |
| 2 Preliminaries | 0.5-0.8 | 400-600 | |
| 3 Methodology | 2.8-3.3 | 1,000-1,300 | framework `figure*`, optional key-idea figure (a `wrapfigure` is fine in one column), optional algorithm |
| 4 Experiments | 2.6-2.9 | 950-1,150 | one main `table*`, 2-3 figures (`wrapfigure` allowed) |
| 5 Limitations + 6 Conclusion | 0.25-0.3 | 120-150 | |
| Total | 9.0 | ≈3,700-3,850 | then References, Appendix (A Related Works … Broader Impacts, Asset Licenses), checklist |

### C. ICML, 8 pages at submission (+1 camera-ready), two columns `[venue: ICML]` (measured in a Times build)

| Block | Pages | Prose words | Floats |
|---|---|---|---|
| Title + abstract | 0.35 | 120-200 (ICML asks 4-6 sentences) | – |
| 1 Introduction | 1.3-1.5 | 750-850 | teaser `figure[t]` at `\columnwidth` (0.3 pp), first thing in the intro file |
| 2 Preliminary | 0.8-1.0 | 550-650 | 3-6 numbered eqs incl. one multi-row `aligned` objective |
| 3 Methodology | 2.4-2.7 | 1,000-1,150 | framework `figure*` 0.9\textwidth; optional second `figure*`; column algorithm; optional prompt figure |
| 4 Experiments | 2.2-2.5 | 900-1,000 | main `table*`; 1 column table; 4-6 column figures (pairs as subfigures) |
| 5 Limitations + 6 Conclusion | 0.25-0.3 | 130-150 | – |
| **Total** | **8.0** | **3,650-3,900** | about 12 floats, about 2.6 pp |
| After p8 | – | – | `\clearpage`, `\section*{Impact Statement}`, (Acknowledgements at camera-ready), References, `\newpage\appendix\onecolumn`, A Related Works … |

- Two columns hold about 1.27× the words of a one-column page (≈850-900 vs ≈680-700), but column floats are taller relative to the text, so the total is about the same as NeurIPS 9 pp.
- Fewer floats → more words: each column-height of floats removed frees about 360 prose words.
- Camera-ready (9 pp): the extra page is optional. If you use it, bring material back from the appendix (one more RQ figure, a second backbone, case-study lines): about +600-700 words or +2 column floats.
- Page map, per-float widths and placement mechanics: `figures_tables.md` §6; build recipe: `venues.md` §2.3.

## 6. Main text vs appendix `[all]`

Main text = one clean story:
- motivation; the method with EVERY equation needed to understand and re-implement it (in `[some]` papers the appendix holds no method math at all);
- theorem/lemma/definition STATEMENTS with a proof sketch or a pointer;
- one headline table per claim; one mechanism or ablation analysis; at most one short case study.

Appendix = everything else: Related Works; complete proofs and derivations; complexity and cost; full setup (datasets and statistics, baseline descriptions, metrics, hardware, hyperparameter tables, prompts); extra backbones, sizes, datasets; reviewer-style robustness checks (data leakage, evaluator swap, stress tests); sensitivity sweeps, visualizations, case studies; Broader Impacts, licenses, LLM use.

Every move to the appendix leaves a specific pointer at the end of the paragraph: "Results for larger filtering budgets are reported in Appendix~\ref{apdx:larger-budgets}."; "(proof in Appendix~\ref{apdx:closed-form-proof})".

## 7. Appendix order (each top-level section starts with `\clearpage`, label prefix `apdx:`)

```
A  Related Works              ([most] put it first)
B  Methodology / Theoretical Analysis   ("Proof of Lemma 1", "Closed-Form Solution for W", "Complete Derivation of the Loss", "Proof of Theorem 3.1")
C  Implementation Details     (frameworks with footnote URLs, hyperparameter table, hardware/compute)
D  Prompts                    (tcolorbox figures; LLM papers)
E  Experiments                (subsections mirror the main-text RQs: "Performance on ...", "Effect of ...")
F  Analysis / Case Study
G  Broader Impacts (NeurIPS) | Asset Licenses (NeurIPS) | Use of LLMs (ICLR 2026-style kits only, last;
   ICLR 2027 moved it to an AI Use Statement before the references)
```
- ICML `[venue: ICML]`: `7_appendix.tex` holds only `\newpage \appendix \onecolumn`; the next file holds A Related Works, B Implementation Details, C Prompts, D Experiments (mirroring the RQs), E Case Study. The main text never points to the Related Works appendix.
- Findings papers mirror the main-text section titles in the appendix (B-D = Sec 3-5) `[some]`.
- A thorough appendix can run to 20+ pages with this outline: A Related Works; B Methodology (all proofs); C Computational Cost; D Experimental Setup (implementation, evaluation protocol, benchmarks, baselines); E Experiments (other model sizes, other datasets or domains, robustness checks); F Analysis (visualizations, sweeps, case study); then the venue sections.

## 8. Related Works placement and size

- Appendix A `[most]`. Titled "Related Works" (plural) there `[most]`; "Related Work" `[some]`. Only analysis papers keep it in the main text (as "Related Works", Sec 2) `[some]`. Every method paper commented out `\input{chapters/5_related_work}` or wrote it as an appendix file `[all]`.
- ~1 page, 380-500 words, 2-3 `\subsection`s of one paragraph each (100-170 words). Subsections map onto the method's ingredients: base paradigm, enabling technique, form of supervision/constraint (e.g. for a data-filtering method: Data Selection for Fine-Tuning / Curriculum Learning / Learned Data Valuation).
- Main-text substitutes: intro P2/P3 positions the work; the Preliminary introduces the base paradigm; an optional §2.2 "Literature Review" is organised by which term of the base equation each method modifies `[some]`.
- Paragraph writing rules: `prose.md` §7.

## 9. Venue differences (summary; the full table with sources is `venues.md` §1)

| Item | NeurIPS | ICLR | ICML |
|---|---|---|---|
| Limit (last verified) | 9 pp (+1 camera-ready), 2026 | 9 submission / 10 rebuttal and camera-ready, 2026 and 2027 (10 in 2025) | 8 pp (+1 camera-ready), two columns, 2026; 2027 call not out on 2026-09-25 |
| Citation | `\cite{}` numeric (`numbers, compress`, `unsrtnat`) | `\citep{}` only; never `\citet` | `\cite{}` or `\citep{}`: `icml2026.sty` maps `\cite` to `\citep` (parenthetical, one argument); multiple keys in chronological order |
| hyperref | `linkcolor=red, citecolor=green` | `linkcolor=red, citecolor=cyan, urlcolor=cyan` | template default dark blue |
| Equation refs | "Equation~\ref{}" | `\Eqref{}` only: math_commands.tex redefines `\eqref{x}` as "equation~\ref{x}", so "Equation \eqref" renders "Equation equation 1" and a bare `\eqref` renders a lowercase "equation 1" | "Equation~\ref{}" |
| Statements | Limitations in main text; Broader Impacts in the appendix (`[most]`); checklist after the appendix, kept at camera-ready | ICLR 2026+: `\section*{Ethics Statement}` + `\section*{Reproducibility Statement}` before References (not used before 2026); 2027: required `\section*{AI Use Statement}` there too | `\clearpage` then `\section*{Impact Statement}` (required at submission) (+ Acknowledgements at camera-ready) before References |
| Code link | camera-ready in the abstract; submission: commented anonymous `anonymous.4open.science` link | same (in the abstract) | optional; anonymous only at submission (no public code links during review) |
| Other | compute disclosure + license table in the appendix (checklist Q8, Q12) | acknowledgements as `\subsubsection*` per the kit | line-number ruler automatic; supplementary zip/pdf at the paper deadline; ≤50 MB (20 MB camera-ready); prefer vector plots; prompt injection = desk rejection; Conflict of Interest paragraph at camera-ready if needed |

All venues: when the paper shows offensive or unsafe text, a one-line red content warning goes under the abstract `[some]` (`\warning{}`, `latex_source.md` §7).

## 10. Statement templates

- **AI Use Statement (ICLR 2027, required, before the references)** — say whether generative AI generated data, developed theoretical models or frameworks, formulated or proved mathematical claims, proposed or refined hypotheses, designed or gave feedback on the methodology or experiments, implemented methods, translated, cleaned or reformatted data, supported qualitative or thematic analysis, or interpreted results (disclosure required: name every category as used, not used, or not applicable), and whether it helped with writing, figures, code edits or literature search (recommended); close by confirming that the authors reviewed all AI-assisted work and accept responsibility for it (ICLR 2027 AI Policy; K-ICLR27 l.397-412). Template: `templates/chapters/8_ethics_reproducibility_statement.tex`.
- **Ethics Statement (ICLR 2026+)** — three short paragraphs: (1) aim and benefit + a data sentence ("Our experiments use only public models and datasets and involve neither private data nor human subjects."); (2) the risk ("We also see a risk of misuse: X could be applied to ..., for instance ...."); (3) the residual gap ("X reduces ... but does not eliminate it, and we invite further work on ....").
- **Reproducibility Statement (ICLR 2026+)** — "We took several steps to make our results reproducible. An anonymous implementation is available at \url{}. Section~\ref{} specifies .... Appendix~\ref{} lists the implementation details, hardware, hyperparameters, data processing and prompts .... With these materials, others can rerun every experiment in the paper."
- **Use of LLMs (ICLR 2026-style kits, last appendix section)** — "LLMs played no part in forming research ideas, designing experiments or analysing data. We used them only for:" + 2 bullets + "None of the results or conclusions rest on LLM output, and the authors are responsible for every part of the paper."
- **Broader Impacts (NeurIPS `[most]`)** — two paragraphs, contribution then "At the same time, X could be misused; for example, ...." , or three bold heads `\textbf{Benefits.}` / `\textbf{Risks.}` / `\textbf{Safeguards.}` `[some]`. Plus an Asset Licenses table and a compute-disclosure paragraph `[some]`. Checklist justifications cite labels: "Section~\ref{sec:limitations} states the limitations of this work."
- **Impact Statement (ICML)** — three sentences: aim and benefit ("This work aims to advance ... by ..., which can help applications such as ...."), expected effect ("We expect its societal effects to be mainly positive, for example ...."), one risk ("A possible concern is that ...."). Drop inflated or dismissive adjectives, per the calibrated-claims rule. The CFP's verbatim default sentence is the safe fallback (`venues.md` §2.2).
- **Acknowledgments** — omitted at submission; one grant sentence at camera-ready, then `\clearpage`. Heading per kit: ICLR `\subsubsection*{Acknowledgments}`, NeurIPS `\begin{ack}`, ICML `\section*{Acknowledgements}` next to the Impact Statement.
- Optional footnote on the last Conclusion sentence pointing to the statement `[some]`: "\footnote{Appendix~\ref{apdx:use-of-llms} describes how LLMs were used.}"; "\footnote{Appendix~\ref{apdx:broader-impacts} discusses broader impacts.}".
