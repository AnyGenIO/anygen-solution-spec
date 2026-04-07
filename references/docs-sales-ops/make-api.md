# Make (Integromat) API

> 可视化集成自动化平台(Zapier竞品,更强大的逻辑控制)

## 快速开始

### 安装
无CLI,主要使用 Web界面配置,API用于编程控制

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `MAKE_API_KEY` — Make API Token
- **获取步骤:**
  1. 登录 Make
  2. Profile → API
  3. Generate token → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Token $MAKE_API_KEY" \
    https://eu1.make.com/api/v2/scenarios
  ```

## 核心能力

### 可视化工作流(Scenario)
- 拖拽式流程设计
- 2000+应用集成
- 模块化组件(Modules)
- 实时执行预览
- 流程模板库

### 高级路由和过滤
- 复杂条件分支(Router)
- 错误处理路径(Error Handler)
- 聚合器(Aggregator)
- 迭代器(Iterator)
- 比Zapier更灵活的逻辑控制

### 数据转换
- JSON/Array操作
- 文本处理(正则/格式化)
- 数学计算
- 日期时间处理
- 变量和函数

### 错误处理和重试
- 自动重试失败模块
- 错误通知(Email/Slack)
- Fallback路径
- 日志和调试

### Webhooks和HTTP
- 接收Webhook
- 发送HTTP请求(GET/POST/PUT/DELETE)
- 自定义Headers和认证
- OAuth集成

### 数据存储(Data Store)
- 临时存储数据
- 跨Scenario共享
- 键值对数据库
- 记录增删改查

## 使用示例

### 列出所有Scenarios
```bash
curl -H "Authorization: Token $MAKE_API_KEY" \
  https://eu1.make.com/api/v2/scenarios | jq
```

### 创建Scenario(Web界面示例)

**场景1: Salesforce新Deal → Slack通知 + 创建Google Doc**
1. Trigger: Salesforce - Watch Records (Opportunity)
2. Filter: Amount > 10000
3. Module 1: Slack - Create a Message
   - Channel: #deals
   - Text: 
     ```
     新大单! 🎉
     客户: {{Account.Name}}
     金额: ${{Amount}}
     阶段: {{StageName}}
     负责人: {{Owner.Name}}
     ```
4. Module 2: Google Docs - Create a Document
   - Title: Deal-{{Name}}-{{today}}
   - Content: 自动生成Deal摘要
5. Module 3: Salesforce - Update Record
   - 添加Google Doc链接到Notes字段

**场景2: 批量处理CSV线索导入**
1. Trigger: Google Drive - Watch Files(CSV)
2. Module 1: CSV - Parse CSV
3. Module 2: Iterator(遍历每一行)
4. Router(按条件分流):
   - Path 1(Email有效): 
     * Clearbit - Enrich Company
     * HubSpot - Create Contact
   - Path 2(Email无效):
     * Google Sheets - Add Row(错误日志)
5. Aggregator(汇总结果)
6. Gmail - Send Summary Email

**场景3: 客户回复邮件 → 更新CRM + 通知销售**
1. Trigger: Gmail - Watch Emails
2. Filter: From contains "@customer-domain.com"
3. Module 1: OpenAI - Analyze Email Sentiment
4. Router:
   - Path 1(Negative Sentiment):
     * Slack - Urgent Notification
     * Salesforce - Create Task(Priority: High)
   - Path 2(Positive):
     * Salesforce - Update Opportunity(Stage: Negotiation)
     * Calendar - Create Event(Follow-up Call)
5. Module 2: Gmail - Reply
   - Template: 感谢回复,我们的AE会尽快联系您...

### 使用Webhook触发Scenario
```bash
# 在Make创建Scenario: Trigger = Webhooks - Custom Webhook
# 获得Webhook URL

curl -X POST https://hook.eu1.make.com/abcdef1234567890 \
  -H "Content-Type: application/json" \
  -d '{
    "event": "deal_won",
    "deal_name": "科技公司-企业版",
    "amount": 50000,
    "customer_email": "cto@techcorp.com"
  }'
```

### 数据转换示例(Web界面)
**JSON解析和提取:**
```javascript
// 输入JSON
{
  "customer": {
    "name": "张三",
    "company": "科技公司",
    "contacts": [
      {"type": "email", "value": "zhang@example.com"},
      {"type": "phone", "value": "+86-138-0000-0000"}
    ]
  }
}

// Make函数提取
{{customer.name}}  // 张三
{{first(customer.contacts; type; email).value}}  // zhang@example.com
```

**日期计算:**
```javascript
// 计算7天后
{{addDays(now; 7; YYYY-MM-DD)}}

// 格式化日期
{{formatDate(customer.created_at; DD/MM/YYYY)}}
```

### 使用API运行Scenario
```bash
SCENARIO_ID="12345"

# 手动触发执行
curl -X POST \
  -H "Authorization: Token $MAKE_API_KEY" \
  "https://eu1.make.com/api/v2/scenarios/$SCENARIO_ID/run"
```

### 查看Scenario执行历史
```bash
curl -H "Authorization: Token $MAKE_API_KEY" \
  "https://eu1.make.com/api/v2/scenarios/$SCENARIO_ID/executions?limit=20" | jq
```

### 启用/禁用Scenario
```bash
# 启用
curl -X PATCH \
  -H "Authorization: Token $MAKE_API_KEY" \
  -H "Content-Type: application/json" \
  "https://eu1.make.com/api/v2/scenarios/$SCENARIO_ID" \
  -d '{"scheduling": {"type": "indefinitely"}}'

# 禁用
curl -X PATCH \
  -H "Authorization: Token $MAKE_API_KEY" \
  -H "Content-Type: application/json" \
  "https://eu1.make.com/api/v2/scenarios/$SCENARIO_ID" \
  -d '{"scheduling": {"type": "off"}}'
```

## 适用场景
- 需要复杂条件逻辑(if-else/loop)
- Zapier功能不够用(如需要数组操作/JSON解析)
- 预算有限(比Zapier便宜)
- 需要错误处理和重试机制
- 数据转换和清洗需求

## 限制和注意事项
- 学习曲线比Zapier陡(更专业)
- 生态和集成不如Zapier广(但主流应用都有)
- API限流: 60次/分钟
- 免费版1000 operations/月(小项目够用)
- 复杂Scenario调试需要时间

## 定价
- Free版: 1,000 operations/月,2个活跃Scenarios
- Core: $10.59/月 - 10,000 ops,5个Scenarios
- Pro: $18.82/月 - 10,000 ops,无限Scenarios,优先支持
- Teams: $34.12/月 - 10,000 ops,3用户,团队协作
- Enterprise: 需联系销售 - 高级功能和支持

## 参考链接
- [官方文档](https://www.make.com/en/help)
- [API参考](https://www.make.com/en/api-documentation)
- [模板库](https://www.make.com/en/templates)
- [函数参考](https://www.make.com/en/help/functions)
