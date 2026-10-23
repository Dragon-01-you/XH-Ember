# XH-Ember

芯火（XH）· 自动化部署

## 沟通约定

- **面向人的输出一律用简体中文。** 英文术语第一次出现时，紧跟一句中文解释。
- 不许用未解释的缩写。`spec` 写成「规格书」，`issue` 写成「议题」，`ADR` 写成「决策记录」。
- 需要保留英文的地方只有三处：技能名（如 `/wayfinder`）、命令行、代码标识符。
- 术语对照见 `docs/agents/术语对照.md`。

## Agent skills

### Issue tracker

Issue 与 spec 存在本仓库的 GitHub Issues 里（`Dragon-01-you/XH-Ember`，用 `gh` CLI 读写）。见 `docs/agents/issue-tracker.md`。

### Triage labels

五个 triage 状态标签沿用规范英文名，中文含义见映射表。见 `docs/agents/triage-labels.md`。

### Domain docs

单上下文（single-context）布局：根目录 `GLOSSARY.md` + `docs/adr/`。见 `docs/agents/domain.md`。
