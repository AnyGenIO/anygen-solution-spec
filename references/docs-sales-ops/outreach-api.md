<!-- doc-level: detailed -->

# Outreach API

> 销售自动化平台API,序列化邮件/电话/LinkedIn触达

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `OUTREACH_CLIENT_ID` — OAuth应用Client ID
  - `OUTREACH_CLIENT_SECRET` — OAuth应用Client Secret
  - `OUTREACH_ACCESS_TOKEN` — OAuth访问令牌
- **获取步骤:**
  1. 登录 https://app.outreach.io
  2. Settings → API → OAuth Applications
  3. Create new OAuth application
  4. 配置 Redirect URI
  5. 通过 OAuth 2.0 流程获取 Access Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
    https://api.outreach.io/api/v2/users
  ```

## 核心能力
- 创建和管理触达序列(Sequence - 多步骤自动化)
- 邮件模板管理
- 任务分配和跟进提醒
- 通话记录和分析
- 与CRM双向同步(Salesforce/HubSpot)
- 邮件打开/点击追踪
- Prospect(联系人)管理
- A/B测试(邮件主题/正文)

## 使用示例

### 获取所有Sequences
```bash
curl -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  https://api.outreach.io/api/v2/sequences
```

### 创建Prospect(联系人)
```bash
curl -X POST \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.outreach.io/api/v2/prospects \
  -d '{
    "data": {
      "type": "prospect",
      "attributes": {
        "firstName": "张",
        "lastName": "三",
        "emails": ["zhang@example.com"],
        "company": "科技公司",
        "title": "VP Sales"
      }
    }
  }'
```

### 将Prospect加入Sequence
```bash
curl -X POST \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.outreach.io/api/v2/sequenceStates \
  -d '{
    "data": {
      "type": "sequenceState",
      "relationships": {
        "prospect": {
          "data": {"type": "prospect", "id": "123"}
        },
        "sequence": {
          "data": {"type": "sequence", "id": "456"}
        }
      }
    }
  }'
```

### 查询邮件统计
```bash
curl -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  "https://api.outreach.io/api/v2/mailings?filter[prospectId]=123"
```

### 创建Task(手动跟进任务)
```bash
curl -X POST \
  -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.outreach.io/api/v2/tasks \
  -d '{
    "data": {
      "type": "task",
      "attributes": {
        "action": "call",
        "note": "跟进Demo后的反馈",
        "dueAt": "2026-04-10T10:00:00Z"
      },
      "relationships": {
        "prospect": {
          "data": {"type": "prospect", "id": "123"}
        }
      }
    }
  }'
```

### 获取通话记录
```bash
curl -H "Authorization: Bearer $OUTREACH_ACCESS_TOKEN" \
  "https://api.outreach.io/api/v2/calls?filter[prospectId]=123"
```

## 适用场景
- 设计多步骤触达序列(Day1邮件→Day3跟进→Day7电话)
- 批量个性化邮件发送
- 跟踪触达效果(打开率/回复率)
- 自动化跟进提醒
- 团队协作(SDR→AE无缝切换)
- A/B测试最佳邮件话术

## 限制和注意事项
- 付费工具,价格较高(起步$100/用户/月)
- API限流: 10,000次/小时/用户
- 邮件发送受Gmail/Outlook日限额限制
- Sequence步骤数有上限(具体看套餐)
- 需要邮箱预热(避免进垃圾箱)
- 不包含联系人数据库(需配合Apollo/ZoomInfo)

## 定价
- 起步价: $100/用户/月(需联系销售)
- 企业版: 需定制报价
- 通常与Salesforce等CRM捆绑购买

## 参考链接
- [官方文档](https://api.outreach.io/api/v2/docs)
- [API参考](https://api.outreach.io/api/v2/docs)
- [开发者指南](https://developers.outreach.io/)
- [集成文档](https://support.outreach.io/hc/en-us/categories/360001288613-Integrations)
