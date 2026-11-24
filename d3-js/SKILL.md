---
name: d3-js
description: Use when building custom data visualizations in the browser: charts, maps, graphs, or data-driven DOM updates with full control.
---
来源:https://github.com/d3/d3 (2026-11-24 ET 核实, stars 113,999, 语言 JavaScript, 许可证 ISC, 上游提交 7b634fc, 版本 v7.9.2). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是可视化库集合,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编 API,示例以 https://d3js.org 文档与 Observable 图库为准.

# D3.js

## 何时用

浏览器里要做定制数据可视化时:折线/柱状/散点图、地图、力导向图、层级图、时间线、刷选联动.要完全掌控每一像素、做数据驱动的 DOM 增删改时.不要拿它当开箱即用的图表组件库,那是高层图表库的活.

## 安装

```bash
npm install d3          # 全量, 约 30 个模块
npm install d3-selection d3-scale d3-shape   # 只装要用的模块
```

## 核心模块 (v7.9.2, package.json 核实, 共 30 个)

- 数据连接:d3-selection (enter / update / exit 是 D3 的核心机制)、d3-array、d3-dsv、d3-fetch.
- 比例尺与坐标:d3-scale、d3-scale-chromatic、d3-axis、d3-time、d3-time-format、d3-format.
- 图形:d3-shape (线、面、弧、饼)、d3-path、d3-polygon、d3-contour、d3-geo (地图)、d3-hierarchy (树/打包/分区)、d3-chord、d3-force (力导向)、d3-delaunay、d3-quadtree.
- 交互与动画:d3-transition、d3-ease、d3-timer、d3-drag、d3-zoom、d3-brush、d3-dispatch、d3-interpolate、d3-color、d3-random.

## 核心用法 (思路, 不是现成代码)

1. 选数据, 用 d3-scale 把数据域映射到像素域.
2. 用 d3-selection 的 data join (enter/update/exit) 把数据绑到 DOM 元素.
3. 用 d3-shape / d3-path 生成路径, 用 d3-axis 画坐标轴.
4. 变化时用 d3-transition 做过渡, 不要直接改属性跳变.
5. 具体 API 签名先查 https://d3js.org 对应模块页, 不要凭记忆写.

## 适用场景

定制图表、数据新闻、仪表盘、地图可视化、关系图谱、交互式数据故事.

## 注意事项

- D3 是底层库, 画一张图要自己写比例尺、坐标轴、图例, 工作量比图表组件库大, 换来的是完全自由.
- 文档在 https://d3js.org, 示例在 https://observablehq.com/@d3/gallery.
- 上游 README 见同目录 README.md (另附 d3-selection 与 d3-scale 的 README 节选), 许可证见 LICENSE.upstream.
