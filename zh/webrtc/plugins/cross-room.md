> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

默认情况下，不同房间的音视频流是相互隔离的，只有同一房间内的用户可以进行音视频通话。跨房连麦功能允许不同房间的用户进行音视频通话。

在直播场景中，该功能支持主播之间的通话，并让双方观众观看对方主播。本文介绍如何使用 CrossRoom 插件实现跨房连麦。

跨房连麦支持两种模式：
- **房间级连麦**：与目标房间的所有主播连麦。
- **用户级连麦**：与目标房间的某个主播连麦。

## 工作原理

- **房间级连麦**：将当前房间所有主播的音视频流额外推送到目标房间，同时将目标房间所有主播的音视频流额外推送到当前房间。
- **用户级连麦**：将当前房间自己的音视频流额外推送到目标房间，同时将目标房间指定主播的音视频流额外推送到当前房间。

## 使用步骤

> **注意**：确保您的 SDK 版本 >= v5.8.0

### 1. 开始跨房连麦

```js
import { CrossRoom } from 'trtc-sdk-v5/plugins/cross-room';

const trtc = TRTC.create({ plugins: [CrossRoom] });

await trtc.enterRoom({ sdkAppId, userId: 'user1', userSig, roomId: 7777 });

// 将当前房间 7777 的 user1 和目标房间 8888 的 user2 连接。user2 的流会推送到 7777 房间，user1 的流会推送到 8888 房间。
await trtc.startPlugin('CrossRoom', {
  roomId: 8888,
  // 与 roomId 二选一
  // strRoomId: '8888'
  userId: 'user2' // 不传则表示房间级跨房连麦
});
```

#### 参数说明

| 参数名      | 类型     | 必填 | 说明                                   |
|-------------|----------|------|----------------------------------------|
| roomId      | Number   | 否  | 目标房间 ID。                         |
| strRoomId      | String   | 否   | 目标房间 string 类型 ID，与 roomId 二选一。                         |
| userId      | String   | 否   | 目标房间的主播 ID，不传表示房间级连麦。|

---

### 2. 更新跨房连麦对端主播的 mute 状态

开启跨房连麦后，对端房间主播的流会推送到当前房间，当前房间内的所有用户都能接收到该主播的音视频流。

通过以下接口，您可以限制跨房主播在当前房间的上行能力（音频/主路视频/辅路视频），影响房间内所有用户。

禁用某种上行能力后，当前房间内的用户将无法接收对应的音视频流，也无法订阅。

```js
await trtc.updatePlugin('CrossRoom', {
  updateList: [{
    roomId: 8888,
    // strRoomId: '8888'
    userId: 'user2', // 不传则表示房间级跨房连麦
    muteAudio: true,
    muteVideo: false,
    muteSubStream: false,
  }]
});
```

#### 参数说明

| 参数名         | 类型     | 必填 | 说明                                   |
|----------------|----------|------|----------------------------------------|
| updateList     | Array    | 是   | 包含需要更新的跨房主播 mute 状态的列表 |
| updateList[0].roomId         | Number   | 否  | 目标房间 ID。                         |
| updateList[0].strRoomId         | Number   | 否   | 目标房间 string 类型 ID，与 roomId 二选一。                         |
| updateList[0].userId         | String   | 否   | 目标房间的主播 ID，不传表示房间级连麦。|
| updateList[0].muteAudio      | Boolean  | 否   | 是否停止跨房主播的音频流。                      |
| updateList[0].muteVideo      | Boolean  | 否   | 是否停止跨房主播的主路视频流。                  |
| updateList[0].muteSubStream  | Boolean  | 否   | 是否停止跨房主播的辅路视频流。                  |

---

### 3. 停止跨房连麦

```js
await trtc.stopPlugin('CrossRoom', { roomId: 8888 }); // 停止指定房间的跨房连麦

await trtc.stopPlugin('CrossRoom'); // 停止当前用户发起的所有跨房连麦
```

#### 参数说明

| 参数名      | 类型     | 必填 | 说明                                   |
|-------------|----------|------|----------------------------------------|
| roomId      | Number   | 否   | 目标房间 ID。                         |
| strRoomId   | String   | 否   | 目标房间 string 类型 ID，与 roomId 二选一          |
