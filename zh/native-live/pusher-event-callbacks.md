# 腾讯云直播 SDK V2TXLivePusherObserver 推流回调 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## onError

```java
public void onError(int code, String msg, Bundle extraInfo)
```
```objc
- (void)onError:(V2TXLiveCode)code message:(NSString *)msg extraInfo:(NSDictionary *)extraInfo;
```

**功能**: 推流器出现错误时回调
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onError(int code, String msg, Bundle extraInfo) {
        switch (code) {
            case V2TXLIVE_ERROR_REQUEST_TIMEOUT:
                Log.e(TAG, "推流连接超时");
                break;
            case V2TXLIVE_ERROR_SERVER_PROCESS_FAILED:
                Log.e(TAG, "推流服务器处理失败");
                break;
            default:
                Log.e(TAG, "其它错误: " + msg);
                break;
        }
    }
});
```

**参数**:
- `code`: 错误码，参考 `V2TXLiveCode` 枚举
- `msg`: 错误描述信息
- `extraInfo`: 扩展信息，包含详细的错误上下文

**使用场景**:
- 网络连接失败
- 推流地址无效
- 编码器错误
- 推流权限问题

## onWarning

```java
public void onWarning(int code, String msg, Bundle extraInfo)
```
```objc
- (void)onWarning:(V2TXLiveCode)code message:(NSString *)msg extraInfo:(NSDictionary *)extraInfo;
```

**功能**: 推流器出现警告时回调
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onWarning(int code, String msg, Bundle extraInfo) {
        switch (code) {
            case V2TXLiveCode.V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED:
                int codec_type = extraInfo.getInt("codec_type");
                if (codec_type == 0) {
                    Log.w(TAG, "编码器类型变更，h.264, " + msg);
                } else if (codec_type == 1) {
                    Log.w(TAG, "编码器类型变更，h.265, " + msg);
                }
                break;
            case V2TXLiveCode.V2TXLIVE_WARNING_NETWORK_BUSY:
                Log.w(TAG, "网络繁忙，" + msg);
                break;
            case V2TXLiveCode.V2TXLIVE_WARNING_CAMERA_START_FAILED:
                Log.w(TAG, "摄像头启动失败，" + msg);
                break;
            case V2TXLiveCode.V2TXLIVE_WARNING_CAMERA_NO_PERMISSION:
                Log.w(TAG, "摄像头设备未授权，" + msg);
                break;
            case V2TXLiveCode.V2TXLIVE_WARNING_CAMERA_OCCUPIED:
                Log.w(TAG, "摄像头设备被占用，" + msg);
                break;
            case V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED:
                Log.w(TAG, "屏幕采集启动失败，" + msg);
                break;
            case V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED:
                Log.w(TAG, "屏幕采集不支持，" + msg);
                break;
            case V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED:
                Log.w(TAG, "屏幕采集中断，" + msg);
                break;
            case V2TXLIVE_WARNING_MICROPHONE_START_FAILED:
                Log.w(TAG, "麦克风启动失败，" + msg);
                break;
            case V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION:
                Log.w(TAG, "麦克风未授权，" + msg);
                break;
            case V2TXLIVE_WARNING_MICROPHONE_OCCUPIED:
                Log.w(TAG, "麦克风被占用，" + msg);
                break;
            default:
                Log.w(TAG, "其他警告，" + msg);
                break;
        }
    }
});
```

**参数**:
- `code`: 警告码，参考 `V2TXLiveCode` 枚举
- `msg`: 警告描述信息
- `extraInfo`: 扩展信息

**使用场景**:
- 网络质量下降
- 编码延迟
- 缓冲区不足

## onPushStatusUpdate

```java
public void onPushStatusUpdate(V2TXLivePushStatus status, String msg, Bundle extraInfo)
```
```objc
- (void)onPushStatusUpdate:(V2TXLivePushStatus)status message:(NSString *)msg extraInfo:(NSDictionary *)extraInfo;
```

