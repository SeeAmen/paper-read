# 完整验证计划

## 总体原则

实验不只验证 happy path。每个实验都要记录 Context 快照、Fiber 状态、committed/target view、disposer 执行顺序、通知链和最终残留资源。

## EXP-01：局部 LIFO 恢复

- 对应机制：Definitions 8–16。
- 假设：同一 Fiber 的多个 effect 即使不交换，只要严格反序执行 inverse，仍能恢复初始状态。
- 操作：依次注册 Tool、listener、timer、临时文件句柄；每步返回 inverse。
- 断言：disposer 顺序与安装相反；卸载后 registry、listener、timer、handle 全部回到基线。
- 反例注入：让中间 inverse 抛错，观察剩余 inverse 是否继续执行，并记录框架策略。

## EXP-02：跨 Fiber 独立性

- 对应机制：Definition 19、Theorem 61。
- 假设：不同 key 的注册操作可交错，撤销任一 Fiber 不改变其他 Fiber 的可观察结果。
- 操作：A/B 分别在不同 registry key 上执行多步 effect，交错运行后先卸载 A。
- 断言：A 的 key 消失，B 的 key、inverse 和 outcome 不变。
- 反例：A/B 写同一个有序 middleware chain，证明不满足 independence 时结果依赖顺序。

## EXP-03：Provider withdrawal ordering

- 对应机制：Theorem 63、Algorithm 5。
- 假设：consumer teardown 期间仍能读取 committed provider；provider inverse 在所有 dependents 完成后才运行。
- 操作：DB provider 提供连接池，Report consumer 在 teardown 中异步归还连接。
- 断言：事件顺序为 `provider unavailable → consumer unload → consumer drained → provider dispose`。
- 反例：去掉 withdrawal guard，复现 stale/closed dependency。

## EXP-04：Activation 中 target 翻转

- 对应机制：Effect iterator、Theorem 64。
- 假设：activation 分多步执行时替换 provider，Fiber 不会以混合 resolution 进入 Active。
- 操作：在第二个 yield 前把 provider V1 替换为 V2。
- 断言：已完成步骤被回滚；下一 episode 全程绑定 V2；不存在同时引用 V1/V2 的 Active 状态。
- 异步变体：让步骤已经 in flight，验证其先落地再回滚。

## EXP-05：Failure rollback

- 对应机制：L-Raise、Corollary 62。
- 假设：第 N 步失败后，前 N-1 步贡献被清除，Fiber 进入 FAILED，不自动重试。
- 操作：安装 Tool、listener 后模拟端口绑定失败。
- 断言：Tool/listener 不残留；错误记录在该 Fiber；siblings 保持 Active。
- 反例：inverse 本身失败，明确论文未覆盖的恢复策略。

## EXP-06：Progress 与依赖环

- 对应机制：Theorem 66。
- 假设：有限 DAG 最终 quiescent；cycle 中组件保持 inactive。
- 操作：生成不同深度/宽度的 DAG，再生成 A↔B 与 self-cycle。
- 断言：DAG 在有界 lifecycle steps 内静止；cycle 被提前诊断而不是无日志等待。

## EXP-07：Confluence

- 对应机制：Theorem 73。
- 假设：满足全部前提时，不同调度和中间 reload 历史得到相同可观察终态。
- 操作：对同一 orchestration input 随机化 Fiber 调度 1000 次，与 from-scratch final config 对比。
- 断言：最终 active set、provider identity、registry observations 等价。
- 排除：故障、外部 emission、非独立 effect；这些应单独作为负例。

## EXP-08：HMR 事务边界

- 对应机制：Algorithms 8–10。
- 假设：导入错误时 module cache 和 Context 内 effect 均恢复旧版本。
- 操作：同时替换多个 stale entries，让中间 entry import 失败。
- 断言：所有旧 Fiber 恢复；无新旧版本混合；未 Context 化的文件写入被记录为边界外反例。

## EXP-09：规模与可观测性

- 目标：补足论文缺失的量化证据。
- 规模：10、100、1000、5000 Fibers；不同 dependency density 与 realm 数量。
- 指标：notify 扫描时间、reconcile 时间、内存/Fiber、reload p50/p95/p99、drain 深度、inverse failure 数。
- 输出：原始事件日志、环境信息、可重复脚本和图表。

## 通过标准

只有 EXP-01 至 EXP-08 的正例和负例均符合预期，才把论文状态从“已通读”提升为“已验证”。当前这些实验均为设计状态，尚未宣称结果。
