<!-- doc-level: standard -->

# Square API

POS终端和支付网关一体化平台,支持线上线下融合场景和多币种支付,适合零售和餐饮业务。

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
- **环境变量:** `SQUARE_ACCESS_TOKEN`, `SQUARE_ENVIRONMENT`
- **获取步骤:**
  1. 访问 developer.squareup.com → Applications
  2. 创建应用 → 选择 Sandbox/Production
  3. 获取 Access Token
- **验证:** 使用SDK测试连接locations API

## 核心能力
- 在线和POS支付处理(信用卡/Apple Pay/Google Pay)
- 产品目录和库存管理(线上线下同步)
- 发票和订阅计费
- 客户管理和会员积分
- Webhook事件和App集成

## 使用示例

### 创建支付(Python)
```python
from square.client import Client
import uuid

client = Client(access_token='YOUR_TOKEN', environment='production')

result = client.payments.create_payment(
    body = {
        "source_id": "cnon:card-nonce-ok",
        "idempotency_key": str(uuid.uuid4()),
        "amount_money": {"amount": 2999, "currency": "USD"},
        "location_id": "YOUR_LOCATION_ID"
    }
)
```

### 创建产品
```python
result = client.catalog.upsert_catalog_object(
    body = {
        "idempotency_key": "key-123",
        "object": {
            "type": "ITEM",
            "item_data": {
                "name": "蓝牙耳机",
                "variations": [{
                    "type": "ITEM_VARIATION",
                    "item_variation_data": {
                        "price_money": {"amount": 2999, "currency": "USD"}
                    }
                }]
            }
        }
    }
)
```

### 创建发票
```python
result = client.invoices.create_invoice(
    body = {
        "invoice": {
            "location_id": "YOUR_LOCATION_ID",
            "primary_recipient": {"customer_id": "CUSTOMER_ID"},
            "payment_requests": [{"request_type": "BALANCE", "due_date": "2026-05-01"}]
        },
        "idempotency_key": str(uuid.uuid4())
    }
)
```

## 适用场景
- 线上线下一体化零售
- 跨境电商多币种支付(40+国家)
- 移动销售和餐饮POS

## 限制和注意事项
- 交易手续费: 线上2.6%+10¢, 线下2.75%, 跨境额外+1%
- API限流: 500次/10秒
- 高级功能需Square Plus($60/月)
- 主要覆盖美/加/英/澳/日

## 定价
- 免费注册
- 线上支付2.6%+10¢, 线下2.75%
- Square Online: Free/$29/$79月
- Square Plus: $60/月

## 参考链接
- [官方文档](https://developer.squareup.com/docs)
- [API参考](https://developer.squareup.com/reference/square)
