---
name: pixi-js
description: Use when building 2D web graphics, games, ads, or interactive canvases with WebGL/WebGPU: sprites, textures, filters, masks, text.
---
来源:https://github.com/pixijs/pixijs (2026-11-24 ET 核实, stars 48,886, 语言 TypeScript, 许可证 MIT, 上游提交 16011d7, 默认分支 dev). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是 2D 渲染库,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编 API,示例以 README 为准,完整指南见 https://pixijs.com/8.x/guides, API 文档见 https://pixijs.download/release/docs/index.html.

# PixiJS

## 何时用

网页上要做 2D 交互图形时:小游戏、广告、交互画布、数据可视化动效、精灵动画.要 WebGL / WebGPU 渲染、60fps、贴图、滤镜、遮罩、文字时.

## 安装

```bash
npm create pixi.js@latest   # 脚手架新项目
npm install pixi.js         # 加进已有项目
```

## 核心用法 (README 原例, PixiJS v8)

```typescript
import { Application, Assets, Sprite } from 'pixi.js';

(async () =>
{
    // Create a new application
    const app = new Application();

    // Initialize the application
    await app.init({ background: '#1099bb', resizeTo: window });

    // Append the application canvas to the document body
    document.body.appendChild(app.canvas);

    // Load the bunny texture
    const texture = await Assets.load('https://pixijs.com/assets/bunny.png');

    // Create a bunny Sprite
    const bunny = new Sprite(texture);

    // Center the sprite's anchor point
    bunny.anchor.set(0.5);

    // Move the sprite to the center of the screen
    bunny.x = app.screen.width / 2;
    bunny.y = app.screen.height / 2;

    app.stage.addChild(bunny);

    // Listen for animate update
    app.ticker.add((time) =>
    {
        // Just for fun, let's rotate mr rabbit a little.
        // * Delta is 1 if running at 100% performance *
        // * Creates frame-independent transformation *
        bunny.rotation += 0.1 * time.deltaTime;
    });
})();
```

## 关键能力 (README)

- WebGL 与 WebGPU 双渲染器,性能优先,体积轻.
- 资源加载器 Assets,动态贴图,遮罩,滤镜,高级混合模式.
- 鼠标与多点触控全支持,灵活的文字渲染,图元与 SVG 绘制.
- TypeScript 支持.

## 适用场景

H5 小游戏、交互广告、数据动效、在线画板、精灵动画、粒子与滤镜特效.

## 注意事项

- v8 写法与旧版不同 (`new Application()` 后 `await app.init(...)`,再 `app.canvas`),不要照 v7 旧代码写.
- 贴图先 `Assets.load` 再建 Sprite,不要直接 new Texture.
- 动画用 `app.ticker`,用 `time.deltaTime` 做帧率无关位移.
- 上游 README 见同目录 README.md,许可证见 LICENSE.upstream.
