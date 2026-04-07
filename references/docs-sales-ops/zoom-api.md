# Zoom API

> 视频会议API,销售Demo/Discovery Call自动化(录制/转录/集成CRM)

## 快速开始

### 安装
```bash
# Python(非官方但好用)
pip install zoom-python

# 或使用官方SDK
pip install zoomus
```

### 认证配置
- **方式:** oauth / Server-to-Server OAuth(推荐)
- **环境变量:**
  - `ZOOM_ACCOUNT_ID` — Zoom账户ID
  - `ZOOM_CLIENT_ID` — OAuth Client ID
  - `ZOOM_CLIENT_SECRET` — OAuth Client Secret
- **获取步骤:**
  1. 访问 [Zoom App Marketplace](https://marketplace.zoom.us/)
  2. Develop → Build App
  3. 选择 "Server-to-Server OAuth"
  4. 填写App信息并激活
  5. 获取 Account ID, Client ID, Client Secret
  6. 配置Scopes(如meeting:write, recording:read等)
- **验证:**
  ```bash
  # 获取Access Token
  ACCESS_TOKEN=$(curl -s -X POST "https://zoom.us/oauth/token?grant_type=account_credentials&account_id=$ZOOM_ACCOUNT_ID" \
    -u "$ZOOM_CLIENT_ID:$ZOOM_CLIENT_SECRET" | jq -r '.access_token')
  
  # 测试API
  curl -H "Authorization: Bearer $ACCESS_TOKEN" \
    https://api.zoom.us/v2/users/me
  ```

## 核心能力

### 会议管理
- 创建即时会议或预约会议
- 更新会议设置
- 删除/取消会议
- 列出用户的所有会议
- 会议邀请链接生成

### 会议录制
- 获取云录制列表
- 下载录制文件(MP4/M4A/Chat等)
- 删除录制
- 录制设置管理
- 录制分享权限

### 转录文本(Transcription)
- 获取会议转录文本(需Professional版以上)
- 下载VTT/SRT字幕文件
- 转录语言设置
- 转录搜索

### 参会者报告
- 谁参加了会议
- 参与时长
- 加入/离开时间
- 设备信息

### Webinar管理
- 创建和管理Webinar
- 注册管理
- 问卷调查
- 投票功能

### Webhook事件
- 会议开始/结束
- 参会者加入/离开
- 录制完成
- 转录完成

## 使用示例

### 获取Access Token
```bash
ACCESS_TOKEN=$(curl -s -X POST \
  "https://zoom.us/oauth/token?grant_type=account_credentials&account_id=$ZOOM_ACCOUNT_ID" \
  -u "$ZOOM_CLIENT_ID:$ZOOM_CLIENT_SECRET" | jq -r '.access_token')

echo "Access Token: $ACCESS_TOKEN"
```

### 创建会议
```bash
curl -X POST https://api.zoom.us/v2/users/me/meetings \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "topic": "产品Demo-科技公司",
    "type": 2,
    "start_time": "2026-04-10T14:00:00Z",
    "duration": 30,
    "timezone": "Asia/Shanghai",
    "agenda": "演示企业版功能和Q&A",
    "settings": {
      "host_video": true,
      "participant_video": true,
      "join_before_host": false,
      "mute_upon_entry": true,
      "auto_recording": "cloud"
    }
  }' | jq
```

### 列出所有会议
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/users/me/meetings?page_size=30" | jq
```

### 获取会议详情
```bash
MEETING_ID="1234567890"

curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/meetings/$MEETING_ID" | jq
```

### 获取参会者报告
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/report/meetings/$MEETING_ID/participants" | jq
```

### 获取录制列表
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/users/me/recordings?from=2026-04-01&to=2026-04-07" | jq
```

### 下载录制文件
```bash
RECORDING_ID="abc123def456"
DOWNLOAD_URL=$(curl -s -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/meetings/$MEETING_ID/recordings" | \
  jq -r '.recording_files[] | select(.file_type=="MP4") | .download_url')

# 下载(需要添加access_token参数)
curl "$DOWNLOAD_URL?access_token=$ACCESS_TOKEN" -o "demo-recording.mp4"
```

### 获取转录文本
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/meetings/$MEETING_ID/recordings/transcript" | jq
```

### 删除会议
```bash
curl -X DELETE \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  "https://api.zoom.us/v2/meetings/$MEETING_ID"
```

### Webhook订阅(会议事件)
```bash
curl -X POST https://api.zoom.us/v2/webhooks \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-domain.com/zoom-webhook",
    "events": [
      "meeting.started",
      "meeting.ended",
      "recording.completed"
    ],
    "event_delivery_method": "https"
  }' | jq
```

## 适用场景
- 自动创建Demo会议并发给客户(集成CRM)
- Demo结束自动下载录制上传到CRM
- 分析客户参与度(参会人数/时长)
- Webinar注册自动化
- 会议纪要自动生成(转录→AI总结)
- No-show追踪和重新预约

## 限制和注意事项
- 免费版会议40分钟限制(3人以上)
- 转录需Pro版以上($149/年/用户)
- API限流较严(部分端点10次/秒)
- 云录制存储有限额(Pro版1GB/用户)
- Webhook需要验证端点(Zoom会发送验证请求)
- 录制下载链接24小时过期

## 定价
- Basic: 免费 - 40分钟限制,无云录制
- Pro: $149/年/用户 - 无限时长,云录制1GB
- Business: $199/年/用户 - 更多云存储,管理功能
- Enterprise: 需联系销售 - 无限云存储,专属支持

## 参考链接
- [官方文档](https://developers.zoom.us/docs/api/)
- [API参考](https://marketplace.zoom.us/docs/api-reference/zoom-api/)
- [Webhook指南](https://developers.zoom.us/docs/api/rest/webhook-reference/)
- [录制管理](https://developers.zoom.us/docs/api/rest/reference/zoom-api/methods/#tag/Cloud-Recording)
