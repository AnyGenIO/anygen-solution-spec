# FFmpeg

> 开源视频处理工具，转码、剪辑、合并、滤镜

## 快速开始

### 安装
```bash
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt update
sudo apt install ffmpeg

# Windows (Chocolatey)
choco install ffmpeg

# Windows (手动)
# 下载: https://ffmpeg.org/download.html
# 解压并添加到 PATH

# 验证安装
ffmpeg -version
```

### 认证配置
- **方式:** 无需认证
- 本地 CLI 工具，无需 API Key 或账号

## 核心能力
- 视频格式转换（MP4/MOV/AVI/WebM/MKV 等）
- 视频压缩和码率调整
- 视频剪辑和合并
- 音视频分离和合成
- 添加水印和字幕
- 视频滤镜（模糊、锐化、色彩调整）
- 帧提取和截图
- 音频转换和处理
- 流媒体推流（RTMP/HLS）
- 硬件加速编码（NVIDIA/AMD/Intel）

## 使用示例

### 视频格式转换
```bash
# MOV 转 MP4
ffmpeg -i input.mov output.mp4

# 指定编码器和质量
ffmpeg -i input.avi -c:v libx264 -crf 23 -c:a aac output.mp4

# 转 WebM（网页优化）
ffmpeg -i input.mp4 -c:v libvpx-vp9 -crf 30 -b:v 0 output.webm
```

### 视频压缩
```bash
# 压缩至目标大小（如 10MB）
ffmpeg -i input.mp4 -b:v 1M -maxrate 1M -bufsize 2M output.mp4

# 高质量压缩（CRF 18-28，越低越清晰）
ffmpeg -i input.mp4 -c:v libx264 -crf 28 output.mp4

# 调整分辨率
ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4
```

### 视频剪辑
```bash
# 截取片段（从 00:01:30 开始，持续 30 秒）
ffmpeg -ss 00:01:30 -i input.mp4 -t 30 -c copy output.mp4

# 快速剪辑（无重新编码）
ffmpeg -ss 00:00:10 -to 00:00:20 -i input.mp4 -c copy clip.mp4
```

### 视频合并
```bash
# 方法 1: 使用 concat 协议（相同格式）
echo "file 'video1.mp4'" > filelist.txt
echo "file 'video2.mp4'" >> filelist.txt
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4

# 方法 2: 合并不同格式视频
ffmpeg -i video1.mp4 -i video2.avi -filter_complex "[0:v][0:a][1:v][1:a]concat=n=2:v=1:a=1" output.mp4
```

### 音视频分离
```bash
# 提取音频
ffmpeg -i input.mp4 -vn -acodec copy audio.m4a

# 提取视频（无音频）
ffmpeg -i input.mp4 -an -vcodec copy video.mp4

# 音视频合成
ffmpeg -i video.mp4 -i audio.mp3 -c:v copy -c:a aac output.mp4
```

### 添加水印
```bash
# 图片水印（右上角）
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=W-w-10:10" output.mp4

# 文字水印
ffmpeg -i input.mp4 -vf "drawtext=text='Sample':fontsize=24:fontcolor=white:x=10:y=10" output.mp4
```

### 添加字幕
```bash
# 硬字幕（烧录到视频中）
ffmpeg -i input.mp4 -vf "subtitles=subtitle.srt" output.mp4

# 软字幕（可切换）
ffmpeg -i input.mp4 -i subtitle.srt -c:v copy -c:a copy -c:s mov_text output.mp4
```

### 截取视频帧
```bash
# 每秒截取 1 帧
ffmpeg -i input.mp4 -vf fps=1 frame_%04d.png

# 截取特定时间点的截图
ffmpeg -ss 00:00:05 -i input.mp4 -frames:v 1 thumbnail.jpg
```

### 调整播放速度
```bash
# 加速 2 倍
ffmpeg -i input.mp4 -filter:v "setpts=0.5*PTS" -filter:a "atempo=2.0" output.mp4

# 慢放 0.5 倍
ffmpeg -i input.mp4 -filter:v "setpts=2.0*PTS" -filter:a "atempo=0.5" output.mp4
```

### 硬件加速（NVIDIA GPU）
```bash
# H.264 硬件编码
ffmpeg -hwaccel cuda -i input.mp4 -c:v h264_nvenc -preset fast output.mp4

# H.265 硬件编码
ffmpeg -hwaccel cuda -i input.mp4 -c:v hevc_nvenc -crf 28 output.mp4
```

## 适用场景
- 视频格式批量转换
- 视频压缩和优化（社交媒体上传）
- 自动化视频剪辑（去片头片尾）
- 视频素材预处理
- 流媒体推流（直播）
- 提取视频帧用于 AI 训练
- 音视频同步调整
- 视频质量优化和修复

## 限制和注意事项
- 编码速度取决于硬件性能和编码参数
- H.265/HEVC 编码速度慢但压缩率高
- 硬件加速需要兼容显卡（NVIDIA/AMD/Intel）
- CRF 值范围 0-51（推荐 18-28，越低质量越高）
- 使用 `-c copy` 快速处理但不能改变编码参数
- 某些滤镜组合会显著增加处理时间
- 字幕格式兼容性（SRT/ASS/VTT）
- 大文件处理需要足够磁盘空间

## 定价
- 完全免费开源（LGPL/GPL 许可证）
- 无使用限制
- 社区维护和更新

## 参考链接
- [官方网站](https://ffmpeg.org/)
- [官方文档](https://ffmpeg.org/documentation.html)
- [Wiki 指南](https://trac.ffmpeg.org/wiki)
- [常用命令合集](https://github.com/leandromoreira/ffmpeg-libav-tutorial)
- [硬件加速指南](https://trac.ffmpeg.org/wiki/HWAccelIntro)
