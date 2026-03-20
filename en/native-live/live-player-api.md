# V2TXLivePlayer 播放器 API

适用平台：Android / iOS / Flutter

## 平台差异总览

| 概念 | Android | iOS | Flutter |
|------|------|------|------|
| 实例创建 | `new V2TXLivePlayerImpl(context)` | `[[V2TXLivePlayer alloc] init]` | `await V2TXLivePlayer.create()` |
| 设置回调 | `setObserver(observer)` | `setObserver:observer` | `addListener(observer)` |
| 设置渲染视图 | `setRenderView(view)` 支持 TXCloudVideoView/TextureView/SurfaceView | `setRenderView:(TXView *)view` iOS为UIView，macOS为NSView | `setRenderViewID(int viewID)` 参数为视图ID |
| 布尔值 | `true/false` | `YES/NO` | `true/false` |
| 异步调用 | 同步返回 int | 同步返回 V2TXLiveCode | 异步 `Future<V2TXLiveCode>` 需 await |
| 枚举前缀 | `V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation0` | `V2TXLiveRotation0` | `V2TXLiveRotation.v2TXLiveRotation0` |

**iOS 缺失的 API**：`setObserver`、`setRenderView`、`setRenderRotation`、`setRenderFillMode`、`setRenderMirrorMode`、`startLivePlay`、`stopPlay`、`isPlaying`、`pauseAudio`、`resumeAudio`、`pauseVideo`、`resumeVideo`、`setPlayoutVolume`、`setCacheParams`、`switchStream`、`getStreamList`、`enableVolumeEvaluation`、`snapshot`、`enableObserveVideoFrame`、`enableObserveAudioFrame`、`enableReceiveSeiMessage`、`startLocalRecording`、`stopLocalRecording`、`showDebugView`、`setProperty`、`enablePictureInPicture`、`getTextureId`
**Flutter 缺失的 API**：`setObserver`、`setRenderView`、`setRenderRotation`、`setRenderFillMode`、`setRenderMirrorMode`、`startLivePlay`、`stopPlay`、`isPlaying`、`pauseAudio`、`resumeAudio`、`pauseVideo`、`resumeVideo`、`setPlayoutVolume`、`setCacheParams`、`switchStream`、`getStreamList`、`enableVolumeEvaluation`、`snapshot`、`enableObserveVideoFrame`、`enableObserveAudioFrame`、`enableReceiveSeiMessage`、`startLocalRecording`、`stopLocalRecording`、`showDebugView`、`setProperty`、`enablePictureInPicture`、`getTextureId`
**Android 独有 API**：`setObserver`、`setRenderView`、`setRenderRotation`、`setRenderFillMode`、`setRenderMirrorMode`、`startLivePlay`、`stopPlay`、`isPlaying`、`pauseAudio`、`resumeAudio`、`pauseVideo`、`resumeVideo`、`setPlayoutVolume`、`setCacheParams`、`switchStream`、`getStreamList`、`enableVolumeEvaluation`、`snapshot`、`enableObserveVideoFrame`、`enableObserveAudioFrame`、`enableReceiveSeiMessage`、`startLocalRecording`、`stopLocalRecording`、`showDebugView`、`setProperty`、`enablePictureInPicture`、`getTextureId`

## Basic Setup Interfaces

### setObserver

Set Player Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract void setObserver(V2TXLivePlayerObserver observer)
```

### setRenderView

Set Video Rendering View

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderView(TXCloudVideoView view)
public abstract int setRenderView(TextureView view)
public abstract int setRenderView(SurfaceView view)
```

### setRenderRotation

Set Video Rotation Angle

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderRotation(V2TXLiveRotation rotation)
```

### setRenderFillMode

Set Video Fill Mode

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderFillMode(V2TXLiveFillMode mode)
```

### setRenderMirrorMode

Set Video Mirroring Mode

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderMirrorMode(boolean enable)
```

## Playback Control Interfaces

### startLivePlay

Start Audio/Video Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int startLivePlay(String url)
```

### stopPlay

Stop Audio/Video Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int stopPlay()
```

### isPlaying

Check Playback Status

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int isPlaying()
```

## Audio/Video Control Interfaces

### pauseAudio

Pause Audio Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int pauseAudio()
```

### resumeAudio

Resume Audio Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int resumeAudio()
```

### pauseVideo

Pause Video Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int pauseVideo()
```

### resumeVideo

Resume Video Playback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int resumeVideo()
```

### setPlayoutVolume

Adjust Playback Volume

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setPlayoutVolume(int volume)
```

## Advanced Feature Interfaces

### setCacheParams

Configure Cache Time Range

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setCacheParams(float minTime, float maxTime)
```

### switchStream

Switch Stream Resolution Seamlessly (FLV, LEB)

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int switchStream(String newUrl)
```

### getStreamList

Retrieve Stream Information (HLS Supported)

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract ArrayList<V2TXLiveDef.V2TXLiveStreamInfo> getStreamList()
```

### enableVolumeEvaluation

Enable Playback Volume Level Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableVolumeEvaluation(int intervalMs)
```

### snapshot

Capture Video Frame

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int snapshot()
```

### enableObserveVideoFrame

Enable Custom Video Rendering

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableObserveVideoFrame(boolean enable, V2TXLivePixelFormat pixelFormat, V2TXLiveBufferType bufferType)
```

### enableObserveAudioFrame

Enable Audio Data Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableObserveAudioFrame(boolean enable)
```

### enableReceiveSeiMessage

Enable SEI Message Reception

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableReceiveSeiMessage(boolean enable, int payloadType)
```

### startLocalRecording

Start Local Recording

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int startLocalRecording(V2TXLiveLocalRecordingParams params)
```

### stopLocalRecording

Stop Local Recording

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract void stopLocalRecording()
```

### showDebugView

Show/Hide Debug Overlay

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract void showDebugView(boolean isShow)
```

### setProperty

Advanced API Access

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setProperty(String key, Object value)
```

### enablePictureInPicture

Enable Picture-in-Picture Mode (iOS Only)

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
Future<V2TXLiveCode> enablePictureInPicture(bool enable)
```

### getTextureId

Retrieve Texture ID

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
Future<int> getTextureId(int width, int height)
```
