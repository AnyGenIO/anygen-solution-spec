<!-- doc-level: detailed -->

# Salesforce CLI (sf)

> Salesforce 官方命令行工具,覆盖 Sales/Service/Marketing Cloud 全生态，企业级 CRM 标杆平台

Salesforce 是全球最大的企业级 CRM 平台，提供从销售到服务到营销的完整客户关系管理解决方案。Salesforce CLI 是官方命令行工具，用于组织管理、数据操作和应用部署；业务数据操作主要通过 REST/SOAP API 实现。平台包含三大核心产品：Sales Cloud（销售自动化）、Service Cloud（客户服务）、Marketing Cloud（营销自动化），以及强大的 AppExchange 生态（3000+ 第三方应用）。

## 快速开始

### 安装

```bash
# macOS/Linux
npm install -g @salesforce/cli

# 验证安装
sf --version

# Windows (推荐使用安装包)
# 下载: https://developer.salesforce.com/tools/salesforcecli
```

### 认证配置（详细步骤）

Salesforce 支持多种认证方式，推荐使用 OAuth 2.0 Web Server Flow。

#### 方式一：OAuth Web Flow（推荐用于生产环境）

**步骤 1: 创建 Connected App**

1. 登录 Salesforce → 右上角齿轮图标 → Setup
2. 左侧搜索框输入 "App Manager" → 点击 "App Manager"
3. 点击右上角 "New Connected App"
4. 填写基本信息：
   - Connected App Name: `My Integration App`
   - API Name: 自动生成
   - Contact Email: 你的邮箱
5. 勾选 "Enable OAuth Settings"
6. 配置 OAuth 设置：
   - Callback URL: `http://localhost:1717/OauthRedirect`（CLI 默认）
   - Selected OAuth Scopes: 选择以下 scopes
     - `Full access (full)` — 完整访问权限
     - `Perform requests on your behalf at any time (refresh_token, offline_access)` — 离线刷新
     - `Access and manage your data (api)` — API 访问
7. 点击 "Save" → 等待 2-10 分钟生效
8. 回到 App Manager → 找到刚创建的 App → "View" → 复制 "Consumer Key" 和 "Consumer Secret"

**步骤 2: 获取 Access Token**

```bash
# 使用 CLI 交互式登录（会打开浏览器）
sf org login web --set-default-dev-hub --alias myOrg

# 或使用环境变量方式（CI/CD 场景）
export SF_CLIENT_ID="your_consumer_key"
export SF_CLIENT_SECRET="your_consumer_secret"
export SF_USERNAME="your_username"
export SF_PASSWORD="your_password+security_token"

sf org login jwt --client-id $SF_CLIENT_ID --jwt-key-file server.key --username $SF_USERNAME --set-default-dev-hub --alias myOrg
```

**步骤 3: 验证连接**

```bash
# 列出所有已连接的组织
sf org list

# 显示当前组织信息
sf org display --target-org myOrg

# 测试 API 调用
sf data query --query "SELECT Id, Name FROM Account LIMIT 5" --target-org myOrg
```

#### 方式二：Session-based Auth（用于沙箱测试）

```bash
# 使用用户名密码登录
sf org login web --instance-url https://test.salesforce.com --alias mySandbox
```

#### 常见认证错误及解决方案

1. **错误: `invalid_grant: authentication failure`**
   - 原因：密码错误或需要附加 Security Token
   - 解决：密码后面追加 Security Token（Setup → My Personal Information → Reset Security Token）

2. **错误: `API_DISABLED_FOR_ORG`**
   - 原因：组织未启用 API 访问权限
   - 解决：需升级到 Professional 版及以上

3. **错误: `REQUEST_LIMIT_EXCEEDED`**
   - 原因：API 调用超限
   - 解决：等待 24 小时重置或升级版本提升限额

### 环境变量配置

```bash
# 访问令牌
export SF_ACCESS_TOKEN="00D1234567890ABC..."

# 实例 URL
export SF_INSTANCE_URL="https://yourorg.my.salesforce.com"

# 用于 JWT 认证
export SF_CLIENT_ID="your_consumer_key"
export SF_USERNAME="user@example.com"
```

## 核心能力

### Sales Cloud 销售云核心功能

**对象管理（全对象 CRUD）**
- Lead（线索）：潜在客户跟踪
- Contact（联系人）：客户联系信息
- Account（客户）：公司/组织信息
- Opportunity（商机）：销售管道管理
- Task/Event（任务/事件）：活动跟踪
- Quote（报价单）：CPQ 集成
- Contract（合同）：合同生命周期管理

