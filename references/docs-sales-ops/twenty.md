# Twenty

> 开源CRM(Salesforce替代),GraphQL API + CLI,可自托管或云服务,10k+ stars

## 快速开始

### 安装
```bash
# 自托管
git clone https://github.com/twentyhq/twenty
cd twenty
docker-compose up

# 或使用云服务 app.twenty.com(免费版)
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `TWENTY_API_KEY` — API Key
- **获取步骤:**
  1. 登录 Twenty(自托管或 app.twenty.com)
  2. Settings → Developers → API Keys
  3. Generate → 复制保存
- **验证:**
  ```bash
  curl -X POST https://api.twenty.com/graphql \
    -H "Authorization: Bearer $TWENTY_API_KEY" \
    -H "Content-Type: application/json" \
    -d '{"query": "{ companies { edges { node { name } } } }"}'
  ```

## 核心能力

### 核心CRM(完全可定制)
- 自定义对象和关系(GraphQL schema驱动)
- People/Companies/Opportunities/Tasks管理
- 灵活数据模型(无预设限制)
- 视图和过滤器自定义

### GraphQL API
- 全功能GraphQL API(查询/变更/订阅)
- Webhooks(事件驱动集成)
- 批量操作

### 自动化(开发中)
- Workflow自动化(Roadmap中)
- 与n8n/Zapier集成

### 开源生态
- 可自定义UI(React前端开源)
- 插件系统(扩展功能)
- 数据完全掌控(自托管无vendor lock-in)

## 使用示例

### 查询公司列表
```graphql
query {
  companies(first: 10) {
    edges {
      node {
        id
        name
        domainName
        employees
      }
    }
  }
}
```

### 创建联系人
```graphql
mutation {
  createPerson(
    data: {
      firstName: "张"
      lastName: "三"
      email: "zhang@example.com"
      phone: "+86-138-0000-0000"
    }
  ) {
    id
    firstName
    lastName
    email
  }
}
```

### 更新商机
```graphql
mutation {
  updateOpportunity(
    id: "123e4567-e89b-12d3-a456-426614174000"
    data: {
      stage: "PROPOSAL"
      amount: 50000
    }
  ) {
    id
    stage
    amount
  }
}
```

### 订阅实时更新
```graphql
subscription {
  opportunityUpdated {
    id
    name
    stage
    amount
  }
}
```

## 适用场景
- 不想被商业CRM绑架(数据和定制自由)
- 需要深度定制CRM(可改源码)
- 预算极低或零预算(自托管免费)
- 技术团队有能力维护开源系统
- 数据隐私敏感(必须自托管)

## 限制和注意事项
- 相对年轻项目(2023年启动),功能还在快速迭代
- 开箱即用功能不如成熟商业CRM(需自己配置)
- 自托管需要运维能力
- 生态和集成需要自己开发
- 云服务目前功能有限

## 定价
- 开源免费自托管
- 云服务: $0(免费版) / $9/用户/月(Pro) / $39/用户/月(Enterprise)

## 参考链接
- [官方网站](https://twenty.com/)
- [开发者文档](https://twenty.com/developers)
- [GitHub仓库](https://github.com/twentyhq/twenty)
