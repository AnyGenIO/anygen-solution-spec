<!-- doc-level: detailed -->

# Google Ads API

> Google 官方广告 API,管理搜索/展示/视频广告,全球最大数字广告平台

Google Ads API 是 Google 官方提供的广告管理 API,用于编程方式管理 Google Ads 广告账户。支持搜索广告(Search)、展示广告(Display)、视频广告(YouTube)、购物广告(Shopping)、应用广告(App)等全系列广告产品。通过 API 可以自动化创建广告系列(Campaign)、管理关键词(Keywords)、调整出价(Bidding)、查询性能报表(Reports)、管理受众(Audiences)等。Google Ads API 使用 OAuth 2.0 认证和 gRPC/REST 协议,提供 Python、Java、PHP、Node.js、Ruby、.NET 等主流语言的官方 SDK。

## 为什么选这个工具

### Google Ads API vs Facebook Ads API vs Microsoft Ads API

| 维度 | Google Ads | Facebook Ads | Microsoft Ads |
|------|-----------|--------------|---------------|
| **平台定位** | 搜索 + 展示 + 视频全渠道 | 社交媒体广告(Facebook/Instagram) | 搜索广告(Bing) |
| **市场份额** | 搜索广告 90%+,展示广告 30% | 社交广告 20% | 搜索广告 3% |
| **广告类型** | Search/Display/Video/Shopping/App | Feed/Story/Video/Carousel | Search/Display |
| **受众定向** | 关键词 + 受众 + 再营销 | 兴趣 + 行为 + 自定义受众 | 关键词 + LinkedIn 数据 |
| **API 成熟度** | 极高(GAQL 查询语言) | 高(Graph API) | 高(类似 Google Ads) |
| **开发者门槛** | 中等(需申请 Developer Token) | 低(快速接入) | 低 |
| **限流** | 15,000 次/分钟 | 200 次/小时/用户 | 10,000 次/分钟 |
| **适用场景** | 搜索意图营销,品牌曝光 | 社交媒体营销,DTC 品牌 | B2B 营销,补充搜索流量 |

## 快速开始

### 安装

Google Ads API 提供多语言官方 SDK:

```bash
# Python SDK(推荐)
pip install google-ads

# Node.js
npm install google-ads-api

# Java (Maven pom.xml)
<dependency>
  <groupId>com.google.api-ads</groupId>
  <artifactId>google-ads</artifactId>
  <version>31.0.0</version>
</dependency>

# PHP
composer require googleads/google-ads-php

# Ruby
gem install google-ads-googleads

# .NET (NuGet)
Install-Package Google.Ads.GoogleAds

# 验证安装
python -c "from google.ads.googleads.client import GoogleAdsClient; print('SDK installed')"
```

### 认证配置(详细步骤)

Google Ads API 使用 OAuth 2.0 + Developer Token 双重认证。

#### 方式一:OAuth 2.0(推荐用于管理客户账户)

**步骤 1:创建 Google Cloud 项目**

1. 访问 Google Cloud Console:https://console.cloud.google.com
2. 点击右上角 **Select a project** → **New Project**
3. 输入项目名称:`My Google Ads Integration`
4. 点击 **Create**

**步骤 2:启用 Google Ads API**

1. 在左侧菜单中,选择 **APIs & Services** → **Library**
2. 搜索 `Google Ads API`
3. 点击 **Google Ads API** → **Enable**

**步骤 3:创建 OAuth 2.0 凭据**

1. 进入 **APIs & Services** → **Credentials**
2. 点击 **+ CREATE CREDENTIALS** → **OAuth client ID**
3. 如果首次创建,需先配置 **OAuth consent screen**:
   - User Type:选择 **External**(外部)
   - App name:`My Google Ads App`
   - User support email:你的邮箱
   - Developer contact information:你的邮箱
   - 点击 **Save and Continue** → 跳过 Scopes → 添加测试用户(你的 Google 账号)
4. 返回 **Credentials** 页面,再次点击 **CREATE CREDENTIALS** → **OAuth client ID**
5. Application type:选择 **Desktop app**(桌面应用)
6. Name:`Google Ads API Client`
7. 点击 **Create** → 下载 JSON 文件(包含 Client ID 和 Client Secret)

