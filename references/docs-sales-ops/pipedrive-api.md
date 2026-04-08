<!-- doc-level: standard -->

# Pipedrive API

销售管线管理CRM,专注可视化Pipeline和简洁易用性,适合中小型销售团队快速上手。

## 快速开始

### 认证配置
- **方式:** api_key
- **环境变量:** `PIPEDRIVE_API_TOKEN`
- **获取步骤:**
  1. 登录 Pipedrive → Settings → Personal preferences → API
  2. Generate new token → 复制保存
- **验证:**
  ```bash
  curl "https://api.pipedrive.com/v1/users/me?api_token=$PIPEDRIVE_API_TOKEN"
  ```

## 核心能力
- Deal和Pipeline管理(可视化看板)
- Person和Organization管理
- Activity计划和邮件集成
- Workflow自动化(需Professional版)
- 销售报表和Dashboard

## 使用示例

### 创建Deal
```bash
curl -X POST \
  "https://api.pipedrive.com/v1/deals?api_token=$PIPEDRIVE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "新客户项目",
    "value": 50000,
    "currency": "CNY"
  }'
```

### 更新Deal阶段
```bash
curl -X PUT \
  "https://api.pipedrive.com/v1/deals/789?api_token=$PIPEDRIVE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"stage_id": 3}'
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
    "due_date": "2026-04-10"
  }'
```

## 适用场景
- 中小型销售团队需要简单易用的CRM
- 重视Pipeline可视化和直观操作
- 预算有限但需要完整CRM功能

## 限制和注意事项
- API限流: 100次/10秒
- 高级自动化需Professional版以上
- 企业级功能有限(无CPQ/高级预测)

## 定价
- Essential: $14/用户/月
- Advanced: $34/用户/月
- Professional: $49/用户/月
- Enterprise: $99/用户/月

## 参考链接
- [API文档](https://developers.pipedrive.com/docs/api/v1)
