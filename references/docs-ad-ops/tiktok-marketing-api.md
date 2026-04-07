# TikTok Marketing API

> TikTok 官方营销 API，管理 TikTok 广告投放

## 快速开始

### 安装
```bash
# Python SDK (官方)
pip install tiktok-business-api-sdk

# Node.js SDK (官方)
npm install tiktok-business-api

# 直接调用 REST API
curl -X GET "https://business-api.tiktok.com/open_api/v1.3/advertiser/info/" \
  -H "Access-Token: YOUR_ACCESS_TOKEN"
```

### 认证配置
- **方式:** Access Token (OAuth2)
- **环境变量:**
  - `TIKTOK_ACCESS_TOKEN` — 访问令牌
  - `TIKTOK_APP_ID` — 应用 ID
  - `TIKTOK_SECRET` — 应用密钥
  - `TIKTOK_ADVERTISER_ID` — 广告主 ID
- **获取步骤:**
  1. 注册 TikTok for Business: https://business.tiktok.com
  2. 创建开发者应用: https://ads.tiktok.com/marketing_api/apps
  3. 申请 Marketing API 权限（需审核）
  4. 生成 Access Token（通过 OAuth2 授权流程）
  5. 获取 Advertiser ID（广告账户 ID）
- **OAuth2 授权流程:**
  ```bash
  # 1. 跳转授权页面
  https://business-api.tiktok.com/portal/auth?app_id=YOUR_APP_ID&redirect_uri=YOUR_REDIRECT_URI&state=RANDOM_STATE
  
  # 2. 用户授权后，使用 auth_code 换取 access_token
  curl -X POST "https://business-api.tiktok.com/open_api/v1.3/oauth2/access_token/" \
    -H "Content-Type: application/json" \
    -d '{
      "app_id": "YOUR_APP_ID",
      "secret": "YOUR_SECRET",
      "auth_code": "AUTH_CODE"
    }'
  ```

## 核心能力
- 广告系列（Campaign）管理
- 广告组（Ad Group）配置
- 广告创意（Ad）创建
- 受众定向（年龄、性别、兴趣、行为）
- 自定义受众（Custom Audiences）
- TikTok Pixel 事件跟踪
- Events API（服务器端转化跟踪）
- 视频/图片素材管理
- 广告性能报表
- 出价策略优化
- Spark Ads（使用自然视频做广告）
- TikTok Shop 集成
- Lead Generation（潜在客户开发）

## 使用示例

### Python SDK 查询广告账户
```python
import requests

ACCESS_TOKEN = "YOUR_ACCESS_TOKEN"
ADVERTISER_ID = "YOUR_ADVERTISER_ID"

url = "https://business-api.tiktok.com/open_api/v1.3/advertiser/info/"
headers = {
    "Access-Token": ACCESS_TOKEN
}
params = {
    "advertiser_ids": [ADVERTISER_ID]
}

response = requests.get(url, headers=headers, json=params)
print(response.json())
```

### 创建广告系列
```python
url = "https://business-api.tiktok.com/open_api/v1.3/campaign/create/"
headers = {
    "Access-Token": ACCESS_TOKEN,
    "Content-Type": "application/json"
}
data = {
    "advertiser_id": ADVERTISER_ID,
    "campaign_name": "My TikTok Campaign",
    "objective_type": "TRAFFIC",  # 流量目标
    "budget_mode": "BUDGET_MODE_DAY",
    "budget": 100.0,  # 日预算 $100
}

response = requests.post(url, headers=headers, json=data)
campaign_id = response.json()['data']['campaign_id']
print(f"Campaign ID: {campaign_id}")
```

