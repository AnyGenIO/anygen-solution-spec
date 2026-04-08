<!-- doc-level: standard -->

# Attio API

新一代灵活CRM,类Notion的数据模型和实时协作体验,适合现代SaaS团队跨部门使用。

## 快速开始

### 认证配置
- **方式:** api_key
- **环境变量:** `ATTIO_API_KEY`
- **获取步骤:**
  1. 登录 Attio → Settings → Developers → API Keys
  2. Create → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $ATTIO_API_KEY" \
    "https://api.attio.com/v2/objects/people?limit=10"
  ```

## 核心能力
- 自定义对象和多对多关系(类Notion)
- People/Companies/Deals管理
- 邮件集成和追踪
- 实时协作和活动时间线
- 数据自动富化和Workflow自动化

## 使用示例

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
        "domains": ["example.com"]
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

## 适用场景
- 追求灵活数据模型避免传统CRM限制
- 跨团队协作(Sales/CS/Product)
- 中小型SaaS团队快速上手

## 限制和注意事项
- 相对年轻(2019年成立),企业级功能完善中
- API限流: 免费版60次/分钟, 付费版600次/分钟
- 生态和集成不如Salesforce

## 定价
- Free(10用户)
- Plus: $29/用户/月
- Pro: $59/用户/月
- Enterprise: 定制

## 参考链接
- [API文档](https://developers.attio.com/)
