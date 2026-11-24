# 可复用排版运行时

本运行时提取 V5 中通用的裁切、字体注册、文字容器、蒙版叠层、导航与 PDF 拆页能力，去除了单期照片清单和本机路径。它是版式组件层；主编仍需为本期选择图片、事实、构图和叙事，不能把示范页直接填满当作全刊。

## 从包内资源启动

Python 3.11+；依赖版本固定在 [requirements.txt](../scripts/requirements.txt)。预览还需要 Poppler 的 pdftoppm；没有它仍可生成 PDF，但必须在可用的阅读器中完成视觉检查。所有命令从本 Skill 根目录运行，使用环境里可用的 Python，不写死某台机器的解释器。

```sh
python -m pip install -r scripts/requirements.txt
python scripts/prepare_fonts.py --out work/fonts
python scripts/magazine_layout.py --plan assets/specimens/pilot.json --fonts work/fonts/font-manifest.json --out work/technical-smoke --preview --demo
```

这一步是技术自检，不能直接作为本期封面。pilot标记为component-demo，只有显式--demo才可运行；真实制作另建本期plan。默认准备完整字库，第一次实例化 CJK 字体可能需要几分钟。之后复用生成的字体目录；没有系统字体依赖，也不需要联网下载字体。只针对定稿文字缩减体积时：

```sh
python scripts/prepare_fonts.py --out work/fonts-final --text work/issue.json
```

子集包含文本文件中的实际 Unicode 字符和基础 ASCII。修改文字后重新生成子集；输出前组件再次按所用字体逐字检查，缺字直接拒绝，不静默回退。字体来源校验、轴值、输出哈希与许可证写入 font-manifest.json。复用完整字库时无需每次实例化。

## 默认字体系统

固定配置在 [default-fonts.json](../assets/default-fonts.json)，角色也定义默认字号、行距。尺寸仅为本 Skill 可运行起点，不称为样刊官方参数。

|角色|默认家族与字重|字号 / 行距（pt）|
|---|---|---|
|正文|思源宋体 400|10.5 / 18|
|宋体标题|思源宋体 600|26 / 34|
|图注 / 导航|Noto Sans SC 500|8 / 12|
|强展示字|Noto Sans SC 800|46 / 53|
|数字 / 拉丁短题|Roboto Condensed 400|44 / 48；10 / 13|

Roboto Condensed 是为可分发性选定的开源窄体，**不是 Arial Narrow 的等宽替代品**；V5 原页面必须重新校准。无声替换字体会改变断行与页高，因此更换字体必须修改 profile、记录哈希、重做视觉样张。复杂文字另按地方文字指南选择塑形引擎；本运行时不自动支持藏文塑形。

包内保留未修改的三份 OFL 字体源及许可证；运行时为实例/子集设置独立 FieldKit 字族名，保留版权记录。来源、版本及哈希见 [字体来源](../assets/fonts/sources.json)。

## 输入格式

JSON schema_version=1。真实制作时，根据本期已经选定的封面配方和跨页方案新建work/issue.json；按内容组合下列组件，确定主图焦点、文字字场与层次。教学[pilot.json](../assets/specimens/pilot.json)只解释数据结构，不能只换image.src后当作本期设计；其几何前景不是真实照片蒙版。素材可与plan放在一起，或用--asset-root指定素材根目录。相对路径按plan所在目录解析，字体按font-manifest所在目录解析，不依赖当前工作目录。

顶层：title、author、page_mm（单页宽高，默认190×254mm）、pages。每个 pages 项是一个物理画布：leaves=1 单页、leaves=2 对页；id、name、background、elements、可选 navigation。按元素数组顺序绘制，即后元素覆盖前元素。所有坐标单位 **mm，原点左上角**；字号/行距/线宽为 **pt**。单页文字框跨中缝与否由设计者明确选择。

