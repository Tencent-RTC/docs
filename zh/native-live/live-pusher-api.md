# 腾讯云直播 SDK V2TXLivePusher 推流器 API 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## setObserver

```java
public abstract void setObserver(V2TXLivePusherObserver observer);
```
```objc
- (void)setObserver:(id<V2TXLivePusherObserver>)observer;
```
```dart
void addListener(V2TXLivePusherObserver observer)
```

```java
pusher.setObserver(new V2TXLivePusherObserver(){
  // ...
});
```

**参数**:
- `observer`: 推流器回调目标对象，需实现 `V2TXLivePusherObserver` 协议

**使用时机**: 推流器初始化后立即设置
**说明**: 通过设置回调，可以监听推流器状态、音量回调、统计数据、警告和错误信息等

## setRenderView

```java
public abstract int setRenderView(TXCloudVideoView view);
public abstract int setRenderView(TextureView view);
public abstract int setRenderView(SurfaceView view);
```
```objc
- (V2TXLiveCode)setRenderView:(TXView *)view;
```
```dart
Future<V2TXLiveCode> setRenderViewID(int viewID)
```

```java
pusher.setRenderView(videoView);
```

**参数**:
- `view`: 本地摄像头预览View，支持三种类型

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 必须在开始推流前设置预览视图
**说明**: 本地摄像头采集到的画面，经过美颜、脸型调整、滤镜等多种效果叠加之后，最终会显示View上

## setRenderMirror

```java
public abstract int setRenderMirror(V2TXLiveMirrorType mirrorType);
```
```objc
- (V2TXLiveCode)setRenderMirror:(V2TXLiveMirrorType)mirrorType;
```
```dart
Future<V2TXLiveCode> setRenderMirror(V2TXLiveMirrorType mirrorType)
```

```java
// 前置摄像头镜像，后置摄像头不镜像
pusher.setRenderMirror(V2TXLiveDef.V2TXLiveMirrorType.V2TXLiveMirrorTypeAuto);
// 所有摄像头都镜像
pusher.setRenderMirror(V2TXLiveDef.V2TXLiveMirrorType.V2TXLiveMirrorTypeEnable);
// 所有摄像头都不镜像
pusher.setRenderMirror(V2TXLiveDef.V2TXLiveMirrorType.V2TXLiveMirrorTypeDisable);
```

**参数**:
- `mirrorType`: 摄像头镜像类型 `V2TXLiveDef#V2TXLiveMirrorType`
  - `V2TXLiveMirrorTypeAuto`【默认值】: 前置摄像头镜像，后置摄像头不镜像
  - `V2TXLiveMirrorTypeEnable`: 前置和后置摄像头都镜像
  - `V2TXLiveMirrorTypeDisable`: 前置和后置摄像头都不镜像

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setEncoderMirror

```java
public abstract int setEncoderMirror(boolean mirror);
```
```objc
- (V2TXLiveCode)setEncoderMirror:(BOOL)mirror;
```
```dart
Future<V2TXLiveCode> setEncoderMirror(bool mirror)
```

```java
// 观众端看到镜像画面
pusher.setEncoderMirror(true);
// 观众端看到非镜像画面
pusher.setEncoderMirror(false);
```

**参数**:
- `mirror`: 是否镜像【默认值】：false

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 编码镜像只影响观众端看到的视频效果
**使用时机**: 无限制

## setRenderRotation

```java
public abstract int setRenderRotation(V2TXLiveRotation rotation);
```
```objc
- (V2TXLiveCode)setRenderRotation:(V2TXLiveRotation)rotation;
```
```dart
Future<V2TXLiveCode> setRenderRotation(V2TXLiveRotation rotation)
```

```java
// 设置预览画面旋转0度
pusher.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation0);
// 设置预览画面旋转90度
pusher.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation90);
// 设置预览画面旋转180度
pusher.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation180);
// 设置预览画面旋转270度
pusher.setRenderRotation(V2TXLiveDef.V2TXLiveRotation.V2TXLiveRotation270);
```

