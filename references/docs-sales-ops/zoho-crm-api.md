<!-- doc-level: detailed -->

# Zoho CRM API

> 性价比 CRM,支持 Deluge 脚本深度定制,功能全但价格比 Salesforce 便宜 50%+

Zoho CRM 是面向中小企业的全功能客户关系管理平台,提供从线索获取到商机转化的完整销售自动化能力。作为 Zoho One 生态的核心产品,它以极高的性价比(比 Salesforce 便宜 50%)提供 90% 的企业级功能,同时通过 Deluge 脚本语言支持深度定制。Zoho CRM API v7 是最新版本,提供 REST 风格的 HTTP 接口,支持所有核心对象的 CRUD 操作、批量导入导出、自动化工作流和 AI 驱动的销售洞察。

## 为什么选这个工具

### Zoho CRM vs Salesforce vs HubSpot

| 维度 | Zoho CRM | Salesforce | HubSpot |
|------|----------|-----------|---------|
| **定位** | 中小企业性价比之选 | 大型企业 CRM 标杆 | 中小企业增长营销 |
| **价格** | $14-$52/用户/月 | $80-$330/用户/月 | 免费-$120/用户/月 |
| **功能完整度** | 90% 企业级功能 | 100% 企业级功能 | 70% CRM + 强营销 |
| **定制能力** | Deluge 脚本(中等复杂) | Apex + Flow(极强) | Workflows(简单) |
| **AI 能力** | Zia AI(Lead/Deal 评分) | Einstein AI(全方位) | 基础 AI 推荐 |
| **生态集成** | Zoho One(40+ 应用) | AppExchange(3000+) | HubSpot Ecosystem(500+) |
| **学习曲线** | 中等(UI 较旧) | 陡峭(需培训) | 平缓(易上手) |
| **API 限流** | 10 次/10 秒(可提升) | 10 万次/天起 | 10 万次/天 |
| **适用场景** | 预算有限的成长型企业 | 预算充足的大企业 | 重视 Inbound 营销的 SMB |

## 快速开始

### 安装

无需安装 CLI,Zoho CRM 使用标准 HTTP API 调用。推荐使用以下工具:

```bash
# 使用 curl(Linux/macOS 自带)
curl --version

# Python SDK(非官方)
pip install zohocrm-python-sdk

# Node.js SDK(非官方)
npm install zohocrm

# Postman Collection(官方提供)
# 下载: https://www.zoho.com/crm/developer/docs/api/v7/postman.html
```

### 认证配置(详细步骤)

Zoho CRM 使用 OAuth 2.0 认证,支持两种方式:Server-based(适合后端服务)和 Self-Client(适合个人脚本)。

#### 方式一:Server-based OAuth(推荐用于生产环境)

**步骤 1:注册开发者账号**

1. 访问 https://api-console.zoho.com
2. 点击右上角 "Get Started" 或 "Sign Up"
3. 使用 Zoho 账号登录(或新注册一个账号)
4. 完成邮箱验证

**步骤 2:创建 OAuth 应用**

1. 登录 API Console 后,点击 "Add Client"
2. 选择 "Server-based Applications"
3. 填写应用信息:
   - Client Name: `My CRM Integration`
   - Homepage URL: `https://yourcompany.com`
   - Authorized Redirect URIs: `https://yourcompany.com/oauth/callback`
     (开发环境可用 `http://localhost:8080/callback`)
4. 点击 "Create"
5. 记录 **Client ID** 和 **Client Secret**(仅显示一次,请妥善保存)

**步骤 3:获取 Authorization Code**

在浏览器中访问以下 URL(替换 `{CLIENT_ID}` 和 `{REDIRECT_URI}`):

```
https://accounts.zoho.com/oauth/v2/auth?
  response_type=code&
  client_id={CLIENT_ID}&
  scope=ZohoCRM.modules.ALL,ZohoCRM.settings.ALL&
  redirect_uri={REDIRECT_URI}&
  access_type=offline
```

示例:
```
https://accounts.zoho.com/oauth/v2/auth?response_type=code&client_id=1000.ABC123XYZ&scope=ZohoCRM.modules.ALL&redirect_uri=http://localhost:8080/callback&access_type=offline
```