**步骤 4:申请 Developer Token**

1. 登录 Google Ads 账号:https://ads.google.com
2. 点击右上角 **Tools & Settings** → **Setup** → **API Center**
3. 如果没有 Developer Token,点击 **Request Developer Token**
4. 填写申请表单(说明 API 使用场景)
5. 提交后,测试账号立即获得 **Test Developer Token**(只能访问测试账户)
6. 生产环境需等待 Google 审核(通常 1-3 个工作日)

**步骤 5:生成 Refresh Token**

使用 Google Ads Python SDK 提供的工具生成 Refresh Token:

```bash
# 下载 generate_user_credentials.py
wget https://raw.githubusercontent.com/googleads/google-ads-python/main/examples/authentication/generate_user_credentials.py

# 运行(替换 CLIENT_ID 和 CLIENT_SECRET)
python generate_user_credentials.py \
  --client_id="123456789012-abcdefg.apps.googleusercontent.com" \
  --client_secret="YOUR_CLIENT_SECRET"

# 按提示操作:
# 1. 访问生成的 URL(在浏览器中打开)
# 2. 登录 Google 账号并授权
# 3. 复制 Authorization Code 粘贴到终端
# 4. 生成的 Refresh Token 会打印在终端(永久有效)
```

**步骤 6:获取 Customer ID**

Customer ID 是 Google Ads 账户 ID(10 位数字,无连字符):

1. 登录 Google Ads → 右上角可看到账户 ID(格式:`123-456-7890`)
2. 去掉连字符得到 Customer ID:`1234567890`

**步骤 7:配置 google-ads.yaml 文件**

```yaml
# google-ads.yaml
developer_token: YOUR_DEVELOPER_TOKEN
client_id: 123456789012-abcdefg.apps.googleusercontent.com
client_secret: YOUR_CLIENT_SECRET
refresh_token: YOUR_REFRESH_TOKEN
login_customer_id: 1234567890  # 管理账户 ID(可选)
use_proto_plus: True
```

#### 方式二:Service Account(适合服务器端自动化)

Service Account 不适用于 Google Ads API,必须使用 OAuth 2.0。

#### 常见认证错误及解决方案

1. **错误:`DEVELOPER_TOKEN_NOT_APPROVED`**
   - 原因:Developer Token 未审核通过
   - 解决:使用测试账户或等待审核通过(生产环境)

2. **错误:`CUSTOMER_NOT_FOUND`**
   - 原因:Customer ID 错误或账户不存在
   - 解决:检查 Customer ID(10 位数字,无连字符)

3. **错误:`AUTHENTICATION_ERROR`**
   - 原因:OAuth 认证失败(Refresh Token 无效)
   - 解决:重新生成 Refresh Token

4. **错误:`PERMISSION_DENIED`**
   - 原因:当前用户无权访问指定账户
   - 解决:确认 Google 账号有该广告账户的管理员权限

5. **错误:`QUOTA_EXCEEDED`**
   - 原因:API 调用超限(15,000 次/分钟)
   - 解决:降低请求频率或申请提升限额

### 环境变量配置

```bash
# Google Ads API 凭证
export GOOGLE_ADS_DEVELOPER_TOKEN="YOUR_DEVELOPER_TOKEN"
export GOOGLE_ADS_CLIENT_ID="123456789012-abcdefg.apps.googleusercontent.com"
export GOOGLE_ADS_CLIENT_SECRET="YOUR_CLIENT_SECRET"
export GOOGLE_ADS_REFRESH_TOKEN="YOUR_REFRESH_TOKEN"

# 客户账户 ID
export GOOGLE_ADS_CUSTOMER_ID="1234567890"  # 10 位数字,无连字符

# 管理账户 ID(可选,用于管理多个子账户)
export GOOGLE_ADS_LOGIN_CUSTOMER_ID="9876543210"

# API 版本(可选,默认使用最新版本)
export GOOGLE_ADS_API_VERSION="v17"
```

### 验证

