# Prose: abstract, introduction, transitions, phrase bank, revision rules, sentence structure

Evidence labels as in SKILL.md: `[all]` every source paper follows the rule; `[most]` the clear majority; `[some]` an option shown by a subset (use it when it fits, never as a checklist item); `[current]` / `[older]` practice that changed over time (follow `[current]`); `[venue: X]` a venue requirement or venue-specific practice; `[fix]` the skill corrects common practice; `[skill choice]` a convention the skill adds. Every example below is synthetic, written for the invented running methods of `paper_types.md` (DIVA, a data-filtering workflow; PriorTilt, a closed-form label-shift correction; CaliLoss, a calibration loss with a theorem; SynthBonus, a shaped RL bonus for molecule generation; and a findings paper on which training examples a network forgets). Rules marked "revision habit" record how drafts were revised; they are explicit authorial intent but `[some]` evidence.

## 1. Invariants (apply to every paragraph)

1. **Rule of two** `[all]`. Frame problems, limitations, components and objectives as a pair: "(i) ... and (ii) ...", "On the one hand ... On the other hand ...". Examples: two limitations of a prior loss and two properties of the new one; target fit vs prior fidelity (PriorTilt); difficulty scorer vs diversity scorer (DIVA); potential-based shaping vs ensemble penalty (SynthBonus).
   - **Scope.** Pairs are for problems, limitations, method components and goals. The intro P5 evidence roadmap and the number of RQs follow the experiments (3-4, "First, ... Second, ... Third, ..."), not the rule of two. Three-item lists appear only in taxonomies, challenge lists and attribution lists; an attribution list has two items by default, three at most.
   - **One-to-one mapping check** `[most]`: write the chain as a `%` comment in the outline and keep it true while drafting:
     `intro limitation (i) / (ii)` ↔ `method component (i) / (ii)` ↔ `Methodology subsection titles (or run-in heads)` ↔ `RQ / finding that tests each`. Same order, same words (the core-concept phrase) at every link. DIVA: loss of rare clusters / crowding by near-duplicates ↔ difficulty scorer / diversity scorer ↔ subsections "Difficulty Scorer" / "Diversity Scorer" ↔ the two run-ins of the mechanism RQ. A component with no limitation, or a limitation with no RQ, is a gap to fix.
2. **Every problem is followed by a remedy sentence**: "To address this / To this end / To fill this gap, we ..." `[all]` (every paper with a Preliminary).
3. **The Preliminary and Methodology open with a roadmap** naming their subsections in order: "We first ... After that, ... Then, ... Finally, ..." (Methodology `[most]`; Preliminary `[current]`, while `[older]` papers start with the task formulation). The Introduction has no roadmap; the Experiments section opens with the RQ list; findings sections open with their RQs or hypothesis `[some]`.
4. **Bold run-in heads** `\textbf{Head.}` (period inside the braces); no `\paragraph` `[most]`. Setup heads are nouns (`\textbf{Models.}`, `\textbf{Baselines.}`); component heads name the term (`\textbf{Coverage term.}` … `\textbf{Total objective.}`); finding heads are full sentences.
5. **Findings are bold full sentences** after "We have the following observations:" `[most]`. "We observe that:" is `[older]`; use the current form.
6. **No takeaway boxes, "Obs." labels, Remark boxes, or italics for emphasis** `[all]`. Bold carries all emphasis. Italics appear only for a term at its definition (`\emph{support drift}`, `\emph{score collapse}`) and inside the single bold-italic question `[some]`.
7. **Definitions in parentheses**: `(\ie ...)` defines, `(\eg ...)` exemplifies; 6-26 `\ie` per main text `[all]`.
8. **The second sentence of a paragraph usually starts with "Specifically,"** (3-7 per main text) `[all]`. "To be specific," is `[older]` and retired.
9. **Details go to the appendix with a pointer at the end of the paragraph** `[all]`.
10. **Named twin concepts** are introduced once and reused verbatim in titles, subsection titles, experiment titles and the conclusion `[current]`. Three phrasings exist for introducing the pair; **use one per paper**:
    - "(termed X)" after the description: "it should explain the new data (termed *target fit*), while moving only as far as the evidence allows (termed *prior fidelity*)" `[current]`.
    - "By X, we mean ...": "By *target fit*, we mean that ...; by *prior fidelity*, we mean that ..." `[older]`.
    - "X, meaning that ...": "(i) *target fit*, meaning that ..., and (ii) *prior fidelity*, meaning that ..." `[older]`.
    Keep "(termed X)" for the core pair: the number of "(termed" in the main text should not exceed the number of core concepts (source papers: 1-3; `check_paper.py` reports it).
11. **Running example** `[most]`. Pick one concrete example (one input, one question, one prompt) and show the same example in **at least two places**: the teaser, the framework figure, and one sentence in the text ("Consider Figure~\ref{fig:teaser-figure} as an example: the photo labelled ``cat'' is ..."). Long examples (generated text) make two places the minimum, not every figure.

## 2. Abstract

Rules:
- One paragraph; NO numbers, NO citations, NO dataset names `[all]`. A headline number ("up to <k>× faster") is cut from the abstract before submission and moves to intro P5.
- Length at most 250 words `[most]`; `[current]` abstracts are shorter. Aim for 120-200 words at ICML and NeurIPS, and at most 250 at ICLR; there is no evidence for a stricter target. Sentences: 5-11 across the sources (`[older]` up to 11, `[current]` 5-6); target 5-9; ICML asks for 4-6 `[venue: ICML]`.
- Expand every acronym at first use (large language models (LLMs), expected calibration error (ECE), reinforcement learning (RL)). An acronym method name is expanded with bold initials `[some]` (\textbf{D}ifficulty- and d\textbf{IV}ersity-\textbf{A}ware filtering); a non-acronym name is bold in some papers and plain in others `[some]`.
- Firm on results ("outperform", "clearly improves ... while keeping ... intact"); hedged on interpretation ("suggests a possible", "potential applications").
- Symbols only when they name the new signals: "$s^{\text{diff}}$ and $s^{\text{div}}$" (DIVA).
- Code: submission `% Our code is available at \url{https://anonymous.4open.science/...}`; camera-ready "Our code is available at \url{...}." (singular "code") `[some]`.
- One bold sentence only in findings papers, for the headline finding `[some]`.

Slot template (method paper). Patterns are skeletons; the example sentence shows one filled slot (DIVA unless noted).

| Slot | Function | Pattern · example |
|---|---|---|
| S1 | context + importance fused with the difficulty | "Although X underpins Y, doing Z directly is hard: A, and B." · "While X matters for many practical systems, it stays difficult because ..." · "As X moves into deployed systems, making it ... becomes a requirement for trustworthy use." · e.g. "Although data filtering largely decides what a model learns, choosing examples during training is hard: useful examples are rare, and their value changes as the model improves." |
| S2 | current paradigm / named prior work | "Recently, X has become a common way to achieve Y by ..." · "A recent method, <Prior>, approaches this by ..." · e.g. "A recent method, <Prior>, scores the whole pool once with a small proxy model and keeps the top-ranked examples." |
| S3 | gap, as a pair | "However, this simple ... suffers from two key drawbacks: (i) ...; and (ii) ..." · "However, applying X uniformly forces a trade-off between A and B, because ..." · e.g. "However, this one-pass scheme has two key drawbacks: (i) coverage shrinks, since rare examples are dropped by a fixed score; and (ii) near-duplicates crowd the kept set, since similar examples receive similar scores." |
| S3b | why prior fixes fall short (opt.) | "Earlier attempts such as A and B reduce this trade-off, but without formal guarantees they remain fragile." · e.g. (PriorTilt) "Earlier fixes such as EM re-estimation and confusion-matrix inversion reduce this trade-off, but without any control on how far the prior moves they remain fragile." |
| S4 | desideratum (opt.) | "These issues call for a ... method that needs no ... and can ..." · e.g. (SynthBonus) "These issues call for a bonus that is cheap enough for every rollout and robust to the errors of its estimate." |
| S5 | proposal | "To resolve both, we propose X, which adds ... for ... and ... training." · "Motivated by recent ..., we propose \textbf{X} (\textbf{X}...), which improves ... by ..." · e.g. "To resolve both, we propose DIVA, which equips the training loop with two learned scorers for cheaper and more faithful filtering." |
| S6 | mechanism | "Specifically, X ... only when ..., and ... as soon as ..." · "Specifically, it treats X as a learnable ... with two objectives: A and B. For A, it learns to .... For B, it learns to ...." · e.g. "Specifically, DIVA raises the sampling weight of examples the model still gets wrong, and caps how many examples any one cluster may contribute." |
| S7 | how the capability is trained (opt.) | "To give the model this ability, we design two ... signals within ..., which reward ... and ... respectively." · e.g. "To give the scorers this ability, we train them with two feedback signals, $s^{\text{diff}}$ and $s^{\text{div}}$, derived from held-out loss changes." |
| S8 | main result, qualitative | "Experiments on several A and B show that X is effective, clearly improving ... while keeping ... intact." · "We find that ... give dependable ... even without ..., which lets X outperform ..." · e.g. "Experiments on several models and benchmarks show that DIVA is effective, reaching higher accuracy at lower filtering cost." |
| S9 | bonus finding (opt.) | "In addition, when reused as ..., these ... also improve ..." · e.g. "In addition, when reused as a data-valuation signal, the learned scores also improve active labelling." |

