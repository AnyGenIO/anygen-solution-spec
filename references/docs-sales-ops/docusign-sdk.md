<!-- doc-level: detailed -->

# DocuSign SDK

> 电子签名平台 SDK,发送合同/收集签名,支持多语言 SDK(Python/Node.js/Java/C#/PHP/Ruby)

DocuSign 是全球领先的电子签名和数字交易管理平台,提供安全、合法的电子签名服务。DocuSign eSignature API 支持编程方式发送签署请求(Envelope)、管理模板(Template)、查询签署状态和下载已签文档。广泛应用于销售合同、HR 入职、采购订单、金融协议等场景。DocuSign 提供 Python、Node.js、Java、C#、PHP、Ruby 等主流语言的官方 SDK,以及 REST API 支持。

## 为什么选这个工具

### DocuSign vs Adobe Sign vs PandaDoc

| 维度 | DocuSign | Adobe Sign | PandaDoc |
|------|----------|-----------|----------|
| **定位** | 企业级电子签名标杆 | Adobe 生态电子签名 | 文档自动化 + 电子签名 |
| **市场份额** | 70%+(行业第一) | 20% | 5% |
| **价格** | $25-$60/用户/月 | $20-$40/用户/月 | $19-$49/用户/月 |
| **API 成熟度** | 极强(10+ 语言 SDK) | 强(REST API) | 中等(REST API) |
| **模板功能** | 强大(拖拽设计器) | 强大 | 基础 |
| **集成生态** | Salesforce/SAP/Workday 等 400+ | Adobe 生态为主 | Salesforce/HubSpot 等 |
| **高级功能** | 高级工作流,批量发送,CLM | PDF 原生集成 | 文档分析,CPQ 集成 |
| **合规认证** | SOC 2,ISO 27001,HIPAA,FedRAMP | SOC 2,ISO 27001 | SOC 2 |
| **适用场景** | 大型企业,高合规要求 | Adobe 用户,创意团队 | 中小企业,销售团队 |

## 快速开始

### 安装

DocuSign 提供多语言 SDK,选择适合你的语言:

```bash
# Python
pip install docusign-esign

# Node.js
npm install docusign-esign

# Java (Maven pom.xml)
<dependency>
  <groupId>com.docusign</groupId>
  <artifactId>docusign-esign-java</artifactId>
  <version>6.5.0</version>
</dependency>

# C# (NuGet)
Install-Package DocuSign.eSign.dll

# PHP (Composer)
composer require docusign/esign-client

# Ruby
gem install docusign_esign

# Go (非官方)
go get github.com/docusign/docusign-go-client
```

### 认证配置(详细步骤)

DocuSign 支持两种认证方式:JWT Grant(适合后端服务)和 Authorization Code Grant(适合 Web 应用)。

#### 方式一:JWT Grant(推荐用于生产环境)

**步骤 1:创建 DocuSign 开发者账号**

1. 访问 https://developers.docusign.com
2. 点击 **Sign Up for Free** 注册开发者账号
3. 验证邮箱后登录 **Developer Portal**
4. 免费获得 **Demo 环境**(沙箱),可测试所有功能

**步骤 2:创建集成应用(Integration Key)**

1. 登录 Developer Portal → 点击 **My Apps & Keys**
2. 点击 **Add App and Integration Key**
3. 填写应用信息:
   - App Name: `My eSignature Integration`
4. 点击 **Create App** → 记录 **Integration Key**(即 Client ID)

**步骤 3:生成 RSA 密钥对**

1. 在 **My Apps & Keys** 页面,找到刚创建的 App
2. 滚动到 **Authentication** 部分
3. 点击 **Add RSA Keypair** → 系统自动生成密钥对
4. 点击 **Download Private Key** → 保存为 `private.key` 文件(仅显示一次,请妥善保管)
5. 记录显示的 **Public Key**(无需手动保存,已自动上传到 DocuSign)

**步骤 4:添加 Redirect URI(可选,用于 Admin Consent)**

1. 在 **Authentication** 部分,点击 **Add URI**
2. 输入:`https://localhost`(开发环境)或你的实际回调地址
3. 点击 **Save**

**步骤 5:获取 User ID 和 Account ID**

```bash
# User ID(GUID 格式)
# 登录 DocuSign Demo 账号 → 右上角头像 → Settings → Apps & Keys
# 在页面顶部可看到 "API Account ID" 和 "User ID"

# 或通过 API 查询
# API Account ID: 对应下面的 base_account_id
# User ID: 对应 userId
```

**步骤 6:执行 Individual Consent(管理员授权)**

在浏览器中访问以下 URL(替换 `{INTEGRATION_KEY}` 和 `{REDIRECT_URI}`):

```
https://account-d.docusign.com/oauth/auth?
  response_type=code&
  scope=signature%20impersonation&
  client_id={INTEGRATION_KEY}&
  redirect_uri={REDIRECT_URI}
```

示例(Demo 环境):
```
https://account-d.docusign.com/oauth/auth?response_type=code&scope=signature%20impersonation&client_id=12345678-abcd-1234-abcd-123456789abc&redirect_uri=https://localhost
```

生产环境替换为:
```
https://account.docusign.com/oauth/auth?...
```

点击 **Allow Access** 完成授权。授权后会跳转到 `REDIRECT_URI?code=xxx`,可忽略 code(JWT 方式不需要)。

**步骤 7:使用 JWT 获取 Access Token(Python 示例)**

```python
from docusign_esign import ApiClient
from docusign_esign.client.api_exception import ApiException

# 配置
integration_key = "12345678-abcd-1234-abcd-123456789abc"  # Integration Key
user_id = "abcd1234-5678-90ab-cdef-123456789012"  # User ID(GUID)
oauth_base_path = "account-d.docusign.com"  # Demo 环境,生产用 account.docusign.com
private_key_path = "private.key"  # RSA 私钥文件路径

# 初始化 API 客户端
api_client = ApiClient()
api_client.set_base_path("https://demo.docusign.net/restapi")  # Demo 环境,生产用 https://na1.docusign.net/restapi

# 读取私钥
with open(private_key_path, 'r') as key_file:
    private_key = key_file.read()

# 请求 JWT Access Token
try:
    token_response = api_client.request_jwt_user_token(
        client_id=integration_key,
        user_id=user_id,
        oauth_host_name=oauth_base_path,
        private_key_bytes=private_key,
        expires_in=3600,  # Token 有效期 1 小时
        scopes=["signature", "impersonation"]
    )
    
    access_token = token_response.access_token
    print(f"Access Token: {access_token}")
    
    # 设置 Access Token
    api_client.set_default_header("Authorization", f"Bearer {access_token}")
    
except ApiException as e:
    print(f"Error: {e}")
```

**步骤 8:获取 Account ID**

```python
from docusign_esign import AccountsApi

accounts_api = AccountsApi(api_client)
user_info = accounts_api.get_user_info()

# 获取默认账户 ID
account_id = user_info.accounts[0].account_id
base_uri = user_info.accounts[0].base_uri  # 如 https://demo.docusign.net/restapi
print(f"Account ID: {account_id}")
print(f"Base URI: {base_uri}")

# 更新 API Client Base Path
api_client.set_base_path(base_uri)
```

#### 方式二:Authorization Code Grant(适合 Web 应用)

1. 用户点击"连接 DocuSign"按钮,跳转到 DocuSign 授权页面
2. 用户登录并授权
3. DocuSign 回调你的 `REDIRECT_URI?code={AUTHORIZATION_CODE}`
4. 后端用 Authorization Code 交换 Access Token

```python
# 交换 Access Token
token_response = api_client.generate_access_token(
    client_id=integration_key,
    client_secret=client_secret,  # 需在 App 中启用 "Secret Key"
    code=authorization_code
)
access_token = token_response.access_token
```

#### 常见认证错误及解决方案

1. **错误:`consent_required`**
   - 原因:未执行 Individual Consent(管理员授权)
   - 解决:访问 Consent URL 完成授权

2. **错误:`invalid_grant`(JWT 认证时)**
   - 原因:私钥文件错误或 User ID 错误
   - 解决:重新下载私钥,检查 User ID 是否为 GUID 格式

3. **错误:`account_not_found`**
   - 原因:User ID 对应的账户不存在
   - 解决:检查 User ID 是否正确(在 DocuSign Settings 中查看)

4. **错误:`AUTHORIZATION_INVALID_TOKEN`(API 调用时)**
   - 原因:Access Token 已过期(1 小时有效期)
   - 解决:重新请求 JWT Token

5. **错误:`USER_LACKS_PERMISSIONS`**
   - 原因:用户权限不足(如无权发送 Envelope)
   - 解决:在 DocuSign Admin Console 中为用户分配权限

### 环境变量配置

```bash
# DocuSign JWT 认证凭证
export DOCUSIGN_INTEGRATION_KEY="12345678-abcd-1234-abcd-123456789abc"
export DOCUSIGN_USER_ID="abcd1234-5678-90ab-cdef-123456789012"
export DOCUSIGN_ACCOUNT_ID="your_account_id"
export DOCUSIGN_PRIVATE_KEY_PATH="./private.key"

# 环境配置(Demo 或生产)
export DOCUSIGN_ENV="demo"  # 或 "production"
export DOCUSIGN_BASE_PATH="https://demo.docusign.net/restapi"  # Demo 环境
# 生产环境: https://na1.docusign.net/restapi (NA1 数据中心)
# 或 https://eu.docusign.net/restapi (EU 数据中心)

# OAuth Base Path
export DOCUSIGN_OAUTH_BASE_PATH="account-d.docusign.com"  # Demo
# 生产: account.docusign.com
```

### 验证

```python
# 验证认证是否成功
from docusign_esign import ApiClient, AccountsApi

api_client = ApiClient()
api_client.set_base_path(os.getenv("DOCUSIGN_BASE_PATH"))

# 获取 Access Token(省略,见上文)
# ...

# 查询账户信息
accounts_api = AccountsApi(api_client)
user_info = accounts_api.get_user_info()
print(f"User Name: {user_info.accounts[0].account_name}")
print(f"Account ID: {user_info.accounts[0].account_id}")
```

## 核心能力

### Envelope(签署请求)管理

- **创建 Envelope**:上传文档并发送签署请求
- **Envelope 状态**:created(草稿)、sent(已发送)、delivered(已送达)、completed(已完成)、declined(已拒绝)、voided(已作废)
- **批量发送(Bulk Send)**:一次发送给多个签署人(如季度末批量发合同)
- **定时发送**:设置发送时间
- **过期时间**:设置 Envelope 过期时间(默认 120 天)

### Recipient(签署人)管理

- **签署人类型**:
  - Signer:需要签名的人
  - Carbon Copy:抄送人(仅接收已签文档)
  - Certified Delivery:认证接收人(需确认收到)
  - Editor:可编辑文档字段的人
  - Intermediary:中间人(接收后转发给下一个签署人)
- **签署顺序**:顺序签署(Routing Order)或并行签署
- **认证方式**:
  - Email:仅邮箱(默认)
  - Access Code:访问码
  - Phone Authentication:短信验证
  - ID Verification:身份证验证(需启用 ID Check)

### Template(模板)管理

- **创建模板**:在 DocuSign Web UI 中创建,或通过 API 创建
- **模板字段**:签名位置、日期、文本框、复选框、下拉框等
- **角色(Role)**:模板中定义 "Signer 1"、"Signer 2" 等角色,发送时指定具体人员
- **使用模板发送**:只需传递签署人信息,无需上传文档

### Tab(签署字段)管理

- **签名 Tab**:Sign Here(签名)、Initial Here(首字母缩写)、Date Signed(签署日期)
- **输入 Tab**:Text(文本框)、Checkbox(复选框)、Radio Group(单选)、Dropdown(下拉)、Number(数字)
- **计算 Tab**:Formula Tab(公式字段,如自动计算总金额)
- **附件 Tab**:Signer Attachment(签署人上传附件)
- **Tab 定位**:通过坐标(`x_position`, `y_position`, `page_number`)或 Anchor String(锚点文本,如 "\**SIGN HERE\**")

### Webhook(Connect)通知

- **事件类型**:
  - envelope-sent:Envelope 已发送
  - envelope-delivered:签署人已收到
  - envelope-completed:所有人已签署
  - envelope-declined:签署人拒绝签署
  - envelope-voided:Envelope 已作废
  - recipient-authenticationfailure:认证失败
- **Webhook 配置**:在 DocuSign Admin Console → Connect 中配置 Webhook URL
- **重试机制**:失败后自动重试(最多 100 次)

### 嵌入式签署(Embedded Signing)

- **在你的应用内完成签署**:无需跳转到 DocuSign 网站
- **RecipientViewRequest**:生成签署 URL,嵌入 iframe 或新窗口
- **签署后回调**:签署完成后跳转到你的 `return_url`

### Document Management

- **文档格式支持**:PDF、Word、Excel、PowerPoint、图片(自动转 PDF)
- **文档大小限制**:单个文档 25 MB,Envelope 总大小 25 MB
- **下载已签文档**:下载单个文档或合并的 PDF
- **Certificate of Completion**:签署证书(包含签署人、时间、IP 等审计信息)

## 使用示例(行业场景)

### 场景 1:SaaS 销售 - 发送销售合同签署请求

```python
from docusign_esign import EnvelopesApi, EnvelopeDefinition, Document, Signer, SignHere, Tabs, Recipients
import base64

# 初始化(省略 API Client 配置,见上文)
envelopes_api = EnvelopesApi(api_client)

# 读取合同 PDF
with open('sales_contract.pdf', 'rb') as file:
    pdf_content = file.read()

# 创建 Envelope
envelope_definition = EnvelopeDefinition(
    email_subject="请签署销售合同 - Tech Corp",
    email_blurb="您好,请查看并签署附件中的销售合同。如有疑问请联系我们。",
    documents=[
        Document(
            document_base64=base64.b64encode(pdf_content).decode('utf-8'),
            name="销售合同",
            file_extension="pdf",
            document_id="1"
        )
    ],
    recipients=Recipients(
        signers=[
            Signer(
                email="client@example.com",
                name="Zhang San",
                recipient_id="1",
                routing_order="1",  # 第一个签署人
                tabs=Tabs(
                    sign_here_tabs=[
                        SignHere(
                            document_id="1",
                            page_number="1",
                            x_position="100",  # 距左边缘 100 像素
                            y_position="700",  # 距顶部 700 像素
                            scale_value="0.7"  # 签名框大小(0.5-1.0)
                        )
                    ]
                )
            )
        ]
    ),
    status="sent"  # sent=立即发送,created=保存为草稿
)

# 发送 Envelope
result = envelopes_api.create_envelope(account_id, envelope_definition=envelope_definition)
print(f"Envelope ID: {result.envelope_id}")
print(f"Status: {result.status}")
```

### 场景 2:HR - 使用模板发送入职文件(Offer Letter)

```python
from docusign_esign import TemplateRole

# 使用已创建的模板(在 DocuSign Web UI 中创建)
envelope_definition = EnvelopeDefinition(
    email_subject="欢迎加入 Tech Corp - 请签署 Offer Letter",
    template_id="your_template_id",  # 模板 ID
    template_roles=[
        TemplateRole(
            email="newemployee@example.com",
            name="Li Si",
            role_name="Employee",  # 模板中定义的角色名称
            tabs=Tabs(
                text_tabs=[
                    {
                        "tab_label": "Title",  # 模板中字段的标签
                        "value": "Senior Software Engineer"
                    },
                    {
                        "tab_label": "StartDate",
                        "value": "2026-05-01"
                    },
                    {
                        "tab_label": "Salary",
                        "value": "$120,000"
                    }
                ]
            )
        ),
        TemplateRole(
            email="hr@techcorp.com",
            name="HR Manager",
            role_name="HRManager"
        )
    ],
    status="sent"
)

result = envelopes_api.create_envelope(account_id, envelope_definition=envelope_definition)
```

### 场景 3:AE - 查询 Envelope 签署状态

```python
# 查询单个 Envelope 状态
envelope = envelopes_api.get_envelope(account_id, envelope_id)
print(f"Status: {envelope.status}")
print(f"Sent Date: {envelope.sent_date_time}")
print(f"Completed Date: {envelope.completed_date_time}")

# 查询签署人状态
recipients = envelopes_api.list_recipients(account_id, envelope_id)
for signer in recipients.signers:
    print(f"Signer: {signer.name} - Status: {signer.status}")
    # Status: sent, delivered, signed, completed, declined
```

### 场景 4:财务 - 下载已签文档

```python
# 下载单个文档
document = envelopes_api.get_document(account_id, envelope_id, document_id="1")
with open('signed_contract.pdf', 'wb') as f:
    f.write(document)

# 下载合并的所有文档
combined_doc = envelopes_api.get_document(account_id, envelope_id, document_id="combined")
with open('signed_documents_combined.pdf', 'wb') as f:
    f.write(combined_doc)

# 下载签署证书(Certificate of Completion)
certificate = envelopes_api.get_document(account_id, envelope_id, document_id="certificate")
with open('completion_certificate.pdf', 'wb') as f:
    f.write(certificate)
```

### 场景 5:销售运营 - 批量发送季度续约合同(Bulk Send)

```python
from docusign_esign import BulkSendingListsApi, BulkSendingList, BulkSendingCopy

# 1. 创建批量发送列表
bulk_list = BulkSendingList(
    name="Q2 Renewal Contracts",
    bulk_copies=[
        BulkSendingCopy(
            recipients=[
                {"name": "Client A", "email": "clienta@example.com", "role_name": "Client"}
            ],
            custom_fields=[
                {"name": "ContractValue", "value": "$50,000"}
            ]
        ),
        BulkSendingCopy(
            recipients=[
                {"name": "Client B", "email": "clientb@example.com", "role_name": "Client"}
            ],
            custom_fields=[
                {"name": "ContractValue", "value": "$80,000"}
            ]
        )
    ]
)

bulk_lists_api = BulkSendingListsApi(api_client)
bulk_list_result = bulk_lists_api.create_bulk_send_list(account_id, bulk_sending_list=bulk_list)

# 2. 使用批量列表发送 Envelope
envelope_definition = EnvelopeDefinition(
    template_id="renewal_template_id",
    status="sent"
)
# 关联批量列表
# (具体实现需参考官方 Bulk Send API 文档)
```

### 场景 6:嵌入式签署 - 在你的 Web 应用内完成签署

```python
from docusign_esign import RecipientViewRequest

# 1. 创建 Envelope(状态设为 sent)
# ...

# 2. 生成签署 URL
recipient_view_request = RecipientViewRequest(
    authentication_method="email",  # 或 "none"(无认证)
    client_user_id="user123",  # 你的系统中的用户 ID
    recipient_id="1",  # Envelope 中签署人的 recipient_id
    return_url="https://yourapp.com/signing-complete",  # 签署完成后跳转
    user_name="Zhang San",
    email="zhang@example.com"
)

recipient_view = envelopes_api.create_recipient_view(account_id, envelope_id, recipient_view_request=recipient_view_request)
signing_url = recipient_view.url
print(f"Signing URL: {signing_url}")

# 3. 在前端打开签署 URL(iframe 或新窗口)
# <iframe src="{{ signing_url }}" width="100%" height="600px"></iframe>
```

### 场景 7:集成场景 - Webhook 接收签署完成通知

**在 DocuSign Admin Console 配置 Connect Webhook**

Webhook Payload 示例:
```json
{
  "event": "envelope-completed",
  "apiVersion": "v2.1",
  "data": {
    "accountId": "your_account_id",
    "envelopeId": "abc123-def456",
    "envelopeSummary": {
      "status": "completed",
      "emailSubject": "请签署销售合同 - Tech Corp",
      "completedDateTime": "2026-04-08T10:30:00Z"
    }
  }
}
```

你的服务器接收 Webhook:
```python
# Flask 示例
from flask import Flask, request
import hmac, hashlib

app = Flask(__name__)

@app.route('/docusign-webhook', methods=['POST'])
def docusign_webhook():
    payload = request.data
    
    # 验证 Webhook 签名(可选但推荐)
    hmac_signature = request.headers.get('X-DocuSign-Signature-1')
    # 计算期望的签名
    # expected_signature = hmac.new(webhook_secret.encode(), payload, hashlib.sha256).hexdigest()
    # if hmac_signature != expected_signature:
    #     return "Invalid signature", 401
    
    # 解析事件
    event_data = request.json
    if event_data['event'] == 'envelope-completed':
        envelope_id = event_data['data']['envelopeId']
        print(f"Envelope {envelope_id} completed!")
        
        # 触发后续动作
        # 1. 下载已签文档到 S3
        # 2. 更新 Salesforce Opportunity 状态为 "Closed Won"
        # 3. 通知 Slack
        
    return "OK", 200
```

### 场景 8:使用 Anchor String 自动定位签名位置

```python
# 在合同 PDF 中,插入锚点文本 "**SIGN_HERE**"
# DocuSign 会自动在该文本附近放置签名框

envelope_definition = EnvelopeDefinition(
    documents=[
        Document(
            document_base64=base64.b64encode(pdf_content).decode('utf-8'),
            name="销售合同",
            file_extension="pdf",
            document_id="1"
        )
    ],
    recipients=Recipients(
        signers=[
            Signer(
                email="client@example.com",
                name="Zhang San",
                recipient_id="1",
                tabs=Tabs(
                    sign_here_tabs=[
                        SignHere(
                            document_id="1",
                            anchor_string="**SIGN_HERE**",  # 锚点文本
                            anchor_x_offset="0",  # 相对锚点的偏移
                            anchor_y_offset="-10",
                            anchor_units="pixels"
                        )
                    ]
                )
            )
        ]
    ),
    status="sent"
)
```

## 适用场景

### 典型工作流

1. **销售合同签署工作流**
   - AE 在 Salesforce 中将 Opportunity 标记为 "Closed Won"
   - 触发 Webhook 调用 DocuSign API
   - 使用合同模板生成 Envelope,填充客户信息(公司名、金额、生效日期)
   - 发送签署请求给客户和公司法务
   - 客户签署 → 法务签署(顺序签署)
   - 签署完成后,Webhook 通知系统
   - 自动下载已签合同到 SharePoint/Google Drive
   - 更新 Salesforce,附加已签合同 PDF

2. **HR 入职流程工作流**
   - HR 在 HRIS 系统中创建新员工
   - 触发 DocuSign 批量发送:Offer Letter、保密协议、税务表格
   - 新员工依次签署各文档
   - 签署完成后自动归档到员工档案
   - 触发下一步流程(如分配工位、创建邮箱账号)

3. **采购订单审批工作流**
   - 采购员创建 PO(Purchase Order)
   - 发送给供应商签署
   - 供应商签署后,发送给公司财务审批
   - 财务签署完成,PO 生效
   - 自动同步到 ERP 系统(如 SAP/NetSuite)

4. **房地产租赁工作流**
   - 租客在线填写租赁申请
   - 生成租赁合同,发送给租客和房东
   - 双方签署后,自动生成租金支付计划
   - 每月自动发送租金收据签署请求

## 与其他工具的区分

### DocuSign vs HelloSign(Dropbox Sign)

| 维度 | DocuSign | HelloSign |
|------|----------|-----------|
| **价格** | $25-$60/用户/月 | $13-$40/用户/月(便宜 50%) |
| **API 限流** | 1000 次/小时 | 5000 次/小时 |
| **模板功能** | 强大(拖拽设计器) | 基础 |
| **高级工作流** | 支持(多层审批) | 不支持 |
| **品牌定制** | 支持(Enterprise 版) | 支持(Pro 版起) |
| **适用场景** | 大型企业,复杂工作流 | 中小企业,简单签署 |

### DocuSign vs SignNow

| 维度 | DocuSign | SignNow |
|------|----------|---------|
| **价格** | $25-$60/用户/月 | $8-$50/用户/月 |
| **市场份额** | 70% | 2% |
| **集成生态** | 400+ 应用 | 50+ 应用 |
| **合规认证** | 全面(FedRAMP 等) | 基础(SOC 2) |
| **适用场景** | 需要强合规和生态 | 预算极有限 |

## 限制和注意事项

### API 调用限额

| 维度 | 限制 | 备注 |
|------|------|------|
| **速率限制** | 1000 次/小时/集成应用 | 超限返回 429 错误 |
| **Envelope 数量** | 按套餐计费 | Personal:5 封/月,Standard:无限 |
| **文档大小** | 单个 25 MB,Envelope 总 25 MB | 超出返回 413 错误 |
| **Envelope 过期** | 默认 120 天 | 可配置(最长 999 天) |
| **批量发送** | 最多 1000 个 Recipient/Envelope | Bulk Send 功能 |
| **Webhook 重试** | 失败后最多重试 100 次 | 建议实现幂等性 |

### 常见报错及解决方案

| 报错 | 原因 | 解决 |
|------|------|------|
| **AUTHORIZATION_INVALID_TOKEN** | Access Token 已过期 | 重新请求 JWT Token |
| **USER_LACKS_PERMISSIONS** | 用户权限不足 | 在 Admin Console 中分配权限 |
| **ACCOUNT_LACKS_PERMISSIONS** | 账户未启用该功能 | 升级套餐或联系销售 |
| **ENVELOPE_CANNOT_VOID_INVALID_STATE** | Envelope 状态不允许作废 | 只能作废 sent/delivered 状态的 Envelope |
| **RECIPIENT_EMAIL_NOT_VALID** | 邮箱格式错误 | 检查邮箱格式 |
| **TAB_POSITION_INVALID** | Tab 坐标超出页面范围 | 检查 x/y 坐标是否在页面内 |
| **INVALID_REQUEST_BODY** | JSON 格式错误 | 检查 JSON 是否符合 API 规范 |

### 注意事项

- **Demo vs 生产环境**:Demo 环境数据不会迁移到生产,需单独配置
- **JWT Consent 有效期**:Individual Consent 永久有效,除非用户撤销
- **Access Token 缓存**:Token 有效期 1 小时,建议缓存复用
- **文档格式**:Word/Excel 会自动转为 PDF,可能影响排版
- **签名位置精确度**:坐标单位为像素,不同 PDF 渲染器可能略有差异,建议使用 Anchor String
- **Webhook 安全**:建议验证 HMAC 签名,防止伪造请求
- **GDPR 合规**:欧盟用户数据存储在欧洲数据中心(eu.docusign.net)
- **电子签名法律效力**:美国(ESIGN Act)、欧盟(eIDAS)、中国(电子签名法)均认可

## 定价(详细分级)

### DocuSign eSignature 定价

| 版本 | 价格(年付) | Envelope 数量 | 核心功能 | 适用场景 |
|------|----------|--------------|---------|---------|
| **Personal** | $10/月 | 5 封/月 | 基础签名,移动 App | 个人用户,偶尔签署 |
| **Standard** | $25/用户/月 | 无限 | +模板,批量发送,提醒 | 小团队(5-20 人) |
| **Business Pro** | $40/用户/月 | 无限 | +高级表单字段,支付集成,品牌定制 | 中型企业(20-100 人) |
| **Enterprise** | $60/用户/月起 | 无限 | +高级工作流,SSO,SLA,专属支持 | 大型企业(100+ 人) |

### 附加功能定价

- **ID Verification(身份验证)**:$5/验证(通过政府 ID 验证身份)
- **SMS Delivery(短信发送)**:$0.25/短信
- **Signing Groups(签署组)**:包含在 Business Pro 及以上
- **PowerForms(公开表单)**:包含在 Business Pro 及以上
- **Guided Forms(引导式表单)**:包含在 Enterprise 版

### DocuSign CLM(合同生命周期管理)

- **定价**:需定制报价(通常 $50+/用户/月)
- **功能**:合同起草、谈判、审批、存储、分析

### 免费试用

- **30 天免费试用**:可申请试用 Business Pro 版(https://www.docusign.com/products/electronic-signature/free-trial)

## 参考链接

### 官方文档

- [DocuSign 开发者中心](https://developers.docusign.com/)
- [eSignature REST API 参考](https://developers.docusign.com/docs/esign-rest-api/)
- [Python SDK GitHub](https://github.com/docusign/docusign-python-client)
- [Node.js SDK GitHub](https://github.com/docusign/docusign-node-client)
- [JWT 认证指南](https://developers.docusign.com/platform/auth/jwt/)

### 进阶学习

- [代码示例(多语言)](https://github.com/docusign/code-examples-python)
- [API Explorer(在线测试 API)](https://developers.docusign.com/docs/esign-rest-api/reference/)
- [Webhook(Connect)配置指南](https://developers.docusign.com/platform/webhooks/)
- [嵌入式签署指南](https://developers.docusign.com/docs/esign-rest-api/esign101/concepts/embedding/)

### 社区资源

- [DocuSign Community](https://community.docusign.com/)
- [Stack Overflow(docusignapi 标签)](https://stackoverflow.com/questions/tagged/docusignapi)
- [DocuSign University(免费培训)](https://university.docusign.com/)
- [API Support](https://developers.docusign.com/support)

### SDK 和工具

- [Python SDK](https://github.com/docusign/docusign-python-client)
- [Node.js SDK](https://github.com/docusign/docusign-node-client)
- [Java SDK](https://github.com/docusign/docusign-java-client)
- [C# SDK](https://github.com/docusign/docusign-csharp-client)
- [PHP SDK](https://github.com/docusign/docusign-php-client)
- [Ruby SDK](https://github.com/docusign/docusign-ruby-client)
- [Postman Collection](https://www.postman.com/docusign)