**参数**:
- `rotation`: 预览画面的旋转角度 `V2TXLiveDef#V2TXLiveRotation`
  - `V2TXLiveRotation0`【默认值】: 0度，不旋转
  - `V2TXLiveRotation90`: 顺时针旋转90度
  - `V2TXLiveRotation180`: 顺时针旋转180度
  - `V2TXLiveRotation270`: 顺时针旋转270度

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 只旋转本地预览画面，不影响推流出去的画面
**使用时机**: 无限制

## setRenderFillMode

```java
public abstract int setRenderFillMode(V2TXLiveFillMode mode);
```
```objc
- (V2TXLiveCode)setRenderFillMode:(V2TXLiveFillMode)mode;
```
```dart
Future<V2TXLiveCode> setRenderFillMode(V2TXLiveFillMode mode)
```

```java
// 图像铺满屏幕
pusher.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeFill);
// 图像适应屏幕
pusher.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeFit);
// 图像拉伸铺满
pusher.setRenderFillMode(V2TXLiveDef.V2TXLiveFillMode.V2TXLiveFillModeScaleFill);
```

**参数**:
- `mode`: 画面填充模式 `V2TXLiveDef#V2TXLiveFillMode`
  - `V2TXLiveFillModeFill`【默认值】: 图像铺满屏幕，不留黑边，部分画面内容可能被裁剪
  - `V2TXLiveFillModeFit`: 图像适应屏幕，保持画面完整，可能有黑边
  - `V2TXLiveFillModeScaleFill`: 图像拉伸铺满，长度和宽度可能不会按比例变化

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## startCamera

```java
public abstract int startCamera(boolean frontCamera);
public abstract int stopCamera();
```
```dart
Future<V2TXLiveCode> startCamera(bool frontCamera)
```

**重要说明**: 
- **推流前必须开启至少一个采集源**，否则推流将失败
- `startVirtualCamera`, `startCamera`, `startScreenCapture`在同一Pusher实例下，仅有一个采集源可以上行
- 不同采集源之间切换，请先关闭前一采集源，再开启后一采集源
```java
// 开启前置摄像头
pusher.startCamera(true);
// 开启后置摄像头
pusher.startCamera(false);
// 停止摄像头采集
pusher.stopCamera();
```

**参数**:
- `frontCamera`: 指定摄像头方向是否为前置【默认值】：true

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 必须推流前调用

## startMicrophone

```java
public abstract int startMicrophone();
public abstract int stopMicrophone();
```
```objc
- (V2TXLiveCode)startMicrophone;
```
```dart
Future<V2TXLiveCode> startMicrophone()
```

**注意事项**:
- **推流前必须调用此方法开启麦克风采集**，否则推流将只有视频没有音频
- 纯音频推流场景下，可以单独使用麦克风采集
- 推流过程中可以动态开启/关闭麦克风采集
```java
// 开启麦克风采集
pusher.startMicrophone();
// 停止麦克风采集
pusher.stopMicrophone();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用场景**:
- **音视频直播**: 配合摄像头采集提供音频
- **纯音频直播**: 单独使用麦克风进行音频推流
- **屏幕分享**: 配合屏幕采集提供系统声音和麦克风声音
**使用时机**: 推流前必须调用

## startVirtualCamera
> **适用平台：** Android / iOS

```java
public abstract int startVirtualCamera(Bitmap image);
public abstract int stopVirtualCamera();
```
```objc
- (V2TXLiveCode)startVirtualCamera:(TXImage *)image;
```

**注意事项**:
- **推流前必须调用此方法开启图片推流**，否则推流将失败
- 图片会被编码为视频流进行推送，消耗带宽
- 通常需要配合麦克风采集提供音频
- 图片推流和摄像头采集不能同时开启
```java
// 开启图片推流
Bitmap image = BitmapFactory.decodeResource(getResources(), R.drawable.live_image);
pusher.startVirtualCamera(image);
// 停止图片推流
pusher.stopVirtualCamera();
```

**参数**:
- `image`: 推流的图片

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用场景**:
- **图片直播**: 将静态图片作为视频源进行推流
- **维护页面**: 系统维护时显示维护页面
- **广告展示**: 展示广告图片
**使用时机**: 推流前必须调用

## startScreenCapture

```java
public abstract int startScreenCapture();
public abstract int stopScreenCapture();
```
```objc
- (V2TXLiveCode)startScreenCapture:(NSString *)appGroup;
```
```dart
Future<V2TXLiveCode> startScreenCapture(String appGroup)
```

**注意事项**:
- **推流前必须调用此方法开启屏幕采集**，否则推流将失败
- Android系统需要申请屏幕录制权限
- 可以配合`startSystemAudioLoopback()`采集系统声音
- 屏幕采集和摄像头采集不能同时开启
```java
// 开启屏幕采集
pusher.startScreenCapture();
// 停止屏幕采集
pusher.stopScreenCapture();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用场景**:
- **屏幕分享**: 分享设备屏幕内容进行直播
- **远程教学**: 教师分享屏幕进行在线教学
- **游戏直播**: 分享游戏画面进行游戏直播
- **应用演示**: 演示App操作流程
**使用时机**: 推流前必须调用

