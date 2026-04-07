# SendGrid API

> Twilio旗下邮件发送平台,批量邮件基础设施(非营销自动化)

## 快速开始

### 安装
```bash
# Python
pip install sendgrid

# Node.js
npm install @sendgrid/mail

# Ruby
gem install sendgrid-ruby
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `SENDGRID_API_KEY` — SendGrid API密钥
- **获取步骤:**
  1. 登录 SendGrid
  2. Settings → API Keys
  3. Create API Key → 选择权限(Full Access或Restricted)
  4. 复制保存(仅显示一次)
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SENDGRID_API_KEY" \
    https://api.sendgrid.com/v3/user/profile
  ```

## 核心能力

### 邮件发送
- 单封邮件发送(事务性邮件)
- 批量邮件发送(最多1000个收件人/请求)
- 模板化邮件(动态内容替换)
- 附件支持(Base64编码)
- HTML和纯文本邮件
- 自定义邮件头

### 邮件追踪
- 打开率追踪(Open Tracking)
- 点击率追踪(Click Tracking)
- 退订管理
- 垃圾邮件投诉追踪
- 邮件送达/失败事件

### 邮件验证
- Email Validation API(验证邮箱有效性)
- 语法检查
- DNS验证
- 一次性邮箱检测
- 风险评分

### 发信基础设施
- IP池管理(专用IP/共享IP)
- 域名认证(SPF/DKIM/DMARC)
- 发信声誉监控
- ISP反馈循环
- 邮件预热(Warmup)

### Webhook事件
- 实时事件推送(发送/送达/打开/点击/退订/投诉/失败)
- 批量事件批处理
- 事件重试机制

## 使用示例

### 发送简单邮件(curl)
```bash
curl -X POST https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [{
      "to": [{"email": "customer@example.com", "name": "张三"}],
      "subject": "欢迎加入我们的产品试用"
    }],
    "from": {"email": "sales@yourcompany.com", "name": "销售团队"},
    "content": [{
      "type": "text/html",
      "value": "<p>您好,张三,</p><p>感谢您注册试用我们的产品...</p>"
    }]
  }'
```

### 使用Python SDK发送邮件
```python
import os
from sendgrid import SendGridAPIClient
from sendgrid.helpers.mail import Mail

message = Mail(
    from_email='sales@yourcompany.com',
    to_emails='customer@example.com',
    subject='Demo会议确认',
    html_content='<p>您好,</p><p>我们的Demo会议已安排在明天下午2点...</p>'
)

try:
    sg = SendGridAPIClient(os.environ.get('SENDGRID_API_KEY'))
    response = sg.send(message)
    print(f"邮件已发送,状态码: {response.status_code}")
except Exception as e:
    print(f"发送失败: {e}")
```

### 使用模板发送个性化邮件
```bash
curl -X POST https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [{
      "to": [{"email": "customer@example.com"}],
      "dynamic_template_data": {
        "first_name": "张三",
        "company": "科技公司",
        "meeting_time": "明天下午2点"
      }
    }],
    "from": {"email": "sales@yourcompany.com"},
    "template_id": "d-1234567890abcdef"
  }'
```

### 批量发送(多个收件人不同内容)
```bash
curl -X POST https://api.sendgrid.com/v3/mail/send \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "personalizations": [
      {
        "to": [{"email": "customer1@example.com"}],
        "dynamic_template_data": {"name": "张三", "deal_amount": "50000"}
      },
      {
        "to": [{"email": "customer2@example.com"}],
        "dynamic_template_data": {"name": "李四", "deal_amount": "80000"}
      }
    ],
    "from": {"email": "sales@yourcompany.com"},
    "template_id": "d-proposal-template"
  }'
```

### 验证邮箱地址
```bash
curl -X POST https://api.sendgrid.com/v3/validations/email \
  -H "Authorization: Bearer $SENDGRID_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "prospect@example.com",
    "source": "signup"
  }'
```

### 查询邮件发送统计
```bash
curl -H "Authorization: Bearer $SENDGRID_API_KEY" \
  "https://api.sendgrid.com/v3/stats?start_date=2026-04-01&end_date=2026-04-07"
```

## 适用场景
- 销售工具发送事务性邮件(欢迎邮件/会议确认/报价发送)
- 批量外呼后的跟进邮件
- Demo预约确认和提醒
- 签约后的欢迎邮件
- 需要高送达率和邮件基础设施管理
- 配合CRM自动化(商机Stage变化→触发邮件)

## 限制和注意事项
- 免费版仅100封/天,只能试用
- API限流: 600次/秒
- 单次请求最多1000个收件人
- 营销邮件需额外购买Marketing Campaigns功能
- 附件总大小不超过30MB
- 需要域名认证(SPF/DKIM)才能达到高送达率
- 大量发送前需要IP预热,避免进垃圾箱

## 定价
- Free版: 100封/天(仅试用)
- Essentials: $15/月 - 25,000封 ($0.60/千封)
- Pro: $60/月 - 100,000封 ($0.60/千封)
- Premier: $90/月 - 1,500,000封 ($0.06/千封)
- 超出部分按量计费

## 参考链接
- [官方文档](https://www.twilio.com/docs/sendgrid)
- [API参考](https://www.twilio.com/docs/sendgrid/api-reference)
- [Python SDK](https://github.com/sendgrid/sendgrid-python)
- [邮件验证API](https://www.twilio.com/docs/sendgrid/api-reference/mail-send/mail-send)