```python
from google.ads.googleads.client import GoogleAdsClient
from google.ads.googleads.errors import GoogleAdsException

# 方式 1:从 YAML 文件加载配置
client = GoogleAdsClient.load_from_storage("google-ads.yaml")

# 方式 2:从环境变量加载
# client = GoogleAdsClient.load_from_env()

# 验证连接:查询账户信息
customer_id = "1234567890"
ga_service = client.get_service("GoogleAdsService")

query = """
    SELECT
      customer.id,
      customer.descriptive_name,
      customer.currency_code,
      customer.time_zone
    FROM customer
    LIMIT 1
"""

try:
    response = ga_service.search(customer_id=customer_id, query=query)
    for row in response:
        print(f"Account ID: {row.customer.id}")
        print(f"Account Name: {row.customer.descriptive_name}")
        print(f"Currency: {row.customer.currency_code}")
        print(f"Time Zone: {row.customer.time_zone}")
    print("API 连接成功!")
except GoogleAdsException as ex:
    print(f"错误: {ex}")
```

## 核心能力

### Campaign(广告系列)管理

- **广告系列类型**:
  - **Search**:搜索广告(关键词触发)
  - **Display**:展示广告(GDN - Google Display Network)
  - **Video**:视频广告(YouTube)
  - **Shopping**:购物广告(Google Shopping)
  - **App**:应用广告(Google Play/App Store)
  - **Performance Max**:智能广告系列(AI 自动优化全渠道)
- **广告系列状态**:Enabled(启用)、Paused(暂停)、Removed(已删除)
- **预算管理**:日预算(Daily Budget)、共享预算(Shared Budget)
- **出价策略**:手动 CPC、目标 CPA、目标 ROAS、最大化转化

### Ad Group(广告组)管理

- **广告组结构**:Campaign → Ad Group → Ads + Keywords
- **广告组出价**:默认出价(Default Bid)
- **设备定向**:Mobile、Desktop、Tablet

### Keywords(关键词)管理

- **匹配类型**:
  - **Broad Match**(广泛匹配):最大流量
  - **Phrase Match**(短语匹配):相关性中等
  - **Exact Match**(精确匹配):最精准
- **否定关键词**:排除不相关流量
- **质量得分**:1-10 分(影响广告排名和 CPC)
- **关键词出价**:单独为关键词设置出价

### Ads(广告创意)管理

- **搜索广告创意**:
  - **Responsive Search Ad(RSA)**:最多 15 个标题 + 4 个描述(AI 自动组合)
  - **Expanded Text Ad(ETA)**:已弃用,仅可编辑
- **展示广告创意**:
  - **Responsive Display Ad**:上传图片 + 徽标 + 文案,AI 自动生成
  - **Image Ad**:静态图片广告
- **视频广告创意**:
  - **TrueView In-Stream**:可跳过视频广告(YouTube)
  - **Bumper Ads**:6 秒不可跳过广告
- **广告附加信息(Extensions)**:Sitelink、Callout、Structured Snippet、Call、Location

### Audiences(受众)管理

- **再营销(Remarketing)**:访问过网站的用户
- **客户匹配(Customer Match)**:上传客户邮箱/电话号码
- **相似受众(Similar Audiences)**:基于现有受众扩展
- **兴趣受众(Affinity Audiences)**:基于兴趣爱好
- **意向受众(In-Market Audiences)**:有购买意向的用户

### Conversion Tracking(转化跟踪)

- **转化目标**:购买、注册、电话、下载 App
- **转化来源**:网站、电话、App、导入
- **转化归因**:Last Click、Data-Driven、Time Decay
- **转化价值**:为每次转化设置价值(用于 ROAS 优化)

### Reports(报表)管理

- **GAQL(Google Ads Query Language)**:类 SQL 的查询语言
- **报表维度**:Campaign、Ad Group、Keyword、Search Term、Geo、Device、Time
- **报表指标**:Impressions(曝光)、Clicks(点击)、CTR(点击率)、CPC(单次点击成本)、Conversions(转化)、Cost(花费)、ROAS(广告支出回报率)
- **日期范围**:Today、Yesterday、Last 7 Days、Last 30 Days、Custom Range

### Automation(自动化)

