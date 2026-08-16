# Cordis / Spatiotemporal Composability vs Pi Harness

## 核心定位

| 维度 | Cordis 路线 | Pi Harness 路线 |
|---|---|---|
| 核心目标 | 动态组合的语义保证 | 极简、可扩展、可用的 Coding Harness |
| 核心抽象 | Context / Effect / Coeffect / Component | Tools / Skills / Extensions / Packages |
| 生命周期 | 强调可逆、副作用跟踪 | 主要由扩展工程实现自行负责 |
| 依赖 | Reactive Coeffect | 更偏显式扩展与包机制 |
| Core | Meta-framework | Minimal product core |
| 自修改潜力 | 高，理论上适合动态自演化 | 高扩展性，但生命周期语义较弱 |
| 工程复杂度 | 高 | 低到中 |
| 当前落地性 | 偏底层与研究 | 强 |

## 初步结论

- **Pi 更像“可直接使用的 Harness 产品哲学”**。
- **Cordis 更像“动态 Harness 应该如何安全生老病死的底层 Runtime 理论”**。
- 最值得实践的是混合设计：**Pi-style Minimal Core + Cordis-style Lifecycle / Dependency Semantics**。

## 形式化分析后的修正

- Cordis 的优势不是功能更多，而是把 disposer ownership、provider identity、dependent drain 和 failure rollback 纳入一套生命周期语义。
- Cordis 的定理不能直接移植成 Pi 的保证；必须先约束所有共享 effect 经过 Context，并验证 inverse、independence、acyclicity 与 provision totality。
- Pi 风格开放扩展若保留 ambient filesystem/process/network access，就超出 Cordis Context boundary；这部分仍需 permission、sandbox 和 compensation。
- 更现实的融合路径是先引入 EffectScope 和显式 Capability Graph，再逐步加入 target/committed view 与 reconciliation，而不是一次性复制 Cordis loader。

更广泛的方案比较见 [related-systems.md](related-systems.md)。
