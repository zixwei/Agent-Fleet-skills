# 常用表达词库（合并版）

生成日期：2026-09-26。机器可读版本在同目录 `lexicon.json`；本文件是它的可读表格版。本 skill 中的路径都相对于 skill 根目录：词库在 `assets/lexicon/`，可直接查询的短语索引在 `tools/phrase_index.json.gz`（见 `tools/README.md`）。

## 1. 语料是什么

- **house（本 skill 要模仿的写作风格，6 篇）**：H1（2023）、H2（2025）、H3（2025）、H4（2026）、H5（2026）、H6（2026）。其中 H2、H3、H4、H5、H6 5 篇算“近作”（表里的“近作篇数”只数这 5 篇）。
- **refA（参考脉络 A，早期论文，8 篇，2017-2022）**：refA1（2017）、refA2（2019）、refA3（2019）、refA4（2019）、refA5（2020）、refA6（2020）、refA7（2021）、refA8（2022）。只用来对比，不是写作证据的首选来源。
- **refB（参考脉络 B，早期论文，8 篇，2021-2024）**：refB1（2021）、refB2（2022）、refB3（2023）、refB4（2023）、refB5（2024）、refB6（2024）、refB7（2024）、refB8（2024）。用法同上。
- 语料按句切分（可用 `tools/build_corpus.py` 对自己的论文重建同结构的 `corpus.jsonl`），公式记作 `[M]` / `[EQ]`，引用记作 `[CITE]`，交叉引用记作 `[REF]`。本词库自带的 `tools/phrase_index.json.gz` 是同一批语料按 1-5 词 n-gram 预先计数的索引，不含完整句子，只用来查一个短语在各组各篇里出现了几次。

## 2. 数字是怎么来的

- 原始切片共 1697 条。按“同一个表达只留一条”合并后剩 **1272 条**，其中 251 条由多个切片的条目合并而来，保留的是功能最具体的那一条。
- 合并规则：同一表达在“句式骨架”类切片和“词类”类切片都出现时，裸连接词、裸动词、裸形容词等放进词类或连接词分区（例如 However、To this end、Specifically）；带有具体句式的放进对应写作步骤分区（例如 To fill this gap, we propose X 放 M4）。
- **所有计数都用 `tools/ps.py` 的匹配规则重新算过**，统一覆盖正文、附录、图注和声明四个区域（即 ps.py 默认设置）。每条的 `query` 字段是可直接改写成 ps.py 调用的短语模式。
- “house（篇/次）”= 含该表达的 house 论文篇数 / 总出现次数；refA、refB 同理。“近作篇数”= 在 house_current（H2-H6）中出现的篇数。
- 附录里偶尔有模型输出或提示词，个别词（如句首 Thus）的计数因此偏高；备注里标出了已知的情况。
- 例句取自语料，每句最多约 15 个词，超长的截取包含该表达的一段，并用 ... 标出省略；例句里能识别出具体论文的方法/模型名已替换为 X。

## 3. 状态怎么读（统一规则）

| 状态 | 规则 | 怎么用 |
|---|---|---|
| 常用 | current_papers >= 2 | 放心用，首选 |
| 偶用 | current_papers == 1 | 可以用，但不要当成招牌句式反复用 |
| 已弃用 | house_total > 0 but only in H1 (2023) | 一般不用；需要时找“常用”里的替代 |
| 仅参考 | house 0, used in >= 2 reference (refA+refB) papers | 知道即可；想用时先确认没有 house 自己的等价写法 |
| 禁用 | on the ground-rule dropped/banned list (regardless of counts) | 不用；草稿里出现要改 |

全表层级分布：常用 659，偶用 440，已弃用 71，仅参考 75，禁用 27。

## 4. 用 ps.py 查一个新表达

```bash
cd <skill>/tools
python3 ps.py "remains largely unexplored"          # 普通短语，大小写不敏感，按词边界匹配
python3 ps.py "we * that" -n 5                        # * 匹配一个词；-n 控制每组例句数
python3 ps.py "is not free" -q                        # -q 只看计数
python3 ps.py -g house,refA,refB "As shown in"        # 显式选组（默认就是这三组）
python3 ngrams.py -g house --contains gap --min-papers 2   # 找含某词的高频 n-gram（需要自建 corpus.jsonl）
```

不传 `--corpus` 时，`ps.py` 直接查本 skill 自带的 `tools/phrase_index.json.gz`（1-5 词的短语计数索引，只有词频，没有完整句子），按 house / refA / refB 三组分别报告：出现总次数、覆盖多少篇论文、逐篇次数。读输出：`papers 4/6` 是 6 篇 house 论文里有 4 篇用过；`H2:3 H4:1` 是逐篇次数。数一下其中属于 house_current（H2-H6）的篇数，套用第 3 节的规则，就得到状态。传 `--corpus <你的 corpus.jsonl>` 则改为查你自己建的语料（见 `tools/README.md`），这时还能看到完整例句和文件行号。

## 5. 分区目录

- M1 背景与重要性：53 条（常用 24，偶用 21，已弃用 2，仅参考 6）
- M2 缺口与局限：74 条（常用 21，偶用 36，已弃用 5，仅参考 12）
- M3 动机与研究问题：29 条（常用 8，偶用 18，已弃用 1，仅参考 2）
- M4 提出方法：68 条（常用 23，偶用 29，已弃用 5，仅参考 5，禁用 6）
- M5 机制与公式：131 条（常用 62，偶用 50，已弃用 6，仅参考 12，禁用 1）
- M6 理论与分析：97 条（常用 16，偶用 43，已弃用 31，仅参考 5，禁用 2）
- 实验设置：125 条（常用 64，偶用 46，已弃用 5，仅参考 10）
- M7 报告结果：71 条（常用 48，偶用 21，已弃用 1，仅参考 1）
- M8 分析与归因：52 条（常用 35，偶用 14，仅参考 2，禁用 1）
- M9 消融与敏感性：28 条（常用 19，偶用 7，已弃用 1，仅参考 1）
- 图表指代：36 条（常用 26，偶用 9，禁用 1）
- 章节开头与路线图：47 条（常用 29，偶用 17，仅参考 1）
- 摘要框架：16 条（常用 6，偶用 10）
- M10 结论、局限与展望：58 条（常用 14，偶用 35，已弃用 5，仅参考 4）
- 相关工作：50 条（常用 9，偶用 34，已弃用 4，仅参考 3）
- 连接词（按功能）：108 条（常用 66，偶用 21，已弃用 1，仅参考 8，禁用 12）
- 词类：动词：71 条（常用 65，偶用 2，已弃用 1，仅参考 1，禁用 2）
- 词类：形容词：46 条（常用 35，偶用 9，已弃用 1，禁用 1）
- 词类：副词（模糊语、增强语、程度）：37 条（常用 34，偶用 3）
- 词类：名词：35 条（常用 28，偶用 5，仅参考 2）
- 词类：介词搭配：29 条（常用 18，偶用 8，已弃用 2，禁用 1）
- 括号套式：11 条（常用 9，偶用 2）

## M1 背景与重要性

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `such as A \cite{}, B \cite{}, and C \cite{}` | 列举应用并逐项引用〔M1.1 领域开场：兴起与成就〕。注：P1 首句后半的标准写法（每项各带引用） | 6/19 | 5 | 7/19 | 5/12 | 常用 | ...complex reasoning tasks, such as mathematical problem solving [CITE], code generation [CITE], and scientific question...（H3） |
| `Recent studies (empirically) reveal / find / indicate / show that X \cite{}` | 近期研究表明〔M1.3 近期研究与发现〕。注：house最常用的背景证据引子。 | 4/11 | 3 | 3/3 | 3/5 | 常用 | Recent studies empirically reveal that large reasoning models (LRMs) can automatically allocate more reasoning strengths...（H3） |
| `X has (recently) emerged as a promising / effective method (approach) for Y \cite{}` | 新方法/新范式兴起〔M1.1 领域开场：兴起与成就〕。注：house主力开场动词；H4 摘要、引言、结论都用。 | 4/8 | 3 | 0/0 | 1/1 | 常用 | ...steering has recently emerged as a promising method for defending against jailbreak attacks, requiring no...（H4） |
| `As illustrated in Figure \ref{}, instead of A \cite{}, this method reformulates X as Y` | 图示对比已有方法〔M1.5 已有方法的具体描述〕。注：H5 P2；house签名式图指针。 | 4/6 | 3 | 2/3 | 3/4 | 常用 | As illustrated in Figure [REF], instead of encoding the entire context in a single forward...（H5） |
| `recent works \cite{} show that ...` | 近期工作（复数）〔M1.3 近期研究与发现〕 | 4/6 | 4 | 3/4 | 0/0 | 常用 | ...is inspired by recent works of disentangling advantage calculation for stabilizing the training with different...（H5） |
| `X, especially Y \cite{}, ...` | 聚焦到具体子问题〔M1.1 领域开场：兴起与成就〕。注：H4 摘要、引言首句 'especially jailbreak attacks'；refA 更常用。 | 4/5 | 3 | 6/17 | 3/4 | 常用 | ...ability to refuse malicious prompts, especially jailbreak attacks, is essential for safe and reliable use.（H4） |
| `Recent advances in X reveal that ...` | 近期进展〔M1.3 近期研究与发现〕 | 4/5 | 4 | 0/0 | 1/1 | 常用 | ...this from the recent advances in the research line of self-evolving training methods, where a...（H6） |
| `prevailing (strategies / understanding / CF models)` | 主流的〔M1.4 主流做法与范式〕。注：H2 8 次集中；H4 1 次。 | 3/11 | 2 | 0/0 | 3/5 | 常用 | To mitigate this, two prevailing strategies are used: vector calibration and conditional steering.（H4） |
| `across various domains / tasks / benchmarks` | 覆盖面广〔M1.1 领域开场：兴起与成就〕。注：house偏好 across various；refB 偏好 a variety of。 | 3/6 | 2 | 0/0 | 2/2 | 常用 | ...models (LMs) encode rich world knowledge beyond mere semantics, attracting significant attention across various fields.（H2） |
| `X has emerged as a critical factor for both A and B \cite{}` | 关键因素〔M1.2 重要性〕。注：H3 引言；H1 'as a key factor for'。 | 3/4 | 2 | 3/3 | 1/2 | 常用 | ...it has emerged as a critical factor for both performance optimization and controllable model behavior.（H3） |
| `Recent work X has tried to tackle this by ... / recent studies try to ...` | 已有工作尝试〔M1.5 已有方法的具体描述〕。注：H5 摘要；H4 预备节 'recent studies try to'；H6 相关工作 'several efforts have tried to mitigate this'。 | 3/4 | 3 | 0/0 | 0/0 | 常用 | Recent work MemAgent has tried to tackle this by processing context chunk-by-chunk in an RNN-like...（H5） |
| `This vector is typically derived as ..., capturing ...` | 已有做法的常规做法〔M1.5 已有方法的具体描述〕。注：H4、H5 描述已有方法常规。 | 3/3 | 3 | 2/2 | 2/2 | 常用 | This vector is typically derived as the mean difference between activations of compliant and refused...（H4） |
| `the widely adopted X paradigm / tasks` | 广泛采用〔M1.4 主流做法与范式〕。注：H5、H6 预备节；替代 'standard practice'。 | 3/3 | 2 | 2/2 | 0/0 | 常用 | In this paper, we primarily focus on the widely adopted long-context question answering (QA) tasks.（H5） |
| `X \cite{} have demonstrated exceptional / impressive performance across ...` | 领域成就开场〔M1.1 领域开场：兴起与成就〕。注：H3 引言首句；H1 用 achieved impressive performance。 | 3/3 | 2 | 0/0 | 2/2 | 常用 | ...reasoning models (LRMs) have demonstrated exceptional performance across a variety of complex reasoning tasks, such...（H3） |
| `Recently, studies have shown that X` | 近期研究显示〔M1.3 近期研究与发现〕。注：H6 预备节、H3 相关工作。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | Recent studies have shown that LRMs can adaptively allocate reasoning strength (i.e. the number of...（H3） |
| `for / in real-world applications` | 落到现实应用〔M1.1 领域开场：兴起与成就〕。注：H5 首句 'for real-world applications \cite{}, such as ...'。 | 2/3 | 2 | 4/4 | 4/4 | 常用 | ...models (LLMs) for real-world applications, such as reading an entire book or processing large-scale memories...（H5） |
| `recent work X \cite{} has explored / has tried to ...` | 点名一个近期工作〔M1.3 近期研究与发现〕。注：H5 摘要和引言 'Recent work MemAgent'。 | 2/3 | 2 | 2/2 | 0/0 | 常用 | Recent work MemAgent has tried to tackle this by processing context chunk-by-chunk in an RNN-like...（H5） |
| `a new paradigm of / for X` | 新范式〔M1.1 领域开场：兴起与成就〕。注：H3 相关工作开场；H6 用于展望。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...this could enable a new paradigm of self-improving reasoning, shifting RL from optimizing what LLMs...（H6） |
| `Trained end-to-end with X, this paradigm shows potential in mitigating Y` | 已有范式的潜力〔M1.5 已有方法的具体描述〕。注：H5 P2 收尾；H3。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...recurrent memory paradigm shows potential in mitigating the performance degradation from ingesting an overly long...（H5） |
| `X \cite{} is essential for LLMs to Y, where its quality strongly affects Z \cite{}` | X 对 Y 必不可少〔M1.2 重要性〕。注：H6 引言首句、H4 摘要首句。 | 2/2 | 2 | 2/2 | 1/1 | 常用 | ...ability to refuse malicious prompts, especially jailbreak attacks, is essential for safe and reliable use.（H4） |
| `Various X strategies are proposed, including A \cite{}, B \cite{}, and C \cite{}` | 列举已有策略〔M1.4 主流做法与范式〕。注：H2 引言。 | 2/2 | 2 | 1/1 | 1/1 | 常用 | Various alignment strategies are proposed, including fine-tuning LMs with user behavior data, incorporating embeddings from...（H2） |
| `..., where its quality strongly affects Y \cite{}` | 质量直接影响结果〔M1.2 重要性〕。注：H6 'strongly affects'；H2 'directly impacts'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...language models (LLMs) to solve complex tasks, where its quality strongly affects the final-answer correctness.（H6） |
| `Currently, one prevailing X holds that ...` | 句首 Currently〔M1.4 主流做法与范式〕。注：H2 引言。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | Currently, one prevailing understanding holds that general LMs and traditional recommenders (e.g. collaborative filtering models)...（H2） |
| `Together, these capabilities make it plausible to X` | 合起来使 X 成为可能〔M1.4 主流做法与范式〕。注：H6 P3(c) | 2/2 | 2 | 0/0 | 0/0 | 常用 | Together, these capabilities make it plausible to self-propose supervision criteria (e.g. rubrics) for assessing CoT...（H6） |
| `current X methods / approaches` | 当前方法（统称）〔M1.4 主流做法与范式〕。注：H4 结论 'Current activation steering methods are limited by'；refB 常用。 | 2/2 | 1 | 1/2 | 4/7 | 偶用 | Current activation steering methods are limited by their heuristic design, raising concerns about their robustness...（H4） |
| `is crucial for / to` | 对……至关重要〔M1.2 重要性〕 | 2/2 | 1 | 2/3 | 3/5 | 偶用 | ...over long context is crucial for various real-world applications, it remains challenging for large language...（H5） |
| `X plays crucial roles in Y / plays a crucial role in Y` | X 在 Y 中起关键作用〔M1.2 重要性〕。注：H6 摘要 'Despite CoT playing crucial roles in'（非谓语）；H1 'plays a more crucial role'。 | 2/2 | 1 | 2/2 | 2/2 | 偶用 | Despite chain-of-thought (CoT) playing crucial roles in LLM reasoning, directly rewarding it is difficult: training...（H6） |
| `across a variety of complex X tasks, such as A \cite{}, B \cite{}, and C \cite{}` | 列举应用场景〔M1.1 领域开场：兴起与成就〕。注：H3 引言首句；导师更常用。 | 1/2 | 1 | 1/1 | 4/7 | 偶用 | ...exceptional performance across a variety of complex reasoning tasks, such as mathematical problem solving, code...（H3） |
| `attracting significant / widespread attention (interest)` | 受到关注〔M1.1 领域开场：兴起与成就〕。注：只在 H2；2026 年后不用。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/2 | 1 | 2/3 | 3/3 | 偶用 | ...models (LMs) encode rich world knowledge beyond mere semantics, attracting significant attention across various fields.（H2） |
| `These empirical observations suggest that X may ...` | 归纳观察〔M1.3 近期研究与发现〕。注：H3 引言 P2 收尾。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | These empirical observations suggest that LRMs may have automatic and controllable mechanisms for reasoning strength...（H3） |
| `existing efforts / methods / works` | 已有工作（导师式统称）〔M1.4 主流做法与范式〕。注：refA 7/8 篇用；house仅 H5 相关工作 1 次；house更常点名方法（'recent work MemAgent'） | 1/1 | 1 | 7/14 | 0/0 | 偶用 | Existing works show that LLM performance often degrades when relevant evidence is dispersed across long...（H5） |
| `plays a pivotal / vital role in X` | 起关键作用（导师式）〔M1.2 重要性〕。注：导师开场套话；house用 crucial。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 2/3 | 2/2 | 偶用 | ...curve in Figure [REF] suggests that both user interests and user intention play vital roles.（H2） |
| `a common paradigm is to ...` | 常见范式（导师式）〔M1.4 主流做法与范式〕。注：refA refA3、refA4。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 2/2 | 2/2 | 偶用 | Scrutinizing leading CF models, we summarize a common paradigm [M] involving three components:（H2） |
| `prior efforts ("Prior efforts have shown ..." / "Although prior efforts, such as A and B, have attempted to ...")` | 已有努力（名词）〔M1.3 近期研究与发现〕。注：refA 常用 'Prior efforts have shown the importance of'；house仅 H4 摘要让步句。 | 1/1 | 1 | 3/6 | 0/0 | 偶用 | Although prior efforts, such as vector calibration and conditional steering, have attempted to mitigate this...（H4） |
| `X have achieved great success across various domains \cite{}, raising ...` | 领域成就开场〔M1.1 领域开场：兴起与成就〕。注：H2 首句；导师各 1 次。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 1/1 | 1/1 | 偶用 | ...models (LMs) have achieved great success across various domains, raising a critical question about the...（H2） |
| `A key idea shared by these methods is to ...` | 这些方法的共同思路〔M1.4 主流做法与范式〕。注：H6。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | A key idea shared by these methods is to let a single policy model [M]...（H6） |
| `As X are increasingly deployed in real-world applications, ensuring Y is essential for ...` | 部署增多→需求〔M1.1 领域开场：兴起与成就〕。注：H4 摘要首句。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | As LLMs are increasingly deployed in real-world applications, ensuring their ability to refuse malicious prompts,...（H4） |
| `Recent findings have revealed two key properties of X: A \cite{} and B \cite{}` | 近期发现两点性质〔M1.3 近期研究与发现〕。注：H3 引言 P2（prose.md P2(c)） | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Recent findings have revealed two key properties of reasoning strength in LRMs: it can be...（H3） |
| `The wide deployment of X \cite{} has raised growing concerns about Y` | 部署带来担忧（安全类开场）〔M1.1 领域开场：兴起与成就〕。注：H4 引言首句。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...(LLMs) has raised growing concerns about their vulnerability in refusing malicious prompts, especially those crafted...（H4） |
| `To address these limitations, recent work X \cite{} has explored a Y paradigm for Z` | 已有工作的尝试〔M1.5 已有方法的具体描述〕。注：H5 引言 P2 开头。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...recent work MemAgent has explored a recurrent memory paradigm for long context reasoning in an...（H5） |
| `While this X phenomenon has been widely observed, its underlying mechanism remains largely unexplored` | 现象已被广泛观察→机制未明〔M1.3 近期研究与发现〕。注：H3 摘要。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...automatic reasoning strength allocation phenomenon has been widely observed, its underlying mechanism remains largely unexplored.（H3） |
| `While X is crucial for Y, it remains challenging for LLMs as they suffer from Z` | 重要性+难点一句合并（摘要）〔M1.2 重要性〕。注：H5 摘要首句。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | While reasoning over long context is crucial for various real-world applications, it remains...（H5） |
| `X has been shown to substantially improve Y \cite{}` | 已被证明〔M1.3 近期研究与发现〕。注：H3 引言。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Increasing reasoning strength has been shown to substantially improve model performance on complex reasoning tasks.（H3） |
| `X is a crucial capability of large language models (LLMs) for real-world applications \cite{}, such as ...` | X 是关键能力〔M1.2 重要性〕。注：H5 引言首句（prose.md §8 收录） | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...contexts is a crucial capability of large language models (LLMs) for real-world applications, such as...（H5） |
| `X is essential, as the quality of Y directly impacts Z` | 说明必要性〔M1.2 重要性〕。注：H2 预备节。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This process is essential, as the quality of these representations directly impacts the recommendation performance.（H2） |
| `prevalent (solution / technique)` | 普遍的（导师式）〔M1.4 主流做法与范式〕。注：导师主力；house仅 H1。 | 1/3 | 0 | 4/9 | 7/13 | 已弃用 | Specifically, the prevalent paradigm in CL-based CF methods is to routinely adopt heuristic-based data augmentation...（H1） |
| `a surge of interest in X` | 兴趣激增〔M1.1 领域开场：兴起与成就〕。注：H1 早期用。 | 1/1 | 0 | 0/0 | 2/2 | 已弃用 | ...contrastive learning (CL) in self-supervised learning tasks, show a surge of interest in contrastive loss:（H1） |
| `X is of crucial / critical importance to Y` | 极其重要（导师式）〔M1.2 重要性〕。注：导师套话，house不用。 | 0/0 | 0 | 5/7 | 2/3 | 仅参考 | Clearly, representation learning is of critical importance to delineate user preference.（refB8） |
| `At the core (of X) is Y` | 核心在于（导师式）〔M1.2 重要性〕。注：refB 标志句式。 | 0/0 | 0 | 2/4 | 4/5 | 仅参考 | At its core is estimating how likely a user will adopt an item based on...（refA3） |
| `a dominant paradigm / line / scheme` | 主导范式（导师式）〔M1.4 主流做法与范式〕。注：refB 用。 | 0/0 | 0 | 0/0 | 3/4 | 仅参考 | ...emerged as a dominant line, which often recasts user-item interactions as a bipartite graph and...（refB5） |
| `has attracted increasing attention` | 受到越来越多关注〔M1.1 领域开场：兴起与成就〕。注：导师开场套话，house不用。 | 0/0 | 0 | 2/3 | 1/1 | 仅参考 | ...is of crucial importance to engender user trust, interpreting network behavior has attracted increasing attention.（refB1） |
| `has shown great potential in X` | 显示潜力（导师式）〔M1.1 领域开场：兴起与成就〕。注：refA 开场套话；house改用 shows potential in。 | 0/0 | 0 | 3/3 | 0/0 | 仅参考 | Despite great potential, it is highly challenging to exploit KG to guide negative sampling.（refA5） |
| `X is a keystone / cornerstone of Y` | 基石（导师式）〔M1.2 重要性〕。注：refB 用词，house不用。 | 0/0 | 0 | 0/0 | 2/4 | 仅参考 | ...(CF) is a keystone of personalized recommendation, hypothesizing that behaviorally similar users tend to have...（refB3） |

## M2 缺口与局限

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `..., leading to X and degraded performance on Y \cite{}` | 导致（分词尾）〔M2.5 后果链〕。注：house最常用的后果分词尾。 | 6/8 | 5 | 1/1 | 5/7 | 常用 | ...activation space of benign prompts, leading to unintended behaviors and degraded performance on non-harmful tasks.（H4） |
| `largely unexplored / underexplored (any form)` | 基本未探索（总计）〔M2.2 空白：未探索与不清楚〕。注：含 'leaving ... largely unexplored'、'with ... largely underexplored'。 | 5/10 | 4 | 1/2 | 3/3 | 常用 | However, the underlying nature and internal structure of these mechanisms remain largely unexplored.（H3） |
| `this issue / these limitations / such risks (回指名词)` | 回指上文的问题〔M2.5 后果链〕。注：house常用于句首回扣：'To mitigate this issue,' 'These limitations motivate' 'As a result, such risks ...'。 | 4/13 | 3 | 5/10 | 6/11 | 常用 | To mitigate this issue, activation steering has recently emerged as a promising method for defending...（H4） |
| `X lacks Y` | 缺乏（动词）〔M2.4 缺陷用词〕 | 4/8 | 3 | 3/5 | 1/1 | 常用 | ...largely heuristic and lack theoretical grounding, limiting their robustness and effectiveness in inducing refusal responses...（H4） |
| `X (fundamentally) suffers from Y` | 受困于〔M2.4 缺陷用词〕。注：H4 摘要 'fundamentally suffers from the trade-off'；H5 结论。 | 4/7 | 3 | 7/8 | 6/16 | 常用 | ...the utility of the LLM, while baselines suffer from degraded general capabilities (cf. Section [REF]).（H4） |
| `Although effective / While effective, ...` | 虽有效但〔M2.1 转折与让步〕。注：H4 P2 'However, while effective at X,'；H5 相关工作 'While effective, such methods face risks of'。 | 4/5 | 3 | 0/0 | 1/1 | 常用 | However, while effective at inducing refusal for malicious prompts, directly injecting a refusal direction vector...（H4） |
| `performance degradation / degraded performance` | 性能下降〔M2.5 后果链〕。注：H5、H4 高频。 | 3/10 | 3 | 0/0 | 2/3 | 常用 | ...activation space of benign prompts, leading to unintended behaviors and degraded performance on non-harmful tasks.（H4） |
| `X struggle(s) to / with Y` | 难以〔M2.4 缺陷用词〕。注：H5 P1 'struggle to handle'；H6 摘要。 | 3/4 | 3 | 1/1 | 3/5 | 常用 | ...context length grows, and they struggle to handle corpora that exceed their maximum context window.（H5） |
| `However, how to X remains largely underexplored` | 如何做仍缺研究〔M2.2 空白：未探索与不清楚〕。注：H6、H1、H2；prose.md §8 收录。 | 3/4 | 2 | 0/0 | 1/1 | 常用 | ...to adaptively propose and evolve such criteria in the self-evolving training paradigm remains largely underexplored.（H6） |
| `Despite these efforts / findings, X remain largely unexplored` | 尽管已有努力→空白〔M2.1 转折与让步〕。注：H2、H3 引言。 | 3/3 | 2 | 0/0 | 0/0 | 常用 | Despite these findings, the underlying mechanisms behind such planning capabilities remain largely unexplored, and it...（H3） |
| `X are limited by their heuristic design / is limited to Y` | 受限于〔M2.4 缺陷用词〕。注：H4、H5、H6 都在结论或局限节。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | ...one hand, it is limited to the QA domain, with other tasks (e.g., summarization) largely...（H5） |
| `..., mainly for two reasons / faces two drawbacks: (i) ...; and (ii) ...` | 行内 (i)(ii) 枚举〔M2.3 缺陷枚举框架〕。注：H5 摘要、H6 引言；导师不用 (i)(ii) | 2/5 | 2 | 0/0 | 0/0 | 常用 | ...(i) training additional reward models (RMs) for CoT supervision typically requires labor-intensive and fine-grained annotations,...（H6） |
| `time-consuming / labor-intensive` | 耗时、耗人工〔M2.4 缺陷用词〕。注：H6 'labor-intensive and fine-grained annotations'；refA refA7。 | 2/3 | 2 | 5/9 | 2/2 | 常用 | ...supervision typically requires labor-intensive and fine-grained annotations, and (ii) the policy’s CoT distribution shifts during...（H6） |
| `Directly V-ing X is difficult / hard: ...` | 直接做 X 很难〔M2.4 缺陷用词〕。注：H6 摘要、结论；H4 1 次出自附录提示词；按正文计只有 H6。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 5/6 | 1/2 | 常用 | ...directly rewarding it is difficult: training a reward model demands heavy human labeling efforts, and...（H6） |
| `undesirable / unintended X` | 不良后果〔M2.5 后果链〕。注：H4。 | 2/3 | 2 | 0/0 | 2/2 | 常用 | When compromised, LLMs may generate harmful or misleading outputs, posing undesirable legal and social risks.（H4） |
| `it remains unclear / uncertain whether X  \|  it is still unclear whether X` | 尚不清楚是否（合计）〔M2.2 空白：未探索与不清楚〕。注：H2 2 + H3 1；三个变体合计。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...remain largely unexplored, and it is still unclear whether similar planning capabilities occur in LRMs.（H3） |
| `we argue that ...` | 我们认为（导师主力）〔M2.1 转折与让步〕。注：导师主力，house少用。 | 2/2 | 2 | 7/18 | 5/10 | 常用 | We argue that we can effectively identify the occurrence of possible overthink, by using our...（H3） |
| `X may hinder Y` | 妨碍〔M2.5 后果链〕。注：H5 P3 'may hinder'。 | 2/2 | 2 | 4/7 | 6/7 | 常用 | ...two key limitations that may hinder the memory stability and efficiency of reasoning in practice:（H5） |
| `neglects X` | 忽略〔M2.4 缺陷用词〕。注：H6 结论。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | ...RLVR improves answer accuracy but neglects direct CoT supervision, which may lead to suboptimal reasoning.（H6） |
| `However, while effective at X, directly Y introduces a fundamental trade-off between A and B` | 先认可再指出问题〔M2.1 转折与让步〕。注：H4 P2；H6 'However, while important,'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | However, while important, directly rewarding or supervising CoTs remains challenging in practice, mainly for two...（H6） |
| `While important, this capability remains challenging: A \cite{}, and B \cite{}` | 重要但困难〔M2.1 转折与让步〕。注：H5 P1 第二句（prose.md §8 收录） | 2/2 | 2 | 0/0 | 0/0 | 常用 | While important, this capability remains challenging: LLMs typically experience dramatic performance degradation as the context...（H5） |
| `X fails to Y` | 未能（导师主力）〔M2.4 缺陷用词〕。注：导师主力，house少用。 | 2/8 | 1 | 7/28 | 7/23 | 偶用 | ...figure), the LLM fails to achieve performance improvements and even exhibits sudden performance drops during...（H6） |
| `sub-optimal / suboptimal` | 次优〔M2.4 缺陷用词〕 | 2/6 | 1 | 5/10 | 5/9 | 偶用 | Consequently, the model can converge to sub-optimal reasoning behaviors, limiting robustness and overall capability.（H6） |
| `overlooks the risk of X` | 忽视〔M2.4 缺陷用词〕。注：H1；2026 年少用；H4 的 1 次出自附录提示词，非正文。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/4 | 1 | 3/4 | 2/3 | 偶用 | ...as negative signals overlooks the risk of false negatives, thus failing to provide reliable contrastive...（H1） |
| `..., resulting in suboptimal X` | 造成（分词尾）〔M2.5 后果链〕 | 2/3 | 1 | 1/1 | 4/8 | 偶用 | ...to overly refuse benign prompts, resulting in a trade-off between safety enhancement and utility preservation.（H4） |
| `rarely (any form)` | 很少（总计）〔M2.2 空白：未探索与不清楚〕 | 2/3 | 1 | 1/2 | 0/0 | 偶用 | However, CoT is rarely rewarded or optimized explicitly, even often regarded as a by-product of...（H6） |
| `the need for X` | 对 X 的需求〔M2.7 收束与承接〕 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | These limitations motivate the need for more theoretically grounded approaches that can reliably induce refusal...（H4） |
| `X (potentially) impede(s) Y` | 阻碍〔M2.5 后果链〕。注：H1、H5。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | ...noise can further impede subsequent updates, making it harder to incorporate new key evidence from...（H5） |
| `X is rarely rewarded / considered (explicitly)` | 很少被……〔M2.2 空白：未探索与不清楚〕。注：H6 引言 P1、预备节；H1。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | However, CoT is rarely rewarded or optimized explicitly, even often regarded as a by-product of...（H6） |
| `X easily suffers from / easily results in Y` | 容易导致（导师式）〔M2.4 缺陷用词〕。注：refB 标志；house仅 H1。 | 2/2 | 1 | 5/9 | 4/11 | 偶用 | ...treats all unobserved interactions as negative items, thus easily overlooking the risk of false negatives.（H1） |
| `We identify two key limitations that may hinder X in practice:` | 列出两点关键局限〔M2.3 缺陷枚举框架〕。注：H5（去掉 Here,）；H1 'two critical limitations have been observed'。 | 2/2 | 1 | 1/1 | 0/0 | 偶用 | Here, we identify two key limitations that may hinder the memory stability and efficiency of...（H5） |
| `the underlying mechanism / nature of X remains largely unexplored` | 内在机制未明〔M2.2 空白：未探索与不清楚〕。注：H3 摘要、引言、相关工作。 | 1/9 | 1 | 0/0 | 1/1 | 偶用 | However, the underlying nature and internal structure of these mechanisms remain largely unexplored.（H3） |
| `(their) lack of theoretical grounding` | 缺理论依据〔M2.4 缺陷用词〕。注：H4 全文复用。 | 1/4 | 1 | 0/0 | 0/0 | 偶用 | ...attempted to mitigate this trade-off, their lack of theoretical grounding limits their robustness and effectiveness.（H4） |
| `incurring avoidable / unnecessary computation` | 多余计算〔M2.5 后果链〕。注：H5。 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | ...appeared), the model must still process the remaining chunks until the end, incurring avoidable computation.（H5） |
| `an untenable / impractical / infeasible assumption` | 不成立的假设（导师式）〔M2.4 缺陷用词〕。注：导师用。 | 1/2 | 1 | 3/3 | 3/4 | 偶用 | ...direct activation steering infeasible for real-world deployment application of activation steering infeasible for real-world safety...（H4） |
| `However, this naive X faces two crucial drawbacks: (i) ...; and (ii) ...` | 指出两点缺陷〔M2.3 缺陷枚举框架〕。注：H5 摘要；refA refA8 'faces two obstacles'。 | 1/2 | 1 | 1/1 | 0/0 | 偶用 | ...recurrent memory update faces two crucial drawbacks: (i) memory can quickly explode because it can...（H5） |
| `..., raising concerns about their robustness and generalizability` | 引发担忧〔M2.5 后果链〕。注：H4 预备节、结论。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...steering methods are limited by their heuristic design, raising concerns about their robustness and effectiveness.（H4） |
| `However, these methods are largely heuristic and lack theoretical grounding, limiting ...` | 启发式、缺理论依据〔M2.4 缺陷用词〕。注：H4 引言、预备节（prose.md §8 收录） | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...these methods are largely heuristic and lack theoretical grounding, limiting their robustness and effectiveness in...（H4） |
| `X hardly holds / hardly satisfies Y` | 几乎不能（导师式）〔M2.4 缺陷用词〕。注：导师用。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 4/9 | 4/10 | 偶用 | ...when trained jointly on multiple datasets, hardly experiences a performance decline on each source dataset.（H2） |
| `Despite their effectiveness, we argue that X` | 尽管有效，我们认为（导师式）〔M2.1 转折与让步〕。注：refA 标志句。 | 1/1 | 1 | 5/5 | 0/0 | 偶用 | However, despite its effectiveness in incentivizing reasoning, most RLVR training remains outcome-centric and provides no...（H6） |
| `This results in X` | 这导致（回指句首）〔M2.5 后果链〕 | 1/1 | 1 | 1/2 | 3/3 | 偶用 | This results in updated activations [M] , thereby inducing refusal behavior and achieving safety enhancement.（H4） |
| `heavily relying on empirically designed X or manually crafted Y` | 依赖人工规则〔M2.4 缺陷用词〕。注：H4 预备节。 | 1/1 | 1 | 1/1 | 2/2 | 偶用 | ...methods are largely heuristic, heavily relying on empirically designed calibration rules or manually crafted conditions.（H4） |
| `X undermine(s) Y` | 削弱〔M2.5 后果链〕。注：H5。 | 1/1 | 1 | 0/0 | 2/2 | 偶用 | ...such risks of memory explosion undermine both long-horizon stability and runtime efficiency for long-term reasoning.（H5） |
| `..., which may lead to suboptimal X` | 可能导致〔M2.5 后果链〕。注：H6 结论。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ...RLVR improves answer accuracy but neglects direct CoT supervision, which may lead to suboptimal reasoning.（H6） |
| `Once already exploded, the accumulated noise can further impede Y` | 进一步恶化〔M2.6 问题放大〕。注：H5 bullet。 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | ...accumulated noise can further impede subsequent updates, making it harder to incorporate new key evidence...（H5） |
| `When compromised, X may generate Y, posing undesirable Z risks \cite{}` | 带来风险〔M2.5 后果链〕。注：H4 P1。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | When compromised, LLMs may generate harmful or misleading outputs, posing undesirable legal and social risks.（H4） |
| `X are costly to train / V` | 代价高〔M2.4 缺陷用词〕。注：house仅 H6 结论 1 次；house更常用具体说法：'avoidable computation'、'increases the inference cost'、'labor-intensive'。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ...as reward models are costly to train and vulnerable to distribution shift and reward hacking.（H6） |
| `..., limiting their robustness and effectiveness in X` | 限制了（分词尾）〔M2.5 后果链〕。注：H4 引言、H6 P1。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...and lack theoretical grounding, limiting their robustness and effectiveness in inducing refusal responses to malicious...（H4） |
| `Contrary to prevailing understanding that X, this work re-examines ...` | 挑战主流看法〔M2.1 转折与让步〕。注：H2 摘要（发现类）层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Contrary to prevailing understanding that LMs and traditional recommenders learn two distinct representation spaces due...（H2） |
| `few studies have demonstrated that X` | 少有研究〔M2.2 空白：未探索与不清楚〕。注：H2 预备节。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...representations in literatures, few studies have demonstrated that they can solely outperform ID-based generators in...（H2） |
| `However, the vanilla approach inherits several drawbacks of X \cite{}` | 继承已有缺陷〔M2.3 缺陷枚举框架〕。注：H5 P3(a) | 1/1 | 1 | 0/0 | 0/0 | 偶用 | However, the vanilla approach inherits several drawbacks of naive RNN-style memory updates.（H5） |
| `introduces a fundamental trade-off between A and B` | 根本性权衡〔M2.4 缺陷用词〕。注：H4 P2。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...prompts introduces a fundamental trade-off between safety and utility -- the vector may indiscriminately affect...（H4） |
| `These limitations motivate the need for X that can ...` | 局限→需求〔M2.7 收束与承接〕。注：H4 预备节。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | These limitations motivate the need for more theoretically grounded approaches that can reliably induce refusal...（H4） |
| `This inefficiency is amplified when X (\eg ...)` | 问题在某条件下放大〔M2.6 问题放大〕。注：H5；prose.md 修订规则。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This inefficiency is amplified when evidence is unevenly distributed (e.g. when reranking brings key evidence...（H5） |
| `This X becomes even more severe when Y` | 更严重〔M2.6 问题放大〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...This inefficiency becomes even more severe when evidence is unevenly distributed across the context, where...（H5） |
| `This yields an underconstrained X: [explanation]` | 这带来（回指句首）〔M2.5 后果链〕。注：H6 P1。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This yields an underconstrained learning signal: for the same final answer, many distinct reasoning trajectories...（H6） |
| `X remains challenging in practice, mainly for two reasons: (i) ..., and (ii) ...` | 两点原因〔M2.3 缺陷枚举框架〕。注：H6 P2(b) | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...challenging in practice, mainly for two reasons: (i) training additional reward models (RMs) for CoT...（H6） |
| `Despite the (empirical) success of X, ...` | 尽管成功（让步）〔M2.1 转折与让步〕。注：H1；refB 常用 'Despite great success'。 | 1/3 | 0 | 2/2 | 5/7 | 已弃用 | Despite the empirical success of CL-based CF methods, two critical limitations have been observed that...（H1） |
| `Yet, ...` | 转折（早期）〔M2.1 转折与让步〕。注：H1 摘要；2025 年后不用。 | 1/1 | 0 | 0/0 | 3/3 | 已弃用 | Yet, some CF-tailored challenges make this adoption suboptimal, such as the issue of out-of-distribution, the...（H1） |
| `there is limited understanding of X` | 理解有限（早期）〔M2.2 空白：未探索与不清楚〕。注：H1 摘要（与 Worse still 连用） | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | ...still, there is limited understanding of contrastive loss in CF methods, especially w.r.t. its generalization...（H1） |
| `these loss functions share a common limitation that ...` | 共同局限〔M2.3 缺陷枚举框架〕。注：H1 预备节。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | ...loss functions share a common limitation that lacks sensitivity to label noise and false negatives.（H1） |
| `X remains limited` | 理解仍有限〔M2.2 空白：未探索与不清楚〕。注：H1 结论。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | ...the empirical successes, the profound understanding of contrastive loss in collaborative filtering (CF) remains limited.（H1） |
| `X is insufficient to Y` | 不足以（导师式）〔M2.4 缺陷用词〕。注：refA 用。 | 0/0 | 0 | 7/24 | 2/2 | 仅参考 | Hence, such node-based schemes are insufficient to capture the interactions among relations.（refA7） |
| `is prone to / susceptible to` | 易受（导师式）〔M2.4 缺陷用词〕。注：refB 用。 | 0/0 | 0 | 1/2 | 5/18 | 仅参考 | However, the representations are susceptible to learning popularity bias in the training data as shortcuts.（refB8） |
| `Despite (the) great success, ...` | 让步开句（refB）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 1/1 | 5/7 | 仅参考 |  |
| `However, there exist two deficiencies: (1) ...; (2) ...` | 存在两点不足（导师式）〔M2.3 缺陷枚举框架〕。注：refB refB8/RobustCF。 | 0/0 | 0 | 3/4 | 2/4 | 仅参考 | ...to rely on the interaction function to make up for the deficiency of suboptimal embeddings.（refA3） |
| `existing efforts have not fully explored X` | 未充分探索（导师式）〔M2.2 空白：未探索与不清楚〕。注：refA refA2、X。 | 0/0 | 0 | 4/6 | 1/1 | 仅参考 | ...existing efforts have not fully explored this connectivity to infer user preferences, especially in terms...（refA2） |
| `X are still far from being able to ...` | 结论开句（refB）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 0/0 | 4/4 | 仅参考 |  |
| `current methods fall short in X` | 不足以（导师式）〔M2.3 缺陷枚举框架〕。注：refA refA7、refB refB6。 | 0/0 | 0 | 1/1 | 2/3 | 仅参考 | ...we argue that current GNN-based methods fall short in modeling two factors: (1) User Intents.（refA7） |
| `The key reason is that X` | 关键原因在于（导师式）〔M2.3 缺陷枚举框架〕。注：refA refA3、refB refB1。 | 0/0 | 0 | 2/2 | 1/2 | 仅参考 | The key reason is that the entanglement among these features weakens the ability to identify...（refB1） |
| `X is non-trivial` | 并非易事（导师式）〔M2.1 转折与让步〕。注：refB 用。 | 0/0 | 0 | 1/1 | 2/3 | 仅参考 | We argue that the transition from DG to UDG is non-trivial.（refB7） |
| `However, each type of method suffers from some (inherent) limitations:` | 每类方法都有局限（导师式）〔M2.3 缺陷枚举框架〕。注：refA refA5、refB refB1。 | 0/0 | 0 | 2/2 | 1/1 | 仅参考 | However, each type of methods suffers from some limitations:（refB1） |
| `which derails X` | 使……偏离（导师式）〔M2.5 后果链〕。注：refB 用。 | 0/0 | 0 | 0/0 | 3/3 | 仅参考 | ...between the head and tail evaluations results in suboptimal representations, which derails the generalization ability.（refB2） |
| `X is still far from well-studied / has rarely been studied` | 研究不足（导师式）〔M2.2 空白：未探索与不清楚〕。注：refB refB7、refA refA1。 | 0/0 | 0 | 1/2 | 1/1 | 仅参考 | UDG is fast gaining attention but is still far from well-studied.（refB7） |

## M3 动机与研究问题

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `In this work, we focus on X` | 限定研究范围〔M3c 研究问题的提出〕。注：house多用于 Preliminary 节开头（H4、H6），不在 intro。 | 4/4 | 3 | 5/10 | 5/6 | 常用 | In this work, we focus on incentivizing LLMs to reason with RL, ...（H6） |
| `two key questions / two key properties / two key limitations` | '两个关键……'计数名词短语〔M3c 研究问题的提出〕。注：house用于问题、性质、限制的计数；导师 6/8 篇也用。 | 3/4 | 3 | 6/9 | 2/2 | 常用 | Here, we identify two key limitations that may hinder ...（H5） |
| `we investigate / examine / assess / test / explore whether X` | 检验某件事是否成立〔M3c 研究问题的提出〕。注：house 3 篇各 1 次，未超过 1 次；'we test whether' house 0 次（house用 'we test the possibility of'、'we assess whether'）。 | 3/3 | 3 | 2/2 | 0/0 | 常用 | For the first, we examine whether the number of reasoning tokens can be predicted ...（H3） |
| `we argue / conjecture / postulate / hypothesize / claim that` | 表明立场或假设〔M3a 理想/期望陈述〕。注：按规则为常用，但house 3 次都在方法/实验节（H3 方法、实验，H6 方法），intro 动机段从不用。导师 8/8 篇都用（refA 23、refB 18），多在 intro 'we argue that existing methods ...'。写 intro 动机时不要用。 | 2/3 | 2 | 8/23 | 8/18 | 常用 | we hypothesize that LRMs may modulate their reasoning planning through ...（H3） |
| `\textit{\textbf{Can X ... ?}}（单独成句的核心问题）` | 以一个是非问句提出核心问题〔M3c 研究问题的提出〕。注：house H6 intro 1 次（粗斜体，全文只此一问）；H4 2 次在实验 RQ 列表。 | 2/3 | 2 | 0/0 | 2/2 | 常用 | Can the policy model self-propose rubrics as CoT supervision criteria ...?（H6） |
| `Positive results / findings would imply / indicate that X` | 预先说明正面结果的含义〔M3d 假设与预期结果〕。注：house自有（导师 0 次），H2 'Positive results would imply that ...'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | Positive findings would indicate that LRMs plan reasoning strength ahead of generation ...（H3） |
| `To test this hypothesis, we employ X to V` | 引出检验假设的手段〔M3d 假设与预期结果〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | To test this hypothesis, we employ linear mapping to project language representations ...（H2） |
| `whether and how X` | 是否以及如何〔M3c 研究问题的提出〕。注：H3 结论、H6 各 1。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | This paper investigated whether and how large reasoning models (LRMs) plan reasoning strength ...（H3） |
| `we explore how X` | 探究如何……〔M3c 研究问题的提出〕 | 2/2 | 1 | 1/1 | 1/1 | 偶用 | For the second, we explore how LRMs encode pre-allocation signals within activations ...（H3） |
| `we draw inspiration from recent X studies and propose Y` | 借鉴已有研究提出方法〔M3e 灵感来源与可行性线索〕 | 1/2 | 1 | 0/0 | 2/2 | 偶用 | To this end, we draw inspiration from recent null-space studies and propose X, ...（H4） |
| `For the first, we examine whether ... For the second, we explore how ...` | 逐一对应两个研究问题〔M3c 研究问题的提出〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | For the second, we explore how LRMs encode pre-allocation signals ...（H3） |
| `we explore the possibility of V-ing X` | 探索可能性〔M3e 灵感来源与可行性线索〕。注：H2 3 次（摘要、intro）；H4 'the possibility of' 1 次。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | we explore the possibility of designing advanced collaborative filtering (CF) models ...（H2） |
| `These challenges motivate us to seek X that ... and ...` | 挑战→驱动我们寻找……〔M3b 必要性与动机收束〕。注：house只在 H6 摘要用 1 次；refB 用 'This motivates us to develop'（refB5、refB3）。 | 1/1 | 1 | 1/1 | 2/2 | 偶用 | These challenges motivate us to seek an autonomous CoT rewarding approach ...（H6） |
| `To further investigate, we show that X` | 进一步探究〔M3f Intro 中的初步发现〕 | 1/1 | 1 | 1/1 | 1/1 | 偶用 | To further investigate, we show that this direction vector consistently produces ...（H3） |
| `effective X should V` | 理想的 X 应当……〔M3a 理想/期望陈述〕。注：refB refB5 有 'effective debiasing should comprehensively address'。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | effective steering should lead to distinct trends（H4） |
| `..., raising a critical question about X` | 由背景引出关键问题〔M3b 必要性与动机收束〕。注：H2 intro 首句的分词尾。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Language models (LMs) have achieved great success ..., raising a critical question about ...（H2） |
| `As visualized in Figure X, we summarize three key empirical findings:` | 列出初步发现〔M3f Intro 中的初步发现〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | As visualized in Figure [REF], we summarize three key empirical findings ...（H3） |
| `Here we conduct preliminary experiments using X on Y` | 引入初步实验〔M3f Intro 中的初步发现〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Here we conduct preliminary experiments using DeepSeek-R1-distilled-Qwen on the MATH dataset（H3） |
| `In this work, we re-examine this prevailing understanding, by exploring whether X` | 重新审视主流认识〔M3c 研究问题的提出〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this work, we re-examine this prevailing understanding, by exploring whether ...（H2） |
| `In this work, we take an important step further, by V-ing ...` | 在已有工作上更进一步〔M3c 研究问题的提出〕。注：H3 预备知识节。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this work, we take an important step further, by demonstrating that ...（H3） |
| `Our empirical observations and findings include:` | 列出发现（早期式）〔M3f Intro 中的初步发现〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Our empirical observations and findings include:（H2） |
| `Specifically, we test the possibility of V-ing X -- that is, we assess whether ...` | 把问题转成可检验的可能性〔M3d 假设与预期结果〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Specifically, we test the possibility of directly deriving a behavior space from the language space（H2） |
| `This result indicates that ..., suggesting that ...` | 由结果推断〔M3f Intro 中的初步发现〕。注：'This indicates that' 在house组更常见（ngram: 3 篇 10 次）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This result indicates that the number of reasoning tokens is predictable prior to generation, ...（H3） |
| `This vulnerability highlights the necessity for more principled approaches.` | 局限→需要更有原则的方法〔M3b 必要性与动机收束〕。注：H4 intro P3 末句，下一段以 'To this end, we ...' 接提出句。refA refA1 有同句式。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This vulnerability highlights the necessity for more principled approaches.（H4） |
| `To fill this research gap, we investigate X by asking two key questions: (1) ...? (2) If so, ...?` | 以两个问题展开研究〔M3c 研究问题的提出〕。注：发现型论文（H3）的研究问题句式。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | we investigate the underlying mechanism ... by asking two key questions: (1) ...（H3） |
| `We find possibilities of achieving this from the recent advances in the research line of X` | 在某研究方向中找到可行线索〔M3e 灵感来源与可行性线索〕。注：'research line of' 另有 H2 1 次（非此功能）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We find possibilities of achieving this from the recent advances in the research line of...（H6） |
| `X necessitate(s) Y to focus more on Z` | 要求……更关注……〔M3b 必要性与动机收束〕。注：仅 H1 摘要；2025 后不用。 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | They necessitate the CL-based CF scheme to focus more on mining hard negatives ...（H1） |
| `Such intuition motivates us to ...` | 直觉驱动（导师式）〔M3b 必要性与动机收束〕。注：refA refA4、refA7；house 0 次。 | 0/0 | 0 | 2/2 | 0/0 | 仅参考 | [refA4] Such intuition motivates us to ... |
| `X should satisfy two requirements: 1) ..., and 2) ...` | 列出理想条件（导师式）〔M3a 理想/期望陈述〕。注：house 0 次；导师各 1 篇。 | 0/0 | 0 | 1/1 | 1/1 | 仅参考 | [refA5] high-quality negative samples should satisfy two requirements |

## M4 提出方法

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `In this work, we ...（任意动词）` | '本文'起句〔M4a 提出句〕。注：house 5/6 篇各 1 次左右；refA 8/8 篇共 27 次。 | 5/6 | 4 | 8/27 | 6/9 | 常用 | In this work, we re-examine this prevailing understanding, ...（H2） |
| `The key idea is to V: augment X with two ... -- a A and a B -- which ...` | 一句话讲核心思想〔M4d 核心思想〕。注：house自有（refA 仅 refA7 1）。也用于介绍已有方法的核心思想（H4、H5 附录、H3）。'key idea' 总计 12 次 5/6 篇。 | 4/6 | 4 | 1/1 | 0/0 | 常用 | The key idea is to augment the recurrent workflow with two text-controlled gates ...（H5） |
| `Trained end-to-end with X, this Y ...` | 训练方式作分词短语〔M4f 赋予能力/训练方式〕。注：H5 9 次。 | 3/11 | 2 | 5/12 | 1/1 | 常用 | Trained end-to-end with reinforcement learning (RL), this recurrent memory paradigm shows potential ...（H5） |
| `Extensive experiments on / across X demonstrate / highlight Y` | 大量实验表明（导师常用）〔M4h 证据路线图〕。注：导师 15/16 篇都用。house 5 次中 2026 年的 3 次是 intro 路线图 'We further conduct extensive experiments ...'（H4、H5，已单列）和 H4 结论；句首 'Extensive experiments ... demonstrate' 作主语只见于 H1 intro 和 H4 结论。 | 3/5 | 2 | 8/18 | 7/15 | 常用 | Extensive experiments on the out-of-distribution benchmark datasets ... further highlight ...（H1） |
| `a ... method / model called X` | 给方法命名（called）〔M4b 命名〕。注：house用 called（H2、H4）、termed（H2）、coined（H1）；refA 用 named，refB 用 called/coined。 | 3/5 | 2 | 3/4 | 2/2 | 常用 | a theoretically grounded and empirically effective activation steering method called X（H4） |
| `To address these X（任意后续）` | 回指上文问题〔M4a 提出句〕。注：H5 也用它引出'已有方法'（P2），不只引出本文方法。 | 3/5 | 3 | 1/1 | 0/0 | 常用 | To address these limitations, recent work MemAgent has explored a recurrent memory paradigm ...（H5） |
| `Second, we validate / it can ... (cf. Section Y).` | 路线图第二条〔M4h 证据路线图〕 | 3/4 | 3 | 3/3 | 2/3 | 常用 | Second, we validate the effectiveness of the two introduced gating mechanisms, ...（H5） |
| `we propose X, a/an [修饰] [framework/approach/method] for / that ...` | 方法名后接同位语说明类型与用途〔M4c 论文定语与方法修饰语〕。注：H4 intro 'propose X, a null-space-constrained activation steering approach that ...'；方法节首句同样使用（H4）。 | 3/4 | 2 | 1/1 | 2/2 | 常用 | we propose X, a gated recurrent memory framework for stable and efficient long-context reasoning（H5） |
| `to verify / examine / validate / demonstrate the effectiveness of X` | 验证有效性〔M4h 证据路线图〕 | 3/3 | 3 | 4/5 | 5/7 | 常用 | We further conduct comprehensive experiments to examine the effectiveness of the proposed X.（H6） |
| `Third, we systematically analyze ... (cf. Section Y).` | 路线图第三条〔M4h 证据路线图〕 | 3/3 | 3 | 0/0 | 2/2 | 常用 | Third, we systematically analyze the role of components in X with the ablation study（H5） |
| `We further conduct extensive / comprehensive experiments to verify / examine the effectiveness of X.` | 引出实验证据路线〔M4h 证据路线图〕。注：house自有（导师 0）。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | We further conduct extensive experiments to verify the effectiveness of X.（H5） |
| `we propose X（X 为方法名）` | 提出（方法名紧跟）〔M4a 提出句〕。注：house 'we propose' 总计 6 次（H4 1 为 'we propose to'）。导师更常用 'we propose a new model/solution, named X'。 | 2/5 | 2 | 1/1 | 2/2 | 常用 | we propose X, which incorporates two text-controlled gates ...（H5） |
| `To mitigate this (issue), X / two prevailing strategies are used` | 回指问题并引出缓解手段〔M4a 提出句〕。注：H4 4 次、H6 1 次；多用于引出'已有方法'。 | 2/5 | 2 | 0/0 | 0/0 | 常用 | To mitigate this issue, activation steering has recently emerged as a promising method ...（H4） |
| `We then uncover / We further reveal that X（发现型论文的摘要路线）` | 发现型论文逐条列出发现〔M4h 证据路线图〕。注：H3 摘要、intro、结论；H4 附录 1 次。 | 2/5 | 2 | 0/0 | 0/0 | 常用 | We further reveal that this direction vector consistently yields positive reasoning length prediction ...（H3） |
| `a simple yet effective X` | 论文定语（H2）〔M4c 论文定语与方法修饰语〕。注：refB 4/8 篇共 11 次（导师的招牌定语）。house 2026 年不再使用。 | 2/4 | 2 | 0/0 | 4/11 | 常用 | to build a simple yet effective model, with the language representations ... as the input（H2） |
| `we propose X (Full Name) / we introduce the Full Name (X)` | 首次提出时给方法全称〔M4b 命名〕。注：H5 方法节 'we introduce the X (X)'；refA 用 'we propose a new model, Full Name (X)'。 | 2/4 | 2 | 2/2 | 1/1 | 常用 | we propose X (X), ...（H6） |
| `we systematically analyze X` | 系统分析〔M4h 证据路线图〕 | 2/4 | 2 | 0/0 | 0/0 | 常用 | Furthermore, we systematically analyze this simple model and discover several potentials ...（H2） |
| `without compromising X` | 不牺牲……〔M4c 论文定语与方法修饰语〕 | 2/4 | 2 | 0/0 | 0/0 | 常用 | significantly improves the safety of LLMs without compromising general capabilities（H4） |
| `The core idea is to V / is that, given X, ...` | 一句话讲核心思想（core）〔M4d 核心思想〕。注：H4 两次（一次介绍已有方法 activation steering）。refB 'Our core idea is'。 | 2/3 | 2 | 0/0 | 2/2 | 常用 | The core idea is to let the policy model self-propose CoT supervision criteria ...（H6） |
| `To endow the model with such capabilities, we introduce X` | 引出训练手段以获得能力〔M4f 赋予能力/训练方式〕。注：H2 'To endow X with user intention-aware ability, we adopt ...'。 | 2/3 | 2 | 0/0 | 1/1 | 常用 | To endow the model with such capabilities, we introduce two reward signals ...（H5） |
| `Experiments across X demonstrate the effectiveness of Y, which ...` | 摘要中的实验总结句〔M4h 证据路线图〕 | 2/2 | 2 | 1/1 | 0/0 | 常用 | Experiments across multiple jailbreak attacks and utility benchmarks demonstrate the effectiveness of X（H4） |
| `First, X generally / consistently outperforms ... across ... (cf. Section Y).` | 路线图第一条：主结果〔M4h 证据路线图〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | First, X consistently outperforms existing activation steering baselines ... (cf. Section [REF]).（H4） |
| `To fill this (research) gap, we propose / investigate X` | 填补空白〔M4a 提出句〕。注：house自有（导师 0）。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | To fill this gap, we propose X (X via ...)（H6） |
| `X, a variant of Y` | ……的变体〔M4c 论文定语与方法修饰语〕。注：H1 4、H2 1；2026 不用。 | 2/5 | 1 | 0/0 | 0/0 | 偶用 | X, which is a variant of InfoNCE（H1） |
| `In this work, we propose / present / devise X` | '本文'+提出（导师式）〔M4a 提出句〕。注：house只在结论用（H1、H4），intro 不用；refB 7/8 篇在 intro 用。 | 2/3 | 1 | 4/4 | 7/9 | 偶用 | To this end, in this work, we presented a theoretically grounded ... method（H4） |
| `To bridge / close the gap, we ...` | 弥合差距（早期式）〔M4a 提出句〕。注：house只在 H1、H2；2026 年改为 'To fill this gap'。refB 4 篇用 'To close/bridge the gap'。H1 该句含禁用词 delve、underpinning。 | 2/3 | 1 | 0/0 | 4/4 | 偶用 | To bridge ... (X 'To bridge the gap, we delve into ...')（H2） |
| `model-agnostic X` | 与模型无关〔M4c 论文定语与方法修饰语〕。注：导师 refA8、refB4 各 1 篇。 | 2/3 | 1 | 1/5 | 1/6 | 偶用 | (2 次)（H1） |
| `we validate the effectiveness of X` | 验证有效性（we validate）〔M4h 证据路线图〕 | 2/2 | 1 | 0/0 | 4/4 | 偶用 | Second, we validate the effectiveness of the two introduced gating mechanisms（H5） |
| `for (more) stable and efficient X` | 论文定语（H5）〔M4c 论文定语与方法修饰语〕。注：H5 5 次。 | 1/5 | 1 | 0/0 | 0/0 | 偶用 | a gated recurrent memory framework for stable and efficient long-context reasoning（H5） |
| `with no / without any human annotation(s)` | 无需人工标注〔M4c 论文定语与方法修饰语〕。注：H6 5 次（摘要、intro、结论反复）。 | 1/5 | 1 | 0/0 | 0/0 | 偶用 | ... without any human annotation.（H6） |
| `a theoretically grounded and empirically effective X` | 论文定语（H4）〔M4c 论文定语与方法修饰语〕。注：每篇只选一个定语并在摘要、intro、方法、结论重复使用（H4 4 次）。 | 1/4 | 1 | 0/0 | 0/0 | 偶用 | X provides a theoretically grounded and empirically effective solution ...（H4） |
| `we present X, a ... method / a theoretically grounded ... method called X` | 提出（present）〔M4a 提出句〕。注：house 'we present' 共 18 次，但多数是 'we present the performance / how to ...'；用于提出方法的只有 H4 2 次。 | 1/2 | 1 | 1/1 | 2/2 | 偶用 | we present a theoretically grounded and empirically effective activation steering method called X（H4） |
| `For A, it learns to V ..., with B. For C, it learns to V ..., with D.` | 对两类输入分别说明〔M4e Intro 中的机制展开〕 | 1/2 | 1 | 0/0 | 1/1 | 偶用 | For utility preservation, it learns to construct a nearly zero vector for steering benign data（H4） |
| `Finally, we demonstrate two potential applications of our findings: A and B.` | 发现型论文路线图末条：应用〔M4h 证据路线图〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Finally, we demonstrate two potential applications of our findings: ...（H3） |
| `requiring no additional X / without requiring additional X` | 不需要额外代价〔M4c 论文定语与方法修饰语〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | requiring no additional post-training（H4） |
| `propose / introduce a novel X` | 用 novel 修饰本文方法〔M4a 提出句〕。注：house只在 H4 方法节 1 次，intro 和摘要从不用 'a novel'；refA、refB 常用 'we propose a novel framework/solution'。建议不用。 | 1/1 | 1 | 3/5 | 4/7 | 偶用 | We first introduce a novel and learnable activation steering mechanism ...（H4） |
| `An overview of the key process is illustrated in Figure X.` | 指向总览图〔M4d 核心思想〕。注：refB 3 篇 'An overview of X is shown in Figure'。 | 1/1 | 1 | 0/0 | 3/3 | 偶用 | An overview of the key process is illustrated in Figure [REF].（H6） |
| `X (short for Y) / Y, X for short` | 缩写说明〔M4b 命名〕 | 1/1 | 1 | 2/2 | 1/1 | 偶用 | (1 次)（H4） |
| `this method reformulates X as a Y process` | 重述问题形式〔M4d 核心思想〕 | 1/1 | 1 | 1/1 | 1/1 | 偶用 | this method reformulates long-context reasoning as a sequential, recurrent, and chunk-by-chunk ...（H5） |
| `..., thus achieving both A and B` | 同时达成两个目标〔M4g 益处收束句〕 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ..., thus achieving both safety enhancement and utility preservation.（H4） |
| `X provides a ... solution that V-s ... while V-ing ...` | 方法提供……的方案〔M4g 益处收束句〕 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | X provides a theoretically grounded and empirically effective solution that rejects malicious prompts ...（H4） |
| `..., which decide (i) whether ... and (ii) whether ...` | 两个部件各管一个决定〔M4d 核心思想〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | which decide (i) whether the memory should be updated ... and (ii) whether ...（H5） |
| `First, we conduct a primary study showcasing that X` | 先做初步研究〔M4h 证据路线图〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | First, we conduct a primary study showcasing that self-proposed rubrics can be reliable ...（H6） |
| `Here, we deem X valid if ... (i.e. ...), since ...` | 给判定标准并说明理由〔M4e Intro 中的机制展开〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Here, we deem a rubric valid if its satisfaction indicator is strongly correlated ...（H6） |
| `Inspired by X, we propose Y (full name), which ...` | 摘要中以灵感起句提出方法〔M4a 提出句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Inspired by recent self-evolving training methods, we propose X ...（H6） |
| `it considers X as a learnable process with two principled learning objectives: A and B` | 把问题重述为学习过程〔M4d 核心思想〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | it considers activation steering as a learnable process with two principled learning objectives（H4） |
| `Our work provides new insights into X and offers practical tools for Y` | 摘要收尾（发现型）〔M4i 路线图后的收尾句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Our work provides new insights into the internal mechanisms of reasoning in LRMs ...（H3） |
| `Overall, our results suggest a new paradigm in which ..., pointing toward ...` | 总结并展望方向〔M4i 路线图后的收尾句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Overall, our results suggest a new paradigm in which models autonomously generate ...（H6） |
| `The A enables ..., mitigating ..., while the B provides ...; together, they yield ...` | 两部件各自好处→合力〔M4g 益处收束句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...; together, they yield more stable and efficient long-context reasoning.（H5） |
| `the simplicity and effectiveness of X offer a convenient solution for Y, without requiring Z` | 强调简单有效〔M4i 路线图后的收尾句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | the simplicity and effectiveness of X offer a convenient solution for enhancing the safety ...（H4） |
| `To better address X, we present Y` | 为更好解决……提出〔M4a 提出句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To better address the trade-off between safety and utility, we present ...（H4） |
| `X provides a ... paradigm with the introduction of Y` | 新范式收尾〔M4i 路线图后的收尾句〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | X provides a stable and efficient long context reasoning paradigm with the introduction of ...（H5） |
| `we advocate (for) its adoption as a standard X` | 倡导采用（早期式）〔M4i 路线图后的收尾句〕。注：仅 H1；refB 3 篇。2025 后不用。 | 1/3 | 0 | 0/0 | 3/3 | 已弃用 | we advocate its adoption as a standard loss in recommender systems（H1） |
| `specially tailored for X` | 专门为……定制（早期式）〔M4c 论文定语与方法修饰语〕。注：仅 H1。 | 1/3 | 0 | 0/0 | 0/0 | 已弃用 | a variant of InfoNCE, specially tailored for CF methods（H1） |
| `we frame X as Y` | 把……表述为……〔M4d 核心思想〕。注：仅 H1。 | 1/1 | 0 | 2/4 | 1/1 | 已弃用 | we frame hardness learning as an adversarial optimization problem（H1） |
| `The basic idea is to V` | 基本思想（早期式）〔M4d 核心思想〕。注：house只 H1；refA refA8、refA5 常用。 | 1/1 | 0 | 2/5 | 0/0 | 已弃用 | The basic idea is to routinely conduct heuristic-based data augmentation ...（H1） |
| `X, coined Y` | 命名（coined，早期式）〔M4b 命名〕。注：仅 H1；refB refB4 1。 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | This modified contrastive loss coined X, is explicitly designed for ...（H1） |
| `In summary, the main contributions of this work are as follows:` | 引言贡献列表（导师式）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 8/8 | 4/6 | 仅参考 |  |
| `The contributions of this work are summarized as follows: / In a nutshell, this work makes the following main contributions:` | 贡献列表引导句〔M4k 贡献列表与总结引导〕。注：refA 8/8、refB 4/8；house 0 次（6/6 篇终稿都不写贡献列表，改为证据路线图）。 | 0/0 | 0 | 8/8 | 4/4 | 仅参考 | [refA4] The contributions of this work are summarized as follows: |
| `To the best of our knowledge, we are the first to V` | 声称首创〔M4k 贡献列表与总结引导〕。注：house 0 次。 | 0/0 | 0 | 3/5 | 2/2 | 仅参考 | [refA7] To the best of our knowledge, ... |
| `a new method named X` | 命名（named，导师式）〔M4b 命名〕。注：house 0 次；refA 4/8 篇。 | 0/0 | 0 | 4/9 | 0/0 | 仅参考 | [refA4] We propose a new method named X（refA4） |
| `we develop a new model / solution, X` | 开发新模型（导师式）〔M4a 提出句〕 | 0/0 | 0 | 4/4 | 0/0 | 仅参考 | [refA6] In this work, we develop a new model, X（refA6） |
| `X sheds light on Y` | 带来启示（禁用）〔M4i 路线图后的收尾句〕。注：禁用。 | 2/5 | 1 | 0/0 | 3/4 | 禁用 | This result sheds light on using language representations for better recommendation.（H2） |
| `we delve into X` | 深入研究（禁用）〔M4a 提出句〕。注：禁用词；仅 H1。 | 1/3 | 0 | 0/0 | 3/5 | 禁用 | To bridge the gap, we delve into the reasons underpinning the success ...（H1） |
| `We believe (that) X ... will inspire / is an important stepping stone` | 我们相信（禁用）〔M4i 路线图后的收尾句〕。注：禁用。 | 1/2 | 0 | 2/2 | 5/7 | 禁用 | We believe that our X ... provides a valuable baseline ...（H1） |
| `which de facto concurrently ...` | 事实上（禁用）〔M4e Intro 中的机制展开〕。注：禁用。 | 1/1 | 0 | 0/0 | 1/1 | 禁用 | which de facto concurrently enlarges the similarity discrepancy ...（H1） |
| `the reasons underpinning X` | 支撑（禁用）〔M4a 提出句〕。注：禁用词。 | 1/1 | 0 | 0/0 | 0/0 | 禁用 | the reasons underpinning the success of contrastive loss in CF（H1） |
| `unleash the power of X` | 释放潜力（禁用）〔M4i 路线图后的收尾句〕。注：禁用。 | 1/1 | 1 | 0/0 | 0/0 | 禁用 | we discussed how to unleash the power of advanced language representations ...（H2） |

## M5 机制与公式

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `by V-ing ...` | 说明手段〔8 目的与手段〕。注：H4 11、H6 14。 | 6/41 | 5 | 6/18 | 6/15 | 常用 | ...regression function [M] by minimizing the following regularized loss: [EQ] Equation [REF] denotes the Lasso...（H3） |
| `For each X, we ...` | 逐个处理〔7 步骤顺序〕 | 6/32 | 5 | 8/47 | 7/29 | 常用 | For each question, we extract the [M] -dimentional residual stream activation [M] at the position...（H3） |
| `as follows: [EQ]` | 冒号接展示公式〔1 公式引入〕。注：6 篇都用；前面的动词可换 formulated / calculated / defined / conduct ... as follows。 | 6/26 | 5 | 5/16 | 3/8 | 常用 | ...in Section [REF] as follows: [EQ] The pre-allocation vectors yield positive reasoning token number predictions...（H3） |
| `To V X, we V ...` | 目的状语+做法〔8 目的与手段〕。注：house方法段最常见的段首句型。 | 6/25 | 5 | 6/14 | 6/11 | 常用 | To implement this Lasso regression, we use the Python package of scikit-learn [CITE].（H3） |
| `where [M] is (the / a) ...` | 公式后说明符号是什么〔2 公式后符号解释〕。注：6 篇都用； | 6/18 | 5 | 8/62 | 7/35 | 常用 | ... for avoiding overfitting, where [M] is a hyperparameter controlling the regularization strength.（H3） |
| `... (\ie [M]) / (\ie [M] == [M])` | 用公式复述文字〔5 命名与首次解释〕。注：H5 11、H6 13；把文字条件换成符号。 | 5/34 | 5 | 4/8 | 4/16 | 常用 | ...extracted four vectors (i.e. [M] ) as: [EQ] where [M] and [M] are the original...（H3） |
| `Under this / the X, ...` | 在某约束或设定下〔9 条件与分支〕。注：H4：Under this null-space constraint, we ensure that ... | 5/25 | 4 | 5/10 | 6/9 | 常用 | Under this null-space constraint, we ensure that the steering term vanishes for benign prompts ...（H4） |
| `we use X to V` | 使用〔8 目的与手段〕 | 5/19 | 5 | 8/42 | 6/26 | 常用 | Specifically, we use frozen LMs to transform pre-existing item title features [M] ...（H2） |
| `To ensure X, we ...` | 为保证〔8 目的与手段〕。注：H4 5 次。 | 5/10 | 4 | 7/13 | 5/9 | 常用 | ...X To ensure the benign prompts remain unaffected for utility preservation, we aim...（H4） |
| `[M] is a hyperparameter ...` | 说明是超参数〔3 超参数与项的说明〕。注：5 篇；导师 refA 0。 | 5/6 | 4 | 0/0 | 2/2 | 常用 | ... overfitting, where [M] is a hyperparameter controlling the regularization strength.（H3） |
| `The overall / total / final X (loss, reward, advantage, framework ...)` | 汇总量的名称〔10 组件与组合〕。注：方法末尾给出总量或总框架时的主语。 | 4/18 | 4 | 1/1 | 6/12 | 常用 | ... components and present the overall framework of the X method in Section [REF].（H4） |
| `X can be formulated as (follows): [EQ]` | 引出公式（首选）〔1 公式引入〕。注：house最常用的公式引入；H5/H6 各 4 次。冒号后直接接公式。 | 4/12 | 3 | 2/2 | 3/4 | 常用 | This reward at step [M] can be formulated as follows: [EQ]（H5） |
| `X (\ie the ...)` | 用定冠词短语解释〔5 命名与首次解释〕。注：H3：reasoning strength (i.e. the number of reasoning tokens)。 | 4/11 | 3 | 5/18 | 4/6 | 常用 | ...more reasoning strengths (i.e. the number of reasoning tokens) for harder problems, exhibiting difficulty-awareness for...（H3） |
| `[EQ] where [M] denotes (the) ...` | 公式后解释符号（首选）〔2 公式后符号解释〕。注：house最常用的 notation 句；多个符号用 , and [M] denotes ... 串联。 | 4/10 | 3 | 5/12 | 4/7 | 常用 | ...formulation [M] , where [M] denotes the activation and [M] is a trainable transformation matrix...（H4） |
| `we compute X` | 计算〔8 目的与手段〕 | 4/6 | 4 | 1/1 | 1/2 | 常用 | Specifically, we compute the difference-in-means vector [M] between difficulty the hardest level [M] and easiest...（H3） |
| `[EQ] Here(,) [M] denotes / is ...` | 公式后另起句解释〔2 公式后符号解释〕。注：house自有（refA 0）；比 where 更适合长解释。 | 4/6 | 3 | 0/0 | 2/2 | 常用 | ...(SVD) as follows: [EQ] Here [M] is the orthonormal eigenvector matrix of [M] where each...（H4） |
| `... as [M] , where [M] ... (inline)` | 行内公式+where〔2 公式后符号解释〕。注：当前 4 篇都用；导师 refA 5 篇。 | 4/5 | 4 | 5/8 | 3/3 | 常用 | ...can be formulated as [M] , where [M] is a learnable transformation matrix and [M]...（H4） |
| `..., [M] , indicating ...` | 分词补充符号含义〔2 公式后符号解释〕。注：H5：two binary gating signals, [M] and [M], indicating whether to ... | 4/5 | 3 | 2/2 | 2/2 | 常用 | ...status [M] is [M] , indicating the last evidence required occurs in this chunk, the...（H5） |
| `we adopt the same X as (introduced in) ...` | 采用相同设置〔12 沿用已有做法〕。注：house自有（导师 0）。 | 4/5 | 3 | 0/0 | 0/0 | 常用 | ... the outcome reward, we adopt the same reward as introduced in Equation [REF].（H5） |
| `..., denoted as [M]` | 引入记号〔2 公式后符号解释〕。注：H2、H5、H6；变体 which can be denoted as a triplet / each denoted as。 | 4/4 | 3 | 1/1 | 3/5 | 常用 | ... which can be denoted as a triplet [M] .（H5） |
| `..., and [M] denotes ...` | 串联第二个符号〔2 公式后符号解释〕。注：H6：where [M] denotes the CoT process, and [M] denotes the predicted final answer。 | 4/4 | 3 | 3/3 | 1/1 | 常用 | ... the predicted answer, and [M] denotes the indicator function.（H5） |
| `Specifically, the X is V-ed to ...` | 展开：某组件怎么做〔7 步骤顺序〕。注：H4：Specifically, the transformation matrix [M] is optimized to satisfy ... | 3/7 | 2 | 4/6 | 8/15 | 常用 | Specifically, the transformation matrix [M] is optimized to satisfy the following two core objectives: utility...（H4） |
| `After V-ing X, we ...` | 完成上一步后〔7 步骤顺序〕。注：H6：After computing role-specific rewards, we optimize ... | 3/7 | 3 | 1/1 | 1/1 | 常用 | After processing all chunks, an answer agent [M] predicts the answer [M] conditioned on the...（H5） |
| `That is, ...` | 即、换言之〔6 核心思路与直觉〕。注：H2、H3、H5（That is, most of the chunks do not contain ...）。 | 3/6 | 3 | 7/17 | 6/11 | 常用 | That is, most of the chunks do not contain any information for answering ...（H5） |
| `Formally, (given X,) this X can be defined as follows: [EQ]` | 转入形式化表述〔1 公式引入〕。注：house 3 篇各 1-3 次；导师远更多（refA 25、refB 17），house只在 Prelim 或 Method 首个公式前用一次。 | 3/5 | 2 | 7/25 | 7/17 | 常用 | Formally, given a dataset of [M] samples, we construct an activation matrix [M] where each...（H3） |
| `X consists of Y` | 由…组成〔10 组件与组合〕 | 3/5 | 3 | 6/12 | 6/12 | 常用 | ...prompts, and [M] consists of [M] identical copies of the same refusal direction vector stacked...（H4） |
| `Here [M] is set as 0.2 by default in our paper.` | 给默认取值〔3 超参数与项的说明〕。注：H6：Here [M] is set as 0.2 by default in our paper。 | 3/5 | 2 | 5/7 | 3/3 | 常用 | ... set as 0.2 by default in our paper.（H6） |
| `X is calculated as (follows): [EQ]` | 计算量的公式引入〔1 公式引入〕。注：用于 reward、advantage、projection matrix 等计算量；导师 0 次，house自有。 | 3/5 | 3 | 0/0 | 0/0 | 常用 | ...null-space projection matrix is calculated as: [EQ] [M] projects [M] into the null space of...（H4） |
| `where [M] and [M] are the X and Y ..., respectively` | 成对符号一起解释〔2 公式后符号解释〕。注：house说 are，不说 denote（where [M] and [M] denote house 0）。 | 3/4 | 3 | 4/7 | 6/8 | 常用 | ...Lasso regression [CITE], where [M] and [M] are the learnable parameters of this linear regression...（H3） |
| `[M] is the number of ...` | 说明计数符号〔2 公式后符号解释〕 | 3/4 | 2 | 2/3 | 1/2 | 常用 | ... [M] , where [M] is the number of output tokens in this conversation.（H5） |
| `Finally, the overall / total X is the combination of ...` | 最后汇总〔7 步骤顺序〕。注：H6：Finally, the overall reward for the reasoner is the combination of ... | 3/4 | 3 | 1/1 | 0/0 | 常用 | Finally, the total advantage [M] in Equation [REF] is a combination of these two terms:...（H5） |
| `[M] is the set of ...` | 说明集合符号〔2 公式后符号解释〕 | 3/3 | 2 | 3/8 | 5/10 | 常用 | ...as null space) [M] is the set of all vectors [M] such that [M] :...（H4） |
| `To implement X, we use ...` | 实现方式〔8 目的与手段〕。注：H3：To implement this Lasso regression, we use the Python package of scikit-learn。 | 3/3 | 3 | 2/4 | 2/2 | 常用 | To implement this Lasso regression, we use the Python package of scikit-learn [CITE].（H3） |
| `Otherwise, ...` | 否则〔9 条件与分支〕 | 3/3 | 3 | 1/2 | 2/2 | 常用 | ... similarities exceed thresholds; otherwise, [M] is set to zero.（H4） |
| `... [M] if X, and [M] otherwise` | 分段取值〔9 条件与分支〕。注：H2、H5、H6（which returns [M] if ... and [M] otherwise）。 | 3/3 | 3 | 2/2 | 0/0 | 常用 | ... answer [M] , and [M] otherwise.（H5） |
| `... to avoid overfitting / for avoiding overfitting` | 说明正则项目的〔3 超参数与项的说明〕。注：H2、H3、H4。 | 3/3 | 3 | 2/2 | 0/0 | 常用 | ...[M] is introduced for avoiding overfitting, where [M] is a hyperparameter controlling the regularization strength...（H3） |
| `To achieve this (goal), we ...` | 为达此目的〔8 目的与手段〕。注：H4、H5、H6。 | 3/3 | 3 | 1/1 | 1/1 | 常用 | To achieve this, we aim to steer activations of malicious prompts toward refusal.（H4） |
| `Once V-ed / V-ing X, ...` | 一旦完成某步〔7 步骤顺序〕。注：H4：Once deriving this null space projection matrix [M], we can thereby ensure ... | 3/3 | 3 | 0/0 | 0/0 | 常用 | Once deriving this null space projection matrix [M] , we can thereby ensure [M] [CITE].（H4） |
| `at (each) step [M], ...` | 按步描述循环过程〔7 步骤顺序〕。注：H5、H6。 | 2/9 | 2 | 2/8 | 0/0 | 常用 | At step [M] , a memory agent [M] reads the question [M] , chunk [M]...（H5） |
| `only when X` | 仅当〔9 条件与分支〕。注：H4 4、H5 5。 | 2/9 | 2 | 0/0 | 0/0 | 常用 | ...the refusal vector only when input activations exceed a predefined threshold, which is intended to...（H4） |
| `..., as introduced in X [CITE] / Equation [REF]` | 指向已有定义〔12 沿用已有做法〕。注：H5 6 次。 | 2/7 | 2 | 2/2 | 0/0 | 常用 | ... recurrent memory paradigm, as introduced in previous work MemAgent [CITE], in Section [REF].（H5） |
| `we extract X at / from ...` | 提取〔8 目的与手段〕。注：H3、H4。 | 2/4 | 2 | 4/5 | 0/0 | 常用 | For each question, we extract the [M] -dimentional residual stream activation [M] at the position...（H3） |
| `X and Y share the same Z` | 共享参数或奖励〔10 组件与组合〕。注：H5 3、H6 1。 | 2/4 | 2 | 2/2 | 0/0 | 常用 | ...answer agent [M] share the same parameterized policy model [M] , with their behaviors differentiated...（H5） |
| `X is (a / the) combination of Y and Z` | 组合多项〔10 组件与组合〕。注：H5、H6（total reward / total advantage）。 | 2/4 | 2 | 1/1 | 0/0 | 常用 | ...format reward, they can be combined as one total trajectory-level reward [M] as follows: [EQ]...（H5） |
| `two / three components` | 组件数〔10 组件与组合〕。注：H2、H4；导师 refA 8 篇都用。 | 2/3 | 2 | 8/11 | 4/4 | 常用 | ...efforts primarily target two components, [M] and [M] , through strategies categorized as vector calibration...（H4） |
| `X is commonly known as / also referred to as Y [CITE]` | 借用文献已有名称〔5 命名与首次解释〕。注：H2、H5（commonly known as the needle in a haystack problem）。 | 2/3 | 2 | 1/1 | 2/3 | 常用 | ... reasoning is also commonly known as the needle in a haystack (NIAH) [CITE] problem.（H5） |
| `..., which can be formulated as: [EQ]` | 定语从句引出公式〔1 公式引入〕。注：接在描述过程的长句末尾（H5 Prelim、H6 Prelim）。 | 2/3 | 2 | 1/1 | 2/2 | 常用 | ... status [M] , which can be formulated as [M] .（H5） |
| `[M] is a (scalar) hyperparameter controlling the X` | 说明超参数控制什么〔3 超参数与项的说明〕。注：H3 2、H4 1（H4 用 a scalar hyperparameter controlling the steering strength）。 | 2/3 | 2 | 1/1 | 1/1 | 常用 | ... [M] is a hyperparameter controlling the regularization strength.（H3） |
| `By varying [M] from 1 to 5, we can get ...` | 遍历参数得到结果〔7 步骤顺序〕。注：H2、H3。 | 2/3 | 2 | 2/2 | 0/0 | 常用 | By varying the target difficulty [M] from 1 to 5, we can get four such...（H3） |
| `otherwise, [M] is set to zero` | 设定取值〔3 超参数与项的说明〕。注：H4、H5；导师 refB 4 篇多用于实验设定。 | 2/2 | 2 | 0/0 | 4/8 | 常用 | ... thresholds; otherwise, [M] is set to zero.（H4） |
| `we can get X` | 得到结果〔7 步骤顺序〕。注：H3、H4。 | 2/2 | 2 | 1/1 | 3/5 | 常用 | ...1 to 5, we can get four such vectors at each layer, namely [M] ,...（H3） |
| `X can be expressed as [M], where ...` | 行内公式表述〔1 公式引入〕。注：常与 where 连用，公式放行内。 | 2/2 | 2 | 1/1 | 1/3 | 常用 | ...this vector [M] can be expressed as follows: [EQ] where the first and second terms...（H4） |
| `We calculate X as ...` | 计算为〔8 目的与手段〕。注：H3、H5。 | 2/2 | 2 | 0/0 | 2/3 | 常用 | We calculate [M] as the number of tokens between the start-of-reasoning token <think> and the...（H3） |
| `X is optimized to V / by V-ing` | 被优化以〔8 目的与手段〕。注：H4、H6。 | 2/2 | 2 | 1/2 | 1/1 | 常用 | ...transformation matrix [M] is optimized to satisfy the following two core objectives: utility preservation and...（H4） |
| `..., so that ...` | 以便〔8 目的与手段〕。注：house H4、H5 各 1； | 2/2 | 2 | 1/1 | 1/1 | 常用 | ...this reward strict so that only when all the generation output [M] meets the format...（H5） |
| `X parameterized by [M]` | 参数化〔10 组件与组合〕。注：H2、H6。 | 2/2 | 2 | 2/2 | 0/0 | 常用 | ... and frozen model parameterized by [M] .（H6） |
| `..., indicating whether to V` | 说明信号含义〔9 条件与分支〕。注：H5、H6。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | ...and [M] , indicating whether to update the memory and whether to terminate the loop,...（H5） |
| `This is inspired by recent works of ... [CITE]` | 说明设计来源〔12 沿用已有做法〕。注：H4、H5。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | This is inspired by recent works of disentangling advantage calculation for stabilizing the training with...（H5） |
| `(\eg via PCA)` | 括号举实现方式〔5 命名与首次解释〕。注：H4、H6。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...extracting principal components (e.g. via PCA) [CITE] or subtracting components associated with false refusals [CITE]...（H4） |
| `..., which (in turn) enables ...` | 从而使得〔11 设计理由与效果〕。注：H2、H6；H4 另有 This equivalence enables efficient computation。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ... CoT supervision criteria, which in turn enables the self-evolution of CoT rewarding.（H6） |
| `..., which is calculated as follows: [EQ]` | 定语从句引出计算式〔1 公式引入〕。注：H5：Here, the two advantages are calculated as follows。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...two advantages are calculated as follows: [EQ] As shown in Figure [REF], the trajectory-level advantage...（H5） |
| `where the first and second terms denote ...` | 解释公式中的两项〔2 公式后符号解释〕。注：H3、H4 都用于 difference-in-means 公式。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...as: [EQ] where the first and second terms denote the mean activations at layer [M]...（H3） |
| `X is defined as Y` | 定义某量〔1 公式引入〕。注：house多在附录用；导师更常用（refA 6 篇、refB 5 篇）。 | 2/6 | 1 | 6/11 | 5/7 | 偶用 | ... of a matrix is defined as its left null space [CITE].（H4） |
| `[M] will be V-ed ...` | 描述流程结果（将被）〔7 步骤顺序〕。注：H1 1、H5 4。 | 2/5 | 1 | 0/0 | 0/0 | 偶用 | ..., the memory [M] will be updated with the the candidate memory [M] , otherwise...（H5） |
| `This reward / This process is formulated as (follows): [EQ]` | 被动引出公式（无 can）〔1 公式引入〕。注：H5 3 次、H1 1 次（This reward is formulated as:）；导师 refA 6 篇、refB 5 篇都用。 | 2/4 | 1 | 6/7 | 5/6 | 偶用 | This process is formulated as follows: [EQ] To achieve this, the memory agent [M] responds...（H5） |
| `..., which is commonly referred to as X [CITE]` | 借用已有叫法〔5 命名与首次解释〕。注：H1 3、H5 1。 | 2/4 | 1 | 0/0 | 1/1 | 偶用 | ... which is commonly referred to as lost in the middle [CITE].（H5） |
| `the following optimization problem / objective: [EQ]` | 引出优化问题〔1 公式引入〕。注：H1、H4；多为 we ... by minimizing the following regularized loss。 | 2/4 | 1 | 1/1 | 0/0 | 偶用 | ... optimize the problem in Equation [REF] with the following objective function [M] :（H4） |
| `where the second term [M] serves as a regularization ...` | 说明某项的作用〔2 公式后符号解释〕。注：H4：serves as a regularization with Frobenius norm to avoid overfitting。 | 2/3 | 1 | 5/8 | 4/7 | 偶用 | ...second term [M] serves as a regularization with Frobenius norm to avoid overfitting and [M]...（H4） |
| `[M] represents ...` | 符号代表〔2 公式后符号解释〕。注：house少用；导师多（refB refB5 6 次）。 | 2/3 | 1 | 5/7 | 3/8 | 偶用 | ...[M] , and [M] represents the same underlying policy model [M] instantiated under the prompt...（H6） |
| `We define [M] and [M] over these rollouts as: [EQ]` | 定义某量〔4 记号设定与定义〕。注：H1、H6；导师 refA 7 篇。 | 2/3 | 1 | 7/10 | 1/1 | 偶用 | We define [M] and [M] over these [M] rollouts as: [EQ] The intuition is that...（H6） |
| `[M] refers to ...` | 符号指代〔2 公式后符号解释〕。注：H5：[M] refers to the importance sampling weight。 | 2/3 | 1 | 2/6 | 3/9 | 偶用 | ...[CITE], and [M] refers to the importance sampling weight: [EQ] In this vanilla approach, all...（H5） |
| `X comprises Y` | 包含〔10 组件与组合〕。注：H1、H4。 | 2/3 | 1 | 1/1 | 3/4 | 偶用 | ... refusal direction vector comprises multiple semantically entangled sub-directions responsible for different refusal reasons [CITE].（H4） |
| `To satisfy the constraint in Equation [REF], we ...` | 为满足约束〔8 目的与手段〕。注：H1、H4。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | To satisfy the constraint in [REF], we follow previous works [CITE] ...（H4） |
| `For simplicity, we V ...` | 简化设定〔4 记号设定与定义〕。注：H1、H2；之后未再用。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 4/4 | 0/0 | 偶用 | For simplicity, we adopt text-embeddings-3-large [CITE] for language representation generation by default, for its excellent...（H2） |
| `The closed-form solution to this optimization problem is given by: [EQ]` | 给出解/结果〔1 公式引入〕。注：H4 用于闭式解，H1 用于梯度。 | 2/2 | 1 | 1/1 | 2/2 | 偶用 | ... this optimization problem is given by: [EQ] where [M] denotes the pseudoinverse.（H4） |
| `X is considered valid if ...` | 判定条件〔9 条件与分支〕。注：H1、H6。 | 2/2 | 1 | 0/0 | 2/2 | 偶用 | ...a rubric [M] is considered valid if it is informative as a rewarding criterion: (i)...（H6） |
| `For brevity, ...` | 为简洁起见〔4 记号设定与定义〕。注：H1、H6（Format reward is ignored for brevity）。 | 2/2 | 1 | 0/0 | 1/1 | 偶用 | ... reward is ignored for brevity.（H6） |
| `... from now on` | 此后沿用该设定〔4 记号设定与定义〕。注：H4 的 1 次是附录中引用的越狱提示词，不算house行文；实际只 H1 用。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | ... we consider a single observed user-item pair [M] from now on.（H1） |
| `Let [M] be ...` | 引入数学对象〔4 记号设定与定义〕。注：house只 H4（lemma 与附录）；导师 refA 4 篇、refB 5 篇； | 1/4 | 1 | 4/8 | 5/11 | 偶用 | ... following lemma: ] Let [M] be a high-dimensional utility activation matrix.（H4） |
| `Similarly, ...` | 同理〔11 设计理由与效果〕。注：只 H3。 | 1/2 | 1 | 3/3 | 3/4 | 偶用 | Similarly, the correlation coefficient reaches over 0.8, indicating that the reasoning strength can be predicted...（H3） |
| `Such a reward (design) enables / encourages ...` | 公式后说明设计效果〔11 设计理由与效果〕。注：H6 2 次。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...CoT reward: [EQ] Such a reward enables autonomously optimizing what LLMs answer and how LLMs...（H6） |
| `..., so as to V` | 以便（so as to）〔8 目的与手段〕。注：house只 H6 1；导师 refA 7 篇常用。 | 1/1 | 1 | 7/26 | 3/8 | 偶用 | ...rubric generation capability, so as to progressively produce more informative and challenging rubrics that mitigate...（H6） |
| `a ... model termed X` | 给方法命名〔5 命名与首次解释〕。注：不带括号的只 H2 1 次（model termed X）；H4 的 2 次在括号里，见下一条。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 5/13 | 5/6 | 偶用 | we aim to develop a simple yet effective CF model termed X ...（H2） |
| `(e.g. [M])` | 括号给参数例值〔3 超参数与项的说明〕。注：house只 H6 1 次；导师 refA 6 篇常用。 | 1/1 | 1 | 6/14 | 3/5 | 偶用 | ... the clipping hyperparameter (e.g. [M] ).（H6） |
| `we follow ...` | 遵循〔12 沿用已有做法〕。注：H4 1；导师 refB 3 篇。 | 1/1 | 1 | 2/3 | 3/9 | 偶用 | ...constraint in [REF], we follow previous works [CITE] to construct a null-space projection matrix [M]...（H4） |
| `Next, it V ...` | 下一动作〔7 步骤顺序〕。注：H5 单次。 | 1/1 | 1 | 2/3 | 3/4 | 偶用 | Next, it outputs the candidate memory [M] within [M] and [M] .（H5） |
| `consisting of` | 由…组成（分词）〔10 组件与组合〕。注：H4 单次。 | 1/1 | 1 | 2/2 | 1/1 | 偶用 | ...is a matrix consisting of [M] activation vectors sampled from the benign prompts set [M]...（H4） |
| `The intuition is that ...` | 公式后的直觉解释〔6 核心思路与直觉〕。注：H6：The intuition is that higher correlation implies ... | 1/1 | 1 | 3/3 | 0/0 | 偶用 | ...rollouts as: [EQ] The intuition is that higher correlation implies that satisfying the rubric is...（H6） |
| `..., which is short for X` | 说明缩写全称〔5 命名与首次解释〕。注：H4 单次；refA 2 篇。 | 1/1 | 1 | 2/2 | 0/0 | 偶用 | ... baseline, which is short for RV, and one activation-based method Circuit Breaker [CITE].（H4） |
| `[M] denotes the indicator function` | 指示函数〔2 公式后符号解释〕。注：H5 单次。 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | ... [M] denotes the indicator function.（H5） |
| `Concretely, ...` | 具体来说〔7 步骤顺序〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | Concretely, the entire long context [M] is divided into [M] fixed-size chunks [M] first.（H5） |
| `Equation [REF] denotes the X` | 说明整条公式是什么〔2 公式后符号解释〕。注：H3 单次。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ...regularized loss: [EQ] Equation [REF] denotes the Lasso regression [CITE], where [M] and [M] are...（H3） |
| `Following X [CITE], we V ...` | 沿用某方法〔12 沿用已有做法〕。注：H6：Following DAPO [CITE], we assign a binary outcome reward ... | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ...as follows: [EQ] Following DAPO [CITE], we assign a binary outcome reward based on answer...（H6） |
| `The matrix form is shown as follows: [EQ]` | 给出矩阵形式〔1 公式引入〕。注：H4 单次。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | The matrix form is shown as follows: [EQ] where [M] is a matrix consisting of...（H4） |
| `where [M] means ...` | 说明取值的含义〔2 公式后符号解释〕。注：H5：where [M] means continuing the recurrent loop。 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | ...and [M] , where [M] means continuing the recurrent loop and [M] means terminating the...（H5） |
| `X is computed by aggregating ...: [EQ]` | 说明计算方式〔1 公式引入〕。注：H6：The CoT reward is computed by aggregating the scores ... | 1/1 | 1 | 1/1 | 0/0 | 偶用 | The CoT reward is computed by aggregating the scores of the satisfied rubrics: [EQ] where...（H6） |
| `..., which is an extension of X [CITE] in Y` | 说明算法来源〔12 沿用已有做法〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...in MemAgent [CITE], which is an extension of the group relative policy optimization (GRPO) algorithm...（H5） |
| `[M] is a hyperparameter called temperature [CITE]` | 带名称的超参数〔3 超参数与项的说明〕。注：只 H2。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...[M] is a hyperparameter called temperature [CITE], [M] denoting the observed interactions between users [M]...（H2） |
| `In our case, ...` | 落到本文设定〔4 记号设定与定义〕。注：H3 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In our case, we construct contrasting data pairs with questions of different difficulties, since LRMs...（H3） |
| `its left null space (abbreviated as null space)` | 给缩写〔5 命名与首次解释〕。注：H4 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...null space (abbreviated as null space) [M] is the set of all vectors [M]...（H4） |
| `Specifically, as shown in Figure [REF], at each step [M] ...` | 展开并指图〔7 步骤顺序〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Specifically, as shown in Figure [REF], at each step [M] the memory agent [M] takes...（H5） |
| `the formal definition of X is given as follows` | 引出正式定义〔4 记号设定与定义〕。注：H4 单次（后接定义环境）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...null space is given as follows [CITE]: ] Given a matrix [M] , its left...（H4） |
| `The key idea lies in V-ing ..., and then V-ing ...` | 核心思路（lies in）〔6 核心思路与直觉〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The key idea lies in treating each conversation (i.e. one individual memory or answer turn)...（H5） |
| `This can be done by V-ing ...` | 说明怎么实现〔8 目的与手段〕。注：H4 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This can be done by reconstructing refusal direction vectors based on the malicious activations, which...（H4） |
| `This means ...` | 说明含义〔11 设计理由与效果〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This means the memory agent is required to generate [M] end [M] (i.e. [M] ==...（H5） |
| `This strict design is because ...` | 解释设计原因〔11 设计理由与效果〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This strict design is because we can not infer whether the incorrect format is caused...（H5） |
| `where [M] is a hyperparameter for balancing two terms` | 平衡两项的超参数〔3 超参数与项的说明〕。注：H5 单次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ... is a hyperparameter for balancing two terms.（H5） |
| `while keeping X unchanged` | 保持其他不变〔9 条件与分支〕。注：H5 单次；unchanged 在 4 篇共 15 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...memory agent [M] while keeping the answer agent [M] unchanged, extending the vanilla [M] with...（H5） |
| `With above definition, X is calculated as: [EQ]` | 承接定义后计算〔1 公式引入〕。注：原文缺冠词（应为 With the above definition）；借用时补 the。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | With above definition, the null-space projection matrix is calculated as: [EQ] [M] projects [M] into...（H4） |
| `With the obtained X, the final Y of Z is: [EQ]` | 汇总成最终公式〔1 公式引入〕。注：H4 在方法末尾整合各组件时用。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...layer [M] , the final steering function of X is: [EQ] Grounded in null-space projection...（H4） |
| `X is obtained as the average of ...: [EQ]` | 说明由什么得到〔1 公式引入〕。注：只在 H2。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...(item [M] ) is obtained as the average of representations from each layer: [EQ] Contrastive...（H2） |
| `X is written as: [EQ]` | 写出已知公式〔1 公式引入〕。注：只在 H2（InfoNCE loss is written as）。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...InfoNCE loss [CITE] is written as: [EQ] Here, [M] is a hyperparameter called temperature [CITE],...（H2） |
| `denoted by [M]` | 记号引入（by）〔2 公式后符号解释〕。注：只在 H1；后期改用 denoted as。 | 1/2 | 0 | 1/1 | 3/4 | 已弃用 | ... of negative instances, denoted by [M] , from the unobserved item set [M] .（H1） |
| `if and only if` | 当且仅当〔9 条件与分支〕。注：只 H1。 | 1/1 | 0 | 2/2 | 2/2 | 已弃用 | ... the equality holds if and only if the interactions [M] are ranked top-K.（H1） |
| `Let [M] denote ...` | 引入记号〔4 记号设定与定义〕。注：只 H1；导师 refB 2 篇。 | 1/1 | 0 | 0/0 | 2/2 | 已弃用 | ...abuse of notation, let [M] denote the relative hardness of negative item [M] in contrast...（H1） |
| `Without loss of generality, ...` | 不失一般性〔4 记号设定与定义〕。注：只 H1。 | 1/1 | 0 | 1/1 | 1/1 | 已弃用 | ... to be [M] without loss of generality.（H1） |
| `[M] stands for ...` | 符号代表〔2 公式后符号解释〕。注：只在 H1。 | 1/1 | 0 | 1/1 | 0/0 | 已弃用 | ...[EQ] where [M] stands for the distribution of uniformly drawn negative samples, i.e. [M] ;...（H1） |
| `Here, with a slight abuse of notation, let [M] denote ...` | 复用记号〔4 记号设定与定义〕。注：只 H1（refB refB4 1）。 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | ...problem: [EQ] Here, with a slight abuse of notation, let [M] denote the relative hardness...（H1） |
| `Analogously, ...` | 类似地〔11 设计理由与效果〕。注：导师专用（refA 5 篇、refB 4 篇），house 0。 | 0/0 | 0 | 5/10 | 4/8 | 仅参考 |  |
| `More formally, ...` | 更形式化地〔1 公式引入〕。注：导师专用（refA 3 篇、refB 3 篇），house 0。 | 0/0 | 0 | 3/9 | 3/4 | 仅参考 |  |
| `X is represented as [M]` | 表示为〔1 公式引入〕。注：导师专用（refA 4 篇、refB 2 篇），house 0。 | 0/0 | 0 | 4/4 | 2/3 | 仅参考 |  |
| `iteratively` | 迭代地〔7 步骤顺序〕。注：导师专用（refA 2 篇、refB 3 篇），house 0。 | 0/0 | 0 | 2/6 | 3/9 | 仅参考 |  |
| `..., which consists of two components` | 方法组成（导师式）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 2/3 | 3/6 | 仅参考 |  |
| `we minimize X` | 最小化目标〔1 公式引入〕。注：导师专用（refB 3 篇），house 0；house说 by minimizing the following ... loss。 | 0/0 | 0 | 1/2 | 3/4 | 仅参考 |  |
| `X is composed of Y` | 由…构成〔10 组件与组合〕。注：导师专用（refA 2 篇、refB 2 篇），house 0。 | 0/0 | 0 | 2/4 | 2/2 | 仅参考 |  |
| `with parameters [M]` | 带参数〔10 组件与组合〕。注：导师专用（refA 2 篇、refB 1 篇），house 0；house用 parameterized by。 | 0/0 | 0 | 2/3 | 1/1 | 仅参考 |  |
| `two key components` | 两个关键组件〔10 组件与组合〕。注：导师专用（refA 3 篇），house 0。 | 0/0 | 0 | 3/3 | 0/0 | 仅参考 |  |
| `we use [M] to denote X` | 用某符号表示〔4 记号设定与定义〕。注：导师专用（refA 2 篇），house 0。 | 0/0 | 0 | 2/2 | 0/0 | 仅参考 |  |
| `where [M] and [M] denote ...` | 成对符号解释（denote）〔2 公式后符号解释〕。注：导师 refA 2 篇；house 0，house用 where [M] and [M] are。 | 0/0 | 0 | 2/2 | 0/0 | 仅参考 |  |
| `X is formalized as: [EQ]` | 形式化为〔1 公式引入〕。注：导师各 1 篇，house 0。 | 0/0 | 0 | 1/1 | 1/1 | 仅参考 |  |
| `Wherein ...` | （禁用）句首 Wherein〔2 公式后符号解释〕。注：禁用词；导师 refA 5 篇、refB 5 篇用，house只 H1。 | 1/2 | 0 | 5/6 | 4/6 | 禁用 | Wherein, a common assumption underlying these methods is considering all unobserved interactions as negative signals...（H1） |

## M6 理论与分析

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `Then: [EQ] / Then [M] is X` | 句首接下一步〔M6.6 证明步骤〕。注：house 5 篇；证明中 H4 "Then: [EQ] Thus, [M]."。 | 5/14 | 4 | 3/5 | 6/11 | 常用 | Then: [EQ] This satisfies the benign constraint in [REF]（H4） |
| `Details about how to derive X can be found in Appendix [REF].` | 指向附录细节〔M6.5 证明的指引〕。注：5 篇house论文；H4 "Details about how to derive [M] can be found in Appendix"。 | 5/14 | 4 | 0/0 | 3/5 | 常用 | Details about how to derive [M] can be found in Appendix [REF].（H4） |
| `First, suppose [M], so [M]. Then: [EQ] ... Conversely, suppose [M], so [M].` | 证明另一方向〔M6.6 证明步骤〕。注：Conversely house 5 篇（一般用法）；证明中 H4 1 次；H6:1 为附录 rubric 提示词文本；真实用法 H1/H2/H3/H4 4 篇。 | 5/7 | 4 | 0/0 | 2/3 | 常用 | Conversely, suppose [M] , so [M] .（H4） |
| `Specifically, X is proportional to Y, indicating that Z` | 由结论得出含义〔M6.7 命题后的解读〕。注：house 4 篇（H1 3、H3 4） | 4/10 | 3 | 4/5 | 4/5 | 常用 | proportional to the hardness term [M] , indicating that X is a hardness-aware loss.（H1） |
| `By analyzing X, we have the following findings/observations: (列表)` | 引出多条解读〔M6.7 命题后的解读〕。注：house 4 篇（多用于实验；H3 用于分析向量："By analyzing these vectors, we have the following findings:"）。 | 4/10 | 4 | 7/15 | 0/0 | 常用 | By analyzing these vectors, we have the following findings:（H3） |
| `In practice, [M] is regulated/controlled by X` | 理论量在实现中的对应〔M6.3 陈述假设〕。注：house 4 篇；H1 "In practice, [M] is regulated by the number of ... epochs"。 | 4/8 | 3 | 1/1 | 3/4 | 常用 | In practice, [M] is regulated by the number of adversarial training epochs（H1） |
| `Under this X constraint / Under X, we ensure that Y` | 在某约束/设定下〔M6.3 陈述假设〕。注：house 4 篇；H4 "Under this null-space constraint, we ensure that ..."；ps.py 计数中 H3:1 为附录中模型输出文本（Kant 例题），真实用法 H4/H5/H6 3 篇。 | 4/5 | 4 | 0/0 | 2/2 | 常用 | Under this null-space constraint, we ensure that the steering term vanishes for benign prompts（H4） |
| `Then, X is equivalent to Y` | 陈述等价关系〔M6.4 陈述命题/等价/闭式解〕。注：H1 5、H4 1、H6 1；定理结论最常用的动词。 | 3/7 | 2 | 0/0 | 3/4 | 常用 | is equivalent to the null space of its non-central covariance matrix (X)（M） |
| `We define [M], where [M] is X / Define [M] as X` | 定义量〔M6.2 记号与设定〕。注：H1 4、H4 1、H6 1；H4 证明中 "For any arbitrary [M], define [M]."。 | 3/6 | 2 | 0/0 | 0/0 | 常用 | We define [M] , where [M] is the probability of sampling negative item [M]（H1） |
| `These results validate the X of Y` | 实验验证〔M6.8 理论与实验对照〕。注：house 3 篇；refB 4 篇（refB4 "validate the property of adaptive weights in Theorem"） | 3/5 | 2 | 0/0 | 4/9 | 常用 | Second, we validate the effectiveness of the two introduced gating mechanisms, where X can reduce（H5） |
| `X, with other tasks (e.g., Y) largely underexplored` | 其余情形很少研究〔M6.9 分析的局限〕。注：H5 1 次；H1 用 under-explored。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | However, its application in the recommendation domain remains largely underexplored.（H2） |
| `..., ensuring X / thereby guaranteeing X` | 说明结论的保证〔M6.7 命题后的解读〕。注：H4 3、H6 1。 | 2/4 | 2 | 0/0 | 3/6 | 常用 | This satisfies the benign constraint ..., ensuring a zero steering term for benign activations.（H4） |
| `For any arbitrary [M], define [M].` | 任意取值〔M6.6 证明步骤〕。注：H4 2 次。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 0/0 | 1/1 | 常用 | For any arbitrary [M] , define [M] .（H4） |
| `..., we obtain: [EQ]` | 得到结果〔M6.6 证明步骤〕。注：H2、H4；refA 7 篇（多为 "we obtain the representations"） | 2/2 | 2 | 7/11 | 1/1 | 常用 | By rearranging the above equation, we obtain: [EQ]（H4） |
| `X is independent of the potentially large Y` | 与某量无关〔M6.7 命题后的解读〕。注：H4 1；refA 4 篇。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 4/5 | 2/2 | 常用 | is of size [M] , independent of the potentially large sample size [M] . (X)（M） |
| `This result ensures that X, leaving Y unchanged.` | 解读结论〔M6.7 命题后的解读〕。注：H4、H3。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | This result ensures that the steering transformation produces a zero steering term（H4） |
| `Theorem/Lemma [REF] (正文回指)` | 回指命题〔M6.4 陈述命题/等价/闭式解〕。注：H1 (Theorem)、H4 (Lemma)；house从未用 Proposition/Corollary 字样。 | 2/6 | 1 | 0/0 | 3/11 | 偶用 | Building on Lemma [REF], we now present the computation process of [M] .（H4） |
| `X assume(s) that Y` | 陈述前人/方法的假设〔M6.3 陈述假设〕。注：H1 2、H4 1；导师 9 篇（多为 "CF assumes that ..."） | 2/3 | 1 | 5/6 | 4/8 | 偶用 | These methods assume that the refusal direction vector comprises multiple semantically entangled sub-directions responsible for（H4） |
| `X is proportional to Y / correlate proportionally to Y` | 比例关系〔M6.7 命题后的解读〕。注：H1 2、H3 1。 | 2/3 | 1 | 1/1 | 3/3 | 偶用 | and is approximately proportional to the average additional reasoning token number required to solve the（H3） |
| `we can rewrite X as: [EQ] / X can be rewritten in a different form` | 改写〔M6.4 陈述命题/等价/闭式解〕。注：H1 2、H4 1。 | 2/3 | 1 | 1/1 | 2/2 | 偶用 | Using [M] , we rewrite: [EQ] Gradient and stationarity.（H4） |
| `Proof of Theorem/Lemma [REF] (附录小节标题)` | 证明小节标题〔M6.5 证明的指引〕。注：H1、H4；refB refB2、refB4 同。 | 2/3 | 1 | 0/0 | 2/3 | 偶用 | Proof of Lemma [REF] Consider the problem of establishing the equivalence between the null spaces（H4） |
| `Suppose (that) X` | 证明中的假设〔M6.3 陈述假设〕。注：H1 1、H4 2（证明内 "First, suppose ..., so ..."）；refB refB4 定理用 "Suppose that ..."。 | 2/3 | 1 | 0/0 | 1/3 | 偶用 | First, suppose [M] , so [M] .（H4） |
| `By Lemma [REF], [M], so [M]. / by Eq [REF] and Eq [REF],` | 引用已证结论推进〔M6.6 证明步骤〕。注：H1 2、H4 1。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | By Lemma [REF], [M] , so [M] .（H4） |
| `For simplicity, we consider X from now on` | 为简便只考虑一种情形〔M6.2 记号与设定〕。注：H1、H2；refA 5 篇。 | 2/2 | 1 | 5/5 | 1/1 | 偶用 | For simplicity, we consider a single observed user-item pair [M] from now on.（H1） |
| `X is given by: [EQ]` | 给出结果式〔M6.4 陈述命题/等价/闭式解〕。注：H1、H4 各 1。 | 2/2 | 1 | 1/1 | 2/2 | 偶用 | The gradients with respect to the negative representations ... is given by: [EQ]（H1） |
| `Given the theoretical guarantees and empirical superiority of X, we ...` | 理论保证〔M6.1 引出理论分析〕。注：H1 用 guarantees 作卖点，H2 用于 Limitations（lacks theoretical guarantee）；refB refB2 "Considering the theoretical guarantee and empirical success"。 | 2/2 | 1 | 0/0 | 2/4 | 偶用 | Given the theoretical guarantees and empirical superiority of X over most contrastive loss functions（H1） |
| `..., we have [M] / we have: [EQ]` | 给出推导结果〔M6.6 证明步骤〕。注：H1、H4 各 1。 | 2/2 | 1 | 0/0 | 1/2 | 偶用 | Since [M] , we have: [EQ]（H4） |
| `For brevity, we present a proof sketch summarizing the key idea here and delegate the full proof to Appendix [REF].` | 正文给证明梗概〔M6.5 证明的指引〕。注：H1 1 次；delegate 较少见，可改 "leave the full proof to Appendix"。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | For brevity, we present a proof sketch summarizing the key idea here（H1） |
| `Therefore, by rearranging the terms, X can be restated as ... / By rearranging the above equation, we obtain:` | 整理式子〔M6.6 证明步骤〕。注：H1、H4 各 1。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | By rearranging the above equation, we obtain: [EQ]（H4） |
| `X lies in / spans the null space of Y` | 陈述性质（矩阵张成）〔M6.4 陈述命题/等价/闭式解〕。注：H4 2 次（"This retained matrix [M] spans the null space"）；领域特定。 | 1/5 | 1 | 0/0 | 0/0 | 偶用 | This retained matrix [M] spans the null space [CITE] of [M] .（H4） |
| `We analyze the X of Y` | 开始一段分析〔M6.1 引出理论分析〕。注：H4 3 次（复杂度分析） | 1/3 | 1 | 1/2 | 2/3 | 偶用 | We analyze the per-layer computational complexity of X.（H4） |
| `X ensures that Y` | 说明保证〔M6.7 命题后的解读〕。注：H4 3 次。 | 1/3 | 1 | 0/0 | 3/3 | 偶用 | the null-space constraint ensures that [M] , leveraging fundamental properties of null space [CITE]（H4） |
| `X is consistent with our previous findings / theoretical analysis` | 与前文一致〔M6.8 理论与实验对照〕。注：H2 2（previous findings）；refB refB4 "consistent with our theoretical analysis in Section"。 | 1/2 | 1 | 0/0 | 2/2 | 偶用 | effectively encoded user preference similarities, which is consistent with our previous findings in Section [REF].（H2） |
| `In practice, this cost is dominated either by X or by Y, depending on Z` | 说明主导项〔M6.7 命题后的解读〕。注：H4 2 次。 | 1/2 | 1 | 1/1 | 0/0 | 偶用 | In practice, this cost is dominated either by the SVD on the benign covariance（H4） |
| `... (See Appendix [REF] for the proof).` | 括号指向证明〔M6.5 证明的指引〕。注：H4 2 次，括号放句末句号前。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | This equivalence enables efficient computation when [M] (See Appendix [REF] for the proof).（H4） |
| `All of our findings also hold on this model / whether these findings hold for X` | 说明结论是否成立〔M6.9 分析的局限〕。注：H3 2 次。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | it remains unexplored whether these findings hold for reasoning models based on other backbones [CITE].（H3） |
| `Consider the (optimization) problem: [EQ] where [M] denotes X` | 证明开头设定问题〔M6.2 记号与设定〕。注：H4 附录 2 次："Consider the problem of establishing the equivalence ..."。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Consider the problem of establishing the equivalence between the null spaces of [M] and [M]（H4） |
| `Since each X contains the other, we conclude: [EQ] / We conclude: [EQ]` | 证明收尾〔M6.6 证明步骤〕。注：H4 2 次；house无 "This completes the proof"（refB refB2 有 "This concludes the proof"） | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Since each null space contains the other, we conclude: [EQ]（H4） |
| `..., we get: [EQ] / we can get [M] via X as follows:` | 得到结果〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 1/2 | 1/1 | 偶用 | Substituting [M] , [M] , [M] , and [M] , we get: [EQ]（H4） |
| `Grounded in X theory and guided by Y, Z ...` | 方法落在某理论上〔M6.1 引出理论分析〕。注：H4 方法小结句："Grounded in null-space projection theory and guided by ..."。 | 1/1 | 1 | 0/0 | 2/3 | 偶用 | Grounded in null-space projection theory and guided by learned refusal behavior, X steers（H4） |
| `Substituting [M], [M], and [M], we get: [EQ]` | 代回变量〔M6.6 证明步骤〕。注：H4 1 次（"Restoring original symbols." 段） | 1/1 | 1 | 0/0 | 1/1 | 偶用 | Substituting [M] , [M] , [M] , and [M] , we get: [EQ]（H4） |
| `This approach is significantly more efficient than directly X` | 说明计算优势〔M6.7 命题后的解读〕。注：H4 1 次。 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | This approach is significantly more efficient than directly analyzing [M]（H4） |
| `... based on the following lemma: [lemma] Let [M] be X. Then Y` | 引出引理〔M6.4 陈述命题/等价/闭式解〕。注：H4 1 次； | 1/1 | 1 | 0/0 | 0/0 | 偶用 | we simplify the computation process ... based on the following lemma:（H4） |
| `..., under the assumption that X` | 附加假设条件〔M6.3 陈述假设〕。注：H4 1 次（相关方法的隐含假设） | 1/1 | 1 | 0/0 | 0/0 | 偶用 | uniformly applied to all prompts, under the assumption that it selectively affects only malicious ones.（H4） |
| `Computing X, as in [REF], yields a basis for Z` | 计算得到〔M6.6 证明步骤〕。注：H4 1 次；yields house 3 篇多用于结果（yields a consistent boost） | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Computing its singular value decomposition, as in the SVD identity, yields a basis for [M]（H4） |
| `Define X as: [EQ] ... We aim to prove that [M].` | 说明证明目标〔M6.6 证明步骤〕。注：H4 附录 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We aim to prove that [M] .（H4） |
| `First, this research lacks theoretical guarantee, and we do not X` | 承认缺理论保证〔M6.9 分析的局限〕。注：H2 Limitations 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | First, this research lacks theoretical guarantee, and we do not contribute to designing any new（H2） |
| `Notation and setup. (段首粗体小标题)` | 证明内部小标题〔M6.2 记号与设定〕。注：H4 附录证明用粗体段首：Notation and setup. / Computational efficiency. / Trace form. / Gradient and stationarity. / Restoring original symbols. | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Notation and setup.（H4） |
| `Noting that [M] (as defined in [REF]), the Y is: [EQ]` | 插入已知事实〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Noting that [M] (as defined in [REF]), the projection matrix is: [EQ]（H4） |
| `Setting the gradient to zero yields: [EQ]` | 令梯度为零〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Setting the gradient to zero yields: [EQ]（H4） |
| `Since [M], we have: [EQ]` | 由条件推出〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Since [M] , we have: [EQ] For any arbitrary [M] , define [M] .（H4） |
| `So [M] spans [M].` | 短推论〔M6.6 证明步骤〕。注：H4 证明 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Thus, [M] satisfies: [EQ] So [M] spans [M] .（H4） |
| `The proof of X is in Appendix [REF].` | 指向证明〔M6.5 证明的指引〕。注：H4 1 次，单独成句。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The proof of [REF] is in Appendix [REF].（H4） |
| `This equivalence enables X when [M].` | 解读等价的用处〔M6.7 命题后的解读〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This equivalence enables efficient computation when [M] (See Appendix [REF] for the proof).（H4） |
| `This satisfies the X constraint in [REF], ensuring Z.` | 验证满足约束〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Then: [EQ] This satisfies the benign constraint in [REF], ensuring a zero steering term for（H4） |
| `To simplify the solution of this optimization problem, we re-organize the variables as follows:` | 为求解重排变量〔M6.2 记号与设定〕。注：H4 附录 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To simplify the solution of this optimization problem, we re-organize the variables as follows: [EQ]（H4） |
| `To this end, consider the quadratic form: [EQ]` | 引入辅助量〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To this end, consider the quadratic form: [EQ] Since [M] is symmetric and positive semi-definite,（H4） |
| `Using the matrix derivative rule [EQ] we compute the gradient: [EQ]` | 求梯度〔M6.6 证明步骤〕。注：H4 1 次。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Using the matrix derivative rule [EQ] we compute the gradient: [EQ]（H4） |
| `We prove [M] through mutual inclusion.` | 说明证明思路〔M6.6 证明步骤〕。注：H4 1 次（集合相等） | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We prove [M] through mutual inclusion.（H4） |
| `the theoretical understanding of X (remains limited / untouched)` | 点出理论理解不足〔M6.1 引出理论分析〕。注：只见于 H1（6 次）；2025-26 未再用，改用 lack theoretical grounding。 | 1/6 | 0 | 0/0 | 0/0 | 已弃用 | Limited theoretical understanding for the generalization ability of CL-based CF methods.（H1） |
| `X holds when / holds by / holds under Y` | 说明不等式成立条件〔M6.6 证明步骤〕。注：H1 4、H4 1。 | 1/4 | 0 | 0/0 | 1/1 | 已弃用 | the first inequality in Eq [REF] holds when [M]（H1） |
| `In this section, we study it through the lens of X` | 换一个理论视角分析〔M6.1 引出理论分析〕。注：H1 3 次；2025-26 未用。 | 1/3 | 0 | 0/0 | 1/1 | 已弃用 | In this section, we study it through the lens of distributionally robust optimization (DRO)（H1） |
| `In-depth Analysis of X (小节标题)` | 理论分析小节标题〔M6.1 引出理论分析〕。注：H1 小节标题；refA/refB 各 3 篇也用（多为实验分析） | 1/2 | 0 | 3/4 | 3/3 | 已弃用 | In-depth Analysis of X（H1） |
| `Grounded by theoretical proof, X ...` | 句首强调有证明〔M6.1 引出理论分析〕。注：H1 2 次 + refB refB2/refB4 各 1；导师共享模板，但house 2025-26 未用。 | 1/2 | 0 | 0/0 | 2/2 | 已弃用 | Grounded by theoretical proof, X empowers top- [M] recommendations via informative negative sampling（H1） |
| `In this definition / In this formulation, [M] is X` | 解释刚给出的定义〔M6.2 记号与设定〕。注：H1 附录 2 次。 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | In this definition, [M] is the number of negative samples for each user [M] ,（H1） |
| `Then, optimizing X is equivalent to solving Y` | 优化目标等价〔M6.4 陈述命题/等价/闭式解〕。注：H1 定理与附录 2 次。 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | Then, optimizing X loss is equivalent to solving Kullback-Leibler (KL) divergence-constrained distributionally robust optimization (DRO)（H1） |
| `Therefore, by Eq [REF] and Eq [REF], [EQ]` | 由前式推出〔M6.6 证明步骤〕。注：H1 附录 2 次；Therefore 句首本身 6 篇全用（26 次），是house替代 Hence/Thus 的首选。 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | Therefore, by rearranging the terms, optimizing X can be restated as a standard DRO problem:（H1） |
| `We briefly outline the main steps of derivation (see Appendix [REF] for the complete derivation):` | 正文只列推导主要步骤〔M6.5 证明的指引〕。注：H1 2 次。 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | We briefly outline the main steps of derivation (see Appendix [REF] for the complete derivation):（H1） |
| `We denote X as [M]` | 命名记号〔M6.2 记号与设定〕。注：H1 1；导师 5 篇。 | 1/1 | 0 | 2/4 | 3/6 | 已弃用 | We denote the relative hardness of negative item [M] with respect to observed interaction [M]（H1） |
| `In essence, by X, Y effectively Z.` | 点出本质〔M6.7 命题后的解读〕。注：H1 1；refB 3 篇。 | 1/1 | 0 | 0/0 | 3/3 | 已弃用 | In essence, by learning the hardness in an adversarial manner, X effectively enhances the robustness（H1） |
| `X is a special case of Y, when [M]` | 特例关系〔M6.4 陈述命题/等价/闭式解〕。注：H1 1；refA refA3/refA6。 | 1/1 | 0 | 2/3 | 0/0 | 已弃用 | InfoNCE is a special case of X, when [M] .（H1） |
| `Suppose that there are X, let them be [M] without loss of generality.` | 不失一般性〔M6.6 证明步骤〕。注：H1 1 + refA refA8 + refB refB4；house句末用法。 | 1/1 | 0 | 1/1 | 1/1 | 已弃用 | let them to be [M] without loss of generality.（H1） |
| `These results validate X as demonstrated in Theorem [REF], proving that Y` | 用实验印证定理〔M6.8 理论与实验对照〕。注：H1 1；refA refA8、refB refB5 各 1。 | 1/1 | 0 | 1/1 | 1/1 | 已弃用 | These results validate the strong generalization ability of X as demonstrated in Theorem [REF]（H1） |
| `To some extent, X is equivalent to Y.` | 有保留的等价〔M6.7 命题后的解读〕。注：H1 1。 | 1/1 | 0 | 1/1 | 1/1 | 已弃用 | To some extent, our X is equivalent to the widely-adopted Discounted Cumulative Grain (DCG) ranking（H1） |
| `By defining [M], we have [M].` | 定义后得到结论〔M6.6 证明步骤〕。注：H1 1 次（证明梗概） | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | By defining [M] , we have [M] .（H1） |
| `the equality holds if and only if X` | 等号成立条件〔M6.4 陈述命题/等价/闭式解〕。注：H1 1 + refB refB4 1。 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | the equality holds if and only if the interactions [M] are ranked top-K.（H1） |
| `X can be obtained as follows: [EQ]` | 得到结果式〔M6.6 证明步骤〕。注：H1 1 + refB refB5。 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | the [M] can be obtained as follows: [EQ]（H1） |
| `..., which shows that X` | 由结论得出含义〔M6.7 命题后的解读〕。注：H1 1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | to the hardness term [M] , which shows that the X loss function is hardness-aware.（H1） |
| `a common assumption underlying these methods is X` | 点出通行假设〔M6.3 陈述假设〕。注：H1 1 次，且句首为禁用的 Wherein；改为 "A common assumption underlying these methods is ..."。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Wherein, a common assumption underlying these methods is considering all unobserved interactions as negative signals（H1） |
| `Consequently, the right-hand side of Equation [REF] can be derived by X` | 说明右边如何得到〔M6.6 证明步骤〕。注：H1 1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Consequently, the right-hand side of Equation [REF] can be derived by factoring out the negative（H1） |
| `From this perspective, X focuses on Y` | 承接视角后的解读〔M6.1 引出理论分析〕。注：H1 1 次。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | From this perspective, X focuses on the worst-case distribution over high-quality hard negative sampling.（H1） |
| `In terms of implementation, [M] is controlled by X` | 理论量的实现方式〔M6.3 陈述假设〕。注：H1 1 次；2025-26 用 In practice。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | In terms of implementation, [M] is controlled by the number of adversarial training epochs (see（H1） |
| `Let us assume that for [M], we have access to X` | 提出理想化假设〔M6.3 陈述假设〕。注：H1 方法 1 次（oracle hardness），随后用 "However, X is not accessible ..." 放松。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Let us assume that for [M] , we have access to the oracle hardness [M]（H1） |
| `The analysis begins with X, primarily due to its simplicity` | 说明从哪一步开始分析〔M6.1 引出理论分析〕。注：H1 附录梯度分析开头。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | The analysis begins with the main part of X for a single positive interaction [M]（H1） |
| `The last inequality is satisfied by [M].` | 说明最后一步〔M6.6 证明步骤〕。注：H1 1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | The last inequality is satisfied by [M] .（H1） |
| `we theoretically demonstrate (through X) that Y` | 用理论说明某性质〔M6.1 引出理论分析〕。注：H1 1 次；refB 用 "we theoretically reveal"（refB2, refB4） | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | we theoretically demonstrate through adaptive gradients that X subtly employs informative negative sampling（H1） |
| `We would like to highlight the straightforward nature of this derivation, which ...` | 强调推导的简单〔M6.7 命题后的解读〕。注：H1 1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | We would like to highlight the straightforward nature of this derivation, which is achieved without（H1） |
| `which is achieved without the need for assumptions` | 强调推导不需额外假设〔M6.3 陈述假设〕。注：H1 1 次。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | derivation, which is achieved without the need for assumptions, but the widely-used LogSumExp operator [CITE].（H1） |
| `X can be derived by factoring out Y and reorganizing the term` | 提取公因子〔M6.6 证明步骤〕。注：H1 1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | be derived by factoring out the negative sign of the logarithm and reorganizing the term.（H1） |
| `X can be restated as a standard Y problem: [EQ]` | 改写成标准问题〔M6.4 陈述命题/等价/闭式解〕。注：H1 1 次（"can be restated as a standard DRO problem"）； | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | optimizing X can be restated as a standard DRO problem: [EQ]（H1） |
| `Assuming X, Y ...` | 分词句首带假设〔M6.3 陈述假设〕。注：house 0；导师 refA/refB 多篇。house用 "Under X," 或 "Let us assume that"。 | 0/0 | 0 | 1/1 | 4/6 | 仅参考 | Assuming generative agents can accurately simulate genuine human behaviors, it's plausible that these agents would... |
| `See detailed proof in Appendix [REF]. / See Appendix [REF] for the detailed proof.` | 指向详细证明〔M6.5 证明的指引〕。注：house 0；refB refB2、refB4。 | 0/0 | 0 | 0/0 | 2/3 | 仅参考 | See detailed proof in Appendix [REF].（refB2） |
| `X hardly holds / is easily violated in real-world scenarios` | 假设在实际中不成立〔M6.9 分析的局限〕。注：house 0；refB refB3、refB4。 | 0/0 | 0 | 0/0 | 2/3 | 仅参考 | However, this assumption hardly holds in the real-world scenarios, due to an inherent factor --（refB3） |
| `we theoretically reveal that X` | 用理论揭示〔M6.1 引出理论分析〕。注：house 0；refB 2 篇。house偏好 demonstrate / show。 | 0/0 | 0 | 0/0 | 2/2 | 仅参考 | Third, we theoretically reveal that BC loss tends to learn a low-entropy cluster for positive（refB2） |
| `X without any assumption(s) on the test data` | 强调无需假设〔M6.3 陈述假设〕。注：house 0；refB refB3/refB8。 | 0/0 | 0 | 0/0 | 2/2 | 仅参考 | Therefore, it is infeasible to solve this generalization problem, without any assumption.（refB3） |
| `As elucidated in Theorem [REF], X` | 回指定理〔M6.10 禁用/退役〕。注：禁用（elucidate）。改 "As shown in Theorem [REF]" 或 "By Theorem [REF]"。 | 1/1 | 0 | 0/0 | 0/0 | 禁用 | As elucidated in Theorem [REF], learning the hardness [M] in an adversarial manner essentially conducts（H1） |
| `This furnishes a potential theoretical rationalization for X` | 给理论解释〔M6.10 禁用/退役〕。注：禁用。H1 1 次。 | 1/1 | 0 | 0/0 | 0/0 | 禁用 | This furnishes a potential theoretical rationalization for the exceptional robustness observed in contrastive learning.（H1） |

## 实验设置

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `We evaluate models on various reasoning benchmarks.` | 说明评测对象〔S4 评测基准与任务〕。注：6/6 篇。 | 6/11 | 5 | 2/2 | 3/7 | 常用 | We evaluate models on various reasoning benchmarks.（H6） |
| `We train these LLMs on the same data as introduced in X.` | 沿用相同数据/配置〔S3 训练数据与数据划分〕。注：H2/H4/H5/H6 的 'the same ... as (in) X'。 | 6/11 | 5 | 4/6 | 0/0 | 常用 | ...the same proprietary teacher model, using the same SFT recipe as in the cold-start stage...（H6） |
| `We compare X with the vanilla Y which only considers ...` | 称呼原始方法/原模型〔S5 基线〕。注：5/6 篇 (H5 10); house标志用词, 导师几乎不用。 | 5/20 | 5 | 0/0 | 1/1 | 常用 | ...compare X with the vanilla RLVR which only considers the outcome reward, and report...（H6） |
| `we sample N=16 responses with sampling temperature as 0.7` | 采样温度〔S9 解码与生成设置〕 | 5/9 | 4 | 0/0 | 2/6 | 常用 | For each testing question, we sample [M] =16 responses with sampling temperature as 0.7.（H6） |
| `open-source (LLMs / LLM backbones / Qwen models / LRMs)` | 修饰所用模型〔S2 模型与骨干〕。注：5/6 篇; refA 0。 | 5/9 | 5 | 0/0 | 1/1 | 常用 | We conduct experiments on two open-source Qwen models of different sizes (8B and 4B) [CITE].（H6） |
| `(varied / varying / different) model sizes` | 模型规模〔S2 模型与骨干〕。注：5/6 篇; 多用于 'across multiple model sizes'。 | 5/9 | 5 | 0/0 | 0/0 | 常用 | ...with X outperforms the vanilla RLVR across multiple datasets and multiple model sizes on average...（H6） |
| `We first cold-start the pre-trained models (\ie A and B) by performing supervised fine-tuning on ...` | 括号内用 \ie 列出具体模型名〔S2 模型与骨干〕。注：\ie + 模型名; 计数为 '(i.e. 大写+数字' 形式。 | 5/8 | 5 | 3/4 | 1/1 | 常用 | ...the pre-trained models (i.e. Qwen3-8 B-Base and Qwen3-4B-Base) by performing supervised fine-tuning on correctly...（H6） |
| `Implementation Details  [heading]` | 实现细节小节标题〔S7 实现框架与算力〕。注：5/6 篇; 导师少用。 | 5/8 | 5 | 1/1 | 2/3 | 常用 | Implementation Details We conduct the cold-start training with the LlamaFactory and all the RL experiments...（H6） |
| `We conduct experiments on N open-source LLMs: A \cite{}, B \cite{}, and C \cite{}.` | 列出所用模型/数据〔S2 模型与骨干〕。注：5/6 篇; 当前论文用来列模型 (H4, H5, H6, H3) | 5/6 | 4 | 1/1 | 4/6 | 常用 | We conduct experiments on two open-source Qwen models of different sizes (8B and 4B) [CITE].（H6） |
| `X backbone(s) / the LightGCN and MF backbones` | 骨干模型〔S2 模型与骨干〕。注：H1 最多; 2025-26 仅 H4、H5 少量使用 ("LLM backbones") | 4/16 | 3 | 1/1 | 6/51 | 常用 | Additionally, under the backbone LLM size of 3B, X even gains more.（H5） |
| `the average / averaged performance (\ie averaged accuracy among A, B, and C)` | 平均指标〔S6 评测指标与打分〕 | 4/12 | 4 | 0/0 | 1/6 | 常用 | We report pass@1 as the accuracy metric (i.e. the average pass rate among 16 responses).（H6） |
| `... for evaluation` | 说明用途〔S4 评测基准与任务〕。注：4/6 篇。 | 4/8 | 3 | 0/0 | 1/1 | 常用 | After we finish generation, we extract the answer inside the boxed for evaluation .（H3） |
| `For the large size of X, we evaluate on three subsets, each including 100 questions` | 子集〔S3 训练数据与数据划分〕 | 4/7 | 3 | 4/5 | 3/5 | 常用 | ...we evaluate on three subsets , each including 100 questions, namely SuperGPQA-Eng, SuperGPQA-Med, and SuperGPQA-Sci...（H6） |
| `X is a benchmark designed to evaluate ... / X is a dataset that ...` | 逐个介绍数据集(附录)〔S3 训练数据与数据划分〕。注：4/6 篇; 附录数据集介绍的首句句型。 | 4/7 | 3 | 5/5 | 2/3 | 常用 | AlpacaEval is a benchmark designed to evaluate the capabilities of large language models on a...（H4） |
| `we set the ...` | 设定〔S8 超参数与训练设置〕。注：4/6 篇。 | 4/5 | 3 | 5/10 | 1/1 | 常用 | In X, we set the key hyperparameters as follows: (1) the threshold [M] for selecting...（H4） |
| `For each testing question, we sample N=16 responses with sampling temperature as 0.7.` | 逐个样本的设置〔S8 超参数与训练设置〕。注：只算句首 'For each + 名词,'。 | 4/4 | 4 | 4/7 | 2/2 | 常用 | For each testing question , we sample [M] =16 responses with sampling temperature as 0.7.（H6） |
| `in-distribution / out-of-distribution task(s)` | 分布内/外任务〔S3 训练数据与数据划分〕。注：H5: 'HQA is one in-distribution task ..., while the remaining tasks are out-of-distribution tasks.'。 | 3/25 | 2 | 1/4 | 4/14 | 常用 | HQA is one in-distribution task similar to the training data, while the remaining tasks are...（H5） |
| `a maximum prompt length of 8,192 tokens / max training steps` | 长度与步数上限〔S8 超参数与训练设置〕。注：H3、H5、H6。 | 3/9 | 3 | 0/0 | 0/0 | 常用 | For all RL training, we fix the max training steps to 1500.（H6） |
| `two metrics / the accuracy metric / evaluation metrics` | 指标〔S6 评测指标与打分〕。注：排除 H6 提示词里的 metrics; 2026 句型为 'We report X as the accuracy metric' 与 'for two metrics:'。 | 3/7 | 2 | 1/3 | 5/9 | 常用 | ...classify responses for two metrics : the Defense Success Rate (DSR), which measures the...（H4） |
| `we set the X as Y` | 设定超参数值〔S8 超参数与训练设置〕。注：house主用 'set ... as' (H1, H3, H4); 导师也多用。 | 3/6 | 2 | 7/13 | 4/7 | 常用 | ...DeepSeek , we set the temperature as 0.6 to prevent endless repetitions, set the maximum...（H3） |
| `The training batch size is set to 128, with ...` | 批大小〔S8 超参数与训练设置〕。注：H1、H5、H6。 | 3/6 | 2 | 5/6 | 4/11 | 常用 | ...32768, per-device train batch size = 1, gradient accumulation steps = 1, base learning...（H6） |
| `For reducing the overfitting, we set the regularization term as 10.` | 防过拟合〔S8 超参数与训练设置〕。注：H2、H3、H4。 | 3/6 | 3 | 4/15 | 1/1 | 常用 | ...serves as a regularization with Frobenius norm to avoid overfitting and [M] is a hyperparameter...（H4） |
| `computational cost / computational resource cost` | 算力开销〔S7 实现框架与算力〕。注：H3、H4、H5。 | 3/6 | 3 | 2/3 | 2/3 | 常用 | The whole computational resource cost of this research is about 80 A100 GPU days ...（H3） |
| `on the validation set` | 验证集上的指标〔S6 评测指标与打分〕。注：H1、H2、H5。 | 3/6 | 2 | 1/1 | 3/5 | 常用 | ...stopping at the last evidence turn ([REF]) and the reward on the validation set ([REF])...（H5） |
| `we set the X to Y / X is set to Y` | 设定超参数值〔S8 超参数与训练设置〕。注：H4、H5; 与 set as 并用。 | 3/5 | 2 | 5/6 | 7/15 | 常用 | We adopt a clip ratio of 0.2 and set the learning rate to [M] .（H5） |
| `... by answering the following questions.` | 引出 RQ 列表〔S1 小节开头与研究问题〕。注：合并 'the following questions' 与 'the following research questions'。 | 3/4 | 2 | 7/7 | 5/5 | 常用 | ...In this section, we explore the effectiveness of X by answering the following questions...（H5） |
| `We compare X with Y (, to assess ...).` | 与基线比较〔S5 基线〕。注：H2、H4、H6。 | 3/4 | 3 | 4/4 | 3/5 | 常用 | We compare X with the vanilla RLVR which only considers the outcome reward, and report...（H6） |
| `We implement our method / all the experiments / another version of X` | 实现说明〔S7 实现框架与算力〕。注：H2、H3、H4; 导师常用 we implement our X in PyTorch。 | 3/4 | 3 | 4/5 | 2/2 | 常用 | ...Setup Implementation Details We implement all the experiments with PyTorch and Transformers on a...（H4） |
| `Following previous work(s) / studies \cite{}, we ...` | 沿用前人做法〔S10 沿用前人设置〕。注：H1、H2、H4。 | 3/4 | 2 | 3/5 | 2/4 | 常用 | ...BLEU similarity between all malicious training prompts and all evaluation prompts following prior work [CITE]...（H4） |
| `we partition / divide ... into training, validation, and test sets` | 划分训练/验证/测试集〔S3 训练数据与数据划分〕。注：H1、H2、H4。 | 3/4 | 2 | 1/1 | 2/3 | 常用 | To ensure robust evaluation, we partition the datasets into training, validation , and test sets.（H4） |
| `Experimental Setup / Experiment Setup  [heading]` | 设置小节标题〔S1 小节开头与研究问题〕。注：H3、H4 用 Experimental Setup, H6 用 Experiment Setup; 导师用 Experimental Settings。 | 3/4 | 3 | 0/0 | 0/0 | 常用 | Experiment Setup We briefly introduce the experiment setup of our paper, and more details can...（H6） |
| `PyTorch` | 框架名〔S7 实现框架与算力〕 | 3/3 | 2 | 2/2 | 3/4 | 常用 | ...the experiments with PyTorch and Transformers on a single NVIDIA A40 GPU and an...（H4） |
| `on a single NVIDIA X GPU` | 单卡说明〔S7 实现框架与算力〕 | 3/3 | 2 | 0/0 | 4/4 | 常用 | ...Transformers on a single NVIDIA A40 GPU and an Intel(R) Xeon(R) Gold 6248R CPU...（H4） |
| `We conduct all the experiments in PyTorch with a single X GPU and a Y CPU.` | 说明实验环境〔S7 实现框架与算力〕。注：H1、H2、H5。 | 3/3 | 2 | 0/0 | 1/1 | 常用 | Implementation Details We conduct all the experiments based on the verl framework.（H5） |
| `rollout number` | 每题采样次数〔S9 解码与生成设置〕。注：H3、H5。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | ...size = 32, rollout number [M] = 8, mini batch size = 64, weight...（H6） |
| `We use the following template for generation: ...` | 生成模板〔S9 解码与生成设置〕。注：H3、H6。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | ...dataset using Qwen3 prompt template , with a sequence cutoff length of 32768, per-device...（H6） |
| `information leakage` | 训练与测试数据泄漏〔S3 训练数据与数据划分〕。注：H2、H4; house固定用词, 不用 contamination。 | 2/6 | 2 | 0/0 | 0/0 | 常用 | To prevent information leakage , we exclude prompts from the training and validation sets that...（H4） |
| `For math reasoning, we evaluate ... benchmarks including A \cite{}, ... / For assessing X, we adopt the Y benchmark \cite{}.` | 按评测目的列基准〔S4 评测基准与任务〕。注：H4、H6; 按能力逐句列基准, 每句一个能力。 | 2/4 | 2 | 0/0 | 0/0 | 常用 | For math reasoning, we evaluate math-reasoning benchmarks including AIME24 [CITE], AIME25 [CITE], and AMC23 [CITE].（H6） |
| `To prevent / avoid / alleviate information leakage, we ...` | 防泄漏措施的目的状语〔S3 训练数据与数据划分〕。注：H2、H4。 | 2/4 | 2 | 0/0 | 0/0 | 常用 | To prevent information leakage , we exclude prompts from the training and validation sets that...（H4） |
| `We train models based on the X dataset \cite{}, which contains 17k high-quality math questions.` | 说明训练数据〔S3 训练数据与数据划分〕。注：H6、H5。 | 2/4 | 2 | 0/0 | 0/0 | 常用 | We train models based on the DAPO-Math-17k dataset [CITE], which contains 17k high-quality math questions.（H6） |
| `training time cost / time cost` | 时间开销〔S7 实现框架与算力〕。注：H1、H4、H5。 | 2/3 | 2 | 5/8 | 2/2 | 常用 | ...compare the training time cost of our method with refusal training on the same...（H4） |
| `a wide range of open-source LRMs, including the X series and Y` | 强调覆盖面〔S2 模型与骨干〕。注：H3、H4。 | 2/3 | 2 | 0/0 | 2/3 | 常用 | ...baselines in inducing refusal behavior across a wide range of jailbreak attacks (cf. Section [REF])...（H4） |
| `we measure the defense success rate (DSR) against X, where the DSR is computed using GPT-4o` | 定义指标与计算方式〔S6 评测指标与打分〕。注：H4; H6 的 1 次为方法句 (is computed by aggregating ...) | 2/3 | 2 | 1/1 | 0/0 | 常用 | ...in safety enhancement, we measure the defense success rate (DSR) against jailbreak attacks, where...（H4） |
| `For the RL training / For the cold-start stage, we ...` | 按训练阶段分段〔S8 超参数与训练设置〕。注：H5、H6。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | For the cold-start stage , we collect 40k training data from the responses of a...（H6） |
| `the 8B-sized / 7B-sized model` | 指代某规模模型〔S2 模型与骨干〕。注：H5、H6。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...on the 8B- sized model by abandoning the evolving reward [M] Rub [M] ,...（H6） |
| `under both X and Y (inference modes / settings)` | 两种设置下均评测〔S4 评测基准与任务〕。注：H5、H6。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...in Table [REF], under both w/o EG and w EG inference modes, X outperforms...（H5） |
| `we (randomly) sample 100 questions from X \cite{}` | 抽取评测子集〔S3 训练数据与数据划分〕。注：H3、H4。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | For inference efficiency, we randomly sample 100 questions from the GSM8K [CITE] and MATH500 [CITE]...（H4） |
| `We apply a learning rate warmup for 20 steps.` | 学习率预热〔S8 超参数与训练设置〕。注：H5、H6。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...2e-6), weight decay = 0.1, warmup ratio = 0.01 and total training epochs = 5.0...（H6） |
| `We implement all the experiments on 8 NVIDIA A100 GPUs / with PyTorch and Transformers on ...` | 说明实验环境〔S7 实现框架与算力〕。注：H3、H4。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...Setup Implementation Details We implement all the experiments with PyTorch and Transformers on a...（H4） |
| `In X, we set the key hyperparameters as follows: (1) ...; (2) ...; and (3) ...` | 行内编号列超参数〔S8 超参数与训练设置〕。注：H4 (超参数), H5 (方法中的两个判断); 导师更常用。 | 2/2 | 2 | 4/12 | 5/11 | 常用 | ...(1) the threshold [M] for selecting the nullspace, typically set to [M] ; (2)...（H4） |
| `All subsequent RL experiments are conducted based on these carefully cold-started models.` | 说明实验基于哪个模型/设备〔S2 模型与骨干〕。注：H5 'All the evaluations are conducted on an 8-GPU node', H6。 | 2/2 | 2 | 0/0 | 4/5 | 常用 | All subsequent RL experiments are conducted based on these carefully cold-started models.（H6） |
| `We adopt three X methods as baselines: A \cite{}, B \cite{}, and C \cite{}.` | 列出基线〔S5 基线〕。注：H2、H4。 | 2/2 | 2 | 1/1 | 2/2 | 常用 | ...activation steering methods as baselines : two vector calibration methods, Jailbreak Antidote [CITE] and...（H4） |
| `..., which contains ...` | 补充数据集规模〔S3 训练数据与数据划分〕。注：H3、H6。 | 2/2 | 2 | 2/2 | 0/0 | 常用 | We train models based on the DAPO-Math-17k dataset [CITE], which contains 17k high-quality math questions.（H6） |
| `Following the suggestions of X, we set ...` | 沿用他人建议〔S10 沿用前人设置〕。注：H3; H1/H2 'following the previous work'。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | Following the suggestions of DeepSeek , we set the temperature as 0.6 to prevent endless...（H3） |
| `We conduct all the experiments based on the verl framework.` | 说明训练框架〔S7 实现框架与算力〕。注：H5、H6。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | ...training with the LlamaFactory and all the RL experiments based on the verl framework...（H6） |
| `For inference efficiency / For evaluation efficiency, we sample 100 questions from ...` | 说明为何只用子集〔S3 训练数据与数据划分〕。注：H3、H4; 一句话交代理由后给数量。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | For inference efficiency , we randomly sample 100 questions from the GSM8K [CITE] and MATH500...（H4） |
| `For the RL training, we train all models with a chunk size of ..., a maximum prompt length of ..., and ...` | 一句列出多项训练配置〔S8 超参数与训练设置〕。注：H5、H6。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...the RL training, we train all the models with the DAPO-Math-17K data, with prompt...（H6） |
| `two open-source LLM backbones with different sizes: A and B` | 说明模型规模不同〔S2 模型与骨干〕。注：H5 'with different sizes', H6 'of different sizes'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We conduct experiments on two open-source Qwen models of different sizes (8B and 4B) [CITE].（H6） |
| `We adopt a clip ratio of 0.2 and set the learning rate to \num{}.` | RL 超参数〔S8 超参数与训练设置〕。注：H5、H6。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...no KL reward/loss, clip ratio = 0.2, learning rate for actor = 1e-6, learning...（H6） |
| `We adopt the same setting as (in) X for evaluation.` | 沿用同一设置〔S10 沿用前人设置〕。注：H2、H3。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We adopt the same setting as in lm-evaluation-harness for evaluation.（H3） |
| `We also consider X as one ablation baseline, which is short for RV` | 补充基线〔S5 基线〕。注：H2、H4。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We also consider directly using the refusal direction vector adopted in our paper as one...（H4） |
| `We randomly split the dataset with a ratio of 9:1 for training and testing.` | 数据划分比例〔S3 训练数据与数据划分〕。注：H2、H3。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We randomly split the dataset with a ratio of 9:1 for training and testing.（H3） |
| `We select four benchmarks from three aspects for evaluating the utility.` | 先总述基准分组〔S4 评测基准与任务〕。注：H4 ("from three aspects"); H5 'on two aspects'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We select four benchmarks from three aspects for evaluating the utility.（H4） |
| `We stop training until we observe the convergence of reward on the validation set.` | 停止训练条件〔S8 超参数与训练设置〕。注：只算停止条件用法; 注意 H5 原句 'stop training until' 语法不佳, 宜改为 'We train until the reward on the validation set converges.'。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We stop training until we observe the convergence of reward on the validation set.（H5） |
| `leading ... baselines / leading ID-based CF models` | 称呼强基线〔S5 基线〕。注：只算 'leading + 名词 + baselines/models/methods' (排除 leading to); 基本只在 H2; 2026 论文改称 the vanilla X / baseline methods。 | 2/12 | 1 | 0/0 | 3/4 | 偶用 | ...model can outperform leading ID-based CF models on multiple datasets, which sheds light on using...（H2） |
| `We search hyperparameters for baselines according to the suggestion in the literature.` | 超参数搜索〔S8 超参数与训练设置〕。注：H1、H2。 | 2/4 | 1 | 4/7 | 1/3 | 偶用 | We search hyperparameters for baselines according to the suggestion in the literature.（H2） |
| `on 100 harmful questions randomly selected / sampled from X \cite{}` | 随机抽样来源〔S3 训练数据与数据划分〕。注：H1、H4。 | 2/4 | 1 | 1/1 | 0/0 | 偶用 | We generate these jailbreak attacks on 100 harmful questions randomly selected from the AdvBench [CITE].（H4） |
| `We apply the all-ranking strategy \cite{} for all experiments, which ranks all items ...` | 推荐评测协议〔S4 评测基准与任务〕。注：H1、H2; 与本稿无关, 仅作句型参考。 | 2/3 | 1 | 1/1 | 3/3 | 偶用 | We apply the all-ranking strategy [CITE] for all experiments, which ranks all items except positive...（H2） |
| `we search X in [a, b]` | 搜索区间〔S8 超参数与训练设置〕。注：H1、H2。 | 2/3 | 1 | 1/1 | 1/4 | 偶用 | ...the only hyperparameter is the temperature [M] and we search it in [ 0.05, 2]...（H2） |
| `two categories of X: A-based baselines (...) and B-based baselines (...)` | 按类别列基线〔S5 基线〕。注：H1、H2。 | 2/3 | 1 | 0/0 | 1/1 | 偶用 | ...[REF], we consider two categories of leading ID-based CF baselines, CL-based CF methods: SGL...（H2） |
| `Here, the number -N indicates the difficulty level, with larger values corresponding to greater difficulty.` | 设置中解释记号〔S3 训练数据与数据划分〕。注：H1、H5; H5 2 次中 1 次在设置段, 1 次在方法段 (Here, the X denotes ...) | 2/3 | 1 | 0/0 | 0/0 | 偶用 | Here, the number -N indicates the difficulty level, with larger values corresponding to greater difficulty.（H5） |
| `For simplicity, we adopt X for ... by default` | 简化选择〔S8 超参数与训练设置〕。注：H1、H2。 | 2/2 | 1 | 5/5 | 1/1 | 偶用 | For simplicity , we adopt text-embeddings-3-large [CITE] for language representation generation by default, for its...（H2） |
| `Hyperparameter Settings (and Implementation Details)  [heading]` | 超参数小节标题〔S1 小节开头与研究问题〕。注：H1、H2; 2026 统一为 Implementation Details。 | 2/2 | 1 | 2/4 | 3/3 | 偶用 | Hyperparameter Settings and Implementation Details We conduct all the experiments in PyTorch with a single...（H2） |
| `We adopt the early stop strategy that stops training if ... does not increase for 20 successive evaluations.` | 早停〔S8 超参数与训练设置〕。注：H1、H2; 2026 改为按验证集收敛停止 (H5) | 2/2 | 1 | 0/0 | 2/2 | 偶用 | We use the early stop strategy to avoid overfitting.（H2） |
| `We optimize all methods with the Adam optimizer.` | 优化器〔S8 超参数与训练设置〕。注：H1、H2。 | 2/2 | 1 | 2/2 | 0/0 | 偶用 | We optimize all methods with the Adam optimizer .（H2） |
| `The test set comprises 100 prompts randomly sampled from X` | 说明集合组成〔S3 训练数据与数据划分〕。注：只算 '数据集/集合 + comprises'; 其余 comprises/consists of 多为方法中的矩阵或样本结构。 | 2/2 | 1 | 1/1 | 0/0 | 偶用 | The test set comprises 100 prompts randomly sampled from AdvBench, ...（H4） |
| `these baseline methods / the baseline method X` | 称呼基线〔S5 基线〕。注：仅 H4 (6 次); 与 the vanilla X 并用。 | 1/6 | 1 | 0/0 | 0/0 | 偶用 | ...safety without compromising utility across various tasks, while baseline methods show instability in preserving utility...（H4） |
| `a newly published dataset / the latest version of X` | 选用新数据防泄漏〔S3 训练数据与数据划分〕。注：仅 H2。 | 1/4 | 1 | 0/0 | 0/0 | 偶用 | ...data, we also conduct experiments on a newly published dataset Amazon 2023 in Appendix [REF]...（H2） |
| `We compare our method with existing X baselines as follows:` | 引出列表〔S5 基线〕。注：只算 'baselines/hyperparameters ... as follows'; 通用 'as follows:' 6/6 篇 30 次(多引出公式) | 1/3 | 1 | 0/0 | 1/1 | 偶用 | Baselines We compare our method with existing activation steering baselines as follows:（H4） |
| `the bold lines are the rolling averaged performance with a window size of 3` | 曲线平滑说明〔S6 评测指标与打分〕。注：H6。 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | ...lines are the rolling average d performance with a window size of 3 and...（H6） |
| `We ensure there is no overlap between the training dataset and the test dataset.` | 保证数据不重叠〔S3 训练数据与数据划分〕。注：仅 H4; 计数 3 次中 1 次为方法句 (we ensure that ...) | 1/3 | 1 | 0/0 | 0/0 | 偶用 | We ensure there is no overlap between the training dataset and the test dataset.（H4） |
| `as suggested in the paper / according to the suggestion in the literature` | 按原文建议设置〔S10 沿用前人设置〕。注：H2; refA 常用 as suggested in。 | 1/2 | 1 | 6/11 | 1/1 | 偶用 | ...adopt the same model structure as suggested in the paper: 600 → 200 → 600...（H2） |
| `Our anonymous implementation is publicly available at ...` | 代码公开〔S12 复现性与统计检验〕。注：H4 (复现声明+伦理声明); 替代禁用的 'Codes are available at'。 | 1/2 | 1 | 3/3 | 1/1 | 偶用 | Our anonymous implementation is publicly available at .（H4） |
| `For all RL training, we fix the max training steps to 1500.` | 固定训练步数〔S8 超参数与训练设置〕。注：仅 H6; 导师 3 篇。 | 1/2 | 1 | 3/4 | 0/0 | 偶用 | For all RL training, we fix the max training steps to 1500.（H6） |
| `the improvements aggregate to statistically significant gains under standard two-sided tests at significance level ...` | 显著性检验〔S12 复现性与统计检验〕。注：H6。 | 1/2 | 1 | 2/2 | 1/2 | 偶用 | ...improvements aggregate to statistically significant gains under standard two-sided tests at significance level [M]...（H6） |
| `X is an activation steering method that ...` | 附录逐个介绍基线〔S5 基线〕。注：H4。 | 1/2 | 1 | 1/1 | 2/2 | 偶用 | ...the AIM Prompt, is a jailbreak method that tells the AI to overlook moral and...（H4） |
| `The hyperparameter search space is reported in Table \ref{}.` | 搜索空间指针〔S8 超参数与训练设置〕。注：H2。 | 1/2 | 1 | 1/6 | 1/1 | 偶用 | The hyperparameter search space is reported in Table [REF].（H2） |
| `Section \ref{} details the X, Y, and Z. Appendix \ref{} further provides ...` | 复现材料清单〔S12 复现性与统计检验〕。注：H4。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Section [REF] details the model backbones, jailbreak attacks, utility benchmarks and baselines.（H4） |
| `the Defense Success Rate (DSR), which measures the proportion of ...` | 指标定义从句〔S6 评测指标与打分〕。注：H4。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...Success Rate (DSR), which measures the proportion of jailbreak prompts correctly rejected, and the...（H4） |
| `we adopt the X benchmark \cite{}` | 选用基准〔S4 评测基准与任务〕。注：H4。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | For assessing general instruction following capabilities, we adopt the AlpacaEval benchmark [CITE].（H4） |
| `We conduct paired comparisons between X and Y at matched model scales` | 配对比较〔S12 复现性与统计检验〕。注：H6。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | We conduct paired comparisons between X and RLVR on Table [REF] at matched model scales...（H6） |
| `we slightly modify X, Y, and Z as baselines` | 改造基线以适配任务〔S5 基线〕。注：仅 H2。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...in general CF, we slightly modify three zero-shot methods in the sequential recommendation [CITE],...（H2） |
| `We strictly follow the evaluation setup as introduced in X \cite{}.` | 严格沿用设置〔S10 沿用前人设置〕。注：仅 H5 (2 次); H6 的 strictly follow 指模型遵循指令, 不计。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | We strictly follow the evaluation setup as introduced in MemAgent [CITE].（H5） |
| `X is set as Y` | 被动设定〔S8 超参数与训练设置〕。注：H6 1; 导师 11 篇。 | 1/1 | 1 | 7/10 | 4/7 | 偶用 | Here [M] is set as 0.2 by default in our paper.（H6） |
| `grid search` | 网格搜索〔S8 超参数与训练设置〕。注：house仅 H4 1 次, 且是说基线开销 (due to its threshold grid search), 不是自己的调参; 导师 9 篇用于调参。 | 1/1 | 1 | 7/7 | 2/3 | 偶用 | ...incurs the largest training cost among steering-based methods due to its threshold grid search...（H4） |
| `To implement this X, we use the Python package of Y \cite{}.` | 实现工具〔S7 实现框架与算力〕。注：仅 H3 (大写句首); 小写 to implement 多在提示词或方法句。 | 1/1 | 1 | 2/3 | 1/1 | 偶用 | To implement this Lasso regression, we use the Python package of scikit-learn [CITE].（H3） |
| `Table \ref{} reports the dataset statistics.` | 数据统计表指针〔S3 训练数据与数据划分〕。注：仅 H2; 导师常写 'The statistics of the datasets are summarized in Table'。 | 1/1 | 1 | 1/1 | 2/2 | 偶用 | Table [REF] reports the dataset statistics .（H2） |
| `During training, we sample responses with ..., while for validation we use ...` | 区分训练与验证设置〔S8 超参数与训练设置〕。注：只算句首 'During training,'; 小写 during training 16 次多为方法描述。 | 1/1 | 1 | 0/0 | 2/3 | 偶用 | During training, we sample responses with temperature [M] and top- [M] [M] , while for...（H5） |
| `For the cold-start stage, we collect 40k training data from ...` | 收集数据〔S3 训练数据与数据划分〕。注：H6。 | 1/1 | 1 | 0/0 | 1/3 | 偶用 | ...the cold-start stage, we collect 40k training data from the responses of a proprietary...（H6） |
| `Here, both X and Y start from the SFT checkpoint.` | 说明起点检查点〔S2 模型与骨干〕。注：H6; 与本稿 checkpoint 设定贴近。 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | Here, both X and RLVR start from the SFT checkpoint.（H6） |
| `Specifically, we evaluate methods on one multi-hop QA task X \cite{}, ...` | 展开列出任务〔S4 评测基准与任务〕。注：H5。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | Specifically, we evaluate methods on one multi-hop QA task HotpotQA (HQA) [CITE], one single-hop QA...（H5） |
| `... we therefore omit concrete compute specifications beyond software and hyperparameter settings` | 说明不披露算力〔S7 实现框架与算力〕。注：H6 特例 (机构政策) | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...reported in this paper; we therefore omit concrete compute specifications beyond software and hyperparameters below...（H6） |
| `For X, the only hyperparameter is the temperature and we search it in [0.05, 2].` | 强调超参数少〔S8 超参数与训练设置〕。注：H2。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | For X, the only hyperparameter is the temperature [M] and we search it in [0.05,...（H2） |
| `the inference process follows the official template` | 按官方模板推理〔S9 解码与生成设置〕。注：H4。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...process follows the official template , and we set do_sample to False for generation,...（H4） |
| `The whole computational resource cost of this research is about 80 A100 GPU days, which is mainly spent on ...` | 总算力开销〔S7 实现框架与算力〕。注：H3。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...is about 80 A100 GPU days , which is mainly spent on the answer generation...（H3） |
| `We report pass@1 as the accuracy metric (\ie the average pass rate among 16 responses).` | 指定报告指标〔S6 评测指标与打分〕。注：H6; 指标后用 (\ie ...) 解释。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We report pass@1 as the accuracy metric (i.e. the average pass rate among 16 responses).（H6） |
| `we set do_sample to False for generation, which means using greedy decoding` | 贪心解码〔S9 解码与生成设置〕。注：H4。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...template, and we set do_sample to False for generation, which means using greedy decoding...（H4） |
| `We take the average accuracy of all 8 rollouts as the accuracy of one question` | 说明如何取平均〔S6 评测指标与打分〕。注：H3。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We take the average accuracy of all 8 rollouts as the accuracy of one question,...（H3） |
| `We use the same X as we adopt in Y, to make the comparison relatively fair.` | 说明公平比较〔S5 基线〕。注：仅 H2; 导师常写 'For a fair comparison'。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...we adopt in X for KAR and XSimGCL, to make the comparison relatively fair...（H2） |
| `we use the vLLM \cite{} framework for acceleration` | 推理加速框架〔S7 实现框架与算力〕。注：H3。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | For the answer generation, we use the vLLM [CITE] framework for acceleration .（H3） |
| `Experimental Settings  [heading]` | 设置小节标题(旧式)〔S1 小节开头与研究问题〕。注：仅 H1; 导师 13/16 篇; house 2025-26 改为 Experimental Setup。 | 1/2 | 0 | 7/7 | 6/7 | 已弃用 | ...divergence in distribution shift (for more details on the experimental settings , see Appendix [REF])...（H1） |
| `We employ three commonly used metrics (A, B, C) for evaluation, with K set by default at 20.` | 常用指标〔S6 评测指标与打分〕。注：仅 H1; 导师 5 篇。 | 1/2 | 0 | 2/3 | 3/3 | 已弃用 | We employ three commonly used metrics (Hit Ratio (HR@ [M] ), Recall@ [M] , Normalized...（H1） |
| `For a fair comparison, we ...` | 说明公平比较(导师式)〔S5 基线〕。注：house仅 H1; 导师 12/16 篇。 | 1/1 | 0 | 6/7 | 6/6 | 已弃用 | ...Settings For a fair comparison , we conduct all the experiments in PyTorch with...（H1） |
| `Two high-performing encoders ... are selected as CF backbone models.` | 说明选用对象〔S2 模型与骨干〕。注：仅 H1。 | 1/1 | 0 | 1/1 | 1/1 | 已弃用 | ...encoders - ID-based (MF [CITE]) and graph-based (LightGCN [CITE]), are selected as CF backbone models...（H1） |
| `within the range provided by the corresponding references` | 沿用原文搜索范围〔S10 沿用前人设置〕。注：仅 H1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | We search for hyperparameters within the range provided by the corresponding references .（H1） |
| `We conduct experiments on three benchmark datasets: A, B, and C.` | 列数据集(导师式)〔S3 训练数据与数据划分〕。注：house 0 次; refA 8/8 篇。 | 0/0 | 0 | 8/19 | 4/6 | 仅参考 | We conduct experiments on three real-world datasets (i.e. Movielens-1M [CITE], Steam[CITE], Amazon-Book[CITE])." (X, mentor) |
| `the strongest baselines / strong baselines` | 称呼强基线(导师式)〔S5 基线〕。注：house 0 次; 导师 9 篇; house改用 the vanilla X 或具体方法名。 | 0/0 | 0 | 6/12 | 3/9 | 仅参考 | ...starred, while the strongest baselines are underlined; Imp.% measures the relative improvements of BC...（refB2） |
| `X is tuned in {a, b, c}` | 超参数搜索(导师式)〔S8 超参数与训练设置〕。注：house 0 次; refA 6 篇, refB 2 篇; house用 we search X in [a, b]。 | 0/0 | 0 | 6/8 | 2/2 | 仅参考 | ...as intervened set, and [M] is tuned in [M] with a step size of 0.02...（refB2） |
| `The statistics of the datasets are summarized in Table \ref{}.` | 数据统计表指针(导师式)〔S3 训练数据与数据划分〕。注：refA 8/8 篇; house 0 次 (house只写 dataset statistics) | 0/0 | 0 | 7/9 | 0/0 | 仅参考 | The statistics of the datasets are summarized in Table [REF].（refA1） |
| `Parameter Settings  [heading]` | 参数小节标题(导师式)〔S1 小节开头与研究问题〕。注：house 0 次; refA 4 篇。 | 0/0 | 0 | 4/6 | 1/2 | 仅参考 | Parameter Settings and Training Cost Table [REF] and [REF] show the parameter settings for X...（refB3） |
| `A grid search is conducted to confirm the optimal settings for each method` | 基线调到最优(导师式)〔S8 超参数与训练设置〕。注：house 0 次; refA 5 篇。 | 0/0 | 0 | 5/6 | 0/0 | 仅参考 | A grid search is conducted to confirm the optimal settings for each method ...（refA7） |
| `We report the average metrics for all users in the test set.` | 指标取平均(导师式)〔S6 评测指标与打分〕。注：house 0 次; refA 5 篇。 | 0/0 | 0 | 5/5 | 0/0 | 仅参考 | We report the average metrics at [M] of all instances in the test set.（refA2） |
| `Dataset Description  [heading]` | 数据小节标题(导师式)〔S1 小节开头与研究问题〕。注：house 0 次; refA 4 篇。 | 0/0 | 0 | 4/4 | 0/0 | 仅参考 | Dataset Description We consider two scenarios: movie recommendation and music recommendation.（refA2） |
| `The model parameters are initialized with the Xavier initializer` | 初始化(导师式)〔S8 超参数与训练设置〕。注：house 0 次; refA 4 篇。 | 0/0 | 0 | 4/4 | 0/0 | 仅参考 | We use the Xavier initializer [CITE] to initialize the model parameters .（refA3） |
| `The embedding size is fixed to 64 for all models` | 固定超参数(导师式)〔S8 超参数与训练设置〕。注：house 0 次; refA 2 篇。 | 0/0 | 0 | 2/2 | 0/0 | 仅参考 | The embedding size is fixed to [M] for all models.（refA3） |

## M7 报告结果

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `\textbf{<Claim sentence>.} As shown in Table / Figure \ref{}, ... (bold finding + evidence)` | 粗体结论句 + 证据句〔M7.4 粗体结论句〕。注：手工计数：LaTeX 源中 "We have the following observations:" 后的 \textbf{完整句}（不含段落小标题；H5 含附录训练动态 3 句，其余只计正文）。结构固定：粗体结论 -> "As shown in ..." -> Moreover/Additionally -> We attribute ... -> Such results ...。 | 6/37 | 5 | 0/0 | 0/0 | 常用 | X generally outperforms the vanilla MemAgent across diverse datasets.（H5） |
| `shows / exhibits a rising / decreasing / consistent trend` | 趋势名词 trend〔M7.7 趋势描述〕 | 6/23 | 5 | 2/5 | 6/17 | 常用 | ...set shows a more stable trend, outperforming experiments with [M] values of 1.0 and 0.5...（H5） |
| `reaching / over / around N (e.g. over 0.8, around 80%)` | 数值限定词〔M7.5 比较与胜出〕 | 6/15 | 5 | 7/10 | 6/11 | 常用 | Under the w/o EG mode, X generally achieves around 200% inference acceleration.（H5） |
| `..., while maintaining / preserving Y` | 同时保持〔M7.6 保持/稳定/代价〕 | 6/15 | 5 | 1/1 | 1/1 | 常用 | ...accurate exiting ratio, X exhibits much faster inference time acceleration, while maintaining the same performance...（H5） |
| `To verify / study / test / simulate this, we ...` | 回指 this 引出实验〔M7.1 结果小节开头与实验目的〕 | 6/12 | 5 | 2/2 | 3/4 | 常用 | To simulate this, we manually construct an unbalanced evidence occurrence setting, where the last evidence...（H5） |
| `X consistently outperforms / yields / performs better than Y` | 稳定胜出〔M7.5 比较与胜出〕 | 6/11 | 5 | 4/7 | 6/9 | 常用 | As shown, X consistently yields a higher inference speed, with the acceleration more obvious as...（H5） |
| `X outperforms Y (on most of the tasks)` | 胜出（主动词）〔M7.5 比较与胜出〕 | 5/29 | 4 | 8/26 | 8/17 | 常用 | X generally outperforms the vanilla MemAgent across diverse datasets.（H5） |
| `as X increases / as the layer becomes deeper` | 随……变化〔M7.7 趋势描述〕 | 5/23 | 4 | 0/0 | 6/9 | 常用 | ...yields a higher inference speed, with the acceleration more obvious as the context length increases...（H5） |
| `X significantly improves / reduces Y` | 显著提升/降低〔M7.5 比较与胜出〕。注：house "significantly" 多作副词，不一定有显著性检验。 | 5/19 | 4 | 1/1 | 2/3 | 常用 | ...ever-growing memory hurts its performance, and generating overly long memory also significantly increases inference overhead...（H5） |
| `... across multiple datasets / diverse tasks / various benchmarks` | 限定范围：跨数据集〔M7.5 比较与胜出〕 | 5/18 | 5 | 2/2 | 5/5 | 常用 | X generally outperforms the vanilla MemAgent across diverse datasets.（H5） |
| `The improvements aggregate to statistically significant gains` | 显著性说明〔M7.5 比较与胜出〕。注：H1/H2 在表注写 p-value；H6 在正文写。 | 5/15 | 4 | 3/3 | 5/7 | 常用 | ...improvements aggregate to statistically significant gains under standard two-sided tests at significance level [M]...（H6） |
| `X even gains more / can even / or even Y` | 递进强调 even〔M7.5 比较与胜出〕 | 5/14 | 5 | 2/3 | 4/7 | 常用 | Additionally, under the backbone LLM size of 3B, X even gains more.（H5） |
| `We further evaluate / test / study / analyze X on / against Y` | 追加评估〔M7.1 结果小节开头与实验目的〕 | 5/14 | 5 | 1/1 | 1/2 | 常用 | We further report the effectiveness of RL training in Figure \ref{}.（H5） |
| `X shows a sharp performance drop / performance degradation` | 性能下降〔M7.5 比较与胜出〕 | 5/13 | 4 | 1/3 | 5/14 | 常用 | ...maintains a high performance on MK task series, while MemAgent shows a sharp performance drop...（H5） |
| `X maintains Y (unaffected / the same performance)` | 保持〔M7.6 保持/稳定/代价〕 | 5/13 | 4 | 0/0 | 3/9 | 常用 | ...example, X consistently maintains a high performance on MK task series, while MemAgent shows...（H5） |
| `... on most of the tasks / in most cases / on most metrics` | 限定范围：多数情况〔M7.5 比较与胜出〕 | 5/12 | 4 | 6/14 | 2/3 | 常用 | ...w/o EG and w EG inference modes, X outperforms MemAgent on most of the tasks...（H5） |
| `X generally outperforms Y` | 总体胜出（留余地）〔M7.5 比较与胜出〕。注：house的留余地版本（H5 粗体句）。 | 5/8 | 4 | 0/0 | 0/0 | 常用 | X generally outperforms the vanilla MemAgent across diverse datasets.（H5） |
| `In contrast, the baselines exhibit relatively lower and less robust performance` | 反衬基线〔M7.5 比较与胜出〕。注：结果段用来反衬基线（H4 7 次）；refA 很少用。 | 4/17 | 3 | 4/7 | 8/22 | 常用 | In contrast, when we assign random values between 0 and 1 as rubric rewards (i.e...（H6） |
| `X gradually outperforms / becomes harder` | 渐进〔M7.7 趋势描述〕 | 4/12 | 3 | 2/2 | 4/6 | 常用 | The correct exit behavior is gradually learned.（H5） |
| `We have the following observations:` | 引出观察列表〔M7.3 引出观察〕。注：2026 三篇的固定句；H4 也用 "..., with following observations:"。 | 4/11 | 4 | 7/18 | 0/0 | 常用 | We have the following observations:（H5） |
| `becomes more evident / more refined / more separable as X` | 变得更……〔M7.7 趋势描述〕 | 4/11 | 4 | 1/1 | 1/1 | 常用 | ...rubrics, it gradually becomes harder to gain the CoT reward [M] Rub [M] as...（H6） |
| `We observe that (:)` | 引出观察〔M7.3 引出观察〕。注：H1、H2 用 "We observe that:" 引列表；2026 改为 "We have the following observations:"。 | 4/10 | 3 | 5/6 | 8/14 | 常用 | ...we observe that even post-trained versions of such small-sized models (e.g. Qwen3-8B) cannot always...（H6） |
| `baselines struggle to balance A and B / show instability` | 基线不稳〔M7.5 比较与胜出〕 | 4/9 | 3 | 1/2 | 3/7 | 常用 | ...safety without compromising utility across various tasks, while baseline methods show instability in preserving utility...（H4） |
| `X shows potential in Y / suggests the potential of Y` | 潜力〔M7.5 比较与胜出〕 | 4/8 | 3 | 2/3 | 2/8 | 常用 | ...moderate positive steering shows potential in enhancing performance across multiple math datasets, including MATH500...（H3） |
| `For example, X consistently maintains ..., while Y shows a sharp drop.` | 举具体数据/任务作证据〔M7.5 比较与胜出〕。注：house轻用（导师重用）。 | 4/7 | 4 | 8/44 | 6/18 | 常用 | For example, X consistently maintains a high performance on MK task series, while MemAgent shows...（H5） |
| `X enhances A without compromising / harming B` | 不损失另一指标〔M7.6 保持/稳定/代价〕 | 4/6 | 4 | 0/0 | 3/3 | 常用 | ...a maximum 400% times faster in several cases, such as MK-1, without harming the performance...（H5） |
| `Y drops dramatically / increases stably / decreases progressively` | 变化 + 程度副词〔M7.7 趋势描述〕 | 4/6 | 3 | 2/2 | 1/1 | 常用 | ...on evidence-free chunks drops dramatically, indicating that without the update reward, LLMs tend to...（H5） |
| `..., achieving N (e.g. achieving an exit accuracy over 0.8)` | 分词尾给出数值〔M7.5 比较与胜出〕 | 4/5 | 4 | 1/2 | 2/2 | 常用 | ...be even faster, achieving a maximum 400% times faster in several cases, such as...（H5） |
| `X brings performance gain(s) / improvement(s)` | 带来提升〔M7.5 比较与胜出〕 | 4/5 | 4 | 0/0 | 0/0 | 常用 | ...RL training generally brings performance gain, especially on hard tasks such as HQA, SQuAD,...（H5） |
| `X fails to / even fails on Y` | 失败〔M7.5 比较与胜出〕 | 3/8 | 2 | 7/24 | 5/17 | 常用 | ...figure), the LLM fails to achieve performance improvements and even exhibits sudden performance drops...（H6） |
| `is approximately proportional to / highly correlates with Y` | 相关性〔M7.7 趋势描述〕。注：H3 机制分析。 | 3/7 | 2 | 1/1 | 3/3 | 常用 | ...of these pre-allocated vectors highly correlate with the required reasoning token number, showing implicit connections...（H3） |
| `X (yields / exhibits / achieves) superior Y` | 优越表现〔M7.5 比较与胜出〕 | 3/7 | 3 | 0/0 | 1/2 | 常用 | X exhibits superior inference efficiency compared to the vanilla MemAgent.（H5） |
| `much better / lower / faster than X` | 程度比较〔M7.5 比较与胜出〕 | 3/6 | 3 | 2/2 | 2/2 | 常用 | ...to MemAgent, X excels at out-of-distribution tasks, as it performs much better on NIAH tasks...（H5） |
| `X performs well / excellently across Y` | 表现良好（平铺）〔M7.5 比较与胜出〕 | 3/3 | 2 | 0/0 | 3/4 | 常用 | Our proposed method X also perform well under the evaluation of Llama-Guard-3-8B, outperforming existing baselines.（H4） |
| `To further verify / examine / test whether X, we ...` | "To further"引出追加实验〔M7.1 结果小节开头与实验目的〕。注：附录追加实验的标准开头（H4 3 次）。 | 3/3 | 3 | 0/0 | 2/2 | 常用 | To further verify the absence of information leakage, we compute BLEU similarity between all malicious...（H4） |
| `\textbf{X (generally / consistently) outperforms Y across Z.}` | 粗体结论：胜出型〔M7.4 粗体结论句〕。注：手工计数（粗体句中）。H1 "consistently outperforms"，H5 "generally outperforms"，H6 "outperforms"。 | 3/3 | 2 | 0/0 | 0/0 | 常用 | X outperforms the outcome-centric RLVR training.（H6） |
| `Based on the reliability of X / Based on the above, we further test Y / we adopt ...` | RQ 之间的承接（上一 RQ 结论作前提）〔M7.1 结果小节开头与实验目的〕。注：H6 RQ2 开头；H5 "Based on the above, we adopt ..."；H3 "Based on the linear regression experiments, we have ..."。 | 3/3 | 3 | 0/0 | 0/0 | 常用 | Based on the above, we adopt the [M] of 0.9 as the default setting due...（H5） |
| `Y remains largely unchanged / unaffected` | 基本不变〔M7.6 保持/稳定/代价〕 | 2/13 | 2 | 1/1 | 0/0 | 常用 | ...of benign prompts remain largely unchanged (Figure \ref{}), while those of malicious prompts shift...（H4） |
| `X reduces the inference time to 1/4 of Y / achieves around 200% acceleration` | 效率提升（倍数）〔M7.5 比较与胜出〕。注：H5 原稿的 "400% times faster" 是冗余写法，勿用。 | 2/12 | 2 | 0/0 | 0/0 | 常用 | Under the w/o EG mode, X generally achieves around 200% inference acceleration.（H5） |
| `Y drops / decreases accordingly` | 相应变化〔M7.7 趋势描述〕 | 2/6 | 2 | 1/3 | 2/3 | 常用 | As shown, X can identify the last evidence position and exit accordingly in most cases.（H5） |
| `We (can) find (out) that ...` | 引出发现〔M7.3 引出观察〕 | 2/4 | 2 | 7/23 | 6/12 | 常用 | We can find that our predictor yields significantly longer reasoning lengths on overthink questions than...（H3） |
| `comparable to (or even surpassing) X / comparable cost` | 持平〔M7.5 比较与胜出〕 | 2/4 | 2 | 6/13 | 3/5 | 常用 | ...Jailbreak Antidote have comparable cost dominated by PCA/SVD, while Surgical is the lightest, relying...（H4） |
| `... by a large margin` | 大幅领先〔M7.5 比较与胜出〕 | 2/3 | 2 | 4/4 | 3/4 | 常用 | ...all selected jailbreak attacks, consistently outperforming all the methods by a large margin on average...（H4） |
| `... on average` | 平均意义上〔M7.5 比较与胜出〕 | 2/2 | 2 | 1/1 | 2/2 | 常用 | ...with X outperforms the vanilla RLVR across multiple datasets and multiple model sizes on average...（H6） |
| `the performance gain is more obvious on X` | 提升在某处更明显〔M7.5 比较与胜出〕 | 2/2 | 2 | 1/1 | 0/0 | 常用 | ...yields a higher inference speed, with the acceleration more obvious as the context length increases...（H5） |
| `\textbf{X enhances A without compromising B, while baselines show instability in B.}` | 粗体结论：兼顾型〔M7.4 粗体结论句〕。注：手工计数（粗体句中）；H2 "..., without compromising performance on source datasets."。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | X enhances safety without compromising utility across various tasks（H4） |
| `nearly identical to X` | 几乎相同〔M7.5 比较与胜出〕。注：H4 "nearly identical to the vanilla models"。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...X demonstrates high performance on all the utility tasks, nearly identical to the vanilla models...（H4） |
| `There is only one exception on X, where ... / The only exception lies in X` | 说明例外〔M7.5 比较与胜出〕。注：附录里交代反例的固定句。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | There is only one exception on R1-Distill-Qwen-1.5B, where reducing the reasoning token number even brings...（H3） |
| `X surpasses Y / comparable to or even surpassing Y` | 超过〔M7.5 比较与胜出〕 | 2/6 | 1 | 0/0 | 4/6 | 偶用 | ...be unleashed by carefully designing the model, showcasing the potential to surpass prevailing ID-based recommenders...（H2） |
| `the (second-)best baseline` | 参照最强基线〔M7.5 比较与胜出〕 | 2/6 | 1 | 0/0 | 1/1 | 偶用 | The best-performing methods are bold, while the second-best methods are underlined.（H2） |
| `We have similar observations to those in Section \ref{} / exhibits the same pattern` | 同样现象（附录复现）〔M7.3 引出观察〕。注：附录复现段落的固定开头。 | 2/5 | 1 | 0/0 | 0/0 | 偶用 | ...linear regression exhibits the same pattern of an increasing trend as the model depth increases...（H3） |
| `remarkable improvement / performance` | 可观（早期）〔M7.5 比较与胜出〕 | 2/4 | 1 | 3/3 | 7/13 | 偶用 | ...does not bring a stable and remarkable improvement, which is consistent with previous findings \cite{}...（H2） |
| `(yields) consistent improvement(s) / a consistent boost` | 稳定提升〔M7.5 比较与胜出〕 | 2/4 | 1 | 5/6 | 3/3 | 偶用 | ...Only in the figure), the reasoning capability of the LLM shows consistent improvement during training...（H6） |
| `X only adds negligible cost / a marginal increase` | 代价可忽略〔M7.6 保持/稳定/代价〕 | 2/4 | 1 | 3/3 | 2/4 | 偶用 | ...projection and update per layer, which is negligible compared to the base model’s forward pass...（H4） |
| `To evaluate the effectiveness of X in Y, we measure / conduct ...` | 目的不定式引出实验〔M7.1 结果小节开头与实验目的〕 | 2/3 | 1 | 3/3 | 2/2 | 偶用 | To evaluate the effectiveness of this modification, we conduct an ablation study on the adopted...（H2） |
| `X excels at / on Y` | 擅长某类任务〔M7.5 比较与胜出〕 | 2/3 | 1 | 0/0 | 3/4 | 偶用 | ...to MemAgent, X excels at out-of-distribution tasks, as it performs much better on NIAH...（H5） |
| `It can be observed that / We can observe that` | 被动/情态观察句〔M7.3 引出观察〕 | 2/3 | 1 | 0/0 | 2/2 | 偶用 | We can observe that, as the steering strength [M] increases from -0.2 to 0.2, the...（H3） |
| `significant improvements / gains over X` | 显著提升（名词式）〔M7.5 比较与胜出〕 | 2/2 | 1 | 7/15 | 3/8 | 偶用 | ...improvements aggregate to statistically significant gains under standard two-sided tests at significance level [M]...（H6） |
| `with the increase in X` | 随……增加（名词式）〔M7.7 趋势描述〕 | 2/2 | 1 | 0/0 | 3/4 | 偶用 | With the increase in the DSR on all jailbreak attacks, X consistently maintains the performance...（H4） |
| `at the expense of / at an acceptable cost of` | 以……为代价〔M7.6 保持/稳定/代价〕 | 2/2 | 1 | 0/0 | 2/2 | 偶用 | In contrast, X significantly improves uniformity at an acceptable cost of increasing align loss.（H1） |
| `up to a certain threshold / beyond a certain point` | 拐点〔M7.7 趋势描述〕 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | ...strength beyond a certain point does not lead to further gains, and may even...（H3） |
| `with an improvement ranging from a% to b% on M compared to the best baseline` | 给出提升区间〔M7.5 比较与胜出〕。注：只见于 H1、H2（推荐领域）。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | ...datasets, with an improvement ranging from 6.79% to 9.75% on Recall@20 compared to the...（H2） |
| `The more / less X, the better / worse Y` | 比较级对举〔M7.7 趋势描述〕 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | The better the space coverage is, the better the performance on utility preservation is.（H4） |
| `X is relatively robust to Y` | 稳健性结论〔M7.6 保持/稳定/代价〕 | 1/2 | 1 | 1/1 | 2/2 | 偶用 | Language representations are relatively robust to prompt disturbances.（H2） |
| `We also see / note that ...` | 追加观察（also）〔M7.3 引出观察〕 | 1/2 | 1 | 1/1 | 0/0 | 偶用 | We also see that the performance improves with more recent and advanced LMs.（H2） |
| `\textbf{A higher X improves A, but also increases the risk of B.} / \textbf{A mild X leads to a balanced ...}` | 粗体结论：超参数取舍型〔M7.4 粗体结论句〕。注：手工计数；敏感性分析的粗体句模板。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | A higher α improves accuracy on evidence-present chunks, but also increases the risk（H5） |
| `\textbf{X exist / are indeed responsible for Y, since Z.}` | 粗体结论：机制型（带 since 理由）〔M7.4 粗体结论句〕。注：手工计数；H3 机制分析的粗体句常带 ", since ..." 给出依据。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Such pre-allocated vectors are indeed responsible for the reasoning strength planning, since ...（H3） |
| `\textbf{Y is rapidly acquired / gradually learned.}` | 粗体结论：训练动态型〔M7.4 粗体结论句〕。注：手工计数；H5 附录训练动态。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | The correct exit behavior is gradually learned.（H5） |
| `Additionally, we observe that ...` | 追加观察〔M7.3 引出观察〕。注：H6 用法。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...Additionally, we observe that the performance gain is more obvious on larger size models...（H6） |
| `Consistent observations can be found on X in Appendix \ref{}.` | 复现结果指向附录〔M7.3 引出观察〕。注：只见于 H1。 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Consistent observations can be found on KuaiRec in Appendix \ref{}.（H1） |
| `X achieves the best performance / relative improvement of N%` | 最佳/相对提升（导师式）〔M7.5 比较与胜出〕。注：house不用 "achieves the best performance"；house用 "outperforms ... on most of the tasks"。 | 0/0 | 0 | 8/10 | 6/11 | 仅参考 |  |

## M8 分析与归因

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `X may Y (hedged claim)` | 留余地（may）〔M8.1 解读〕。注：house may 1.55/1k，高于两位导师。 | 6/59 | 5 | 6/17 | 7/17 | 常用 | ...updates the memory on a few critical chunks, while MemAgent may indiscriminately update the memory...（H5） |
| `These/Such results/findings suggest/show ...` | 结果名词作主语回指〔回指·结果句〕 | 6/32 | 5 | 4/7 | 7/16 | 常用 | Such phenomena show the effectiveness of self-evolving in enhancing reasoning.（H6） |
| `..., indicating that / indicating X` | 解读（分词尾）〔M8.1 解读〕。注：house最常用的解读形式。 | 6/25 | 5 | 7/11 | 8/15 | 常用 | ...chunks drops dramatically, indicating that without the update reward, LLMs tend to update the...（H5） |
| `..., which enables it to / enabling Y` | 机制说明（which enable）〔M8.2 归因〕 | 6/14 | 5 | 1/1 | 3/11 | 常用 | ...vector reconstruction capabilities, which enable it to consistently steer the activations of these malicious...（H4） |
| `\textbf{Claim}, since Z. / ..., since Z (reason clause after a finding)` | 理由从句 since〔M8.2 归因〕。注：H3 粗体结论句用 ", since ..." 给依据。 | 5/14 | 5 | 7/26 | 2/3 | 常用 | We attribute this to the possibility of overfitting, since the MLP may be over-parameterized.（H4） |
| `..., thereby V-ing / ..., thus V-ing` | 结果（分词尾）〔M8.2 归因〕 | 4/20 | 3 | 5/15 | 7/21 | 常用 | ...significantly improve the DSR of LLMs against jailbreak attacks, thereby enhancing their safety during inference...（H4） |
| `This indicates/suggests/demonstrates that ...` | 回指上句并下结论〔回指·结果句〕 | 4/18 | 3 | 8/26 | 7/14 | 常用 | This indicates that self-evolving enables the policy model to gradually propose more informative rubrics that...（H6） |
| `demonstrate / validate / show the effectiveness of X` | 证明有效〔M8.1 解读〕 | 4/10 | 3 | 7/11 | 5/9 | 常用 | Such phenomena validate the effectiveness of X, which provides a "free-lunch" for improving the RL...（H6） |
| `We attribute this (improvement / drop / phenomenon) to X` | 归因（回指 this）〔M8.2 归因〕 | 4/10 | 3 | 2/3 | 5/6 | 常用 | We attribute this to its heuristically predefined condition rules, which mistakenly classify these math problems...（H4） |
| `These results (further) indicate / suggest / show / demonstrate / confirm ...` | 总括性解读〔M8.1 解读〕。注：H4 把 "The above phenomena" 改成 "These results demonstrate"。 | 4/9 | 3 | 0/0 | 1/2 | 常用 | These results confirm that X generalizes to unseen attacks rather than memorizing or overfitting to...（H4） |
| `It is worth noting that ... / It's worth noting` | 提示读者注意〔M8.4 立场与强调〕。注：H1 用 "It's"（已弃）；2026 仍有 "It is worth noting that"（H4、H6）。切片曾标为禁用，但不在禁用清单上，层级按计数重定；不要写缩写 It's，写全 It is。 | 4/7 | 3 | 2/3 | 3/3 | 常用 | It is worth noting that Llama-Guard-3-8B tends to yield higher DSR than GPT-4o, with a...（H4） |
| `..., showcasing X` | 展示（分词尾）〔M8.1 解读〕 | 4/6 | 3 | 1/3 | 6/9 | 常用 | The results showcase the effectiveness of the X on varying model sizes.（H4） |
| `This phenomenon highlights / indicates / is because ...` | 回指现象〔M8.1 解读〕 | 4/6 | 4 | 0/0 | 2/2 | 常用 | This phenomenon is because X only updates the memory on a few critical chunks, while...（H5） |
| `..., (further) proving / confirming X` | 证实（分词尾）〔M8.1 解读〕 | 4/4 | 3 | 0/0 | 1/1 | 常用 | ...which shows improved performance when rubrics are used as hints—proving the rubrics effectively guide reasoning...（H6） |
| `X reveals that / We reveal that ...` | 揭示〔M8.1 解读〕 | 3/12 | 2 | 5/14 | 7/10 | 常用 | We further reveal that this performance drop mainly comes from the decreased space coverage.（H4） |
| `This indicates that ...` | 解读：说明〔M8.1 解读〕。注：H3 最重。 | 3/12 | 2 | 7/9 | 4/5 | 常用 | This indicates that self-evolving enables the policy model to gradually propose more informative rubrics that...（H6） |
| `X is mainly responsible for Y / mainly comes from Z` | 主要来源〔M8.1 解读〕 | 3/9 | 2 | 4/6 | 2/2 | 常用 | We further reveal that this performance drop mainly comes from the decreased space coverage.（H4） |
| `This aligns with findings from prior research \cite{} / aligns well with X` | 吻合〔M8.3 与已有发现对照〕 | 3/9 | 2 | 0/0 | 4/16 | 常用 | ...model to gradually propose more informative rubrics that better align with the final answer correctness...（H6） |
| `These findings underscore / highlight ...` | 回指发现（findings）〔M8.1 解读〕 | 3/8 | 2 | 0/0 | 1/1 | 常用 | These findings underscore the importance of fine-grained hardness, suggesting that X learns more generalized representations.（H1） |
| `..., suggesting that / suggesting X` | 解读（分词尾，较弱）〔M8.1 解读〕 | 3/7 | 2 | 4/7 | 2/2 | 常用 | ...different model kinds, suggesting that the reasoning strength planning is possibly encoded in the...（H3） |
| `Such results / phenomena show / highlight / validate ...` | 总括性解读（Such）〔M8.1 解读〕。注：H6 段末签名句。 | 3/6 | 3 | 0/0 | 0/0 | 常用 | Such property makes X work more flexibly under evidence-unbalanced scenarios.（H5） |
| `..., limiting / making it difficult to Y` | 负面后果（分词尾）〔M8.2 归因〕。注：H4 修订：把 "which makes it difficult for them to" 改为 ", limiting ..."。 | 3/5 | 3 | 1/1 | 0/0 | 常用 | ...their heuristic designs, limiting generalization to diverse or evolving jailbreaks. which makes it difficult...（H4） |
| `We attribute this to the possible / the fact that / the possibility of X` | 归因（带模糊语）〔M8.2 归因〕 | 3/5 | 3 | 1/1 | 0/0 | 常用 | We attribute this to the possibility of overfitting, since the MLP may be over-parameterized.（H4） |
| `highlight(s) the importance of X` | 强调重要性〔M8.1 解读〕 | 3/4 | 2 | 3/3 | 1/1 | 常用 | Such results highlight the importance of rubrics self-evolving.（H6） |
| `These findings suggest ...` | 回指发现〔回指·结果句〕。注：H3、H6。 | 3/4 | 2 | 0/0 | 1/1 | 常用 | These findings suggest that LRMs pre-plan their reasoning strength through pre-allocating a direction vector, whose...（H3） |
| `If X still holds under this setup, it would indicate that Y / A good result will support our hypothesis` | 预设检验逻辑（would indicate）〔M8.1 解读〕。注：H6 RQ1、H2 线性映射、H3 探针：先说"结果好则说明什么"，再报告结果。 | 3/4 | 3 | 0/0 | 0/0 | 常用 | ...effectively under this setup, it would indicate that self-proposed rubrics can yield meaningful RL signals...（H6） |
| `To understand why X, we compare Y` | 引出原因分析〔M8.2 归因〕 | 3/3 | 2 | 1/1 | 0/0 | 常用 | ... to understand how generated rubrics guide models toward better reasoning.（H6） |
| `The above findings / observations suggest ...` | 回指多条发现〔M8.1 解读〕。注：H4 的 1 次是被 \ignore 删掉的 "The above phenomena"（改成 "These results"）；实际只在 H2、H3。 | 2/5 | 2 | 0/0 | 0/0 | 常用 | The above observations suggest that LRMs may have the capability of planning the reasoning strength...（H3） |
| `This suggests (that) ...` | 解读：暗示（较弱）〔M8.1 解读〕 | 2/4 | 2 | 2/6 | 3/3 | 常用 | This suggests that LRMs may utilize a single, shared directional vector to distinguish between questions...（H3） |
| `We (carefully) analyze X and find that ... / We analyze this discrepancy by ...` | 深挖原因〔M8.2 归因〕 | 2/4 | 2 | 1/2 | 2/3 | 常用 | ... We analyze the per-layer computational complexity of X.（H4） |
| `This (phenomenon) is because X` | 归因（because）〔M8.2 归因〕 | 2/3 | 2 | 1/1 | 0/0 | 常用 | This phenomenon is because X only updates the memory on a few critical chunks, while...（H5） |
| `Such results show / highlight (the importance of) ...` | 回指结果并下结论〔回指·结果句〕。注：H6。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | Such results show that the self-proposed and self-evolving rubrics can yield meaningful CoT rewarding signals,...（H6） |
| `The results show / indicate that ...` | "The results"作主语〔M8.1 解读〕 | 2/3 | 2 | 0/0 | 0/0 | 常用 | The results showcase the effectiveness of the X on varying model sizes.（H4） |
| `We attribute the success of X to its Y` | 归因成功〔M8.2 归因〕。注：H4 2、H5 1：主结果段的标准句。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | We attribute the success of X to its more stable memory updating with the introduced...（H5） |
| `We further reveal that this performance drop mainly comes from X` | 追加揭示原因〔M8.2 归因〕 | 2/3 | 2 | 0/0 | 0/0 | 常用 | We further reveal that this performance drop mainly comes from the decreased space coverage.（H4） |
| `..., which is consistent with previous findings / our findings in Section \ref{}` | 与已有发现一致〔M8.3 与已有发现对照〕 | 2/4 | 1 | 5/7 | 3/7 | 偶用 | ...does not bring a stable and remarkable improvement, which is consistent with previous findings \cite{}...（H2） |
| `This demonstrates / validates / reveals that ...` | 强解读（This + 强动词）〔M8.1 解读〕 | 2/3 | 1 | 6/12 | 5/11 | 偶用 | This demonstrates that steering with the pre-allocated vector enables a simple yet effective test-time scaling...（H3） |
| `X plays a more significant / vital role in Y` | 作用大小〔M8.1 解读〕 | 2/3 | 1 | 2/2 | 2/2 | 偶用 | ...objective, rather than data augmentation, plays a more significant role in improving recommendation performance \cite{}...（H2） |
| `We attribute the performance gap / superior performance / above findings to X` | 归因（名词短语）〔M8.2 归因〕 | 2/3 | 1 | 1/1 | 0/0 | 偶用 | We attribute the above findings to the homomorphism between the language space and a good...（H2） |
| `Overall, our results suggest ... / In summary / To summarize, ...` | 总结句〔M8.4 立场与强调〕。注：house 2 次：H1 为小节标题 "Overall Performance Comparison"，H6 为引言 "Overall, our results suggest ..."；结果段内house不用总结副词，改用 "Such results ..."。 | 2/2 | 1 | 4/8 | 7/17 | 偶用 | Overall, our results suggest a new paradigm in which models autonomously generate ...（H6） |
| `This is likely due to X, limiting Y` | 归因（likely due to）〔M8.2 归因〕 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | This is likely due to their heuristic designs, limiting generalization to diverse or evolving jailbreaks...（H4） |
| `X benefits from Y` | 受益于〔M8.3 与已有发现对照〕 | 1/4 | 1 | 3/5 | 1/1 | 偶用 | ...language representations generally benefits from an increased amount of training data, without compromising performance...（H2） |
| `If X, then Y are expected to show Z (falsifiable prediction before a test)` | 先给可检验的预测〔M8.1 解读〕。注：H3 1 次（"If the LRM plans ..., then the four vectors are expected to show ..."）； | 1/1 | 1 | 3/4 | 4/4 | 偶用 | ...directional vector, then the four vectors are expected to show a high degree of similarity...（H3） |
| `It is worth mentioning that ...` | 值得一提（导师式）〔M8.4 立场与强调〕 | 1/1 | 1 | 3/3 | 2/3 | 偶用 | It is worth mentioning that during our initial attempts to directly prompt the proprietary teacher...（H6） |
| `X may be attributed to Y` | 归因（被动）〔M8.2 归因〕。注：house唯一一次（H4）在源文件中已被 \ignore 删除，改成 "may stem from"，视为已弃；导师仍用。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 1/1 | 2/3 | 偶用 | [已删] The inferior performance of the baselines ... may be attributed to ...（H4） |
| `lay an important foundation for X` | 为后续铺垫〔M8.1 解读〕。注：H6 RQ1 -> RQ2 的承接。 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | ...rubrics can yield meaningful CoT rewarding signals, which lay an important foundation for our approach...（H6） |
| `..., possibly because X` | 可能的原因（句中）〔M8.2 归因〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...& Scientific dataset, possibly because it captures user behavioral patterns of the same platform...（H2） |
| `We attribute this phenomenon to two aspects. On the one hand, ... On the other hand, ...` | 两方面归因〔M8.2 归因〕。注：H2；两点原因的完整模板。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We attribute this phenomenon to two aspects.（H2） |
| `X may stem from Y` | 归因（may stem from）〔M8.2 归因〕。注：H4 修订时把 "may be attributed to" 改为 "may stem from"。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The weaker performance of baselines ... may stem from their trade-off strategy in preserving utility（H4） |
| `Jointly analyzing Table X and Y, we ...` | 联合分析（refA 式）〔M8.3 与已有发现对照〕。注：refA 签名式；house不用。 | 0/0 | 0 | 7/12 | 2/2 | 仅参考 |  |
| `This verifies / which verifies X` | 验证（导师式）〔M8.3 与已有发现对照〕。注：house不用；house用 "This validates" 或 "confirming"。 | 0/0 | 0 | 5/6 | 3/5 | 仅参考 |  |
| `demonstrate(s) the rationality and effectiveness of X` | 合理且有效（导师式）〔M8.3 与已有发现对照〕。注：在禁用/弃用清单上。 | 0/0 | 0 | 7/21 | 2/3 | 禁用 |  |

## M9 消融与敏感性

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `with varying X / varying numbers of` | 变化的取值〔M9.2 超参数与敏感性〕 | 5/41 | 4 | 8/20 | 7/16 | 常用 | More results under varying context lengths can be found in Appendix \ref{}.（H5） |
| `Effect of X. (paragraph title)` | 段落小标题：效应〔M9.1 消融设置与变体命名〕。注：只计正文/附录（段落或小节标题）。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 5/17 | 4 | 3/15 | 4/5 | 常用 | Effect of Varying Language Representations in X We evaluate the effect of different language representations...（H2） |
| `... under different X (settings / strengths)` | 在不同取值下〔M9.2 超参数与敏感性〕 | 5/15 | 5 | 0/0 | 2/2 | 常用 | ...the training dynamics under different selection of [M] (i.e. 1.0, 0.9, and 0.5) in...（H5） |
| `ablation study / studies` | 消融实验名词〔M9.1 消融设置与变体命名〕 | 4/15 | 4 | 4/7 | 4/13 | 常用 | RQ3: (Ablation Study) How does the hyperparameter [M] affect the training dynamics of X?（H5） |
| `varying X from a to b / from -0.2 to 0.2 (with an interval of 0.05)` | 给出取值范围〔M9.2 超参数与敏感性〕 | 4/12 | 3 | 0/0 | 3/6 | 常用 | Increasing the strength from 0 to 0.4 yields the following: The activations of benign prompts...（H4） |
| `Based on the above, we adopt X of v as the default setting due to Y` | 选定默认值并给理由〔M9.2 超参数与敏感性〕。注：H5 敏感性段的收尾句。 | 4/6 | 3 | 5/7 | 3/3 | 常用 | ...[M] of 0.9 as the default setting due to the relatively high reward and...（H5） |
| `A higher / larger / mild X leads to / improves ...` | 以超参数取值作主语〔M9.2 超参数与敏感性〕 | 4/4 | 3 | 2/2 | 3/3 | 常用 | A mild [M] leads to a balanced update accuracy on both evidence-present and evidence-free chunks.（H5） |
| `X (w/o C)` | 消融变体命名〔M9.1 消融设置与变体命名〕。注：house统一用 "X (w/o C)"；H2 "w/o MLP"，H6 "H6 (w/o evolving)"；H5 的 w/o EG 是推理模式名。 | 3/17 | 3 | 0/0 | 2/3 | 常用 | ... gradually outperforms the ablation version (i.e. X (w/o evolving)).（H6） |
| `removing / abandoning X` | 去除式消融〔M9.1 消融设置与变体命名〕 | 3/9 | 3 | 6/8 | 4/13 | 常用 | ...8B-sized model by abandoning the evolving reward [M] Rub [M] , and the rubricator...（H6） |
| `we disable X to build a variant / a variant of X` | 变体（variant）〔M9.1 消融设置与变体命名〕。注：refA 用 "variant" 命名消融模型；house的 6 次多指攻击变体或 H2 的 XSimGCL 变体，house消融用 "(w/o C)"。 | 3/6 | 2 | 7/18 | 6/14 | 常用 | ...84-100% across FlipAttack variants, and 100% on WildGuard Test at default steering strength [M]...（H4） |
| `We conduct an ablation study on X by abandoning / replacing Y` | 引出消融〔M9.1 消融设置与变体命名〕 | 3/5 | 3 | 1/2 | 1/1 | 常用 | ...this section, we conduct ablation studies on two aspects: the impact of [M] selection...（H5） |
| `strike a balance / a balanced Y` | 平衡〔M9.2 超参数与敏感性〕 | 3/5 | 2 | 0/0 | 2/2 | 常用 | A mild [M] leads to a balanced update accuracy on both evidence-present and evidence-free chunks.（H5） |
| `Impact of X (on Y). (paragraph title)` | 段落小标题：影响〔M9.1 消融设置与变体命名〕。注：只计正文/附录（段落或小节标题）。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/11 | 2 | 4/19 | 1/2 | 常用 | The Impact of Steering Strength [M] (RQ2)strength In this section, we study how X gradually...（H4） |
| `We visualize the training dynamics under different selection of X` | 训练动态图〔M9.2 超参数与敏感性〕 | 2/11 | 2 | 0/0 | 0/0 | 常用 | We visualize the training dynamics under different selection of [M] (i.e. 1.0, 0.9, and 0.5)...（H5） |
| `replacing X with Y` | 替换式消融〔M9.1 消融设置与变体命名〕 | 2/7 | 2 | 1/1 | 3/4 | 常用 | Replacing Linear Regression with Multilayer Perceptron We report the choice of replacing the linear regression...（H4） |
| `the ablation version / our ablation baseline` | 称呼消融变体〔M9.1 消融设置与变体命名〕 | 2/5 | 2 | 0/0 | 0/0 | 常用 | ...a more stable learning curve and gradually outperforms the ablation version (i.e. X (w/o evolving))...（H6） |
| `Effectiveness of X. (paragraph title)` | 段落小标题：有效性〔M9.1 消融设置与变体命名〕 | 2/4 | 2 | 0/0 | 0/0 | 常用 | Effectiveness of RL training.（H5） |
| `By varying X, we have (the) following observations` | 变动超参数后引出观察〔M9.2 超参数与敏感性〕 | 2/3 | 2 | 2/2 | 0/0 | 常用 | By varying the target difficulty [M] from 1 to 5, we can get four such...（H3） |
| `To study how X affects Y, we report the performance under different X in Table \ref{}` | 引出敏感性实验〔M9.2 超参数与敏感性〕 | 2/2 | 2 | 2/2 | 0/0 | 常用 | To study how selection of [M] (the fraction of retained singular vectors in the benign...（H4） |
| `Study of / on X (paragraph title)` | 段落小标题：研究〔M9.1 消融设置与变体命名〕 | 2/4 | 1 | 5/6 | 6/7 | 偶用 | Study of Eigenvalues（H4） |
| `X is more sensitive to Y` | 敏感〔M9.2 超参数与敏感性〕。注：refB 常用 "sensitivity"；house极少。 | 2/4 | 1 | 4/6 | 6/11 | 偶用 | Interestingly, DeepSeek-R1-Distill-Llama-8B is more sensitive to the steering strength [M] .（H3） |
| `does not lead to further gains, and may even degrade performance` | 过犹不及〔M9.2 超参数与敏感性〕 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | ...beyond a certain point does not lead to further gains, and may even degrade performance...（H3） |
| `each component (in X) contributes positively` | 各组件都有贡献〔M9.1 消融设置与变体命名〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...\ref{}, each component contributes positively (see more ablation results and details in Appendix \ref{})...（H2） |
| `the performance degradation caused by (w/o C) indicates that ...` | 由下降推出组件作用〔M9.1 消融设置与变体命名〕。注：H2 附录。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Specifically, the performance degradation caused by replacing the MLP with a linear weight matrix (w/o...（H2） |
| `the optimal X lies around v` | 最优值〔M9.2 超参数与敏感性〕 | 1/1 | 1 | 5/11 | 3/8 | 偶用 | ...trained on the mixed dataset, while the optimal [M] for Video Games lies around 0.2...（H2） |
| `In this section, we conduct ablation studies on two aspects: A, and B.` | 消融小节开头〔M9.1 消融设置与变体命名〕。注：H5 RQ3 首句。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...this section, we conduct ablation studies on two aspects: the impact of [M] selection...（H5） |
| `hyper-parameter study / sensitivity analysis` | 超参分析名词〔M9.2 超参数与敏感性〕 | 1/1 | 0 | 0/0 | 3/5 | 已弃用 | ...X inherits the hyperparameter sensitivity property of adversarial learning, therefore it's necessary to choose...（H1） |
| `we search X in the range of {..}` | 搜索范围（导师式）〔M9.2 超参数与敏感性〕。注：house不用；house直接写 "from a to b"。 | 0/0 | 0 | 5/6 | 2/2 | 仅参考 |  |

## 图表指代

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `As shown in Figure \ref{}, X ...` | 句首引图〔M7.2 图表指针〕。注：house签名式证据指针；refA 0 次。 | 6/29 | 5 | 3/4 | 2/2 | 常用 | As shown in Figure \ref{}, X shows a much lower memory size increasing speed, while...（H5） |
| `We (also) report X in Table / Figure \ref{}` | 报告某项结果〔M7.2 图表指针〕。注：house最常用的报告句；H4/H5/H6 实验段都以此起头。 | 6/22 | 5 | 1/2 | 1/1 | 常用 | ...We report the averaged performance and inference time cost across different context sizes in...（H5） |
| `Figure \ref{} depicts / shows / illustrates / reflects X` | 图作主语〔M7.2 图表指针〕 | 6/16 | 5 | 7/21 | 7/21 | 常用 | Figure \ref{} reflects the update accuracy on evidence-present (\ref{}) and evidence-free (\ref{}) chunks.（H5） |
| `As shown in Table \ref{}, X ...` | 句首引表〔M7.2 图表指针〕。注：house 6/6 篇；H4 最多（7）。导师 refA 0 次。 | 6/16 | 5 | 0/0 | 4/5 | 常用 | As shown in Table \ref{}, under both w/o EG and w EG inference modes, X...（H5） |
| `The effect / impact of X (on Y).` | 图注：效应〔M7.8 图注套话〕。注：只计图注。 | 5/30 | 4 | 6/34 | 4/7 | 常用 | The effect of self-evolving on correlation and CoT-reward dynamics.（H6） |
| `Figure/Table X shows/presents/depicts ...` | 图表作主语〔指针·证据〕 | 5/19 | 4 | 6/18 | 8/27 | 常用 | Figure [REF] shows the ratio of exactly stopping at the last evidence turn ([REF]) and...（H5） |
| `(see more details / results in Appendix \ref{})` | 括号内指向附录〔M7.2 图表指针〕 | 5/19 | 4 | 0/0 | 3/3 | 常用 | ...sampled from AdvBench, combined with various jailbreak methods (see Appendix \ref{}), to evaluate malicious...（H4） |
| `More (implementation) details can be found in Appendix~\ref{}.` | 小节末指向附录〔B5 指向附录 / 后文〕 | 5/14 | 4 | 0/0 | 4/7 | 常用 | The prompts can be found in Appendix [REF].（H6） |
| `As shown in this figure / table, ...` | 回指刚提到的图表〔M7.2 图表指针〕。注：先 "We report/visualize ... in Figure X." 再 "As shown in this figure, ..."。 | 5/12 | 5 | 0/0 | 0/0 | 常用 | As shown in this table, X (w EG) reduces the inference time to 1/4 of...（H5） |
| `Table \ref{} presents / reports / shows / summarizes X` | 表格作主语〔M7.2 图表指针〕 | 4/16 | 3 | 4/7 | 7/26 | 常用 | Table \ref{} presents the results of X and the baselines across four benchmarks.（H4） |
| `The performance comparison on / across X.` | 表注开头〔M7.8 图注套话〕 | 4/12 | 3 | 0/0 | 4/7 | 常用 | The performance comparison across diverse long-context tasks.（H5） |
| `As illustrated in Figure / Table \ref{}, ...` | 句首引图（变体）〔M7.2 图表指针〕 | 4/8 | 3 | 2/3 | 3/5 | 常用 | ... as illustrated in Figure \ref{} and Appendix \ref{}.（H4） |
| `X in Equation [REF]` | 回指公式〔13 算法附录图指针〕。注：house写 Equation，不写 Eq. 或 Eqn.。 | 4/7 | 3 | 4/8 | 5/20 | 常用 | ... reward as introduced in Equation [REF].（H5） |
| `See Appendix \ref{} for details.` | 以 See 开头的指针句〔M7.2 图表指针〕 | 4/7 | 3 | 1/3 | 5/20 | 常用 | See Appendix \ref{} for details.（H4） |
| `We report the performance of X (and baselines) in Table \ref{}` | 报告主结果〔M7.2 图表指针〕 | 4/6 | 4 | 0/0 | 1/1 | 常用 | We report the performance and efficiency comparison of the 7B-sized model under this setting in...（H5） |
| `Here, X denotes / indicates Y (reading a figure or table)` | 图表内符号说明〔M7.2 图表指针〕 | 3/9 | 2 | 0/0 | 1/1 | 常用 | Here, the number -N indicates the difficulty level, with larger values corresponding to greater difficulty.（H5） |
| `We present X in Figure / Table \ref{}` | 呈现结果〔M7.2 图表指针〕 | 3/6 | 3 | 1/2 | 1/1 | 常用 | ...We present the model’s performance and efficiency across a range of context lengths in...（H5） |
| `We conduct N experiments with different random seeds and report the mean value with standard error` | 报告随机种子与误差〔M7.2 图表指针〕。注：H1、H5、H6；与本稿的 seed 设置直接相关。 | 3/5 | 2 | 1/1 | 2/4 | 常用 | ...experiments with different random seeds for each strategy and report the mean value with...（H1） |
| `The best-performing methods (per test) are bold, while the second-best methods are underlined.` | 表注：加粗/下划线说明〔M7.8 图注套话〕 | 3/5 | 2 | 0/0 | 2/2 | 常用 | The best-performing methods per test are bold, except for our ablation study of directly applying...（H4） |
| `More (similar) results are in Appendix \ref{}.` | 指向附录（短句）〔M7.2 图表指针〕。注：H3/H5/H4 的短句收尾。 | 3/5 | 3 | 0/0 | 0/0 | 常用 | More results are in Appendix \ref{}.（H5） |
| `The overall framework / process of X is illustrated in Figure [REF].` | 指向机制图〔13 算法附录图指针〕。注：H2、H5、H6。 | 3/3 | 3 | 2/2 | 0/0 | 常用 | ... [M] in X is illustrated in Figure [REF].（H5） |
| `The detailed / overall process of X is described / illustrated in Algorithm [REF].` | 指向算法框〔13 算法附录图指针〕。注：H4、H5、H6 各 1。 | 3/3 | 3 | 0/0 | 1/1 | 常用 | ... process of X is described in Algorithm [REF].（H5） |
| `As shown, ...` | 极简回指（上句已给图号）〔M7.2 图表指针〕。注：H5、H3 常用；前一句必须已点名图表。 | 2/6 | 2 | 0/0 | 0/0 | 常用 | As shown, X can identify the last evidence position and exit accordingly in most cases.（H5） |
| `We provide more (similar) results in Appendix / Figure \ref{}` | 补充结果〔M7.2 图表指针〕 | 2/5 | 2 | 0/0 | 0/0 | 常用 | We also provide similar results in Appendix \ref{}, confirming their limited utility preservation.（H4） |
| `We present a case study in Figure \ref{} / We further conduct a case study on X` | 引出案例〔M7.2 图表指针〕 | 2/3 | 2 | 2/2 | 0/0 | 常用 | Case Study (RQ3) We present a case study in Figure \ref{}.（H4） |
| `... are provided in Appendix \ref{}.` | 指向附录〔S11 附录指针〕。注：H4、H6。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 1/1 | 1/1 | 常用 | More details are provided in Appendix [REF].（H4） |
| `The improvement achieved by X is significant (p-value < 0.05).` | 表注：显著性〔M7.8 图注套话〕。注：只见 H1、H2；2026 年不再写。 | 2/8 | 1 | 0/0 | 2/3 | 偶用 | The improvement achieved by X is significant ( [M] -value [M] 0.05).（H2） |
| `as defined in Equation [REF]` | 回指定义〔13 算法附录图指针〕。注：H1、H4。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | ...that [M] (as defined in [REF]), the projection matrix is: [EQ] Projection to the...（H4） |
| `The results in Table \ref{} show / indicate that ...` | "The results in Table"作主语〔M7.2 图表指针〕。注：H1、H4 各见；2026 更常用 "As shown in Table"。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | ...The results in Table \ref{} show that CAST \cite{} leads to a drastic utility...（H4） |
| `Please refer to Appendix \ref{} for ...` | 指向附录〔S11 附录指针〕。注：H1、H6; 不用 kindly refer to。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 0/0 | 1/1 | 偶用 | Please refer to Appendix [REF] and [REF] for more experimental results over additional backbones, additional...（H1） |
| `As reported in Table X, ...` | 句首表格指针〔指针·证据〕。注：house的用例只在附录中。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | As reported in Table [REF], advanced language representations consistently yield higher performance than early BERT-style...（H2） |
| `where the bold lines report the rolling average and the shallow lines report the raw scores` | 图例说明（线型）〔M7.2 图表指针〕。注：只见 H6（训练曲线图）。 | 1/5 | 1 | 0/0 | 0/0 | 偶用 | ...\ref{}, where the bold lines are the rolling averaged performance with a window size...（H6） |
| `<Figure caption finding sentence>: e.g. "Decreasing X consistently reduces Y."` | 图注里写结论句〔M7.8 图注套话〕。注：H3 图注常带一句结论。 | 1/4 | 1 | 0/0 | 0/0 | 偶用 | Decreasing the steering strength [M] consistently reduces the reasoning token number and corresponding performance.（H3） |
| `X is in Appendix \ref{}.` | 简短指针〔S11 附录指针〕。注：H4。 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | The proof of [REF] is in Appendix [REF].（H4） |
| `We detail how to achieve X in the following two sections.` | 预告后续小节〔B5 指向附录 / 后文〕 | 1/1 | 1 | 0/0 | 2/2 | 偶用 | ...these learning objectives in the following two sections.（H4） |
| `Best viewed in color.` | 图注提示(禁用)〔S13 禁用或弃用(设置段常见)〕。注：house 0; 导师 4 篇。 | 0/0 | 0 | 3/12 | 1/1 | 禁用 | Best viewed in color.（refB1） |

## 章节开头与路线图

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `In this section, we ... (all verbs)` | 节开头元话语（总计）〔B1 节开头：In this section, we ...〕。注：house每个大节和 RQ 小节都用这一句起头；一节只用一次。 | 6/29 | 5 | 5/7 | 3/5 | 常用 | In this section, we briefly review the necessary background that forms the foundation of our...（H6） |
| `(RQ1) Performance and Efficiency Comparison  [subsection heading]` | 小节标题带 RQ 编号〔S1 小节开头与研究问题〕。注：house 2025-26 把 (RQ1) 放在标题前; H1/H4 放在标题后。 | 5/16 | 4 | 7/21 | 7/23 | 常用 | (RQ1) X A key factor that determines the success of our method...（H6） |
| `we detail / present / discuss how to V` | 说明本节讲怎么做〔14 方法小节路线〕。注：5 篇当前论文都用。 | 5/9 | 5 | 1/1 | 1/1 | 常用 | ... in Section [REF], we present how to preserve the utility of LLMs ...（H4） |
| `After that, (in Section~\ref{},) we present / detail / summarize ...` | 路线图中间步〔B3 路线图（first → After that → Then → Finally）〕。注：计数也含实验段中的 After that, we。 | 5/9 | 5 | 0/0 | 0/0 | 常用 | After that, we discuss how to incentivize reasoning with self-proposed and rubrics in Section [REF].（H6） |
| `We briefly V ...` | 简要介绍〔14 方法小节路线〕。注：house自有（导师 0）。 | 5/8 | 4 | 0/0 | 0/0 | 常用 | Preliminary We briefly review activation steering for inducing refusal for safety enhancement in this section.（H4） |
| `After that / Then / Finally, (in Section [REF],) we present / detail ...` | 路线后续步骤〔14 方法小节路线〕。注：H4：Finally, we integrate these components and present the overall framework ... | 4/9 | 4 | 1/1 | 0/0 | 常用 | After that, in Section [REF], we present how to preserve the utility of LLMs by...（H4） |
| `We first introduce / present / describe X in Section~\ref{}.` | 路线图第一步〔B3 路线图（first → After that → Then → Finally）〕 | 4/6 | 4 | 0/0 | 0/0 | 常用 | ...of this section, we first briefly introduce the key idea of X in Section [REF]...（H6） |
| `Together, these X make it plausible to ... / ...; together, they yield ...` | 段末合并两点〔B4 小节开头与小节间交接〕。注：计数含 1 次 H6 提示词附录。 | 4/5 | 4 | 2/2 | 0/0 | 常用 | Together, these capabilities make it plausible to self-propose supervision criteria (e.g. rubrics) for assessing CoT...（H6） |
| `In this work / paper, we (primarily) focus on the widely adopted X` | 限定本文范围（预备知识节）〔B1 节开头：In this section, we ...〕 | 4/4 | 4 | 3/4 | 0/0 | 常用 | In this work, we focus on incentivizing LLMs to reason with RL, where the policy...（H6） |
| `In this section, we present X / how to ...` | 方法节、应用节、附录节开头〔B1 节开头：In this section, we ...〕 | 4/4 | 4 | 1/1 | 0/0 | 常用 | In this section, we present X, a theoretically grounded and empirically effective activation steering method...（H4） |
| `Finally, (in Section~\ref{},) we integrate / present / briefly introduce ...` | 路线图末步〔B3 路线图（first → After that → Then → Finally）〕 | 4/4 | 4 | 0/0 | 0/0 | 常用 | Finally, we present how to optimize the policy [M] under the two roles based on...（H6） |
| `RQ1: Can X ...? / RQ2: How does X ...?` | RQ 列表（直接问句）〔B2 实验节开头与 RQ 列表〕 | 3/9 | 2 | 7/18 | 8/18 | 常用 | RQ1: Can self-proposed rubrics provide reliable rewarding signals for RL training?（H6） |
| `Then, (in Section~\ref{},) we detail / discuss ...` | 路线图中间步〔B3 路线图（first → After that → Then → Finally）〕 | 3/6 | 3 | 1/1 | 2/2 | 常用 | Then, we discuss the multiple roles under the same policy model in Section [REF].（H6） |
| `We briefly introduce X (in Section~\ref{} first), and more details can be found in Appendix~\ref{}.` | 简要介绍（实验设置、预备知识节开头）〔B2 实验节开头与 RQ 列表〕 | 3/4 | 3 | 0/0 | 0/0 | 常用 | We briefly introduce the experiment setup of our paper, and more details can be found...（H6） |
| `..., focusing on (the) following research questions, and more analysis (\eg ...) is in Appendix~\ref{}.` | 实验节开头引出 RQ（H4）〔B2 实验节开头与 RQ 列表〕 | 3/3 | 2 | 7/7 | 5/5 | 常用 | ...X, focusing on following research questions, and more analysis (e.g., varying model sizes, module study,...（H4） |
| `Building on Lemma~\ref{} / this view, we now present / investigate ...` | 基于前文结论推进〔B4 小节开头与小节间交接〕 | 3/3 | 2 | 0/0 | 3/4 | 常用 | Building on this view, we investigate whether the same policy can simultaneously serve as a...（H6） |
| `In this section, we explore the effectiveness of X / the hypothesis that ...` | 实验节开头〔B1 节开头：In this section, we ...〕 | 3/3 | 3 | 0/0 | 0/0 | 常用 | In this section, we explore the effectiveness of X by answering the following questions.（H5） |
| `In this section, we report X / We report X in this section.` | 附录小节开头〔B1 节开头：In this section, we ...〕 | 3/3 | 3 | 0/0 | 0/0 | 常用 | In this section, we report the prompts used as the reasoner [M] Rea [M] and...（H6） |
| `In this section, we study how X ... (\ie the role of ...).` | RQ 小节开头〔B1 节开头：In this section, we ...〕 | 3/3 | 3 | 0/0 | 0/0 | 常用 | In this section, we study how the rubrics self-evolving affects training within the X framework...（H6） |
| `RQ1: (Performance) Can X ...? RQ2: (Mechanism) How does X ...?` | RQ 列表（括号标签）〔B2 实验节开头与 RQ 列表〕。注：2026 两篇（H4、H5）的新写法：RQ 后加括号标签。 | 2/6 | 2 | 0/0 | 0/0 | 常用 | RQ1: (Performance and Efficiency) Can X achieve better performance compared to the vanilla MemAgent with...（H5） |
| `in the following (two sections / discussions)` | 下文〔14 方法小节路线〕。注：H2、H4。 | 2/3 | 2 | 0/0 | 4/4 | 常用 | ... these learning objectives in the following two sections.（H4） |
| `In this section, we discuss how / the connection between ...` | 小节开头：讨论〔B1 节开头：In this section, we ...〕 | 2/3 | 2 | 0/0 | 1/1 | 常用 | In this section, we discuss how to autonomously reward how LLMs think via self-proposed rubrics.（H6） |
| `We detail (how to) X in Section~\ref{}.` | 指向细节小节〔B3 路线图（first → After that → Then → Finally）〕 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...in Section [REF], we detail how to enhance the safety by learning to dynamically construct...（H4） |
| `In this section, we introduce X, a ... for ....` | 方法节开头：介绍方法〔B1 节开头：In this section, we ...〕 | 2/2 | 2 | 1/1 | 0/0 | 常用 | In this section, we introduce X (X) for...（H6） |
| `In this section, we take a closer look at X` | 小节开头：细看某部分〔B1 节开头：In this section, we ...〕 | 2/2 | 2 | 0/0 | 1/1 | 常用 | ...this section, we take a closer look at the different roles of the reasoner [M]...（H6） |
| `In this section, we briefly review the necessary background that forms the foundation of our work, including X in Section~\ref{} and Y in Section~\ref{}.` | 预备知识节开头〔B1 节开头：In this section, we ...〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...this section, we briefly review the necessary background that forms the foundation of our work,...（H6） |
| `In this section, we first introduce X in Section~\ref{}. / we first explore the following research questions.` | 节开头+路线图首步〔B1 节开头：In this section, we ...〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | In this section, we first introduce the reward design in Section [REF].（H5） |
| `We briefly review / introduce X in this section.` | 节开头（句末倒装）〔B1 节开头：In this section, we ...〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...for safety enhancement in this section.（H4） |
| `We further conduct extensive experiments to verify the effectiveness of X. First, ... (\cf Section~\ref{}). Second, ...` | 引言末段：证据路线图（代替贡献列表）〔B3 路线图（first → After that → Then → Finally）〕。注：代替贡献列表；house 6/6 终稿都没有贡献列表。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | We further conduct extensive experiments to verify the effectiveness of X.（H5） |
| `..., we now turn to / we now present ...` | 回扣后转入新内容〔B4 小节开头与小节间交接〕 | 2/3 | 1 | 7/7 | 1/1 | 偶用 | ...on Lemma [REF], we now present the computation process of [M].（H4） |
| `In this section, we aim to / focus on X` | 节开头（H1/H2 早期）〔B1 节开头：In this section, we ...〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/3 | 1 | 0/0 | 0/0 | 偶用 | In this section, we focus on this question: What new opportunities beyond good performance can...（H2） |
| `... by answering the following questions. / We aim to answer the following research questions:` | 实验节开头引出 RQ〔B2 实验节开头与 RQ 列表〕 | 2/2 | 1 | 7/7 | 6/6 | 偶用 | ...of X by answering the following questions.（H5） |
| `A key factor that determines the success of our method lies in whether ...` | RQ 小节开头：关键因素〔B4 小节开头与小节间交接〕 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | A key factor that determines the success of our method lies in whether the self-proposed...（H6） |
| `To investigate / address these questions, ... / To answer this, ...` | 由 RQ 转入做法〔B4 小节开头与小节间交接〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | To investigate these questions, we use linear mapping to project language representations of item titles...（H2） |
| `To address the risk of A and lack of B, we introduce ..., inspired by the success of ... \cite{}.` | 方法小节开头：回应上文问题〔B4 小节开头与小节间交接〕 | 1/2 | 1 | 1/1 | 0/0 | 偶用 | To address the risk of memory explosion and lack of an exit mechanism, we introduce...（H5） |
| `We investigate the underlying mechanism behind X, given the observation that ...` | 发现型：转入机制〔B4 小节开头与小节间交接〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...this research gap, we investigate the underlying mechanism of reasoning strength in LRMs by asking...（H3） |
| `In the remainder of this section, we first briefly introduce X in Section~\ref{} to facilitate a high-level understanding.` | 路线图起句（H6）〔B3 路线图（first → After that → Then → Finally）〕 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | In the remainder of this section, we first briefly introduce the key idea of X...（H6） |
| `... in Section~\ref{} to facilitate a high-level understanding` | 说明为何先讲〔B3 路线图（first → After that → Then → Finally）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...in Section [REF] to facilitate a high-level understanding.（H6） |
| `In this paper, we consider one common X setting: ...` | 限定任务设定〔B1 节开头：In this section, we ...〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this paper, we consider one common recommendation setting: collaborative filtering (CF) [CITE].（H2） |
| `In this section, we demonstrate how to ...` | 应用小节开头〔B1 节开头：In this section, we ...〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this section, we demonstrate how to detect potential overthink behavior before the generation of...（H3） |
| `In this section, we test the existence of X` | 发现型小节开头〔B1 节开头：In this section, we ...〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this section, we test the existence of pre-allocated direction vectors (i.e. pre-allocation vectors) for...（H3） |
| `Subsequently, in Section~\ref{}, we empirically assess ...` | 路线图中间步（H2）〔B3 路线图（first → After that → Then → Finally）〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Subsequently, in Section [REF], we empirically assess the existence and robustness of collaborative signals in...（H2） |
| `This finding of X arouses interest in the following questions.` | 发现型：以发现引出下一节 RQ〔B4 小节开头与小节间交接〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...encoded collaborative signals arouse interest in the following questions.（H2） |
| `To enable more principled and adaptive control, we ...` | 方法小节开头：目的〔B4 小节开头与小节间交接〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To enable more principled and adaptive control, we novelly introduce learnability into the activation steering...（H4） |
| `We organize this section as follows: We first ...` | 路线图起句（H3）〔B3 路线图（first → After that → Then → Finally）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We organize this section as follows: We first describe how to find the existence of...（H3） |
| `We then discuss X in Section~\ref{}.` | 路线图中间步〔B3 路线图（first → After that → Then → Finally）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We then discuss how to self-evolve such rubrics in Section [REF].（H6） |
| `In what follows, we ...` | 节内路线（refA）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 4/7 | 1/1 | 仅参考 |  |

## 摘要框架

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `Inspired by recent X methods, we propose Y (...), which enhances ... by ...` | 以灵感来源引出方法〔A3 摘要：动机与提出方法〕。注：计数含引言、方法节、相关工作中的所有 Inspired by the/these/this。 | 6/11 | 5 | 5/8 | 1/1 | 常用 | Inspired by recent self-evolving training methods, we propose X（H6） |
| `Specifically, it considers X as ... / Specifically, in X, the ... only ... when ...` | 展开机制（it 指代方法）〔A4 摘要：机制、训练与结果〕。注：结论中同式：Specifically, it preserves A by ..., and enhances B by ...（H4） | 3/4 | 2 | 5/6 | 2/3 | 常用 | Specifically, in X, the memory only updates when the update gate is open and the...（H5） |
| `We show that X provide reliable ... even without ..., enabling Y to outperform Z.` | 结果句（we show）〔A4 摘要：机制、训练与结果〕 | 3/4 | 3 | 0/0 | 0/0 | 常用 | We show that self-proposed and self-evolving rubrics provide reliable CoT supervision signals even without outcome...（H6） |
| `Recent work X has tried to tackle this by ... / Several efforts have tried to mitigate this by ...` | 点名前作的尝试〔A1 摘要：背景与重要性〕 | 3/3 | 3 | 0/0 | 0/0 | 常用 | Recent work MemAgent has tried to tackle this by processing context chunk-by-chunk in an RNN-like...（H5） |
| `We further reveal(ed) that ...` | 第三个发现〔A5 摘要：发现型骨架（H3 / H2）〕 | 2/3 | 2 | 0/0 | 0/0 | 常用 | We further reveal that this direction vector consistently yields positive reasoning length prediction, and it...（H3） |
| `X is essential for Y` | 说明某能力不可或缺〔A1 摘要：背景与重要性〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...especially jailbreak attacks, is essential for safe and reliable use.（H4） |
| `Empirical results show / suggest (the possible) ...` | 报告发现（H2）〔A5 摘要：发现型骨架（H3 / H2）〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | Empirical results show that such a simple model can outperform leading ID-based CF models on...（H2） |
| `Although prior efforts, such as A and B, have attempted to mitigate this ..., their lack of ... limits ...` | 说明已有修补为何不足〔A2 摘要：空白与缺陷〕。注：导师计数多为泛用的 prior efforts。 | 1/2 | 1 | 4/7 | 0/0 | 偶用 | Although prior efforts, such as vector calibration and conditional steering, have attempted to mitigate this...（H4） |
| `This outcome suggests the possible X between A and B` | 谨慎解读发现〔A5 摘要：发现型骨架（H3 / H2）〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | This outcome suggests the possible homomorphism between the advanced language representation space and an effective...（H2） |
| `However, indiscriminately applying X fundamentally suffers from the trade-off between A and B, since ...` | 指出根本性权衡〔A2 摘要：空白与缺陷〕 | 1/1 | 1 | 0/0 | 2/2 | 偶用 | ...activation steering fundamentally suffers from the trade-off between safety and utility, since the same steering...（H4） |
| `Despite X playing crucial roles in Y, directly Z is difficult: A, and B.` | 让步句：作用关键但难做〔A1 摘要：背景与重要性〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Despite chain-of-thought (CoT) playing crucial roles in LLM reasoning, directly rewarding it is difficult: training...（H6） |
| `Moreover, when used as X, these Y further improve ...` | 附加发现〔A4 摘要：机制、训练与结果〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Moreover, when used as in-prompt hints, these self-proposed rubrics further improve inference-time performance.（H6） |
| `Our findings highlight the connection between A and B` | 贡献收尾（H2）〔A5 摘要：发现型骨架（H3 / H2）〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Our findings highlight the connection between language modeling and behavior modeling, which can inspire both...（H2） |
| `Specifically, we show that ..., indicating that ...` | 展开头条发现〔A5 摘要：发现型骨架（H3 / H2）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Specifically, we show that the number of reasoning tokens is predictable solely based on the...（H3） |
| `We find evidence that X ...` | 给出头条发现〔A5 摘要：发现型骨架（H3 / H2）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We find evidence that LRMs pre-plan the reasoning strengths in their activations even before generation,...（H3） |
| `We then uncover that ...` | 第二个发现〔A5 摘要：发现型骨架（H3 / H2）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We then uncover that LRMs encode this reasoning strength through a pre-allocated directional vector embedded...（H3） |

## M10 结论、局限与展望

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `This research is supported by X` | 致谢开句〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 5/5 | 4 | 0/0 | 7/7 | 常用 | This research is supported by National Natural Science Foundation of China (XXXXXXXX) and the National...（H5） |
| `It consistently outperforms X across A and B, verifying ...` | 结论证据句（H6）〔C3 结论：证据与定位〕。注：计数含实验段。 | 4/5 | 3 | 3/3 | 3/3 | 常用 | It consistently outperforms outcome-only RLVR across datasets and LLMs, verifying such rubrics provide effective CoT...（H6） |
| `Any opinions, findings and conclusions or recommendations expressed in this material are those of the author(s) ...` | 致谢免责句（资助方模板）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 3/3 | 3 | 0/0 | 2/2 | 常用 | Any opinions, findings and conclusions or recommendations expressed in this material are those of the...（H5） |
| `However, we acknowledge potential risks. / However, our findings may also pose potential risks.` | 转入风险〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕。注：计数含 H6 NeurIPS checklist 1 次，定级时已扣除（扣除后 2 篇） | 3/3 | 3 | 0/0 | 0/0 | 常用 | However, we acknowledge potential risks.（H4） |
| `All experiments rely solely on publicly available models and benchmark datasets; no private or human-subject data are involved.` | 数据伦理声明〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕。注：计数含 H6 NeurIPS checklist 3 次，定级时已扣除（扣除后 1 篇）层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/5 | 2 | 0/0 | 0/0 | 常用 | ...rely solely on publicly available models and benchmark datasets; no private or human-subject data are...（H4） |
| `our experiments focus primarily on the X model series` | 实验范围受限〔D 局限（Limitations 节）〕 | 2/3 | 2 | 1/1 | 1/1 | 常用 | ...addition, our experiments focus primarily on the Qwen model series [CITE], and it remains unexplored...（H3） |
| `X remains unknown (on / for ...)` | 范围外效果未知〔D 局限（Limitations 节）〕 | 2/3 | 2 | 0/0 | 1/3 | 常用 | ...on non-verifiable domains unknown.（H6） |
| `we only use X and do not explore whether ...` | 方法选择受限〔D 局限（Limitations 节）〕 | 2/3 | 2 | 0/0 | 1/1 | 常用 | First, we only use a linear probe and do not explore whether more complex architectures,...（H3） |
| `In principle, X could be abused / malicious methods could exploit X to ...` | 风险举例〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...principle, steering techniques could be abused, for example, during the training of linear regression as...（H4） |
| `our method is (still quite) limited to the X domain, (leaving / with) ...` | 适用范围受限〔D 局限（Limitations 节）〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...is still quite limited to the RLVR domain, leaving the effectiveness of rewarding with self-proposed...（H6） |
| `the introduction of X increases the ... burden and thereby requires more training time / reduce training stability` | 额外代价〔D 局限（Limitations 节）〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...the rubricator role increases the rollout burden and thereby requires more training time.（H6） |
| `There are still several limitations in this paper.` | 局限节开句（H6、H4）〔D 局限（Limitations 节）〕。注：局限节 2-3 句，不写补救，不许诺。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | There are still several limitations in this paper.（H6） |
| `This work / paper aims to enhance / investigate X` | 伦理或影响声明开句〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | This work aims to enhance the safety of large language models (LLMs) by inducing refusals...（H4） |
| `This work mainly brings significant positive societal effects, for instance, ...` | 正面影响（H5）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕。注：计数含 H6 NeurIPS checklist 1 次，定级时已扣除（扣除后 1 篇）层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | ...mainly brings significant positive societal effects, for instance, helping humans read and process long contexts...（H5） |
| `To address these issues, we proposed X, which equips ... with ...` | 结论提出句（过去时，H5）〔C2 结论：本文做了什么〕 | 2/2 | 1 | 2/2 | 6/6 | 偶用 | ...address these issues, we proposed X, which equips the recurrent loop with two text-controlled gates:...（H5） |
| `Current X methods are limited by their heuristic design, raising concerns about their robustness and effectiveness.` | 重述现有方法不足〔C1 结论：重述问题〕 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | ...activation steering methods are limited by their heuristic design, raising concerns about their robustness and...（H4） |
| `We did not rely on LLMs for research ideation ...; LLMs were used in limited ways: ...` | LLM 使用声明〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/3 | 1 | 0/0 | 0/0 | 偶用 | ...of LLMs We did not rely on LLMs for research ideation, experiment design, or data...（H4） |
| `Potential benefits. / Potential risks.` | 影响声明分项小标题（H6）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕。注：计数含 H6 NeurIPS checklist 1 次，定级时已扣除（扣除后 1 篇） | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Potential benefits.（H6） |
| `X lacks theoretical guarantee / a personalized design` | 缺少某性质（H2）〔D 局限（Limitations 节）〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | First, this research lacks theoretical guarantee, and we do not contribute to designing any new...（H2） |
| `We have made significant efforts to ensure the reproducibility of our results.` | 可复现声明开句〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 1/1 | 1/1 | 偶用 | ...significant efforts to ensure the reproducibility of our results.（H4） |
| `We found the existence of X` | 发现型结论证据句（H3）〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | We found the existence of pre-allocated direction vectors in LRMs, whose magnitude causally affects the...（H3） |
| `..., and more efforts are required to make X applicable in real-world practice` | 说明尚需努力（H3）〔E 未来工作〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...application directions, and more efforts are required to make such potential applicable in real-world practice.（H3） |
| `Appendix~\ref{} further provides more implementation details, hardware specifications, hyperparameter choices ...` | 可复现声明：附录清单〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Appendix [REF] further provides more implementation details, hardware specifications, hyperparameter choices, data selection and prompt...（H4） |
| `Despite showing the effectiveness of X, there are still several limitations in this paper.` | 局限节开句（H4，让步）〔D 局限（Limitations 节）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Despite showing the effectiveness of X, there are still several limitations in this paper.（H4） |
| `Directly X is hard, as ... are costly to train and vulnerable to ...` | 重述难点（H6）〔C1 结论：重述问题〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Directly rewarding CoTs is hard, as reward models are costly to train and vulnerable to...（H6） |
| `Experiments across diverse X tasks showed X outperforms Y while achieving up to ...` | 结论证据句（H5）〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Experiments across diverse long-context reasoning tasks showed X outperforms vanilla MemAgent while achieving up to...（H5） |
| `Extensive experiments across various models demonstrated the effectiveness of X, highlighting it as an efficient solution for ....` | 结论证据句（H4）〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...effectiveness of X, highlighting it as an efficient solution for safety enhancement at inference time...（H4） |
| `In this paper, we explored the relationship between A and B` | 发现型结论开句（H2）〔C2 结论：本文做了什么〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | In this paper, we explored the relationship between language space and behavior spaces for recommendation,...（H2） |
| `it remains unexplored whether these findings hold for ...` | 范围外是否成立未知〔D 局限（Limitations 节）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...[CITE], and it remains unexplored whether these findings hold for reasoning models based on other...（H3） |
| `leaving the effectiveness of X on Y unknown` | 范围外效果未知（分词尾）〔D 局限（Limitations 节）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...the RLVR domain, leaving the effectiveness of rewarding with self-proposed rubrics on non-verifiable domains unknown.（H6） |
| `Our code is available at \url{...}.` | 代码链接（单数 code）〔A4 摘要：机制、训练与结果〕。注：H1/H2/H4 写 Codes/codes；投稿版注释掉，终稿启用。切片曾标为禁用，但不在禁用清单上，层级按计数重定；不要写 Codes，写 Our code is available at。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Our code is available at.（H3） |
| `Our paper studied X from the Y perspective, which can help the community better understand Z.` | 发现型结论收尾〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...perspective, which can help the community better understand LRMs.（H3） |
| `Our study contributes to a deeper understanding of X` | 贡献定位（影响声明）〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Our study contributes to a deeper understanding of LRMs within the LLM research community.（H3） |
| `Outcome-centric X improves A but neglects B, which may lead to ...` | 重述问题（H6）〔C1 结论：重述问题〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...improves answer accuracy but neglects direct CoT supervision, which may lead to suboptimal reasoning.（H6） |
| `Possible future work will involve exploring ...` | 一句话未来工作（H2）〔E 未来工作〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Possible future work will involve exploring the space relationship from both theoretical and multimodal perspectives.（H2） |
| `Several limitations remain in this paper.` | 局限节开句（H5）〔D 局限（Limitations 节）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Several limitations remain in this paper.（H5） |
| `The negative impacts are largely trivial in comparison, and one potential issue is that ...` | 风险较小（H5）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The negative impacts are largely trivial in comparison, and one potential issue is that LLM...（H5） |
| `This paper presents a study aimed at advancing X via Y` | 影响声明开句（ICML）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...Statement This paper presents a study aimed at advancing long-context reasoning via a memory mechanism,...（H5） |
| `This work has several limitations.` | 局限节开句（H3）〔D 局限（Limitations 节）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | This work has several limitations.（H3） |
| `To this end, in this work, we presented a ... method called X for both A and B.` | 结论提出句（过去时，H4）〔C2 结论：本文做了什么〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To this end, in this work, we presented a theoretically grounded and empirically effective activation...（H4） |
| `Together, these materials allow independent researchers to reproduce and verify all our results.` | 可复现声明收尾〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Together, these materials allow independent researchers to reproduce and verify all our results.（H4） |
| `Using linear probing, we showed that ..., suggesting ...` | 发现型结论证据句（H3）〔C3 结论：证据与定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Using linear probing, we showed that the reasoning token number can be predicted merely using...（H3） |
| `WARNING: This paper may contain offensive and harmful contents.` | 内容警告（安全论文）〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | WARNING: This paper may contain offensive and harmful contents.（H4） |
| `We encourage continued collaboration on ... and stress the need for ...` | 伦理声明收尾〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We encourage continued collaboration on emerging threats and stress the need for transparent, ethical AI...（H4） |
| `We propose X to reward ... via ... without ...` | 结论提出句（现在时，H6）〔C2 结论：本文做了什么〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We propose X to reward CoTs via self-built, self-evolving rubrics without human annotation.（H6） |
| `We will further explore its generalization to X.` | 一句话未来工作（H6）〔E 未来工作〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | We will further explore its generalization to non-verifiable domains.（H6） |
| `While recent X work Y provided a ... paradigm for ..., it suffers from A due to B and C from D.` | 让步句重述前作问题（H5）〔C1 结论：重述问题〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | While recent recurrent memory work MemAgent [CITE] provided a chunk-by-chunk paradigm for addressing long-context reasoning,...（H5） |
| `While X strengthens defense against ..., no method offers absolute security.` | 限定安全性结论〔F 影响 / 伦理 / 可复现 / LLM 使用 / 致谢〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...no method offers absolute security.（H4） |
| `X has emerged as an effective method in ..., but struggles between A and B.` | 开句：范式有效但有难题（H4）〔C1 结论：重述问题〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...behaviors of LLMs, but struggles between safety enhancement and utility preservation.（H4） |
| `Despite the empirical success(es), the ... understanding of X remains limited.` | 开句：成功但理解有限（H1）〔C1 结论：重述问题〕 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | Despite the empirical successes, the profound understanding of contrastive loss in collaborative filtering (CF) remains...（H1） |
| `provides a valuable baseline for future X studies and is an important stepping stone toward ...` | 定位（H1，早期）〔C3 结论：证据与定位〕 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | ...InfoNCE, provides a valuable baseline for future contrastive learning-based CF studies and is an important...（H1） |
| `In this work / paper, we proposed X, ...` | 结论提出句（refB）〔H 导师常用而house不用（对照）〕 | 1/1 | 0 | 0/0 | 5/5 | 已弃用 | In this paper, we proposed two specific mapping methods: embedding-based (i.e. X-embed) mapping and multilayer...（H1） |
| `In this work, we devised a principled X ...` | 结论提出句（H1，导师式）〔C2 结论：本文做了什么〕 | 1/1 | 0 | 0/0 | 1/1 | 已弃用 | In this work, we devised a principled X loss specially tailored for CF methods, utilizing...（H1） |
| `..., which aligns with the limitation of X.` | 实验结果呼应局限（H1）〔D 局限（Limitations 节）〕 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | ...specific datasets, which aligns with the limitation of X.（H1） |
| `Future work includes / A worthwhile direction for future work is / An interesting avenue for future work` | 未来工作（refB 常用）〔E 未来工作〕 | 0/0 | 0 | 2/2 | 4/4 | 仅参考 |  |
| `In future work, we will / In future, we will ...` | 未来工作（导师常用）〔E 未来工作〕 | 0/0 | 0 | 5/5 | 0/0 | 仅参考 |  |
| `This work represents an initial attempt to ... and opens up new research possibilities.` | 结论定位（refA）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 3/4 | 0/0 | 仅参考 |  |
| `The limitations of X ... will be addressed in future work / will be covered in subsequent works` | 把局限推给未来（refB）〔E 未来工作〕 | 0/0 | 0 | 0/0 | 2/3 | 仅参考 |  |

## 相关工作

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `... research line (any)` | 研究线（总计）〔G2 相关工作：分类与研究线〕 | 5/13 | 4 | 2/4 | 6/11 | 常用 | Another research line aims to improve the safety at the activation level, using techniques such...（H4） |
| `X \cite{} have recently emerged as a new paradigm of Y / a promising way to ...` | 段首：新范式出现〔G1 相关工作：段首（领域地位）〕 | 3/4 | 3 | 0/0 | 0/0 | 常用 | ...with rubrics has emerged as a promising way to provide fine-grained and interpretable reward signals...（H6） |
| `Traditional methods for X can be broadly categorized into A \cite{} and B \cite{} techniques.` | 分类句〔G2 相关工作：分类与研究线〕 | 3/3 | 2 | 4/5 | 5/8 | 常用 | ...for long-context reasoning can be broadly categorized into architectural modifications [CITE] and context extension [CITE]...（H5） |
| `However, despite its effectiveness in X / despite showing capabilities in X, ... still ...` | However+despite 双重让步〔G4 相关工作：共同局限〕 | 3/3 | 3 | 0/0 | 1/1 | 常用 | However, despite its effectiveness in incentivizing reasoning, most RLVR training remains outcome-centric and provides no...（H6） |
| `One line of research focuses on .... Another research line aims to ...` | 研究线对举〔G2 相关工作：分类与研究线〕 | 2/4 | 2 | 3/3 | 1/2 | 常用 | One line of research focuses on post-training methods such as supervised fine-tuning (SFT) [CITE], reinforcement...（H4） |
| `Current methods tend to adopt two main paradigms: A \cite{} and B \cite{}.` | 现有方法二分〔G2 相关工作：分类与研究线〕 | 2/3 | 2 | 1/1 | 0/0 | 常用 | Current methods tend to adopt two main paradigms: vector calibration [CITE] and conditional steering [CITE].（H4） |
| `Recent efforts have tried to / demonstrated that ...` | 引述尝试〔G3 相关工作：代表方法与进展〕 | 2/2 | 2 | 1/1 | 0/0 | 常用 | Recent efforts have demonstrated that LLMs can learn multiple objectives [CITE] by rewarding different learning...（H5） |
| `These methods typically involve / focus on ...` | 概括一类方法〔G3 相关工作：代表方法与进展〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | These methods typically involve refusal training, encouraging the model to reject malicious prompts.（H4） |
| `X is one research line among the Y \cite{}.` | 段首：定位子领域〔G1 相关工作：段首（领域地位）〕 | 2/2 | 2 | 0/0 | 0/0 | 常用 | Activation steering is one research line among the representation learning [CITE].（H4） |
| `The first research direction / The second research line / The third research line ...` | 按序号展开研究线〔G2 相关工作：分类与研究线〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/5 | 1 | 0/0 | 1/1 | 偶用 | The first research direction, LM-enhanced recommendation, focuses on empowering traditional recommenders with the semantic representations...（H2） |
| `Despite these findings, X remains largely unexplored, and it is still unclear whether ...` | 承接发现后指出空白〔G4 相关工作：共同局限〕 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | Despite these findings, the underlying mechanisms behind such planning capabilities remain largely unexplored, and it...（H3） |
| `However, although effective, these methods are still limited, due to ...` | 让步+局限〔G4 相关工作：共同局限〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 0/0 | 0/0 | 偶用 | However, although effective, these methods are still limited, due to the ID-based paradigm.（H2） |
| `However, existing work largely applies X to ..., leaving Y underexplored.` | 空白（分词尾）〔G4 相关工作：共同局限〕 | 1/2 | 1 | 1/1 | 0/0 | 偶用 | ...score response quality, leaving their effectiveness for CoT supervision underexplored.（H6） |
| `Existing works show that ..., which is commonly referred to as X \cite{}.` | 引述现象并给出通名〔G3 相关工作：代表方法与进展〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Existing works show that LLM performance often degrades when relevant evidence is dispersed across long...（H5） |
| `For architectures, .... For context extension methods, they typically focus on ....` | 逐类展开（H5）〔G2 相关工作：分类与研究线〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | For architectures, sparse attention mechanisms restrict attention patterns using predefined structures such as sliding windows...（H5） |
| `Generally, despite showing potential, the heuristic design of current methods limits ..., urging more principled ...` | 段末总评+呼吁〔G4 相关工作：共同局限〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | Generally, despite showing potential, the heuristic design of current methods limits their robustness and effectiveness...（H4） |
| `These approaches are motivated by / This research line is inspired by recent advances in X that ...` | 交代一类方法的动机〔G3 相关工作：代表方法与进展〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | These approaches are motivated by recent advances in mechanism explainability that aligned LLMs actually are...（H4） |
| `X remains challenging for LLMs \cite{}, while this capability is important for ...` | 段首：困难+重要〔G1 相关工作：段首（领域地位）〕 | 1/2 | 1 | 0/0 | 0/0 | 偶用 | ...over long contexts remains challenging for large language models (LLMs) [CITE], while this capability is...（H5） |
| `Within this X research line, Y has emerged as one promising approach recently.` | 聚焦子线〔G2 相关工作：分类与研究线〕 | 1/1 | 1 | 0/0 | 2/3 | 偶用 | Within this activation-level research line, activation steering [CITE] for refusal has emerged as one promising...（H4） |
| `It works by injecting X that ...` | 一句话说明原理〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | It works by injecting a directional vector that encodes the semantics of refusal behaviors, steering...（H4） |
| `They either aim to ..., or enable ...` | 概括两类做法〔G2 相关工作：分类与研究线〕 | 1/1 | 1 | 1/1 | 0/0 | 偶用 | They either aim to calibrate the refusal direction vector for better targeting malicious prompts, or...（H4） |
| `Various approaches have been proposed to improve X against Y.` | 引出多种方法〔G2 相关工作：分类与研究线〕 | 1/1 | 1 | 0/0 | 1/1 | 偶用 | Various approaches have been proposed to improve the safety of LLMs against jailbreak attacks.（H4） |
| `A critical step toward X lies in the ability to ...` | 段首：长远意义〔G1 相关工作：段首（领域地位）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | A critical step toward general artificial intelligence lies in the ability to self-evolve with little...（H6） |
| `A milestone paradigm is X \cite{}, where ...` | 点出里程碑范式〔G1 相关工作：段首（领域地位）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | A milestone paradigm is reinforcement learning with verifiable rewards (RLVR) [CITE], where LLMs are trained...（H6） |
| `Early attempts focus on ...` | 早期工作〔G3 相关工作：代表方法与进展〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Early attempts focus on adopting in-context learning (ICL) [CITE] and prompting pre-trained LMs [CITE].（H2） |
| `However, how to X remains one crucial issue.` | 段末抛出核心难题（H4）〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...with activation steering remains one crucial issue.（H4） |
| `Inspired by these observations, this work presents the first investigation into ...` | 段末：由观察引出本文〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Inspired by these observations, this work presents the first investigation into the reasoning planning capabilities...（H3） |
| `It has been evidenced that A \cite{}, B \cite{}, and C \cite{} have been encoded in ...` | 列举证据〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | It has been evidenced that response style [CITE], refusal behaviors [CITE], and memory extraction capabilities...（H3） |
| `More recent studies further incorporate ...` | 更新进展〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | More recent studies further incorporate explicit reasoning processes during post-training to mitigate the issue of...（H4） |
| `Recent work has made progress via A and B, where ...` | 引述进展（H6）〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Recent work has made progress via self-training and self-play, where a single policy model [M]...（H6） |
| `Several recent works have also tried to ... but still lack principled guidance \cite{}.` | 近期尝试仍有缺陷〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...into activation steering but still lack principled guidance [CITE].（H4） |
| `Such X mechanisms have also been utilized for Y recently \cite{}.` | 技术迁移到本问题〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Such memory mechanisms have also been utilized for long-context reasoning recently [CITE].（H5） |
| `The core issue of X lies in how to ...` | 点明核心难题〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The core issue of adopting activation steering for safety enhancement lies in how to maintain...（H4） |
| `The X issue is a critical research area in Y \cite{}, primarily focusing on ...` | 段首：研究领域重要性〔G1 相关工作：段首（领域地位）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | The safety issue is a critical research area in large language models (LLMs) [CITE], primarily...（H4） |
| `Therefore, such challenges hinder X in practice.` | 局限的后果（H6）〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | Therefore, such challenges hinder the combination of RLVR training with CoT supervision criteria for rewarding...（H6） |
| `Therefore, these advances in X suggest the possibilities of Y, while this direction remains largely under-explored.` | 由进展推出可能性+空白〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...such self-evolving methods suggest the possibilities of self-proposing criteria for CoT supervision, while this direction...（H6） |
| `These advances inspire us to design X, enabling it to ...` | 段末：由进展引出本文设计〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | These advances inspire us to design role-specific rewards for a single policy model, enabling it...（H5） |
| `These phenomena suggest that X may possess ...` | 由现象推出假设〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | These phenomena suggest that LRMs may possess some underlying mechanisms to plan and control the...（H3） |
| `To address the limitations that ..., recent works have begun to explore ...` | 以局限引出新方向〔G3 相关工作：代表方法与进展〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...size, recent works have begun to explore augmenting the LLM with memory mechanisms [CITE].（H5） |
| `To address these gaps, we introduce a framework that ..., integrating A with B.` | 末段定位句（H6）〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | To address these gaps, we introduce a framework that self-proposes rubrics as CoT supervision criteria...（H6） |
| `While effective, such methods face risks of A and B.` | 一类方法的共同局限（H5）〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | While effective, such methods face risks of memory explosion and a lack of exiting mechanism.（H5） |
| `While showing effectiveness in X, such traditional methods still suffer from ...` | 让步+局限〔G4 相关工作：共同局限〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | While showing effectiveness in addressing long-context reasoning, such traditional methods still suffer from performance degradation...（H5） |
| `X has become an essential technique for Y \cite{}.` | 段首：技术地位〔G1 相关工作：段首（领域地位）〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...RL Reinforcement learning has become an essential technique for incentivizing chain-of-thought (CoT) reasoning in LLMs...（H6） |
| `X \cite{} takes a different approach and ... / takes it a step further` | 逐个方法对比（H1）〔G3 相关工作：代表方法与进展〕 | 1/2 | 0 | 0/0 | 0/0 | 已弃用 | NCL [CITE] takes a different approach and incorporates potential neighbors from both the graph structure...（H1） |
| `Our work is related to the literature on A and B.` | 相关工作总起（H1）〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Our work is related to the literature on contrastive learning-based collaborative filtering (CL-based CF) methods,...（H1） |
| `Several studies like A \cite{} and B \cite{} elaborate on ...` | 列举代表（H1）〔G3 相关工作：代表方法与进展〕 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | Several studies like SGL [CITE] and DCL [CITE] elaborate on data-heuristic augmentation operators such as...（H1） |
| `We remind important related works to understand how X stands ...` | 相关工作元话语开句（H1，已过时）〔G5 相关工作：与本文衔接和定位〕 | 1/1 | 0 | 0/0 | 0/0 | 已弃用 | We remind important related works to understand how our X stands and its role in...（H1） |
| `Distinct from X, we ...` | 与前作区分（导师式）〔H 导师常用而house不用（对照）〕 | 0/0 | 0 | 6/12 | 5/6 | 仅参考 |  |
| `Our work differs from them in that ...` | 与前作区分（导师式）〔H 导师常用而house不用（对照）〕。注：house没有这个句式，相关工作末段用 To address these gaps, we ... 或 In this work, we take an important step further, by ... | 0/0 | 0 | 3/4 | 4/4 | 仅参考 |  |
| `In this section, we review two research lines of related works: A and B. / This work is relevant to two research lines: ...` | 相关工作元话语开句（导师式）〔H 导师常用而house不用（对照）〕。注：house预备知识节用 we briefly review。 | 0/0 | 0 | 2/2 | 1/1 | 仅参考 |  |

## 连接词（按功能）

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `..., while Y ...` | 句中对照：X 如何，而 Y 如何〔连接·转折〕。注：house的对照口头禅（H5 最多） | 6/53 | 5 | 8/60 | 8/74 | 常用 | ...practical implementation challenges [CITE], while the lack of such supervision may induce sub-optimal reasoning...（H6） |
| `Specifically, ...` | 句首细化：展开上一句〔连接·细化〕。注：三方共用；段内第 2 句的标准开头。 | 6/44 | 5 | 7/29 | 8/65 | 常用 | Specifically, we reward a CoT based on how well it satisfies the valid rubrics proposed...（H6） |
| `such as X and Y` | 列举例子〔连接·举例〕 | 6/43 | 5 | 8/58 | 8/38 | 常用 | ...self-generate training data, such as verifiable question-answer pairs for RL or high-quality CoT trajectories...（H6） |
| `However, ...` | 句首转折，引出缺陷或反例〔连接·转折〕。注：三方共用的主转折词；house放句首，不放句末。 | 6/41 | 5 | 8/61 | 8/63 | 常用 | However, how to adaptively propose and evolve such criteria in the self-evolving training paradigm remains...（H6） |
| `Here, ... / Here we ...` | 句首：就地说明符号或设置〔连接·细化〕。注：公式后常用 Here [M] denotes ... | 6/35 | 5 | 8/37 | 7/26 | 常用 | Here [M] is set as 0.2 by default in our paper.（H6） |
| `Moreover, ...` | 句首递进：再加一点〔连接·递进〕。注：三方共用。 | 6/33 | 5 | 8/56 | 8/28 | 常用 | Moreover, when used as in-prompt hints, these self-proposed rubrics further improve inference-time performance.（H6） |
| `including X, Y and Z` | 列举成员〔连接·举例〕 | 6/29 | 5 | 6/12 | 6/20 | 常用 | For math reasoning, we evaluate math-reasoning benchmarks including AIME24 [CITE], AIME25 [CITE], and AMC23 [CITE].（H6） |
| `Therefore, ...` | 句首推论〔连接·因果〕。注：house自有（导师用 Hence）；附录里另有模型输出里的 Therefore，不计。 | 6/26 | 5 | 3/6 | 4/8 | 常用 | Therefore, we further propose to self-evolve the rubric generation capability, so as to progressively produce...（H6） |
| `We first ... / we then ...` | 路线图：先……再……〔连接·顺序〕 | 6/21 | 5 | 8/45 | 7/27 | 常用 | We then discuss how to self-evolve such rubrics in Section [REF].（H6） |
| `We further + V (conduct / reveal / propose) ...` | 递进：我们进一步做某事〔连接·递进〕。注：We further conduct extensive experiments to verify ... | 6/21 | 5 | 3/12 | 4/11 | 常用 | We further conduct comprehensive experiments to examine the effectiveness of the proposed X.（H6） |
| `To evaluate/study/examine/verify/test ..., we ...` | 目的不定式开段：研究动作〔连接·目的〕。注：实验小节段首签名。 | 6/19 | 5 | 7/17 | 7/19 | 常用 | To study this, we conduct an ablation study on the 8B-sized model by abandoning the...（H6） |
| `Since ..., ...` | 句首原因从句〔连接·因果〕 | 6/16 | 5 | 1/1 | 1/3 | 常用 | Since all the generation outputs [M] in the group [M] share the same outcome reward,...（H5） |
| `To ensure/achieve/endow/mitigate ..., we ...` | 目的不定式开段：方法动作〔连接·目的〕。注：H4: To ensure the benign prompts remain unaffected ..., we aim to ... | 6/15 | 5 | 5/6 | 8/13 | 常用 | To achieve this goal, we introduce two key roles in the multi-role RL framework for...（H6） |
| `First, ... / Firstly, ...` | 列举第一点〔连接·顺序〕 | 6/13 | 5 | 3/5 | 6/8 | 常用 | First, we conduct a primary study showcasing that self-proposed rubrics can be reliable and learnable...（H6） |
| `..., inspired by the success/effectiveness of ...` | 句中：受……启发〔连接·依据〕。注：H5: inspired by the effectiveness of ... in ... | 6/12 | 5 | 6/10 | 2/2 | 常用 | Inspired by recent self-evolving training methods, we propose X（H6） |
| `..., leading to ...` | 句中后果〔连接·因果〕 | 6/11 | 5 | 2/4 | 6/9 | 常用 | ...loop lacks an exit mechanism, leading to unnecessary computation after even sufficient evidence is collected...（H5） |
| `..., but ...` | 句中转折〔连接·转折〕 | 5/37 | 4 | 8/26 | 5/15 | 常用 | ...accuracy on evidence-present chunks, but also increases the risk of unnecessary updating on evidence-free...（H5） |
| `If X ..., (then) Y ...` | 句首条件从句〔连接·条件〕 | 5/31 | 4 | 2/2 | 4/5 | 常用 | If so, this could enable a new paradigm of self-improving reasoning, shifting RL from optimizing...（H6） |
| `due to ...` | 原因介词〔连接·因果〕 | 5/20 | 4 | 8/18 | 8/21 | 常用 | ...rarely rewarded directly due to practical implementation challenges [CITE], while the lack of such...（H6） |
| `Additionally, ...` | 句首递进：补充发现或设置〔连接·递进〕。注：house自有（导师几乎不用）；H5、H6 用它代替 Moreover。 | 5/19 | 4 | 0/0 | 4/8 | 常用 | Additionally, introducing self-evolving training for the rubricator further improves the reasoning performance (cf. Section [REF]).（H6） |
| `..., rather than ...` | 对举：是 A 而不是 B〔连接·转折〕。注：house对举手法之一（prose.md T12） | 5/17 | 4 | 7/16 | 8/20 | 常用 | ...causally controls the planning of the reasoning token number, rather than the answer token number...（H3） |
| `We also + V (report / compare / consider) ...` | 递进：我们还做了某事〔连接·递进〕 | 5/17 | 4 | 6/14 | 7/9 | 常用 | We also additionally calculate the ratio of early stopping, exact stopping, and late stopping in...（H5） |
| `While X ..., Y ...` | 句首让步/对照从句〔连接·转折〕。注：house常用 While important, ... / While X enables ..., we argue ... | 5/14 | 4 | 6/14 | 4/6 | 常用 | While the ablation baseline’s rubrics can be gradually satisfied by [M] Rea [M] as [M]...（H6） |
| `Then(,) ...` | 路线图：然后〔连接·顺序〕 | 5/14 | 4 | 3/5 | 6/11 | 常用 | Then, we discuss the multiple roles under the same policy model in Section [REF].（H6） |
| `After that, ...` | 路线图：之后〔连接·顺序〕。注：house签名（H5 最多）；导师少用。 | 5/14 | 5 | 0/0 | 0/0 | 常用 | After that, we discuss how to incentivize reasoning with self-proposed and rubrics in Section [REF].（H6） |
| `we aim to ...` | 说明目标〔连接·目的〕 | 5/11 | 4 | 7/13 | 7/10 | 常用 | To achieve this, we aim to steer activations of malicious prompts toward refusal.（H4） |
| `On the other hand, ...` | 对举第二面（可单用）〔连接·对举〕 | 5/11 | 4 | 1/1 | 2/3 | 常用 | On the other hand, [M] itself can self-assign reward signals by acting as a judge...（H6） |
| `Given (that) ..., ...` | 句首前提〔连接·因果〕 | 5/7 | 4 | 3/4 | 3/3 | 常用 | Given the question [M] , the rollout sampling process of the reasoner can be formulated...（H6） |
| `To this end, (we propose X) ...` | 句首：为此〔连接·目的〕。注：house用 To this end；refB 用 Towards this end。 | 5/7 | 4 | 1/1 | 4/5 | 常用 | To this end, we propose X, a gated recurrent memory framework for stable and efficient...（H5） |
| `On the one hand, ... On the other hand, ...` | 对举两面〔连接·对举〕。注：house自有。 | 5/6 | 4 | 0/0 | 1/1 | 常用 | On the one hand, the introduction of the rubricator role increases the rollout burden and...（H6） |
| `In contrast, ...` | 句首对比：基线 vs 本方法〔连接·转折〕。注：house偏爱（H4 最多）； | 4/14 | 3 | 3/5 | 7/16 | 常用 | In contrast, when we assign random values between 0 and 1 as rubric rewards (i.e...（H6） |
| `..., thereby V-ing ...` | 句中结果：从而〔连接·因果〕。注：house偏好的句内因果。 | 4/14 | 3 | 3/4 | 4/8 | 常用 | ...introduction of the rubricator role increases the rollout burden and thereby requires more training time...（H6） |
| `Finally, ...` | 列举最后一点/路线图收尾〔连接·顺序〕 | 4/12 | 4 | 3/6 | 2/2 | 常用 | Finally, we present how to optimize the policy [M] under the two roles based on...（H6） |
| `..., thus V-ing ... / X thus V ...` | 句中结果〔连接·因果〕。注：house句中用法； | 4/11 | 3 | 6/34 | 8/25 | 常用 | ...lack theoretical grounding, thus raising concerns about their robustness and generalizability in addressing the...（H4） |
| `To address this/these issues/limitations, we ...` | 回指问题后给出对策〔连接·目的〕。注：house最常用的回指型钩子（H5、H4） | 4/9 | 3 | 3/3 | 4/5 | 常用 | To address this, we use these rubrics as in-prompt hints and evaluate the model’s reasoning...（H6） |
| `instead of ...` | 替代：用 A 代替 B〔连接·转折〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 4/8 | 3 | 5/9 | 4/9 | 常用 | ...in Figure [REF], instead of encoding the entire context in a single forward pass...（H5） |
| `For example, ...` | 句首举例〔连接·举例〕。注：导师重、house轻。 | 4/7 | 4 | 8/44 | 6/18 | 常用 | For example, some sub-directions may cause refusal in response to roleplay-style prompts [CITE].（H4） |
| `When ..., ...` | 句首条件〔连接·条件〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 4/7 | 3 | 7/13 | 7/15 | 常用 | When compromised, LLMs may generate harmful or misleading outputs, posing undesirable legal and social risks...（H4） |
| `Despite (the success of / these findings) ...` | 句首让步：承认成就再转折〔连接·转折〕。注：Despite these findings / the empirical success。 | 4/6 | 3 | 7/11 | 4/7 | 常用 | Despite chain-of-thought (CoT) playing crucial roles in LLM reasoning, directly rewarding it is difficult: training...（H6） |
| `Inspired by (the success of) ..., ...` | 句首：受……启发〔连接·依据〕 | 4/6 | 3 | 4/6 | 0/0 | 常用 | Inspired by recent self-evolving training methods, we propose X（H6） |
| `Generally, ...` | 句首概括〔连接·总结〕 | 4/6 | 3 | 0/0 | 1/1 | 常用 | Generally, X enables RLVR to move beyond outcome-centric optimization by rewarding CoTs with supervision criteria...（H6） |
| `Second, ... / Secondly, ...` | 列举第二点〔连接·顺序〕 | 4/5 | 3 | 3/3 | 2/3 | 常用 | Second, we validate the effectiveness of the two introduced gating mechanisms, where X can reduce...（H5） |
| `Conversely, ...` | 句首反向对比〔连接·转折〕 | 4/5 | 3 | 0/0 | 2/3 | 常用 | Conversely, positive steering strength decreases the logits of the </think> token, reducing the likelihood of...（H3） |
| `As a result, ...` | 句首结果〔连接·因果〕 | 3/5 | 3 | 8/20 | 6/18 | 常用 | As a result, such risks of memory explosion undermine both long-horizon stability and runtime efficiency...（H5） |
| `That is, ... / -- that is, ...` | 换言之（多用破折号插入）〔连接·细化〕。注：house多写成 ' -- that is, '。 | 3/5 | 3 | 4/14 | 6/11 | 常用 | That is, most of the chunks do not contain any information for answering the question...（H5） |
| `In other words, ...` | 句首换个说法〔连接·细化〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 3/4 | 2 | 0/0 | 4/5 | 常用 | In other words, we need to treat the positive item as an anchor, quantify the...（H1） |
| `To fill/bridge this gap, we ...` | 句首：填补空白〔连接·目的〕。注：house自有。 | 3/4 | 2 | 0/0 | 3/3 | 常用 | To fill this gap, we propose X (X),...（H6） |
| `For instance, ...` | 句首举例〔连接·举例〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 3/3 | 2 | 8/13 | 5/7 | 常用 | For instance, while certain movies, such as those of homosexual movies (illustrated in Figure [REF]),...（H2） |
| `..., whereas ...` | 句中对照〔连接·转折〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/3 | 2 | 7/14 | 3/6 | 常用 | ...(i.e. [M] ), whereas for chunks without evidence, it is rewarded when generating [M]...（H5） |
| `Although ...` | 句首让步〔连接·转折〕 | 3/3 | 2 | 5/10 | 5/8 | 常用 | Although prior efforts, such as vector calibration and conditional steering, have attempted to mitigate this...（H4） |
| `To further + V ..., we ...` | 目的不定式：进一步研究〔连接·目的〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/3 | 3 | 0/0 | 4/6 | 常用 | To further investigate, we show that this direction vector consistently produces positive predictions of reasoning...（H3） |
| `We move on to / We begin with / Now, ...` | 顺序过渡〔连接·顺序〕。注：导师式。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/3 | 3 | 2/3 | 2/2 | 常用 | ...matrix [M] , we now turn to enhancing safety by inducing refusal behaviors on malicious...（H4） |
| `Based on these findings / this insight / the above, ...` | 句首：基于前述发现〔连接·依据〕。注：house自有（H3、H6、H5） | 3/3 | 3 | 1/3 | 2/4 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `Third, ...` | 列举第三点〔连接·顺序〕 | 3/3 | 3 | 0/0 | 2/2 | 常用 | Third, we systematically analyze the role of components in X with the ablation study, including...（H5） |
| `For simplicity / For notational simplicity, ...` | 句首：简化记号〔连接·细化〕 | 3/3 | 2 | 0/0 | 0/0 | 常用 | For notational simplicity, we omit the layer superscript [M] in the following discussions.（H4） |
| `Once ..., ...` | 句首时间条件〔连接·条件〕 | 2/4 | 2 | 0/0 | 2/2 | 常用 | Once terminated, the final memory [M] will be sent into the answer agent [M] immediately...（H5） |
| `To address ..., we propose/introduce X, ...` | 框架句：针对问题提出方法〔连接·目的〕。注：house intro 的提出句。 | 2/3 | 2 | 2/2 | 3/3 | 常用 | To fill this gap, we propose X (X),...（H6） |
| `In addition, ...` | 句首递进〔连接·递进〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 1/1 | 1/1 | 常用 | In addition, we present the trend of average cosine similarity across layers in Figure [REF].（H3） |
| `Motivated by ..., ...` | 句首动机〔连接·依据〕 | 2/3 | 2 | 0/0 | 1/1 | 常用 | Motivated by the observation that LRMs automatically allocate longer reasoning strength for more difficult questions,...（H3） |
| `In this way, ...` | 句首收尾：这样就实现了……〔连接·细化〕。注：公式或段落后的 so-what 收尾（prose.md T7） | 2/3 | 2 | 0/0 | 0/0 | 常用 | In this way, we reconstruct a refusal direction vector [M] for malicious prompts to steer...（H4） |
| `Note that ... / We note that ...` | 提示细节〔连接·立场〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 7/20 | 6/10 | 常用 | We note that two LM-enhanced CF methods, KAR and RLMRec, both show improvements over the...（H2） |
| `This is because ...` | 解释原因〔连接·因果〕。注：house的用例只在附录中。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 1/1 | 0/0 | 常用 | This is because the introduction of the update gate encourage the memory agent only to...（H5） |
| `If X ..., then Y is expected to ...` | 可证伪预测：若……则……〔连接·条件〕。注：H3 T16。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | If the LRM plans the reasoning strength via a shared directional vector, then the four...（H3） |
| `It is important to highlight/note that ...` | 定位声明〔连接·立场〕。注：H2、H3（prose.md T15） | 2/2 | 2 | 0/0 | 0/0 | 常用 | It is important to highlight that we merely outline the potential application directions, and more...（H3） |
| `Together, ...` | 句首合起来看〔连接·总结〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 0/0 | 0/0 | 常用 | Together, these capabilities make it plausible to self-propose supervision criteria (e.g. rubrics) for assessing CoT...（H6） |
| `We highlight that ...` | 强调收尾〔连接·立场〕。注：house自有（H4、H5） | 2/2 | 2 | 0/0 | 0/0 | 常用 | We highlight that X provides a stable and efficient long context reasoning paradigm with the...（H5） |
| `Meanwhile, ...` | 句首并行补充〔连接·递进〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/4 | 1 | 1/1 | 2/3 | 偶用 | Meanwhile, as Figure [REF] depicts, if [M] , [M] tends to be a false negative...（H1） |
| `..., resulting in ...` | 句中后果〔连接·因果〕 | 2/3 | 1 | 2/3 | 5/9 | 偶用 | ...to overly refuse benign prompts, resulting in a trade-off between safety enhancement and utility preservation...（H4） |
| `Consequently, ...` | 句首结果〔连接·因果〕。注：house罕用（0-1 次/篇） | 2/3 | 1 | 1/1 | 5/7 | 偶用 | Consequently, the model can converge to sub-optimal reasoning behaviors, limiting robustness and overall capability [CITE].（H6） |
| `..., in contrast, ... / in contrast to X` | 句中对比（不含句首）〔连接·转折〕。注：含 in contrast to / with。 | 2/3 | 1 | 1/1 | 4/6 | 偶用 | Conditional steering, in contrast, activates the refusal vector only when input activations exceed a predefined...（H4） |
| `draw inspiration from ...` | 借鉴〔连接·依据〕。注：H4: we draw inspiration from recent ... studies。 | 2/3 | 1 | 0/0 | 4/4 | 偶用 | They draw inspiration from the findings that activations of benign and malicious prompts [CITE] are...（H4） |
| `In particular, ...` | 句首聚焦特例〔连接·细化〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 8/51 | 6/10 | 偶用 | In particular, X substantially improves Recall@20 by 10.00% to 21.90% compared to InfoNCE and by...（H1） |
| `Benefiting from ..., ...` | 句首：得益于〔连接·依据〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 2/3 | 3/3 | 偶用 | Benefiting from this adversarial framework, our X endows two appealing properties.（H1） |
| `Nevertheless, ...` | 句首让步转折〔连接·转折〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/2 | 1 | 4/5 | 0/0 | 偶用 | Nevertheless, the tasks of effectively distinguishing between the hard and false negatives, and of learning...（H1） |
| `To better + V ..., ...` | 目的不定式：更好地……〔连接·目的〕 | 2/2 | 1 | 0/0 | 3/4 | 偶用 | To better understand and reshape the contrastive loss specially tailored for top- [M] recommendation, we...（H1） |
| `In practice, ...` | 句首：实际做法〔连接·细化〕 | 2/2 | 1 | 0/0 | 1/1 | 偶用 | In practice, the memory agent [M] and the answer agent [M] share the same parameterized...（H5） |
| `Take/Consider X as an example` | 以某例说明〔连接·举例〕。注：house用 Consider(ing) Figure X as an example；导师用 Taking X as an example。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/2 | 1 | 6/12 | 5/7 | 偶用 | Consider Figure [REF] as an example again, homosexual movies, which differ significantly in textual meaning,...（H2） |
| `Similarly, / Likewise, / Analogously, ...` | 句首类比〔连接·类比〕。注：house几乎不用；house的用例只在附录中。 | 1/2 | 1 | 5/9 | 4/8 | 偶用 | Similarly, the correlation coefficient reaches over 0.8, indicating that the reasoning strength can be predicted...（H3） |
| `Intuitively, ...` | 句首：先给直觉〔连接·细化〕。注：H4: Intuitively, effective X should ...； | 1/2 | 1 | 4/6 | 4/7 | 偶用 | Intuitively, effective steering should lead to distinct trends: for malicious prompts, substantial activation shifts indicate...（H4） |
| `Having established/ensured X, we now ...` | 句首回顾过渡〔连接·依据〕。注：house仅 H4 一篇；导师多。 | 1/1 | 1 | 6/14 | 5/7 | 偶用 | ...X Having ensured the utility preservation via null-space projection matrix [M] ,...（H4） |
| `in order to ...` | 目的〔连接·目的〕。注：导师用，house 0；用 to V 即可；附录里的命中全是模型输出或提示词，按house 0 计。层级由切片的 仅导师 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 5/9 | 3/6 | 偶用 | ...[CITE] learn invariant representations against popularity bias in order to achieve stability and generalization ability...（refB8） |
| `we conjecture/hypothesize that ...` | 提出猜想〔连接·立场〕。注：refB 常用。 | 1/1 | 1 | 2/4 | 5/7 | 偶用 | ...language models [CITE], we hypothesize that LRMs may modulate their reasoning planning through pre-allocated...（H3） |
| `Overall, ...` | 句首总括〔连接·总结〕 | 1/1 | 1 | 0/0 | 4/5 | 偶用 | Overall, our results suggest a new paradigm in which models autonomously generate CoT supervision criteria...（H6） |
| `Besides, ...` | 句首补充〔连接·递进〕。注：house的用例只在附录中。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 2/2 | 2/2 | 偶用 | Besides, the performance also drops from replacing InfoNCE loss [CITE] with BPR loss [CITE] (w/o...（H2） |
| `Interestingly, ...` | 句首表意外〔连接·立场〕。注：house的用例只在附录中。 | 1/1 | 1 | 2/2 | 1/1 | 偶用 | Interestingly, DeepSeek-R1-Distill-Llama-8B is more sensitive to the steering strength [M] .（H3） |
| `Subsequently, ...` | 句首：随后〔连接·顺序〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 2/2 | 1/1 | 偶用 | Subsequently, in Section [REF], we empirically assess the existence and robustness of collaborative signals in...（H2） |
| `If so, ...` | 承接问题：如果是这样〔连接·条件〕 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | If so, this could enable a new paradigm of self-improving reasoning, shifting RL from optimizing...（H6） |
| `Unfortunately, ...` | 句首负面转折〔连接·转折〕 | 1/1 | 0 | 0/0 | 2/2 | 已弃用 | Unfortunately, this inherent inductive bias is rarely considered in current CL-based CF methods, resulting in...（H1） |
| `In summary, / To sum up, / To summarize, ...` | 句首总结〔连接·总结〕 | 0/0 | 0 | 3/5 | 2/4 | 仅参考 | To summarize, X uses very few additional model parameters to achieve the high-order connectivity modeling.（refA3） |
| `More specifically, ...` | 句首进一步细化〔连接·细化〕 | 0/0 | 0 | 2/3 | 2/3 | 仅参考 | More specifically, the representation vector of an ego node is computed by recursively aggregating and...（refA7） |
| `Nonetheless, ...` | 句首让步转折〔连接·转折〕。注：导师用，house不用。 | 0/0 | 0 | 1/3 | 3/3 | 仅参考 | Nonetheless, the learning objective is easily influenced by the popularity bias of the training interactions.（refB8） |
| `On the contrary, ...` | 句首反驳〔连接·转折〕 | 0/0 | 0 | 1/1 | 2/2 | 仅参考 | On the contrary, SR-a and NSCR-a emphasizes the social modelling via the effective social regularization.（refA1） |
| `Accordingly, ...` | 句首相应地〔连接·因果〕 | 0/0 | 0 | 0/0 | 2/3 | 仅参考 | Accordingly, we specially designed a user profile for X that incorporates these social traits.（refB6） |
| `Remarkably, ...` | 句首强调〔连接·立场〕 | 0/0 | 0 | 0/0 | 2/3 | 仅参考 | Remarkably, X can discern domain styles and fuse domain-specific elements across them — a notable...（refB7） |
| `By doing so, / In doing so, ...` | 句首：这样做〔连接·细化〕 | 0/0 | 0 | 2/2 | 0/0 | 仅参考 | By doing so, we not only enrich the initial embeddings with embedding propagation layers, but...（refA3） |
| `Importantly, ...` | 句首强调〔连接·立场〕 | 0/0 | 0 | 0/0 | 2/2 | 仅参考 | Importantly, the decoder is operational only during training for reconstruction.（refB7） |
| `Furthermore, ...` | 句首递进〔连接·递进〕。注：house罕用，规则列为回避。 | 3/5 | 3 | 7/16 | 8/19 | 禁用 | Furthermore, they lack theoretical grounding, thus raising concerns about their robustness and generalizability in addressing...（H4） |
| `Thus, ...` | 句首推论〔连接·因果〕。注：句首 Thus 已禁用；house只用句中 ', thus V-ing'；house的用例只在附录中。 | 3/3 | 2 | 2/5 | 4/8 | 禁用 | Thus, [M] satisfies: [EQ] So [M] spans [M] .（H4） |
| `Notably, ...` | 句首强调〔连接·立场〕。注：house罕用，规则列为回避。 | 2/4 | 1 | 0/0 | 4/6 | 禁用 | Notably, the conditional steering baseline CAST [CITE] even fails on all the mathematical problems.（H4） |
| `Surprisingly, ...` | 句首表惊讶〔连接·立场〕。注：已禁用。 | 2/4 | 1 | 0/0 | 4/5 | 禁用 | Surprisingly, our findings demonstrate that item representations, when linearly mapped from advanced LM representations, yield...（H2） |
| `To be specific, ...` | 句首细化〔连接·细化〕。注：H1、H2 用过，已禁用。 | 2/3 | 1 | 0/0 | 0/0 | 禁用 | To be specific, we incorporate several crucial components (i.e. a multilayer perceptron (MLP), graph convolution,...（H2） |
| `Worse still, ...` | 句首升级负面〔连接·递进〕。注：refB 常用；house在 H1、H2 用过，已禁用。 | 2/2 | 1 | 0/0 | 5/8 | 禁用 | Worse still, the relationship between the LM-based language space and the behavior space remain largely...（H2） |
| `Clearly, / Obviously, ...` | 句首断言显然〔连接·立场〕。注：已禁用。 | 1/2 | 0 | 8/22 | 5/14 | 禁用 | Clearly, the hardness degree [M] encapsulates a fine-grained ranking criterion for user [M] , thereby...（H1） |
| `Hence, ...` | 句首推论〔连接·因果〕。注：导师专用，house从不句首用；已禁用。 | 0/0 | 0 | 7/27 | 6/20 | 禁用 | Hence, the key to popularity generalization lies in mitigating the influence of popularity on these...（refB3） |
| `Towards this end, ...` | 句首：为此〔连接·目的〕。注：refB 专用，已禁用。 | 0/0 | 0 | 8/24 | 4/8 | 禁用 | Towards this end, CF simply assumes that behaviorally similar users would have similar preference for...（refB8） |
| `As such, ...` | 句首因此〔连接·因果〕。注：已禁用。 | 0/0 | 0 | 7/32 | 2/2 | 禁用 | As such, MF can be deemed as a shallow neural network with one hidden layer...（refA1） |
| `In a nutshell, ...` | 句首总结〔连接·总结〕。注：refA 用，已禁用。 | 0/0 | 0 | 4/5 | 4/5 | 禁用 | In a nutshell, the reward [M] for the intervention action [M] at state [M] is...（refA8） |
| `Technically, ...` | 句首：技术上说〔连接·细化〕。注：refA 专用，已禁用。 | 0/0 | 0 | 2/7 | 0/0 | 禁用 | Technically, it uses ID embeddings of users and items to perform the prediction.（refA7） |

## 词类：动词

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `show(s/ed/n) (that)` | 展示/表明〔报告动词〕。注：house最高频报告动词。 | 6/144 | 5 | 8/72 | 8/84 | 常用 | As shown in Table [REF], training with X outperforms the vanilla RLVR across multiple datasets...（H6） |
| `enhance / improve` | 提升〔方法动词〕 | 6/84 | 5 | 8/59 | 8/63 | 常用 | Additionally, introducing self-evolving training for the rubricator further improves the reasoning performance (cf. Section [REF]).（H6） |
| `indicate(s) (that)` | 表明〔报告动词〕 | 6/70 | 5 | 8/56 | 8/50 | 常用 | ...improvement on the AIME dataset, indicating rubric hints may encourage LLMs to conduct more exploration...（H6） |
| `compute / calculate` | 计算〔方法动词〕 | 6/61 | 5 | 6/13 | 7/22 | 常用 | After computing role-specific rewards, we optimize the shared policy model [M] , with advantages computed...（H6） |
| `adopt` | 采用（设置/方法）〔方法动词〕。注：We adopt X as ... | 6/57 | 5 | 8/72 | 8/37 | 常用 | In the widely adopted RLVR paradigm, rewards typically depend solely on the correctness of the...（H6） |
| `denote(s)` | 表示（符号）〔方法动词〕 | 6/57 | 5 | 8/60 | 8/38 | 常用 | ...as: [EQ] where [M] denotes the CoT process, and [M] denotes the predicted final answer...（H6） |
| `present` | 呈现〔报告动词〕。注：不计 evidence-present 之类复合词。 | 6/56 | 5 | 8/40 | 8/43 | 常用 | Finally, we present how to optimize the policy [M] under the two roles based on...（H6） |
| `conduct (experiments)` | 开展（实验）〔方法动词〕。注：We conduct experiments on ... | 6/55 | 5 | 8/60 | 8/40 | 常用 | We conduct experiments on two open-source Qwen models of different sizes (8B and 4B) [CITE].（H6） |
| `explore / investigate / examine` | 探究〔方法动词〕 | 6/51 | 5 | 8/106 | 8/30 | 常用 | We further conduct comprehensive experiments to examine the effectiveness of the proposed X.（H6） |
| `achieve(s)` | 取得（性能）〔报告动词〕 | 6/50 | 5 | 8/73 | 8/78 | 常用 | Additionally, BoN (N=16) achieves further improvement on the AIME dataset, indicating rubric hints may encourage...（H6） |
| `introduce` | 引入（模块/方法）〔方法动词〕。注：H5 最高频动词；也作形容词 the introduced gates。 | 6/49 | 5 | 8/41 | 8/27 | 常用 | Additionally, introducing self-evolving training for the rubricator further improves the reasoning performance (cf. Section [REF]).（H6） |
| `preserve / maintain` | 保持（性能）〔方法动词〕。注：H4: utility preservation。 | 6/45 | 5 | 3/12 | 7/18 | 常用 | ...[M] , allowing the model to learn from role-specific feedback while maintaining a unified policy...（H6） |
| `yield(s)` | 带来/产出〔报告动词〕 | 6/44 | 5 | 6/23 | 7/18 | 常用 | ...self-evolving rubrics can yield meaningful CoT rewarding signals, which lay an important foundation for...（H6） |
| `enable(s) X to ...` | 使……能够〔方法动词〕。注：Such a reward enables ... | 6/43 | 5 | 3/5 | 5/19 | 常用 | RQ3: How does the self-evolving enable the rubricator to gradually propose better rubrics?（H6） |
| `apply` | 应用〔方法动词〕 | 6/39 | 5 | 8/37 | 8/23 | 常用 | ...outcome reward is applied first by matching the generated answer [M] with the ground-truth...（H6） |
| `exhibit(s)` | 表现出〔报告动词〕 | 6/37 | 5 | 6/16 | 7/26 | 常用 | ...[M] , X exhibits a more stable learning curve and gradually outperforms the ablation...（H6） |
| `illustrate / depict` | 图示〔报告动词〕 | 6/36 | 5 | 8/23 | 8/47 | 常用 | An overview of the key process is illustrated in Figure [REF].（H6） |
| `evaluate` | 评估〔方法动词〕 | 6/36 | 5 | 8/24 | 8/41 | 常用 | ...address this, we use these rubrics as in-prompt hints and evaluate the model’s reasoning performance...（H6） |
| `suggest(s) (that)` | 暗示/表明（弱）〔报告动词〕 | 6/36 | 5 | 7/37 | 7/20 | 常用 | Overall, our results suggest a new paradigm in which models autonomously generate CoT supervision criteria...（H6） |
| `report (the performance)` | 报告（数字）〔报告动词〕。注：We report the performance ... | 6/32 | 5 | 8/19 | 7/16 | 常用 | ...RLVR which only considers the outcome reward, and report the performance comparison in Table [REF]...（H6） |
| `focus on` | 聚焦〔方法动词〕 | 6/24 | 5 | 7/29 | 8/25 | 常用 | ...[M] ) by focusing on count aggregation") of rubric [M] and [M] is the...（H6） |
| `..., indicating that ...` | 分词尾：表明〔报告动词〕 | 6/22 | 5 | 6/9 | 7/13 | 常用 | ...improvement on the AIME dataset, indicating rubric hints may encourage LLMs to conduct more exploration...（H6） |
| `highlight(s) (the importance/necessity of)` | 凸显〔报告动词〕 | 6/15 | 5 | 8/27 | 6/16 | 常用 | Such results highlight the importance of rubrics self-evolving.（H6） |
| `We (further) conduct (extensive) experiments on ...` | 开展实验〔方法动词〕 | 6/14 | 5 | 8/14 | 7/15 | 常用 | We conduct experiments on two open-source Qwen models of different sizes (8B and 4B) [CITE].（H6） |
| `demonstrate` | 证明/展示〔报告动词〕 | 5/33 | 4 | 8/36 | 8/33 | 常用 | ...these baseline methods demonstrate some degree of utility preservation, their performance is unstable and...（H4） |
| `design` | 设计〔方法动词〕。注：多为名词 reward design。 | 5/31 | 4 | 8/30 | 8/42 | 常用 | ...Such a reward design encourages the rubricator to continuously refine its proposed rubrics, consistently...（H6） |
| `optimize` | 优化〔方法动词〕 | 5/30 | 4 | 8/38 | 6/40 | 常用 | We jointly optimize the policy model [M] under two roles by assigning different rewards for...（H6） |
| `outperform(s)` | 优于〔报告动词〕 | 5/29 | 4 | 8/26 | 8/17 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `visualize` | 可视化〔方法动词〕。注：We visualize the ... | 5/26 | 5 | 1/1 | 6/10 | 常用 | We visualize the training dynamics under different selection of [M] (i.e. 1.0, 0.9, and 0.5)...（H5） |
| `incorporate / integrate` | 融入〔方法动词〕 | 5/24 | 4 | 8/57 | 7/39 | 常用 | ...we propose X, which incorporates two text-controlled gates for more stable and efficient long-context reasoning...（H5） |
| `address (the issue)` | 解决/应对〔方法动词〕 | 5/24 | 4 | 5/16 | 7/23 | 常用 | To address this, we use these rubrics as in-prompt hints and evaluate the model’s reasoning...（H6） |
| `lack (of)` | 缺乏〔问题动词〕。注：house自有。 | 5/24 | 4 | 5/8 | 4/8 | 常用 | ...implementation challenges [CITE], while the lack of such supervision may induce sub-optimal reasoning strategies [CITE]...（H6） |
| `identify` | 识别/指出〔报告动词〕。注：H5: We identify two key limitations ... | 5/22 | 4 | 3/8 | 7/34 | 常用 | As shown, X can identify the last evidence position and exit accordingly in most cases.（H5） |
| `ensure` | 确保〔方法动词〕 | 5/21 | 4 | 7/14 | 7/23 | 常用 | Additionally, to ensure the proposed rubrics can be stably parsed, we reward the rubricator with...（H6） |
| `mitigate / alleviate` | 缓解（问题）〔方法动词〕 | 5/20 | 4 | 3/7 | 6/66 | 常用 | ...generation capability, so as to progressively produce more informative and challenging rubrics that mitigate saturation...（H6） |
| `compare` | 比较〔方法动词〕 | 5/18 | 4 | 8/18 | 6/20 | 常用 | We compare X with the vanilla RLVR which only considers the outcome reward, and report...（H6） |
| `we test/investigate whether ...` | 检验是否〔方法动词〕。注：house自有（H3、H6） | 5/14 | 4 | 4/5 | 1/1 | 常用 | ...this view, we investigate whether the same policy can simultaneously serve as a reasoner...（H6） |
| `verify` | 验证〔报告动词〕 | 5/13 | 4 | 7/43 | 4/13 | 常用 | ...datasets and LLMs, verifying such rubrics provide effective CoT supervision to boost LLM reasoning...（H6） |
| `leverage` | 利用〔方法动词〕 | 5/11 | 4 | 8/28 | 8/29 | 常用 | ...that [M] , leveraging fundamental properties of null space [CITE] to preserve utility --...（H4） |
| `consistently/generally outperform(s)` | 稳定/总体优于〔报告动词〕。注：house结果句（H5: generally outperforms） | 5/11 | 4 | 5/7 | 6/9 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `construct` | 构造〔方法动词〕 | 4/31 | 4 | 8/28 | 5/11 | 常用 | These tasks are constructed with different context lengths, ranging from 7K to 896K.（H5） |
| `capture` | 捕捉〔方法动词〕 | 4/27 | 4 | 8/64 | 7/38 | 常用 | ...activations of compliant and refused prompts, capturing the latent semantics causing the refusal behavior [CITE]...（H4） |
| `employ` | 使用〔方法动词〕 | 4/17 | 3 | 8/50 | 8/36 | 常用 | Specifically, we employ a linear probe to predict the reasoning token count from question activations,...（H3） |
| `encourage` | 鼓励（模型行为）〔方法动词〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 4/15 | 3 | 6/22 | 5/19 | 常用 | ...improvement on the AIME dataset, indicating rubric hints may encourage LLMs to conduct more exploration...（H6） |
| `demonstrate(s) the effectiveness of X` | 证明有效性〔报告动词〕。注：refA 的 rationality and effectiveness 已禁用。 | 4/13 | 3 | 8/17 | 5/12 | 常用 | Such phenomena show the effectiveness of self-evolving in enhancing reasoning.（H6） |
| `We observe/notice that ...` | 报告观察〔报告动词〕 | 4/12 | 3 | 5/6 | 8/14 | 常用 | ...GPQA series.) Additionally, we observe that the performance gain is more obvious on larger...（H6） |
| `suffer(s) from ...` | 受困于（缺陷）〔问题动词〕 | 4/7 | 3 | 7/9 | 6/17 | 常用 | ...the utility of the LLM, while baselines suffer from degraded general capabilities (cf. Section [REF])...（H4） |
| `study how ... / We study how` | 研究如何〔方法动词〕 | 4/7 | 4 | 3/3 | 0/0 | 常用 | We study how the update gate reduces the risk of memory explosion by tracking the...（H5） |
| `prove` | 证明（理论）〔报告动词〕 | 4/6 | 3 | 1/1 | 2/3 | 常用 | ...which shows improved performance when rubrics are used as hints—proving the rubrics effectively guide reasoning...（H6） |
| `induce` | 诱导（行为）〔方法动词〕 | 3/29 | 3 | 1/2 | 1/1 | 常用 | ...implementation challenges [CITE], while the lack of such supervision may induce sub-optimal reasoning strategies [CITE]...（H6） |
| `reveal(s)` | 揭示〔报告动词〕 | 3/19 | 2 | 6/20 | 7/19 | 常用 | ...strength increases by leveraging the null-space constraint, which is revealed through visualization (cf. Section [REF])...（H4） |
| `augment` | 增强（加上模块）〔方法动词〕。注：H5: augment ... with two gates。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/11 | 2 | 2/2 | 5/14 | 常用 | ...idea is to augment the recurrent workflow with two text-controlled gates -- an update...（H5） |
| `utilize` | 利用〔方法动词〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/8 | 2 | 7/16 | 8/31 | 常用 | ...that LRMs may utilize a single, shared directional vector to distinguish between questions of...（H3） |
| `find / We find that` | 我们发现〔报告动词〕。注：排除 can be found。 | 3/8 | 3 | 7/24 | 6/14 | 常用 | We find possibilities of achieving this from the recent advances in the research line of...（H6） |
| `validate` | 验证〔报告动词〕 | 3/7 | 2 | 3/4 | 8/25 | 常用 | Such phenomena validate the effectiveness of X, which provides a "free-lunch" for improving the RL...（H6） |
| `develop` | 开发〔方法动词〕 | 3/6 | 3 | 8/13 | 6/12 | 常用 | ...that the reasoning planning capabilities may be developed in the later layers of these models...（H3） |
| `endow X with ...` | 赋予能力〔方法动词〕。注：H5: To endow the model with such capabilities。 | 3/6 | 2 | 5/9 | 6/9 | 常用 | To endow the model with such capabilities, we introduce two reward signals [M] and [M]...（H5） |
| `hinder / impede` | 阻碍〔问题动词〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/5 | 2 | 4/7 | 6/7 | 常用 | ...two key limitations that may hinder the memory stability and efficiency of reasoning in practice:...（H5） |
| `facilitate` | 促进〔方法动词〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/5 | 2 | 6/16 | 2/5 | 常用 | RQ4: How effective are generated rubrics at facilitating reasoning when used as in-prompt hints?（H6） |
| `can be defined/written/expressed as (follows):` | 公式引入〔方法动词〕 | 3/4 | 2 | 1/2 | 1/3 | 常用 | ...in group [M] can be expressed as [M] , where [M] denotes the total turn...（H5） |
| `imply / implies` | 意味着〔报告动词〕 | 2/6 | 2 | 3/5 | 4/7 | 常用 | ...that higher correlation implies that satisfying the rubric is consistently associated with correct final...（H6） |
| `uncover` | 揭示（发现隐藏的）〔报告动词〕。注：house签名词之一。 | 2/6 | 2 | 3/6 | 3/3 | 常用 | ...the reasoning planning capabilities of LRMs, and uncovers the underlying mechanisms that control such planning...（H3） |
| `limit(s/ing) the ...` | 限制〔问题动词〕。注：H5: still largely limit the memory stability。 | 2/4 | 2 | 4/9 | 3/3 | 常用 | ...early exit mechanism still largely limit the memory stability and efficiency of the vanilla approach...（H5） |
| `remain(s) challenging` | 仍具挑战〔问题动词〕。注：H5: While important, this capability remains challenging。 | 2/4 | 2 | 0/0 | 0/0 | 常用 | ...or supervising CoTs remains challenging in practice, mainly for two reasons: (i) training additional...（H6） |
| `confirm` | 证实〔报告动词〕 | 2/3 | 2 | 4/4 | 3/3 | 常用 | While we have confirmed that self-proposed and self-evolved rubrics can provide valid reward signals for...（H6） |
| `fail(s) to ...` | 未能〔问题动词〕。注：导师主力，house少。 | 2/8 | 1 | 7/28 | 8/25 | 偶用 | ...figure), the LLM fails to achieve performance improvements and even exhibits sudden performance drops...（H6） |
| `amplify / is amplified when ...` | 放大（问题）〔方法动词〕。注：H5: This X is amplified when ... | 1/1 | 1 | 1/1 | 4/11 | 偶用 | This inefficiency is amplified when evidence is unevenly distributed (e.g. when reranking brings key evidence...（H5） |
| `devise` | 设计（较正式）〔方法动词〕。注：H1 后少用。 | 1/6 | 0 | 7/22 | 6/14 | 已弃用 | ...bridge the gap, we aim to devise a variant of InfoNCE by considering these factors...（H1） |
| `justify` | 论证〔报告动词〕 | 0/0 | 0 | 7/12 | 6/12 | 仅参考 | Baselines: To justify the effectiveness of our proposal, we study the performance of the following...（refA1） |
| `One possible reason is that ...` | 归因〔报告动词〕。注：refA 专用，已禁用。 | 0/0 | 0 | 5/9 | 0/0 | 禁用 | One possible reason is that the preferences of users with too many interactions are too...（refA4） |
| `We ascribe ... to ...` | 归因〔报告动词〕。注：导师专用，已禁用。 | 0/0 | 0 | 1/1 | 4/7 | 禁用 | We ascribe the failure to their preconceived idea of tail items, which possibly change over...（refB2） |

## 词类：形容词

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `key (factor / idea / limitation)` | 关键〔评价形容词〕 | 6/39 | 5 | 8/31 | 7/20 | 常用 | An overview of the key process is illustrated in Figure [REF].（H6） |
| `diverse / various` | 多样的〔评价形容词〕 | 6/33 | 5 | 7/25 | 8/68 | 常用 | We evaluate models on various reasoning benchmarks.（H6） |
| `important / importance` | 重要〔评价形容词〕 | 6/32 | 5 | 8/75 | 6/53 | 常用 | ...rubrics can yield meaningful CoT rewarding signals, which lay an important foundation for our approach...（H6） |
| `effective` | 有效的〔评价形容词〕。注：house最高频评价词。 | 6/30 | 5 | 7/25 | 6/23 | 常用 | RQ4: How effective are generated rubrics at facilitating reasoning when used as in-prompt hints?（H6） |
| `significant` | 显著的〔评价形容词〕 | 6/25 | 5 | 7/24 | 8/37 | 常用 | ...improvements aggregate to statistically significant gains under standard two-sided tests at significance level [M]...（H6） |
| `critical` | 关键的〔评价形容词〕 | 6/9 | 5 | 3/3 | 5/13 | 常用 | ...has emerged as a critical factor for both performance optimization and controllable model behavior [CITE]...（H3） |
| `efficient / efficiency` | 高效〔评价形容词〕 | 5/47 | 5 | 5/8 | 6/10 | 常用 | ...the performance and efficiency comparison of the 7B-sized model under this setting in Table...（H5） |
| `effectiveness` | 有效性〔评价形容词〕 | 5/46 | 4 | 8/56 | 8/27 | 常用 | We further conduct comprehensive experiments to examine the effectiveness of the proposed X.（H6） |
| `vanilla (X)` | 原始基线〔评价形容词〕。注：H5 高频。 | 5/33 | 5 | 0/0 | 1/1 | 常用 | ...X with the vanilla RLVR which only considers the outcome reward, and report the...（H6） |
| `stable / stability` | 稳定〔评价形容词〕。注：H5 epithet: stable and efficient。 | 5/17 | 4 | 3/4 | 6/18 | 常用 | ...exhibits a more stable learning curve and gradually outperforms the ablation version (i.e. X...（H6） |
| `crucial` | 关键的〔评价形容词〕 | 5/11 | 4 | 6/12 | 7/15 | 常用 | ...chain-of-thought (CoT) playing crucial roles in LLM reasoning, directly rewarding it is difficult: training...（H6） |
| `strong` | 强的〔评价形容词〕 | 5/11 | 4 | 5/6 | 6/18 | 常用 | This suggests a strong positive correlation between the pre-allocated vector magnitudes and the reasoning strengths...（H3） |
| `robust / robustness` | 稳健〔评价形容词〕 | 4/35 | 3 | 3/6 | 8/36 | 常用 | Consequently, the model can converge to sub-optimal reasoning behaviors, limiting robustness and overall capability [CITE].（H6） |
| `fine-grained` | 细粒度〔评价形容词〕 | 4/31 | 3 | 3/6 | 3/3 | 常用 | ...requires labor-intensive and fine-grained annotations [CITE], and (ii) the policy’s CoT distribution shifts during...（H6） |
| `simple` | 简单〔评价形容词〕 | 4/17 | 3 | 4/9 | 7/21 | 常用 | ...reward provides a simple way for evolving the quality of generated rubrics, by connecting...（H6） |
| `consistent` | 一致的〔评价形容词〕 | 4/16 | 3 | 7/20 | 8/19 | 常用 | ...Only in the figure), the reasoning capability of the LLM shows consistent improvement during training...（H6） |
| `superior / superiority` | 更优〔评价形容词〕 | 4/16 | 3 | 5/9 | 6/18 | 常用 | ...demonstrate that item representations, when linearly mapped from advanced LM representations, yield superior recommendation performance...（H2） |
| `real-world` | 真实场景的〔评价形容词〕 | 4/14 | 3 | 8/24 | 7/57 | 常用 | ...models (LLMs) for real-world applications [CITE], such as reading an entire book or processing...（H5） |
| `extensive (experiments)` | 大量的（实验）〔评价形容词〕 | 4/11 | 3 | 8/23 | 8/24 | 常用 | We further conduct extensive experiments to verify the effectiveness of X.（H5） |
| `challenging` | 有挑战的〔评价形容词〕 | 4/8 | 3 | 3/5 | 6/13 | 常用 | ...generation capability, so as to progressively produce more informative and challenging rubrics that mitigate saturation...（H6） |
| `widely adopted/used` | 被广泛采用〔评价形容词〕。注：house用 the widely adopted X paradigm；替代 standard practice。 | 4/5 | 3 | 5/7 | 4/5 | 常用 | In the widely adopted RLVR paradigm, rewards typically depend solely on the correctness of the...（H6） |
| `practical` | 实用〔评价形容词〕 | 4/5 | 4 | 2/3 | 4/4 | 常用 | ...directly due to practical implementation challenges [CITE], while the lack of such supervision may...（H6） |
| `informative` | 信息丰富的〔评价形容词〕 | 3/15 | 2 | 6/26 | 3/18 | 常用 | ...model to gradually propose more informative rubrics that better align with the final answer correctness...（H6） |
| `prevailing / prevalent` | 流行的〔评价形容词〕。注：H1、H2。 | 3/14 | 2 | 4/9 | 8/18 | 常用 | To mitigate this, two prevailing strategies are used: vector calibration [CITE] and conditional steering [CITE].（H4） |
| `indiscriminate(ly)` | 不加区分地〔评价形容词〕。注：house自有（H5） | 3/13 | 2 | 0/0 | 1/1 | 常用 | However, indiscriminately updating on all chunks and the lack of an early exit mechanism still...（H5） |
| `sufficient` | 充分的〔评价形容词〕 | 3/10 | 3 | 6/9 | 5/7 | 常用 | ...to process all chunks and provides no early-exit mechanism when the collected evidence is sufficient...（H5） |
| `comprehensive` | 全面的〔评价形容词〕 | 3/9 | 2 | 1/1 | 7/21 | 常用 | We further conduct comprehensive experiments to examine the effectiveness of the proposed X.（H6） |
| `reliable` | 可靠〔评价形容词〕 | 3/7 | 2 | 0/0 | 5/19 | 常用 | RQ1: Can self-proposed rubrics provide reliable rewarding signals for RL training?（H6） |
| `novel` | 新颖的〔评价形容词〕。注：house极少自夸 novel。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/6 | 3 | 3/6 | 5/13 | 常用 | ...first introduce a novel and learnable activation steering mechanism for better principled control in...（H4） |
| `naive (X)` | 朴素基线〔评价形容词〕 | 3/6 | 3 | 0/0 | 0/0 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `promising` | 有前景的〔评价形容词〕。注：H4: has recently emerged as a promising method。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/5 | 2 | 3/7 | 7/12 | 常用 | ...emerged as a promising method for defending against jailbreak attacks [CITE], requiring no additional...（H4） |
| `essential` | 必需的〔评价形容词〕 | 3/5 | 3 | 3/4 | 3/8 | 常用 | ...[CITE] reasoning is essential for large language models (LLMs) to solve complex tasks, where...（H6） |
| `best-performing` | 最好的（方法）〔评价形容词〕 | 3/4 | 2 | 0/0 | 2/2 | 常用 | The best-performing methods per test are bold, except for our ablation study of directly applying...（H4） |
| `straightforward` | 直接的〔评价形容词〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 3/3 | 2 | 2/2 | 2/2 | 常用 | ...text-based user intentions, enabling recommenders to evolve into intention-aware systems through a straightforward paradigm shift...（H2） |
| `comparable / competitive` | 相当的〔评价形容词〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 2/6 | 2 | 6/13 | 6/9 | 常用 | ...allows the generator to effectively learn user preferences and behaviors, leading to competitive recommendation performance...（H2） |
| `heuristic` | 启发式的（贬义：缺理论）〔评价形容词〕。注：H4: largely heuristic and lack theoretical grounding。 | 2/12 | 1 | 1/1 | 1/2 | 偶用 | ...steering methods are limited by their heuristic design, raising concerns about their robustness and effectiveness...（H4） |
| `principled` | 有原则依据的〔评价形容词〕。注：H1、H4 epithet。 | 2/10 | 1 | 0/0 | 1/2 | 偶用 | ...introduce a novel and learnable activation steering mechanism for better principled control in Section [REF]...（H4） |
| `remarkable / impressive` | 出色的〔评价形容词〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/7 | 1 | 4/4 | 7/15 | 偶用 | ...does not bring a stable and remarkable improvement, which is consistent with previous findings [CITE]...（H2） |
| `flexible` | 灵活〔评价形容词〕 | 2/5 | 1 | 3/3 | 2/3 | 偶用 | After that, we combine them with a hyperparameter [M] for more flexible control.（H5） |
| `substantial` | 大幅的〔评价形容词〕 | 2/3 | 1 | 2/2 | 5/8 | 偶用 | ...for malicious prompts, substantial activation shifts indicate successful induction of refusal behavior (termed safety...（H4） |
| `fundamental` | 根本的〔评价形容词〕 | 2/3 | 1 | 1/3 | 2/4 | 偶用 | ...[M] , leveraging fundamental properties of null space [CITE] to preserve utility -- i.e....（H4） |
| `theoretically grounded` | 有理论依据〔评价形容词〕。注：H4 epithet: theoretically grounded and empirically effective。 | 1/9 | 1 | 0/0 | 0/0 | 偶用 | Furthermore, they lack theoretical grounding, thus raising concerns about their robustness and generalizability in addressing...（H4） |
| `time-consuming` | 耗时的〔评价形容词〕 | 1/1 | 1 | 3/3 | 2/2 | 偶用 | ...[M] based on [M] is time-consuming, since the number of datapoints [M] is usually large...（H4） |
| `costly / expensive` | 昂贵的〔评价形容词〕。注：house用 time-consuming / avoidable computation。 | 1/1 | 1 | 3/3 | 1/2 | 偶用 | ...as reward models are costly to train and vulnerable to distribution shift and reward hacking...（H6） |
| `ideal / an ideal X should` | 理想的（先说理想）〔评价形容词〕。注：refB: an ideal X should ... | 1/1 | 0 | 1/1 | 2/4 | 已弃用 | ...most on the fully-exposed dataset, KuaiRec, which is considered as an ideal offline A/B testing...（H1） |
| `state-of-the-art / SOTA` | 最先进的〔评价形容词〕。注：2025 后禁用。 | 1/2 | 0 | 8/38 | 5/26 | 禁用 | ...consistently outperforms the state-of-the-art CL-based CF baselines in terms of all metrics on all...（H1） |

## 词类：副词（模糊语、增强语、程度）

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `further (adv.)` | 进一步〔副词·方式〕 | 6/61 | 5 | 8/47 | 8/65 | 常用 | Additionally, introducing self-evolving training for the rubricator further improves the reasoning performance (cf. Section [REF]).（H6） |
| `may` | 可能（模糊）〔副词·模糊〕。注：house主力模糊词。 | 6/59 | 5 | 6/17 | 7/17 | 常用 | ...RLVR improves answer accuracy but neglects direct CoT supervision, which may lead to suboptimal reasoning...（H6） |
| `potentially / potential` | 潜在地〔副词·模糊〕 | 6/48 | 5 | 7/21 | 7/33 | 常用 | ...optimizing what LLMs answer to autonomously improving how LLMs think, potentially yielding "free-lunch" reasoning gains...（H6） |
| `even` | 甚至〔副词·增强〕 | 6/44 | 5 | 5/9 | 7/20 | 常用 | ...generated rubrics can even serve as in-prompt hints to enhance reasoning at inference time...（H6） |
| `possibly / possible` | 可能〔副词·模糊〕 | 6/39 | 5 | 7/34 | 5/8 | 常用 | We find possibilities of achieving this from the recent advances in the research line of...（H6） |
| `directly` | 直接地〔副词·方式〕 | 6/37 | 5 | 6/17 | 8/13 | 常用 | Directly rewarding CoTs is hard, as reward models are costly to train and vulnerable to...（H6） |
| `consistently` | 一致地〔副词·增强〕 | 6/29 | 5 | 5/15 | 8/21 | 常用 | It consistently outperforms outcome-only RLVR across datasets and LLMs, verifying such rubrics provide effective CoT...（H6） |
| `significantly` | 显著地〔副词·增强〕 | 6/28 | 5 | 6/9 | 8/21 | 常用 | ...steering has minimal impact on benign activations, while significantly altering those associated with malicious prompts...（H4） |
| `largely` | 很大程度上〔副词·模糊〕。注：house签名词。 | 6/27 | 5 | 3/5 | 5/8 | 常用 | ...to adaptively propose and evolve such criteria in the self-evolving training paradigm remains largely underexplored...（H6） |
| `mainly / primarily / mostly` | 主要地〔副词·模糊〕 | 6/25 | 5 | 6/15 | 8/22 | 常用 | ...challenging in practice, mainly for two reasons: (i) training additional reward models (RMs) for...（H6） |
| `respectively` | 分别〔副词·方式〕 | 6/23 | 5 | 8/83 | 8/62 | 常用 | ...where [M] and [M] are the numbers of all generated rubrics and valid rubrics, respectively...（H6） |
| `merely / solely / only` | 仅仅〔副词·方式〕。注：H2: solely based on / merely ... | 6/23 | 5 | 5/9 | 6/13 | 常用 | ...when training is driven solely by rubric-based rewards, without any outcome reward (cf. Section [REF])...（H6） |
| `generally (outperforms)` | 总体上〔副词·模糊〕。注：house结果句的有界表述。 | 6/21 | 5 | 4/7 | 3/3 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `effectively` | 有效地〔副词·增强〕 | 6/20 | 5 | 6/19 | 7/20 | 常用 | ...which shows improved performance when rubrics are used as hints—proving the rubrics effectively guide reasoning...（H6） |
| `tend(s) to` | 倾向于〔副词·模糊〕 | 6/20 | 5 | 6/8 | 7/29 | 常用 | ...drops dramatically, indicating that without the update reward, LLMs tend to update the memory indiscriminately...（H5） |
| `typically` | 通常〔副词·模糊〕 | 6/16 | 5 | 7/15 | 6/15 | 常用 | ...for CoT supervision typically requires labor-intensive and fine-grained annotations [CITE], and (ii) the policy’s...（H6） |
| `explicitly` | 显式地〔副词·方式〕 | 6/14 | 5 | 7/25 | 1/1 | 常用 | ...rewarded or optimized explicitly, even often regarded as a by-product of outcome-centric optimization objectives...（H6） |
| `could` | 可能/能够〔副词·模糊〕 | 5/20 | 4 | 6/23 | 8/21 | 常用 | If so, this could enable a new paradigm of self-improving reasoning, shifting RL from optimizing...（H6） |
| `might` | 可能〔副词·模糊〕。注：refA 偏好。层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 4/20 | 4 | 6/29 | 7/19 | 常用 | ...preference on items reflected by their behavioral similarities) [CITE], might be implicitly encoded by LMs...（H2） |
| `empirically / theoretically` | 实证上/理论上〔副词·方式〕 | 4/19 | 3 | 7/18 | 2/8 | 常用 | ...largely heuristic, heavily relying on empirically designed calibration rules [CITE] or manually crafted conditions [CITE]...（H4） |
| `jointly / simultaneously` | 同时/联合〔副词·方式〕 | 4/12 | 3 | 8/27 | 7/18 | 常用 | We jointly optimize the policy model [M] under two roles by assigning different rewards for...（H6） |
| `especially / particularly` | 尤其〔副词·增强〕 | 4/12 | 3 | 7/23 | 4/7 | 常用 | ...generally brings performance gain, especially on hard tasks such as HQA, SQuAD, and MK series...（H5） |
| `relatively` | 相对地〔副词·模糊〕。注：H4: relatively lower and less robust performance。 | 4/12 | 3 | 5/7 | 6/8 | 常用 | ...as the default setting due to the relatively high reward and balanced update gate accuracy...（H5） |
| `gradually` | 逐渐〔副词·方式〕 | 4/12 | 3 | 2/2 | 4/6 | 常用 | ...baseline’s rubrics can be gradually satisfied by [M] Rea [M] as [M] Rub [M] increases...（H6） |
| `likely / is likely to` | 很可能〔副词·模糊〕 | 4/7 | 3 | 7/22 | 3/5 | 常用 | This is likely due to their heuristic designs, limiting generalization to diverse or evolving jailbreaks...（H4） |
| `simply` | 只需〔副词·方式〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 4/6 | 3 | 2/4 | 5/9 | 常用 | Therefore, we simply use the contrast learning object InfoNCE [CITE] as the loss function without...（H2） |
| `successfully` | 成功地〔副词·方式〕 | 4/5 | 4 | 3/4 | 7/16 | 常用 | The LLM was successfully exploited by the ReNeLLM [CITE] attack.（H4） |
| `greatly / dramatically / considerably` | 大幅地〔副词·增强〕 | 4/4 | 3 | 7/12 | 4/4 | 常用 | ...evidence-free chunks drops dramatically, indicating that without the update reward, LLMs tend to update...（H5） |
| `clearly (mid-sentence)` | 明显地〔副词·增强〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 3/10 | 2 | 3/3 | 5/12 | 常用 | This clearly indicates that X greatly enhances the robustness of the CF model in real-world...（H1） |
| `nearly / almost` | 几乎〔副词·模糊〕。注：house用 nearly zero / largely unchanged。 | 3/8 | 2 | 4/5 | 4/9 | 常用 | ...learns to construct a nearly zero vector for steering benign data, with the null-space constraints...（H4） |
| `up to (N%)` | 最多达到〔副词·模糊〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/7 | 2 | 1/1 | 3/4 | 常用 | ...reasoning tasks showed X outperforms vanilla MemAgent while achieving up to 400% inference speed acceleration...（H5） |
| `easily` | 容易地〔副词·增强〕。注：refB: easily + 负面动词。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 3/5 | 2 | 6/15 | 7/27 | 常用 | ...across all inputs easily causes LLMs to overly refuse benign prompts, resulting in a...（H4） |
| `substantially` | 大幅地〔副词·增强〕 | 3/3 | 2 | 7/8 | 2/3 | 常用 | ...agent workflow is substantially enhanced, even enabling small-sized models to outperform big models that...（H5） |
| `highly` | 高度〔副词·增强〕 | 2/2 | 2 | 5/13 | 6/6 | 常用 | ...of these pre-allocated vectors highly correlate with the required reasoning token number, showing implicit connections...（H3） |
| `approximately / roughly / around` | 大约〔副词·模糊〕 | 2/3 | 1 | 5/6 | 5/7 | 偶用 | ...difficulty, and is approximately proportional to the average additional reasoning token number required to...（H3） |
| `slightly` | 略微〔副词·模糊〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/3 | 1 | 3/4 | 4/5 | 偶用 | ...general CF, we slightly modify three zero-shot methods in the sequential recommendation [CITE], ZESRec...（H2） |
| `strongly` | 强烈地〔副词·增强〕 | 1/2 | 1 | 2/2 | 3/5 | 偶用 | ...models (LLMs) to solve complex tasks, where its quality strongly affects the final-answer correctness [CITE]...（H6） |

## 词类：名词

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `performance` | 性能〔名词·结果〕 | 6/263 | 5 | 8/160 | 8/220 | 常用 | ...address this, we use these rubrics as in-prompt hints and evaluate the model’s reasoning performance...（H6） |
| `capability / ability` | 能力〔名词·结果〕 | 6/113 | 5 | 8/24 | 8/73 | 常用 | Consequently, the model can converge to sub-optimal reasoning behaviors, limiting robustness and overall capability [CITE].（H6） |
| `behavior(s)` | 行为〔名词·结果〕 | 6/108 | 5 | 8/69 | 5/47 | 常用 | Consequently, the model can converge to sub-optimal reasoning behaviors, limiting robustness and overall capability [CITE].（H6） |
| `problem(s)` | 问题〔名词·问题〕 | 6/87 | 5 | 7/19 | 6/55 | 常用 | ...end, if you haven't collected enough information for the problem, return [M] continue [M]...（H5） |
| `mechanism` | 机制〔名词·方法〕 | 6/54 | 5 | 6/44 | 6/26 | 常用 | ...for CoTs, their mechanism of implicitly guiding reasoning remains unclear due to the difficulty...（H6） |
| `improvement(s)` | 提升〔名词·结果〕 | 6/37 | 5 | 8/54 | 7/48 | 常用 | ...(N=16) achieves further improvement on the AIME dataset, indicating rubric hints may encourage LLMs...（H6） |
| `approach` | 方法〔名词·方法〕 | 6/34 | 5 | 6/18 | 8/53 | 常用 | ...an autonomous CoT rewarding approach that requires no human annotation efforts and can evolve gradually...（H6） |
| `limitation(s)` | 局限〔名词·问题〕。注：H5: We identify two key limitations。 | 6/30 | 5 | 8/27 | 8/19 | 常用 | Limitations There are still several limitations in this paper.（H6） |
| `paradigm` | 范式〔名词·方法〕。注：house签名词。 | 6/28 | 5 | 6/12 | 4/18 | 常用 | ...to adaptively propose and evolve such criteria in the self-evolving training paradigm remains largely underexplored...（H6） |
| `objective` | 目标函数/目标〔名词·方法〕 | 6/26 | 5 | 7/21 | 7/31 | 常用 | ...a by-product of outcome-centric optimization objectives in large-scale reinforcement learning with verifiable rewards (RLVR) [CITE]...（H6） |
| `observation(s)` | 观察〔名词·结果〕 | 6/25 | 5 | 7/23 | 6/27 | 常用 | ...have the following observation: Self-proposed rubrics can provide meaningful CoT rewarding signals for RL...（H6） |
| `trend` | 趋势〔名词·结果〕 | 6/23 | 5 | 2/5 | 6/17 | 常用 | ...[M] Rub [M] as the Figure [REF] shows a decreasing trend during the X training...（H6） |
| `framework` | 框架〔名词·方法〕 | 6/22 | 5 | 8/34 | 8/49 | 常用 | ...application under this framework is to let the model simultaneously act as a reasoner...（H6） |
| `property / properties` | 性质〔名词·结果〕 | 6/13 | 5 | 5/13 | 7/29 | 常用 | ...describing desirable reasoning properties like "avoid tangential explorations") [CITE], rewarding CoTs by their rubric...（H6） |
| `finding(s)` | 发现〔名词·结果〕 | 5/55 | 4 | 8/25 | 5/9 | 常用 | ...inspiration from the findings that activations of benign and malicious prompts [CITE] are separable...（H4） |
| `strategy` | 策略〔名词·方法〕 | 5/40 | 4 | 7/31 | 6/86 | 常用 | ...reasoning trajectories receive identical rewards, allowing optimization to drift toward shortcut or brittle strategies [CITE]...（H6） |
| `module / component` | 模块/组件〔名词·方法〕 | 5/25 | 4 | 8/62 | 8/82 | 常用 | ...the role of components in X with the ablation study, including the training dynamics...（H5） |
| `phenomenon / phenomena` | 现象〔名词·结果〕 | 5/16 | 5 | 0/0 | 3/7 | 常用 | Such phenomena show the effectiveness of self-evolving in enhancing reasoning.（H6） |
| `technique` | 技术〔名词·方法〕 | 5/15 | 4 | 7/20 | 7/11 | 常用 | ...[CITE], with all roles optimized jointly (e.g. via techniques such as multi-agent reinforcement learning [CITE])...（H6） |
| `the success of X` | ……的成功〔名词·结果〕。注：We attribute the success of X to ... | 5/8 | 4 | 1/1 | 0/0 | 常用 | ...factor that determines the success of our method lies in whether the self-proposed and...（H6） |
| `issue(s)` | 问题〔名词·问题〕 | 4/16 | 3 | 6/27 | 4/17 | 常用 | To address these issues, we propose X, which incorporates two text-controlled gates for more stable...（H5） |
| `gain(s)` | 增益〔名词·结果〕 | 4/15 | 3 | 1/1 | 8/19 | 常用 | ...optimizing what LLMs answer to autonomously improving how LLMs think, potentially yielding "free-lunch" reasoning gains...（H6） |
| `perspective` | 视角〔名词·方法〕 | 4/11 | 3 | 3/6 | 7/23 | 常用 | To this end, we provide explanations for this phenomenon from the perspective of model activations.（H3） |
| `gap` | 空白/差距〔名词·问题〕 | 4/9 | 3 | 3/4 | 5/10 | 常用 | To fill this gap, we propose X (X),...（H6） |
| `insight` | 洞见〔名词·方法〕。注：H6: Based on this insight。 | 4/5 | 3 | 6/16 | 4/18 | 常用 | Based on this insight, X generally outperforms the naive RLVR (cf. Section [REF]).（H6） |
| `evidence` | 证据〔名词·结果〕。注：H5 的 58 次多为领域词（evidence chunk），作为写法用词只看 H3、H2。 | 3/65 | 2 | 6/13 | 4/7 | 常用 | As shown, X can identify the last evidence position and exit accordingly in most cases.（H5） |
| `challenge(s)` | 挑战〔名词·问题〕 | 3/9 | 2 | 7/13 | 7/24 | 常用 | These challenges motivate us to seek an autonomous CoT rewarding approach that requires no human...（H6） |
| `the role of X` | ……的作用〔名词·结果〕。注：H6: (i.e. the role of ...) | 3/3 | 2 | 2/2 | 3/5 | 常用 | ...we systematically analyze the role of components in X with the ablation study, including...（H5） |
| `the risk(s) of` | 风险〔名词·问题〕。注：H5: the risks of memory explosion。 | 2/8 | 1 | 0/0 | 1/1 | 偶用 | ...update gate reduces the risk of memory explosion by tracking the memory size dynamics...（H5） |
| `scheme` | 方案〔名词·方法〕 | 2/2 | 1 | 3/17 | 3/13 | 偶用 | ...can anticipate future tokens—such as planning rhyme schemes several lines ahead when composing poems [CITE]...（H3） |
| `trade-off` | 权衡〔名词·问题〕 | 1/11 | 1 | 1/8 | 4/21 | 偶用 | ...theoretical grounding, thus raising concerns about their robustness and generalizability in addressing the safety–utility trade-off...（H4） |
| `vulnerability` | 脆弱性〔名词·问题〕。注：H4。 | 1/3 | 1 | 0/0 | 4/6 | 偶用 | This vulnerability highlights the necessity for more principled approaches.（H4） |
| `drawback(s)` | 缺点〔名词·问题〕。注：H5: inherits several drawbacks of ... | 1/2 | 1 | 2/2 | 3/4 | 偶用 | However, the vanilla approach inherits several drawbacks of naive RNN-style memory updates [CITE].（H5） |
| `deficiency / obstacle / weakness` | 缺陷/障碍〔名词·问题〕。注：导师用 obstacles / deficiencies。 | 0/0 | 0 | 4/5 | 3/6 | 仅参考 | ...[M] enforces [M] to be acyclic, it becomes the main obstacle to the score-based scheme...（refB4） |
| `bottleneck` | 瓶颈〔名词·问题〕 | 0/0 | 0 | 1/1 | 1/1 | 仅参考 | ...will be a bottleneck when explaining large-scale graphs that involve massive edges like social...（refA8） |

## 词类：介词搭配

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `across (different/all) X` | 跨（多个设置）〔介词搭配〕。注：house高频：across different model sizes。 | 6/78 | 5 | 7/31 | 8/51 | 常用 | ...with X outperforms the vanilla RLVR across multiple datasets and multiple model sizes on average...（H6） |
| `based on X` | 基于〔介词搭配〕 | 6/50 | 5 | 8/36 | 8/54 | 常用 | We train models based on the DAPO-Math-17k dataset [CITE], which contains 17k high-quality math questions.（H6） |
| `through X` | 通过〔介词搭配〕 | 6/48 | 5 | 7/11 | 7/19 | 常用 | Through such RLVR training, the policy model [M] gradually learns to answer questions, which is...（H6） |
| `under (different/both) X` | 在……设置下〔介词搭配〕。注：H5: under both ... and ... | 6/39 | 5 | 5/10 | 5/7 | 常用 | Then, we discuss the multiple roles under the same policy model in Section [REF].（H6） |
| `via X` | 通过〔介词搭配〕 | 6/38 | 5 | 8/39 | 8/26 | 常用 | We propose X to reward CoTs via self-built, self-evolving rubrics without human annotation.（H6） |
| `beyond X` | 超出〔介词搭配〕 | 5/24 | 5 | 5/9 | 4/8 | 常用 | ...RLVR to move beyond outcome-centric optimization by rewarding CoTs with supervision criteria that are...（H6） |
| `according to X` | 根据〔介词搭配〕 | 5/11 | 4 | 0/0 | 5/18 | 常用 | ...is significantly smaller for benign prompts than for malicious ones according to our null-space constraints...（H4） |
| `similar to X` | 类似于〔介词搭配〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 5/8 | 4 | 2/4 | 4/6 | 常用 | ...one in-distribution task similar to the training data, while the remaining tasks are out-of-distribution...（H5） |
| `solely on / solely based on` | 仅基于〔介词搭配〕。注：H2。 | 5/8 | 4 | 1/1 | 2/2 | 常用 | ...rewards typically depend solely on the correctness of the predicted answer [M] : [EQ]...（H6） |
| `corresponding to X` | 对应于〔介词搭配〕 | 5/8 | 5 | 0/0 | 1/1 | 常用 | ...the same underlying policy model [M] instantiated under the prompt [M] corresponding to that role...（H6） |
| `compared to/with X` | 与……相比〔介词搭配〕 | 4/28 | 3 | 8/31 | 8/54 | 常用 | ...of the baselines compared to directly adding our refusal direction vector may be attributed...（H4） |
| `toward(s) X` | 朝向〔介词搭配〕 | 4/27 | 3 | 8/57 | 7/28 | 常用 | ...CoT supervision criteria to strengthen RL training, pointing toward more self-improving and self-evolving reasoning systems...（H6） |
| `ranging from X to Y` | 范围从……到……〔介词搭配〕 | 4/5 | 3 | 3/3 | 2/2 | 常用 | These tasks are constructed with different context lengths, ranging from 7K to 896K.（H5） |
| `regardless of X` | 无论〔介词搭配〕。注：refB 常用；附录里的命中全是模型输出或提示词，按house 0 计。层级由切片的 仅导师 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 2/2 | 6/15 | 常用 | Regardless of applications, we can roughly divide the existing platforms into information-oriented and social-oriented domains.（refA1） |
| `with the help of X` | 借助〔介词搭配〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/3 | 2 | 0/0 | 0/0 | 常用 | ...construct a refusal direction vector for steering malicious data, with the help of linear regression...（H4） |
| `independent of X` | 与……无关〔介词搭配〕。注：house的用例只在附录中。层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 4/5 | 2/2 | 常用 | ...[M] is of size [M] , independent of the potentially large sample size [M]...（H4） |
| `in addition to X` | 除……之外〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 3/3 | 3/3 | 常用 | In addition to classic baselines introduced in section [REF], we consider two categories of leading...（H2） |
| `conditioned on X` | 以……为条件〔介词搭配〕。注：层级由切片的 偶用 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 3/10 | 1/1 | 常用 | ...predicts the answer [M] conditioned on the final memory [M] and the question [M]...（H5） |
| `along with X` | 连同〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/5 | 1 | 2/2 | 3/4 | 偶用 | ...bipartite graph augmentation along with augmented views as positive signals (referred to as augmentation-based),...（H1） |
| `with respect to X` | 关于〔介词搭配〕。注：house的用例只在附录中。层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/5 | 1 | 0/0 | 2/5 | 偶用 | ...Then the gradient with respect to the positive representations [M] of item [M] is...（H1） |
| `consistent with X` | 与……一致〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/4 | 1 | 5/7 | 3/7 | 偶用 | ...effectively encoded user preference similarities, which is consistent with our previous findings in Section [REF]...（H2） |
| `from the perspective of X` | 从……角度〔介词搭配〕。注：H3。 | 2/3 | 1 | 2/5 | 3/7 | 偶用 | To this end, we provide explanations for this phenomenon from the perspective of model activations.（H3） |
| `relative to X` | 相对于〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/3 | 1 | 0/0 | 3/4 | 偶用 | ...refer to the increase and decrease of CF methods relative to InfoNCE in each metric...（H1） |
| `as well as` | 以及〔介词搭配〕。注：导师用，house 0。层级由切片的 仅导师 按统一规则改为 偶用（全区域重算）。 | 1/2 | 1 | 6/12 | 7/9 | 偶用 | A sharp increase as well as a higher area under the accuracy curve indicate a...（refB1） |
| `owing to X` | 由于〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 2/3 | 1/1 | 偶用 | ...effectiveness for recommendation [CITE], owing to the natural user-item graph structure in recommender systems [CITE]...（H2） |
| `in the presence of X` | 存在……时〔介词搭配〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 1/1 | 1 | 0/0 | 0/0 | 偶用 | ...and whether this encoding is robust in the presence of noise in the input prompts...（H2） |
| `in terms of X` | 在……方面〔介词搭配〕。注：house只在 H1 用；导师常用。 | 1/3 | 0 | 6/11 | 7/15 | 已弃用 | In terms of implementation, [M] is controlled by the number of adversarial training epochs (see...（H1） |
| `in line with X` | 与……一致〔介词搭配〕 | 1/1 | 0 | 0/0 | 1/2 | 已弃用 | Firstly, in line with [CITE], only a minor portion of negative instances are potentially important...（H1） |
| `w.r.t. X` | 关于（缩写）〔介词搭配〕。注：正文禁用；house只在 H1 用。 | 1/6 | 0 | 8/85 | 7/88 | 禁用 | ...there is limited understanding of contrastive loss in CF methods, especially w.r.t. its generalization ability...（H1） |

## 括号套式

| 表述 | 功能 | house（篇/次） | 近作篇数 | refA（篇/次） | refB（篇/次） | 状态 | 例句 |
|---|---|---|---|---|---|---|---|
| `X (\ie ...)` | 首次出现时括号解释〔括号框架〕。注：house每篇 4-26 次；保留这些解释。 | 6/106 | 5 | 8/78 | 8/55 | 常用 | We report pass@1 as the accuracy metric (i.e. the average pass rate among 16 responses).（H6） |
| `X (\eg ...)` | 括号举例〔括号框架〕 | 6/79 | 5 | 8/67 | 7/50 | 常用 | ...self-propose supervision criteria (e.g. rubrics) for assessing CoT quality and to self-evolve as training...（H6） |
| `(RQ1) / (Section X) run-in tags` | RQ 标签〔括号框架〕 | 5/16 | 4 | 7/21 | 7/23 | 常用 | (RQ2) Effectiveness of X Based on the reliability of self-proposed rubrics, we further test the...（H6） |
| `X -- a/that is ... -- Y (dash aside)` | 破折号插入语〔括号框架〕。注：house源码写 --（prose.md 要求 ' --- '，语料抽取后为 --） | 5/12 | 5 | 7/69 | 7/30 | 常用 | ...two text-controlled gates -- an update gate (UG) and an exit gate (EG) --...（H5） |
| `... (\cf Section X)` | 贡献列表末尾指向对应小节〔括号框架〕。注：house intro 贡献/发现列表的收尾（H4、H5、H6） | 4/12 | 3 | 7/29 | 6/36 | 常用 | Additionally, introducing self-evolving training for the rubricator further improves the reasoning performance (cf. Section [REF]).（H6） |
| `namely X` | 即〔括号框架〕 | 3/3 | 3 | 3/3 | 3/4 | 常用 | ...SuperGPQA, we evaluate on three subsets, each including 100 questions, namely SuperGPQA-Eng, SuperGPQA-Med, and SuperGPQA-Sci...（H6） |
| `(i) ... and (ii) ...` | 罗马数字并列〔括号框架〕。注：H5: decide (i) whether ... and (ii) whether ... | 2/5 | 2 | 0/0 | 0/0 | 常用 | ...quality, which can depend on: (i) the CoT [M] and (ii) the answer [M]...（H6） |
| `(termed X) / a ... termed X` | 给概念命名〔括号框架〕。注：house每篇 0-2 次，只给核心概念； | 2/3 | 2 | 6/14 | 5/6 | 常用 | ...of refusal behavior (termed safety enhancement); while for benign prompts, minimal shifts are essential...（H4） |
| `..., \eg ... (unbracketed)` | 句中不带括号的 \eg〔括号框架〕。注：附录里的命中全是模型输出或提示词，按house 0 计。层级由切片的 仅导师 按统一规则改为 常用（全区域重算）。 | 2/2 | 2 | 3/7 | 2/3 | 常用 | ...applies fixed distribution to sample from missing data, e.g. uniform [CITE] and popularity-biased distribution [CITE]...（refA5） |
| `..., \ie ... (unbracketed)` | 句中不带括号的 \ie〔括号框架〕 | 2/6 | 1 | 8/24 | 7/27 | 偶用 | ...null space [CITE] to preserve utility -- i.e. the steered activations remain unchanged: [M]...（H4） |
| `we term X / which we term` | 给概念命名〔括号框架〕。注：层级由切片的 已弃用 按统一规则改为 偶用（全区域重算）。 | 2/3 | 1 | 3/4 | 0/0 | 偶用 | We term this model X for its simplicity and a series of good properties.（H2） |

## 仅参考（refA/refB 常用、house 不用）

状态为“仅参考”的全部条目，按 refA+refB 使用篇数排序。写作时优先找 house 自己的等价写法（多数条目的备注里给了替代）。

| 表述 | 分区 | refA（篇/次） | refB（篇/次） | 备注 |
|---|---|---|---|---|
| `X achieves the best performance / relative improvement of N%` | M7 报告结果 | 8/10 | 6/11 | house不用 "achieves the best performance"；house用 "outperforms ... on most of the tasks"。 |
| `justify` | 词类：动词 | 7/12 | 6/12 | — |
| `In summary, the main contributions of this work are as follows:` | M4 提出方法 | 8/8 | 4/6 | — |
| `The contributions of this work are summarized as follows: / In a nutshell, this work makes the following main contributions:` | M4 提出方法 | 8/8 | 4/4 | refA 8/8、refB 4/8；house 0 次（6/6 篇终稿都不写贡献列表，改为证据路线图）。 |
| `We conduct experiments on three benchmark datasets: A, B, and C.` | 实验设置 | 8/19 | 4/6 | house 0 次; refA 8/8 篇。 |
| `Distinct from X, we ...` | 相关工作 | 6/12 | 5/6 | — |
| `X is insufficient to Y` | M2 缺口与局限 | 7/24 | 2/2 | refA 用。 |
| `Analogously, ...` | M5 机制与公式 | 5/10 | 4/8 | 导师专用（refA 5 篇、refB 4 篇），house 0。 |
| `the strongest baselines / strong baselines` | 实验设置 | 6/12 | 3/9 | house 0 次; 导师 9 篇; house改用 the vanilla X 或具体方法名。 |
| `Jointly analyzing Table X and Y, we ...` | M8 分析与归因 | 7/12 | 2/2 | refA 签名式；house不用。 |
| `X is tuned in {a, b, c}` | 实验设置 | 6/8 | 2/2 | house 0 次; refA 6 篇, refB 2 篇; house用 we search X in [a, b]。 |
| `This verifies / which verifies X` | M8 分析与归因 | 5/6 | 3/5 | house不用；house用 "This validates" 或 "confirming"。 |
| `X is of crucial / critical importance to Y` | M1 背景与重要性 | 5/7 | 2/3 | 导师套话，house不用。 |
| `The statistics of the datasets are summarized in Table \ref{}.` | 实验设置 | 7/9 | 0/0 | refA 8/8 篇; house 0 次 (house只写 dataset statistics) |
| `we search X in the range of {..}` | M9 消融与敏感性 | 5/6 | 2/2 | house不用；house直接写 "from a to b"。 |
| `Our work differs from them in that ...` | 相关工作 | 3/4 | 4/4 | house没有这个句式，相关工作末段用 To address these gaps, we ... 或 In this work, we take an important step further, by ... |
| `deficiency / obstacle / weakness` | 词类：名词 | 4/5 | 3/6 | 导师用 obstacles / deficiencies。 |
| `At the core (of X) is Y` | M1 背景与重要性 | 2/4 | 4/5 | refB 标志句式。 |
| `is prone to / susceptible to` | M2 缺口与局限 | 1/2 | 5/18 | refB 用。 |
| `Despite (the) great success, ...` | M2 缺口与局限 | 1/1 | 5/7 | — |
| `More formally, ...` | M5 机制与公式 | 3/9 | 3/4 | 导师专用（refA 3 篇、refB 3 篇），house 0。 |
| `X is represented as [M]` | M5 机制与公式 | 4/4 | 2/3 | 导师专用（refA 4 篇、refB 2 篇），house 0。 |
| `Future work includes / A worthwhile direction for future work is / An interesting avenue for future work` | M10 结论、局限与展望 | 2/2 | 4/4 | — |
| `However, there exist two deficiencies: (1) ...; (2) ...` | M2 缺口与局限 | 3/4 | 2/4 | refB refB8/RobustCF。 |
| `existing efforts have not fully explored X` | M2 缺口与局限 | 4/6 | 1/1 | refA refA2、X。 |
| `To the best of our knowledge, we are the first to V` | M4 提出方法 | 3/5 | 2/2 | house 0 次。 |
| `iteratively` | M5 机制与公式 | 2/6 | 3/9 | 导师专用（refA 2 篇、refB 3 篇），house 0。 |
| `..., which consists of two components` | M5 机制与公式 | 2/3 | 3/6 | — |
| `Assuming X, Y ...` | M6 理论与分析 | 1/1 | 4/6 | house 0；导师 refA/refB 多篇。house用 "Under X," 或 "Let us assume that"。 |
| `Parameter Settings  [heading]` | 实验设置 | 4/6 | 1/2 | house 0 次; refA 4 篇。 |
| `A grid search is conducted to confirm the optimal settings for each method` | 实验设置 | 5/6 | 0/0 | house 0 次; refA 5 篇。 |
| `We report the average metrics for all users in the test set.` | 实验设置 | 5/5 | 0/0 | house 0 次; refA 5 篇。 |
| `In what follows, we ...` | 章节开头与路线图 | 4/7 | 1/1 | — |
| `In future work, we will / In future, we will ...` | M10 结论、局限与展望 | 5/5 | 0/0 | — |
| `In summary, / To sum up, / To summarize, ...` | 连接词（按功能） | 3/5 | 2/4 | — |
| `X are still far from being able to ...` | M2 缺口与局限 | 0/0 | 4/4 | — |
| `a new method named X` | M4 提出方法 | 4/9 | 0/0 | house 0 次；refA 4/8 篇。 |
| `we develop a new model / solution, X` | M4 提出方法 | 4/4 | 0/0 | — |
| `we minimize X` | M5 机制与公式 | 1/2 | 3/4 | 导师专用（refB 3 篇），house 0；house说 by minimizing the following ... loss。 |
| `X is composed of Y` | M5 机制与公式 | 2/4 | 2/2 | 导师专用（refA 2 篇、refB 2 篇），house 0。 |
| `Dataset Description  [heading]` | 实验设置 | 4/4 | 0/0 | house 0 次; refA 4 篇。 |
| `The model parameters are initialized with the Xavier initializer` | 实验设置 | 4/4 | 0/0 | house 0 次; refA 4 篇。 |
| `More specifically, ...` | 连接词（按功能） | 2/3 | 2/3 | — |
| `Nonetheless, ...` | 连接词（按功能） | 1/3 | 3/3 | 导师用，house不用。 |
| `a dominant paradigm / line / scheme` | M1 背景与重要性 | 0/0 | 3/4 | refB 用。 |
| `has attracted increasing attention` | M1 背景与重要性 | 2/3 | 1/1 | 导师开场套话，house不用。 |
| `has shown great potential in X` | M1 背景与重要性 | 3/3 | 0/0 | refA 开场套话；house改用 shows potential in。 |
| `current methods fall short in X` | M2 缺口与局限 | 1/1 | 2/3 | refA refA7、refB refB6。 |
| `The key reason is that X` | M2 缺口与局限 | 2/2 | 1/2 | refA refA3、refB refB1。 |
| `X is non-trivial` | M2 缺口与局限 | 1/1 | 2/3 | refB 用。 |
| `However, each type of method suffers from some (inherent) limitations:` | M2 缺口与局限 | 2/2 | 1/1 | refA refA5、refB refB1。 |
| `which derails X` | M2 缺口与局限 | 0/0 | 3/3 | refB 用。 |
| `with parameters [M]` | M5 机制与公式 | 2/3 | 1/1 | 导师专用（refA 2 篇、refB 1 篇），house 0；house用 parameterized by。 |
| `two key components` | M5 机制与公式 | 3/3 | 0/0 | 导师专用（refA 3 篇），house 0。 |
| `This work represents an initial attempt to ... and opens up new research possibilities.` | M10 结论、局限与展望 | 3/4 | 0/0 | — |
| `In this section, we review two research lines of related works: A and B. / This work is relevant to two research lines: ...` | 相关工作 | 2/2 | 1/1 | house预备知识节用 we briefly review。 |
| `On the contrary, ...` | 连接词（按功能） | 1/1 | 2/2 | — |
| `X is a keystone / cornerstone of Y` | M1 背景与重要性 | 0/0 | 2/4 | refB 用词，house不用。 |
| `X is still far from well-studied / has rarely been studied` | M2 缺口与局限 | 1/2 | 1/1 | refB refB7、refA refA1。 |
| `Such intuition motivates us to ...` | M3 动机与研究问题 | 2/2 | 0/0 | refA refA4、refA7；house 0 次。 |
| `X should satisfy two requirements: 1) ..., and 2) ...` | M3 动机与研究问题 | 1/1 | 1/1 | house 0 次；导师各 1 篇。 |
| `we use [M] to denote X` | M5 机制与公式 | 2/2 | 0/0 | 导师专用（refA 2 篇），house 0。 |
| `where [M] and [M] denote ...` | M5 机制与公式 | 2/2 | 0/0 | 导师 refA 2 篇；house 0，house用 where [M] and [M] are。 |
| `X is formalized as: [EQ]` | M5 机制与公式 | 1/1 | 1/1 | 导师各 1 篇，house 0。 |
| `See detailed proof in Appendix [REF]. / See Appendix [REF] for the detailed proof.` | M6 理论与分析 | 0/0 | 2/3 | house 0；refB refB2、refB4。 |
| `X hardly holds / is easily violated in real-world scenarios` | M6 理论与分析 | 0/0 | 2/3 | house 0；refB refB3、refB4。 |
| `we theoretically reveal that X` | M6 理论与分析 | 0/0 | 2/2 | house 0；refB 2 篇。house偏好 demonstrate / show。 |
| `X without any assumption(s) on the test data` | M6 理论与分析 | 0/0 | 2/2 | house 0；refB refB3/refB8。 |
| `The embedding size is fixed to 64 for all models` | 实验设置 | 2/2 | 0/0 | house 0 次; refA 2 篇。 |
| `The limitations of X ... will be addressed in future work / will be covered in subsequent works` | M10 结论、局限与展望 | 0/0 | 2/3 | — |
| `Accordingly, ...` | 连接词（按功能） | 0/0 | 2/3 | — |
| `Remarkably, ...` | 连接词（按功能） | 0/0 | 2/3 | — |
| `By doing so, / In doing so, ...` | 连接词（按功能） | 2/2 | 0/0 | — |
| `Importantly, ...` | 连接词（按功能） | 0/0 | 2/2 | — |
| `bottleneck` | 词类：名词 | 1/1 | 1/1 | — |

## 已弃用 / 禁用

### 禁用（在禁用 / 弃用清单上，不看次数）

| 表述 | 分区 | house（篇/次） | 备注 |
|---|---|---|---|
| `X sheds light on Y` | M4 提出方法 | 2/5 | 禁用。 |
| `we delve into X` | M4 提出方法 | 1/3 | 禁用词；仅 H1。 |
| `We believe (that) X ... will inspire / is an important stepping stone` | M4 提出方法 | 1/2 | 禁用。 |
| `which de facto concurrently ...` | M4 提出方法 | 1/1 | 禁用。 |
| `the reasons underpinning X` | M4 提出方法 | 1/1 | 禁用词。 |
| `unleash the power of X` | M4 提出方法 | 1/1 | 禁用。 |
| `Wherein ...` | M5 机制与公式 | 1/2 | 禁用词；导师 refA 5 篇、refB 5 篇用，house只 H1。 |
| `As elucidated in Theorem [REF], X` | M6 理论与分析 | 1/1 | 禁用（elucidate）。改 "As shown in Theorem [REF]" 或 "By Theorem [REF]"。 |
| `This furnishes a potential theoretical rationalization for X` | M6 理论与分析 | 1/1 | 禁用。H1 1 次。 |
| `demonstrate(s) the rationality and effectiveness of X` | M8 分析与归因 | 0/0 | 在禁用/弃用清单上。 |
| `Best viewed in color.` | 图表指代 | 0/0 | house 0; 导师 4 篇。 |
| `Furthermore, ...` | 连接词（按功能） | 3/5 | house罕用，规则列为回避。 |
| `Thus, ...` | 连接词（按功能） | 3/3 | 句首 Thus 已禁用；house只用句中 ', thus V-ing'；house的用例只在附录中。 |
| `Notably, ...` | 连接词（按功能） | 2/4 | house罕用，规则列为回避。 |
| `Surprisingly, ...` | 连接词（按功能） | 2/4 | 已禁用。 |
| `To be specific, ...` | 连接词（按功能） | 2/3 | H1、H2 用过，已禁用。 |
| `Worse still, ...` | 连接词（按功能） | 2/2 | refB 常用；house在 H1、H2 用过，已禁用。 |
| `Clearly, / Obviously, ...` | 连接词（按功能） | 1/2 | 已禁用。 |
| `Hence, ...` | 连接词（按功能） | 0/0 | 导师专用，house从不句首用；已禁用。 |
| `Towards this end, ...` | 连接词（按功能） | 0/0 | refB 专用，已禁用。 |
| `As such, ...` | 连接词（按功能） | 0/0 | 已禁用。 |
| `In a nutshell, ...` | 连接词（按功能） | 0/0 | refA 用，已禁用。 |
| `Technically, ...` | 连接词（按功能） | 0/0 | refA 专用，已禁用。 |
| `One possible reason is that ...` | 词类：动词 | 0/0 | refA 专用，已禁用。 |
| `We ascribe ... to ...` | 词类：动词 | 0/0 | 导师专用，已禁用。 |
| `state-of-the-art / SOTA` | 词类：形容词 | 1/2 | 2025 后禁用。 |
| `w.r.t. X` | 词类：介词搭配 | 1/6 | 正文禁用；house只在 H1 用。 |

### 已弃用（只在 H1 中出现，近作不再用）

| 表述 | 分区 | house（篇/次） | refA（篇/次） | refB（篇/次） | 备注 |
|---|---|---|---|---|---|
| `prevalent (solution / technique)` | M1 背景与重要性 | 1/3 | 4/9 | 7/13 | 导师主力；house仅 H1。 |
| `a surge of interest in X` | M1 背景与重要性 | 1/1 | 0/0 | 2/2 | H1 早期用。 |
| `Despite the (empirical) success of X, ...` | M2 缺口与局限 | 1/3 | 2/2 | 5/7 | H1；refB 常用 'Despite great success'。 |
| `Yet, ...` | M2 缺口与局限 | 1/1 | 0/0 | 3/3 | H1 摘要；2025 年后不用。 |
| `there is limited understanding of X` | M2 缺口与局限 | 1/1 | 0/0 | 0/0 | H1 摘要（与 Worse still 连用） |
| `these loss functions share a common limitation that ...` | M2 缺口与局限 | 1/1 | 0/0 | 0/0 | H1 预备节。 |
| `X remains limited` | M2 缺口与局限 | 1/1 | 0/0 | 0/0 | H1 结论。 |
| `X necessitate(s) Y to focus more on Z` | M3 动机与研究问题 | 1/1 | 0/0 | 1/1 | 仅 H1 摘要；2025 后不用。 |
| `we advocate (for) its adoption as a standard X` | M4 提出方法 | 1/3 | 0/0 | 3/3 | 仅 H1；refB 3 篇。2025 后不用。 |
| `specially tailored for X` | M4 提出方法 | 1/3 | 0/0 | 0/0 | 仅 H1。 |
| `we frame X as Y` | M4 提出方法 | 1/1 | 2/4 | 1/1 | 仅 H1。 |
| `The basic idea is to V` | M4 提出方法 | 1/1 | 2/5 | 0/0 | house只 H1；refA refA8、refA5 常用。 |
| `X, coined Y` | M4 提出方法 | 1/1 | 0/0 | 1/1 | 仅 H1；refB refB4 1。 |
| `denoted by [M]` | M5 机制与公式 | 1/2 | 1/1 | 3/4 | 只在 H1；后期改用 denoted as。 |
| `if and only if` | M5 机制与公式 | 1/1 | 2/2 | 2/2 | 只 H1。 |
| `Let [M] denote ...` | M5 机制与公式 | 1/1 | 0/0 | 2/2 | 只 H1；导师 refB 2 篇。 |
| `Without loss of generality, ...` | M5 机制与公式 | 1/1 | 1/1 | 1/1 | 只 H1。 |
| `[M] stands for ...` | M5 机制与公式 | 1/1 | 1/1 | 0/0 | 只在 H1。 |
| `Here, with a slight abuse of notation, let [M] denote ...` | M5 机制与公式 | 1/1 | 0/0 | 1/1 | 只 H1（refB refB4 1）。 |
| `the theoretical understanding of X (remains limited / untouched)` | M6 理论与分析 | 1/6 | 0/0 | 0/0 | 只见于 H1（6 次）；2025-26 未再用，改用 lack theoretical grounding。 |
| `X holds when / holds by / holds under Y` | M6 理论与分析 | 1/4 | 0/0 | 1/1 | H1 4、H4 1。 |
| `In this section, we study it through the lens of X` | M6 理论与分析 | 1/3 | 0/0 | 1/1 | H1 3 次；2025-26 未用。 |
| `In-depth Analysis of X (小节标题)` | M6 理论与分析 | 1/2 | 3/4 | 3/3 | H1 小节标题；refA/refB 各 3 篇也用（多为实验分析） |
| `Grounded by theoretical proof, X ...` | M6 理论与分析 | 1/2 | 0/0 | 2/2 | H1 2 次 + refB refB2/refB4 各 1；导师共享模板，但house 2025-26 未用。 |
| `In this definition / In this formulation, [M] is X` | M6 理论与分析 | 1/2 | 0/0 | 0/0 | H1 附录 2 次。 |
| `Then, optimizing X is equivalent to solving Y` | M6 理论与分析 | 1/2 | 0/0 | 0/0 | H1 定理与附录 2 次。 |
| `Therefore, by Eq [REF] and Eq [REF], [EQ]` | M6 理论与分析 | 1/2 | 0/0 | 0/0 | H1 附录 2 次；Therefore 句首本身 6 篇全用（26 次），是house替代 Hence/Thus 的首选。 |
| `We briefly outline the main steps of derivation (see Appendix [REF] for the complete derivation):` | M6 理论与分析 | 1/2 | 0/0 | 0/0 | H1 2 次。 |
| `We denote X as [M]` | M6 理论与分析 | 1/1 | 2/4 | 3/6 | H1 1；导师 5 篇。 |
| `In essence, by X, Y effectively Z.` | M6 理论与分析 | 1/1 | 0/0 | 3/3 | H1 1；refB 3 篇。 |
| `X is a special case of Y, when [M]` | M6 理论与分析 | 1/1 | 2/3 | 0/0 | H1 1；refA refA3/refA6。 |
| `Suppose that there are X, let them be [M] without loss of generality.` | M6 理论与分析 | 1/1 | 1/1 | 1/1 | H1 1 + refA refA8 + refB refB4；house句末用法。 |
| `These results validate X as demonstrated in Theorem [REF], proving that Y` | M6 理论与分析 | 1/1 | 1/1 | 1/1 | H1 1；refA refA8、refB refB5 各 1。 |
| `To some extent, X is equivalent to Y.` | M6 理论与分析 | 1/1 | 1/1 | 1/1 | H1 1。 |
| `By defining [M], we have [M].` | M6 理论与分析 | 1/1 | 0/0 | 1/1 | H1 1 次（证明梗概） |
| `the equality holds if and only if X` | M6 理论与分析 | 1/1 | 0/0 | 1/1 | H1 1 + refB refB4 1。 |
| `X can be obtained as follows: [EQ]` | M6 理论与分析 | 1/1 | 0/0 | 1/1 | H1 1 + refB refB5。 |
| `..., which shows that X` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1。 |
| `a common assumption underlying these methods is X` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次，且句首为禁用的 Wherein；改为 "A common assumption underlying these methods is ..."。 |
| `Consequently, the right-hand side of Equation [REF] can be derived by X` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1。 |
| `From this perspective, X focuses on Y` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次。 |
| `In terms of implementation, [M] is controlled by X` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次；2025-26 用 In practice。 |
| `Let us assume that for [M], we have access to X` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 方法 1 次（oracle hardness），随后用 "However, X is not accessible ..." 放松。 |
| `The analysis begins with X, primarily due to its simplicity` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 附录梯度分析开头。 |
| `The last inequality is satisfied by [M].` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1。 |
| `we theoretically demonstrate (through X) that Y` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次；refB 用 "we theoretically reveal"（refB2, refB4） |
| `We would like to highlight the straightforward nature of this derivation, which ...` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1。 |
| `which is achieved without the need for assumptions` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次。 |
| `X can be derived by factoring out Y and reorganizing the term` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1。 |
| `X can be restated as a standard Y problem: [EQ]` | M6 理论与分析 | 1/1 | 0/0 | 0/0 | H1 1 次（"can be restated as a standard DRO problem"）； |
| `Experimental Settings  [heading]` | 实验设置 | 1/2 | 7/7 | 6/7 | 仅 H1; 导师 13/16 篇; house 2025-26 改为 Experimental Setup。 |
| `We employ three commonly used metrics (A, B, C) for evaluation, with K set by default at 20.` | 实验设置 | 1/2 | 2/3 | 3/3 | 仅 H1; 导师 5 篇。 |
| `For a fair comparison, we ...` | 实验设置 | 1/1 | 6/7 | 6/6 | house仅 H1; 导师 12/16 篇。 |
| `Two high-performing encoders ... are selected as CF backbone models.` | 实验设置 | 1/1 | 1/1 | 1/1 | 仅 H1。 |
| `within the range provided by the corresponding references` | 实验设置 | 1/1 | 0/0 | 0/0 | 仅 H1。 |
| `Consistent observations can be found on X in Appendix \ref{}.` | M7 报告结果 | 1/1 | 0/0 | 0/0 | 只见于 H1。 |
| `hyper-parameter study / sensitivity analysis` | M9 消融与敏感性 | 1/1 | 0/0 | 3/5 | — |
| `Despite the empirical success(es), the ... understanding of X remains limited.` | M10 结论、局限与展望 | 1/2 | 0/0 | 0/0 | — |
| `provides a valuable baseline for future X studies and is an important stepping stone toward ...` | M10 结论、局限与展望 | 1/2 | 0/0 | 0/0 | — |
| `In this work / paper, we proposed X, ...` | M10 结论、局限与展望 | 1/1 | 0/0 | 5/5 | — |
| `In this work, we devised a principled X ...` | M10 结论、局限与展望 | 1/1 | 0/0 | 1/1 | — |
| `..., which aligns with the limitation of X.` | M10 结论、局限与展望 | 1/1 | 0/0 | 0/0 | — |
| `X \cite{} takes a different approach and ... / takes it a step further` | 相关工作 | 1/2 | 0/0 | 0/0 | — |
| `Our work is related to the literature on A and B.` | 相关工作 | 1/1 | 0/0 | 0/0 | — |
| `Several studies like A \cite{} and B \cite{} elaborate on ...` | 相关工作 | 1/1 | 0/0 | 0/0 | — |
| `We remind important related works to understand how X stands ...` | 相关工作 | 1/1 | 0/0 | 0/0 | — |
| `Unfortunately, ...` | 连接词（按功能） | 1/1 | 0/0 | 2/2 | — |
| `devise` | 词类：动词 | 1/6 | 7/22 | 6/14 | H1 后少用。 |
| `ideal / an ideal X should` | 词类：形容词 | 1/1 | 1/1 | 2/4 | refB: an ideal X should ... |
| `in terms of X` | 词类：介词搭配 | 1/3 | 6/11 | 7/15 | house只在 H1 用；导师常用。 |
| `in line with X` | 词类：介词搭配 | 1/1 | 0/0 | 1/2 | — |
