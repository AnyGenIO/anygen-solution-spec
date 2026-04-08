<!-- doc-level: standard -->

# Runway

AI视频生成平台,提供文本/图片生成视频和视频编辑功能,适合快速制作概念视频和创意内容。

## 快速开始

### 认证配置
- **方式:** API Key
- **环境变量:** `RUNWAY_API_KEY`
- **获取步骤:**
  1. 注册 https://runwayml.com/ → 订阅付费计划
  2. Settings → API Keys → 生成保存
- **验证:**
  ```bash
  curl -H "Authorization: Bearer YOUR_API_KEY" \
       https://api.runwayml.com/v1/models
  ```

## 核心能力
- 文本生成视频(Gen-3)和图片生成视频(Gen-2)
- 视频风格迁移和超分辨率
- 背景移除和对象擦除
- 帧插值和慢动作生成

## 使用示例

### 文本生成视频
```bash
curl -X POST https://api.runwayml.com/v1/gen3/text-to-video \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "A cinematic shot of astronaut on Mars at sunset",
    "duration": 5,
    "resolution": "1280x768"
  }'
```

### 图片生成视频
```bash
curl -X POST https://api.runwayml.com/v1/gen2/image-to-video \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -F "image=@input.jpg" \
  -F "prompt=Camera zooms in slowly" \
  -F "duration=4"
```

### Python轮询任务状态
```python
import requests, time

response = requests.post(API_URL, headers=headers, json=payload)
task_id = response.json()["id"]

while True:
    status = requests.get(f"https://api.runwayml.com/v1/tasks/{task_id}", headers=headers).json()
    if status["status"] == "completed":
        print(f"视频: {status['output']['video_url']}")
        break
    time.sleep(5)
```

## 适用场景
- 广告创意预览和社交媒体短视频
- 产品演示和概念视频制作
- 电影预演和艺术创作

## 限制和注意事项
- 免费试用125 Credits(约5个5秒视频)
- Gen-3视频最长10秒,生成时间2-5分钟
- API速率限制根据订阅计划
- 某些内容可能被审核拦截
- 商业使用需付费计划

## 定价
- Free Trial: 125 Credits
- Standard: $12/月(625 Credits)
- Pro: $28/月(2,250 Credits, 4K)
- Unlimited: $76/月(无限720p + 7,500 Credits)

## 参考链接
- [官方网站](https://runwayml.com/)
- [API文档](https://docs.runwayml.com/api)
- [定价](https://runwayml.com/pricing)
