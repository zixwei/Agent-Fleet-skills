---
name: inference-optimization-playbook
description: Use when the task is to make local LLM inference faster or to explain why it is slow, especially large MoE models that do not fit in VRAM: expert offload to RAM, hot expert placement, live expert cache, CPU lane vs PCIe copy, multi-GPU (layer split, tensor parallel, expert parallel, all-reduce), prefill and re-read speed, speculative decoding and drafters, quant choice for speed, expert pruning, KV, prompt cache and session snapshots for agents, big n-gram tables on NVMe, output head cost, benchmark methodology and quality gates for engine changes. Also for porting a new model into an inference engine or planning its bring-up on given hardware. A map of 115 tested hypotheses with verdicts and numbers from DeepSeek V4.1 Flash, Qwen3.8-Flash-Next, GLM-5.3-Flash, MiniMax-M3.1-Flash, Hy4 Flash and Inkling 2 Mini on 1-4 RTX 3090. Not for training, prompt engineering or cloud API cost questions.
---

# Inference optimization playbook

A map of real experiments on speeding up LLM inference on consumer GPUs: what was tried, on which model, what the numbers were, what the decision was and why. All paths below are relative to the directory this file is in.

The 5 offloaded models in it behaved differently. A static hot set worked on 2 and was close to random on 2 others. The cheapest way to serve a miss flipped between models on the same hardware. So there is no fixed sequence to run. Find where the user's case is on the map, then use only the results that were obtained under the same conditions.

## The route

1. **Orient.** Fill `protocol/situation-card.md` for the case in front of you: model, hardware, engine and placement, workload, current numbers. Unknown fields are the first things to find out, and the card says how. It ends with a tag line such as `#offload #weak-skew #cpu-lane #multi-gpu #agent`.
2. **Locate.** Open `playbook/00-start-here.md`. Answer its 7 questions by regime, or enter by symptom. Each answer points to 1-2 chapters. Read the first lines of a chapter: every chapter says when to read it and when to skip it.
3. **Filter.** Grep `ledger/hypotheses.md` by your tags to see what was already tried under your conditions, with the verdict, the reason and a "revisit when". Then open the closest case from the traits table in `cases/README.md`.
4. **Test.** Pick the cheapest test that reproduces the bottleneck. Follow `protocol/experiment-rules.md`: ceiling first, one change, controls on both sides, thresholds written in advance, a stop rule.
5. **Record.** Write the result with `protocol/entry-template.md`, failures included. Inside this repo follow `AGENTS.md`.

Do not load every chapter. They are 3-13 KB each and most of them will not apply to one case.

```bash
grep "#weak-skew" ledger/hypotheses.md              # results on models with weak routing skew
grep "#offload" ledger/hypotheses.md | grep "#tp"   # tensor parallel with experts in RAM
grep "#drafter" ledger/hypotheses.md | grep "❌"    # drafter ideas that failed
grep -n -i "prefetch\|numa" ledger/hypotheses.md    # by keyword
```

## Instruments for step 1

Both are standard-library Python, no install.

| Tool | Question it answers | Input |
|---|---|---|
| `tools/route_sim.py` | Is routing skewed? Does a static hot set beat a random one at my VRAM budget? How far is a live cache from the best possible one? | a router trace in JSON Lines |
| `tools/fit_cost.py` | Which term of token time is larger, the constant part or the misses? What is the ceiling with no misses? | a CSV of 3 or more runs |

`tools/README.md` has the formats. Both have `--demo`.

## Routing table

| The user's problem | Read |
|---|---|
| Will offload be fast at all, how many experts fit, where does token time go, what is the ceiling | `playbook/01-cost-model.md` |
| Misses in decode: CPU or PCIe, memory tiers | `playbook/02-serving-a-miss.md` |
| Which experts to keep in VRAM, profiles, domains | `playbook/03-expert-profile-and-placement.md` |
| Cache policy, capacity, warming, predicting experts | `playbook/04-live-expert-cache.md` |
| 2-4 GPUs: layer split vs TP vs EP, exchange between cards, padding | `playbook/05-multi-gpu.md` |
| RAM channels, NUMA, thread count, pinning | `playbook/06-cpu-and-ram.md` |
| Slow prompt reading, chunk size, DMA, CUDA graphs | `playbook/07-prefill.md` |
| Drafters, MTP, acceptance, window policy, drafter training | `playbook/08-speculative-decoding.md` |
| Which quant, hybrids, KL noise floor | `playbook/09-quants.md` |
| Dropping, masking or merging experts | `playbook/10-pruning-and-merging.md` |
| Agent turns are slow, prompt cache misses, A → B → A | `playbook/11-sessions-and-prompt-cache.md` |
| Load time, THP stalls, a table too big for RAM | `playbook/12-loading-and-big-tables.md` |
| Vocabulary projection cost | `playbook/13-output-head.md` |
| Building a reference, replay, why a benchmark lied | `playbook/14-benchmarking-and-traps.md` |
| Did the change keep quality, should this build ship | `playbook/15-quality-and-release-gates.md` |
| A new model from zero | `protocol/new-model-checklist.md` |
| A term is unclear | `glossary.md` |

## Rules for using these numbers

- **Transfer mechanisms, not numbers.** Everything was taken on 4x RTX 3090 (PCIe 3.0, no NVLink), an EPYC 7642 and DDR4 whose channel count changed during the work. Before you quote a number to the user, say which model, engine and hardware it is from. Never present it as what they will get.
- **Match the conditions before you trust a verdict.** Compare the tags of a ledger row with your tag line. A row without `#weak-skew` was not tested on weak skew.
- **A ❌ is local.** It means "did not pay on that model, engine and hardware". Read the "why" and "revisit when" columns. Several ideas flipped between models: the CPU lane lost to PCIe on GLM and won on 3 other models, TP under offload was dropped on DeepSeek and worked on Hy4 Flash with a CPU lane serving the missing experts.
- **Respect the evidence labels.** No label means a real run. (sim) is a simulator on real traces, (calc) a calculation, (micro) a microbenchmark of one operation, (ext) someone else's number, (1 run) not repeated. Do not turn a (calc) into a promise.
- **Reproduce the bottleneck first.** Each chapter says what to record on the user's setup to know whether the trick applies. Do that before you implement anything.
- **Do not skip the quality gate.** Any change in chunk size, quant, placement, exchange packing or drafter acceptance is a numeric change until shown otherwise. Use the 3 levels in chapter 15 and always test a second turn.
- **Short benchmarks are for debugging.** Decide on a replay of real sessions and free generation.

## The model in one paragraph

Decode of an offloaded MoE costs `constant + misses per token × cost of one miss`. So there are 4 levers: fewer misses at the same VRAM (profile, live cache), more experts in VRAM (fewer bits, smaller buffers), a cheaper miss (CPU lane, faster RAM, better CPU code), and a smaller constant (kernels, exchange, drafter). Fit the model first and work on the larger term. Prefill is a different regime: almost every expert is needed per chunk, so the lever is bytes moved over PCIe per token. Agent workloads are a third regime: most time is generation and short re-reads, so caches and snapshots matter more than cold prefill speed.

## What this playbook does not have

- A benchmark harness. The tools here help to orient, they do not launch or time a server.
- Data on concurrent load. Almost every number here is for 1 active request.
- Hardware other than the rig in `cases/README.md`.

When the map has no answer, say so. Then use the closest mechanism to design the cheapest test that would answer it, run it if you can, and record the result. A new ledger row with a clear verdict and tags is the most useful contribution.
