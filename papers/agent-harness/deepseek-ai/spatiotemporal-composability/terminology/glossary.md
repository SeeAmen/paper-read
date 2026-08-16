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
