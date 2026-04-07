# Clearbit API

> 公司信息补全和富化(Enrichment),自动填充公司规模/行业/技术栈等

## 快速开始

### 安装
```bash
pip install clearbit
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `CLEARBIT_API_KEY` — Clearbit API密钥
- **获取步骤:**
  1. 注册 https://clearbit.com (已被HubSpot收购)
  2. Dashboard → API → API Keys
  3. 复制 Secret Key
- **验证:**
  ```bash
  curl -u "$CLEARBIT_API_KEY:" \
    https://company.clearbit.com/v2/companies/find?domain=stripe.com
  ```

## 核心能力
- 公司信息补全(规模/行业/融资/技术栈/社交媒体)
- 联系人信息补全(职位/LinkedIn/邮箱)
- 公司搜索(按条件筛选目标公司)
- 网站访客识别(Reveal - 识别匿名访客公司)
- Logo API(获取公司Logo)
- 实时数据富化(Enrichment API)

## 使用示例

### 通过域名获取公司信息
```bash
curl -u "$CLEARBIT_API_KEY:" \
  "https://company.clearbit.com/v2/companies/find?domain=shopify.com"
```

### Python SDK使用
```python
import clearbit
clearbit.key = 'your_api_key'

# 公司富化
company = clearbit.Company.find(domain='stripe.com', stream=True)
print(f"公司名称: {company['name']}")
print(f"员工数: {company['metrics']['employees']}")
print(f"行业: {company['category']['industry']}")
print(f"技术栈: {company['tech']}")
```

### 联系人富化
```python
import clearbit

person = clearbit.Person.find(email='john@example.com', stream=True)
if person:
    print(f"姓名: {person['name']['fullName']}")
    print(f"职位: {person['employment']['title']}")
    print(f"公司: {person['employment']['name']}")
    print(f"LinkedIn: {person['linkedin']['handle']}")
```

### 批量富化(通过API)
```bash
curl -X POST \
  -u "$CLEARBIT_API_KEY:" \
  -H "Content-Type: application/json" \
  https://person.clearbit.com/v2/combined/find \
  -d '{
    "email": "alex@clearbit.com"
  }'
```

### 公司搜索
```bash
curl -u "$CLEARBIT_API_KEY:" \
  "https://company.clearbit.com/v2/companies/search?query=name:clearbit"
```

### Prospector API(按条件找公司)
```bash
curl -u "$CLEARBIT_API_KEY:" \
  "https://prospector.clearbit.com/v1/people/search?domain=clearbit.com&role=engineering&seniority=executive"
```

## 适用场景
- CRM数据补全(线索进来后自动填字段)
- 判断ICP匹配度(自动评分)
- 配合Apollo做更精准的目标筛选
- 表单填写后自动识别公司
- 销售优先级排序(按公司规模/融资轮次)
- 网站访客识别(Reveal - 谁在看我的网站)

## 限制和注意事项
- HubSpot收购后免费额度有限(50次/月)
- 主要覆盖欧美公司,亚洲数据较弱
- API限流: 600次/分钟(付费版)
- Enrichment可能返回空(数据库中无该公司)
- 实时查询响应时间2-3秒(非缓存)
- Reveal功能需单独付费

## 定价
- Free版: 50次/月(HubSpot收购后)
- Risk: $99/月起
- Growth: $499/月起
- Business: 需联系销售
- 已被HubSpot收购,部分功能已整合到HubSpot

## 参考链接
- [官方文档](https://dashboard.clearbit.com/docs)
- [API参考](https://dashboard.clearbit.com/docs#api-reference)
- [Enrichment API](https://dashboard.clearbit.com/docs#enrichment-api)
- [Python SDK](https://github.com/clearbit/clearbit-python)