**功能**: 推流器连接状态回调通知
**状态枚举**:
- `V2TXLivePushStatus.V2TXLivePushStatusDisconnected`: 断开连接
- `V2TXLivePushStatus.V2TXLivePushStatusConnecting`: 连接中
- `V2TXLivePushStatus.V2TXLivePushStatusConnectSuccess`: 连接成功
- `V2TXLivePushStatus.V2TXLivePushStatusReconnecting`: 重连中
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onPushStatusUpdate(V2TXLivePushStatus status, String msg, Bundle extraInfo) {
        switch (status) {
            case V2TXLivePushStatusConnectSuccess:
                Log.i(TAG, "推流连接成功");
                break;
            case V2TXLivePushStatusConnecting:
                Log.i(TAG, "推流连接中...");
                break;
            case V2TXLivePushStatusReconnecting:
                Log.w(TAG, "推流重连中...");
                break;
            case V2TXLivePushStatusDisconnected:
                Log.e(TAG, "推流连接断开");
                break;
        }
    }
});
```

**参数**:
- `status`: 推流器连接状态 `V2TXLivePushStatus`
- `msg`: 连接状态信息
- `extraInfo`: 扩展信息

**使用场景**:
- 推流状态实时监控
- 连接失败重试机制
- 用户界面状态更新

## onCaptureFirstAudioFrame

```java
public void onCaptureFirstAudioFrame()
```
```objc
- (void)onCaptureFirstAudioFrame;
- (void)onCaptureFirstAudioFrame {
    NSLog(@"音频首帧采集完成");
    [self updateUIWithAudioStatus:YES];
}
```

**功能**: 首帧音频采集完成的回调通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onCaptureFirstAudioFrame() {
        Log.i(TAG, "音频采集开始，首帧音频已采集");
        // 更新UI状态，显示音频采集中
    }
});
```

**使用场景**:
- 确认音频采集已开始
- 音频相关UI状态更新
- 音频处理初始化

## onCaptureFirstVideoFrame

```java
public void onCaptureFirstVideoFrame()
```
```objc
- (void)onCaptureFirstVideoFrame;
- (void)onCaptureFirstVideoFrame {
    NSLog(@"视频首帧采集完成");
    [self updateUIWithVideoStatus:YES];
    
    // 可以开始显示预览画面
    [self showPreviewAnimation];
}
```

**功能**: 首帧视频采集完成的回调通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onCaptureFirstVideoFrame() {
        Log.i(TAG, "视频采集开始，首帧视频已采集");
        // 更新UI状态，显示视频采集中
    }
});
```

**使用场景**:
- 确认视频采集已开始
- 视频相关UI状态更新
- 视频处理初始化

## onMicrophoneVolumeUpdate

```java
public void onMicrophoneVolumeUpdate(int volume)
```
```objc
- (void)onMicrophoneVolumeUpdate:(NSInteger)volume;
```

**功能**: 麦克风采集音量值回调
**前提条件**: 需要调用 `enableVolumeEvaluation()` 开启采集音量大小提示
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onMicrophoneVolumeUpdate(int volume) {
        // 实时更新音量显示
        
        // 静音检测
        if (volume < 5) {
            Log.d(TAG, "检测到静音状态");
        }
    }
});
// 开启音量评估
pusher.enableVolumeEvaluation(300);
```

**参数**:
- `volume`: 音量大小（0-100）

**使用场景**:
- 实时音量显示
- 音频波形动画
- 静音检测

## onProcessAudioFrame
> **适用平台：** Android / iOS

```java
public void onProcessAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objc
- (void)onProcessAudioFrame:(V2TXLiveAudioFrame *)frame;
```