**高级销售功能**
- SOQL 查询（Salesforce Object Query Language）：类 SQL 的查询语言
- Bulk API 2.0：百万级数据批量导入导出
- 报表和 Dashboard：可视化销售漏斗和 KPI
- Lead/Opportunity Scoring：AI 驱动的评分
- Territory Management：销售区域管理
- Forecasting：销售预测和配额管理

### Service Cloud 服务云

- Case Management：工单创建、分配、升级、解决
- Knowledge Base：知识库文章管理和搜索
- Omni-Channel：智能路由，自动分配工单给合适座席
- Live Chat：实时客服聊天
- Field Service：现场服务调度（FSL - Field Service Lightning）
- Service Analytics：客服 KPI 和 CSAT 分析

### Marketing Cloud（需单独订阅）

Marketing Cloud 是独立的营销自动化平台，通过 API 集成：
- Email Studio：邮件营销和 A/B 测试
- Journey Builder：多渠道自动化客户旅程
- Audience Studio（CDP）：客户数据平台，统一客户画像
- Advertising Studio：Facebook/Google Ads 集成
- Social Studio：社交媒体监听和发布

### 自动化和 AI

- **Flow（Process Builder 替代者）**：可视化工作流，无需代码即可自动化复杂业务逻辑
- **Apex**：Salesforce 专有编程语言（类 Java），用于自定义业务逻辑
- **Einstein AI**：
  - Lead Scoring：预测线索转化概率
  - Opportunity Insights：商机成功率预测
  - Next Best Action：推荐最佳下一步行动

### 扩展和集成

- **AppExchange**：3000+ 第三方应用（CPQ、DocuSign、Gong、LinkedIn Sales Navigator）
- **REST/SOAP API**：几乎所有功能都可编程
- **Metadata API**：部署自定义对象、字段、Flow、Apex 代码
- **Streaming API**：实时事件推送（如 Opportunity 状态变更）
- **Sandbox**：多环境管理（Dev/QA/UAT/Prod）

## 使用示例（行业场景）

### 场景 1：SaaS 销售团队 - 查询本月新增高价值商机

```bash
# 查询 Amount > $50k 且本月创建的 Opportunity
sf data query --query "
  SELECT Id, Name, StageName, Amount, CloseDate, Account.Name, Owner.Name
  FROM Opportunity
  WHERE Amount > 50000
    AND CreatedDate = THIS_MONTH
    AND IsClosed = false
  ORDER BY Amount DESC
" --result-format json --target-org myOrg
```

### 场景 2：市场团队 - 批量导入线索（活动后）

```bash
# 准备 CSV 文件 leads.csv（包含 FirstName, LastName, Company, Email, LeadSource）
# LeadSource 示例: 'Webinar', 'Trade Show', 'Website'

sf data import bulk \
  --sobject Lead \
  --file leads.csv \
  --wait 10 \
  --target-org myOrg

# 批量更新 Lead Status
sf data upsert bulk \
  --sobject Lead \
  --file leads_update.csv \
  --external-id Email \
  --wait 10
```

### 场景 3：销售 AE - 推进商机阶段并记录活动

```bash
# 更新 Opportunity Stage
sf data update record \
  --sobject Opportunity \
  --record-id 0061234567890ABC \
  --values "StageName='Proposal/Price Quote' Probability=75" \
  --target-org myOrg

# 创建 Task（记录电话跟进）
sf data create record \
  --sobject Task \
  --values "Subject='Follow-up call' WhoId='0031234567890DEF' Status='Completed' ActivityDate='2026-04-08'" \
  --target-org myOrg
```

### 场景 4：销售运营 - 导出本季度销售漏斗报表

```bash
# 导出所有商机数据到 CSV
sf data query --query "
  SELECT 
    Id, Name, StageName, Amount, CloseDate, Probability,
    Account.Name, Owner.Name, LeadSource, CreatedDate
  FROM Opportunity
  WHERE CloseDate = THIS_QUARTER
  ORDER BY Amount DESC
" --result-format csv --target-org myOrg > q1_pipeline.csv
```

### 场景 5：客服团队 - 创建工单并分配

```bash
# 创建 Case（工单）
sf data create record \
  --sobject Case \
  --values "Subject='产品无法登录' Status='New' Priority='High' Origin='Email' SuppliedEmail='customer@example.com'" \
  --target-org myOrg

# 查询未解决的 High Priority Cases
sf data query --query "
  SELECT Id, CaseNumber, Subject, Status, Priority, CreatedDate
  FROM Case
  WHERE Status != 'Closed' AND Priority = 'High'
  ORDER BY CreatedDate DESC
" --target-org myOrg
```

### 场景 6：自动化 - 使用 Apex 批量合并重复 Lead

