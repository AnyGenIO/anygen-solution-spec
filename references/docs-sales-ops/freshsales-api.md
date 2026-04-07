# Freshsales API

> AI驱动的轻量级CRM,Freshworks旗下(客服Freshdesk同厂),侧重AI Lead评分和内置电话

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `FRESHSALES_API_KEY` — API Key
  - `FRESHSALES_DOMAIN` — 域名(如 yourcompany.freshsales.io)
- **获取步骤:**
  1. 登录 Freshsales
  2. Settings → API Settings
  3. Generate API Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Token token=$FRESHSALES_API_KEY" \
    "https://$FRESHSALES_DOMAIN/api/leads/view/12345"
  ```

## 核心能力

### 核心CRM
- Lead/Contact/Account/Deal管理
- 自定义字段和模块
- 销售Pipeline可视化

### AI功能(Freddy AI)
- AI Lead评分(预测成单概率)
- Deal智能提醒(风险预警/下一步建议)
- 邮件自动回复建议
- 最佳联系时间推荐

### 内置通讯
- 内置Cloud Phone(拨号器+通话录音,需额外付费)
- 邮件集成(Gmail/Outlook双向同步)
- 邮件追踪(打开/点击)

### 自动化
- Workflow自动化(触发器-动作)
- 邮件序列(Sales Sequences)
- 自动分配规则

### 集成
- Freshdesk客服系统无缝联动
- Zapier/Segment集成
- Marketplace(50+原生集成)

## 使用示例

### 获取所有Lead
```bash
curl -H "Authorization: Token token=$FRESHSALES_API_KEY" \
  "https://$FRESHSALES_DOMAIN/api/leads/view/12345?page=1&per_page=10"
```

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
      "email": "zhang@example.com",
      "mobile_number": "+86-138-0000-0000",
      "lead_source_id": "12345"
    }
  }'
```

### 更新Deal阶段
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

### 记录活动
```bash
curl -X POST \
  -H "Authorization: Token token=$FRESHSALES_API_KEY" \
  -H "Content-Type: application/json" \
  "https://$FRESHSALES_DOMAIN/api/notes" \
  -d '{
    "note": {
      "description": "与客户通话,讨论了需求",
      "targetable_type": "Deal",
      "targetable_id": 67890
    }
  }'
```

## 适用场景
- 小型销售团队(<50人)快速上手
- 重视AI辅助(Lead评分/下一步建议)
- 已用Freshdesk做客服,想打通Sales-Service数据
- 需要内置电话功能(不想单独买Twilio/Aircall)

## 限制和注意事项
- API限流: 100次/分钟
- 企业级功能有限(无CPQ/高级预测/Territory管理)
- 集成生态不如Salesforce/HubSpot
- 主要面向中小团队

## 定价
- Growth: $15/用户/月
- Pro: $39/用户/月
- Enterprise: $69/用户/月

## 参考链接
- [官方文档](https://developers.freshworks.com/crm/api/)
- [API参考](https://developers.freshsales.io/api/)
