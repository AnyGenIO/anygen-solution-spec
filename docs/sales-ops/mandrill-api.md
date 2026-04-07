# Mailchimp Transactional (Mandrill)

> Mailchimp旗下事务性邮件API,适合个性化1对1销售邮件

## 快速开始

### 安装
```bash
# Python
pip install mailchimp-transactional

# Node.js
npm install @mailchimp/mailchimp_transactional

# PHP
composer require mailchimp/transactional
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `MANDRILL_API_KEY` — Mandrill API密钥
- **获取步骤:**
  1. 登录 Mailchimp
  2. 导航到 Transactional(原Mandrill)
  3. Settings → API Keys
  4. Create API Key → 复制保存
  5. 注意: 需要有付费Mailchimp账户
- **验证:**
  ```bash
  curl -X POST https://mandrillapp.com/api/1.0/users/ping \
    -d '{"key":"'$MANDRILL_API_KEY'"}'
  ```

## 核心能力

### 个性化邮件发送
- 1对1销售邮件(高度个性化)
- Handlebars模板语法(动态内容)
- 合并标签(Merge Tags)
- 条件内容块
- 内联CSS自动处理

### 邮件追踪
- 打开率追踪
- 点击率追踪
- 退信分析和分类
- 实时发送状态
- 历史邮件搜索

### 模板管理
- 可视化模板编辑器
- Handlebars动态内容
- 模板版本管理
- A/B测试变体
- 代码片段复用

### 与Mailchimp联动
- 共享联系人数据
- 营销活动和事务性邮件统一管理
- 订阅状态同步
- 标签和分段共享

### 发信优化
- 自动重试失败邮件
- 时区智能发送
- 送达率优化
- 垃圾邮件测试

## 使用示例

### 发送简单邮件(curl)
```bash
curl -X POST https://mandrillapp.com/api/1.0/messages/send \
  -H "Content-Type: application/json" \
  -d '{
    "key": "'$MANDRILL_API_KEY'",
    "message": {
      "from_email": "sales@yourcompany.com",
      "from_name": "销售顾问-张伟",
      "to": [
        {
          "email": "prospect@example.com",
          "name": "李总",
          "type": "to"
        }
      ],
      "subject": "关于我们今天通话的跟进",
      "html": "<p>李总您好,</p><p>非常感谢您今天抽出时间与我沟通...</p>",
      "track_opens": true,
      "track_clicks": true,
      "auto_text": true
    }
  }'
```

### 使用Python SDK发送模板邮件
```python
import mailchimp_transactional as MailchimpTransactional
from mailchimp_transactional.api_client import ApiClientError

mailchimp = MailchimpTransactional.Client(os.environ['MANDRILL_API_KEY'])

message = {
    "from_email": "sales@yourcompany.com",
    "from_name": "AE-王明",
    "to": [{"email": "customer@example.com", "name": "张三"}],
    "subject": "您的专属报价单",
    "merge_language": "handlebars",
    "global_merge_vars": [
        {"name": "CUSTOMER_NAME", "content": "张三"},
        {"name": "DEAL_AMOUNT", "content": "50,000"},
        {"name": "DISCOUNT", "content": "15%"}
    ]
}

try:
    response = mailchimp.messages.send_template({
        "template_name": "sales-proposal",
        "template_content": [],
        "message": message
    })
    print(f"邮件已发送: {response}")
except ApiClientError as error:
    print(f"发送失败: {error.text}")
```

### 发送带附件的邮件
```bash
curl -X POST https://mandrillapp.com/api/1.0/messages/send \
  -H "Content-Type: application/json" \
  -d '{
    "key": "'$MANDRILL_API_KEY'",
    "message": {
      "from_email": "sales@yourcompany.com",
      "to": [{"email": "customer@example.com"}],
      "subject": "报价单和产品手册",
      "html": "<p>您好,附件是我们的报价单...</p>",
      "attachments": [
        {
          "type": "application/pdf",
          "name": "报价单-2026Q2.pdf",
          "content": "base64编码的PDF内容..."
        }
      ]
    }
  }'
```

### 使用动态模板(Handlebars)
```handlebars
<!-- 模板内容示例 -->
<p>{{customer_name}}您好,</p>

{{#if is_vip}}
  <p>作为我们的VIP客户,您可以享受<strong>{{vip_discount}}</strong>的专属折扣。</p>
{{else}}
  <p>我们为您准备了<strong>{{standard_discount}}</strong>的优惠。</p>
{{/if}}

<p>商机金额: ¥{{deal_amount}}</p>
<p>预计签约日期: {{close_date}}</p>
```

### 查询邮件发送状态
```bash
curl -X POST https://mandrillapp.com/api/1.0/messages/info \
  -d '{
    "key": "'$MANDRILL_API_KEY'",
    "id": "abc123def456"
  }'
```

### 搜索邮件历史
```bash
curl -X POST https://mandrillapp.com/api/1.0/messages/search \
  -d '{
    "key": "'$MANDRILL_API_KEY'",
    "query": "to:customer@example.com",
    "date_from": "2026-04-01",
    "date_to": "2026-04-07",
    "limit": 50
  }'
```

## 适用场景
- 销售序列化邮件(个性化程度高,非群发)
- AE发送定制化提案和报价
- Demo后的跟进邮件(引用通话内容)
- 已用Mailchimp做营销,需要事务性邮件补充
- 1对1客户关系维护邮件

## 限制和注意事项
- 必须有Mailchimp付费账户才能使用
- API限流: 每小时根据套餐不同(一般每小时数千封)
- 不适合大规模群发(建议用Mailchimp营销功能或SendGrid)
- 模板编辑需要在Mailchimp Web界面完成
- 中国大陆访问可能较慢

## 定价
- 需要Mailchimp付费账户($20/月起)
- Transactional邮件块定价:
  - 25,000封: $20/月 ($0.80/千封)
  - 50,000封: $35/月 ($0.70/千封)
  - 100,000封: $65/月 ($0.65/千封)
  - 超出部分按量计费

## 参考链接
- [官方文档](https://mailchimp.com/developer/transactional/)
- [API参考](https://mailchimp.com/developer/transactional/api/)
- [模板语法](https://mailchimp.com/developer/transactional/docs/templates-dynamic-content/)
- [Python SDK](https://github.com/mailchimp/mailchimp-transactional-python)
