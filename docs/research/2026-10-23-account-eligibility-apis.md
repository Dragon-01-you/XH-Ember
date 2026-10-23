# 账号"资质与优惠状态"能否用官方 API 读到

> **结论先行**：能读到"券"和"实名"，读不到"资格"。优惠券的余额/可用性/适用范围/过期时间，阿里云有完整官方接口；实名认证与个人/企业主体类型，只有腾讯云有直接接口；而**学生认证是否通过、学生优惠是否已用完、新用户与免费试用资格**，八家厂商全部**未找到**公开 API。综合覆盖约 **35%**（券类 ~80%，实名类 ~30%，资格类 0）。

调研时间：2026-10-23。仅记录官方文档写明的内容；未查到的一律写"未找到"，不臆造接口名。

## 一、已核实

### 阿里云

- **优惠券**：BSS OpenAPI 2023-09-30 的 [`DescribeCoupon`](https://www.alibabacloud.com/help/en/user-center/developer-reference/api-bssopenapi-2023-09-30-describecoupon)（券列表）与 `DescribeCouponItemList`（券可用商品）。返回面额、剩余金额、状态、生效/过期时间、适用范围、适用订单类型——正好覆盖需求里的"余额、可用性、范围、过期"。RAM 权限 `bss:DescribeCoupon`（list 级，资源 `*`），凭证用 AccessKey（建议 RAM 用户）。
- **资金/订单**：`GetFundAccountAvailableAmount`、`GetOrders` 可辅助判断是否已消费。
- **实名认证 / 学生认证 / 新用户与试用资格**：**未找到**。该版本[接口总览](https://www.alibabacloud.com/help/en/user-center/developer-reference/api-bssopenapi-2023-09-30-overview)只有订单、发票、资金、优惠券、账单、财务单元六类，无账号资质类接口。

### 腾讯云

- **实名认证 + 主体类型**：`ba` 产品 [`DescribeGetAuthInfo`](https://cloud.tencent.com/document/api/243/59312)（2020-07-20）。返回 `IsTenPayMasked`（实名状态）、`IsAuthenticated`/`Type`（个人/企业）、`Level`（大客户标识）。凭证：SecretId/SecretKey（CAM）。
- **代金券**：计费产品 [`DescribeVoucherInfo`](https://intl.cloud.tencent.com/document/api/555/45893) 文档存在，用于查询代金券信息；返回字段未逐项核实。
- **学生认证 / 优惠是否已用**：**未找到**。
- 易混点：[`ess` 的 `DescribeUserVerifyStatus`](https://cloud.tencent.com/document/api/1323/106080) 是电子签 C 端用户实名，不是云账号资质。

### 华为云

- 仅核实到**合作伙伴中心** [`ListSubCustomerCoupons`](https://www.huaweicloud.com/guide/productsdesc-bms_8b7c493bc07190aec1d0728a256931e2support0)（查询子客户优惠券），面向伙伴/经销商。
- 普通账号自助查券、实名认证、学生认证：**未找到**公开接口。

### 海外 SaaS

- **Vercel**：有 [Billing API](https://vercel.com/docs/rest-api/billing/list-focus-billing-charges)（FOCUS 账单、合同承诺、购买 credits），能读消费，读不到当前 plan、试用/学生资格 → **未找到**。
- **Cloudflare**：[`GET /user/billing/profile`](https://developers.cloudflare.com/api/resources/user/subresources/billing/)（账单档案）、[`GET /accounts/{id}/subscriptions`](https://developers.cloudflare.com/api/resources/accounts/subresources/subscriptions/)（订阅 rate plan，可间接看付费套餐），Token 需 Billing Read。credits、试用/优惠资格 → **未找到**。
- **Railway / Render / Supabase / Neon**：均有项目与用量类 API（[Neon Consumption](https://neon.com/docs/reference/api/consumption)、[Supabase Management API](https://supabase.com/docs/reference/api/introduction)），但当前 plan、credits、试用与优惠资格 → **未找到**。

### CLI / MCP / Skill

`aliyun` CLI、`tccli` 可直接调用上述任一 OpenAPI（含 `bss:DescribeCoupon`、`ba:DescribeGetAuthInfo`），但**没有专门的"资质查询"命令**；官方 MCP / Skill 中封装资质与优惠券能力的，**未找到**。

## 二、汇总表

| 厂商 | 资质项 | API 可读 | 接口/文档 | 凭证 |
|---|---|---|---|---|
| 阿里云 | 券余额/可用/范围/过期 | ✅ | `DescribeCoupon` | AK + `bss:DescribeCoupon` |
| 阿里云 | 实名认证、个人/企业 | ❌ 未找到 | — | — |
| 阿里云 | 学生认证、优惠已用、新用户/试用 | ❌ 未找到 | — | — |
| 腾讯云 | 实名 + 个人/企业 + 大客户标识 | ✅ | `DescribeGetAuthInfo` | SecretId/Key |
| 腾讯云 | 代金券信息 | ⚠️ 接口存在，字段未核实 | `DescribeVoucherInfo` | SecretId/Key |
| 腾讯云 | 学生认证、优惠已用 | ❌ 未找到 | — | — |
| 华为云 | 子客户优惠券 | ⚠️ 仅伙伴侧 | `ListSubCustomerCoupons` | 伙伴 AK/SK |
| 华为云 | 实名、学生、试用 | ❌ 未找到 | — | — |
| Vercel | 账单/合同承诺 | ⚠️ 部分 | FOCUS Billing Charges | Bearer Token |
| Vercel | plan、试用、credits 资格 | ❌ 未找到 | — | — |
| Cloudflare | 订阅套餐、账单档案 | ⚠️ 部分 | subscriptions / billing profile | Token(Billing Read) |
| Cloudflare | credits、试用资格 | ❌ 未找到 | — | — |
| Railway/Render/Supabase/Neon | 项目与用量 | ⚠️ 部分 | 各家官方 API | API Token |
| 同上 | plan、credits、试用资格 | ❌ 未找到 | — | — |

## 三、最终判断

**"判断某用户某个优惠还能不能用"，官方 API 只能覆盖约 35%**：券的静态属性（面额、余额、有效期、适用商品）在阿里云可完全覆盖，腾讯云需再核实字段；实名与主体类型只在腾讯云可读。

**必须靠浏览器页面才能看到的**：①学生认证是否通过、学生优惠是否已使用/用完；②新用户资格与免费试用资格；③券对"这一单"的实时可用判定（接口给范围，不给风控结论）；④Vercel/Cloudflare/Railway/Render/Supabase/Neon 的 trial 剩余、credits 余额与优惠资格。这些在官方文档中均无对应接口，只能读控制台。
