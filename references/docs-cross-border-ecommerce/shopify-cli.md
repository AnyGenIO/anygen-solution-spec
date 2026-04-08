<!-- doc-level: detailed -->

# Shopify CLI

> Shopify 官方命令行工具 + GraphQL Admin API，全球领先的电商 SaaS 平台，管理店铺、产品、订单、主题和应用开发

Shopify 是全球最大的电商 SaaS 平台，为超过 200 万商家提供在线商店和销售渠道管理服务。Shopify CLI 是官方命令行工具，主要用于主题开发、应用开发和店铺管理自动化。业务数据操作通过 Admin API（REST 和 GraphQL）实现。平台提供完整的电商解决方案，包括产品管理、订单处理、支付集成、物流管理、营销工具和 App Store 生态（8000+ 应用）。

## 为什么选这个工具

### Shopify vs WooCommerce

| 维度 | Shopify | WooCommerce |
|------|---------|-------------|
| **定位** | 托管 SaaS 平台 | WordPress 插件（自托管） |
| **技术门槛** | 低（无需维护服务器） | 高（需配置主机/数据库/安全） |
| **扩展性** | 自动扩展，无需担心流量 | 需手动扩展服务器 |
| **价格** | $39-$399/月订阅 | 免费插件 + 自己承担主机费 |
| **支付手续费** | 2.9%+$0.30（Shopify Payments） | 由支付网关决定（如 Stripe 2.9%） |
| **适用场景** | 快速启动、专注销售、无技术团队 | 需深度定制、已有 WordPress 网站 |

### Shopify vs Amazon

| 维度 | Shopify | Amazon |
|------|---------|--------|
| **品牌所有权** | 完全拥有客户数据和品牌 | 客户属于 Amazon，品牌受限 |
| **利润率** | 高（2% 交易费 + 订阅费） | 低（8-15% 佣金 + FBA 费用） |
| **流量来源** | 需自己引流（广告/SEO/社交） | Amazon 自带流量 |
| **定制化** | 高（完全自定义店铺） | 低（统一产品页模板） |
| **适用场景** | 建立自有品牌、DTC（直接面向消费者） | 快速销售、利用平台流量 |

### Shopify vs 其他电商平台（BigCommerce, Magento）

- **Shopify**: 易用性最佳，生态最完善，适合 90% 电商商家
- **BigCommerce**: 功能更强大，适合大型企业，但学习曲线陡
- **Magento**: 完全开源，深度定制，需专业开发团队

## 快速开始

### 安装

```bash
# macOS/Linux
npm install -g @shopify/cli

# 验证安装
shopify version

# 或使用 Homebrew (macOS)
brew tap shopify/shopify
brew install shopify-cli

# Windows (推荐使用 Node.js 方式)
npm install -g @shopify/cli
```

### 认证配置（详细步骤）

#### 方式一：CLI 交互式登录（推荐用于开发测试）

**步骤 1: 登录 Shopify 账户**

```bash
# CLI 会打开浏览器进行授权
shopify auth login

# 选择 Partner 账户或商店账户
# - Partner 账户：适合应用/主题开发者
# - Store 账户：适合商家管理自己的店铺
```

**步骤 2: 验证连接**

```bash
# 显示当前登录状态
shopify auth status

# 列出可访问的店铺
shopify store list
```

#### 方式二：使用 Custom App（推荐用于生产/自动化）

**步骤 1: 创建 Custom App**

1. 登录 Shopify Admin: `https://your-store.myshopify.com/admin`
2. 左侧菜单 → Settings → Apps and sales channels
3. 点击 "Develop apps" → "Create an app"
4. 填写 App 名称：`My Integration App`
5. 点击 "Create app"

**步骤 2: 配置 API Scopes（权限）**

