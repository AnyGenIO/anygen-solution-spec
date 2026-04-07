# Drift API

> 对话式营销平台,实时聊天+ABM+会议预约(Intercom竞品,偏Sales)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `DRIFT_ACCESS_TOKEN` — Drift OAuth访问令牌
- **获取步骤:**
  1. 登录 Drift
  2. Settings → App Settings → APIs & Webhooks
  3. Create New App → OAuth
  4. 配置Redirect URL和Scopes
  5. 获取Client ID和Client Secret
  6. OAuth授权流程获取Access Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
    https://driftapi.com/users
  ```

## 核心能力

### 实时网站聊天(Drift Messenger)
- 访客实时聊天
- 富文本和GIF支持
- 屏幕共享
- 视频消息(录制发送)
- 移动端SDK

### 聊天机器人(Chatbots)
- 资格验证(BANT问题自动问)
- Lead路由(企业→AE, SMB→SDR)
- 常见问题自动回复
- 多语言支持
- 自定义对话流(可视化编辑器)

### 目标账户识别(ABM)
- 识别访客公司(基于IP/域名)
- VIP账户自动弹窗("您好,欢迎来自XX公司的访客!")
- 目标账户列表管理
- 优先级路由(VIP立即转人工)
- 访问提醒(Slack实时通知)

### 即时会议预约
- 聊天中直接订日历(无需跳转)
- 与Google/Outlook日历集成
- 智能路由(按销售可用性分配)
- 会议提醒和确认
- No-show追踪

### 视频消息(Video)
- 录制个性化视频
- 发给特定客户
- 视频查看追踪
- 嵌入到邮件和聊天

### Playbooks(自动化规则)
- 按访客行为触发(如"访问定价页→弹窗")
- 按公司属性触发(如"员工>500→路由到企业团队")
- 按时间触发(如"工作日9-5点→真人,其他时间→Bot")
- 条件逻辑和A/B测试

### 与Salesforce/Marketo深度集成
- 实时同步对话到CRM
- 自动创建Lead/Contact
- 更新商机Stage
- 营销自动化触发

## 使用示例

### 获取所有联系人
```bash
curl -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  https://driftapi.com/contacts | jq
```

### 创建或更新联系人
```bash
curl -X POST https://driftapi.com/contacts \
  -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "attributes": {
      "email": "vp@bigcorp.com",
      "name": "李总",
      "company": "大型企业",
      "title": "VP of Sales",
      "custom_field_1": "高优先级"
    }
  }' | jq
```

### 发送消息给用户
```bash
curl -X POST https://driftapi.com/conversations/new \
  -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contact_id": 12345,
    "message": "您好李总!看到您访问了我们的企业版页面,我可以为您安排一个定制化Demo。",
    "type": "private_note"
  }' | jq
```

### 查询对话记录
```bash
curl -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  "https://driftapi.com/conversations?limit=50" | jq
```

### 获取对话详情
```bash
CONVERSATION_ID=67890

curl -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  "https://driftapi.com/conversations/$CONVERSATION_ID" | jq
```

### 创建Playbook(自动化规则)
```bash
curl -X POST https://driftapi.com/playbooks \
  -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "VIP账户自动问候",
    "trigger": {
      "type": "page_visit",
      "url_pattern": "/pricing"
    },
    "conditions": {
      "company_employee_count": {"min": 500}
    },
    "actions": [
      {
        "type": "show_message",
        "message": "您好!我看到您来自{{company_name}},我是专门负责企业客户的顾问,有什么可以帮您的吗?",
        "assign_to": "enterprise-team"
      }
    ]
  }' | jq
```

### 查询会议预约
```bash
curl -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  "https://driftapi.com/meetings?start_date=2026-04-01&end_date=2026-04-30" | jq
```

### 批量标记联系人
```bash
curl -X POST https://driftapi.com/contacts/tag \
  -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contact_ids": [12345, 67890],
    "tags": ["高意向", "Demo已预约"]
  }' | jq
```

### Webhook订阅
```bash
curl -X POST https://driftapi.com/webhooks \
  -H "Authorization: Bearer $DRIFT_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/drift-webhook",
    "events": [
      "conversation.new",
      "conversation.assignee_changed",
      "meeting.booked"
    ]
  }' | jq
```

## 适用场景
- ABM策略 - 目标账户访问网站立即接待
- 聊天转Demo预约无缝衔接(提高转化率)
- 高价值线索需要立即响应(VIP自动弹窗)
- 资格验证自动化(Bot问BANT,合格才转人工)
- 销售团队需要实时提醒(Slack通知目标账户访问)

## 限制和注意事项
- 定价较高(面向企业,起步$2,500/月)
- 配置复杂(Playbooks需要时间调优)
- API限流: 120次/分钟
- 主要面向B2B SaaS(B2C不适用)
- 需要一定网站流量才能发挥价值
- 与Intercom功能重叠,需二选一

## 定价
- Premium: $2,500/月起(需联系销售)
- Advanced: 需联系销售(高级自动化+ABM)
- Enterprise: 需联系销售(多团队+SSO+专属支持)
- 按席位和流量组合定价

## 参考链接
- [官方文档](https://devdocs.drift.com/)
- [API参考](https://devdocs.drift.com/docs/api-overview)
- [Playbook指南](https://gethelp.drift.com/hc/en-us/articles/360019668573-Playbooks-Overview)
- [集成示例](https://devdocs.drift.com/docs/integrations)
