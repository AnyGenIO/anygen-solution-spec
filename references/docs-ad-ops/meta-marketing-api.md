# Meta Marketing API

> Meta（Facebook/Instagram）官方营销 API，管理社交媒体广告

## 快速开始

### 安装
```bash
# Python SDK (官方)
pip install facebook-business

# Node.js SDK
npm install facebook-nodejs-business-sdk

# PHP SDK
composer require facebook/php-business-sdk

# 直接调用 REST API
curl -X GET "https://graph.facebook.com/v19.0/me/adaccounts" \
  -d "access_token=YOUR_ACCESS_TOKEN"
```

### 认证配置
- **方式:** Access Token (OAuth2)
- **环境变量:**
  - `FACEBOOK_ACCESS_TOKEN` — 访问令牌
  - `FACEBOOK_APP_ID` — 应用 ID
  - `FACEBOOK_APP_SECRET` — 应用密钥
  - `FACEBOOK_AD_ACCOUNT_ID` — 广告账户 ID
- **获取步骤:**
  1. 创建 Facebook 应用: https://developers.facebook.com/apps
  2. 添加 Marketing API 产品
  3. 生成访问令牌（需要 ads_management、ads_read 权限）
  4. 获取广告账户 ID: https://business.facebook.com/settings/ad-accounts
  5. 使用 Graph API Explorer 测试: https://developers.facebook.com/tools/explorer
- **配置示例 (Python):**
  ```python
  from facebook_business.api import FacebookAdsApi
  
  FacebookAdsApi.init(
      app_id='YOUR_APP_ID',
      app_secret='YOUR_APP_SECRET',
      access_token='YOUR_ACCESS_TOKEN'
  )
  ```

## 核心能力
- Facebook/Instagram 广告系列管理
- 广告组（Ad Sets）配置
- 广告创意创建和管理
- 受众定向（年龄、性别、兴趣、行为）
- 自定义受众（Custom Audiences）
- 类似受众（Lookalike Audiences）
- 像素（Pixel）事件跟踪
- 转化 API（Conversion API）集成
- 广告性能报表
- 出价和预算优化
- A/B 测试（Split Testing）
- 动态广告（Dynamic Ads）
- Catalog 管理（电商产品目录）

## 使用示例

### Python SDK 初始化
```python
from facebook_business.api import FacebookAdsApi
from facebook_business.adobjects.adaccount import AdAccount

# 初始化 API
FacebookAdsApi.init(access_token='YOUR_ACCESS_TOKEN')

# 获取广告账户
account = AdAccount('act_1234567890')
print(account.remote_read())
```

### 查询广告系列
```python
from facebook_business.adobjects.campaign import Campaign

account = AdAccount('act_1234567890')
campaigns = account.get_campaigns(
    fields=[
        Campaign.Field.name,
        Campaign.Field.status,
        Campaign.Field.objective,
        Campaign.Field.daily_budget,
    ]
)

for campaign in campaigns:
    print(f"Campaign: {campaign[Campaign.Field.name]}, "
          f"Status: {campaign[Campaign.Field.status]}")
```

### 创建广告系列
```python
from facebook_business.adobjects.campaign import Campaign

account = AdAccount('act_1234567890')

campaign = account.create_campaign(
    params={
        'name': 'My Campaign',
        'objective': 'OUTCOME_TRAFFIC',  # 流量目标
        'status': Campaign.Status.paused,
        'special_ad_categories': [],
    }
)
print(f"Created campaign ID: {campaign['id']}")
```

### 创建广告组（Ad Set）
```python
from facebook_business.adobjects.adset import AdSet

campaign = Campaign('campaign_id')

adset = campaign.create_ad_set(
    params={
        'name': 'My Ad Set',
        'optimization_goal': 'LINK_CLICKS',
        'billing_event': 'IMPRESSIONS',
        'bid_amount': 200,  # 出价 $2.00 (单位: cents)
        'daily_budget': 5000,  # 日预算 $50
        'campaign_id': 'campaign_id',
        'targeting': {
            'geo_locations': {'countries': ['US']},
            'age_min': 25,
            'age_max': 55,
            'genders': [1],  # 1=male, 2=female
        },
        'status': AdSet.Status.paused,
    }
)
```

