# 速卖通 Open Platform

> 速卖通官方开放平台API

## 快速开始

### 安装
```bash
# 社区SDK(推荐)
pip install aliexpress-async-api

# 或直接使用HTTP API调用
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `AE_APP_KEY` — 应用Key
  - `AE_APP_SECRET` — 应用Secret
  - `AE_ACCESS_TOKEN` — 访问令牌
- **获取步骤:**
  1. 访问 open.aliexpress.com 注册开发者
  2. 创建应用获取 App Key/Secret
  3. 卖家授权获取 Access Token
  4. 配置API权限和回调URL
- **验证:**
  ```bash
  # 使用curl测试API连接
  curl -X GET "https://gw.api.alibaba.com/openapi/param2/1/aliexpress.open/api.getUser" \
    -H "Content-Type: application/json" \
    -d "app_key=YOUR_APP_KEY&access_token=YOUR_ACCESS_TOKEN"
  ```

## 核心能力

- 产品发布和编辑
- 订单管理
- 物流发货
- 店铺数据
- 类目和属性查询
- 评价管理

## 使用示例

### 使用社区SDK初始化
```python
from aliexpress_async_api import AliexpressApi

# 初始化API客户端
api = AliexpressApi(
    app_key='YOUR_APP_KEY',
    app_secret='YOUR_APP_SECRET',
    access_token='YOUR_ACCESS_TOKEN',
    language='en'
)
```

### 发布产品
```python
# 创建新产品
product_data = {
    'subject': '高品质蓝牙耳机',
    'category_id': 123456,
    'price': '29.99',
    'inventory': 100,
    'description': '<p>高音质,长续航,支持主动降噪</p>',
    'image_urls': [
        'https://example.com/image1.jpg',
        'https://example.com/image2.jpg'
    ],
    'package_length': 15,
    'package_width': 10,
    'package_height': 5,
    'package_weight': 0.2
}

response = api.post_product(product_data)
print(f"Product ID: {response['product_id']}")
```

### 查询订单
```python
# 获取订单列表
from datetime import datetime, timedelta

start_date = (datetime.now() - timedelta(days=7)).strftime('%Y-%m-%d %H:%M:%S')
end_date = datetime.now().strftime('%Y-%m-%d %H:%M:%S')

orders = api.get_orders(
    create_time_start=start_date,
    create_time_end=end_date,
    page_size=20
)

for order in orders['order_list']:
    print(f"Order ID: {order['order_id']}, Total: ${order['order_amount']}")
```

### 更新库存和价格
```python
# 批量更新产品库存
api.update_product_stock(
    product_id='1234567890',
    inventory=50
)

# 更新价格
api.update_product_price(
    product_id='1234567890',
    price='24.99'
)
```

### 处理订单发货
```python
# 标记订单已发货
tracking_info = {
    'order_id': '12345678901234',
    'logistics_no': 'TRACK123456789',
    'service_name': 'China Post'
}

api.mark_order_shipped(tracking_info)
```

### 直接HTTP API调用示例
```python
import requests
import hashlib
import time

def sign_request(params, secret):
    """生成API签名"""
    sorted_params = sorted(params.items())
    sign_string = secret + ''.join([f"{k}{v}" for k, v in sorted_params]) + secret
    return hashlib.md5(sign_string.encode()).hexdigest().upper()

# 调用API获取类目列表
params = {
    'app_key': 'YOUR_APP_KEY',
    'timestamp': str(int(time.time() * 1000)),
    'format': 'json',
    'v': '2.0',
    'method': 'aliexpress.category.get'
}

params['sign'] = sign_request(params, 'YOUR_APP_SECRET')

response = requests.get(
    'https://gw.api.alibaba.com/openapi/param2/1/aliexpress.open/api.category.get',
    params=params
)
print(response.json())
```

## 适用场景
- 在速卖通上架产品
- 管理速卖通订单
- 批量更新商品信息
- 自动化物流跟踪
- 多店铺统一管理

## 限制和注意事项
- 日调用配额限制(按应用等级)
- 需要完成卖家入驻认证
- 部分类目需要额外资质
- 标题限制128字符
- 图片需要符合平台规范(白底/尺寸要求)

## 定价
- API本身免费
- 速卖通费用:
  - 年费: $0-399(按类目)
  - 佣金: 5-8%(按类目)
  - 部分类目免佣金

## 参考链接
- [官方文档](https://openservice.aliexpress.com/doc/api.htm)
- [开发者中心](https://open.aliexpress.com/)
- [社区SDK](https://github.com/QiYuTechDev/aliexpress-async-api)
- [卖家中心](https://sell.aliexpress.com/)
