# Clari API

> Revenue Operations平台,AI预测收入/管道健康度/Deal风险

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `CLARI_CLIENT_ID` — OAuth Client ID
  - `CLARI_CLIENT_SECRET` — OAuth Client Secret
  - `CLARI_ACCESS_TOKEN` — OAuth访问令牌
- **获取步骤:**
  1. 登录 Clari Dashboard
  2. Settings → Integrations → API Access
  3. Create OAuth Application
  4. 复制 Client ID 和 Client Secret
  5. 通过 OAuth 2.0 流程获取 Access Token
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
    https://api.clari.com/v4/health
  ```

## 核心能力
- AI预测本季度/全年ARR达成率
- Deal健康度评分(活动频率/Stage停留时间)
- Pipeline覆盖率分析(需要3x才能完成目标)
- 自动识别风险Deal(突然无活动/对方不回)
- 销售团队活动分析
- 预测vs实际对比
- 与CRM实时同步(Salesforce/Dynamics)
- 趋势分析和异常检测

## 使用示例

### 获取季度预测
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  "https://api.clari.com/v4/forecasts?quarter=Q2&year=2026"
```

### 获取Pipeline覆盖率
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  "https://api.clari.com/v4/pipeline/coverage?quarter=Q2&year=2026"
```

### 获取风险Deal列表
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  "https://api.clari.com/v4/deals/at-risk?threshold=high"
```

### 获取Deal健康度评分
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  https://api.clari.com/v4/deals/{deal_id}/health
```

### 获取销售团队活动统计
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  "https://api.clari.com/v4/activities?userId={user_id}&startDate=2026-04-01&endDate=2026-04-07"
```

### 提交预测(Commit/Best Case/Pipeline)
```bash
curl -X POST \
  -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.clari.com/v4/forecasts/submit \
  -d '{
    "quarter": "Q2",
    "year": 2026,
    "commit": 5000000,
    "bestCase": 6000000,
    "pipeline": 7500000,
    "userId": "user_12345"
  }'
```

### 获取预测准确率分析
```bash
curl -H "Authorization: Bearer $CLARI_ACCESS_TOKEN" \
  "https://api.clari.com/v4/forecasts/accuracy?quarter=Q1&year=2026"
```

## 适用场景
- VP Sales要看季度收入预测准确率
- 提前2周发现Deal有风险
- 判断Pipeline是否足够(3x覆盖率)
- RevOps分析团队效率
- 自动化预测流程(减少手动更新)
- 季度业务回顾(QBR)数据准备

## 限制和注意事项
- 仅企业版(中小企业通常用不上)
- 需CRM数据质量高(GIGO原则 - Garbage In, Garbage Out)
- API限流: 根据套餐不同(估算120次/分钟)
- AI预测需要至少1-2个季度历史数据训练
- 主要适配Salesforce和Dynamics CRM
- 实时数据同步延迟约5-15分钟
- 需要RevOps团队配置和维护

## 定价
- 企业版定价需联系销售
- 估算: $1,500-3,000/用户/年起
- 通常按席位+功能模块计费

## 参考链接
- [官方文档](https://developer.clari.com/)
- [API参考](https://developer.clari.com/api-reference)
- [集成指南](https://support.clari.com/hc/en-us/categories/360002280154-Integrations)
