---
name: motion-starter-kit
description: Build motion-graphics videos in Remotion from a reference video, or animate a logo in 17 styles. Read the sub-skill SKILL.md in .agents/skills/ for the task.
---
来源:https://github.com/motalim-ai/motion-starter-kit (2026-11-24 ET 核实, stars 59, 许可证 MIT, 上游提交 4f71f89). 帖子来源:https://x.com/nexai_sbus/status/2125037585580122213 (@nexai_sbus, 2026-11-23 ET).

> 本文件是包入口, 2 个子技能在 .agents/skills/ 下, 各有自己的 SKILL.md, 用哪个读哪个.上游仓库以阿拉伯语为主, 技能正文含阿拉伯语.不要凭记忆编流程, 以正文为准.两个子技能都要求:先在 Remotion Studio 里审过, 用户点头后再渲染.

# motion-starter-kit

照参考视频复刻动效, 或给 Logo 做动画, 全套 Remotion.

## 子技能

| 子技能 | 用途 |
|---|---|
| `.agents/skills/motion-from-reference` | 照参考视频做新的 motion-graphics 视频:在 styles/ 库里点风格 (plotter、doodle、whiteboard、chalkboard、stickman、isometric、fake3d、infographic、map-journey、product), 或把视频放进 reference/, 研究参考、一轮提问、写脚本与配音时间轴、搭场景、Studio 里审、用户点头后再渲染. |
| `.agents/skills/logo-animation` | 把用户自己的 Logo 在 Remotion 里做成 17 种风格的动画 (line draw、handwriting、assembly、morph、geometric、liquid fill、mask wipe、light sweep、kinetic typing、elastic、character、3D flip、particles、glitch、neon、实拍 mockup 品牌主视觉、设计工具语言的品牌片), 合成音效, 背景纯色或透明 (alpha MOV/WebM). |

## 风格库

- styles/ 下 10 种现成风格 (README 表):plotter、doodle、whiteboard、chalkboard、stickman、isometric、fake3d、infographic、map-journey、product.图库文字另写 26 种风格分 6 类, 两个数字上游都写了, 以 styles/ 实际目录为准.
- gallery/logo/ 是 Logo 动画风格图库 (17 种).
- logo/ 与 video/ 是 Remotion 示例工程源码 (代码已入库, 按 batch 7 "代码也收" 办).

## 何时用

用户给参考视频要复刻动效, 或要 Logo 动画、Logo 揭幕、Logo 片头时.适用 Claude Code、Codex、Antigravity.

## 文件

- 子技能正文见 .agents/skills/ 下各目录, 风格库见 styles/, 示例工程见 logo/ 与 video/, 上游 README 见 README.md, 许可证见 LICENSE.upstream.
- 二进制媒体 (预览图、参考视频帧) 未入库, 看效果去上游.
