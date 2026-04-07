---
name: industry-tools
description: "行业工具说明书 — 列出并指导使用各行业的 CLI/API 工具。当用户提到销售运营、跨境电商、广告投放、投研金融、客服运营、法务助理、税务财务等行业场景,或询问某个行业有哪些工具、怎么安装、怎么认证时使用。"
---

# Industry Tools — 行业工具说明书

当用户进入某个行业场景时,你需要:

1. **识别行业** — 根据用户的问题判断属于哪个行业
2. **列出工具** — 读本地 `references/<domain>.md` 展示工具清单
3. **展示详情** — 用户询问具体工具时,从 GitHub 拉取该工具的独立文档
4. **指导操作** — 指导安装配置、环境变量设置、验证连接

## 工具详情文档获取

本地 references 分两层：
- `references/<domain>.md` — 工具清单索引（先读这个）
- `references/docs-<domain>/<tool-id>.md` — 每个工具的详细文档（按需读取）

**使用 `read` 加载**，示例：
```
read("references/docs-sales-ops/salesforce-cli.md")
```

### 文档路径映射

| 行业 | 索引 | 详情目录 |
|------|------|------------|
| 销售运营 | `references/sales-ops.md` | `references/docs-sales-ops/<tool-id>.md` |
| 跨境电商 | `references/cross-border-ecommerce.md` | `references/docs-cross-border-ecommerce/<tool-id>.md` |

tool-id 就是工具清单里每个工具对应的文件名（如 salesforce-cli、apollo-api、shopify-cli）。

> GitHub 同步地址：https://github.com/AnyGenIO/anygen-solution-spec/tree/main/docs

---

## 支持的行业

| 行业 | 文件 | 关键词 |
|------|------|--------|
| 销售运营 | `references/sales-ops.md` | CRM、线索、SDR、AE、商机、Pipeline、Salesforce、HubSpot |
| 跨境电商 | `references/cross-border-ecommerce.md` | Shopify、Amazon、TikTok Shop、速卖通、选品、上架 |
| 广告投放 | `references/ad-ops.md` | Google Ads、Meta、Facebook、TikTok广告、ROAS、Campaign |
| 投研金融 | `references/investment-research.md` | 股票、基金、A股、美股、行情、K线、投资 |
| 客服运营 | `references/customer-support.md` | 工单、客服、Zendesk、退款、SLA |
| 法务助理 | `references/legal-assistant.md` | 合同审查、法规、裁判文书、法务 |
| 税务财务 | `references/tax-finance.md` | 增值税、发票、记账、VAT、金蝶、用友 |

---

## 核心流程

### 1. 用户提到某个行业 → 路由到对应文档

**示例**:
- 用户: "我想做销售运营,有哪些工具?"
- 你: 读取 `references/sales-ops.md`,列出工具清单

### 2. 列出工具清单(表格形式)

展示格式:

| 工具名称 | 用途 | 安装方式 |
|----------|------|----------|
| Salesforce CLI | 企业级CRM | `npm install -g @salesforce/cli` |
| HubSpot CLI | 一体化CRM+营销 | `npm install -g @hubspot/cli` |
| Apollo.io API | 2.75亿联系人数据库 | HTTP API调用 |

### 3. 用户询问某个具体工具 → 展示详情

**示例**:
- 用户: "怎么配 Salesforce?"
- 你: 从 `references/sales-ops.md` 找到 Salesforce CLI 段落,展示:
  - 安装命令
  - 认证方式(OAuth)
  - 环境变量列表
  - 如何获取凭证
  - 验证命令
  - 使用场景
  - 限制和注意事项

### 4. 用户想用某个工具 → 操作指导

**示例**:
- 用户: "帮我安装 Shopify CLI"
- 你: 
  1. 给出安装命令: `npm install -g @shopify/cli`
  2. 指导认证: `shopify auth login`
  3. 说明需要的环境变量
  4. 提供验证命令: `shopify auth token`

---

## Progressive Disclosure 原则

- **先概览** → 列出所有工具,让用户知道有什么
- **再详情** → 用户询问具体工具时,展示该工具的完整信息
- **再操作** → 用户想用时,给出具体的安装和配置步骤

**不要一次性把所有信息都扔给用户**。

---

## 行业路由规则

根据用户的问题关键词,路由到对应行业:

### 销售运营 (sales-ops.md)
**关键词**: CRM、线索、销售、商机、Pipeline、Salesforce、HubSpot、SDR、AE、客户管理、签约、佣金、合同、电子签、收款、订阅、报价、通话录音、预测、预约、冷邮件、触达

### 跨境电商 (cross-border-ecommerce.md)
**关键词**: Shopify、Amazon、亚马逊、TikTok Shop、TikTok小店、抖店、速卖通、选品、上架、listing、FBA、跨境、电商、带货、开店

### 广告投放 (ad-ops.md)
**关键词**: Google Ads、Meta、Facebook广告、Instagram广告、TikTok广告、投放、ROAS、CPA、Campaign、广告优化

### 投研金融 (investment-research.md)
**关键词**: 股票、基金、A股、美股、港股、行情、K线、研报、投资

### 客服运营 (customer-support.md)
**关键词**: 工单、客服、Zendesk、Intercom、物流查询、退款、SLA

### 法务助理 (legal-assistant.md)
**关键词**: 合同、合同审查、法规、法律、裁判文书、电子签约、法务

### 税务财务 (tax-finance.md)
**关键词**: 增值税、发票、记账、财务、税务申报、VAT、金蝶、用友

---

## 使用场景示例

### 场景 1: 用户询问某个行业有什么工具

**用户**: "销售有什么工具?"

**你的回答**:
1. 读取 `references/sales-ops.md`
2. 列出工具清单(表格形式):

| 类别 | 工具名称 | 用途 |
|------|----------|------|
| CRM平台 | Salesforce CLI | 企业级CRM,覆盖Sales/Service/Marketing Cloud |
| CRM平台 | HubSpot CLI | 一体化CRM+营销+服务平台 |
| 线索获取 | Apollo.io API | 2.75亿联系人数据库,找邮箱/电话 |
| ... | ... | ... |

3. 询问用户对哪个工具感兴趣

---

### 场景 2: 用户询问如何使用某个工具

**用户**: "怎么配 Salesforce?"

**你的回答**:
1. 从 `references/sales-ops.md` 找到 Salesforce CLI 部分
2. 展示:

**Salesforce CLI (sf)**

**安装**:
```bash
npm install -g @salesforce/cli
```

**认证方式**: OAuth

**环境变量**(CI场景):
- `SF_ACCESS_TOKEN`
- `SF_INSTANCE_URL` (如 https://yourorg.my.salesforce.com)

**如何获取**:
1. Setup → Apps → Connected Apps → 创建OAuth App

**验证**:
```bash
sf org list
```

**使用场景**:
- 查询/更新CRM中的线索、商机、客户
- 批量导入线索
- 推进商机阶段,记录通话/会议

**限制**:
- API调用日限额(Professional 1000次/天,Enterprise 10万次/天)

---

### 场景 3: 用户想安装工具

**用户**: "帮我安装 Shopify CLI"

**你的回答**:
```bash
npm install -g @shopify/cli
```

安装完成后,运行以下命令登录:
```bash
shopify auth login
```

这会打开浏览器让你授权 Shopify 店铺。

验证安装:
```bash
shopify auth token
```

**需要什么?**
- Node.js 环境
- Shopify 店铺账号

**下一步**:
- 管理产品: `shopify app dev`
- 查看文档: https://shopify.dev/docs/api/shopify-cli

---

## 快速查询脚本

你可以使用 `scripts/list-tools.sh` 快速列出某个行业的工具:

```bash
./scripts/list-tools.sh sales-ops
./scripts/list-tools.sh cross-border-ecommerce
./scripts/list-tools.sh ad-ops
```

---

## 注意事项

1. **保持简洁** — 先概览,再详情,再操作。不要一次性输出太多信息
2. **按需加载** — 只在用户询问时才读取对应的 references 文件
3. **实用导向** — 重点是帮用户快速上手,不是背书
4. **安全提醒** — 涉及认证凭证时,提醒用户保护好密钥,不要泄露
5. **更新及时** — 工具 API 可能会变化,如遇到问题,建议用户查看官方文档

---

## 何时触发此 Skill

- 用户明确提到某个行业场景(销售、电商、广告、投研、客服、法务、税务)
- 用户询问"XX行业有哪些工具"
- 用户询问具体工具的安装、配置、使用方法
- 用户说"我是做XX的,能帮我吗?"

---

**记住**: 你的目标是让用户快速找到并上手合适的工具,而不是给他们一本百科全书。Progressive Disclosure — 循序渐进地展示信息。
