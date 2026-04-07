# HubSpot CLI

> HubSpot 官方命令行(偏开发部署),业务数据走REST API(全Hubs功能:Marketing/Sales/Service/CMS/Operations)

## 快速开始

### 安装
```bash
npm install -g @hubspot/cli
```

### 认证配置
- **方式:** api_key / oauth
- **环境变量:**
  - `HUBSPOT_ACCESS_TOKEN` — Private App Token(推荐)
- **获取步骤:**
  1. 登录 HubSpot → Settings
  2. Integrations → Private Apps → Create private app
  3. 配置 Scopes(如 crm.objects.contacts.read, crm.objects.deals.write)
  4. Create app → 复制 Access token
- **验证:**
  ```bash
  curl -H 'Authorization: Bearer $HUBSPOT_ACCESS_TOKEN' https://api.hubapi.com/crm/v3/objects/contacts?limit=1
  ```

## 核心能力

### Sales Hub
- 联系人/公司/交易/票据 CRUD(CRM Objects API)
- 管道(Pipeline)和阶段自定义
- 邮件跟踪(打开/点击)
- 邮件序列(Sequences)自动化
- 通话记录和录音
- 会议预约(Meetings工具)
- Sales Analytics和预测

### Marketing Hub
- 表单和落地页管理
- 邮件营销(Marketing Email)
- 工作流自动化(Workflows,类似Salesforce Flow)
- Lead评分(Lead Scoring)
- 广告管理(Facebook/Google Ads ROI追踪)
- SEO优化建议

### Service Hub
- 工单(Ticket)管理
- 知识库(Knowledge Base)
- 客户反馈调查(NPS/CSAT)
- 实时聊天(Live Chat)
- 聊天机器人(Chatbot Builder)

### CMS Hub
- 网站页面和博客管理
- 内容优化(SEO/A/B测试)
- 会员登录和个性化

### Operations Hub
- 数据同步(双向同步Salesforce/其他系统)
- 数据质量自动化(去重/格式化)
- 可编程自动化(Custom Code Workflows)
- Webhooks(事件驱动集成)

### 扩展
- HubSpot App Marketplace(1000+集成)
- 自定义对象(Custom Objects,类似Salesforce)
- HubDB(内容数据库,存储结构化数据)

## 使用示例

### 查询联系人
```bash
curl -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  'https://api.hubapi.com/crm/v3/objects/contacts?limit=10&properties=firstname,lastname,email'
```

### 创建新的交易(Deal)
```bash
curl -X POST \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.hubapi.com/crm/v3/objects/deals \
  -d '{
    "properties": {
      "dealname": "新客户合作",
      "amount": "50000",
      "dealstage": "presentationscheduled",
      "pipeline": "default"
    }
  }'
```

### 更新联系人
```bash
curl -X PATCH \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.hubapi.com/crm/v3/objects/contacts/12345 \
  -d '{
    "properties": {
      "phone": "+86-138-0000-0000",
      "lifecyclestage": "customer"
    }
  }'
```

### 搜索公司
```bash
curl -X POST \
  -H "Authorization: Bearer $HUBSPOT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.hubapi.com/crm/v3/objects/companies/search \
  -d '{
    "filterGroups": [{
      "filters": [{
        "propertyName": "industry",
        "operator": "EQ",
        "value": "TECHNOLOGY"
      }]
    }],
    "limit": 10
  }'
```

## 适用场景
- 中小企业一体化CRM+营销+服务(比Salesforce便宜且易用)
- Inbound营销策略(内容+SEO+Lead Nurturing)
- 创建/更新联系人和交易
- 营销自动化(邮件培育/Lead评分)
- 客服工单和知识库
- 网站和落地页一站式管理

## 限制和注意事项
- 免费版功能受限:
  - 最多1个Dashboard
  - 无工作流自动化
  - 邮件发送有HubSpot品牌
- API限流:
  - Private App: 100次/10秒
  - OAuth: 150次/10秒
- 高级功能需Professional/Enterprise版($800-$3,600/月起)
- 与Salesforce比:企业级高级功能弱(如CPQ需第三方)

## 定价
- Free版(有限功能)
- Starter: $18/月起
- Professional: $800/月起(3个席位)
- Enterprise: $3,600/月起(5个席位)

## 参考链接
- [官方文档](https://developers.hubspot.com/docs/api/overview)
- [API参考](https://developers.hubspot.com/docs/api/crm/understanding-the-crm)
- [CLI文档](https://developers.hubspot.com/docs/cms/developer-reference/local-development-cli)