1. 进入 App → "Configuration"
2. 点击 "Configure Admin API scopes"
3. 选择需要的权限（常用权限）：
   - **Products**: `read_products`, `write_products` — 产品读写
   - **Orders**: `read_orders`, `write_orders` — 订单读写
   - **Customers**: `read_customers`, `write_customers` — 客户读写
   - **Inventory**: `read_inventory`, `write_inventory` — 库存读写
   - **Fulfillments**: `read_fulfillments`, `write_fulfillments` — 发货管理
   - **Marketing**: `read_marketing_events`, `write_marketing_events` — 营销事件
   - **Analytics**: `read_reports` — 报表数据
4. 点击 "Save"

**步骤 3: 获取 Access Token**

1. 进入 App → "API credentials"
2. 点击 "Install app" → 确认安装
3. 复制显示的 **Admin API access token**（只显示一次！立即保存）
4. 记录 **API key** 和 **API secret key**

**步骤 4: 配置环境变量**

```bash
# Admin API Access Token（用于 API 调用）
export SHOPIFY_ACCESS_TOKEN="shpat_abcd1234..."

# 店铺域名
export SHOPIFY_STORE_DOMAIN="your-store.myshopify.com"

# API Version（推荐使用最新稳定版）
export SHOPIFY_API_VERSION="2024-01"
```

**步骤 5: 验证连接**

```bash
# 使用 GraphQL API 查询店铺信息
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ shop { name email } }"}'
```

#### 常见认证错误及解决方案

1. **错误: `[API] Invalid API key or access token (unrecognized login or wrong password)`**
   - 原因：Access Token 无效或已过期
   - 解决：重新生成 Custom App 的 Access Token

2. **错误: `Access denied for XXX scope`**
   - 原因：App 缺少必要的 API scopes
   - 解决：返回 App 设置，添加缺失的 scopes 并重新安装

3. **错误: `Exceeded 2 calls per second for api client`**
   - 原因：触发 API 限流（REST API: 2次/秒，GraphQL: 1000 cost points/秒）
   - 解决：添加请求延迟或使用批量查询（GraphQL Bulk Operations）

### 环境变量配置

```bash
# Admin API Access Token
export SHOPIFY_ACCESS_TOKEN="shpat_abcd1234..."

# 店铺域名
export SHOPIFY_STORE_DOMAIN="your-store.myshopify.com"

# API 版本（格式: YYYY-MM）
export SHOPIFY_API_VERSION="2024-01"

# Partner 账户 Token（用于 CLI 部署）
export SHOPIFY_CLI_PARTNERS_TOKEN="shppt_xyz5678..."
```

## 核心能力

### 产品管理（Products）

- **Products（产品）**：标题、描述、价格、SKU、库存
- **Variants（变体）**：尺寸、颜色、材质等多规格管理
- **Collections（集合）**：产品分组（如"夏季新品"、"热卖榜单"）
- **Images（图片）**：产品主图和多图管理
- **Metafields（元字段）**：自定义产品属性（如"原产地"、"材质"）
- **Product Types & Tags（类型和标签）**：分类和筛选

### 订单管理（Orders）

- **Orders（订单）**：订单创建、查询、更新、取消
- **Fulfillments（发货）**：发货确认、物流跟踪号上传
- **Refunds（退款）**：部分退款、全额退款、退货处理
- **Transactions（交易）**：支付状态、退款记录
- **Draft Orders（草稿订单）**：手动创建订单（如电话订单）
- **Abandoned Checkouts（弃单）**：未完成结账的订单，用于营销恢复

### 客户管理（Customers）

- **Customers（客户）**：客户信息、订单历史、标签
- **Customer Groups（客户群组）**：VIP 客户、批发客户分组（Shopify Plus）
- **Addresses（地址）**：收货地址管理
- **Customer Metafields（客户元字段）**：自定义客户属性

### 库存和物流（Inventory & Fulfillment）

- **Inventory Levels（库存水平）**：多仓库库存管理
- **Locations（仓库位置）**：多仓库配置
- **Fulfillment Services（物流服务）**：第三方物流集成（3PL）
- **Shipping Zones & Rates（运费配置）**：按地区设置运费规则

### 营销和折扣（Marketing）

- **Discounts（折扣码）**：固定金额、百分比、买X送Y
- **Price Rules（价格规则）**：批量折扣、客户群组定价
- **Gift Cards（礼品卡）**：发行和管理礼品卡
- **Marketing Events（营销活动）**：追踪活动 ROI

