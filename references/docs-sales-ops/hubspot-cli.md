<!-- doc-level: detailed -->

# HubSpot CLI

> HubSpot 官方命令行工具（偏开发部署），业务数据走 REST API，一体化 Inbound 营销+销售+服务平台

HubSpot 是全球领先的 Inbound Marketing（集客营销）和中小企业 CRM 平台，提供 Marketing Hub、Sales Hub、Service Hub、CMS Hub、Operations Hub 五大核心产品。与 Salesforce 相比，HubSpot 更注重中小企业市场，提供免费版本和更易上手的用户体验。HubSpot CLI 主要用于开发者部署自定义模块、主题和应用，业务数据操作通过 REST API 实现。

## 快速开始

### 安装

```bash
# 全局安装 HubSpot CLI
npm install -g @hubspot/cli

# 验证安装
hs --version

# 或使用 yarn
yarn global add @hubspot/cli
```

### 认证配置（详细步骤）

HubSpot 支持三种认证方式：Private App（推荐）、OAuth、Personal Access Key（已弃用）。

#### 方式一：Private App（推荐，最安全）

**步骤 1: 创建 Private App**

1. 登录 HubSpot → 右上角设置图标（齿轮）→ "Integrations"
2. 左侧菜单选择 "Private Apps"
3. 点击 "Create a private app"
4. 填写基本信息：
   - App name: `My Integration App`
   - Description: `用于 API 集成和数据同步`
5. 切换到 "Scopes" 选项卡，选择需要的权限（常用权限）：
   - **CRM Scopes:**
     - `crm.objects.contacts.read` / `.write` — 联系人读写
     - `crm.objects.companies.read` / `.write` — 公司读写
     - `crm.objects.deals.read` / `.write` — 交易读写
     - `crm.objects.owners.read` — 读取销售人员信息
   - **Marketing Scopes:**
     - `forms` — 表单访问
     - `content` — 内容访问（页面/博客）
   - **Automation Scopes:**
     - `automation` — 工作流访问
   - **Files Scopes:**
     - `files` — 文件访问
6. 点击 "Create app"
7. 复制显示的 Access Token（**只显示一次！立即保存**）

**步骤 2: 配置 CLI**

```bash
# 初始化 HubSpot CLI 配置
hs init

# 或直接配置
hs auth

# 输入你的 HubSpot 账户名称（如: mycompany）
# 选择认证方式: personalaccesskey（实际输入 Private App Token）
# 粘贴刚才复制的 Access Token
```

**步骤 3: 验证连接**

```bash
# 测试 CLI 连接
hs list

# 或使用 curl 测试 API
curl -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  "https://api.hubapi.com/crm/v3/objects/contacts?limit=1"
```

#### 方式二：OAuth 2.0（用于第三方应用集成）

**步骤 1: 创建 OAuth App**

1. 登录 HubSpot App Developer Account: https://app.hubspot.com/signup-hubspot/developers
2. 创建 App → 选择 "Public App"
3. 配置 OAuth 设置：
   - Redirect URL: `http://localhost:3000/oauth-callback`
   - Scopes: 选择需要的权限
4. 获取 Client ID 和 Client Secret

**步骤 2: OAuth 授权流程**

```bash
# 1. 生成授权 URL（替换 CLIENT_ID 和 REDIRECT_URI）
https://app.hubspot.com/oauth/authorize?client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&scope=contacts%20content

# 2. 用户授权后，使用 authorization_code 换取 access_token
curl -X POST https://api.hubapi.com/oauth/v1/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code&client_id=CLIENT_ID&client_secret=CLIENT_SECRET&redirect_uri=REDIRECT_URI&code=AUTHORIZATION_CODE"

# 3. 使用 refresh_token 获取新的 access_token（token 有效期 6 小时）
curl -X POST https://api.hubapi.com/oauth/v1/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=refresh_token&client_id=CLIENT_ID&client_secret=CLIENT_SECRET&refresh_token=REFRESH_TOKEN"
```

#### 常见认证错误及解决方案

1. **错误: `AUTHENTICATION_FAILED`**
   - 原因：Access Token 无效或已过期
   - 解决：重新生成 Private App Token 或刷新 OAuth Token

2. **错误: `MISSING_SCOPES`**
   - 原因：Token 缺少必要的权限
   - 解决：返回 Private App 设置，添加缺失的 Scopes

3. **错误: `RATE_LIMIT`**
   - 原因：API 调用超限（100 次/10 秒）
   - 解决：添加重试逻辑或优化 API 调用频率

