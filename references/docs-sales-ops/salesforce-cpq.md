# Salesforce CPQ

> Salesforce原生报价配置工具,处理复杂定价(折扣规则/产品组合/审批流)

## 快速开始

### 安装
无独立CLI,通过Salesforce CLI操作。需要在Salesforce org中安装CPQ License。

### 认证配置
- **方式:** oauth(与Salesforce CLI相同)
- **环境变量:**
  - `SF_ACCESS_TOKEN` — Salesforce访问令牌
  - `SF_INSTANCE_URL` — 组织URL(如 https://yourorg.my.salesforce.com)
- **获取步骤:**
  1. 同Salesforce CLI认证流程
  2. 确保org已安装Salesforce CPQ License
  3. 管理员配置CPQ设置(Setup → Installed Packages → Salesforce CPQ)
- **验证:**
  ```bash
  sf data query --query "SELECT Id, Name FROM SBQQ__Quote__c LIMIT 1"
  ```

## 核心能力
- 产品目录和价格表管理
- 报价单生成(支持多币种/多层级折扣)
- 产品配置规则(Bundle/选配/互斥)
- 审批工作流(超折扣需审批)
- 续约和增购管理
- 报价→订单→发票自动流转
- 与Salesforce CRM深度集成
- 合同修正(Amendment)

## 使用示例

### 查询报价单
```bash
sf data query --query "SELECT Id, SBQQ__QuoteNumber__c, SBQQ__NetAmount__c, SBQQ__Status__c FROM SBQQ__Quote__c WHERE SBQQ__Opportunity2__c = '006xxxxxxxxxxxxxxx'"
```

### 创建报价单(通过Apex/API)
```bash
# 使用Salesforce REST API
curl -X POST \
  -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/sobjects/SBQQ__Quote__c" \
  -d '{
    "SBQQ__Opportunity2__c": "006xxxxxxxxxxxxxxx",
    "SBQQ__Account__c": "001xxxxxxxxxxxxxxx",
    "SBQQ__PricebookId__c": "01sxxxxxxxxxxxxxxx",
    "SBQQ__Type__c": "Quote"
  }'
```

### 添加产品到报价单
```bash
# 创建Quote Line(报价行)
curl -X POST \
  -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/sobjects/SBQQ__QuoteLine__c" \
  -d '{
    "SBQQ__Quote__c": "a0Dxxxxxxxxxxxxxxx",
    "SBQQ__Product__c": "01txxxxxxxxxxxxxxx",
    "SBQQ__Quantity__c": 10,
    "SBQQ__Discount__c": 15
  }'
```

### 计算报价(使用CPQ API)
```bash
# CPQ Calculate API需要通过Apex调用
# 示例: 通过匿名Apex执行
sf apex run -f - <<EOF
SBQQ__Quote__c quote = [SELECT Id FROM SBQQ__Quote__c WHERE Id = 'a0Dxxxxxxxxxxxxxxx'];
QuoteCalculator.calculate(quote.Id);
EOF
```

### 生成PDF报价单
```bash
# 通过Visualforce页面生成PDF
curl -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  "https://yourorg.my.salesforce.com/apex/sbqq__pi?id=a0Dxxxxxxxxxxxxxxx" \
  --output quote.pdf
```

### 提交审批
```bash
# 使用Approval Process API
curl -X POST \
  -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/process/approvals" \
  -d '{
    "requests": [{
      "actionType": "Submit",
      "contextId": "a0Dxxxxxxxxxxxxxxx",
      "comments": "请审批15%折扣"
    }]
  }'
```

### 查询产品配置规则
```bash
sf data query --query "SELECT Id, SBQQ__Active__c, SBQQ__Type__c, SBQQ__ErrorMessage__c FROM SBQQ__ProductRule__c WHERE SBQQ__Active__c = true"
```

## 适用场景
- 产品SKU多且定价复杂(如SaaS分层+席位+模块)
- 需要多层级审批(AE→经理→VP)
- 年度续约和扩展场景
- 企业级B2B报价
- 产品Bundle和配置(基础版+可选模块)
- 多货币/多地区定价

## 限制和注意事项
- 学习曲线陡峭,配置复杂
- 需专职CPQ管理员
- 与Salesforce绑定(无法独立使用)
- License费用高($75-150/用户/月)
- 需要Salesforce Enterprise版以上
- 复杂配置规则可能影响性能
- 升级CPQ版本需谨慎测试

## 定价
- Salesforce CPQ: $75/用户/月
- CPQ Plus: $150/用户/月(包含高级功能)
- 需要Salesforce Enterprise版以上($165/用户/月起)

## 参考链接
- [官方文档](https://developer.salesforce.com/docs/atlas.en-us.cpq_dev_api.meta/cpq_dev_api/)
- [CPQ API指南](https://developer.salesforce.com/docs/atlas.en-us.cpq_dev_api.meta/cpq_dev_api/cpq_dev_api_intro.htm)
- [管理员指南](https://help.salesforce.com/s/articleView?id=sf.cpq_admin_guide.htm)
- [Trailhead学习](https://trailhead.salesforce.com/content/learn/trails/get-started-with-cpq)