### 主题和网站（Themes & Online Store）

- **Themes（主题）**：上传、部署、预览主题
- **Pages（页面）**：静态页面（如关于我们、FAQ）
- **Blogs & Articles（博客）**：内容营销
- **Redirects（重定向）**：URL 重定向配置
- **Metaobjects（元对象）**：自定义结构化内容（Shopify 2.0）

### 分析和报表（Analytics）

- **Reports（报表）**：销售报表、产品报表、客户报表
- **Analytics（分析）**：流量来源、转化率、AOV（平均订单价值）
- **Shopify Analytics API**：自定义数据分析

### 应用开发（App Development）

- **Shopify CLI**：本地开发、热重载、调试
- **App Extensions**：结账扩展、产品页扩展、订单状态页扩展
- **Webhooks（事件通知）**：订单创建、产品更新等实时通知
- **App Bridge**：嵌入 Shopify Admin 的应用

## 使用示例（行业场景）

### 场景 1：跨境电商 - 批量上传产品（从 CSV）

```bash
# 准备产品 CSV 文件（示例：products.csv）
# Handle,Title,Body (HTML),Vendor,Type,Tags,Published,Option1 Name,Option1 Value,Variant SKU,Variant Price,Variant Inventory Qty

# 使用 GraphQL Bulk Operations 批量导入（推荐）
cat << 'EOF' > import_products.graphql
mutation {
  bulkOperationRunMutation(
    mutation: """
      mutation productCreate($input: ProductInput!) {
        productCreate(input: $input) {
          product { id }
        }
      }
    """,
    stagedUploadPath: "path/to/products.jsonl"
  ) {
    bulkOperation { id }
  }
}
EOF

# 调用 API
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d @import_products.graphql
```

### 场景 2：DTC 品牌 - 查询本周新订单

```bash
# 使用 GraphQL 查询订单
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "{ orders(first: 50, query: \"created_at:>=' + $(date -d '7 days ago' +%Y-%m-%d) + '\") { edges { node { id name totalPriceSet { shopMoney { amount } } customer { email } } } } }"
  }'
```

### 场景 3：补货管理 - 更新库存数量

```bash
# 使用 GraphQL 更新库存
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "mutation { inventoryAdjustQuantity(input: { inventoryLevelId: \"gid://shopify/InventoryLevel/123456789\", availableDelta: 50 }) { inventoryLevel { available } } }"
  }'
```

### 场景 4：订单履约 - 确认发货并上传追踪号

```bash
# 创建 Fulfillment
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "mutation { fulfillmentCreateV2(fulfillment: { lineItemsByFulfillmentOrder: { fulfillmentOrderId: \"gid://shopify/FulfillmentOrder/123\", fulfillmentOrderLineItems: [{ id: \"gid://shopify/FulfillmentOrderLineItem/456\", quantity: 1 }] }, trackingInfo: { company: \"UPS\", number: \"1Z999AA10123456784\" } }) { fulfillment { id } } }"
  }'
```

### 场景 5：营销活动 - 创建折扣码

```bash
# 创建百分比折扣码
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "mutation { discountCodeBasicCreate(basicCodeDiscount: { title: \"SUMMER20\", code: \"SUMMER20\", startsAt: \"2026-06-01T00:00:00Z\", endsAt: \"2026-08-31T23:59:59Z\", customerGets: { value: { percentage: 0.2 }, items: { all: true } } }) { codeDiscountNode { id } } }"
  }'
```

### 场景 6：主题开发 - 本地开发和部署

```bash
# 初始化新主题项目
shopify theme init my-theme

# 拉取线上主题到本地
shopify theme pull --store your-store.myshopify.com

# 本地开发（热重载）
shopify theme dev --store your-store.myshopify.com

# 部署到线上（开发环境）
shopify theme push --development

# 发布主题到生产环境
shopify theme publish
```

### 场景 7：自动化脚本 - 每日销售报表