## startPush

```java
public abstract int startPush(String url);
```
```objc
- (V2TXLiveCode)startPush:(NSString *)url;
```
```dart
Future<V2TXLiveCode> startPush(String url)
```

**重要注意事项**:
- **必须在调用startPush()之前开启至少一个采集源**，否则推流将失败
- 支持的采集源类型：摄像头采集、麦克风采集、屏幕采集、图片推流、自定义采集
- 推流过程中可以切换采集源，但需要先关闭当前采集源再开启新采集源
```java
int result = pusher.startPush("rtmp://example.com/live/stream");
if (result == V2TXLiveCode.V2TXLIVE_OK) {
    Log.i(TAG, "开始推流成功");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_INVALID_PARAMETER) {
    Log.w(TAG, "推流地址不合法");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_INVALID_LICENSE) {
    Log.w(TAG, "Licence不合法，请检查Licence");
} else if (result == V2TXLiveCode.V2TXLIVE_ERROR_REFUSED) {
    Log.w(TAG, "调用被拒绝");
}
```

**参数**:
- `url`: 推流的目标地址，支持任意推流服务端

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 操作成功，开始连接推流目标地址
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 操作失败，url不合法
- `V2TXLIVE_ERROR_INVALID_LICENSE`: 操作失败，license不合法，鉴权失败
- `V2TXLIVE_ERROR_REFUSED`: 操作失败，RTC不支持同一设备上同时推拉同一个StreamId

**使用时机**: 采集源开启后

## stopPush

```java
public abstract int stopPush();
```
```objc
- (V2TXLiveCode)stopPush;
```
```dart
Future<V2TXLiveCode> stopPush()
```

```java
pusher.stopPush();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 开始推流后

## isPushing

```java
public abstract int isPushing();
```
```objc
- (int)isPushing;
```
```dart
Future<V2TXLiveCode> isPushing()
```

```java
int isPushing = pusher.isPushing();
if (isPushing == 1) {
    Log.i(TAG, "正在推流中");
} else {
    Log.i(TAG, "推流停止");
}
```

**返回值**:
- `1`: 正在推流中
- `0`: 已经停止推流

**使用时机**: 无限制

## pauseAudio

```java
public abstract int pauseAudio();
public abstract int resumeAudio();
```
```objc
- (V2TXLiveCode)pauseAudio;
- (V2TXLiveCode)resumeAudio;
```
```dart
Future<V2TXLiveCode> pauseAudio()
```

```java
// 暂停音频流
pusher.pauseAudio();
// 恢复音频流
pusher.resumeAudio();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## pauseVideo

```java
public abstract int pauseVideo();
public abstract int resumeVideo();
```
```objc
- (V2TXLiveCode)pauseVideo;
- (V2TXLiveCode)resumeVideo;
```
```dart
Future<V2TXLiveCode> pauseVideo()
```

