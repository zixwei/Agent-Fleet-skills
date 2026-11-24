# Situation card

Fill this before you read chapters or propose anything. The models in this playbook behaved differently: a trick that gave +44% on one gave nothing on another. The card tells you which results apply to the case in front of you.

The last line of the card is a tag line. Use it to filter the [ledger](../ledger/hypotheses.md) and to pick the closest [case](../cases/README.md).

Write "unknown" where you do not know. An unknown about the model, the hardware or the engine is the first thing to find out, and the right column says how.

## The model

| Field | Value | How to get it cheaply |
|---|---|---|
| Name, revision, total size at the chosen quant | | model card, file sizes |
| Dense or MoE | | config |
| MoE: layers × experts per layer, top-k, shared expert | | config, tensor names |
| Bytes per expert at this quant | | tensor sizes in the file, not parameters × bits |
| Dense part: attention, shared experts, head (bytes) | | tensor sizes |
| Side tables (n-gram embeddings and similar), size | | tensor sizes |
| Built-in drafter (MTP) or a separate one | | config, engine docs |
| Attention kinds (full, sliding, sparse, linear, hybrid) | | config |
| Vocabulary size | | tokenizer |

## The hardware

| Field | Value | How to get it cheaply |
|---|---|---|
| GPUs: count, VRAM each, generation | | `nvidia-smi` |
| Link per card: PCIe generation and width, host-to-device GB/s | | a pinned-memory copy test, per card and all cards at once |
| P2P between cards | | a P2P copy test |
| CPU: model, cores, AVX2 / AVX-512 | | `lscpu` |
| RAM: size, channels populated, NUMA nodes | | `dmidecode`, `numactl -H` |
| RAM bandwidth | | STREAM Triad by thread count |
| Storage that holds the weights | | NVMe or SATA, read speed |
| Power limit per card, swap, other jobs on the box | | `nvidia-smi`, `free`, process list |

## The engine and the placement

| Field | Value | How to get it cheaply |
|---|---|---|
| Engine, revision, full launch arguments | | |
| Context window, KV precision, prefill chunk or block | | launch arguments, startup log |
| Multi-GPU mode: layer split, TP, EP | | startup log |
| Experts in VRAM per layer (and in total) | | startup log |
| Who serves a miss: CPU, PCIe copy, disk | | engine docs, log |
| Expert cache: static profile, live cache, both | | launch arguments |

## The workload and the target

| Field | Value |
|---|---|
| 1 request at a time or N concurrent | |
| Agent sessions, chat, batch jobs | |
| Typical input and output length, depth of context | |
| Languages, content type, sampling mode | |
| What the user feels as slow: decode, first read, re-read, load, pauses inside an answer | |
| Target number, and which one: mean, worst answer, worst window | |
| Allowed quality change: bit-exact, a stated tolerance, behavior only | |

## Current numbers

Take them through the normal entry point (the API), on free generation and on real content. [Experiment rules](experiment-rules.md) say how.

| Metric | Value | Conditions |
|---|---|---|
| Decode tok/s at short and at deep context | | |
| Prefill tok/s on a cold input, by length | | |
| Re-read of a typical turn, s | | |
| TTFT at the client | | |
| Load time | | |
| Hit rate in decode (share of picks served from VRAM) | | |
| Share of token time spent on misses | | from a fit: [tools/fit_cost.py](../tools/fit_cost.py) |

## Derived answers

| Question | Answer | How |
|---|---|---|
| Does the model fit in VRAM at a quant you accept? | | sizes of the model vs VRAM minus KV, buffers and drafter |
| Is routing skewed? | | [tools/route_sim.py](../tools/route_sim.py) on a router trace: static vs random at your budget |
| Which term of token time is larger: constant or misses? | | the fit from the current numbers |
| What costs less per miss: a CPU expert, a PCIe copy? | | time 1 expert each way and 1 full layer |
| What is the ceiling of the metric you are asked to raise? | | [01 Cost model](../playbook/01-cost-model.md), section on ceilings |

## The tag line

Pick the tags that describe your case. The vocabulary is in the [ledger](../ledger/hypotheses.md#tags).

```
#offload or #in-vram
#strong-skew or #weak-skew            (MoE only, from the derived answers)
#cpu-lane or #pcie                    (who serves a miss)
#1gpu or #multi-gpu, then #layer-split / #tp / #ep
#drafter  #big-table  #agent  #long-context  #batch      (what is present)
#zen2  #pcie3  #numa                  (hardware traits that changed results here)
```

Example: an MoE with part of the experts in RAM on 2 cards in tensor parallel, a CPU lane, a built-in drafter and a big n-gram table, used by a coding agent on a Zen 2 host with PCIe 3.0:

```
#offload #cpu-lane #multi-gpu #tp #drafter #big-table #agent #long-context #pcie3 #zen2
```

Then:

```bash
grep "#offload" ledger/hypotheses.md | grep "#tp"
grep "#agent" ledger/hypotheses.md
```

Next step: [the map](../playbook/00-start-here.md).
