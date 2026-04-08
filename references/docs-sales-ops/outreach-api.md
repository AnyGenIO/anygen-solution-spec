<!-- doc-level: detailed -->

# Outreach API

> 销售自动化平台 API,序列化邮件/电话/LinkedIn 触达,销售团队提效利器

Outreach 是企业级销售参与平台(Sales Engagement Platform),专注于自动化和优化 SDR/AE 的多渠道触达流程。通过 Sequence(序列)功能,销售团队可以设计多步骤触达策略(Day 1 邮件 → Day 3 跟进 → Day 7 电话),并通过 AI 分析邮件打开/回复数据优化话术。Outreach API v2 提供完整的 REST 接口,支持 Prospect(联系人)、Sequence、Task、Mailing 等核心对象的编程操作,适合构建自动化销售工作流和 CRM 深度集成。

## 为什么选这个工具

### Outreach vs Salesloft vs HubSpot Sales

| 维度 | Outreach | Salesloft | HubSpot Sales |
|------|----------|-----------|--------------|
| **定位** | 企业级销售自动化 | 企业级销售参与平台 | 中小企业销售 + 营销 |
| **价格** | $100+/用户/月 | $100+/用户/月 | 免费-$50/用户/月 |
| **Sequence 功能** | 极强(多步骤 + 条件分支) | 极强(类似 Outreach) | 基础序列化邮件 |
| **A/B 测试** | 内置(邮件主题/正文) | 内置 | 需手动对比 |
| **电话集成** | 原生集成(拨号器 + 录音) | 原生集成 | 第三方集成 |
| **LinkedIn 自动化** | 支持(Sales Navigator 集成) | 支持 | 需手动操作 |
| **AI 分析** | 邮件情绪分析,最佳发送时间 | Conversation Intelligence | 基础分析 |
| **CRM 集成** | Salesforce/HubSpot/Dynamics | Salesforce 为主 | 原生 HubSpot CRM |
| **API 成熟度** | 成熟(REST + Webhooks) | 成熟 | 成熟 |
| **适用场景** | 大型销售团队(100+ SDR/AE) | 大型销售团队 | 中小企业快速启动 |

## 快速开始

### 安装

无需安装 CLI,Outreach 使用标准 HTTP API 调用。推荐工具:

```bash
# 使用 curl(Linux/macOS 自带)
curl --version

# Python 第三方库(非官方)
pip install requests  # 直接用 requests 库调用 REST API

# Node.js
npm install axios

# Postman Collection(官方提供)
# 下载: https://developers.outreach.io/api/postman-collection/
```

### 认证配置(详细步骤)

Outreach 使用 OAuth 2.0 认证,支持 Authorization Code Flow(适合 Web 应用)和 Application Flow(适合后端服务)。

#### 方式一:OAuth 2.0 Authorization Code Flow(推荐)

**步骤 1:创建 OAuth 应用**

1. 登录 Outreach → 点击右上角头像
2. 进入 **Settings → Platform → OAuth Applications**
3. 点击 **Create New Application**
4. 填写应用信息:
   - Application Name: `My Sales Automation`
   - Redirect URIs: `https://yourapp.com/oauth/callback`
     (开发环境可用 `http://localhost:8080/callback`)
5. 点击 **Create** → 记录 **Client ID** 和 **Client Secret**

**步骤 2:获取 Authorization Code**

在浏览器中访问以下 URL(替换 `{CLIENT_ID}` 和 `{REDIRECT_URI}`):

```
https://api.outreach.io/oauth/authorize?
  client_id={CLIENT_ID}&
  redirect_uri={REDIRECT_URI}&
  response_type=code&
  scope=prospects.read prospects.write sequenceStates.write tasks.all
```

示例 Scope 说明:
- `prospects.read`:读取 Prospect 数据
- `prospects.write`:创建和编辑 Prospect
- `sequenceStates.write`:将 Prospect 加入 Sequence
- `tasks.all`:完整 Task 管理权限
- `mailings.read`:读取邮件统计数据
- `calls.all`:通话记录管理

用户授权后,浏览器跳转到 `REDIRECT_URI?code={AUTHORIZATION_CODE}`。

**步骤 3:交换 Access Token**

```bash
curl -X POST https://api.outreach.io/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id={CLIENT_ID}" \
  -d "client_secret={CLIENT_SECRET}" \
  -d "redirect_uri={REDIRECT_URI}" \
  -d "grant_type=authorization_code" \
  -d "code={AUTHORIZATION_CODE}"
```