用户授权后,浏览器会跳转到 `REDIRECT_URI?code={AUTHORIZATION_CODE}`,复制 `code` 参数的值。

**步骤 4:交换 Access Token 和 Refresh Token**

```bash
curl -X POST https://accounts.zoho.com/oauth/v2/token \
  -d "code={AUTHORIZATION_CODE}" \
  -d "client_id={CLIENT_ID}" \
  -d "client_secret={CLIENT_SECRET}" \
  -d "redirect_uri={REDIRECT_URI}" \
  -d "grant_type=authorization_code"
```

响应示例:
```json
{
  "access_token": "1000.abcd1234efgh5678ijkl",
  "refresh_token": "1000.xyz9876mnop5432qrst",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

**重要**:Access Token 有效期 1 小时,Refresh Token 永久有效(除非撤销)。

**步骤 5:使用 Refresh Token 获取新 Access Token**

```bash
curl -X POST https://accounts.zoho.com/oauth/v2/token \
  -d "refresh_token={REFRESH_TOKEN}" \
  -d "client_id={CLIENT_ID}" \
  -d "client_secret={CLIENT_SECRET}" \
  -d "grant_type=refresh_token"
```

#### 方式二:Self-Client(适合个人脚本)

1. 访问 https://api-console.zoho.com
2. 点击 "Get Started" → "Self Client"
3. 选择 Scope:`ZohoCRM.modules.ALL,ZohoCRM.settings.ALL`
4. 点击 "Create" → 立即获得 Access Token(有效期 1 小时)
5. 点击 "Generate Code" → 获得 Refresh Token(永久有效)

#### 常见认证错误及解决方案

1. **错误:`invalid_client`**
   - 原因:Client ID 或 Client Secret 错误
   - 解决:检查 API Console 中的凭证是否正确复制

2. **错误:`invalid_code`**
   - 原因:Authorization Code 已过期(10 分钟有效期)或已使用
   - 解决:重新生成 Authorization Code

3. **错误:`redirect_uri_mismatch`**
   - 原因:请求的 Redirect URI 与注册时不一致
   - 解决:确保 URI 完全匹配(包括协议、域名、路径)

4. **错误:`INVALID_TOKEN`(API 调用时)**
   - 原因:Access Token 已过期
   - 解决:使用 Refresh Token 获取新 Access Token

5. **错误:`INSUFFICIENT_PERMISSION`**
   - 原因:Token 的 Scope 不足
   - 解决:重新授权时添加所需 Scope(如 `ZohoCRM.modules.Leads.CREATE`)

### 环境变量配置

```bash
# Zoho CRM 认证凭证
export ZOHO_CLIENT_ID="1000.ABC123XYZ"
export ZOHO_CLIENT_SECRET="your_client_secret"
export ZOHO_REFRESH_TOKEN="1000.xyz9876mnop5432qrst"
export ZOHO_ACCESS_TOKEN="1000.abcd1234efgh5678ijkl"

# 数据中心(根据你的 Zoho 账号所在区域)
export ZOHO_DC="com"  # US: com, EU: eu, IN: in, AU: com.au, CN: com.cn

# API 基础 URL(自动拼接,无需手动设置)
# US: https://www.zohoapis.com
# EU: https://www.zohoapis.eu
# IN: https://www.zohoapis.in
```

### 验证

```bash
# 验证 Access Token 是否有效
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Leads?fields=Last_Name,Email&per_page=5" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN"

