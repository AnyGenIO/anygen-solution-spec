<!-- doc-level: standard -->

# Stripe CLI

Stripe官方CLI工具,用于管理在线支付、订阅计费和发票,适合SaaS业务快速集成收款功能。

## 快速开始

### 安装
```bash
# macOS
brew install stripe/stripe-cli/stripe

# Linux
curl -s https://packages.stripe.dev/api/security/keypair/stripe-cli-gpg/public | gpg --dearmor | sudo tee /usr/share/keyrings/stripe.gpg
echo "deb [signed-by=/usr/share/keyrings/stripe.gpg] https://packages.stripe.dev/stripe-cli-debian-local stable main" | sudo tee /etc/apt/sources.list.d/stripe.list
sudo apt update && sudo apt install stripe
```

### 认证配置
- **方式:** api_key
- **环境变量:** `STRIPE_API_KEY`
- **获取步骤:**
  1. 登录 https://dashboard.stripe.com → Developers → API keys
  2. 复制 Secret key (测试用sk_test,生产用sk_live)
- **验证:** `stripe config --list`

## 核心能力
- 客户和订阅管理
- 支付链接和发票创建
- Webhook事件监听和调试
- 产品/价格/报价单管理

## 使用示例

### 创建客户
```bash
stripe customers create --email="customer@example.com" --name="张三"
```

### 创建订阅
```bash
# 创建产品和价格
stripe products create --name="企业版SaaS"
stripe prices create --product="prod_xxx" --unit-amount=9900 --currency=usd --recurring[interval]=month

# 创建订阅
stripe subscriptions create --customer="cus_xxx" --items[0][price]="price_xxx"
```

### 创建发票并发送
```bash
stripe invoiceitems create --customer="cus_xxx" --amount=5000 --currency=usd --description="咨询费"
stripe invoices create --customer="cus_xxx" --auto_advance=true
stripe invoices send --invoice="in_xxx"
```

## 适用场景
- SaaS订阅收费和发票管理
- 在线支付集成和调试
- 自动化收款和退款流程

## 限制和注意事项
- 交易手续费: 2.9% + $0.30(美国), 国际卡额外+1.5%
- 测试模式无限制,生产需验证业务信息
- 部分国家需合规认证(如欧洲SCA)
- Webhook重试3天内多次自动执行

## 定价
- API使用免费
- 按交易收费: 2.9% + $0.30/笔(美国)
- 订阅管理和发票功能免费

## 参考链接
- [官方文档](https://stripe.com/docs/stripe-cli)
- [API参考](https://stripe.com/docs/api)
- [定价](https://stripe.com/pricing)
