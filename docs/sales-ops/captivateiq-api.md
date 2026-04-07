# CaptivateIQ API

> 销售佣金自动计算平台,处理复杂佣金规则(加速器/分层/团队split)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `CAPTIVATEIQ_API_KEY` — CaptivateIQ API密钥
- **获取步骤:**
  1. 登录 CaptivateIQ
  2. Settings → API Access(需管理员权限)
  3. Generate API Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
    https://api.captivateiq.com/v1/users/me
  ```

## 核心能力

### 复杂佣金规则配置
- 多维度规则(产品/区域/客户类型/交易类型)
- 分层佣金(不同金额档位不同比例)
- 加速器(超额完成奖励,如120%以上额外奖励)
- 团队split(AE+SDR分成,可配置比例)
- 回溯调整(历史佣金修正)

### 实时佣金计算
- 从CRM拉取交易数据
- 自动应用佣金规则
- 实时计算应得佣金
- 预估佣金(未关单但有可能)
- 佣金报表生成

### What-if分析
- 调整佣金计划模拟影响
- 对比不同方案
- 成本预测
- ROI分析
- 激励效果预测

### 销售Dashboard
- 销售实时查看佣金明细
- 每笔交易的佣金拆解
- 目标vs实际对比
- 排行榜和竞赛
- 历史佣金趋势

### 与CRM和财务系统集成
- Salesforce/HubSpot双向同步
- NetSuite/QuickBooks财务集成
- 自动拉取交易数据
- 佣金发放后回写状态

### 审批和争议管理
- 佣金审批工作流
- 争议提交和追踪
- 调整历史记录
- 审计日志

## 使用示例

### 获取销售人员列表
```bash
curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  "https://api.captivateiq.com/v1/users?role=sales" | jq
```

### 查询某销售的佣金明细
```bash
USER_ID="user123"
PERIOD="2026-Q2"

curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  "https://api.captivateiq.com/v1/commissions?user_id=$USER_ID&period=$PERIOD" | jq
```

### 创建佣金记录(手动调整)
```bash
curl -X POST https://api.captivateiq.com/v1/commissions \
  -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "user123",
    "period": "2026-Q2",
    "deal_id": "deal456",
    "deal_amount": 50000,
    "commission_amount": 5000,
    "commission_rate": 0.10,
    "note": "新客户签约,标准10%佣金"
  }' | jq
```

### 获取佣金计划配置
```bash
curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  "https://api.captivateiq.com/v1/plans" | jq
```

### 同步CRM交易数据
```bash
curl -X POST https://api.captivateiq.com/v1/integrations/salesforce/sync \
  -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "object_type": "Opportunity",
    "sync_date_from": "2026-04-01",
    "sync_date_to": "2026-04-07"
  }' | jq
```

### 查询佣金争议
```bash
curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  "https://api.captivateiq.com/v1/disputes?status=open" | jq
```

### 导出佣金报表
```bash
PERIOD="2026-Q2"

curl -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  "https://api.captivateiq.com/v1/reports/commissions?period=$PERIOD&format=csv" \
  -o "佣金报表-2026Q2.csv"
```

### 批量更新佣金状态(标记为已支付)
```bash
curl -X PATCH https://api.captivateiq.com/v1/commissions/bulk \
  -H "Authorization: Bearer $CAPTIVATEIQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "commission_ids": ["comm1", "comm2", "comm3"],
    "status": "paid",
    "payment_date": "2026-04-15"
  }' | jq
```

## 适用场景
- 佣金规则复杂(分层/加速器/quota达成率/多产品)
- 销售团队超过20人(手工计算易错)
- 每月手工算佣金太慢(自动化节省RevOps时间)
- 需要激励透明度(销售实时看到自己的佣金)
- 频繁调整佣金计划(What-if分析影响)
- 跨团队分成(SDR+AE, AE+SE, CSM续约分成)

## 限制和注意事项
- 仅企业版,价格较高(需联系销售)
- 配置需要专人(RevOps或Finance)
- 学习曲线陡峭(复杂规则需培训)
- API限流: 100次/分钟
- 历史数据迁移需要专业服务
- 主要面向美国企业(合规/税务逻辑)

## 定价
- 企业定制定价,需联系销售
- 一般起步价: $30,000-60,000/年
- 按销售人数和交易量定价
- 实施和培训服务额外收费

## 参考链接
- [官方文档](https://support.captivateiq.com/hc/en-us/categories/4408979449491-API)
- [API参考](https://developers.captivateiq.com/)
- [佣金最佳实践](https://www.captivateiq.com/resources/commission-plan-examples)
