# Loading, page cache, THP and big tables on NVMe

Covers Qwen3.8-Flash-Next (Strata), GLM-5.3-Flash, DeepSeek V4.1 Flash (Maya), Hy4 Flash.

> **Read when:** the service starts slowly, or system RAM is the limit because of one big table. **Skip when:** loading and RAM are fine. **Ledger tags:** `#load` `#big-table` `#ram`.

## Loading

- **Source of the weights.** Strata read experts from a SATA SSD in 3.5-8 minutes, from NVMe in 45 s, from the page cache in 26-44 s. GLM from SATA 6-13 minutes vs 65-100 s from NVMe.
- **Dropping the page cache on load.** DeepSeek in Maya by default drops the page cache of the pack before copying to RAM. With 251 GB of RAM and no drop, the copy ran at 0.8 GB/s. On 503 GiB without the drop, a cold load became 66.8 s instead of 80.8 s, a repeat load 47.4 s. The price: the page cache holds 86 GB. RAM bandwidth after loading did not change (1 run).
- **THP and compaction.** With RAM full, a copy of ~96 GB of experts hung in kernel compaction. A Strata start once hung for 12 minutes. On Hy4, with RAM full of file cache, requesting transparent huge pages for the expert storage stretched loading to more than 10 minutes, and the mode was turned off. A working trick: disable THP for the process during the copy (`prctl PR_SET_THP_DISABLE`), or better, enable it only for the duration of the copy. On 503 GiB a copy of 175.8 GB with THP took 79 s, 99.6% of copies on 2 MB pages, decode +1.6-3.8%.
- **No swap means watch the neighbors.** Strata with ~57 GB in RAM plus a text encoder on the CPU and a music tagging job left 4 GB of free memory. A request of 33.6K took 262 s to read and the engine crashed.
- **mlock of large volumes** runs into `ulimit -l`.

## n-gram tables: RAM, page cache or NVMe

Several of these models carry a large n-gram embedding table next to the weights (Engram in DeepSeek and Hy4, PLE in Qwen Flash-Next). Per request only a small share of rows is read.

| Hypothesis | Result |
|---|---|
| Engram/n-gram tables in RAM instead of the page cache (DeepSeek) | no: 0-1.3%, and +30-45 s of loading |
| n-gram table on NVMe with direct reads (Qwen Flash-Next) | yes: -27 GiB of RAM for -3% prefill |
| Engram on NVMe with direct reads (Hy4 Flash) | yes as a separate mode, numbers below |

### Hy4 Flash: a 59 GiB table off pinned RAM

**Where the RAM went.** Engram has 660,006,416 rows of 96 bytes, 59.009 GiB in total. The loader pinned the full file mapping through `cudaHostRegister`. Moving the GGUF to another drive kept this cost. The experiment changed the access path: gather the needed rows through `O_DIRECT`, keep up to 64 requests in the queue, and give 104 MiB to a bounded cache of rows and keys. Experts stayed in RAM and VRAM as before.

| 1 card, original Q3, same task | Engram in RAM | Engram on NVMe |
|---|---:|---:|
| Pinned by the full API service | 137.2 GiB | 78.2 GiB |
| Checked cgroup limit without swap, cold start | 148 GiB | 88 GiB |
| Cold HTTP, 44,840 fresh tokens, prefill / decode | 1,176.8 / 29.4 | 986.3 / 30.5 |
| Tokens from the prefix cache on a repeat | 32,768 | 32,768 |
| Native input 261,888, prefill / decode near the end of the window | 1,385.3 / 33.1 | 1,097.5 / 20.9 |

**What came out.** The saving in pinned memory reproduced the size of the table to rounding. The read cost depended on the input and the depth. In a full native run of 261,888 tokens with generation the drive served about 3,321 MiB. With the expert weights replaced by IQ2, the combined service pinned 45.4 GiB and passed under a 56 GiB limit with a peak of 46.2 GiB. That last run started with a warm OS file cache. For Q3, file coldness was checked through `mincore`. The process limit, RSS, pinned memory and the installed system RAM answer different questions.

**How to check precision.** All 98,544 comparisons of read rows and 258 full logit vectors on 2 inputs matched byte for byte. In free generation on another input of 57,587 tokens only the first 273 IDs matched: between runs the number of experts in the temporary GPU cache could change. A separate variant with a mapped buffer and a fixed budget matched both checked HTTP answers, at 944.9 / 29.3 tok/s on the first. These are 3 independent checks: storage bytes, computation on a fixed history, and server behavior.

**How to transfer.** Fits a large table where the indices of the needed rows are known and a small share of the file is read per request. Record the count of unique rows, the physical bytes of the block device, hits, latencies at different queue depths and the peak RAM of the full service tree. In an A/B fix separately the OS file cache, the application row cache, the random seed and the GPU buffer sizes. A large matrix that every step reads in full needs a different cost calculation.

## Transfer checklist

1. Load from NVMe or a warm page cache. A SATA source can cost 10 minutes per start.
2. If RAM is nearly full, expect THP compaction stalls on big copies. Scope THP to the copy.
3. With no swap, budget RAM for every neighbor process before a heavy run.
4. For any large table: how many unique rows per request? If it is a small share, move it off pinned RAM.
5. Report RAM with the right number: pinned, RSS, cgroup limit and page cache are 4 different things.
