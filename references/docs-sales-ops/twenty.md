<!-- doc-level: standard -->

# Twenty

开源CRM(Salesforce替代),基于GraphQL API,可自托管或使用云服务,适合需要数据自主和深度定制的团队。

## 快速开始

### 安装
```bash
# 自托管
git clone https://github.com/twentyhq/twenty
cd twenty
docker-compose up

# 或使用云服务 app.twenty.com
```

### 认证配置
- **方式:** api_key
- **环境变量:** `TWENTY_API_KEY`
- **获取步骤:**
  1. 登录 Twenty → Settings → Developers → API Keys
  2. Generate → 复制保存
- **验证:**
  ```bash
  curl -X POST https://api.twenty.com/graphql \
    -H "Authorization: Bearer $TWENTY_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{"query": "{ companies { edges { node { name } } } }"}'
  ```

## 核心能力
- 自定义对象和关系(GraphQL驱动)
- People/Companies/Opportunities管理
- 全功能GraphQL API和Webhooks
- 开源可定制UI和插件系统

## 使用示例

### 查询公司
```graphql
query {
  companies(first: 10) {
    edges {
      node { id name domainName }
    }
  }
}
```

### 创建联系人
```graphql
mutation {
  createPerson(data: {
    firstName: "张"
    lastName: "三"
    email: "zhang@example.com"
  }) {
    id email
  }
}
```

## 适用场景
- 需要数据自主和避免vendor lock-in
- 深度定制CRM需求
- 预算有限或零预算(自托管免费)

## 限制和注意事项
- 相对年轻项目,功能快速迭代中
- 开箱即用功能不如成熟商业CRM
- 自托管需要运维能力

## 定价
- 开源免费自托管
- 云服务: $0(免费) / $9/用户/月(Pro) / $39/用户/月(Enterprise)

## 参考链接
- [官方网站](https://twenty.com/)
- [GitHub](https://github.com/twentyhq/twenty)
