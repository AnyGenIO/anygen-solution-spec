<!-- doc-level: standard -->

# ElevenLabs

AI语音合成平台,提供真人级TTS和语音克隆功能,支持29种语言和流式生成,适合视频配音和语音助手开发。

## 快速开始

### 安装
```bash
# Python SDK
pip install elevenlabs

# Node.js SDK
npm install elevenlabs
```

### 认证配置
- **方式:** API Key
- **环境变量:** `ELEVEN_API_KEY`
- **获取步骤:**
  1. 注册 https://elevenlabs.io/ → Profile Settings → API Keys
  2. 生成新 API Key
- **验证:**
  ```bash
  curl -H "xi-api-key: YOUR_API_KEY" https://api.elevenlabs.io/v1/user
  ```

## 核心能力
- 文本转语音(TTS)和多语言支持(29种)
- 语音克隆和语音设计
- 流式音频生成(WebSocket低延迟)
- 情感和语调控制
- 预设音色库和自定义语音

## 使用示例

### Python SDK基础TTS
```python
from elevenlabs import generate, save, set_api_key

set_api_key("YOUR_API_KEY")
audio = generate(
    text="欢迎使用 ElevenLabs AI 配音服务。",
    voice="Bella",
    model="eleven_multilingual_v2"
)
save(audio, "output.mp3")
```

### REST API调用
```bash
curl -X POST https://api.elevenlabs.io/v1/text-to-speech/21m00Tcm4TlvDq8ikWAM \
  -H "xi-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello from ElevenLabs!",
    "model_id": "eleven_monolingual_v1"
  }' \
  --output output.mp3
```

### 流式生成
```python
from elevenlabs import generate, stream

audio_stream = generate(
    text="This is a streaming example.",
    voice="Bella",
    stream=True
)
stream(audio_stream)
```

## 适用场景
- 视频AI配音和有声书制作
- 播客自动化和游戏角色语音
- 多语言视频本地化
- 客服语音机器人

## 限制和注意事项
- 免费计划10,000字符/月
- 语音克隆需Creator计划以上
- 单次请求最大5,000字符
- 商业使用需付费计划
- 英语质量最优

## 定价
- Free: 10,000字符/月
- Starter: $5/月(30,000字符)
- Creator: $22/月(100,000字符+语音克隆)
- Pro: $99/月(500,000字符)

## 参考链接
- [官方网站](https://elevenlabs.io/)
- [API文档](https://elevenlabs.io/docs/api-reference)
- [定价](https://elevenlabs.io/pricing)
