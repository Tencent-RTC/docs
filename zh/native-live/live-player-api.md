# 腾讯云直播 SDK V2TXLivePlayer 播放器 API 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## setObserver

```java
public abstract void setObserver(V2TXLivePlayerObserver observer)
```
```objc
- (void)setObserver:(id<V2TXLivePlayerObserver>)observer;
```
```dart
void addListener(V2TXLivePlayerObserver observer)
```

```java
player.setObserver(new V2TXLivePlayerObserver() {
  // ...
});
```

**参数**:
- `observer`: 播放器回调目标对象，需实现 `V2TXLivePlayerObserver` 协议

**使用时机**: 播放器初始化后立即设置
**说明**: 通过设置回调，可以监听播放器状态、播放音量回调、音视频首帧回调、统计数据、警告和错误信息等

## setRenderView

```java
public abstract int setRenderView(TXCloudVideoView view)
public abstract int setRenderView(TextureView view)
public abstract int setRenderView(SurfaceView view)
```
```objc
- (V2TXLiveCode)setRenderView:(TXView *)view;
```
```dart
Future<V2TXLiveCode> setRenderViewID(int viewID)
```

```java
player.setRenderView(videoView);
```

**参数**:
- `view`: 播放器渲染 View，支持 TXCloudVideoView、TextureView、SurfaceView（HDR播放必须为 SurfaceView）

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 必须在开始播放前设置渲染视图

## setRenderRotation

```java
public abstract int setRenderRotation(V2TXLiveRotation rotation)
```
```objc
- (V2TXLiveCode)setRenderRotation:(V2TXLiveRotation)rotation;
```
```dart
Future<V2TXLiveCode> setRenderRotation(V2TXLiveRotation rotation)
```

```java
// 设置视频画面旋转0度
player.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation0);
// 设置视频画面旋转90度
player.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation90);
// 设置视频画面旋转180度
player.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation180);
// 设置视频画面旋转270度
player.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation270);
```

**参数**:
- `rotation`: 旋转角度 `V2TXLiveDef#V2TXLiveRotation`
  - `V2TXLiveRotation0`【默认值】: 0度, 不旋转
  - `V2TXLiveRotation90`: 顺时针旋转90度
  - `V2TXLiveRotation180`: 顺时针旋转180度
  - `V2TXLiveRotation270`: 顺时针旋转270度

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setRenderFillMode

```java
public abstract int setRenderFillMode(V2TXLiveFillMode mode)
```
```objc
- (V2TXLiveCode)setRenderFillMode:(V2TXLiveFillMode)mode;
```
```dart
Future<V2TXLiveCode> setRenderFillMode(V2TXLiveFillMode mode)
```

```java
// 图像铺满屏幕
player.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeFill);
// 图像适应屏幕
player.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeFit);
// 图像拉伸铺满
player.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeScaleFill);
```

**参数**:
- `mode`: 填充模式 `V2TXLiveDef#V2TXLiveFillMode`
  - `V2TXLiveFillModeFill`:【默认值】: 图像铺满屏幕，不留黑边，如果图像宽高比不同于屏幕宽高比，部分画面内容会被裁剪掉
  - `V2TXLiveFillModeFit`: 图像适应屏幕，保持画面完整，但如果图像宽高比不同于屏幕宽高比，会有黑边的存在
  - `V2TXLiveFillModeScaleFill`: 图像拉伸铺满，因此长度和宽度可能不会按比例变化

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setRenderMirrorMode
> **适用平台：** Android / iOS

```java
public abstract int setRenderMirrorMode(boolean enable)
```
```objc
- (V2TXLiveCode)setRenderMirrorMode:(BOOL)enable;
```

```java
// 开启镜像模式
player.setRenderMirrorMode(true);
// 关闭镜像模式
player.setRenderMirrorMode(false);
```

**参数**:
- `enable`: 是否开启播放端镜像模式【默认值】：false

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 开启镜像模式后，视频画面将左右翻转，您可以根据需要随时切换观看效果
**使用时机**: 无限制

## startLivePlay

```java
public abstract int startLivePlay(String url)
```
```objc
- (V2TXLiveCode)startLivePlay:(NSString *)url;
```
```dart
Future<V2TXLiveCode> startLivePlay(String url)
```

**重要说明**: 
- 10.7 版本开始，需要通过 `V2TXLivePremier#setLicence` 设置 Licence 后方可成功播放，否则将播放失败（黑屏），全局仅设置一次即可
- 直播 Licence、短视频 Licence 和视频播放 Licence 均可使用
```java
int result = player.startLivePlayer("");
if (result == V2TXLiveCode.V2TXLIVE_OK) {
    Log.i(TAG, "开始播放成功");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_INVALID_PARAMETER) {
    Log.w(TAG, "播放地址不合法");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_REFUSED) {
    Log.w(TAG, "调用被拒绝");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_INVALID_LICENSE) {
    Log.w(TAG, "Licence 不合法，请检查 Licence");
}
```

