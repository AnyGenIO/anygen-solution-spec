# Twilio CLI

> Twilio 官方CLI,打电话、发短信、管号码,最像真正的CLI工具

## 快速开始

### 安装
```bash
npm install -g twilio-cli
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `TWILIO_ACCOUNT_SID` — 账户SID
  - `TWILIO_AUTH_TOKEN` — 认证令牌
- **获取步骤:**
  1. 访问 https://www.twilio.com/console
  2. 首页即可看到 Account SID 和 Auth Token
  3. 复制两者
- **交互式登录:**
  ```bash
  twilio login
  # 按提示输入Account SID和Auth Token
  ```
- **验证:**
  ```bash
  twilio api:core:accounts:fetch
  ```

## 核心能力
- 发送短信/彩信(SMS/MMS)
- 拨打电话(可接TTS或录音)
- 购买和管理电话号码
- 查看通话/短信记录
- WhatsApp消息发送
- 号码路由配置
- IVR(交互式语音应答)设置
- 通话录音管理

## 使用示例

### 发送短信
```bash
twilio api:core:messages:create \
  --from "+12345678901" \
  --to "+8613800000000" \
  --body "您好!这是来自销售团队的跟进消息。"
```

### 拨打电话(TTS)
```bash
twilio api:core:calls:create \
  --from "+12345678901" \
  --to "+8613800000000" \
  --url "http://demo.twilio.com/docs/voice.xml"
```

### 购买电话号码
```bash
# 搜索可用号码
twilio api:core:available-phone-numbers:local:list \
  --country-code US \
  --area-code 415

# 购买号码
twilio api:core:incoming-phone-numbers:create \
  --phone-number "+14155551234"
```

### 查看短信历史
```bash
twilio api:core:messages:list \
  --date-sent-after "2026-04-01" \
  --limit 50
```

### 发送WhatsApp消息
```bash
twilio api:core:messages:create \
  --from "whatsapp:+14155238886" \
  --to "whatsapp:+8613800000000" \
  --body "您好!感谢您的咨询,我们的销售会尽快联系您。"
```

### 查看通话记录
```bash
twilio api:core:calls:list \
  --status "completed" \
  --limit 20
```

## 适用场景
- SDR批量短信触达
- 自动拨打跟进电话
- 设置电话转接/IVR
- 发送WhatsApp消息
- 验证码和通知短信
- 会议电话和多方通话
- 客户回拨自动化

## 限制和注意事项
- 按用量计费:
  - 短信: $0.0079/条起(美国),国际短信更贵
  - 通话: $0.014/分钟起(美国)
- 需要购买号码: $1-2/月/号码
- 批量短信需要注册Campaign(美国10DLC要求)
- 短信字符限制: 单条160字符(超过自动分段计费)
- 部分国家需要合规审批(中国需ICP备案)
- WhatsApp需单独申请Business账户

## 定价
- 免费试用账户(有限额度)
- Pay-as-you-go(按用量计费):
  - 短信: $0.0079/条起
  - 通话: $0.014/分钟起
  - 号码租赁: $1/月起
- Enterprise套餐可议价

## 参考链接
- [官方文档](https://www.twilio.com/docs/twilio-cli)
- [CLI命令参考](https://www.twilio.com/docs/twilio-cli/general-usage)
- [API参考](https://www.twilio.com/docs/usage/api)
- [定价](https://www.twilio.com/pricing)
