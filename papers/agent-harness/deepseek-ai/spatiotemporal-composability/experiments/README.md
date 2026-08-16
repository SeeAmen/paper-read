# 实验计划

目标：把论文中的抽象概念逐步实现成一个可运行的 Mini Harness。

## 实验路线

### EXP-01：制造一个卸载不干净的 Plugin

目标：直观看到 Temporal Composability 为什么是问题。

步骤：
1. HarnessContext 内维护 Tool Registry。
2. GitPlugin 启动时注册 `git.status`。
3. 删除 Plugin 对象，但不执行清理。
4. 验证 Tool Registry 中 `git.status` 仍然存在。

预期：产生 ghost state。

### EXP-02：Disposable / Undo

让 `registerTool()` 返回 `Disposable`，卸载 Plugin 时调用逆操作。

验证：

```text
state_before_load == state_after_unload
```

### EXP-03：Effect Scope

一个插件连续注册多个 Tool / Event / Resource，Runtime 自动记录全部 disposer，并按逆序执行。

### EXP-04：Reactive Dependency

ReportPlugin 声明依赖 XmlParserPlugin：

```text
XmlParser present  -> Report ACTIVE
XmlParser removed  -> Report INACTIVE
XmlParser restored -> Report ACTIVE
```

### EXP-05：Failure Rollback

Plugin 启动到一半抛异常，验证已经产生的 Effect 能否自动回滚。

### EXP-06：Hot Reload

替换 Plugin V1 → V2，不重启 Harness，同时验证旧 Effect 被清理、新 Effect 生效。

## 最终产物

```text
mini-harness/
├── core/
├── plugins/
└── tests/
```
