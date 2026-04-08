---
name: industry-tools
description: "行业工具说明书 — Connector 层。覆盖销售运营、广告投放、跨境电商、视频内容四个行业的 26 个工具。Data 数据源见 anygen-solution-hub。"
---

# Industry Tools — 行业工具说明书

当用户进入某个行业场景时：

1. **识别行业** — 根据关键词路由到对应行业
2. **列出工具** — 读 `references/<domain>.md` 展示工具清单
3. **展示详情** — 读 `references/docs-<domain>/<tool-id>.md` 展示安装/认证/能力
4. **指导操作** — 引导安装、配置密钥、验证连通

## 支持的行业和文档详细度

| 行业 | 索引 | 详情目录 | 工具数 | 文档详细度 |
|------|------|----------|--------|------------|
| 销售运营 | `references/sales-ops.md` | `references/docs-sales-ops/` | 13 | 详细版(6) + 标准版(7) |
| 广告投放 | `references/ad-ops.md` | `references/docs-ad-ops/` | 3 | 详细版(3) |
| 跨境电商 | `references/cross-border-ecommerce.md` | `references/docs-cross-border-ecommerce/` | 4 | 详细版(3) + 标准版(1) |
| 视频内容 | `references/video-content.md` | `references/docs-video-content/` | 6 | 精简版(4) + 标准版(2) |

### 文档详细度说明

**精简版（lite）** — AI 内置知识强，Spec 只补场景和注意事项（<30 行）
- FFmpeg, Whisper, yt-dlp, MoneyPrinterTurbo

**标准版（standard）** — AI 基本懂但认证要准确（60-100 行）
- Stripe, Twilio, ElevenLabs, Square, Pipedrive, Freshsales, Twenty, Attio, Salesloft, Lemlist, Runway

**详细版（detailed）** — 复杂/易混淆，需要完整引导（150+ 行）
- Salesforce, HubSpot, Shopify, Amazon SP-API, Google Ads, Meta Marketing, TikTok Marketing, TikTok Shop, Zoho CRM, Outreach, DocuSign

## 行业路由关键词

### 销售运营 (sales-ops)
CRM、线索、销售、商机、Pipeline、Salesforce、HubSpot、SDR、AE、合同、电子签、收款、冷邮件、触达、通话录音、预测、佣金

### 广告投放 (ad-ops)
Google Ads、Meta、Facebook广告、Instagram广告、TikTok广告、投放、ROAS、CPA、Campaign、广告优化、竞品监控

### 跨境电商 (cross-border-ecommerce)
Shopify、Amazon、亚马逊、TikTok Shop、TikTok小店、上架、listing、FBA、跨境、电商、开店

### 视频内容 (video-content)
视频剪辑、字幕、配音、TTS、短视频生成、视频下载、FFmpeg、转码、AI视频

## 使用流程

### 用户问行业工具 → 读索引
```
read("references/sales-ops.md")
```
展示工具清单表格。

### 用户问具体工具 → 读详情
```
read("references/docs-sales-ops/salesforce-cli.md")
```
展示安装、认证、能力、示例、限制。

### 用户想安装 → 引导操作
1. 给安装命令
2. 引导认证（环境变量 + 获取步骤）
3. 验证连通性

## Connector 层 vs Data 层

本项目是 **Connector 层**（业务操作工具），配套项目 [anygen-solution-hub](https://github.com/AnyGenIO/anygen-solution-hub) 是 **Data 层**（原始数据源）。

| 层次 | 定位 | 示例 | 何时使用 |
|------|------|------|----------|
| **Connector 层**（本项目） | 业务操作工具 | Salesforce、HubSpot、Shopify、Stripe | 需要执行业务操作（创建订单、发邮件、投放广告） |
| **Data 层**（anygen-solution-hub） | 原始数据源 | Apollo.io、ZoomInfo、Gong、Bright Data | 需要查询原始数据（联系人库、通话录音、竞品广告） |

**典型组合：**
- 销售线索拓展 → Data: Apollo 搜索联系人 → Connector: Salesforce 创建 Lead
- 广告投放优化 → Data: Bright Data 采集竞品素材 → Connector: Google Ads 创建广告

## 原则

- **按需加载** — 只读用户需要的文件
- **先概览再详情** — 先列清单，再看具体工具
- **实用导向** — 重点是帮用户快速上手
- **安全提醒** — 凭证不要泄露
