# Runway

> AI 视频生成平台，文本/图片生成视频和视频编辑

## 快速开始

### 安装
```bash
# Python SDK（非官方）
pip install runwayml

# 或直接使用 REST API
# 官方主要提供 Web 界面和 API
```

### 认证配置
- **方式:** API Key
- **环境变量:**
  - `RUNWAY_API_KEY` — API 密钥
- **获取步骤:**
  1. 注册账号: https://runwayml.com/
  2. 订阅付费计划（免费试用有限）
  3. 访问 Settings → API Keys
  4. 生成 API Key
- **验证:**
  ```bash
  curl -H "Authorization: Bearer YOUR_API_KEY" \
       https://api.runwayml.com/v1/models
  ```

## 核心能力
- **Gen-3 Alpha:** 文本生成视频（Text-to-Video）
- **Gen-2:** 图片生成视频（Image-to-Video）
- **视频风格迁移:** 艺术风格应用
- **视频超分辨率:** 提升视频清晰度
- **背景移除:** 自动抠像
- **慢动作生成:** AI 补帧
- **对象擦除:** 移除视频中的对象
- **帧插值:** 提升帧率
- **颜色校正:** AI 调色
- **音频增强:** 降噪和优化

## 使用示例

### REST API - 文本生成视频（Gen-3）
```bash
curl -X POST https://api.runwayml.com/v1/gen3/text-to-video \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "A cinematic shot of a astronaut walking on Mars at sunset, realistic, 4K",
    "duration": 5,
    "resolution": "1280x768",
    "style": "cinematic"
  }'
```

### 图片生成视频（Gen-2）
```bash
curl -X POST https://api.runwayml.com/v1/gen2/image-to-video \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "image=@input.jpg" \
  -F "prompt=Camera slowly zooms in, warm lighting" \
  -F "duration=4"
```

### Python SDK 示例（非官方）
```python
import requests

API_KEY = "YOUR_API_KEY"
API_URL = "https://api.runwayml.com/v1/gen3/text-to-video"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

payload = {
    "prompt": "A peaceful river flowing through a forest, birds flying, morning light",
    "duration": 5,
    "resolution": "1280x768",
    "seed": 12345  # 固定随机种子
}

response = requests.post(API_URL, headers=headers, json=payload)
task_id = response.json()["id"]

# 轮询任务状态
import time
while True:
    status = requests.get(
        f"https://api.runwayml.com/v1/tasks/{task_id}",
        headers=headers
    ).json()
    
    if status["status"] == "completed":
        video_url = status["output"]["video_url"]
        print(f"视频已生成: {video_url}")
        break
    elif status["status"] == "failed":
        print("生成失败")
        break
    
    time.sleep(5)
```

### 视频风格迁移
```python
payload = {
    "input_video": "https://example.com/video.mp4",
    "style": "anime",  # 可选: anime, watercolor, oil_painting, sketch
    "strength": 0.8
}

response = requests.post(
    "https://api.runwayml.com/v1/video-style-transfer",
    headers=headers,
    json=payload
)
```

### 视频超分辨率
```python
payload = {
    "input_video": "https://example.com/low_res.mp4",
    "target_resolution": "3840x2160",  # 4K
    "enhance_details": True
}

response = requests.post(
    "https://api.runwayml.com/v1/upscale",
    headers=headers,
    json=payload
)
```

### 背景移除（绿幕）
```python
payload = {
    "input_video": "https://example.com/video.mp4",
    "mode": "auto"  # auto 或 green_screen
}

response = requests.post(
    "https://api.runwayml.com/v1/remove-background",
    headers=headers,
    json=payload
)
```

### 对象擦除
```python
payload = {
    "input_video": "https://example.com/video.mp4",
    "mask": "https://example.com/mask.png",  # 标记要擦除的区域
    "inpainting_mode": "default"
}

response = requests.post(
    "https://api.runwayml.com/v1/inpaint",
    headers=headers,
    json=payload
)
```

### 慢动作生成（帧插值）
```python
payload = {
    "input_video": "https://example.com/video.mp4",
    "target_fps": 60,  # 从 30fps 提升到 60fps
    "interpolation_quality": "high"
}

response = requests.post(
    "https://api.runwayml.com/v1/interpolate",
    headers=headers,
    json=payload
)
```

### 批量生成视频
```python
prompts = [
    "A dragon flying over mountains",
    "Underwater coral reef with tropical fish",
    "City street at night with neon lights"
]

for idx, prompt in enumerate(prompts):
    payload = {
        "prompt": prompt,
        "duration": 5,
        "resolution": "1280x768"
    }
    
    response = requests.post(API_URL, headers=headers, json=payload)
    print(f"任务 {idx+1} 已提交: {response.json()['id']}")
```

## 适用场景
- 概念视频制作
- 广告创意预览
- 社交媒体短视频
- 电影预演（Previz）
- 产品演示视频
- 艺术创作
- 教育内容可视化
- 游戏宣传片
- 音乐视频制作
- 快速原型设计

## 限制和注意事项
- 免费试用额度有限（约 125 Credits）
- 生成时间较长（5 秒视频约 2-5 分钟）
- Gen-3 视频最长 10 秒
- 分辨率限制（最高 4K，但耗费更多 Credits）
- API 速率限制（根据订阅计划）
- 生成质量依赖提示词质量
- 某些内容可能被审核拦截
- 不支持音频生成（仅视频）
- 商业使用需付费计划
- 视频生成不保证完美一致性

## 定价
- **Free Trial:**
  - 125 Credits（约 5 个 5 秒视频）
  - 基础分辨率
- **Standard ($12/月):**
  - 625 Credits/月
  - 1080p 分辨率
  - 去水印
- **Pro ($28/月):**
  - 2,250 Credits/月
  - 4K 分辨率
  - 优先生成队列
- **Unlimited ($76/月):**
  - 无限 720p 生成
  - 7,500 Credits 用于高分辨率
  - 并发生成
- **Enterprise (定制):**
  - 专属支持
  - API 访问
  - 自定义模型训练

> **Credits 消耗:**
> - Gen-3 (5s, 720p): 10 Credits
> - Gen-3 (5s, 1080p): 15 Credits
> - Gen-2 (4s): 5 Credits
> - 超分辨率 4K: 20 Credits/秒

## 参考链接
- [官方网站](https://runwayml.com/)
- [API 文档](https://docs.runwayml.com/api)
- [Gen-3 Alpha](https://runwayml.com/research/introducing-gen-3-alpha)
- [教程和案例](https://runwayml.com/learn)
- [定价](https://runwayml.com/pricing)
- [Discord 社区](https://discord.com/invite/runwayml)
