# DocuSign SDK

> 电子签名平台SDK,发送合同/收集签名(无官方CLI)

## 快速开始

### 安装
```bash
# Python
pip install docusign-esign

# Node.js
npm install docusign-esign

# 其他语言: Java, C#, PHP, Ruby等也有官方SDK
```

### 认证配置
- **方式:** oauth (JWT)
- **环境变量:**
  - `DOCUSIGN_INTEGRATION_KEY` — 应用Integration Key
  - `DOCUSIGN_USER_ID` — 用户ID(GUID格式)
  - `DOCUSIGN_ACCOUNT_ID` — 账户ID
  - `DOCUSIGN_PRIVATE_KEY_PATH` — RSA私钥文件路径
- **获取步骤:**
  1. 访问 https://developers.docusign.com
  2. 创建应用(Apps → Create New App)
  3. 选择 "Service Integration"
  4. 获取 Integration Key
  5. 生成 RSA 密钥对(Settings → RSA Keypairs)
  6. 下载私钥保存为文件
  7. 管理员同意授权(Admin Consent)
- **验证:**
  ```python
  import docusign_esign
  print(docusign_esign.__version__)
  ```

## 核心能力
- 发送签署请求(Envelope)
- 模板管理(Templates)
- 签署状态查询
- 已签文档下载
- 批量发送(Bulk Send)
- 嵌入式签署(Web签署集成)
- Webhook通知(签署完成/拒绝/查看)
- 签署工作流(多人顺序/并行签署)

## 使用示例

### Python SDK - 发送签署请求
```python
from docusign_esign import ApiClient, EnvelopesApi, EnvelopeDefinition, Document, Signer, SignHere, Tabs, Recipients
from docusign_esign.client.api_exception import ApiException
import base64

# 初始化客户端
api_client = ApiClient()
api_client.set_base_path("https://demo.docusign.net/restapi")  # 生产环境用 https://www.docusign.net/restapi

# JWT认证
private_key = open('private.key').read()
api_client.configure_jwt_authorization_flow(
    private_key_bytes=private_key,
    oauth_base_path='account-d.docusign.com',  # 生产环境用 account.docusign.com
    integration_key='your_integration_key',
    user_id='your_user_id',
    expires_in=3600
)

# 创建Envelope
envelope_definition = EnvelopeDefinition(
    email_subject="请签署销售合同",
    documents=[
        Document(
            document_base64=base64.b64encode(open('contract.pdf', 'rb').read()).decode('utf-8'),
            name="销售合同",
            file_extension="pdf",
            document_id="1"
        )
    ],
    recipients=Recipients(
        signers=[
            Signer(
                email="client@example.com",
                name="张三",
                recipient_id="1",
                routing_order="1",
                tabs=Tabs(
                    sign_here_tabs=[
                        SignHere(
                            document_id="1",
                            page_number="1",
                            x_position="100",
                            y_position="700"
                        )
                    ]
                )
            )
        ]
    ),
    status="sent"  # sent=立即发送, created=保存为草稿
)

# 发送
envelopes_api = EnvelopesApi(api_client)
results = envelopes_api.create_envelope('your_account_id', envelope_definition=envelope_definition)
print(f"Envelope ID: {results.envelope_id}")
```

### 使用模板发送
```python
from docusign_esign import EnvelopeDefinition, TemplateRole

envelope_definition = EnvelopeDefinition(
    email_subject="请签署销售合同",
    template_id="your_template_id",
    template_roles=[
        TemplateRole(
            email="client@example.com",
            name="张三",
            role_name="Signer"
        )
    ],
    status="sent"
)

results = envelopes_api.create_envelope('your_account_id', envelope_definition=envelope_definition)
```

### 查询签署状态
```python
envelope = envelopes_api.get_envelope('your_account_id', 'envelope_id')
print(f"状态: {envelope.status}")  # sent, delivered, completed, declined, voided
```

### 下载已签文档
```python
document = envelopes_api.get_document('your_account_id', 'envelope_id', 'combined')
with open('signed_contract.pdf', 'wb') as f:
    f.write(document)
```

### Node.js SDK - 发送签署请求
```javascript
const docusign = require('docusign-esign');
const fs = require('fs');

const apiClient = new docusign.ApiClient();
apiClient.setBasePath('https://demo.docusign.net/restapi');

// JWT认证
const privateKey = fs.readFileSync('private.key');
await apiClient.requestJWTUserToken(
  'your_integration_key',
  'your_user_id',
  ['signature', 'impersonation'],
  privateKey,
  3600
);

// 创建Envelope
const envelopeDefinition = new docusign.EnvelopeDefinition();
envelopeDefinition.emailSubject = '请签署销售合同';
envelopeDefinition.documents = [
  docusign.Document.constructFromObject({
    documentBase64: fs.readFileSync('contract.pdf').toString('base64'),
    name: '销售合同',
    fileExtension: 'pdf',
    documentId: '1'
  })
];
envelopeDefinition.recipients = docusign.Recipients.constructFromObject({
  signers: [
    docusign.Signer.constructFromObject({
      email: 'client@example.com',
      name: '张三',
      recipientId: '1',
      routingOrder: '1'
    })
  ]
});
envelopeDefinition.status = 'sent';

const envelopesApi = new docusign.EnvelopesApi(apiClient);
const results = await envelopesApi.createEnvelope('your_account_id', {envelopeDefinition});
console.log(`Envelope ID: ${results.envelopeId}`);
```

## 适用场景
- AE发合同给客户签署
- 跟踪合同签署进度
- 签署完成后自动归档到CRM
- 多方签署(甲方+乙方+见证人)
- 嵌入式签署(在你的应用内完成签署)
- 批量发送合同(季度末大批客户续约)

## 限制和注意事项
- 免费版每月5个Envelope
- API限流: 1000次/小时(Developer版)
- 文档大小限制: 25MB/文档
- Envelope过期时间可配置(默认120天)
- 签署完成后文档存储有时限(根据套餐)
- JWT认证需要管理员同意(Admin Consent)
- 部分高级功能需企业版(如高级工作流/自定义品牌)

## 定价
- Personal: $10/月(5 envelopes)
- Standard: $25/月(基础API访问)
- Business Pro: $40/月(高级API功能)
- Enterprise: $60/月起(自定义)
- 超出Envelope按$0.5-1/份计费

## 参考链接
- [官方文档](https://developers.docusign.com/docs/esign-rest-api/)
- [Python SDK](https://github.com/docusign/docusign-python-client)
- [Node.js SDK](https://github.com/docusign/docusign-node-client)
- [代码示例](https://github.com/docusign/code-examples-python)
- [API Explorer](https://developers.docusign.com/docs/esign-rest-api/reference/)
