# 与相关系统的横向比较

| 方案 | Effect 撤销 | 依赖变化 | 异步 teardown | 状态迁移 | 主要边界 |
|---|---|---|---|---|---|
| Cordis | 原子 effect 返回 inverse，自动组合 | provider identity 驱动 Fiber lifecycle | 有 inertia 与 dependent drain | 默认无，重建旧/新 Fiber | Context 外 emission、witness/independence 假设 |
| RAII / Rust ownership | 词法作用域退出时释放 | 不处理运行时 provider 拓扑 | 受语言/析构限制 | 无 | scope 必须静态可见 |
| React `useEffect` | effect 返回 cleanup | dependency array 变化重跑 | effect body 本身不接受 async | UI state 由 React 另管 | Hook 调用顺序和组件作用域 |
| OSGi Declarative Services | 手写 deactivate/cleanup | service 出现/消失驱动组件 | teardown 协议较弱 | 无统一方案 | 完整清理由作者负责 |
| 普通 DI 容器 | 通常由 scope/container disposal | 多在初始化时解析 | 框架相关 | 无 | provider 替换后 consumer 通常不重绑定 |
| DSU / HMR | 替换版本，常需手写 accept/dispose | 关注 module graph，不一定有 service graph | 工具相关 | 强项是 forward migration | 不保证完整卸载任意组件 |
| STM / transaction | 固定事务范围内自动回滚 | 不处理组件依赖 | 通常不覆盖长期 async lifecycle | 事务状态 | scope 预先固定，外部 I/O 困难 |
| Saga / compensation | 业务补偿，不是精确 inverse | 由流程编排 | 支持长事务 | 业务状态前移 | 只保证应用定义的补偿等价 |
| FRP / Signals | 重新计算派生值 | 值级 dependency 自动传播 | 通常以 turn/scheduler 为单位 | 派生值重算 | 不管理组件资源生命周期 |
| Process/container restart | OS 回收进程资源 | orchestrator 管 service dependency | 可 drain 服务 | 进程外持久状态 | 粒度粗、重建成本高 |

## 关键判断

- Cordis 与 RAII 互补：局部资源交给 RAII，跨动态组件生命周期的资源交给 EffectScope。
- Cordis 与 FRP 互补：Fiber-level reactivity 管“组件是否运行”，signals 管“组件内部哪些值重算”。
- Cordis 与 DSU 互补：Cordis 负责旧 effect 完整退出，DSU 负责必须保留的私有状态向前迁移。
- Cordis 与 Saga 互补：Context 内 acquisition 用 inverse；已经越过边界的 emission 用 compensation。
- Cordis 不能替代 sandbox：capability mediation 没有阻止恶意代码绕过语言级 API。

## 成熟度判断

Cordis 的形式化完整度高于普通插件生命周期框架，但实证成熟度低于论文的理论强度：当前案例是单语言、单生态的 adoption evidence，且 Koishi 使用 v3、论文描述 v4。工程选型应以 PoC 和故障注入验证关键假设，不能只凭定理名称判断可用性。
