# Issue 追踪器：GitHub

本仓库的 issue 与 spec 都放在 GitHub Issues 里。所有操作都走 `gh` CLI。

- 仓库：`Dragon-01-you/XH-Ember`（公开）
- 在 clone 目录里跑 `gh` 会自动推断仓库，一般不用显式带 `-R`。

## 约定

- **新建 issue**：`gh issue create --title "..." --body "..."`。多行正文用 heredoc。
- **读 issue**：`gh issue view <编号> --json number,title,body,labels,comments`。
- **列 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，配合 `--label` 和 `--state` 过滤。
- **设父子关系**：`gh issue create --parent <父编号> ...`，或事后 `gh issue edit <父编号> --add-sub-issue <子编号>`（gh 2.94+）。旧版 gh 用 `gh api --method POST repos/<owner>/<repo>/issues/<父编号>/sub_issues -F sub_issue_id=<子 issue 的数据库 id>`（注意是数据库 id，不是 `#编号`）。用不上子 issue 时，在子单正文顶部写 `Part of #<父编号>`。
- **评论**：`gh issue comment <编号> --body "..."`。
- **贴/去标签**：`gh issue edit <编号> --add-label "..."` / `--remove-label "..."`。
- **关闭**：`gh issue close <编号> --comment "..."`。

## 外部 PR 是否当作需求入口

**不作为（PRs as a request surface: no）。**

若以后你想让外部 PR 也进分诊队列，把这一项改成 `yes`，然后按下面的等价命令走。

启用后（`yes`）的等价命令：

- **读 PR**：`gh pr view <编号> --comments`、`gh pr diff <编号>`。
- **列外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论 / 贴标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label` / `--remove-label`、`gh pr close`。

GitHub 的 issue 和 PR 共用同一个编号空间，所以裸的 `#42` 可能是两者之一：
先 `gh pr view 42`，失败再 `gh issue view 42`。

## 当技能说「发布到 issue 追踪器」

创建一个 GitHub issue。

## 当技能说「取出相关工单」

按上面「读 issue」的方式读。用户一般会直接给你编号或链接。

## Wayfinding 操作（`/wayfinder` 使用）

**地图**是一张 issue，**子工单**是挂在地图下的子 issue。

- **地图**：一个打了 `wayfinder:map` 标签的 issue，正文承载 背景 / 已定决策 / 迷雾。`gh issue create --label wayfinder:map`。
- **子工单**：作为地图的子 issue 创建（见上面「设父子关系」）。子 issue 不可用时，把子单加进地图正文的 task list，并在子单顶部写 `Part of #<地图编号>`。标签用 `wayfinder:<type>`（`research` / `prototype` / `grilling` / `task`）。被认领后指派给执行人。
- **阻塞**：用 GitHub 原生的 issue 依赖。`gh api --method POST repos/<owner>/<repo>/issues/<子编号>/dependencies/blocked_by -F issue_id=<阻塞 issue 的数据库 id>`，其中数据库 id 由 `gh api repos/<owner>/<repo>/issues/<n> --jq .id` 取得——**不是** `#编号`，也不是 `node_id`。GitHub 用 `issue_dependencies_summary.blocked_by` 报告未关闭的阻塞数（这才是实时闸门）；每个阻塞都关闭才算解锁。依赖功能不可用时，退化为子单顶部的 `Blocked by: #<n>, #<n>` 行。
- **前沿查询**：列地图下未关闭的子 issue，去掉有未关闭阻塞的（`issue_dependencies_summary.blocked_by > 0`）和有指派人的，按地图顺序取第一个。
- **认领**：`gh issue edit <n> --add-assignee @me`，这是本会话的第一次写操作。
- **解决**：`gh issue comment <n> --body "<答案>"` → `gh issue close <n>` → 往地图的「已定决策」追加一条上下文指针（要点 + 链接）。
