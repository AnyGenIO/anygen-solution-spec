# MoneyPrinterTurbo

> 自动生成产品展示/带货短视频,54k stars

## 快速开始

### 安装
```bash
git clone https://github.com/harry0703/MoneyPrinterTurbo
cd MoneyPrinterTurbo
pip install -r requirements.txt
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `OPENAI_API_KEY` — 或其他LLM的key,用来生成文案
  - `PEXELS_API_KEY` — 素材图片/视频(免费)
- **获取步骤:**
  - LLM API key: 从 OpenAI/Claude/通义千问等获取
  - Pexels: 访问 pexels.com 注册免费获取API key
- **验证:**
  ```bash
  # 检查配置文件
  cat config.toml
  # 或运行测试
  python main.py --help
  ```

## 核心能力

- 根据产品描述自动生成短视频
- 自动配音、字幕、背景音乐
- 支持多种视频比例(9:16竖屏、16:9横屏)
- 批量生成
- 自动素材搜索和下载
- 文案智能生成
- 多语言支持

## 使用示例

### 配置环境变量
```bash
# 创建.env文件
cat > .env << 'EOF'
OPENAI_API_KEY=sk-your-api-key-here
PEXELS_API_KEY=your-pexels-key-here
EOF

# 或在config.toml中配置
```

### 生成单个产品视频
```bash
# 基础用法
python main.py \
  --topic "蓝牙降噪耳机,高音质,长续航30小时,适合通勤和运动" \
  --video_aspect "9:16" \
  --voice_name "zh-CN-XiaoxiaoNeural" \
  --bgm_volume 0.3
```

### 使用配置文件批量生成
```python
# 创建配置文件 products.json
import json

products = [
    {
        "topic": "智能手表,心率监测,GPS定位,防水50米",
        "video_aspect": "9:16",
        "voice": "zh-CN-YunxiNeural"
    },
    {
        "topic": "便携蓝牙音箱,360度环绕音,IPX7防水",
        "video_aspect": "16:9",
        "voice": "zh-CN-XiaoxiaoNeural"
    }
]

with open('products.json', 'w', encoding='utf-8') as f:
    json.dump(products, f, ensure_ascii=False, indent=2)
```

### 批量生成脚本
```python
#!/usr/bin/env python3
import subprocess
import json

with open('products.json', 'r', encoding='utf-8') as f:
    products = json.load(f)

for i, product in enumerate(products):
    print(f"生成视频 {i+1}/{len(products)}: {product['topic']}")
    
    cmd = [
        'python', 'main.py',
        '--topic', product['topic'],
        '--video_aspect', product['video_aspect'],
        '--voice_name', product['voice'],
        '--output', f'output/video_{i+1}.mp4'
    ]
    
    subprocess.run(cmd)
    print(f"完成: output/video_{i+1}.mp4")
```

### 自定义素材来源
```bash
# 使用本地素材而非在线搜索
python main.py \
  --topic "户外背包,大容量,防水设计" \
  --video_aspect "9:16" \
  --material_directory "./my_materials" \
  --voice_name "zh-CN-YunyangNeural"
```

### 高级参数配置
```bash
python main.py \
  --topic "咖啡机,全自动,19Bar压力,一键打奶泡" \
  --video_aspect "9:16" \
  --voice_name "zh-CN-XiaoxiaoNeural" \
  --bgm_volume 0.2 \
  --subtitle_enabled true \
  --subtitle_position "bottom" \
  --video_concat_mode "sequential" \
  --output "coffee_maker_ad.mp4"
```

### Web界面使用
```bash
# 启动Web服务
python webui.py

# 访问 http://localhost:7860
# 在界面中:
# 1. 输入产品描述
# 2. 选择视频比例
# 3. 选择配音语言
# 4. 点击生成
```

### 使用Docker运行
```bash
# 构建镜像
docker build -t moneyprinter .

# 运行容器
docker run -it --rm \
  -e OPENAI_API_KEY=your_key \
  -e PEXELS_API_KEY=your_key \
  -v $(pwd)/output:/app/output \
  moneyprinter \
  python main.py --topic "你的产品描述"
```

## 适用场景
- 需要给产品做展示视频
- TikTok/YouTube/Instagram 内容生产
- 批量生成多个产品的视频素材
- 电商产品短视频营销
- 快速制作带货视频
- A/B测试不同视频风格

## 限制和注意事项
- 视频质量取决于素材质量
- 每个视频生成需要几分钟
- LLM API调用有成本
- 免费Pexels素材有使用限制
- 生成的文案可能需要人工审核
- 某些类目(医疗/金融)需谨慎使用AI生成内容

## 定价
- MoneyPrinterTurbo: 开源免费
- 成本:
  - OpenAI API: ~$0.01-0.05/视频(文案生成)
  - Pexels API: 免费(有月度配额)
  - Azure TTS: ~$0.001/字符(可选)
  - 本地运行无额外费用

## 参考链接
- [GitHub仓库](https://github.com/harry0703/MoneyPrinterTurbo)
- [使用文档](https://github.com/harry0703/MoneyPrinterTurbo/blob/main/README.md)
- [常见问题](https://github.com/harry0703/MoneyPrinterTurbo/issues)
- [Pexels API](https://www.pexels.com/api/)
