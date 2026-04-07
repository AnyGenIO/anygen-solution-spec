# anygen-solution-spec — AI 行业工具说明书

> 让 AI 读懂每个行业该用什么工具、怎么装、怎么配、怎么串

## OpenClaw Skill 包

这是一个标准的 OpenClaw Skill 包，clone 到 skills 目录即可使用：

```bash
# 安装方式
git clone https://github.com/AnyGenIO/anygen-solution-spec.git ~/.openclaw/agents/main/skills/anygen-solution-spec
```

覆盖 **7 个行业、80+ 工具**，支持 Progressive Disclosure（按需加载详细文档）。

## 问题

世界上有成千上万的 CLI/API 工具，但 AI 不知道：

1. **发现** — 这个行业有哪些工具可用？
2. **配置** — 每个工具怎么装、怎么认证？
3. **能力** — 每个工具能做什么、有什么限制？
4. **编排** — 要完成一个业务目标，工具之间怎么串？
5. **领域知识** — 这个行业有什么规则和坑？

## 解决方案

一个行业一个 YAML 文件，AI Agent 读了就能干活。

每个 YAML 包含：
- **工具清单** — 名称、安装方式、认证方式、环境变量
- **能力描述** — 每个工具能做什么、什么时候用
- **限制说明** — API 限流、费用、注意事项
- **领域术语** — 行业黑话翻译
- **业务规则** — 合规要求、最佳实践
- **典型工作流** — 真实场景的工具编排步骤

## 已覆盖行业

| 行业 | 文件 | 工具数 |
|------|------|--------|
| 跨境电商 | [`domains/cross-border-ecommerce.yaml`](domains/cross-border-ecommerce.yaml) | 多平台上架、订单、广告、竞品、库存 |
| 销售运营 | [`domains/sales-ops.yaml`](domains/sales-ops.yaml) | CRM、通讯触达、线索获取、合同签署 |
| 广告投放 | [`domains/ad-ops.yaml`](domains/ad-ops.yaml) | Google/Meta/TikTok/Microsoft 广告 |
| 投研金融 | [`domains/investment-research.yaml`](domains/investment-research.yaml) | 行情数据、财务分析、组合监控 |
| 客服运营 | [`domains/customer-support.yaml`](domains/customer-support.yaml) | 工单、沟通、退款、物流查询 |
| 法务助理 | [`domains/legal-assistant.yaml`](domains/legal-assistant.yaml) | 合同审查、法规查询、电子签约 |
| 税务财务 | [`domains/tax-finance.yaml`](domains/tax-finance.yaml) | 发票管理、记账对账、VAT 合规 |

## 快速上手

```yaml
# 示例：AI Agent 读取跨境电商说明书
domain: 跨境电商
description: 多平台产品上架、订单管理、广告投放、竞品监控、库存管理

tools:
  - name: Shopify CLI
    id: shopify-cli
    what: Shopify 官方命令行，管理店铺、主题、产品
    install: npm install -g @shopify/cli
    auth:
      required: true
      env_vars:
        - SHOPIFY_ACCESS_TOKEN
        - SHOPIFY_STORE_URL
    capabilities:
      - 产品CRUD（创建/更新/删除/批量操作）
      - 订单查询和管理
      - 库存管理
    when_to_use:
      - 批量上架产品
      - 查询和管理订单
```

## 目录结构

```
anygen-solution-spec/
├── SKILL.md                       # Skill 主文件（行业路由 + 按需加载逻辑）
├── README.md                      # GitHub 展示用
├── references/                    # Skill references 目录
│   ├── sales-ops.md              # 工具索引（45个工具）
│   ├── cross-border-ecommerce.md # 工具索引（9个工具）
│   ├── ad-ops.md                 # 工具索引
│   ├── investment-research.md
│   ├── customer-support.md
│   ├── legal-assistant.md
│   ├── tax-finance.md
│   ├── docs-sales-ops/           # 45个工具详细文档
│   └── docs-cross-border-ecommerce/  # 9个工具详细文档
├── domains/                       # 原始 YAML 数据源
│   ├── cross-border-ecommerce.yaml
│   ├── sales-ops.yaml
│   └── ...
└── spec/                          # 规范和示例
```

## 如何贡献

1. Fork 本仓库
2. 在 `domains/` 下新建你的行业 YAML 文件
3. 按现有格式填写工具信息
4. 提交 PR

## License

MIT

---

**AnyGen** — AI 原生的行业工具连接器