- **Smart Bidding**:AI 自动出价(Target CPA、Target ROAS、Maximize Conversions)
- **Automated Rules**:自定义规则(如 CPC > $5 时暂停关键词)
- **Scripts**:JavaScript 脚本自动化(类似 Excel VBA)
- **Performance Max**:AI 自动优化广告系列(全渠道)

### Bidding Strategy(出价策略)

- **Manual CPC**:手动设置每次点击出价
- **Enhanced CPC(ECPC)**:手动 CPC + AI 辅助调整
- **Target CPA**:目标单次转化成本
- **Target ROAS**:目标广告支出回报率
- **Maximize Clicks**:最大化点击量
- **Maximize Conversions**:最大化转化次数
- **Target Impression Share**:目标展示份额(首位/页首/任意位置)

## 使用示例(行业场景)

### 场景 1:SaaS 营销 - 创建搜索广告系列

```python
from google.ads.googleads.client import GoogleAdsClient
from google.ads.googleads.errors import GoogleAdsException

# 加载配置
client = GoogleAdsClient.load_from_storage("google-ads.yaml")
customer_id = "1234567890"

# 1. 创建广告系列预算
campaign_budget_service = client.get_service("CampaignBudgetService")
campaign_budget_operation = client.get_type("CampaignBudgetOperation")

budget = campaign_budget_operation.create
budget.name = "Search Campaign Budget - May 2026"
budget.amount_micros = 1000000000  # $1000(单位:micros,1,000,000 micros = $1)
budget.delivery_method = client.enums.BudgetDeliveryMethodEnum.STANDARD

try:
    budget_response = campaign_budget_service.mutate_campaign_budgets(
        customer_id=customer_id, operations=[campaign_budget_operation]
    )
    budget_resource_name = budget_response.results[0].resource_name
    print(f"Created budget: {budget_resource_name}")
except GoogleAdsException as ex:
    print(f"Error: {ex}")
    exit()

# 2. 创建搜索广告系列
campaign_service = client.get_service("CampaignService")
campaign_operation = client.get_type("CampaignOperation")

campaign = campaign_operation.create
campaign.name = "SaaS Search Campaign - May 2026"
campaign.advertising_channel_type = client.enums.AdvertisingChannelTypeEnum.SEARCH
campaign.status = client.enums.CampaignStatusEnum.PAUSED  # 暂停(避免立即投放)
campaign.campaign_budget = budget_resource_name

# 网络设置
campaign.network_settings.target_google_search = True  # Google 搜索
campaign.network_settings.target_search_network = True  # 搜索网络合作伙伴
campaign.network_settings.target_content_network = False  # 不投放展示网络

# 出价策略:目标 CPA
campaign.target_cpa.target_cpa_micros = 50000000  # $50

campaign_response = campaign_service.mutate_campaigns(
    customer_id=customer_id, operations=[campaign_operation]
)
campaign_resource_name = campaign_response.results[0].resource_name
print(f"Created campaign: {campaign_resource_name}")
```

### 场景 2:广告运营 - 批量添加关键词

```python
# 3. 创建广告组
ad_group_service = client.get_service("AdGroupService")
ad_group_operation = client.get_type("AdGroupOperation")

ad_group = ad_group_operation.create
ad_group.name = "CRM Software Keywords"
ad_group.campaign = campaign_resource_name
ad_group.status = client.enums.AdGroupStatusEnum.ENABLED
ad_group.type_ = client.enums.AdGroupTypeEnum.SEARCH_STANDARD
ad_group.cpc_bid_micros = 5000000  # $5(默认出价)

ad_group_response = ad_group_service.mutate_ad_groups(
    customer_id=customer_id, operations=[ad_group_operation]
)
ad_group_resource_name = ad_group_response.results[0].resource_name
print(f"Created ad group: {ad_group_resource_name}")

# 4. 添加关键词
ad_group_criterion_service = client.get_service("AdGroupCriterionService")

keywords = [
    ("crm software", client.enums.KeywordMatchTypeEnum.EXACT),
    ("best crm for small business", client.enums.KeywordMatchTypeEnum.PHRASE),
    ("customer relationship management", client.enums.KeywordMatchTypeEnum.BROAD)
]

operations = []
for keyword_text, match_type in keywords:
    operation = client.get_type("AdGroupCriterionOperation")
    criterion = operation.create
    criterion.ad_group = ad_group_resource_name
    criterion.status = client.enums.AdGroupCriterionStatusEnum.ENABLED
    criterion.keyword.text = keyword_text
    criterion.keyword.match_type = match_type
    criterion.cpc_bid_micros = 8000000  # $8(关键词出价)
    operations.append(operation)

criterion_response = ad_group_criterion_service.mutate_ad_group_criteria(
    customer_id=customer_id, operations=operations
)
print(f"Added {len(criterion_response.results)} keywords")
```

