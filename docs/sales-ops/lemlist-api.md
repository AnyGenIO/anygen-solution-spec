# Lemlist API

> 冷邮件自动化平台,专注个性化序列触达和高送达率(比Outreach更轻量,更适合Outbound)

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `LEMLIST_API_KEY` — Lemlist API密钥
- **获取步骤:**
  1. 登录 https://app.lemlist.com
  2. Settings → Integrations → API → Generate
  3. 复制生成的API Key
- **验证:**
  ```bash
  curl -H "Content-Type: application/json" \
    -H "Authorization: Bearer $LEMLIST_API_KEY" \
    https://api.lemlist.com/api/team
  ```

## 核心能力

### 邮件序列
- 多步骤邮件序列(Campaign)
- 个性化变量(姓名/公司/自定义字段)
- 动态图片(嵌入联系人姓名的截图)
- A/B测试(主题行/正文)
- 发送时间优化(按时区/收件人习惯)

### 送达率优化
- 邮箱预热(Warm-up,逐步提升发送量)
- SPF/DKIM/DMARC检查
- 垃圾邮件检测(发送前测试)
- 多邮箱轮换(避免单个邮箱超限)
- 自动退订处理(CAN-SPAM合规)

### 多渠道触达(高级版)
- LinkedIn自动化(访问Profile/发InMail,需Chrome插件)
- 电话任务提醒

### 数据和集成
- Lead列表管理
- CRM集成(HubSpot/Salesforce/Pipedrive)
- Webhook事件(邮件打开/点击/回复)
- Zapier集成

### 分析
- 打开率/点击率/回复率
- 每步效果分析
- 不合格Lead自动移除

## 使用示例

### 获取团队信息
```bash
curl -H "Authorization: Bearer $LEMLIST_API_KEY" \
  https://api.lemlist.com/api/team
```

### 创建Campaign(序列)
```bash
curl -X POST \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lemlist.com/api/campaigns \
  -d '{
    "name": "SaaS Outbound Q2",
    "settings": {
      "warmup": true,
      "timezone": "Asia/Shanghai"
    }
  }'
```

### 添加Lead到Campaign
```bash
curl -X POST \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lemlist.com/api/campaigns/your-campaign-id/leads/john@example.com \
  -d '{
    "firstName": "John",
    "lastName": "Smith",
    "companyName": "Tech Corp",
    "customFields": {
      "painPoint": "低转化率",
      "industry": "SaaS"
    }
  }'
```

### 批量导入Leads
```bash
curl -X POST \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lemlist.com/api/campaigns/your-campaign-id/leads \
  -d '[
    {
      "email": "contact1@example.com",
      "firstName": "Alice",
      "companyName": "Company A"
    },
    {
      "email": "contact2@example.com",
      "firstName": "Bob",
      "companyName": "Company B"
    }
  ]'
```

### 获取Campaign统计
```bash
curl -H "Authorization: Bearer $LEMLIST_API_KEY" \
  https://api.lemlist.com/api/campaigns/your-campaign-id/stats
```

### 取消订阅Lead
```bash
curl -X DELETE \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  https://api.lemlist.com/api/campaigns/your-campaign-id/leads/john@example.com
```

### 获取邮箱预热状态
```bash
curl -H "Authorization: Bearer $LEMLIST_API_KEY" \
  https://api.lemlist.com/api/warmup
```

## 适用场景
- SDR团队做大规模Outbound冷邮件
- 重视送达率和邮箱健康度(不想进垃圾箱)
- 需要个性化但预算不够Outreach/Salesloft
- 配合LinkedIn做多渠道触达
- 创业公司快速启动外呼
- 邮件预热和域名信誉建设

## 限制和注意事项
- API限流: 120次/分钟
- LinkedIn自动化有被封号风险(违反LinkedIn ToS)
- 免费版不存在,必须付费
- 主要适合Outbound场景,Inbound跟进用HubSpot/Outreach更好
- 需要自己准备联系人数据(不含数据库)
- 动态图片功能可能被某些邮箱屏蔽

## 定价
- Email Outreach: $59/用户/月
- Sales Engagement: $99/用户/月
- Multichannel Expert: $159/用户/月
- 年付通常有20%折扣

## 参考链接
- [官方文档](https://developer.lemlist.com/)
- [API参考](https://developer.lemlist.com/#introduction)
- [集成指南](https://help.lemlist.com/en/collections/1959426-integrations)
- [最佳实践](https://www.lemlist.com/blog)