```java
// 暂停视频流
pusher.pauseVideo();
// 恢复视频流
pusher.resumeVideo();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## setAudioQuality

```java
public abstract int setAudioQuality(V2TXLiveAudioQuality quality);
```
```objc
- (V2TXLiveCode)setAudioQuality:(V2TXLiveAudioQuality)quality;
```
```dart
Future<V2TXLiveCode> setAudioQuality(V2TXLiveAudioQuality quality)
```

```java
// 设置通用音质
pusher.setAudioQuality(V2TXLiveDef.V2TXLiveAudioQuality.V2TXLiveAudioQualityDefault);
// 设置语音音质
pusher.setAudioQuality(V2TXLiveDef.V2TXLiveAudioQuality.V2TXLiveAudioQualitySpeech);
// 设置音乐音质
pusher.setAudioQuality(V2TXLiveDef.V2TXLiveAudioQuality.V2TXLiveAudioQualityMusic);
```

**参数**:
- `quality`: 音频质量 `V2TXLiveDef#V2TXLiveAudioQuality`
  - `V2TXLiveAudioQualityDefault`【默认值】: 通用音质
  - `V2TXLiveAudioQualitySpeech`: 语音音质
  - `V2TXLiveAudioQualityMusic`: 音乐音质

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_REFUSED`: 推流过程中，不允许调整音质

**使用时机**: 推流开始前

## setVideoQuality

```java
public abstract int setVideoQuality(V2TXLiveVideoEncoderParam param);
```
```objc
- (V2TXLiveCode)setVideoQuality:(V2TXLiveVideoEncoderParam *)param;
```
```dart
Future<V2TXLiveCode> setVideoQuality(V2TXLiveVideoEncoderParam param)
```

```java
V2TXLiveVideoEncoderParam param = new V2TXLiveVideoEncoderParam();
param.videoResolution = V2TXLiveVideoResolution.V2TXLiveVideoResolution1280x720;
param.videoFps = 15;
param.videoBitrate = 1200;
pusher.setVideoQuality(param);
```

**参数**:
- `param`: 视频编码参数

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 推流开始前

## getAudioEffectManager

```java
public abstract TXAudioEffectManager getAudioEffectManager();
```
```objc
- (TXAudioEffectManager *)getAudioEffectManager;
```

```java
TXAudioEffectManager audioEffectManager = pusher.getAudioEffectManager();
// 设置人声音量
audioEffectManager.setVoiceVolume(100);
// 设置背景音乐音量
audioEffectManager.setAllMusicVolume(50);
```

**返回值**: `TXAudioEffectManager` 音效管理对象

**功能说明**:
- 调整麦克风收集的人声音量
- 设置混响和变声效果
- 开启耳返，设置耳返音量
- 添加背景音乐，调整背景音乐的播放效果
**使用时机**: 无限制

## getBeautyManager
> **适用平台：** Android / iOS

```java
public abstract TXBeautyManager getBeautyManager();
```
```objc
- (TXBeautyManager *)getBeautyManager;
```

```java
TXBeautyManager beautyManager = pusher.getBeautyManager();
// 设置美颜风格
beautyManager.setBeautyStyle(TXBeautyManager.TXBeautyStyleNature);
// 设置美白级别
beautyManager.setWhitenessLevel(5);
// 设置红润级别
beautyManager.setRuddyLevel(5);
```

**返回值**: `TXBeautyManager` 美颜管理对象

**功能说明**:
- 设置"美颜风格"、美白、红润、大眼、瘦脸等美容效果
- 调整发际线、眼间距、眼角、嘴形等面部特征
- 设置人脸挂件等动态效果
- 添加美妆
- 进行手势识别
**使用时机**: 无限制

## getDeviceManager

```java
public abstract TXDeviceManager getDeviceManager();
```
```objc
- (TXDeviceManager *)getDeviceManager;
```

```java
TXDeviceManager deviceManager = pusher.getDeviceManager();
// 切换摄像头
deviceManager.switchCamera(true);
// 设置自动聚焦
deviceManager.setAutoFocusEnabled(true);
// 设置闪光灯
deviceManager.enableTorch(true);
```

**返回值**: `TXDeviceManager` 设备管理对象

**功能说明**:
- 切换前后摄像头
- 设置自动聚焦
- 设置摄像头缩放倍数
- 打开或关闭闪光灯
- 切换耳机或者扬声器
- 修改音量类型(媒体音量或者通话音量)
**使用时机**: 无限制

## snapshot

```java
public abstract int snapshot();
```
```objc
- (V2TXLiveCode)snapshot;
```
```dart
Future<V2TXLiveCode> snapshot()
```

```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onSnapshotComplete(Bitmap image) {
        // 处理截图结果
    }
});
pusher.snapshot();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_REFUSED`: 已经停止推流，不允许调用截图操作

**使用时机**: 开始推流后

## setWatermark
> **适用平台：** Android / iOS

```java
public abstract int setWatermark(Bitmap image, float x, float y, float scale);
```
```objc
- (V2TXLiveCode)setWatermark:(TXImage *)image x:(float)x y:(float)y scale:(float)scale;
```

```java
Bitmap watermark = BitmapFactory.decodeResource(getResources(), R.drawable.watermark);
pusher.setWatermark(watermark, 0.1f, 0.1f, 0.2f);
// 禁用水印
pusher.setWatermark(null, 0, 0, 0);
```

**参数**:
- `image`: 水印图片，如果为null则禁用水印
- `x`: 水印的横坐标，取值范围为0-1的浮点数
- `y`: 水印的纵坐标，取值范围为0-1的浮点数
- `scale`: 水印图片的缩放比例，取值范围为0-1的浮点数

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用时机**: 无限制

## enableVolumeEvaluation

```java
public abstract int enableVolumeEvaluation(int intervalMs);
```
```objc
- (V2TXLiveCode)enableVolumeEvaluation:(NSUInteger)intervalMs;
```
```dart
Future<V2TXLiveCode> enableVolumeEvaluation(int intervalMs)
```

```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onMicrophoneVolumeUpdate(int volume) {
        // 处理音量更新
    }
});
pusher.enableVolumeEvaluation(300);
```

**参数**:
- `intervalMs`: 回调触发间隔，单位为ms，最小间隔为100ms，如果小于等于0则关闭回调【默认值】：0

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 开启后可以在`onMicrophoneVolumeUpdate`回调中获取到SDK对音量大小值的评估
**使用时机**: 无限制

## enableCustomVideoProcess

```java
public abstract int enableCustomVideoProcess(boolean enable, V2TXLivePixelFormat pixelFormat, V2TXLiveBufferType bufferType);
```
```objc
- (V2TXLiveCode)enableCustomVideoProcess:(BOOL)enable 
                            pixelFormat:(V2TXLivePixelFormat)pixelFormat 
                            bufferType:(V2TXLiveBufferType)bufferType;
