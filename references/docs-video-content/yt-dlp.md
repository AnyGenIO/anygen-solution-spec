# yt-dlp

> 强大的视频下载工具，支持 1000+ 平台

## 快速开始

### 安装
```bash
# macOS
brew install yt-dlp

# Ubuntu/Debian
sudo apt install yt-dlp

# 或使用 pip
pip install yt-dlp

# Windows (Chocolatey)
choco install yt-dlp

# 或直接下载可执行文件
# https://github.com/yt-dlp/yt-dlp/releases

# 验证安装
yt-dlp --version
```

### 认证配置
- **方式:** 无需认证（大部分平台）
- **部分平台需登录:**
  - YouTube 会员内容
  - Bilibili 大会员视频
  - Netflix/Disney+ 等订阅平台
- **Cookie 登录（推荐）:**
  ```bash
  # 导出浏览器 Cookie
  # 使用插件: Get cookies.txt LOCALLY
  yt-dlp --cookies cookies.txt VIDEO_URL
  ```

## 核心能力
- 下载 1000+ 平台视频（YouTube/Bilibili/Twitter/TikTok 等）
- 自动选择最佳画质
- 下载字幕和缩略图
- 提取音频（MP3/M4A/FLAC）
- 播放列表批量下载
- 频道全部视频下载
- 直播录制
- 格式转换（需 FFmpeg）
- 自动合并音视频
- 元数据嵌入
- 断点续传
- 代理和镜像支持

## 使用示例

### 基础下载
```bash
# 下载默认最佳质量
yt-dlp https://www.youtube.com/watch?v=VIDEO_ID

# Bilibili 视频
yt-dlp https://www.bilibili.com/video/BV1xxxxxxxxx

# TikTok 视频
yt-dlp https://www.tiktok.com/@user/video/1234567890
```

### 指定画质和格式
```bash
# 列出所有可用格式
yt-dlp -F VIDEO_URL

# 下载最佳 MP4（1080p）
yt-dlp -f "bestvideo[ext=mp4][height<=1080]+bestaudio[ext=m4a]" VIDEO_URL

# 下载 4K 视频
yt-dlp -f "bestvideo[height<=2160]+bestaudio" VIDEO_URL

# 下载最佳质量并合并
yt-dlp -f bestvideo+bestaudio --merge-output-format mp4 VIDEO_URL
```

### 仅下载音频
```bash
# 提取音频为 MP3
yt-dlp -x --audio-format mp3 VIDEO_URL

# 提取音频为 M4A（最佳质量）
yt-dlp -x --audio-format m4a VIDEO_URL

# 提取音频并保留原格式
yt-dlp -x VIDEO_URL
```

### 下载播放列表
```bash
# 下载整个播放列表
yt-dlp https://www.youtube.com/playlist?list=PLAYLIST_ID

# 仅下载播放列表第 1-5 个视频
yt-dlp --playlist-items 1-5 PLAYLIST_URL

# 跳过已下载视频
yt-dlp --download-archive archive.txt PLAYLIST_URL
```

### 下载字幕
```bash
# 下载所有可用字幕
yt-dlp --write-subs --all-subs VIDEO_URL

# 下载中文字幕
yt-dlp --write-subs --sub-lang zh-Hans VIDEO_URL

# 下载自动生成字幕
yt-dlp --write-auto-subs --sub-lang en VIDEO_URL

# 嵌入字幕到视频
yt-dlp --embed-subs --sub-lang zh-Hans VIDEO_URL
```

### 限制下载速度
```bash
# 限速 1MB/s
yt-dlp -r 1M VIDEO_URL

# 限速 500KB/s
yt-dlp --limit-rate 500K VIDEO_URL
```

### 自定义输出文件名
```bash
# 使用模板
yt-dlp -o "%(title)s.%(ext)s" VIDEO_URL

# 包含上传日期
yt-dlp -o "%(upload_date)s-%(title)s.%(ext)s" VIDEO_URL

# 播放列表编号
yt-dlp -o "%(playlist_index)s-%(title)s.%(ext)s" PLAYLIST_URL

# 保存到指定目录
yt-dlp -o "~/Videos/%(title)s.%(ext)s" VIDEO_URL
```

### 使用代理
```bash
# HTTP 代理
yt-dlp --proxy http://127.0.0.1:7890 VIDEO_URL

# SOCKS5 代理
yt-dlp --proxy socks5://127.0.0.1:1080 VIDEO_URL
```

### 批量下载
```bash
# 从文件读取 URL 列表
echo "https://www.youtube.com/watch?v=VIDEO1" > urls.txt
echo "https://www.bilibili.com/video/BV1xxxxxxxxx" >> urls.txt

yt-dlp -a urls.txt
```

### 下载缩略图
```bash
# 下载缩略图
yt-dlp --write-thumbnail VIDEO_URL

# 嵌入缩略图到 MP3
yt-dlp -x --audio-format mp3 --embed-thumbnail VIDEO_URL
```

### Cookie 登录下载会员内容
```bash
# 使用浏览器 Cookie
yt-dlp --cookies-from-browser chrome VIDEO_URL

# 使用 Cookie 文件
yt-dlp --cookies cookies.txt VIDEO_URL
```

### 录制直播
```bash
# 录制正在进行的直播
yt-dlp LIVE_URL

# 等待直播开始并录制
yt-dlp --wait-for-video 10 LIVE_URL
```

### 下载频道所有视频
```bash
# YouTube 频道
yt-dlp https://www.youtube.com/@ChannelName/videos

# Bilibili UP 主
yt-dlp https://space.bilibili.com/USER_ID/video
```

### 配置文件（高级）
```bash
# 创建配置文件 ~/.config/yt-dlp/config
cat > ~/.config/yt-dlp/config <<EOF
# 默认格式
-f bestvideo[height<=1080]+bestaudio

# 输出模板
-o ~/Videos/%(title)s.%(ext)s

# 下载字幕
--write-subs
--sub-lang zh-Hans,en

# 嵌入元数据
--embed-metadata
--embed-thumbnail

# 限速
--limit-rate 2M
EOF

# 使用配置
yt-dlp VIDEO_URL
```

## 适用场景
- 视频素材收集
- 离线观看
- 备份 YouTube/Bilibili 内容
- 提取视频音频
- 字幕下载和翻译
- 播客下载
- 教程视频存档
- 社交媒体视频保存
- 直播录制

## 限制和注意事项
- 需要 FFmpeg 支持高级功能（格式转换、合并）
- 部分平台有下载限制（如 Netflix/Disney+）
- YouTube 可能限速或要求登录
- 大文件下载耗时较长
- 某些平台需要 Cookie 认证
- 直播录制需实时在线
- 版权内容请遵守当地法律
- 速率限制可能触发 IP 封禁
- 部分平台格式需手动选择（-f 参数）
- 定期更新以支持新平台和修复

## 定价
- 完全免费开源（Unlicense 许可证）
- 无使用限制
- 社区驱动开发

## 参考链接
- [GitHub 仓库](https://github.com/yt-dlp/yt-dlp)（170K+ stars）
- [支持平台列表](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md)
- [格式选择指南](https://github.com/yt-dlp/yt-dlp#format-selection)
- [输出模板](https://github.com/yt-dlp/yt-dlp#output-template)
- [常见问题](https://github.com/yt-dlp/yt-dlp#faq)
