# Hypothesis ledger

Every idea we tried, with the verdict. Check here before you propose or build something: it may already have an answer, and the "revisit when" column says what would change it.

Verdicts: ✅ worked, ❌ did not work, ◐ narrow or with conditions, ○ calculation or simulation only, not run.

Models: DS = DeepSeek V4/V4.1 Flash, QW = Qwen3.8-Flash-Next, GLM = GLM-5.3-Flash, MM = MiniMax-M3.1-Flash, HY = Hy4 Flash. All numbers are from the rig in [cases/README.md](../cases/README.md) unless marked (ext). They transfer as mechanisms, not as absolute values.

A ❌ here means "did not pay on this model, engine and hardware". Read the reason before you drop the idea on a different setup.

## Tags

The last column says under which conditions the verdict was obtained. Fill the [situation card](../protocol/situation-card.md) to get your own tag line, then filter:

```bash
grep "#weak-skew" ledger/hypotheses.md                 # everything about models with weak routing skew
grep "#offload" ledger/hypotheses.md | grep "#tp"      # tensor parallel with experts in RAM
grep "#drafter" ledger/hypotheses.md | grep "❌"       # drafter ideas that failed
grep "#dense-ok" ledger/hypotheses.md                  # also applies to dense models
```

| Group | Tags |
|---|---|
| Where the experts are | `#offload` (part of them in RAM), `#in-vram` |
| Routing | `#strong-skew`, `#weak-skew` |
| Who serves a miss | `#cpu-lane`, `#pcie` |
| Cards | `#1gpu`, `#multi-gpu`, `#layer-split`, `#tp`, `#ep` |
| Phase | `#decode`, `#prefill`, `#reread`, `#load` |
| Part of the system | `#drafter`, `#kv`, `#head`, `#big-table`, `#kernel` |
| Workload | `#agent`, `#long-context`, `#batch` |
| Hardware | `#ram`, `#numa`, `#zen2`, `#pcie3` |
| Kind of change | `#quant`, `#pruning`, `#quality-risk` (changes the numbers the model outputs) |
| Scope | `#dense-ok` (the result does not depend on MoE routing) |

A row without a tag of some group was not tested across that group. A missing `#weak-skew` does not mean the result holds on weak skew.

