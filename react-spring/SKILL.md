---
name: react-spring
description: Use when animating React UI with spring physics: fades, mounts, gestures, scroll-linked motion, or React Three Fiber animation.
---
来源:https://github.com/pmndrs/react-spring (2026-11-24 ET 核实, stars 29,326, 语言 TypeScript, 许可证 MIT, 上游提交 13aacc5, 默认分支 next). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是动画库,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编 API,示例以 README 为准,完整文档见 https://www.react-spring.dev.

# react-spring

## 何时用

React 页面或组件要做动效时:淡入淡出、挂载/卸载过渡、手势联动、滚动出现、以及 React Three Fiber 里的 3D 动效.弹簧物理优先,动效自然不断;也支持按时长 + 缓动的写法.

## 安装

```bash
npm install @react-spring/web
# React Three Fiber 用
npm install @react-spring/three
```

## 核心用法

```jsx
import { animated, useSpring } from '@react-spring/web'

const styles = useSpring({
  from: { opacity: 0 },
  to: { opacity: 1 },
})

<animated.div style={styles} />
```

滚动出现示例 (README 原例):

```jsx
import { animated, useSpring } from '@react-spring/web'

const FadeIn = ({ isVisible, children }) => {
  const styles = useSpring({
    opacity: isVisible ? 1 : 0,
    y: isVisible ? 0 : 24,
  })

  return <animated.div style={styles}>{children}</animated.div>
}
```

## 关键点

- 跨平台:支持 react-dom 与 react-three-fiber.
- 声明式为主,也支持命令式写法.
- 默认弹簧物理,也支持 duration + easing.
- 其余 hooks (useSprings / useTrail / useTransition / useChain 等) 与各 hook 的演示见官网 https://react-spring.dev/hooks/use-spring, 用前先查, 不要凭记忆写参数.
- TypeScript 支持,类型定义随包发布.

## 适用场景

页面切换、列表增删动画、卡片翻转、滚动渐显、手势拖拽回弹、3D 场景里的物体动效.

## 注意事项

- 动效写在 `animated` 组件的 `style` 上,普通 div 不接 spring 值.
- 性能:动画值变化频繁时不要每帧 setState,用 spring 值直驱样式.
- 上游 README 见同目录 README.md,许可证见 LICENSE.upstream.