### 环境变量配置

```bash
# Private App Token
export HUBSPOT_ACCESS_TOKEN="pat-na1-abcd1234-..."

# 账户 ID（可选，多账户场景）
export HUBSPOT_ACCOUNT_ID="12345678"

# OAuth 配置
export HUBSPOT_CLIENT_ID="your_client_id"
export HUBSPOT_CLIENT_SECRET="your_client_secret"
export HUBSPOT_REFRESH_TOKEN="your_refresh_token"
```

## 核心能力

### Sales Hub 销售中心

**CRM 对象管理（CRM Objects API）**
- Contacts（联系人）：完整联系人数据管理
- Companies（公司）：B2B 客户公司信息
- Deals（交易）：销售管道和商机管理
- Tickets（票据）：客服工单（也属于 Service Hub）
- Products（产品）：产品目录和定价
- Line Items（订单行项目）：交易关联的产品明细
- Quotes（报价单）：生成和发送报价（Professional+）

**销售自动化功能**
- Pipelines（管道）：自定义销售阶段和工作流
- Sequences（邮件序列）：自动化邮件跟进（Professional+）
- Email Tracking（邮件跟踪）：打开/点击追踪
- Meeting Scheduler（会议预约）：Calendly 替代品
- Call Tracking（通话记录）：集成电话系统或 HubSpot 通话
- Sales Analytics（销售分析）：销售漏斗、预测、团队绩效
- Playbooks（销售手册）：销售流程标准化指导

### Marketing Hub 营销中心

**Inbound Marketing 核心功能**
- Landing Pages（落地页）：无需开发，拖拽式建站
- Forms（表单）：潜在客户信息收集
- Marketing Email（营销邮件）：批量邮件发送 + A/B 测试
- Workflows（工作流）：营销自动化（类似 Salesforce Flow）
- Lead Scoring（潜客评分）：自动评估 Lead 质量
- Campaign Management（营销活动管理）：多渠道活动跟踪
- SEO & Content Strategy（SEO 工具）：关键词推荐、优化建议
- Social Media Management（社交媒体）：发布和监听
- Ads Management（广告管理）：Facebook/Google/LinkedIn Ads ROI 追踪

### Service Hub 客户服务中心

- Ticketing System（工单系统）：客户支持工单管理
- Knowledge Base（知识库）：自助服务文章库
- Live Chat（实时聊天）：网站嵌入式聊天窗口
- Chatbots（聊天机器人）：自动化客户咨询（无需代码）
- Customer Feedback（客户反馈）：NPS/CSAT/CES 调查
- Conversation Inbox（对话收件箱）：统一管理邮件/聊天/社交消息
- Help Desk Automation（客服自动化）：工单路由、自动回复

### CMS Hub 内容管理系统

- Website Pages（网站页面）：拖拽式页面编辑器
- Blog（博客）：内置 SEO 优化博客系统
- Themes & Modules（主题和模块）：自定义网站主题
- Dynamic Content（动态内容）：个性化内容展示
- Membership & Gated Content（会员内容）：登录墙和付费内容
- A/B Testing（A/B 测试）：页面转化优化
- Multi-language Support（多语言）：全球化网站支持

### Operations Hub 运营中心

- Data Sync（数据同步）：双向同步 Salesforce、NetSuite、QuickBooks 等
- Data Quality Automation（数据质量）：去重、格式化、丰富数据
- Programmable Automation（可编程自动化）：JavaScript 代码工作流
- Webhooks（事件驱动）：实时触发外部系统
- Custom Objects（自定义对象）：类似 Salesforce Custom Objects
- Calculated Properties（计算属性）：自动计算字段值
- Data Cleansing（数据清洗）：批量更新和清理脏数据

### 扩展和集成

- **App Marketplace**：1000+ 集成（Slack、Zapier、PandaDoc、Gong）
- **HubDB**：内容数据库，存储结构化数据（如产品目录）
- **Custom Objects**：创建自定义业务对象（Enterprise+）
- **HubSpot CRM API**：REST API 覆盖所有功能
- **Serverless Functions**：托管无服务器函数（Operations Hub Pro+）

## 使用示例（行业场景）

### 场景 1：SaaS 营销团队 - 查询本月通过表单注册的联系人

```bash
# 使用 HubSpot API v3 查询联系人
curl -X GET \
  "https://api.hubapi.com/crm/v3/objects/contacts?limit=50&properties=firstname,lastname,email,lifecyclestage,createdate" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  | jq '.results[] | select(.properties.createdate | fromdateiso8601 > (now - 30*24*3600)) | {name: "\(.properties.firstname) \(.properties.lastname)", email: .properties.email}'
```

