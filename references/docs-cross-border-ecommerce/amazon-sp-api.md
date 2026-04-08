<!-- doc-level: detailed -->

# Amazon SP-API

> 亚马逊卖家官方API的Python SDK,管理listing、订单、库存、广告

## 快速开始

### 安装
```bash
pip install python-amazon-sp-api
```

### 认证配置
- **方式:** oauth (LWA - Login with Amazon)
- **环境变量:**
  - `SP_API_REFRESH_TOKEN` — 卖家授权token
  - `LWA_APP_ID` — App Client ID
  - `LWA_CLIENT_SECRET` — App Client Secret
  - `SP_API_AWS_ACCESS_KEY` — AWS IAM key
  - `SP_API_AWS_SECRET_KEY` — AWS IAM secret
  - `SP_API_ROLE_ARN` — AWS Role ARN
- **获取步骤:**
  1. 访问 sellercentral.amazon.com → 开发者注册
  2. 创建 App,获取 LWA 凭证
  3. AWS 控制台创建 IAM 用户和 Role
  4. 用卖家账号授权 App 获取 Refresh Token
- **验证:**
  ```bash
  python -c "from sp_api.api import Orders; print('ok')"
  ```

## 核心能力

- Listing 创建和更新(标题/价格/图片/描述)
- 订单查询和发货确认
- 库存管理(FBA和FBM)
- 广告管理(SP/SB/SD广告)
- 财务报表和结算
- 产品评价和反馈查询
- 类目和关键词查询

## 使用示例

### 初始化客户端
```python
from sp_api.api import Orders, Products, Inventories
from sp_api.base import Marketplaces

# 配置凭证
credentials = {
    'refresh_token': 'YOUR_REFRESH_TOKEN',
    'lwa_app_id': 'YOUR_LWA_APP_ID',
    'lwa_client_secret': 'YOUR_LWA_CLIENT_SECRET',
    'aws_access_key': 'YOUR_AWS_ACCESS_KEY',
    'aws_secret_key': 'YOUR_AWS_SECRET_KEY',
    'role_arn': 'YOUR_ROLE_ARN'
}
```

### 查询订单
```python
# 获取最近7天的订单
orders_api = Orders(credentials=credentials, marketplace=Marketplaces.US)
response = orders_api.get_orders(CreatedAfter='2024-01-01T00:00:00Z')
print(response.payload)
```

### 更新产品价格
```python
from sp_api.api import Feeds
from sp_api.base import Feeds as FeedsBase

# 更新价格
feed_api = Feeds(credentials=credentials, marketplace=Marketplaces.US)
price_feed = {
    "sku": "YOUR-SKU-123",
    "price": "29.99",
    "currency": "USD"
}
# 提交价格更新feed
response = feed_api.submit_feed(feedType='POST_PRODUCT_PRICING_DATA', file=price_feed)
```

### 查询FBA库存
```python
inventory_api = Inventories(credentials=credentials, marketplace=Marketplaces.US)
response = inventory_api.get_inventory_summary_marketplace(
    granularityType='Marketplace',
    granularityId='ATVPDKIKX0DER',
    marketplaceIds=['ATVPDKIKX0DER']
)
print(response.payload)
```

### 创建广告活动
```python
from sp_api.api import CatalogItems

# 获取产品目录信息用于广告投放
catalog_api = CatalogItems(credentials=credentials, marketplace=Marketplaces.US)
response = catalog_api.get_catalog_item(asin='B08N5WRWNW')
print(response.payload)
```

### 确认订单发货
```python
# 确认发货并上传追踪号
orders_api.update_shipment_status(
    orderId='123-4567890-1234567',
    payload={
        'shipmentStatus': 'ReadyForPickup',
        'trackingNumber': 'TRACK123456789'
    }
)
```

## 适用场景
- 在亚马逊上架/改listing
- 查看和处理亚马逊订单
- 管理FBA库存和补货
- 创建和调整PPC广告
- 批量更新产品信息
- 自动化订单处理流程

## 限制和注意事项
- 不同API有不同限流(通常1-5次/秒)
- 需要卖家账号是专业卖家(Professional Seller)
- 某些操作需要品牌注册(Brand Registry)
- FBA操作有时效性要求
- 广告API需要单独申请权限

## 定价
- API本身免费
- 亚马逊卖家费用:
  - 专业卖家: $39.99/月 + 销售佣金(8-15%)
  - FBA费用: 按商品尺寸和重量计费
  - 广告费用: 按点击付费(CPC)

## 参考链接
- [官方文档](https://developer-docs.amazon.com/sp-api/)
- [Python SDK](https://github.com/saleweaver/python-amazon-sp-api)
- [API参考](https://developer-docs.amazon.com/sp-api/docs)
- [卖家中心](https://sellercentral.amazon.com/)