|组件 type|必要属性|用途|
|---|---|---|
|text|text、x/y/w/h、role|纯文本，换行用 \n；检查缺字与框高，正文可选择|
|rect / panel|x/y/w/h、fill|稳定字场、色面、遮罩|
|image|src、x/y/w/h|按 EXIF 校正，focal=[0..1,0..1] 为原图焦点（左上原点）；fit=cover/contain|
|image + clip|clip=[[0,0],[1,0],[.8,1],[0,1]]|相对于图片框的异形窗口；真实照片保持原比例|
|image + PNG alpha|透明 PNG，fit=contain|有真实依据的前景蒙版；置于标题之后实现穿字|
|line|points、stroke|可编辑引线/时间线；line_width、dash可选|
|polygon|points、fill，可选stroke|矢量结构形状、图例、资料支持的图解|
|quote|x/y/w/h、text、role、fill|padding、offset、border、corner_gap 控制错位底色和开角边框|

text 的可选参数：size、leading、color、align（left/center/right）、background（真实纯色背景）、over_image=true。不支持任意 HTML、自动绕排或自动从照片推导事实。长文分栏用多个明确的 text 框按阅读顺序接力；超出框高则先编辑文字或调整版面，不自动缩小到不可读。

image 裁切把归一化焦点尽量置于目标框中心，再限制裁切范围；它与 V5 的“裁切余量比例”不是同一参数。contain 保留全部图像，空白由下层色场承担。原文件不改写。

navigation={label,color,background} 生成每一单页的连续页码。name 生成 PDF 书签。导航的背景必须真实，不能随意填 background 让对比检查通过。各页需要不同颜色时使用独立 text 组件；导航不自动感知图片亮度。

## 输出与质量边界

输出 magazine-spreads.pdf、magazine.pdf（拆分的单页版）、layout-report.json，可选 preview-*.png。指定新 out 目录，不覆盖旧版。缺字、文字溢出、已声明纯色底的低对比以及越界框都会阻止提交 PDF。文字绝不自动缩小；纯色辅助对比阈值与字体指南一致。摄影上的文字留在 report.manual_review，必须逐块目视，不声称代码能自动保证所有局部对比。

对象重叠可能正是设计意图，因此程序不把任意重叠当错误；仍须人工检查中缝、遮挡、引线和误叠。图片、正文与图注应分离；图片实际分辨率由最终印刷规格另查。这里的阅读版不是自动取得印厂色彩管理与出血认证的印刷文件。

## 原创示范与重建

先看 [样册索引](visual-specimens.md)，再看相关 PNG/PDF，不只读方法名字。样册用原创矢量地景作为输入，清楚标明非实景；不分发参考杂志截图、用户旅行照片或 V5 整本 PDF。

```sh
python scripts/create_demo.py --assets work/demo-assets --fonts work/fonts/font-manifest.json --out work/demo
```

该命令重建标记为component-demo的原创输入图、教学样册plan与pilot，并在内部显式启用演示模式。它用于技术自检；正式摄影封面先打开封面Skill的成品示例、选择本期构图后另建plan。项目特有数据在JSON内；运行时没有单期硬编码。行为回归用scripts/test_toolkit.py，真实照片的私有试排不进入发行包。


## 照片裁切

审片记录 EXIF 转正后的宽高比与必须保留的范围，例如塔尖、完整人物或合影。横图可裁竖、竖图可入跨页，先评估画框和主题是否匹配。关键照片设 `keep_region: [left, top, right, bottom]`，坐标相对于转正后的原图，范围 0 到 1。程序在铺满画框的前提下尽量靠近 focal 并保留该区域。无法同时满足时调整画框、改用 contain 保留完整图片或换图。全程等比缩放。

运行排版时加 `--crop-previews`，在 crop-previews 目录查看原图、裁切框和裁切结果。layout-report.json 的 images 记录每张图的 source_crop、source_area_lost 与 crop_review。裁掉超过 40% 的矩形原图面积时提示人工复核，该阈值用于筛选风险。主体靠边即使损失较小也要检查。

必须保留区域的自动检查与面积统计针对矩形画框。异形窗口、出血、中缝和其他图层遮挡需结合最终 PDF 检查，矩形对照图用于辅助定位。优先调裁切位置和框比，其次调整组图或使用完整图。改版后重看最终页面再交付。
