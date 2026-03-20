> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

# 集成音视频通话
## 说明
RTC Engine Web SDK 也称为 TRTC Web SDK，是腾讯云实时音视频 Web SDK。提供 npm package name “trtc-sdk-v5”。

## 前提条件

1. [注册腾讯云](https://cloud.tencent.com/document/product/378/17985)账号，[创建实时音视频应用](https://console.cloud.tencent.com/trtc/app)。
2. [获取临时 userSig](https://console.cloud.tencent.com/trtc/usersigtool) ，或者部署 [userSig 签发服务](https://cloud.tencent.com/document/product/647/17275?from_cn_redirect=1#formal)。
3. 为了体验完整的 TRTC 能力，建议开发时使用 `http://localhost` ，生产环境用 `https://[域名]` 访问页面，参考文档[页面访问协议说明](./tutorial-05-info-browser.html#h2-3)。
4. 为了避免防火墙安全策略限制正常的 TRTC 数据传输，需要参考文档[应对防火墙策略](./tutorial-05-info-browser.html#h2-4)进行设置。
5. 为了保证通话体验，建议在正式开始音视频通话前，进行设备检测和浏览器兼容性检测，可以参考文档[浏览器兼容信息](./tutorial-05-info-browser.html#h2-2)，[通话前环境与设备检测](tutorial-23-advanced-support-detection.html)。

## 集成 SDK

SDK 提供了 UMD、ES Module 类型的模块，以及 TypeScript Type Definition 文件，满足在不同类型项目中集成。

### NPM 集成

1. 您可以在项目中使用 [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) 安装 [trtc-sdk-v5](https://www.npmjs.com/package/trtc-sdk-v5)。

```javascript
npm install trtc-sdk-v5 --save
```

2. 在项目脚本里导入模块。

```javascript
import TRTC from 'trtc-sdk-v5';
```

### Script 集成

1. 在您的 Web 页面中添加如下代码即可：

```html
<script src="trtc.js"></script>
// or
<script src="https://cdn.jsdelivr.net/npm/trtc-sdk-v5@latest"></script>
// 禁止使用 https://web.sdk.qcloud.com/trtc/webrtc/v5/trtc.js
```

**资源下载**
- CDN 资源:https://cdn.jsdelivr.net/npm/trtc-sdk-v5@latest
- [npm package](https://www.npmjs.com/package/trtc-sdk-v5)
- [单击下载 SDK 及示例代码](https://web.sdk.qcloud.com/trtc/webrtc/v5/download/webrtc_v5_latest.zip)
- [GitHub 仓库地址](https://github.com/LiteAVSDK/TRTC_Web)

## SDK 使用逻辑概览

**基本概念**

您在使用 TRTC Web SDK 时，会接触到以下概念：

- [TRTC](TRTC.html) 类，其实例代表一个本地客户端。TRTC 的对象方法提供了加入通话房间、预览本地摄像头、发布本地摄像头和麦克风、播放远端音视频等功能。

**实现音视频通话基本逻辑**

1. 调用 [TRTC.create()](TRTC.html#.create) 方法创建 [trtc](TRTC.html) 对象。
2. 调用 [trtc.enterRoom()](TRTC.html#enterRoom) 进入房间，进入后其他用户会收到 [TRTC.EVENT.REMOTE_USER_ENTER](module-EVENT.html#.REMOTE_USER_ENTER) 进房事件。
3. 在进入房间后，可以开启摄像头和麦克风并发布到房间。
   - 调用 [TRTC.startLocalVideo()](TRTC.html#startLocalVideo) 开启摄像头并发布到房间。
   - 调用 [TRTC.startLocalAudio()](TRTC.html#startLocalAudio) 开启麦克风并发布到房间。
4. 当一个远端用户发布了音视频后，SDK 默认情况下会自动播放远端音频。您需要通过如下方式来播放远端视频：
   - 在进房前监听 [TRTC.EVENT.REMOTE_VIDEO_AVAILABLE](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件，就能收到所有远端用户的发布视频事件。
   - 在事件回调函数中，调用 [trtc.startRemoteVideo()](TRTC.html#startRemoteVideo) 方法播放远端视频。

下图展示了实现音视频通话全过程的基础 API 调用流程：
![TRTC SDK调用序列图](assets/trtc-sdk-call-sequence-cn.png)

## 创建 TRTC 对象

通过 [TRTC.create()](TRTC.html#.create) 方法创建 [TRTC](TRTC.html) 对象

```javascript
const trtc = TRTC.create();
```

## 进入房间

调用 [trtc.enterRoom()](TRTC.html#enterRoom) 进入房间。通常在`开始通话`按钮的点击回调里进行调用。
关键参数：

- `scene`: 实时音视频通话模式，设置为 'rtc'。
- `sdkAppId`: 您在腾讯云创建的音视频应用的 sdkAppId。
- `userId`: 用户 ID，由您指定。
- `userSig`: 用户签名，参考[获取临时 userSig](https://console.cloud.tencent.com/trtc/usersigtool)，或者部署 [userSig 签发服务](https://cloud.tencent.com/document/product/647/17275?from_cn_redirect=1#formal)。
- `roomId`：房间 ID，由您指定，通常是生成唯一的房间ID。
  更详细的参数说明参考接口文档 [trtc.enterRoom()](TRTC.html#enterRoom)。

```javascript
try {
  await trtc.enterRoom({ roomId: 8888, scene:'rtc', sdkAppId, userId, userSig });
  console.log('进房成功');
} catch (error) {
  console.error('进房失败 ' + error);
}
```

## 开启摄像头、麦克风

### 开启摄像头

使用 [trtc.startLocalVideo()](TRTC.html#startLocalVideo) 方法开启摄像头，并发布到房间。

```javascript
// 为了预览摄像头画面，您需在 DOM 中放置一个 HTMLElement，可以是一个 div 标签，假设其 id 为 local-video。
const view = 'local-video';
await trtc.startLocalVideo({ view });
```

### 开启麦克风

使用 [trtc.startLocalAudio()](TRTC.html#startLocalAudio) 方法开启麦克风，并发布到房间。

```javascript
await trtc.startLocalAudio();
```

## 播放远端音视频

### 播放远端音频

默认情况下，SDK 会自动播放远端音频，您无需调用 API 来播放远端音频。

- 需要注意的是：如果用户在进房前没有与页面产生过交互，自动播放音频可能会因为【浏览器的自动播放策略限制】而失败，您需参考[自动播放受限处理建议](./tutorial-21-advanced-auto-play-policy.html)进行处理。
- 若您不希望 SDK 自动播放音频，您可以在 [trtc.enterRoom()](TRTC.html#enterRoom) 时设置 autoReceiveAudio = false 关闭自动拉流及自动播放音频。
- 监听 [TRTC.EVENT.REMOTE_AUDIO_AVAILABLE](module-EVENT.html#.REMOTE_AUDIO_AVAILABLE) 事件，记录有远端音频的 userId，在需要播放音频时，调用 [trtc.muteRemoteAudio(userId, false)](TRTC.html#muteRemoteAudio) 方法。

### 播放远端视频

在进房前监听 [TRTC.EVENT.REMOTE_VIDEO_AVAILABLE](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件，在收到该事件时，通过 [trtc.startRemoteVideo()](TRTC.html#startRemoteVideo) 播放远端视频流。

```javascript
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  // 为了播放视频画面，您需在 DOM 中放置一个 HTMLElement，可以是一个 div 标签，假设其 id 为 `${userId}_${streamType}`
  const view = `${userId}_${streamType}`;
  trtc.startRemoteVideo({ userId, streamType, view });
});
```

## 退出房间

调用 [trtc.exitRoom()](TRTC.html#exitRoom) 方法退出房间，结束音视频通话。

```javascript
await trtc.exitRoom(); 
// 退房成功后，若后续无需使用 trtc 实例，则可以调用 trtc.destroy 方法销毁实例，及时释放相关资源。销毁后的 trtc 实例无法继续使用，需要重新创建新的实例。
trtc.destroy();
```

**处理被踢**

除了用户主动退出房间之外，用户也有可能因为如下原因被踢出房间，此时 SDK 会抛出 [KICKED_OUT](module-EVENT.html#.KICKED_OUT) 事件，这时不需要调用 `trtc.exitRoom()` 退房，SDK 自动进入退房状态。

1. `kick`：两个相同 userId 的用户进入相同房间，前一个进房的用户会被踢出。同名用户同时进入同一房间是不允许的行为，可能会导致双方音视频通话异常，应避免出现这种情况。
2. `banned`：通过服务端的 [RemoveUser](https://cloud.tencent.com/document/api/647/40496) | [RemoveUserByStrRoomId](https://cloud.tencent.com/document/api/647/50426) 接口将某个用户踢出某个 TRTC 房间，该用户会收到被踢事件，reason 为 `banned` 。
3. `room_disband`：通过服务端的 [DismissRoom](https://cloud.tencent.com/document/api/647/50089) | [DismissRoomByStrRoomId](https://cloud.tencent.com/document/api/647/37088)接口将某个 TRTC 房间解散，解散房间之后，该房间的所有用户都会收到被踢事件，reason 为 `room_disband`。

```javascript
trtc.on(TRTC.EVENT.KICKED_OUT, error => {
  console.error(`kicked out, reason:${error.reason}, message:${error.message}`);
  // error.reason 有以下几种情况
  // 'kick' 由于相同 userId 进相同房间，导致先进入的用户被踢。
  // 'banned' 被管理员移出房间
  // 'room_disband' 管理员解散了房间
});
```

# 互动直播连麦

## 主播端

主播端实现音视频通话的流程与 [TRTC.TYPE.SCENE_RTC](module-TYPE.html#.SCENE_RTC) 场景的实现流程基本一致。参考：[开始集成音视频通话](./tutorial-11-basic-video-call.html)。

主要差异在于进房时，设置的 screne 和 role 参数不同。参考如下示例代码：

```javascript
await trtc.enterRoom({ sdkAppId, userId, userSig, roomId, scene: TRTC.TYPE.SCENE_LIVE, role: TRTC.TYPE.ROLE_ANCHOR });
```

## 观众端

### 以观众身份进房

设置 role 参数为 TRTC.TYPE.ROLE_AUDIENCE。

```javascript
await trtc.enterRoom({ roomId, sdkAppId, userId, userSig, scene: TRTC.TYPE.SCENE_LIVE, role: TRTC.TYPE.ROLE_AUDIENCE })
```

### 播放远端音频

默认情况下，SDK 会自动播放远端音频，您无需调用 API 来播放远端音频。

- 需要注意的是：如果用户在进房前没有与页面产生过交互，自动播放音频可能会因为【浏览器的自动播放策略限制】而失败，您需参考[自动播放受限处理建议](./tutorial-21-advanced-auto-play-policy.html)进行处理。
- 若您不希望 SDK 自动播放音频，您可以在 [trtc.enterRoom()](TRTC.html#enterRoom) 时设置 autoReceiveAudio = false 关闭自动拉流及自动播放音频。
- 监听 [TRTC.EVENT.REMOTE_AUDIO_AVAILABLE](module-EVENT.html#.REMOTE_AUDIO_AVAILABLE) 事件，记录有远端音频的 userId，在需要播放音频时，调用 [trtc.muteRemoteAudio(userId, false)](TRTC.html#muteRemoteAudio) 方法。

### 播放远端视频

在进房前监听 [TRTC.EVENT.REMOTE_VIDEO_AVAILABLE](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件，在收到该事件时，通过 [trtc.startRemoteVideo()](TRTC.html#startRemoteVideo) 播放远端视频流。

```javascript
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  // 为了播放视频画面，您需在 DOM 中放置一个 HTMLElement，可以是一个 div 标签，假设其 id 为 `${userId}_${streamType}`
  const view = `${userId}_${streamType}`;
  trtc.startRemoteVideo({ userId, streamType, view });
});
```

### 上麦与主播连麦互动

观众角色没有发布音视频的权限，因此观众若想与主播连麦互动，需要先使用 [switchRole](TRTC.html#switchRole) 切换成主播角色，再发布音视频流。

```javascript
// 切换成主播上麦
await trtc.switchRole(TRTC.TYPE.ROLE_ANCHOR);
// 上麦后开启麦克风
await trtc.startLocalAudio();
// 上麦后开启摄像头
await trtc.startLocalVideo();
// 连麦结束后，调用 exitRoom 退房结束连麦。
await trtc.exitRoom();
```

## 切换摄像头

```javascript
// 打开摄像头，默认是摄像头列表中第一个摄像头。
await trtc.startLocalVideo();
const cameraList = await TRTC.getCameraList();
// 切换到第二个摄像头
if (cameraList[1]) {
  await trtc.updateLocalVideo({ option: { cameraId: cameraList[1].deviceId }});
}
// 移动端
// 切换前置摄像头
await trtc.updateLocalVideo({ option: { useFrontCamera: true }});
// 切换后置摄像头
await trtc.updateLocalVideo({ option: { useFrontCamera: false }});
```

## 切换麦克风

```javascript
// 打开麦克风，默认麦克风列表中第一个麦克风。
await trtc.startLocalAudio();
const microphoneList = await TRTC.getMicrophoneList();
// 切换到第二个麦克风
if (microphoneList[1]) {
  await trtc.updateLocalAudio({ option: { microphoneId: microphoneList[1].deviceId }});
}
```

# Tutorial: 屏幕分享

## 功能描述

本文主要介绍如何在 TRTC Web SDK 实现屏幕分享功能。

## 实现流程

1. **【推流端】开启屏幕分享**

```javascript
const trtcA = TRTC.create();
await trtcA.enterRoom({
  scene: 'rtc',
  sdkAppId: 140000000, // 填写您的 sdkAppId
  userId: 'userA', // 填写您的 userId
  userSig: 'userA_sig', // 填写 userId 对应的 userSig
  roomId: 6969
})
await trtcA.startScreenShare();
```

2. **【拉流端】播放屏幕分享**

```javascript
const trtcB = TRTC.create();
trtcB.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  // 主路视频流，一般是推摄像头的那路流
  if (streamType === TRTC.TYPE.STREAM_TYPE_MAIN) {
    // 1. 在页面中放置一个 id 为 `${userId}_main` 的 div 标签，用于在 div 标签内播放主路流。业务侧可自定义 div 标签的 id，此处只是举例说明。
    // 2. 播放主路视频流
    trtcB.startRemoteVideo({ userId, streamType,  view: `${userId}_main` });
  } else {
    // 辅路视频流，一般是推屏幕分享的那路流。
    // 1. 在页面中放置一个 id 为 `${userId}_screen` 的 div 标签，用于在 div 标签内播放屏幕分享。业务侧可自定义 div 标签的 id，此处只是举例说明。
    // 2. 播放屏幕分享
    trtcB.startRemoteVideo({ userId, streamType, view: `${userId}_screen` });
  }
});
await trtcB.enterRoom({
  scene: 'rtc',
  sdkAppId: 140000000, // 填写您的 sdkAppId
  userId: 'userB', // 填写您的 userId
  userSig: 'userB_sig', // 填写 userId 对应的 userSig
  roomId: 6969
})
```

3. **同时推摄像头 + 屏幕分享**

```javascript
await trtcA.startLocalVideo();
await trtcA.startScreenShare();
```

4. **屏幕分享 + 系统音频**

采集系统音频支持 Chrome M74+

- 在 Windows 和 Chrome OS 上，可以采集整个系统的音频。
- 在 Linux 和 Mac 上，只能采集某个页面的音频。
- 其它 Chrome 版本、其它系统、其它浏览器均不支持。

```javascript
await trtcA.startScreenShare({ option: { systemAudio: true }});
```

在弹出的对话框中勾选`分享音频`，系统音频会与本地麦克风混音后发布，房间内其他用户会收到 TRTC.EVENT.REMOTE_AUDIO_AVALIABLE 事件

![系统音频分享对话框](https://main.qcloudimg.com/raw/4e990a612028480c9c36419d96ea64b7.png)

5. **停止屏幕分享**

```javascript
// 停止屏幕分享采集及发布
await trtcA.stopScreenShare();
// 房间内的其他用户会收到 TRTC.EVENT.REMOTE_VIDEO_UNAVAILABLE 事件，streamType 是 TRTC.TYPE.STREAM_TYPE_SUB。
trtcB.on(TRTC.EVENT.REMOTE_VIDEO_UNAVAILABLE, ({ userId, streamType }) => {
   if (streamType === TRTC.TYPE.STREAM_TYPE_SUB) {
   }
})
```

另外用户还可能会通过浏览器自带的按钮停止屏幕分享，因此屏幕分享流需要监听屏幕分享停止事件，并进行相应的处理。

![屏幕分享停止按钮](./assets/screen-sharing-stop.png)

```javascript
// 监听屏幕分享停止事件
trtcA.on(TRTC.EVENT.SCREEN_SHARE_STOPPED, () => {
  console.log('screen sharing was stopped');
});
```

## 在 Electron 中屏幕分享

> 建议优先使用 [TRTC Electron SDK](https://cloud.tencent.com/document/product/647/38551)

因为 Electron 未实现浏览器支持的 WebRTC 标准的 `getDisplayMedia` 接口，所以如果网页中包含了 WebRTC 屏幕分享相关的逻辑，将无法在 Electron 中像普通浏览器一样正常使用 [TRTC Web SDK](https://cloud.tencent.com/document/product/647/17249) 进行屏幕分享。如果只想使用 [TRTC Web SDK](https://cloud.tencent.com/document/product/647/17249)，请参考以下方案。

实现屏幕分享，需要使用到 Electron 的 API：`desktopCapturer.getSources({ types: ['window', 'screen'] })`

1. 主进程 `main.js` 监听页面加载完成后，通过 `desktopCapturer.getSources` 获取屏幕分享源列表，并将屏幕分享源列表发送给渲染进程
2. 渲染进程监听主进程事件，从而获取屏幕分享源列表
3. 创建屏幕分享流并通过 [TRTC Web SDK](https://cloud.tencent.com/document/product/647/17249) 进行推流
   1. 通过 `navigator.mediaDevices.getUserMedia()` 从系统 API 获取屏幕分享的 `MediaStream`
   2. 在 `TRTC.startScreenShare()` 的`option`参数中传入自定义采集的 videoTrack，推屏幕分享流

```javascript
// 【1】main.js 主进程获取屏幕分享源列表
const { app, BrowserWindow, desktopCapturer, systemPreferences } = require('electron');
function createWindow () {
  const win = new BrowserWindow({
    // ...
  });
  win.loadFile('./src/index.html');
  win.webContents.on("did-finish-load", async () => {
    if (win) {
      const sources = await desktopCapturer.getSources({
        types: ["window", "screen"],
      });
      win.webContents.send("SEND_SCREEN_SHARE_SOURCES", sources);
    }
  });
}
// 【2】渲染进程监听主进程事件拿到屏幕分享源列表
const { ipcRenderer } = require('electron');
let shareSourceList = [];
ipcRenderer.on('SEND_SCREEN_SHARE_SOURCES', async (event, sources) => {
  const selectContainer = window.document.getElementById('screen-share-select');
  shareSourceList = sources;
  sources.forEach(obj => {
    const optionElement = document.createElement('option');
    optionElement.innerText = `${obj.name}`;
    selectContainer.appendChild(optionElement);
  });
})
// 【3】渲染进程推屏幕分享
async function startScreenShare() {
  const selectContainer = document.getElementById('screen-share-select');
  const selectValue = selectContainer.options[selectContainer.selectedIndex].value;
  const [ source ] = shareSourceList.filter(obj => obj.name === `${selectValue}`);
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      audio: false,
      video: {
        mandatory: {
          chromeMediaSource: 'desktop',
          chromeMediaSourceId: source.id, // 屏幕分享源 id
          minWidth: 1280,
          maxWidth: 1280,
          minHeight: 720,
          maxHeight: 720
        }
      }
    });
    const trtc = TRTC.create();
    await trtc.enterRoom({
      // ...
    });
    await trtc.startScreenShare({
      option: {
        videoTrack: stream.getVideoTracks()[0],
      }
    })
  } catch (error) {
    console.error('start screen share error = ', error)
  }
}
```

## 注意事项

1. [什么是主流，辅流？](module-TYPE.html#.STREAM_TYPE_MAIN)
2. SDK 默认使用 `1080p` 参数配置来采集屏幕分享，具体参考接口：[startScreenShare](TRTC.html#startScreenShare)

## 常见问题

1. **Safari 屏幕分享出现报错 `getDisplayMedia must be called from a user gesture handler`**

这是因为 Safari 限制了 `getDisplayMedia` 屏幕采集的接口，必须在用户点击事件的回调函数执行的 1 秒内才可以调用。

参考：[webkit issue](https://bugs.webkit.org/show_bug.cgi?id=198040)。

```javascript
// good
async function onClick() {
  // 建议在 onClick 执行时，先执行采集逻辑
  await trtcA.startScreenShare();
  await trtcA.enterRoom({ 
    roomId: 123123,
    sdkAppId: 140000000, // 填写您的 sdkAppId
    userId: 'userA', // 填写您的 userId
    userSig: 'userA_sig', // 填写 userId 对应的 userSig });
});
// bad
async function onClick() {
  await trtcA.enterRoom({ 
    roomId: 123123,
    sdkAppId: 140000000, // 填写您的 sdkAppId
    userId: 'userA', // 填写您的 userId
    userSig: 'userA_sig', // 填写 userId 对应的 userSig });
  })
  // 进房可能耗时超过 1s，可能会采集失败
   await trtcA.startScreenShare();
}
```

2. **macOS Monterey(12.2.1), 在 Electron 环境下使用屏幕分享需在主进程中请求设备权限**

```javascript
async function checkAndApplyDeviceAccessPrivilege() {
  const cameraPrivilege = systemPreferences.getMediaAccessStatus('camera');
  console.log(
    `checkAndApplyDeviceAccessPrivilege before apply cameraPrivilege: ${cameraPrivilege}`
  );
  if (cameraPrivilege !== 'granted') {
    await systemPreferences.askForMediaAccess('camera');
  }
  const micPrivilege = systemPreferences.getMediaAccessStatus('microphone');
  console.log(
    `checkAndApplyDeviceAccessPrivilege before apply micPrivilege: ${micPrivilege}`
  );
  if (micPrivilege !== 'granted') {
    await systemPreferences.askForMediaAccess('microphone');
  }
  const screenPrivilege = systemPreferences.getMediaAccessStatus('screen');
  console.log(
    `checkAndApplyDeviceAccessPrivilege before apply screenPrivilege: ${screenPrivilege}`
  );
}
```

3. **Mac Chrome 在已授权屏幕录制的情况下屏幕分享失败，出现 "NotAllowedError: Permission denied by system" 或者 "NotReadableError: Could not start video source" 错误信息，[Chrome bug](https://bugs.chromium.org/p/chromium/issues/detail?id=1306876)。解决方案：打开【设置】> 点击【安全性与隐私】> 点击【隐私】> 点击【屏幕录制】> 关闭 Chrome 屏幕录制授权 > 重新打开 Chrome 屏幕录制授权 > 关闭 Chrome 浏览器 > 重新打开 Chrome 浏览器。**

4. **[WebRTC 屏幕分享已知问题及规避方案](./tutorial-02-info-webrtc-issues.html#h2-9)**

# Tutorial: 设置本地视频属性

本文主要介绍如何在视频通话或互动直播中设置视频属性，开发者可以根据具体业务需求调整视频画面的清晰度和流畅度，获得更好的用户体验。视频属性包括分辨率、帧率和码率。

## 实现方式

通过 trtc 对象的 [startLocalVideo()](TRTC.html#startLocalVideo) 或 [updateLocalVideo()](TRTC.html#updateLocalVideo) 方法设置视频属性：

- 指定一个预定义的 Profile，每个 Profile 对应着一套推荐的分辨率、帧率和码率。

```javascript
// 启动时指定视频属性
await trtc.startLocalVideo({
  option: { profile: '480p' }
});
// 通话过程中动态调整视频属性
await trtc.updateLocalVideo({
  option: { profile: '360p' }
});
```

- 指定自定义分辨率、帧率和码率

```javascript
// 启动时指定视频属性
await trtc.startLocalVideo({
  option: { profile: { width: 640, height: 480, frameRate: 15, bitrate: 900 /* kpbs */} }
});
// 通话过程中动态调整视频属性
await trtc.updateLocalVideo({
  option: { profile: { width: 640, height: 360, frameRate: 15, bitrate: 800 /* kpbs */} }
});
```

## 视频属性 Profile 列表

| 视频 Profile | 分辨率（宽 x 高） | 帧率（fps） | 码率（kbps） | 备注 |
|-------------|-----------------|-----------|------------|------|
| 120p | 160 x 120 | 15 | 200 | |
| 120p_2 | 160 x 120 | 15 | 100 | v5.1.1+ 支持 |
| 180p | 320 x 180 | 15 | 350 | |
| 180p_2 | 320 x 180 | 15 | 150 | v5.1.1+ 支持 |
| 240p | 320 x 240 | 15 | 400 | |
| 240p_2 | 320 x 240 | 15 | 200 | v5.1.1+ 支持 |
| 360p | 640 x 360 | 15 | 800 | |
| 360p_2 | 640 x 360 | 15 | 400 | v5.1.1+ 支持 |
| 480p | 640 x 480 | 15 | 900 | |
| 480p_2 | 640 x 480 | 15 | 500 | 默认值，v5.1.1+ 支持 |
| 720p | 1280 x 720 | 15 | 1500 | |
| 1080p | 1920 x 1080 | 15 | 2000 | |
| 1440p | 2560 x 1440 | 30 | 4860 | |
| 4K | 3840 x 2160 | 30 | 9000 | |

- 由于设备和浏览器的限制，视频分辨率不一定能够完全匹配，在这种情况下，浏览器会自动调整分辨率使其接近 Profile 对应的分辨率