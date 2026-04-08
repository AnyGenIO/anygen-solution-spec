<!-- doc-level: detailed -->

# TikTok Shop Open API

> TikTok 小店官方 API,管理产品、订单、物流,社交电商新势力,直播带货必备

TikTok Shop Open API 是 TikTok 官方提供的电商平台 API,支持商家和 ISV(独立软件供应商)编程方式管理 TikTok 小店。平台深度融合短视频和直播带货场景,提供商品上架(Products)、订单处理(Orders)、物流管理(Logistics)、营销活动(Promotions)、数据分析(Analytics)等核心能力。TikTok Shop 目前覆盖美国、英国、东南亚(印尼、泰国、越南、菲律宾、马来西亚、新加坡)等市场,是社交电商(Social Commerce)的代表性平台。

## 为什么选这个工具

### TikTok Shop API vs Shopee API vs Lazada API

| 维度 | TikTok Shop | Shopee | Lazada |
|------|-------------|--------|--------|
| **定位** | 短视频 + 直播电商 | 东南亚电商平台第一 | 阿里系东南亚电商 |
| **流量来源** | TikTok 短视频/直播流量 | 平台搜索 + 活动 | 平台搜索 + 广告 |
| **市场覆盖** | 美国、英国、东南亚 8 国 | 东南亚 7 国 + 台湾、巴西 | 东南亚 6 国 |
| **直播带货** | 原生支持(核心场景) | 支持(Shopee Live) | 支持(Lazada Live) |
| **佣金** | 2-8%(按类目,较低) | 5-10% | 3-5%(阿里补贴) |
| **物流** | TikTok Shop Shipping | Shopee 物流 | Lazada 物流 |
| **API 成熟度** | 中等(快速迭代中) | 高(成熟) | 高(成熟) |
| **入驻门槛** | 中等(需审核) | 低(快速入驻) | 中等(需企业资质) |
| **适用场景** | 内容创作者,网红带货 | 传统跨境电商 | 品牌商,阿里生态 |

## 快速开始

### 安装

TikTok Shop 无官方 Python SDK,推荐直接使用 HTTP 请求库:

```bash
# Python - 使用 requests 库
pip install requests

# Node.js - 使用 axios
npm install axios

# 验证安装
python -c "import requests; print('Requests installed')"
```

### 认证配置(详细步骤)

TikTok Shop API 使用 OAuth 2.0 认证,支持授权码模式(适合多商家 SaaS)和 Password 模式(适合单店铺自用)。

#### 方式一:授权码模式(推荐用于 SaaS 工具)

**步骤 1:注册 TikTok Shop 卖家账号**

1. 访问对应市场的 Seller Center:
   - 美国:https://seller-us.tiktok.com
   - 英国:https://seller-uk.tiktok.com
   - 东南亚:https://seller.tiktokglobalshop.com
2. 使用 TikTok 账号登录或注册
3. 完成店铺入驻审核(提供营业执照、身份证等资质)

**步骤 2:注册开发者账号**

1. 访问 TikTok Shop 开发者平台:https://partner.tiktokshop.com
2. 使用 TikTok 账号登录
3. 进入 **Developer Portal** → **Apps** → **Create New App**
4. 填写应用信息:
   - App Name: `My TikTok Shop Integration`
   - App Type: 选择 **Authorized**(授权型应用)
   - Redirect URI: `https://yourapp.com/oauth/callback`
     (开发环境可用 `http://localhost:8080/callback`)
5. 提交审核(通常 1-3 个工作日)

**步骤 3:审核通过后获取 App Key 和 App Secret**

1. 审核通过后,在 **Apps** 页面找到你的应用
2. 点击 **View** → 记录 **App Key** 和 **App Secret**
3. 配置 **Scopes(权限)**:
   - `product.write`:创建和编辑商品
   - `order.read`:读取订单信息
   - `fulfillment.write`:更新订单物流状态
   - `shop.read`:读取店铺信息
   - `product.read`:读取商品信息
   - `authorization.write`:长期授权(Refresh Token)

**步骤 4:获取 Authorization Code**

在浏览器中访问以下 URL(替换 `{APP_KEY}` 和 `{REDIRECT_URI}`):

```
https://services.tiktokshop.com/open/authorize?
  app_key={APP_KEY}&
  state=random_state_123&
  redirect_uri={REDIRECT_URI}
```

