---
name: cinematic-skill
description: Make narrated cinematic films entirely from code, on any subject. One continuous camera over photos, documents, cards, maps and big numbers, timed to the voice. Read skills/cinematic-film/SKILL.md for the full method.
---
来源:https://github.com/Moh4696/cinematic-skill (2026-11-24 ET 核实, stars 952, 许可证 MIT, 上游提交 bf43556). 帖子来源:https://x.com/nexai_sbus/status/2125037585580122213 (@nexai_sbus, 2026-11-23 ET).

> 本文件是包入口, 技能正文在 skills/cinematic-film/SKILL.md, 用的时候读它.不要凭记忆编流程, 以正文为准.

# cinematic-skill

纯代码做有旁白的电影感影片, 任何题材都行:一个连续镜头滑过一个设计好的世界, 照片、文件、卡片、地图、路线、大数字, 每个图章、高亮、数字都落在旁白念到那个词的时刻, 再加音效、可选配乐、烧录字幕.适用 Claude Code、Codex、Cursor、Gemini CLI.

## 观感 (上游原文)

- 纪录片 / 历史 / 真实犯罪 = Cork evidence wall, slate.
- 产品发布 = Studio, charcoal.
- 科学 = Blueprint, paper.
- 科技 / 太空 / 预告片 = Night.

元素:照片与撕边报纸剪报、带真实引用的排版文件、干净卡片、大号做旧字体与滚动计数、印章、钢笔圈与荧光笔划线、照片之间的红线、地图.聚光灯跟着镜头走, 一行旁白一个主角.

## 关键事实 (上游说法)

- 92 秒成片, 16GB 笔记本上 1080p 渲染约 6 分钟.
- 全代码 (Python Skia), 每次渲染结果一致, 换个声音整片自动重定时.
- 规则来自实拍一部 5:22 的 Al Capone 纪录片时踩过的坑.

## 何时用

"make a cinematic video about"、纪录片、解说、发布片、Vox 风格视频、证据板动画, 或把故事转成 X / YouTube / TikTok 视频时.历史 / 真实犯罪首选.

## 文件

- 技能正文见 skills/cinematic-film/, 上游 README 见 README.md, 许可证见 LICENSE.upstream, 安装见 install.sh 与 requirements.txt, 自检见 tests/check.mjs.
- examples/ 下 3 张 webp 预览图是二进制, 未入库.
- `.claude-plugin/` 已排除, 不入库.