### 场景 3:创意优化 - 创建响应式搜索广告

```python
# 5. 创建响应式搜索广告(RSA)
ad_group_ad_service = client.get_service("AdGroupAdService")
ad_group_ad_operation = client.get_type("AdGroupAdOperation")

ad_group_ad = ad_group_ad_operation.create
ad_group_ad.ad_group = ad_group_resource_name
ad_group_ad.status = client.enums.AdGroupAdStatusEnum.ENABLED

# 设置最终访问 URL
ad_group_ad.ad.final_urls.append("https://www.example.com/crm")

# 响应式搜索广告(最多 15 个标题,4 个描述)
responsive_search_ad = ad_group_ad.ad.responsive_search_ad

# 标题(Headline)
headlines = [
    "Best CRM Software 2026",
    "Grow Your Sales with CRM",
    "Free CRM Trial - 14 Days",
    "Trusted by 10,000+ Businesses"
]
for headline_text in headlines:
    headline = client.get_type("AdTextAsset")
    headline.text = headline_text
    responsive_search_ad.headlines.append(headline)

# 描述(Description)
descriptions = [
    "Manage contacts, deals, and pipelines in one place. Start free trial today.",
    "AI-powered sales automation. Integrate with Salesforce, HubSpot, and 100+ tools."
]
for description_text in descriptions:
    description = client.get_type("AdTextAsset")
    description.text = description_text
    responsive_search_ad.descriptions.append(description)

ad_response = ad_group_ad_service.mutate_ad_group_ads(
    customer_id=customer_id, operations=[ad_group_ad_operation]
)
print(f"Created ad: {ad_response.results[0].resource_name}")
```

### 场景 4:数据分析 - 查询广告系列性能报表

```python
# 查询最近 30 天的广告系列性能
ga_service = client.get_service("GoogleAdsService")

query = """
    SELECT
      campaign.id,
      campaign.name,
      campaign.status,
      metrics.impressions,
      metrics.clicks,
      metrics.ctr,
      metrics.average_cpc,
      metrics.cost_micros,
      metrics.conversions,
      metrics.conversions_value,
      metrics.cost_per_conversion
    FROM campaign
    WHERE segments.date DURING LAST_30_DAYS
      AND campaign.status = 'ENABLED'
    ORDER BY metrics.cost_micros DESC
"""

response = ga_service.search_stream(customer_id=customer_id, query=query)

print(f"{'Campaign':<30} {'Impr.':<10} {'Clicks':<8} {'CTR':<8} {'CPC':<10} {'Cost':<12} {'Conv.':<8} {'CPA':<10}")
print("-" * 110)

for batch in response:
    for row in batch.results:
        campaign_name = row.campaign.name
        impressions = row.metrics.impressions
        clicks = row.metrics.clicks
        ctr = row.metrics.ctr * 100  # 转换为百分比
        cpc = row.metrics.average_cpc / 1_000_000  # micros 转为美元
        cost = row.metrics.cost_micros / 1_000_000
        conversions = row.metrics.conversions
        cpa = row.metrics.cost_per_conversion / 1_000_000 if row.metrics.conversions > 0 else 0
        
        print(f"{campaign_name:<30} {impressions:<10} {clicks:<8} {ctr:<8.2f}% ${cpc:<9.2f} ${cost:<11.2f} {conversions:<8.1f} ${cpa:<9.2f}")
```

### 场景 5:A/B 测试 - 对比两个广告创意的表现