## Expert placement and cache

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| PL-01 | Hot experts in VRAM by profile | ✅ on skewed models | DS: 86% coverage with our own profile vs 68% with a foreign one | routing skew | [03](../playbook/03-expert-profile-and-placement.md) | #offload #strong-skew #decode |
| PL-02 | Same on models with weak skew | ❌ | MM (sim): profile 64-69%, random 61%. GLM: a fixed set cuts misses by only 10% | globally routing is almost uniform, every request has its own working set | [01](../playbook/01-cost-model.md), [03](../playbook/03-expert-profile-and-placement.md) | #offload #weak-skew #decode |
| PL-03 | Live cache with decay | ✅ | MM (sim): 61-69% → 87-96%, in service 71-94%. HY 2 GPUs: 81 tok/s at 600 tokens, 91-97 at 1,200 | every answer has its own working set | [04](../playbook/04-live-expert-cache.md) | #offload #weak-skew #strong-skew #decode |
| PL-04 | Refresh the profile for the task before launch | ❌ (for us) | zero | the live cache catches up in 1-2 requests. Upstream saw 42 → 75% hits on the first answer (ext) | [04](../playbook/04-live-expert-cache.md) | #offload #strong-skew #decode |
| PL-05 | Warm the cache from the prompt after prefill | ❌ | DS: short test +21-24%, free generation 0, TTFT +0.3-1.9 s | the effect exists only on short decode. Revisit with a cheap copy from the prefill buffer (~20 µs, MM plan) | [04](../playbook/04-live-expert-cache.md) | #offload #decode #long-context |
| PL-06 | Load the "almost picked" experts of the previous token | ❌ | GLM: 22.9-30.9 misses vs 23.2 | neighbor tokens pick different experts | [04](../playbook/04-live-expert-cache.md) | #offload #weak-skew #decode |
| PL-07 | Predict the router of the next layer | ○ | GLM (sim): +20-28%. MM offline: top-8 catches 84-90% | PCIe 3.0 makes it only 2+ layers ahead. Revisit on PCIe 4.0/5.0 | [04](../playbook/04-live-expert-cache.md) | #offload #weak-skew #decode #pcie3 |
| PL-08 | Free VRAM for experts (smaller context, int8 KV, encoder on demand) | ◐ | QW +3-5%, GLM Maya-L +7-10%, DS ~+1.8% per 10 GB (calc) | works while misses are expensive | [01](../playbook/01-cost-model.md) | #offload #decode #kv |
| PL-09 | "Faster" live cache with less hysteresis | ❌ | DS: 36.4 vs 39-40 tok/s | swaps cost time | [04](../playbook/04-live-expert-cache.md) | #offload #strong-skew #decode |
| PL-10 | Give free prefill VRAM to experts during decode | ✅ | HY 1 GPU: part of the build that took decode at 57K from 39.69 to 53.70 | one adaptive cache instead of a permanent and a temporary set | [04](../playbook/04-live-expert-cache.md) | #offload #1gpu #decode #prefill |
| PL-11 | Reserve KV, projector and buffers first, then place experts | ✅ | HY: with an automatic budget the same binary could place a different number of experts, which affected the NVMe A/B. A variant with a fixed budget matched both checked HTTP answers | an A/B must compare the same placement | [03](../playbook/03-expert-profile-and-placement.md) | #offload #decode |
| PL-12 | Lossless compression of experts with in-kernel decode | ❌ | HY: frees ~0.725 GiB on the busiest card, 470,016 values match, but 12 Q3 experts 21.71 → 326.25 µs, Q4 23.35 → 192.10 µs | decode cost. Revisit with another decoder, or if the residency gain covers it in a full step | [09](../playbook/09-quants.md) | #offload #kernel |
| PL-13 | Choose the hot set for an answer from the stats of its prompt | ❌ | HY (1 long text): 220 experts picked by input stats cover 48% of generation picks, picked by the generation itself 96% | the prompt does not predict the experts of the answer. Start from past answers and adapt | [03](../playbook/03-expert-profile-and-placement.md) | #offload #decode #long-context |
| PL-14 | Commit global KV on demand so a large configured window can coexist with resident experts on short turns | ✅ | Inkling 2 Mini on 1 card, 1M configured: 285 resident slots with eager KV, all 3,072 with managed global KV | deep decode must be timed at populated depth | [03](../playbook/03-expert-profile-and-placement.md) | #kv #offload #long-context |
| PL-15 | Keep every expert in VRAM and replay a token as one CUDA graph, picks resolved on the device | ✅ | Inkling 2 Mini, 1 RTX 3090: llama.cpp 57 → 112 tok/s | only when experts, dense weights and KV fit one card | [03](../playbook/03-expert-profile-and-placement.md) | #in-vram #1gpu #decode #kernel |
| PL-16 | Fuse a token's small launches (convs into products, routing, norms, loop messages) | ✅ | Inkling 2 Mini: 1,404 → 781 launches a token, each fusion bit-exact | any launch costs 2-3 µs; count launches before bytes | [03](../playbook/03-expert-profile-and-placement.md) | #in-vram #decode #kernel #dense-ok |
| PL-17 | Token attention on tensor cores straight from the KV cache into registers | ✅ | Inkling 2 Mini 65K deep: FP16 63 → 77, FP8 53 → 84 tok/s; 846 / 776 GB/s | needs R ≤ 8 heads per KV head, D = 128 | [03](../playbook/03-expert-profile-and-placement.md) | #kv #long-context #decode #kernel #dense-ok |
| PL-18 | Skip V reads for steps whose attention weights are all tiny | ❌ | Inkling 2 Mini real 64K text: 0.0% of 16-key steps skippable | revisit on models with peaked global attention | [03](../playbook/03-expert-profile-and-placement.md) | #kv #long-context #dense-ok |
| PL-19 | Group loads inside small latency-bound token kernels | ◐ | Inkling 2 Mini: attention residuals +2.5% bit-exact; loop gates and FP16 matvec slower (4.9 → 24 µs) | profile each kernel, registers and occupancy decide | [03](../playbook/03-expert-profile-and-placement.md) | #in-vram #decode #kernel |
| PL-20 | All core layers' loop messages of a pass in one launch | ✅ | Inkling 2 Mini: 127.2 → 128.2 tok/s, bit-exact | looped models only | [03](../playbook/03-expert-profile-and-placement.md) | #in-vram #decode #kernel |
| PL-21 | Image encoder on the CPU to give its VRAM to the context | ✅ | Inkling 2 Mini: 262K → 344K context with images; new image ~4.5 s on 48 cores | worth it when context, not image latency, matters | [03](../playbook/03-expert-profile-and-placement.md) | #kv #long-context #1gpu |