**参数**:
- `url`: 音视频流的播放地址，支持 HTTP-FLV、WebRTC、TRTC、HLS、RTMP

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 操作成功，开始连接并播放
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 操作失败，url 不合法
- `V2TXLIVE_ERROR_REFUSED`: RTC 不支持同一设备上同时推拉同一个 StreamId
- `V2TXLIVE_ERROR_INVALID_LICENSE`: licence 不合法，播放失败

**使用时机**: 无限制

## stopPlay

```java
public abstract int stopPlay()
```
```objc
- (V2TXLiveCode)stopPlay;
```
```dart
Future<V2TXLiveCode> stopPlay()
```

```java
player.stopPlay();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 开始播放后

## isPlaying

```java
public abstract int isPlaying()
```
```objc
- (int)isPlaying;
```
```dart
Future<int> isPlaying()
```

```java
int isPlaying = player.isPlaying();
if (isPlaying == 1) {
    Log.i(TAG, "正在播放中");
} else {
    Log.i(TAG, "播放停止");
}
```

**返回值**:
- `1`: 正在播放中
- `0`: 已经停止播放

**使用时机**: 无限制

## pauseAudio
> **适用平台：** Android / iOS

```java
public abstract int pauseAudio()
```
```objc
- (V2TXLiveCode)pauseAudio;
```

```java
player.pauseAudio();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制，若在开始播放前调用，则开始播放后，仅有视频画面，无音频播放

## resumeAudio
> **适用平台：** Android / iOS

```java
public abstract int resumeAudio()
```
```objc
- (V2TXLiveCode)resumeAudio;
```

```java
player.resumeAudio();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## pauseVideo
> **适用平台：** Android / iOS

```java
public abstract int pauseVideo()
```
```objc
- (V2TXLiveCode)pauseVideo;
```

```java
player.pauseVideo();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制，若在开始播放前调用，则开始播放后，仅有音频播放，无视频播放

## resumeVideo
> **适用平台：** Android / iOS

```java
public abstract int resumeVideo()
```
```objc
- (V2TXLiveCode)resumeVideo;
```

```java
player.resumeVideo();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setPlayoutVolume
> **适用平台：** Android / iOS

```java
public abstract int setPlayoutVolume(int volume)
```
```objc
- (V2TXLiveCode)setPlayoutVolume:(NSUInteger)volume;
```

```java
player.setPlayoutVolume(50);
```

**参数**:
- `volume`: 音量大小，取值范围0 - 100【默认值】: 100

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setCacheParams

```java
public abstract int setCacheParams(float minTime, float maxTime)
```
```objc
- (V2TXLiveCode)setCacheParams:(CGFloat)minTime maxTime:(CGFloat)maxTime;
```
```dart
Future<V2TXLiveCode> setCacheParams(double minTime, double maxTime)
```

**优化建议**:
- 网络稳定：设置较小缓存（1-3秒）
- 网络波动：设置较大缓存（3-5秒）
- 无特殊要求，建议使用默认值
```java
player.setCacheParams(1, 5);
```

**参数**:
- `minTime`: 缓存自动调整的最小时间，取值需要大于0【默认值】：1
- `maxTime`: 缓存自动调整的最大时间，取值需要大于0【默认值】：5

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 操作失败，minTime 和 maxTime 需要大于0

**使用时机**: 无限制

## switchStream

```java
public abstract int switchStream(String newUrl)
```
```objc
- (V2TXLiveCode)switchStream:(NSString *)newUrl;
```
```dart
Future<V2TXLiveCode> switchStream(String newUrl)
```

```java
player.startLivePlayer("");

