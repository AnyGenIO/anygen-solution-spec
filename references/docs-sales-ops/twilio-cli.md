<!-- doc-level: standard -->

# Twilio CLI

Twilio官方CLI工具,用于发送短信、拨打电话和管理电话号码,适合销售团队自动化外呼和批量触达。

## 快速开始

### 安装
```bash
npm install -g twilio-cli
```

### 认证配置
- **方式:** api_key
- **环境变量:** `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`
- **获取步骤:**
  1. 访问 https://www.twilio.com/console
  2. 首页复制 Account SID 和 Auth Token
- **验证:** `twilio api:core:accounts:fetch`

## 核心能力
- 发送短信/彩信和WhatsApp消息
- 拨打电话和TTS语音
- 购买和管理电话号码
- 通话/短信记录查询

## 使用示例

### 发送短信
```bash
twilio api:core:messages:create \
  --from "+12345678901" \
  --to "+8613800000000" \
  --body "您好!这是来自销售团队的跟进消息。"
```

### 拨打电话
```bash
twilio api:core:calls:create \
  --from "+12345678901" \
  --to "+8613800000000" \
  --url "http://demo.twilio.com/docs/voice.xml"
```

### 购买电话号码
```bash
twilio api:core:available-phone-numbers:local:list --country-code US --area-code 415
twilio api:core:incoming-phone-numbers:create --phone-number "+14155551234"
```

## 适用场景
- SDR批量短信和自动外呼
- 验证码和通知短信
- 客户回拨和IVR系统

## 限制和注意事项
- 按用量计费: 短信$0.0079/条起, 通话$0.014/分钟起
- 号码租赁: $1-2/月/号码
- 批量短信需注册Campaign(美国10DLC要求)
- 短信单条160字符,超过自动分段计费
- 部分国家需合规审批

## 定价
- 免费试用有限额度
- Pay-as-you-go: 短信$0.0079/条, 通话$0.014/分钟
- 号码租赁$1/月起

## 参考链接
- [官方文档](https://www.twilio.com/docs/twilio-cli)
- [定价](https://www.twilio.com/pricing)
