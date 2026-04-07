# Salesforce Einstein Analytics

> Salesforce原生AI分析,预测商机成功率/流失风险/最佳行动

## 快速开始

### 安装
无独立CLI,通过Salesforce CLI + REST API访问。需要在Salesforce org中启用Einstein License。

### 认证配置
- **方式:** oauth(与Salesforce CLI相同)
- **环境变量:**
  - `SF_ACCESS_TOKEN` — Salesforce访问令牌
  - `SF_INSTANCE_URL` — 组织URL(如 https://yourorg.my.salesforce.com)
- **获取步骤:**
  1. 同Salesforce CLI认证流程
  2. 确保org已购买Einstein Analytics/Tableau CRM License
  3. 管理员启用Einstein功能(Setup → Einstein → Enable)
- **验证:**
  ```bash
  sf data query --query "SELECT Id, Name FROM Dashboard LIMIT 1"
  ```

## 核心能力
- Opportunity Scoring(AI预测成单概率)
- 流失预警(Customer Success场景)
- 销售预测(基于历史数据和活动)
- 自动洞察(异常检测,如突然掉的Deal)
- 自定义Dashboard和报表
- Einstein Lead Scoring(线索质量评分)
- Einstein Activity Capture(自动记录邮件/会议)
- Einstein Conversation Insights(邮件情感分析)

## 使用示例

### 查询Opportunity预测评分
```bash
# Einstein Opportunity Scoring字段会自动添加到Opportunity对象
sf data query --query "SELECT Id, Name, StageName, Amount, Probability, Einstein_Score__c, Einstein_Factors__c FROM Opportunity WHERE IsClosed = false ORDER BY Einstein_Score__c DESC LIMIT 20"
```

### 获取Lead评分
```bash
sf data query --query "SELECT Id, Name, Email, Company, LeadScore, Einstein_Score__c FROM Lead WHERE Status = 'Open' ORDER BY Einstein_Score__c DESC LIMIT 50"
```

### 查询Einstein Analytics Dashboard
```bash
# 使用Analytics REST API
curl -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/wave/dashboards"
```

### 运行Einstein Discovery Story
```bash
curl -X POST \
  -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/wave/stories" \
  -d '{
    "label": "Opportunity Win Rate Analysis",
    "datasetId": "0Fb..."
  }'
```

### 获取Einstein推荐(Next Best Action)
```bash
sf data query --query "SELECT Id, Name, Recommendation__c, ActionType__c FROM Einstein_Recommendation__c WHERE OwnerId = '005...'"
```

### 查询Einstein Activity数据
```bash
# Einstein Activity Capture自动关联邮件和事件
sf data query --query "SELECT Id, Subject, ActivityDate, WhoId, Einstein_Sentiment__c FROM Task WHERE Einstein_Captured__c = true AND ActivityDate = THIS_WEEK"
```

### 获取预测模型性能
```bash
curl -H "Authorization: Bearer $SF_ACCESS_TOKEN" \
  "https://yourorg.my.salesforce.com/services/data/v60.0/einstein/models/{model_id}/metrics"
```

## 适用场景
- 已重度使用Salesforce生态
- 想在CRM内直接看AI预测
- 自动化线索打分(MQL识别)
- 识别高风险流失客户
- 优先级排序(先跟进高分Lead/Opportunity)
- Sales Manager季度预测
- 异常检测(Deal突然停滞/客户不回邮件)

## 限制和注意事项
- 需要足够历史数据训练(至少1-2年Closed Won/Lost数据)
- 与Salesforce绑定(无法独立使用)
- Einstein Analytics License费用高($75/用户/月起)
- AI模型准确率取决于数据质量(GIGO原则)
- Opportunity Scoring需要至少200个Closed Opportunities
- Lead Scoring需要至少1000个转化/未转化Lead
- 模型训练和更新周期通常1-4周
- 部分高级功能需Einstein Analytics Plus($150/用户/月)

## 定价
- Einstein Analytics: $75/用户/月起
- Einstein Analytics Plus: $150/用户/月
- Einstein Discovery: 需联系销售(通常$75-200/用户/月)
- 需要Salesforce Enterprise版以上($165/用户/月起)

## 参考链接
- [官方文档](https://developer.salesforce.com/docs/atlas.en-us.bi_dev_guide_rest.meta/bi_dev_guide_rest/)
- [Einstein Analytics API](https://developer.salesforce.com/docs/atlas.en-us.bi_dev_guide_rest.meta/bi_dev_guide_rest/bi_resources.htm)
- [Einstein Opportunity Scoring](https://help.salesforce.com/s/articleView?id=sf.einstein_sales_opportunity_scoring.htm)
- [Einstein Lead Scoring](https://help.salesforce.com/s/articleView?id=sf.einstein_sales_lead_scoring.htm)
- [Trailhead学习](https://trailhead.salesforce.com/content/learn/trails/wave_analytics_intro_trail)
