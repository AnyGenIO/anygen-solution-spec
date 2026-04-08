<!-- doc-level: detailed -->

# Amazon SP-API

> 亚马逊卖家官方 API 的 Python SDK,管理 Listing、订单、库存、广告,跨境电商卖家必备

Amazon Selling Partner API(SP-API)是亚马逊官方提供的卖家平台 API,用于编程方式管理亚马逊店铺。相比旧版 MWS API,SP-API 采用现代 REST 架构,支持更丰富的功能:商品上架(Listings)、订单处理(Orders)、FBA 库存管理(Fulfillment)、广告投放(Advertising)、财务报表(Finances)、评价管理(Ratings)。SP-API 使用 AWS Signature V4 签名 + LWA(Login with Amazon)OAuth 认证,适合构建 ERP 集成、自动化运营工具和数据分析平台。

## 为什么选这个工具

### Amazon SP-API vs eBay API vs Shopify API

| 维度 | Amazon SP-API | eBay API | Shopify API |
|------|---------------|----------|-------------|
| **平台定位** | 全球最大电商平台(B2C) | 拍卖 + 固定价格(C2C/B2C) | 独立站建站平台(DTC) |
| **市场规模** | 3.1 亿活跃用户 | 1.3 亿活跃用户 | 200 万商家 |
| **卖家模式** | FBA(亚马逊配送)+FBM(自配送) | 卖家自配送为主 | 商家自建站+自配送 |
| **API 复杂度** | 高(AWS 签名+LWA OAuth) | 中等(OAuth 2.0) | 低(REST + API Key) |
| **Listing 管理** | 严格(需匹配目录或创建新品) | 灵活(可自由创建) | 完全自主(无限制) |
| **FBA 支持** | 原生支持(API 管理库存/发货) | 不支持 | 需第三方物流集成 |
| **广告 API** | 成熟(Sponsored Products/Brands) | 基础(Promoted Listings) | Shopify Marketing API |
| **限流** | 严格(按端点不同,1-100 次/秒) | 中等(5000 次/天) | 宽松(2 次/秒,Burst 40 次) |
| **适用场景** | 跨境电商,大规模 FBA 运营 | 二手商品,拍卖场景 | 品牌独立站,DTC 业务 |

## 快速开始

### 安装

Amazon SP-API 无官方 Python SDK,但社区提供成熟的第三方 SDK:

```bash
# Python SDK(推荐使用 python-amazon-sp-api)
pip install python-amazon-sp-api

# 验证安装
python -c "from sp_api.api import Orders; print('SP-API SDK installed')"

# 其他语言
# Java: https://github.com/amzn/selling-partner-api-models
# Node.js: https://github.com/amz-tools/amazon-sp-api
# C#: https://github.com/abuzuhri/Amazon-SP-API-CSharp
```

### 认证配置(详细步骤)

SP-API 使用双重认证:LWA OAuth(获取 Access Token)+ AWS Signature V4(签名请求)。

#### 方式一:卖家授权(Self-authorized,推荐用于自己的店铺)

**步骤 1:注册 Amazon Seller Central 账号**

1. 访问 https://sellercentral.amazon.com(美国站)或对应市场的 Seller Central
2. 注册专业卖家账号(Professional Seller,$39.99/月)
3. 完成店铺设置和身份验证

**步骤 2:注册为开发者**

1. 登录 Seller Central → **Apps & Services** → **Develop Apps**
2. 点击 **Add new app client**
3. 填写应用信息:
   - App Name: `My SP-API Integration`
   - OAuth Redirect URIs: `https://localhost`(自授权可用任意 URI)
4. 选择 **SP-API** 作为 API 类型
5. 点击 **Save and exit** → 记录 **LWA Client ID** 和 **LWA Client Secret**

**步骤 3:自授权(Self-Authorization)**

1. 在 **Develop Apps** 页面,找到刚创建的 App
2. 点击 **Edit App** → 滚动到底部 **Authorize** 部分
3. 点击 **Authorize** → 系统自动完成授权
4. 复制 **Refresh Token**(永久有效,除非撤销)

