# 00. 标题与摘要

## 标题

**A Programming Paradigm for Spatiotemporal Composability**

建议译法：**《一种面向时空可组合性的编程范式》**

## 摘要翻译

现代软件——从插件系统到能够自我演化的智能体 Harness——越来越需要动态组合能力，但这一领域的形式化理论基础仍然不够完善。

作者识别出两个彼此正交的维度：

- **时间可组合性（Temporal Composability）**：当一个组件被移除时，能够完整撤销该组件产生的副作用。
- **空间可组合性（Spatial Composability）**：能够声明组件之间的依赖，并以响应式方式管理这些依赖关系。

论文通过把经典的 effect 与 coeffect 概念提升为运行时机制来处理这两个问题。

具体而言：

1. **Revertible Effects**：每一次上下文变换都携带一个逆操作，并由运行时跟踪该逆操作。
2. **Reactive Coeffects**：每当上下文发生变化，运行时都会依据组件声明的 coeffect specification 对组件进行通知和重新判断。
3. 把 effect context 与 coeffect context 统一成单一的 context type。
4. 在此基础上定义 component，并给出动态组合演算，使局部可组合性能够推广到多个交错运行的组件组成的系统。
5. 最后在 Cordis 中实现 effect tracking、coeffect resolution、声明式组件加载、配置协调以及热模块替换。

## 大白话版

装一个插件并不难，真正难的是：

- 插件删掉以后，它注册的工具、事件、线程、连接、缓存能不能一起删干净？
- A 插件依赖 B，B 突然消失以后，A 能不能自动知道自己已经不能继续工作？
- B 重新出现以后，A 能不能重新恢复？

这篇论文就是想把这些传统上靠工程师自己写 cleanup、回调和依赖检查的事情，变成 Runtime 可以统一管理、甚至可以形式化证明的机制。
