# 原创视觉样册与组件索引

本页为组件教学，摄影封面的成品审美以[封面Skill](../../photo-magazine-cover/SKILL.md)中的摄影感案例为准。不要把这里的几何山形、绿色板面和标题位置当作默认封面。sample plan带component-demo标记，CLI需--demo运行。

执行时先打开[10页单页样册](../assets/specimens/original-layout-specimens.pdf)或[6个画布的对页样册](../assets/specimens/original-layout-specimens-spreads.pdf)。这是可随包分发的原创比例示范，不包含参考杂志原页或旅行者私人照片。源图为原创矢量地景；展示字、正文、图注、框线与引线均由随包运行时生成。

|画布|直接观看|对应阅读任务 / 方法|可修改输入|
|---|---|---|---|
|封面|[比例示范](../assets/specimens/specimen-01.png)|单焦点、外框、标题与前景分层 / T01|specimen.json 的 cover|
|02–03 开篇|[超大字与稳定字场](../assets/specimens/specimen-02.png)|跨页展示字、共同色场、中缝分词 / T02、C05|opening|
|04–05 组照|[错层与引文](../assets/specimens/specimen-03.png)|异形主窗口、大小图接力、偏移开放框 / P01、P02、R02|collage|
|06–07 图解|[可编辑引线](../assets/specimens/specimen-04.png)|整体到层级、明确落点、标签 / D04|explanation|
|08–09 阅读|[正文与窄侧轨](../assets/specimens/specimen-05.png)|稳定阅读层、数字层级 / R04|reading|
|10 收束|[校准顺序](../assets/specimens/specimen-06.png)|纯主题色与文字层级|back|

这是38种方法中首批核心方法的组合示范；不是38种一键模板，也不是要求整本沿用一种绿金风格。生成式封面的具体艺术指导另见封面子 Skill，不以这张矢量封面代表图像生成品质。

源文件：[完整JSON](../assets/specimens/specimen.json)、[封面+代表跨页JSON](../assets/specimens/pilot.json)、[校准样张](../assets/specimens/visual-pilot-spreads.pdf)。绘图与布局重建命令见[运行时](production-toolkit.md)。图形、文字与版式源可用于制作新的样张；真实建筑信息不能从示范图推导。

选用时比较：字与主图的面积比例、正文框宽、落点、空白、中缝和页码。把本期真实图片、标题和最长正文替换进去再渲染，不能只换一个主色就认为视觉已校准。
