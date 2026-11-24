---
name: react-frontend-skills
description: Production-grade React frontend skills: React 19, Next.js 16, TypeScript 7, Tailwind v4, shadcn/ui, testing, data, UI, and architecture. Use the sub-skill SKILL.md for the task, and run the detect CLI to check dependency versions.
---
来源:https://github.com/PyModel/react-frontend-skills (2026-11-24 ET 核实, stars 45, 许可证 MIT, 上游提交 85f6b47). 帖子来源:https://x.com/awe0_io/status/2125162708991619193 (@awe0_io, 2026-11-24 ET).

> 本文件是包入口, 18 个子技能各在 skills/ 下, 各有自己的 SKILL.md, 用哪个读哪个.不要凭记忆编 API, 以子技能正文为准.

# React Frontend Skills

## 子技能 (上游 README 表, 共 18 个)

核心框架:

| 子技能 | 规则数 | 覆盖 |
|---|---|---|
| `skills/react` | 44 | React 19 并发渲染、Server Components、hook 优化 |
| `skills/nextjs` | 44 | Next.js 16 App Router、缓存、server components、路由 |
| `skills/typescript` | 44 | TypeScript 7 编译器配置、类型安全、异步模式 |

UI 与样式:

| 子技能 | 规则数 | 覆盖 |
|---|---|---|
| `skills/tailwind` | 42 | Tailwind CSS v4 优化、工具类模式、主题 |
| `skills/shadcn` | 44 | shadcn/ui (Radix 或 Base UI  primitives)、无障碍 |
| `skills/ui-design` | 42 | UI 与 UX 实践、无障碍、响应式设计 |
| `skills/web-design-guidelines` | pinned | 按 Vercel Web Interface Guidelines 审 UI 代码, 取自 pinned 上游提交 |

数据与状态:

| 子技能 | 规则数 | 覆盖 |
|---|---|---|
| `skills/tanstack-query` | 41 | 数据获取、缓存、mutation、乐观更新 |
| `skills/react-hook-form` | 45 | 表单校验、性能、field arrays |
| `skills/zod` | 46 | Schema 校验、类型推断、错误处理 |
| `skills/nuqs` | 42 | Next.js 的类型安全 URL query 状态 |

测试:

| 子技能 | 规则数 | 覆盖 |
|---|---|---|
| `skills/vitest` | 47 | Vitest 5 配置、mock、异步测试、worker pools |
| `skills/playwright` | 45 | 端到端测试、选择器、认证、CI |
| `skills/msw` | 51 | Mock Service Worker 做 API mock |
| `skills/tdd` | 42 | 测试驱动开发方法 |

架构与实践:

| 子技能 | 规则数 | 覆盖 |
|---|---|---|
| `skills/feature-arch` | 42 | Feature-based 架构、模块组织 |
| `skills/vercel-composition-patterns` | 8 | React 组合模式 |
| `skills/vercel-react-best-practices` | 70 | React 性能优化 |

## 版本基线 (上游 README, 2026-11-09 复核)

React 19.3 (react 19.3.1)、Next.js 16.4 (next 16.4.5)、TypeScript 7.0 (typescript 7.0.2)、Tailwind CSS 4.3 (tailwindcss 4.3.5)、Vitest 5.1 (vitest 5.1.0)、Playwright 1.65 (@playwright/test 1.65.0)、MSW 3.0 (msw 3.0.4)、TanStack Query 5.x (@tanstack/react-query 5.107.1)、React Hook Form 7.x (react-hook-form 7.91.0)、Zod 4.6 (zod 4.6.7)、nuqs 2.10 (nuqs 2.10.2).规则只教这些版本, 旧大版本的弃用 API、迁移指南已删掉.

## 安装

```bash
npx skills add PyModel/react-frontend-skills --all
npx skills add PyModel/react-frontend-skills -s react,nextjs,tailwind
npx skills add PyModel/react-frontend-skills --all -g
```

MCP 版:`@pymodel/react-frontend-skills-mcp`.

## 版本检查 (配套 CLI `detect`)

```bash
npx -y @pymodel/react-frontend-skills detect
```

报项目依赖里旧大版本、未知版本、范围不匹配.写代码前先跑它, 依赖版本不对先对齐, 不要照旧版本习惯写.

## 何时用

Codex / Claude Code / Cursor 等写 React 代码时:新项目搭框架、写组件、接表单校验、写测试、做性能优化、审 UI, 先读对应子技能.适用 Codex、Claude Code、Cursor、Pi、OpenCode、Kiro CLI.

## 文件

- 子技能正文见 skills/ 下各目录, 上游 README 见 README.md, 许可证见 LICENSE.upstream, 变更见 CHANGELOG.md.
- `mcp/package-lock.json` 已排除, 不入库.