```bash
# 使用 GraphQL 查询昨日销售数据
#!/bin/bash

YESTERDAY=$(date -d '1 day ago' +%Y-%m-%d)

curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{
    \"query\": \"{ orders(first: 250, query: \\\"created_at:$YESTERDAY\\\") { edges { node { id name totalPriceSet { shopMoney { amount currencyCode } } } } } }\"
  }" | jq '.data.orders.edges[] | .node | {id, name, total: .totalPriceSet.shopMoney.amount}'
```

### 场景 8：客户管理 - 导出高价值客户

```bash
# 查询总消费 > $1000 的客户
curl -X POST \
  https://your-store.myshopify.com/admin/api/2024-01/graphql.json \
  -H "X-Shopify-Access-Token: $SHOPIFY_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "{ customers(first: 100, query: \"total_spent:>1000\") { edges { node { id email displayName totalSpent } } } }"
  }'
```

## 适用场景

### 典型工作流

1. **跨境电商工作流**
   - 从供应商处获取产品数据 → 批量导入到 Shopify
   - 设置多币种、多语言店铺
   - 配置国际物流和关税
   - 使用 Shopify Markets 管理多国市场

2. **DTC 品牌工作流**
   - 创建自定义主题展示品牌故事
   - 使用 Shopify Email/SMS 进行客户营销
   - 通过 Shopify POS 同步线下门店
   - 集成 Klaviyo/Omnisend 进行邮件自动化

3. **订单履约工作流**
   - 接收订单 → 打印订单（Shopify Printer）
   - 拣货打包 → 上传物流追踪号
   - 自动发送发货邮件给客户
   - 处理退货退款请求

4. **库存管理工作流**
   - 多仓库库存同步（如美国仓、欧洲仓）
   - 低库存预警（通过 Webhooks）
   - 自动补货（集成 ERP 系统）
   - 滞销产品清仓（创建自动折扣）

## 与其他工具的区分

### Shopify vs WooCommerce（详细对比）

| 维度 | Shopify | WooCommerce |
|------|---------|-------------|
| **托管** | 全托管，无需担心服务器 | 需自己购买主机、配置 SSL |
| **扩展性** | 自动扩展，支持黑五大促 | 需手动升级服务器配置 |
| **安全性** | PCI DSS 认证，自动安全更新 | 需自己维护安全补丁 |
| **主题** | 官方 + 第三方主题市场 | WordPress 生态主题 |
| **支付手续费** | 2.9%+$0.30（Shopify Payments） | 由 Stripe/PayPal 决定 |
| **交易费** | 0%（使用 Shopify Payments）或 0.5-2% | 无交易费 |
| **技术门槛** | 低，无需编程 | 中等，需了解 WordPress/PHP |
| **总成本** | $39-$399/月 | 主机费 + 插件费（$10-100/月） |

### Shopify vs BigCommerce

| 维度 | Shopify | BigCommerce |
|------|---------|-------------|
| **易用性** | 更简单，界面友好 | 功能更多，学习曲线陡 |
| **交易费** | 0.5-2%（非 Shopify Payments） | 无交易费 |
| **产品变体** | 最多 100 个变体/产品 | 最多 600 个变体/产品 |
| **内置功能** | 基础功能，依赖 App 扩展 | 内置更多高级功能（如多货币） |
| **App 生态** | 8000+ 应用 | 1000+ 应用 |
| **适用场景** | 中小电商、DTC 品牌 | 大型企业、复杂产品线 |

## 限制和注意事项

### API 调用限额

| API 类型 | 限流规则 | 建议策略 |
|---------|---------|---------|
| **REST Admin API** | 2 次/秒（burst: 40 次/秒） | 使用指数退避重试 |
| **GraphQL Admin API** | 1000 cost points/秒 | 使用 `cost` 字段预估，批量查询 |
| **Storefront API** | 无硬性限制，按店铺计划自动扩展 | 用于前端展示 |
| **Bulk Operations** | 每次最多 100 万条记录 | 用于大批量数据导入导出 |

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **`Exceeded 2 calls per second`** | REST API 限流 | 添加 500ms 延迟或改用 GraphQL |
| **`Throttled`** | GraphQL cost 超限 | 减少查询字段或分批查询 |
| **`Access denied for XXX scope`** | 缺少 API 权限 | 在 Custom App 中添加 scope |
| **`Product not found`** | 产品 ID 不存在 | 使用 `handle` 或 `sku` 查询 |
| **`Inventory item not found`** | 库存未关联到 Location | 创建 InventoryLevel 关联 |

