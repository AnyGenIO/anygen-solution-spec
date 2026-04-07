# Whisper

> OpenAI 开源语音识别模型，多语言转录和翻译

## 快速开始

### 安装
```bash
# Python 环境（推荐 Python 3.8+）
pip install openai-whisper

# macOS 额外依赖
brew install ffmpeg

# Ubuntu/Debian
sudo apt update && sudo apt install ffmpeg

# 验证安装
whisper --help
```

### 认证配置
- **方式:** 本地模型，无需 API Key
- 首次运行会自动下载模型文件
- 模型存储位置: `~/.cache/whisper/`

## 核心能力
- 多语言语音识别（99 种语言）
- 自动语言检测
- 字幕生成（SRT/VTT/TXT）
- 语音翻译（翻译为英文）
- 时间戳对齐
- 多种模型规格（tiny/base/small/medium/large）
- 词级时间戳（word-level timestamps）
- 长音频自动切分
- 初始提示词优化（initial prompt）

## 使用示例

### 基础转录
```bash
# 自动检测语言并转录
whisper audio.mp3

# 指定语言（中文）
whisper audio.mp3 --language Chinese

# 指定语言（英文）
whisper audio.mp3 --language English
```

### 选择模型规格
```bash
# tiny（最快，精度最低）
whisper audio.mp3 --model tiny

# base（快速，精度一般）
whisper audio.mp3 --model base

# small（平衡）
whisper audio.mp3 --model small

# medium（精度高，速度慢）
whisper audio.mp3 --model medium

# large（最高精度，速度最慢）
whisper audio.mp3 --model large
```

### 生成字幕文件
```bash
# 生成 SRT 字幕
whisper video.mp4 --output_format srt

# 生成 VTT 字幕（网页视频）
whisper video.mp4 --output_format vtt

# 生成纯文本
whisper audio.wav --output_format txt

# 生成所有格式
whisper audio.mp3 --output_format all
```

### 语音翻译为英文
```bash
# 中文音频翻译为英文
whisper chinese_audio.mp3 --task translate

# 日文视频翻译为英文字幕
whisper japanese_video.mp4 --task translate --output_format srt
```

### Python API 使用
```python
import whisper

# 加载模型
model = whisper.load_model("base")

# 转录音频
result = model.transcribe("audio.mp3")

# 打印全文
print(result["text"])

# 带时间戳的片段
for segment in result["segments"]:
    print(f"[{segment['start']:.2f}s -> {segment['end']:.2f}s] {segment['text']}")
```

### 高级选项 - 初始提示词
```python
import whisper

model = whisper.load_model("small")

# 使用初始提示词优化识别（如专业术语）
result = model.transcribe(
    "audio.mp3",
    language="zh",
    initial_prompt="本次会议讨论 AI、机器学习、GPT、LLM 相关技术"
)

print(result["text"])
```

### 词级时间戳
```python
import whisper

model = whisper.load_model("base")

# 启用词级时间戳
result = model.transcribe("audio.mp3", word_timestamps=True)

for segment in result["segments"]:
    for word in segment.get("words", []):
        print(f"{word['start']:.2f}s - {word['end']:.2f}s: {word['word']}")
```

### 批量处理
```bash
# 批量转录多个文件
for file in *.mp3; do
    whisper "$file" --model small --output_format srt
done
```

### 指定输出目录
```bash
# 输出到指定目录
whisper audio.mp3 --output_dir ./subtitles --output_format srt
```

## 适用场景
- 视频自动生成字幕
- 会议录音转文字
- 播客转录
- 多语言视频翻译
- 语音笔记整理
- 无障碍字幕生成
- 语音数据集标注
- 客服录音文字化

## 限制和注意事项
- 模型大小:
  - tiny: 39M（最快，精度 ~32% WER）
  - base: 74M（快速，精度 ~16% WER）
  - small: 244M（推荐，精度 ~9% WER）
  - medium: 769M（高精度，精度 ~6% WER）
  - large: 1550M（最高精度，精度 ~5% WER）
- 首次运行需下载模型（根据规格 39M-1.5GB）
- 长音频建议使用 medium 或 large 模型
- GPU 加速需要 CUDA（NVIDIA 显卡）
- 背景噪音会影响识别精度
- 方言和口音可能降低准确率
- 专业术语建议使用 initial_prompt 优化
- 音频质量建议: 16kHz 采样率，单声道

## 定价
- 完全免费开源（MIT 许可证）
- 本地运行，无 API 调用费用
- 无使用次数限制

## 参考链接
- [GitHub 仓库](https://github.com/openai/whisper)
- [模型性能对比](https://github.com/openai/whisper#available-models-and-languages)
- [支持语言列表](https://github.com/openai/whisper/blob/main/whisper/tokenizer.py)
- [论文](https://arxiv.org/abs/2212.04356)
- [Whisper API（云端付费版）](https://platform.openai.com/docs/guides/speech-to-text)