响应示例:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "Bearer",
  "expires_in": 7200,
  "refresh_token": "def502003e8f7a2b...",
  "scope": "prospects.read prospects.write"
}
```

**步骤 4:刷新 Access Token**

Access Token 有效期 2 小时,需使用 Refresh Token 定期刷新:

```bash
curl -X POST https://api.outreach.io/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id={CLIENT_ID}" \
  -d "client_secret={CLIENT_SECRET}" \
  -d "grant_type=refresh_token" \
  -d "refresh_token={REFRESH_TOKEN}"
```

#### 方式二:Application Token(适合内部工具)

1. 登录 Outreach → Settings → Platform → API Tokens
2. 点击 **Create New Token**
3. 选择 Scope(权限)
4. 记录生成的 Token(格式: `Bearer eyJ0eXAiOiJKV1Q...`)
5. 直接在请求头中使用(无需 OAuth 流程)

**注意**:Application Token 无过期时间,但权限受限(不能以用户身份操作)。

#### 常见认证错误及解决方案

1. **错误:`invalid_client`**
   - 原因:Client ID 或 Client Secret 错误
   - 解决:检查 OAuth 应用配置

2. **错误:`invalid_grant`(刷新 Token 时)**
   - 原因:Refresh Token 已过期或被撤销
   - 解决:重新执行 OAuth 授权流程

3. **错误:`unauthorized`(API 调用时)**
   - 原因:Access Token 已过期或无效
   - 解决:使用 Refresh Token 获取新 Access Token

4. **错误:`forbidden`**
   - 原因:Token 的 Scope 不足
   - 解决:重新授权时添加所需 Scope

5. **错误:`rate_limit_exceeded`**
   - 原因:API 调用超限(10,000 次/小时/用户)
   - 解决:实现退避重试逻辑,或联系 Outreach 提升限额

### 环境变量配置

```bash
# Outreach OAuth 凭证
export OUTREACH_CLIENT_ID="your_client_id"
export OUTREACH_CLIENT_SECRET="your_client_secret"
export OUTREACH_ACCESS_TOKEN="eyJ0eXAiOiJKV1QiLCJhbGc..."
export OUTREACH_REFRESH_TOKEN="def502003e8f7a2b..."

# API 基础 URL(固定)
export OUTREACH_API_BASE="https://api.outreach.io/api/v2"
```

### 验证

```bash
# 验证 Access Token 是否有效(查询当前用户信息)
curl -X GET \
  "https://api.outreach.io/api/v2/users" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json"

# 成功响应示例(JSON:API 格式)
# {"data":[{"type":"user","id":"1","attributes":{"email":"john@example.com","firstName":"John"}}]}
```

## 核心能力

### Prospect(联系人)管理

- **CRUD 操作**:创建、查询、更新、删除 Prospect
- **字段支持**:姓名、邮箱、电话、公司、职位、自定义字段
- **批量导入**:CSV 导入(通过 Web UI)或 API 批量创建
- **去重检测**:基于邮箱自动去重
- **阶段管理**:Prospect Stage(Cold/Warm/Hot)

### Sequence(触达序列)管理

- **多步骤触达**:设计 7-15 步触达流程(邮件 + 电话 + LinkedIn)
- **触达类型**:
  - Email:自动发送个性化邮件
  - Call:提醒 SDR 拨打电话
  - LinkedIn Message:提示发送 LinkedIn 消息
  - Manual Task:自定义任务(如 "寄送礼品")
- **条件分支**:根据邮件打开/回复自动调整后续步骤
- **A/B 测试**:测试不同邮件主题/正文的效果
- **Sequence Enrollment**:将 Prospect 加入 Sequence(通过 API 或手动)

### Task(任务)管理

- **任务类型**:Call、Email、LinkedIn、Generic
- **优先级管理**:High/Medium/Low
- **到期时间**:自动提醒逾期任务
- **完成状态**:Pending/Completed/Skipped
- **批量操作**:批量创建/更新/完成任务

### Mailing(邮件)统计

- **邮件状态**:Scheduled(待发送)、Sent(已发送)、Delivered(已送达)、Opened(已打开)、Clicked(已点击)、Replied(已回复)、Bounced(退信)
- **打开率/回复率分析**:按 Sequence/Template 统计
- **最佳发送时间推荐**:基于历史打开时间
- **邮件模板管理**:创建和编辑邮件模板(支持变量插入)

### Call(通话)管理

- **拨号器集成**:一键拨号
- **通话录音**:自动录音并转写(需启用 Conversation Intelligence)
- **通话时长统计**:自动记录
- **通话结果**:Connected、Left Voicemail、No Answer、Busy

### Account(客户)管理

- **Account 对象**:公司/组织信息
- **与 Prospect 关联**:一个 Account 对应多个 Prospect
- **Account 层级统计**:该 Account 下所有 Prospect 的触达数据汇总

### Webhook 和事件订阅

- **实时事件推送**:Prospect 创建、邮件打开、回复等事件
- **Webhook 配置**:设置 Webhook URL 接收事件
- **事件类型**:prospect.created、mailing.opened、mailing.replied、call.completed

## 使用示例(行业场景)

### 场景 1:SaaS SDR - 批量创建 Prospect 并加入 Sequence

```bash
# 步骤 1: 创建 Prospect
curl -X POST \
  "https://api.outreach.io/api/v2/prospects" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -d '{
    "data": {
      "type": "prospect",
      "attributes": {
        "firstName": "Zhang",
        "lastName": "San",
        "emails": ["zhang@techcorp.com"],
        "company": {
          "name": "Tech Corp",
          "domain": "techcorp.com"
        },
        "title": "VP of Sales",
        "phoneNumbers": [{"value": "+1-415-555-0001", "type": "work"}]
      }
    }
  }'