示例:
```
https://services.tiktokshop.com/open/authorize?app_key=abc123def456&state=xyz&redirect_uri=http://localhost:8080/callback
```

用户登录并授权后,浏览器跳转到:
```
http://localhost:8080/callback?code={AUTHORIZATION_CODE}&state=random_state_123
```

复制 `code` 参数的值。

**步骤 5:交换 Access Token**

```python
import requests
import json

app_key = "abc123def456"
app_secret = "your_app_secret"
authorization_code = "code_from_redirect"

# 生成签名(TikTok Shop 需要签名)
import hmac
import hashlib
import time

timestamp = str(int(time.time()))
sign_str = f"{app_key}{timestamp}"
sign = hmac.new(app_secret.encode(), sign_str.encode(), hashlib.sha256).hexdigest()

# 请求 Access Token
url = "https://auth.tiktok-shops.com/api/v2/token/get"
payload = {
    "app_key": app_key,
    "app_secret": app_secret,
    "auth_code": authorization_code,
    "grant_type": "authorized_code"
}

headers = {
    "Content-Type": "application/json",
    "x-tts-access-token": ""  # 首次获取 Token 无需传递
}

response = requests.post(url, json=payload, headers=headers)
data = response.json()

access_token = data['data']['access_token']
refresh_token = data['data']['refresh_token']
expires_in = data['data']['access_token_expire_in']  # 有效期(秒)

print(f"Access Token: {access_token}")
print(f"Refresh Token: {refresh_token}")
```

**步骤 6:刷新 Access Token**

Access Token 有效期通常为 24 小时,需使用 Refresh Token 定期刷新:

```python
# 刷新 Access Token
refresh_url = "https://auth.tiktok-shops.com/api/v2/token/refresh"
refresh_payload = {
    "app_key": app_key,
    "app_secret": app_secret,
    "refresh_token": refresh_token,
    "grant_type": "refresh_token"
}

response = requests.post(refresh_url, json=refresh_payload)
new_data = response.json()
new_access_token = new_data['data']['access_token']
```

#### 方式二:店铺授权 Token(适合单店铺自用)

1. 登录 TikTok Shop Seller Center
2. 进入 **Settings → Open Platform**
3. 点击 **Generate Token** → 直接获得 Access Token(无需 OAuth 流程)
4. **注意**:该 Token 通常为临时 Token,不推荐生产使用

#### 常见认证错误及解决方案

1. **错误:`invalid_app_key`**
   - 原因:App Key 错误或应用未审核通过
   - 解决:检查 App Key,确认应用已审核通过

2. **错误:`invalid_auth_code`**
   - 原因:Authorization Code 已过期(10 分钟有效期)或已使用
   - 解决:重新生成 Authorization Code

3. **错误:`invalid_signature`**
   - 原因:签名计算错误
   - 解决:检查签名算法(HMAC-SHA256)

4. **错误:`access_token_expired`**
   - 原因:Access Token 已过期
   - 解决:使用 Refresh Token 获取新 Access Token

5. **错误:`insufficient_permissions`**
   - 原因:App 未申请足够的 Scope
   - 解决:在开发者平台中申请所需权限

### 环境变量配置

```bash
# TikTok Shop 应用凭证
export TIKTOK_APP_KEY="abc123def456"
export TIKTOK_APP_SECRET="your_app_secret"
export TIKTOK_ACCESS_TOKEN="your_access_token"
export TIKTOK_REFRESH_TOKEN="your_refresh_token"

# API 基础 URL(根据市场不同)
# 全球通用(推荐)
export TIKTOK_API_BASE="https://open-api.tiktokglobalshop.com"

# 其他区域
# 美国: https://open-api.tiktokshop.com
# 英国: https://open-api-uk.tiktokshop.com
# 东南亚: https://open-api.tiktokglobalshop.com

# Shop ID(店铺 ID,授权后获取)
export TIKTOK_SHOP_ID="your_shop_id"
```

### 验证

