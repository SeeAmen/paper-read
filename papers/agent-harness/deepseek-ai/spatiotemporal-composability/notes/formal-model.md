# 形式化模型拆解

## 符号速查

| 符号 | 含义 | 工程对应 |
|---|---|---|
| `Γ` | Context 状态空间 | Runtime/Context 全部可管理状态 |
| `∂Γ = Γ × (Γ → Γ)` | Effect Context | 当前状态 + inverse accumulator |
| `Σ` | Coeffect Context | 类型化 dependency/capability store |
| `EΓ` | Effect function | 返回新状态和 disposer 的操作 |
| `E*Γ` | Witnessed effect | disposer 在执行现场能恢复原状态 |
| `d` | Coeffect specification | required capabilities |
| `p` | Provision | component 可能提供的 capabilities |
| `ω` | Committed view | activation 实际绑定的 provider 身份 |
| `target` | Target view | 当前状态下应该绑定的 provider 身份 |
| `θ` | Lifecycle state | Inactive/Reloading/Active/Unloading |

## 1. 时间方向：可逆 Effect

### 1.1 为什么 inverse 反向组合

若正向依次执行 `f1`、`f2`，恢复必须依次执行 `g2`、`g1`。论文把 pair 的组合定义为：

```text
(f1, g1) ∘ (f2, g2) = (f1 ∘ f2, g2 ∘ g1)
```

这不是技巧，而是 disposer stack 的代数表达。

### 1.2 Effect Context

```text
∂Γ = Γ × (Γ → Γ)
track(f, g)(γ, φ) = (f(γ), φ ∘ g)
recover(γ, φ) = (φ(γ), id)
```

Theorems 4–7 说明 track 保持组合，且当 `g(f(γ)) = γ` 时，track 前后 recover 的目标不变。

### 1.3 现场生成 inverse

固定 inverse 太强，因此 effect function 在当前 `γ` 上返回 `(δ, g)`：

```text
e : Γ → Γ × (Γ → Γ)
g(δ) ≃ γ
```

`g` 只需在这次 effect 实际产生的 `δ` 上恢复 `γ`，不要求是全局双射。这使“恢复旧值”“删除刚注册的对象”“关闭刚创建的连接”等工程操作可表达。

### 1.4 LIFO 与独立性

严格反序回滚只需要每个 effect 自己有 witness。若要撤销中间组件，则 foreign effects 已把状态移走，需要额外满足：

1. 两边所有 forward/inverse 变换互相交换；
2. foreign effect 不改变另一边现场选择出的 inverse/continuation。

论文在 observational equivalence `≃` 下读取交换性。不同 key 的操作天然独立；同 key 的操作是否独立由 provider interface 决定。

## 2. 空间方向：Reactive Coeffect

### 2.1 依赖满足

```text
σ ⊧ d  iff  d 中每个 key 都存在于 dom(σ)
```

Context 从 `σ` 变为 `σ'` 时：

```text
unsatisfied → satisfied   = activating
satisfied   → unsatisfied = deactivating
otherwise                 = neutral
```

Provider 替换不能只比较值。Fiber 的 view 记录 provider identity，保证“同值的新 provider”仍触发重绑定。

### 2.2 Isolation 与 Interception

- Isolation 改变 `key → realm → value` 的第一跳，用于租户、测试或子树私有 binding。
- Interception 合并 component-declared metadata 与 context-carried metadata，用于权限和调用策略；它改变“如何用”，不改变“是否满足”。

## 3. 统一 Context

```text
Γ∞ = μΓ. Γ × (Γ → Γ) × Σ
```

递归结构让 child Context 的 disposer 成为 parent Context 的 effect。任何跨组件共享状态都应绑定到 `Σ` 的 key；否则它既无法参与依赖排序，也无法进入 independence 证明。

观察等价由每个 key 的 operation interface 组装：若两个值通过所有允许操作都不可区分，就可以视为等价。其工程含义是：恢复承诺针对公开行为，不针对内部地址、对象标识或内存布局。

## 4. Component、Fiber 与 Registry

```text
Component = (requirements d, provisions p, effect e)
Fiber = Component + parent + own store + retired + lifecycle
Registry = uid → Fiber
```

约束：

- 每个 key 在共享 realm 中最多一个 provider；
- effect 被 confined 到自身 Fiber table，除非通过受控 primitive 注册 child Fiber；
- effect 只能读取自己声明的 coeffects，不能读取其他 Fiber control fields；
- retirement 与 removal 分离，防止先丢 accumulator 再泄漏资源。

## 5. 生命周期规则

| 类别 | 规则 | 作用 |
|---|---|---|
| Orchestration | O-Insert | 创建 inactive Fiber |
| Orchestration | O-Retire | 标记目标为停止，不直接删除 |
| Orchestration | O-Remove | 仅删除 inactive 且无 child 的 Fiber |
| Activation | L-Begin | 固化 committed view，进入 Reloading |
| Activation | L-Iter | 执行一步并把 inverse 压入 accumulator |
| Activation | L-Finish | 最后一步成功，进入 Active |
| Activation | L-Divert | target 变化，停止继续安装并转入回滚 |
| Failure | L-Raise | 记录错误前先转入回滚 |
| Withdrawal | L-Leave | 先停止对新 consumer 提供服务 |
| Withdrawal | L-Unload | dependents 排空后执行 accumulator |

## 6. 关键执行序列

设 A 提供 `db`，B 声明 `db`：

```text
insert A
  A: Inactive → Reloading → Active; db visible
insert B
  B resolves db → A.uid
  B: Inactive → Reloading → Active
retire A
  A: Active → Unloading; db no longer visible to new consumers
  B.target becomes ⊥
  B: Active → Unloading → Inactive; B cleanup can still read committed A.db
  A guard releases
  A disposer removes db and other effects
  A: Inactive → removed
```

这个顺序是论文 spatial guarantee 的核心：provider 先“下线”，但后“销毁资源”。

## 7. 定理与前提矩阵

| 保证 | Witness | Pairwise independence | Acyclic precedence | Finite/bounded | Total provision | No failure |
|---|---:|---:|---:|---:|---:|---:|
| 局部 LIFO 恢复 | 是 | 否 | 否 | 否 | 否 | 否 |
| 跨 Fiber recovery exactness | 是 | 是 | 否 | 否 | 否 | 否 |
| Provider/consumer ordering | 是 | 否 | 否 | 否 | 否 | 否 |
| Progress/termination | 是 | 否 | 是 | 是 | 否 | 否 |
| Confluence/canonical form | 是 | 是 | 是 | 隐含有限 | 是 | 是 |

## 8. 不应误读的地方

- `inverse` 是左逆且只在执行现场有义务，不是全局可逆函数。
- `≃` 由可观察操作定义，不是物理状态相同。
- “所有 effect 独立”不是运行时自动产生的性质。
- Confluence 只比较最终 Context state，不比较期间已经发出的日志、网络包或用户可见输出。
- 失败 Fiber 的贡献会被清空，但不同调度是否失败可以不同，因此失败状态不汇合。