# 成功响应示例
# {"data":[{"Last_Name":"Zhang","Email":"zhang@example.com","id":"4150868000001234567"}]}
```

## 核心能力

### CRM 核心对象管理

**标准模块(全对象 CRUD)**
- **Lead(线索)**:潜在客户信息收集和评分
- **Contact(联系人)**:已转化客户的联系信息
- **Account(客户)**:公司/组织信息
- **Deal(商机)**:销售机会跟踪和管道管理
- **Task(任务)**:待办事项和跟进提醒
- **Event(事件)**:会议和活动记录
- **Call(通话)**:电话记录和录音
- **Product(产品)**:产品目录和价格管理
- **Quote(报价单)**:报价生成和发送
- **Sales Order(销售订单)**:订单确认和履行
- **Invoice(发票)**:开票和收款跟踪
- **Campaign(营销活动)**:活动管理和 ROI 分析

**自定义模块**
- 创建自定义对象(如 "项目"、"投诉" 等)
- 自定义字段(文本、数字、日期、下拉、查找、多选等 20+ 类型)
- 跨模块关联关系(Lookup、Subform)

### 销售自动化

**Blueprint(销售流程管理)**
- 可视化流程设计器(类似 Salesforce Flow)
- 多阶段销售流程(Discovery → Demo → Proposal → Negotiation → Closed Won/Lost)
- 每阶段必填字段、自动任务、邮件通知
- 条件分支和并行审批

**Workflow 自动化**
- 字段更新规则(如 Lead Score > 80 自动分配给 AE)
- 邮件提醒(商机 7 天未跟进自动发邮件给 Owner)
- Webhook 触发(推送数据到第三方系统)
- 定时任务(每日汇总报表)

**Deluge 脚本(自定义函数)**
- 类似 Salesforce Apex 的脚本语言
- 支持复杂业务逻辑(如自动计算佣金、重复检测)
- 可调用外部 API(如调用 Slack/Twilio)
- 支持 CRUD、循环、条件判断、异常处理

### AI 和智能洞察

**Zia AI(内置 AI 助手)**
- **Lead/Deal 评分**:预测转化概率(基于历史数据训练模型)
- **最佳联系时间推荐**:分析邮件打开时间,推荐最佳发送时段
- **异常检测**:识别异常高价值商机或长时间停滞的 Deal
- **语音转文字**:通话录音自动转写
- **邮件情绪分析**:识别客户邮件情绪(积极/消极)
- **预测销售趋势**:基于历史数据预测季度/年度销售额

**Canvas(自定义 UI)**
- 为每个模块创建自定义界面
- 嵌入第三方组件(如 Google Maps、Stripe 支付)
- 自定义按钮和动作

### 多渠道触达

**邮件集成**
- Gmail/Outlook/Zoho Mail 双向同步
- 邮件模板管理(支持变量插入)
- 邮件打开/点击追踪
- 批量邮件发送(支持个性化)

**电话集成**
- RingCentral/Twilio/Aircall 集成
- 一键拨号(Click-to-Call)
- 通话录音和自动记录到 CRM
- 电话脚本提示

**社交媒体集成**
- Facebook/Twitter/LinkedIn 消息同步
- 社交信号收集(如 LinkedIn 职位变动)

### 报表和分析

**内置报表**
- 销售漏斗报表(Lead → Contact → Deal → Closed Won)
- 销售预测和配额管理
- 团队绩效对比
- Territory(区域)销售分析

**自定义报表**
- 拖拽式报表构建器
- 支持多维分析(按时间、地区、产品、销售代表等)
- 导出为 CSV/PDF/Excel

**Dashboard**
- 实时 KPI 监控(今日新增 Lead、本月关单金额等)
- 图表类型:柱状图、饼图、漏斗图、仪表盘、热力图

### 集成生态

**Zoho One 生态(40+ 应用无缝集成)**
- **Zoho Books**:会计和财务管理
- **Zoho Desk**:客服工单系统
- **Zoho Campaigns**:邮件营销自动化
- **Zoho Analytics**:BI 和高级分析
- **Zoho Sign**:电子签名

**第三方集成**
- **Zapier/Integromat**:连接 2000+ 应用
- **Marketplace**:300+ 扩展和插件
- **Slack/Microsoft Teams**:即时通知

## 使用示例(行业场景)

### 场景 1:SaaS 销售团队 - 查询本月高分 Lead

```bash
# 查询 Lead Score > 80 且本月创建的线索
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Leads?fields=Last_Name,Email,Lead_Score,Company&criteria=(Lead_Score:greater_than:80)and(Created_Time:greater_equal:2026-04-01T00:00:00%2B08:00)&sort_by=Lead_Score&sort_order=desc&per_page=50" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN"
```

### 场景 2:市场团队 - 批量导入展会收集的 Lead

```bash
# 批量创建 Lead(最多 100 条/请求)
curl -X POST \
  "https://www.zohoapis.com/crm/v7/Leads" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [
      {
        "Last_Name": "Zhang",
        "First_Name": "San",
        "Email": "zhang@example.com",
        "Phone": "+86-138-0000-0001",
        "Company": "Tech Corp",
        "Lead_Source": "Trade Show",
        "Lead_Status": "Not Contacted"
      },
      {
        "Last_Name": "Li",
        "First_Name": "Si",
        "Email": "li@example.com",
        "Company": "Innovation Inc",
        "Lead_Source": "Trade Show"
      }
    ],
    "trigger": ["workflow"]
  }'