**步骤 4:创建 AWS IAM 用户和角色**

1. 登录 AWS Console → 进入 **IAM** 服务
2. 创建新用户:
   - 用户名:`sp-api-user`
   - 访问类型:选择 **Programmatic access**(编程访问)
   - 权限:选择 **Attach existing policies directly** → 搜索并附加 `PowerUserAccess`(或自定义策略)
3. 创建完成后,记录 **Access Key ID** 和 **Secret Access Key**
4. 创建 IAM 角色:
   - 进入 **IAM → Roles** → **Create role**
   - 选择 **AWS account** → **This account**
   - 附加策略:`PowerUserAccess`
   - 角色名称:`sp-api-role`
   - 记录 **Role ARN**(格式:`arn:aws:iam::123456789012:role/sp-api-role`)

**步骤 5:在 Seller Central 中关联 AWS Role**

1. 回到 Seller Central → **Develop Apps** → 编辑你的 App
2. 在 **IAM ARN** 字段中,粘贴刚创建的 Role ARN
3. 保存

#### 常见认证错误及解决方案

1. **错误:`InvalidAccessKeyId`**
   - 原因:AWS Access Key ID 错误
   - 解决:检查 IAM 用户的 Access Key

2. **错误:`SignatureDoesNotMatch`**
   - 原因:AWS 签名计算错误
   - 解决:SDK 会自动处理签名,检查 AWS Secret Key 是否正确

3. **错误:`InvalidInput: refresh_token`**
   - 原因:Refresh Token 无效或已撤销
   - 解决:重新执行自授权流程获取新 Refresh Token

4. **错误:`Unauthorized: Access to requested resource is denied`**
   - 原因:IAM Role 权限不足
   - 解决:检查 Role ARN 是否正确,以及是否附加了足够权限

5. **错误:`QuotaExceeded`**
   - 原因:API 调用超限
   - 解决:实现速率限制和退避重试逻辑

### 环境变量配置

```bash
# LWA(Login with Amazon)OAuth 凭证
export LWA_APP_ID="amzn1.application-oa2-client.abc123def456"
export LWA_CLIENT_SECRET="your_lwa_client_secret"
export SP_API_REFRESH_TOKEN="Atzr|IwEBIxxxxxx"  # 永久 Refresh Token

# AWS IAM 凭证
export SP_API_AWS_ACCESS_KEY="AKIAIOSFODNN7EXAMPLE"
export SP_API_AWS_SECRET_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export SP_API_ROLE_ARN="arn:aws:iam::123456789012:role/sp-api-role"

# 市场配置
export SP_API_REGION="us-east-1"  # 美国站
# 其他市场:
# 欧洲: eu-west-1
# 日本: us-west-2
# 澳大利亚: us-west-2

export SP_API_MARKETPLACE_ID="ATVPDKIKX0DER"  # 美国站
# 其他市场 ID:
# 英国: A1F83G8C2ARO7P
# 德国: A1PA6795UKMFR9
# 日本: A1VC38T7YXB528
# 加拿大: A2EUQ1WTGCTBG2
```

### 验证

```python
from sp_api.api import Orders
from sp_api.base import Marketplaces

# 配置凭证
credentials = {
    'refresh_token': 'Atzr|IwEBIxxxxxx',
    'lwa_app_id': 'amzn1.application-oa2-client.abc123',
    'lwa_client_secret': 'your_secret',
    'aws_access_key': 'AKIAIOSFODNN7EXAMPLE',
    'aws_secret_key': 'wJalrXUtnFEMI/K7MDENG/bPxRfiCY',
    'role_arn': 'arn:aws:iam::123456789012:role/sp-api-role'
}

# 测试查询订单
try:
    orders_api = Orders(credentials=credentials, marketplace=Marketplaces.US)
    response = orders_api.get_orders(CreatedAfter='2026-04-01T00:00:00Z')
    print("API 连接成功!")
    print(f"订单数量: {len(response.payload.get('Orders', []))}")
except Exception as e:
    print(f"错误: {e}")
```

