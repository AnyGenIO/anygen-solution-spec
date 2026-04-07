# Lightpanda

> 轻量级无头浏览器,比Puppeteer快10倍,适合爬竞品页面

## 快速开始

### 安装
```bash
curl -fsSL https://lightpanda.io/install.sh | sh
```

### 认证配置
- **方式:** 无需认证
- **环境变量:** 无
- **验证:**
  ```bash
  lightpanda --version
  ```

## 核心能力

- 高速网页抓取
- JavaScript渲染
- 竞品页面数据提取(价格/评价/排名)
- 轻量级资源占用
- 支持自动化脚本
- 反爬虫绕过(基础)

## 使用示例

### 基础页面抓取
```bash
# 抓取单个页面
lightpanda fetch "https://www.amazon.com/dp/B08N5WRWNW" > product.html
```

### 提取竞品价格
```bash
# 使用JavaScript提取数据
lightpanda eval \
  --url "https://www.amazon.com/dp/B08N5WRWNW" \
  --script "document.querySelector('.a-price-whole').textContent"
```

### 批量抓取产品信息
```bash
#!/bin/bash
# 批量抓取ASIN列表

ASINS=("B08N5WRWNW" "B09JQL3NVT" "B0BDJ8L3LY")

for asin in "${ASINS[@]}"; do
  echo "抓取 ASIN: $asin"
  lightpanda fetch "https://www.amazon.com/dp/$asin" > "data/${asin}.html"
  sleep 2  # 避免频率过高
done
```

### 使用JavaScript API
```javascript
// scrape.js - 使用Lightpanda的JS API
const lightpanda = require('lightpanda');

async function scrapeProduct(asin) {
  const browser = await lightpanda.launch();
  const page = await browser.newPage();
  
  await page.goto(`https://www.amazon.com/dp/${asin}`);
  
  const data = await page.evaluate(() => {
    return {
      title: document.querySelector('#productTitle')?.textContent.trim(),
      price: document.querySelector('.a-price-whole')?.textContent.trim(),
      rating: document.querySelector('.a-icon-star span')?.textContent,
      reviews: document.querySelector('#acrCustomerReviewText')?.textContent,
      availability: document.querySelector('#availability span')?.textContent.trim()
    };
  });
  
  await browser.close();
  return data;
}

// 使用
scrapeProduct('B08N5WRWNW').then(console.log);
```

### 监控竞品价格变化
```python
#!/usr/bin/env python3
import subprocess
import json
import time
from datetime import datetime

def get_price(asin):
    """使用lightpanda获取产品价格"""
    cmd = [
        'lightpanda', 'eval',
        '--url', f'https://www.amazon.com/dp/{asin}',
        '--script', "document.querySelector('.a-price-whole').textContent"
    ]
    result = subprocess.run(cmd, capture_output=True, text=True)
    return result.stdout.strip()

def monitor_competitors(asins, interval=3600):
    """定期监控竞品价格"""
    while True:
        timestamp = datetime.now().isoformat()
        prices = {}
        
        for asin in asins:
            try:
                price = get_price(asin)
                prices[asin] = price
                print(f"{timestamp} - {asin}: ${price}")
                time.sleep(2)  # 避免请求过快
            except Exception as e:
                print(f"Error scraping {asin}: {e}")
        
        # 保存到文件
        with open(f'prices_{timestamp}.json', 'w') as f:
            json.dump({'timestamp': timestamp, 'prices': prices}, f)
        
        time.sleep(interval)

# 监控竞品
competitor_asins = ['B08N5WRWNW', 'B09JQL3NVT', 'B0BDJ8L3LY']
monitor_competitors(competitor_asins, interval=3600)  # 每小时检查一次
```

### 抓取评价数据
```javascript
// reviews.js - 抓取产品评价
const lightpanda = require('lightpanda');

async function scrapeReviews(asin, maxPages = 3) {
  const browser = await lightpanda.launch();
  const page = await browser.newPage();
  
  const allReviews = [];
  
  for (let pageNum = 1; pageNum <= maxPages; pageNum++) {
    const url = `https://www.amazon.com/product-reviews/${asin}?pageNumber=${pageNum}`;
    await page.goto(url);
    
    const reviews = await page.evaluate(() => {
      const reviewElements = document.querySelectorAll('[data-hook="review"]');
      return Array.from(reviewElements).map(el => ({
        rating: el.querySelector('.review-rating')?.textContent,
        title: el.querySelector('[data-hook="review-title"]')?.textContent,
        text: el.querySelector('[data-hook="review-body"]')?.textContent,
        date: el.querySelector('[data-hook="review-date"]')?.textContent,
        verified: !!el.querySelector('[data-hook="avp-badge"]')
      }));
    });
    
    allReviews.push(...reviews);
    await page.waitFor(1000);  // 延迟
  }
  
  await browser.close();
  return allReviews;
}

scrapeReviews('B08N5WRWNW', 5).then(reviews => {
  console.log(JSON.stringify(reviews, null, 2));
});
```

### 抓取Best Seller排名
```bash
# 获取类目排名
lightpanda eval \
  --url "https://www.amazon.com/dp/B08N5WRWNW" \
  --script "Array.from(document.querySelectorAll('#detailBullets_feature_div li')).find(li => li.textContent.includes('Best Sellers Rank'))?.textContent"
```

## 适用场景
- 爬取竞品的产品页面
- 监控竞品价格变化
- 批量采集市场数据
- 提取产品评价和评分
- 追踪Best Seller排名
- 分析竞品listing优化

## 限制和注意事项
- 需要遵守目标网站robots.txt
- 频率太高可能被封IP
- 建议使用代理IP池
- 某些网站有强反爬机制
- 抓取数据需符合网站服务条款
- 商业用途需注意法律风险
- 相比Puppeteer功能较少(专注速度)

## 定价
- 完全免费开源
- 无API调用费用
- 可能需要:
  - 代理服务费用(如需绕过反爬)
  - 服务器运行成本

## 参考链接
- [官方文档](https://lightpanda.io/docs)
- [GitHub仓库](https://github.com/lightpanda-io/browser)
- [API参考](https://lightpanda.io/docs/api)
- [使用示例](https://lightpanda.io/docs/examples)
