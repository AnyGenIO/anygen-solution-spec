# anygen-solution-spec — AI 行业工具说明书（Connector 层）

> 让 AI 读懂每个行业该用什么工具、怎么装、怎么配、怎么串

## 定位

本项目是 AnyGen 生态的 **Connector 层**（业务操作工具），配套项目 [anygen-solution-hub](https://github.com/AnyGenIO/anygen-solution-hub) 是 **Data 层**（原始数据源）。

| 层次 | 项目 | 工具数 | 谁买单 | 示例 |
|------|------|--------|--------|------|
| **Connector**（本项目） | anygen-solution-spec | 26 | 用户自己授权 | Salesforce、Shopify、Google Ads |
| **Data** | anygen-solution-hub | 8 | 平台统一采购 | Apollo、ZoomInfo、Gong |

## OpenClaw Skill 包

标准 OpenClaw Skill 包，clone 到 skills 目录即可使用：

```bash
git clone https://github.com/AnyGenIO/anygen-solution-spec.git \
  ~/.openclaw/agents/main/skills/industry-tools
```

## 覆盖行业

| 行业 | 索引 | 工具数 | 核心工具 |
|------|------|--------|---------|
| 销售运营 | [`references/sales-ops.md`](references/sales-ops.md) | 13 | Salesforce / HubSpot / Stripe / DocuSign / Twilio |
| 广告投放 | [`references/ad-ops.md`](references/ad-ops.md) | 3 | Google Ads / Meta / TikTok Marketing |
| 跨境电商 | [`references/cross-border-ecommerce.md`](references/cross-border-ecommerce.md) | 4 | Shopify / Amazon SP-API / TikTok Shop / Square |
| 视频内容 | [`references/video-content.md`](references/video-content.md) | 6 | FFmpeg / Whisper / ElevenLabs / yt-dlp / Runway |

### 文档详细度三档

- **精简版**（4个）：FFmpeg、Whisper、yt-dlp、MoneyPrinterTurbo — AI 内置知识强，只补场景
- **标准版**（11个）：Stripe、Twilio、ElevenLabs 等 — 补认证和环境变量
- **详细版**（11个）：Salesforce、HubSpot、Shopify 等 — 完整认证步骤 + 行业场景

## 目录结构

```
anygen-solution-spec/
├── SKILL.md                           # Skill 主文件（行业路由 + 按需加载）
├── README.md                          # 本文件
└── references/                        # 所有参考文档
    ├── sales-ops.md                   # 销售运营 13 个工具索引
    ├── ad-ops.md                      # 广告投放 3 个工具索引
    ├── cross-border-ecommerce.md      # 跨境电商 4 个工具索引
    ├── video-content.md               # 视频内容 6 个工具索引
    ├── docs-sales-ops/                # 13 个工具详细文档
    ├── docs-ad-ops/                   # 3 个工具详细文档
    ├── docs-cross-border-ecommerce/   # 4 个工具详细文档
    └── docs-video-content/            # 6 个工具详细文档
```

## AI 如何使用

```
用户："我想用 Shopify 开店"
  ↓
AI 读 SKILL.md → 识别"跨境电商"
  ↓
AI 读 references/cross-border-ecommerce.md → 列出 4 个工具
  ↓
AI 读 references/docs-cross-border-ecommerce/shopify-cli.md → 给出完整安装+认证指南
```

按需加载，不一次性读所有文档，节省 context。

## 贡献

1. Fork 本仓库
2. 在 `references/docs-<行业>/` 下新建工具 Markdown
3. 更新对应的索引文件 `references/<行业>.md`
4. 提交 PR

## License

MIT

---

**AnyGen** — AI 原生的行业工具连接器
