# Spiff API

> 佣金自动化平台(CaptivateIQ竞品),侧重实时佣金可见和游戏化

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `SPIFF_API_KEY` — Spiff API密钥
- **获取步骤:**
  1. 登录 Spiff
  2. Settings → API Keys
  3. Generate New Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SPIFF_API_KEY" \
    https://api.spiff.com/v1/users/current
  ```

## 核心能力

### 实时佣金计算和显示
- 交易完成→佣金立即计算
- 销售Dashboard实时更新
- 移动App推送通知
- 激励即时反馈(完成Deal立刻看到佣金)

### No-code佣金规则引擎
- 可视化配置佣金规则(无需代码)
- 拖拽式规则构建器
- 复杂逻辑支持(if-then-else)
- 公式计算器
- 规则模板库

### 游戏化和排行榜
- 实时排行榜(个人/团队)
- 徽章和成就系统
- 竞赛和挑战
- 目标进度条
- 社交分享(庆祝成交)

### 佣金规则类型
- 基础佣金比例
- 分层佣金(阶梯式)
- 加速器(超额奖励)
- SPIFFs(临时激励活动,如"本月卖X产品额外奖励")
- MBO(Management By Objectives,目标奖金)

### 团队协作和Split
- 多人分成(AE+SDR+SE)
- 自定义分成比例
- 自动分配规则
- 回溯调整

### 与CRM和ERP集成
- Salesforce/HubSpot/Pipedrive集成
- NetSuite/QuickBooks财务集成
- 实时数据同步
- 自定义字段映射

### 佣金争议管理
- 在线提交争议
- 工作流审批
- 调整历史记录
- 通知和提醒

## 使用示例

### 获取用户佣金摘要
```bash
USER_ID="user123"

curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/users/$USER_ID/commissions/summary?period=current" | jq
```

### 创建佣金记录
```bash
curl -X POST https://api.spiff.com/v1/commissions \
  -H "Authorization: Bearer $SPIFF_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "user123",
    "deal_id": "deal456",
    "deal_name": "科技公司-企业版",
    "amount": 50000,
    "commission_plan": "enterprise-plan",
    "effective_date": "2026-04-07"
  }' | jq
```

### 查询排行榜
```bash
curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/leaderboards?period=2026-Q2&metric=total_commission&limit=10" | jq
```

### 创建临时激励活动(SPIFF)
```bash
curl -X POST https://api.spiff.com/v1/spiffs \
  -H "Authorization: Bearer $SPIFF_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "四月冲刺-新产品推广",
    "description": "本月每签约1个新产品额外奖励$500",
    "start_date": "2026-04-01",
    "end_date": "2026-04-30",
    "rules": {
      "product_filter": "新产品A",
      "bonus_per_deal": 500
    },
    "target_users": ["team-sales-west"]
  }' | jq
```

### 获取佣金计划配置
```bash
curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/plans" | jq
```

### 查询佣金争议
```bash
curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/disputes?status=pending" | jq
```

### 同步Salesforce交易
```bash
curl -X POST https://api.spiff.com/v1/integrations/salesforce/sync \
  -H "Authorization: Bearer $SPIFF_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sync_closed_won_deals": true,
    "date_from": "2026-04-01"
  }' | jq
```

### 导出佣金报表
```bash
curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/reports/commissions?period=2026-Q2&format=csv" \
  -o "佣金报表-2026Q2.csv"
```

### 获取实时通知设置
```bash
curl -H "Authorization: Bearer $SPIFF_API_KEY" \
  "https://api.spiff.com/v1/notifications/settings" | jq
```

## 适用场景
- 想让销售实时看到佣金(激励效果更好)
- 需要游戏化激励(排行榜/徽章/竞赛)
- 频繁运行临时激励活动(SPIFFs)
- RevOps需要no-code配置佣金规则(不依赖工程师)
- 与CaptivateIQ二选一(功能类似,Spiff偏游戏化)

## 限制和注意事项
- 企业版定价,需联系销售
- API限流: 100次/分钟
- 复杂规则配置需要培训
- 主要面向美国市场
- 游戏化可能不适合所有企业文化

## 定价
- 企业定制定价,需联系销售
- 一般起步价: $25,000-50,000/年
- 按销售人数和交易量定价

## 参考链接
- [官方文档](https://developer.spiff.com/)
- [API参考](https://developer.spiff.com/api-reference)
- [佣金最佳实践](https://www.spiff.com/resources/commission-plan-design)
