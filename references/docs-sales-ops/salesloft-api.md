<!-- doc-level: standard -->

# Salesloft API

销售触达自动化平台,通过Cadence(节奏)管理多步骤触达序列,适合企业销售团队规模化外呼。

## 快速开始

### 认证配置
- **方式:** oauth / api_key
- **环境变量:** `SALESLOFT_API_KEY`
- **获取步骤:**
  1. 登录 https://app.salesloft.com → Settings → API
  2. Generate API Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SALESLOFT_API_KEY" \
    https://api.salesloft.com/v2/me.json
  ```

## 核心能力
- Cadence(节奏)管理和多步骤触达
- 邮件发送追踪和通话拨号器
- CRM同步(Salesforce/Dynamics)
- People和Accounts管理
- 分析和团队表现报表

## 使用示例

### 创建联系人
```bash
curl -X POST \
  -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.salesloft.com/v2/people.json \
  -d '{
    "email_address": "john@example.com",
    "first_name": "John",
    "last_name": "Smith"
  }'
```

### 将联系人加入Cadence
```bash
curl -X POST \
  -H "Authorization: Bearer $SALESLOFT_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.salesloft.com/v2/cadence_memberships.json \
  -d '{"person_id": 123, "cadence_id": 456}'
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
    "duration": 600
  }'
```

## 适用场景
- 多步骤触达自动化(邮件+电话+LinkedIn)
- 销售团队活动追踪和CRM同步
- 企业已使用Salesloft平台

## 限制和注意事项
- API限流: 600次/分钟
- 需付费订阅(无免费版)
- 不包含联系人数据库

## 定价
- 通常$75-125/用户/月(需联系销售)
- 企业版定制报价

## 参考链接
- [API文档](https://developers.salesloft.com/docs/api/)