### 注意事项

- **API 版本生命周期**：每个版本支持 12 个月，需定期升级到最新版本
- **Shopify Payments 限制**：仅部分国家可用（美国、加拿大、英国等）
- **产品限制**：
  - 免费图片托管最多 250 张/产品
  - 产品变体最多 100 个/产品
  - Metafields 最多 25 个命名空间/资源
- **Shopify Plus 独占功能**：
  - Checkout.liquid 自定义结账页
  - Launchpad（定时任务，如闪购活动）
  - 批发渠道（Wholesale Channel）
- **App Store 审核**：公开发布的 App 需通过严格审核（2-4 周）

## 定价（详细分级）

### Shopify 订阅计划

| 版本 | 价格（年付） | 核心功能 | 适用场景 |
|------|------------|---------|---------|
| **Starter** | $5/月 | 社交媒体销售、Buy Button | 社交电商、测试产品 |
| **Basic** | $39/月 | 完整在线商店、2 个员工账号 | 初创电商、小型商家 |
| **Shopify** | $105/月 | 5 个员工账号、专业报表 | 成长中的电商（月销 $10k-50k） |
| **Advanced** | $399/月 | 15 个员工账号、高级报表、第三方运费计算 | 大型电商（月销 $50k+） |
| **Shopify Plus** | $2,000/月起 | 无限员工、自动化 Launchpad、API 批量操作 | 企业级（月销 $500k+） |

### 交易费用

| 支付方式 | Basic | Shopify | Advanced | Plus |
|---------|-------|---------|----------|------|
| **Shopify Payments** | 2.9% + $0.30 | 2.7% + $0.30 | 2.5% + $0.30 | 2.15% + $0.30 |
| **第三方支付（额外交易费）** | 2.0% | 1.0% | 0.5% | 0.15% |

### App 费用

- **免费 App**：约 30% 的 App 免费
- **订阅 App**：$5-$300/月（如 Klaviyo $20/月起、Yotpo $19/月起）
- **一次性付费 App**：$10-$500（如高级主题 $180-$350）

### 其他费用

- **Shopify Email**：前 10,000 封/月免费，之后 $1/1000 封
- **Shopify Capital**：借款费用 10-16%（不是年利率，是总费用）
- **POS Pro**：$89/月（用于实体店）

## 参考链接

### 官方文档

- [Shopify 开发者文档](https://shopify.dev/docs)
- [Shopify CLI 命令参考](https://shopify.dev/docs/api/shopify-cli)
- [GraphQL Admin API 参考](https://shopify.dev/docs/api/admin-graphql)
- [REST Admin API 参考](https://shopify.dev/docs/api/admin-rest)
- [Liquid 模板语言](https://shopify.dev/docs/api/liquid)

### 进阶学习

- [Shopify App 开发教程](https://shopify.dev/docs/apps/getting-started)
- [Shopify Theme 开发指南](https://shopify.dev/docs/themes)
- [Shopify Plus 合作伙伴计划](https://www.shopify.com/plus/partners)
- [Shopify Unite（开发者大会）](https://unite.shopify.com/)

### 社区资源

- [Shopify Community Forums](https://community.shopify.com/)
- [Shopify Partners Slack](https://shopifypartners.slack.com/)
- [eCommerce Fuel（电商社区）](https://www.ecommercefuel.com/)

### 第三方工具

- [Shopify MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/shopify) — 通过 MCP 协议管理店铺
- [Shopify API Libraries](https://shopify.dev/docs/api/admin-rest#libraries) — 官方 SDK（Ruby, Python, Node.js, PHP）