```
```dart
Future<V2TXLiveCode> enableCustomVideoProcess(bool enable, V2TXLivePixelFormat pixelFormat, V2TXLiveBufferType bufferType)
```

**支持的格式组合**:
- `V2TXLivePixelFormatTexture2D + V2TXLiveBufferTypeTexture`
- `V2TXLivePixelFormatI420 + V2TXLiveBufferTypeByteBuffer`
```java
pusher.enableCustomVideoProcess(true, 
    V2TXLiveDef.V2TXLivePixelFormat.V2TXLivePixelFormatTexture2D,
    V2TXLiveDef.V2TXLiveBufferType.V2TXLiveBufferTypeTexture);
```

**参数**:
- `enable`: true开启，false关闭【默认值: false】
- `pixelFormat`: 像素格式 `V2TXLiveDef#V2TXLivePixelFormat`
- `bufferType`: 缓冲区类型 `V2TXLiveDef#V2TXLiveBufferType`

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_NOT_SUPPORTED`: 不支持的格式

**使用时机**: 推流开始前

## enableCustomVideoCapture

```java
public abstract int enableCustomVideoCapture(boolean enable);
```
```objc
- (V2TXLiveCode)enableCustomVideoCapture:(BOOL)enable;
```
```dart
Future<V2TXLiveCode> enableCustomVideoCapture(bool enable)
```

**注意事项**:
- **推流前必须调用此方法开启自定义采集**，否则推流将失败
- 必须在`startPush`之前调用才会生效
- 自定义采集模式下，SDK只负责编码和发送，不进行实际采集
```java
// 开启自定义视频采集
pusher.enableCustomVideoCapture(true);
pusher.startPush(url);

