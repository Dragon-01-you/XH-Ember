# 浏览器扩展桥：现状与可依赖性

2026-10-23｜13 个项目｜标注：**[已核实]** 有官方原文，**[判断]** 我的推断，**[未找到]** 检索无果

## 结论先行

1. **机制没有分叉：能用的桥都是「扩展 + 本地进程 + CDP」。** 扩展申请 `chrome.debugger` 就能 attach 当前标签页，不必让用户重启 Chrome 开 `--remote-debugging-port`；Kimi 与 Claude for Chrome 都走这条路 **[已核实]**。
2. **「复用登录态」已被官方产品验证，但只对自己的助手开放。** Kimi 称登录态与网页内容不出设备；微软 Copilot Cowork 直接用你本机 Edge 的 SSO **[已核实]**。这不是给第三方程序的许可 **[判断]**。
3. **可以当依赖，但没有一家提供稳定第三方接口。** 唯一接近「通用第三方桥」的是 OpenCLI（Apache-2.0、CLI、任意站点、你的登录态）；Kimi 桥开放给「其他本地 Agent」，但接口随 Kimi 版本走、无兼容承诺 **[判断]**。
4. **死项目别碰：** Puppeteer MCP 已归档停更；OpenAI Atlas 2026-08-09 停服。Kimi WebBridge 只是改名，仍维护 **[已核实]**。
5. **建议：桥自研，流程层借现成 MCP。** 薄桥（扩展 + WebSocket + CDP 封装）是 1–2 周量级；难点不在桥，在控制台 DOM 变更与风控 **[判断]**。

## 逐项要点

- **Kimi 浏览器扩展**（原 WebBridge）：本地服务默认 `ws://127.0.0.1:10086/ws`，配置在 `~/.kimi-webbridge/config.json`，支持 Chrome/Edge，FAQ 明写「与其他 Agent 配合」并给排错步骤。这是目前最接近「可借用」的国产桥，但闭源、无 API 文档 **[已核实]**。
- **OpenCLI**：本地 daemon（`localhost:19825`）+ Browser Bridge 扩展，`opencli browser *` 用结构化 DOM 快照而非截图，支持 `OPENCLI_CDP_ENDPOINT` 接远程 CDP，含 100+ 站点适配器；Apache-2.0，可商用 **[已核实]**。
- **Claude for Chrome**：扩展权限表里同时有 `debugger`（真控制浏览器）与 `nativeMessaging`（只对接 Claude Desktop/Code），说明它是 CDP 型而非视觉型；仅付费计划，侧栏仍标 beta **[已核实]**。
- **Gemini in Chrome / Edge Copilot**：都是浏览器内置能力，不是可装的第三方桥；Gemini 的「auto browse」按标签页代操作，Edge 的 Cowork 在隐藏标签页里跑并继承管理员策略与 DLP。两者都无对外本地接口 **[已核实机制，未找到接口]**。
- **开源侧**：browser-use（MIT）、Stagehand（MIT）、Playwright MCP（Apache-2.0）、Chrome DevTools MCP（Apache-2.0）都能接已有 Chrome；**Skyvern 是 AGPL-3.0**，做成 SaaS 会触发网络 copyleft，商用前必须法务过一遍 **[已核实]**。Chrome DevTools MCP 默认上报使用统计，需 `--no-usage-statistics` 关闭 **[已核实]**。

## 三个判断题

**1. 条款与风控是否明显更优？** 架构上更优：身份是用户本人、有逐次同意、可被企业策略与 DLP 约束，且已有厂商明确背书「在你自己的浏览器里操作」——微软把它做成租户开关，Anthropic 做成付费功能并配企业白名单。但它降的是「冒充登录」的风险，不是法律免责：Netlify、华为云 AUP 仍禁止自动化访问站点（见同目录 `vendor-onboarding-paths`）。**未找到**任何云厂商允许第三方程序借用户浏览器操作其控制台 **[判断]**。

**2. 当依赖现实吗？** 分三层看：桥（扩展+CDP）无成熟第三方供应商，自研；编排（点击/填表/重试）可用 Playwright MCP、Chrome DevTools MCP，成熟且免费；站点适配必须自己做，且是长期维护成本。**结论：桥自研、编排借力，不要把 Kimi 桥写进架构关键路径** **[判断]**。

**3. 已死/停维护：** Puppeteer MCP（归档、npm 弃用、无安全更新）、OpenAI Atlas（停服，能力并入 ChatGPT 桌面）、Operator（据称已并入 ChatGPT agent，日期 **未核实**）。另注意 GitHub 上同时存在 `browserbase/stagehand` 与 `yutori-ai/stagehand` 两个同名仓库，归属关系 **未核实**，选型前先确认版权主体。

