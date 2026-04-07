# Salesforce MCP Server

> 社区 MCP Server,AI Agent 直接操作 Salesforce CRM,339+ stars

## 快速开始

### 安装
```bash
git clone https://github.com/benitomartin/salesforce-mcp-server
cd salesforce-mcp-server
pip install -r requirements.txt
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `SF_ACCESS_TOKEN` — Salesforce访问令牌
  - `SF_INSTANCE_URL` — 组织URL(如 https://yourorg.my.salesforce.com)
- **获取步骤:**
  1. 登录 Salesforce → Setup
  2. 搜索 "Apps" → App Manager → New Connected App
  3. 启用 OAuth Settings → 勾选 Enable OAuth Settings
  4. 配置 Callback URL 和 OAuth Scopes(如 api, refresh_token)
  5. 保存后获取 Consumer Key 和 Consumer Secret
  6. 使用 OAuth 流程获取 Access Token
- **验证:**
  ```bash
  # 启动MCP服务器后测试连接
  python -c "import os; print('SF_ACCESS_TOKEN:', 'set' if os.getenv('SF_ACCESS_TOKEN') else 'not set')"
  ```

## 核心能力
- 通过 MCP 协议操作 Contacts/Opportunities/Leads
- SOQL 查询(支持复杂查询条件)
- 记录 CRUD(创建/读取/更新/删除)
- AI Agent 直接调用(无需写代码)
- 与 Claude/其他 MCP 兼容的 AI Agent 无缝集成

## 使用示例

### 启动MCP服务器
```bash
export SF_ACCESS_TOKEN="your_access_token"
export SF_INSTANCE_URL="https://yourorg.my.salesforce.com"

python server.py
```

### 通过MCP协议查询Contacts
```python
# MCP客户端示例
from mcp import Client

client = Client("http://localhost:8080")
contacts = client.call_tool("query_contacts", {
    "filter": "LastName='Smith'",
    "limit": 10
})
print(contacts)
```

### AI Agent直接操作Salesforce
```bash
# 配置Claude Desktop或其他MCP兼容客户端
# 在配置文件中添加:
{
  "mcpServers": {
    "salesforce": {
      "command": "python",
      "args": ["/path/to/salesforce-mcp-server/server.py"],
      "env": {
        "SF_ACCESS_TOKEN": "your_token",
        "SF_INSTANCE_URL": "https://yourorg.my.salesforce.com"
      }
    }
  }
}
```

## 适用场景
- 已有 MCP 兼容的 AI Agent(如 Claude Desktop)
- 想让 AI 直接操作 Salesforce 而无需手写API调用
- 快速原型开发和自动化工作流
- 通过自然语言查询和更新CRM数据
- 构建 AI 驱动的销售助手

## 限制和注意事项
- 社区项目,功能覆盖不如官方 Salesforce API 全面
- 活跃维护中,可能存在未实现的功能
- 需要 MCP 协议兼容的客户端(如 Claude Desktop, Continue等)
- 继承 Salesforce API 的限流限制(根据版本不同)
- 错误处理和日志可能不如商业工具完善

## 定价
- 完全免费(开源社区项目)
- 需要有效的 Salesforce 订阅(Professional版起)

## 参考链接
- [GitHub仓库](https://github.com/benitomartin/salesforce-mcp-server)
- [MCP协议文档](https://modelcontextprotocol.io/)
- [Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/)
