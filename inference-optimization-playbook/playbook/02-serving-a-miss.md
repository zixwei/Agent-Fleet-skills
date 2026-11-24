# Memory tiers and where to serve a miss

Covers DeepSeek V4.1 Flash, GLM-5.3-Flash, MiniMax-M3.1-Flash, Hy4 Flash.

> **Read when:** decode has misses and you choose between computing them on the CPU and copying them over PCIe. **Skip when:** every expert is in VRAM. **Ledger tags:** `#offload` `#cpu-lane` `#pcie`.

## Typical layout

| Tier | What is there | How it is used |
|---|---|---|
| VRAM | dense part of the model, KV, buffers, drafter, **hot experts** | computed on the GPU |
| Pinned RAM | **all other experts**, on 2 MB pages when possible | decode: the CPU computes the expert in place. Prefill: DMA into VRAM |
| Page cache / NVMe | n-gram tables (Engram, PLE), sometimes a cold tail of experts | read row by row |
| Disk | session files, profiles | off the hot path |

Example (DeepSeek Maya-S, live service): 7,392 of 15,744 experts in VRAM (about 20 GB per card for experts), 92.7 GB of copies in pinned RAM, 161 GB of Engram from the page cache. Expert files are not read from disk after loading: on a live session of the UD quant, 299 returns of borrowed experts read nothing from files.

We never used disk as a tier for experts. Everything fit in RAM. A third-party project, glm53-flash-offload, kept 3 tiers (VRAM / RAM / NVMe) on one 3090 and got 17.3 tok/s with part of the experts on NVMe vs 28.2 tok/s with everything in RAM, on a host with 8 channels and PCIe 4.0 (ext). So NVMe as an expert tier is possible but costs about 40% of speed.

## A miss in decode: CPU or PCIe

This is the main design choice of an offloading engine. Numbers on our rig:

| Path | Cost of one miss | Where |
|---|---|---|
| CPU computes the expert in RAM (AVX2) | 0.42-0.46 ms per DeepSeek expert (~11 MB, after the PDEP fix). 0.034-0.041 ms per MB | DeepSeek, Maya (2 and 8 channels) |
| CPU, GGUF experts of GLM | 0.8 ms in practice on 2 channels (idle calibration promised 0.14-0.18 ms) | GLM Maya-M, Maya |
| PCIe 3.0, copy the expert to VRAM | ~0.75 ms for 9.4 MB (GLM 3.05). 1.4-1.5 ms for ~18 MB (MiniMax) | GLM, MiniMax |
| One full decode layer, for comparison | 0.4-0.7 ms | MiniMax |

The rows come from different models, engines and RAM configs. Read them as orders of magnitude.

What follows:

- **If a PCIe miss costs more than a layer, streaming over the bus loses in decode.** A MiniMax expert takes 1.4 ms to copy and the full layer takes 0.4-0.7 ms. Streaming misses over PCIe was worse and was removed (already on 2-channel RAM).
- **On DeepSeek, turning off the PCIe lane in decode gave +17%** (flag `MAYA_DS4_STREAM=0`, also 2 channels). Once the CPU lane got fast, the stream only slowed things down: the kernel waited for the host on every layer.
- **It does not always go this way.** GLM in Maya on the same 2 channels: stock sent cold experts to the CPU and got 24.9 tok/s. After a calibration that sent 75% of misses to PCIe: 45-46 tok/s. There the CPU expert under load cost more than the PCIe copy.
- **Bad CPU code breaks the calculation.** On Zen 2, a ggml build with `-march=native` took a branch with the PDEP instruction, which runs in microcode on that CPU generation. One cold expert cost 1.6 ms instead of 0.44. The fix (build flag `GGML_IQ1_NO_PDEP`) made the CPU lane 3.6x faster. It is now upstream in Maya and Strata ("EPYC 7002 up to 3x"). Before you decide "CPU or PCIe", time the real cost of one expert on the CPU in your build.
- **Idle calibration is wrong under load.** Maya's planner for GLM timed a CPU expert at idle (0.6 ms). Under load it was 1.2-4.2 ms. The planner predicted 114 ms per token, the real number was 42-49. Calibrate on the real load.

The PCIe copy into a VRAM cache also worked in one more place: our exllamav3 fork for GLM in TP mode ([05](05-multi-gpu.md)). A cold expert is copied into a VRAM cache on access, with CLOCK eviction. TP4 without the cache (cold experts read straight from RAM) gave 19.8 tok/s, with the cache at 94-95% hits 37.6-38.7 (stock in layer split with a CPU tail was 11.5). Also on 2-channel RAM.

## Cold experts on the CPU in parallel with hot ones on the GPU (Hy4 Flash)

With partial placement the GPU can read misses from RAM over PCIe by itself. On Hy4 Flash after a 57K prompt, the early 2-GPU build gave about 20 tok/s decode this way.

Computing cold experts on the CPU in parallel with the hot ones on the GPU: 2 GPUs, original quant, short code answer of 600 tokens, **24.6 → 70.4 tok/s**. The set of active experts stays the same.

To check transfer: time the slower of the 2 executors, the load on the RAM channels, and the cost of merging the partial results.

The same idea ported into upstream Maya v1.1.69 let tensor parallel keep working when not all experts fit in VRAM ([05](05-multi-gpu.md), [mw00/project-maya#1068](https://github.com/mw00/project-maya/pull/1068)).

## What "serve the miss cheaper" is worth

The miss cost model tells you where to invest. For GLM 4.05 on our rig (`9.5 ms + 866 MB / bandwidth`), 55+ tok/s needs no more than ~520 MB of RAM traffic per token, that is -40% misses. RAM bandwidth alone (8 channels instead of 4) cannot get there (calc, GLM on 8 channels was not run). It needs more experts in VRAM (fewer bits) or a different quant.

## Prefill is the opposite case

In prefill almost every expert of a layer is needed on every chunk, so cold experts go to the GPU by DMA from pinned RAM. See [07](07-prefill.md).

## Transfer checklist

1. Time one cold expert on the CPU in your build, under real load, at your quant.
2. Time one expert copy over your PCIe generation, with the real expert size.
3. Time one full decode layer.
4. If copy > layer, compute misses on the CPU. If CPU expert under load > copy, stream over PCIe. Test both, the answer flipped between models on the same hardware.
5. Check the CPU code path for microcoded instructions on your CPU generation (PDEP/PEXT on Zen 1 and Zen 2).
