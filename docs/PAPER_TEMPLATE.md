# 论文目录模板说明

可直接复制的模板位于 [`../templates/paper/`](../templates/paper/)。新论文不要从旧论文复制，以免继承旧论文的版本信息、结论或实验假设。

## 标准目录

```text
<paper-slug>/
├── README.md
├── source/
│   ├── SOURCE.md
│   └── VERSIONS.md
├── translation/
│   └── README.md
├── terminology/
│   └── glossary.md
├── notes/
│   ├── reading-map.md
│   └── questions.md
├── experiments/
│   └── README.md
├── practice/
│   └── README.md
└── comparisons/
    └── README.md
```

## 创建示例

```bash
cp -R templates/paper papers/agent-harness/example-org/example-paper
```

复制后立即完成以下操作：

1. 替换所有 `<...>` 占位符；
2. 填写权威来源、获取日期和版本；
3. 根据原文目录创建翻译文件；
4. 在 `papers/README.md` 登记论文；
5. 检查相对链接是否有效。

若论文不包含实验或暂时无法落地，仍保留对应目录，并在 README 中说明原因和后续条件。