// 发送自定义视频帧
V2TXLiveVideoFrame videoFrame = new V2TXLiveVideoFrame();
videoFrame.width = 1920;
videoFrame.height = 1080;
videoFrame.pixelFormat = V2TXLivePixelFormat.V2TXLivePixelFormatI420;
videoFrame.bufferType = V2TXLiveBufferType.V2TXLiveBufferTypeByteBuffer;
pusher.sendCustomVideoFrame(videoFrame);
```

**参数**:
- `enable`: true开启自定义采集，false关闭【默认值: false】

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用场景**:
- **外部设备采集**: 连接外部摄像头、采集卡等设备
- **视频处理**: 对视频进行特殊处理后再推流
- **多路合成**: 将多路视频源合成为一路推流
- **特殊格式**: 支持特殊视频格式的推流
**使用时机**: 推流开始前

## enableCustomAudioCapture

```java
public abstract int enableCustomAudioCapture(boolean enable);
```
```objc
- (V2TXLiveCode)enableCustomAudioCapture:(BOOL)enable;
```
```dart
Future<V2TXLiveCode> enableCustomAudioCapture(bool enable)
```

**注意事项**:
- **推流前必须调用此方法开启自定义采集**，否则推流将失败
- 必须在`startPush`之前调用才会生效
- 自定义采集模式下，SDK只负责编码和发送，不进行实际采集
```java
// 开启自定义音频采集
pusher.enableCustomAudioCapture(true);
pusher.startPush(url);

// 发送自定义音频帧
V2TXLiveAudioFrame audioFrame = new V2TXLiveAudioFrame();
audioFrame.data = audioData; // PCM音频数据
audioFrame.sampleRate = 44100; // 采样率
audioFrame.channel = 1; // 声道数
pusher.sendCustomAudioFrame(audioFrame);
```

**参数**:
- `enable`: true开启自定义采集，false关闭【默认值: false】

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**使用场景**:
- **外部音频设备**: 连接外部麦克风、音频接口等设备
- **音频处理**: 对音频进行特殊处理后再推流
- **多路音频**: 将多路音频源合成为一路推流
**使用时机**: 推流开始前

## sendSeiMessage

```java
public abstract int sendSeiMessage(int payloadType, byte[] data);
```
```objc
- (V2TXLiveCode)sendSeiMessage:(int)payloadType data:(NSData *)data;
```
```dart
Future<V2TXLiveCode> sendSeiMessage(int payloadType, Uint8List data)
```

```java
byte[] seiData = "Hello SEI".getBytes();
pusher.sendSeiMessage(242, seiData);
```

**参数**:
- `payloadType`: 数据类型，支持5、242，推荐填242
- `data`: 待发送的数据

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**: 播放端通过`onReceiveSeiMessage`回调来接收该消息
**使用时机**: 推流过程中

## startSystemAudioLoopback
> **适用平台：** Android

```java
public abstract int startSystemAudioLoopback();
public abstract int stopSystemAudioLoopback();
// 开启系统声音采集
pusher.startSystemAudioLoopback();
// 停止系统声音采集
pusher.stopSystemAudioLoopback();
```

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功

**说明**:
- 开启后可以采集整个操作系统的播放声音，并将其混入到当前麦克风采集的声音中一起发送到云端
- 只在Android API 29及以上的版本生效
- 需要先使用该接口来开启系统声音采集，当使用屏幕分享接口开启屏幕分享时才会真正生效
- 需要添加前台服务确保系统声音采集不被静默
**使用时机**: 推流开始前

## setMixTranscodingConfig

```java
public abstract int setMixTranscodingConfig(V2TXLiveTranscodingConfig config);
```
```objc
- (V2TXLiveCode)setMixTranscodingConfig:(V2TXLiveTranscodingConfig *)config;
```
```dart
Future<V2TXLiveCode> setMixTranscodingConfig(V2TXLiveTranscodingConfig? config)
```

**注意事项**:
- **仅支持RTC协议混流**：该接口只能在RTC协议下使用，不支持RTMP等其他推流协议
- 云端转码会引入1-2秒的CDN观看延时
- 及时传入null取消混流，避免不必要的计费损失
```java
V2TXLiveTranscodingConfig config = new V2TXLiveTranscodingConfig();
config.videoWidth = 1280;
config.videoHeight = 720;
config.videoBitrate = 1500;
config.videoFps = 15;
pusher.setMixTranscodingConfig(config);