### 场景 2：销售团队 - 创建新的 Deal 并关联 Contact

```bash
# 创建 Deal
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/deals" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "properties": {
      "dealname": "Q2 Enterprise License",
      "amount": "50000",
      "dealstage": "presentationscheduled",
      "pipeline": "default",
      "closedate": "2026-06-30T00:00:00Z"
    }
  }'

# 关联 Contact 到 Deal（使用 Associations API）
curl -X PUT \
  "https://api.hubapi.com/crm/v3/objects/deals/DEAL_ID/associations/contacts/CONTACT_ID/deal_to_contact" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN"
```

### 场景 3：内容营销 - 批量更新联系人的 Lifecycle Stage

```bash
# 批量更新联系人（从 Lead → MQL）
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/contacts/batch/update" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [
      {"id": "101", "properties": {"lifecyclestage": "marketingqualifiedlead"}},
      {"id": "102", "properties": {"lifecyclestage": "marketingqualifiedlead"}},
      {"id": "103", "properties": {"lifecyclestage": "marketingqualifiedlead"}}
    ]
  }'
```

### 场景 4：客服团队 - 创建工单并自动分配

```bash
# 创建 Ticket
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/tickets" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "properties": {
      "subject": "产品无法登录",
      "hs_pipeline_stage": "1",
      "hs_ticket_priority": "HIGH",
      "hubspot_owner_id": "123456"
    }
  }'
```

### 场景 5：运营团队 - 搜索高价值公司（年收入 > $1M）

```bash
# 使用 Search API 查询公司
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/companies/search" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filterGroups": [{
      "filters": [{
        "propertyName": "annualrevenue",
        "operator": "GTE",
        "value": "1000000"
      }]
    }],
    "properties": ["name", "domain", "industry", "annualrevenue"],
    "limit": 50
  }'
```

### 场景 6：营销自动化 - 触发 Workflow（通过 Webhook）

```bash
# 使用 Webhooks API 触发工作流
curl -X POST \
  "https://api.hubapi.com/automation/v3/workflows/WORKFLOW_ID/enrollments" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": {
      "email": "customer@example.com",
      "firstname": "张",
      "lastname": "三"
    }
  }'
```

### 场景 7：内容团队 - 创建博客文章

```bash
# 创建博客文章（需要 Content API 权限）
curl -X POST \
  "https://api.hubapi.com/content/api/v2/blog-posts" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "如何提升 SaaS 产品的用户留存率",
    "post_body": "<p>内容正文...</p>",
    "blog_author_id": "123456",
    "content_group_id": "789",
    "meta_description": "提升用户留存的 10 个实用技巧"
  }'
```

### 场景 8：集成场景 - 同步 Salesforce Lead 到 HubSpot Contact

```bash
# 创建或更新 Contact（使用 Email 作为唯一标识）
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/contacts" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "properties": {
      "email": "lead@example.com",
      "firstname": "李",
      "lastname": "四",
      "phone": "+86-138-0000-0000",
      "company": "科技公司",
      "hs_lead_status": "NEW",
      "salesforce_id": "00Q1234567890ABC"
    }
  }'
```

### 场景 9：销售分析 - 导出本季度所有 Closed Won Deals

```bash
# 使用 Search API 导出成交的 Deals
curl -X POST \
  "https://api.hubapi.com/crm/v3/objects/deals/search" \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "filterGroups": [{
      "filters": [
        {"propertyName": "dealstage", "operator": "EQ", "value": "closedwon"},
        {"propertyName": "closedate", "operator": "GTE", "value": "2026-01-01T00:00:00Z"},
        {"propertyName": "closedate", "operator": "LTE", "value": "2026-03-31T23:59:59Z"}
      ]
    }],
    "properties": ["dealname", "amount", "closedate", "pipeline"],
    "limit": 100
  }' | jq -r '["Deal Name","Amount","Close Date"], (.results[] | [.properties.dealname, .properties.amount, .properties.closedate]) | @csv'
```

### 场景 10：开发者 - 部署自定义 CRM Card

```bash
# 使用 HubSpot CLI 部署自定义扩展
cd my-hubspot-extension
hs project upload
hs project watch
```

## 适用场景

### 典型工作流

