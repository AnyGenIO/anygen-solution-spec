# ZoomInfo API

> 企业级联系人和公司数据库(Apollo竞品),数据更深(组织架构/技术栈/意图)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key + 用户名密码
- **环境变量:**
  - `ZOOMINFO_API_KEY` — ZoomInfo API密钥
  - `ZOOMINFO_USERNAME` — ZoomInfo用户名
  - `ZOOMINFO_PASSWORD` — ZoomInfo密码
- **获取步骤:**
  1. 联系ZoomInfo销售购买企业版
  2. 单独申请API访问权限(需额外付费)
  3. 登录ZoomInfo平台
  4. Settings → Integrations → API → Generate Key
- **验证:**
  ```bash
  # 首先获取JWT Token
  curl -X POST https://api.zoominfo.com/authenticate \
    -H "Content-Type: application/json" \
    -d '{
      "username": "'$ZOOMINFO_USERNAME'",
      "password": "'$ZOOMINFO_PASSWORD'"
    }'
  
  # 使用返回的JWT测试
  curl -H "Authorization: Bearer $JWT_TOKEN" \
    https://api.zoominfo.com/lookup/company/domain?domain=example.com
  ```

## 核心能力

### 深度联系人搜索
- 精准邮箱和直线电话
- 手机号码(部分高管)
- 联系人职位和职责
- 工作经历和教育背景
- 社交媒体账号

### 公司信息和组织架构
- 公司基本信息(规模/收入/地址/行业)
- 完整组织架构(部门和汇报关系)
- 高管团队名单
- 决策链识别(谁向谁汇报)
- 分支机构和子公司

### 技术栈识别(Technographics)
- 公司使用的技术产品(MarTech/SalesTech/DevOps等)
- 技术栈变更历史
- 竞品替换信号
- IT预算和支出

### Scoops(公司动态)
- 融资和并购新闻
- 高管变动
- 招聘信号(正在招哪些岗位)
- 办公室扩张/搬迁
- 获奖和认证

### Intent Data(购买意图)
- 正在研究的产品类别
- 浏览行为和内容消费
- 购买阶段(Awareness/Consideration/Decision)
- 竞品对比信号

### 数据补全(Enrichment)
- 自动填充缺失字段
- 邮箱验证
- 数据更新和去重
- 批量补全(API或CSV上传)

## 使用示例

### 获取JWT Token
```bash
JWT_TOKEN=$(curl -s -X POST https://api.zoominfo.com/authenticate \
  -H "Content-Type: application/json" \
  -d '{
    "username": "'$ZOOMINFO_USERNAME'",
    "password": "'$ZOOMINFO_PASSWORD'"
  }' | jq -r '.jwt')

echo "JWT Token: $JWT_TOKEN"
```

### 通过域名查询公司信息
```bash
curl -H "Authorization: Bearer $JWT_TOKEN" \
  "https://api.zoominfo.com/lookup/company/domain?domain=salesforce.com" | jq
```

### 搜索联系人(按职位和公司)
```bash
curl -X POST https://api.zoominfo.com/search/contact \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "managementLevel": ["VP", "C-Level"],
    "jobFunction": ["Sales", "Revenue Operations"],
    "companyRevenue": {
      "min": 10000000,
      "max": 100000000
    },
    "companyEmployeeCount": {
      "min": 50,
      "max": 500
    },
    "country": ["United States"],
    "page": 1,
    "rpp": 25
  }' | jq
```

### 获取公司组织架构
```bash
# 获取公司ID
COMPANY_ID="12345678"

curl -H "Authorization: Bearer $JWT_TOKEN" \
  "https://api.zoominfo.com/enrich/company?id=$COMPANY_ID&outputFields=organizationChart" | jq
```

### 查询公司技术栈
```bash
curl -X POST https://api.zoominfo.com/search/company \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "technologies": ["Salesforce", "HubSpot"],
    "companyRevenue": {"min": 5000000}
  }' | jq
```

### 获取Intent Data(购买意图信号)
```bash
curl -X POST https://api.zoominfo.com/search/intent \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "topics": ["CRM Software", "Sales Automation"],
    "intentStrength": "Strong",
    "dateRange": {
      "start": "2026-03-01",
      "end": "2026-04-07"
    }
  }' | jq
```

### 批量数据补全(Enrichment)
```bash
curl -X POST https://api.zoominfo.com/enrich/contact \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "matchContactInput": [
      {
        "firstName": "John",
        "lastName": "Smith",
        "companyName": "Acme Corp"
      },
      {
        "email": "jane.doe@example.com"
      }
    ],
    "outputFields": ["email", "directPhone", "mobilePhone", "jobTitle"]
  }' | jq
```

### 查询Scoops(公司新闻/招聘信号)
```bash
curl -H "Authorization: Bearer $JWT_TOKEN" \
  "https://api.zoominfo.com/enrich/company?id=12345678&outputFields=scoops" | jq '.scoops[] | select(.category=="Hiring")'
```

## 适用场景
- 企业级ABM(Account-Based Marketing)策略
- 找大公司多层级决策链(VP→Director→Manager)
- 捕捉购买意图信号(正在研究你的产品类别)
- 竞品替换机会(发现正在用竞品的公司)
- 数据准确性要求高(比Apollo更深更准)
- 技术栈定向销售(如"找所有用Salesforce但没用HubSpot的公司")

## 限制和注意事项
- 价格昂贵(一般$15k-50k/年起,企业定价)
- API额度按Credits计费(每次查询消耗Credits)
- 需要单独购买API访问权限
- 主要覆盖北美和欧洲数据,亚洲数据较弱
- 数据导出和使用受合同限制
- Intent Data需要额外订阅
- JWT Token有效期24小时,需定期刷新

## 定价
- 企业定制定价,需联系销售
- 一般起步价: $15,000-50,000/年
- API访问需额外付费
- Credits按查询消耗(具体价格保密)
- Intent Data需单独订阅

## 参考链接
- [官方文档](https://api-docs.zoominfo.com/)
- [API参考](https://api-docs.zoominfo.com/api-reference)
- [认证指南](https://api-docs.zoominfo.com/authentication)
- [Intent Data说明](https://www.zoominfo.com/business/intent-data)