Findings variant `[some]`: known phenomenon with an inline definition ("Prior studies have observed that ... (\ie ...)") → gap ("Although ... is widely reported, why it happens is still poorly understood.") → lens ("We study this question through the lens of ...") → **bold headline finding** → "Specifically, we show ..., which indicates ..." → "We then find ..." → "We further show ..." → "Finally, we show how the finding can be put to use ..." → "These results clarify ... and give practitioners tools for ...". Example chain: networks forget some training examples repeatedly during training → which ones, and why, is unclear → per-example forgetting counts tracked across seeds → **rare-class examples are forgotten first** → the order is stable across seeds and architectures → it flags likely label errors at almost no cost.

## 3. Introduction

This section gives the paragraph templates; the sentence-to-sentence logic (write the argument chain first, root-cause sentence, standard before weaknesses, key idea as the negation of the root cause, challenge sentence, prior-work moves) and rules IR1-IR15 are in `introduction.md`; the epithet and contribution devices are in `contributions.md`.

Rules:
- 620-830 prose words, 4-6 paragraphs, at most one bullet list and one teaser.
- NO contribution bullet list, NO "the rest of this paper is organized as follows" `[all]` in final versions; drafts that had a list deleted it.
- Citations: 2-5 keys per claim, before the period; the method name precedes its citation ("the optimizer Adam \cite{Adam}").
- BibTeX keys are the short name the text uses `[all]`: methods `Adam`, `Dropout`, `LayerNorm`; models `ResNet`, `ViT`, `BERT`, `T5`; benchmarks `ImageNet`, `CIFAR10`, `GLUE`, `SQuAD`; surveys `Survey-Calibration`, `Survey-DataSelection`; other papers a hyphenated short title (`deep-double-descent`, `lottery-ticket`). That is what makes "Adam \cite{Adam}" and "+ LayerNorm (\citeauthor{LayerNorm}, \citeyear{LayerNorm})" natural. Keys are reused across papers `[all]`: one personal .bib whose keys never change. Rename the rare Google-Scholar keys (`smith2024scaling`). Full list in `templates/references.bib`.
- Teaser panels referenced one by one: "As illustrated in Figure~\ref{fig:teaser-figure}", "As shown in Figure~\ref{fig:teaser-figure}(b)".
- Notation starts in the intro: P4 states the core formula inline `[most]`: PriorTilt $\boldsymbol{\pi}^{\star}\propto\boldsymbol{\pi}^{\text{src}}\odot\exp(\bar{\mathbf{p}}/\lambda)$; DIVA $p_t(x_i)\propto w_i\,q_{c(i)}$; SynthBonus $\tilde{r}_t=r_t+\gamma\Phi(\hat{\mathcal{G}}_{t+1})-\Phi(\hat{\mathcal{G}}_t)-\beta\sigma(\hat{\mathcal{G}}_{t+1})$.
- Concrete quoted examples inside the text `[some]`: `(\eg a blurry photo labelled ``cat'')`, `(\eg the phrase ``not bad at all'')`. Make it the running example (§1 rule 11): the same example in the teaser and the framework figure, and one sentence that walks through it.
- No numbers, except bounded headline claims in P5 ("up to <k>×") or one number per teaser-anchored finding in analysis papers.
- Forward references `(\cf Section~\ref{...})` at the end of each result sentence in P5 `[current]`; theory pointers `(\cf Theorem~\ref{})` `[some]`.
- Key concepts marked with `\concept{}` (one orange colour while drafting, black at submission); selection, borrow-first naming, budget, echo and consistency: `concepts.md`.

Five-paragraph template (method paper):

**P1 — context → importance → current practice → gap and consequence (2-4 sentences).**
"X \cite{} is central to how models ..., and its quality largely decides ... \cite{}. Yet X is usually treated as ..., often as a one-off preprocessing step. This leaves ... unconstrained: [explanation]. As a result, training can drift toward ..." (DIVA) · "X is a key capability for deploying models in practice \cite{}, for instance in ... \cite{}. Despite its importance, X remains hard: ... \cite{}, and ... \cite{}."

**P2 — one of:**
- (a) prior paradigm made concrete `[some]`: "A recent line of work, led by <Prior> \cite{<Prior>}, recasts ... as a ... process. As depicted in Figure~\ref{fig:teaser-figure}, rather than ..., it treats ... as .... Concretely, at stage $k$, .... In practice, .... Optimized jointly with ..., this paradigm has shown promise in reducing ...".
- (b) challenge + the single central question `[some]`: "Yet ... directly is hard in practice, for two reasons: (i) ... \cite{}, and (ii) .... `\textit{\textbf{Can ... ?}}` If so, ... could be ... during training rather than ... afterwards, at little extra cost." Bold italics are reserved for this one question.
- (c) findings papers `[some]`: "Prior work has documented two properties of X .... On the one hand, .... On the other hand, .... Yet what X actually encodes is still poorly understood."

**P3 — one of:**
- (a) the pair of limitations as bold-headed bullets `[some]`: "However, <Prior> carries over several weaknesses of ... \cite{}. We identify two limitations that can hold back ... in practice:" then `\begin{itemize}[leftmargin=*] \item \textbf{Loss of Rare Clusters.} ...`. Avoid opening this sentence with a bare "Here," (§9 rule 3). Each bullet 3-5 sentences: mechanism, consequence, amplifier, gap sentence.
- (b) two prevailing strategies and their shared flaw `[some]`: "Two strategies dominate: A \citep{} and B \citep{}. ... However, both rest on heuristics and offer no guarantee .... Intuitively, an effective correction should ...: it should explain the new data (termed *target fit*), while moving only as far as the evidence allows (termed *prior fidelity*). This gap calls for a more principled design." (PriorTilt)
- (c) an enabling line of work `[some]`: "A promising route comes from recent progress on ... \cite{}. On the one hand, .... On the other hand, .... What these methods share is that .... Taken together, this makes it plausible to .... Yet how to ... is still largely unexplored." (SynthBonus)

**P4 — proposal → one-sentence key idea ("The key/core idea is to ..." `[most]`; split as "(i) ... and (ii) ..." when the method has two components) → mechanism with notation → training → benefit.**
"We therefore propose **X**, a cheap and faithful ... framework, motivated by the success of ... in ... \cite{} (\eg ...)." → "The key idea is to equip ... with two ... --- a ... and a ... --- that set (i) how much ... and (ii) how many ...." (DIVA) → "Figure~\ref{fig:main-figure} gives an overview of the process." → "Specifically, [roles, symbols, inline formula]. An item is drawn more often when ...; otherwise .... In addition, .... Finally, ...." → "Here, $\Phi$ is ... and $\sigma$ is ... (\ie $...$), because ...." (SynthBonus) → "To give the model this ability, we train it with ..." → benefit closer: "The A keeps ..., avoiding ..., while the B supplies ...; jointly, they make ... both cheap and faithful." (DIVA) / "Overall, X lets Y go beyond ... by ..." (SynthBonus).

**P5 — evidence roadmap instead of a contribution list.** One sentence per experiment subsection, in RQ order; the number of sentences follows the RQs, not the rule of two:
"We then run extensive experiments to test X. First, X generally outperforms ... across ... (\cf Section~\ref{sec:main-performance}). Second, ... (\cf Section~\ref{}). Third, ... (\cf Section~\ref{})." Closer: "We highlight that X is simple and effective, making it an easy drop-in for ..., with no need for ...." / "We highlight that X offers a ... paradigm built on ...." / "Overall, these results point to a paradigm in which ..., opening a path toward ...."

Findings-paper P3-P5 `[some]`: "To close this gap, we study ... through two questions: (1) ...? (2) If so, ...? ... A positive answer would imply that ...." → "We first run pilot experiments with ... on .... As Figure~\ref{fig:teaser-figure} shows, three empirical findings stand out:" + 3 bullets, each anchored to one teaser panel with one number, ending "This indicates that ..., which suggests that ...." → "Building on these findings, we show how they can be used for ...."

## 4. Transitions and signposting

