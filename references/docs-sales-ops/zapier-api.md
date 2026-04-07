# Zapier API

> No-code集成自动化平台(连接7000+应用,小团队销售自动化首选)

## 快速开始

### 安装
无CLI,主要使用 Web界面配置,API用于编程控制

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `ZAPIER_API_KEY` — Zapier API密钥
- **获取步骤:**
  1. 登录 Zapier
  2. Settings → APIs
  3. Generate API Key → 复制保存
  4. 注意: API主要用于管理Zaps,日常使用建议Web界面
- **验证:**
  ```bash
  curl -H "X-API-Key: $ZAPIER_API_KEY" \
    https://api.zapier.com/v1/zaps
  ```

## 核心能力

### 触发器-动作自动化(Zaps)
- 7000+应用集成
- 触发器(Trigger): 监听事件(如"HubSpot新Deal")
- 动作(Action): 执行操作(如"Slack通知")
- 多步Zap(3步+需付费版)
- 延迟执行(Delay)

### 过滤和路径(Filter & Paths)
- 条件逻辑(if-then-else)
- 多路径分支
- 过滤不符合条件的数据
- 仅继续执行符合条件的流程

### 数据格式化(Formatter)
- 日期格式转换
- 文本处理(大小写/截取/替换)
- 数字计算
- JSON解析
- 查找和替换

### Webhooks
- 接收Webhook(作为触发器)
- 发送Webhook(作为动作)
- 自定义HTTP请求
- 认证头配置

### 常见销售自动化场景
- 新线索进CRM → Slack通知销售
- 表单提交 → 创建CRM联系人 → 发欢迎邮件
- 签约成功 → 更新电子表格 → 发团队庆祝消息
- Calendar预约 → 创建CRM任务 → 发提醒邮件
- Gmail收到客户回复 → 更新CRM → 通知AE

### Storage(数据存储)
- 临时存储键值对(需Professional版)
- 跨Zap共享数据
- 计数器和累加器

## 使用示例

### 列出所有Zaps
```bash
curl -H "X-API-Key: $ZAPIER_API_KEY" \
  https://api.zapier.com/v1/zaps | jq
```

### 创建Zap(复杂,建议用Web界面)
Web界面创建示例:

**场景1: HubSpot新Deal → Slack通知**
1. Trigger: HubSpot - New Deal
2. 配置HubSpot账户和Pipeline
3. Action: Slack - Send Channel Message
4. 配置消息内容:
   ```
   新商机: {{deal_name}}
   金额: ${{deal_amount}}
   负责人: {{owner_name}}
   ```
5. 测试并开启

**场景2: 表单提交 → CRM + 欢迎邮件**
1. Trigger: Google Forms - New Response
2. Action 1: Salesforce - Create Lead
   - Map字段: Name, Email, Company
3. Filter: Only continue if Email contains "@"
4. Action 2: Gmail - Send Email
   - To: {{Email}}
   - Subject: 欢迎注册!
   - Body: 感谢您的关注...
5. Action 3: Slack - Send Message
   - 通知销售团队有新线索

**场景3: Calendar预约 → CRM任务**
1. Trigger: Calendly - Invitee Created
2. Action 1: HubSpot - Create Task
   - Title: 准备{{event_name}}会议
   - Due Date: {{start_time}}
   - Assigned To: {{sales_owner}}
3. Action 2: Gmail - Send Email
   - To: {{sales_owner_email}}
   - Subject: 新Demo已预约
   - Body: 客户{{invitee_name}}已预约...

### 使用Webhook触发Zap
```bash
# 先在Zapier创建Zap: Trigger = Webhooks by Zapier - Catch Hook
# 获得Webhook URL(类似: https://hooks.zapier.com/hooks/catch/123456/abcdef/)

# 发送数据到Zapier
curl -X POST https://hooks.zapier.com/hooks/catch/123456/abcdef/ \
  -H "Content-Type: application/json" \
  -d '{
    "deal_name": "科技公司-企业版",
    "deal_amount": 50000,
    "customer_email": "cto@techcorp.com",
    "ae_name": "张伟"
  }'
```

### Zapier Formatter示例(Web界面)
**格式化日期:**
- Trigger: 收到包含日期的数据 "2026-04-10T14:00:00Z"
- Action: Formatter - Date/Time - Format
- 输入: {{trigger_date}}
- 从格式: ISO 8601
- 到格式: MMMM DD, YYYY
- 输出: "April 10, 2026"

**文本处理:**
- Action: Formatter - Text - Titlecase
- 输入: "john smith"
- 输出: "John Smith"

### 使用API暂停/启动Zap
```bash
ZAP_ID="12345"

# 暂停Zap
curl -X PATCH https://api.zapier.com/v1/zaps/$ZAP_ID \
  -H "X-API-Key: $ZAPIER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"state": "off"}'

# 启动Zap
curl -X PATCH https://api.zapier.com/v1/zaps/$ZAP_ID \
  -H "X-API-Key: $ZAPIER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"state": "on"}'
```

### 查看Zap运行历史
```bash
curl -H "X-API-Key: $ZAPIER_API_KEY" \
  "https://api.zapier.com/v1/zaps/$ZAP_ID/history?limit=50" | jq
```

## 适用场景
- 中小团队无开发资源
- 需要快速打通工具(CRM+邮件+Slack+表单等)
- 简单自动化(复杂逻辑建议代码实现)
- 非技术人员可自己配置
- 一次性或临时集成需求

## 限制和注意事项
- 免费版单步Zap+100 tasks/月(不够用)
- 复杂逻辑能力有限(无循环/复杂条件)
- 每个动作算1个task(成本可能高)
- 部分高级功能需Professional版($73.50/月)
- 调试和错误处理不如代码灵活
- 多步Zap执行可能较慢(几秒到几分钟)

## 定价
- Free版: 100 tasks/月,单步Zap,5分钟检查间隔
- Starter: $29.99/月 - 750 tasks,多步Zap,15分钟间隔
- Professional: $73.50/月 - 2000 tasks,高级功能,2分钟间隔
- Team: $103.50/月 - 50,000 tasks,无限用户,1分钟间隔
- Company: 需联系销售 - 高级安全和支持

## 参考链接
- [官方文档](https://platform.zapier.com/)
- [Zap模板库](https://zapier.com/apps/categories/crm)
- [Webhook指南](https://zapier.com/page/webhooks/)
- [API参考](https://platform.zapier.com/reference/cli-docs)
