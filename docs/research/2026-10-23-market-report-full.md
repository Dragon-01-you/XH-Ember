# “部署产品本身”现状市场调研分析报告

## 核心结论先行

1. **【已核实】“大厂备好服务器、直接给域名部署”在海外是标配，但在中国大陆因合规要求已被全面限制在“测试预览”阶段**：
   - **海外平台**：[Vercel](https://vercel.com/docs/plans/hobby)、[Netlify](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/how-credits-work/)、[Cloudflare Pages](https://developers.cloudflare.com/pages/platform/limits/)、[Railway](https://docs.railway.com/pricing/free-trial)、[Render](https://render.com/docs/free) 均提供免费默认子域名（如 `*.vercel.app`、`*.pages.dev`），**免服务器运维、免备案、免绑信用卡，且可长期公开访问**。
   - **国内云厂商**：受《互联网信息服务管理办法》及“域名持有人须与备案主体一致”约束（[阿里云备案核验要求](https://help.aliyun.com/zh/icp-filing/basic-icp-service/user-guide/prepare-and-check-the-domain-name)），**无任何国内大厂允许平台默认域名在大陆节点长期、正常、无拦截地公开访问**。所有默认域名均设有限制：**① 浏览器访问强制下载文件而非渲染网页**（阿里云 OSS/FC、华为云 OBS、UCloud US3）；**② 极短有效期**（阿里云 ESA Pages 仅 60 分钟、阿里云 FC 应用中心仅 1 天、腾讯云 EdgeOne Pages 仅 3 小时）；**③ 强制安全提示中间页与限流**（腾讯云 CloudBase、华为云 APIG 每日限 1000 次）；**④ 功能下线**（阿里云 SAE Web 应用已于 2026年6月30日下线）。

2. **【已核实】用户所述阿里云产品（音似 `STE / SITE / SAE / STIE`）真名是「阿里云 SAE（Serverless 应用引擎）」或「阿里云 ESA（边缘安全加速 Pages / 站点 Site）」**：
   - **缩写读音最匹配——阿里云 SAE**：曾提供“免配服务器、创建 Web 应用直接给默认公网域名”功能（[SAE 自定义域名文档](https://help.aliyun.com/zh/sae/access-apps-through-a-custom-domain-name)），但已于 **2026年6月30日正式下线 SAE Web 应用功能**（[SAE Web应用下线公告](https://www.aliyun.com/notice/117829)），由函数计算 FC 承接。
   - **功能与 Site 概念最匹配——阿里云 ESA（函数和 Pages）及「云·速成美站」**：ESA Pages 通过 CLI 部署生成公共预览域名，但 token 仅 60 分钟有效（[通过CLI创建Pages](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/create-pages-by-cli)）；云·速成美站（Site）分配临时二级域名预览，正式上线仍须绑自有域名并备案（[万小智AI建站备案](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/use-ali-greetings-instant-mei)）。

## A. 阿里云候选产品逐一核实（绝不猜测）

1. **阿里云 SAE（Serverless 应用引擎）——【已核实：读音与描述最吻合，Web 应用已下线】**
   - **核实结果**：英文缩写 **SAE**（读音 `/es-eɪ-iː/`，极易听成 `STE / STIE`）。控制台创建 SAE Web 应用后直接分配默认公网地址 `<account_id>.<region_id>.sae.aliyuncs.com`，仅限测试开发，生产须绑已备案域名（[通过自定义域名访问应用](https://help.aliyun.com/zh/sae/access-apps-through-a-custom-domain-name)）。
   - **关键变动**：据[【产品变更】SAE Web 应用功能下线通知](https://www.aliyun.com/notice/117829)，2026年1月30日起禁建新 Web 应用，**2026年6月30日正式下线**。现存微服务应用需配 CLB/ALB/NLB 并绑已备案域名（[SAE绑定公网域名](https://help.aliyun.com/zh/sae/how-to-bind-a-public-domain-name-to-an-sae-application)）。免费开通，按 vCPU/内存用量计费，新户享试用额度（[SAE计费说明](https://help.aliyun.com/zh/sae/product-overview/billing-new)）。

2. **阿里云 ESA（边缘安全加速）之「函数和 Pages」——【已核实：对标 Cloudflare Pages，测试域名仅 60 分钟有效】**
   - **核实结果**：缩写 **ESA**（与 SAE 字母相同，核心单元称“站点 **Site**”）。支持 GitHub 导入或 CLI 一键部署静态站、SPA、SSR 与边缘函数（[什么是函数和Pages](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/what-is-functions-and-pages/)）。
   - **默认域名限制**：通过 `esa-cli` 部署会生成公共预览域名，但[官方文档](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/create-pages-by-cli)明确：**“该公共域名仅供测试使用，访问需添加 token 鉴权，token 有效期为 60 分钟。”** 长期访问必须绑定已购套餐并接入 ESA 的自有域名（[配置自定义访问路径](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/trigger)）。边缘函数免费模式每日限 10 万次请求，Pages 消耗站点套餐流量（[ESA计费说明](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/functions-and-pages-billing)）。

3. **阿里云函数计算 FC（含 Web 函数与「应用中心」）——【已核实：默认域名强制下载或仅 1 天有效】**
   - **HTTP 触发器默认域名 `*.<region_id>.fcapp.run`**：浏览器直接访问触发 `Content-Disposition: attachment` **强制下载文件**而非渲染网页（[FC强制下载解决文档](https://help.aliyun.com/zh/functioncompute/return-results-forcibly-downloaded-when-i-access-an-http-function-through-a-browser)），且官方**禁止在生产环境使用默认公网域名**（[禁止生产使用默认域名公告](https://help.aliyun.com/zh/functioncompute/the-default-public-network-domain-name-of-function-compute-is-not-allowed-in-the-production-environment)）。
   - **FC「应用中心」临时测试域名 `***.devsapp.net`**：一键部署 Web 应用时由 Serverless Devs 分配，可直接浏览网页；但自 **2025年3月14日起有效期由 30 天缩短为 1 天**（[devsapp.net有效期变更公告](https://help.aliyun.com/zh/functioncompute/the-temporary-domain-name-devsapp-net-provided-by-the-serverless-devs-community-has-been-changed-from-30-days-to-1-day)）。新户享 CU 试用包与 10GB 免费磁盘，超出按量付费（[FC按量付费](https://help.aliyun.com/zh/functioncompute/pay-as-you-go-billing-methods)）。

4. **阿里云计算巢（Compute Nest）——【已核实：无默认域名】**
   - **核实结果**：面向 ISV 自动化交付软件的 PaaS 平台（[计算巢文档](https://help.aliyun.com/zh/compute-nest/)），底层拉起 ECS/ACK，仅给公网 IP，**无默认二级域名**，只收云资源费（[计算巢计费](https://help.aliyun.com/zh/compute-nest/product-overview/billing-overview)）。

5. **阿里云云开发 / Web+ / 云·速成美站（万小智 AI 建站）——【已核实：速成美站给临时预览域名，上线必备案】**
   - **云·速成美站 / 万小智 AI 建站（对应 `Site`）**：标配云空间，给临时二级域名预览（[部署云·原生建站工具](https://help.aliyun.com/zh/simple-application-server/use-cases/deploy-cloud-native-site-building-tool)），但**正式对外访问前必须绑定自有域名并完成 ICP 备案**（[万小智AI建站备案](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/use-ali-greetings-instant-mei)）。
   - **Web+ / EMAS Serverless / OSS 静态托管**：[Web+](https://www.alibabacloud.com/zh/product/webx?_p_lc=1) 已向 FC/SAE 收敛；[EMAS Serverless](https://help.aliyun.com/zh/document_detail/436030.html) 已下线；[OSS 静态网站托管](https://help.aliyun.com/zh/oss/user-guide/hosting-static-websites)默认域名访问 HTML **浏览器强制下载**，须绑自定义域名。

6. **阿里云轻量应用服务器（SAS）——【已核实：给公网 IP，无默认域名】**
   - **核实结果**：预装应用镜像，仅给固定公网 IP，**无默认域名**（[轻量服务器须知](https://help.aliyun.com/zh/simple-application-server/product-overview/usage-notes)）。大陆实例绑域名必备案（[轻量ICP备案详解](https://help.aliyun.com/zh/simple-application-server/user-guide/apply-for-an-icp-filing-for-a-domain-name)）；香港/海外实例免备案。

7. **是否存在缩写为 `STE / STIE` 的其他阿里云产品？——【未找到】**
   - 检索[阿里云文档中心](https://help.aliyun.com/)全量目录，未找到名为 `STE` 或 `STIE` 的产品；用户所述确认为 **SAE**、**ESA (Site)** 或 **云·速成美站 (Site)**。

## B. 国内同类产品对比

1. **腾讯云 CloudBase（云开发）——【已核实】**
   - **默认域名与限制**：自动生成免费默认域名（`*.tcloudbaseapp.com` 等）（[CloudBase 概述](https://docs.cloudbase.net/service/introduce)）。自 2025年10月9日起，默认域名**长期强制展示“访问提示中间页”**且限频，生产使用易触发风控关停，须绑已备案自定义域名（[默认域名访问限制及中间页](https://docs.cloudbase.net/service/alias)）。
   - **免费额度与计费**：2026年1月16日起，每账号可建 **1 个长期免费体验环境（3000 资源点/月 + 1GB 静态空间，每 6 个月手动免费续期，用完即停）**。个人版 19.9 元/月，超限按量计费（调用 0.5元/万次/天、存储流量 0.21元/GB、出网流量 0.8元/GB）（[CloudBase 价格文档](https://cloud.tencent.com/document/product/876/75213)）。
   - **腾讯云 EdgeOne Pages（现 EdgeOne Makers）**：分配 `*.edgeone.app` 预览域名，但**仅提供 3 小时限时预览**，长期访问须绑自定义域名（[EdgeOne 简介](https://cloud.tencent.com/document/product/1552/127366)、[EdgeOne Pages 说明](https://cloud.tencent.com/developer/article/2512243)）。

2. **腾讯云轻量应用服务器（Lighthouse）——【已核实】**
   - **默认域名与备案**：仅给公网 IP，**无默认域名**（[轻量常见问题](https://cloud.tencent.com/document/product/1207/44569)）。大陆地域绑域名须购满 3 个月并备案（[ICP备案云资源](https://cloud.tencent.com/document/product/243/18908)）；香港/海外或纯 IP 访问免备案。包月计费（含流量包，超额约 0.8元/GB）。

3. **华为云（FunctionGraph + APIG / CAE / OBS）——【已核实】**
   - **默认域名与限制**：[OBS 静态托管](https://support.huaweicloud.com/usermanual-obs/obs_03_0032.html)默认桶域名访问 HTML **强制下载**；[API 网关 APIG](https://support.huaweicloud.com/productdesc-apig/apig-pd-180307005.html) 调试子域名**禁用于生产且每日限访问 1000 次**；[云应用引擎 CAE](https://support.huaweicloud.com/intl/zh-cn/usermanual-cae/cae_03_0057.html) 仅给 ELB 公网 IP。大陆绑域名必备案。
   - **免费额度与计费**：FunctionGraph 每月 **100 万次免费请求 + 400,000 GB-秒执行时间**，超出按需计费（[FunctionGraph 免费额度](https://support.huaweicloud.com/price-functiongraph/functiongraph_00_0012.html)）。

4. **火山引擎（veFaaS + API 网关 / 对象存储 TOS）——【已核实】**
   - **默认域名与备案**：veFaaS 需挂 API 网关（[函数服务托管静态站点](https://www.volcengine.com/docs/6662/760348)）；[专用服务条款](https://www.volcengine.com/docs/vefaas/Service-SpecificTermsforVolcanoEngineFunctionasaService)禁止未备案域名接入；[对象存储 TOS](https://www.volcengine.com/docs/6349/128983) 正式建站须绑已备案域名。
   - **免费额度与计费**：API 网关调用每月前 **100 万次免费**，超出 0.03~0.06元/万次，实例费与出网流量（0.8元/GB）另计（[API网关计费](https://docs.volcengine.com/docs/apig/Billingoverview-13?lang=zh)）。

5. **UCloud 优刻得（轻量 ULightHost / 对象存储 US3）——【已核实】**
   - **默认域名与限制**：[ULightHost](https://docs.ucloud.cn/ulhost/introduction/Overview) 仅给公网 IP；[US3 静态网站托管](https://docs.ucloud.cn/ufile/guide/static_websit_hosring)默认域名访问**会将静态网站以文件形式下载**，须绑已备案自定义域名（[US3 域名管理](https://docs.ucloud.cn/ufile/guide/domain)）。按包月或按量计费。

## C. 海外主流平台对比

1. **Vercel (Hobby Plan)——【已核实】**
   - **默认域名**：免费 `<project>.vercel.app`（自带 HTTPS，长期公开，限个人非商业用途）。
   - **免费额度与绑卡**：每月 **100GB 流量、100万次请求、100万次函数调用、4 CPU-小时**；超额暂停最多 30 天不扣费；**免绑信用卡**（[Vercel Hobby Plan](https://vercel.com/docs/plans/hobby)、[Limits](https://vercel.com/docs/limits)）。

2. **Netlify (Free Credit-based Plan)——【已核实】**
   - **默认域名**：免费 `<project>.netlify.app`（自带 HTTPS，长期公开）。
   - **免费额度与绑卡**：每月 **300 credits 硬上限**（生产部署 15/次，预览部署免费，流量 20/GB 约合 15GB，请求 2/万次）；用尽暂停站点至下月重置，无超额账单；**免绑信用卡**（[How credits work](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/how-credits-work/)、[Billing FAQ](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-legacy-plans/billing-faq-for-legacy-plans/)）。

3. **Cloudflare Pages (Free Plan)——【已核实】**
   - **默认域名**：免费 `<project>.pages.dev`（自带 HTTPS，长期公开）（[Custom domains](https://developers.cloudflare.com/pages/configuration/custom-domains/)）。
   - **免费额度与绑卡**：**静态请求与带宽无限免费**；月 500 次构建、单站 20,000 文件（≤25 MiB）；Functions 共享 Workers **每日 10 万次免费调用**；**免绑信用卡**（[Limits](https://developers.cloudflare.com/pages/platform/limits/)、[Pricing](https://developers.cloudflare.com/pages/functions/pricing/)）。

4. **Railway (Free Trial & Free Plan)——【已核实】**
   - **默认域名**：免费 `*.up.railway.app`（额度内长期公开）。
   - **免费额度与绑卡**：支持免注册 `ssh railway.new` 体验 24 小时；注册享 **30 天 $5 试用金**；之后转 **Free Plan 每月赠 $1 额度**（限 0.5GB RAM，用完即停）；**免绑信用卡**（新 GitHub 号处于受限试用，升级 $5/月 Hobby 需绑卡）（[Free Trial](https://docs.railway.com/pricing/free-trial)、[Pricing](https://railway.com/pricing)）。

5. **Render (Free Tier)——【已核实】**
   - **默认域名**：免费 `<service>.onrender.com`（自带 HTTPS，长期公开）。
   - **免费额度与绑卡**：静态站免费（100GB带宽）；免费 Web 服务每月 **750 小时**（**闲置 15 分钟休眠**，唤醒约 1 分钟，临时磁盘重启丢数据）；免费 PG 数据库 **1GB 且 30 天强制过期**；**免绑信用卡**（超额暂停不扣费）（[Deploy for Free](https://render.com/docs/free)）。

## D. 备案规则与官方依据

1. **中国大陆服务器的网站公开访问是否必须 ICP 备案？——【已核实：必须备案】**
   - **结论**：域名解析指向中国内地服务器并开通 Web 访问，**必须先完成工信部 ICP 备案**（纯公网 IP 访问不用域名则无需 ICP 备案，见[阿里云备案FAQ](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/faq-about-icp-filing-applications-in-different-scenarios)）。
   - **法律依据**：《互联网信息服务管理办法》（国务院令第 292 号，[司法部国家行政法规库](https://xzfg.moj.gov.cn/front/law/detail?LawID=1756)）第四条规定“**未取得许可或者未履行备案手续的，不得从事互联网信息服务**”；《非经营性互联网信息服务备案管理办法》（原信息产业部令第 33 号，[司法部公报](https://www.moj.gov.cn/pub/sfbgw/flfggz/flfggzbmgz/200504/t20050412_143918.html)、[中国网信网](https://www.cac.gov.cn/2005-02/09/c_1112147171.htm)）第五条规定“**未经备案，不得在中华人民共和国境内从事非经营性互联网信息服务**”。

2. **香港 / 海外服务器是否不需要备案？有没有例外？——【已核实：免 ICP 备案，有 3 个例外】**
   - **结论**：服务器位于中国香港、澳台及海外，**无需工信部 ICP 备案**（[腾讯云是否需要备案](https://cloud.tencent.com/document/product/243/19630)）。
   - **三大例外**：**① 接入中国内地 CDN / 全站加速（如 ESA/EdgeOne 国内节点）或 WAF**，必须完成 ICP 备案；**② 公安联网备案**，[阿里云备案FAQ](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/faq-about-icp-filing-applications-in-different-scenarios)明确“托管于非中国内地服务器的网站无需工信部备案但需完成公安联网备案”；**③ 微信小程序/公众号业务域名**，强制要求具备工信部 ICP 备案号。

3. **“二级域名”或平台子域名在国内是否需要单独备案？——【已核实】**
   - **自有主域名的二级域名**：主域名已在同一云厂商备案，子域名直接解析**无需单独备案**；若子域名指向其他云厂商，须办理**“新增接入备案”**（`.gov.cn` 二级域名须单独备案）（[阿里云备案FAQ](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/faq-about-icp-filing-applications-in-different-scenarios)、[华为云二级域名备案FAQ](https://support.huaweicloud.com/icp_faq/icp_05_0007.html)）。
   - **平台公共子域名（如 `xxx.tcloudbaseapp.com`）**：主域名归云厂商所有，普通用户**无法拿平台域名办个人备案**（违反“域名持有人与备案主体一致”要求，见[阿里云域名核验](https://help.aliyun.com/zh/icp-filing/basic-icp-service/user-guide/prepare-and-check-the-domain-name)），云厂商为规避连带责任均采取强制下载、限时过期或中间页限制。

## E. 静态单页 vs 全站：最短上线路径

1. **纯静态单页**：香港/海外用 [Cloudflare Pages](https://developers.cloudflare.com/pages/platform/limits/) 或 [Vercel](https://vercel.com/docs/plans/hobby) 上传静态包，**1 ~ 3 分钟**永久公开上线；中国大陆临时预览用 [CloudBase](https://docs.cloudbase.net/hosting/manage)（带中间页）或轻量服务器纯 IP 约 **3 ~ 5 分钟**，正式自定义域名须等待域名实名（2~3 天）+ ICP 备案（**3 ~ 20 个工作日**）（[阿里云ICP备案流程](https://help.aliyun.com/zh/icp-filing/basic-icp-service/user-guide/icp-filing-application-overview)）。
2. **带后端 + 数据库全站**：香港/海外用 [Railway](https://docs.railway.com/pricing/free-trial) / [Render](https://render.com/docs/free) 或阿里云/腾讯云**香港轻量服务器**（预装镜像），**5 ~ 15 分钟**长期公开上线；中国大陆用 [CloudBase](https://docs.cloudbase.net/service/introduce) 测试域或大陆轻量纯 IP 约 **5 ~ 15 分钟**预览，正式域名同样需 **5 ~ 20 个工作日**备案。

## 综合对比总表

### 表 1：产品对比表

| 产品 | 厂商 | 默认域名 | 可否长期公开 | 是否需备案 | 是否需绑卡 | 免费额度 | 静态/全站 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [Serverless 应用引擎 SAE](https://help.aliyun.com/zh/sae/access-apps-through-a-custom-domain-name) | 阿里云 | 曾给 `*.sae.aliyuncs.com`（[Web应用已下线](https://www.aliyun.com/notice/117829)） | 否（Web应用已下线；微服务需配SLB+自有域名） | 大陆绑域名必备案；香港/海外免备案 | 否（需实名） | 新户试用额度，超出按vCPU/内存计费（[计费](https://help.aliyun.com/zh/sae/product-overview/billing-new)） | 全站/微服务 |
| [边缘安全加速 ESA Pages](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/what-is-functions-and-pages/) | 阿里云 | CLI 生成公共测试域名（[带token](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/create-pages-by-cli)） | 否（token 仅 **60 分钟有效**，正式需绑自有域名） | 接入大陆节点必备案；纯境外免备案 | 否（需实名） | 边缘函数每日 **10万次免费请求**（[计费](https://help.aliyun.com/zh/edge-security-acceleration/esa/user-guide/functions-and-pages-billing)） | 静态+边缘函数 |
| [函数计算 FC (含应用中心)](https://help.aliyun.com/zh/functioncompute/configure-custom-domain-names) | 阿里云 | `*.fcapp.run` / `*.devsapp.net` | 否（`fcapp.run` [强制下载且禁用于生产](https://help.aliyun.com/zh/functioncompute/the-default-public-network-domain-name-of-function-compute-is-not-allowed-in-the-production-environment)；`devsapp.net` [仅1天有效](https://help.aliyun.com/zh/functioncompute/the-temporary-domain-name-devsapp-net-provided-by-the-serverless-devs-community-has-been-changed-from-30-days-to-1-day)） | 大陆节点绑自定义域名必备案 | 否（需实名） | 首开赠 CU 试用包+10GB免费磁盘（[计费](https://help.aliyun.com/zh/functioncompute/billing-overview-of-fc)） | 静态/全站 |
| [计算巢 Compute Nest](https://help.aliyun.com/zh/compute-nest/) | 阿里云 | 无（仅分配 ECS/SLB 公网 IP） | 公网 IP 可长期公开；域名需自购 | 大陆绑域名必备案；纯 IP 免备案 | 否（需实名） | 免平台费，收底层云资源费（[计费](https://help.aliyun.com/zh/compute-nest/product-overview/billing-overview)） | 全站(含数据库) |
| [云·速成美站 / 万小智](https://help.aliyun.com/zh/document_detail/147158.html) | 阿里云 | 有（临时测试二级域名） | 否（[正式上线前必须绑自有域名并备案](https://help.aliyun.com/zh/icp-filing/basic-icp-service/product-overview/use-ali-greetings-instant-mei)） | 大陆节点必备案；香港节点免备案 | 否（预付费） | 无永久免费版（按年付费） | 模板全站 |
| [对象存储 OSS (静态托管)](https://help.aliyun.com/zh/oss/user-guide/hosting-static-websites) | 阿里云 | `*.aliyuncs.com` | 否（默认域名访问 HTML **强制下载**） | 大陆绑域名必备案；香港免备案 | 否（需实名） | 新户试用包，按存储+流量计费 | 纯静态 |
| [阿里云轻量服务器 SAS](https://help.aliyun.com/zh/simple-application-server/product-overview/usage-notes) | 阿里云 | 无（仅给固定公网 IP） | 公网 IP 或绑域名均可长期公开 | 大陆绑域名**必备案**；纯IP/香港**免备案** | 否（支付宝等） | 新户短期试用，包月约24元/月起 | 静态/全站 |
| [腾讯云开发 CloudBase](https://docs.cloudbase.net/service/introduce) | 腾讯云 | `*.tcloudbaseapp.com` 等 | 否（仅供测试，[长期有安全中间页且限频](https://docs.cloudbase.net/service/alias)） | 绑定自定义域名必备案 | 否（需实名） | **1个免费环境（3000点/月+1GB静态空间，每半年可续期）**（[价格](https://cloud.tencent.com/document/product/876/75213)） | 静态+全站(含DB) |
| [腾讯云 EdgeOne Pages](https://cloud.tencent.com/document/product/1552/127366) | 腾讯云 | `*.edgeone.app` | 否（默认预览链接仅 **3 小时限时预览**） | 大陆节点必备案；纯海外节点免备案 | 否（需实名） | 提供免费版套餐 | 静态+边缘函数 |
| [腾讯云轻量 Lighthouse](https://cloud.tencent.com/document/product/1207/44569) | 腾讯云 | 无（仅给固定公网 IP） | 公网 IP 或绑域名均可长期公开 | 大陆绑域名**必备案**；纯IP/香港**免备案** | 否（微信等） | 新户短期试用，按月预付费 | 静态/全站 |
| [华为云 FunctionGraph/OBS](https://support.huaweicloud.com/price-functiongraph/functiongraph_00_0012.html) | 华为云 | APIG 调试域 / OBS 默认域 | 否（APIG [日限1000次禁生产](https://support.huaweicloud.com/productdesc-apig/apig-pd-180307005.html)；OBS [强制下载](https://support.huaweicloud.com/usermanual-obs/obs_03_0032.html)） | 大陆节点绑自定义域名必备案 | 否（需实名） | FG 每月 **100万次请求+40万GB-秒免费** | 静态/全站 |
| [火山引擎 veFaaS/APIG/TOS](https://www.volcengine.com/docs/6662/760348) | 火山引擎 | APIG 域名 / TOS 默认域 | 否（国内正式 Web 服务须绑已备案域名） | 大陆节点必备案（[服务条款](https://www.volcengine.com/docs/vefaas/Service-SpecificTermsforVolcanoEngineFunctionasaService)） | 否（需实名） | APIG 每月前 **100万次调用免费**（[计费](https://docs.volcengine.com/docs/apig/Billingoverview-13?lang=zh)） | 静态/全站 |
| [UCloud 轻量 ULightHost/US3](https://docs.ucloud.cn/ulhost/introduction/Overview) | UCloud | 轻量无；US3 给默认桶域名 | 轻量 IP 可长期公开；US3 默认域[强制下载](https://docs.ucloud.cn/ufile/guide/static_websit_hosring) | 大陆绑域名**必备案**；纯IP/香港**免备案** | 否（国内支付） | 无永久免费层，包月或按量计费 | 静态/全站 |
| [Vercel (Hobby Plan)](https://vercel.com/docs/plans/hobby) | Vercel | `<project>.vercel.app` | **是**（长期公开，限非商业个人用途） | **否**（免备案） | **否**（免绑卡） | 每月 **100GB流量、100万次请求/函数调用**（[Limits](https://vercel.com/docs/limits)） | 静态+Serverless全栈 |
| [Netlify (Free Plan)](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/how-credits-work/) | Netlify | `<project>.netlify.app` | **是**（长期公开，含免费 HTTPS） | **否**（免备案） | **否**（免绑卡） | 每月 **300 credits 硬上限**（用尽暂停不扣费） | 静态+Serverless+DB |
| [Cloudflare Pages (Free)](https://developers.cloudflare.com/pages/platform/limits/) | Cloudflare | `<project>.pages.dev` | **是**（长期公开，全球边缘网络） | **否**（免备案） | **否**（免绑卡） | **静态流量无限免费**；Functions **日10万次免费**（[Pricing](https://developers.cloudflare.com/pages/functions/pricing/)） | 静态+全栈(含D1数据库) |
| [Railway (Trial / Free)](https://docs.railway.com/pricing/free-trial) | Railway | `*.up.railway.app` | **是**（额度内长期公开） | **否**（免备案） | **否**（免绑卡） | 新户 **30天$5试用**；后 **每月赠$1额度**（用完即停） | 全站(容器+后端+DB) |
| [Render (Free Tier)](https://render.com/docs/free) | Render | `<service>.onrender.com` | **是**（静态常开；免费后端闲置15分钟休眠） | **否**（免备案） | **否**（免绑卡） | 静态免费(100GB)；Web **月750小时**；免费PG **30天过期** | 静态+全站(容器+PG) |

### 表 2：中国小白“想立刻看到自己网站”的最短路径表（大陆 vs 香港/海外）

| 区域路线 | 项目类型 | 最短上线路径（推荐底座） | 操作步骤 | 端到端耗时 | 最终网址形态 | 成本与卡点说明 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **香港/海外**<br>（免备案） | **纯静态单页** | **[Cloudflare Pages](https://developers.cloudflare.com/pages/platform/limits/)** 或 **[Vercel](https://vercel.com/docs/plans/hobby)** | 免绑卡注册并上传静态文件夹或连 GitHub，自动签发 HTTPS 子域名 | **1 ~ 3 分钟** | `https://xxx.pages.dev`<br>`https://xxx.vercel.app` | **0 元、免买域名、免绑卡、免备案**，长期公开有效。 |
| **香港/海外**<br>（免备案） | **带后端+数据库全站** | **0元：[Railway](https://docs.railway.com/pricing/free-trial) / [CF Pages+D1](https://developers.cloudflare.com/pages/functions/pricing/)**<br>**稳定：阿里/腾讯/UCloud 香港轻量** | Railway 连仓库+一键建库分配域名；香港轻量用微信/支付宝开机一键拉起全栈容器 | **5 ~ 15 分钟** | `https://xxx.up.railway.app`<br>或 `http://<香港IP>`（可绑未备案域名） | Railway 享 $5 试用及月 $1 免费额；香港轻量约 **24~30 元/月**（免外币卡、免备案、国内访问快）。 |
| **中国大陆**<br>（需备案） | **纯静态单页** | **阶段1：[腾讯云 CloudBase](https://docs.cloudbase.net/hosting/manage)** 或 **大陆轻量纯IP**<br>**阶段2：买域名 + ICP 备案** | 扫码实名认证后上传静态包，先用测试域名（带中间页）或纯 IP 预览；后台买域名并提交管局备案 | - 预览/纯IP：**3 ~ 5 分钟**<br>- 正式域名：**5 ~ 20 工作日** | - 立刻看：`https://xxx.tcloudbaseapp.com` 或 `http://<大陆IP>`<br>- 备案后：`https://自有域名` | CloudBase 享 0 元免费环境，但默认域有安全中间页；大陆节点**无法绕过备案获得无拦截正式域名**。 |
| **中国大陆**<br>（需备案） | **带后端+数据库全站** | **阶段1：[腾讯云 CloudBase](https://docs.cloudbase.net/service/introduce)** 或 **大陆轻量服务器**<br>**阶段2：绑定已备案自有域名** | 实名认证后一键部署全栈代码与数据库，立即用公网 IP 或测试域预览；备案通过后绑自有域名 | - 预览/纯IP：**5 ~ 15 分钟**<br>- 正式域名：**5 ~ 20 工作日** | - 立刻看：`https://xxx.service.tcloudbase.com` 或 `http://<大陆IP>:端口`<br>- 备案后：`https://自有域名` | 大陆让小白“10分钟看到全站”的唯一无拦截方式是**先给大陆轻量纯公网 IP（或 CloudBase 测试域），异步引导备案**。 |
