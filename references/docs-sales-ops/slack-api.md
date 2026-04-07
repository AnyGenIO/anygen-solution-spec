# Slack API

> 团队协作平台API,销售通知/数据推送/审批流(几乎所有公司都在用)

## 快速开始

### 安装
```bash
# Python
pip install slack-sdk

# Node.js
npm install @slack/web-api

# Go
go get -u github.com/slack-go/slack
```

### 认证配置
- **方式:** oauth (Bot Token)
- **环境变量:**
  - `SLACK_BOT_TOKEN` — Slack Bot User OAuth Token
- **获取步骤:**
  1. 访问 [api.slack.com](https://api.slack.com/apps) → Create New App
  2. 选择 "From scratch" → 输入App名称和Workspace
  3. OAuth & Permissions → Bot Token Scopes → 添加权限(如chat:write, channels:read等)
  4. Install App to Workspace → 复制 "Bot User OAuth Token"
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
    https://slack.com/api/auth.test
  ```

## 核心能力

### 消息发送
- 发消息到频道/私聊
- 富文本(Block Kit)
- 文本格式化(粗体/斜体/链接)
- @提及用户
- 线程回复(Thread)
- 定时发送(Scheduled Messages)

### 频道管理
- 创建频道
- 邀请用户
- 归档频道
- 频道列表
- 频道信息

### 文件上传
- 上传文件/图片
- 分享到频道
- 文件评论

### 互动组件(Interactive)
- 按钮(Buttons)
- 下拉菜单(Select Menus)
- 日期选择器
- 模态框(Modals)
- 表单输入

### Slash Commands
- 自定义命令(如/deal-update)
- 命令参数解析
- 快捷操作

### Workflow Builder
- No-code自动化(类似Zapier)
- 表单收集
- 审批流程
- 定时任务

### 事件订阅(Events API)
- 监听消息
- 用户加入/离开
- 频道变更
- 按钮点击事件

## 使用示例

### 发送简单消息
```bash
curl -X POST https://slack.com/api/chat.postMessage \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "#sales",
    "text": "新商机成交! 🎉 客户:科技公司 金额:$50,000"
  }'
```

### 使用Python SDK发送消息
```python
from slack_sdk import WebClient
import os

client = WebClient(token=os.environ['SLACK_BOT_TOKEN'])

response = client.chat_postMessage(
    channel='#sales',
    text='新商机成交! 🎉',
    blocks=[
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": "*新商机成交!* 🎉"
            }
        },
        {
            "type": "section",
            "fields": [
                {"type": "mrkdwn", "text": "*客户:*\n科技公司"},
                {"type": "mrkdwn", "text": "*金额:*\n$50,000"},
                {"type": "mrkdwn", "text": "*负责人:*\n@张伟"},
                {"type": "mrkdwn", "text": "*阶段:*\nClosed Won"}
            ]
        },
        {
            "type": "actions",
            "elements": [
                {
                    "type": "button",
                    "text": {"type": "plain_text", "text": "查看详情"},
                    "url": "https://crm.example.com/deals/123",
                    "style": "primary"
                }
            ]
        }
    ]
)

print(f"消息已发送: {response['ts']}")
```

### 发送带按钮的互动消息
```bash
curl -X POST https://slack.com/api/chat.postMessage \
  -H "Authorization: Bearer $SLACK_BOT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "#sales-approvals",
    "text": "报价审批请求",
    "blocks": [
      {
        "type": "section",
        "text": {
          "type": "mrkdwn",
          "text": "*报价审批请求*\n客户:科技公司\n金额:$50,000\n折扣:15%(需审批)"
        }
      },
      {
        "type": "actions",
        "elements": [
          {
            "type": "button",
            "text": {"type": "plain_text", "text": "批准"},
            "style": "primary",
            "value": "approve_deal_123"
          },
          {
            "type": "button",
            "text": {"type": "plain_text", "text": "拒绝"},
            "style": "danger",
            "value": "reject_deal_123"
          }
        ]
      }
    ]
  }'
```

### 上传文件
```python
response = client.files_upload_v2(
    channel='#sales',
    file='报价单.pdf',
    title='Q2报价单-科技公司',
    initial_comment='附上最新报价单,请查阅'
)
```

### 创建频道
```python
response = client.conversations_create(
    name='deal-科技公司',
    is_private=False
)

channel_id = response['channel']['id']
print(f"频道已创建: {channel_id}")
```

### 邀请用户到频道
```python
client.conversations_invite(
    channel='C01234567',
    users='U01234567,U98765432'  # 用户ID列表
)
```

### @提及用户
```python
client.chat_postMessage(
    channel='#sales',
    text='<@U01234567> 您负责的客户刚回复了邮件,请尽快跟进!'
)
```

### 线程回复
```python
# 首先发送主消息
response = client.chat_postMessage(
    channel='#sales',
    text='新线索: 科技公司'
)

# 在线程中回复
client.chat_postMessage(
    channel='#sales',
    text='已分配给 @张伟',
    thread_ts=response['ts']  # 关键: thread_ts
)
```

### 创建Slash Command(需在App配置页面设置)
```python
# 配置: /deal-update
# Request URL: https://your-server.com/slack/commands

from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/slack/commands', methods=['POST'])
def handle_command():
    data = request.form
    
    if data['command'] == '/deal-update':
        deal_id = data['text']
        # 查询CRM获取Deal信息
        deal_info = get_deal_from_crm(deal_id)
        
        return jsonify({
            "text": f"Deal: {deal_info['name']}",
            "blocks": [
                {
                    "type": "section",
                    "text": {
                        "type": "mrkdwn",
                        "text": f"*{deal_info['name']}*\n金额: ${deal_info['amount']}\n阶段: {deal_info['stage']}"
                    }
                }
            ]
        })
```

### 定时发送消息
```python
import time

# 定时1小时后发送
scheduled_time = int(time.time()) + 3600

client.chat_scheduleMessage(
    channel='#sales',
    text='提醒: 今天下午3点有客户Demo',
    post_at=scheduled_time
)
```

### 监听事件(Events API)
```python
from slack_bolt import App

app = App(token=os.environ['SLACK_BOT_TOKEN'])

# 监听消息
@app.message("报价")
def handle_pricing_inquiry(message, say):
    say(f"<@{message['user']}> 我已收到您的报价查询,请稍等...")
    # 调用CRM API生成报价
    quote = generate_quote()
    say(f"报价已生成: {quote['url']}")

app.start(port=3000)
```

## 适用场景
- Deal Closed Won自动通知到#sales频道
- 审批流(报价折扣>20%发Slack审批)
- 每日Pipeline播报
- 销售提问→Bot自动查CRM回答
- 客户邮件回复→实时通知销售
- 团队协作(创建Deal专属频道)

## 限制和注意事项
- 免费版消息历史90天(付费版无限)
- API限流: Tier 3每分钟50+请求(大部分端点)
- Block Kit消息最多50个blocks
- 文件上传最大1GB
- Slash Command响应需在3秒内(复杂操作用异步)
- Events API需要公网可访问的HTTPS端点

## 定价
- Free版: 90天消息历史,10个集成
- Pro: $8.75/用户/月 - 无限历史,无限集成
- Business+: $15/用户/月 - 高级安全,24/7支持
- Enterprise Grid: 需联系销售 - 大型组织

## 参考链接
- [官方文档](https://api.slack.com/)
- [Block Kit Builder](https://app.slack.com/block-kit-builder)
- [Python SDK](https://slack.dev/python-slack-sdk/)
- [Events API](https://api.slack.com/apis/connections/events-api)
