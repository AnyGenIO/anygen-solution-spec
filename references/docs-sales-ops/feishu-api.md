# 飞书 OpenAPI

> 飞书开放平台API,消息/日历/文档/审批

## 快速开始

### 安装
```bash
# Python官方SDK
pip install lark-oapi

# Node.js官方SDK
npm install @larksuiteoapi/node-sdk

# Go官方SDK
go get github.com/larksuite/oapi-sdk-go/v3
```

### 认证配置
- **方式:** api_key (App ID + App Secret)
- **环境变量:**
  - `FEISHU_APP_ID` — 飞书应用App ID
  - `FEISHU_APP_SECRET` — 飞书应用App Secret
- **获取步骤:**
  1. 访问 [open.feishu.cn](https://open.feishu.cn/app)
  2. 创建企业自建应用
  3. 凭证与基础信息 → 复制App ID和App Secret
  4. 权限管理 → 添加所需权限(如im:message, calendar:event等)
  5. 版本管理与发布 → 创建版本并申请发布
  6. 管理员在飞书管理后台审批
- **验证:**
  ```bash
  # 获取tenant_access_token
  TOKEN=$(curl -s -X POST https://open.feishu.cn/open-apis/auth/v3/tenant_access_token/internal \
    -H "Content-Type: application/json" \
    -d '{
      "app_id": "'$FEISHU_APP_ID'",
      "app_secret": "'$FEISHU_APP_SECRET'"
    }' | jq -r '.tenant_access_token')
  
  echo "Token: $TOKEN"
  ```

## 核心能力

### 消息发送
- 发消息到群聊/私聊
- 富文本消息(卡片/文本/图片/文件)
- 互动卡片(按钮/表单)
- @提及用户
- 消息回复和撤回

### 日历管理
- 创建日程
- 邀请参会人
- 会议室预订
- 日程提醒
- 忙闲查询

### 文档协作
- 创建文档/表格/多维表格
- 读写云文档
- 评论和协作
- 权限管理

### 审批流程
- 发起审批
- 查询审批状态
- 审批回调
- 自定义审批流

### 通讯录
- 查询组织架构
- 用户信息
- 部门信息

### 任务管理
- 创建任务
- 分配协作人
- 任务进度追踪

## 使用示例

### 获取tenant_access_token
```bash
TOKEN=$(curl -s -X POST https://open.feishu.cn/open-apis/auth/v3/tenant_access_token/internal \
  -H "Content-Type: application/json" \
  -d '{
    "app_id": "'$FEISHU_APP_ID'",
    "app_secret": "'$FEISHU_APP_SECRET'"
  }' | jq -r '.tenant_access_token')
```

### 发送文本消息
```bash
curl -X POST https://open.feishu.cn/open-apis/im/v1/messages?receive_id_type=chat_id \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "receive_id": "oc_abc123def456",
    "msg_type": "text",
    "content": "{\"text\":\"新商机成交! 🎉\\n客户:科技公司\\n金额:¥50,000\"}"
  }'
```

### 使用Python SDK发送卡片消息
```python
import lark_oapi as lark
import os

# 初始化client
client = lark.Client.builder() \
    .app_id(os.environ['FEISHU_APP_ID']) \
    .app_secret(os.environ['FEISHU_APP_SECRET']) \
    .build()

# 构建卡片
card = {
    "config": {
        "wide_screen_mode": True
    },
    "elements": [
        {
            "tag": "div",
            "text": {
                "tag": "lark_md",
                "content": "**新商机成交!** 🎉"
            }
        },
        {
            "tag": "div",
            "fields": [
                {"is_short": True, "text": {"tag": "lark_md", "content": "**客户:**\n科技公司"}},
                {"is_short": True, "text": {"tag": "lark_md", "content": "**金额:**\n¥50,000"}},
                {"is_short": True, "text": {"tag": "lark_md", "content": "**负责人:**\n张伟"}},
                {"is_short": True, "text": {"tag": "lark_md", "content": "**阶段:**\nClosed Won"}}
            ]
        },
        {
            "tag": "action",
            "actions": [
                {
                    "tag": "button",
                    "text": {"tag": "plain_text", "content": "查看详情"},
                    "type": "primary",
                    "url": "https://crm.example.com/deals/123"
                }
            ]
        }
    ]
}

# 发送消息
request = lark.im.v1.CreateMessageRequest.builder() \
    .receive_id_type("chat_id") \
    .request_body(lark.im.v1.CreateMessageRequestBody.builder()
        .receive_id("oc_abc123def456")
        .msg_type("interactive")
        .content(json.dumps({"card": card}))
        .build()) \
    .build()

response = client.im.v1.message.create(request)
print(f"消息已发送: {response.data.message_id}")
```

### 创建日程
```bash
curl -X POST https://open.feishu.cn/open-apis/calendar/v4/calendars/primary/events \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "summary": "客户Demo-科技公司",
    "description": "演示企业版功能",
    "start_time": {
      "timestamp": "1712736000"
    },
    "end_time": {
      "timestamp": "1712737800"
    },
    "attendee_ability": "can_see_others",
    "attendees": [
      {
        "type": "user",
        "attendee_id": "ou_abc123"
      }
    ],
    "reminders": [
      {
        "minutes": 30
      }
    ]
  }'
```

### 发起审批
```bash
curl -X POST https://open.feishu.cn/open-apis/approval/v4/instances \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "approval_code": "approval_code_123",
    "user_id": "ou_abc123",
    "form": [
      {
        "id": "field_1",
        "type": "input",
        "value": "科技公司-企业版报价"
      },
      {
        "id": "field_2",
        "type": "amount",
        "value": "50000"
      },
      {
        "id": "field_3",
        "type": "textarea",
        "value": "客户要求15%折扣,需审批"
      }
    ]
  }'
```

### 查询用户信息
```bash
USER_ID="ou_abc123"

curl -H "Authorization: Bearer $TOKEN" \
  "https://open.feishu.cn/open-apis/contact/v3/users/$USER_ID" | jq
```

### 创建任务
```bash
curl -X POST https://open.feishu.cn/open-apis/task/v2/tasks \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "summary": "准备客户Demo材料",
    "description": "科技公司Demo,需要准备企业版功能演示",
    "due": {
      "timestamp": "1712650800"
    },
    "collaborators": [
      {
        "id": "ou_abc123",
        "type": "user"
      }
    ]
  }'
```

### 上传文件
```bash
curl -X POST https://open.feishu.cn/open-apis/im/v1/files \
  -H "Authorization: Bearer $TOKEN" \
  -F "file_type=stream" \
  -F "file_name=报价单.pdf" \
  -F "file=@报价单.pdf"
```

### Webhook订阅(事件回调)
```python
# 需要在飞书开放平台配置事件回调URL
# 接收事件示例

from flask import Flask, request, jsonify
import hashlib
import json

app = Flask(__name__)

@app.route('/feishu/webhook', methods=['POST'])
def handle_event():
    # 验证签名
    timestamp = request.headers.get('X-Lark-Request-Timestamp')
    nonce = request.headers.get('X-Lark-Request-Nonce')
    signature = request.headers.get('X-Lark-Signature')
    
    # 事件数据
    data = request.json
    
    if data['type'] == 'url_verification':
        # URL验证
        return jsonify({'challenge': data['challenge']})
    
    # 处理消息事件
    if data['header']['event_type'] == 'im.message.receive_v1':
        message = data['event']['message']
        # 自动回复
        reply_message(message['message_id'], "我已收到您的消息")
    
    return jsonify({'code': 0})

app.run(port=8080)
```

## 适用场景
- 给销售团队派任务/发通知(替代Slack)
- 创建审批流(报价审批/合同审批)
- 在飞书群里推送销售数据(Deal成交通知)
- 会议纪要自动写入飞书文档
- 自动创建客户Demo日程
- 销售数据Dashboard(飞书多维表格)

## 限制和注意事项
- 需要企业管理员授权应用
- API限流: 按应用级别(一般100-200次/分钟)
- 部分API需要特定权限(需在开放平台申请)
- 应用发布需要管理员审批
- Webhook需要公网HTTPS端点
- tenant_access_token有效期2小时(需刷新)

## 定价
飞书企业版定价(含API免费使用):
- 标准版: 免费 - 基础功能
- 专业版: 按需定价 - 高级功能
- 旗舰版: 按需定价 - 企业级功能
- API调用免费(在合理限流内)

## 参考链接
- [官方文档](https://open.feishu.cn/document/home/index)
- [API参考](https://open.feishu.cn/document/server-docs/api-call-guide/calling-process/overview)
- [Python SDK](https://github.com/larksuite/oapi-sdk-python)
- [消息卡片搭建工具](https://open.feishu.cn/tool/cardbuilder)