```python
import requests
import hmac
import hashlib
import time
import json

app_key = "abc123def456"
app_secret = "your_app_secret"
access_token = "your_access_token"

# 生成签名(TikTok Shop 所有 API 都需要签名)
def generate_sign(app_key, app_secret, timestamp, access_token):
    sign_str = f"{app_key}{timestamp}"
    sign = hmac.new(app_secret.encode(), sign_str.encode(), hashlib.sha256).hexdigest()
    return sign

# 测试 API:获取店铺信息
timestamp = str(int(time.time()))
sign = generate_sign(app_key, app_secret, timestamp, access_token)

url = "https://open-api.tiktokglobalshop.com/api/shop/get_authorized_shop"
headers = {
    "Content-Type": "application/json",
    "x-tts-access-token": access_token
}

params = {
    "app_key": app_key,
    "timestamp": timestamp,
    "sign": sign
}

response = requests.get(url, headers=headers, params=params)
data = response.json()

if data['code'] == 0:
    print("API 连接成功!")
    print(f"店铺名称: {data['data']['shop_name']}")
    print(f"Shop ID: {data['data']['shop_id']}")
else:
    print(f"错误: {data['message']}")
```

## 核心能力

### Product(商品)管理

- **创建商品**:上传商品信息(标题、图片、价格、库存、SKU)
- **批量操作**:批量上传/更新商品(最多 100 件/次)
- **商品状态**:Draft(草稿)、Pending Review(审核中)、Approved(已上架)、Rejected(已拒绝)、Deleted(已删除)
- **类目管理**:查询类目树,获取类目属性要求
- **品牌管理**:关联品牌(需品牌授权)
- **变体(SKU)**:支持多规格商品(如颜色、尺寸)
- **商品图片**:主图 + 8 张附图(最大 5 MB/张)

### Order(订单)管理

- **订单查询**:按时间范围、状态查询订单
- **订单详情**:买家信息、配送地址、商品明细、价格
- **订单状态**:Unpaid(待支付)、Awaiting Shipment(待发货)、Awaiting Collection(待揽收)、In Transit(运输中)、Delivered(已送达)、Completed(已完成)、Cancelled(已取消)
- **发货确认**:上传追踪号和物流商
- **订单取消**:卖家取消订单(需符合平台规则)

### Logistics(物流)管理

- **物流商查询**:查询平台支持的物流商
- **运单号上传**:发货后上传追踪号
- **物流追踪**:查询包裹物流状态
- **TikTok Shop Shipping**:使用平台物流服务(部分市场支持)
- **退货管理**:处理买家退货请求

### Promotion(营销)管理

- **优惠券**:创建店铺优惠券或商品优惠券
- **促销活动**:参与平台大促(如黑五、双 11)
- **限时折扣**:设置商品限时折扣
- **捆绑销售**:组合销售多个商品

### Fulfillment(履约)管理

- **发货时效**:查询各市场的发货时效要求
- **履约率监控**:查询店铺履约指标(发货及时率、妥投率)
- **异常订单处理**:处理延迟发货、物流异常等

### Finance(财务)管理

- **结算周期**:根据市场不同(美国 14 天,东南亚 7-14 天)
- **费用明细**:平台佣金、支付手续费、物流费、促销补贴
- **提现**:绑定银行账户并提现

### Analytics(数据分析)

- **销售数据**:GMV(成交额)、订单量、客单价
- **流量数据**:曝光量、点击量、转化率
- **商品表现**:各商品的销售排名
- **直播数据**:直播观看人数、直播 GMV(需单独申请权限)

## 使用示例(行业场景)

### 场景 1:电商运营 - 批量上传商品

