# Calendly API

> 自助日程预约,客户按可用时间自选会议时间(无需邮件来回)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key (Personal Access Token)
- **环境变量:**
  - `CALENDLY_API_KEY` — Calendly Personal Access Token
- **获取步骤:**
  1. 登录 Calendly
  2. Integrations → API & Webhooks
  3. Personal Access Token → Generate New Token
  4. 复制保存 Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $CALENDLY_API_KEY" https://api.calendly.com/users/me
  ```

## 核心能力

### Event Types 管理
- 创建不同类型的会议(Demo/Discovery Call/30分钟咨询等)
- 设置会议时长和可用时间
- 自定义会议前问题收集
- 会议确认和提醒邮件配置

### 日程预约
- 获取用户空闲时间
- 查询已安排会议列表
- 取消和重新安排会议
- 与Google/Outlook日历双向同步

### 自动化
- Webhook通知(会议预约/取消/重新安排)
- 会后跟进邮件自动发送
- 自动添加Zoom/Google Meet链接
- 会议录制和转录(集成第三方)

### 数据分析
- 会议预约统计
- No-show率追踪
- 热门时间段分析

## 使用示例

### 获取当前用户信息
```bash
curl -H "Authorization: Bearer $CALENDLY_API_KEY" \
  https://api.calendly.com/users/me
```

### 列出所有Event Types
```bash
curl -H "Authorization: Bearer $CALENDLY_API_KEY" \
  "https://api.calendly.com/event_types?user=https://api.calendly.com/users/AAAAAAAAAAAAAAAA"
```

### 查询已安排的会议
```bash
# 获取组织URI
ORG_URI=$(curl -s -H "Authorization: Bearer $CALENDLY_API_KEY" \
  https://api.calendly.com/users/me | jq -r '.resource.current_organization')

# 查询近期会议
curl -H "Authorization: Bearer $CALENDLY_API_KEY" \
  "https://api.calendly.com/scheduled_events?organization=$ORG_URI&count=10"
```

### 获取会议详情(包含参会者信息)
```bash
EVENT_URI="https://api.calendly.com/scheduled_events/AAAAAAAAAAAAAAAA"

curl -H "Authorization: Bearer $CALENDLY_API_KEY" \
  "$EVENT_URI"

# 获取参会者信息
curl -H "Authorization: Bearer $CALENDLY_API_KEY" \
  "$EVENT_URI/invitees"
```

### 取消会议
```bash
INVITEE_URI="https://api.calendly.com/scheduled_events/AAAAAAAAAAAAAAAA/invitees/BBBBBBBBBBBBBBBB"

curl -X POST \
  -H "Authorization: Bearer $CALENDLY_API_KEY" \
  -H "Content-Type: application/json" \
  "$INVITEE_URI/cancellation" \
  -d '{"reason": "客户临时有事需要重新安排"}'
```

### 创建Webhook订阅
```bash
curl -X POST \
  -H "Authorization: Bearer $CALENDLY_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.calendly.com/webhook_subscriptions \
  -d '{
    "url": "https://your-domain.com/calendly-webhook",
    "events": ["invitee.created", "invitee.canceled"],
    "organization": "https://api.calendly.com/organizations/AAAAAAAAAAAAAAAA",
    "scope": "organization"
  }'
```

## 适用场景
- SDR拿到回复后发Calendly链接预约Demo
- 减少邮件来回确认时间(客户自选时间)
- 官网放Calendly链接实现自助预约
- Inbound Lead自动分配销售+预约会议
- 客户成功团队预约季度业务回顾(QBR)

## 限制和注意事项
- 免费版仅支持1个Event Type
- API限流: 1000次/分钟
- 会议时长最短15分钟
- 不支持直接创建会议(只能通过预约链接由客户预约)
- Webhook需要HTTPS端点

## 定价
- Free版: 1个Event Type,基础功能
- Essentials: $10/用户/月 - 无限Event Types,去除品牌
- Professional: $15/用户/月 - 自动化提醒,Salesforce集成
- Teams: $20/用户/月 - Round Robin分配,团队协作

## 参考链接
- [官方文档](https://developer.calendly.com/api-docs)
- [API参考](https://developer.calendly.com/api-docs/reference)
- [Webhook指南](https://developer.calendly.com/api-docs/docs/webhooks-overview)