**功能**: 本地采集并经过音频模块前处理、音效处理和混BGM后的音频数据回调
**技术规格**:
- 音频时间帧长固定为0.02s
- 格式为PCM格式
- 字节帧长计算公式：`采样率 × 时间帧长 × 声道数 × 采样点位宽`
- 默认格式（48000采样率、单声道、16采样点位宽）：1920字节
**重要注意事项**:
1. 不要在此回调函数中做任何耗时操作
2. SDK每隔20ms处理一帧音频数据，处理时间超过20ms会导致声音异常
3. 音频数据可读写，可同步修改但需保证处理耗时
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onProcessAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame) {
        // 自定义音频处理，如添加混响效果
        
        // 音频数据统计
        audioFrameCount++;
        if (audioFrameCount % 50 == 0) {
            Log.d(TAG, "已处理音频帧数: " + audioFrameCount);
        }
    }
});
```

**参数**:
- `frame`: PCM格式的音频数据帧

**使用场景**:
- 自定义音频处理
- 音频特效应用
- 音频分析统计

## onProcessVideoFrame

```java
public int onProcessVideoFrame(V2TXLiveVideoFrame srcFrame, V2TXLiveVideoFrame dstFrame)
int onProcessVideoFrame(V2TXLiveVideoFrame srcFrame, V2TXLiveVideoFrame dstFrame) {
    thirdparty_process(srcFrame.texture.textureId, srcFrame.width, srcFrame.height, dstFrame.texture.textureId);
    return 0;
}
```
```objc
- (void)onProcessVideoFrame:(V2TXLiveVideoFrame *_Nonnull)srcFrame dstFrame:(V2TXLiveVideoFrame *_Nonnull)dstFrame;
```

**功能**: 自定义视频处理回调
**前提条件**: 需要调用 `enableCustomVideoProcess()` 开启自定义视频处理
**使用场景示例**:
**情况一：美颜组件产生新纹理**
```java
@Override
public void onGLContextCreated() {
    mFURenderer.onSurfaceCreated();
    mFURenderer.setUseTexAsync(true);
}

@Override
public int onProcessVideoFrame(V2TXLiveVideoFrame srcFrame, V2TXLiveVideoFrame dstFrame) {
    dstFrame.texture.textureId = mFURenderer.onDrawFrameSingleInput(
                        srcFrame.texture.textureId, srcFrame.width, srcFrame.height);
    return 0;
}

@Override
public void onGLContextDestroyed() {
    mFURenderer.onSurfaceDestroyed();
}
```
**情况二：美颜组件不产生新纹理**
```java
int onProcessVideoFrame(V2TXLiveVideoFrame srcFrame, V2TXLiveVideoFrame dstFrame) {
    thirdparty_process(srcFrame.texture.textureId, srcFrame.width, srcFrame.height, dstFrame.texture.textureId);
    return 0;
}
```

**参数**:
- `srcFrame`: 用于承载未处理的视频画面
- `dstFrame`: 用于承载处理过的视频画面

**返回值**: 处理结果（0表示成功）

## onGLContextCreated
> **适用平台：** Android / Flutter

```java
public void onGLContextCreated()
```

**功能**: SDK内部的OpenGL环境的创建通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onGLContextCreated() {
        Log.i(TAG, "OpenGL环境已创建");
        // 初始化美颜组件
        if (mBeautyProcessor != null) {
            mBeautyProcessor.initGL();
        }
    }
});
```

**使用场景**:
- 美颜组件初始化
- 视频处理模块初始化
- OpenGL资源创建

## onGLContextDestroyed

```java
public void onGLContextDestroyed()
```
```objc
- (void)onGLContextDestroyed;
```

**功能**: SDK内部的OpenGL环境的销毁通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onGLContextDestroyed() {
        Log.i(TAG, "OpenGL环境已销毁");
        // 清理美颜组件资源
        if (mBeautyProcessor != null) {
            mBeautyProcessor.releaseGL();
        }
    }
});
```

**使用场景**:
- 美颜组件清理
- 视频处理模块清理
- OpenGL资源释放

## onStatisticsUpdate

```java
public void onStatisticsUpdate(V2TXLivePusherStatistics statistics)
```
```objc
- (void)onStatisticsUpdate:(V2TXLivePusherStatistics *)statistics;
```

**功能**: 推流器统计数据实时回调，2s回调一次
**包含数据**:
- 视频宽度/高度
- 帧率/码率
- 网络状况
- 缓存大小
- 推流延迟
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onStatisticsUpdate(V2TXLivePusherStatistics statistics) {
        Log.d(TAG, "推流统计 - 帧率: " + statistics.fps + 
                  ", 码率: " + statistics.videoBitrate + 
                  ", 分辨率: " + statistics.width + "x" + statistics.height);
        
        // 更新推流质量显示
    }
});
```

