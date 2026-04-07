# Intercom API

> 客户沟通平台,网站实时聊天+邮件+产品内消息(Inbound转化利器)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key (Access Token)
- **环境变量:**
  - `INTERCOM_ACCESS_TOKEN` — Intercom访问令牌
- **获取步骤:**
  1. 登录 Intercom
  2. Settings → Developers → Developer Hub
  3. Your Apps → New App 或选择已有App
  4. Authentication → Create Access Token
  5. 复制保存 Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
    -H "Accept: application/json" \
    https://api.intercom.io/admins
  ```

## 核心能力

### 网站实时聊天(Messenger)
- 访客实时聊天
- 离线消息留言
- 富文本和附件支持
- 预设回复(Saved Replies)
- 聊天路由(按技能/语言分配)

### 访客识别和公司信息
- 自动识别访客公司(基于域名)
- 显示公司信息(规模/行业/ARR)
- 访客行为追踪(访问页面/时长)
- Lead评分和标签
- 高价值访客自动提醒

### 自动化消息(Automation)
- 按用户行为触发消息(如"访问定价页3次→弹窗")
- 时间触发(如"注册7天未激活→发邮件")
- 条件逻辑(if-then-else)
- A/B测试
- 多渠道(Email/In-app/Push)

### 产品内消息(Product Tours)
- 新用户引导(Onboarding)
- 功能提示(Tooltips)
- 新功能公告(Banners)
- 产品更新推送
- 交互式教程

### 帮助中心/Knowledge Base
- 自助服务文章
- 搜索和分类
- 多语言支持
- 文章性能分析
- 嵌入到产品内

### 工单管理(Inbox)
- 统一收件箱(Chat/Email/社交媒体)
- 工单分配和SLA
- 团队协作(内部备注)
- 工单标签和优先级
- 自动化规则

### 聊天机器人(Operator)
- 常见问题自动回复
- 资格验证(BANT问题)
- 会议预约
- 路由到人工
- 自定义对话流

## 使用示例

### 获取所有联系人
```bash
curl -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Accept: application/json" \
  "https://api.intercom.io/contacts?per_page=50" | jq
```

### 创建或更新联系人
```bash
curl -X POST https://api.intercom.io/contacts \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "prospect@example.com",
    "name": "张三",
    "custom_attributes": {
      "company": "科技公司",
      "deal_amount": 50000,
      "lead_source": "Website"
    }
  }' | jq
```

### 发送消息给用户
```bash
curl -X POST https://api.intercom.io/messages \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message_type": "inapp",
    "from": {
      "type": "admin",
      "id": "1234567"
    },
    "to": {
      "type": "user",
      "email": "prospect@example.com"
    },
    "body": "您好!看到您访问了我们的定价页面,有什么可以帮您的吗?"
  }' | jq
```

### 查询对话记录
```bash
curl -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Accept: application/json" \
  "https://api.intercom.io/conversations?per_page=20" | jq
```

### 回复对话
```bash
CONVERSATION_ID="12345"

curl -X POST https://api.intercom.io/conversations/$CONVERSATION_ID/reply \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message_type": "comment",
    "type": "admin",
    "admin_id": "1234567",
    "body": "感谢您的咨询!我们的Demo可以在这里预约: https://calendly.com/..."
  }' | jq
```

### 创建标签并打标签
```bash
# 创建标签
curl -X POST https://api.intercom.io/tags \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "高意向-Demo请求"
  }' | jq

# 给联系人打标签
TAG_ID="tag123"
CONTACT_ID="contact456"

curl -X POST https://api.intercom.io/contacts/$CONTACT_ID/tags \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "id": "'$TAG_ID'"
  }' | jq
```

### 触发自动化消息(手动触发)
```bash
curl -X POST https://api.intercom.io/messages \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message_type": "email",
    "from": {
      "type": "admin",
      "id": "1234567"
    },
    "to": {
      "type": "user",
      "id": "user789"
    },
    "subject": "您的Demo会议确认",
    "body": "<p>您好,我们的Demo已安排在明天下午2点...</p>",
    "template": "plain"
  }' | jq
```

### 查询公司信息
```bash
curl -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Accept: application/json" \
  "https://api.intercom.io/companies?page=1&per_page=20" | jq
```

### Webhook订阅(对话创建/回复)
```bash
curl -X POST https://api.intercom.io/subscriptions \
  -H "Authorization: Bearer $INTERCOM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/intercom-webhook",
    "topics": ["conversation.user.created", "conversation.user.replied"],
    "metadata": {
      "webhook_name": "CRM同步"
    }
  }' | jq
```

## 适用场景
- 官网访客实时聊天转化(立即响应询问)
- 高价值访客自动弹窗("看到您是VP,要Demo吗?")
- 产品内引导和支持(SaaS产品Onboarding)
- Sales/Support统一收件箱(避免客户多次咨询)
- 自动化培育(注册未激活→发邮件引导)
- 客户成功团队主动触达(如"使用遇到问题了吗?")

## 限制和注意事项
- 按席位收费,团队大了成本高
- 主要面向SaaS产品(需要嵌入产品内)
- API限流: 500次/分钟(企业版更高)
- 免费版功能受限(无自动化/无产品内消息)
- Webhook需要HTTPS端点
- 聊天机器人需要额外配置和训练

## 定价
- Essential: $39/月(2席位) - 基础聊天和邮件
- Advanced: $99/月(5席位) - 自动化,产品内消息
- Expert: $139/月(5席位) - 高级自动化,A/B测试,多团队
- 超出席位额外收费

## 参考链接
- [官方文档](https://developers.intercom.com/)
- [API参考](https://developers.intercom.com/intercom-api-reference)
- [Webhook指南](https://developers.intercom.com/building-apps/docs/webhook-model)
- [集成示例](https://developers.intercom.com/building-apps/docs)