1. **Inbound Marketing 工作流（HubSpot 核心优势）**
   - 创建 Landing Page + Form → 访客填表 → 自动创建 Contact
   - 触发 Workflow：发送欢迎邮件系列（Email Nurturing）
   - Lead Scoring：根据行为（打开邮件、访问定价页）自动评分
   - 达到分数阈值 → 自动转为 MQL → 通知销售团队

2. **SMB 销售工作流**
   - SDR 在 HubSpot 记录通话/邮件（自动记录到 Contact Timeline）
   - 创建 Deal → 推进 Deal Stage（Meeting Scheduled → Proposal Sent → Closed Won）
   - 使用 Sequences 自动发送跟进邮件（3 天后自动发邮件提醒）
   - 成交后触发 Onboarding Workflow

3. **客服工作流**
   - 客户发送邮件 → 自动创建 Ticket
   - Live Chat 对话 → 自动创建 Ticket 或 Contact
   - 座席回复 → 自动更新 Ticket Status
   - 关闭 Ticket → 自动发送 NPS 调查

4. **内容营销工作流**
   - 发布 Blog Post → 自动分享到社交媒体
   - 访客阅读文章 → 追踪为 Contact 活动
   - 下载 Gated Content（如白皮书） → 触发 Lead Nurturing Workflow
   - 多次访问定价页 → 自动通知销售团队（Hot Lead）

## 与其他工具的区分

### HubSpot vs Salesforce

| 维度 | HubSpot | Salesforce |
|------|---------|------------|
| **定位** | 中小企业 + Inbound 营销 | 大型企业 + 全行业 CRM |
| **易用性** | 极易上手（拖拽式，无需培训） | 复杂（需专业培训/顾问） |
| **免费版** | 有（永久免费，功能有限） | 无（最低 $25/月） |
| **价格** | $18-$3,600/月（3-5 个席位起） | $80-$500/用户/月 |
| **营销功能** | 内置 Marketing Hub（性价比高） | 需单独购买 Marketing Cloud（昂贵） |
| **CPQ** | 需第三方集成（PandaDoc/QuoteWerks） | 内置 CPQ（$75/用户/月） |
| **定制性** | 中等（Workflows/Custom Properties） | 极强（Apex/Flow/Custom Objects） |
| **适用场景** | SMB、快速启动、Inbound 营销 | 大型企业、复杂流程、全球部署 |
| **学习曲线** | 1-2 周 | 1-3 个月 |

### HubSpot vs Zoho CRM

| 维度 | HubSpot | Zoho CRM |
|------|---------|----------|
| **价格** | 免费版 + $18-$3,600/月 | $14-$52/用户/月 |
| **UI/UX** | 现代化、直观 | 略显陈旧 |
| **营销功能** | 强大（Landing Page/Email/SEO 一体化） | 基础（需 Zoho Campaigns） |
| **生态** | App Marketplace（1000+ 集成） | Zoho One（40+ 自有应用） |
| **适用场景** | 重视营销自动化和用户体验 | 预算有限、已用 Zoho 生态 |

### HubSpot vs Pipedrive

| 维度 | HubSpot | Pipedrive |
|------|---------|-----------|
| **核心优势** | 营销+销售+服务一体化 | 专注销售管道管理 |
| **营销功能** | 完整 Marketing Hub | 基本无（需集成 Mailchimp 等） |
| **价格** | 免费版 + $18/月起 | $14-$99/用户/月 |
| **适用场景** | 需要营销+CRM 一体化 | 纯销售团队，只需管道管理 |

## 限制和注意事项

### 免费版功能限制

- 最多 1 个 Dashboard
- 无 Workflows 自动化（需 Professional+）
- 无 Sequences 邮件序列（需 Professional+）
- 邮件有 HubSpot 品牌水印
- 表单提交有 HubSpot 品牌
- 无自定义报表（仅预设报表）
- 联系人数量无限，但功能受限

### API 限流

| 认证方式 | 限流 | 适用场景 |
|---------|------|---------|
| **Private App** | 100 次/10 秒 | 单账户集成 |
| **OAuth** | 150 次/10 秒 | 多账户应用 |
| **Bursting** | 支持短时爆发流量（最多 10 倍） | 批量操作 |

### 常见报错及解决方案

1. **错误: `RATE_LIMIT_REACHED`**
   - 原因：10 秒内调用超过 100 次
   - 解决：添加指数退避重试逻辑，或批量操作使用 Batch API

2. **错误: `OBJECT_NOT_FOUND`**
   - 原因：Contact/Deal ID 不存在或已删除
   - 解决：先使用 Search API 查询确认对象存在