## Where to serve a miss

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| MS-01 | Decode: compute the miss on the CPU in RAM | ✅ on DS, MM, HY | DS: turning off the PCIe lane +17% (2 channels). HY 2 GPUs, short code of 600 tokens: 24.6 → 70.4 tok/s | a PCIe expert costs more than a layer | [02](../playbook/02-serving-a-miss.md) | #offload #cpu-lane #decode #pcie3 |
| MS-02 | Decode: move the miss over PCIe | ◐ | GLM in Maya on 2 channels: 24.9 → 45-46 after calibration | there the CPU expert under load cost more | [02](../playbook/02-serving-a-miss.md) | #offload #pcie #weak-skew #decode |
| MS-03 | PDEP fix of the CPU lane on Zen 2 | ✅ | expert 1.6 → 0.44 ms, lane ×3.6 | PDEP runs in microcode | [02](../playbook/02-serving-a-miss.md) | #offload #cpu-lane #zen2 #kernel |
| MS-04 | Pin threads to near CCX ("near memory" tier was not built) | ❌ | -10 to +3% (4 channels) | by our estimate the limit is the CCD link, not distance. Revisit on 2 sockets or NPS4 | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #numa |
| MS-05 | 8 RAM channels instead of 4 | ✅ when the CPU is on the path | QW 1 card +7-17%, 2 cards 0. DS +20% (short test) | decode = RAM bandwidth for misses | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #ram |
| MS-06 | NPS2 instead of NPS1 on a partial population | ✅ | DS 67.7 vs 46.5-57.5 (short test) | NPS1 split memory into halves with no interleaving. Needs NUMA-aware copies | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #numa #ram |
| MS-07 | SMT for the CPU lane | ❌ | worse by 2-148% | | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane |
| MS-08 | Generic software prefetch in the CPU lane | ❌ | DS: -1 to +3% | | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane |
| MS-09 | Prefetch the rows that slow down at depth | ✅ | HY: CPU lane request 0.88 → 0.33 ms (was 0.39 shallow) | a targeted fix of a profiled cache-miss problem | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #long-context |
| MS-10 | Split CPU lane work by dot products, not by rows | ✅ | MM: +16-23% in speculation windows | 40% of lane time was waiting for the slowest thread | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #drafter |
| MS-11 | Fewer CPU lane threads than cores | ✅ | Strata 0.1.45, 1 card: 46 threads (set by the engine) 20-60 tok/s, 32 threads ~75. Strata 0.2 sets 32 itself and holds ~87 | the thread count of the CPU lane is critical, SMT does not help | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #1gpu |
| MS-12 | Specialized kernels for the quant in use | ◐ | HY: IQ2 kernels, short decode 92.6 → 99.2 tok/s. A new SWA kernel for 32 heads: 0 (attention was ~2% of the step) | profile the share first | [09](../playbook/09-quants.md) | #offload #quant #kernel |
| MS-13 | Move complete expert layers to the CPU in order of model visits per byte when global KV occupies the GPU | ◐ | Inkling 2 Mini, 1 RTX 3090 + 24 EPYC threads, 8-channel DDR4: at 1,048,064 allocated positions a controlled synthetic-KV decode improved from 10.55 to 18.13 tok/s | count the repeated visits, preserve the dot product order and time a populated request | [06](../playbook/06-cpu-and-ram.md) | #offload #cpu-lane #1gpu #long-context |

