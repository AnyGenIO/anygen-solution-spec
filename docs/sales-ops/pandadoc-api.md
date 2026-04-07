# PandaDoc API

> 提案+报价+电子签一体化平台(DocuSign竞品+提案功能)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `PANDADOC_API_KEY` — PandaDoc API密钥
- **获取步骤:**
  1. 登录 PandaDoc
  2. Settings → API
  3. Generate Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: API-Key $PANDADOC_API_KEY" \
    https://api.pandadoc.com/public/v1/members/current
  ```

## 核心能力

### 提案和文档管理
- 提案模板管理(拖拽编辑器)
- 从模板创建文档
- 动态定价表(从CRM拉数据自动填充)
- 产品目录管理
- 文档版本控制

### 电子签名
- 发送签署请求
- 多方签署(顺序/并行)
- 签署提醒和催办
- 签署状态追踪
- 已签文档下载

### 文档追踪
- 谁打开了文档
- 阅读时长和次数
- 具体看了哪些页面
- 停留时间最长的部分
- 实时查看通知

### 支付集成
- Stripe支付集成
- 在文档内直接收款
- 报价→签署→付款一站式
- 分期付款配置

### CRM集成
- Salesforce/HubSpot/Pipedrive双向同步
- 商机关联
- 自动创建文档(从CRM触发)
- 签署完成自动更新CRM状态

### 文档分析
- 转化率分析(发送→查看→签署)
- 平均签署时间
- 模板效果对比
- 客户行为洞察

## 使用示例

### 从模板创建文档
```bash
curl -X POST https://api.pandadoc.com/public/v1/documents \
  -H "Authorization: API-Key $PANDADOC_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "销售提案-科技公司-Q2",
    "template_uuid": "aBcDeFgH1234567890",
    "recipients": [
      {
        "email": "cto@techcorp.com",
        "first_name": "张",
        "last_name": "总",
        "role": "Signer"
      }
    ],
    "tokens": [
      {"name": "Company.Name", "value": "科技公司"},
      {"name": "Deal.Amount", "value": "50000"},
      {"name": "Discount", "value": "15%"}
    ],
    "pricing_tables": [
      {
        "name": "产品清单",
        "sections": [
          {
            "rows": [
              {
                "options": {
                  "product": "企业版订阅",
                  "qty": 50,
                  "price": 1000
                }
              }
            ]
          }
        ]
      }
    ]
  }'
```

### 发送文档签署请求
```bash
DOCUMENT_ID="abc123def456"

curl -X POST https://api.pandadoc.com/public/v1/documents/$DOCUMENT_ID/send \
  -H "Authorization: API-Key $PANDADOC_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "请查看并签署我们的提案",
    "subject": "Q2企业版提案-科技公司",
    "silent": false
  }'
```

### 查询文档状态
```bash
curl -H "Authorization: API-Key $PANDADOC_API_KEY" \
  "https://api.pandadoc.com/public/v1/documents/$DOCUMENT_ID" | jq '.status, .recipients[].status'
```

### 获取文档查看统计
```bash
curl -H "Authorization: API-Key $PANDADOC_API_KEY" \
  "https://api.pandadoc.com/public/v1/documents/$DOCUMENT_ID/analytics" | jq
```

### 下载已签署文档
```bash
curl -H "Authorization: API-Key $PANDADOC_API_KEY" \
  "https://api.pandadoc.com/public/v1/documents/$DOCUMENT_ID/download" \
  -o "已签署提案.pdf"
```

### 创建Webhook(文档状态变化)
```bash
curl -X POST https://api.pandadoc.com/public/v1/webhooks \
  -H "Authorization: API-Key $PANDADOC_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/pandadoc-webhook",
    "events": [
      "document_viewed",
      "document_completed",
      "recipient_completed"
    ],
    "shared_key": "your-secret-key-for-verification"
  }'
```

### 批量创建文档(从CSV)
```bash
# 准备CSV: name,email,company,amount
# 逐行创建文档
while IFS=, read -r name email company amount; do
  curl -X POST https://api.pandadoc.com/public/v1/documents \
    -H "Authorization: API-Key $PANDADOC_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{
      "name": "提案-'$company'",
      "template_uuid": "template-id",
      "recipients": [{"email": "'$email'", "first_name": "'$name'", "role": "Signer"}],
      "tokens": [
        {"name": "Company.Name", "value": "'$company'"},
        {"name": "Deal.Amount", "value": "'$amount'"}
      ]
    }'
  sleep 1
done < prospects.csv
```

## 适用场景
- 需要精美提案文档(不只是合同)
- 想知道客户是否真的读了提案(行为追踪)
- 提案→报价→签署→收款全流程一站式
- 标准化提案流程(复用模板)
- 缩短签署周期(电子签比纸质快10倍)
- 销售需要实时提醒(客户刚打开提案)

## 限制和注意事项
- API限流: 300次/分钟
- 免费版功能受限(无API访问)
- 文档模板需要在Web界面设计
- 电子签名在某些国家/行业可能不被认可
- 文件大小限制: 25MB/文档
- Webhook需要HTTPS端点

## 定价
- Essentials: $19/用户/月 - 基础功能,无API
- Business: $49/用户/月 - API访问,CRM集成,支付集成
- Enterprise: 需联系销售 - 高级自动化,SSO,专属支持

## 参考链接
- [官方文档](https://developers.pandadoc.com/)
- [API参考](https://developers.pandadoc.com/reference/about)
- [Webhook指南](https://developers.pandadoc.com/reference/webhooks)
- [集成示例](https://developers.pandadoc.com/recipes)