# 响应示例
# {"data":{"type":"prospect","id":"123","attributes":{"firstName":"Zhang","lastName":"San"}}}

# 步骤 2: 将 Prospect 加入 Sequence
curl -X POST \
  "https://api.outreach.io/api/v2/sequenceStates" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -d '{
    "data": {
      "type": "sequenceState",
      "relationships": {
        "prospect": {
          "data": {"type": "prospect", "id": "123"}
        },
        "sequence": {
          "data": {"type": "sequence", "id": "456"}
        }
      }
    }
  }'
```

### 场景 2:销售运营 - 查询本周新增 Prospect

```bash
# 查询本周创建的 Prospect(按创建时间过滤)
curl -X GET \
  "https://api.outreach.io/api/v2/prospects?filter[createdAt][gte]=2026-04-06T00:00:00Z&page[size]=50&sort=-createdAt" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json"

# 响应示例(JSON:API 分页格式)
# {"data":[{"type":"prospect","id":"123","attributes":{"firstName":"Zhang"}}],"links":{"next":"..."}}
```

### 场景 3:SDR - 查询今日待完成任务

```bash
# 查询今日到期的 Call 任务
curl -X GET \
  "https://api.outreach.io/api/v2/tasks?filter[action]=call&filter[dueAt][lte]=2026-04-08T23:59:59Z&filter[state]=pending&page[size]=50" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json"
```

### 场景 4:SDR - 完成任务并记录结果

```bash
# 完成 Call 任务
curl -X PATCH \
  "https://api.outreach.io/api/v2/tasks/789" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -d '{
    "data": {
      "type": "task",
      "id": "789",
      "attributes": {
        "state": "completed",
        "note": "Left voicemail, will follow up via email"
      }
    }
  }'
```

### 场景 5:市场团队 - 查询 Sequence 邮件打开率

```bash
# 查询指定 Sequence 的所有邮件
curl -X GET \
  "https://api.outreach.io/api/v2/mailings?filter[sequenceId]=456&page[size]=200" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json"

# 用 jq 统计打开率
# curl ... | jq '[.data[]] | {total: length, opened: [.[] | select(.attributes.openedAt != null)] | length}'
```

### 场景 6:AE - 从 Sequence 中移除已回复的 Prospect

```bash
# 查询已回复的 Prospect
curl -X GET \
  "https://api.outreach.io/api/v2/sequenceStates?filter[sequenceId]=456&filter[state]=replied&page[size]=50" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json"

# 暂停 SequenceState(移出 Sequence)
curl -X PATCH \
  "https://api.outreach.io/api/v2/sequenceStates/999" \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -d '{
    "data": {
      "type": "sequenceState",
      "id": "999",
      "attributes": {
        "state": "paused"
      }
    }
  }'
```

### 场景 7:集成场景 - Webhook 接收邮件回复通知

**在 Outreach 中配置 Webhook(Settings → Webhooks)**

```bash
# Webhook Payload 示例(POST 到你的服务器)
{
  "meta": {
    "eventName": "mailing.replied"
  },
  "data": {
    "type": "mailing",
    "id": "111",
    "attributes": {
      "repliedAt": "2026-04-08T10:30:00Z",
      "subject": "Re: Quick question about your sales process"
    },
    "relationships": {
      "prospect": {"data": {"type": "prospect", "id": "123"}}
    }
  }
}