**参数**:
- `statistics`: 推流统计信息 `V2TXLivePusherStatistics`

**使用场景**:
- 推流质量监控
- 性能优化分析
- 用户体验统计

## onSnapshotComplete

```java
public void onSnapshotComplete(Bitmap image)
```
```objc
- (void)onSnapshotComplete:(nullable TXImage *)image;
```

**功能**: 截图操作完成回调
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onSnapshotComplete(Bitmap image) {
        Log.i(TAG, "截图完成，图片尺寸: " + image.getWidth() + "x" + image.getHeight());
        
        // 保存截图到相册
        
        // 显示截图预览
    }
});

// 触发截图
pusher.snapshot();
```

**参数**:
- `image`: 截取的视频画面Bitmap

**使用场景**:
- 视频截图保存
- 画面分享功能
- 证据保存

## onSetMixTranscodingConfig

```java
public void onSetMixTranscodingConfig(int code, String msg)
```
```objc
- (void)onSetMixTranscodingConfig:(V2TXLiveCode)code message:(NSString *)msg;
```

**功能**: 设置云端的混流转码参数的回调，仅RTC协议支持
**对应接口**: `setMixTranscodingConfig()`
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onSetMixTranscodingConfig(int code, String msg) {
        if (code == 0) {
            Log.i(TAG, "云端混流转码配置成功");
        } else {
            Log.e(TAG, "云端混流转码配置失败: " + msg);
        }
    }
});

// 配置云端混流
V2TXLiveDef.V2TXLiveTranscodingConfig config = new V2TXLiveDef.V2TXLiveTranscodingConfig();
// ... 配置混流参数
pusher.setMixTranscodingConfig(config);
```

**参数**:
- `code`: 0表示成功，其余值表示失败
- `msg`: 具体错误原因

**使用场景**:
- 混流转码状态监控
- 转码失败处理
- 多路流合成状态跟踪

## onVoiceActivityDetectionUpdate
> **适用平台：** Android / iOS

```java
public void onVoiceActivityDetectionUpdate(boolean active)
```
```objc
- (void)onVoiceActivityDetectionUpdate:(BOOL)active;
```

**功能**: 人声检测状态更新回调
**前提条件**: 调用 `enableVoiceActivityDetection()` 开启人声检测
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onVoiceActivityDetectionUpdate(boolean active) {
        if (active) {
            Log.i(TAG, "检测到人声活动开始");
            // 显示说话状态指示器
            showSpeakingIndicator(true);
        } else {
            Log.i(TAG, "检测到人声活动停止");
            // 隐藏说话状态指示器
            showSpeakingIndicator(false);
        }
    }
});

// 开启人声检测
pusher.enableVoiceActivityDetection(true);
```

**参数**:
- `active`: 人声开始或停止

**使用场景**:
- 语音活动检测
- 智能静音处理
- 语音识别应用

## onScreenCaptureStarted

```java
public void onScreenCaptureStarted()
```
```objc
- (void)onScreenCaptureStarted;
```

**功能**: 当屏幕分享开始时，SDK会通过此回调通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onScreenCaptureStarted() {
        Log.i(TAG, "屏幕分享已开始");
        // 更新UI显示屏幕分享状态
        updateScreenShareStatus(true);
    }
});
```

**使用场景**:
- 屏幕分享状态监控
- UI状态更新
- 权限检查确认

## onScreenCaptureStopped

```java
public void onScreenCaptureStopped(int reason)
```
```objc
- (void)onScreenCaptureStopped:(int)reason;
```

