# Chorus.ai by ZoomInfo

> 对话智能平台(ZoomInfo旗下),功能类似Gong,侧重ZoomInfo数据联动

## 快速开始

### 安装
无CLI,使用 HTTP API 调用

### 认证配置
- **方式:** oauth
- **环境变量:**
  - `CHORUS_API_KEY` — Chorus API密钥
- **获取步骤:**
  1. 购买ZoomInfo订阅(含Chorus)
  2. 联系客户经理开通API权限
  3. 登录Chorus平台
  4. Settings → API Access(需管理员权限)
  5. Generate API Key
- **验证:**
  ```bash
  curl -H "Authorization: Bearer $CHORUS_API_KEY" \
    https://api.chorus.ai/v1/calls | jq
  ```

## 核心能力

### 通话录音和转录
- 自动录制销售电话/视频会议
- 实时转录(低延迟)
- 多语言支持
- 话者分离
- 集成主流会议平台

### AI内容提取
- 客户需求和痛点识别
- 异议类型分类
- 竞品提及追踪
- 定价讨论提取
- 决策流程分析

### 销售行为分析
- 提问vs倾听比例
- 开放式问题质量
- Feature Dump检测(单方面灌输)
- 客户参与度评分
- 通话流程结构分析

### Deal Tracker
- 追踪商机进展信号
- 多次通话的情绪变化
- 风险预警(客户态度转冷)
- 关键人物参与情况
- 下一步行动追踪

### Call Summary自动生成
- AI生成通话摘要
- 关键要点提取
- Action Items列表
- 客户承诺记录
- 同步到CRM

### ZoomInfo数据联动
- 联系人信息自动补全
- 公司组织架构显示
- 决策人识别
- 技术栈关联
- Intent Data联动

## 使用示例

### 获取最近的通话
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/calls?start_date=2026-04-01&end_date=2026-04-07" | jq
```

### 获取通话转录
```bash
CALL_ID="abc123"

curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/calls/$CALL_ID/transcript" | jq
```

### 查询包含特定关键词的通话
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/calls/search?keywords=pricing,budget&date_from=2026-03-01" | jq
```

### 获取通话分析报告
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/calls/$CALL_ID/analytics" | jq '.talk_ratio, .questions_asked, .customer_sentiment'
```

### 获取Deal相关通话
```bash
DEAL_ID="0061234567"

curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/opportunities/$DEAL_ID/calls" | jq
```

### 下载通话录音
```bash
curl -H "Authorization: Bearer $CHORUS_API_KEY" \
  "https://api.chorus.ai/v1/calls/$CALL_ID/recording" \
  --output "chorus_call_${CALL_ID}.mp4"
```

## 适用场景
- 已用ZoomInfo时的配套选择(数据打通)
- 类Gong需求但预算考虑(价格相对灵活)
- 需要Intent Data + 通话分析联动
- 销售培训和Coaching
- 竞品分析(批量提取竞品提及)
- QA质量检查(客户服务场景)

## 限制和注意事项
- 需ZoomInfo订阅,API功能需单独开通
- API功能覆盖不如Gong完善
- 部分高级分析需要企业版
- 录音合规要求(GDPR/TCPA)
- API限流:50-100次/分钟(根据套餐)
- ZoomInfo生态绑定(不如Gong独立性强)
- 主要优化英语,其他语言支持有限

## 定价
- 需ZoomInfo生态订阅,联系销售
- Chorus功能一般作为ZoomInfo套餐的一部分
- 单独Chorus定价:$800-1,200/用户/年
- API访问需企业级套餐
- 最少10用户起

## 参考链接
- [官方文档](https://support.chorus.ai/hc/en-us/categories/360003040054-API)
- [ZoomInfo集成](https://www.chorus.ai/integrations/zoominfo/)
- [最佳实践](https://www.chorus.ai/conversation-intelligence/)
