---
name: musicgen
description: Use when generating music or sound from text (or text + melody) with MusicGen: BGM, jingles, or sound beds for video.
---
来源:https://github.com/facebookresearch/audiocraft (MusicGen, 2026-11-24 ET 核实, stars 23,951, 语言 Python, 许可证 MIT, 上游提交 896ec7c). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是音频生成库,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md 与 MUSICGEN.md,再按下面流程动手.不要凭记忆编 API,示例以 docs/MUSICGEN.md 为准.

# MusicGen (AudioCraft)

## 何时用

用文字 (或文字 + 旋律) 生成音乐时:视频 BGM、片头音乐、音效床、广告配乐.同库还有 AudioGen (文字生成音效) 与 EnCodec (音频编解码).

## 重要:非商用

帖子明确说明:MusicGen 模型仅限非商用 (non-commercial only).商用前必须先核对模型卡 model_cards/MUSICGEN_MODEL_CARD.md 与授权条款,不要默认可以商用.仓库代码本身是 MIT,不等于模型权重可以商用.

## 安装

```bash
# Python 3.9, PyTorch 2.1.0; 先装 torch, 再装 audiocraft
python -m pip install 'torch==2.1.0'
python -m pip install setuptools wheel
python -m pip install -U audiocraft
# 另需 ffmpeg
sudo apt-get install ffmpeg
```

推理要 GPU, 中等模型 (~1.5B 参数) 建议 16GB 显存, 小模型可跑短片段.

## 模型 (docs/MUSICGEN.md)

- `facebook/musicgen-small`:300M, 纯文字生成音乐.
- `facebook/musicgen-medium`:1.5B, 纯文字生成音乐.
- `facebook/musicgen-melody`:1.5B, 文字 + 旋律生成音乐.
- `facebook/musicgen-large`:3.3B, 纯文字生成音乐.
- `facebook/musicgen-melody-large`:3.3B, 文字 + 旋律生成音乐.
- `facebook/musicgen-stereo-*`:以上模型的立体声微调版.
- 性价比首选 `facebook/musicgen-medium` 或 `facebook/musicgen-melody`.

## 核心用法 (docs/MUSICGEN.md 原例)

```python
import torchaudio
from audiocraft.models import MusicGen
from audiocraft.data.audio import audio_write

model = MusicGen.get_pretrained('facebook/musicgen-melody')
model.set_generation_params(duration=8)  # generate 8 seconds.
wav = model.generate_unconditional(4)    # generates 4 unconditional audio samples
descriptions = ['happy rock', 'energetic EDM', 'sad jazz']
wav = model.generate(descriptions)  # generates 3 samples.

melody, sr = torchaudio.load('./assets/bach.mp3')
# generates using the melody from the given audio and the provided descriptions.
wav = model.generate_with_chroma(descriptions, melody[None].expand(3, -1, -1), sr)

for idx, one_wav in enumerate(wav):
    # Will save under {idx}.wav, with loudness normalization at -14 db LUFS.
    audio_write(f'{idx}', one_wav.cpu(), model.sample_rate, strategy="loudness", loudness_compressor=True)
```

其他入口:Hugging Face Space `facebook/MusicGen` 在线试, 本地 gradio 演示 `python -m demos.musicgen_app --share`, Colab 演示见 MUSICGEN.md.

## 适用场景

短视频 BGM、播客片头、游戏配乐草稿、广告音乐、旋律引导的改编.

## 注意事项

- 必须有 GPU 才能本地跑, 显存不够先用 small 模型或缩短时长.
- 生成的是音频文件, 混音、剪辑仍要靠 FFmpeg 或音频软件.
- 上游 README 见同目录 README.md, MusicGen 文档见同目录 MUSICGEN.md, 许可证见 LICENSE.upstream.
