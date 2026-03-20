# V2TXLivePlayerObserver 播放器回调 API

适用平台：Android / iOS / Flutter

## Error and Warning Callbacks

### onError

Player Error Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onError(V2TXLivePlayer player, int code, String msg, Bundle extraInfo)
```

### onWarning

Player Warning Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onWarning(V2TXLivePlayer player, int code, String msg, Bundle extraInfo)
```

## Connection and Status Callbacks

### onConnected

Connection Success Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onConnected(V2TXLivePlayer player, Bundle extraInfo)
```

### onVideoResolutionChanged

Video Resolution Change Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onVideoResolutionChanged(V2TXLivePlayer player, int width, int height)
```

## Playback Event Callbacks

### onVideoPlaying

Video Playback Start Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onVideoPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo)
```

### onVideoLoading

Video Loading Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onVideoLoading(V2TXLivePlayer player, Bundle extraInfo)
```

### onAudioPlaying

Audio Playback Start Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onAudioPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo)
```

### onAudioLoading

Audio Loading Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onAudioLoading(V2TXLivePlayer player, Bundle extraInfo)
```

## Audio/Video Data Processing Callbacks

### onPlayoutVolumeUpdate

Volume Update Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onPlayoutVolumeUpdate(V2TXLivePlayer player, int volume)
```

### onRenderVideoFrame

Custom Video Rendering Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onRenderVideoFrame(V2TXLivePlayer player, V2TXLiveVideoFrame videoFrame)
```

### onPlayoutAudioFrame

Audio Frame Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onPlayoutAudioFrame(V2TXLivePlayer player, V2TXLiveAudioFrame audioFrame)
```

## Statistics and Monitoring Callbacks

### onStatisticsUpdate

Playback Statistics Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onStatisticsUpdate(V2TXLivePlayer player, V2TXLivePlayerStatistics statistics)
```

## Advanced Feature Callbacks

### onSnapshotComplete

Snapshot Completion Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onSnapshotComplete(V2TXLivePlayer player, Bitmap image)
```

### onReceiveSeiMessage

SEI Message Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onReceiveSeiMessage(V2TXLivePlayer player, int payloadType, byte[] data)
```

### onStreamSwitched

Stream Switch Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onStreamSwitched(V2TXLivePlayer player, String url, int code)
```

## Local Recording Callbacks

### onLocalRecordBegin

Recording Start Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecordBegin(V2TXLivePlayer player, int code, String storagePath)
```

### onLocalRecording

Recording Progress Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecording(V2TXLivePlayer player, long durationMs, String storagePath)
```

### onLocalRecordComplete

Recording Completion Callback

> **适用平台：** Android（iOS、Flutter 不支持）

**签名：**
```java
// Android
public void onLocalRecordComplete(V2TXLivePlayer player, int code, String storagePath)
```