```python
import requests
import hmac
import hashlib
import time
import json

def tiktok_api_call(endpoint, method="POST", data=None):
    """封装 TikTok Shop API 调用(含签名)"""
    app_key = "abc123def456"
    app_secret = "your_app_secret"
    access_token = "your_access_token"
    base_url = "https://open-api.tiktokglobalshop.com"
    
    timestamp = str(int(time.time()))
    sign_str = f"{app_key}{timestamp}"
    sign = hmac.new(app_secret.encode(), sign_str.encode(), hashlib.sha256).hexdigest()
    
    url = f"{base_url}{endpoint}"
    headers = {
        "Content-Type": "application/json",
        "x-tts-access-token": access_token
    }
    
    params = {
        "app_key": app_key,
        "timestamp": timestamp,
        "sign": sign
    }
    
    if method == "POST":
        response = requests.post(url, headers=headers, params=params, json=data)
    else:
        response = requests.get(url, headers=headers, params=params)
    
    return response.json()

# 创建商品
product_data = {
    "product_name": "春季新款 T 恤",
    "description": "100% 纯棉,舒适透气,多色可选",
    "category_id": "123456",  # 类目 ID(需先查询类目树)
    "brand_id": "brand_123",  # 品牌 ID(可选)
    "main_images": [
        {"url": "https://example.com/image1.jpg"}
    ],
    "video": {
        "url": "https://example.com/video.mp4"  # 商品视频(可选)
    },
    "skus": [
        {
            "sku_name": "白色-M",
            "price": {
                "amount": "2999",  # 单位:分
                "currency": "USD"
            },
            "stock_info": [
                {
                    "warehouse_id": "default",
                    "available_stock": 100
                }
            ],
            "seller_sku": "MY-SKU-001"
        },
        {
            "sku_name": "黑色-L",
            "price": {"amount": "2999", "currency": "USD"},
            "stock_info": [{"warehouse_id": "default", "available_stock": 150}],
            "seller_sku": "MY-SKU-002"
        }
    ],
    "package_dimensions": {
        "length": "20",  # cm
        "width": "15",
        "height": "5"
    },
    "package_weight": {
        "value": "200",  # g
        "unit": "GRAM"
    }
}

response = tiktok_api_call("/api/products/create", method="POST", data=product_data)
if response['code'] == 0:
    product_id = response['data']['product_id']
    print(f"商品创建成功! Product ID: {product_id}")
else:
    print(f"错误: {response['message']}")
```

### 场景 2:订单处理 - 查询待发货订单

```python
# 查询待发货订单
order_params = {
    "order_status": "AWAITING_SHIPMENT",  # 待发货
    "create_time_from": int(time.time()) - 7 * 24 * 3600,  # 最近 7 天
    "create_time_to": int(time.time()),
    "page_size": 50
}

response = tiktok_api_call("/api/orders/search", method="POST", data=order_params)
orders = response['data']['orders']

print(f"待发货订单数: {len(orders)}")
for order in orders:
    print(f"订单号: {order['order_id']}, 金额: {order['payment_info']['total_amount'] / 100} {order['payment_info']['currency']}")
```

### 场景 3:履约 - 确认发货并上传追踪号

```python
# 上传物流信息
shipping_data = {
    "order_id": "123456789",
    "tracking_number": "TRACK123456789",
    "shipping_provider_id": "10001",  # 物流商 ID(需先查询支持的物流商)
    "package_list": [
        {
            "package_id": "PKG001",
            "items": [
                {"product_id": "7234567890123456", "sku_id": "sku_001", "quantity": 1}
            ]
        }
    ]
}

response = tiktok_api_call("/api/fulfillment/ship", method="POST", data=shipping_data)
if response['code'] == 0:
    print("发货成功!")
else:
    print(f"错误: {response['message']}")
```

### 场景 4:库存管理 - 批量更新库存

```python
# 批量更新库存
inventory_data = {
    "skus": [
        {
            "sku_id": "sku_001",
            "stock_info": [
                {
                    "warehouse_id": "default",
                    "available_stock": 50  # 更新为 50
                }
            ]
        },
        {
            "sku_id": "sku_002",
            "stock_info": [
                {
                    "warehouse_id": "default",
                    "available_stock": 80
                }
            ]
        }
    ]
}

response = tiktok_api_call("/api/products/stock/update", method="POST", data=inventory_data)
print("库存更新完成")
```

### 场景 5:营销 - 创建店铺优惠券

```python
# 创建优惠券
coupon_data = {
    "promotion_name": "春季促销 - 满 $50 减 $10",
    "promotion_type": "SHOP_COUPON",  # 店铺券
    "discount_type": "FIXED_AMOUNT",  # 固定金额折扣
    "discount_value": {
        "amount": "1000",  # $10(单位:分)
        "currency": "USD"
    },
    "minimum_order_value": {
        "amount": "5000",  # 满 $50 可用
        "currency": "USD"
    },
    "start_time": int(time.time()),
    "end_time": int(time.time()) + 30 * 24 * 3600,  # 30 天后过期
    "quantity": 1000  # 发放 1000 张
}

response = tiktok_api_call("/api/promotions/create", method="POST", data=coupon_data)
print("优惠券创建成功")
```

### 场景 6:数据分析 - 获取店铺销售数据

