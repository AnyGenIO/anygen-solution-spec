# TikTok Shop Open API

> TikTok小店官方API,管理产品、订单、物流

## 快速开始

### 安装
```bash
pip install tiktok-business-api-sdk-official
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `TIKTOK_APP_KEY` — 应用Key
  - `TIKTOK_APP_SECRET` — 应用Secret
  - `TIKTOK_ACCESS_TOKEN` — 访问令牌
- **获取步骤:**
  1. 访问 partner.tiktokshop.com 注册开发者
  2. 创建 App 获取 App Key/Secret
  3. 授权店铺获取 Access Token
  4. 配置回调URL
- **验证:**
  ```bash
  # 调用API测试
  curl -X GET "https://open-api.tiktokglobalshop.com/api/products/search" \
    -H "x-tts-access-token: YOUR_ACCESS_TOKEN" \
    -H "Content-Type: application/json"
  ```

## 核心能力

- 产品创建和管理
- 订单查询和发货
- 物流信息上传
- 店铺信息管理
- 促销活动设置
- 数据分析和报表

## 使用示例

### 初始化SDK
```python
import os
from tiktok_business_api import TikTokBusinessApi

# 配置凭证
app_key = os.getenv('TIKTOK_APP_KEY')
app_secret = os.getenv('TIKTOK_APP_SECRET')
access_token = os.getenv('TIKTOK_ACCESS_TOKEN')

api = TikTokBusinessApi(
    app_key=app_key,
    app_secret=app_secret,
    access_token=access_token
)
```

### 创建产品
```python
# 创建新产品
product_data = {
    "product_name": "春季新款T恤",
    "description": "100%纯棉,舒适透气",
    "category_id": "123456",
    "price": {
        "amount": "2999",
        "currency": "USD"
    },
    "stock_info": {
        "available_stock": 100
    },
    "images": [
        {"url": "https://example.com/image1.jpg"}
    ]
}

response = api.product.create(product_data)
print(f"Product ID: {response['product_id']}")
```

### 查询订单
```python
# 获取最近订单
from datetime import datetime, timedelta

start_time = int((datetime.now() - timedelta(days=7)).timestamp())
end_time = int(datetime.now().timestamp())

orders = api.order.search(
    create_time_from=start_time,
    create_time_to=end_time,
    page_size=20
)

for order in orders['order_list']:
    print(f"Order ID: {order['order_id']}, Status: {order['order_status']}")
```

### 更新库存
```python
# 更新产品库存
api.product.update_stock(
    product_id="7234567890123456",
    available_stock=50
)
```

### 确认发货
```python
# 上传物流信息
tracking_data = {
    "order_id": "123456789",
    "tracking_number": "TRACK123456789",
    "shipping_provider_id": "10001",
    "package_list": [
        {
            "package_id": "PKG001",
            "products": [
                {"product_id": "7234567890123456", "quantity": 1}
            ]
        }
    ]
}

api.order.ship(tracking_data)
```

### 获取店铺数据
```python
# 获取销售数据
analytics = api.analytics.get_sales_data(
    start_date="2024-01-01",
    end_date="2024-01-31"
)
print(f"Total Sales: {analytics['total_sales']}")
```

## 适用场景
- 在TikTok Shop上架产品
- 处理TikTok订单
- TikTok直播带货相关
- 批量管理产品和库存
- 自动化订单履约流程

## 限制和注意事项
- 日调用配额限制(按App等级)
- 部分市场未开放(需检查地区可用性)
- 需要完成店铺入驻审核
- 某些类目需要额外资质
- 直播带货需要达人/商家账号

## 定价
- API本身免费
- TikTok Shop费用:
  - 平台佣金: 2-8%(按类目)
  - 支付手续费: 2.9%
  - 部分市场无月租费

## 参考链接
- [官方文档](https://partner.tiktokshop.com/docv2)
- [API参考](https://partner.tiktokshop.com/docv2/page/api-overview)
- [开发者中心](https://partner.tiktokshop.com/)
- [卖家中心](https://seller.tiktokshop.com/)
