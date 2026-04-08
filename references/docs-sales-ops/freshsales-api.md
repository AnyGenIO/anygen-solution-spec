<!-- doc-level: standard -->

# Freshsales API

AI驱动的轻量级CRM,Freshworks旗下产品,侧重AI Lead评分和内置电话功能,适合小型销售团队。

## 快速开始

### 认证配置
- **方式:** api_key
- **环境变量:** `FRESHSALES_API_KEY`, `FRESHSALES_DOMAIN`
- **获取步骤:**
  1. 登录 Freshsales → Settings → API Settings
  2. Generate API Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Token token=$FRESHSALES_API_KEY" \
    "https://$FRESHSALES_DOMAIN/api/leads/view/12345"
  ```

## 核心能力
- Lead/Contact/Deal管理
- AI Lead评分和Deal智能提醒(Freddy AI)
- 内置Cloud Phone和邮件集成
- Workflow自动化和邮件序列
- 与Freshdesk客服系统联动

## 使用示例

### 创建Lead
```bash
curl -X POST \
  -H "Authorization: Token token=$FRESHSALES_API_KEY" \
  -H "Content-Type: application/json" \
  "https://$FRESHSALES_DOMAIN/api/leads" \
  -d '{
    "lead": {
      "first_name": "张",
      "last_name": "三",
      "email": "zhang@example.com"
    }
  }'
```

### 更新Deal
```bash
curl -X PUT \
  -H "Authorization: Token token=$FRESHSALES_API_KEY" \
  -H "Content-Type: application/json" \
  "https://$FRESHSALES_DOMAIN/api/deals/67890" \
  -d '{
    "deal": {
      "deal_stage_id": "34567",
      "amount": 50000
    }
  }'
```

## 适用场景
- 小型销售团队需要AI辅助决策
- 已使用Freshdesk客服系统
- 需要内置电话功能避免额外集成

## 限制和注意事项
- API限流: 100次/分钟
- 企业级功能有限(无CPQ/Territory管理)
- 集成生态不如Salesforce

## 定价
- Growth: $15/用户/月
- Pro: $39/用户/月
- Enterprise: $69/用户/月

## 参考链接
- [API文档](https://developers.freshworks.com/crm/api/)
