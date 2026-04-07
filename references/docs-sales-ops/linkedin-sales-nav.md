# LinkedIn Sales Navigator API

> LinkedIn 销售导航器,精准找决策人和目标客户

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API(需企业版订阅 + 开发者申请)。

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `LINKEDIN_CLIENT_ID` — LinkedIn应用Client ID
  - `LINKEDIN_CLIENT_SECRET` — LinkedIn应用Client Secret
  - `LINKEDIN_ACCESS_TOKEN` — OAuth访问令牌
- **获取步骤:**
  1. 访问 https://developer.linkedin.com
  2. 创建 Application
  3. 申请 Sales Navigator API 权限(需企业版订阅)
  4. 配置 OAuth 2.0 Redirect URLs
  5. 通过 OAuth 2.0 授权流程获取 Access Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $LINKEDIN_ACCESS_TOKEN" \
    https://api.linkedin.com/v2/me
  ```

## 核心能力
- 高级人脉搜索(按职位/公司/行业/地区/资历)
- 目标客户列表管理
- InMail消息发送(付费消息额度)
- 查看目标客户的动态和互动
- CRM同步(Salesforce/HubSpot/Dynamics)
- Lead Builder(自动化潜在客户推荐)
- Account IQ(公司深度洞察)
- TeamLink(利用团队人脉网络)

## 使用示例

### 获取当前用户信息
```bash
curl -H "Authorization: Bearer $LINKEDIN_ACCESS_TOKEN" \
  https://api.linkedin.com/v2/me
```

### 搜索人脉(需Sales Navigator权限)
```bash
# 注意: 实际API端点需要Sales Navigator企业版权限
curl -H "Authorization: Bearer $LINKEDIN_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://api.linkedin.com/v2/salesNavigatorSearch/people?q=search&keywords=VP%20Sales&geoUrn=urn:li:geo:103644278"
```

### 发送InMail
```bash
curl -X POST \
  -H "Authorization: Bearer $LINKEDIN_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.linkedin.com/v2/messages \
  -d '{
    "recipients": ["urn:li:person:ABC123"],
    "subject": "合作机会探讨",
    "body": "您好,我注意到您在SaaS领域的丰富经验..."
  }'
```

### 获取联系人详细信息
```bash
curl -H "Authorization: Bearer $LINKEDIN_ACCESS_TOKEN" \
  "https://api.linkedin.com/v2/people/(id:{person_id})?projection=(id,firstName,lastName,headline,profilePicture)"
```

### 同步到CRM(Webhook方式)
```bash
# 配置Webhook接收LinkedIn事件
# Sales Navigator原生支持与Salesforce/HubSpot同步
# 通过界面配置: Settings → CRM Sync → Connect
```

## 适用场景
- 找高管/决策人的联系方式和背景
- 判断目标客户的兴趣和近期动态
- 配合Apollo做多渠道线索挖掘
- ABM账户深度研究
- 利用团队人脉网络Warm Intro
- 追踪目标客户的职位变动

## 限制和注意事项
- API访问门槛高(需Sales Navigator企业版 + 开发者审批)
- 限流较严(具体限制未公开)
- InMail配额有限(根据订阅级别)
- 大部分操作建议浏览器自动化(API覆盖不全)
- 数据导出受LinkedIn用户协议限制
- 不允许批量抓取数据(违反ToS)
- OAuth Token过期时间较短(60天)

## 定价
- Sales Navigator Core: $99/月起
- Sales Navigator Advanced: $149/月起
- Sales Navigator Advanced Plus: 需联系销售
- API访问需企业级订阅(通常数千美元/年)

## 参考链接
- [官方文档](https://learn.microsoft.com/en-us/linkedin/sales/)
- [LinkedIn API文档](https://developer.linkedin.com/docs)
- [Sales Navigator Help](https://www.linkedin.com/help/sales-navigator)
- [OAuth 2.0认证](https://learn.microsoft.com/en-us/linkedin/shared/authentication/authentication)
