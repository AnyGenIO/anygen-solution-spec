# 销售运营 — Connector 工具清单

> 仅含 Connector（用户授权即可用），Data 数据源归 anygen-solution-hub
> 详细文档：`read("references/docs-sales-ops/<tool-id>.md")`

## CRM 平台

| 工具 | tool-id | 核心动作 | 用户需要 |
|------|---------|---------|---------|
| Salesforce | salesforce-cli | 建Lead/推Stage/查Pipeline | 授权 OAuth |
| HubSpot | hubspot-cli | 建Contact/管Deal/发邮件 | Private App Token |
| Pipedrive | pipedrive-api | 管Pipeline/移Stage | API Token |
| Zoho CRM | zoho-crm-api | CRUD+Deluge脚本 | OAuth |
| Freshsales | freshsales-api | Lead评分/Deal管理 | API Key |
| Twenty | twenty | 自托管CRM全操作 | 自建/API Key |
| Attio | attio-api | 灵活数据模型CRM | API Key |

## 触达自动化

| 工具 | tool-id | 核心动作 | 用户需要 |
|------|---------|---------|---------|
| Outreach | outreach-api | 创建序列/发邮件/排任务 | OAuth |
| Salesloft | salesloft-api | 管Cadence/发邮件 | API Key |
| Lemlist | lemlist-api | 冷邮件序列/个性化 | API Key |

## 支付/签署/通讯

| 工具 | tool-id | 核心动作 | 用户需要 |
|------|---------|---------|---------|
| Stripe | stripe-cli | 收款/建订阅/发发票 | API Key |
| DocuSign | docusign-sdk | 发合同/收签名 | OAuth(JWT) |
| Twilio | twilio-cli | 打电话/发短信/WhatsApp | SID + Token |
