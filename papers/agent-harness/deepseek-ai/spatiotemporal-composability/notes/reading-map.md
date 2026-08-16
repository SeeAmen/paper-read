# 阅读地图

## 论文主线

```text
现实问题：插件 / 自演化 Agent 需要动态组合
        ↓
两个维度
├─ 时间：副作用如何完整回滚
└─ 空间：依赖如何动态解析
        ↓
理论来源
├─ Effect
└─ Coeffect
        ↓
运行时化
├─ Revertible Effect
└─ Reactive Coeffect
        ↓
Unified Context
        ↓
Component + Dynamic Composition Calculus
        ↓
Cordis Runtime
        ↓
插件系统 / Agent Harness / HMR
```

## 阅读重点

1. 第 1 章：作者为什么认为传统重启/容器粒度不够。
2. 第 2 章：只需要理解 effect/coeffect 的方向，不要求先掌握范畴论。
3. 第 3 章：全文核心，重点读 inverse、tracking、dependency notification。
4. 第 4 章：重点看作者究竟想证明哪些运行时性质，而不是死抠每一步证明。
5. 第 5 章：把形式化模型映射回 Cordis 工程实现。

## 结论地图

```text
Local temporal
  witnessed inverse + LIFO accumulator
        ↓ 加上 pairwise independence
Global temporal
  recovery exactness across interleaved fibers

Local spatial
  specification + notify
        ↓ 加上 committed view + withdrawal guard
Global spatial
  provider-before-consumer + coherent resolution

Global system
  acyclic precedence + finite/bounded fibers + total provision + no failure
        ↓
  progress + canonical/confluent quiescent state
```

完整结论和限制见 [完整分析](full-analysis.md)，定理前提见 [形式化模型拆解](formal-model.md)。
