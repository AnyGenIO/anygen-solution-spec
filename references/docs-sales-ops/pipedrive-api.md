# Pipedrive API

> 销售管线管理CRM,专注可视化Pipeline和简洁易用(中小团队首选)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `PIPEDRIVE_API_TOKEN` — Pipedrive API令牌
- **获取步骤:**
  1. 登录 Pipedrive
  2. Settings → Personal preferences → API
  3. Generate new token → 复制保存
- **验证:**
  ```bash
  curl "https://api.pipedrive.com/v1/users/me?api_token=$PIPEDRIVE_API_TOKEN"
  ```

## 核心能力

### Deal和Pipeline管理
- Deal CRUD(创建/更新/移动Stage)
- 自定义Pipeline和Stage配置
- Deal可视化看板(拖拽移动Stage)
- Deal概率和加权预测
- 活动关联(Email/Call/Meeting记录)

### 联系人和组织
- Person和Organization管理
- 自定义字段
- 数据去重

### 活动和跟进
- Activity计划和提醒
- 邮件集成(Gmail/Outlook双向同步)
- 通话记录

### 自动化
- Workflow自动化(触发器-动作,需Professional版)
- 邮件模板和序列(需Professional版)

### 报表和分析
- 销售报表和Dashboard
- 活动分析(邮件/电话/会议统计)
- 团队表现对比

### 集成
- Zapier/Make集成(2000+应用)
- Marketplace(100+原生集成)

## 使用示例

### 查询所有Deal
```bash
curl "https://api.pipedrive.com/v1/deals?api_token=$PIPEDRIVE_API_TOKEN&limit=10"
```

### 创建新Deal
```bash
curl -X POST \
  "https://api.pipedrive.com/v1/deals?api_token=$PIPEDRIVE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "新客户项目",
    "value": 50000,
    "currency": "CNY",
    "person_id": 123,
    "org_id": 456
  }'
```

### 更新Deal Stage
```bash
curl -X PUT \
  "https://api.pipedrive.com/v1/deals/789?api_token=$PIPEDRIVE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "stage_id": 3
  }'
```

### 搜索联系人
```bash
curl "https://api.pipedrive.com/v1/persons/search?term=张三&api_token=$PIPEDRIVE_API_TOKEN"
```

### 添加活动记录
```bash
curl -X POST \
  "https://api.pipedrive.com/v1/activities?api_token=$PIPEDRIVE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "subject": "跟进电话",
    "type": "call",
    "deal_id": 789,
    "due_date": "2026-04-10",
    "due_time": "14:00",
    "duration": "00:30",
    "note": "讨论了价格和实施时间表"
  }'
```

## 适用场景
- 中小型销售团队(<100人)不想Salesforce的复杂度
- 重视Pipeline可视化和直观操作
- 快速上手CRM(培训成本低)
- 预算有限但需要完整功能

## 限制和注意事项
- API限流: 100次/10秒(企业版更高)
- 高级自动化需Professional版以上
- 企业级功能不如Salesforce/Dynamics(无CPQ/高级预测)
- 报表自定义能力有限

## 定价
- Essential: $14/用户/月
- Advanced: $34/用户/月
- Professional: $49/用户/月
- Enterprise: $99/用户/月

## 参考链接
- [官方文档](https://developers.pipedrive.com/docs/api/v1)
- [API参考](https://developers.pipedrive.com/docs/api/v1/APIReference)