| # | Pattern | Where | Example (synthetic) |
|---|---|---|---|
| T1 | Section roadmap: first → After that → Then → Finally | Methodology `[most]`, Preliminary `[current]` | "In the rest of this section, we first outline the key idea of DIVA in Section~\ref{} to give a high-level picture. Then, ... After that, ... We then ... Finally, ..." |
| T2 | Preliminary opener | Sec 2 | "This section reviews the background our method builds on, namely one-pass data filtering in Section~\ref{} and curriculum training in Section~\ref{}." |
| T3 | Base-paradigm closer = hand-off (the limitation the method removes) | end of the base-paradigm subsection `[all]`; a later background subsection may follow it | "Still, scoring every example once with a fixed proxy and keeping only the top of the ranking leave both the coverage and the precision of one-pass filtering far from ideal." (DIVA) |
| T4 | Method subsection opener = purpose clause answering the previous problem | 3.x | "To counter the shrinking coverage and the crowding by near-duplicates, we introduce two learned scorers, motivated by the success of ... \cite{}." (DIVA) · "To explain the unlabelled predictions of the new site for target fit, we aim to ...." (PriorTilt) |
| T5 | Callback between sibling subsections `[some]` | 3.x | "With target fit measured by $\langle \boldsymbol{\pi}, \bar{\mathbf{p}} \rangle$, we now turn to prior fidelity by ...." · "Building on Lemma~\ref{lem:gibbs}, we next derive ..." (PriorTilt) |
| T6 | Argue, then propose (motivation before definition) | 3.x | "While shaping speeds up learning, we argue that the policy may drift toward molecules where the estimate is wrong .... We therefore add ..." (SynthBonus) · target first: "Let us assume that we could minimize the calibration error itself .... However, its binned form has zero gradient almost everywhere .... The question, then, is how to ...." (CaliLoss) |
| T7 | So-what closer after an equation or paragraph | 3.x | "This bonus favours ...." · "In this way, we recover ...." · "The indicator equals $1$ when ..., and $0$ otherwise." |
| T8 | Forward / appendix pointer closing a subsection | everywhere `[all]` | "The next two subsections show how to meet these objectives. For brevity, we drop the site index ...." · "Further implementation details are given in Appendix~\ref{apdx:impl-details}." |
| T9 | Experiments opener + RQ list | Sec 4 | "In this section, we evaluate X by addressing the following questions." |
| T10 | RQ subsection opener = bridge from the previous RQ, then the topic | 4.x | "Given that the learned scores are reliable, we further test ..." · "To examine whether ..., we ..." · "Finally, we test whether ..." · then "In this section, we examine how ... (\ie the contribution of ...)." `[current]` · "Whether our method works hinges on whether ....". "In this section, ..." alone is not a bridge (§4b rule 5) |
| T11 | Observation block: report → lead-in → bold finding → evidence → interpretation `[all]`; attribution, contrast, closer and pointer as needed | 4.x | see `experiments.md` §5 |
| T12 | Pair devices | everywhere | "On the one hand, ... On the other hand, ..." `[most]` · "(i) ... and (ii) ..." · "..., rather than ..." · "In contrast, ..." |
| T14 | Chaining findings sections | findings papers | "This finding raises a natural follow-up question. \textbf{RQ3}: ..." |
| T15 | Positioning disclaimer `[some]` | findings papers | "We stress that our goal is to understand ..., not to design a new ... method." |
| T16 | Falsifiable prediction before a result | findings | "If forgetting depends on class frequency, then examples of rare classes should be forgotten earlier in every seed." · "A positive result would suggest that ...." |

(There is no T13; the numbers T14-T16 are kept stable because other files refer to them.)

Micro-level rules:
- Em-dash asides typed ` --- ` with spaces: "two learned scorers --- a difficulty scorer and a diversity scorer --- that decide ..." (DIVA).
- Emphasis budget: bold = method name at first mention, bullet/run-in heads, finding sentences, at most one key coined term in the intro (`\textbf{forgetting order}`) `[some]`; bold italics = the single research question `[some]`; no italics for emphasis.
- Hedging: hedge motivation, interpretation and outlook ("may stem from", "is likely due to", "could enable", "make it plausible", "pointing toward"); keep own results firm but bounded ("generally outperforms", "on most of the tasks", "largely unchanged", "nearly zero", "up to"). Never "always"; never "state-of-the-art"/"SOTA" (`[older]` only).
- References typed by hand with a tie: "Figure~\ref{}", "Table~\ref{}", "Section~\ref{}", "Appendix~\ref{}", "Equation~\ref{}"; no cleveref `[all]`. The sources mostly type a plain space; the tie only prevents a line break before the number `[fix]` (`latex_source.md` §3).
- Connectives: pick them by function from the library in §4a (four tiers), and check the cohesion rules in §4b.

## 4a. Connective library by function (four tiers)

Counted as the main link of each sentence over Abstract + Introduction + Method 3.1-3.2 + Experiments analysis, on the same three groups as the lexicon (`assets/lexicon/`): house = the 6 papers this skill imitates (748 sentences), refA = 8 earlier papers of one reference lineage (480 sentences), refB = 8 earlier papers of another (1,097 sentences). Numbers are uses as the main link of a sentence, with the rate per 100 sentences in parentheses where it matters. Tiers: **preferred** = house's current form (use first); **usable** = attested but rarer or older (fine once); **ref-only** = a reference lineage uses it, house 0 (avoid; tagged `[refA]`/`[refB]`/`[lab]` for "both lineages"); **foreign** = appeared only in machine-written drafts, 0 in all 22 papers (never). Sentence-initial unless marked "mid".

| Function | Preferred | Usable | Ref-only (avoid) | Foreign (never) |
|---|---|---|---|---|
| Contrast | However, 18 (2.4) · In contrast, 12 (1.6; H4 7, also mid ", in contrast,") · While / Although ... 7 | Despite ... 2 · Conversely / Nevertheless / Yet (H1, H2) | Nonetheless `[lab]` · Unfortunately (H1 only) | mid ", however," · "X, not Y" tails · "is not free, however" |
| Addition | Additionally, 17 (2.3; `[house]`, refB 0.4, refA 0) · Moreover, 18 (2.4) · We further / also ... (mid) | Furthermore (2; on the banned list, see §8) | Worse still `[refB]` (banned) | "Nor does it ..." |
| Elaboration | Specifically, 24 (3.2) `[refB]` · Here, X denotes / Here, we deem 17 (2.3) | That is, / In other words, 2 · Formally, 2 · In practice, / Concretely, | In particular `[refA]` · To be (more) specific `[refA]` · Technically `[refA]` · More formally | — |
| Example | For example, 5 · Consider(ing) Figure X as an example | For instance, | Taking X as an example, `[lab]` | — |
| Result | Therefore, 7 (`[house]`; reference lineages write Hence) · As a result, 4 · mid ", thereby V-ing" / ", limiting ..." · fronted "Since ..., ..." | Consequently, 2 | Hence, `[lab]` · Thus, (sentence start) · Accordingly, · As such `[refA]` | ", so" + clause · mid "therefore" · "whenever" |
| Response to a gap | To this end, 5 · To address this / these X, ~8 · To fill this gap, 4 (`[house]`) | To mitigate this, | Towards this end `[refA]` (refB also) | — |
| Basis | Based on these findings / this insight, 5 (`[house]`) · Motivated by / Inspired by ..., 6 | Guided by / Grounded in (H4) · Having ensured X, we now turn to Y (H4, one paper) · Building on Lemma X, | Benefiting from (H1 only) · We move on to / We begin with / Now, | "As a direct corollary of" |
| Sequence | First, ... After that, ... Then, ... Finally, (~20; "After that" `[house]`) · purpose infinitive "To evaluate / To further examine ..." (~20) | Second, / Third, | — | — |
| Pair | On the one hand, ... On the other hand, (~5 pairs, `[house]`) · (i) ... and (ii) ... | "..., rather than ..." | — | — |
| Condition | If so, (~8, `[house]`) · If ..., then ... | — | — | "Whenever ..., then" · "Ideally," (house writes "Intuitively, effective X should ...") |
| Summary / stance | Overall, / Together, / Generally, 4 (`[house]`) · We highlight that ... 2 (`[house]`) | — | Clearly / Obviously `[lab]` · In a nutshell `[lab]` · Analogously / likewise · One possible reason is that `[refA]` · We ascribe | "likewise" (mid) |
| Evidence pointer | As shown in / As illustrated in Figure X, (11-10-5 across three house papers; `[house]`) | — | — | bare "(Appendix~\ref{})" · "Table X lists / compares" |
| Section metatext | In this section, we ... (RQ openers, after a bridge; `[house]`) | — | a bare "Here we present ..." | — |

Frequency facts (per main text): Specifically 3-7 · However 2-6 · As shown in 0-9 · After that 0-6 · Additionally 0-8 · Moreover 2-9 · In contrast 0-6 · "We attribute" 0-3; rare: Furthermore (0-1), Notably (0-1), Consequently (0-1). Every entry above can be re-checked with `tools/ps.py` against the shipped index (e.g. `python3 tools/ps.py "Additionally," -q`) or your own corpus (`--corpus <dir>/corpus.jsonl -r "^Additionally,"`); the lexicon section "连接词（按功能）" holds 108 connective entries with their tier.

## 4b. Cohesion: every sentence links back

Label each sentence C = connective, A = anaphora ("This / These / Such / It ..."), L = lexical chain (repeats or rephrases a noun of the previous sentence), S = structural continuation (list item, run-in head, figure pointer), N = no link. Measured on the source papers: about 3% of sentences have no link (about 1% are real missing links), and nearly every paragraph opener links back. Machine-written drafts reach 10-25% unlinked sentences, mostly in the Experiments. The rules:

