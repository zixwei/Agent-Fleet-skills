# 封面执行配方

先完成[素材与构图判断](art-direction.md)，再为本期新建schema_version=2配方。样例提供结构与具体程度；“复制JSON”不代表沿用其刊名、照片、色彩和几何位置。

## 从成品回到可执行输入

[河流配方](../assets/examples/river-journey.json)、[建筑配方](../assets/examples/architecture-close.json)、[微距配方](../assets/examples/stone-macro.json)、[山岳配方](../assets/examples/mountain-depth.json)分别附有实际成品与编译提示词。原V5的[铁路稻田配方](../assets/railway-cover-recipe.json)保留编辑目标、轨道方向、天空、稻穗与文字层次；它需要本期实际输入照片，不含用户原始图。

```sh
python scripts/build_cover_prompt.py --recipe assets/examples/architecture-close.json --out work/architecture-prompt.txt
```

以上命令只编译配方，不制作图片。真实照片配方完成后，用--check-inputs同时核对source相对于配方路径的文件是否存在；脚本不读取像素，执行者仍须实际查看图片、检查拍摄对象和授权。

## schema_version=2

|字段|必须落实的判断|
|---|---|
|publication / topic / content_promise|本期刊名、题目和内页兑现什么；未给刊名时自主提出原创名字|
|mode / inputs|photo-layout、photo-derivative或original-illustration；每个输入标记edit-target/content-reference/style-reference及描述|
|direction|route、why_this_image、rejected_route、why_rejected；用本期可见关系说明选择|
|art_direction|subject、camera、subject_scale、foreground、middle_ground、background、eye_path、light、palette、texture|
|aspect_ratio / frame|实际开本比例、外框颜色和宽度占封面宽的比例；未用外框可设0|
|text|每项role、逐字content、lines断行、归一化box=[x,y,w,h]、font_character、contrast_plan、occlusion|
|factual_anchors / allowed_changes / forbidden_changes|保留事实、允许处理、不能添加的对象/身份/场景|
|review_checks|本期最容易失败的视觉部位与文字|

text角色包括masthead、headline、location、secondary、issue，必须恰有一个刊头和一个主标题；其余按需要添加。lines拼接必须与content逐字相等；box以整幅平面稿左上角为原点，留在外框内部。occlusion.enabled=true时写明前景与不得损坏的字形部位。原版prose配方需迁移到v2，不再把“composition=高级美观”当充分输入。

结构检查会拦截缺项、错误断行、越界字场、没有照片输入却声称照片编辑等问题；不能自动判断画面是否漂亮。区域比例是指导目标，最终校验以实际成品为准。

## 制作路径

- photo-layout：保留现场图像内容，用可编辑字层、裁切与已有真实前景蒙版构图。
- photo-derivative：已获相应授权的照片艺术衍生，图像生成工具附上实际看过的目标照片，锁住事实与身份；记录改动。
- original-illustration：题材适合的原创解释/概念插画，不能冒充用户亲拍或真实档案。四个成品案例为此模式，不能据此把所有用户照片都改成新场景。

主题需要图文融合时主动调用当前可用工具，不等待固定关键词。工具不可用时继续采用与素材相符的摄影排字；复杂中文出错时保留好图、分离准确文字。主包的magazine_layout.py可以按背景→标题→真实透明前景→刊头小字的顺序绘制；独立封面执行可用当前可用矢量/PDF引擎，无需假装某个工具存在。

首次/改向时渲染真实封面与代表跨页。技术自检通过后另建本期方案，不能用pilot.json只换image.src。验收按[视觉标准](acceptance.md)执行。


## 本项目的默认成品路线

优先为本期实际照片建立photo-derivative配方并调用可用图像生成工具，或在明确的主题合成授权内组织照片参考。mode必须与实际输入和制作相符。photo-layout用于用户指定保留原片、生成未获授权或工具不可用等明确情况，不因实现简单而默认选它。