## 核心能力

### Listings(商品管理)

- **创建 Listing**:上架新商品(需匹配亚马逊目录或创建新 ASIN)
- **更新 Listing**:修改标题、价格、库存、图片、描述
- **批量操作**:通过 Feeds API 批量上传/更新商品(XML/JSON 格式)
- **商品属性**:标题、品牌、类目、关键词、Bullet Points、描述、变体(Size/Color)
- **图片管理**:主图 + 8 张附图(最大 10 MB/张)
- **A+ Content**:品牌旗舰店页面(需品牌注册)

### Orders(订单管理)

- **查询订单**:按时间范围、状态、市场查询
- **订单详情**:买家信息、配送地址、商品明细、价格、运费
- **订单状态**:Pending、Unshipped、Shipped、Canceled、Returned
- **发货确认**:上传追踪号(Tracking Number)和承运商
- **FBM 订单处理**:卖家自配送订单的完整生命周期管理

### FBA(Fulfillment by Amazon)管理

- **库存查询**:实时查询 FBA 仓库库存(可售/不可售/预留)
- **入库计划(Inbound Shipment)**:创建入库计划,生成箱唛标签
- **库存补货推荐**:基于历史销量预测补货需求
- **移除订单**:将库存从 FBA 仓库移除(退回或销毁)
- **多渠道配送(MCF)**:使用 FBA 仓库发货到非亚马逊订单

### Catalog(目录查询)

- **ASIN 查询**:根据 ASIN 查询商品详情
- **类目浏览**:查询类目树和属性要求
- **关键词搜索**:根据关键词搜索目录中的商品
- **UPC/EAN 查询**:根据条形码查询对应 ASIN

### Pricing(定价管理)

- **价格查询**:查询商品当前价格(含促销价)
- **竞争定价**:查询同 ASIN 其他卖家的价格(Buy Box 竞争)
- **更新价格**:修改标准价格和促销价
- **批量定价**:通过 Feeds API 批量更新价格

### Advertising(广告管理)

- **Sponsored Products**:关键词广告(CPC)
- **Sponsored Brands**:品牌推广广告(展示品牌 Logo + 多个商品)
- **Sponsored Display**:展示广告(站内外投放)
- **广告活动管理**:创建、暂停、调整出价
- **广告报表**:曝光、点击、转化、ACoS(广告成本占销售额比例)

### Reports(报表管理)

- **销售报表**:订单报表、退款报表、销售流量报表
- **库存报表**:FBA 库存报表、库存健康报表
- **财务报表**:结算报表、费用报表
- **广告报表**:广告绩效报表
- **报表生成**:请求生成报表 → 轮询状态 → 下载报表(CSV/XML)

### Notifications(通知订阅)

- **实时通知**:订单创建、库存变更、Listing 变更、Buy Box 变更
- **推送方式**:SQS(Amazon Simple Queue Service)或 EventBridge
- **通知类型**:ANY_OFFER_CHANGED、FEED_PROCESSING_FINISHED、FBA_INBOUND_SHIPMENT_STATUS

### Finances(财务管理)

- **结算周期**:每 14 天结算一次
- **费用明细**:销售佣金(8-15%)、FBA 费用、退款、广告费
- **财务事件**:Shipment、Refund、Adjustment、Charge

## 使用示例(行业场景)

### 场景 1:跨境卖家 - 查询最近 7 天订单

