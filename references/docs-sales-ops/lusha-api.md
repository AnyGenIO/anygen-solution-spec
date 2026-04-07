# Lusha API

> 联系人数据平台,快速获取邮箱和电话号码,Chrome插件+API组合使用

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。同时推荐安装Chrome插件用于LinkedIn场景。

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `LUSHA_API_KEY` — Lusha API密钥
- **获取步骤:**
  1. 登录 https://www.lusha.com
  2. Settings → API → Generate API Key
  3. 复制生成的API Key
- **验证:**
  ```bash
  curl -H "api_key: $LUSHA_API_KEY" \
    https://api.lusha.com/company?domain=stripe.com
  ```

## 核心能力
- 联系人邮箱查询(B2B邮箱验证准确率90%+)
- 直线电话号码获取
- 公司信息补全(规模/行业/地址/社交媒体)
- 批量查询(Excel上传→批量返回结果)
- Chrome插件(LinkedIn上直接显示联系方式)
- 邮箱验证(Deliverability检查)
- CRM集成(Salesforce/HubSpot自动同步)

## 使用示例

### 通过邮箱获取联系人信息
```bash
curl -X POST \
  -H "api_key: $LUSHA_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lusha.com/person \
  -d '{
    "property": {
      "type": "email",
      "value": "john@example.com"
    }
  }'
```

### 通过公司域名获取信息
```bash
curl -H "api_key: $LUSHA_API_KEY" \
  "https://api.lusha.com/company?domain=shopify.com"
```

### 通过LinkedIn URL获取联系方式
```bash
curl -X POST \
  -H "api_key: $LUSHA_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lusha.com/person \
  -d '{
    "property": {
      "type": "linkedInUrl",
      "value": "https://www.linkedin.com/in/johndoe"
    }
  }'
```

### 批量查询联系人
```bash
curl -X POST \
  -H "api_key: $LUSHA_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lusha.com/bulk/person \
  -d '{
    "data": [
      {
        "property": {
          "type": "email",
          "value": "contact1@example.com"
        }
      },
      {
        "property": {
          "type": "email",
          "value": "contact2@example.com"
        }
      }
    ]
  }'
```

### 验证邮箱可达性
```bash
curl -X POST \
  -H "api_key: $LUSHA_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.lusha.com/email/validate \
  -d '{
    "email": "john@example.com"
  }'
```

## 适用场景
- 在LinkedIn找到目标人后快速获取联系方式
- 批量获取联系人列表的邮箱/电话
- 配合Apollo做补充(覆盖不同数据源)
- 验证邮箱有效性(发邮件前去掉无效邮箱)
- Chrome插件实时查看LinkedIn联系人信息
- CRM数据富化

## 限制和注意事项
- 免费版仅5 credits/月(1 credit=1次查询)
- 主要覆盖北美/欧洲数据,亚洲数据较弱
- 手机号码准确率低于邮箱(约70%)
- API限流: 60次/分钟
- Credits消耗规则:
  - Email reveal: 1 credit
  - Phone reveal: 1 credit
  - Bulk enrichment: 每条1 credit
- Chrome插件需要浏览器权限

## 定价
- Free版: 5 credits/月
- Pro: $29/用户/月 (480 credits/年)
- Premium: $51/用户/月 (960 credits/年)
- Scale: 需联系销售(自定义credits)

## 参考链接
- [官方文档](https://www.lusha.com/docs/)
- [API参考](https://www.lusha.com/docs/api/)
- [Chrome插件](https://chrome.google.com/webstore/detail/lusha/mcebeofpilippmndlpcghpmghcljajna)
- [集成指南](https://www.lusha.com/integrations/)