```python
# 查询广告创意级别的性能
query = """
    SELECT
      ad_group_ad.ad.id,
      ad_group_ad.ad.responsive_search_ad.headlines,
      ad_group_ad.ad.responsive_search_ad.descriptions,
      metrics.impressions,
      metrics.clicks,
      metrics.ctr,
      metrics.conversions,
      metrics.cost_micros
    FROM ad_group_ad
    WHERE segments.date DURING LAST_30_DAYS
      AND ad_group_ad.status = 'ENABLED'
      AND campaign.id = 123456789
    ORDER BY metrics.impressions DESC
    LIMIT 10
"""

response = ga_service.search(customer_id=customer_id, query=query)

for row in response:
    ad_id = row.ad_group_ad.ad.id
    headlines = [h.text for h in row.ad_group_ad.ad.responsive_search_ad.headlines[:3]]
    print(f"Ad ID: {ad_id}")
    print(f"Headlines: {', '.join(headlines)}")
    print(f"Clicks: {row.metrics.clicks}, CTR: {row.metrics.ctr * 100:.2f}%, Conversions: {row.metrics.conversions}")
    print("-" * 80)
```

### 场景 6:自动化优化 - 暂停低绩效关键词

```python
# 查询 CPC > $10 且转化 = 0 的关键词
query = """
    SELECT
      ad_group_criterion.criterion_id,
      ad_group_criterion.keyword.text,
      ad_group_criterion.keyword.match_type,
      metrics.average_cpc,
      metrics.cost_micros,
      metrics.conversions,
      ad_group.id,
      campaign.id
    FROM keyword_view
    WHERE segments.date DURING LAST_30_DAYS
      AND metrics.average_cpc > 10000000
      AND metrics.conversions = 0
      AND ad_group_criterion.status = 'ENABLED'
"""

response = ga_service.search(customer_id=customer_id, query=query)

# 暂停这些关键词
ad_group_criterion_service = client.get_service("AdGroupCriterionService")
operations = []

for row in response:
    criterion_id = row.ad_group_criterion.criterion_id
    ad_group_id = row.ad_group.id
    keyword_text = row.ad_group_criterion.keyword.text
    cpc = row.metrics.average_cpc / 1_000_000
    
    print(f"Pausing keyword: {keyword_text} (CPC: ${cpc:.2f}, Conv: 0)")
    
    # 创建更新操作
    operation = client.get_type("AdGroupCriterionOperation")
    criterion = operation.update
    criterion.resource_name = client.get_service("AdGroupCriterionService").ad_group_criterion_path(
        customer_id, ad_group_id, criterion_id
    )
    criterion.status = client.enums.AdGroupCriterionStatusEnum.PAUSED
    operation.update_mask.paths.append("status")
    operations.append(operation)

# 批量更新
if operations:
    response = ad_group_criterion_service.mutate_ad_group_criteria(
        customer_id=customer_id, operations=operations
    )
    print(f"Paused {len(response.results)} keywords")
```

### 场景 7:受众管理 - 创建再营销受众

```python
# 创建再营销受众(需先在网站安装 Google Ads Tag)
user_list_service = client.get_service("UserListService")
user_list_operation = client.get_type("UserListOperation")

user_list = user_list_operation.create
user_list.name = "Website Visitors - Last 30 Days"
user_list.description = "Users who visited our website in the last 30 days"
user_list.membership_life_span = 30  # 30 天有效期

# 基本用户列表(需配合 Google Ads Tag)
user_list.basic_user_list.actions.extend([
    client.get_type("UserListActionInfo")
])

user_list_response = user_list_service.mutate_user_lists(
    customer_id=customer_id, operations=[user_list_operation]
)
print(f"Created user list: {user_list_response.results[0].resource_name}")
```

### 场景 8:跨账户管理 - 批量查询多个子账户性能

```python
# 如果你是 MCC(Manager Account),可批量查询子账户
# 设置 login_customer_id 为 MCC Account ID

login_customer_id = "9876543210"  # MCC Account ID
child_customer_ids = ["1234567890", "1234567891", "1234567892"]

for child_id in child_customer_ids:
    query = """
        SELECT
          customer.id,
          customer.descriptive_name,
          metrics.cost_micros,
          metrics.conversions
        FROM customer
        WHERE segments.date DURING LAST_30_DAYS
    """
    
    # 设置 login_customer_id
    client.login_customer_id = login_customer_id
    
    response = ga_service.search(customer_id=child_id, query=query)
    for row in response:
        print(f"Account: {row.customer.descriptive_name}, Cost: ${row.metrics.cost_micros / 1_000_000:.2f}, Conv: {row.metrics.conversions}")
```

