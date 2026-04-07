# Shopify CLI

> Shopify 官方命令行,管理店铺产品、订单、主题

## 快速开始

### 安装
```bash
npm install -g @shopify/cli
```

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `SHOPIFY_CLI_PARTNERS_TOKEN` — CI场景使用的Partners Token
- **获取步骤:**
  1. 访问 partners.shopify.com
  2. 进入 Settings → CLI tokens
  3. 生成新的CLI token
  4. 保存token到环境变量
- **验证:**
  ```bash
  shopify auth login
  shopify auth token
  ```

## 核心能力

- 产品CRUD(创建/读取/更新/删除)
- 订单查询
- 主题开发和部署
- App 开发调试
- 库存管理(多仓库)

## 使用示例

### 登录授权
```bash
# 交互式登录(会打开浏览器)
shopify auth login
```

### 查看店铺信息
```bash
shopify store list
```

### 创建新产品
```bash
shopify product create --title "新款T恤" --price 29.99 --inventory 100
```

### 部署主题
```bash
# 在主题目录下
shopify theme push --development
```

### 启动本地开发环境
```bash
shopify app dev
```

### 管理库存
```bash
shopify inventory update --sku "TSHIRT-001" --quantity 50
```

## 适用场景
- 在Shopify上架/改产品
- 批量更新价格或库存
- 部署店铺主题
- 开发Shopify App
- 自动化店铺管理任务

## 限制和注意事项
- REST API: 2次/秒
- GraphQL: 1000 cost点/秒
- 批量操作需要注意速率限制
- 某些操作需要店铺所有者权限

## 定价
- Shopify CLI 免费
- 店铺订阅费用:
  - Basic: $39/月
  - Shopify: $105/月
  - Advanced: $399/月
  - Plus: $2000/月起

## 参考链接
- [官方文档](https://shopify.dev/docs/api/shopify-cli)
- [CLI命令参考](https://shopify.dev/docs/api/shopify-cli/commands)
- [GraphQL API](https://shopify.dev/docs/api/admin-graphql)
