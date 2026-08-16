# 工程落地实践

## 目标

把论文思想映射到实际 Agent Harness，而不是直接照搬数学模型。

## 推荐落地层级

### V1：Minimal Harness Core

- Tool Registry
- Skill Registry
- Plugin API
- Session / Context
- Permission

### V2：生命周期与所有权

- Plugin Scope
- Effect Ownership
- Disposable / Rollback
- Startup Failure Recovery

### V3：依赖图

- Capability Dependency Graph
- Reactive Activation / Deactivation
- Dependency Version Check

### V4：动态重配置

- Hot Reload
- Configuration Reconciliation
- Plugin Replacement

### V5：Self-Evolving Harness

- Agent 发现能力缺失
- 生成/安装 Plugin
- Sandbox Test
- 运行时加载
- 失败自动回滚

## 工程原则

用 Pi 的思想保持 Core 足够小，用 Cordis 的思想加强 Plugin 的生命周期、回滚和依赖语义。