## Several GPUs

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| MG-01 | 2 cards instead of 1 (layer split) | ✅ | QW +31-38% (4 RAM channels, ~+15-24% on 8) | more experts in VRAM, hits 74-79% → 92-95% | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu #layer-split #decode #strong-skew |
| MG-02 | 3-4 cards for decode (layer split) | ❌ | QW about zero. Only prefill grows | every stage is one more transfer, misses are already rare | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu #layer-split #decode |
| MG-03 | Helper cards for experts instead of layer split | ❌ | -25-30% (77 vs 105 tok/s on 2 cards, IQ3_S, old Strata 0.1.40) | | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu |
| MG-04 | TP under offload | ❌ / ◐ | DS TP=2 with P2P: decode +7%, read of a 344K prompt 2.2x slower, EP does not work under TP, 6,170 experts in VRAM instead of 7,352. Dropped | a miss on any rank blocks everyone. See MG-06 for the case where a CPU lane serves the misses | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu #tp |
| MG-05 | TP without offload | ✅ | GLM 48.3 → 66-68 | | [05](../playbook/05-multi-gpu.md) | #in-vram #multi-gpu #tp |
| MG-06 | TP under partial placement with a CPU lane | ✅ | HY Q3_K, v1.1.69: prefill 695-696 → 1,098-1,101, decode 54.7-55.9 → 67.1-68.9 | the cards keep computing in parallel and the CPU lane serves the missing experts. 1 model, 1 engine version | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu #tp #cpu-lane |
| MG-07 | Layer split instead of TP on 4 cards | ❌ | HY 4 cards: decode with MTP 25-35% slower | | [05](../playbook/05-multi-gpu.md) | #multi-gpu #layer-split #tp #drafter |
| MG-08 | P2P all-reduce for prefill in TP | ✅ | GLM 961 → 1,447. HY peer DMA 1,958.6 → 2,363.5 on 2K-8K | host all-reduce was ~70% of the pass | [05](../playbook/05-multi-gpu.md) | #multi-gpu #tp #prefill #dense-ok |
| MG-09 | P2P all-reduce for decode in TP (GLM transport) | ❌ | -6% (63.4 vs 67.7) | stock transport is 8 µs per call, the wait is for the slowest rank | [05](../playbook/05-multi-gpu.md) | #in-vram #multi-gpu #tp #decode |
| MG-10 | Decode all-reduce as one 8-byte word with the step number | ✅ | HY: verify 12.64 → 11.22 ms, MTP K=2 code 145.3 → 153.0-153.6 tok/s | tiny messages, the tail waited for flags | [05](../playbook/05-multi-gpu.md) | #multi-gpu #tp #decode #drafter #dense-ok |
| MG-11 | More threads, bigger blocks, fewer fences in the push | ❌ | HY: no gain. One shared fence helps T=1, hurts T≥2 | | [05](../playbook/05-multi-gpu.md) | #multi-gpu #tp #decode |
| MG-12 | EP in prefill over several PCIe links | ✅ | DS: re-read of 1.5K -42% | idle links pull cold experts in parallel | [07](../playbook/07-prefill.md) | #offload #multi-gpu #layer-split #ep #prefill #reread |
| MG-13 | EP in decode | ❌ | llama.cpp, DS V4 in VRAM: -34% | +345 µs per MoE layer of host sync and launch ladder | [05](../playbook/05-multi-gpu.md) | #in-vram #multi-gpu #ep #decode |
| MG-14 | Each expert on 1 card instead of equal slots | ✅ | HY IQ2: VRAM 79.3 → 65.5 GB, decode 121.8 → 128.1 | padding: 8 blocks instead of 6 | [05](../playbook/05-multi-gpu.md) | #multi-gpu #tp #quant |
| MG-15 | TP only for attention | ◐ | MM: decode +3-10%, prefill -4.8% | P2P gather shares PCIe with the expert stream. Behind a flag | [05](../playbook/05-multi-gpu.md) | #offload #multi-gpu #tp |
| MG-16 | Send packed query heads to 3 neighbors | ❌ | HY: verify T=3 12.12 → 13.26 ms | | [07](../playbook/07-prefill.md) | #multi-gpu #tp #drafter |

