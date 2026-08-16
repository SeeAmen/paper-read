# 开放问题

| ID | 问题 | 来源位置 | 状态 | 当前答案/证据 | 下一步 |
|---|---|---|---|---|---|
| Q-001 | Effect 的独立性条件在真实插件系统中如何判定？ | Definitions 19/39/60 | 部分回答 | 【论文原文支持】不同 key 的 operations 天然独立；同 key 由 provider interface 证明 commutativity。Cordis 未给出一般自动检查器。 | 用同 key ordered middleware 构造负例，并探索静态声明或保守串行化。 |
| Q-002 | Reactive Coeffect 在依赖频繁波动时如何避免级联抖动？ | Theorems 64/66，Algorithm 5 | 部分回答 | 【论文原文支持】inertia 保证 transition 完成后再响应新 target，有限变化下会 quiesce；论文没有 debounce、rate limit 或无限抖动策略。 | 压测 target 高频翻转，测 reload amplification 和 tail latency。 |
| Q-003 | 可组合性保证能覆盖权限撤销和外部不可逆操作吗？ | Sections 6.1/6.3 | 已回答 | 【论文原文支持】Context capability 可做访问控制但不替代 sandbox；外部 emission 不在精确恢复内，只能 withhold 或 compensate。 | 在落地方案中分别建模 capability、sandbox、outbox 和 compensation。 |
| Q-004 | Inverse 自身失败时，其他 disposer 是否继续执行？ | Algorithm 1 | 待验证 | 【待验证】伪代码直接调用 composite recover，未定义异常隔离、重试和聚合错误。 | 实现 fault-injection，比较 fail-fast 与 best-effort cleanup。 |
| Q-005 | Cordis v4 是否真正满足论文中的 confinement 与 guard 语义？ | Section 5 | 待验证 | 【论文原文支持】Table 2 给出映射，但 Koishi 当前使用 v3，论文没有源码级证明。 | 对 v4 源码做逐算法审计并运行 lifecycle trace tests。 |
| Q-006 | HMR 的“事务性”是否覆盖外部 I/O？ | Algorithm 10 / Section 6.1 | 已回答 | 【论文原文支持】只覆盖 module cache 与 Context 内可逆 effects；外部 emission 不自动回滚。 | 加入文件写入/网络发送负例，验证边界文档是否足够清楚。 |
| Q-007 | 大规模 Fiber 下 notify 与 reconciliation 的成本是多少？ | Algorithms 3/5 | 待验证 | 【待验证】论文没有性能数据，Algorithm 3 表面上扫描 live fibers。 | 10–5000 Fibers 分层压测，并统计 dependency density 影响。 |
| Q-008 | Key identity 如何处理跨包版本与行为契约？ | Section 6.6 | 开放问题 | 【论文原文支持】当前使用 npm peer dependency；namespacing、structural compatibility 仍未统一。 | 设计 namespace + schema hash + semver range 的 capability descriptor。 |
