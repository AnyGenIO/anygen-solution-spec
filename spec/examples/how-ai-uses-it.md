# AI 怎么用这套说明书

## 场景示意

用户说: "帮我把这个蓝牙耳机上架到亚马逊和Shopify"

### AI 的思考链:

```
1. 识别意图 → 匹配 Solution: new-product-multi-platform
   (通过 trigger_phrases 匹配)

2. 读 Solution → 知道需要收集哪些信息
   → 问用户要: 标题、价格、SKU、图片

3. 读 Registry → 知道 amazon-sp-api 和 shopify-cli 可用
   → 检查认证状态（已配置？需要引导配置？）

4. 读 Tool Spec → 知道每个工具的具体命令和参数
   → shopify-cli: shopify product create --title "..." 
   → amazon-sp-api: sp_api.ListingsItems().put_listings_item(...)

5. 读 Domain Knowledge → 知道业务规则
   → Amazon 标题不超过200字符，主图要白底
   → 价格不能比 Shopify 低（价格平价规则）

6. 执行 Solution 的 execution_plan
   → 按步骤调用工具，处理错误，生成报告
```

## 三种消费方式

### 方式 1: 作为 System Prompt（最简单）
把相关的 YAML 文件内容塞进系统提示词。
适合: 固定场景的 AI Agent。

### 方式 2: 作为 MCP Server（最灵活）
把整套说明书做成一个 MCP Server，暴露这些 tool:
- lookup_tools(domain, category) → 返回可用工具
- get_tool_spec(tool_id) → 返回工具说明书
- get_solution(solution_id) → 返回解决方案模板
- get_domain_rules(domain) → 返回业务规则
- search_glossary(term) → 查术语

AI Agent 按需查询，不用全量加载。

### 方式 3: 作为 RAG 知识库（最省 Token）
把所有 YAML 文件向量化，AI 按需检索相关片段。
适合: 通用型 AI 助手，按问题动态拉取相关知识。

## 社区贡献模型

```
用户/开发者贡献:
├── 新增一个工具说明书 (Tool Spec)     → PR 到 tools/
├── 新增一个解决方案 (Solution)        → PR 到 solutions/
├── 补充行业知识 (Domain Knowledge)    → PR 到 domain.yaml
├── 新增一个行业 (Domain)              → PR 新目录
└── 修正/更新已有内容                   → PR 修改

质量保证:
├── CI 校验 YAML 是否符合 schema
├── AI 自动测试: 给 AI 一个任务，看能否用说明书完成
└── 社区 review
```
