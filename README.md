# Paper Read

面向长期积累的论文阅读、翻译、实验与工程实践知识库。

这里的目标不是记录“看过哪些论文”，而是把每篇论文加工成可检索、可验证、可复用的知识资产：

**论文原文 → 中文翻译 → 术语体系 → 阅读笔记 → 实验验证 → 工程落地 → 横向比较**

## 快速开始

1. 从 [`templates/paper/`](templates/paper/) 复制一份论文目录。
2. 放到 `papers/<domain>/<organization>/<paper-slug>/`。
3. 先填写论文 README、`source/SOURCE.md` 和 `source/VERSIONS.md`。
4. 按原文章节推进翻译与笔记，并同步更新术语、实验和实践结论。
5. 提交前执行 [`docs/QUALITY_CHECKLIST.md`](docs/QUALITY_CHECKLIST.md) 中的检查。

详细规则见 [`docs/REPOSITORY_GUIDE.md`](docs/REPOSITORY_GUIDE.md)，完整流程见 [`docs/RESEARCH_WORKFLOW.md`](docs/RESEARCH_WORKFLOW.md)。

## 统一目录

```text
paper-read/
├── docs/                         # 仓库规范、公共术语和质量门禁
├── templates/paper/              # 新论文的唯一标准模板
├── papers/
│   └── <domain>/<organization>/<paper-slug>/
│       ├── README.md             # 元信息、结论、状态和导航
│       ├── source/               # 原文、来源与版本记录
│       ├── translation/          # 按原文章节编号的翻译
│       ├── terminology/          # 本文术语表
│       ├── notes/                # 阅读地图、问题与机制拆解
│       ├── experiments/          # 可复现实验、代码和结果
│       ├── practice/             # 工程映射与落地实践
│       └── comparisons/          # 本文与其他方案的比较
└── comparisons/                  # 跨多篇论文的主题级横向比较
```

## 维护原则

- **翻译与解释分离**：翻译忠于原文，推导和观点写入笔记或实践。
- **事实与判断分离**：统一使用 `【论文原文支持】`、`【工程推导】`、`【个人设计】`、`【待验证】` 标记证据层级。
- **结论必须可追溯**：关键结论链接到原文章节、实验记录或比较依据。
- **实验必须可复现**：记录环境、步骤、预期、实际结果和失败场景。
- **版本必须可同步**：论文更新时先登记版本，再检查翻译和结论是否失效。
- **版权与许可优先**：仅在许可允许时提交论文全文；否则保存来源链接、版本和校验信息。

## 论文索引

论文清单与阅读状态统一维护在 [`papers/README.md`](papers/README.md)。

当前研究：

- [A Programming Paradigm for Spatiotemporal Composability](papers/agent-harness/deepseek-ai/spatiotemporal-composability/README.md) — 阅读中

## 公共知识

- [跨论文公共术语表](docs/GLOSSARY.md)
- [主题级横向比较](comparisons/README.md)
