# Close CRM API

> 为Inside Sales优化的CRM,内置拨号器+邮件,适合高频触达团队(非企业级)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `CLOSE_API_KEY` — Close API密钥
- **获取步骤:**
  1. 登录 Close
  2. Settings → API Keys
  3. Create API Key → 复制保存
- **验证:**
  ```bash
  curl -u "$CLOSE_API_KEY:" https://api.close.com/api/v1/me/
  ```

## 核心能力

### 联系人和商机管理
- Lead和Contact管理
- Opportunity(商机)CRUD
- 自定义字段
- 数据导入导出
- 去重和合并

### 内置拨号器(Power Dialer)
- 点击拨打(Click-to-Call)
- 自动录音
- 通话转录(需额外付费)
- 拨号队列(批量拨打)
- 拨打统计(接通率/通话时长)

### 内置邮件(Email)
- 从Close直接发邮件(无需切换Gmail)
- Gmail/Outlook集成(双向同步)
- 邮件模板
- 邮件序列(自动跟进)
- 打开/点击追踪

### 活动和任务
- 任务管理和提醒
- 通话/邮件/会议记录
- 活动时间线
- 自动活动记录

### Pipeline可视化
- Deal看板(拖拽移动Stage)
- 自定义Pipeline
- 概率和金额预测
- 漏斗分析

### 报表和分析
- 销售活动报表(邮件/电话数量)
- 转化率分析
- 团队表现对比
- 自定义报表

### 集成
- Zapier集成(2000+应用)
- Slack通知
- Calendly会议预约
- Mailchimp营销

## 使用示例

### 获取所有Lead
```bash
curl -u "$CLOSE_API_KEY:" \
  "https://api.close.com/api/v1/lead/?_limit=50" | jq
```

### 创建新Lead
```bash
curl -X POST https://api.close.com/api/v1/lead/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "科技公司",
    "contacts": [
      {
        "name": "张三",
        "title": "CTO",
        "emails": [{"email": "zhang@techcorp.com", "type": "office"}],
        "phones": [{"phone": "+86-138-0000-0000", "type": "mobile"}]
      }
    ],
    "custom": {
      "lead_source": "Website",
      "company_size": "50-100"
    }
  }' | jq
```

### 创建商机(Opportunity)
```bash
LEAD_ID="lead_abc123"

curl -X POST https://api.close.com/api/v1/opportunity/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "lead_id": "'$LEAD_ID'",
    "status_label": "Demo已完成",
    "value": 50000,
    "value_period": "one_time",
    "note": "企业版产品,预计Q2签约"
  }' | jq
```

### 发送邮件
```bash
LEAD_ID="lead_abc123"
CONTACT_ID="contact_xyz789"

curl -X POST https://api.close.com/api/v1/activity/email/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "lead_id": "'$LEAD_ID'",
    "to": ["zhang@techcorp.com"],
    "subject": "关于明天Demo的确认",
    "body_html": "<p>您好张总,</p><p>我们的Demo会议已安排在明天下午2点...</p>",
    "status": "outbox"
  }' | jq
```

### 记录通话
```bash
curl -X POST https://api.close.com/api/v1/activity/call/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "lead_id": "'$LEAD_ID'",
    "direction": "outbound",
    "status": "completed",
    "duration": 1800,
    "note": "讨论了产品需求和实施时间表,下周发正式报价"
  }' | jq
```

### 创建任务
```bash
curl -X POST https://api.close.com/api/v1/task/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "lead_id": "'$LEAD_ID'",
    "assigned_to": "user_123",
    "text": "发送报价单给张总",
    "due_date": "2026-04-10"
  }' | jq
```

### 搜索Lead
```bash
# 搜索包含"科技"的公司
QUERY="name:科技"

curl -u "$CLOSE_API_KEY:" \
  "https://api.close.com/api/v1/lead/?query=$(echo $QUERY | jq -sRr @uri)" | jq
```

### 批量更新商机Stage
```bash
OPP_ID="oppo_abc123"

curl -X PUT https://api.close.com/api/v1/opportunity/$OPP_ID/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "status_label": "谈判中"
  }' | jq
```

### Webhook订阅
```bash
curl -X POST https://api.close.com/api/v1/webhook/ \
  -u "$CLOSE_API_KEY:" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/close-webhook",
    "events": ["lead.created", "opportunity.status_changed", "call.completed"]
  }' | jq
```

## 适用场景
- 小型Inside Sales团队(<50人)
- 不想Salesforce的复杂度和成本
- 高频电话+邮件触达场景(SDR团队)
- 创业公司快速上手CRM
- 重视通话功能(内置拨号器比单独买Aircall方便)

## 限制和注意事项
- 企业级功能弱(无CPQ/高级自动化/Territory管理)
- 集成生态不如Salesforce/HubSpot
- API限流: 600次/分钟
- 报表自定义能力有限
- 通话录音转录需额外付费
- 主要面向中小团队(<100人)

## 定价
- Startup: $49/用户/月 - 基础CRM+邮件
- Professional: $99/用户/月 - 内置拨号器+自动化
- Business: $149/用户/月 - 高级报表+Workflow

## 参考链接
- [官方文档](https://developer.close.com/)
- [API参考](https://developer.close.com/resources/)
- [集成指南](https://help.close.com/en/collections/24507-integrations)