## 适用场景

### 典型工作流

1. **SEM 投放工作流(Search Engine Marketing)**
   - 关键词研究(使用 Keyword Planner API 或第三方工具)
   - 创建广告系列 → 广告组 → 关键词 → 广告创意
   - 设置出价策略(Target CPA 或 Manual CPC)
   - 添加广告附加信息(Sitelinks、Callouts)
   - 投放 → 监控性能(CTR、CPC、Conversion Rate)
   - 优化:暂停低绩效关键词、提升高 ROI 关键词出价
   - A/B 测试广告创意(标题、描述、Landing Page)

2. **电商 Shopping 广告工作流**
   - 同步产品目录到 Google Merchant Center
   - 创建 Shopping Campaign
   - 设置产品组(Product Groups)和出价
   - 优化产品 Feed(标题、图片、价格)
   - 监控 ROAS(广告支出回报率)
   - 识别畅销品 → 单独设置高出价
   - 负向定向低利润或低库存商品

3. **品牌曝光工作流(Display + Video)**
   - 创建 Display Campaign(展示广告系列)
   - 上传响应式展示广告创意
   - 设置受众定向(兴趣、再营销、相似受众)
   - 投放 YouTube 视频广告(TrueView In-Stream)
   - 监控 View Rate 和 CPV(每次观看成本)
   - 分析品牌搜索量变化(Brand Lift Study)

4. **自动化出价优化工作流**
   - 启用 Smart Bidding(Target CPA/ROAS)
   - 设置转化目标(购买、注册、电话)
   - AI 学习期(14-30 天)
   - 监控性能变化
   - 调整目标 CPA/ROAS
   - 结合 Automated Rules 暂停异常关键词

## 与其他工具的区分

### Google Ads vs Facebook Ads(详细对比)

| 功能 | Google Ads | Facebook Ads |
|------|-----------|--------------|
| **搜索意图捕捉** | 强(用户主动搜索) | 弱(Feed 推送) |
| **受众规模** | 全球 90% 搜索流量 | 30 亿社交用户 |
| **受众定向** | 关键词 + 再营销 | 兴趣 + 行为 + Lookalike |
| **广告形式** | 文字 + 图片 + 视频 | 图片 + 视频 + Carousel |
| **平均 CPC** | $1-$2(竞争激烈) | $0.50-$2(较低) |
| **转化率** | 高(搜索意图明确) | 中等(需培育) |
| **B2B 适用度** | 高(LinkedIn 更好) | 中等 |
| **B2C 适用度** | 高 | 极高 |
| **学习曲线** | 陡峭(GAQL、Quality Score) | 中等(Pixel、受众管理) |

### Google Ads vs Microsoft Advertising(Bing Ads)

| 维度 | Google Ads | Microsoft Ads |
|------|-----------|---------------|
| **市场份额** | 90% | 3% |
| **CPC** | 高 | 低 30-50% |
| **受众画像** | 全年龄段 | 偏中老年、高收入 |
| **B2B 适用度** | 高 | 极高(LinkedIn Audience) |
| **API 相似度** | - | 极高(几乎一致) |
| **适用场景** | 主力渠道 | 补充流量,降低 CPA |

## 限制和注意事项

### API 调用限额

| 维度 | 限制 | 备注 |
|------|------|------|
| **速率限制** | 15,000 次/分钟/Developer Token | 超限返回 429 错误 |
| **报表查询** | 单次最多 10,000 行 | 需分页查询 |
| **批量操作** | 单次最多 5,000 个操作 | 需分批请求 |
| **GAQL 查询** | 最多 10 个 FROM 子句 | 复杂查询需拆分 |

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **DEVELOPER_TOKEN_NOT_APPROVED** | Developer Token 未审核 | 使用测试账户或等待审核 |
| **CUSTOMER_NOT_FOUND** | Customer ID 错误 | 检查 10 位数字 ID |
| **AUTHENTICATION_ERROR** | OAuth Token 无效 | 重新生成 Refresh Token |
| **QUOTA_EXCEEDED** | API 限流 | 降低请求频率 |
| **INVALID_QUERY** | GAQL 语法错误 | 检查 SELECT/FROM/WHERE 子句 |
| **CRITERION_ERROR** | 关键词错误(如过长) | 关键词最多 80 字符 |
| **BUDGET_ERROR** | 预算设置错误 | 检查预算金额(micros 单位) |

