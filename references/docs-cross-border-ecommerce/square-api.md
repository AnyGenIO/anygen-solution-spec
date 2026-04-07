# Square API

> POS(线下销售终端) + 支付网关 + 在线店铺一体化,全球支持,适合线上线下融合场景

## 快速开始

### 安装
```bash
# Python SDK
pip install squareup

# Node.js SDK
npm install square
```

### 认证配置
- **方式:** oauth / api_key
- **环境变量:**
  - `SQUARE_ACCESS_TOKEN` — 生产环境 Access Token
  - `SQUARE_ENVIRONMENT` — production 或 sandbox
- **获取步骤:**
  1. 访问 developer.squareup.com → Applications
  2. 创建应用
  3. 选择 Sandbox/Production → 获取 Access Token
  4. 配置 OAuth(如需接入其他商家账户)
  5. 设置Webhook URL(可选)
- **验证:**
  ```bash
  # 使用Python测试连接
  python -c "from square.client import Client; client = Client(access_token='YOUR_TOKEN', environment='sandbox'); print(client.locations.list_locations())"
  ```

## 核心能力

### 支付处理
- 在线支付(信用卡/借记卡/Apple Pay/Google Pay)
- POS支付(线下刷卡/扫码/NFC)
- 发票和账单(Invoice)
- 订阅计费(Subscriptions)
- 分期付款(Buy Now Pay Later)

### 电商店铺(Square Online)
- 产品目录管理
- 在线店铺搭建(类Shopify Lite)
- 库存同步(线上线下统一库存)
- 订单管理

### POS硬件
- POS终端管理(Square Register/Terminal/Stand)
- 读卡器集成
- 收据打印

### 客户管理
- 客户档案(Customer Directory)
- 会员卡和积分(Loyalty)
- 营销活动(邮件/短信)

### 数据和报表
- 销售报表(按产品/时间/地点)
- 财务对账
- 员工业绩追踪

### 集成和扩展
- Webhook事件(支付成功/退款/订单等)
- App Marketplace(1000+集成)
- 与Shopify/WooCommerce/BigCommerce集成

## 使用示例

### 初始化客户端(Python)
```python
from square.client import Client

# 初始化Square客户端
client = Client(
    access_token='YOUR_ACCESS_TOKEN',
    environment='production'  # 或 'sandbox' 用于测试
)
```

### 创建产品
```python
# 创建产品目录项
result = client.catalog.upsert_catalog_object(
    body = {
        "idempotency_key": "unique-key-123",
        "object": {
            "type": "ITEM",
            "id": "#item1",
            "item_data": {
                "name": "蓝牙耳机",
                "description": "高品质降噪耳机",
                "variations": [
                    {
                        "type": "ITEM_VARIATION",
                        "id": "#variation1",
                        "item_variation_data": {
                            "name": "标准版",
                            "pricing_type": "FIXED_PRICING",
                            "price_money": {
                                "amount": 2999,
                                "currency": "USD"
                            }
                        }
                    }
                ]
            }
        }
    }
)

print(f"Item ID: {result.body['catalog_object']['id']}")
```

### 创建支付
```python
import uuid

# 处理在线支付
payment_result = client.payments.create_payment(
    body = {
        "source_id": "cnon:card-nonce-ok",  # 从前端获取的卡token
        "idempotency_key": str(uuid.uuid4()),
        "amount_money": {
            "amount": 2999,
            "currency": "USD"
        },
        "autocomplete": True,
        "location_id": "YOUR_LOCATION_ID",
        "note": "订单 #12345 付款"
    }
)

print(f"Payment ID: {payment_result.body['payment']['id']}")
```

