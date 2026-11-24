---
name: stickman-skill
description: Make cinematic stickman story videos as MP4 from code, with a free built-in AI voiceover (no ElevenLabs needed). Use for stickman animation requests. Read skills/stickman/SKILL.md for the full method.
---
来源:https://github.com/krishnagoswami52/stickman-skill (2026-11-24 ET 核实, stars 27, 许可证 MIT, 上游提交 d3f9321). 帖子来源:https://x.com/nexai_sbus/status/2125037585580122213 (@nexai_sbus, 2026-11-23 ET).

> 本文件是包入口, 技能正文在 skills/stickman/SKILL.md, 用的时候读它.不要凭记忆编流程, 以正文为准.

# stickman-skill

纯代码渲染电影感火柴人故事视频, 输出 MP4, 自带免费开源配音 (不用 ElevenLabs).2D / 3D 同一套观感, 平涂色彩, 落脚走路, 逐帧审计, Kokoro 语音.

## 技能正文

`skills/stickman/SKILL.md` (上游原文, name: stickman):给一个话题出成片, 全代码渲染, 扁平 2D 角色、黄昏配色、电影式运镜、真实音效.产出 4 样:视频、配音稿、YouTube 标题、缩略图提示词.

## 何时用

用户要火柴人动画、火柴人故事视频、2D/3D 打草稿时.免费, 不花模型额度.

## 文件

- 技能正文见 skills/stickman/, 上游 README 见 README.md, 许可证见 LICENSE.upstream (子技能目录里还有一份 LICENSE, 原样保留).
- tools/make_zip.py 已入库 (文本代码).stickman.zip 是二进制, 未入库.
- `.claude-plugin/` 已排除, 不入库.
