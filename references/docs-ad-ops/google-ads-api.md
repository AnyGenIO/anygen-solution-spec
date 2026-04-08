<!-- doc-level: detailed -->

# Google Ads API

> Google 官方广告 API，管理搜索/展示/视频广告

## 快速开始

### 安装
```bash
# Python SDK (推荐)
pip install google-ads

# Node.js
npm install google-ads-api

# PHP
composer require googleads/google-ads-php

# Java
# 添加到 pom.xml
<dependency>
  <groupId>com.google.api-ads</groupId>
  <artifactId>google-ads</artifactId>
  <version>31.0.0</version>
</dependency>
```

### 认证配置
- **方式:** OAuth2 + Developer Token
- **环境变量:**
  - `GOOGLE_ADS_DEVELOPER_TOKEN` — 开发者令牌
  - `GOOGLE_ADS_CLIENT_ID` — OAuth2 客户端 ID
  - `GOOGLE_ADS_CLIENT_SECRET` — OAuth2 客户端密钥
  - `GOOGLE_ADS_REFRESH_TOKEN` — OAuth2 刷新令牌
  - `GOOGLE_ADS_LOGIN_CUSTOMER_ID` — 管理账户 ID（可选）
- **获取步骤:**
  1. 访问 https://ads.google.com/aw/apicenter
  2. 申请 Developer Token（需审核，测试账号可立即使用）
  3. 创建 OAuth2 凭据: https://console.cloud.google.com/apis/credentials
  4. 启用 Google Ads API
  5. 使用 generate_user_credentials.py 生成 refresh token
- **配置文件 (google-ads.yaml):**
  ```yaml
  developer_token: YOUR_DEVELOPER_TOKEN
  client_id: YOUR_CLIENT_ID
  client_secret: YOUR_CLIENT_SECRET
  refresh_token: YOUR_REFRESH_TOKEN
  login_customer_id: 1234567890  # 可选
  ```

## 核心能力
- 搜索广告系列管理（Search Campaigns）
- 展示广告系列管理（Display Campaigns）
- 视频广告系列管理（Video/YouTube Ads）
- 广告组和关键词管理
- 广告创意创建和优化
- 出价策略设置
- 受众定向配置
- 转化跟踪和归因
- 报表和性能数据查询
- 预算管理
- 广告投放时段设置
- 地理位置定向

## 使用示例

### Python SDK 基础查询
```python
from google.ads.googleads.client import GoogleAdsClient

# 加载配置
client = GoogleAdsClient.load_from_storage("google-ads.yaml")

# 查询广告系列
query = """
    SELECT
      campaign.id,
      campaign.name,
      campaign.status,
      metrics.impressions,
      metrics.clicks,
      metrics.cost_micros
    FROM campaign
    WHERE segments.date DURING LAST_30_DAYS
"""

ga_service = client.get_service("GoogleAdsService")
response = ga_service.search(customer_id="1234567890", query=query)

for row in response:
    print(f"Campaign: {row.campaign.name}, Clicks: {row.metrics.clicks}")
```

### 创建搜索广告系列
```python
from google.ads.googleads.client import GoogleAdsClient

client = GoogleAdsClient.load_from_storage("google-ads.yaml")
campaign_service = client.get_service("CampaignService")
campaign_budget_service = client.get_service("CampaignBudgetService")

# 1. 创建预算
budget_operation = client.get_type("CampaignBudgetOperation")
budget = budget_operation.create
budget.name = "Monthly Budget"
budget.amount_micros = 500000000  # $500
budget.delivery_method = client.enums.BudgetDeliveryMethodEnum.STANDARD

budget_response = campaign_budget_service.mutate_campaign_budgets(
    customer_id="1234567890", operations=[budget_operation]
)

# 2. 创建广告系列
campaign_operation = client.get_type("CampaignOperation")
campaign = campaign_operation.create
campaign.name = "Search Campaign"
campaign.advertising_channel_type = client.enums.AdvertisingChannelTypeEnum.SEARCH
campaign.status = client.enums.CampaignStatusEnum.PAUSED
campaign.campaign_budget = budget_response.results[0].resource_name
campaign.network_settings.target_google_search = True

response = campaign_service.mutate_campaigns(
    customer_id="1234567890", operations=[campaign_operation]
)
print(f"Created campaign: {response.results[0].resource_name}")
```

### 创建关键词
```python
ad_group_criterion_service = client.get_service("AdGroupCriterionService")

criterion_operation = client.get_type("AdGroupCriterionOperation")
criterion = criterion_operation.create
criterion.ad_group = "customers/1234567890/adGroups/9876543210"
criterion.status = client.enums.AdGroupCriterionStatusEnum.ENABLED
criterion.keyword.text = "cloud storage"
criterion.keyword.match_type = client.enums.KeywordMatchTypeEnum.EXACT

response = ad_group_criterion_service.mutate_ad_group_criteria(
    customer_id="1234567890", operations=[criterion_operation]
)
```

### 查询性能报表
```python
query = """
    SELECT
      campaign.name,
      ad_group.name,
      metrics.impressions,
      metrics.clicks,
      metrics.ctr,
      metrics.average_cpc,
      metrics.cost_micros,
      metrics.conversions,
      metrics.cost_per_conversion
    FROM keyword_view
    WHERE segments.date DURING LAST_7_DAYS
    ORDER BY metrics.impressions DESC
    LIMIT 50
"""

ga_service = client.get_service("GoogleAdsService")
response = ga_service.search_stream(customer_id="1234567890", query=query)

for batch in response:
    for row in batch.results:
        print(f"{row.campaign.name} | {row.ad_group.name} | "
              f"Impressions: {row.metrics.impressions} | "
              f"CPC: ${row.metrics.average_cpc / 1_000_000:.2f}")
```

## 适用场景
- 自动化广告系列创建和管理
- 批量关键词和广告创意上传
- 实时出价调整和优化
- 跨账户广告性能监控
- 自定义报表生成
- A/B 测试自动化
- 预算分配优化
- 竞争对手关键词监控
- 广告投放自动化规则

## 限制和注意事项
- Developer Token 需要申请（测试账号可立即使用，生产需审核）
- 测试账号只能访问测试广告账户
- API 调用有速率限制（每个开发者令牌每分钟 15,000 次操作）
- 某些功能需要 Google Ads 管理员权限
- 成本以 micros 为单位（1,000,000 micros = $1）
- GAQL（Google Ads Query Language）学习曲线
- OAuth2 refresh token 需定期更新
- 部分高级功能需要标准访问权限（Standard Access）

## 定价
- API 使用: 完全免费
- 广告支出: 按实际广告投放计费（CPC/CPM/CPA）
- 无最低消费要求
- 搜索广告 CPC: $1-$2（美国平均）
- 展示广告 CPM: $0.50-$4
- 视频广告 CPV: $0.10-$0.30

## 参考链接
- [官方文档](https://developers.google.com/google-ads/api/docs/start)
- [Python SDK](https://github.com/googleads/google-ads-python)
- [GAQL 查询语言](https://developers.google.com/google-ads/api/docs/query/overview)
- [API 参考](https://developers.google.com/google-ads/api/reference/rpc/v17/overview)
- [MCP Server (第三方)](https://github.com/modelcontextprotocol/servers/tree/main/src/google-ads)
