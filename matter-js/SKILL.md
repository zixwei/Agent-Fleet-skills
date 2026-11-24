---
name: matter-js
description: Use when adding 2D rigid-body physics to a web page or game: gravity, collisions, constraints, ragdolls, soft bodies, or mouse interaction.
---
来源:https://github.com/liabru/matter-js (2026-11-24 ET 核实, stars 18,674, 语言 JavaScript, 许可证 MIT, 上游提交 b8ee71c, 默认分支 master). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是物理引擎,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md 与上游 wiki,再按下面流程动手.不要凭记忆编 API,示例以 wiki 的 Getting started 为准.

# matter.js

## 何时用

网页或小游戏要加 2D 刚体物理时:重力、碰撞、弹跳、绳索、布料、软体、推箱子、弹弓、牛顿摆、汽车、桥、链条、射线检测、鼠标拖拽物体.

## 安装

```bash
npm install matter-js
```

或直接引稳定版脚本:

```html
<script src="matter.js" type="text/javascript"></script>
```

## 核心用法

最小示例见上游 wiki [Getting started](https://github.com/liabru/matter-js/wiki/Getting-started),自写渲染循环见 [Running](https://github.com/liabru/matter-js/wiki/Running) 与 [Rendering](https://github.com/liabru/matter-js/wiki/Rendering).动手前先读这三页,不要凭记忆拼 Engine/Runner/Render 的接法.

能力覆盖 (README 演示清单):混合形状、凹多边形、SVG 路径、凹地形、复合体、牛顿摆、 wrecking ball、弹弓、圆角、视图、时间缩放、物体操纵、射线检测、精灵、金字塔、汽车、弹射器、反重力、桥、雪崩、基础软体、布料、事件、碰撞过滤、链条.

插件机制:引擎可扩展,见 wiki [Using plugins](https://github.com/liabru/matter-js/wiki/Using-plugins) / [Creating plugins](https://github.com/liabru/matter-js/wiki/Creating-plugins) / [List of plugins](https://github.com/liabru/matter-js/wiki/List-of-plugins).

## 适用场景

物理小游戏、交互动效、教学演示、碰撞模拟、拖拽拼图、粒子与布料效果.

## 注意事项

- API 文档见 https://brm.io/matter-js/docs/,教程见 wiki Tutorials,示例源码在上游 examples/ 目录 (另有 CodePen 合集).
- 打包器与框架默认配置会拖慢实时性能:Webpack 默认 sourcemap 影响大,Vue 的 watcher 影响大,见 README 的 Performance 一节与对应 issue.
- 上游 README 见同目录 README.md,许可证见 LICENSE.upstream.