**功能**: 当屏幕分享停止时，SDK会通过此回调通知
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onScreenCaptureStopped(int reason) {
        Log.i(TAG, "屏幕分享已停止，原因: " + reason);
        
        switch (reason) {
            case 0:
                Log.i(TAG, "用户主动停止屏幕分享");
                break;
            case 1:
                Log.w(TAG, "屏幕分享被系统中断");
                break;
            case 2:
                Log.w(TAG, "显示屏状态变更导致屏幕分享停止");
                break;
        }
        
        // 更新UI显示屏幕分享状态
        updateScreenShareStatus(false);
    }
});
```

**参数**:
- `reason`: 停止原因
  - `0`: 用户主动停止
  - `1`: iOS表示录屏被系统中断；Mac、Windows表示屏幕分享窗口被关闭
  - `2`: Windows表示屏幕分享的显示屏状态变更；其他平台不抛出

**使用场景**:
- 屏幕分享异常处理
- 用户界面状态恢复
- 权限丢失处理

## onLocalRecordBegin
> **适用平台：** Android / iOS

```java
public void onLocalRecordBegin(int code, String storagePath)
```
```objc
- (void)onLocalRecordBegin:(NSInteger)errCode storagePath:(NSString *)storagePath;
```

**功能**: 录制任务开始的事件回调
**对应接口**: `startLocalRecording()`
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onLocalRecordBegin(int code, String storagePath) {
        if (code == 0) {
            Log.i(TAG, "本地录制开始，文件路径: " + storagePath);
            // 显示录制状态
            showRecordingStatus(true);
        } else {
            Log.e(TAG, "本地录制开始失败，错误码: " + code);
            // 处理录制失败情况
            handleRecordingError(code);
        }
    }
});
```

**参数**:
- `code`: 状态码
  - `0`: 录制任务启动成功
  - `-1`: 内部错误导致录制任务启动失败
  - `-2`: 文件后缀名有误
  - `-6`: 录制已经启动，需要先停止录制
  - `-7`: 录制文件已存在，需要先删除文件
  - `-8`: 录制目录无写入权限
- `storagePath`: 录制的文件地址

## onLocalRecording
> **适用平台：** Android / iOS

```java
public void onLocalRecording(long durationMs, String storagePath)
```
```objc
- (void)onLocalRecording:(NSInteger)durationMs storagePath:(NSString *)storagePath;
```

**功能**: 录制任务正在进行中的进展事件回调
**默认行为**: 不抛出本事件回调，需要在 `startLocalRecording()` 时设定回调间隔参数
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onLocalRecording(long durationMs, String storagePath) {
        // 更新录制进度显示
        updateRecordingProgress(durationMs);
        
        // 录制时长限制检查（例如限制录制1小时）
        if (durationMs >= 3600000) {
            Log.w(TAG, "录制时长已达限制，自动停止录制");
            pusher.stopLocalRecording();
        }
    }
});
```

**参数**:
- `durationMs`: 录制时长
- `storagePath`: 录制的文件地址

**使用场景**:
- 录制进度显示
- 录制时长限制
- 录制状态监控

## onLocalRecordComplete
> **适用平台：** Android / iOS

```java
public void onLocalRecordComplete(int code, String storagePath)
```
```objc
- (void)onLocalRecordComplete:(NSInteger)errCode storagePath:(NSString *)storagePath;
```

**功能**: 录制任务已经结束的事件回调
**对应接口**: `stopLocalRecording()`
```java
pusher.setObserver(new V2TXLivePusherObserver() {
    @Override
    public void onLocalRecordComplete(int code, String storagePath) {
        if (code == 0) {
            Log.i(TAG, "本地录制完成，文件路径: " + storagePath);
            // 显示录制完成状态
            showRecordingComplete(storagePath);
        } else {
            Log.w(TAG, "本地录制异常结束，状态码: " + code);
            // 处理录制异常结束
            handleRecordingAbnormalEnd(code);
        }
    }
});
```

**参数**:
- `code`: 状态码
  - `0`: 结束录制任务成功
  - `-1`: 录制失败
  - `-2`: 切换分辨率或横竖屏导致录制结束
  - `-3`: 录制时间太短，或未采集到任何视频或音频数据
- `storagePath`: 录制的文件地址

## V2TXLivePusherListenerType
> **适用平台：** Flutter

定义了推流器观察者的所有回调类型：
