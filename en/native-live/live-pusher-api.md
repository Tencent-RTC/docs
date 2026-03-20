# V2TXLivePusher 推流器 API

适用平台：Android / iOS / Flutter

## 平台差异总览

| 概念 | Android | iOS | Flutter |
|------|------|------|------|
| 实例创建 | `new V2TXLivePusherImpl(context, mode)` | `initWithLiveMode:(V2TXLiveMode)liveMode` | `V2TXLivePusher.create(mode)` |
| 设置回调 | `setObserver(observer)` | `setObserver:` | `addListener(listener)` |
| 设置渲染视图 | `setRenderView(view)` | `setRenderView:(TXView *)view` | `setRenderViewID(viewID)` |

**iOS 缺失的 API**：`setObserver`、`setRenderView`、`setRenderMirror`、`setEncoderMirror`、`setRenderRotation`、`setRenderFillMode`、`startPush`、`stopPush`、`isPushing`、`setAudioQuality`、`setVideoQuality`、`getAudioEffectManager`、`getBeautyManager`、`getDeviceManager`、`snapshot`、`setWatermark`、`enableVolumeEvaluation`、`enableCustomVideoProcess`、`enableCustomVideoCapture`、`enableCustomAudioCapture`、`sendSeiMessage`、`setMixTranscodingConfig`、`showDebugView`、`setProperty`
**Flutter 缺失的 API**：`setObserver`、`setRenderView`、`setRenderMirror`、`setEncoderMirror`、`setRenderRotation`、`setRenderFillMode`、`startPush`、`stopPush`、`isPushing`、`setAudioQuality`、`setVideoQuality`、`getAudioEffectManager`、`getBeautyManager`、`getDeviceManager`、`snapshot`、`setWatermark`、`enableVolumeEvaluation`、`enableCustomVideoProcess`、`enableCustomVideoCapture`、`enableCustomAudioCapture`、`sendSeiMessage`、`setMixTranscodingConfig`、`showDebugView`、`setProperty`
**Android 独有 API**：`setObserver`、`setRenderView`、`setRenderMirror`、`setEncoderMirror`、`setRenderRotation`、`setRenderFillMode`、`startPush`、`stopPush`、`isPushing`、`setAudioQuality`、`setVideoQuality`、`getAudioEffectManager`、`getBeautyManager`、`getDeviceManager`、`snapshot`、`setWatermark`、`enableVolumeEvaluation`、`enableCustomVideoProcess`、`enableCustomVideoCapture`、`enableCustomAudioCapture`、`sendSeiMessage`、`setMixTranscodingConfig`、`showDebugView`、`setProperty`

## Basic Setup Interfaces

### setObserver

Set Pusher Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract void setObserver(V2TXLivePusherObserver observer);
```

### setRenderView

Set Local Camera Preview View

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderView(TXCloudVideoView view);
public abstract int setRenderView(TextureView view);
public abstract int setRenderView(SurfaceView view);
```

### setRenderMirror

Set Local Camera Preview Mirroring

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderMirror(V2TXLiveMirrorType mirrorType);
```

### setEncoderMirror

Set Video Encoding Mirroring

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setEncoderMirror(boolean mirror);
```

### setRenderRotation

Set Local Camera Preview Rotation

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderRotation(V2TXLiveRotation rotation);
```

### setRenderFillMode

Set Local Camera Preview Fill Mode

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setRenderFillMode(V2TXLiveFillMode mode);
```

## Streaming Control Interfaces

### startPush

Start Streaming

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int startPush(String url);
```

### stopPush

Stop Streaming

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int stopPush();
```

### isPushing

Check Streaming Status

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int isPushing();
```

## Audio/Video Control Interfaces

### setAudioQuality

Set Audio Quality

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setAudioQuality(V2TXLiveAudioQuality quality);
```

### setVideoQuality

Set Video Encoding Parameters

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setVideoQuality(V2TXLiveVideoEncoderParam param);
```

## Effects Management Interfaces

### getAudioEffectManager

Get Audio Effect Manager

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract TXAudioEffectManager getAudioEffectManager();
```

### getBeautyManager

Get Beauty Manager

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract TXBeautyManager getBeautyManager();
```

### getDeviceManager

Get Device Manager

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract TXDeviceManager getDeviceManager();
```

## Advanced Feature Interfaces

### snapshot

Capture Local Frame

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int snapshot();
```

### setWatermark

Set Watermark

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setWatermark(Bitmap image, float x, float y, float scale);
```

### enableVolumeEvaluation

Enable Capture Volume Evaluation

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableVolumeEvaluation(int intervalMs);
```

### enableCustomVideoProcess

Enable/Disable Custom Video Processing

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableCustomVideoProcess(boolean enable, V2TXLivePixelFormat pixelFormat, V2TXLiveBufferType bufferType);
```

### enableCustomVideoCapture

Enable/Disable Custom Video Capture

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableCustomVideoCapture(boolean enable);
```

### enableCustomAudioCapture

Enable/Disable Custom Audio Capture

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int enableCustomAudioCapture(boolean enable);
```

### sendSeiMessage

Send SEI Message

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int sendSeiMessage(int payloadType, byte[] data);
```

### setMixTranscodingConfig

Set Cloud Stream Mixing Parameters

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setMixTranscodingConfig(V2TXLiveTranscodingConfig config);
```

### showDebugView

Show Debug Dashboard

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract void showDebugView(boolean isShow);
```

### setProperty

Call Advanced V2TXLivePusher API

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public abstract int setProperty(String key, Object value);
```
