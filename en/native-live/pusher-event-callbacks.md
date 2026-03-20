# V2TXLivePusherObserver 推流器回调 API

适用平台：Android / iOS / Flutter

## Error and Warning Callbacks

### onError

Stream Pusher Error Notification

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onError(int code, String msg, Bundle extraInfo)
```

### onWarning

Stream Pusher Warning Notification

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onWarning(int code, String msg, Bundle extraInfo)
```

## Connection and Status Callbacks

### onPushStatusUpdate

Stream Pusher Connection Status

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onPushStatusUpdate(V2TXLivePushStatus status, String msg, Bundle extraInfo)
```

## Capture Event Callbacks

### onCaptureFirstAudioFrame

First Audio Frame Notification

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onCaptureFirstAudioFrame()
```

### onCaptureFirstVideoFrame

First Video Frame Notification

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onCaptureFirstVideoFrame()
```

### onMicrophoneVolumeUpdate

Microphone Capture Volume

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onMicrophoneVolumeUpdate(int volume)
```

## Audio/Video Data Processing Callbacks

### onProcessAudioFrame

Audio Frame Processing

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onProcessAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```

### onProcessVideoFrame

Custom Video Frame Processing

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public int onProcessVideoFrame(V2TXLiveVideoFrame srcFrame, V2TXLiveVideoFrame dstFrame)
```

## OpenGL Environment Callbacks

### onGLContextCreated

OpenGL Environment Created

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onGLContextCreated()
```

### onGLContextDestroyed

OpenGL Environment Destroyed

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onGLContextDestroyed()
```

## Statistics and Monitoring Callbacks

### onStatisticsUpdate

Streaming Statistics

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onStatisticsUpdate(V2TXLivePusherStatistics statistics)
```

## Advanced Feature Callbacks

### onSnapshotComplete

Snapshot Completion

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onSnapshotComplete(Bitmap image)
```

### onSetMixTranscodingConfig

Cloud Stream Mixing (MixTranscoding)

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onSetMixTranscodingConfig(int code, String msg)
```

### onVoiceActivityDetectionUpdate

Voice Activity Detection

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onVoiceActivityDetectionUpdate(boolean active)
```

## Screen Sharing Callbacks

### onScreenCaptureStarted

Screen Sharing Started

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onScreenCaptureStarted()
```

### onScreenCaptureStopped

Screen Sharing Stopped

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onScreenCaptureStopped(int reason)
```

## Local Recording Callbacks

### onLocalRecordBegin

Local Recording Started

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecordBegin(int code, String storagePath)
```

### onLocalRecording

Local Recording Progress

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecording(long durationMs, String storagePath)
```

### onLocalRecordComplete

Local Recording Completed

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecordComplete(int code, String storagePath)
```