```python
# 获取销售数据(需申请 Analytics Scope)
analytics_params = {
    "start_date": "2026-04-01",
    "end_date": "2026-04-08",
    "metrics": ["gmv", "order_count", "avg_order_value"]
}

response = tiktok_api_call("/api/analytics/sales", method="POST", data=analytics_params)
analytics = response['data']
print(f"GMV: ${analytics['gmv'] / 100}")
print(f"订单数: {analytics['order_count']}")
print(f"客单价: ${analytics['avg_order_value'] / 100}")
```

### 场景 7:直播带货 - 关联商品到直播间

```python
# 将商品添加到直播间(需 Live Streaming Scope)
live_product_data = {
    "live_stream_id": "live_123456",
    "product_ids": ["7234567890123456", "7234567890123457"]
}

response = tiktok_api_call("/api/live/products/add", method="POST", data=live_product_data)
print("商品已添加到直播间")
```

### 场景 8:自动化 - Webhook 接收订单创建通知

**在 Seller Center 配置 Webhook(Settings → Webhooks)**

Webhook Payload 示例:
```json
{
  "event": "ORDER_CREATED",
  "timestamp": 1712563200,
  "data": {
    "order_id": "123456789",
    "shop_id": "your_shop_id",
    "order_status": "UNPAID",
    "total_amount": 5999,
    "currency": "USD"
  }
}
```

你的服务器接收 Webhook:
```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/tiktok-webhook', methods=['POST'])
def tiktok_webhook():
    payload = request.json
    
    if payload['event'] == 'ORDER_CREATED':
        order_id = payload['data']['order_id']
        print(f"新订单: {order_id}")
        
        # 触发后续动作
        # 1. 推送到 ERP 系统
        # 2. 发送通知到 Slack
        # 3. 自动打印发货单
    
    return "OK", 200
```

## 适用场景

### 典型工作流

1. **网红带货工作流(Creator Economy)**
   - 网红在 TikTok 创作短视频,植入商品链接
   - 用户点击视频中的商品链接 → 跳转到 TikTok Shop 商品页
   - 用户下单 → 订单推送到卖家 ERP
   - 卖家发货 → 上传追踪号
   - 用户收货 → 完成交易
   - 平台结算(扣除佣金后打款给卖家)

2. **直播带货工作流(Live Shopping)**
   - 主播开启 TikTok Live
   - 将商品添加到直播间购物车
   - 主播讲解商品 → 观众下单(边看边买)
   - 直播结束后,批量查询订单
   - 批量发货并上传追踪号
   - 直播数据分析(观看人数、GMV、转化率)

3. **平台大促工作流(Campaign)**
   - 报名参加平台大促(如黑五、双 11)
   - 创建促销活动(限时折扣、优惠券)
   - 提前备货(增加 FBA 库存或自有仓库库存)
   - 大促期间监控库存,及时补货
   - 大促结束后,批量导出订单并发货
   - 分析大促 ROI(投入 vs 销售额)

4. **跨境电商 ERP 集成工作流**
   - 卖家在多个平台销售(TikTok Shop + Amazon + Shopify)
   - 通过 API 同步订单到 ERP
   - ERP 统一管理库存(多平台共享库存)
   - 订单分配到最优仓库发货
   - 自动上传追踪号到各平台
   - 财务自动对账(各平台费用汇总)

## 与其他工具的区分

### TikTok Shop vs Instagram Shopping

| 维度 | TikTok Shop | Instagram Shopping |
|------|-------------|-------------------|
| **平台属性** | 独立电商平台(闭环交易) | 导流到外部网站(非闭环) |
| **结算** | TikTok Shop 平台结算 | 第三方支付(Shopify 等) |
| **物流** | TikTok Shop Shipping | 商家自行处理 |
| **佣金** | 2-8% | 无(但需 Shopify 等建站费用) |
| **直播购物** | 原生支持 | 需第三方工具 |
| **适用场景** | 完整电商解决方案 | 品牌引流到独立站 |

### TikTok Shop vs Douyin(抖音)电商

| 维度 | TikTok Shop | 抖音电商 |
|------|-------------|---------|
| **市场** | 全球(美国、东南亚等) | 中国大陆 |
| **API 开放度** | 开放(支持 ISV) | 开放(抖音开放平台) |
| **商家类型** | 跨境卖家为主 | 国内品牌 + 达人 |
| **物流模式** | 跨境物流 | 国内物流 |
| **适用场景** | 出海卖家 | 国内电商 |