```python
from sp_api.api import Orders
from sp_api.base import Marketplaces
from datetime import datetime, timedelta

# 配置凭证
credentials = {
    'refresh_token': 'Atzr|IwEBIxxxxxx',
    'lwa_app_id': 'amzn1.application-oa2-client.abc123',
    'lwa_client_secret': 'your_secret',
    'aws_access_key': 'AKIAIOSFODNN7EXAMPLE',
    'aws_secret_key': 'wJalrXUtnFEMI/K7MDENG/bPxRfiCY',
    'role_arn': 'arn:aws:iam::123456789012:role/sp-api-role'
}

# 查询最近 7 天的订单
orders_api = Orders(credentials=credentials, marketplace=Marketplaces.US)
start_date = (datetime.now() - timedelta(days=7)).isoformat() + 'Z'

response = orders_api.get_orders(CreatedAfter=start_date)
orders = response.payload.get('Orders', [])

print(f"订单数量: {len(orders)}")
for order in orders:
    print(f"订单号: {order['AmazonOrderId']}, 总金额: {order['OrderTotal']['Amount']} {order['OrderTotal']['CurrencyCode']}")
```

### 场景 2:卖家运营 - 批量更新商品价格

```python
from sp_api.api import Feeds
from sp_api.base import Marketplaces, FeedType
import json

# 准备价格更新数据(JSON Lines 格式)
price_feed = [
    {
        "sku": "MY-SKU-001",
        "price": {
            "value": 29.99,
            "currency": "USD"
        }
    },
    {
        "sku": "MY-SKU-002",
        "price": {
            "value": 39.99,
            "currency": "USD"
        }
    }
]

# 转换为 JSON Lines 格式
feed_content = '\n'.join([json.dumps(item) for item in price_feed])

# 提交 Feed
feeds_api = Feeds(credentials=credentials, marketplace=Marketplaces.US)
response = feeds_api.create_feed(
    feedType=FeedType.JSON_LISTINGS_FEED,
    marketplaceIds=['ATVPDKIKX0DER'],
    inputFeedDocumentId=feed_content  # 简化示例,实际需先上传到 S3
)

feed_id = response.payload['feedId']
print(f"Feed ID: {feed_id}")

# 轮询 Feed 处理状态
import time
while True:
    feed_status = feeds_api.get_feed(feed_id)
    status = feed_status.payload['processingStatus']
    print(f"Feed 状态: {status}")
    if status in ['DONE', 'CANCELLED', 'FATAL']:
        break
    time.sleep(10)
```

### 场景 3:FBA 卖家 - 查询库存并生成补货建议

```python
from sp_api.api import Inventories
from sp_api.base import Marketplaces

inventories_api = Inventories(credentials=credentials, marketplace=Marketplaces.US)

# 查询 FBA 库存摘要
response = inventories_api.get_inventory_summary_marketplace(
    granularityType='Marketplace',
    granularityId='ATVPDKIKX0DER',
    marketplaceIds=['ATVPDKIKX0DER']
)

inventory_summaries = response.payload.get('inventorySummaries', [])

print("库存摘要:")
for item in inventory_summaries:
    sku = item['sellerSku']
    asin = item.get('asin', 'N/A')
    total_quantity = item.get('totalQuantity', 0)
    fc_quantity = item.get('fulfillableQuantity', 0)  # 可售库存
    inbound_quantity = item.get('inboundWorkingQuantity', 0)  # 在途库存
    
    print(f"SKU: {sku}, ASIN: {asin}, 可售: {fc_quantity}, 在途: {inbound_quantity}")
    
    # 简单补货逻辑(可售 < 100 时提醒)
    if fc_quantity < 100:
        print(f"  ⚠️ 库存不足,建议补货!")
```

### 场景 4:广告优化 - 查询广告活动绩效

```python
from sp_api.api import CatalogItems
from sp_api.base import Marketplaces

# 注意:Advertising API 需单独申请权限,且使用不同的 SDK
# 这里用 Catalog API 查询商品信息作为示例

catalog_api = CatalogItems(credentials=credentials, marketplace=Marketplaces.US)

# 查询 ASIN 详情
asin = 'B08N5WRWNW'
response = catalog_api.get_catalog_item(asin=asin, marketplaceIds=['ATVPDKIKX0DER'])

item = response.payload
print(f"ASIN: {asin}")
print(f"标题: {item.get('summaries', [{}])[0].get('itemName', 'N/A')}")
print(f"品牌: {item.get('summaries', [{}])[0].get('brand', 'N/A')}")
```

