# Chorus.ai by ZoomInfo

> 对话智能平台(ZoomInfo旗下),功能类似Gong,侧重ZoomInfo数据联动

## 快速开始

### 安装
无需安装CLI,直接使用HTTP API调用。

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `CHORUS_API_KEY` — API密钥
- **获取步骤:**
  1. 登录 Chorus Dashboard
  2. Settings → API Access(需管理员开通)
  3. Generate API Key
  4. 复制生成的API Key
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $CHORUS_API_KEY" \
    https://api.chorus.ai/api/v1/me
  ```

## 核心能力
- 通话录音和转录
- AI提取客户需求/异议/竞品
- 销售行为分析(提问/倾听比例)
- Deal Tracker(追踪商机进展信号)
- 自动生成Call Summary
- 与ZoomInfo联系人数据打通
- 关键时刻标记(Key Moments)
- 团队表现分析

## 使用示例

### 获取当前用户信息
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  https://api.chorus.ai/api/v1/me
```

### 获取通话列表
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/api/v1/calls?from=2026-04-01&to=2026-04-07&limit=50"
```

### 获取通话详情
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  https://api.chorus.ai/api/v1/calls/{call_id}
```

### 获取通话转录
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  https://api.chorus.ai/api/v1/calls/{call_id}/transcript
```

### 获取通话分析洞察
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  https://api.chorus.ai/api/v1/calls/{call_id}/insights
```

### 获取提及的竞争对手
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/api/v1/calls/{call_id}/trackers?type=competitor"
```

### 导出通话摘要
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  https://api.chorus.ai/api/v1/calls/{call_id}/summary \
  > call_summary.json
```

## 适用场景
- 已用ZoomInfo时的配套选择
- 类Gong需求但预算考虑
- 通话录音和AI分析
- 识别客户痛点和异议
- 销售Coaching和培训
- 与ZoomInfo联系人数据无缝联动

## 限制和注意事项
- API功能不如Gong完善
- 需ZoomInfo生态绑定
- 录音合规要求(GDPR/各地法规)
- API限流: 具体限制未公开(估算60-120次/分钟)
- 转录语言支持有限(主要英语)
- 需要与视频会议工具集成(Zoom/Teams等)
- 录音存储根据套餐有时限

## 定价
- 需ZoomInfo订阅,联系销售
- 通常捆绑在ZoomInfo套餐中
- 估算: $1,000-1,800/用户/年起(含ZoomInfo)

## 参考链接
- [官方文档](https://support.chorus.ai/hc/en-us/categories/360003040054-API)
- [ZoomInfo集成](https://www.zoominfo.com/products/chorus)
- [帮助中心](https://support.chorus.ai/)
