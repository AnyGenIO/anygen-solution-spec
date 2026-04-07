# 销售运营 — 工具清单

> 详细文档：`read("references/docs-sales-ops/<tool-id>.md")`

## CRM 平台

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Salesforce CLI | salesforce-cli | 企业级CRM，Sales/Service/Marketing Cloud | `npm install -g @salesforce/cli` |
| HubSpot CLI | hubspot-cli | 一体化CRM+营销+服务 | `npm install -g @hubspot/cli` |
| Pipedrive API | pipedrive-api | 销售管线CRM，中小团队首选 | HTTP API |
| Zoho CRM API | zoho-crm-api | 性价比CRM，Deluge脚本定制 | HTTP API |
| Freshsales API | freshsales-api | AI驱动轻量CRM | HTTP API |
| Twenty | twenty | 开源CRM，GraphQL API，可自托管 | `docker-compose up` |
| Attio API | attio-api | 新一代灵活CRM，类Notion体验 | HTTP API |
| Salesforce MCP | salesforce-mcp | MCP Server，AI Agent直接操作SF | `git clone` + `pip install` |

## 通讯触达

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Twilio CLI | twilio-cli | 打电话/发短信/WhatsApp | `npm install -g twilio-cli` |
| Aircall API | aircall-api | 云呼叫中心，销售团队共享电话 | HTTP API |

## 线索获取

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Apollo.io API | apollo-api | 2.75亿联系人数据库 | HTTP API |
| Clearbit API | clearbit-api | 公司信息补全和富化 | `pip install clearbit` |
| Lusha API | lusha-api | 联系人数据平台 | HTTP API |
| ZoomInfo API | zoominfo-api | 企业级联系人数据库 | HTTP API |
| LinkedIn Sales Nav | linkedin-sales-nav | 精准找决策人 | HTTP API |
| 6sense API | 6sense-api | ABM平台，AI预测购买意图 | HTTP API |

## 序列化触达

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Outreach API | outreach-api | 销售自动化，序列化触达 | HTTP API |
| Salesloft API | salesloft-api | Outreach竞品，节奏管理 | HTTP API |
| Lemlist API | lemlist-api | 冷邮件自动化，高送达率 | HTTP API |

## 合同签署

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| DocuSign SDK | docusign-sdk | 电子签名 | `pip install docusign-esign` |
| PandaDoc API | pandadoc-api | 提案+报价+电子签一体化 | HTTP API |
| Proposify API | proposify-api | 提案管理 | HTTP API |

## 支付/账单

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Stripe CLI | stripe-cli | 支付、订阅、发票 | `brew install stripe` |

## 会话智能

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Gong API | gong-api | 录音转文字+AI分析 | HTTP API |
| Chorus.ai | chorus-api | 对话智能（ZoomInfo旗下） | HTTP API |

## CPQ/报价

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Salesforce CPQ | salesforce-cpq | 原生报价配置 | 通过SF CLI |
| DealHub API | dealhub-api | 独立CPQ+Playbook | HTTP API |

## 销售预测/BI

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Clari API | clari-api | AI预测收入/管道健康度 | HTTP API |
| Salesforce Einstein | salesforce-einstein | AI分析预测 | 通过SF CLI |

## 日程安排

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Calendly API | calendly-api | 自助日程预约 | HTTP API |
| Chili Piper API | chilipiper-api | 智能会议路由 | HTTP API |

## 邮件发送

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| SendGrid API | sendgrid-api | 批量邮件基础设施 | `pip install sendgrid` |
| Mandrill | mandrill-api | 事务性邮件 | `pip install mailchimp-transactional` |
| Gmail API | gmail-api | 从Gmail发邮件 | `pip install google-api-python-client` |

## 佣金管理

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| CaptivateIQ API | captivateiq-api | 佣金自动计算 | HTTP API |
| Spiff API | spiff-api | 佣金自动化，实时可见 | HTTP API |

## 对话式营销

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Intercom API | intercom-api | 网站实时聊天+邮件 | HTTP API |
| Drift API | drift-api | 对话式营销+ABM | HTTP API |

## 其他

| 工具 | tool-id | 用途 | 安装方式 |
|------|---------|------|---------|
| Close CRM API | close-crm-api | Inside Sales CRM | HTTP API |
| Zoom API | zoom-api | 视频会议/Demo | `pip install zoom-python` |
| Google Meet API | google-meet-api | Google Meet | `pip install google-api-python-client` |
| Zapier API | zapier-api | No-code集成自动化 | HTTP API |
| Make API | make-api | 可视化集成自动化 | HTTP API |
| Slack API | slack-api | 团队协作通知 | `pip install slack-sdk` |
| 飞书 OpenAPI | feishu-api | 消息/日历/文档/审批 | `pip install lark-oapi` |