### 场景 5:订单履约 - 确认发货并上传追踪号

```python
from sp_api.api import Orders

orders_api = Orders(credentials=credentials, marketplace=Marketplaces.US)

# 确认订单发货(FBM - 卖家自配送)
order_id = '123-4567890-1234567'
tracking_number = 'TRACK123456789'
carrier_code = 'USPS'  # 或 'UPS', 'FedEx', 'DHL'

# 注意:实际 API 调用需使用 Shipping API
# 这里展示概念性流程

print(f"订单 {order_id} 已发货,追踪号: {tracking_number}")
```

### 场景 6:数据分析 - 导出销售报表

```python
from sp_api.api import Reports
from sp_api.base import Marketplaces, ReportType
import time

reports_api = Reports(credentials=credentials, marketplace=Marketplaces.US)

# 请求生成销售报表(最近 30 天)
from datetime import datetime, timedelta
start_date = (datetime.now() - timedelta(days=30)).isoformat() + 'Z'
end_date = datetime.now().isoformat() + 'Z'

response = reports_api.create_report(
    reportType=ReportType.GET_FLAT_FILE_ALL_ORDERS_DATA_BY_ORDER_DATE_GENERAL,
    marketplaceIds=['ATVPDKIKX0DER'],
    dataStartTime=start_date,
    dataEndTime=end_date
)

report_id = response.payload['reportId']
print(f"报表 ID: {report_id}")

# 轮询报表生成状态
while True:
    report_status = reports_api.get_report(report_id)
    status = report_status.payload['processingStatus']
    print(f"报表状态: {status}")
    if status in ['DONE', 'CANCELLED', 'FATAL']:
        break
    time.sleep(10)

# 下载报表
if status == 'DONE':
    document_id = report_status.payload['reportDocumentId']
    document = reports_api.get_report_document(document_id)
    # 下载并解析报表内容(CSV 格式)
    print("报表下载完成")
```

### 场景 7:客服工具 - 查询订单详情和买家留言

```python
from sp_api.api import Orders

orders_api = Orders(credentials=credentials, marketplace=Marketplaces.US)

# 查询订单详情
order_id = '123-4567890-1234567'
order = orders_api.get_order(order_id)
order_data = order.payload

print(f"订单号: {order_data['AmazonOrderId']}")
print(f"买家邮箱: {order_data.get('BuyerEmail', '未提供')}")
print(f"配送地址: {order_data['ShippingAddress']}")

# 查询订单商品明细
items = orders_api.get_order_items(order_id)
for item in items.payload['OrderItems']:
    print(f"商品: {item['Title']}, SKU: {item['SellerSKU']}, 数量: {item['QuantityOrdered']}")
```

### 场景 8:自动化补货 - FBA 入库计划创建

```python
from sp_api.api import FbaInbound
from sp_api.base import Marketplaces

fba_api = FbaInbound(credentials=credentials, marketplace=Marketplaces.US)

# 创建入库计划(简化示例,实际需更多参数)
inbound_shipment_plan = {
    'shipFromAddress': {
        'name': 'Your Warehouse',
        'addressLine1': '123 Main St',
        'city': 'Los Angeles',
        'stateOrProvinceCode': 'CA',
        'postalCode': '90001',
        'countryCode': 'US'
    },
    'items': [
        {
            'sellerSKU': 'MY-SKU-001',
            'quantity': 500,
            'prepDetailsList': []
        }
    ]
}

# 实际调用需参考完整 API 文档
print("入库计划已创建")
```

## 适用场景

### 典型工作流

1. **新品上架工作流(Brand 卖家)**
   - 准备商品信息(标题、图片、描述、关键词)
   - 通过 Listings API 创建新 ASIN 或匹配现有 ASIN
   - 上传主图 + 附图(需符合亚马逊图片规范)
   - 设置价格和库存
   - 提交 A+ Content(品牌旗舰店页面)
   - 监控 Listing 状态(Active/Inactive/Suppressed)

