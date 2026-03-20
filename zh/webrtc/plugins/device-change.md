> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何检测用户的设备插入、拔出、启动的行为。以及如何处理设备采集异常。

> **关于 SDK 自动恢复采集**<br>
> SDK 会检测摄像头、麦克风的插拔行为，并在恰当时机，自动恢复采集。具体逻辑如下：
> 1. 若当前正在推流的摄像头/麦克风被拔出，SDK 会尝试使用剩余可用的媒体设备，恢复采集。
> 2. 若摄像头/麦克风被拔出后，没有可用的媒体设备。SDK 会检测设备插入行为，若插入了新的可用的媒体设备，SDK 会尝试使用新插入的媒体设备，恢复采集并推流。
> 3. 若恢复失败，则会抛出错误 [TRTC.ERROR_CODE.DEVICE_ERROR](./module-ERROR_CODE.html#.DEVICE_ERROR)，extraCode 为 5308，5309。

## 实现流程

### 检测设备插入、拔出、启动

```js
trtc.on(TRTC.EVENT.DEVICE_CHANGED, (event) => {
  // 设备插入
  if (event.action === 'add') {
    // 摄像头插入
    if (event.type === 'camera') {}
  } else if (event.action === 'remove') {
    // 设备拔出
  } else if (event.action === 'active') {
    // 设备启动
  }
  console.log(`${event.type}(${event.device.label}) ${event.action}`);
});
```

### 判断是否当前正在使用的设备被拔出

```js
trtc.on(TRTC.EVENT.DEVICE_CHANGED, (event) => {
  if (event.action === 'remove') {
    if (event.type === 'camera') {
      const currentCameraId = trtc.getVideoTrack()?.getSettings()?.deviceId;
      if (event.device.deviceId === currentCameraId) {
        // 当前正在使用的摄像头被拔出。
      }
    } else if (event.type === 'microphone') {
      const currentMicId = trtc.getAudioTrack()?.getSettings()?.deviceId;
      if (event.device.deviceId === currentMicId) {
        // 当前正在使用的麦克风被拔出。
      }
    }
  }
});
```

### 在用户插入新设备时，自动切换到使用新设备。

```js
trtc.on(TRTC.EVENT.DEVICE_CHANGED, (event) => {
  if (event.action === 'add') {
    if (event.type === 'camera') {
      // 插入摄像头后，自动切换到新的摄像头
      trtc.updateLocalVideo({ option: { cameraId: event.device.deviceId }});
    }
  }
});
```

### 处理设备采集异常

监听设备采集异常事件

```js
trtc.on(TRTC.EVENT.VIDEO_PLAY_STATE_CHANGED, event => {
  // 本地摄像头采集异常，此时 SDK 会尝试自动恢复采集，您可以引导用户检查摄像头是否正常。
  if (event.userId === '' && event.streamType === TRTC.TYPE.STREAM_TYPE_MAIN && (event.reason === 'mute' || event.reason === 'ended')) {}
});

trtc.on(TRTC.EVENT.AUDIO_PLAY_STATE_CHANGED, event => {
  // 本地麦克风采集异常，此时 SDK 会尝试自动恢复采集，您可以引导用户检查麦克风是否正常。
  if (event.userId === '' && (event.reason === 'mute' || event.reason === 'ended')) {}
});
```

监听 SDK 自动恢复采集失败事件
```js
trtc.on(TRTC.EVENT.ERROR, error => {
  if (error.code === TRTC.ERROR_CODE.DEVICE_ERROR) {
    // 摄像头恢复采集失败
    if (error.extraCode === 5308) {
      // 引导用户检查设备后，调用 updateLocalVideo 传入 cameraId 重新采集。
      trtc.updateLocalVideo({ option: { cameraId: '' }});
    }

    // 麦克风恢复采集失败
    if (error.extraCode === 5309) {
      // 引导用户检查设备后，调用 updateLocalAudio 传入 microphoneId 重新采集。
      trtc.updateLocalAudio({ option: { microphoneId: '' }});
    }
  }
})
```