1. **Isolation ceiling.** At most 5% of non-first sentences have no link (N), and at most 2% are real missing links; Introduction and Method ≤ 3%, Experiments analysis ≤ 5% `[most]`. An unlinked sentence is allowed only right after a bold run-in head, a table/figure pointer, or at the start of an RQ subsection.
2. **Every non-first sentence carries a link** (C, A, L or S), and the known part sits in its first ~5 words (subject or fronted adverbial) `[most]`. Self-check: label each sentence C/A/L/S/N; for every N, say why the break is allowed.
3. **Connective density** (soft targets; `check_paper.py` prints them per chapter): share of connective-led sentences 45-55% overall, Introduction ≥ 55%, Method ≥ 45%, Experiments ≥ 35%; about 2.2 connective sentences per 100 words (source papers about 1.8-2.5). Do not replace connectives with lexical chains in the Introduction and Experiments: there L reads as listing, not arguing (L is fine in the Method). The script's automatic proxy reads a few points lower than hand labels and higher on isolation (on source papers: about 40-52% connective-led, Experiments 32-42%, approximate isolation 13-24% in the Experiments), so compare a draft with those proxy values and label by hand before concluding.
4. **Double lock and shell nouns** `[most]`. At every problem → solution turn in the Abstract, the Introduction and the Method subsection openers, combine connective + this/these/such + noun: "To address these issues", "However, this one-pass scheme ...", "To fill this gap", "As a result, such crowding ..." (about a quarter of the connective sentences in the source papers carry an anaphor). After a paragraph describes a phenomenon, pack it into a shell noun and move on: "This crowding lowers the coverage of ..." (DIVA), "This mismatch calls for a correction that ..." (PriorTilt), "These observations suggest ..." (findings paper). At least 3 shell-noun anaphors per Introduction; an A-type paragraph opener ("These findings suggest ...") is allowed.
5. **Every paragraph opener links back** to the previous paragraph (source papers: nearly all; every Introduction and Method opener). Method subsection openers already do this (T4). **RQ subsections open with a bridge**: a purpose infinitive ("To examine whether ..."), a basis clause ("Given that the learned scores are reliable, we further test ...") or "Finally, we test whether ..."; "In this section, we study ..." may follow the bridge but never replaces it.
6. **Paragraph hand-off.** A paragraph ends by naming the problem the next paragraph solves, and the next opener repeats that noun ("... leave both the coverage and the precision of one-pass filtering far from ideal." → "To improve coverage and precision, ..."). **An RQ subsection ends with its conclusion** ("Such results show ...", "This indicates ..."), with the appendix pointer before or inside that last sentence, never as the last sentence on its own.
7. **Pronoun reference.** "It / Its / This" refers to the subject of the previous sentence or to that whole sentence. If a sentence with a different subject intervenes (often after a page-fit cut), write the full noun ("The transfer table ...", "This term's ..."). A sentence-initial "Its" two sentences away from its noun is a known draft habit (habit 14 below).
8. **Cap the in-sentence ", so".** At most 1 per 1,000 words `[skill choice]` (absent from the main text of the source papers). If the second half is a new claim, split the sentence and open it with "Therefore," or "As a result,", or front the cause with "Since ...,". Result clauses stay in-sentence as ", thereby V-ing" / ", limiting ...".
9. **Reference: hedges and boosters.** Source papers use about 3 standard hedges per 1,000 words (may, might, could, potentially, likely, largely, somewhat; mostly "may" and "largely") and about 2 boosters (significantly, substantially, consistently, remarkably, notably, clearly), with the "effective" family near 3. A draft without results should not stack "is expected to": use "may", "is likely to" or "should", and switch to the result frames (show, outperform, "This indicates", "We attribute") once numbers exist.
10. **Reference: setup and list paragraphs.** When a run-in head names two things ("Scorers and baselines."), open the second half with "For the baselines, ...". Parallel specifications use a parallel frame ("On Model-S, ... On Model-L, ...") or "Additionally,". A rule that covers all items opens with "For each X, ..." (write "each", not "every"; habit 2).

## 5. Limitations (numbered section before the Conclusion) `[most]`

- 2-3 sentences, 35-60 words; no mitigation, no promise.
- Opener: "This work still has a few limitations." / "Our study has several limitations." / "Although X proves effective, this work still has limitations."
- Two items `[most]`. Preferred phrasing, not a rule: "On the one hand, [extra cost or stability issue] .... On the other hand, [scope: domain, scale, backbone] ... is still unknown / largely unexplored." `[current]` ("On one hand" also occurs). Otherwise "First, ... In addition, ..." or "First, ... Second, ..." `[some]`, or "For example, ... Moreover, ..." `[some]`. A paper may have no Limitations section `[older]`.
- Cross-link at least one limitation to an experiment `[some]`: an instability limitation matches the sensitivity study of the key coefficient ("..., consistent with the sensitivity to $\alpha$ reported in Section~\ref{sec:sensitivity}").

## 6. Conclusion `[all]`

- One paragraph, 3-6 sentences, 80-140 words (`[current]` papers sit in the lower half); mirrors the abstract; no new claims; numbers only as a bounded headline ("up to <k>×").
- Sentences: problem/gap → (why prior fixes fall short) → proposal ("To this end, this work presented PriorTilt, a training-free and bounded correction that targets both A and B.") → (mechanism: "Specifically, it secures A through ..., and B through ....") → evidence ("Experiments on several models and datasets showed that PriorTilt is effective, making it a practical choice for ....") → one-line future work ("We will further explore ...." `[some]`).
- Tense: past `[most]` ("we proposed", "presented") or present `[some]`. Keep one tense within the paragraph.

## 7. Related Works paragraphs (Appendix A)

Paragraph closers, the target line vs the enabling line and the forms to avoid ("Different from them", "Our work differs from them", "is not our contribution") are detailed in `introduction.md` §4 (RW1-RW13) and rule IR12.

Paragraph arc `[all]`: (1) significance ("Data selection has become a standard step in training large models ...") → (2) taxonomy ("Existing methods fall into two families, A \cite{} and B \cite{}. A methods .... B methods ...." / "One line of work ... A second line ...") → (3) representatives, 2-4 cites per sentence, one sentence per key method ("<Method> \cite{} instead ranks examples by ...") → (4) shared limitation ("Although effective, these methods risk [the paper's two problems].") → (5) bridge ("These results motivate our design of ...."; "..., yet this direction is still largely unexplored."; "However, how to ... is still an open question.").
- The last subsection ends with the positioning sentence: "To close these gaps, we propose a framework that ..., combining A with B."
- Reuse the RW taxonomy for the baseline groups in the experiments `[some]` (e.g. score-based vs gradient-based selection; EM-based vs confusion-matrix prior estimation).
- A self-citation cluster places the work in the group's own line of work `[some]`.
- Trim hard (≈380-500 words). No meta openers ("This section surveys the literature closest to ours." is `[older]`).

## 8. Phrase bank (current forms)

- **Importance:** "X is a key capability of large models for practical use \cite{}, for instance in ..." · "X has recently become a popular way to achieve Y \cite{}, with no extra training required."
- **Gap:** "Despite its importance, X remains hard: ..." · "However, <Prior> carries over several weaknesses of .... We identify two limitations that can hold back ... in practice:" · "However, these methods rest on heuristics without guarantees, which limits their reliability in ...." · "However, how to ... is still largely unexplored."
- **Inspiration:** "..., motivated by the success of ... in ... \cite{} for handling ... (\eg ...)." · "To this end, we build on recent ... studies \citep{} and propose X, a ... method that ...."
- **Proposal / core idea:** "The key idea is to equip ... with two ... --- a ... and a ... --- that set (i) how much ... and (ii) how many ...." · "The core idea is to turn ... into ...." · "To give the model this ability, we train it with ...."
- **Epithet** — pick ONE per paper and reuse it: "cheap and faithful", "provably faithful and practical", "simple and calibrated", "self-supervised and stable", "principled". Its two words negate the two drawback labels and appear verbatim in abstract, intro, Methodology opener and Conclusion (`contributions.md` D2-D3, KR2-KR3; declare it as `% epithet:`).
- **Section openers:** "This section introduces X, a ... for ...." · "In this section, we look more closely at ...." · "This section studies how ...." · "With X in place, we now turn to Y."
- **Equation glue:** "Formally, ... can be defined as follows:" · "..., which can be formulated as:" · "The closed-form solution ... is given by:" → "where ... denotes ..., and ... is a scalar hyperparameter controlling ...."
- **Results:** "As shown in Table~\ref{}, with and without ..., X outperforms Y on most benchmarks." · "We attribute X's gains to its ...." · "This is likely due to ...." · "In contrast, the baselines are weaker and less robust ...." · "Such results underline why ... matters." · "We therefore adopt ... as the default setting because ...."

Signature vocabulary: augment, endow (with), impede, amplify ("is amplified when"), mitigate, preserve, induce, attribute (to), verify/validate, highlight, uncover, reveal; crucial, vanilla (for the named baseline), naive, indiscriminate(ly), principled, fine-grained, informative, discriminative, largely, nearly, generally, consistently; the risk(s) of, trade-off, paradigm, key idea, drawback(s).

