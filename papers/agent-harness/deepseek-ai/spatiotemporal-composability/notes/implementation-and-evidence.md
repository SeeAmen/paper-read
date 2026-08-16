# 实现与证据分析

## Theory → Cordis

| 理论对象 | Cordis | 审查结论 |
|---|---|---|
| Context `Γ∞` | `ctx` + Context tree | 一等 Context 和继承结构可承载 scoped resolution |
| Effect function | `ctx.effect(callback)` | callback 返回/yield inverse；运行时不验证 witness |
| Accumulator | `fiber.dispose` | disposer 以 LIFO 组合并要求幂等触发 |
| Coeffect store | `@@store` | key 经 realm indirection 找 binding |
| Isolation | `@@isolate` | derived Context 覆盖 realm，不修改 parent |
| Interception | `@@intercept` | 访问时合并 metadata，不触发 dependency reload |
| Component/Fiber | `ctx.use` / `fiber` | child 生命周期注册为 parent 的普通 effect |
| Committed view | `fiber.committed` | teardown 期间继续读取原 provider |
| Target view | `fiber.target` | 由 active provider uid 构成，身份变化可检测 |
| Transition inertia | `fiber.inertia` | 异步步骤落地后再链式 reload/unload |

## Algorithms 1–10

### Algorithm 1：Effect tracking

`execute` 驱动 iterator，每次 yield 一个 inverse 并前置到 composite；guard 在迭代边界检查是否继续。`effect` 用 armed flag 防止重复 disposal，并把 disposer 组合到父 Context。

风险：已经在 flight 的异步 iteration 无法被 guard 取消，只能落地后再回滚；inverse 抛错时如何继续执行剩余 inverse，伪代码未定义。

### Algorithms 2–3：Coeffect operations 与通知

`set` 安装 binding，inverse 删除 binding；两端都调用 `notify`。通知按 declared key 和 realm 找 affected Fibers，再让幂等 `refresh` 判断实际状态变化。

风险：全量遍历 live Fibers 的复杂度、通知风暴、批量变更的一致性窗口均未量化。

### Algorithms 4–5：Fiber lifecycle

`ctx.use` 把 child Fiber 的存在也建模为 parent effect。`reload` 固化 target 并执行 component effect；`unload` 先等待 notified dependents，再运行 disposer。互相链式调用实现 inertia。

这是实现最强的部分：Line 10 提前标记 UNLOADING，Line 25 drain dependents，Line 26 才 cleanup，直接对应 Theorem 63。

风险：依赖图有环时 drain 会永久等待；论文依靠 acyclicity 前提，伪代码没有展示检测与诊断。

### Algorithm 6：Context access

Proxy 沿 Fiber chain 查 committed view：未激活声明报 `INACTIVE_ACCESS`，未声明访问报 `UNDECLARED_ACCESS`。这是 capability mediation，但恶意代码仍可绕过 Proxy 直接访问 host runtime。

### Algorithm 7：Realm reassignment

通过 delimiter tag 判断 provider binding 是否属于被移动子树，只对真正跨隔离边界的 dependents 发通知。设计精巧，但其正确性主要由文字推理给出，未纳入第 4 章演算。

### Algorithms 8–10：HMR

模块图先分 accepted/declined，再找 stale entries，最后备份 cache、卸载旧 Fiber、导入新组件；失败时恢复 cache 并重建旧 Fiber。

“Transactional”只在模型边界内成立：若旧/新组件存在未 Context 化的 effect 或不可逆 emission，cache 恢复与 Fiber 重建不能撤销这些外部结果。

## Koishi 证据强度

### 支持的结论

- 同一 Context 模型可以承载服务端聊天机器人与浏览器控制台两类应用。
- 开放生态中确实存在 adapter、database、feature plugin 的真实 dependency topology。
- 多年、数千插件规模说明抽象至少具有表达力和可采用性。

### 不能支持的结论

- 论文描述 Cordis v4，Koishi 当前使用 v3；不能把 v3 运行历史当作 v4 全部语义的生产验证。
- 没有给出 CPU、内存、启动、reload、notification 或 dependency graph scale 基准。
- 没有与 VSCode/OSGi/普通 DI 做受控对照。
- 没有统计 inverse 缺失率、rollback 失败率、故障恢复时间或开发者错误率。
- 没有 self-evolving Agent Harness 的实际实验。

## 建议验证优先级

1. inverse failure 与 partial rollback；
2. provider withdrawal 下的异步 dependent teardown；
3. target 在 activation 中多次翻转；
4. 同 key 非交换 operations 的冲突；
5. dependency cycle 诊断；
6. 1000+ Fiber 的 notify/reconcile 性能；
7. HMR import failure 与外部 emission；
8. v3/v4 行为差异和迁移成本。
