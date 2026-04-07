# 跨境电商工具文档索引

> 多平台产品上架、订单管理、广告投放、竞品监控、库存管理

## 店铺管理

| 工具 | 说明 | 文档 |
|------|------|------|
| Shopify CLI | Shopify官方CLI,管理店铺产品/订单/主题 | [查看](shopify-cli.md) |
| Shopify MCP Server | Shopify官方MCP Server,AI Agent直接操作店铺 | [查看](shopify-mcp.md) |
| Amazon SP-API | 亚马逊卖家API,管理listing/订单/库存/广告 | [查看](amazon-sp-api.md) |
| TikTok Shop Open API | TikTok小店官方API,产品/订单/物流 | [查看](tiktok-shop-api.md) |
| 速卖通 Open Platform | 速卖通开放平台API | [查看](aliexpress-api.md) |

## 支付/POS

| 工具 | 说明 | 文档 |
|------|------|------|
| Square API | POS+支付网关+在线店铺一体化,全球支持 | [查看](square-api.md) |

## 内容生成

| 工具 | 说明 | 文档 |
|------|------|------|
| MoneyPrinterTurbo | 自动生成产品展示/带货短视频 | [查看](moneyprinter.md) |

## 竞品研究

| 工具 | 说明 | 文档 |
|------|------|------|
| Lightpanda | 轻量级无头浏览器,爬竞品页面 | [查看](lightpanda.md) |
| Helium 10 | Amazon选品和关键词研究工具 | [查看](helium10.md) |

---

**总计:** 9 个工具,覆盖跨境电商核心场景

## 使用建议

### 典型工具栈(独立站)
- 店铺: Shopify
- 支付: Square (或 Stripe)
- 内容: MoneyPrinterTurbo(视频生成)
- 分析: Google Analytics

### 典型工具栈(Amazon卖家)
- 平台: Amazon SP-API
- 选品: Helium 10
- 竞品: Lightpanda(爬虫)
- 广告: Amazon SP-API(PPC管理)

### 典型工具栈(多平台)
- 独立站: Shopify
- 市场: Amazon SP-API + TikTok Shop API
- 支付: Square
- 内容: MoneyPrinterTurbo
- 竞品: Helium 10 + Lightpanda

## 典型工作流

### 新品上架
1. 准备资料(图片/描述/价格)
2. Shopify CLI 创建产品
3. Amazon SP-API 同步到亚马逊
4. TikTok Shop API 同步到TikTok小店
5. MoneyPrinterTurbo 生成展示视频
6. 设置库存同步规则

### 竞品监控
1. Lightpanda 爬取竞品页面
2. 提取价格/评价/排名数据
3. 对比分析
4. 生成报告
5. 调整策略

### 广告优化(Amazon)
1. Amazon SP-API 拉广告数据
2. 分析ACOS(广告成本占比)
3. 调整bid(竞价)
4. 暂停低效关键词
5. 添加新关键词

### 订单处理
1. 各平台API拉取新订单
2. 统一订单管理系统
3. 库存扣减
4. 物流发货
5. 追踪物流状态
6. 同步到平台

## 平台对比

| 平台 | 优势 | 适合场景 | API成熟度 |
|------|------|---------|----------|
| Shopify | 独立站,品牌控制 | 建立品牌,长期运营 | ⭐⭐⭐⭐⭐ |
| Amazon | 流量大,转化高 | 快速起量,标品 | ⭐⭐⭐⭐ |
| TikTok Shop | 内容电商,年轻用户 | 直播带货,新品 | ⭐⭐⭐ |
| 速卖通 | 全球化,低价 | 低客单价,批发 | ⭐⭐⭐ |

## 注意事项

### 数据同步
- 多平台卖必须实时同步库存
- 超卖会被平台处罚
- 建议使用ERP系统统一管理

### 平台规则
- Amazon不允许其他平台卖更低价
- Amazon主图必须纯白底,1000x1000px以上
- TikTok Shop标题建议30-80字符
- 速卖通标题上限128字符

### 合规要求
- 遵守各平台政策
- 产品认证(CE/FCC/UL等)
- 知识产权(避免侵权)
- 税务合规(VAT/GST)
