# "部署产品本身"现状调研：默认域名、备案与最短路径

2026-10-23｜口径：只认官方文档原文，查不到写"未找到"

## 结论先行

1. **"大厂帮你备好服务器、直接给域名"这一层，在国内被刻意做窄了。** 阿里云、腾讯云都给默认域名，但官方文档无一例外限定为**测试用途**：FC 明令"禁止在生产环境中使用"，SAE 称"并不适宜作为生产环境的访问端点"，CloudBase 会插入"访问提示中间页"（均已核实）。
2. **A 的答案是：没有这样的阿里云产品。** 用户听到的 STE / STIE 在阿里云产品线中**匹配不到任何名称**（未找到）。功能最接近的 ESA"函数和Pages"与 FC"应用中心"默认域名都不能长期公开；计算巢、轻量应用服务器根本不给域名。
3. **这不是能力差异，是备案制度的后果。** CloudBase 原文：默认域名内容"未经前置备案与人工审核，根据监管要求必须履行提醒义务"——**免备案与可长期公开，只能二选一**。
4. **海外默认域名可长期、免费、公开访问，且不强制绑卡。** 五家文档中均未找到"默认域名仅供测试"的限制（"未找到"≠"允许"）。

## A. 阿里云那个产品的真名

| 候选 | 默认域名 | 可否长期公开 |
|---|---|---|
| 函数计算 FC（应用中心） | `*.<region_id>.fcapp.run` | **否**，"禁止在生产环境中使用…只能用于测试环境" |
| SAE | 创建 Web 应用后给默认公网/内网地址 | **否**，"并不适宜作为生产环境中的访问端点" |
| ESA 函数和Pages | 构建后生成"公共域名" | **否**，"仅供测试…需添加 token 鉴权，token 有效期 60 分钟" |
| 计算巢 | 未找到 | 它是服务交付/编排平台，不是托管入口 |
| 云开发 / 云·速成美站 | 未找到 | 速成美站是模板建站，不是"部署你的项目" |
| 轻量应用服务器 | 无，只有公网 IP | 域名须自购＋备案 |

