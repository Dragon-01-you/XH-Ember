# Triage 标签映射表

`/triage` 技能内部用五个规范英文名（角色）思考；这张表把它们映射到本仓库 GitHub 上真实存在的标签。
目前两者同名，所以映射是 1:1。若以后你在 GitHub 上改了标签名，只需改中间一列。

> 一句话：**分诊（triage）不是追踪器。** 追踪器是 GitHub Issues（东西存在哪、怎么读写）；
> 分诊是给进来的单子贴状态、决定下一步谁来做的那套流程。

## 五个状态（每条单子同时只能有一个）

| 规范角色名 | 本仓库标签名 | 中文含义 | 什么时候用 |
| --- | --- | --- | --- |
| `needs-triage` | `needs-triage` | 待分诊 | 刚创建、还没人评估过的默认入口 |
| `needs-info` | `needs-info` | 缺信息 | 等报告人补充复现步骤、环境、期望结果；补完回到「待分诊」 |
| `ready-for-agent` | `ready-for-agent` | 可交给 AI | 需求已写清楚，一个不需要人盯着的 agent 能独立完成 |
| `ready-for-human` | `ready-for-human` | 需要人来做 | 需要判断力、外部权限、设计决策或手工验证，AI 干不了 |
| `wontfix` | `wontfix` | 不做 | 要么已经实现（没人知道），要么明确拒绝（拒绝理由存进 `.out-of-scope/`） |

## 两个类别（每条单子恰好一个，与状态并存）

| 规范角色名 | 本仓库标签名 | 中文含义 |
| --- | --- | --- |
| `bug` | `bug` | 有东西坏了 |
| `enhancement` | `enhancement` | 新功能或改进 |

## 状态怎么流转

```
新单子（没标签）
      ↓
  needs-triage ──→ needs-info ──→（报告人补充了）──→ 回到 needs-triage
      │
      ├──→ ready-for-agent   交给 AI 直接干
      ├──→ ready-for-human   必须人来
      └──→ wontfix           不做
```

「缺信息」在报告人回复后回到「待分诊」。维护者可以随时手动覆盖；
遇到反常的跳转，技能会先问你，不会自己乱走。

## 用在哪

`/triage` 每次要贴标签时都查这张表。当技能说「贴上 AFK-ready 的标签」，它就会去贴 `ready-for-agent`。
