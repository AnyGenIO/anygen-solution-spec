# Attio API

> 新一代灵活CRM,强大的数据模型和协作,适合现代SaaS团队(类Notion的CRM体验)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `ATTIO_API_KEY` — API Key
- **获取步骤:**
  1. 登录 Attio
  2. Settings → Developers → API Keys
  3. Create → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $ATTIO_API_KEY" \
    "https://api.attio.com/v2/objects/people?limit=10"
  ```

## 核心能力

### 灵活数据模型
- 自定义对象和属性(类Notion Database)
- 多对多关系(不局限于传统Lead→Account层级)
- 灵活视图(List/Board/Timeline)
- 高级过滤和分组

### CRM核心
- People/Companies/Deals管理
- 邮件集成(Gmail/Outlook双向同步)
- 邮件追踪(打开/点击/回复)
- 任务和提醒

### 协作
- 实时协作(多人同时编辑,类Google Docs)
- @提及和评论
- 活动时间线(所有互动历史)

### 自动化
- 数据自动富化(从邮件/LinkedIn自动提取信息)
- Workflow自动化(字段更新/任务分配)

### API和集成
- REST API(全功能)
- Webhooks(实时事件)
- Zapier/Make集成

## 使用示例

### 查询People
```bash
curl -X GET \
  "https://api.attio.com/v2/objects/people?limit=10" \
  -H "Authorization: Bearer $ATTIO_API_KEY"
```

### 创建Company
```bash
curl -X POST \
  "https://api.attio.com/v2/objects/companies" \
  -H "Authorization: Bearer $ATTIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "values": {
        "name": "科技公司",
        "domains": ["example.com"],
        "employee_count": 50
      }
    }
  }'
```

### 更新Deal
```bash
curl -X PATCH \
  "https://api.attio.com/v2/objects/deals/{record_id}" \
  -H "Authorization: Bearer $ATTIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "values": {
        "stage": "proposal",
        "value": {"currency_code": "USD", "value": 50000}
      }
    }
  }'
```

### 查询Companies with filters
```bash
curl -X POST \
  "https://api.attio.com/v2/objects/companies/records/query" \
  -H "Authorization: Bearer $ATTIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "filter": {
      "employee_count": {
        "$gte": 100
      }
    },
    "limit": 20
  }'
```

## 适用场景
- 现代SaaS团队追求灵活性和协作
- 传统CRM(Salesforce)太死板,想要自定义数据模型
- 重视团队实时协作(Sales/CS/Product跨团队使用)
- 中小型团队快速上手(UI直观类Notion)

## 限制和注意事项
- 相对新(2019年成立),企业级功能还在完善
- 生态和集成不如Salesforce/HubSpot
- 高级报表和预测功能有限
- API限流:
  - 免费版: 60次/分钟
  - 付费版: 600次/分钟

## 定价
- Free版(10用户)
- Plus: $29/用户/月
- Pro: $59/用户/月
- Enterprise: 定制

## 参考链接
- [官方网站](https://attio.com/)
- [开发者文档](https://developers.attio.com/)
- [API参考](https://developers.attio.com/reference)
