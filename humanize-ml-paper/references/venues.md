# Venues: ICLR / NeurIPS / ICML (single source of truth + conversion)

Read this at Step 0 (confirm the venue and fetch the current call for papers), when switching venues, and before the camera-ready. The other reference files point here instead of repeating venue facts. Two-column house practice rests on the ICML kit plus the house layout and is labelled `[venue: ICML]`; everything else in the house style is one-column.

**Venue kits**: the official instruction files of each venue. Cite them by these names:

| Name | Official source | Contents |
|---|---|---|
| K-ICLR25 | `iclr2025_conference.pdf` from the official ICLR 2025 template (ICLR Master-Template on GitHub) | ICLR 2025 formatting instructions (7 pp); the PDF is the blank template, not a paper |
| K-ICLR26 | `iclr2026_conference.pdf`, `.sty` from the official ICLR 2026 template | ICLR 2026 instructions (7 pp) |
| K-ICLR27 | ICLR Master-Template `iclr2027/iclr2027_conference.tex` and `iclr2027_conference.sty` (fetched 2026-09-25; commit 46ed6f4, 2026-09-01), plus the iclr.cc 2027 Author Guide and AI Policy for Authors | current ICLR cycle (deadline 2026-09-25) |
| K-NIPS25 | `neurips_2025.pdf` from the official NeurIPS 2025 style zip (neurips.cc) | NeurIPS 2025 instructions and the 16-item checklist |
| K-NIPS26 | `neurips_2026.sty`, `checklist.tex` from the official NeurIPS 2026 style zip, plus the neurips.cc 2026 Main Track Handbook (fetched 2026-09-25) | NeurIPS 2026 |
| K-ICML26 | `example_paper.pdf`, `example_paper.tex` (incl. the template comments in its appendix), `icml2026.sty` from `media.icml.cc/Conferences/ICML2026/Styles/icml2026.zip`, plus the icml.cc 2026 Call for Papers and Author Instructions (fetched 2026-09-25) | ICML 2026; the PDF is the blank template, not a paper |

**Tags on every rule**
- **V**: venue requirement, stated in a kit or official page named above. Breaking it risks desk rejection.
- **V?**: known venue practice that no kit here states, or a rule inferred from house practice only. Confirm it in the current call for papers.
- **S**: house style, with its evidence label (`[all]`, `[most]`, `[some]`, `[current]`, `[older]`, `[venue: X]`; see SKILL.md). It is binding for this skill but not for the venue.
- **S≠V**: the house style goes against a kit instruction or suggestion. Decide on purpose; the default for each is given.
- **[fix]**: common practice (or an earlier version of this skill) got this wrong; the skill corrects it (listed together in §5).
- **Verified by compiling**: tested with tectonic 0.17 on 2026-09-25 against the official kits.

---

## 1. Source-of-truth table

### 1.0 ALWAYS re-check the current year's call and kit first

