# Stripe CLI

> Stripe 官方CLI,管理支付、订阅、发票

## 快速开始

### 安装
```bash
# macOS
brew install stripe/stripe-cli/stripe

# Linux (Debian/Ubuntu)
curl -s https://packages.stripe.dev/api/security/keypair/stripe-cli-gpg/public | gpg --dearmor | sudo tee /usr/share/keyrings/stripe.gpg
echo "deb [signed-by=/usr/share/keyrings/stripe.gpg] https://packages.stripe.dev/stripe-cli-debian-local stable main" | sudo tee /etc/apt/sources.list.d/stripe.list
sudo apt update
sudo apt install stripe

# Windows
scoop install stripe

# 或直接下载: https://github.com/stripe/stripe-cli/releases
```

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `STRIPE_API_KEY` — API密钥(sk_live_xxx 或 sk_test_xxx)
- **获取步骤:**
  1. 登录 https://dashboard.stripe.com
  2. Developers → API keys
  3. 复制 Secret key(测试模式用sk_test,生产用sk_live)
- **交互式登录:**
  ```bash
  stripe login
  # 浏览器授权后会自动配置
  ```
- **验证:**
  ```bash
  stripe config --list
  ```

## 核心能力
- 创建和管理客户(Customer)
- 创建支付链接(Payment Link)
- 发送发票(Invoice)
- 订阅管理(Subscription)
- 查看支付记录(Charges/PaymentIntents)
- Webhook监听和调试
- 报价单(Quote)生成
- 产品和价格管理(Products/Prices)

## 使用示例

### 创建客户
```bash
stripe customers create \
  --email="customer@example.com" \
  --name="张三" \
  --description="企业客户 - 科技公司"
```

### 创建订阅(需先创建产品和价格)
```bash
# 1. 创建产品
stripe products create \
  --name="企业版SaaS" \
  --description="年度订阅"

# 2. 创建价格
stripe prices create \
  --product="prod_xxxxx" \
  --unit-amount=9900 \
  --currency=usd \
  --recurring[interval]=month

# 3. 创建订阅
stripe subscriptions create \
  --customer="cus_xxxxx" \
  --items[0][price]="price_xxxxx"
```

### 创建支付链接(一次性支付)
```bash
stripe payment_links create \
  --line-items[0][price]="price_xxxxx" \
  --line-items[0][quantity]=1 \
  --after_completion[type]=hosted_confirmation \
  --after_completion[hosted_confirmation][custom_message]="感谢您的购买!"
```

### 创建发票
```bash
# 1. 创建发票项
stripe invoiceitems create \
  --customer="cus_xxxxx" \
  --amount=5000 \
  --currency=usd \
  --description="咨询服务费"

# 2. 创建并发送发票
stripe invoices create \
  --customer="cus_xxxxx" \
  --auto_advance=true

# 3. 发送发票(邮件)
stripe invoices send --invoice="in_xxxxx"
```

### 查询支付历史
```bash
stripe charges list --limit=10
```

### 监听Webhook事件(本地调试)
```bash
stripe listen --forward-to localhost:3000/webhook
```

### 触发测试事件
```bash
stripe trigger payment_intent.succeeded
```

### 创建报价单
```bash
stripe quotes create \
  --customer="cus_xxxxx" \
  --line-items[0][price]="price_xxxxx" \
  --line-items[0][quantity]=5
```

### 查询客户订阅
```bash
stripe subscriptions list --customer="cus_xxxxx"
```

## 适用场景
- 给客户发账单/发票
- 设置订阅收费(SaaS月费/年费)
- 生成报价→合同→收费联动
- 调试支付集成
- 自动化收款流程
- 处理订阅升级/降级
- 退款管理

## 限制和注意事项
- 交易手续费: 2.9% + $0.30(美国),其他国家不同
- API免费使用
- 测试模式无限制,生产需验证业务
- 部分国家需要额外合规(如欧洲SCA)
- 订阅按自然月/年计费
- Webhook重试机制: 3天内多次重试
- 争议(Dispute)处理需及时响应

## 定价
- API使用: 免费
- 交易手续费:
  - 美国: 2.9% + $0.30/笔
  - 国际卡: +1.5%
  - 货币转换: +1%
- Stripe Billing(订阅管理): 包含在交易费中
- Stripe Invoicing: 免费

## 参考链接
- [官方文档](https://stripe.com/docs/stripe-cli)
- [CLI命令参考](https://stripe.com/docs/cli)
- [API参考](https://stripe.com/docs/api)
- [Webhook指南](https://stripe.com/docs/webhooks)
- [定价](https://stripe.com/pricing)
