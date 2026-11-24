# Annotated examples of the house techniques

Short synthetic excerpts, written for the invented running methods of `paper_types.md`: DIVA (difficulty- and diversity-aware data filtering, type W), PriorTilt (a training-free label-shift correction in closed form, type T), CaliLoss (a calibration loss with a bound on expected calibration error, type L) and SynthBonus (a shaped synthesizability bonus for RL-based molecule generation, type R). None of them is a real method and no result below is real. Each excerpt shows one technique; the notes say what to copy. Sentences are shortened with "...". Evidence labels as in SKILL.md. For whole intro P2-P4 and Methodology plans per paper type, see `paper_types.md`.

---

## 1. Abstract: gap pair → proposal → mechanism → training signal (DIVA)

> However, this one-pass filtering suffers from two key drawbacks: (i) coverage shrinks quickly, because a fixed proxy score discards examples even when they fill rare regions of the data; and (ii) the kept set fills with near-duplicates, because similar examples receive similar scores. To resolve both, we propose DIVA, which equips the training loop with two learned scorers for cheaper and more faithful data filtering. Specifically, ... To give the scorers this ability, we train them with two feedback signals $s^{\text{diff}}$ and $s^{\text{div}}$ derived from held-out loss changes ...

- Slots S3 → S5 → S6 → S7 from `prose.md` §2. The gap is a numbered PAIR, the fix names ONE ingredient ("two learned scorers"), and the epithet "cheap and faithful" returns in the intro and conclusion.
- No numbers: a headline claim such as "up to <k>× less filtering time" is cut from the abstract before submission and moves to intro P5.

## 2. Intro P2: the single bold-italic research question (SynthBonus)

> \revision{Yet rewarding synthesizability during generation is hard in practice, for two reasons:} (i) exact route planning takes minutes per molecule, far too slow for every rollout \cite{Placeholder-A}, and (ii) fast estimates are noisy, and a policy trained on them soon learns to exploit their errors \cite{Placeholder-B} ... \textit{\textbf{Can a cheap route-length estimate guide RL toward makeable molecules without the policy learning to game it?}} If so, synthesizability could be optimized during generation rather than filtered afterwards, by combining \concept{potential-based shaping} \cite{Placeholder-C} with an \concept{ensemble penalty} ...

- Rule of two ("two reasons: (i) ... (ii) ..."), then the one question in bold italics (used once per paper `[some]`), then "If so, ..." to state the stakes.
- The twin concept pairs a borrowed term ("potential-based shaping", cited in the same sentence) with one composed from plain words ("ensemble penalty"); both are marked once with `\concept{}` here and reused unmarked in titles and the conclusion (`concepts.md`).

## 3. Intro P4: key idea in one sentence with (i)/(ii) and inline notation (DIVA)

> The key idea is to equip the training loop with two learned scorers --- a \concept{difficulty scorer} and a \concept{diversity scorer} --- that set (i) how much weight each candidate example receives and (ii) how many examples each cluster of the pool may contribute to a batch. Specifically, at step $t$, the difficulty scorer $g^{\text{diff}}_\omega$ gives each example $x_i$ a weight $w_i$, the diversity scorer $g^{\text{div}}_\omega$ gives each cluster $c$ a quota $q_c$, and the batch is drawn with $p_t(x_i) \propto w_i\, q_{c(i)}$.

- Precise verb "equip" (a draft would say "introduce"; `prose.md` §9 rule 1). Em-dash aside with spaces. The inline formula uses the exact Methodology symbols.

## 4. Intro P5: evidence roadmap instead of a contribution list (DIVA)

> We then run extensive experiments to test DIVA. First, a pilot study shows that the learned difficulty scores are reliable and track held-out loss changes ... (\cf Section~\ref{sec:score-reliability}). Building on this, DIVA generally outperforms <Prior> at equal compute (\cf Section~\ref{sec:main-performance}). In addition, ... (\cf Section~\ref{sec:ablation}).

- One sentence per RQ subsection, in RQ order, each ending with `(\cf Section~\ref{})`. Bounded claims ("generally outperforms"). No bullet list.

## 5. Section roadmap opener (PriorTilt, Methodology)

> We first state both objectives in one budgeted estimation problem in Section~\ref{sec:budgeted-estimate}. After that, in Section~\ref{sec:target-fit}, we show how to measure how well a candidate prior explains the unlabelled predictions of the new site ... Then, in Section~\ref{sec:prior-fidelity}, we describe how a divergence budget keeps the prior close to the training prior ... Finally, we combine these parts into the complete PriorTilt method in Section~\ref{sec:priortilt}.

- "first → After that → Then → Finally", every subsection named with its `\ref`; the last subsection assembles the method.

## 6. Transition between sibling subsections (PriorTilt)

> With target fit measured by the expected log-likelihood $\langle \boldsymbol{\pi}, \bar{\mathbf{p}} \rangle$, we now turn to prior fidelity by bounding how far $\boldsymbol{\pi}$ may move from $\boldsymbol{\pi}^{\text{src}}$.

- Callback to the finished twin concept (target fit) + hand-off to the other (prior fidelity). Reuses the twin names verbatim. `[some]`

## 7. Preliminary closer = hand-off to the method (DIVA)

> Still, scoring every example once with a fixed proxy and keeping only the top of the ranking leave both the coverage and the precision of one-pass filtering far from ideal.

- Names the two limitations the Methodology removes, with the paper's twin concepts ("coverage and precision"). A longer motivating sentence after it is cut in revision when the next section already says it (`prose.md` §9 rule 17).

## 8. Borrowed lemma + parenthetical proof pointer (PriorTilt)

