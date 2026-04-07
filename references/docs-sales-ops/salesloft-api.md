# Salesloft API

> Outreach的竞品,功能类似,序列化销售触达

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** oauth / api_key
- **环境变量:**
  - `SALESLOFT_API_KEY` — API密钥
- **获取步骤:**
  1. 登录 https://app.salesloft.com
  2. Settings → API → Generate API Key
  3. 复制生成的API Key
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
    https://api.salesloft.com/v2/me.json
  ```

## 核心能力
- 节奏(Cadence)管理——多步骤触达序列
- 邮件发送和追踪(打开/点击/回复)
- 通话拨号器(Dialer)
- CRM同步(Salesforce/Dynamics)
- 分析和报表(漏斗/活动/团队表现)
- People(联系人)和Accounts(公司)管理
- 任务自动化

## 使用示例

### 获取当前用户信息
```bash
curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  https://api.salesloft.com/v2/me.json
```

### 创建Person(联系人)
```bash
curl -X POST \
  -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.salesloft.com/v2/people.json \
  -d '{
    "email_address": "john@example.com",
    "first_name": "John",
    "last_name": "Smith",
    "title": "VP Sales",
    "company_name": "Tech Corp"
  }'
```

### 获取所有Cadences(节奏)
```bash
curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  https://api.salesloft.com/v2/cadences.json
```

### 将联系人加入Cadence
```bash
curl -X POST \
  -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.salesloft.com/v2/cadence_memberships.json \
  -d '{
    "person_id": 123,
    "cadence_id": 456
  }'
```

### 查询邮件活动
```bash
curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  "https://api.salesloft.com/v2/activities.json?type=email&per_page=50"
```

### 记录通话
```bash
curl -X POST \
  -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.salesloft.com/v2/calls.json \
  -d '{
    "person_id": 123,
    "sentiment": "positive",
    "disposition": "interested",
    "duration": 600,
    "notes": "客户对产品很感兴趣,下周发送Proposal"
  }'
```

### 获取Cadence统计
```bash
curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  https://api.salesloft.com/v2/cadences/456.json
```

## 适用场景
- 与Outreach二选一,功能类似
- 企业已用Salesloft时
- 多步骤触达自动化(邮件+电话+LinkedIn)
- 销售团队活动追踪
- CRM数据双向同步
- 团队表现分析

## 限制和注意事项
- API限流: 600次/分钟
- 需付费订阅(无免费版)
- 邮件发送受邮箱提供商限制
- Cadence复杂度根据套餐不同
- 不包含联系人数据(需配合Apollo等)
- 录音存储有限制

## 定价
- 需联系销售(通常$75-125/用户/月)
- 企业版定制报价
- 按年付费通常有折扣

## 参考链接
- [官方文档](https://developers.salesloft.com/docs/api/)
- [API参考](https://developers.salesloft.com/api.html)
- [认证指南](https://developers.salesloft.com/docs/api/getting-started/)
- [Webhooks](https://developers.salesloft.com/docs/api/webhooks/)
