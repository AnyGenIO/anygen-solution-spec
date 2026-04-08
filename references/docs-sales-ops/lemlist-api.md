<!-- doc-level: standard -->

# Lemlist API

冷邮件自动化平台,专注个性化序列触达和送达率优化,适合SDR团队大规模Outbound。

## 快速开始

### 认证配置
- **方式:** api_key
- **环境变量:** `LEMLIST_API_KEY`
- **获取步骤:**
  1. 登录 https://app.lemlist.com → Settings → Integrations → API
  2. Generate → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $LEMLIST_API_KEY" \
    https://api.lemlist.com/api/team
  ```

## 核心能力
- 多步骤邮件序列和个性化变量
- 邮箱预热和送达率优化(SPF/DKIM/DMARC)
- A/B测试和发送时间优化
- LinkedIn自动化(高级版)
- CRM集成和Webhook事件

## 使用示例

### 创建Campaign
```bash
curl -X POST \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lemlist.com/api/campaigns \
  -d '{
    "name": "SaaS Outbound Q2",
    "settings": {"warmup": true}
  }'
```

### 添加Lead到Campaign
```bash
curl -X POST \
  -H "Authorization: Bearer $LEMLIST_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lemlist.com/api/campaigns/campaign-id/leads/john@example.com \
  -d '{
    "firstName": "John",
    "companyName": "Tech Corp"
  }'
```

### 获取Campaign统计
```bash
curl -H "Authorization: Bearer $LEMLIST_API_KEY" \
  https://api.lemlist.com/api/campaigns/campaign-id/stats
```

## 适用场景
- SDR大规模Outbound冷邮件
- 重视送达率和邮箱健康度
- 配合LinkedIn多渠道触达
- 预算有限但需个性化功能

## 限制和注意事项
- API限流: 120次/分钟
- LinkedIn自动化有被封号风险
- 无免费版,必须付费
- 需自备联系人数据

## 定价
- Email Outreach: $59/用户/月
- Sales Engagement: $99/用户/月
- Multichannel Expert: $159/用户/月

## 参考链接
- [API文档](https://developer.lemlist.com/)
