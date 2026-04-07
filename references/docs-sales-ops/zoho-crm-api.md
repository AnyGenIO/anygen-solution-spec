# Zoho CRM API

> 性价比CRM,支持Deluge脚本深度定制,功能全但价格比Salesforce便宜50%+

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `ZOHO_CLIENT_ID` — Client ID
  - `ZOHO_CLIENT_SECRET` — Client Secret
  - `ZOHO_REFRESH_TOKEN` — Refresh Token
- **获取步骤:**
  1. 访问 api-console.zoho.com 注册开发者
  2. 创建 Server-based Application
  3. 获取 Client ID 和 Client Secret
  4. 使用 OAuth授权流程获取 Refresh Token
  5. 用 Refresh Token 换取 Access Token
- **验证:**
  ```bash
  curl -X GET \
    "https://www.zohoapis.com/crm/v7/Leads?fields=Last_Name,Email" \
    -H "Authorization: Zoho-oauthtoken $ACCESS_TOKEN"
  ```

## 核心能力

### CRM核心
- Lead/Contact/Account/Deal/Task全对象CRUD
- 自定义模块和字段
- Blueprint(可视化销售流程,类似Salesforce Flow)
- 销售预测和配额管理
- Territory管理(区域分配)

### 自动化和AI
- Workflow自动化(邮件/任务/字段更新/Webhook)
- Deluge脚本(自定义函数和业务逻辑,类似Apex)
- Zia AI(Lead/Deal评分、异常检测、推荐最佳时机)
- Canvas(自定义UI界面)

### 多渠道触达
- 邮件集成(Gmail/Outlook/Zoho Mail)
- 电话集成(RingCentral/Twilio)
- 社交媒体集成(Facebook/Twitter/LinkedIn)
- 实时聊天和邮件营销(需Zoho One套件)

### 报表和BI
- 高级报表和Dashboard
- Anomaly Detection(异常预警)
- 目标vs实际对比

### 集成生态
- Zoho One生态(Office/Books/Desk等40+应用)
- Zapier/Integromat集成
- Marketplace(300+扩展)

## 使用示例

### 查询Lead
```bash
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Leads?fields=Last_Name,Email,Lead_Source&per_page=10" \
  -H "Authorization: Zoho-oauthtoken $ACCESS_TOKEN"
```

### 创建Contact
```bash
curl -X POST \
  "https://www.zohoapis.com/crm/v7/Contacts" \
  -H "Authorization: Zoho-oauthtoken $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "Last_Name": "张",
      "First_Name": "三",
      "Email": "zhang@example.com",
      "Phone": "+86-138-0000-0000",
      "Account_Name": "科技公司"
    }]
  }'
```

### 更新Deal
```bash
curl -X PUT \
  "https://www.zohoapis.com/crm/v7/Deals" \
  -H "Authorization: Zoho-oauthtoken $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": [{
      "id": "4150868000001234567",
      "Stage": "Proposal/Price Quote",
      "Amount": 100000
    }]
  }'
```

### 搜索记录
```bash
curl -X GET \
  "https://www.zohoapis.com/crm/v7/Contacts/search?criteria=(Email:equals:zhang@example.com)" \
  -H "Authorization: Zoho-oauthtoken $ACCESS_TOKEN"
```

## 适用场景
- 预算有限但需要企业级CRM功能
- 需要深度定制(Deluge脚本能力强)
- 已用Zoho其他产品(Books会计/Desk客服)
- 重视性价比(功能90%但价格50%)

## 限制和注意事项
- API限流: 10次/10秒(默认),Enterprise版可提升
- 用户体验不如Salesforce流畅(UI稍显陈旧)
- Deluge学习曲线陡
- 主要面向中小企业,超大企业生态不如Salesforce

## 定价
- Standard: $14/用户/月
- Professional: $23/用户/月
- Enterprise: $40/用户/月
- Ultimate: $52/用户/月
(比Salesforce便宜一半)

## 参考链接
- [官方文档](https://www.zoho.com/crm/developer/docs/api/v7/)
- [API参考](https://www.zoho.com/crm/developer/docs/api/v7/api-methods.html)
