# Proposify API

> 专注提案管理,模板化生成销售提案和报价

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `PROPOSIFY_API_KEY` — Proposify API密钥
- **获取步骤:**
  1. 登录 Proposify
  2. Settings → Integrations → API
  3. Generate API Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
    https://api.proposify.com/api/v1/users/current
  ```

## 核心能力

### 提案模板管理
- 提案模板库
- 可视化模板编辑器
- 品牌元素管理(Logo/颜色/字体)
- 内容块复用
- 模板版本控制

### 从CRM自动生成提案
- 从Salesforce/HubSpot/Pipedrive拉数据
- 自动填充客户信息
- 产品和定价表导入
- 自定义字段映射
- 批量生成提案

### 费用表和定价
- 动态定价选项
- 可选/必选产品配置
- 折扣规则
- 税费计算
- 多币种支持

### 提案分析
- 打开率追踪
- 阅读时长统计
- 页面停留时间
- 查看次数
- 转化率分析

### 内容库管理
- 案例研究库
- 产品介绍片段
- 客户证言
- 图表和图片库
- 一键插入复用内容

### 协作和审批
- 团队协作编辑
- 审批工作流
- 评论和反馈
- 版本对比

## 使用示例

### 获取所有提案模板
```bash
curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  https://api.proposify.com/api/v1/templates | jq
```

### 从模板创建提案
```bash
curl -X POST https://api.proposify.com/api/v1/proposals \
  -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "template_id": 12345,
    "name": "企业版提案-科技公司",
    "contact": {
      "first_name": "张",
      "last_name": "总",
      "email": "zhang@techcorp.com",
      "company": "科技公司"
    },
    "fields": {
      "deal_amount": "50000",
      "start_date": "2026-05-01",
      "duration": "12个月"
    }
  }' | jq
```

### 发送提案给客户
```bash
PROPOSAL_ID=67890

curl -X POST https://api.proposify.com/api/v1/proposals/$PROPOSAL_ID/send \
  -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "to": "zhang@techcorp.com",
    "subject": "您的专属企业版提案",
    "message": "您好张总,附上我们为贵司定制的提案,请查阅。"
  }' | jq
```

### 查询提案状态
```bash
curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  "https://api.proposify.com/api/v1/proposals/$PROPOSAL_ID" | jq '.status, .viewed_at, .view_count'
```

### 获取提案分析数据
```bash
curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  "https://api.proposify.com/api/v1/proposals/$PROPOSAL_ID/analytics" | jq
```

### 更新提案内容
```bash
curl -X PATCH https://api.proposify.com/api/v1/proposals/$PROPOSAL_ID \
  -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "fields": {
      "deal_amount": "45000",
      "discount": "10%"
    }
  }' | jq
```

### 列出所有提案(筛选已发送)
```bash
curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  "https://api.proposify.com/api/v1/proposals?status=sent&limit=50" | jq
```

### 导出提案为PDF
```bash
curl -H "Authorization: Bearer $PROPOSIFY_API_KEY" \
  "https://api.proposify.com/api/v1/proposals/$PROPOSAL_ID/download" \
  -o "提案-科技公司.pdf"
```

## 适用场景
- 专注提案环节(不需要签署和支付功能)
- 标准化提案流程(模板化复用)
- 需要提案分析(客户看了哪些页面)
- 团队协作编辑提案
- 从CRM自动生成提案(减少手工复制粘贴)
- 内容库管理(案例/产品介绍复用)

## 限制和注意事项
- 不含电子签名功能(需集成DocuSign)
- 功能不如PandaDoc全面(无支付集成)
- API限流: 60次/分钟
- 免费试用14天,之后必须付费
- 模板设计需要一定学习曲线
- 主要面向代理公司和咨询公司

## 定价
- Team: $49/用户/月 - 基础功能,5个模板
- Business: $590/月(5用户起) - 无限模板,API访问,CRM集成
- Enterprise: 需联系销售 - 高级自动化,SSO,专属支持

## 参考链接
- [官方文档](https://docs.proposify.com/api/)
- [API参考](https://docs.proposify.com/api/reference/)
- [集成指南](https://docs.proposify.com/api/integrations/)
