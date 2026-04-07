# Aircall API

> 云呼叫中心,销售团队共享电话系统(比Twilio更完整的Sales场景方案)

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** api_key (Basic Auth)
- **环境变量:**
  - `AIRCALL_API_ID` — API ID
  - `AIRCALL_API_TOKEN` — API Token
- **获取步骤:**
  1. 登录 Aircall Dashboard
  2. Settings → Integrations → API Keys
  3. 点击 "Create a new API Key"
  4. 复制 API ID 和 API Token
- **验证:**
  ```bash
  curl -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
    https://api.aircall.io/v1/company
  ```

## 核心能力
- 共享号码池(团队共用销售号码)
- 智能呼叫分配(Round Robin/技能路由)
- 通话录音和转录
- IVR(自动语音应答)配置
- 呼叫标签和备注管理
- 与CRM深度集成(Salesforce/HubSpot,通话自动记录)
- 实时监控(Manager监听/介入通话)
- Analytics(接通率/通话时长/队列等待)

## 使用示例

### 获取公司信息
```bash
curl -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  https://api.aircall.io/v1/company
```

### 拨打电话
```bash
curl -X POST \
  -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.aircall.io/v1/calls \
  -d '{
    "to": "+8613800000000",
    "from": "your_aircall_number_id"
  }'
```

### 获取通话记录
```bash
curl -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  'https://api.aircall.io/v1/calls?order=desc&per_page=20'
```

### 为通话添加标签和备注
```bash
# 添加标签
curl -X POST \
  -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.aircall.io/v1/calls/12345/tags \
  -d '{"tags": ["qualified_lead", "follow_up_needed"]}'

# 添加备注
curl -X POST \
  -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.aircall.io/v1/calls/12345/comments \
  -d '{"content": "客户对产品很感兴趣,下周三再次跟进"}'
```

### 获取团队成员列表
```bash
curl -u "$AIRCALL_API_ID:$AIRCALL_API_TOKEN" \
  https://api.aircall.io/v1/users
```

## 适用场景
- 销售团队需要统一电话系统(非个人手机)
- 需要通话录音和质量监控
- Manager要实时看团队拨打情况
- 与CRM自动同步通话记录
- SDR高频外呼场景
- 客服团队呼入/呼出统一管理

## 限制和注意事项
- 按席位月费+通话分钟费(按用量计费)
- 主要覆盖欧美号码,亚洲号码支持有限
- API限流: 100次/分钟
- 录音存储有时限(根据套餐不同,通常30-90天)
- 需要稳定的网络连接(云端服务)

## 定价
- Essentials: $30/用户/月
- Professional: $50/用户/月
- Custom: 需联系销售
- 通话费用另计(根据目标国家不同)

## 参考链接
- [官方文档](https://developer.aircall.io/api-references/)
- [API参考](https://developer.aircall.io/api-references/rest-api)
- [集成指南](https://developer.aircall.io/integrations)
