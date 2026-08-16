# A Programming Paradigm for Spatiotemporal Composability

## 元信息

- 作者：Yifan Shi, Wei Zhang, Tianyi Cui
- 机构：Peking University / DeepSeek-AI
- 当前版本：Draft of August 13, 2026
- 页数：88
- 主题：Dynamic Composition / Revertible Effects / Reactive Coeffects / Cordis / Agent Harness
- 阅读状态：已通读
- 最近更新：2026-08-16

## 一句话理解

这篇论文试图回答：**一个运行中的软件系统，怎样让组件可以动态加入、删除和替换，同时保证它留下的副作用能被完整回滚、它与其他组件之间的依赖也能自动重新协调。**

## 核心判断

- 论文把动态组件安全拆成 Revertible Effect 与 Reactive Coeffect 两个维度，并通过 Fiber lifecycle calculus 将局部保证提升为全系统保证。
- 全局结论依赖 witnessed inverse、effect independence、无环依赖、有限 Fiber、provision totality 等前提；Cordis 运行时不会自动验证全部前提。
- Context 内 acquisition 可结构化回滚；已经越过边界的 emission 仍需延迟提交或业务补偿。
- Koishi 案例证明模型可落地，但不是 Cordis v4 的量化或对照实验；self-evolving Agent Harness 仍是未来验证方向。

## 阅读状态

- [x] 标题与摘要
- [x] 第 1 章 Introduction
- [x] 第 2 章 Preliminaries
- [x] 第 3 章 Revertible Effects and Reactive Coeffects
- [x] 第 4 章 A Calculus of Dynamic Composition
- [x] 第 5 章 Implementation and Case Study
- [x] 第 6 章 Discussion
- [x] 第 7 章 Related Work
- [x] 第 8 章 Conclusion
- [x] 形式化模型与关键前提拆解
- [x] 实现、证据、工程落地和横向比较
- [ ] 运行实验与源码级验证

## 导航

- [原文来源、版本与本地文件](source/SOURCE.md)
- [摘要翻译与解读](translation/00-title-and-abstract.md)
- [翻译索引](translation/README.md)
- [本文术语表](terminology/glossary.md)
- [阅读地图](notes/reading-map.md)
- [完整分析](notes/full-analysis.md)
- [形式化模型拆解](notes/formal-model.md)
- [实现与证据分析](notes/implementation-and-evidence.md)
- [开放问题](notes/questions.md)
- [实验计划](experiments/README.md)
- [完整验证计划](experiments/validation-plan.md)
- [工程实践](practice/README.md)
- [Agent Harness 落地蓝图](practice/agent-harness-blueprint.md)
- [与 Pi Harness 比较](comparisons/pi-harness.md)
- [与相关系统比较](comparisons/related-systems.md)