## 限制和注意事项

### API 调用限额

| API 类型 | 速率限制 | 备注 |
|---------|---------|------|
| **Product API** | 10 次/分钟 | 创建和更新商品 |
| **Order API** | 60 次/分钟 | 查询订单 |
| **Fulfillment API** | 30 次/分钟 | 发货操作 |
| **Analytics API** | 5 次/分钟 | 数据分析 |

**限流机制**:超限返回 `rate_limit_exceeded` 错误,需实现退避重试。

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **invalid_access_token** | Access Token 无效或过期 | 使用 Refresh Token 刷新 |
| **invalid_signature** | 签名错误 | 检查签名算法(HMAC-SHA256) |
| **insufficient_permissions** | Scope 不足 | 申请所需权限 |
| **product_under_review** | 商品审核中,无法编辑 | 等待审核完成 |
| **order_already_shipped** | 订单已发货,无法重复操作 | 检查订单状态 |
| **invalid_category_id** | 类目 ID 错误 | 查询类目树获取正确 ID |
| **stock_insufficient** | 库存不足 | 补充库存后再上架 |

### 注意事项

- **入驻审核**:需提供营业执照、品牌授权等资质,审核周期 3-7 天
- **类目限制**:部分类目(如食品、化妆品)需额外资质
- **商品审核**:新上架商品需人工审核(1-3 天),审核期间无法销售
- **佣金差异**:不同类目佣金不同(服装 5%,电子产品 8%)
- **发货时效**:各市场不同(美国 3 天,东南亚 2 天),超时影响店铺评分
- **退货率**:高退货率(> 10%)会影响店铺权重
- **Listing 优化**:标题、主图、视频质量直接影响转化率
- **直播权限**:需达到一定粉丝数或销售额才能开通直播
- **跨境物流**:需对接国际物流商(DHL、FedEx、UPS 等)

## 定价(详细分级)

### TikTok Shop 平台费用

| 费用项 | 价格 | 备注 |
|--------|------|------|
| **平台佣金** | 2-8%(按类目) | 服装 5%,电子产品 8%,美妆 6% |
| **支付手续费** | 2.9% | Stripe/Adyen 等支付网关费用 |
| **提现手续费** | $0(部分市场免费) | 东南亚部分市场收取 1% |
| **物流费** | 按重量和距离 | TikTok Shop Shipping 或第三方物流 |
| **广告费(TikTok Ads)** | CPC/CPM 竞价 | 可选,推广商品或直播 |

### API 费用

- **API 使用**:完全免费(无 API 调用费用)

### 入驻费用

- **店铺注册费**:免费(部分市场)
- **保证金**:$0-$500(根据市场和类目不同)

## 参考链接

### 官方文档

- [TikTok Shop 开发者文档](https://partner.tiktokshop.com/docv2)
- [API 参考](https://partner.tiktokshop.com/docv2/page/api-overview)
- [认证指南](https://partner.tiktokshop.com/docv2/page/authorization-guide)
- [Seller Center](https://seller.tiktokshop.com/)
- [开发者平台](https://partner.tiktokshop.com/)

### 进阶学习

- [TikTok Shop Academy(卖家培训)](https://seller.tiktokshop.com/university)
- [API 最佳实践](https://partner.tiktokshop.com/docv2/page/best-practices)
- [Webhook 指南](https://partner.tiktokshop.com/docv2/page/webhook)
- [类目规范](https://seller-us.tiktok.com/university/category)

### 社区资源

- [TikTok Shop Seller Community](https://seller-community.tiktokshop.com/)
- [Facebook TikTok Shop 卖家交流群](https://www.facebook.com/groups/tiktokshopsellers)
- [Reddit r/TikTokShop](https://www.reddit.com/r/TikTokShop/)
- [Discord TikTok Shop Sellers](https://discord.gg/tiktokshop)

### SDK 和工具

- [Python Requests(推荐直接用)](https://docs.python-requests.org/)
- [Node.js Axios](https://axios-http.com/)
- [TikTok Shop ERP(第三方)](https://www.ginee.com/tiktok-shop/)
- [TikTok Shop Analytics(第三方)](https://www.tikbuddy.com/)
- [Postman Collection(社区维护)](https://github.com/tiktok-shop-api/postman-collection)