**Banned (dropped over time or on the house watch list):** delve, furnish, elucidate, underpin(ning), de facto, sentence-initial "Wherein,"; "Surprisingly", "sheds light on", "unleash the power of", "Worse still"; "state-of-the-art"/"SOTA"; "To be specific"; "Furthermore" and "Notably" (rare); "kindly refer to"; "we novelly propose"; uncountable nouns counted ("two key information"); "Codes"; "It's"; a redundant "\% times" ("a 30\% times speed-up").

**Older house-style markers (avoid):** Towards this end · As such · In particular · Distinct from · "the rationality and effectiveness of X" · "One possible reason is that" · "Best viewed in color." · Technically · Hence (sentence start) · Clearly / Obviously · In a nutshell · w.r.t. · "We believe X will inspire ..." · "In stark contrast" · "simple yet effective" as a repeated slogan (it may still be the paper's one epithet) `[older]`.

**Foreign expressions (never; absent from every source paper, typical of machine-written drafts):** "Ideally," · "Nor does it ..." · "likewise" · "X is not free, however" · "is an empirical question" · "As a direct corollary of" · ", so" + clause · "whenever" · "idealized" / "regime" / "headline" as labels `[skill choice]`. The full list with replacements is the habits table below.

## 9. Revision rules (each has a draft → final pair; the pairs below are synthetic)

1. Precise verb over generic verb + adverb (revision habit; endow, mitigate and impede recur across papers): "add two scoring modules to the training loop" → "equip the training loop with two scorers"; "To teach the scorer" → "To endow the scorer with"; "make later updates harder and may even block" → "impede later updates"; "gets even worse when" → "is amplified when". `\ignore{aims to improve}improves`.
2. Say what it does, not an abstraction: "shortcomings that come from how simple its one-shot design is" → "drawbacks of scoring each example only once".
3. Avoid a bare "Here," that adds nothing (revision habit); prefer "In practice," or "Concretely," when the sentence says how something is done. "Here, X denotes ..." after a display and "Here, we regard ..." stay in use `[all]`. Example: "Here, the two scorers ... are the same network" → "In practice, the difficulty scorer and the diversity scorer share one backbone $g_\omega$ and differ only in their output heads."
4. Shorten nominal phrases: "which makes it hard for these methods to transfer to new or shifting data distributions" → "limiting transfer to shifting distributions"; "adding unnecessary scoring passes and wasted GPU time" → "incurring redundant compute".
5. Split any clause of 40+ words (revision habit: a 45-word clause becomes two sentences).
6. State the core idea in ONE sentence, never vaguely (revision habit); split it as (i)/(ii) when the method has two components.
7. Soften overclaims, especially in bold claims and the abstract: "over all existing calibration losses" → "over most calibration losses"; "always encode" → "tend to encode", "consistently ... under every budget" → "... in most cases"; `\rebuttal{in most settings}` inserted into a bold claim.
8. Narrow the scope to a named prior method so reviewers cannot demand many baselines `[some]`: "prior work has studied" → "the recent method <Prior> \cite{<Prior>} has studied".
9. Remove numbers from the abstract.
10. Delete contribution lists and roadmap filler; replace rhetorical questions with numbered research questions `[some]`.
11. Unify terminology across text AND figures: "filter threshold/strength" → "filter strictness" (concept) + "kept fraction" (measure); $s_{\text{div}}$ → $s^{\text{div}}$; $\epsilon_k$ → $\epsilon_{k_1},\epsilon_{k_2}$ to match the figure.
12. Turn every vague pointer into a concrete `\ref`: "(\cf Theorem)" → "(\cf Theorem~\ref{thm:ece-bound})"; "in the Appendix" → "in Appendix~\ref{apdx:proof-bound}".
13. Drop politeness filler: "For the full proof, kindly refer to the Appendix" → "We sketch the main steps below (the full proof is in Appendix~\ref{}):".
14. Make statements direct: "When $\alpha=0$, CaliLoss falls back to the NLL loss, as one would expect." → "The NLL loss is the special case $\alpha=0$ of CaliLoss."
15. Compress observation lead-ins: "Looking at the two figures, it can be seen that as the filter strictness grows from 0.1 to 0.5, several observations emerge:" → "Raising the strictness from 0.1 to 0.5 shows the following:".
16. Merge short sentences or bullets into one denser unit (two thin bullets → one paragraph; a 7-sentence RQ paragraph → one tighter paragraph).
17. Drop trailing clauses that add nothing ("..., thereby improving the final accuracy" when the sentence already says so); cut a Preliminary closer that the next section repeats. **Exception: never delete a clause that carries the link between sentences** (", which answers the cost side of RQ2", "consistent with ...", "although ...", ", so ... would be consistent with it"). Page-fit cuts tend to remove exactly these, and each cut leaves an unlinked sentence. After any cut for pages, re-label the next sentence C/A/L/S/N (§4b) and repair it if it became N.
18. Shorten closing sections: "our study is restricted to tabular benchmarks only, while other modalities such as text and images have not yet been examined" → "it covers only tabular benchmarks (text and images remain untested)".
19. Add concreteness: example inputs and inline equations for the two cases (small vs large shift) added to the intro.
20. Move caveats to where they frame the claim (a closing caveat moves to the opening sentence).
21. Answer rebuttal requests in the appendix, wrapped in `\rebuttal{}` or `\revise{}`, with a one-sentence main-text pointer `[venue: ICLR]`, because only ICLR's discussion phase has a revised PDF.
22. Keep a trace: old wording as a `%` line next to the new one (a rewritten paragraph as a commented block above it) or in `\ignore{}` `[some]`; stacked alternatives, usually keep the shortest; draft from a logic outline kept as comments at the top of each chapter `[some]`.
23. Proofread what drafts typically miss: duplicate labels (one `eq:` label on two displays; one `fig:` label reused by several appendix prompt figures), a model-size mismatch between caption and text, a lost RQ number, typos ("seperate", "calibartion", "Trainning").

## 10. Tense

Counted over the compiled main-text chapters: present-tense "we + verb" (we propose / conduct / observe / report / find ...) outnumbers past-tense forms by an order of magnitude `[all]`, and almost all past forms sit in the Conclusion.
- **Present** for the method, the setup, the results and the findings, everywhere in the main text and appendix: "We conduct experiments on ...", "As shown in Table~\ref{}, X outperforms ...", "We attribute X's gains to ...", "Such results underline ...". Also in the Limitations ("the scoring head adds a forward pass to every rollout") and Related Works ("<Method> \cite{} instead ranks examples by ...").
- **Conclusion**: past `[most]` ("we proposed", "we presented", "This paper investigated ... we showed ...", "we explored") or present `[some]` ("We propose X ... It consistently outperforms ..."). One tense per paragraph; a closing future-work sentence, when present, reads "We will further explore ..." `[some]`; otherwise end on the evidence sentence.
- Do not narrate the research process in the past ("we first tried ..., then we found ..."); state what the method does and what the results show.

## 11. Density norms (measured on the compiled main text)

Sentence length is the same across the sources `[all]`: median 20-21 words, mean 20-22, 90th percentile 30-36, and 2-7% of sentences at 40+ words. Abstract sentences run longer (median 22-27). Draft to these numbers; split outliers. Length alone is not enough: section 12 gives the sentence shape, subject and emphasis rules, and `check_paper.py` (`== sentences`) checks them.

| Unit | Norm | Evidence |
|---|---|---|
| Abstract | 1 paragraph, 5-9 sentences (`[older]` 9-11) | `[current]` 5-9 |
| Intro paragraph | 4-5 paragraphs, median 4-6 sentences each (range 1-9) | `[all]` |
| Intro limitation bullet | 85-115 words, 4-6 sentences (mechanism → consequence → amplifier → gap) | `[some]` |
| Findings-paper intro bullet | 25-50 words, 1-3 sentences, one teaser panel + one number | `[some]` |
| Preliminary paragraph | median 1-3 sentences between displays (max 5) | `[all]` papers with a Preliminary |
| Methodology paragraph | median 2-3 sentences between displays (max 5-9; the long end is a derivation paragraph) | `[all]` method papers |
| Experiments paragraph | median 3-4 sentences (a setup benchmark paragraph up to 11) | `[most]` |
| RQ bullet | one question (two for a paired RQ), 10-24 words | `[current]` |
| Bold finding sentence | 8-20 words, one clause, optionally "while ..." for the contrast | `[current]` (`[older]` up to 23-29) |
| Finding bullet (claim + body) | 85-125 words, 4-7 sentences | `[most]` |
| Limitations | exactly 3 sentences: opener + two items | `[all]` papers with a Limitations section |
| Conclusion | 3-6 sentences, 80-140 words | `[most]` |

## 12. Sentence structure, subject consistency and emphasis

Evidence: hand-annotated samples of 60-108 sentences per source paper (introduction, the first two method subsections, two findings paragraphs), plus corpus counts on the main text; inline math = 1 word, citations not counted. Ranges are the five `[current]` papers; the `[older]` paper is reference only. Examples are synthetic (running methods of `paper_types.md`). `check_paper.py` block `== sentences` reports SR1-SR15.

### 12a. Sentence shape

Default shape `[all]`: **[short front] + subject + verb early + object, with subordinate material hung to the right + the point at the end.** 90-98% of sentences in the sources put the new or strongest information last.

| Measure | Source range | Rule |
|---|---|---|
| Mean length | 20.4-24.1 words; Abstract 21.4-25.5, Intro 22.1-26.3, Method 19.1-27.0, Exp 17.9-20.5 | SR1: mean Abs/Intro <= 26, Method <= 27, Exp <= 21 |
| Over 35 / 45 words | 5-16% / 1-6% of sentences; Intro 4-19% / 0-11%; Exp 3-9% / 0-2% | SR2: at most one 45+ sentence per section |
| Subject length | 2.5-2.9 words (we, the method name, a short noun phrase, "This result") | a 10-word subject with three prepositions is a slip |
| Words before the main verb | 3.7-6.1; fronts of 10+ words in 0.6-3.5% of sentences | SR4: front <= 10 words; use short fixed fronts "As shown in Figure X,", "To fill this gap,", "Based on these findings," |
| Two main clauses (", and" + new subject) | 4.8-16.7% (`[older]` 30%, dropped since) | SR6: one subject per sentence; give one subject two verbs instead |
| Subordinate items per sentence | 1.29-1.9; nesting up to 3 levels, always rightward (that-clause → participle → relative clause) | never between subject and verb |
| Relative clauses | ~100% sentence-final; 0.06-0.12 which/whose per sentence, never two | SR8: mid-sentence ", which ...," only to concede then overturn ("near-duplicate prompts, which differ little in wording, receive opposite labels ...") |
| Parentheticals | 0.12-0.37 per sentence; 2+ in one sentence 1-7%; "(termed" 0-0.6% of sentences | SR7: at most one per sentence, right after the word it explains or at the end, never at the start |
| Passive | 8.7-22.3%; definitions, formula lead-ins, "component X is rewarded by"; ~0 in experiments | the authors' own actions are never passive |
| "Since" opener | 0-0.6% of sentences | SR5: <= 2%; state the result as the main clause |
| This/These/Such opener | 5.7-11% | SR11: at least 5% in Intro and Experiments |
| Pointer at the sentence end | 5.7-12.1% | SR14: <= 12%; never "as Figure X shows" / "is illustrated in Figure X" at the end |

Long sentences stay readable when they are **linear** `[all]`: subject and verb in the first 3-5 words, details added rightward, or two items split by (i)/(ii).
- "DIVA consistently outperforms the filtering baselines across all benchmarks, with an improvement ranging from 2.1% to 4.8% in average accuracy compared to the strongest baseline." (27 words)
- "The key idea is to score each example with two scorers — a difficulty scorer and a diversity scorer — which decide (i) whether the example still teaches the model something and (ii) whether a near-duplicate is already kept." (39 words)

**A long sentence is followed by a short one** (SR3, `[all]`): "... (a 45-word description of the scorer) ... We term this workflow DIVA." / "... (four long sentences on calibration failures) ... This gap calls for a loss that targets calibration directly."

### 12b. Subject consistency

The sources' paragraphs do **not** keep one subject: subjects change almost every sentence (2.5-3.1 switches per paragraph; 14% constant, 19% linear, 28% derived, 37% mixed). They read as consistent because **a fixed cast of subjects takes fixed roles, and every switch has a reason** `[all]`.

Roles:
- **we** = method steps and experimental actions ("We first score ... / We then keep ... / We finally retrain ...").
- **the method name** = results ("PriorTilt consistently outperforms ..." / "PriorTilt only adds negligible time ...").
- **one component** = a paragraph about that component ("It first estimates ... / Next, it reweights ...").
- **This/These/Such + noun** = the handover ("This result indicates ...", "Such results highlight ...").

Allowed switches:
- **derived**: name the whole with its parts, then take the parts in turn ("two strategies are common: loss reweighting and temperature scaling." → "Loss reweighting ..." → "Temperature scaling, in contrast, ...");
- **linear**: the object of one sentence becomes the next subject ("... we build a projection matrix P." → "This can be formulated as ...");
- **parallel contrast openers**: "For in-distribution inputs, ... / In contrast, for shifted inputs, ..."; "On the one hand ... / On the other hand ...";
- **a bold run-in** resets the topic.

Rules:
- **SC1** Method steps use "we + verb"; keep the passive for definitions and "X is formulated as".
- **SC2** Result sentences take the method name or "Table X / These results" as subject, not "we obtain higher ...".
- **SC3** A paragraph about one object keeps it (or "it" / "this update") as the subject; to move to its parts, first write "whole: part 1 and part 2".
- **SC4** Every subject switch must be derived, linear, an anaphor or a parallel contrast opener; if not, rewrite with the previous subject or object.
- **SC5** One subject per sentence; two main clauses with their own subjects stay under ~15% per section.
- **SC6** Do not insert a sentence about prior work between two sentences about our method; fold it in ("as prior work does [cite]") or move it to the paragraph end.
- **SC7** Use This/These/Such + noun at least once or twice in the Introduction and Experiments.

Typical draft slip `[fix]`: method steps written as "a scorer is trained ..., and the filtered set also serves as ...", one component plus a second subject after ", and" / ", while" in each sentence (one draft paragraph had 12 subjects in 8 sentences). Fix: "we train ... and use ..."; "For each example, we compute ... and keep ..."; "Each scorer is ... . The diversity scorer then ...".

### 12c. Emphasis through clause structure

The sources use no cleft sentences (one "It is important to highlight that ..." in six papers) and "not only ... but also" once. **Emphasis comes from word order and the main/subordinate split: the point goes in the main clause and at the end; known information, conditions, mechanism and pointers go in subordinate slots** (participles, since/when clauses, parentheses) `[all]`.

Devices:
1. **Contrast: our side in the main clause, first; the baseline in a final "while" clause** `[most]`. "SynthBonus keeps the validity rate above 95%, while the unshaped baselines collapse after 2k steps." The reverse (our side in the "while" clause) is a slip (SR12).
2. **Concede, then claim** `[all]`: "While important, X remains challenging", "Despite these efforts, ..."; the concession takes 3-4 words, 1-3 times per paper, mostly in the problem sentence.
3. **Purpose first** `[all]`: "To correct the shift without retraining, we introduce ..."; never "in order to" at the end.
4. **Colon reveal** before a formula, a list or "two X": "We observe that:", "we identify two key limitations ...:"; 11-24% of sentences carry a colon.
5. **Focus adverbs next to their target and near the end**: "CaliLoss only adds negligible time compared to cross-entropy."; "... 3x faster ... without harming accuracy." Not buried in a long front.
6. **Negative results on the main verb**: "simply adding the prior term does not bring a stable improvement".
7. **Bold full-sentence finding** once per finding.

Mismatches to avoid (SR13, SR14, SR15):
- a key result inside a "Since" clause, a parenthesis or a final relative clause ("Since, to first order, the two scores rank examples identically, we further design ..." hides a result of the paper in the premise: state it as its own main clause, then "Therefore, we ...");
- a key contrast buried mid-sentence while the end repeats known information;
- the core operation defined only inside a parenthesis ("combines them (\ie adds them to ...)");
- two or three tails stacked at the sentence end: "(termed X)" + citation + "(Section Y)";
- a "While ..." fragment with no main clause.

Kept as the sources write: "If ..., X" fronts of up to ~20 words with the result in the main clause; colon plus (i)/(ii) lists; roadmap sentences ending "in Section X"; a sentence-final ", thereby V-ing"; "This is because ...".

### 12d. Checks (`check_paper.py`, block `== sentences`)

| id | Rule | Check |
|---|---|---|
| SR1 | Section mean length within the source range | exact (table) |
| SR2 | At most one 45+ word sentence per section | exact (list) |
| SR3 | A 35+ word sentence is not followed by another 30+ word sentence in the paragraph | exact, soft (list) |
| SR4 | At most 10 words before the subject | approximate (list) |
| SR5 | "Since" openers <= 2% | exact (table + list) |
| SR6 | Two-main-clause sentences <= 15-17% | approximate (table; lists Abstract and Intro) |
| SR7 | At most one parenthetical per sentence; per-sentence rate within range | exact (table + list) |
| SR8 | No mid-sentence ", which ...," before the verb | approximate (list) |
| SR9 | Subject roles (12b) | by hand |
| SR10 | Every subject switch has a reason; never three subjects in one sentence | by hand |
| SR11 | This/These/Such openers >= 5% in Intro and Experiments | exact (table) |
| SR12 | Our method in the main clause of a contrast | approximate (lists while/whereas clauses that contain we/our/the method name) |
| SR13 | The point is not in a parenthesis, a final "which" clause or a "Since" clause | candidates (lists parentheses > 10 words, final which-clauses > 10 words, result verbs inside a "Since" clause) |
| SR14 | Bare pointer endings ("... in Table X.", "... are in Appendix Y.") <= 12% in Method/Experiments; no "as Figure X shows" ending; no stacked tails. A claim followed by "(see Appendix X)" and roadmap sentences ("We first ... in Section X") are fine | exact (table + list) |
| SR15 | No fragments, no cleft sentences | exact (list) |

Workflow: after drafting a section, run the check, rewrite the listed lines, and read each paragraph's subject chain aloud (who acts in each sentence?) for SR9/SR10.

## Plain words, no coinage

Outside the few core concepts (`concepts.md`), the house writes with **plain, common words** and almost never invents terms.
- **Hyphenated compounds** are the main coinage channel. Main-text rate per 1,000 words in the source papers: about 14-41, median about 27. Stay **≤ 41**, aim for ≤ 30. A draft far above that usually holds one-off compounds that name something only once (*score-locked*, *label-neutral*, *proxy-only*); rewrite them as plain phrases.
- **A low-frequency term (used once or twice) must be a plain phrase**, not a new compound or a rare word. Coined names are allowed only for the 2-5 core concepts, which are declared, borrowed or composed from existing terms, marked once and echoed (`concepts.md`).
- **Prefer your own established vocabulary.** With `--vocab PATH`, `scripts/check_paper.py` lists long low-frequency main-text words that are missing from your word list (declared concepts excluded); replace them unless they are field terms (model, dataset or method names, standard ML terms with a citation). Without `--vocab`, only the compound checks run. `tools/build_corpus.py` writes such a word list (`vocab.txt`) from your own papers; for multi-word expressions follow "Use attested expressions" at the end of this file.
- **A reference vocabulary is not the field's.** A word can pass the rare-word check and still be a coinage or a sense clash as a technical label (e.g. *probe* for a test run, which readers take as a linear probe). Key terms are checked against the cited papers' own text in the term table (`concepts.md` §3).
- Bold heads and limitation bullets use plain nouns ("Loss of Rare Clusters."), not coined adjectives ("Rare-Cluster-Starved Selection.").

**Build your own word list** (once, from papers you have already published or written in the style you want):
1. Collect the main-text `.tex` files of those papers (chapters only; skip the appendix, the `.bib` and the checklist).
2. Strip comments (`%` to end of line), math (`$...$`, display environments), and commands with their optional arguments (`\cite{...}`, `\ref{...}`, `\label{...}`, `\textbf` keeps its text).
3. Lower-case the rest, keep alphabetic tokens (letters, inner apostrophes and hyphens), and write the unique tokens one per line; `#` lines are comments.
4. Pass the file: `python3 scripts/check_paper.py <paper_dir> --vocab my_vocab.txt`.

```bash
cat paperA/chapters/[1-6]_*.tex paperB/chapters/[1-6]_*.tex \
  | sed -E -e 's/^%.*$//' -e 's/([^\\])%.*$/\1/' -e 's/\$[^$]*\$//g' \
           -e 's/\\(cite[pt]?|ref|label|eqref|Eqref|url)(\[[^]]*\])*\{[^}]*\}//g' -e 's/\\[A-Za-z]+//g' \
  | tr 'A-Z' 'a-z' | grep -oE "[a-z][a-z'-]*[a-z]" | sort -u > my_vocab.txt
```
The list only needs to be approximately right: it is a prompt to look at a word again, not a ban.

Replacements applied in revision (obscure → plain):

| Avoid | Write |
|---|---|
| *X-locked*, *X-locking* | fixed X |
| *X-agnostic* | that ignores X |
| *label-neutral* | leaves the labels unchanged |
| *train-free* | training-free (the usual spelling) |
| *easy-first ordering* (a new name for a known method) | curriculum learning (the established term, cited) |
| *proxy-only run* | control run |
| *score-shift probe* | short test run (the plain description) |
| *same-order pairs* | pairs with the same ranking |
| affordability | cost |
| undercut | be lower than / match |
| systematize | use (as a standard step) |
| fold into | combine in |
| parity / reach parity | tie / match |
| interior optimum | intermediate optimum |
| regime (for "our setting") | setting |
| reparameterization | mapping |
| displacement | parameter change |
| stratify | group |
| mimic | approximate |

## Explain at first use; no jargon

An explicit house rule for every draft; it complements "Plain words, no coinage" above (that section limits new words, this one explains the necessary ones).
- **No hard-to-understand words.** A reader opening the paper cold must understand every word. Prefer a plain word over subfield jargon even when the jargon is "standard" in a neighbouring literature, and do not replace one piece of jargon with another. Invented example (PriorTilt): a draft that writes *source* for the training data and *importance weights* for the per-class correction, and then offers *density ratio* as the replacement, is revised to say *training data* and *class weights*.
- **Every term that is not obvious at first sight is explained at its first occurrence**, once, with a short clause, then used unchanged: "X (\ie ...)" or "... (termed X)". The abstract counts as a first occurrence (short clause), and the Introduction explains again at its own first use. Source introductions carry 2-7 such clauses (median about 4-5) `[most]`; §1 rule 7 counts `\ie` over the whole main text. Invented examples: "shifted test sites (\ie hospitals whose class frequencies differ from those of the training data)" (PriorTilt), "a route-length estimate (\ie the number of reaction steps a planner needs to make the molecule)" (SynthBonus), "a second component that rates how unlike the already selected examples each candidate is (termed \emph{diversity scorer})" (DIVA).
- Typical candidates for a first-use clause: the names of evaluation protocols (in the style of *held-out site*, *oracle baseline*, *fixed-budget run*), borrowed technique names (in the style of *mixup*, *label smoothing*, *temperature scaling*), quantities (in the style of *retention rate*, *prior gap*, *expected calibration error*), and every core concept (`concepts.md`).
- Self-check: read the abstract and Introduction as a newcomer and list every phrase you would have to look up; each needs a plain word or a first-use clause.
- **Where the explanation goes.** First-use clauses added after a newcomer review are a common cause of broken links. Put the definition inside the sentence that carries the link, as a parenthesis or a sentence-final clause. Do not insert it as a separate sentence between a claim and its continuation, and do not put it before the subject so that the known part is pushed to the end of the sentence. If a definition must stand alone, put it at the end of the paragraph or as the first sentence after a run-in head.
- **"(termed X)" is for the core concepts only**: the number of "(termed" in the main text should not exceed the number of core concepts in the concept table (source papers: 1-3). Other terms get "(\ie ...)" or a plain phrase (`concepts.md` §2.2; `check_paper.py` prints both counts).

## Habits to avoid (recurring patterns of machine-written drafts → plain alternatives)

These 27 patterns were found by diffing a 24k-word machine-written draft against the house/reference corpus, grouped into 27 patterns; counts come from `tools/ps.py`. `check_paper.py` prints every match of these patterns as a warning with its line number (`== habits`); some patterns also catch legitimate uses, so read the line before changing it. Evidence column: house total uses and papers out of 6 for the replacement; "0/6" = no house paper uses the flagged form. house = H1-H6 (this skill's own 6 papers, see `assets/lexicon/`); refA / refB = the 16 earlier reference-lineage papers, kept only for comparison.

| # | Avoid | Use instead (house-attested) | Evidence |
|---|---|---|---|
| 1 | ", so" joining two full clauses | "Therefore," (26, 6/6) · fronted "Since ..., ..." (16, 6/6) · ", thereby V-ing" (12, 4/6) · "As a result," (5, 3/6) | ", so" + clause: 0 in house main text; the 18 appendix hits are model outputs or proof steps; cap ≤ 1 per 1k words |
| 2 | "every" as the default quantifier | "each" (94, 6/6) · "for each" (32, 6/6) | "every" 4 (H4 2, H6 2): a few are fine; a machine-written draft's rate was 3.2/1k vs house 0.07/1k |
| 3 | motion verbs for change: shrinks, grows, stays, falls from/to, lowers, raises, beats, caps, lies within | reduces (12, 4/6) · decreases (10, 5/6) · increases (35, 6/6) · remains (29, 6/6) · outperforms (18, 5/6) · is within | only H5's "as the context length grows" has this sense |
| 4 | "against" meaning "compared with"; sentence-initial "Against X," | compared to (18, 4/6) · compared with (10, 3/6) · "with X as ground truth" · "correlation between X and Y" (4, 2/6) | 13 of house's 14 "against" mean "against attacks/noise"; "Against X," 0 in all 22 papers |
| 5 | coined labels: headline, seed-mean, sweep, by construction, catalogue, arm(s), surrogate, analogue, noise floor, stress-test | main result / main metric (H6) · the average over seeds ("the average" 18, 6/6) · baselines (49, 4/6) · a plain description | each 0/6 |
| 6 | draft-wide labels: idealized / idealization / regime, resolve / resolvable, additive / additivity | simplified (4, 3/6) · setting (12, 6/6) · detect (18, 3/6) / distinguish (13, 4/6) | 0/6; decide once and change every file (theorem titles included) |
| 7 | proof register in prose: recovers, reduces to, amounts to, coincides, specializing X to Y, in place of, iterates, inductively, whenever, rests on | is equivalent to (7, 3/6) · corresponds to (4, 2/6) · replacing X with Y (7, 2/6) · relies on (5, 3/6) · when (99, 6/6) · yields (16, 6/6) | house 2 (H1 only); "whenever", "rests on" 0 in all 22 papers |
| 8 | bare "(Appendix~\ref{})" | "(see Appendix~\ref{})" (8, 3/6) · "... can be found in Appendix~\ref{}" (14, 5/6) | 0 of 22 papers write the bare form |
| 9 | "needs" / "extra" for requirements and cost | requires (10, 5/6) · additional (22, 6/6) · "without additional" (3, 2/6) | house's 4 "needs" are quoted prompts |
| 10 | derivation "gives" / "which gives" after a step | "yields:" (16, 6/6) | house's 9 "gives" are "Figure X gives examples" (that sense is fine) |
| 11 | metaphor verbs: pays, buys, cuts, saves, one-time cost, trades X against Y, carry | requires · reduces · share the same (4, 2/6) · contain(s) (31, 6/6) · improves (15, 5/6) · affects (14, 4/6) | "carry signal" is `[refA]` only (13 uses, 6/8); money verbs 0 |
| 12 | prediction wording: "is expected/predicted to", "makes/gives no prediction" | should (95, 5/6) · "the analysis does not predict ..." · may / is likely to | keep "is expected to" only inside the bold predictions fixed in advance (`[lab]` frame, refA 3/8, refB 4/8), ≤ 2 per section |
| 13 | mid-sentence "therefore" ("We therefore use", ", therefore,") | sentence-initial "Therefore," (26, 6/6); "Therefore, we" (7, 5/6) | mid form only H4 3, H6 1, appendix |
| 14 | sentence-initial "Its" whose noun is two sentences back | name the referent ("The pool at the small scale", "The two rows of Table~\ref{}") | §4b rule 7 |
| 15 | stance predicates: "is standard / established", "standard practice", "we claim", "not a (claimed) contribution" | "is widely adopted" (3, 3/6) · "..., rather than a contribution of this paper" ("rather than" 17, 5/6) | 0/6; keep such disclaimers few |
| 16 | a table or metric as the agent: "Table X lists / compares / measures / gives / scores" | "We report ... in Table X" (12, 5/6) · "Table X reports / summarizes / shows" (9, 3/6) | 0/6; same for "the comparison measures" |
| 17 | "the theory" as subject ("the theory checks / predicts") | "our analysis ..." · "we do not predict ..." ("does not" 41, 6/6) | 0/6 |
| 18 | scope limits: "does not cover", "lies outside", "Nor does it", "covers only" | beyond (24, 5/6) · "is (still) limited to" (2, 2/6) | 0/6 |
| 19 | question-style run-in heads ("What the theory predicts.", "Why two testbeds.") | noun-phrase heads: "Impact of X." (5, 2/6), "Connection with X." | 0/6 |
| 20 | "we write X for Y" to introduce notation | "..., where X denotes ..." (denote 50, 6/6) · "denoted as" (4, 4/6) · "we define" | "Let X denote" is retired (H1) |
| 21 | "X, not Y" contrast tails | "rather than" (17, 5/6) | 0/6 |
| 22 | "is / are called" for a decision rule | "is considered X if ..." (3, 2/6) | 0/6 |
| 23 | mid- or end-of-sentence ", however," | sentence-initial "However," (41, 6/6) | house 1 |
| 24 | "the latter" / "the former" | repeat the noun | 0/6 |
| 25 | "next to" for side-by-side table columns | "together with" (4, 2/6) | 0/6 |
| 26 | "flag / flagged" for marking data | "mark(ed)" (5, 3/6) | house 1 |
| 27 | "put into context" for a reference table | "serves as a reference for" (reference 14, 4/6) | 0/6 |
| + | foreign connectives: "Ideally,", "Nor ...", "likewise", "is not free, however", "is an empirical question", "As a direct corollary of" | "Intuitively, effective X should ..." (H4) · "It also does not ..." · "Moreover," · "requires additional ..." · "we test whether ..." · "Based on Proposition~\ref{}, ..." | 0 in all 22 papers (§4a) |

## Use attested expressions (lexicon rule)

A word list checks single words; most wording problems in machine-written drafts are multi-word expressions, sentence frames and link words. The fix is to look an expression up **before** using it: every connective, sentence frame, reporting verb, pointer and non-domain phrase should be one that a house paper (or the papers whose style you follow) actually uses. The skill ships a ready-to-use lexicon and phrase index, built from 6 house papers + 16 reference papers (`assets/lexicon/lexicon.md`, `assets/lexicon/lexicon.json`, `tools/phrase_index.json.gz`); you can also build the same thing from your own papers instead, or in addition.

**A. Use the shipped lexicon (default, no setup)**

1. **For each sentence, take the wording from the lexicon section for that rhetorical move** (`assets/lexicon/lexicon.md`, organized by writing step: M1 background ... M10 conclusion, 实验设置, 连接词（按功能）, 图表指代, 章节开头与路线图, 括号套式, word classes). Take entries marked 常用 (core: ≥ 2 of the 5 most recent house papers) first, then 偶用 (occasional: 1 recent house paper). Avoid 已弃用 (retired: the oldest house paper only), 禁用 (banned) and 仅参考 (ref_only) entries; a ref_only frame is acceptable only when no house form exists and it is not banned.
2. **Check any multiword phrase you are unsure about** with `tools/ps.py` (no `--corpus` needed): `python3 tools/ps.py "phrase" -q` (plain phrase, `*` = one word), `-g house,refA,refB` to pick groups, `--region`/`-r`/`--draft` need `--corpus` (section B). Accept it if it is used in ≥ 2 of the 5 most recent house papers; use it once if it is in 1; otherwise rewrite it in plain words or take the lexicon's alternative.
3. **Senses matter.** A hit counts only in the same sense: "against" meaning "against attacks" is not "compared with"; "gives" in "Figure X gives examples" is not a derivation "gives". Read the lexicon's example before trusting a count.
4. **Exempt**: the paper's fixed terms (declared in the concept table), domain terms, dataset / model / method names, math, citations and references. A plain single word is not rewritten only because the index finds 0 hits; the check is for multi-word expressions, frames and unusual senses.
5. **After a section**: run `scripts/check_paper.py`; its `== habits` block lists every match of the 27 habit patterns above with a house-attested alternative, and `== vocabulary` lists rare single words (pass `--vocab` from your own corpus, section B, for that check).
6. **Rewrites follow the same rule**: a rewrite may not introduce an expression with weaker evidence than the one it replaces; keep `\ie`, `\eg`, `\cf`, `\concept{}`, citations, references and math verbatim; never change a fixed term. A label used in several files ("idealized", "headline metric") is changed everywhere at once or not at all.

**B. Build your own lexicon from your own papers (optional)**

1. **Build the corpus** (once, then after each new paper). Collect the main `.tex` file of each paper you wrote as first author (the file with `\begin{document}`; `\input` files are followed), optionally plus papers you admire as `reference`:
   ```bash
   python3 <skill>/tools/build_corpus.py --mine ~/papers/a/main.tex::2024 ~/papers/b/main.tex::2026 \
       [--reference ~/papers/admired/main.tex::2025] --out ~/style_corpus
   # or: --config my_sources.json   {"papers": [{"key": "A", "group": "mine", "year": 2026, "path": "a/main.tex"}, ...]}
   ```
   It writes `corpus.jsonl` (one sentence per row, with region main / appendix / caption / statement, section, file and line; math → [M] / [EQ], citations → [CITE], refs → [REF]) and `vocab.txt` (the words of your main text, usable as `check_paper.py --vocab`). It refuses to write inside the skill folder, so your sentences are never published with the skill.
2. **Build a first lexicon** from the corpus, by writing step. Recurring openers and frames: `python3 tools/ngrams.py --corpus ~/style_corpus/corpus.jsonl -g mine --openers --min-papers 2` and `... -n 3,4 --min-papers 2`; frames around a word: `--contains gap`. Sort what you find into the same sections as the shipped lexicon and give each entry a tier by rule: **core** = in ≥ 2 of your recent papers (default: the last 3 years); **occasional** = 1 recent paper; **retired** = only in your older papers; **reference only** = only in the `reference` group; **banned** = on the banned or foreign lists of this file regardless of counts. Keep the lexicon as a Markdown table in your workspace (expression | function | papers / uses | tier | example); `ps.py --corpus` prints the counts for each row.
3. **Check any multi-word phrase**: `python3 tools/ps.py "phrase" --corpus ~/style_corpus/corpus.jsonl -q` (`*` = one word, `...` = any words, `-r` for a regex such as `-r "^Therefore,"`, `--region main` for main-text use, `--draft <main.tex>` to compare your draft's rate). Accept it if you use it in ≥ 2 recent papers; use it once if it is in 1; otherwise rewrite it in plain words.
4. **After a section**: run `scripts/check_paper.py --vocab ~/style_corpus/vocab.txt` for the rare-word list, on top of the `== habits` check from section A.

**Without any papers to check against**, use the shipped lexicon (§A) and phrase index as-is, the connective library (§4a), the habits table and the plain-words rules.