player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onStreamSwitched(V2TXLivePlayer player, String url, int code) {
        if (code == 0) {
            Log.i(TAG, "切换成功");
        } else if (code == -1) {
            Log.i(TAG, "切换超时");
        } else if (code == -2) {
            Log.i(TAG, "切换失败，服务端错误");
        } else if (code == -3) {
            Log.i(TAG, "切换失败，客户端错误");
        }
    }
});
player.switchStream("";
```

**参数**:
- `newUrl`: 新分辨率拉流地址

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 开始播放后

## getStreamList
> **适用平台：** Android / iOS

```java
public abstract ArrayList<V2TXLiveDef.V2TXLiveStreamInfo> getStreamList()
```
```objc
- (NSArray<V2TXLiveStreamInfo *> *)getStreamList;
```

```java
player.startLivePlayer("");

ArrayList<V2TXLiveStreamInfo> streamList = player.getStreamList();
```

**返回值**: 码流信息数组，包含不同清晰度的流信息

**使用时机**: 开始播放后

## enableVolumeEvaluation

```java
public abstract int enableVolumeEvaluation(int intervalMs)
```
```objc
- (V2TXLiveCode)enableVolumeEvaluation:(NSUInteger)intervalMs;
```
```dart
Future<V2TXLiveCode> enableVolumeEvaluation(int intervalMs)
```

```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onPlayoutVolumeUpdate(V2TXLivePlayer player, int volume) {
        // 音频播放音量回调，可以更新音量大小
        Log.i(TAG, "音频播放音量，音量：" + volume);
    }
});
player.enableVolumeEvaluation(300);
```

**参数**:
- `intervalMs`: 决定了 `onPlayoutVolumeUpdate` 回调的触发间隔，单位为ms，最小间隔为100ms，如果小于等于0则会关闭回调，建议设置为300ms【默认值】：0，不开启

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 开启后可以在 `V2TXLivePlayerObserver` 回调中获取到 SDK 对音量大小值的评估
**使用时机**: 无限制

## snapshot

```java
public abstract int snapshot()
```
```objc
- (V2TXLiveCode)snapshot;
```
```dart
Future<V2TXLiveCode> snapshot()
```

```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onSnapshotComplete(V2TXLivePlayer player, Bitmap image) {
        // doSomething
    }
});
player.snapshot();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_REFUSED`: 播放器处于停止状态，不允许调用截图操作

**使用时机**: 开始播放后

## enableObserveVideoFrame

```java
public abstract int enableObserveVideoFrame(boolean enable, V2TXLivePixelFormat pixelFormat, V2TXLiveBufferType bufferType)
```
```objc
- (V2TXLiveCode)enableObserveVideoFrame:(BOOL)enable 
                          pixelFormat:(V2TXLivePixelFormat)pixelFormat 
                           bufferType:(V2TXLiveBufferType)bufferType;