```bash
# 部署 Apex 类到组织
sf project deploy start --source-dir force-app/main/default/classes --target-org myOrg

# 执行匿名 Apex（去重逻辑）
sf apex run --file scripts/merge_duplicate_leads.apex --target-org myOrg
```

### 场景 7：集成场景 - 同步 HubSpot Contact 到 Salesforce Lead

```bash
# 使用 REST API 创建 Lead（可集成到 CI/CD）
curl -X POST "https://yourorg.my.salesforce.com/services/data/v60.0/sobjects/Lead" \
  -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "FirstName": "张",
    "LastName": "三",
    "Company": "科技公司",
    "Email": "zhang@example.com",
    "LeadSource": "HubSpot Import"
  }'
```

### 场景 8：复杂查询 - 关联查询（Parent-Child Relationship）

```bash
# 查询 Account 及其关联的所有 Opportunities
sf data query --query "
  SELECT Id, Name, Industry, 
    (SELECT Id, Name, StageName, Amount FROM Opportunities)
  FROM Account
  WHERE Industry = 'Technology'
  LIMIT 10
" --target-org myOrg
```

### 场景 9：数据备份 - 导出所有 Account 数据

```bash
# 导出所有客户数据（含自定义字段）
sf data export tree \
  --query "SELECT FIELDS(ALL) FROM Account LIMIT 200" \
  --output-dir ./backup \
  --plan \
  --target-org myOrg
```

### 场景 10：Sandbox 部署 - 从 Dev 部署到 QA

```bash
# 部署元数据（Flow, Custom Objects, Apex）
sf project deploy start \
  --source-dir force-app \
  --target-org myQASandbox \
  --test-level RunLocalTests \
  --wait 30
```

## 适用场景

### 典型工作流

1. **SDR（销售发展代表）工作流**
   - 导入展会/活动收集的 Leads
   - 批量分配 Lead 给 SDR
   - 记录电话/邮件触达（Task/Event）
   - 转化 Lead → Opportunity（当 Lead 合格）

2. **AE（客户经理）工作流**
   - 推进 Opportunity Stage（Discovery → Demo → Proposal → Negotiation → Closed Won）
   - 创建 Quote（报价单）
   - 生成合同（Contract）并发送给客户
   - 关单后触发 Onboarding Flow

3. **客服工作流**
   - 接收客户邮件/电话/聊天自动创建 Case
   - Omni-Channel 自动分配给空闲座席
   - 查询 Knowledge Base 寻找解决方案
   - 升级 Case（如无法解决）
   - 关闭 Case 并发送满意度调查（CSAT）

4. **销售运营工作流**
   - 导出月度/季度销售报表
   - 数据清洗（去重、补全字段）
   - 配置 Flow 自动化（如 Lead 7 天未跟进自动提醒）
   - 集成第三方工具（Slack 通知、Zapier 同步）

## 与其他工具的区分

### Salesforce vs HubSpot

| 维度 | Salesforce | HubSpot |
|------|-----------|---------|
| **定位** | 企业级 CRM（Fortune 500） | 中小企业 CRM + Inbound Marketing |
| **复杂度** | 高（需培训/顾问） | 低（易上手，免费版可用） |
| **定制性** | 极强（Apex/Flow/Custom Objects） | 中等（Workflows/Custom Properties） |
| **价格** | 高（$80-$330/用户/月） | 低（免费版 + $18-$3600/月） |
| **CPQ** | 内置 Salesforce CPQ（需额外购买） | 需第三方集成（如 PandaDoc） |
| **Marketing** | Marketing Cloud（独立产品，昂贵） | 内置 Marketing Hub（性价比高） |
| **适用场景** | 大型企业、复杂销售流程、多部门协作 | SMB、Inbound 营销、快速启动 |

### Salesforce vs Zoho CRM

| 维度 | Salesforce | Zoho CRM |
|------|-----------|---------|
| **价格** | $80-$330/用户/月 | $14-$52/用户/月（便宜 50%+） |
| **功能覆盖** | 100% 企业级功能 | 90% 功能，部分高级功能弱 |
| **生态** | AppExchange（3000+ 应用） | Zoho One（40+ 自有应用） |
| **自定义语言** | Apex（强大但复杂） | Deluge（学习曲线陡） |
| **UI/UX** | 现代化（Lightning） | 略显陈旧 |
| **适用场景** | 预算充足、需最强生态 | 预算有限、已用 Zoho 套件 |

### Salesforce vs Microsoft Dynamics 365

| 维度 | Salesforce | Dynamics 365 |
|------|-----------|--------------|
| **生态** | 纯云端，SaaS 先驱 | 云+本地部署，Microsoft 生态 |
| **集成** | AppExchange | Office 365/Teams/Power BI 原生集成 |
| **定价** | 按用户订阅 | 按用户 + 模块订阅 |
| **适用场景** | 云优先企业 | 深度使用 Microsoft 技术栈企业 |