# 你的服务器接收后,可以触发后续动作(如通知 Slack)
curl -X POST https://hooks.slack.com/services/YOUR/WEBHOOK/URL \
  -H "Content-Type: application/json" \
  -d '{"text": "New reply from Zhang San! Check Outreach."}'
```

### 场景 8:自动化 - 从 Salesforce Lead 同步到 Outreach Prospect

```bash
# 从 Salesforce 查询新 Lead(伪代码)
# salesforce_leads = sf.query("SELECT Name, Email, Company FROM Lead WHERE CreatedDate = TODAY")

# 批量创建 Outreach Prospect
for lead in salesforce_leads:
  curl -X POST \
    "https://api.outreach.io/api/v2/prospects" \
    -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
    -H "Content-Type: application/vnd.api+json" \
    -d "{
      \"data\": {
        \"type\": \"prospect\",
        \"attributes\": {
          \"firstName\": \"${lead.FirstName}\",
          \"lastName\": \"${lead.LastName}\",
          \"emails\": [\"${lead.Email}\"],
          \"company\": {\"name\": \"${lead.Company}\"}
        }
      }
    }"
done
```

## 适用场景

### 典型工作流

1. **SDR 外呼工作流(Outbound Sales)**
   - 从 ZoomInfo/Apollo 导入目标联系人到 Outreach
   - 批量创建 Prospect 并加入 "Cold Outreach" Sequence
   - Sequence 自动发送:
     - Day 1:首次邮件介绍产品价值
     - Day 3:跟进邮件(附案例研究)
     - Day 5:电话 + 语音留言
     - Day 7:LinkedIn 连接请求
     - Day 10:最后一封"分手邮件"(Breakup Email)
   - 邮件打开/点击自动创建 Task 提醒 SDR 跟进
   - Prospect 回复后自动暂停 Sequence,转交给 AE

2. **活动后跟进工作流(Event Follow-up)**
   - 展会/Webinar 后导入参会者名单
   - 创建 "Event Follow-up" Sequence:
     - Day 1:感谢参加邮件(附 PPT 下载链接)
     - Day 3:邀请 Demo 邮件
     - Day 7:电话跟进
   - 报名 Demo 的 Prospect 自动转入 "Demo Nurture" Sequence

3. **AE 重新激活沉睡客户工作流(Re-engagement)**
   - 查询 6 个月未互动的老客户
   - 创建 "Re-engagement" Sequence:
     - Day 1:分享行业趋势报告
     - Day 5:邀请参加新产品发布会
     - Day 10:电话联系询问近况
   - 回复的客户创建新 Opportunity 到 Salesforce

4. **销售运营监控工作流(Sales Ops Monitoring)**
   - 每日查询 Sequence 邮件打开率/回复率
   - 识别低绩效 Sequence(回复率 < 5%)并优化话术
   - 导出 Prospect 数据到 BI 工具(如 Tableau)做漏斗分析
   - 设置 Webhook 监听高价值 Prospect 的回复,实时通知 AE

## 与其他工具的区分

### Outreach vs Apollo.io

| 维度 | Outreach | Apollo.io |
|------|----------|-----------|
| **核心定位** | 销售参与平台(SEP) | 数据 + 销售参与一体化 |
| **联系人数据库** | 无(需接入第三方) | 内置 2.5 亿+ 联系人数据库 |
| **价格** | $100+/用户/月 | $49-$149/用户/月 |
| **Sequence 功能** | 极强(多步骤 + A/B 测试) | 强(基础序列化) |
| **CRM 集成** | Salesforce/HubSpot 深度集成 | 集成但较浅 |
| **适用场景** | 已有联系人数据,需强大 SEP | 需要数据挖掘 + 基础 SEP |

### Outreach vs Groove(by Clari)

| 维度 | Outreach | Groove |
|------|----------|--------|
| **Salesforce 集成** | 深度集成 | 原生嵌入 Salesforce(更深) |
| **价格** | $100+/用户/月 | $75+/用户/月 |
| **独立性** | 独立平台 | 依赖 Salesforce |
| **适用场景** | 跨 CRM 团队 | 深度使用 Salesforce 团队 |

## 限制和注意事项

### API 调用限额

| 维度 | 限制 | 备注 |
|------|------|------|
| **速率限制** | 10,000 次/小时/用户 | 超限返回 429 错误 |
| **批量创建** | 单次最多 100 条记录 | 需分批请求 |
| **分页大小** | 默认 50,最大 1000 | 使用 `page[size]` 参数 |
| **Webhook 重试** | 失败后重试 3 次(1/5/15 分钟) | 确保 Webhook 端点稳定 |
| **邮件发送限额** | 受邮箱提供商限制 | Gmail:500 封/天,Outlook:300 封/天 |

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **401 Unauthorized** | Access Token 无效/过期 | 使用 Refresh Token 获取新 Token |
| **403 Forbidden** | Scope 不足 | 检查 OAuth Scope 配置 |
| **404 Not Found** | 资源不存在(如 Prospect ID 错误) | 检查 ID 是否正确 |
| **422 Unprocessable Entity** | 请求参数错误(如缺少必填字段) | 检查 API 文档中的必填字段 |
| **429 Too Many Requests** | API 限流 | 实现指数退避重试,或减少请求频率 |
| **500 Internal Server Error** | Outreach 服务器错误 | 稍后重试,或联系支持 |

### 注意事项

- **邮箱预热**:新邮箱需逐步增加发送量(Week 1:20 封/天 → Week 2:50 封/天 → Week 4:200 封/天),避免进垃圾箱
- **Sequence 步骤限制**:单个 Sequence 最多 30 步(实际建议 7-15 步)
- **LinkedIn 自动化风险**:LinkedIn 禁止过度自动化,需手动发送消息(Outreach 仅提示)
- **不包含联系人数据**:Outreach 不提供联系人数据库,需配合 ZoomInfo/Apollo/Cognism 等工具
- **邮件模板变量**:支持 `{{firstName}}` 等变量,需确保 Prospect 字段有值
- **API 格式**:使用 JSON:API 规范(与普通 REST JSON 略有不同)
- **GDPR 合规**:欧盟用户需遵守 GDPR,提供退订选项

## 定价(详细分级)

### Outreach 定价

| 版本 | 价格(估算) | 核心功能 | 适用场景 |
|------|----------|---------|---------|
| **Starter** | $100/用户/月起 | Sequence,邮件追踪,Task 管理 | 小型销售团队(5-20 人) |
| **Professional** | $150/用户/月起 | +A/B 测试,高级报表,Webhook | 成长型团队(20-100 人) |
| **Enterprise** | 定制报价 | +Conversation Intelligence,SSO,SLA | 大型企业(100+ 人) |

**注意**:Outreach 不公开定价,需联系销售获取报价(通常起步 $100-150/用户/月,按年付费)。

### 附加费用

- **Conversation Intelligence(通话录音 + AI 分析)**:需额外付费(约 $50/用户/月)
- **Outreach Commit(销售预测)**:需额外付费
- **Outreach Kaia(AI 销售助手)**:需额外付费

### 免费试用

- **14 天免费试用**:可申请试用账号(https://www.outreach.io/trial)

## 参考链接

### 官方文档

- [Outreach API 文档](https://api.outreach.io/api/v2/docs)
- [API 参考](https://api.outreach.io/api/v2/docs)
- [OAuth 2.0 指南](https://api.outreach.io/api/v2/docs#authentication)
- [Webhook 指南](https://api.outreach.io/api/v2/docs#webhooks)
- [速率限制说明](https://api.outreach.io/api/v2/docs#rate-limiting)

### 进阶学习

- [Outreach University(官方培训)](https://university.outreach.io/)
- [Sequence 最佳实践](https://www.outreach.io/resources/blog/sequence-best-practices)
- [邮件模板库](https://www.outreach.io/resources/email-templates)
- [A/B 测试指南](https://www.outreach.io/resources/blog/email-ab-testing)

### 社区资源

- [Outreach Community](https://community.outreach.io/)
- [Outreach API GitHub(示例代码)](https://github.com/getoutreach)
- [Postman Collection](https://developers.outreach.io/api/postman-collection/)
- [Stack Overflow(outreach-api 标签)](https://stackoverflow.com/questions/tagged/outreach-api)

### SDK 和工具

- [Python Requests(推荐直接用)](https://docs.python-requests.org/)
- [Node.js Axios](https://axios-http.com/)
- [Zapier 集成](https://zapier.com/apps/outreach/integrations)
- [Make(Integromat)集成](https://www.make.com/en/integrations/outreach)