### 查询订单
```python
# 获取订单列表
orders_result = client.orders.search_orders(
    body = {
        "location_ids": ["YOUR_LOCATION_ID"],
        "query": {
            "filter": {
                "date_time_filter": {
                    "created_at": {
                        "start_at": "2024-01-01T00:00:00Z",
                        "end_at": "2024-01-31T23:59:59Z"
                    }
                }
            },
            "sort": {
                "sort_field": "CREATED_AT",
                "sort_order": "DESC"
            }
        },
        "limit": 50
    }
)

for order in orders_result.body.get('orders', []):
    print(f"Order ID: {order['id']}, Total: {order['total_money']['amount']/100}")
```

### 管理库存
```python
# 更新库存数量
inventory_result = client.inventory.batch_change_inventory(
    body = {
        "idempotency_key": str(uuid.uuid4()),
        "changes": [
            {
                "type": "PHYSICAL_COUNT",
                "physical_count": {
                    "catalog_object_id": "ITEM_VARIATION_ID",
                    "location_id": "YOUR_LOCATION_ID",
                    "quantity": "50",
                    "occurred_at": "2024-01-15T10:00:00Z"
                }
            }
        ]
    }
)
```

### 创建客户
```python
# 添加新客户
customer_result = client.customers.create_customer(
    body = {
        "given_name": "张",
        "family_name": "三",
        "email_address": "zhang@example.com",
        "phone_number": "+1-555-0123",
        "note": "VIP客户"
    }
)

print(f"Customer ID: {customer_result.body['customer']['id']}")
```

### 创建发票
```python
# 创建并发送发票
invoice_result = client.invoices.create_invoice(
    body = {
        "invoice": {
            "location_id": "YOUR_LOCATION_ID",
            "order_id": "ORDER_ID",
            "primary_recipient": {
                "customer_id": "CUSTOMER_ID"
            },
            "payment_requests": [
                {
                    "request_type": "BALANCE",
                    "due_date": "2024-02-01"
                }
            ],
            "delivery_method": "EMAIL"
        },
        "idempotency_key": str(uuid.uuid4())
    }
)

# 发布发票
client.invoices.publish_invoice(
    invoice_id=invoice_result.body['invoice']['id'],
    body={"version": invoice_result.body['invoice']['version']}
)
```

### 设置Webhook
```bash
# 在Square Dashboard配置Webhook,或通过API:
curl https://connect.squareup.com/v2/webhooks/subscriptions \
  -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "subscription": {
      "name": "Payment Notifications",
      "event_types": ["payment.created", "payment.updated"],
      "notification_url": "https://your-server.com/webhook"
    },
    "idempotency_key": "unique-key"
  }'
```

## 适用场景
- 线上线下一体化零售(实体店+电商)
- 跨境电商需要多币种支付(支持全球40+国家)
- 快速搭建在线店铺(无需Shopify,Square Online免费版)
- 移动销售场景(市集/展会/移动餐车)
- 订阅付费业务(SaaS/会员制)
- 餐饮行业(POS+外卖+预定)

## 限制和注意事项
- 交易手续费:
  - 线上: 2.6% + 10¢
  - 线下: 2.75%
  - 跨境: 额外 +1%
- 部分高级功能需Plus订阅($60/月)
  - 多地点库存管理
  - 高级报表
  - 员工权限管理
- API限流: 500次/10秒(默认)
- 主要覆盖美国/加拿大/英国/澳洲/日本,其他地区功能有限
- 电商功能不如Shopify丰富(主打POS场景)

## 定价
- 免费注册
- 交易费:
  - 线上支付: 2.6% + 10¢
  - 线下POS: 2.75%
  - 跨境交易: 额外 +1%
- Square Online:
  - Free: $0/月(基础店铺)
  - Professional: $29/月
  - Premium: $79/月
- Square Plus: $60/月(高级功能)

## 参考链接
- [官方文档](https://developer.squareup.com/docs)
- [API参考](https://developer.squareup.com/reference/square)
- [Python SDK](https://github.com/square/square-python-sdk)
- [Node.js SDK](https://github.com/square/square-nodejs-sdk)
- [开发者中心](https://developer.squareup.com/)