## 对照表

| 方案 | 机制 | 复用登录态 | 数据出本机 | 第三方可调用 | 许可证 | 状态 |
|---|---|---|---|---|---|---|
| Kimi 浏览器扩展 | 扩展+本地桥+CDP | 是 | 否（官方称） | 是（本地 ws，面向其他 Agent） | 闭源 | 正式，维护中 |
| OpenCLI | 扩展+daemon+CDP | 是 | 否（daemon 本地） | 是（CLI/JSON） | Apache-2.0 | 活跃 |
| Claude for Chrome | 扩展 `chrome.debugger`→CDP | 是 | 是（云端推理） | 否（nativeMessaging 仅自家） | 闭源 | 正式，侧栏 beta |
| Gemini in Chrome | 浏览器内置 | 是 | 是（送 Google） | 否（未找到） | 闭源 | 正式，分地区 |
| Edge Copilot Cowork | Edge 内置隐藏标签页 | 是（SSO） | 凭据不出机，任务上云 | 否 | 闭源 | 正式，需管理员开 |
| OpenAI（Atlas→ChatGPT） | 原为本地浏览器代理 | 是 | 是 | 否 | 闭源 | **已停服** |
| Perplexity Comet | 自带 Chromium+助手 | 是 | 是（历史用于广告画像） | 否 | 闭源 | 活跃 |
| browser-use | Python 库，Playwright/CDP，DOM+视觉 | 可（接已有 Chrome） | 取决于所接模型 | 是（库/自建） | MIT | 活跃 |
| Skyvern | 视觉+DOM，偏云端 | 可（持久化配置） | 是（默认云） | 是（SDK/API） | **AGPL-3.0** | 活跃，商用慎 |
| Stagehand | TS 库，Playwright，act/extract | 可（CDP 接入） | 取决于模型 | 是（库） | MIT | 活跃，归属待核 |
| Nanobrowser | 纯扩展多智能体，用你的 key | 是 | 是（送所选模型商） | 否 | Apache-2.0 | 活跃 |
| Playwright MCP | MCP，无障碍树快照 | 可（profile/CDP） | 取决于客户端 | 是（MCP） | Apache-2.0 | 活跃 |
| Chrome DevTools MCP | Puppeteer/CDP，快照+截图+性能 | 可（接已开 Chrome） | 是（默认统计上报） | 是（MCP/CLI） | Apache-2.0 | 活跃 |

## 来源

- Kimi：[产品页](https://www.kimi.com/products/kimi-browser-extension)、[工作原理](https://www.kimi.com/help/kimi-webbridge/kimi-webbridge-how-it-works)、[FAQ（端口/路径/其他 Agent）](https://www.kimi.com/help/kimi-webbridge/kimi-webbridge-faq)
- OpenCLI：[仓库](https://github.com/jackwener/OpenCLI)、[LICENSE](https://github.com/jackwener/OpenCLI/blob/main/LICENSE)
- Claude for Chrome：[帮助中心（权限表含 debugger）](https://support.claude.com/zh-CN/articles/12012173-%E5%9C%A8-chrome-%E4%B8%AD%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8-claude)
- Gemini in Chrome：[官方帮助](https://support.google.com/chrome/answer/16283624)、[auto browse 报道](https://9to5google.com/2026/01/28/chrome-gemini-auto-browse/)
- Edge Copilot Cowork：[Microsoft Learn](https://learn.microsoft.com/en-us/microsoft-365/copilot/cowork/cowork-local-browser)
- OpenAI：[Atlas 并入 ChatGPT 公告](https://help.openai.com/en/articles/20001371-evolving-atlas-into-chatgpt-for-browser-based-agentic-work)、[停服报道](https://baoquocte.vn/openai-khai-tu-trinh-duyet-atlas-421557.html)
- Comet：[Comet 助手隐私说明](https://www.perplexity.ai/help-center/comet/en/articles/12867415-comet-assistant-privacy-data-use)、[iOS 版与广告画像](https://www.macrumors.com/2026/03/18/perplexity-comet-browser-iphone/)
- 开源：[browser-use](https://github.com/browser-use/browser-use)、[Skyvern](https://github.com/Skyvern-AI/skyvern)、[Stagehand](https://github.com/browserbase/stagehand)、[Nanobrowser](https://github.com/nanobrowser/nanobrowser)、[Playwright MCP](https://github.com/microsoft/playwright-mcp)、[Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- 已归档：[Puppeteer MCP（servers-archived）](https://github.com/modelcontextprotocol/servers-archived)
- 合规对照：[本目录厂商接入调研](2026-10-23-vendor-onboarding-paths.md)