```

### 场景 3:销售 AE - 更新 Deal 阶段并创建跟进任务

```bash
# 更新 Deal 阶段
curl -X PUT \
  "https://www.zohoapis.com/crm/v7/Deals" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "id": "4150868000001234567",
      "Stage": "Proposal/Price Quote",
      "Amount": 100000,
      "Closing_Date": "2026-05-15",
      "Probability": 75
    }]
  }'

# 创建跟进任务
curl -X POST \
  "https://www.zohoapis.com/crm/v7/Tasks" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "Subject": "Follow up on proposal",
      "Due_Date": "2026-04-10",
      "Status": "Not Started",
      "Priority": "High",
      "What_Id": "4150868000001234567",
      "Description": "Discuss pricing and contract terms"
    }]
  }'
```

### 场景 4:销售运营 - 按关键词搜索客户

```bash
# 搜索 Account(公司名称包含 "Tech")
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Accounts/search?criteria=(Account_Name:contains:Tech)" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN"

# 或使用全文搜索(跨多个字段)
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Accounts/search?word=Tech&per_page=20" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN"
```

### 场景 5:客服团队 - 创建工单(使用自定义模块)

```bash
# 创建 Support_Tickets 记录(假设已创建自定义模块)
curl -X POST \
  "https://www.zohoapis.com/crm/v7/Support_Tickets" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "Ticket_Subject": "Login issue",
      "Customer": "4150868000001234567",
      "Priority": "High",
      "Status": "Open",
      "Description": "Customer cannot log in after password reset"
    }]
  }'
```

### 场景 6:自动化 - 转化 Lead 为 Contact 和 Deal

```bash
# 转化 Lead(自动创建 Contact + Account + Deal)
curl -X POST \
  "https://www.zohoapis.com/crm/v7/Leads/4150868000001234567/actions/convert" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "overwrite": false,
      "notify_lead_owner": true,
      "notify_new_entity_owner": true,
      "Deals": {
        "Deal_Name": "Q2 Opportunity",
        "Closing_Date": "2026-06-30",
        "Stage": "Qualification",
        "Amount": 50000
      }
    }]
  }'
```

### 场景 7:数据分析 - 获取销售漏斗统计

```bash
# 获取 Lead 阶段分布
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Leads?fields=Lead_Status&per_page=200" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN" | \
  jq '[.data[].Lead_Status] | group_by(.) | map({status: .[0], count: length})'

# 获取 Deal 阶段分布和金额
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Deals?fields=Stage,Amount&per_page=200" \
  -H "Authorization: Zoho-oauthtoken $ZOHO_ACCESS_TOKEN"
```

### 场景 8:集成场景 - Webhook 通知(Deal 关单后通知 Slack)

**在 Zoho CRM 中配置 Webhook(Settings → Automation → Workflow Rules)**

```bash
# Zoho CRM 会发送 POST 请求到你的 Webhook URL
# 你的服务接收后转发到 Slack
curl -X POST https://hooks.slack.com/services/YOUR/WEBHOOK/URL \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Deal Closed Won: *{{Deal_Name}}* - ${{Amount}} by {{Owner.name}}"
  }'
