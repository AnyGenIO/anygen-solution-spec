# Chili Piper API

> 智能会议路由+日程安排,Lead进来自动分配销售+立即预约(转化率更高)

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** api_key
- **环境变量:**
  - `CHILIPIPER_API_KEY` — Chili Piper API密钥
- **获取步骤:**
  1. 登录 Chili Piper
  2. Settings → API
  3. Generate Key → 复制保存
- **验证:**
  ```bash
  curl -H "Authorization: API-Key $CHILIPIPER_API_KEY" \
    https://api.chilipiper.com/v1/users
  ```

## 核心能力

### 智能路由(Smart Routing)
- Lead自动分配(Round Robin/按区域/按产品线)
- 销售可用性实时检测
- 离线自动转下一个销售
- 优先级规则(VIP客户优先)
- 负载均衡(避免单个销售过载)

### Instant Booker
- 表单提交后立即显示日历(秒级响应)
- 嵌入式日历选择器
- 多个销售的空闲时间合并显示
- 自动发送会议确认和提醒

### 会议路由规则
- 按客户类型路由(Enterprise→AE, SMB→SDR)
- 按账户状态路由(已有客户→CSM, 新客户→Sales)
- 按地理位置路由
- 按产品兴趣路由

### CRM深度集成
- 与Salesforce/HubSpot双向同步
- 自动创建Lead/Contact/Event
- 会议记录自动关联到商机
- 销售配额和分配规则同步

### 队列管理
- 实时队列监控
- No-show自动重新预约
- 会议缓冲时间配置
- 加班时段管理

## 使用示例

### 创建预约(Instant Booking)
```bash
curl -X POST \
  -H "Authorization: API-Key $CHILIPIPER_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.chilipiper.com/v1/bookings \
  -d '{
    "workspace": "inbound-sales",
    "router": "demo-requests",
    "lead": {
      "email": "prospect@example.com",
      "firstName": "张",
      "lastName": "三",
      "company": "科技公司",
      "phone": "+86-138-0000-0000"
    },
    "assignee_id": "auto",
    "timezone": "Asia/Shanghai"
  }'
```

### 查询可用时间段
```bash
curl -X POST \
  -H "Authorization: API-Key $CHILIPIPER_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.chilipiper.com/v1/availability \
  -d '{
    "workspace": "inbound-sales",
    "router": "demo-requests",
    "duration": 30,
    "timezone": "Asia/Shanghai",
    "start_date": "2026-04-08",
    "end_date": "2026-04-15"
  }'
```

### 获取路由分配结果
```bash
curl -X POST \
  -H "Authorization: API-Key $CHILIPIPER_API_KEY" \
  -H "Content-Type: application/json" \
  https://api.chilipiper.com/v1/routers/assign \
  -d '{
    "workspace": "inbound-sales",
    "router": "demo-requests",
    "lead": {
      "email": "vp@bigcorp.com",
      "company": "大型企业",
      "country": "United States"
    }
  }'
```

### 取消会议
```bash
curl -X DELETE \
  -H "Authorization: API-Key $CHILIPIPER_API_KEY" \
  https://api.chilipiper.com/v1/bookings/BOOKING_ID
```

### Webhook集成(表单提交后触发)
```javascript
// 网页表单提交后调用Chili Piper
<script>
  ChiliPiper.submit("inbound-sales", "demo-requests", {
    lead: {
      email: document.getElementById('email').value,
      firstName: document.getElementById('firstName').value,
      company: document.getElementById('company').value
    },
    onSuccess: function() {
      console.log('会议已预约');
    }
  });
</script>
```

## 适用场景
- Inbound高价值Lead需要立即响应(表单提交→秒级预约)
- 销售团队分工明确需要智能路由(企业客户→AE, 中小客户→SDR)
- 提高Demo预约的转化率(减少客户流失)
- 多产品线需要不同销售团队
- 全球团队按时区自动分配

## 限制和注意事项
- 仅企业版可用,价格较高(需联系销售)
- 配置复杂度高,需要专人管理路由规则
- 主要支持Salesforce/HubSpot集成,其他CRM支持有限
- 需要销售团队日历同步(Google/Outlook)
- 中国区可能需要VPN访问

## 定价
企业定制定价,需联系销售
- 一般起步价 $30-50/用户/月
- 按功能模块收费(Instant Booker/Distro/Handoff等)
- 年度合约优惠

## 参考链接
- [官方文档](https://help.chilipiper.com/hc/en-us/categories/360002048851-API-Documentation)
- [API参考](https://help.chilipiper.com/hc/en-us/articles/360057685851-API-Reference)
- [集成指南](https://help.chilipiper.com/hc/en-us/categories/360002048891-Integrations)
