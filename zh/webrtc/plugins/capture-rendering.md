> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述
本文主要介绍自定义采集和自定义播放渲染的高阶用法。

## 自定义采集

默认情况下，{@link TRTC#startLocalVideo trtc.startLocalVideo()}，{@link TRTC#startLocalAudio trtc.startLocalAudio()} 是采集并发布摄像头、麦克风，{@link TRTC#startScreenShare trtc.startScreenShare()} 是采集并发布屏幕分享。

您可以通过如下参数传入自定义采集的 MediaStreamTrack：

- `trtc.startLocalAudio({ option: { audioTrack }})`
  - 传入该参数后，SDK 不会采集麦克风，使用自定义 audioTrack 推流。
  - 若同时设置 microphoneId，audioTrack，则按优先级（microphoneId>audioTrack）进行采集，但不建议混用。
- `trtc.startLocalVideo({ option: { videoTrack }})`
  - 传入该参数后，SDK 不会采集摄像头，自定义采集的 videoTrack 将会以主流的形式推流。
  - 若同时设置 cameraId，useFrontCamera，videoTrack，则按优先级（cameraId>useFrontCamera>videoTrack）进行采集，但不建议混用。
- `trtc.startScreenShare({ option: { videoTrack }})`
  - 传入该参数后，SDK 不会采集屏幕分享，自定义采集的 videoTrack 将会以辅流的形式推流。

获取 audioTrack，videoTrack 通常有以下几种方式：

- 通过 [getUserMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) 采集摄像头和麦克风。
- 通过 [getDisplayMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia) 采集屏幕分享。
- 通过 [videoElement.captureStream](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/captureStream) 采集 video 标签中正在播放的音视频。
- 通过 [canvas.captureStream](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/captureStream) 采集 canvas 画布中的动画。

### 采集 video 标签中正在播放的视频

```javascript
// 检测您当前的浏览器是否支持从 video 元素采集
if (!HTMLVideoElement.prototype.captureStream) {
  console.log('your browser does not support capturing stream from video element');
  return
}
// 获取您页面在播放视频的 video 标签 
const video = document.getElementByID('your-video-element-ID');
// 从播放的视频采集视频流
const stream = video.captureStream();
const audioTrack = stream.getAudioTracks()[0];
const videoTrack = stream.getVideoTracks()[0];

trtc.startLocalVideo({ option:{ videoTrack } });
trtc.startLocalAudio({ option:{ audioTrack } });
```

### 采集 canvas 中的画面

```javascript
// 检测您当前的浏览器是否支持从 canvas 元素采集
if (!HTMLCanvasElement.prototype.captureStream) {
  console.log('your browser does not support capturing stream from canvas element');
  return
}
// 获取您的 canvas 标签 
const canvas = document.getElementByID('your-canvas-element-ID');

// 从 canvas 采集 15 fps 的视频流
const fps = 15;
const stream = canvas.captureStream(fps);
const videoTrack = stream.getVideoTracks()[0];

trtc.startLocalVideo({ option:{ videoTrack } });
```

## 自定义播放渲染

正常情况下，在  {@link TRTC#startLocalVideo startLocalVideo()}  {@link TRTC#startRemoteVideo startRemoteVideo()} 时，传入 view 参数，SDK 会在指定的 element 标签下，创建 `video` 标签播放视频画面。

如果您需要自定义播放渲染，不需要 SDK 播放视频，可参考如下步骤实现：

- 在 startLocalVideo 或 startRemoteVideo 方法调用时不填 view 参数或 view 参数传入 null
- 监听 TRTC.EVENT.TRACK 事件，每当有新的 MediaStreamTrack 时，SDK 会通过该事件通知。
- 在上述事件触发时，拿到最新的 MediaStreamTrack，此时您可以使用自己的播放器进行视频的播放渲染。
- 使用这种自定义播放渲染方式后，{@link module:EVENT.VIDEO_PLAY_STATE_CHANGED VIDEO_PLAY_STATE_CHANGED} 事件将不会被触发，您需要自行监听视频轨道 [MediaStreamTrack](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack) 的 `mute/unmute/ended` 等事件来判断当前视频数据流的状态。

### 自定义渲染本地视频

```javascript
trtc.on(TRTC.EVENT.TRACK, event => {
  const isLocal = event.userId === ''; // userId === '' means event.track is a local track, otherwise it's a remote track
  const isSubStream = event.streamType === TRTC.TYPE.STREAM_TYPE_SUB; // Usually the sub stream is a screen-sharing video stream.
  const mediaStreamTrack = event.track; 
  const kind = event.track.kind; // audio or video
})
```
