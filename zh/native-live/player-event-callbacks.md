# 腾讯云直播 SDK V2TXLivePlayerObserver 播放回调 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## onError

```java
public void onError(V2TXLivePlayer player, int code, String msg, Bundle extraInfo)
```
```objc
- (void)onError:(id<V2TXLivePlayer>)player 
          code:(V2TXLiveCode)code 
       message:(NSString *)msg 
     extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onError(V2TXLivePlayer player, int code, String msg, Map<String, dynamic> extraInfo)
```

**功能**: 播放器出现错误时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onError(V2TXLivePlayer player, int code, String msg, Bundle extraInfo) {
        switch (code) {
            case V2TXLIVE_ERROR_INVALID_PARAMETER:
                Log.e(TAG, "rtc拉流参数不合法，" + msg);
                break;
            case V2TXLIVE_ERROR_REQUEST_TIMEOUT:
                Log.e(TAG, "rtc拉流超时，" + msg);
                break;
            case V2TXLIVE_ERROR_SERVER_PROCESS_FAILED:
                Log.e(TAG, "rtc拉流服务器处理失败，请检查拉流参数，" + msg);
                break;
            case V2TXLIVE_ERROR_DISCONNECTED:
                Log.e(TAG, "拉流断开，" + msg);
                break;
            case V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS:
                Log.e(TAG, "找不到hevc解码器，可以切换到avc流进行播放，" + msg);
                break;
            default:
                Log.e(TAG, "其它错误，" + msg);
                break;
        }
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `code`: 错误码，参考 `V2TXLiveCode` 枚举
- `msg`: 错误描述信息
- `extraInfo`: 扩展信息，包含详细的错误上下文

**使用场景**:
- rtc拉流参数不合法
- rtc拉流超时
- rtc拉流服务器处理失败
- 找不到hevc解码器

## onWarning

```java
public void onWarning(V2TXLivePlayer player, int code, String msg, Bundle extraInfo)
```
```objc
- (void)onWarning:(id<V2TXLivePlayer>)player 
            code:(V2TXLiveCode)code 
         message:(NSString *)msg 
       extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onWarning(V2TXLivePlayer player, int code, String msg, Map<String, dynamic> extraInfo)
```

**功能**: 播放器出现警告时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onWarning(V2TXLivePlayer player, int code, String msg, Bundle extraInfo) {
        switch (code) {
            case V2TXLIVE_WARNING_VIDEO_BLOCK:
                Log.w(TAG, "视频渲染发生卡顿");
                break;
            case V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED:
                int codec_type = extraInfo.getInt("codec_type");
                if (codec_type == 0) {
                    Log.w(TAG, "解码器类型变更，h.264，" + msg);
                } else if (codec_type == 1) {
                    Log.w(TAG, "解码器类型变更，h.265，" + msg);
                }
                break;
            default:
                Log.w(TAG, "其它警告，" + msg);
                break;
        }
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `code`: 警告码，参考 `V2TXLiveCode` 枚举
- `msg`: 警告描述信息
- `extraInfo`: 扩展信息

**使用场景**:
- 视频渲染发生卡顿
- 解码器类型变更

## onConnected

```java
public void onConnected(V2TXLivePlayer player, Bundle extraInfo)
```
```objc
- (void)onConnected:(id<V2TXLivePlayer>)player 
         extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onConnected(V2TXLivePlayer player, Map<String, dynamic> extraInfo)
```

**功能**: 播放器成功连接到服务器时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onConnected(V2TXLivePlayer player, Bundle extraInfo) {
        Log.i(TAG, "服务器连接成功");
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `extraInfo`: 连接相关信息

**使用场景**:
- 监控播放器网络连接状态

## onVideoResolutionChanged

```java
public void onVideoResolutionChanged(V2TXLivePlayer player, int width, int height)
```
```objc
- (void)onVideoResolutionChanged:(id<V2TXLivePlayer>)player 
                           width:(NSInteger)width 
                          height:(NSInteger)height;
```
```dart
void onVideoResolutionChanged(V2TXLivePlayer player, int width, int height)
```

**功能**: 视频分辨率发生变化时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onVideoResolutionChanged(V2TXLivePlayer player, int width, int height) {
        Log.i(TAG, "视频流分辨率发生变化: width-" + width + ", height-" + height);
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `width`: 新的视频宽度
- `height`: 新的视频高度

**使用场景**:
- 自适应视频渲染视图
- 动态调整播放器布局

## onVideoPlaying

```java
public void onVideoPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo)
```
```objc
- (void)onVideoPlaying:(id<V2TXLivePlayer>)player 
            firstPlay:(BOOL)firstPlay 
            extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onVideoPlaying(V2TXLivePlayer player, bool firstPlay, Map<String, dynamic> extraInfo)
```

**功能**: 视频开始播放时回调，除首次播放外，与onVideoLoading配对使用
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onVideoPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo) {
        if (firstPlay) {
            // 视频首次开始播放
            Log.i(TAG, "首次开始播放");
        } else {
            // 视频非首次开始播放，可以隐藏加载 UI
            Log.i(TAG, "开始播放");
        }
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `firstPlay`: 是否为首次播放标志
- `extraInfo`: 播放相关信息

**使用场景**:
- 显示播放状态UI
- 开始播放计时
- 记录播放日志
- 计算秒开耗时

## onVideoLoading

```java
public void onVideoLoading(V2TXLivePlayer player, Bundle extraInfo)
```
```objc
- (void)onVideoLoading:(id<V2TXLivePlayer>)player 
             extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onVideoLoading(V2TXLivePlayer player, Map<String, dynamic> extraInfo)
```

**功能**: 视频数据加载时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onVideoLoading(V2TXLivePlayer player, Bundle extraInfo) {
        // 视频播放开始加载，可以显示加载 UI
        Log.i(TAG, "视频播放开始加载");
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `extraInfo`: 加载状态信息

**使用场景**:
- 显示加载动画
- 缓冲状态提示

## onAudioPlaying

```java
public void onAudioPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo)
```
```objc
- (void)onAudioPlaying:(id<V2TXLivePlayer>)player 
            firstPlay:(BOOL)firstPlay 
            extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onAudioPlaying(V2TXLivePlayer player, bool firstPlay, Map<String, dynamic> extraInfo)
```

**功能**: 音频开始播放时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onAudioPlaying(V2TXLivePlayer player, boolean firstPlay, Bundle extraInfo) {
        if (firstPlay) {
            Log.i(TAG, "音频首次开始播放");
        } else {
            Log.i(TAG, "音频开始播放");
        }
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `firstPlay`: 是否为首次播放标志
- `extraInfo`: 播放相关信息

**使用场景**:
- 音频播放状态监控
- 纯音频流播放场景

## onAudioLoading

```java
public void onAudioLoading(V2TXLivePlayer player, Bundle extraInfo)
```
```objc
- (void)onAudioLoading:(id<V2TXLivePlayer>)player 
             extraInfo:(NSDictionary *)extraInfo;
```
```dart
void onAudioLoading(V2TXLivePlayer player, Map<String, dynamic> extraInfo)
```

**功能**: 音频数据加载时回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onAudioLoading(V2TXLivePlayer player, Bundle extraInfo) {
        // 音频播放开始加载
        Log.i(TAG, "音频播放开始加载");
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `extraInfo`: 加载状态信息

**使用场景**:
- 显示加载动画
- 缓冲状态提示

## onPlayoutVolumeUpdate

```java
public void onPlayoutVolumeUpdate(V2TXLivePlayer player, int volume)
```
```objc
- (void)onPlayoutVolumeUpdate:(id<V2TXLivePlayer>)player 
                      volume:(NSInteger)volume;
```
```dart
void onPlayoutVolumeUpdate(V2TXLivePlayer player, int volume)
```

**功能**: 播放音量大小实时回调
**前提条件**: 需要调用 `enableVolumeEvaluation()` 开启音量评估
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
- `player`: 触发回调的播放器实例
- `volume`: 当前播放音量值（0-100）

**使用场景**:
- 实时音量显示
- 音频波形动画
- 静音检测

## onRenderVideoFrame

```java
public void onRenderVideoFrame(V2TXLivePlayer player, V2TXLiveVideoFrame videoFrame)
```
```objc
- (void)onRenderVideoFrame:(id<V2TXLivePlayer>)player 
                    frame:(V2TXLiveVideoFrame *)videoFrame;
```
```dart
void onRenderVideoFrame(V2TXLivePlayer player, Map<String, dynamic> videoFrame)
```

**功能**: 自定义视频帧渲染回调
**前提条件**: 需要调用 `enableObserveVideoFrame()` 开启视频帧回调
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
- `player`: 触发回调的播放器实例
- `videoFrame`: 视频帧数据 `V2TXLiveVideoFrame`

**使用场景**:
- 自定义视频渲染

## onPlayoutAudioFrame

```java
public void onPlayoutAudioFrame(V2TXLivePlayer player, V2TXLiveAudioFrame audioFrame)
```
```objc
- (void)onPlayoutAudioFrame:(id<V2TXLivePlayer>)player 
                    frame:(V2TXLiveAudioFrame *)audioFrame;
```
```dart
void onPlayoutAudioFrame(V2TXLivePlayer player, Map<String, dynamic> audioFrame)
```

**功能**: 音频数据播放回调
**前提条件**: 需要调用 `enableObserveAudioFrame()` 开启音频帧回调
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
- `player`: 触发回调的播放器实例
- `audioFrame`: 音频帧数据 `V2TXLiveAudioFrame`

**使用场景**:
- 自定义音频处理

## onStatisticsUpdate

```java
public void onStatisticsUpdate(V2TXLivePlayer player, V2TXLivePlayerStatistics statistics)
```
```objc
- (void)onStatisticsUpdate:(id<V2TXLivePlayer>)player 
               statistics:(V2TXLivePlayerStatistics *)statistics;
```
```dart
void onStatisticsUpdate(V2TXLivePlayer player, Map<String, dynamic> statistics)
```

**功能**: 播放器统计数据实时回调，2s回调一次
**包含数据**:
- 视频宽度/高度
- 帧率/码率
- 网络状况
- 缓存大小
- 播放延迟
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onStatisticsUpdate(V2TXLivePlayer player, V2TXLivePlayerStatistics statistics) {
        // doSomething
    }
});
```

**参数**:
- `player`: 触发回调的播放器实例
- `statistics`: 播放统计信息 `V2TXLivePlayerStatistics`

**使用场景**:
- 播放质量监控
- 性能优化分析
- 用户体验统计

## onSnapshotComplete

```java
public void onSnapshotComplete(V2TXLivePlayer player, Bitmap image)
```
```objc
- (void)onSnapshotComplete:(id<V2TXLivePlayer>)player 
                   image:(nullable TXImage *)image;
```
```dart
void onSnapshotComplete(V2TXLivePlayer player, Uint8List image)
```

**功能**: 截图操作完成回调
```java
player.setObserver(new V2TXLivePlayerObserver() {
    @Override
    public void onSnapshotComplete(V2TXLivePlayer player, Bitmap image) {
        // doSomething
    }
});
player.snapshot();
```

**参数**:
- `player`: 触发回调的播放器实例
- `image`: 截取的视频画面Bitmap

**使用场景**:
- 视频截图保存
- 画面分享功能

## onReceiveSeiMessage

```java
public void onReceiveSeiMessage(V2TXLivePlayer player, int payloadType, byte[] data)
```
```objc
- (void)onReceiveSeiMessage:(id<V2TXLivePlayer>)player 
               payloadType:(int)payloadType 
                      data:(NSData *)data;
```
```dart
void onReceiveSeiMessage(V2TXLivePlayer player, int payloadType, Uint8List data)
```

**功能**: 接收SEI（Supplemental Enhancement Information）消息回调
**前提条件**: 需要调用 `enableReceiveSeiMessage()` 开启SEI消息接收
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
- `player`: 触发回调的播放器实例
- `payloadType`: SEI消息类型
- `data`: SEI消息数据

**使用场景**:
- 场景信息传递
- 互动消息传输

## onStreamSwitched

```java
public void onStreamSwitched(V2TXLivePlayer player, String url, int code)
```
```objc
- (void)onStreamSwitched:(id<V2TXLivePlayer>)player 
                    url:(NSString *)url 
                   code:(NSInteger)code;
```
```dart
void onStreamSwitched(V2TXLivePlayer player, String url, int code)
```

**功能**: 分辨率切换操作完成回调，调用 `switchStream()` 时回调
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
- `player`: 触发回调的播放器实例
- `url`: 切换后的播放地址
- `code`: 切换状态码（0:成功，-1:超时，-2:服务端错误，-3:客户端错误）

**使用场景**:
- 清晰度切换监控

## onLocalRecordBegin

```java
public void onLocalRecordBegin(V2TXLivePlayer player, int code, String storagePath)
```
```objc
- (void)onLocalRecordBegin:(id<V2TXLivePlayer>)player 
                  errCode:(NSInteger)errCode 
              storagePath:(NSString *)storagePath;
```
```dart
void onLocalRecordBegin(V2TXLivePlayer player, int code, String storagePath)
```

**功能**: 本地录制任务开始回调，调用 `startLocalRecording()` 时回调
**状态码说明**:
- `0`: 录制任务启动成功
- `-1`: 内部错误导致录制失败
- `-2`: 文件后缀名有误
- `-6`: 录制已经启动
- `-7`: 录制文件已存在
- `-8`: 录制目录无写入权限

**参数**:
- `player`: 触发回调的播放器实例
- `code`: 录制状态码
- `storagePath`: 录制文件存储路径

## onLocalRecording

```java
public void onLocalRecording(V2TXLivePlayer player, long durationMs, String storagePath)
```
```objc
- (void)onLocalRecording:(id<V2TXLivePlayer>)player 
             durationMs:(NSInteger)durationMs 
            storagePath:(NSString *)storagePath;
```
```dart
void onLocalRecording(V2TXLivePlayer player, int durationMs, String storagePath)
```

**功能**: 录制任务进行中进度回调，频率与 startLocalRecording 传入的 V2TXLiveDef.V2TXLiveLocalRecordingParams.interval 一致

**参数**:
- `player`: 触发回调的播放器实例
- `durationMs`: 已录制时长（毫秒）
- `storagePath`: 录制文件存储路径

**使用场景**:
- 录制进度显示
- 录制时长限制
- 录制状态监控

## onLocalRecordComplete

```java
public void onLocalRecordComplete(V2TXLivePlayer player, int code, String storagePath)
```
```objc
- (void)onLocalRecordComplete:(id<V2TXLivePlayer>)player 
                     errCode:(NSInteger)errCode 
                 storagePath:(NSString *)storagePath;
```
```dart
void onLocalRecordComplete(V2TXLivePlayer player, int code, String storagePath)
```

**功能**: 录制任务完成回调，调用 `stopLocalRecording()` 时回调
**状态码说明**:
- `0`: 结束录制任务成功
- `-1`: 录制失败
- `-2`: 切换分辨率或横竖屏导致录制结束
- `-3`: 录制时间太短或未采集到数据
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
- `player`: 触发回调的播放器实例
- `code`: 录制完成状态码
- `storagePath`: 录制文件存储路径

## onPictureInPictureStateUpdate
> **适用平台：** iOS

```objc
- (void)onPictureInPictureStateUpdate:(id<V2TXLivePlayer>)player 
                               state:(V2TXLivePictureInPictureState)state 
                             message:(NSString *)msg 
                           extraInfo:(NSDictionary *)extraInfo;
```

**功能**: 画中画状态变化时回调
**支持状态**:
- `V2TXLivePictureInPictureStateWillStart`: 将要开始
- `V2TXLivePictureInPictureStateDidStart`: 已经开始
- `V2TXLivePictureInPictureStateWillStop`: 将要停止
- `V2TXLivePictureInPictureStateDidStop`: 已经停止
- `V2TXLivePictureInPictureStateRestoreUI`: 恢复用户界面
**启用条件**: 需要调用 `enablePictureInPicture` 开启画中画

**参数说明**:
- `player`: 触发回调的播放器对象
- `state`: 画中画状态，参考 `V2TXLivePictureInPictureState`
- `msg`: 状态描述信息
- `extraInfo`: 扩展信息

## V2TXLivePlayerListenerType
> **适用平台：** Flutter

播放器监听器类型枚举，定义了所有可用的回调事件类型：
| 枚举值 | 描述 | 参数说明 |
|--------|------|----------|
| `onError` | **错误回调** - SDK 不可恢复的错误通知 | `code`: 错误码（V2TXLiveCode）<br>`msg`: 错误消息<br>`extraInfo`: 扩展信息 |
| `onWarning` | **警告回调** - 非关键问题通知（如卡顿、可恢复的解码失败） | `code`: 警告码（V2TXLiveCode）<br>`msg`: 警告消息<br>`extraInfo`: 扩展信息 |
| `onVideoResolutionChanged` | **视频分辨率变化通知** | `width`: 视频宽度<br>`height`: 视频高度 |
| `onConnected` | **连接成功通知** | `extraInfo`: 扩展信息 |
| `onVideoPlaying` | **视频播放事件** | `firstPlay`: 首次播放标志<br>`extraInfo`: 扩展信息 |
| `onAudioPlaying` | **音频播放事件** | `firstPlay`: 首次播放标志<br>`extraInfo`: 扩展信息 |
| `onVideoLoading` | **视频加载事件** | `extraInfo`: 扩展信息 |
| `onAudioLoading` | **音频加载事件** | `extraInfo`: 扩展信息 |
| `onPlayoutVolumeUpdate` | **播放音量更新** | `volume`: 音量值（0-100） |
| `onStatisticsUpdate` | **播放器统计信息回调** | `appCpu`: 应用 CPU 使用率（%）<br>`systemCpu`: 系统 CPU 使用率（%）<br>`width`: 视频宽度<br>`height`: 视频高度<br>`fps`: 帧率<br>`videoBitrate`: 视频码率（Kbps）<br>`audioBitrate`: 音频码率（Kbps） |
| `onSnapshotComplete` | **截图完成回调** | `image`: 截取的视频画面（Uint8List） |
| `onRenderVideoFrame` | **自定义视频渲染回调** | `videoFrame`: 视频帧数据（Map） |
| `onReceiveSeiMessage` | **SEI 消息接收回调** | `payloadType`: 消息类型<br>`data`: 消息内容（Uint8List） |
| `onPictureInPictureStateUpdate` | **画中画状态变化回调** | `state`: 状态码<br>`message`: 状态消息<br>`extraInfo`: 扩展信息 |
| `onStreamSwitched` | **流切换回调** | 参数未详细说明 |
