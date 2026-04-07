# DealHub API

> 独立CPQ+Sales Playbook平台,比Salesforce CPQ灵活,支持多CRM

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `DEALHUB_API_KEY` — API密钥
  - `DEALHUB_TENANT_ID` — 租户ID
- **获取步骤:**
  1. 登录 DealHub Dashboard
  2. Settings → API → Generate Key
  3. 复制 API Key 和 Tenant ID
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $DEALHUB_API_KEY" \
    -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
    https://api.dealhub.io/v1/health
  ```

## 核心能力
- 产品配置和报价生成
- 电子签名集成(DocuSign/Adobe Sign)
- Sales Playbook(按Deal类型自动推荐策略)
- 订阅计费管理
- 报价室(Deal Room)——买方可见的协作空间
- 多CRM集成(Salesforce/HubSpot/Dynamics)
- 报价模板和品牌定制
- 审批工作流

## 使用示例

### 获取产品列表
```bash
curl -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  https://api.dealhub.io/v1/products
```

### 创建报价单
```bash
curl -X POST \
  -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  -H "Content-Type: application/json" \
  https://api.dealhub.io/v1/quotes \
  -d '{
    "name": "企业版报价 - Q2 2026",
    "accountId": "acc_12345",
    "opportunityId": "opp_67890",
    "expiryDate": "2026-05-31",
    "currency": "USD"
  }'
```

### 添加产品到报价
```bash
curl -X POST \
  -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  -H "Content-Type: application/json" \
  https://api.dealhub.io/v1/quotes/{quote_id}/items \
  -d '{
    "productId": "prod_abc123",
    "quantity": 50,
    "discount": 10,
    "priceOverride": null
  }'
```

### 生成Deal Room链接
```bash
curl -X POST \
  -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  -H "Content-Type: application/json" \
  https://api.dealhub.io/v1/dealrooms \
  -d '{
    "quoteId": "quote_xyz789",
    "buyerEmail": "buyer@example.com",
    "expiryDays": 30
  }'
```

### 发送电子签名请求
```bash
curl -X POST \
  -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  -H "Content-Type: application/json" \
  https://api.dealhub.io/v1/quotes/{quote_id}/send-for-signature \
  -d '{
    "signerEmail": "signer@example.com",
    "signerName": "张三",
    "message": "请查看并签署报价单"
  }'
```

### 获取报价状态
```bash
curl -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  https://api.dealhub.io/v1/quotes/{quote_id}/status
```

### 获取Sales Playbook推荐
```bash
curl -H "Authorization: Bearer $DEALHUB_API_KEY" \
  -H "X-DealHub-TenantId: $DEALHUB_TENANT_ID" \
  "https://api.dealhub.io/v1/playbooks/recommendations?dealType=enterprise&stage=proposal"
```

## 适用场景
- 不想被Salesforce CPQ绑死
- 需要买卖双方协作的Deal Room
- 既要CPQ又要签署功能一站式
- 多CRM环境(不局限于Salesforce)
- 订阅业务的报价和计费管理
- 复杂产品配置(Bundle/Add-ons)

## 限制和注意事项
- API限流: 100次/分钟
- 电子签名集成需额外配置DocuSign/Adobe账户
- Deal Room链接有过期时间
- 复杂定价规则可能需要专业服务支持
- 主要面向中大型企业
- 模板定制需要学习DealHub DSL

## 定价
- 需联系销售(通常按席位+功能模块)
- 估算: $80-150/用户/月起
- 企业版定制报价

## 参考链接
- [官方文档](https://developers.dealhub.io/)
- [API参考](https://developers.dealhub.io/api-reference)
- [集成指南](https://support.dealhub.io/hc/en-us/categories/360002373634-Integrations)