// 取消混流
pusher.setMixTranscodingConfig(null);
```

**参数**:
- `config`: 混流转码配置，传入null则取消云端混流转码

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_REFUSED`: 未开启推流时，不允许设置混流转码参数

**混流原理**:
```
【画面1】=> 解码 ====> \
                        \
【画面2】=> 解码 =>  画面混合 => 编码 => 【混合后的画面】
                        /
【画面3】=> 解码 ====> /

【声音1】=> 解码 ====> \
                        \
【声音2】=> 解码 =>  声音混合 => 编码 => 【混合后的声音】
                        /
【声音3】=> 解码 ====> /
```
**使用时机**: 推流过程中

## startLocalRecording
> **适用平台：** Android / iOS

```java
public abstract int startLocalRecording(V2TXLiveLocalRecordingParams params);
public abstract void stopLocalRecording();
```
```objc
- (V2TXLiveCode)startLocalRecording:(V2TXLiveLocalRecordingParams *)params;
```

```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onLocalRecordBegin(int code, String storagePath) {
        Log.i(TAG, "开始录制");
    }

    @Override
    public void onLocalRecording(long durationMs, String storagePath) {
        Log.i(TAG, "录制中..., durationMs: " + durationMs);
    }

    @Override
    public void onLocalRecordComplete(int code, String storagePath) {
        Log.i(TAG, "录制完成");
    }
});

V2TXLiveLocalRecordingParams params = new V2TXLiveLocalRecordingParams();
params.filePath = "filepath";
params.interval = 2000;
int code = pusher.startLocalRecording(params);
if (code == V2TXLIVE_OK) {
    Log.i(TAG, "录制开启成功");
} else if (code == V2TXLIVE_ERROR_INVALID_PARAMETER) {
    Log.i(TAG, "录制参数不合法");
} else if (code == V2TXLIVE_ERROR_REFUSED) {
    Log.i(TAG, "录制调用被拒绝");
}
```

**参数**:
- `params`: 本地录制参数

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 参数不合法
- `V2TXLIVE_ERROR_REFUSED`: API被拒绝，推流尚未开始

**说明**: 推流开启后才能开始录制，非推流状态下开启录制无效
**使用时机**: 推流过程中

## showDebugView

```java
public abstract void showDebugView(boolean isShow);
```
```objc
- (void)showDebugView:(BOOL)isShow;
```
```dart
Future<V2TXLiveCode> showDebugView(bool isShow)
```

```java
// 显示调试视图
pusher.showDebugView(true);
// 隐藏调试视图
pusher.showDebugView(false);
```

**参数**:
- `isShow`: 是否显示【默认值: false】

**使用时机**: 无限制

## setProperty
> **适用平台：** Android / Flutter

```java
public abstract int setProperty(String key, Object value);
```
```dart
Future<V2TXLiveCode> setProperty(String key, Object value)
```

```java
pusher.setProperty("key", "value");
```

**参数**:
- `key`: 高级API对应的key，详情参考`V2TXLiveProperty`定义
- `value`: 调用key所对应的高级API时需要的参数

**返回值**: `V2TXLiveCode`
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 操作失败，key不允许为空

**说明**: 该接口用于调用一些高级功能，有特殊需求，请于商务或架构师联系
**使用时机**: 无限制

