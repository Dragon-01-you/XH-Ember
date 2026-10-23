# 域文档（Domain Docs）

技能在探索代码库之前，应该怎样消费本仓库的领域文档。

## 动手前先读这些

- 仓库根目录的 **`GLOSSARY.md`**；或
- 若存在 **`GLOSSARY-MAP.md`**：它指向每个上下文各自的 `GLOSSARY.md`，读与当前主题相关的那些。
- **`docs/adr/`**：读与你即将改动的区域相关的 ADR。多上下文仓库还要看 `src/<上下文>/docs/adr/`。

如果这些文件不存在，**默默继续**。不要提示缺失，也不要主动建议先建它们。
`/domain-modeling` 技能（经由 `/grill-with-docs` 和 `/improve-codebase-architecture` 到达）
会在术语或决策真正被定下来时才懒创建。

## 文件结构

单上下文仓库（绝大多数仓库，本仓库采用这种）：

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-....md
│   └── 0002-....md
└── src/
```

多上下文仓库（根目录存在 `GLOSSARY-MAP.md` 时为多上下文）：

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← 上下文级决策
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## 用术语表的词汇

当你的输出要命名一个领域概念（issue 标题、重构提案、假设、测试名），用 `GLOSSARY.md` 里定义的词。
不要漂移到术语表明确避开的同义词。

如果你需要的概念还不在术语表里，那是一个信号：要么你在发明项目不用的语言（重新考虑），
要么这里有个真实的缺口（记下来，交给 `/domain-modeling`）。

## 撞到 ADR 要显式提出

如果输出与现有 ADR 矛盾，把它显式浮出来，而不是悄悄覆盖：

> _与 ADR-0007（事件溯源订单）冲突，但值得重开，因为……_