Limits and required statements change from one year to the next. Treat every number below as "last verified", never as current:
- ICLR main-text limit: 10 pp in 2025 (K-ICLR25 p2), then 9 at submission and 10 at rebuttal/camera-ready in 2026 and 2027 (K-ICLR26 p2, K-ICLR27 l.131).
- ICLR LLM disclosure: 2026 had a "Use of LLMs" section, placed as the last appendix section (ICLR 2026 requirement). In 2027 the kit has a **required "AI use statement" before the references**, not counted, at most 1 page (K-ICLR27 l.397-412). The 2026 placement no longer matches the kit.
- NeurIPS: the 2026 sty added track options, and `[final]` alone now stops at `\@trackname` (K-NIPS26 `neurips_2026.sty` l.47-69; verified in the skill's test build). The 2025 kit added checklist Q16 "Declaration of LLM usage" (K-NIPS25).
- ICML: `icml2026.sty` went back from newtx to `times`.

**Check procedure.** Run it at Step 0, and again in the week before the deadline.
1. **Identify the cycle.** ICLR Y has its deadline in late September of Y-1; ICML Y in late January of Y; NeurIPS Y in May of Y. As of 2026-09-25: ICLR 2027 is open (deadline today), the ICML 2027 call returns 404 (not yet published), and NeurIPS 2027 is not out.
2. **Read the official pages for that year.**
   - ICLR: `iclr.cc/Conferences/<Y>/CallForPapers`, `/AuthorGuidelines`, `/AIPolicyForAuthors`.
   - NeurIPS: `neurips.cc/Conferences/<Y>/CallForPapers`, `/MainTrackHandbook`, the LLM policy page, the checklist guidelines.
   - ICML: `icml.cc/Conferences/<Y>/CallForPapers`, the author and style instructions.
3. **Download that year's style kit.** Never reuse last year's `.sty`/`.bst`: "Please make sure you use the current files and not previous versions. Tweaking the style files may be grounds for rejection" (K-ICLR26 p1; same wording in K-NIPS25 p1). NeurIPS 2026 may desk-reject submissions that violate the year's style (K-NIPS26 handbook).
4. **Extract the rules from the kit** (instructions PDF or `.tex`) instead of trusting memory:
   ```bash
   python3 -c "import pypdf,sys;[print(p.extract_text()) for p in pypdf.PdfReader(sys.argv[1]).pages]" kit.pdf \
     | grep -n -i -E "page|limit|statement|checklist|anonym|LLM|AI use|appendi|supplement|impact"
   grep -n -i -E "page|statement|required|recommended" iclr<Y>_conference.tex   # ICLR ships the rules in the .tex
   ```
   Record: submission and camera-ready limits; what does not count; required and recommended statements with their placement; checklist version and item count; anonymity and preprint rules; appendix in the same PDF or separate; file-size cap; citation and bibliography style.
5. **Check the kit's `.sty`.** Look at the year macro (`\@neuripsyear`; ICLR `\lhead{... ICLR <Y>}`; the ICML "Proceedings ... PMLR" string), its options (NeurIPS `final`/`preprint`/track; ICLR `\iclrfinalcopy`; ICML `[accepted]`/`[preprint]`), and what it anonymizes or numbers.
6. **Update the "Last verified" row below and the `VENUES` table in `scripts/check_paper.py`**, then run `check_paper.py --venue X --stage submission --limit N` with N from the call. The script prints a NOTE whenever the kit year is newer than its verified year (§4).
7. **Compile the empty skeleton with the new kit** and diff its header against the table (ICLR 2027 `.sty` = ICLR 2026 `.sty` apart from the year: verified by `diff`).

### 1.1 Kit, layout, stage toggles

| Item | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| Last verified | 2027 (K-ICLR27), 2026 (K-ICLR26) | 2026 (K-NIPS26), 2025 (K-NIPS25) | 2026 (K-ICML26); 2027 call not out on 2026-09-25 | – |
| Header (full blocks: `preamble.tex` A1-A3) | `\usepackage{iclr<Y>_conference,times}` + `\input{math_commands.tex}` + hyperref | `\PassOptionsToPackage{numbers, compress}{natbib}` + `\usepackage{neurips_<Y>}` (+ `inputenc`/`T1`) | `hyperref`, then `\newcommand{\theHalgorithm}{\arabic{algorithm}}`, then `\usepackage{icml<Y>}`; `\icmltitlerunning{}`; title block inside `\twocolumn[...]`; `\printAffiliationsAndNotice{}` right after it (required even when empty) | V (kits; K-ICML26 `example_paper.tex`) |
| All venues | `\PassOptionsToPackage{table}{xcolor}` before the venue style | same | same (prevents "Option clash for package xcolor" when `[table]{xcolor}` follows the sty) | S/[fix] (verified by compiling) |
| Submission option | `\iclrfinalcopy` commented out | no option: anonymous, line numbers | no option: anonymous, line numbers | V (K-ICLR26 template comments; K-NIPS25 p2; K-ICML26 p2) |
| arXiv preprint | none in the kit | `[preprint]` (non-anonymous, "Preprint" footer) | `[preprint]` | V (K-NIPS25 p1-2; `icml2026.sty` l.135-143) |
| Camera-ready | `\iclrfinalcopy` | `[main,final]` (2026: track option required; also `position`, `eandd`, `creativeai`, workshop options) | `[accepted]` (PMLR footnote, running title) | V (K-NIPS26 sty l.24-92; K-ICML26 p2) |
| Columns and text block | 1 column, 5.5 × 9 in | 1 column, 5.5 × 9 in | 2 columns, 6.75 × 9 in total, 0.25 in gap → `\columnwidth` = 3.25 in | V (K-ICLR26 p1; K-NIPS25 p2; K-ICML26 p2, `icml2026.sty` l.261-263: `\textheight` 261, `\textwidth` 262, `\columnsep` 263) |
| Body text | 10/11 pt Times, half-line paragraph gap, no indent | identical | 10/11 pt Times, blank line between paragraphs, no indent | V (same pages) |
| Prose capacity per full page | ≈680-700 words | ≈680-710 words (**no difference from ICLR**) | ≈850-900 words (×1.27) | Verified by compiling the same ~5,000-word text with all three kits: 8 / 8 / 7 pages |
| Headings | small caps (the sty does it) | kit: "lower case (except for first word and proper nouns)" | content words capitalized; at most 3 levels | V (K-NIPS25 p2; K-ICML26 p3). **S≠V (NeurIPS)**: house style uses Title Case at NeurIPS too [most]. Keep it: low risk, not enforced |
| Line numbers | added by the kit at submission | added (lineno); "do not refer to these line numbers" | added | V (K-ICLR26 and K-ICML26 margins 000-054; K-NIPS25 p2) |
| Fonts and PDF | US Letter; pages numbered | Type 1 or embedded TrueType only; ≤50 MB (2026) | Type-1 fonts only; ≤50 MB at submission, ≤20 MB camera-ready (the template's "10 MB" is outdated) | V (K-ICLR26 p6; K-NIPS25 p4, K-NIPS26 handbook; ICML 2026 Author Instructions) |
| Hidden text | – | – | prompt injection is forbidden and leads to desk rejection | V (K-ICML26 call) |
| hyperref colours | red / cyan | red / green | template dark blue | S [all] |

### 1.2 Page limits and what counts (re-check every year, §1.0)

| Item | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| Submission limit | **9** (2026, 2027); 10 in 2025 | **9** "including all figures and tables" | **8** | V (K-ICLR27 l.131, K-ICLR26 p2, K-ICLR25 p2; K-NIPS25 p1, K-NIPS26 handbook; K-ICML26 p1) |
| Rebuttal / camera-ready | **10** (rebuttal and camera-ready) | **10** (one extra content page) | **9** | V (K-ICLR27 Author Guide; K-NIPS26 handbook; K-ICML26 p1, p7). S: a camera-ready fills the new limit exactly [most] |
| Not counted | references; appendix after the bibliography; AI use and Ethics statements (≤1 page each) and the one-paragraph Reproducibility statement; acknowledgements | references, technical appendices, the mandatory checklist | references, appendix, Impact Statement, acknowledgements | V (K-ICLR27 l.397-430 + Author Guide; K-NIPS25 p1; K-ICML26 p1, p5) |
| Reviewer obligation | appendix optional to read | appendix optional | "not required to look at more than the first 8 pages": the 8 pp must stand alone | V (K-ICLR27 Author Guide; K-ICML26 p2-3) |
| Fill rule | main text ends exactly on the limit page, and that page is full | same | same | S [all] |
| Spacing | "Do not change any aspects of the formatting parameters" | same | also "do not compress the paper format by reducing the vertical spaces" | V (K-ICLR26 p6; K-NIPS25 p3; K-ICML26 p1). **S within V**: negative `\vspace` only *inside floats* [most]; two-column papers carry the most of them. Never at headings ([fix]: a negative `\vspace` before a `\section` or in a chapter's first line is a common slip), never `\baselinestretch`, `\textfloatsep` or section spacing. **S≠V at ICML**: the kit requires ≥0.1 in before and after captions (K-ICML26 p3-4; `icml2026.sty` l.681 `skip=0.1in`). Never put a negative `\vspace` between graphic and `\caption` at ICML ([fix]). Trims after the caption must leave ≥0.1 in to the body text; when another float stacks directly below in the same column, use no trim (`figures_tables.md` §6) |

### 1.3 Anonymity (submission) and back matter per stage

| Item | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| Authors | hidden while `\iclrfinalcopy` is off; "Non-anonymous submissions will be rejected without review" | the sty prints "Anonymous Author(s)" unless `final`/`preprint`/`nonanonymous` | hidden unless `[accepted]`/`[preprint]`; "Submissions that include the author information will not be reviewed" | V (K-ICLR26 template comments, K-ICLR27; `neurips_2026.sty` l.71-92; K-ICML26 p2) |
| Self-citation | third person | third person ("In the previous work of Jones et al."); unpublished own work → "A. Anonymous" + anonymized copy | third person; do not anonymize published references | V (K-NIPS25 p3; K-ICML26 p2) |
| Code link | anonymous repo link, or zip as supplementary | anonymous link or supplementary ZIP (≤100 MB) | anonymous URL or supplementary | V (K-ICLR27 Author Guide; K-NIPS26 handbook; K-ICML26 p5) |
| House code-link habit | submission: `anonymous.4open.science` link at the end of the abstract; camera-ready: GitHub link, with the anonymous one kept as a `%` comment above it | same | same, or no link at all [some] | S [most] |
| Acknowledgements | camera-ready only; kit: unnumbered third-level heading (`\subsubsection*`) | camera-ready only; `\begin{ack}` → "Acknowledgments and Disclosure of Funding". The 2026 sty does **not** hide it at submission: delete it | not in the submission; unnumbered, not counted, next to the Impact Statement | V (K-ICLR27 l.450-452; `neurips_2026.sty` l.103-109; K-ICML26 p1, p5). **S≠V (ICLR)**: `\section*{Acknowledgments}` is common in practice [fix]. Default: follow the kit |
| Preprints | allowed under the dual-submission policy | non-anonymous preprints allowed | allowed, but not advertised as an ICML submission | V (K-ICLR27 call; K-NIPS26 handbook; K-ICML26 call) |

### 1.4 Citations, equation references, bibliography

| Item | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| natbib mode | author-year (the sty sets `\setcitestyle{authoryear,round}`) | numeric through `\PassOptionsToPackage{numbers, compress}{natbib}`; "any style ... used consistently" | author-year (`icml2026.sty` l.553 `\setcitestyle{authoryear,round}`) | V (`iclr2026_conference.sty` l.20-23; K-NIPS25 p2; K-ICML26 p5) |
| Parenthetical citation | `\citep{}` | `\cite{}` → [1] | `\cite{}` or `\citep{}`: `icml2026.sty` l.209 has `\renewcommand{\cite}[1]{\citep{#1}}`, so `\cite` is parenthetical, but it takes exactly one argument (`\cite[p.~3]{x}` breaks; write `\citep[p.~3]{x}`) | V/S (ICLR `\citep` only [venue: ICLR]; NeurIPS `\cite` [most]; at ICML `\cite` renders "Adam (Doe et al., 2025a)" in a Times build). ICML asks for "(Samuel, 1959)" (K-ICML26 p5). Correction: an earlier draft claimed ICML `\cite` is textual; the sty line above disproves it |
| Textual citation | `\citet{}` per kit; house style never uses it | – | `\citet{}`; `\yrcite{}` prints the year in parentheses, "(1959)" (`icml2026.sty` l.208 `\citeyearpar`), as the kit asks when the author names are already in the sentence; house style never uses either | V (K-ICLR26 p2); S [all]: the method name is written in the text, then `\citep` ("Adam \citep{Adam}") |
| Multiple citations | any order | compressed ranges ([3-5]) | **chronological order** (natbib does not sort by year: order the keys by hand) | V (K-ICML26 p1) |
| Baselines named in table rows | `Name (\citeauthor{K}, \citeyear{K})` | allowed too: the same labels work in numeric mode | as ICLR | S [some]. [fix]: an earlier rule said "never in NeurIPS numeric papers"; they work in both modes |
| Equation references | `\Eqref{x}` only (`math_commands.tex` l.47-49: `\eqref` = "equation~\ref", `\Eqref` = "Equation~\ref"). "Equation \eqref" prints "Equation equation 1"; a bare `\eqref` prints lowercase "equation 1" | "Equation \ref{x}" | "Equation \ref{x}" | S [all] |
| Bibliography style | `\bibliographystyle{iclr<Y>_conference}`; alphabetical; full first names | `unsrtnat`; citation order; references may be set `\small` (9 pt) (house style does not) | `\bibliographystyle{icml<Y>}` (APA: "Doe, J., Roe, R., and Poe, E."); alphabetical; page numbers wherever possible | V (K-ICLR27 l.455-456, K-ICLR26 p2; S [all] for NeurIPS `unsrtnat`; K-NIPS25 p5; K-ICML26 p5) |
| Capitals in titles | all three `.bst` files lowercase unbraced title words (an unbraced "BERT: ... NLP" prints "Bert: ... nlp"; verified). Keep DBLP braces (`{CNN}`, `{ImageNet}`) and brace method names (`{BERT}`) | same | same; ICML asks for it explicitly ("{B}ayesian") | V (K-ICML26 p5); S [all] (DBLP-style .bib) |
| `.bib` file name | named after the main file: `iclr2027_conference.bib` | `neurips_2026.bib` | `example_paper.bib` | S [all] |
| Without `numbers` | – | `unsrtnat` prints "[Doe et al., 2025]" instead of "[1]" (verified) | – | V/S |

### 1.5 Required and optional statements

| Statement | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| AI / LLM use | **2027: required "AI use statement", before the references**, not counted, ≤1 page. Disclosure is required when generative AI generated synthetic data, helped develop theoretical models or conceptual frameworks, formulated mathematical claims, provided critical ingredients for proofs or helped write them, proposed or refined hypotheses, designed or gave feedback on the research methodology or experiments, implemented methods, assisted with translation, cleaned or reformatted data, supported qualitative and thematic data analysis, or interpreted results; it is recommended for drafting and editing text, figures, code edits, experimental parameters, literature search and summaries, brainstorming, reference formatting and titles (AI Policy). The kit's text has four parts: the required tasks AI was used for, those it was not used for, those that do not apply, and "We have reviewed all AI-assisted work" + responsibility (K-ICLR27 l.397-412). 2026: "Use of LLMs" as the last appendix section, plus a footnote from the Conclusion [some] | checklist Q16: declare only when the LLM is "an important, original, or non-standard component" of the method; writing or editing help needs no declaration (K-NIPS25; K-NIPS26 handbook). A method that trains or prompts an LLM as its core component answers Yes | no disclosure section; authors take full responsibility; prompt injection forbidden (K-ICML26 call) | V. **[fix] (applied)**: older templates put "Use of LLMs" last in the appendix. For ICLR 2027 it is `\section*{AI Use Statement}` before `\bibliography`, now in `templates/chapters/8_ethics_reproducibility_statement.tex` (worded to cover every required category of the policy) |
| Ethics | recommended, before the references, ≤1 page: `\section*{Ethics Statement}`, 3 short paragraphs (`structure.md` §10) | covered by checklist Q9 (Code of Ethics) + Broader Impacts | folded into the Impact Statement | V (K-ICLR27 l.414-426); S [some]. Drafting both statements and then commenting them out is common; keep them live when the kit recommends them |
| Reproducibility | recommended, before the references, one paragraph that *points to* sections, appendix and code; it does not repeat the details (house template) | checklist Q4-Q8 | – (Software and Data paragraph: anonymous URL at submission, K-ICML26 p5) | V (K-ICLR27 l.428-444); S [some] |
| Societal impact | inside the Ethics Statement (a former Broader Impacts appendix folds into it) [some] | a dedicated section is not required, but negative impacts must be addressed somewhere (K-NIPS26 handbook; checklist Q10). House style writes Appendix "Broader Impacts": 2 paragraphs, or 3 bold heads Benefits / Risks / Safeguards [some] | **required** `\section*{Impact Statement}`, unnumbered, at the end, next to the acknowledgements (either order), both before the references, not counted. The kit's default sentence may be used verbatim. House style: `\clearpage` + 3 formulaic sentences [venue: ICML] | V; S |
| Limitations | own numbered section before the Conclusion | same; checklist Q2 "encouraged to create a separate Limitations section" | same | S [most]; V-supported at NeurIPS (K-NIPS25 p7) |
| Checklist | none | **required, otherwise desk-rejected**: after the references and the appendix, not counted, 16 questions (2025, 2026); delete the instruction block but keep headings, questions and guidelines; answer only with `\answerYes{}`/`\answerNo{}`/`\answerNA{}` + a 1-2 sentence justification that points to sections (`Section~\ref{sec:limitations}`). **Keep it in the camera-ready** ("its final version will be published with the paper") | none | V (K-NIPS25 p6; K-NIPS26 `checklist.tex`). [fix]: a commented-out `\input{checklist}` (e.g. left from an arXiv build) must never reach the submission. Every "Yes" must be backed by text: a Q7 significance claim needs the test stated in the experiments section |
| Asset licenses | optional | checklist Q12 → Appendix "Asset Licenses" table | – | V (Q12); S [some]. [fix]: a licence table with `\small` and a caption below it is a common slip; use a caption above and no `\small` |
| Compute | hardware in Implementation Details [most] | checklist Q8 → "Compute disclosure" run-in in Implementation Details, or an explicit reason for withholding it [some] | – | V (Q8); S |
| Author contributions | optional `\subsubsection*{Author Contributions}` | – | – | V (K-ICLR27 l.446-448) |
| Content warning | red warning under the abstract when offensive or unsafe example text appears (`\warning{}`) | same | same | S [some] |
| Abstract | one paragraph | one paragraph | one paragraph, "roughly 4-6 sentences" | V (all kits; K-ICML26 p1); S: ≤250 words [most]; 120-200 at ICML and NeurIPS [current]. A 9-11-sentence abstract must be cut for ICML |

### 1.6 Figures and tables

| Item | ICLR | NeurIPS | ICML | Tag / evidence |
|---|---|---|---|---|
| Captions | figure caption below, table caption above | same | same (9 pt; centred if one line); ≥0.1 in before and after every caption (`icml2026.sty` l.681 `skip=0.1in`), so no negative `\vspace` before `\caption` (§1.2 Spacing) | V (K-ICLR26 p2-3; K-NIPS25 p3-4; K-ICML26 p1, p4). S [all] in the main text. [fix]: appendix table captions below the table are a common slip |
| Width units | `\textwidth` = `\columnwidth` = 5.5 in; `figure*` behaves like `figure` | same | `\columnwidth` = 3.25 in inside `figure`/`table`; `\textwidth` = 6.75 in only inside `figure*`/`table*` | V (dimensions) |
| Print-size equivalents | ICML `\columnwidth` = **0.59\textwidth**; ICML `0.49\columnwidth` = 0.29\textwidth; ICML `figure*` at 0.9\textwidth (6.08 in) is wider than the whole one-column text block (5.5 in) | same | – | Arithmetic from the widths above. **[fix] (applied in `figures_tables.md` §4)**: the old "7.7 in wide → `\columnwidth` (ICML) or 0.45\textwidth" was wrong for the second case: at 0.45\textwidth (2.48 in), 16 pt ticks print at 5.1 pt, not ~7 pt. The one-column equivalent is 0.59\textwidth |
| Single result plot | subfigure rows in `figure[t]`/`figure*[t]`, 2-3 panels at 0.49/0.32\textwidth [most] | `wrapfigure{r}{0.52\textwidth}` holding two `subfigure`s at 0.49\linewidth [some], or subfigure rows [some] | `figure[t]` at `\columnwidth`; pairs as two `subfigure[t]{0.49\columnwidth}` + `\hfill` [venue: ICML] | S |
| wrapfigure | allowed by the kit; not house practice at ICLR | `[venue: NeurIPS]` practice [some]: several in the main text, incl. a key-idea `wrapfigure{r}{0.48\textwidth}` | never (two columns) | S (V-safe everywhere except ICML) |
| Teaser | `figure[t]`, lands at the top of p2 [some] | concept teaser `figure[ht]` at 0.9\columnwidth + optional page-1 results figure `[!h]` before `\section{Introduction}` [some] | `figure[t]` at `\columnwidth`, first thing in the intro [venue: ICML] | S |
| Framework figure | `figure*[t]` at `\textwidth` | `figure*[t]` at 0.88\textwidth | `figure*[t]` at 0.9\textwidth | S [most] (0.88-1.0\textwidth) |
| Two-column floats | – | – | only at the top of a page ("Always place two-column figures at the top or bottom"; LaTeX `figure*` takes only `[t]`/`[p]` without dblfloatfix). A `figure*` appears on the page after its source position at the earliest, so put the source early | V (K-ICML26 p4) |
| Main results table | `table*[t]` | `table*[!t]` | `table*[t]` + `\resizebox{0.93\linewidth}` (skill: `\shrinktofit`); secondary tables `table[t]` at 0.93\columnwidth [venue: ICML] | S [all] (`table*`) |
| Vertical rules | – | kit: "publication-quality tables do not contain vertical rules" | – | **S≠V (NeurIPS suggestion)**: one `|` after the row labels [most], also at NeurIPS. Keep |
| Row shading behind text | gray `oursrow` or blue `oursrowblue` | same | kit: "text should not appear on a gray background". Prefer the blue tint `oursrowblue`, or the pink/blue metric-row tints (no gray shading) [venue: ICML] | V (K-ICML26 p3); S |
| Plot files | 600-dpi PNG per panel (S) | Type 1/TrueType only: `plot_style.save()` PDFs use `pdf.fonttype 42` = TrueType, fine | kit prefers vector (.pdf) for plots and PNG for sharp raster images; "only Type-1 fonts". **S≠V**: 600-dpi PNG plots are house practice at ICML too. Keep PNG, or check PDFs with `pdffonts` (no Type 3) | V (K-NIPS25 p4; K-ICML26 p1-2) |
| Graphic content | "neat, clean, legible"; captions readable in B/W | same | name every axis, give a legend, no title inside the graphic, lines ≥0.5 pt | V (K-ICLR26 p2-3; K-ICML26 p3) = S (no plot titles [all]; axis labels with units) |
| Boxes in the main text | `tcolorbox` `width=\textwidth` OK | OK | `width=\linewidth` (never `\textwidth` in a column box) | S/V |
| Algorithm shading | `\algoboxshiftx` −0.6em [current] | same | −0.6em with the skill preamble also aligns in two columns (ICML skeleton render); a `\tikzmk` placed at the end of a comment line can need a much larger shift, so re-check after two passes | S |
| Long display equations | fit 5.5 in | same | break them with `aligned` over several lines [venue: ICML]; never `\small` math | S |
| Theorem counters | Definition 1, Lemma 1 (plain), Theorem 3.1 (within section) | same | kit prefers one counter shared within sections (Definition 2.1, Proposition 2.2), which is how the official template's block is set up (in the kit's `example_paper.tex`; not loaded by `preamble.tex` B6; switch: §3.4) | V-preference (K-ICML26 p4); S [some] |

### 1.7 Order of back matter and appendix

```
ICLR  (K-ICLR27)       ... Limitations, Conclusion, [camera: \subsubsection*{Acknowledgments}]
                       -> \section*{AI Use Statement} (2027, required) -> \section*{Ethics Statement}
                       -> \section*{Reproducibility Statement}    (chapters/8_ethics_reproducibility_statement.tex)
                       -> \bibliography{} \bibliographystyle{iclr<Y>_conference}
                       -> \appendix  A Related Works ... (2026: last = Use of LLMs)
NeurIPS (K-NIPS25/26)  ... Limitations, Conclusion, [camera: \begin{ack}...\end{ack}]
                       -> \bibliographystyle{unsrtnat} \bibliography{}
                       -> appendix  A Related Works ... Broader Impacts, Asset Licenses
                       -> \clearpage \input{checklist}          (required, also at camera-ready)
ICML  (K-ICML26)       ... Limitations, Conclusion -> \clearpage \section*{Impact Statement}
                       -> [camera: \section*{Acknowledgements}] -> \bibliography{} \bibliographystyle{icml<Y>}
                       -> \newpage \appendix \onecolumn  A Related Works ...
```
Appendix in the same PDF as the paper: ICLR allows the same PDF or a separate supplementary file (K-ICLR27 Author Guide); NeurIPS 2026 puts paper, references, appendix and checklist in one PDF (K-NIPS26 handbook); ICML requires one file ("reviewers will not look for appendices as a separate PDF", K-ICML26 p1). House style always appends it to the main PDF [all]. ICML's `\onecolumn` appendix is optional, and nothing else may change (K-ICML26 p7). Each top-level appendix section starts with `\clearpage` and has an `apdx:` label (S [most]).

---

## 2. ICML specifics (official 2026 rules + two-column house practice [venue: ICML])

### 2.1 ICML 2027 status
- `https://icml.cc/Conferences/2027/CallForPapers` returned **HTTP 404** on 2026-09-25, and no ICML 2027 style zip existed. `FutureMeetings` listed only "2027 -- South America". Third-party trackers claim an abstract deadline of Jan 16, 2027 and a paper deadline of Jan 22, 2027. These dates are **unofficial**.
- Use the ICML 2026 kit until the 2027 call appears (`templates/venue/icml/README.md`), then re-check every row of §2.2.

### 2.2 ICML 2026 rules (V unless marked)
Sources: CFP (`icml.cc/Conferences/2026/CallForPapers`), Author Instructions (`/AuthorInstructions`), style zip (`media.icml.cc/Conferences/ICML2026/Styles/icml2026.zip`).

| Item | Rule |
|---|---|
| Limits | 8 pages at submission; over 8 "will be automatically rejected". Camera-ready: 9 pages. |
| Not counted | References, Impact Statement, appendices, and acknowledgements (camera-ready). |
| Single PDF | The appendix goes in the same PDF. Reviewers "are not required to look at more than the first 8 pages". |
| Impact Statement | **Required** for the main track, at submission too. `\section*{Impact Statement}` goes at the end, next to the acknowledgements (either order), before the references. The CFP's verbatim default is allowed: "This paper presents work whose goal is to advance the field of machine learning. There are many potential societal consequences of our work, none of which we feel must be specifically highlighted here." |
| Anonymity | Double-blind: no author information, acknowledgements, grant numbers or public code links. Cite your own work in the third person. Author info may stay in the source, because the sty hides it unless `[accepted]` or `[preprint]` is given. |
| Line numbers | Automatic: in blind mode `icml2026.sty` draws a grey left-margin ruler (l.753-766). It disappears with `[accepted]` or `[preprint]`. |
| Supplementary material | Optional: code/data or extra manuscripts, as a zip or pdf, anonymized, due at the paper deadline. Text appendices go in the main PDF. There is no camera-ready supplement. |
| Format | US Letter; 10 pt Times on 11 pt leading; two columns, text block 6.75 × 9.0 in, 0.25 in gutter, so `\columnwidth` = 3.25 in. At most three heading levels. Content words capitalized. Captions in 9 pt: figures below, tables above. Two-column floats go at the top or bottom of a page. "Do not compress the paper format by reducing the vertical spaces." |
| Layout guard | The sty checks `\textheight`, `\textwidth`, the margins and the paper size at `\begin{document}`. If any of them changed, it prints "The page layout violates the ICML style." Never load geometry, fullpage or savetrees. |
| Abstract | One paragraph, "ideally 4-6 sentences". |
| Citations | Author-year via natbib + `icml2026.bst`; `\cite` = `\citep` (one argument); `\yrcite{}` prints the year in parentheses (`\citeyearpar`, sty l.208), as the kit asks when the author names are already in the sentence. List multiple citations in chronological order (by hand). |
| Graphics | Type-1 fonts. "If possible, please use vector graphics (eps or pdf figures)" for line and bar plots (**S≠V**: 600-dpi PNG plots are house practice; `plot_style.save()` writes both, so include the PDF). |
| Camera-ready | Use `\usepackage[accepted]{icml2026}`. Always call `\printAffiliationsAndNotice{}` (`{\icmlEqualContribution}` for equal contribution). Run https://papercheck.icml.cc/papercheck.html. Also submit a lay summary, and enter the title and abstract in OpenReview without macros. |
| Conflict of interest (new in 2026, camera-ready) | Add a paragraph titled "Conflict of Interest Disclosure" as the last paragraph of the introduction, only if a financial conflict exists. |
| Other | LLM use is allowed, with the authors responsible. **Prompt injection or hidden text means desk rejection.** Do not advertise the paper as an ICML submission during review. |
| Sty options | (none) = blind; `[accepted]` = camera-ready; `[preprint]` = authors shown plus a "Preprint" footer, no ruler; `[nohyperref]`. |

### 2.3 Build recipe (tectonic; verified on the skeleton)
1. **`Option clash for package xcolor`**: loading `[table]{xcolor}` after the sty triggers it. Put `\PassOptionsToPackage{table}{xcolor}` right after `\documentclass`.
2. **Silent Latin Modern fallback under XeTeX.** The sty's `\RequirePackage{times}` has no TU shape. The log says `Font shape TU/ptm/m/n undefined`, and the running head then reads "Title Suppressed Due to Excessive Size". Page positions shift too: a full paper can run about half a page longer. Load `\usepackage[T1]{fontenc}`: `preamble.tex` B1 does, and `main_skeleton_icml.tex` also loads it right after `\documentclass`. After the fix the PDF embeds NimbusRomNo9L and NimbusMonL (the Times and Courier clones).
3. **tectonic cannot download a font** (`cmti9.pfb`, `cmsy5.pfb`: "error decoding response body / operation timed out"). `cmti9` is needed only by the `[accepted]` notice; blind and preprint builds never request it. Fetch the fonts with `scripts/tectonic_fetch.sh <dir> --missing-pfb` (or list the file names), then run `tectonic -X compile --keep-intermediates -Z search-path=<dir> main.tex`. After one successful build the files stay in tectonic's cache. Do not use `--only-cached` for a first ICML build.

### 2.4 ICML density parameters [venue: ICML]
- An 8-page two-column submission holds about **3,650-3,900 prose words** with about 12 floats (e.g. 10 figures, 2 tables and 1 algorithm, ≈2.6 pp of floats). That is about 715 prose words per page. A text-only page holds about 850 extracted words.
- Engine check: XeTeX has no font expansion, so a text that ends exactly on p8 under pdfLaTeX + microtype can end one line into p9 under tectonic. Check the page with the engine you submit with, and treat "one line over" as a fill, not an overflow, only after that check.
- The 9th camera-ready page often goes to floats rather than words (§3.6).
- Per-float widths and pages: `figures_tables.md` §6 (two-column float budget).

---

## 3. Conversion checklist: re-targeting a paper from venue A to venue B

Work in this order. Each step names the direction it applies to. One project skeleton moves between kits: swap the header, keep the chapters, and leave a `%` note wherever a file was added for the port (e.g. at the top of the ICLR `math_commands.tex` block).

### 3.0 Before touching the source
- [ ] Run the §1.0 procedure for venue B's current year. Write B's limits and required statements at the top of the main file as `%` comments.
- [ ] Copy the project (a `backup/` folder is house habit, S [all]). Keep A's statements as commented blocks: they are reused if the paper goes back to A.

### 3.1 Header and files
- [ ] Swap Part A of the preamble (`preamble.tex` A1/A2/A3) and keep Part B unchanged.
- [ ] Copy B's `.sty`/`.bst` (ICML also ships `algorithm.sty`/`algorithmic.sty`; ICLR ships `math_commands.tex`, `natbib.sty`, `fancyhdr.sty`).
- [ ] Rename the main file and the `.bib` file after the kit (`iclr<Y>_conference.tex/.bib`, `neurips_<Y>.tex/.bib`, `example_paper.tex/.bib`; S [all]), and update `\bibliography{}`.
- [ ] → ICML: start from `templates/main_skeleton_icml.tex`. It wraps the title block in `\twocolumn[\icmltitle{} \begin{icmlauthorlist}...\end{icmlauthorlist} \icmlaffiliation{}{} \icmlcorrespondingauthor{}{} \vskip 0.3in]`, calls `\printAffiliationsAndNotice{}` (official template) and `\icmltitlerunning{}`, and loads `\usepackage[T1]{fontenc}` (§2.3).
- [ ] From ICML: delete `\twocolumn[...]`, all `\icml*` commands, `\printAffiliationsAndNotice`, and `\onecolumn` in the appendix; restore `\title{}`/`\author{}` + `\maketitle`.
- [ ] Leaving ICLR: keep `\input{math_commands.tex}` if the text uses its macros (ported papers typically keep `\argmax`, `\sign`, `\Tr`, `\ab`; `preamble.tex` B5 defines them with guards), otherwise drop it after 3.3.
- [ ] Link colours per §1.1 (S).

### 3.2 Citations
- [ ] → ICLR: `perl -pi -e 's/\\cite\{/\\citep{/g' chapters/*.tex <main>.tex` (required; the ICLR sty does not remap `\cite`). → ICML: optional, because `icml2026.sty` maps `\cite` to `\citep`; convert only `\cite[...]` with an optional argument. Do not convert `\citeauthor`/`\citeyear`. Remove `\PassOptionsToPackage{numbers, compress}{natbib}`.
- [ ] → ICML: order multi-key `\citep{}` chronologically by hand (K-ICML26 p1).
- [ ] → NeurIPS: add `\PassOptionsToPackage{numbers, compress}{natbib}` before the sty (without it: "[Doe et al., 2025]", verified). Optionally `perl -pi -e 's/\\citep\{/\\cite{/g'` (S [most]: house style writes `\cite` at NeurIPS; `\citep` also prints [n]).
- [ ] Table row labels `Name (\citeauthor{}, \citeyear{})` work in both modes [some]. Keep them.
- [ ] Check that no citation is used as a noun ("[3] shows", "as in \cite{X}"): house style names the method and then cites it (S [all]). Numeric-to-author-year conversion breaks such sentences.

### 3.3 Equation references
- [ ] ICLR → NeurIPS/ICML: `perl -pi -e 's/\\Eqref\{([^}]*)\}/Equation~\\ref{$1}/g' chapters/*.tex` (same output: `\Eqref` = "Equation~\ref").
- [ ] NeurIPS/ICML → ICLR: `perl -pi -e 's/Equation\s*~?\s*\\(?:eq)?ref\{([^}]*)\}/\\Eqref{$1}/g; s/(?<![A-Za-z])\\eqref\{/\\Eqref{/g' chapters/*.tex`. After `math_commands.tex` is loaded, "Equation \eqref" prints "Equation equation 1" (verified).

### 3.4 Floats: one column ↔ two columns
**NeurIPS/ICLR → ICML (1 → 2 columns)**
- [ ] Every `wrapfigure` becomes `figure[t]`: one plot at `\columnwidth`, a pair as two `subfigure[t]{0.49\columnwidth}` + `\hfill` [venue: ICML]. The key-idea wrapfigure becomes `figure[t]` at `\columnwidth`. Remove `wrapfig` if it is now unused.
- [ ] Replace `\textwidth` with `\columnwidth`/`\linewidth` in every non-star float and in main-text `tcolorbox` widths (`\textwidth` = 6.75 in overflows into the other column). Appendix floats may keep `\textwidth` after `\onecolumn`.
- [ ] Framework and three-panel rows stay `figure*[t]` (0.9\textwidth [venue: ICML]). They print 25% larger (6.08 vs 4.84 in): rebuild each diagram at the new width (`% target-width-in: 6.10`, `render_diagram.py`) rather than scaling it, so its text stays 7 pt.
- [ ] Main table stays `table*[t]`. Put its source one column before the place it should appear (a `figure*`/`table*` floats to the next page at the earliest). Tables with ≤5 columns become `table[t]` + `\shrinktofit{0.93\columnwidth}{...}` [venue: ICML] (pitfall 4: `\resizebox` also enlarges).
- [ ] Re-export plots for 3.25 in: 7.7 in figures at `\columnwidth` give ~7 pt ticks (`plot_style.py`; `figures_tables.md` §4).
- [ ] Break long displays with `aligned` [venue: ICML]. Math-heavy types T and L lose the most space here.
- [ ] Algorithm: single-column `algorithm[t]`; the skill preamble's `\algoboxshiftx` (−0.6em) works in both layouts; check the shading after two passes.
- [ ] Theorem counters (kit preference, not a rule; K-ICML26 p4): the skill's preamble B6 keeps the house numbering (`math.md` §2; `preamble.tex` B6). To follow the kit, replace B6 with the template block from the kit's `example_paper.tex` (`\newtheorem{theorem}{Theorem}[section]`, with proposition/lemma/corollary/definition/assumption/remark on `[theorem]`); a restatable headline theorem then becomes `\begin{restatable}{theorem}{MainTheorem}`.
- [ ] Table shading: gray `oursrow` → `oursrowblue` (K-ICML26 p3 gray-background rule).
- [ ] Delete every negative `\vspace` between a graphic and its `\caption` (≥0.1 in caption gap, §1.2 Spacing; `grep -n -B1 '\\caption' chapters/*.tex | grep 'vspace{-'` must be empty).
- [ ] Appendix: `\newpage\appendix\onecolumn` [venue: ICML].

**ICML → NeurIPS/ICLR (2 → 1 column)**
- [ ] `figure[t]` at `\columnwidth` → `0.59\textwidth` for the same print size. Better: merge two or three column plots into one `figure*[t]` row of subfigures at 0.49/0.32\textwidth [most], or, for NeurIPS only, a `wrapfigure{r}{0.52\textwidth}` pair [some].
- [ ] `figure*` at 0.9\textwidth becomes at most `\textwidth` (5.5 in): rebuild each diagram at its new printed width (`% target-width-in: 4.95` for 0.9\textwidth) instead of letting LaTeX shrink its labels by 10-20%.
- [ ] `table[t]` at `\columnwidth` → `\shrinktofit{0.75\linewidth}{...}`, which does not enlarge.
- [ ] Remove `\onecolumn`; appendix floats unchanged.

**NeurIPS ↔ ICLR (both one column, same 5.5 × 9 in block)**
- [ ] Floats keep their widths. NeurIPS → ICLR: wrapfigures are allowed by the ICLR kit, but they are a `[venue: NeurIPS]` practice, not house practice at ICLR. Following house practice, turn each pair into a subfigure row, or merge two pairs into one `figure*[t]` row of four panels at 0.24\textwidth (costs height, see 3.6).
- [ ] Nothing else in the layout changes (the kits' paragraph and heading rules match; verified word density).

### 3.5 Statements and checklist

| From → to | Remove | Add |
|---|---|---|
| → ICLR | NeurIPS checklist (`\input{checklist}`), ICML Impact Statement, the `ack` env | `chapters/8_ethics_reproducibility_statement.tex` before `\bibliography`, containing **AI Use Statement (2027 required)** + Ethics + Reproducibility (≤1 page each; K-ICLR27). Fold NeurIPS Broader Impacts or the ICML Impact Statement into the Ethics Statement [some]. The reproducibility paragraph *points* to sections, appendix and code. 2026-style kits: "Use of LLMs" as the last appendix section |
| → NeurIPS | ICLR statements (move their content into checklist justifications and appendix sections), ICML Impact Statement | the year's `checklist.tex` (16 items in 2025/26) after `\clearpage`, following the appendix, with every answer justified with `\ref`. Make sure the labels exist: `sec:limitations`, `apdx:impl-details`, `apdx:broader`, `apdx:licenses`, `apdx:prompts`. Appendix "Broader Impacts" (expand the ICLR Ethics or ICML Impact text into Benefits / Risks / Safeguards [some]); Appendix "Asset Licenses" table (caption above, no `\small`); a "Compute disclosure" run-in [some]. Describe LLM use in the experimental setup (or equivalent) only if the LLM is an important, original, or non-standard component of the method (K-NIPS26 handbook; checklist Q16) |
| → ICML | checklist, Ethics/Reproducibility statements, Broader Impacts and Asset Licenses appendices (optional to keep), the LLM-use section | `\clearpage \section*{Impact Statement}` before `\bibliography`: the house three-sentence pattern (`structure.md` §10; `main_skeleton_icml.tex`) or the kit's verbatim default (K-ICML26 p5). Cut the abstract to 4-6 sentences |
| any | acknowledgements at submission | camera-ready only: ICLR `\subsubsection*{Acknowledgments}`, NeurIPS `ack`, ICML `\section*{Acknowledgements}` next to the Impact Statement |
| any | – | keep the numbered Limitations section (S [most]; NeurIPS Q2) |

### 3.6 Page budget re-allocation
Measured capacity (§1.1): one-column ICLR ≈ NeurIPS ≈ 680-700 prose words per full page; ICML ≈ 850-900 (×1.27). Floats, display math and headings take ≈40-47% of the main text [all] (≈ 1 − words ÷ capacity ÷ pages): the lower end with NeurIPS wrapfigures, ≈45% for an 8-page ICML paper with about 12 floats (§2.4), the upper end for a one-column paper with large figures.

| Direction | Words | Floats and sections | Evidence |
|---|---|---|---|
| NeurIPS 9 → ICLR 9 | keep (same capacity). **[fix] (`structure.md` §5A)**: "ICLR ... ~5% less text than NeurIPS" is not supported; both kits specify identical paragraph rules and the compile test gives the same words per page | converting 4 wrapfigure pairs into rows costs ≈0.3-0.5 pp → move one result figure to the appendix, or merge rows | compile test |
| NeurIPS/ICLR 9 → ICML 8 | target 3,650-3,900 (`structure.md` §5C; §2.4): keep about the same word count | column floats are relatively taller and `figure*` grows ×1.26, so move 1-2 floats to the appendix instead of cutting words. Move first: extra backbones/sizes → sweeps → case study → the second mechanism figure → the Literature Review subsection → Preliminary detail (`structure.md` §6). Keep every equation needed to re-implement | `structure.md` §5C; §2.4 |
| ICML 8 → NeurIPS/ICLR 9 | keep the word count (one-column 9 pp holds 3,500-3,850) | merge column plots into subfigure rows; restore a float only if the last page is short; NeurIPS may use wrapfigures | SKILL Step 6.3 |
| any submission → camera-ready (+1 page) | words barely rise [most]: ≈3,750 prose words at 10 one-column pages; the 9th ICML page often goes entirely to floats | spend the page on floats and results brought back from the appendix, in the Experiments and the Introduction [most]; add acknowledgements; swap the anonymous link for GitHub | `structure.md` §4 |
| always | then build, and run `check_paper.py --venue B --stage ...`: `main-text-end` must land exactly on the limit, and that page must be full | – | S [all] |

### 3.7 Bibliography
- [ ] Change `\bibliographystyle` (unsrtnat ↔ `iclr<Y>_conference` ↔ `icml<Y>`). The reference order flips between citation order and alphabetical; the `.bib` needs no edits.
- [ ] Protect capitals in titles (all three `.bst` lowercase them, verified); ICML: add page numbers where DBLP has them.
- [ ] NeurIPS → author-year: look for sentences whose meaning depended on "[n]".

### 3.8 Stage and anonymity toggles
- [ ] Submission: ICLR `% \iclrfinalcopy`; NeurIPS no option; ICML no option. No acknowledgements. Anonymous code link. Self-citations in the third person. No identifying URLs or compute details that name the institution.
- [ ] Camera-ready: ICLR `\iclrfinalcopy`; NeurIPS `[main,final]`; ICML `[accepted]`. Acknowledgements, GitHub link (anonymous link kept as a `%` comment), NeurIPS checklist kept.

### 3.9 Verify (greps + tools)
```bash
V=icml   # target venue
python3 scripts/check_paper.py <paper_dir> --venue $V --stage submission    # §4
grep -n '\\cite{'            chapters/*.tex     # ICLR: must be empty (use \citep); ICML: \cite is fine
grep -n '\\cite\['           chapters/*.tex     # ICML: must be empty (\cite takes one argument)
grep -n '\\Eqref'            chapters/*.tex     # NeurIPS/ICML: must be empty
grep -n 'wrapfigure'         chapters/*.tex     # ICML: must be empty
grep -n 'checklist\|Impact Statement\|Ethics Statement\|AI Use Statement\|Use of LLMs\|iclrfinalcopy\|onecolumn\|numbers, compress' *.tex chapters/*.tex
pdffonts main.pdf | grep -i 'type 3'            # must be empty (NeurIPS, ICML)
```
Then look at every rendered page: teaser position, `figure*` pages, no overlap from the negative `\vspace` of stacked floats, and the last main page full.

---

## 4. What `scripts/check_paper.py` checks

Stdlib only. Usage: `python3 scripts/check_paper.py <paper_dir> [--venue iclr|neurips|icml] [--stage submission|camera] [--limit N] [--vocab PATH]`.

| Check | Why (evidence) |
|---|---|
| `VENUES` table with the last-verified year, submission/camera limits, columns, kit and bst regex; `--stage`; `--limit` overrides | limits change (K-ICLR25 → K-ICLR26); +1 page at camera-ready in all three (§1.2) |
| detects the venue and kit year from the main `.tex`; warns when `--venue` disagrees; prints a NOTE when the kit year is newer than `verified` | §1.0 procedure |
| camera-stage word budget (ICLR 3,600-3,900; NeurIPS 3,750-4,150; ICML 3,800-4,550, i.e. 8 pp of text + a used 9th page); the extra words go to Experiments (0.6) and Introduction (0.4) | `structure.md` §4-5; §3.6 |
| counts and lints only the chapter files the main `.tex` really `\input`s | `structure.md` §3; superseded drafts stay in the folder [all] |
| ICLR: bst; Ethics/Reproducibility (info); 2027: AI use statement before the references; 2026: "Use of LLMs" present; `\iclrfinalcopy` at submission | K-ICLR27, K-ICLR26 |
| NeurIPS: `numbers` natbib option; checklist after refs/appendix (kits ≥2025), questions = answers, no TODO; Broader Impacts / Asset Licenses (info); `final`/`preprint`/`ack` at submission; 2026 camera-ready track option | K-NIPS25, K-NIPS26 |
| ICML: Impact Statement before the references; `\printAffiliationsAndNotice`; no `wrapfigure`; no `\textwidth` in one-column floats; `figure*`/`table*` only `[t]`; abstract 4-6 sentences; `\onecolumn` appendix (info); negative `\vspace` count (info); `[accepted]` or acknowledgements at submission | K-ICML26; §2 |
| all venues at submission: non-anonymous "our code ... \url{}" links; first-person self-citation | K-NIPS25 p3, K-ICML26 p2 |
| lint: ICLR `\cite{`, `\citet{`, bare `\eqref{`; NeurIPS `\citep{` (S); ICML `\cite[` (one-argument macro), scaled math and negative `\vspace` right before `\caption` (≥0.1 in caption gap); negative `\vspace` at a heading; duplicate `\label`s | compile tests; §5 pitfalls |
| `== concepts`: reads the `% concept: phrase \| core/framing \| provenance \| definition` table lines; counts core (2-5, max 6) and framing (≤6) concepts, marked uses (8-15, cap 20) and distinct phrases (≤12); flags undeclared marks, declared core concepts never marked, missing provenance, marks outside the Introduction and the first formal definition, a phrase marked more than twice, bold/italic on a concept; prints an echo table (mentions per section) with NO ECHO / weak-echo hits; notes when the colour is still ON at submission | `concepts.md` |
| `== intro & contributions` (warnings): contribution-list phrasing and "we argue" in the intro; "novel" / self-referential "first" / "for the first time" in abstract, intro and conclusion; "To the best of our knowledge", "Different from them", "Our work differs from", "is not our contribution", sentence-initial "Ideally," anywhere; abstract / intro paragraphs after the proposal ending on however / unclear / any benefit; each `% epithet: <phrase>` counted in abstract, Introduction, Methodology opener and Conclusion; demonstrate / validate / verify / prove / consistently / significantly in the main text while tables hold `\pend` or `--` cells | `introduction.md` IR12, IR14; `contributions.md` KR1, KR2, KR7, KR10, KR14; no warnings on the house papers [all] |
| `== terms` (warnings): reads the `% concept:` and `% term:` rows as the term table; warns when a row lacks a citation key (looked up in the `.bib`) or, for borrowed terms, a quote (≤ 15 words), when more than 2 rows are coined, when a "(termed X)" in the live text has no row, and on sense-clash words in the live text (probe/probing outside a linear-probe context, pre-registration, seed noise, X-aware, base update) with a hint | `concepts.md` §3 |
| `== vocabulary`: hyphenated-compound rate per 1,000 main-text words (hit above 41), the one-off compounds, and, only with `--vocab PATH`, long low-frequency words missing from your own word list (declared concepts excluded) | `prose.md` "Plain words, no coinage" |
| lint noise control: `checklist.tex` skipped; the main `.tex` linted only after `\begin{document}`; one report per line and rule | test runs on complete papers |

## 5. Pitfalls this skill corrects

Common slips in real papers and in earlier template versions. The templates and `check_paper.py` already apply the correct form.

| Pitfall | Correct practice | Evidence |
|---|---|---|
| Unicode ↑/↓ typed in table headers | `$\uparrow$`/`$\downarrow$`; the Unicode glyph drops out under XeLaTeX with Times | compile test; `check_paper.py` lint |
| Appendix table (or licence table) caption below the table, with `\small` | caption above in the appendix too; width only by scaling | K-ICLR26 p2-3; K-NIPS25 p3-4; K-ICML26 p1 |
| Negative `\vspace` between graphic and `\caption` inside ICML floats (−3 to −8 pt) | removed; ICML needs ≥0.1 in before and after captions. The post-label trim is also dropped on a float stacked above another (skeleton: Figure 2 over Algorithm 1 and Figure 6 over Table 2 had only 0.01-0.03 in) | K-ICML26 p3-4; `icml2026.sty` l.681; skeleton render |
| Negative `\vspace` at headings or in a chapter's first line | only inside floats | K-ICML26 p1; K-ICLR26 p6 |
| `\resizebox{w\linewidth}{!}{...}` on every table | `\shrinktofit{w\linewidth}{...}`: scales only when too wide, never enlarges | `preamble.tex` B8 |
| `[table]{xcolor}` loaded after the venue sty ("Option clash") | `\PassOptionsToPackage{table}{xcolor}` before the sty | §2.3 |
| No `T1` fontenc under XeTeX/tectonic (silent Latin Modern fallback) | `\usepackage[T1]{fontenc}` (preamble B1; ICML skeleton) | §2.3 |
| ICLR "Use of LLMs" last in the appendix; statement names only a few categories | ICLR 2027: required `\section*{AI Use Statement}` **before the references** (≤1 page, not counted), naming all 12 required categories in the kit's four-part form; the appendix variant stays commented for 2026-style kits | K-ICLR27 l.397-412; AI Policy |
| NeurIPS camera-ready with `[final]` alone | `[main,final]` (track option required in 2026) | `neurips_2026.sty` l.47-69 |
| ICML `\cite` described as textual; `\cite[p.~3]{x}` | `\cite` is parenthetical (`\citep`) and takes one argument; write `\citep[p.~3]{x}` | `icml2026.sty` l.209 |
| `\yrcite{}` described as "the year only" | prints the year in parentheses, "(1959)" (`\citeyearpar`) | `icml2026.sty` l.208 |
| "Equation \eqref{}" or bare `\eqref{}` under ICLR's `math_commands.tex` | `\Eqref{}` (prints "Equation 1") | `math_commands.tex` l.47-49 |
| ICLR `\section*{Acknowledgments}` | `\subsubsection*{Acknowledgments}` | K-ICLR27 l.450-451 |
| `\textwidth` in ICML column floats and boxes; gray shading behind text at ICML; `figure*` source placed where it should print | `\columnwidth`/`\linewidth`; `oursrowblue` or metric-row tints; `figure*` source one page early | K-ICML26 p3-4 |
| Checklist dropped at camera-ready or commented out; "Yes" without backing text | keep it in every build; every "Yes" points to a sentence or section | K-NIPS25 p6 |
| "7.7 in → `\columnwidth` (ICML) or 0.45\textwidth" | one-column equivalents 0.59\textwidth and 0.29\textwidth (0.45 prints 5.1 pt ticks) | width arithmetic §1.6 |
| "ICLR holds ~5% less text than NeurIPS" | same block and paragraph rules; same words per page when compiled | K-ICLR26 p1, K-NIPS25 p2; compile test |
| ICML budget of 3,300-3,600 words / "a full paper needs 9 pp" | 3,650-3,900 prose words fill 8.0 pp | §2.4 |
| ICML Impact Statement always the three formulaic sentences | the kit's verbatim default is also allowed; drop "significant"/"trivial" (calibrated claims) | K-ICML26 p5 |
| citeauthor row labels "never in NeurIPS numeric papers" | they work in both modes | compile test |
| "Switch to the template's shared theorem counter" (which the skill never loads) | kit preference only; §3.4 says how to swap B6 for the template block | K-ICML26 p4 |
| `\printAffiliationsAndNotice{}` omitted | required even when empty ("Unbalanced write command" otherwise) | verified by compiling |
| A chapter file whose name no longer matches its content | name the file after its content (`chapters_icml/8_appendix.tex`) | `latex_source.md` §11 |
| `icml2026.sty` text block cited at l.262-263 | l.261-263 (`\textheight` 261, `\textwidth` 262, `\columnsep` 263) | `grep -n` on the official sty |
| "Section \ref{}", "Appendix \ref{}" (plain space) in templates | `Figure~\ref{}` etc. everywhere the skill writes its own text | `latex_source.md` §3 |
| ICLR 2027 limits unconfirmed ("2026 was 9/10") | confirmed: 9 pp at submission, 10 at rebuttal/camera-ready | K-ICLR27 l.131 + Author Guide |
| LLM-use appendix at NeurIPS "only if the LLM is a method component" | describe it in the experimental setup (or equivalent) | K-NIPS26 handbook |