## initWithLiveMode
> **适用平台：** iOS

**参数**:
- `liveMode`: 推流协议类型
  - `V2TXLiveModeRTMP`: RTMP协议（默认）
  - `V2TXLiveModeRTC`: ROOM协议

**使用时机**: 应用启动时调用

## sendCustomVideoFrame
> **适用平台：** iOS / Flutter

```objc
- (V2TXLiveCode)sendCustomVideoFrame:(V2TXLiveVideoFrame *)videoFrame;
```
```dart
Future<V2TXLiveCode> sendCustomVideoFrame(V2TXLiveVideoFrame videoFrame)
```

**功能**: 在自定义采集模式下发送视频数据到SDK
**前提条件**: 必须先调用`enableCustomVideoCapture`开启自定义采集

## sendCustomAudioFrame
> **适用平台：** iOS / Flutter

```objc
- (V2TXLiveCode)sendCustomAudioFrame:(V2TXLiveAudioFrame *)audioFrame;
```
```dart
Future<V2TXLiveCode> sendCustomAudioFrame(V2TXLiveAudioFrame audioFrame)
```

**功能**: 在自定义采集模式下发送音频数据到SDK

## enableAudioProcessObserver
> **适用平台：** iOS

```objc
- (V2TXLiveCode)enableAudioProcessObserver:(BOOL)enable 
                                  format:(V2TXLiveAudioFrameObserverFormat *)format;
```

**功能**: 开启/关闭对前处理后的本地音频帧的监听

**使用时机**: 必须在`startPush`之前调用

## stopLocalRecording
> **适用平台：** iOS

```objc
- (void)stopLocalRecording;
```

**功能**: 停止录制音视频流

**自动处理**: 停止推流后，如果还在录制中，SDK会自动结束录制

## enableVoiceActivityDetection
> **适用平台：** iOS

```objc
- (void)enableVoiceActivityDetection:(BOOL)enable;
```

**功能**: 启用人声检测
**回调**: 在`OnVoiceActivityDetectionUpdate`回调中获取语音活动状态

## setProperty / getProperty
> **适用平台：** iOS

```objc
- (V2TXLiveCode)setProperty:(NSString *)key value:(NSObject *)value;
- (NSString *)getProperty:(NSString *)key;
```

**功能**: 调用高级API接口
**参考文档**: `V2TXLiveProperty.h`中的定义

**使用场景**: 调用一些实验性功能或高级配置

## create
> **适用平台：** Flutter

```dart
static Future<V2TXLivePusher> create(V2TXLiveMode liveMode) async
```

**功能**: 创建推流器实例
**使用示例**:
```dart
V2TXLivePusher pusher = await V2TXLivePusher.create(V2TXLiveMode.V2TXLiveModeRTC);
```

**参数**:
- `liveMode`: 推流模式 [V2TXLiveMode]

**返回值**:
- `Future<V2TXLivePusher>`: 推流器实例

## removeObserver
> **适用平台：** Flutter

```dart
void removeListener(V2TXLivePusherObserver observer)
```

**功能**: 移除推流器回调观察者

**参数**:
- `observer`: 推流器回调观察者对象 [V2TXLivePusherObserver]

## destroy
> **适用平台：** Flutter

```dart
void destroy()
```

**功能**: 销毁推流器实例

## stopCamera
> **适用平台：** Flutter

```dart
Future<void> stopCamera()
```

**功能**: 关闭本地摄像头

## stopMicrophone
> **适用平台：** Flutter

```dart
Future<V2TXLiveCode> stopMicrophone()
```

**功能**: 关闭麦克风

## stopScreenCapture
> **适用平台：** Flutter

```dart
Future<V2TXLiveCode> stopScreenCapture()
```

**功能**: 关闭屏幕共享

## resumeAudio
> **适用平台：** Flutter

```dart
Future<V2TXLiveCode> resumeAudio()
```

**功能**: 恢复音频流

## resumeVideo
> **适用平台：** Flutter

```dart
Future<V2TXLiveCode> resumeVideo()
```

**功能**: 恢复视频流
