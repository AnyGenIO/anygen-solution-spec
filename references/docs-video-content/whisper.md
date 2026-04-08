<!-- doc-level: lite -->

# Whisper

> OpenAI 开源语音识别模型，多语言转录和翻译

## 用途
- 多语言语音识别（99 种语言）和自动语言检测
- 字幕生成（SRT/VTT/TXT）和时间戳对齐
- 语音翻译为英文
- 多种模型规格（tiny/base/small/medium/large）
- 词级时间戳和长音频自动切分

## 适用场景
- 视频自动生成字幕和会议录音转文字
- 播客转录和多语言视频翻译
- 语音笔记整理和无障碍字幕生成
- 语音数据集标注和客服录音文字化
- 需要专业术语识别的场景（使用 initial_prompt 优化）

## 注意事项
- 本地运行完全免费，无需 API Key
- 首次运行需下载模型（39M-1.5GB，根据规格）
- 推荐 small 模型（244M，精度 ~9% WER）平衡速度和精度
- GPU 加速需要 CUDA（NVIDIA 显卡）
- 背景噪音、方言和口音会影响识别精度
- 专业术语建议使用 initial_prompt 优化

## 参考链接
- [GitHub 仓库](https://github.com/openai/whisper)
- [模型性能对比](https://github.com/openai/whisper#available-models-and-languages)
- [论文](https://arxiv.org/abs/2212.04356)