```
```dart
Future<V2TXLiveCode> enableObserveVideoFrame(bool enable, int pixelFormat, int bufferType)
```

```java
// 开启自定义渲染
player.enableObserveVideoFrame(true, V2TXLiveDef.V2TXLivePixelFormat.V2TXLivePixelFormatTexture2D, V2TXLiveDef.V2TXLiveBufferType.V2TXLiveBufferTypeTexture);
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onRenderVideoFrame(V2TXLivePlayer player, V2TXLiveVideoFrame videoFrame) {
        // 自定义渲染
    }
});
```

**参数**:
- `enable`: 是否开启自定义渲染【默认值】：false
- `pixelFormat`: 自定义渲染回调的视频像素格式 `V2TXLiveDef#V2TXLivePixelFormat`
- `bufferType`: 自定义渲染回调的视频数据格式 `V2TXLiveDef#V2TXLiveBufferType`

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_NOT_SUPPORTED`: 像素格式或者数据格式不支持

**说明**: 
- SDK 在您开启此开关后将不再渲染视频画面，您可以通过 `V2TXLivePlayerObserver` 获得视频帧，并执行自定义的渲染逻辑
- 支持的类型组合：
  V2TXLiveDef.V2TXLivePixelFormat.V2TXLivePixelFormatTexture2D, V2TXLiveDef.V2TXLiveBufferType.V2TXLiveBufferTypeTexture（推荐）
  V2TXLiveDef.V2TXLivePixelFormat.V2TXLivePixelFormatI420, V2TXLiveDef.V2TXLiveBufferType.V2TXLiveBufferTypeByteArray
  V2TXLiveDef.V2TXLivePixelFormat.V2TXLivePixelFormatI420, V2TXLiveDef.V2TXLiveBufferType.V2TXLiveBufferTypeByteBuffer
**使用时机**: 开始播放前

## enableObserveAudioFrame

```java
public abstract int enableObserveAudioFrame(boolean enable)
```
```objc
- (V2TXLiveCode)enableObserveAudioFrame:(BOOL)enable;
```
```dart
Future<V2TXLiveCode> enableObserveAudioFrame(bool enable)
```

```java
// 开启音频数据监听
player.enableObserveAudioFrame(true);
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onPlayoutAudioFrame(V2TXLivePlayer player, V2TXLiveAudioFrame audioFrame) {
        // doSomething
    }
});
```

**参数**:
- `enable`: 是否开启音频数据回调【默认值】：false

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 如果您开启此开关，您可以通过 `V2TXLivePlayerObserver` 获得音频数据，并执行自定义的逻辑
**使用时机**: 开始播放前

## enableReceiveSeiMessage

```java
public abstract int enableReceiveSeiMessage(boolean enable, int payloadType)
```
```objc
- (V2TXLiveCode)enableReceiveSeiMessage:(BOOL)enable payloadType:(int)payloadType;
```
```dart
Future<V2TXLiveCode> enableReceiveSeiMessage(bool enable, int payloadType)
```

```java
// 开启音频数据监听
player.enableReceiveSeiMessage(true, 5);
player.enableReceiveSeiMessage(true, 242);
player.enableReceiveSeiMessage(true, 243);
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onReceiveSeiMessage(V2TXLivePlayer player, int payloadType, byte[] data) {
        // doSomething
    }
});
```

**参数**:
- `enable`: true: 开启接收 SEI 消息; false: 关闭接收 SEI 消息【默认值】: false
- `payloadType`: 指定接收 SEI 消息的 payloadType，支持 5、242、243，请与发送端的 payloadType 保持一致

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 开始播放前

## startLocalRecording

```java
public abstract int startLocalRecording(V2TXLiveLocalRecordingParams params)
```
```objc
- (V2TXLiveCode)startLocalRecording:(V2TXLiveLocalRecordingParams *)params;
```
```dart
Future<V2TXLiveCode> startLocalRecording(V2TXLiveLocalRecordingParams params)
```

**重要说明**:
- 拉流开启后才能开始录制，非拉流状态下开启录制无效
- 录制过程中不要动态切换软/硬解，生成的视频极有可能出现异常
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onLocalRecordBegin(V2TXLivePlayer player, int code, String storagePath) {
        Log.i(TAG, "开始录制");
    }

    @Override
    public void onLocalRecording(V2TXLivePlayer player, long durationMs, String storagePath) {
        Log.i(TAG, "录制中..., durationMs: " + durationMs);
    }

    @Override
    public void onLocalRecordComplete(V2TXLivePlayer player, int code, String storagePath) {
        Log.i(TAG, "录制完成");
    }
});

V2TXLiveDef.V2TXLiveLocalRecordingParams params = new V2TXLiveDef.V2TXLiveLocalRecordingParams();
params.filePath = "filepath";
params.interval = 2000;
int code = player.startLocalRecording(params);
if (code == V2TXLIVE_OK) {
    Log.i(TAG, "录制开启成功");
} else if (code == V2TXLIVE_ERROR_INVALID_PARAMETER) {
    Log.i(TAG, "录制参数不合法");
} else if (code == V2TXLIVE_ERROR_REFUSED) {
    Log.i(TAG, "录制调用被拒绝");
}
```

**参数**:
- `params`: 请参考 `V2TXLiveDef.java` 中关于 `V2TXLiveLocalRecordingParams` 的介绍

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 参数不合法，例如 filePath 为空
- `V2TXLIVE_ERROR_REFUSED`: API被拒绝，拉流尚未开始

**使用时机**: 开始播放后

## stopLocalRecording

```java
public abstract void stopLocalRecording()
```
```objc
- (void)stopLocalRecording;
```
```dart
Future<void> stopLocalRecording()
```

```java
player.stopLocalRecording();
```

**说明**: 当停止拉流后，如果视频还在录制中，SDK 内部会自动结束录制
**使用时机**: 开始播放并且开启录制后

## showDebugView

```java
public abstract void showDebugView(boolean isShow)
```
```objc
- (void)showDebugView:(BOOL)isShow;
```
```dart
Future<void> showDebugView(bool isShow)
```

```java
player.showDebugView(true);
```

**参数**:
- `isShow`: 是否显示【默认值】：false

**使用时机**: 无限制

## setProperty

```java
public abstract int setProperty(String key, Object value)
```
```objc
- (V2TXLiveCode)setProperty:(NSString *)key value:(NSObject *)value;
```
```dart
Future<V2TXLiveCode> setProperty(String key, Object value)
```

调用 V2TXLivePlayer 的高级 API 接口

**参数**:
- `key`: 高级 API 对应的 key
- `value`: 调用 key 所对应的高级 API 时，需要的参数

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 操作失败，key 不允许为 null

**说明**: 该接口用于调用一些高级功能，有特殊需求，请于商务或架构师联系

## enablePictureInPicture
> **适用平台：** Android / iOS

```objc
- (V2TXLiveCode)enablePictureInPicture:(BOOL)enable;
```

**参数**: 
- `enable`: 是否启用画中画模式，默认: false

## getTextureId
> **适用平台：** Android

**参数**: 
- `width`: 纹理宽度
- `height`: 纹理高度
