# Start here: the map

This playbook is a record of experiments on 5 models. They behaved differently: a static hot set that worked on one model was no better than random on another, and the cheapest way to serve a miss flipped between models on the same hardware. So this is a map, and the first job is to find where you are on it.

For every trick the chapters say what the problem was, what changed, what the control showed, and when the test is worth repeating. The numbers belong to a specific model, engine and rig ([cases/README.md](../cases/README.md)). For transfer, what matters most is being able to reproduce the original bottleneck on your setup.

## The route

1. **Orient.** Fill the [situation card](../protocol/situation-card.md). It ends with a tag line such as `#offload #weak-skew #cpu-lane #multi-gpu #agent`.
2. **Locate.** Answer the questions below, or enter by symptom. Each answer points to 1-2 chapters.
3. **Filter.** Grep the [ledger](../ledger/hypotheses.md) by your tags to see what was already tried under your conditions, and open the closest [case](../cases/README.md).
4. **Test.** Pick the cheapest test that reproduces the bottleneck and follow the [experiment rules](../protocol/experiment-rules.md).
5. **Record.** Write the result down with the [entry template](../protocol/entry-template.md), failures included.

There is no fixed order of chapters. 2 different models will take 2 different routes.

## Entry by regime

### Does the model fit in VRAM at a quant you accept?

Compare the tensor sizes with VRAM minus KV, buffers and the drafter.

- **It fits on 1 card** (`#in-vram #1gpu`). There are no misses, the time is the constant part. Go to the drafter ([08](08-speculative-decoding.md)), the output head ([13](13-output-head.md)) and sessions ([11](11-sessions-and-prompt-cache.md)). This playbook has little data of its own for this regime.
- **It fits across several cards** (`#in-vram #multi-gpu`). Tensor parallel first, then the exchange between cards ([05](05-multi-gpu.md)), then the drafter. Closest case: GLM-5.3-Flash at 2.05 bpw.
- **It does not fit** (`#offload`). Part of the experts go to RAM. Continue with the next question.

### Which term of token time is larger: the constant part or the misses?

Fit `time = a + b × misses` from 2-3 runs with different VRAM budgets for experts ([01](01-cost-model.md), [tools/fit_cost.py](../tools/fit_cost.py)).

- **Misses dominate.** The next 2 questions (skew, and who serves a miss) decide what to do, and the quant ([09](09-quants.md)) decides how many experts fit.
- **The constant part dominates.** Stop moving experts around. On DeepSeek, once the CPU part was ~10% of a round, 10 GB more VRAM for experts gave about +1.8% (calc). Work on kernels, the exchange between cards ([05](05-multi-gpu.md)) and the drafter ([08](08-speculative-decoding.md)).

### Is routing skewed?

Record a router trace and compare a static hot set with a random one at your budget ([tools/route_sim.py](../tools/route_sim.py)).

- **Strong skew** (`#strong-skew`). A profile and static placement work ([03](03-expert-profile-and-placement.md)), with a live cache on top ([04](04-live-expert-cache.md)). Closest cases: DeepSeek V4.1 Flash, Qwen3.8-Flash-Next.
- **Weak skew** (`#weak-skew`, the gap to random is under 10 points). The live cache is the main mechanism ([04](04-live-expert-cache.md)), drafter windows should be short ([08](08-speculative-decoding.md)), and fewer bits put more experts in VRAM ([09](09-quants.md)). Closest cases: MiniMax-M3.1-Flash, GLM-5.3-Flash.

### Who serves a miss cheaper: the CPU or a copy over PCIe?

Time 1 expert on the CPU under load, 1 expert copy over the bus and 1 full decode layer ([02](02-serving-a-miss.md)).

- **The CPU** (`#cpu-lane`). Then RAM bandwidth, thread count and NUMA decide the speed ([06](06-cpu-and-ram.md)).
- **The copy** (`#pcie`). Then a VRAM cache with eviction matters and RAM channels matter less ([02](02-serving-a-miss.md), [04](04-live-expert-cache.md)).

### How many cards, and in which mode?

Count the cards that improve decode on your load, then compare layer split with tensor parallel ([05](05-multi-gpu.md)). On Qwen the third and fourth card added only prefill.

### What does the user feel as slow?

- **Generation.** The questions above, then the drafter ([08](08-speculative-decoding.md)).
- **The first read of a long input.** Prefill ([07](07-prefill.md)).
- **Every turn of an agent.** Log a real session first: how much is generation, how much is re-read, what invalidates the prefix ([11](11-sessions-and-prompt-cache.md)). Then the re-read part of [07](07-prefill.md).
- **Service start.** Loading and page cache ([12](12-loading-and-big-tables.md)).
- **Pauses inside an answer, or a build that is faster on average and feels worse.** Release gates ([15](15-quality-and-release-gates.md)).

### Is system RAM the limit?

A big side table can leave pinned RAM for NVMe ([12](12-loading-and-big-tables.md)). Fewer bits for the experts come next ([09](09-quants.md)). Pruning is the last resort and needs a check on every domain ([10](10-pruning-and-merging.md)).

