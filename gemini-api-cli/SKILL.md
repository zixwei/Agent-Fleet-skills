---
name: gemini-api-cli
description: Use when an agent needs Gemini multimodal abilities from the shell: generate or edit images, audio, music, video, transcribe recordings, analyze files, or run a managed agent. Needs a Gemini API key.
---
来源:https://github.com/google-gemini/gemini-api-cli (2026-11-24 ET 核实, stars 1,664, 语言 Go, 许可证 Apache-2.0, 上游提交 52636d1). 帖子来源:https://x.com/0xjackkk/status/2125052495596859770 (@0xjackkk, 2026-11-23 ET).

> 注意:上游仓库本身是 CLI 工具,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编模型名,模型以 `models list` 为准;示例以 README 为准.

# Gemini API CLI (`gemini-api`)

## 何时用

给已有 Agent (Claude Code, Codex, Antigravity, OpenClaw 等任何能跑 shell 的 Agent) 接 Gemini 多模态能力时:生成/改图、配音、音乐、视频、转录、分析 PDF/图片/音视频、查模型、挂后台 managed agent 跑长任务.帖子实测说法:文件直接落盘、终端只回一个文件路径,省 token 约 85.8%、快约 2 倍(帖子说法,未独立复核).

## 安装

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/google-gemini/gemini-api-cli/main/scripts/install.sh | bash
# Go 安装
go install github.com/google-gemini/gemini-api-cli/cmd/gemini-api@latest
# 或去 GitHub Releases 下预编译包 (macOS / Linux / Windows)
```

## 配 API Key

```bash
export GEMINI_API_KEY=<redacted>
# 持久化进系统钥匙串或 ~/.config/gemini-api/config.yaml
gemini-api configure --api-key "YOUR_API_KEY"
gemini-api whoami   # 查当前配置与凭证来源
```

## 核心命令

| 类别 | 命令 | 用途 (默认模型见 README 命令总表) |
|---|---|---|
| 生成 | `gemini-api generate` | 文本与多模态生成 |
| 生成 | `gemini-api image` | 生成或改图, `--out` 指定落盘目录 |
| 生成 | `gemini-api tts` | 配音, 支持多说话人 |
| 生成 | `gemini-api music` | 生成音乐 |
| 生成 | `gemini-api video` | 生成/改视频, 支持 `--async` |
| 理解 | `gemini-api analyze -i 文件` | 问 PDF、图片、音频、视频、CSV、YouTube 链接 |
| 理解 | `gemini-api transcribe -i 录音 --format srt` | 转录, 格式 md / text / json / srt |
| 管理 | `gemini-api agent run / status / cancel` | 后台 managed agent, 拿 interaction ID 再查结果 |
| 管理 | `gemini-api files upload / list / get / delete` | Files API, 文件保留 48 小时 |
| 管理 | `gemini-api models list --all` | 查该 Key 能用的全部 live 模型 |
| 管理 | `gemini-api voices create / list / get / delete` | 自定义声音 |
| 管理 | `gemini-api configure / auth / whoami` | 凭证与配置 |

```bash
gemini-api image "A friendly robot mascot for a developer tool" --out ./assets/
gemini-api generate "Hello from my agent" --dry-run   # 先 dry-run 校验, 不发请求、不读钥匙串
gemini-api generate "Hello from my agent"
```

## 给 Agent 的接入指令 (按 README Quick start)

```text
You have access to gemini-api through the shell.
Use it to call Gemini models, generate or edit media, analyze files, or launch managed agents.
1. Inspect --help or --usage to choose a command, and --schema when building a --body JSON payload.
2. Preview API calls with --dry-run (or --dry-run --output-format json).
3. For discrete JSON output or --jq filtering on streaming commands, pass --stream=false --output-format json.
4. Capture returned file paths (from media commands) and interaction IDs (from agent/model runs) to continue multi-step tasks.
```

## 关键机制

- 媒体命令把文件写进硬盘,终端只返回文件路径,不把 Base64 塞进上下文.
- `--schema` 看请求结构,`--usage` 看机器可读用法,`--dry-run` 只打印解析后的请求 (凭证已脱敏),不发网.
- 输出格式 `--output-format json` 或紧凑 TOON,`--jq` 内联过滤字段.
- 每次调用无状态,上下文由调用方 Agent 自己给.

## 注意事项

- API Key 不要写进聊天、帖子、GitHub、Notion,只走环境变量或 `configure` 进钥匙串.
- 模型名会变,下单前先 `models list --all` 核实,不要凭记忆写模型名.
- 生成类默认模型见 README 命令总表,上游改版可能换默认模型.
- 上游不接受 PR (见 README),有问题提 Issue.
- 上游 README 见同目录 README.md,许可证见 LICENSE.upstream.
