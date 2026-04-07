# ElevenLabs

> AI 语音合成平台，真人级 TTS 和语音克隆

## 快速开始

### 安装
```bash
# Python SDK
pip install elevenlabs

# Node.js SDK
npm install elevenlabs

# 或直接使用 REST API（curl/requests）
```

### 认证配置
- **方式:** API Key
- **环境变量:**
  - `ELEVEN_API_KEY` — API 密钥
- **获取步骤:**
  1. 注册账号: https://elevenlabs.io/
  2. 访问 Profile Settings → API Keys
  3. 生成新 API Key
- **验证:**
  ```bash
  curl -H "xi-api-key: YOUR_API_KEY" https://api.elevenlabs.io/v1/user
  ```

## 核心能力
- 文本转语音（TTS）
- 多语言支持（29 种语言）
- 语音克隆（Voice Cloning）
- 语音设计（Voice Design）
- 情感和语调控制
- 流式音频生成（WebSocket）
- 语音库（预设音色）
- 长文本合成（自动切分）
- 音频历史记录管理
- 语音共享和市场

## 使用示例

### Python SDK - 基础 TTS
```python
from elevenlabs import generate, play, set_api_key

# 设置 API Key
set_api_key("YOUR_API_KEY")

# 生成语音
audio = generate(
    text="Hello, this is a test of ElevenLabs text to speech.",
    voice="Bella",  # 预设音色
    model="eleven_monolingual_v1"
)

# 播放音频
play(audio)
```

### 保存为文件
```python
from elevenlabs import generate, save

audio = generate(
    text="欢迎使用 ElevenLabs AI 配音服务。",
    voice="Bella",
    model="eleven_multilingual_v2"
)

# 保存为 MP3
save(audio, "output.mp3")
```

### 使用自定义语音
```python
from elevenlabs import generate, Voice, VoiceSettings

audio = generate(
    text="This is a custom voice configuration.",
    voice=Voice(
        voice_id="21m00Tcm4TlvDq8ikWAM",  # Rachel
        settings=VoiceSettings(
            stability=0.75,    # 稳定性（0-1）
            similarity_boost=0.8,  # 相似度增强
            style=0.5,         # 风格强度
            use_speaker_boost=True
        )
    )
)
```

### 流式生成（低延迟）
```python
from elevenlabs import generate, stream

audio_stream = generate(
    text="This is a streaming example with lower latency.",
    voice="Bella",
    model="eleven_monolingual_v1",
    stream=True
)

# 边生成边播放
stream(audio_stream)
```

### REST API - 基础调用
```bash
curl -X POST https://api.elevenlabs.io/v1/text-to-speech/21m00Tcm4TlvDq8ikWAM \
  -H "xi-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello from ElevenLabs API!",
    "model_id": "eleven_monolingual_v1",
    "voice_settings": {
      "stability": 0.75,
      "similarity_boost": 0.8
    }
  }' \
  --output output.mp3
```

### 获取可用音色列表
```python
from elevenlabs import voices

# 获取所有可用音色
all_voices = voices()

for voice in all_voices:
    print(f"{voice.name} - {voice.voice_id}")
```

### 语音克隆
```python
from elevenlabs import clone, generate

# 克隆语音（需上传音频样本）
voice = clone(
    name="My Custom Voice",
    description="A cloned voice from audio samples",
    files=["sample1.mp3", "sample2.mp3", "sample3.mp3"]
)

# 使用克隆的语音
audio = generate(
    text="This is my cloned voice speaking.",
    voice=voice
)
```

### Node.js SDK 示例
```javascript
import { ElevenLabsClient } from "elevenlabs";

const client = new ElevenLabsClient({
  apiKey: process.env.ELEVEN_API_KEY
});

// 生成语音
const audio = await client.textToSpeech.convert("Bella", {
  text: "Hello from Node.js!",
  model_id: "eleven_monolingual_v1"
});

// 保存文件
const fs = require("fs");
fs.writeFileSync("output.mp3", audio);
```

### WebSocket 流式生成（超低延迟）
```python
from elevenlabs import VoiceSettings
from elevenlabs.client import ElevenLabs

client = ElevenLabs(api_key="YOUR_API_KEY")

# WebSocket 流式生成
audio_stream = client.generate(
    text="This is ultra-low latency streaming.",
    voice="Bella",
    model="eleven_turbo_v2",
    stream=True,
    latency_optimizations=4  # 最大延迟优化
)

for chunk in audio_stream:
    # 实时处理音频块
    pass
```

## 适用场景
- 视频 AI 配音
- 有声书制作
- 播客自动化
- 游戏角色语音
- 客服语音机器人
- 教育内容配音
- 多语言视频本地化
- 语音助手开发
- 短视频旁白生成

## 限制和注意事项
- 免费计划每月 10,000 字符
- API 速率限制（根据订阅计划）
- 语音克隆需要 Creator 计划或以上
- 单次请求最大 5,000 字符（推荐切分长文本）
- 商业使用需要付费计划
- 克隆语音需要至少 1 分钟清晰音频样本
- 不支持实时语音转换（仅 TTS）
- 某些语言质量优于其他语言（英语最优）
- 流式生成需要稳定网络连接

## 定价
- **Free（免费）:**
  - 10,000 字符/月
  - 3 个自定义语音
  - 基础音色库
- **Starter（$5/月）:**
  - 30,000 字符/月
  - 10 个自定义语音
  - 商业使用
- **Creator（$22/月）:**
  - 100,000 字符/月
  - 30 个自定义语音
  - 语音克隆
  - 优先处理
- **Pro（$99/月）:**
  - 500,000 字符/月
  - 160 个自定义语音
  - 专业语音克隆
- **Scale（$330/月）:**
  - 2,000,000 字符/月
  - 660 个自定义语音
  - 企业支持

> 按需付费: $0.30/1,000 字符（超出配额后）

## 参考链接
- [官方网站](https://elevenlabs.io/)
- [API 文档](https://elevenlabs.io/docs/api-reference)
- [Python SDK](https://github.com/elevenlabs/elevenlabs-python)
- [Node.js SDK](https://github.com/elevenlabs/elevenlabs-js)
- [语音库](https://elevenlabs.io/voice-library)
- [定价](https://elevenlabs.io/pricing)