## Prefill

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| PF-01 | DMA from pinned copies for all formats (with layer skip on the prompt and auto chunk) | ✅ | DS: 32K 430 → 2,199 tok/s | stock went through staging at ~2.7 GB/s per card | [07](../playbook/07-prefill.md) | #offload #prefill |
| PF-02 | Large chunks | ✅ on long inputs | DS 128K +15%, short -7-8%. GLM 2048 → 4096: 764 → 1,000 | cold experts travel less often | [07](../playbook/07-prefill.md) | #offload #prefill #long-context |
| PF-03 | One expert load per logical block, attention in small tiles | ✅ | HY 1 GPU: 57,587 tokens 415.6 → 1,510.2 tok/s, 261,888 tokens 420.0 → 1,422.5 | bytes per token over PCIe dropped ~8x (calc from 66.0 GB per 4,096 tokens vs 67.7 GB per 32,768, segments at different depths) | [07](../playbook/07-prefill.md) | #offload #1gpu #prefill #long-context |
| PF-04 | RAM copies of borrowed experts | ✅ | DS: tail return 331-516 → 82-117 ms | no re-read from the file | [07](../playbook/07-prefill.md) | #offload #prefill #reread #agent |
| PF-05 | Experts with 1-N rows on the CPU inside a chunk | ❌ | re-read -20-32%, but quality did not pass (CPU rounding) | | [07](../playbook/07-prefill.md) | #offload #prefill #reread #quality-risk |
| PF-06 | Fused prefill experts on the GPU | ◐ | Strata: +13%, KL 0.033 | not taken into the service because of KL | [07](../playbook/07-prefill.md) | #offload #prefill #quality-risk #kernel |
| PF-07 | Full-speed tensor kernels on GeForce (gpu-unlock) | ✅ | Strata prefill +10-12% | decode unchanged, bit for bit | [07](../playbook/07-prefill.md) | #prefill #kernel #dense-ok |
| PF-08 | Gather K/V into VRAM once per chunk (attention read host RAM) | ✅ | MM: prefill 64K ×3.3, session replay ×3.75 (1 run) | attention read the same blocks 73-383 times per chunk | [07](../playbook/07-prefill.md) | #prefill #long-context #kv |
| PF-09 | Capture CUDA Graphs for known widths before ready | ✅ | HY: first answer at 98K 154.83 → 169.60 tok/s | keeping extra graphs alive left too little VRAM for the first prefill | [07](../playbook/07-prefill.md) | #decode #kernel #dense-ok |
| PF-10 | CUDA graphs in decode under offload | ○ | DS: estimate +2-3% for days of work. Gaps <20 µs are only 3.5% | | [07](../playbook/07-prefill.md) | #offload #decode #kernel |
| PF-11 | Deeper local KV replica | ◐ | HY: at 261,632 saves 1.25-1.53% of verify, first step prep 41.9 → 113.5 ms | gain ends past the boundary | [07](../playbook/07-prefill.md) | #multi-gpu #tp #kv #long-context |
| PF-12 | Copy the deep KV snapshot early, during prefill | ❌ | HY: final copy 56.4 → 1.4 ms, but full HTTP 91.206 → 91.276 s | probably PCIe contention in the neighbor block. Revisit if a trace shows a free link | [07](../playbook/07-prefill.md) | #multi-gpu #kv #prefill |
| PF-13 | INT8 for the DSA indexer | ❌ | HY: 47.99 vs 46.93 µs for the FP16 path with the exact recheck counted | revisit with other candidate shares and lengths | [07](../playbook/07-prefill.md) | #kernel #long-context |
| PF-14 | Start independent ops earlier in decode | ◐ | HY: early Engram ~1.4% (7,200 IDs match). Other variants small and shape-dependent | only where the profile shows the critical path | [07](../playbook/07-prefill.md) | #decode #kernel |
| PF-15 | Unify structs and kernels across phases | ❌ | HY: 1 field cost 2-3% prefill, a 4-slot loop +0.35 ms short decode | revisit on a compiler or architecture change | [14](../playbook/14-benchmarking-and-traps.md) | #kernel #dense-ok |
| PF-16 | FP8 cache in flash prefill tiles and a looped model's gates over a chunk as cuBLAS GEMMs | ✅ | Inkling 2 Mini: +24% prefill, 2,500 tok/s at 4K-65K (llama.cpp 1,052) | | [07](../playbook/07-prefill.md) | #prefill #kv #in-vram |
| PF-17 | Short prompts read experts by groups instead of FP16 decode + GEMM | ✅ | Inkling 2 Mini: 12 new tokens 118 → 56 ms | up to ~96 tokens | [07](../playbook/07-prefill.md) | #prefill #in-vram |