### On any route

Benchmark by [14](14-benchmarking-and-traps.md), gate quality by [15](15-quality-and-release-gates.md), and follow the [experiment rules](../protocol/experiment-rules.md).

## Entry by symptom

| What you see on your model | What to record first | Go to |
|---|---|---|
| The model does not fit in VRAM and you do not know if offload will be fast | Router trace, picks from top-N, hits at your VRAM budget | [01 Cost model](01-cost-model.md) |
| During generation most experts are in RAM | Hits by layer and topic, GPU time waiting for the CPU | [02 Serving a miss](02-serving-a-miss.md), [04 Live cache](04-live-expert-cache.md) |
| A static hot set gives almost the same hits as a random one | Profile vs random at the same budget, oracle on the answer itself | [03 Profile and placement](03-expert-profile-and-placement.md), [04 Live cache](04-live-expert-cache.md) |
| Several cards are lightly loaded and the generation step is long | Exchange time, message size, wait for the last card | [05 Multi-GPU](05-multi-gpu.md) |
| Decode got slower after a RAM or BIOS change | Triad by thread count, NUMA layout, DIMM temperature | [06 CPU and RAM](06-cpu-and-ram.md) |
| On a long input the GPU loads the same MoE weights again and again | Bytes CPU→GPU per fresh token and copy time outside compute | [07 Prefill](07-prefill.md) |
| The drafter guesses tokens but the answer is slow | Accepted tokens and milliseconds of the *full* step | [08 Speculative decoding](08-speculative-decoding.md) |
| You are choosing between quants | Experts in VRAM per quant, KL on one panel, a control permutation | [09 Quants](09-quants.md) |
| You want to drop cold experts | REAP score, quality on every domain, the full model at fewer bits | [10 Pruning](10-pruning-and-merging.md) |
| A repeat agent turn takes almost as long as the first | How many tokens the server really restored after another request | [11 Sessions and prompt cache](11-sessions-and-prompt-cache.md) |
| The model does not fit in system RAM because of one table | Row size, unique accesses, pinned pages | [12 Loading and big tables](12-loading-and-big-tables.md) |
| Loading takes minutes or hangs | Source drive, page cache state, THP compaction | [12 Loading and big tables](12-loading-and-big-tables.md) |
| The output projection onto the vocabulary is a large share of the step | Cost of the full pass, number of candidates for the exact recheck | [13 Output head](13-output-head.md) |
| A short benchmark promised a gain that real use does not show | Replay of real sessions, free generation, thresholds set in advance | [14 Benchmarking and traps](14-benchmarking-and-traps.md) |
| The answers of 2 builds diverged | The first difference on a fixed input, logits, the router and the snapshot boundary | [15 Quality and release gates](15-quality-and-release-gates.md) |
| The candidate is faster on average but feels worse | The slowest input block, output windows, TTFT through the API | [15 Quality and release gates](15-quality-and-release-gates.md) |

Have an idea already? Check the [hypothesis ledger](../ledger/hypotheses.md) first. Starting a new model from zero? The [checklist](../protocol/new-model-checklist.md) lists the steps that were useful in the order we would take them now.

## How to read the metrics

Prefill is processing the known input, decode is generating the continuation. TTFT is the time from sending the request to the first token at the client. The KV cache stores intermediate keys and values of attention. The prefix cache reuses the state of the common part of a request. The expert cache holds weights, the Engram row cache holds table data. These caches have different keys, memory budgets and hit criteria. "Verify" is the step where the target model checks the drafter's proposals. Full list: [glossary](../glossary.md).

Evidence labels: a number with no label is a real run on our rig. (sim) is a simulator on real traces, (calc) is a calculation, (micro) is a microbenchmark of one operation, (ext) is someone else's number.

## 15 findings

From 4 months with DeepSeek V4.1 Flash, Qwen3.8-Flash-Next, GLM-5.3-Flash and MiniMax-M3.1-Flash:

