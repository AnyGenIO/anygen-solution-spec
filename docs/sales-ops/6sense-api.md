# 6sense API

> ABM平台,AI预测账户购买意图(基于匿名网站访问/内容消费行为)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `SIXSENSE_API_KEY` — 6sense API密钥
- **获取步骤:**
  1. 联系6sense销售购买企业版
  2. 登录6sense平台
  3. Settings → API Access
  4. Generate API Key(需管理员权限)
  5. 复制保存密钥
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SIXSENSE_API_KEY" \
    https://api.6sense.com/v1/accounts?limit=1
  ```

## 核心能力

### 匿名访客识别
- 识别访问网站的公司(即使未填表单)
- IP地址反查公司信息
- 访问页面和停留时间
- 内容消费行为分析
- 访问频次和深度

### AI购买阶段预测
- Awareness(意识阶段) - 刚开始了解问题
- Consideration(考虑阶段) - 正在对比解决方案
- Decision(决策阶段) - 即将做出购买决定
- Purchase(购买阶段) - 已购买或签约
- 阶段变化趋势

### Intent Score(意图评分)
- 账户购买意图评分(0-100)
- 评分变化趋势
- 突然升高的高意图账户
- 意图衰减预警
- 细分产品类别的意图

### 账户推荐
- 优先跟进的账户列表
- Lookalike账户(类似已成交客户)
- 流失风险账户
- 扩展机会账户
- 自定义评分模型

### 广告投放平台
- 针对高意图账户投放广告
- LinkedIn/Google/Facebook定向投放
- 广告ROI追踪
- 再营销(Retargeting)
- 账户级别广告归因

### CRM集成
- 与Salesforce/HubSpot双向同步
- 意图数据自动更新到CRM
- 账户优先级排序
- 销售提醒和任务分配

## 使用示例

### 获取高意图账户列表
```bash
curl -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  "https://api.6sense.com/v1/accounts?intent_score_min=70&stage=Decision&limit=50" | jq
```

### 查询特定账户的购买阶段
```bash
ACCOUNT_ID="abc123"

curl -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  "https://api.6sense.com/v1/accounts/$ACCOUNT_ID" | jq '.buying_stage, .intent_score'
```

### 获取账户的访问活动
```bash
curl -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  "https://api.6sense.com/v1/accounts/$ACCOUNT_ID/activities?date_from=2026-03-01&date_to=2026-04-07" | jq
```

### 查询正在研究特定主题的账户
```bash
curl -X POST https://api.6sense.com/v1/segments/query \
  -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "filters": {
      "keywords": ["CRM", "Sales Automation"],
      "buying_stage": ["Consideration", "Decision"],
      "intent_score": {"min": 60}
    },
    "limit": 100
  }' | jq
```

### 获取账户意图变化趋势
```bash
curl -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  "https://api.6sense.com/v1/accounts/$ACCOUNT_ID/intent-history?days=30" | jq
```

### 创建账户列表(Segment)
```bash
curl -X POST https://api.6sense.com/v1/segments \
  -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "高意图企业客户-Q2",
    "description": "Intent Score>75且处于Decision阶段的企业客户",
    "filters": {
      "intent_score": {"min": 75},
      "buying_stage": ["Decision"],
      "employee_count": {"min": 100}
    }
  }' | jq
```

### Webhook配置(意图变化通知)
```bash
curl -X POST https://api.6sense.com/v1/webhooks \
  -H "Authorization: Bearer $SIXSENSE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/6sense-webhook",
    "events": [
      "account.intent_score_increased",
      "account.buying_stage_changed"
    ],
    "filters": {
      "intent_score_threshold": 70
    }
  }' | jq
```

## 适用场景
- ABM策略 - 优先攻大客户(基于意图数据)
- 发现谁在默默研究你(还没填表单就知道了)
- 销售优先级排序(先打意图高的账户)
- Marketing和Sales对齐(都看同一个意图数据)
- 缩短销售周期(在客户准备好时介入)
- 流失预警(已有客户意图降低)

## 限制和注意事项
- 仅企业版,价格昂贵(需联系销售)
- 需要网站流量达到一定规模才有效(建议月访客>10k)
- 主要覆盖B2B SaaS场景,B2C效果不佳
- 数据准确性依赖IP反查(小公司可能识别不准)
- 隐私合规(GDPR/CCPA)需注意
- API限流根据套餐不同
- 需要至少3-6个月数据积累才能看到效果

## 定价
- 企业定制定价,需联系销售
- 一般起步价: $50,000-150,000/年
- 按网站流量和账户数量定价
- API访问包含在企业版中
- 广告投放平台需额外付费

## 参考链接
- [官方文档](https://support.6sense.com/hc/en-us/categories/360002897391-APIs)
- [API参考](https://developers.6sense.com/)
- [ABM最佳实践](https://6sense.com/platform/account-based-marketing/)
- [Intent Data说明](https://6sense.com/platform/intent-data/)