## Speculation

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| SD-01 | A ready drafter (DFlash2, DSpark) | ✅ | +20-46% on skewed models (DS: old build) | amortizes the constant part of the round | [08](../playbook/08-speculative-decoding.md) | #drafter #offload #strong-skew |
| SD-02 | A long window on models with weak skew | ❌ / ◐ | GLM: DFlash2 only +4% over MTP. MM negative before fixes. Short MTP on GLM +17% | every row wakes its own cold experts | [08](../playbook/08-speculative-decoding.md) | #drafter #offload #weak-skew |
| SD-03 | Confidence threshold 0.5 | ✅ | QW: +8-15% vs 0.35 | | [08](../playbook/08-speculative-decoding.md) | #drafter #dense-ok |
| SD-04 | Draft vocabulary for the language | ✅ | QW: acceptance on Russian 32% → 54% | no training needed | [08](../playbook/08-speculative-decoding.md) | #drafter #dense-ok |
| SD-05 | Cost-based window policy | ✅ | MM: +3-13% | | [08](../playbook/08-speculative-decoding.md) | #drafter #offload #weak-skew |
| SD-06 | Fine-tune the drafter on our own data | ❌ | QW DSpine +3.5% (2/3 from vocabulary). DS DSpark 3.215 → 3.19-3.20. HY MTP 1,547 → 1,558 of 3,600 | DSpark: errors come from small capacity, not from a mismatch with the quant. The others: gains too small to ship | [08](../playbook/08-speculative-decoding.md) | #drafter #dense-ok |
| SD-07 | Suffix lookup | ◐ | +24% on file edits, zero on text. HY: 0-3 tokens of ~400 | needs repeats in the context | [08](../playbook/08-speculative-decoding.md) | #drafter #agent #dense-ok |
| SD-08 | A separate wider drafter instead of native MTP | ❌ on HY | DFly2 K=5: 126 vs 153 tok/s for MTP K=2 | 25.7 ms verify of 6 positions. Revisit with a cheaper wide verify | [08](../playbook/08-speculative-decoding.md) | #drafter #multi-gpu #tp |
| SD-09 | Switch between 2 drafters, or verify a branch tree | ❌ | HY: 2.423 vs 2.431 tokens/step. Tree: 178.35 → 167.01 tok/s (sim) | decided on saved trajectories, not built | [08](../playbook/08-speculative-decoding.md) | #drafter |
| SD-10 | BlockVerify for a group of proposals | ❌ | HY: 152.96 → 153.09 tok/s | | [08](../playbook/08-speculative-decoding.md) | #drafter |
| SD-11 | MTP draft length 1 instead of the default 3 | ✅ | GLM: 66-68 → 75.7 | default 3 gave zero or negative | [08](../playbook/08-speculative-decoding.md) | #drafter #in-vram #tp |
| SD-12 | Our own Domino drafter for AWQ | ❌ | 4.8% slower than no speculation | | [08](../playbook/08-speculative-decoding.md) | #drafter #in-vram |
| SD-13 | Rejection sampling instead of exact match | ○ | QW DFlash2: +3% (sim) | | [08](../playbook/08-speculative-decoding.md) | #drafter |
| SD-14 | Speculation under batching | ❌ | vLLM n-gram: +40% on 1 stream, -30 to -51% on 4-8 | | [08](../playbook/08-speculative-decoding.md) | #drafter #batch #dense-ok |
| SD-15 | Verify long copies proposed by earlier text in the request | ◐ | Inkling 2 Mini on 1 card: 488.9 tok/s for a verified 509-token Python edit, 113-114 on fresh prose and code using the same serving profile | measure copy acceptance and ordinary requests separately | [08](../playbook/08-speculative-decoding.md) | #drafter #decode |
| SD-16 | DSpark with a 4-5 token window on a model with every expert in VRAM | ○ | Inkling 2 Mini acceptance: prose 1.9-2.2, code 2.7-2.9, rewrite 3.6-4.3; window experts 2.4-2.8× one token's; verify via the prompt path 41 ms (loses) | needs a verify step ≤ ~12-13 ms; vLLM reports +14% prose, +67% code on RTX 5090 | [08](../playbook/08-speculative-decoding.md) | #drafter #in-vram #decode |

