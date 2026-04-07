# Gmail API

> Google Gmail API,销售从自己Gmail发邮件(更个人化,送达率高)

## 快速开始

### 安装
```bash
# Python
pip install google-api-python-client google-auth google-auth-oauthlib google-auth-httplib2

# Node.js
npm install googleapis

# Java
# 添加到pom.xml或build.gradle
com.google.apis:google-api-services-gmail:v1-rev110-1.25.0
```

### 认证配置
- **方式:** oauth (OAuth 2.0 或 Service Account)
- **环境变量:**
  - `GOOGLE_APPLICATION_CREDENTIALS` — Service Account JSON文件路径
- **获取步骤(OAuth 2.0):**
  1. 访问 [Google Cloud Console](https://console.cloud.google.com)
  2. 创建项目 → APIs & Services → Enable APIs
  3. 搜索并启用 "Gmail API"
  4. Credentials → Create Credentials → OAuth Client ID
  5. 应用类型选择 "Desktop app" 或 "Web application"
  6. 下载 credentials.json
  7. 首次运行需要浏览器授权
- **获取步骤(Service Account - 企业用户):**
  1. Google Workspace Admin → Security → API Controls
  2. Domain-wide Delegation → Add new
  3. 配置Service Account的Client ID和OAuth Scopes
  4. 下载Service Account JSON密钥
- **验证:**
  ```python
  from google.oauth2.credentials import Credentials
  from googleapiclient.discovery import build
  
  service = build('gmail', 'v1', credentials=creds)
  results = service.users().getProfile(userId='me').execute()
  print(f"邮箱地址: {results['emailAddress']}")
  ```

## 核心能力

### 邮件发送
- 发送邮件(从个人Gmail地址)
- 回复邮件(保持Thread)
- 转发邮件
- 添加抄送/密送
- 附件支持(最大25MB/封)

### 收件箱管理
- 读取邮件列表
- 搜索邮件(支持Gmail高级搜索语法)
- 标记已读/未读
- 邮件归档/删除
- 垃圾邮件处理

### 邮件标签
- 创建自定义标签
- 批量打标签(自动化分类)
- 标签过滤和搜索
- 系统标签管理(INBOX/SENT/DRAFT等)

### 草稿管理
- 创建草稿
- 更新草稿
- 发送草稿
- 删除草稿

### 邮件线程
- Thread(会话)管理
- 获取完整对话历史
- 按Thread批量操作

### 批量操作
- 批量修改标签
- 批量标记已读
- 批量归档/删除

## 使用示例

### 发送简单邮件(Python)
```python
import base64
from email.mime.text import MIMEText
from googleapiclient.discovery import build
from google.oauth2.credentials import Credentials

def send_email(to, subject, body):
    creds = Credentials.from_authorized_user_file('token.json')
    service = build('gmail', 'v1', credentials=creds)
    
    message = MIMEText(body, 'html')
    message['to'] = to
    message['subject'] = subject
    message['from'] = 'me'
    
    raw = base64.urlsafe_b64encode(message.as_bytes()).decode()
    
    try:
        sent = service.users().messages().send(
            userId='me',
            body={'raw': raw}
        ).execute()
        print(f"邮件已发送,ID: {sent['id']}")
        return sent
    except Exception as e:
        print(f"发送失败: {e}")
        return None

# 调用
send_email(
    to='customer@example.com',
    subject='关于明天Demo的确认',
    body='<p>您好,</p><p>我们的Demo会议已安排在明天下午2点...</p>'
)
```

### 发送带附件的邮件
```python
import base64
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.base import MIMEBase
from email import encoders

def send_with_attachment(to, subject, body, file_path):
    message = MIMEMultipart()
    message['to'] = to
    message['subject'] = subject
    
    # 邮件正文
    message.attach(MIMEText(body, 'html'))
    
    # 附件
    with open(file_path, 'rb') as f:
        part = MIMEBase('application', 'octet-stream')
        part.set_payload(f.read())
        encoders.encode_base64(part)
        part.add_header(
            'Content-Disposition',
            f'attachment; filename= {os.path.basename(file_path)}'
        )
        message.attach(part)
    
    raw = base64.urlsafe_b64encode(message.as_bytes()).decode()
    
    service.users().messages().send(
        userId='me',
        body={'raw': raw}
    ).execute()

# 发送报价单
send_with_attachment(
    to='customer@example.com',
    subject='Q2报价单',
    body='<p>附件是我们的报价单...</p>',
    file_path='报价单.pdf'
)
```

### 搜索邮件
```python
# 搜索最近7天来自特定客户的邮件
query = 'from:customer@example.com newer_than:7d'

results = service.users().messages().list(
    userId='me',
    q=query,
    maxResults=10
).execute()

messages = results.get('messages', [])

for msg in messages:
    # 获取邮件详情
    message = service.users().messages().get(
        userId='me',
        id=msg['id']
    ).execute()
    
    headers = message['payload']['headers']
    subject = next(h['value'] for h in headers if h['name'] == 'Subject')
    print(f"主题: {subject}")
```

### 自动回复邮件
```python
def auto_reply(original_message_id, reply_body):
    # 获取原始邮件
    original = service.users().messages().get(
        userId='me',
        id=original_message_id,
        format='metadata',
        metadataHeaders=['Subject', 'From', 'Message-ID', 'References']
    ).execute()
    
    headers = original['payload']['headers']
    subject = next(h['value'] for h in headers if h['name'] == 'Subject')
    to = next(h['value'] for h in headers if h['name'] == 'From')
    message_id = next(h['value'] for h in headers if h['name'] == 'Message-ID')
    thread_id = original['threadId']
    
    # 构建回复
    message = MIMEText(reply_body, 'html')
    message['to'] = to
    message['subject'] = f"Re: {subject}" if not subject.startswith('Re:') else subject
    message['In-Reply-To'] = message_id
    message['References'] = message_id
    
    raw = base64.urlsafe_b64encode(message.as_bytes()).decode()
    
    # 发送回复(保持在同一Thread)
    service.users().messages().send(
        userId='me',
        body={'raw': raw, 'threadId': thread_id}
    ).execute()
```

### 批量打标签(自动化分类)
```python
# 给所有包含"报价"的邮件打标签
query = 'subject:报价 OR subject:proposal'
results = service.users().messages().list(userId='me', q=query).execute()

# 创建或获取标签
label_name = '待跟进-报价'
labels = service.users().labels().list(userId='me').execute()
label_id = next((l['id'] for l in labels['labels'] if l['name'] == label_name), None)

if not label_id:
    label = service.users().labels().create(
        userId='me',
        body={'name': label_name, 'messageListVisibility': 'show', 'labelListVisibility': 'labelShow'}
    ).execute()
    label_id = label['id']

# 批量打标签
for msg in results.get('messages', []):
    service.users().messages().modify(
        userId='me',
        id=msg['id'],
        body={'addLabelIds': [label_id]}
    ).execute()
```

## 适用场景
- 销售想从自己Gmail发邮件(比SendGrid更个人化,送达率高)
- 自动回复客户邮件(基于规则)
- 监控收件箱(客户回复自动进CRM)
- 邮件归档和自动分类(打标签)
- 批量跟进(搜索未回复的客户邮件)

## 限制和注意事项
- 免费Gmail每日发送限额: 500封
- Google Workspace限额: 2000封/天
- 单封邮件附件总大小: 25MB
- OAuth需用户首次授权(除非用Refresh Token)
- Service Account需Domain-wide Delegation(仅企业)
- 批量发送容易被标记为垃圾邮件(建议用SendGrid做大规模群发)
- API配额: 每日25亿配额单位(读取1单位,发送100单位)

## 定价
- Gmail API免费使用
- Google Workspace(企业邮箱):
  - Business Starter: $6/用户/月
  - Business Standard: $12/用户/月
  - Business Plus: $18/用户/月

## 参考链接
- [官方文档](https://developers.google.com/gmail/api)
- [API参考](https://developers.google.com/gmail/api/reference/rest)
- [Python快速开始](https://developers.google.com/gmail/api/quickstart/python)
- [搜索语法](https://support.google.com/mail/answer/7190)
