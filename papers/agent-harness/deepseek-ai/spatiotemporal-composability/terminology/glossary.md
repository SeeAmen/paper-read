# 本文术语表

| Term | 中文 | 正式含义 | 大白话 |
|---|---|---|---|
| Composition | 组合 | 从简单组成部分构造复杂系统 | 把模块拼起来 |
| Dynamic Composition | 动态组合 | 运行期间加载、卸载、重配置组件 | 系统不停机也能装/删插件 |
| Temporal Composability | 时间可组合性 | 组件移除时，其环境修改可安全完整撤销 | 插件删掉后像从没来过 |
| Spatial Composability | 空间可组合性 | 组件依赖可声明、发现并随环境变化重新解析 | 依赖谁、谁挂了，都能自动知道 |
| Effect | 效应 | 计算对环境产生的修改 | 我改变了世界什么 |
| Coeffect | 共效应 | 计算对环境提出的要求 | 世界必须给我什么 |
| Revertible Effect | 可逆效应 | 带有可由 Runtime 跟踪的逆操作的环境变换 | 做事同时记下怎么撤销 |
| Reactive Coeffect | 响应式共效应 | 环境变化时重新根据依赖规格判断组件状态 | 依赖一变，我就重新判断能不能工作 |
| Context | 上下文 | 统一承载 effect 与 coeffect 的运行环境模型 | 整个运行时世界 |
| Component | 组件 | 具有生命周期、effect 与 coeffect 的动态组成单元 | 可装卸的功能模块 |
| Effect Context | 效应上下文 | 当前 Context 状态与 inverse accumulator 的乘积 `Γ × (Γ → Γ)` | 现在的世界，加上一键回到起点的方法 |
| Witnessed Effect | 有见证的效应 | 在每个执行现场返回能恢复该现场前状态的 inverse | 做完操作时顺手交出有效撤销单 |
| Effect Composition | 效应组合 | 正向按执行顺序、inverse 按反序组合的 `⋄` 运算 | 自动把多个 disposer 拼成一个回滚栈 |
| Effect Independence | 效应独立性 | 两组 forward/inverse 在观察等价下交换，且不改变对方 yield 的 inverse/outcome | 撤销 A 不会碰坏 B，也不会改变 B 的撤销方法 |
| Observational Equivalence | 观察等价 | 通过 coeffect operations 无法区分的 Context 状态关系 | 内部地址可以不同，只要公开行为看起来一样 |
| Coeffect Specification | 共效应规格 | Component 声明的 dependency key 集合及其 metadata | 组件启动前必须拿到的能力清单 |
| Provision | 供给声明 | Component 可能安装到 Context 的 coeffect keys | 组件承诺自己能提供哪些能力 |
| Isolation Realm | 隔离域 | 让同一逻辑 key 在不同 Context 中解析到不同 binding 的 realm | 同名依赖按租户或子树各用各的实例 |
| Interception | 拦截 | 在访问 coeffect 时合并策略 metadata，而不改变 binding identity | 不换服务，只给调用加权限或策略 |
| Fiber | 纤程 / 组件实例 | Component 的一次运行时实例，携带独立 lifecycle、view 与 accumulator | 同一个组件模板启动出来的一份实例 |
| Committed View | 已提交视图 | Fiber activation 开始时实际绑定的 key → provider identity 映射 | 这轮运行承诺一直使用的依赖版本 |
| Target View | 目标视图 | 当前 Context 下 Fiber 应绑定的 provider identity 映射或 `⊥` | 按现在环境，组件应该连到谁、该不该运行 |
| Effect Iterator | 效应迭代器 | 每步返回 inverse 与 continuation 的可中断 activation | 长安装流程每走一步就留一个检查点和撤销单 |
| Inertia | 惯性 | 已发出的异步 iteration 必须落地，再根据最新 target 决定回滚或继续 | 飞出去的请求先回来，再处理环境已变化的问题 |
| Quiescence | 静止态 | 每个 Fiber 的 lifecycle 与 target view 一致的系统状态 | 没有组件还在等着启动、卸载或重载 |
| Recovery Exactness | 精确恢复 | 撤销一个 Fiber 后，状态等价于它从未贡献、其他交错步骤仍存在 | 只擦掉自己的痕迹 |
| Resolution Coherence | 解析一致性 | 一次 transition 要么始终针对同一 committed view 完成，要么完整回滚 | 不允许半程换依赖后混装成功 |
| Confluence | 汇合性 | 同一 orchestration 输入的不同合法调度到达等价终态 | 中间怎么折腾，最终配置相同就落到同一状态 |
