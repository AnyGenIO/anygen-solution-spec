# Salesforce CLI (sf)

> Salesforce 官方命令行,管理org/数据/部署,业务操作走REST API(覆盖Sales/Service/Marketing Cloud全生态)

## 快速开始

### 安装
```bash
npm install -g @salesforce/cli
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `SF_ACCESS_TOKEN` — Salesforce访问令牌
  - `SF_INSTANCE_URL` — 组织URL(如 https://yourorg.my.salesforce.com)
- **获取步骤:**
  1. 登录 Salesforce → Setup
  2. 搜索 "Apps" → App Manager → New Connected App
  3. 启用 OAuth Settings → 勾选 Enable OAuth Settings
  4. 配置 Callback URL 和 OAuth Scopes(如 api, refresh_token)
  5. 保存后获取 Consumer Key 和 Consumer Secret
  6. 使用 OAuth 流程获取 Access Token
- **验证:**
  ```bash
  sf org list
  ```

## 核心能力

### Sales Cloud 核心
- SOQL 查询(Lead/Contact/Opportunity/Account/Task/Event 全对象)
- 记录CRUD(创建/更新Lead、推进Opportunity Stage、记录Activity)
- 报表和Dashboard导出
- 数据批量导入导出(Bulk API 2.0,百万级数据)
- 重复数据检测和合并

### Service Cloud
- Case管理(工单创建/分配/升级)
- Knowledge Base文章操作
- Omni-Channel路由配置

### Marketing Cloud(需单独授权)
- Journey Builder自动化流程
- Email Studio邮件发送和追踪
- Audience Studio(CDP)数据管理

### 自动化和AI
- Flow自动化(可视化工作流)
- Einstein AI(Lead/Opportunity Scoring)
- Apex代码部署(自定义逻辑)

### 扩展和集成
- AppExchange应用安装(CPQ/DocuSign/Gong等3000+应用)
- REST/SOAP API调用(几乎所有功能可编程)
- 元数据管理(部署自定义对象/字段/Flow)
- Sandbox管理(测试环境)

## 使用示例

### 查询所有未关闭的商机
```bash
sf data query --query "SELECT Name, StageName, Amount FROM Opportunity WHERE IsClosed = false"
```

### 创建新的Lead
```bash
sf data create record --sobject Lead --values "FirstName='张' LastName='三' Company='科技公司' Email='zhang@example.com'"
```

### 批量导入数据
```bash
# 准备CSV文件 leads.csv
sf data import bulk --sobject Lead --file leads.csv --wait 10
```

### 推进商机阶段
```bash
sf data update record --sobject Opportunity --record-id 0061234567890ABC --values "StageName='Proposal/Price Quote'"
```

### 导出报表数据
```bash
sf data query --query "SELECT Id, Name, Email, LeadSource FROM Lead WHERE CreatedDate = THIS_MONTH" --result-format csv > leads_this_month.csv
```

## 适用场景
- 查询/更新CRM中的线索、商机、客户
- 批量导入线索(CSV/Excel→Salesforce)
- 推进商机阶段(Stage),记录通话/会议Activity
- 拉漏斗报表和预测
- 自动化复杂业务流程(Flow/Apex)
- 与其他系统集成(双向数据同步)

## 限制和注意事项
- API调用日限额:
  - Professional: 1000次/天
  - Enterprise: 10万次/天
  - Unlimited: 25万次/天
- Bulk API单批1万条记录
- 数据存储按版本限额(Enterprise 120MB+1GB/用户)
- Marketing Cloud需单独订阅

## 定价
- Professional: $80/用户/月
- Enterprise: $165/用户/月
- Unlimited: $330/用户/月

## 参考链接
- [官方文档](https://developer.salesforce.com/docs/)
- [CLI参考](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/)
- [API参考](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/)