> However, solving this constrained problem by projected gradient needs many passes over the unlabelled set, since the budget couples all $C$ classes. We therefore rewrite it with the help of the following lemma:
> `\begin{lemma}[Gibbs Variational Principle \citep{Info-Theory}]` ... `\end{lemma}`
> This identity reduces the problem to a one-dimensional search over $\lambda$, which bisection solves in $O(C \log(1/\delta))$ time (see Appendix~\ref{apdx:gibbs-lemma} for the proof).

- Motivate → state (citation in the optional title for a borrowed result) → one-line consequence + pointer. No proof in the main text.

## 9. Headline theorem + proof sketch (CaliLoss)

> `\begin{restatable}{thm}{MainTheorem}` Let $\hat{p}$ denote the predicted confidence and define the smoothed calibration gap $\Omega_h(\hat{p}) \triangleq \ldots$. Then, for any bandwidth $h > 0$, $\Lapl_{\text{Cali}}$ \textbf{upper-bounds} the expected calibration error (ECE) up to an additive $O(h)$ term: ... `\end{restatable}`
> We give a proof sketch with the main steps here and defer the full proof to Appendix~\ref{apdx:proof-main-theorem}.

- Self-contained statement, key relation in bold, `restatable` with `thm` numbered within the section ("Theorem 3.1") so the appendix can restate it (`\MainTheorem*`). Definitions use `\triangleq` (see `math.md` §2). Two numbering schemes occur, each `[some]`: this within-section scheme for a paper with a headline theorem, and plain counters for definitions and lemmas (excerpt 8 prints "Lemma 1").

## 10. Equation glue: lead-in, where-clause, so-what (CaliLoss)

> Formally, the calibration penalty is defined as follows: ... where $\bar{c}_m$ is the mean confidence in the $m$-th confidence bin $B_m$, $\bar{a}_m$ is the empirical accuracy in that bin, and $\mathbb{I}(\cdot)$ denotes the indicator function. This term penalizes a bin only when its mean confidence exceeds its accuracy, and contributes $0$ otherwise.

- Colon lead-in, every new symbol named right after the display, then one sentence on what the equation does.

## 11. Observation block with bold findings (DIVA, Experiments)

> We report accuracy and filtering time averaged over the three training budgets in Table~\ref{tab:main-table}, ... Per-budget results are in Appendix~\ref{apdx:per-budget}. We have the following observations:
> \item \textbf{DIVA generally outperforms <Prior> across diverse benchmarks.} As shown in Table~\ref{tab:main-table}, both with and without the diversity scorer, DIVA improves over <Prior> on most of the benchmarks. Moreover, ... We attribute the gain to the diversity scorer, which keeps rare but useful examples that a fixed proxy score throws away.

- Report-where + pointer + "We have the following observations:"; bold full-sentence claim; evidence → strengthening → attribution. Bounded wording ("generally", "on most of the benchmarks").

## 12. Mechanism paragraph with a randomised control (SynthBonus)

> We have the following observation: \textbf{The ensemble penalty keeps the shaped reward honest.} ... Without the penalty, the shaped reward keeps rising while the planner-verified route length stops improving after <k> steps ... In contrast, when the potential is shuffled across molecules (the shuffled-potential control in Figure~\ref{fig:hacking-check}), the policy shows no gain in synthesizability ... Such results confirm that the gain comes from the information in the estimate, and that the penalty keeps the policy from exploiting its errors.

- The randomised-signal control isolates the new signal; "In contrast, ..." then a "Such results ..." closer that ties the finding to the method.

## 13. Captions

- Plot (page-1 figure, DIVA): "Accuracy over training on Set-B with Model-S. Filtering with DIVA reaches a higher final accuracy, and the diversity scorer adds a further gain late in training." — title phrase + takeaway.
- Framework (PriorTilt): "Overview of PriorTilt, which maps the mean prediction $\bar{\mathbf{p}}$ of the new site to a corrected prior $\boldsymbol{\pi}^{\star}$ inside a divergence budget around $\boldsymbol{\pi}^{\text{src}}$. For a small shift, the budget keeps $\boldsymbol{\pi}^{\star}$ near the training prior .... For a large shift, the prior moves up to the edge of the budget ...." — walks the figure with math, in the twin order.
- Synthetic diagrams that follow these conventions are bundled, each as a printed-size PDF + 300-dpi PNG: `assets/diagrams/framework_pipeline.{pdf,png}`, `assets/diagrams/teaser_problem_solution.{pdf,png}`, `assets/diagrams/teaser_failure_modes.{pdf,png}`, `assets/diagrams/key_idea_loop.{pdf,png}`, `assets/diagrams/cluster_quota.{pdf,png}` (`figures_tables.md` §5). They illustrate the look, not real results.
- Teaser (problem teaser, DIVA): "Where one-pass filtering fails. A fixed proxy model scores the pool once and keeps the top-ranked examples .... This causes two problems: rare clusters vanish from the kept set ... and near-duplicates crowd it ...." — let a problem-teaser caption grow from one to 2-3 sentences during revision when the figure needs it.

## 14. Limitations (SynthBonus)

> This work still has a few limitations. On the one hand, the estimator ensemble adds several forward passes to every rollout, which lengthens training. On the other hand, we evaluate only on small drug-like molecules, so whether the shaped bonus also helps for polymers or materials is still unknown.

- Opener + cost limitation + scope limitation; no mitigation, no promise; ~50 words.

## 15. Revision pairs (DIVA, draft → final)

- Draft: "adding unnecessary scoring passes and wasted GPU time" → Final: "incurring redundant compute".
- Draft: "Here, the two scorers ... are the same network" → Final: "In practice, the difficulty scorer and the diversity scorer share one backbone $g_\omega$ and differ only in their output heads."
- The direction of every revision: shorter, more precise verb, concrete over abstract (`prose.md` §9).