## Output head

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| HD-01 | Q8 estimate + error bound + exact recheck (greedy) | ✅ | HY: matched on all 1,158 IDs and 9 dialog boundaries | the proof is per kernel | [13](../playbook/13-output-head.md) | #head #decode #dense-ok |
| HD-02 | Q4 only in the drafter head, no recheck | ❌ rolled back | HY: 176.37 → 182.60 tok/s, first turn matched, second turn diverged | shifted the snapshot boundary by 1 position | [13](../playbook/13-output-head.md) | #head #drafter #quality-risk #agent |
| HD-03 | Q2 short list with a norm bound | ❌ lab only | HY: bound left 120,832 of 120,832 rows | the guarantee cost a full pass | [13](../playbook/13-output-head.md) | #head #dense-ok |
| HD-04 | Sampling candidates found on the device (histogram top-K) instead of copying the vocabulary | ✅ | Inkling 2 Mini: sampling 100 → 110 tok/s, same tokens for a seed | | [13](../playbook/13-output-head.md) | #head #decode #dense-ok |

## Quants and pruning

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| QP-01 | Fewer bits → more experts in VRAM → faster | ✅ | QW IQ3_S 138-147 vs Q4_K_XL 102-110. HY 2 cards, short code: Q3 70 vs IQ2 126 | at a quality price | [09](../playbook/09-quants.md) | #quant #offload #decode |
| QP-02 | Hybrid quants (the most called experts more precise) | ◐ | HY, by call frequency: ~90 tok/s (short code), top-1 77.0%, KL 0.041 at 37% original calls, speed and quality on different inputs. DS, by memory tier: KL ~0.078 (calc, not run) | lands between the 2 quants | [09](../playbook/09-quants.md) | #quant #offload #quality-risk |
| QP-03 | Mixed bits inside an expert | ◐ | GLM: gate/up 2 + down 3 bits: 79% in VRAM, 69 tok/s, KL 0.096 | down is only ~17% more useful per unit of size | [09](../playbook/09-quants.md) | #quant #offload |
| QP-04 | REAP instead of frequency selection | ✅ as a selection | GLM: 19-22/25 vs 4-9/25 | frequency vs importance correlation -0.22 | [10](../playbook/10-pruning-and-merging.md) | #pruning |
| QP-05 | REAP vs fewer bits | ◐ | Laguna: lost to Q2 (119 vs 138). Qwen3.6 for code: beat Q2 (146 vs 121) | narrow task only | [10](../playbook/10-pruning-and-merging.md) | #pruning #quant |
| QP-06 | Pruning instead of offload | ❌ | pruned GLM 10.8 tok/s vs full 43 with offload (3 bits) and 79 in VRAM (2 bits), engines differ | saves memory, not decode time | [10](../playbook/10-pruning-and-merging.md) | #pruning #offload |
| QP-07 | Merging experts (REAM) | ❌ | 0.449 vs 0.146 (1 layer) | experts are almost orthogonal | [10](../playbook/10-pruning-and-merging.md) | #pruning |
| QP-08 | Masking experts outside VRAM | ❌ | KL 0.37 vs 0.047 | | [10](../playbook/10-pruning-and-merging.md) | #pruning #offload #quality-risk |
| QP-09 | Skip RAM experts with a small routing weight | ❌ | MM: top-1 98.9%, KL 0.0049, gate not passed | | [10](../playbook/10-pruning-and-merging.md) | #pruning #offload #quality-risk |
| QP-10 | Lower top-k | ❌ | MM top-3/top-2: NLL +46% up to +250% | | [10](../playbook/10-pruning-and-merging.md) | #pruning #quality-risk |
| QP-11 | Slice a dense FFN into an MoE without training | ❌ | Qwen3.8-27B: GSM8K 83% → 5% | | [10](../playbook/10-pruning-and-merging.md) | #pruning #dense-ok |
| QP-12 | Larger chunk without a new numeric check | ❌ as a silent change | HY 2,048 → 3,072: 1 of 33 top-1 changed, NLL 3.961 → 4.147 in the strict series | chunk size is a numeric change | [15](../playbook/15-quality-and-release-gates.md) | #prefill #quality-risk |
| QP-13 | Keep the NVFP4 codebook and use two signed-byte activation limbs for DP4A | ◐ | Inkling 2 Mini on 1 card: 120.29 tok/s for NVFP4 on a short request, 199/199 top tokens matched at fixed FP16 KV. The combined FP8 profile matched 781/795. Paired GSM8K with original short-prompt products gave 71/96 versus 73/96 for scalar experts | activation rounding remains, test the entire distribution on fixed histories | [09](../playbook/09-quants.md) | #kernel #decode |
| QP-14 | Decode NVFP4 nibbles directly to signed half values while preserving scalar FMA order | ✅ | Inkling 2 Mini on one RTX 3090, short new Python: paired runs 112.62-112.83 to 116.33-116.60 tok/s, byte-identical full logits at 795 fixed positions | check both the decode table and the reduction order when changing hardware | [09](../playbook/09-quants.md) | #kernel #decode #1gpu |
| QP-15 | NVFP4 experts on Ampere tensor cores: codes → FP16 in registers, warps split along K, 2 FP16 input limbs | ✅ | Inkling 2 Mini: 121.2 → 127.2 tok/s, 745 GB/s; lone warp 100 vs 118; GSM8K 74/96 vs 73 | median KL 5.7e-5; one FP16 input changes more tokens | [09](../playbook/09-quants.md) | #kernel #decode #in-vram #1gpu |
| QP-16 | Load ahead in the planar Q8 dense matvec | ❌ | Inkling 2 Mini: 0%, already 715 GB/s | it is at the memory limit | [09](../playbook/09-quants.md) | #kernel #decode #dense-ok |

