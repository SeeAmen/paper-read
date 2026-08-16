# Agent Harness 落地蓝图

## 目标

构建能够在不中断 Session 的情况下加载、卸载和替换 Tool/Skill/Provider 的 Harness，同时把不可逆外部操作、安全边界和版本兼容显式留在模型之外处理。

## 最小核心

```text
HarnessContext
├── EffectScope          # 收集 disposer，LIFO rollback
├── CapabilityStore      # key/realm → provider binding
├── FiberRegistry        # uid → lifecycle/target/committed
├── Reconciler           # desired config → fibers
├── TransitionScheduler  # reload/unload/inertia/drain
└── EventJournal         # 每次 effect、notify、rollback、failure
```

## 建议接口

```ts
type Disposer = () => void | Promise<void>

interface EffectScope {
  effect(apply: () => Disposer | AsyncGenerator<Disposer>): Disposer
  dispose(): Promise<void>
}

interface Component {
  requires: CapabilitySpec[]
  provides: CapabilitySpec[]
  activate(ctx: ComponentContext): AsyncGenerator<Disposer>
}
```

`activate` 每完成一个原子步骤就 yield inverse。Harness 只允许通过 `ComponentContext` 注册 Tool、Hook、Resource 和 child Component。

## Capability 设计

每个 key 至少携带：

- namespace/package identity；
- interface schema 或 type hash；
- semantic version range；
- realm/tenant；
- access metadata；
- provider Fiber uid；
- operation commutativity 声明。

若无法证明同 key operations 交换，默认策略应是串行化或把顺序建模为显式 dependency，而不是乐观假定 independence。

## 三阶段卸载

```text
1. Leave: provider 停止接受新 consumer / 新请求
2. Drain: 通知并等待 committed consumers 完成 teardown
3. Dispose: provider 执行 inverse，释放实际资源
```

对长任务应提供 deadline、强制取消和 escalation；论文只证明 guard 最终释放的理想条件，不替代超时策略。

## 外部 emission

以下操作不应伪装成可逆 Context effect：

- 已发送的消息、网络包或邮件；
- 已提交的支付；
- 被其他进程观察到的文件/数据库写入；
- 用户已经看到的输出。

建议分类：

| 类型 | 策略 |
|---|---|
| Acquisition | disposer，例如 close/release/unregister |
| 可延迟 emission | outbox + commit point |
| 可补偿 emission | saga/compensation + idempotency key |
| 不可补偿 emission | 明确标记 irreversible，禁止自动热替换区间内执行 |

## 安全模型

- Context capability 约束善意组件能访问什么。
- 不可信生成代码放入 Wasm、受限子进程或容器。
- Host bridge 只暴露声明且批准的 capabilities。
- High-risk capability 在 load time 审批；runtime 每次调用仍按 path/scope/policy 检查。
- 卸载与回滚事件必须审计，包括失败的 inverse。

## 状态迁移

Cordis 风格默认“撤销旧 effect，再应用新 effect”。需要跨版本保留的状态应：

1. 上移到稳定 provider；或
2. 提供显式 `snapshot/migrate/restore`；或
3. 通过事件日志重建。

迁移与 rollback 分开建模，避免把“旧状态能迁移”误认为“旧 effect 已撤销”。

## 分阶段实施

### Phase 1：Effect ownership

- 所有 registry mutation 返回 disposer。
- 每个 Plugin/Fiber 独立 EffectScope。
- startup failure 自动回滚。

### Phase 2：Reactive capability

- requires/provides 声明。
- provider uid 驱动 target/committed。
- withdrawal drain protocol。

### Phase 3：Reconciliation 与 HMR

- desired config tree。
- incremental diff。
- module cache backup + Fiber replacement。

### Phase 4：安全与边界

- sandbox bridge。
- emission policy。
- version/schema compatibility。

### Phase 5：Self-evolution

- Agent 生成 component → sandbox test → capability review → staged activation。
- 自动 replacement 必须有 canary、event journal 和一键恢复旧 Fiber。

## 准入条件

组件只有同时满足以下条件，才进入可热替换域：

- 所有共享 effect 通过 Context；
- 每个原子 effect 提供幂等 inverse；
- requires/provides 完整；
- dependency graph 无环或 cycle 有显式 broker；
- interface/version 可验证；
- external emission 有 commit/compensation 策略；
- untrusted code 有 sandbox；
- inverse failure 有隔离、重试或人工恢复路径。
