# Gong API

> 会话智能平台,录音转文字+AI分析(竞争对手提及/客户痛点/风险信号)

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** api_key (Basic Auth)
- **环境变量:**
  - `GONG_ACCESS_KEY` — Access Key
  - `GONG_ACCESS_KEY_SECRET` — Access Key Secret
- **获取步骤:**
  1. 登录 Gong Dashboard
  2. Settings → API → Create Access Key
  3. 复制 Access Key 和 Access Key Secret
- **验证:**
  ```bash
  curl -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
    https://api.gong.io/v2/users
  ```

## 核心能力
- 自动录制和转录销售电话/会议
- AI分析关键话题(竞品/痛点/预算/时间线)
- 提取Action Items和承诺
- 销售话术分析(talk ratio/问题质量)
- Deal风险预警
- 最佳实践话术库
- CRM自动同步(通话记录和洞察到Salesforce/HubSpot)
- 通话摘要生成

## 使用示例

### 获取所有用户
```bash
curl -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  https://api.gong.io/v2/users
```

### 获取通话列表
```bash
curl -X POST \
  -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  -H "Content-Type: application/json" \
  https://api.gong.io/v2/calls \
  -d '{
    "filter": {
      "fromDateTime": "2026-04-01T00:00:00Z",
      "toDateTime": "2026-04-07T23:59:59Z"
    }
  }'
```

### 获取通话详情
```bash
curl -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  "https://api.gong.io/v2/calls/{call_id}"
```

### 获取通话转录
```bash
curl -X POST \
  -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  -H "Content-Type: application/json" \
  https://api.gong.io/v2/calls/transcript \
  -d '{
    "filter": {
      "callIds": ["1234567890"]
    }
  }'
```

### 获取通话中的关键话题
```bash
curl -X POST \
  -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  -H "Content-Type: application/json" \
  https://api.gong.io/v2/calls/extensive \
  -d '{
    "filter": {
      "callIds": ["1234567890"]
    },
    "contentSelector": {
      "exposedFields": {
        "trackers": true,
        "topics": true,
        "actionItems": true
      }
    }
  }'
```

### 下载通话录音
```bash
curl -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  "https://api.gong.io/v2/calls/{call_id}/media" \
  --output call_recording.mp4
```

### 获取CRM数据同步状态
```bash
curl -X POST \
  -u "$GONG_ACCESS_KEY:$GONG_ACCESS_KEY_SECRET" \
  -H "Content-Type: application/json" \
  https://api.gong.io/v2/crm/objects \
  -d '{
    "filter": {
      "fromDateTime": "2026-04-01T00:00:00Z"
    }
  }'
```

## 适用场景
- Demo/Discovery Call后自动生成总结
- 识别客户提到的竞争对手
- 新销售入职Coaching(听优秀录音学习)
- 追踪客户多次通话的态度变化
- 自动提取Action Items(客户承诺/销售跟进)
- 分析销售话术质量(提问/倾听比例)
- Deal风险预警(客户犹豫/负面情绪)

## 限制和注意事项
- 仅企业版可用API
- 录音需要合规告知(GDPR/各地录音法规)
- API限流: 根据套餐不同(通常300次/分钟)
- 转录需要一定时间(通常通话结束后5-15分钟)
- 录音存储有时限(根据套餐,通常1-2年)
- 仅支持英语和部分语言的AI分析
- 需要与视频会议工具集成(Zoom/Teams/Meet)

## 定价
- 企业版定价需联系销售
- 通常按席位+录音分钟数计费
- 估算: $1,200-2,000/用户/年起

## 参考链接
- [官方文档](https://help.gong.io/hc/en-us/sections/360007505537-API-Documentation)
- [API参考](https://help.gong.io/hc/en-us/sections/360007505537-API-Documentation)
- [集成指南](https://help.gong.io/hc/en-us/categories/360002176572-Integrations)
- [Webhook文档](https://help.gong.io/hc/en-us/articles/4407630567309-Webhooks)