2. **FBA 补货工作流**
   - 每日查询 FBA 库存(可售/在途/不可售)
   - 根据销售速度预测缺货时间
   - 创建入库计划(Inbound Shipment)
   - 生成箱唛标签并打印
   - 发货到 FBA 仓库
   - 跟踪入库进度(通过 Notifications API 订阅入库状态变更)
   - 库存上架后自动恢复 Listing 可售状态

3. **订单自动化处理工作流(FBM)**
   - 通过 Notifications API 订阅订单创建事件(SQS)
   - 新订单推送到 ERP 系统
   - 打印发货单和面单
   - 仓库拣货 → 打包 → 发货
   - 通过 SP-API 上传追踪号
   - 订单状态自动更新为 Shipped
   - 买家收货后自动请求 Review

4. **广告优化工作流**
   - 每日拉取广告绩效报表(曝光、点击、转化、ACoS)
   - 识别高 ACoS 关键词(成本高但转化低)
   - 自动降低出价或暂停关键词
   - 识别低 ACoS 高转化关键词
   - 自动提升出价以获取更多流量
   - 生成每周广告优化报告

## 与其他工具的区分

### Amazon SP-API vs Shopify API(对比独立站)

| 维度 | Amazon SP-API | Shopify API |
|------|---------------|-------------|
| **流量来源** | 亚马逊平台流量(3 亿用户) | 需自己引流(SEO/广告) |
| **佣金** | 8-15%(按类目) | 无佣金(仅交易手续费 2.9%) |
| **品牌控制** | 弱(受平台规则限制) | 强(完全自主) |
| **上架难度** | 高(需匹配目录) | 低(完全自主) |
| **物流** | FBA(托管)或 FBM(自配送) | 需自己对接物流 |
| **客户数据** | 受限(不提供买家邮箱) | 完全拥有 |
| **适用场景** | 快速起量,利用平台流量 | 品牌建设,长期客户运营 |

### Amazon SP-API vs Walmart API

| 维度 | Amazon | Walmart |
|------|--------|---------|
| **市场份额** | 40%(美国电商) | 7%(美国电商) |
| **审核难度** | 中等 | 高(邀请制) |
| **佣金** | 8-15% | 6-15%(略低) |
| **FBA 类似服务** | FBA(成熟) | WFS(较新) |
| **API 成熟度** | 高 | 中等 |
| **适用场景** | 主力平台 | 多渠道布局 |

## 限制和注意事项

### API 调用限额

| API 类型 | 速率限制 | 备注 |
|---------|---------|------|
| **Orders API** | 0.0167 次/秒(1 次/分钟) | 查询订单列表 |
| **Orders API(单订单)** | 0.5 次/秒 | 查询单个订单详情 |
| **Listings API** | 5 次/秒 | 更新 Listing |
| **Feeds API** | 0.0167 次/秒 | 提交 Feed |
| **Reports API** | 0.0167 次/秒 | 请求报表 |
| **Catalog API** | 5 次/秒 | 查询目录 |
| **Pricing API** | 1 次/秒 | 查询价格 |
| **Notifications API** | 无限制 | 订阅通知 |

**限流机制**:令牌桶算法,超限返回 429 错误,需实现退避重试。

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **InvalidAccessKeyId** | AWS Access Key 错误 | 检查 IAM 凭证 |
| **SignatureDoesNotMatch** | AWS 签名错误 | 检查 AWS Secret Key 和系统时间 |
| **InvalidInput: refresh_token** | Refresh Token 无效 | 重新授权获取新 Token |
| **QuotaExceeded** | API 限流 | 降低请求频率,实现退避重试 |
| **InvalidParameterValue** | 参数错误 | 检查 API 文档中的参数格式 |
| **ResourceNotFound** | 资源不存在(如 ASIN 不存在) | 检查 ASIN/SKU 是否正确 |
| **InternalFailure** | 亚马逊服务器错误 | 稍后重试 |
| **SKU_ALREADY_EXISTS** | SKU 重复 | 使用唯一的 SKU |

