# Apollo.io API

> SDR找人利器,2.75亿联系人数据库,找邮箱/电话/公司信息

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `APOLLO_API_KEY` — Apollo API密钥
- **获取步骤:**
  1. 登录 https://app.apollo.io
  2. Settings → Integrations → API Keys
  3. 点击 "Create API Key"
  4. 复制生成的API Key
- **验证:**
  ```bash
  curl -H "Content-Type: application/json" \
    -H "Cache-Control: no-cache" \
    -H "X-Api-Key: $APOLLO_API_KEY" \
    https://api.apollo.io/v1/auth/health
  ```

## 核心能力
- 按公司/职位/行业/地区搜索联系人(2.75亿+数据库)
- 获取邮箱和电话号码(B2B验证准确率85-90%)
- 公司信息查询(规模/融资/技术栈/收入)
- 邮箱验证(Deliverability检查)
- 联系人列表管理
- 序列化邮件发送(Sales Engagement)
- 技术栈识别(查看目标公司使用的工具)

## 使用示例

### 搜索联系人
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Cache-Control: no-cache" \
  -H "X-Api-Key: $APOLLO_API_KEY" \
  https://api.apollo.io/v1/mixed_people/search \
  -d '{
    "q_organization_domains": ["stripe.com", "shopify.com"],
    "person_titles": ["VP Sales", "Head of Sales", "Sales Director"],
    "page": 1,
    "per_page": 10
  }'
```

### 按行业和职位搜索
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $APOLLO_API_KEY" \
  https://api.apollo.io/v1/mixed_people/search \
  -d '{
    "person_titles": ["CTO", "VP Engineering"],
    "organization_industry_tag_ids": ["5567cd4773696439b10b0000"],
    "organization_num_employees_ranges": ["101,500"],
    "per_page": 25
  }'
```

### 获取联系人详细信息
```bash
curl -X GET \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $APOLLO_API_KEY" \
  "https://api.apollo.io/v1/people/match?email=john@example.com"
```

### 验证邮箱
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $APOLLO_API_KEY" \
  https://api.apollo.io/v1/emailer_campaigns/email_status \
  -d '{
    "email": "contact@example.com"
  }'
```

### 搜索公司信息
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $APOLLO_API_KEY" \
  https://api.apollo.io/v1/organizations/search \
  -d '{
    "q_organization_keyword_tags": ["saas", "b2b"],
    "organization_num_employees_ranges": ["51,200"],
    "page": 1,
    "per_page": 20
  }'
```

## 适用场景
- 找目标客户的联系方式(邮箱/电话)
- 批量获取某行业/职位的联系人
- 验证邮箱有效性(发邮件前去掉无效邮箱)
- 建立外呼名单
- ABM账户研究(找决策链)
- 竞争对手客户挖掘

## 限制和注意事项
- 免费版额度很少(每月50 credits)
- 数据准确率约85-90%(非100%)
- API限流: 根据套餐不同(Basic 60次/分钟)
- Email credits消耗: 每次导出邮箱消耗1 credit
- 主要覆盖北美/欧洲数据,亚洲数据较弱
- Export limits根据套餐(Basic 250/月, Professional 1000/月)

## 定价
- Free版: 50 credits/月
- Basic: $49/用户/月 (2,400 credits/年)
- Professional: $79/用户/月 (12,000 credits/年)
- Organization: $119/用户/月 (24,000 credits/年)

## 参考链接
- [官方文档](https://apolloio.github.io/apollo-api-docs/)
- [API参考](https://apolloio.github.io/apollo-api-docs/#introduction)
- [搜索过滤器](https://apolloio.github.io/apollo-api-docs/#search-for-people)
