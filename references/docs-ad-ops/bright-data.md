# Bright Data

> 企业级代理网络 + Web 数据平台，爬取竞品广告素材和价格

## 快速开始

### 安装
```bash
# Python SDK
pip install brightdata

# Node.js SDK
npm install @brightdata/sdk

# 直接使用代理（无需 SDK）
# 通过代理服务器 + 认证即可使用
```

### 认证配置
- **方式:** API Token + Proxy Credentials
- **环境变量:**
  - `BRIGHT_DATA_API_TOKEN` — API 令牌
  - `BRIGHT_DATA_ZONE` — 代理区域（Proxy Zone）
  - `BRIGHT_DATA_USERNAME` — 代理用户名
  - `BRIGHT_DATA_PASSWORD` — 代理密码
- **获取步骤:**
  1. 注册 Bright Data: https://brightdata.com
  2. 创建 Proxy Zone: https://brightdata.com/cp/zones
  3. 获取 Zone 凭据（用户名/密码）
  4. 生成 API Token: https://brightdata.com/cp/api_token
- **配置示例:**
  ```python
  # 代理配置
  proxy = {
      'http': 'http://USERNAME:PASSWORD@brd.superproxy.io:22225',
      'https': 'http://USERNAME:PASSWORD@brd.superproxy.io:22225',
  }
  ```

## 核心能力
- 住宅代理网络（7200 万+ IP）
- 数据中心代理（高速稳定）
- 移动代理（4G/5G 网络）
- ISP 代理（住宅 IP + 数据中心速度）
- Web Scraper IDE（无代码爬虫）
- SERP API（搜索引擎结果页）
- 电商数据（Amazon/eBay/Walmart 产品价格）
- 社交媒体数据（TikTok/Instagram/Facebook 公开数据）
- 广告情报（竞品广告素材库）
- 浏览器扩展（手动采集）
- 数据集市（预采集数据）

## 使用示例

### Python 代理请求
```python
import requests

# 配置代理
proxy_host = "brd.superproxy.io"
proxy_port = 22225
proxy_user = "brd-customer-YOUR_CUSTOMER_ID-zone-YOUR_ZONE"
proxy_pass = "YOUR_PASSWORD"

proxies = {
    'http': f'http://{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}',
    'https': f'http://{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}',
}

# 发起请求
url = "https://example.com"
response = requests.get(url, proxies=proxies, verify=False)
print(response.text)
```

### 使用指定国家 IP
```python
# 在用户名中指定国家代码
proxy_user = "brd-customer-YOUR_CUSTOMER_ID-zone-YOUR_ZONE-country-us"

proxies = {
    'http': f'http://{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}',
    'https': f'http://{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}',
}

response = requests.get("https://httpbin.org/ip", proxies=proxies)
print(response.json())  # 显示美国 IP
```

### 爬取 Amazon 产品价格
```python
# 使用 SERP API
import requests

api_token = "YOUR_API_TOKEN"
url = "https://api.brightdata.com/datasets/v3/trigger"

params = {
    "dataset_id": "gd_l7q7dkf244hwjntr0",  # Amazon 产品数据集
    "endpoint": "discover_new",
    "format": "json",
    "uncompressed_webhook": True,
}

headers = {
    "Authorization": f"Bearer {api_token}",
    "Content-Type": "application/json",
}

data = [
    {"url": "https://www.amazon.com/dp/B08N5WRWNW", "country": "us"}
]

response = requests.post(url, headers=headers, params=params, json=data)
snapshot_id = response.json()['snapshot_id']
print(f"Snapshot ID: {snapshot_id}")
```

### 下载采集结果
```python
# 查询采集状态
status_url = f"https://api.brightdata.com/datasets/v3/progress/{snapshot_id}"
headers = {"Authorization": f"Bearer {api_token}"}

response = requests.get(status_url, headers=headers)
status = response.json()

# 下载结果
if status['status'] == 'ready':
    download_url = f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}"
    result = requests.get(download_url, headers=headers)
    print(result.json())
```

### 使用 Web Scraper IDE
```javascript
// 在 Bright Data 控制台创建采集器
// 示例：抓取竞品广告素材
{
  "id": "competitor_ads_scraper",
  "start_url": "https://example.com/ads",
  "steps": [
    {
      "action": "scrape",
      "selector": ".ad-container",
      "fields": {
        "title": ".ad-title",
        "image": ".ad-image@src",
        "description": ".ad-description",
        "cta": ".ad-cta"
      }
    }
  ]
}
```

### Selenium + 代理
```python
from selenium import webdriver
from selenium.webdriver.common.proxy import Proxy, ProxyType

# 配置代理
proxy = Proxy()
proxy.proxy_type = ProxyType.MANUAL
proxy.http_proxy = f"{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}"
proxy.ssl_proxy = f"{proxy_user}:{proxy_pass}@{proxy_host}:{proxy_port}"

capabilities = webdriver.DesiredCapabilities.CHROME
proxy.add_to_capabilities(capabilities)

driver = webdriver.Chrome(desired_capabilities=capabilities)
driver.get("https://example.com")
```

## 适用场景
- 竞品广告素材监控（Facebook/Google/TikTok 广告库）
- 电商价格监控（Amazon/eBay/Shopify 竞品定价）
- SEO 排名跟踪（Google SERP 结果）
- 社交媒体数据采集（TikTok/Instagram 公开数据）
- 市场研究和舆情分析
- 品牌保护（假冒商品检测）
- 地理定位测试（不同国家的网站内容）
- 大规模数据采集（不被封 IP）
- 广告验证（Ad Verification）

## 限制和注意事项
- 付费服务，按流量/请求计费
- 住宅代理成本较高（$8-15/GB）
- 部分网站仍可能检测到代理
- 需遵守目标网站的 robots.txt 和 ToS
- 高并发请求需要更大的并发数配额
- 代理响应速度取决于网络质量
- 某些国家/地区 IP 可用性有限
- API 调用有速率限制
- 数据集采集有延迟（分钟到小时级）
- 需合规使用（GDPR、CCPA 等）

## 定价
- 住宅代理: $8.40/GB 起（按流量）
- 数据中心代理: $0.60/GB 起
- 移动代理: $18/GB 起
- ISP 代理: $15/GB 起
- Web Scraper: 按请求计费（$0.001-0.01/请求）
- SERP API: $0.50/1000 请求
- 数据集: 按数据量计费（$0.001-0.1/记录）
- 最低充值: $500（企业版）
- 免费试用: 7 天（需联系销售）

## 参考链接
- [官方网站](https://brightdata.com)
- [文档中心](https://docs.brightdata.com)
- [API 参考](https://docs.brightdata.com/api-reference)
- [Web Scraper IDE](https://brightdata.com/products/web-scraper)
- [数据集市](https://brightdata.com/products/datasets)
- [定价](https://brightdata.com/pricing)
- [Python SDK](https://pypi.org/project/brightdata/)
