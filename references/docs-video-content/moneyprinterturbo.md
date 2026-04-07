# MoneyPrinterTurbo

> AI 自动化短视频生成工具，一键生成带货/解说视频

## 快速开始

### 安装
```bash
# 克隆项目
git clone https://github.com/harry0703/MoneyPrinterTurbo.git
cd MoneyPrinterTurbo

# 创建虚拟环境（推荐）
python -m venv venv
source venv/bin/activate  # Linux/macOS
# venv\Scripts\activate  # Windows

# 安装依赖
pip install -r requirements.txt

# 安装 ImageMagick（字幕渲染）
# macOS
brew install imagemagick

# Ubuntu/Debian
sudo apt install imagemagick

# Windows: 下载安装 https://imagemagick.org/
```

### 认证配置
- **方式:** 配置文件 + API Keys
- **配置文件:** `config.toml`
- **必需的 API Keys:**
  - **OpenAI API Key** (文案生成) 或国产大模型（通义千问/智谱 GLM）
  - **Azure TTS Key** (语音合成) 或 ElevenLabs/Edge-TTS
  - **Pexels API Key** (视频素材，免费) 或 Pixabay
- **配置示例:**
  ```toml
  [app]
  video_duration = 60  # 视频时长（秒）
  video_resolution = "1080x1920"  # 竖屏 9:16

  [openai]
  api_key = "sk-xxxxx"
  base_url = "https://api.openai.com/v1"
  model = "gpt-4"

  [azure]
  speech_key = "YOUR_AZURE_KEY"
  speech_region = "eastus"
  voice = "zh-CN-XiaoxiaoNeural"

  [pexels]
  api_key = "YOUR_PEXELS_KEY"  # 免费注册: https://www.pexels.com/api/
  ```

## 核心能力
- AI 生成视频文案（OpenAI/通义千问/GLM）
- 自动匹配视频素材（Pexels/Pixabay）
- AI 配音（Azure TTS/ElevenLabs/Edge-TTS）
- 自动字幕生成和渲染
- 背景音乐混音
- 视频转场和滤镜
- 批量生成视频
- 多语言支持（中/英/日/韩等）
- 自定义文案和素材
- 视频尺寸预设（竖屏/横屏/方形）

## 使用示例

### Web 界面启动
```bash
# 启动 Streamlit Web UI
streamlit run webui.py

# 访问 http://localhost:8501
```

### 命令行生成视频
```bash
# 使用默认配置生成
python main.py --topic "如何高效学习 Python"

# 指定视频时长
python main.py --topic "AI 工具推荐" --duration 90

# 指定语言和尺寸
python main.py \
  --topic "Top 5 AI Tools" \
  --language en \
  --resolution 1920x1080
```

### 批量生成
```bash
# 创建主题列表文件 topics.txt
echo "AI 绘画工具推荐" > topics.txt
echo "ChatGPT 使用技巧" >> topics.txt
echo "短视频运营方法" >> topics.txt

# 批量生成
python batch_generate.py --file topics.txt
```

### Python API 使用
```python
from moneyprinterturbo import VideoGenerator

# 初始化生成器
generator = VideoGenerator(config_path="config.toml")

# 生成视频
result = generator.generate(
    topic="5 个 AI 写作工具推荐",
    duration=60,
    language="zh",
    resolution="1080x1920",
    voice="zh-CN-XiaoxiaoNeural",
    bgm_volume=0.2
)

print(f"视频已生成: {result['video_path']}")
```

### 自定义文案
```python
# 使用自定义文案而非 AI 生成
custom_script = """
今天给大家推荐 5 个 AI 工具。
第一个是 ChatGPT，最强大的 AI 对话助手。
第二个是 Midjourney，AI 绘画神器。
...
"""

result = generator.generate(
    script=custom_script,  # 使用自定义文案
    duration=60,
    language="zh"
)
```

### 自定义素材
```python
# 使用本地视频素材
result = generator.generate(
    topic="产品介绍",
    video_clips=["clip1.mp4", "clip2.mp4", "clip3.mp4"],
    duration=60
)
```

### 配置不同 TTS 引擎
```toml
# config.toml

# 方案 1: Azure TTS（推荐，质量高）
[azure]
speech_key = "YOUR_KEY"
speech_region = "eastus"
voice = "zh-CN-XiaoxiaoNeural"

# 方案 2: ElevenLabs（英文最佳）
[elevenlabs]
api_key = "YOUR_KEY"
voice_id = "21m00Tcm4TlvDq8ikWAM"

# 方案 3: Edge-TTS（免费，质量一般）
[edge]
voice = "zh-CN-XiaoxiaoNeural"
```

### 添加背景音乐
```python
result = generator.generate(
    topic="冥想放松音乐",
    bgm_path="background.mp3",  # 背景音乐
    bgm_volume=0.15,  # 音量（0-1）
    duration=60
)
```

## 适用场景
- 短视频带货（抖音/快手/视频号）
- 知识解说视频
- 产品介绍视频
- 热点资讯快剪
- 读书/电影解说
- 教程类视频
- 旅游/美食 vlog
- 励志/情感短视频
- 营销推广视频

## 限制和注意事项
- 需要有效的 API Keys（OpenAI/Azure/Pexels）
- OpenAI API 按 token 计费（约 $0.002/视频文案）
- Azure TTS 免费额度: 500,000 字符/月
- Pexels API 免费但有速率限制（200 请求/小时）
- ImageMagick 必须正确安装（字幕渲染依赖）
- 生成速度取决于视频时长和素材下载速度
- 素材版权: Pexels/Pixabay 素材免费可商用
- AI 生成文案需人工审核质量
- 音视频同步可能需要微调
- 长视频（>2 分钟）建议分段生成

## 定价
- **MoneyPrinterTurbo:** 完全免费开源（MIT 许可证）
- **依赖服务成本:**
  - **OpenAI API:** 
    - GPT-4: $0.03/1K tokens（输入）+ $0.06/1K tokens（输出）
    - GPT-3.5: $0.0015/1K tokens（输入）+ $0.002/1K tokens（输出）
    - 单视频文案约 $0.002-$0.01
  - **Azure TTS:**
    - 免费: 500,000 字符/月
    - 付费: $1/100 万字符（神经语音）
  - **Pexels/Pixabay API:** 完全免费
  - **Edge-TTS:** 完全免费（微软浏览器语音引擎）

## 参考链接
- [GitHub 仓库](https://github.com/harry0703/MoneyPrinterTurbo)（54K+ stars）
- [中文文档](https://github.com/harry0703/MoneyPrinterTurbo/blob/main/README_zh.md)
- [配置指南](https://github.com/harry0703/MoneyPrinterTurbo/wiki)
- [视频教程](https://www.bilibili.com/video/BV1Yz421U7Be/)
- [常见问题](https://github.com/harry0703/MoneyPrinterTurbo/issues)
