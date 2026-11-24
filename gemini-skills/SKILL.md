---
name: gemini-skills
description: Gemini API skills: build apps with the Gemini API, call it from the shell with gemini-api, build Live API realtime apps, or make Omni Flash video. Read the sub-skill SKILL.md for the task.
---
来源:https://github.com/google-gemini/gemini-skills (2026-11-24 ET 核实, stars 4,673, 许可证 Apache-2.0, 上游提交 c4d0c62). 帖子来源:https://x.com/0xjackkk/status/2125052495596859770 (@0xjackkk, 2026-11-23 ET).

> 本文件是包入口, 4 个子技能各在 skills/ 下, 各有自己的 SKILL.md, 用哪个读哪个.不要凭记忆编 API, 以子技能正文与官方文档为准.

# Gemini API Skills

Google 官方的 Gemini API 技能库, 补模型知识缺口用.上游实测说法:加上技能后, Agent 按最佳实践写出正确 API 代码的比例, Gemini 3 Flash 为 87%, Gemini 3.1 Pro 为 96%(上游博客说法, 未独立复核).

## 子技能 (上游 README 表)

| 子技能 | 用途 |
|---|---|
| `skills/gemini-api-dev` | 用 Gemini API (Interactions API 与 Decisions API) 做应用:文本生成、多轮对话、流式、函数调用、结构化输出、校准决策 (`gemini-decision-1`)、生图、语音生成 (TTS)、Voice Design、Voice Replication、managed agents、Deep Research、弃用模型护栏, Python 与 TypeScript 双 SDK. |
| `skills/gemini-api-cli` | 用 shell 命令 `gemini-api` 调 Gemini API:文本与媒体生成 (`image`、`tts`、`music`、`video`)、文件与 YouTube 分析、转录、Deep Research、Antigravity 沙箱、`--jq` / `--body` 脚本.用它之前先装 CLI, 见库里 `gemini-api-cli/` 包装技能. |
| `skills/gemini-live-api-dev` | 用 Gemini Live API 做实时双向流式应用:WebSocket 音视频/文本流、语音活动检测、原生音频、函数调用、会话管理. |
| `skills/gemini-omni-flash-api` | 专攻 Gemini Omni 1.2 Flash (`gemini-omni-1.2-flash`) 的视频技能:视频编辑、图文参考生成、首尾帧过渡、视频延长 (每轮 3-15 秒, 总计最长 40 秒), 走 Interactions API. |

注意:`vertex-ai-api-dev` 已搬出本仓库, 现在 https://github.com/google/skills/tree/main/skills/cloud/gemini-api, 不在本包里.

## 何时用

- 做 Gemini 应用、调 Gemini API、接 Live API、做 Omni 视频时, 先读对应子技能.
- 库里已有 `gemini` 目录是另一回事 (awesome-muse-connectors 的 Gemini 媒体生成 connector 包装), 与本包不冲突.

## 文件

- 子技能正文见 skills/ 下各目录, 上游 README 见 README.md, 许可证见 LICENSE.upstream.
- `.claude-plugin/` 已排除, 不入库.