### 注意事项

- **Developer Token 审核**:生产环境需审核(1-3 天),测试账户可立即使用
- **测试账户限制**:只能访问测试广告账户(在 Google Ads UI 中创建)
- **micros 单位**:所有金额使用 micros(1,000,000 micros = $1)
- **质量得分**:影响广告排名和 CPC,需优化广告相关性和 Landing Page 体验
- **Smart Bidding 学习期**:启用后需 14-30 天学习期,期间性能可能波动
- **转化跟踪**:需安装 Google Ads Tag 或配置 Google Analytics 4
- **GAQL 学习曲线**:类 SQL 但语法略有不同,需参考文档
- **账户结构**:建议按产品/服务分 Campaign,按主题分 Ad Group
- **广告政策**:违反 Google Ads 政策会导致账户封禁(如虚假声明、仿冒品)

## 定价(详细分级)

### Google Ads 费用

| 费用项 | 价格 | 备注 |
|--------|------|------|
| **API 使用** | 免费 | 无 API 调用费用 |
| **广告支出** | 按实际投放计费 | CPC/CPM/CPA/CPV |
| **搜索广告 CPC** | $1-$2(平均) | 竞争激烈行业 $5-50 |
| **展示广告 CPM** | $0.50-$4 | 千次展示成本 |
| **视频广告 CPV** | $0.10-$0.30 | 每次观看成本 |
| **无最低消费** | - | 可设置日预算 $1 起 |

### 高竞争行业 CPC(美国市场)

- **保险**:$50-$100
- **律师**:$50-$150
- **金融**:$10-$50
- **SaaS**:$5-$20
- **电商**:$0.50-$2

## 参考链接

### 官方文档

- [Google Ads API 文档](https://developers.google.com/google-ads/api/docs/start)
- [Python SDK GitHub](https://github.com/googleads/google-ads-python)
- [GAQL 查询语言](https://developers.google.com/google-ads/api/docs/query/overview)
- [API 参考(v17)](https://developers.google.com/google-ads/api/reference/rpc/v17/overview)
- [OAuth 2.0 指南](https://developers.google.com/google-ads/api/docs/oauth/overview)

### 进阶学习

- [代码示例(多语言)](https://github.com/googleads/google-ads-python/tree/main/examples)
- [GAQL Playground(在线测试)](https://developers.google.com/google-ads/api/fields/v17/overview_query_builder)
- [Performance Max 指南](https://developers.google.com/google-ads/api/docs/performance-max/overview)
- [Smart Bidding 最佳实践](https://support.google.com/google-ads/answer/7065882)
- [Google Ads Scripts(JavaScript 自动化)](https://developers.google.com/google-ads/scripts)

### 社区资源

- [Google Ads API Forum](https://groups.google.com/g/adwords-api)
- [Stack Overflow(google-ads-api 标签)](https://stackoverflow.com/questions/tagged/google-ads-api)
- [Reddit r/PPC](https://www.reddit.com/r/PPC/)
- [Google Ads Community](https://support.google.com/google-ads/community)

### SDK 和工具

- [Python SDK](https://github.com/googleads/google-ads-python)
- [Node.js SDK](https://github.com/Opteo/google-ads-api)
- [Java SDK](https://github.com/googleads/google-ads-java)
- [PHP SDK](https://github.com/googleads/google-ads-php)
- [Ruby SDK](https://github.com/googleads/google-ads-ruby)
- [.NET SDK](https://github.com/googleads/google-ads-dotnet)
- [MCP Server(第三方)](https://github.com/modelcontextprotocol/servers/tree/main/src/google-ads)
- [Optmyzr(第三方优化工具)](https://www.optmyzr.com/)
- [WordStream(PPC 管理平台)](https://www.wordstream.com/)