### 创建广告组（Ad Group）
```python
url = "https://business-api.tiktok.com/open_api/v1.3/adgroup/create/"
data = {
    "advertiser_id": ADVERTISER_ID,
    "campaign_id": campaign_id,
    "adgroup_name": "My Ad Group",
    "placement_type": "PLACEMENT_TYPE_AUTOMATIC",  # 自动投放位置
    "placements": ["PLACEMENT_TIKTOK"],
    "location_ids": ["6252001"],  # 美国
    "age_groups": ["AGE_25_34", "AGE_35_44"],
    "gender": "GENDER_UNLIMITED",
    "budget_mode": "BUDGET_MODE_DAY",
    "budget": 50.0,
    "schedule_type": "SCHEDULE_START_END",
    "schedule_start_time": "2026-04-07 00:00:00",
    "schedule_end_time": "2026-05-07 23:59:59",
    "billing_event": "CPC",
    "bid_type": "BID_TYPE_NO_BID",  # 自动出价
    "optimization_goal": "CLICK",
}

response = requests.post(url, headers=headers, json=data)
adgroup_id = response.json()['data']['adgroup_id']
```

### 上传视频素材
```python
# 1. 获取上传授权
url = "https://business-api.tiktok.com/open_api/v1.3/file/video/ad/upload/"
data = {
    "advertiser_id": ADVERTISER_ID,
    "upload_type": "UPLOAD_BY_FILE",
    "video_file": open("video.mp4", "rb"),
}

response = requests.post(url, headers=headers, files=data)
video_id = response.json()['data']['video_id']
```

### 创建广告
```python
url = "https://business-api.tiktok.com/open_api/v1.3/ad/create/"
data = {
    "advertiser_id": ADVERTISER_ID,
    "adgroup_id": adgroup_id,
    "creatives": [
        {
            "ad_name": "My TikTok Ad",
            "ad_format": "SINGLE_VIDEO",
            "ad_text": "Check out our product!",
            "call_to_action": "LEARN_MORE",
            "landing_page_url": "https://example.com",
            "video_id": video_id,
        }
    ]
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

### 查询广告性能报表
```python
url = "https://business-api.tiktok.com/open_api/v1.3/report/integrated/get/"
data = {
    "advertiser_id": ADVERTISER_ID,
    "report_type": "BASIC",
    "data_level": "AUCTION_CAMPAIGN",
    "dimensions": ["campaign_id", "stat_time_day"],
    "metrics": [
        "spend", "impressions", "clicks", "ctr", "cpc",
        "conversions", "cost_per_conversion"
    ],
    "start_date": "2026-03-01",
    "end_date": "2026-03-31",
}

response = requests.get(url, headers=headers, json=data)
for row in response.json()['data']['list']:
    print(f"Campaign: {row['dimensions']['campaign_id']}, "
          f"Spend: ${row['metrics']['spend']}, "
          f"Clicks: {row['metrics']['clicks']}")
```

## 适用场景
- TikTok 短视频广告自动化
- 跨境电商产品推广
- 品牌曝光和流量获取
- 潜在客户开发（Lead Ads）
- 应用下载推广（App Install）
- TikTok Shop 商品推广
- 再营销（Retargeting）
- 多账户广告管理
- 广告创意 A/B 测试
- Spark Ads（使用自然内容做广告）

## 限制和注意事项
- 需要申请 Marketing API 权限（审核周期 3-5 个工作日）
- Access Token 有效期 365 天，需定期刷新
- API 调用有速率限制（QPS: 10-20，根据权限等级）
- 广告创意需符合 TikTok 广告政策
- 视频素材要求: 时长 5-60 秒，分辨率 ≥ 540x960
- 某些行业需额外审核（金融、医疗、政治）
- 最低日预算: $20（美国）
- 出价和预算单位为美元（非 cents）
- Spark Ads 需要视频作者授权
- 报表数据有延迟（通常 1-2 小时）

## 定价
- API 使用: 完全免费
- 广告支出: 按实际广告投放计费
- 最低日预算: $20（美国），其他国家不同
- 平均 CPC: $0.50-$1.00（美国）
- 平均 CPM: $1-$10
- 应用下载 CPA: $2-$10
- 无平台使用费，只收广告费

## 参考链接
- [官方文档](https://business-api.tiktok.com/portal/docs)
- [API 参考](https://business-api.tiktok.com/portal/docs?id=1738855176671234)
- [Python SDK](https://pypi.org/project/tiktok-business-api-sdk/)
- [开发者平台](https://ads.tiktok.com/marketing_api/apps)
- [广告政策](https://ads.tiktok.com/help/article?aid=9550)
- [Events API](https://business-api.tiktok.com/portal/docs?id=1741601162187777)