## 限制和注意事项

### API 调用限额

| 版本 | 24小时限额 | 单次 Bulk API 批大小 | 存储限额 |
|------|-----------|---------------------|---------|
| **Professional** | 1,000 次/天 | 1 万条记录 | 10 GB |
| **Enterprise** | 100,000 次/天 | 1 万条记录 | 120 MB + 1 GB/用户 |
| **Unlimited** | 250,000 次/天 | 1 万条记录 | 120 MB + 1 GB/用户 |
| **Performance** | 500,000 次/天 | 1 万条记录 | 120 MB + 1 GB/用户 |

### 常见报错及解决方案

1. **`UNABLE_TO_LOCK_ROW`**
   - 原因：多个进程同时修改同一条记录
   - 解决：减少并发更新，使用批量更新代替单条更新

2. **`STORAGE_LIMIT_EXCEEDED`**
   - 原因：数据存储超限
   - 解决：清理旧数据或升级版本

3. **`FIELD_CUSTOM_VALIDATION_EXCEPTION`**
   - 原因：触发了自定义验证规则
   - 解决：检查数据是否符合业务规则（如必填字段、格式校验）

4. **`QUERY_TIMEOUT`**
   - 原因：SOQL 查询超时（复杂查询或数据量过大）
   - 解决：优化查询（添加索引字段、减少返回字段、使用 LIMIT）

5. **`INACTIVE_OWNER_OR_USER`**
   - 原因：记录 Owner 已被停用
   - 解决：重新分配记录给活跃用户

### 注意事项

- **Marketing Cloud 非标配**：需单独订阅，且价格不菲（起步 $1,250/月）
- **学习曲线陡峭**：Salesforce 功能强大但复杂，团队需培训
- **Apex 开发需谨慎**：部署到生产需 75% 测试覆盖率
- **Governor Limits**：Salesforce 有严格的执行限制（如单次 Apex 执行最多 10,000 条 SOQL 查询）
- **数据迁移复杂**：大量历史数据迁入需使用 Bulk API 或第三方工具（如 MuleSoft）

## 定价（详细分级）

### Sales Cloud 定价

| 版本 | 价格（年付） | 核心功能 | 适用场景 |
|------|------------|---------|---------|
| **Essentials** | $25/用户/月 | 最多 10 用户，基础 CRM | 小微团队试水 |
| **Professional** | $80/用户/月 | 完整 Sales Cloud，1000 API 调用/天 | 中小企业（10-100 人） |
| **Enterprise** | $165/用户/月 | +Flow 自动化 +API 10 万次/天 | 大企业（100-1000 人） |
| **Unlimited** | $330/用户/月 | +24/7 支持 +API 25 万次/天 | 关键业务系统 |
| **Einstein 1 Sales** | $500/用户/月 | +Einstein AI +Data Cloud | AI 驱动销售 |

### Service Cloud 定价

- **Essentials**: $25/用户/月（小团队客服）
- **Professional**: $80/用户/月（标准客服团队）
- **Enterprise**: $165/用户/月（多渠道客服）
- **Unlimited**: $330/用户/月（企业级客服中心）

### Marketing Cloud 定价

- **Email Studio**: $1,250/月起（10,000 联系人）
- **Marketing Cloud Engagement**: $12,500/月起（全功能营销自动化）
- **Data Cloud**: 需定制报价（CDP 客户数据平台）

### 附加费用

- **CPQ（配置-价格-报价）**: $75/用户/月
- **Field Service Lightning**: $50/用户/月
- **Data Storage Overage**: $10/GB/月

## 参考链接

### 官方文档

- [Salesforce 开发者文档](https://developer.salesforce.com/docs/)
- [Salesforce CLI 命令参考](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [REST API 完整参考](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/)
- [SOQL 查询语言指南](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/)
- [Apex 开发者指南](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/)

### 进阶学习

- [Trailhead（官方免费学习平台）](https://trailhead.salesforce.com/)
- [Salesforce Architects（架构师认证路径）](https://trailhead.salesforce.com/career-path/architect/)
- [AppExchange（应用市场）](https://appexchange.salesforce.com/)
- [Salesforce Integration Patterns](https://developer.salesforce.com/docs/atlas.en-us.integration_patterns_and_practices.meta/integration_patterns_and_practices/)

### 社区资源

- [Salesforce StackExchange](https://salesforce.stackexchange.com/)
- [Salesforce Developers YouTube](https://www.youtube.com/salesforcedevelopers)
- [Salesforce MVP Program](https://trailblazer.salesforce.com/hall-of-fame)
