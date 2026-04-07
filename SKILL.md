---
name: industry-tools
description: "行业工具说明书 — 列出并指导使用各行业的 CLI/API 工具。当用户提到销售运营、跨境电商、广告投放、视频内容等行业场景，或询问某个行业有哪些工具、怎么安装、怎么认证时使用。"
---

# Industry Tools — 行业工具说明书

当用户进入某个行业场景时：

1. **识别行业** — 根据关键词路由到对应行业
2. **列出工具** — 读 `references/<domain>.md` 展示工具清单
3. **展示详情** — 读 `references/docs-<domain>/<tool-id>.md` 展示安装/认证/能力
4. **指导操作** — 引导安装、配置密钥、验证连通

## 支持的行业（Tier1-4 P0）

| 行业 | 索引 | 详情目录 | 工具数 |
|------|------|----------|--------|
| 销售运营 | `references/sales-ops.md` | `references/docs-sales-ops/` | 20 |
| 广告投放 | `references/ad-ops.md` | `references/docs-ad-ops/` | 4 |
| 跨境电商 | `references/cross-border-ecommerce.md` | `references/docs-cross-border-ecommerce/` | 4 |
| 视频内容 | `references/video-content.md` | `references/docs-video-content/` | 6 |

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

## 原则

- **按需加载** — 只读用户需要的文件
- **先概览再详情** — 先列清单，再看具体工具
- **实用导向** — 重点是帮用户快速上手
- **安全提醒** — 凭证不要泄露
