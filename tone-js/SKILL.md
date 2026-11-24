---
name: tone-js
description: Use when synthesizing or scheduling sound in the browser: synths, samplers, effects, transport-synced music, or interactive audio.
---
来源:https://github.com/Tonejs/Tone.js (2026-11-24 ET 核实, stars 14,946, 语言 TypeScript, 许可证 MIT, 上游提交 b48d299, 默认分支 dev). 帖子来源:https://x.com/okapi_fukugyo/status/2125013131554660780 (@okapi_fukugyo, 2026-11-23 ET).

> 注意:上游仓库本身是 Web Audio 框架,不是原生 Agent Skill.本文件是舰队包装技能:先读同目录 README.md,再按下面流程动手.不要凭记忆编 API,示例以 README 为准,完整 API 文档见 https://tonejs.github.io/docs/.

# Tone.js

## 何时用

浏览器里要合成或编排声音时:合成器音色、采样播放、效果器、节拍同步、交互音乐、音游、音效按钮.

## 安装

```bash
npm install tone      # 最新稳定版
npm install tone@next # 或 next 版
```

```js
import * as Tone from "tone";
```

## 核心用法 (README 原例)

```javascript
//create a synth and connect it to the main output (your speakers)
const synth = new Tone.Synth().toDestination();

//play a middle 'C' for the duration of an 8th note
synth.triggerAttackRelease("C4", "8n");
```

`Tone.Synth` 是带一个振荡器与 ADSR 包络的基础合成器.

```javascript
const synth = new Tone.Synth().toDestination();
const now = Tone.now();

// triggerAttack 开始发声 (振幅上升), triggerRelease 结束发声 (振幅回落)
synth.triggerAttack("C4", now);
synth.triggerRelease(now + 0.5);
```

## 关键能力 (README 章节)

- 合成器 Tone.Synth:triggerAttack / triggerRelease / triggerAttackRelease, 音符用 "C4"、时长用 "8n" 这类乐谱记法.
- 时间 Tone.Time:音符时值、节拍换算.
- 启动音频 Starting Audio:浏览器要求用户手势后才能启动音频, 见 README 该节.
- 编排 Scheduling / Transport:全局走带, 同步与排程事件.
- 乐器 Instruments、采样 Samples / Tone.Sampler:采样器播真实录音.
- 效果 Effects、信号 Signals:效果链与控制信号.
- 音频上下文 AudioContext、性能 Performance、测试 Testing 见 README 对应章节.

## 适用场景

网页互动音效、节拍器、简单编曲器、游戏音效、音乐可视化联动.

## 注意事项

- 音频必须等用户交互 (点击等手势) 后才能启动, 页面加载就自动播会被浏览器拦住.
- 时间与音高用 Tone 的记法, 不要自己算秒数拼.
- 上游 README 见同目录 README.md, 许可证见 LICENSE.upstream.