1. **While misses are frequent, decode of an offloaded MoE is limited by misses per token times the cost of a miss.** GPU compute is secondary then, and everything that cuts misses or their cost makes it faster. Once misses are cut (on DeepSeek the CPU part is ~10% of a round after all fixes), the limit moves to the constant part: dense weights, attention, kernel launches. There plain GPU optimizations help (+5.6% from 1 matvec kernel).
2. **Hot experts in VRAM work, but how well depends on the model.** On DeepSeek V4.1 Flash 100 of 384 experts of a layer cover 70% of picks, and Qwen3.8-Flash-Next is strongly skewed too. GLM-5.3-Flash and MiniMax-M3.1 have weak global skew: on GLM the "cold" half of experts gets 41% of picks, on MiniMax random placement gives 61% hits vs 64-69% by profile (sim). A static set is almost useless there, but there is locality inside one request and a live cache gives 94-98%. Check the skew before you build the system.
3. **Take the hot expert profile on your own load.** On our agent session the top-180 experts of a layer covered 68% of picks by the stock profile, 86% by a profile accumulated on similar work, 94% by the session itself. By the static placement calculation a foreign profile gives 36 tok/s vs 42 with your own.
4. **Static plus a live cache beats either one alone.** The profile sets the starting placement, the live cache with decay adapts it to the current request. On DeepSeek the live cache catches up in 1-2 requests, so a separate "profile refresh for the task" gave us nothing.
5. **A decode miss is more often cheaper to compute on the CPU in RAM than to move over PCIe.** On DeepSeek and MiniMax the CPU path won already on 2-channel RAM (+17% on DeepSeek, on MiniMax the PCIe stream was removed). On GLM in Maya on the same memory PCIe won. The real cost of a CPU expert in the specific build and the design of the PCIe path decide, not the channel count by itself. In prefill it is the opposite: cold experts go to the GPU by DMA.
6. **Pinning CPU threads to "near" cores did not help** (-10 to +3%, on 4 channels). What helped: interleaving over all channels at once and NUMA-aware copies. 8 channels instead of 4 gave DeepSeek +20% decode on a short test.
7. **Speculative decoding is the best single lever**: +44-46% (DFlash2 on Qwen Flash-Next), 1.2-1.4× (DSpark on DeepSeek, old build). But the gain is smaller than on a dense model in VRAM (2.75-3.6× there): every row of the verify window wakes its own cold experts. On models with weak skew a long window gives almost nothing. Short speculation works there (GLM, MTP length 1: +17%).
8. **Naive pruning of experts is worse than keeping all of them in RAM.** Forbidding experts outside VRAM on GLM, with a third forbidden, gave KL 0.37 vs 0.047 for the full model. Merging experts (REAM) on 1 GLM layer gave an output error 3x larger than plain removal: the experts are almost orthogonal.
9. **In agent work, turn time is generation and re-read, not cold prefill.** On a live session generation took 77-81% of time, and the median re-read was 250-400 tokens. The prompt cache and session persistence pay more than faster prefill at 128K.
10. **Short benchmarks are systematically wrong.** A short test promised +27% decode, real generation gave +2%. Decide only on a replay of real sessions and free generation, with a threshold written down in advance.

From the Hy4 Flash port on 1, 2 and 4 cards:

11. **Separate "tokens per expert load" from "activations materialized at once".** The build with one expert load for a block of up to 57,600 tokens and attention in tiles of 2,048 took single-card prefill from ~420 to ~1,420-1,510 tok/s (the number is for the full build).
12. **The prompt does not predict the experts of the answer.** 220 experts chosen by input stats covered 48% of generation picks, by the generation's own stats 96%.
13. **In tensor parallel the decode all-reduce is a latency problem, not a bandwidth one.** About 30% of decode kernel time went to exchanging tiny messages. One 8-byte word per step cut verify from 12.64 to 11.22 ms.
14. **A change can match on the first turn and diverge on the second.** Q4 in the drafter head matched on 4,400 tokens, then shifted a snapshot boundary by 1 position and changed the second turn. Test 2 turns.
15. **A release gate has to name its number.** Mean over all answers, the worst answer and the worst rolling window gave 187.67, 159.35 and 132.49 tok/s on the same build. A candidate that improved one minimum and lowered the mean was rejected.

## Chapters

| # | Chapter | In one line |
|---|---|---|
| 01 | [Cost model](01-cost-model.md) | `time = constant + misses × cost`, skew, counting in experts |
| 02 | [Serving a miss](02-serving-a-miss.md) | memory tiers, CPU lane vs PCIe copy |
| 03 | [Profile and placement](03-expert-profile-and-placement.md) | how to find and place hot experts, profile traps |
| 04 | [Live expert cache](04-live-expert-cache.md) | policies, capacity, prediction |
| 05 | [Multi-GPU](05-multi-gpu.md) | layer split, TP, EP, all-reduce transport |
| 06 | [CPU and RAM](06-cpu-and-ram.md) | channels, NUMA, threads, what "near memory" did not give |
| 07 | [Prefill](07-prefill.md) | DMA, chunks, big block and small tile, EP in prefill, graphs |
| 08 | [Speculative decoding](08-speculative-decoding.md) | round cost, acceptance, drafter training, evaluation without integration |
| 09 | [Quants](09-quants.md) | bits vs experts in VRAM, hybrids, the control permutation |
| 10 | [Pruning and merging](10-pruning-and-merging.md) | REAP, REAM, masking |
| 11 | [Sessions and prompt cache](11-sessions-and-prompt-cache.md) | anatomy of an agent turn, snapshots, A → B → A |
| 12 | [Loading and big tables](12-loading-and-big-tables.md) | page cache, THP, n-gram tables on NVMe |
| 13 | [Output head](13-output-head.md) | cheap estimate with an exact recheck |
| 14 | [Benchmarking and traps](14-benchmarking-and-traps.md) | reference first, replay, the list of traps |
| 15 | [Quality and release gates](15-quality-and-release-gates.md) | 3 levels of equivalence, what to ship |