```

## 适用场景

### 典型工作流

1. **SDR(销售发展代表)工作流**
   - 导入展会/网站表单收集的 Lead
   - Zia AI 自动评分,筛选高质量 Lead
   - 按 Lead Score 和地区自动分配给 SDR
   - SDR 发送序列化邮件(Day 1 → Day 3 → Day 7)
   - 邮件打开/点击自动创建 Task 提醒跟进
   - 合格 Lead 转化为 Contact + Deal,分配给 AE

2. **AE(客户经理)工作流**
   - 接收 SDR 转化的 Deal
   - 按 Blueprint 推进 Deal 阶段(Demo → Proposal → Negotiation)
   - 每阶段自动触发任务(如 "发送 Proposal" → "跟进反馈")
   - 创建 Quote(报价单)并发送给客户
   - 客户接受后转为 Sales Order
   - 关单后自动创建 Invoice 并通知财务(通过 Zoho Books 集成)

3. **市场团队工作流**
   - 创建 Campaign(如 "Q2 Webinar")
   - 批量导入目标客户到 Campaign
   - 通过 Zoho Campaigns 发送邮件邀请
   - 报名客户自动创建 Lead
   - Campaign ROI 分析(投入成本 vs 产生的 Deal 金额)

4. **销售运营工作流**
   - 每周一自动生成销售漏斗报表(Lead/Deal 各阶段分布)
   - 识别停滞 Deal(超过 30 天未更新)并提醒 Owner
   - 清洗重复数据(Merge Duplicate Leads/Contacts)
   - 配置 Territory 规则(按地区/行业自动分配 Lead)
   - 导出月度销售数据到 Zoho Analytics 做深度分析

## 与其他工具的区分

### Zoho CRM vs Salesforce(详细对比)

| 功能 | Zoho CRM | Salesforce |
|------|----------|-----------|
| **Lead 管理** | Lead Scoring, Web Forms, Duplicate Check | Lead Scoring, Einstein Lead Insights |
| **Deal 管理** | Blueprint 可视化流程 | Opportunity Stages + Path |
| **自定义能力** | Deluge 脚本(学习曲线中等) | Apex(功能最强但复杂) + Flow |
| **AI 能力** | Zia AI(评分、异常检测、推荐) | Einstein(更全面,预测准确度更高) |
| **移动 App** | iOS/Android(功能完整) | Salesforce Mobile(功能最强) |
| **数据导入** | CSV/Excel 导入,最多 1 万条/次 | Data Loader,支持百万级(Bulk API) |
| **API 限流** | 10 次/10 秒(Enterprise 版 25 次/10 秒) | 10 万次/天起(Professional 版) |
| **报表** | 拖拽式报表,基础 Dashboard | 高级报表 + Einstein Analytics |
| **审批流程** | 基础审批(多级审批需配置) | 强大的审批流(可视化设计器) |
| **沙箱环境** | 仅 Ultimate 版提供 | Enterprise 版起提供 |

### Zoho CRM vs Pipedrive

| 维度 | Zoho CRM | Pipedrive |
|------|----------|-----------|
| **定位** | 全功能 CRM | 专注销售管道可视化 |
| **价格** | $14-$52/用户/月 | $15-$99/用户/月 |
| **功能广度** | 涵盖市场/销售/服务 | 主要专注销售 |
| **易用性** | 中等(功能多但 UI 稍旧) | 极简(易上手) |
| **自动化** | Workflow + Deluge 脚本 | 基础自动化(邮件序列) |
| **适用场景** | 需要全功能 CRM 且预算有限 | 小团队需要简单管道管理 |

## 限制和注意事项

### API 调用限额

| 版本 | API 限流 | 存储空间 | 批量导入 | Deluge 脚本 |
|------|---------|---------|---------|-----------|
| **Standard** | 10 次/10 秒 | 1 GB | 2,500 条/天 | 不支持 |
| **Professional** | 10 次/10 秒 | 5 GB | 5,000 条/天 | 支持 |
| **Enterprise** | 25 次/10 秒 | 100 GB | 25,000 条/天 | 支持 |
| **Ultimate** | 50 次/10 秒 | 1 TB | 无限制 | 支持 + Sandbox |

**限流重置机制**:每 10 秒滑动窗口重置(非固定窗口)

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **INVALID_TOKEN** | Access Token 已过期(1 小时有效期) | 使用 Refresh Token 获取新 Access Token |
| **AUTHENTICATION_FAILURE** | Client ID/Secret 错误 | 检查 API Console 中的凭证 |
| **MANDATORY_NOT_FOUND** | 缺少必填字段 | 检查模块设置,补充必填字段(如 Lead 的 Last_Name) |
| **DUPLICATE_DATA** | 违反唯一性约束(如邮箱重复) | 先查询检查重复,或使用 `upsert` API |
| **PATTERN_NOT_MATCHED** | 字段格式不符合验证规则 | 检查字段验证规则(如邮箱/电话格式) |
| **RATE_LIMIT_EXCEEDED** | 超过 API 限流 | 降低请求频率或升级版本 |
| **INVALID_REQUEST_METHOD** | HTTP 方法错误(如用 POST 查询) | 使用正确的方法(GET/POST/PUT/DELETE) |
| **INVALID_MODULE** | 模块名称错误 | 检查模块 API Name(如 `Leads` 不是 `Lead`) |

### 注意事项

- **用户体验**:UI 界面相比 Salesforce 稍显陈旧,但功能完整
- **Deluge 学习曲线**:语法类似 JavaScript,但调试工具较弱
- **数据中心**:根据账号注册地区不同,API 域名不同(`.com` / `.eu` / `.in`)
- **Zoho One 捆绑销售**:如果需要多个 Zoho 产品,建议购买 Zoho One 套件($37/用户/月,包含 40+ 应用)
- **沙箱环境**:仅 Ultimate 版提供,测试需在生产环境谨慎操作
- **批量操作限制**:单次 API 调用最多 100 条记录,大批量导入需使用 Bulk API

## 定价(详细分级)

### Zoho CRM 定价

| 版本 | 价格(年付) | 核心功能 | 适用场景 |
|------|----------|---------|---------|
| **Free** | $0(最多 3 用户) | 基础 CRM,Lead/Contact/Deal,移动 App | 初创团队试水 |
| **Standard** | $14/用户/月 | +销售预测 +工作流自动化 +社交媒体集成 | 小团队(3-10 人) |
| **Professional** | $23/用户/月 | +Deluge 脚本 +Blueprint +库存管理 +Zia AI | 成长型企业(10-50 人) |
| **Enterprise** | $40/用户/月 | +高级自定义 +Territory 管理 +多币种 | 中大型企业(50-500 人) |
| **Ultimate** | $52/用户/月 | +Sandbox +高级 BI +无限存储 | 关键业务系统 |

### Zoho One 套件定价

- **Zoho One(All-in-One)**:$37/用户/月(包含 CRM + 40+ 应用)
  - 包含:Books(会计)、Desk(客服)、Campaigns(邮件营销)、Analytics(BI)、Sign(电子签名)、Projects(项目管理)等
  - 适合需要多个业务系统的企业

### 附加费用

- **额外存储**:$10/GB/月(Enterprise 版起)
- **Sandbox 环境**:包含在 Ultimate 版,无额外费用
- **电话集成(Phonebridge)**:$12/用户/月(与 RingCentral/Twilio 集成)

## 参考链接

### 官方文档

- [Zoho CRM 开发者文档](https://www.zoho.com/crm/developer/docs/api/v7/)
- [API 方法参考](https://www.zoho.com/crm/developer/docs/api/v7/api-methods.html)
- [OAuth 2.0 指南](https://www.zoho.com/crm/developer/docs/api/v7/oauth-overview.html)
- [Deluge 脚本参考](https://www.zoho.com/deluge/help/)
- [Blueprint 指南](https://www.zoho.com/crm/help/blueprint/blueprint.html)

### 进阶学习

- [Zoho CRM 学院(免费课程)](https://www.zoho.com/crm/academy/)
- [API 最佳实践](https://www.zoho.com/crm/developer/docs/api/v7/best-practices.html)
- [Deluge 代码示例](https://help.zoho.com/portal/en/kb/crm/customize-crm-account/deluge-scripts)
- [Zia AI 配置指南](https://www.zoho.com/crm/help/zia/introduction.html)

### 社区资源

- [Zoho CRM 社区论坛](https://help.zoho.com/portal/en/community/zoho-crm)
- [Zoho CRM GitHub(第三方 SDK)](https://github.com/zoho)
- [Postman Collection](https://www.zoho.com/crm/developer/docs/api/v7/postman.html)
- [Marketplace 扩展](https://marketplace.zoho.com/home/crm)

### SDK 和工具

- [Python SDK(非官方)](https://github.com/zoho/zohocrm-python-sdk)
- [Node.js SDK(非官方)](https://github.com/zoho/zohocrm-nodejs-sdk)
- [PHP SDK(非官方)](https://github.com/zoho/zohocrm-php-sdk)
- [Zapier 集成](https://zapier.com/apps/zoho-crm/integrations)