### 创建广告创意和广告
```python
from facebook_business.adobjects.adcreative import AdCreative
from facebook_business.adobjects.ad import Ad

# 1. 创建广告创意
account = AdAccount('act_1234567890')

creative = account.create_ad_creative(
    params={
        'name': 'My Creative',
        'object_story_spec': {
            'page_id': 'YOUR_PAGE_ID',
            'link_data': {
                'message': 'Check out our new product!',
                'link': 'https://example.com',
                'image_hash': 'IMAGE_HASH',  # 需先上传图片
                'call_to_action': {
                    'type': 'LEARN_MORE',
                    'value': {'link': 'https://example.com'}
                }
            }
        }
    }
)

# 2. 创建广告
adset = AdSet('adset_id')
ad = adset.create_ad(
    params={
        'name': 'My Ad',
        'adset_id': 'adset_id',
        'creative': {'creative_id': creative['id']},
        'status': Ad.Status.paused,
    }
)
```

### 查询广告性能报表
```python
from facebook_business.adobjects.adsinsights import AdsInsights

account = AdAccount('act_1234567890')

insights = account.get_insights(
    fields=[
        AdsInsights.Field.campaign_name,
        AdsInsights.Field.impressions,
        AdsInsights.Field.clicks,
        AdsInsights.Field.spend,
        AdsInsights.Field.cpc,
        AdsInsights.Field.ctr,
        AdsInsights.Field.conversions,
    ],
    params={
        'time_range': {'since': '2026-03-01', 'until': '2026-03-31'},
        'level': 'campaign',
    }
)

for insight in insights:
    print(f"{insight[AdsInsights.Field.campaign_name]}: "
          f"Spend: ${insight[AdsInsights.Field.spend]}, "
          f"Clicks: {insight[AdsInsights.Field.clicks]}")
```

### 上传图片素材
```python
from facebook_business.adobjects.adimage import AdImage

account = AdAccount('act_1234567890')

image = account.create_ad_image(
    params={'filename': 'path/to/image.jpg'}
)
print(f"Image hash: {image[AdImage.Field.hash]}")
```

## 适用场景
- Facebook/Instagram 广告自动化投放
- 电商产品动态广告（Dynamic Product Ads）
- 多账户广告管理
- 受众测试和优化
- 广告创意 A/B 测试
- 实时广告性能监控
- 自动化预算分配
- 再营销（Retargeting）自动化
- Conversion API 集成（服务器端转化跟踪）

## 限制和注意事项
- 访问令牌需定期续期（用户令牌60天，系统用户令牌永久）
- 广告账户需完成企业验证（Business Verification）
- 某些行业需特殊审核（金融、医疗、政治）
- API 调用有速率限制（每小时 200 次 / 应用）
- 广告创意需符合 Meta 广告政策
- 敏感类别需声明（special_ad_categories）
- 出价和预算单位为 cents（100 cents = $1）
- 某些功能需要高级权限（Advanced Access）
- 图片/视频需先上传获取 hash/id

## 定价
- API 使用: 完全免费
- 广告支出: 按实际广告投放计费
- 最低日预算: $1
- Facebook 广告 CPC: $0.50-$2.00（美国平均）
- Instagram 广告 CPC: $0.70-$1.00
- CPM: $5-$12
- 无平台使用费，只收广告费

## 参考链接
- [官方文档](https://developers.facebook.com/docs/marketing-apis)
- [Python SDK](https://github.com/facebook/facebook-python-business-sdk)
- [API 参考](https://developers.facebook.com/docs/marketing-api/reference)
- [Graph API Explorer](https://developers.facebook.com/tools/explorer)
- [广告政策](https://www.facebook.com/policies/ads)
- [Conversion API](https://developers.facebook.com/docs/marketing-api/conversions-api)