3. **错误: `PROPERTY_DOESNT_EXIST`**
   - 原因：自定义属性未创建或拼写错误
   - 解决：检查属性名称（区分大小写），或先创建自定义属性

4. **错误: `INVALID_EMAIL`**
   - 原因：邮箱格式不合法
   - 解决：使用正则校验邮箱格式后再提交

5. **错误: `CONTACT_LIMIT_EXCEEDED`**
   - 原因：超过套餐联系人数量限制（Marketing Hub 有限额）
   - 解决：升级套餐或清理无效联系人

### 注意事项

- **Marketing Hub 功能需单独订阅**：免费 CRM 不含营销自动化
- **Workflows 高级功能需 Professional+**：免费版无法使用
- **与 Salesforce 比缺少企业级功能**：无 CPQ、Territory Management、高级权限管理
- **数据迁移需谨慎**：HubSpot 不支持删除后恢复，批量删除前务必备份
- **自定义对象需 Enterprise**：中小企业版无法创建自定义对象

## 定价（详细分级）

### Sales Hub 定价

| 版本 | 价格（年付） | 核心功能 | 适用场景 |
|------|------------|---------|---------|
| **Free** | $0 | 无限联系人，基础 CRM | 小微团队试水 |
| **Starter** | $18/月（2 个席位） | +Email 追踪 +Meeting Scheduler | 小团队（2-5 人） |
| **Professional** | $800/月（3 个席位起） | +Sequences +Playbooks +Goals | 成长型销售团队（10-50 人） |
| **Enterprise** | $3,600/月（5 个席位起） | +Custom Objects +Predictive Lead Scoring | 大型销售组织（50+ 人） |

### Marketing Hub 定价

| 版本 | 价格 | 核心功能 | 适用场景 |
|------|------|---------|---------|
| **Free** | $0 | 表单/Email/落地页（有限） | 入门级营销 |
| **Starter** | $18/月（1,000 联系人） | +Email 去品牌 +简单自动化 | 小企业营销 |
| **Professional** | $800/月（2,000 联系人） | +Workflows +A/B 测试 +SEO | 专业营销团队 |
| **Enterprise** | $3,600/月（10,000 联系人） | +高级报表 +团队管理 | 企业级营销 |

### Service Hub 定价

- **Free**: $0（基础工单系统）
- **Starter**: $18/月（2 个席位）
- **Professional**: $450/月（5 个席位起，+Knowledge Base +Live Chat）
- **Enterprise**: $1,200/月（10 个席位起，+Playbooks +Custom Objects）

### CMS Hub 定价

- **Starter**: $23/月（+CMS 功能）
- **Professional**: $360/月（+Membership +Dynamic Content）
- **Enterprise**: $1,200/月（+多语言 +高级 SEO）

### Operations Hub 定价

- **Free**: $0（基础数据同步）
- **Starter**: $18/月（+Data Sync）
- **Professional**: $800/月（+Programmable Automation +Webhooks）
- **Enterprise**: $2,000/月（+Custom Objects +Calculated Properties）

### 附加费用

- **额外联系人（Marketing Hub）**: $50/月/1,000 联系人
- **额外席位**: 按版本原价比例增加
- **Onboarding 咨询**: $1,500-$7,500（可选）

## 参考链接

### 官方文档

- [HubSpot Developers 文档](https://developers.hubspot.com/docs/api/overview)
- [CRM API 参考](https://developers.hubspot.com/docs/api/crm/understanding-the-crm)
- [HubSpot CLI 文档](https://developers.hubspot.com/docs/cms/developer-reference/local-development-cli)
- [Workflows API](https://developers.hubspot.com/docs/api/automation/workflows)
- [Webhooks 指南](https://developers.hubspot.com/docs/api/webhooks)

### 进阶学习

- [HubSpot Academy（免费认证课程）](https://academy.hubspot.com/)
- [Inbound Marketing 认证](https://academy.hubspot.com/courses/inbound-marketing)
- [HubSpot CMS for Developers](https://academy.hubspot.com/courses/cms-for-developers)
- [API Best Practices](https://developers.hubspot.com/docs/api/usage-details)

### 社区资源

- [HubSpot Community](https://community.hubspot.com/)
- [HubSpot Developer Slack](https://developers.hubspot.com/slack)
- [App Marketplace](https://ecosystem.hubspot.com/marketplace/apps)
- [GitHub - HubSpot Examples](https://github.com/HubSpot)
