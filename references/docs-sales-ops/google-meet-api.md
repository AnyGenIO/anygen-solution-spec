# Google Meet API

> Google Meet会议管理(通过Google Calendar API创建Meet链接)

## 快速开始

### 安装
```bash
# Python
pip install google-api-python-client google-auth google-auth-oauthlib google-auth-httplib2

# Node.js
npm install googleapis
```

### 认证配置
- **方式:** oauth (Service Account或OAuth 2.0)
- **环境变量:**
  - `GOOGLE_APPLICATION_CREDENTIALS` — Service Account JSON文件路径
- **获取步骤(Service Account):**
  1. 访问 [Google Cloud Console](https://console.cloud.google.com)
  2. 创建项目
  3. APIs & Services → Enable APIs → 启用 "Google Calendar API"
  4. Credentials → Create Credentials → Service Account
  5. 创建密钥(JSON格式) → 下载保存
  6. 如需代表用户操作,需配置Domain-wide Delegation(仅Google Workspace)
- **验证:**
  ```python
  from google.oauth2 import service_account
  from googleapiclient.discovery import build
  
  SCOPES = ['https://www.googleapis.com/auth/calendar']
  credentials = service_account.Credentials.from_service_account_file(
      'credentials.json', scopes=SCOPES)
  
  service = build('calendar', 'v3', credentials=credentials)
  calendar_list = service.calendarList().list().execute()
  print(calendar_list)
  ```

## 核心能力

### 通过Calendar API创建Meet会议
- 创建带Meet链接的日历事件
- 自动生成Meet会议链接
- 邀请参会者
- 设置会议提醒

### 会议详情管理
- 获取会议信息
- 更新会议时间/标题
- 取消会议
- 添加/删除参会者

### 录制管理(需Enterprise)
- 录制文件存储到Google Drive
- 获取录制链接
- 共享权限管理
- 录制文件下载

### 参会者报告(需Enterprise)
- 参会人员列表
- 参会时长
- 加入/离开时间

## 使用示例

### 创建带Meet链接的会议(Python)
```python
from google.oauth2 import service_account
from googleapiclient.discovery import build
from datetime import datetime, timedelta
import pytz

SCOPES = ['https://www.googleapis.com/auth/calendar']
credentials = service_account.Credentials.from_service_account_file(
    'credentials.json', scopes=SCOPES)

service = build('calendar', 'v3', credentials=credentials)

# 创建会议
event = {
    'summary': '产品Demo-科技公司',
    'description': '演示企业版功能',
    'start': {
        'dateTime': '2026-04-10T14:00:00',
        'timeZone': 'Asia/Shanghai',
    },
    'end': {
        'dateTime': '2026-04-10T14:30:00',
        'timeZone': 'Asia/Shanghai',
    },
    'attendees': [
        {'email': 'customer@example.com'},
        {'email': 'ae@yourcompany.com'},
    ],
    'conferenceData': {
        'createRequest': {
            'requestId': 'demo-' + str(int(datetime.now().timestamp())),
            'conferenceSolutionKey': {'type': 'hangoutsMeet'}
        }
    },
    'reminders': {
        'useDefault': False,
        'overrides': [
            {'method': 'email', 'minutes': 24 * 60},
            {'method': 'popup', 'minutes': 30},
        ],
    },
}

created_event = service.events().insert(
    calendarId='primary',
    body=event,
    conferenceDataVersion=1,
    sendUpdates='all'
).execute()

print(f"会议已创建: {created_event['htmlLink']}")
print(f"Meet链接: {created_event['hangoutLink']}")
```

### 创建Meet会议(curl - 较复杂,推荐用SDK)
```bash
# 首先需要获取OAuth Token(省略OAuth流程)

curl -X POST https://www.googleapis.com/calendar/v3/calendars/primary/events?conferenceDataVersion=1 \
  -H "Authorization: Bearer $GOOGLE_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "summary": "客户Demo",
    "start": {
      "dateTime": "2026-04-10T14:00:00+08:00",
      "timeZone": "Asia/Shanghai"
    },
    "end": {
      "dateTime": "2026-04-10T14:30:00+08:00",
      "timeZone": "Asia/Shanghai"
    },
    "attendees": [
      {"email": "customer@example.com"}
    ],
    "conferenceData": {
      "createRequest": {
        "requestId": "unique-request-id",
        "conferenceSolutionKey": {"type": "hangoutsMeet"}
      }
    }
  }' | jq '.hangoutLink'
```

### 获取会议详情
```python
EVENT_ID = 'abc123def456'

event = service.events().get(
    calendarId='primary',
    eventId=EVENT_ID
).execute()

print(f"会议: {event['summary']}")
print(f"Meet链接: {event.get('hangoutLink', '无')}")
print(f"参会者: {[a['email'] for a in event.get('attendees', [])]}")
```

### 更新会议时间
```python
event['start']['dateTime'] = '2026-04-11T15:00:00'
event['end']['dateTime'] = '2026-04-11T15:30:00'

updated_event = service.events().update(
    calendarId='primary',
    eventId=EVENT_ID,
    body=event,
    sendUpdates='all'
).execute()

print(f"会议已更新: {updated_event['updated']}")
```

### 取消会议
```python
service.events().delete(
    calendarId='primary',
    eventId=EVENT_ID,
    sendUpdates='all'
).execute()

print("会议已取消")
```

### 列出近期会议
```python
from datetime import datetime
import pytz

now = datetime.now(pytz.UTC).isoformat()
time_max = (datetime.now(pytz.UTC) + timedelta(days=7)).isoformat()

events_result = service.events().list(
    calendarId='primary',
    timeMin=now,
    timeMax=time_max,
    singleEvents=True,
    orderBy='startTime'
).execute()

events = events_result.get('items', [])

for event in events:
    start = event['start'].get('dateTime', event['start'].get('date'))
    print(f"{start}: {event['summary']}")
    if 'hangoutLink' in event:
        print(f"  Meet: {event['hangoutLink']}")
```

### 批量创建会议
```python
customers = [
    {'email': 'customer1@example.com', 'name': '科技公司', 'time': '2026-04-10T14:00:00'},
    {'email': 'customer2@example.com', 'name': '创新企业', 'time': '2026-04-11T10:00:00'},
]

for customer in customers:
    event = {
        'summary': f"产品Demo-{customer['name']}",
        'start': {'dateTime': customer['time'], 'timeZone': 'Asia/Shanghai'},
        'end': {'dateTime': customer['time'].replace('00:00', '00:30'), 'timeZone': 'Asia/Shanghai'},
        'attendees': [{'email': customer['email']}],
        'conferenceData': {
            'createRequest': {
                'requestId': f"demo-{customer['name']}-{int(datetime.now().timestamp())}",
                'conferenceSolutionKey': {'type': 'hangoutsMeet'}
            }
        }
    }
    
    created = service.events().insert(
        calendarId='primary',
        body=event,
        conferenceDataVersion=1,
        sendUpdates='all'
    ).execute()
    
    print(f"{customer['name']}: {created['hangoutLink']}")
```

## 适用场景
- 企业使用Google Workspace(自带Meet)
- 自动创建客户Demo会议
- 与Google Calendar深度集成
- 团队已习惯Google生态
- 需要录制存储到Google Drive

## 限制和注意事项
- Meet录制和报告需Google Workspace Enterprise版
- Meet本身API功能有限(主要通过Calendar API)
- 无法直接通过API控制会议内功能(静音/移除参会者等)
- Service Account需Domain-wide Delegation(仅Google Workspace管理员可配置)
- API配额: 每日100万次请求(Calendar API)

## 定价
- Google Workspace Business Starter: $6/用户/月 - 基础Meet(100人,无录制)
- Business Standard: $12/用户/月 - 150人,录制,出席报告
- Business Plus: $18/用户/月 - 500人,增强安全
- Enterprise: 需联系销售 - 1000人,高级功能

## 参考链接
- [官方文档](https://developers.google.com/calendar/api)
- [Calendar API参考](https://developers.google.com/calendar/api/v3/reference)
- [创建Meet会议](https://developers.google.com/calendar/api/guides/create-events#conferencing)
- [Python快速开始](https://developers.google.com/calendar/api/quickstart/python)