来源：[FC 禁止默认域名用于生产](https://www.alibabacloud.com/help/zh/functioncompute/the-default-public-network-domain-name-of-function-compute-is-not-allowed-in-the-production-environment)、[SAE 自定义域名](https://help.aliyun.com/zh/sae/access-apps-through-a-custom-domain-name)、[ESA 通过模板创建 Pages](https://www.alibabacloud.com/help/zh/edge-security-acceleration/esa/user-guide/create-pages-from-a-template)、[ESA 函数和Pages](https://www.alibabacloud.com/help/zh/edge-security-acceleration/esa/user-guide/what-is-functions-and-pages/)

**判断（未核实）**：可能是 SAE/ESA 的误听，或把腾讯云 CloudBase 记到了阿里云名下。**没有阿里云产品的默认域名可长期公开访问**。

## B. 国内同类产品

**腾讯云 CloudBase（已核实）**：默认域名形如 `*.tcloudbaseapp.com`、`*.run.tcloudbase.com`、`*.app.tcloudbase.com`。浏览器导航请求弹"访问提示中间页"，非导航请求加 `Content-Disposition: attachment`（强制下载）。2025-10-09 后创建的环境限制更多，中间页"长期存在"。移除中间页的**唯一合规途径**是自定义域名，且该域名**必须完成 ICP 备案**。免费额度：每账号一个免费环境、3000 资源点/月、单次续费 6 个月；付费个人版 19.9 元/月起。来源：[默认域名访问限制及中间页](https://docs.cloudbase.net/service/alias)、[HTTP 网关概述](https://docs.cloudbase.net/service/introduce)、[价格文档](https://cloud.tencent.cn/document/product/876/75213)

**轻量应用服务器（阿里云/腾讯云）**：只给公网 IP，无默认域名，必须自购域名＋备案。

**华为云 CAE**：文档要求"进行域名配置前，您需要拥有已购买的域名并且此域名已在工信部备案"；默认域名政策**未核实**（PDF 正文未取全）。来源：[CAE 用户手册](https://support.huaweicloud.com/usermanual-cae/cae-usermanual-pdf.pdf)

**火山引擎 veFaaS、UCloud**：默认域名、免费额度、备案要求**均未核实**——文档为客户端渲染，未取到正文。**不写结论。**

## C. 海外对比

**Vercel Hobby**：免费，Fast Data Transfer 100GB/月、CDN 请求 100 万、函数调用 100 万，超限等 30 天；**仅限非商业个人用途**。**Netlify Free**：100GB 带宽、300 构建分钟、12.5 万函数调用，可商用，官方原话"no credit card required"。**Cloudflare Pages**：静态资源请求"free and unlimited"，500 构建/月、2 万文件、单文件 25MiB。**Railway**：试用 $5/30 天免绑卡，Free 计划 $0 含 $1/月额度，但**自定义域名为 0**。**Render**：免费 Web Service 750 实例小时/月、15 分钟无流量休眠、免费 Postgres **30 天过期**；未绑卡则超限"暂停服务"而非扣费。

来源：[Vercel Hobby](https://vercel.com/docs/plans/hobby)、[Netlify Free](https://www.netlify.com/blog/introducing-netlify-free-plan/)、[Cloudflare Pages 限额](https://developers.cloudflare.com/pages/platform/limits/)、[Cloudflare Pages 定价](https://developers.cloudflare.com/pages/functions/pricing/)、[Railway 定价](https://railway.com/pricing)、[Render 免费额度](https://render.com/docs/free)

**关键差异**：海外拿平台域名直接上线是常态，国内是例外。

## D. 备案规则（官方来源）

**大陆必须备案（已核实）**。《互联网信息服务管理办法》（国务院令第292号）第四条："国家对经营性互联网信息服务实行许可制度；对非经营性互联网信息服务实行备案制度。未取得许可或者未履行备案手续的，不得从事互联网信息服务。"第十九条：未履行备案手续的，"责令限期改正；拒不改正的，责令关闭网站"。来源：[国务院公报原文](https://www.gov.cn/gongbao/content/2000/content_60531.htm)

**香港/海外不需要（已核实）**："（网站/域名）解析指向托管于非中国境内服务器的网站，例如中国香港服务器，则不需要备案。"来源：[是否需要备案](https://cloud.tencent.cn/document/product/243/19630)

**例外（已核实，同源）**：用**国内 CDN**（如腾讯云 CDN）"域名需要进行备案"；域名解析到境内服务器即需备案，"不论设置哪个端口都能被监测到"；域名本身无需备案，但需**实名认证**。

**二级域名/平台子域名（已核实）**：备案以域名为单位，"若二级域名在腾讯云已有备案，则对应的三级域名或四级域名等无需再备案"。平台子域名（如 `*.tcloudbaseapp.com`）不是用户域名，用户无需也无法为其备案——代价是平台标注"未经前置备案与人工审核"，并施加限流、中间页、随时关停。

## E. 静态单页 vs 全站

**纯静态单页**：海外最短 1–3 分钟——拖拽上传或连 Git 即得 `*.pages.dev` / `*.netlify.app`，无需域名、备案、绑卡。国内 3–5 分钟可得 CloudBase 默认域名，但访问者先见中间页；要"干净"上线须加域名＋备案，量级跳到"天"。

**带后端＋数据库的全站**：海外 10–30 分钟（连仓库、开托管 Postgres）；国内 CloudBase 环境＋数据库约 30 分钟–数小时，可做演示但**不能作对外正式入口**；备案周期另计（**具体天数未核实**）。

## 表1 产品对比

| 产品 | 厂商 | 默认域名 | 可否长期公开 | 需备案 | 需绑卡 | 免费额度 | 静态/全站 |
|---|---|---|---|---|---|---|---|
| 函数计算 FC | 阿里云 | `*.fcapp.run` | 否（禁止生产） | 默认不用／自定义要 | 否 | 未核实 | 全站 |
| SAE | 阿里云 | 默认公网地址 | 否（不宜生产） | 同上 | 否 | 未核实 | 全站 |
| ESA 函数和Pages | 阿里云 | 公共域名需 token | 否（60 分钟） | 自定义要 | 否 | 未核实 | 静态＋边缘函数 |
| 轻量应用服务器 | 阿里云/腾讯云 | 无（仅 IP） | — | 要 | 否 | 无 | 全站 |
| CloudBase | 腾讯云 | `*.tcloudbaseapp.com` | 否（中间页＋限流） | 默认不用／自定义要 | 否 | 3000 点/月 | 全站 |
| CAE / veFaaS / UCloud | 华为云/火山/UCloud | 未核实 | 未核实 | 未核实 | 未核实 | 未核实 | 全站 |
| Vercel | Vercel | `*.vercel.app` | 是（限非商业） | 否 | 否 | 100GB/月 | 全站 |
| Netlify | Netlify | `*.netlify.app` | 是 | 否 | 否 | 100GB/月 | 全站 |
| Cloudflare Pages | Cloudflare | `*.pages.dev` | 是 | 否 | 否 | 静态请求无限 | 静态＋函数 |
| Railway | Railway | `*.up.railway.app` | 是（Free 无自定义域名） | 否 | 试用否 | $1/月额度 | 全站 |
| Render | Render | `*.onrender.com` | 是（会休眠） | 否 | 否 | 750 小时/月 | 全站 |

## 表2 小白"立刻看到网站"的最短路径

**大陆（要快，就别追求"正式"）**

1. 静态单页：注册腾讯云 → 实名 → 开通 CloudBase → 静态托管上传 → 得默认域名。**3–5 分钟**，零成本，接受中间页。
2. 全站：CloudBase 建环境 → 开数据库 → 部署云托管/云函数。**30 分钟–数小时**。
3. 要正式对外：买域名 → ICP 备案（数日至数周，**时长未核实**）→ 绑自定义域名。这步无法绕过，也无法代办。

**香港/海外（要正式，就换服务器）**

1. 静态单页：注册 Cloudflare → Pages 上传或连 Git → 得 `*.pages.dev`。**1–3 分钟**，免备案、免绑卡、可长期公开。
2. 全站：Render / Railway 连仓库 → 开 Postgres → 得 `*.onrender.com` / `*.up.railway.app`。**10–30 分钟**，免备案。
3. 要可控：买香港轻量服务器 → 自装环境。**约 1 小时**，仍免备案；但**别接国内 CDN**，否则触发备案。