## Agent work, sessions, loading

| ID | Hypothesis | Verdict | Numbers | Why, and when to revisit | Chapter | Tags |
|---|---|---|---|---|---|---|
| AG-01 | Save sessions to disk | ✅ | Strata 120K: 7.2 s vs 34.7 s | | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #kv #dense-ok |
| AG-02 | Incremental prompt prep on the server | ✅ | DS: turns -14.7% (with PF-04) | a regex walked the full prompt every turn | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #reread #dense-ok |
| AG-03 | Dialog snapshots in pinned RAM for A → B → A | ✅ | HY: TTFT 9.617 → 0.477 s, 26,624 of 27,839 tokens restored | | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #kv #dense-ok |
| AG-04 | Allocate snapshot memory before ready | ✅ | HY: all 128 blocks above 2,500 tok/s (lazy: 1 block at 2,314.12) | | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #kv #prefill |
| AG-05 | Priority for a short request during a long prefill | ✅ in a test | HY: TTFT 8.50 → 1.51 s | scheduling effect, check the long request too | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #batch #dense-ok |
| AG-06 | Warm the prefix while the person types | ✅ | Qwen3.5-122B: TTFT at 8K 4.63 → 0.16 s | | [11](../playbook/11-sessions-and-prompt-cache.md) | #agent #kv #dense-ok |
| AG-07 | Engram/n-gram tables in RAM instead of the page cache | ❌ | DS: 0-1.3%, +30-45 s of loading | | [12](../playbook/12-loading-and-big-tables.md) | #big-table #load |
| AG-08 | n-gram table on NVMe with direct reads | ✅ | QW: -27 GiB RAM for -3% prefill. HY: pinned 137.2 → 78.2 GiB, prefill 1,176.8 → 986.3 | small share of rows per request | [12](../playbook/12-loading-and-big-tables.md) | #big-table #ram #prefill |
| AG-09 | Load without dropping the page cache | ✅ | DS on 503 GiB: cold 80.8 → 66.8 s, repeat 47.4 s | on 251 GB the copy ran at 0.8 GB/s without the drop | [12](../playbook/12-loading-and-big-tables.md) | #load #ram |
| AG-10 | THP only for the duration of the copy | ✅ | DS: no hangs, decode +1.6-3.8% | | [12](../playbook/12-loading-and-big-tables.md) | #load #ram #cpu-lane |
| AG-11 | Request THP for expert storage with RAM full of file cache | ❌ | HY: loading over 10 minutes | cause not established on HY (on DS and Strata similar stalls were kernel compaction). Check cold start on the specific machine | [12](../playbook/12-loading-and-big-tables.md) | #load #ram |
| AG-12 | Experts on NVMe as a third tier | ◐ (ext) | 17.3 vs 28.2 tok/s on 1x3090 | costs ~40% of speed. We never needed it | [02](../playbook/02-serving-a-miss.md) | #offload #1gpu #ram |

## How to add a row

Use the next free number in the area. Keep one line per hypothesis, put the detail in the chapter. If a later run changes a verdict, edit the row and keep the old result in the chapter with the date. See [AGENTS.md](../AGENTS.md).