### 注意事项

- **专业卖家账号必需**:个人卖家无法使用 SP-API
- **品牌注册(Brand Registry)**:部分功能(如 A+ Content)需品牌注册
- **类目审核**:某些类目(如食品、化妆品)需额外资质
- **图片规范**:主图必须白底,尺寸至少 1000x1000 像素
- **关键词限制**:后台关键词最多 249 字节(UTF-8)
- **变体限制**:单个父 ASIN 最多 2000 个变体(子 ASIN)
- **Review 政策**:禁止主动请求好评(但可请求 Review)
- **库存健康**:长期滞销库存(180 天)会产生长期仓储费
- **时区**:API 时间均为 UTC,需转换为本地时区

## 定价(详细分级)

### Amazon 卖家费用

| 费用项 | 价格 | 备注 |
|--------|------|------|
| **专业卖家月租** | $39.99/月 | 必需(个人卖家无 API 访问) |
| **销售佣金** | 8-15%(按类目) | 电子产品 8%,服装 17% |
| **FBA 费用(小标准件)** | $3.22/件起 | 12 oz 以下,$0.50/磅仓储费 |
| **FBA 费用(大标准件)** | $5.14/件起 | 超过 20 磅另计 |
| **长期仓储费** | $6.90/立方英尺/月 | 365 天以上库存 |
| **退货处理费** | $5/件(服装) | 按类目不同 |
| **广告费(Sponsored Products)** | CPC 竞价 | 平均 $0.50-$2/点击 |

### API 费用

- **SP-API 使用**:完全免费(无 API 调用费用)
- **AWS 费用**:SQS 订阅通知产生的 AWS 费用(通常 < $5/月)

## 参考链接

### 官方文档

- [SP-API 开发者文档](https://developer-docs.amazon.com/sp-api/)
- [SP-API 参考](https://developer-docs.amazon.com/sp-api/docs)
- [Seller Central](https://sellercentral.amazon.com/)
- [认证指南](https://developer-docs.amazon.com/sp-api/docs/sp-api-authentication)
- [API 速率限制](https://developer-docs.amazon.com/sp-api/docs/usage-plans-and-rate-limits-in-the-sp-api)

### 进阶学习

- [SP-API GitHub(官方模型)](https://github.com/amzn/selling-partner-api-models)
- [Feeds API 指南](https://developer-docs.amazon.com/sp-api/docs/feeds-api-v2021-06-30-reference)
- [Notifications API 指南](https://developer-docs.amazon.com/sp-api/docs/notifications-api-v1-reference)
- [FBA 入库指南](https://developer-docs.amazon.com/sp-api/docs/fulfillment-inbound-api-v0-reference)

### 社区资源

- [Amazon Seller Forums](https://sellercentral.amazon.com/forums)
- [Stack Overflow(amazon-sp-api 标签)](https://stackoverflow.com/questions/tagged/amazon-sp-api)
- [Reddit r/FulfillmentByAmazon](https://www.reddit.com/r/FulfillmentByAmazon/)
- [Seller Central Help](https://sellercentral.amazon.com/help/hub)

### SDK 和工具

- [Python SDK(python-amazon-sp-api)](https://github.com/saleweaver/python-amazon-sp-api)
- [Node.js SDK](https://github.com/amz-tools/amazon-sp-api)
- [Java SDK(官方)](https://github.com/amzn/selling-partner-api-models/tree/main/clients/sellingpartner-api-aa-java)
- [Postman Collection](https://github.com/amzn/selling-partner-api-models/tree/main/postman)
- [SellerLabs(第三方工具)](https://www.sellerlabs.com/)
- [Jungle Scout(选品工具)](https://www.junglescout.com/)
