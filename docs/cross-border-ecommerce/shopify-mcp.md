# Shopify MCP Server (官方)

> Shopify 官方 MCP Server,AI Agent 直接操作 Shopify 店铺

## 快速开始

### 安装
```bash
npx @shopify/dev-mcp@latest
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - 通过 MCP 协议授权 Shopify 店铺
  - 同 Shopify CLI,使用 partners.shopify.com 获取凭证
- **获取步骤:**
  1. 访问 partners.shopify.com
  2. 创建App并获取API凭证
  3. 通过MCP协议进行OAuth授权
  4. 配置MCP客户端连接
- **验证:**
  ```bash
  # 在MCP客户端中测试连接
  # 例如在Claude Code中验证MCP工具可用性
  ```

## 核心能力

- 通过 MCP 协议暴露 Shopify 全部管理操作
- AI Agent 可直接调用(无需写代码)
- 产品/订单/库存/主题管理
- 与 Claude Code、Gemini CLI 等 MCP 客户端兼容

## 使用示例

### 配置MCP服务器
```json
// 在MCP客户端配置文件中添加
{
  "mcpServers": {
    "shopify": {
      "command": "npx",
      "args": ["@shopify/dev-mcp@latest"]
    }
  }
}
```

### 通过AI Agent查询产品
```
# 在Claude Code或其他MCP客户端中直接对话
"帮我查看Shopify店铺中所有库存少于10的产品"
```

### 通过AI Agent创建产品
```
# 自然语言操作
"在Shopify创建一个新产品:标题是'春季新款连衣裙',价格$49.99,库存100件"
```

### 通过AI Agent更新订单状态
```
# 自然语言操作
"将订单号#1234的发货状态更新为已发货,并添加追踪号TRACK123456"
```

## 适用场景
- 已有 MCP 兼容的 AI Agent
- 想让 AI 直接管理 Shopify 店铺
- 比 CLI 更适合 Agent 场景
- 需要通过对话式界面管理电商运营

## 限制和注意事项
- 官方维护,功能覆盖持续扩展中
- 需要MCP兼容的客户端(如Claude Code、Gemini CLI)
- 继承Shopify API的速率限制
- 某些复杂操作可能需要多次MCP调用

## 定价
- MCP Server 本身免费
- 需要Shopify店铺订阅(参考Shopify定价)

## 参考链接
- [GitHub仓库](https://github.com/Shopify/dev-mcp)
- [MCP协议规范](https://modelcontextprotocol.io/)
- [Shopify API文档](https://shopify.dev/docs/api)
