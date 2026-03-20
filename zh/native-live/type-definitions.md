# 腾讯云直播 SDK V2TXLiveDef 类型定义 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## V2TXLiveMode

| 枚举值 | 说明 |
|--------|------|
| TXLiveMode_RTMP | 支持协议: RTMP |
| TXLiveMode_RTC | 支持协议: RTC |

## V2TXLiveVideoResolution
| 分辨率 | 码率范围 | 帧率 | 说明 |
|--------|----------|------|------|
| 160×160 | 100-150Kbps | 15fps | 超低分辨率，适合语音通话 |
| 270×270 | 200-300Kbps | 15fps | 低分辨率 |
| 480×480 | 350-525Kbps | 15fps | 标准方形分辨率 |
| 320×240 | 250-375Kbps | 15fps | 4:3比例标准分辨率 |
| 480×360 | 400-600Kbps | 15fps | 4:3比例中等分辨率 |
| 640×480 | 600-900Kbps | 15fps | 标清分辨率 |
| 320×180 | 250-400Kbps | 15fps | 16:9比例低分辨率 |
| 480×270 | 350-550Kbps | 15fps | 16:9比例标准分辨率 |
| 640×360 | 500-900Kbps | 15fps | 16:9比例中等分辨率 |
| 960×540 | 800-1500Kbps | 15fps | 高清分辨率 |
| 1280×720 | 1000-1800Kbps | 15fps | 720p高清 |
| 1920×1080 | 2500-3000Kbps | 15fps | 1080p全高清 |

## V2TXLiveVideoResolutionMode

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveVideoResolutionModeLandscape | 横屏模式 |
| V2TXLiveVideoResolutionModePortrait | 竖屏模式 |

## V2TXLiveVideoEncoderParam

| 字段 | 类型 | 说明 |
|------|------|------|
| videoResolution | V2TXLiveVideoResolution | 视频分辨率 |
| videoResolutionMode | V2TXLiveVideoResolutionMode | 分辨率模式 |
| videoFps | int | 视频采集帧率 |
| videoBitrate | int | 目标视频码率 |
| minVideoBitrate | int | 最低视频码率 |
**推荐配置**:
- **帧率**: 15fps或20fps（5fps以下卡顿明显，20fps以上浪费带宽）
- **码率自适应**: 通过同时设置videoBitrate和minVideoBitrate来约束码率调整范围

## V2TXLiveMirrorType

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveMirrorTypeAuto | 系统默认镜像类型，前置摄像头镜像，后置摄像头不镜像 |
| V2TXLiveMirrorTypeEnable | 前置摄像头和后置摄像头都切换为镜像模式 |
| V2TXLiveMirrorTypeDisable | 前置摄像头和后置摄像头都切换为非镜像模式 |

## V2TXLiveFillMode

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveFillModeFill | 图像铺满屏幕，超出部分将被裁剪，画面可能不完整 | 全平台 |
| V2TXLiveFillModeFit | 图像长边填满屏幕，短边区域填充黑色，画面内容完整 | 全平台 |
| V2TXLiveFillModeScaleFill | 图像拉伸铺满，长度和宽度可能不会按比例变化 | 全平台 |

## V2TXLiveRotation

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveRotation0 | 不旋转 |
| V2TXLiveRotation90 | 顺时针旋转90度 |
| V2TXLiveRotation180 | 顺时针旋转180度 |
| V2TXLiveRotation270 | 顺时针旋转270度 |

## V2TXLivePixelFormat

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLivePixelFormatUnknown | 未知像素格式 | 全平台 |
| V2TXLivePixelFormatI420 | I420格式（YUV420P） | 全平台 |
| V2TXLivePixelFormatTexture2D | OpenGL 2D纹理格式 | 全平台 |
| V2TXLivePixelFormatNV12 | NV12格式 | iOS |
| V2TXLivePixelFormatBGRA32 | BGRA32格式 | iOS |
| v2TXLivePixelFormatNV12 | YUV420SP NV12格式 | Flutter |
| v2TXLivePixelFormatBGRA32 | BGRA8888格式 | Flutter |

## V2TXLiveBufferType

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveBufferTypeUnknown | 未知数据包装格式 | 全平台 |
| V2TXLiveBufferTypeByteBuffer | DirectBuffer，装载I420等buffer，在native层使用 | Android/Flutter |
| V2TXLiveBufferTypeByteArray | byte[]，装载I420等buffer，在Java层使用 | Android/Flutter |
| V2TXLiveBufferTypeTexture | OpenGL纹理格式，性能最好，画质损失最少 | 全平台 |
| V2TXLiveBufferTypePixelBuffer | PixelBuffer格式，iOS平台原生视频数据格式 | iOS |
| V2TXLiveBufferTypeNSData | NSData格式，装载I420等buffer数据 | iOS |

## V2TXLiveTexture

> **适用平台：** Android

| 字段 | 类型 | 说明 |
|------|------|------|
| textureId | int | 视频纹理ID |
| eglContext10 | javax.microedition.khronos.egl.EGLContext | OpenGL接口定义 |
| eglContext14 | android.opengl.EGLContext | Android OpenGL接口定义 |

## V2TXLiveVideoFrame

| 字段 | 类型 | 说明 | 平台 |
|------|------|------|------|
| pixelFormat | V2TXLivePixelFormat | 视频帧像素格式 | 全平台 |
| bufferType | V2TXLiveBufferType | 视频数据包装格式 | 全平台 |
| texture | V2TXLiveTexture | 视频纹理包装类 | Android |
| data | byte[] | 视频数据 | 全平台 |
| buffer | ByteBuffer | 视频数据Buffer | Android |
| width | int | 视频宽度 | 全平台 |
| height | int | 视频高度 | 全平台 |
| rotation | int | 视频帧旋转角度 | 全平台 |
| pixelBuffer | CVPixelBufferRef | 视频数据PixelBuffer | iOS |
| textureId | GLuint | 视频纹理ID | iOS/Flutter |

## V2TXLiveAudioQuality

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveAudioQualitySpeech | 语音音质：16k采样率，单声道，16kbps码率，适合语音通话 |
| V2TXLiveAudioQualityDefault | 默认音质：48k采样率，单声道，50kbps码率，SDK默认配置 |
| V2TXLiveAudioQualityMusic | 音乐音质：48k采样率，双声道+全频带，128kbps码率，适合高保真音乐场景 |

## V2TXLiveAudioFrame

| 字段 | 类型 | 说明 |
|------|------|------|
| data | byte[] | 音频数据 |
| sampleRate | int | 采样率（默认48000Hz） |
| channel | int | 声道数（默认1，单声道） |
| timestamp | long | 时间戳，单位ms |

## V2TXLiveAudioFrameOperationMode
> **适用平台：** Android / iOS

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveAudioFrameOperationModeReadWrite | 读写模式：可以获取并修改回调的音频数据（默认模式） |
| V2TXLiveAudioFrameOperationModeReadOnly | 只读模式：仅从回调中获取音频数据 |

## V2TXLiveAudioFrameObserverFormat
> **适用平台：** Android / iOS

| 字段 | 类型 | 说明 |
|------|------|------|
| sampleRate | int | 采样率（默认48000Hz） |
| channel | int | 声道数（默认1） |
| samplesPerCall | int | 采样点数 |
| mode | V2TXLiveAudioFrameOperationMode | 回调数据读写模式 |

## V2TXLivePusherStatistics

| 字段 | 类型 | 说明 |
|------|------|------|
| appCpu | int | 当前App的CPU使用率（%） |
| systemCpu | int | 当前系统的CPU使用率（%） |
| width | int | 视频宽度 |
| height | int | 视频高度 |
| fps | int | 帧率（fps） |
| videoBitrate | int | 视频码率（Kbps） |
| audioBitrate | int | 音频码率（Kbps） |
| rtt | int | SDK到云端的往返延时（ms） |
| netSpeed | int | 上行速度（kbps） |

## V2TXLivePlayerStatistics

| 字段 | 类型 | 说明 |
|------|------|------|
| appCpu | int | 当前App的CPU使用率（%） |
| systemCpu | int | 当前系统的CPU使用率（%） |
| width | int | 视频宽度 |
| height | int | 视频高度 |
| fps | int | 帧率（fps） |
| videoBitrate | int | 视频码率（Kbps） |
| audioBitrate | int | 音频码率（Kbps） |
| audioPacketLoss | int | 网络音频丢包率（%） |
| videoPacketLoss | int | 网络视频丢包率（%） |
| jitterBufferDelay | int | 播放延迟（ms） |
| audioTotalBlockTime | int | 音频播放累计卡顿时长（ms） |
| audioBlockRate | int | 音频播放卡顿率（%） |
| videoTotalBlockTime | int | 视频播放累计卡顿时长（ms） |
| videoBlockRate | int | 视频播放卡顿率（%） |
| rtt | int | 往返延时（ms） |
| netSpeed | int | 下载速度（kbps） |

## V2TXLivePushStatus

| 枚举值 | 说明 |
|--------|------|
| V2TXLivePushStatusDisconnected | 与服务器断开连接 |
| V2TXLivePushStatusConnecting | 正在连接服务器 |
| V2TXLivePushStatusConnectSuccess | 连接服务器成功 |
| V2TXLivePushStatusReconnecting | 重连服务器中 |

## V2TXLiveMixInputType

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveMixInputTypeAudioVideo | 混入音视频 |
| V2TXLiveMixInputTypePureVideo | 只混入视频 |
| V2TXLiveMixInputTypePureAudio | 只混入音频 |

## V2TXLiveMixStream

| 字段 | 类型 | 说明 |
|------|------|------|
| userId | String | 参与混流的userId |
| streamId | String | 推流streamId |
| x | int | 图层位置x坐标（绝对像素值） |
| y | int | 图层位置y坐标（绝对像素值） |
| width | int | 图层宽度（绝对像素值） |
| height | int | 图层高度（绝对像素值） |
| zOrder | int | 图层层次（1-15，不可重复） |
| inputType | V2TXLiveMixInputType | 直播流输入类型 |

## V2TXLiveTranscodingConfig

| 字段 | 类型 | 说明 | 平台 |
|------|------|------|------|
| videoWidth | int | 最终转码后的视频宽度 | 全平台 |
| videoHeight | int | 最终转码后的视频高度 | 全平台 |
| videoBitrate | int | 最终转码后的视频码率（kbps） | 全平台 |
| videoFramerate | int | 最终转码后的视频帧率（FPS） | 全平台 |
| videoGOP | int | 关键帧间隔（秒） | 全平台 |
| backgroundColor | int | 混合后画面的底色颜色 | 全平台 |
| backgroundImage | String | 混合后画面的背景图 | 全平台 |
| audioSampleRate | int | 最终转码后的音频采样率 | 全平台 |
| audioBitrate | int | 最终转码后的音频码率（kbps） | 全平台 |
| audioChannels | int | 最终转码后的音频声道数 | 全平台 |
| mixStreams | ArrayList<V2TXLiveMixStream> | 子画面位置信息 | Android/Flutter |
| outputStreamId | String | 输出到CDN的直播流ID | 全平台 |

## V2TXLiveRecordMode

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveRecordModeBoth | 录制音频和视频 |

## V2TXLiveLocalRecordingParams

| 字段 | 类型 | 说明 |
|------|------|------|
| filePath | String | 录制的文件地址（必填） |
| recordMode | V2TXLiveRecordMode | 媒体录制模式 |
| interval | int | 录制信息更新频率（毫秒） |

## V2TXLiveLogLevel

| 常量名 | 值 | 说明 |
|--------|-----|------|
| V2TXLiveLogLevelAll | 0 | 输出所有级别的log |
| V2TXLiveLogLevelDebug | 1 | 输出DEBUG及以上级别 |
| V2TXLiveLogLevelInfo | 2 | 输出INFO及以上级别 |
| V2TXLiveLogLevelWarning | 3 | 输出WARNING及以上级别 |
| V2TXLiveLogLevelError | 4 | 输出ERROR及以上级别 |
| V2TXLiveLogLevelFatal | 5 | 只输出FATAL级别 |
| V2TXLiveLogLevelNULL | 6 | 不输出任何SDK log |

## V2TXLiveLogConfig

| 字段 | 类型 | 说明 |
|------|------|------|
| logLevel | int | 设置Log级别 |
| enableObserver | boolean | 是否通过Observer接收Log信息 |
| enableConsole | boolean | 是否在编辑器控制台打印Log |
| enableLogFile | boolean | 是否启用本地Log文件 |
| logPath | String | 本地Log存储目录 |

## V2TXLiveSocks5ProxyConfig

| 字段 | 类型 | 说明 |
|------|------|------|
| supportHttps | boolean | 是否支持HTTPS |
| supportTcp | boolean | 是否支持TCP |
| supportUdp | boolean | 是否支持UDP |

## V2TXLiveStreamInfo

| 字段 | 类型 | 说明 |
|------|------|------|
| width | int | 视频宽度 |
| height | int | 视频高度 |
| bitrate | int | 码率（bps） |
| framerate | float | 帧率 |
| url | String | 流地址 |

## V2TXLivePictureInPictureState

> **适用平台：** iOS

| 枚举值 | 说明 |
|--------|------|
| V2TXLivePictureInPictureStateUndefined | 画中画状态未定义 |
| V2TXLivePictureInPictureStateOccurError | 画中画发生错误 |
| V2TXLivePictureInPictureStateWillStart | 画中画将要开始 |
| V2TXLivePictureInPictureStateDidStart | 画中画已经开始 |
| V2TXLivePictureInPictureStateWillStop | 画中画将要停止 |
| V2TXLivePictureInPictureStateDidStop | 画中画已经停止 |
| V2TXLivePictureInPictureStateRestoreUI | 画中画恢复用户界面 |

## V2TXAudioRoute

> **适用平台：** iOS

| 枚举值 | 说明 |
|--------|------|
| V2TXAudioRouteSpeakerphone | 扬声器模式，声音从手机扬声器播放 |
| V2TXAudioRouteEarpiece | 听筒模式，声音从手机听筒播放 |

## V2TXLiveRenderView

> **适用平台：** Flutter

| 常量名 | 值 | 说明 |
|--------|-----|------|
| renderViewType | 'v2_live_view_factory' |  |
渲染视图类，用于定义视频渲染视图的类型。

## TXSystemVolumeType

> **适用平台：** Flutter

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXSystemVolumeTypeAuto | 0 | 自动模式切换 |
| TXSystemVolumeTypeMedia | 1 | 全媒体音量 |
| TXSystemVolumeTypeVOIP | 2 | 通话音量 |

## TXAudioRoute

> **适用平台：** Flutter

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXAudioRouteSpeakerphone | 0 | 扬声器播放 |
| TXAudioRouteEarpiece | 1 | 听筒播放 |

## TXVoiceReverbType

> **适用平台：** Flutter

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXVoiceReverbTypeOff | 0 | 关闭混响 |
| TXVoiceReverbTypeKTV | 1 | KTV |
| TXVoiceReverbTypeSmallRoom | 2 | 小房间 |
| TXVoiceReverbTypeGreatHall | 3 | 大会堂 |
| TXVoiceReverbTypeDeep | 4 | 深沉 |
| TXVoiceReverbTypeLoud | 5 | 洪亮 |
| TXVoiceReverbTypeMetallic | 6 | 金属声 |
| TXVoiceReverbTypeMagnetic | 7 | 磁性 |
| TXVoiceReverbTypeEthereal | 8 | 空灵 |
| TXVoiceReverbTypeStudio | 9 | 录音棚 |
| TXVoiceReverbTypeMelodious | 10 | 悠扬 |
| TXVoiceReverbTypeStudio2 | 11 | 录音棚2 |

## TXVoiceChangerType

> **适用平台：** Flutter

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXVoiceChangerTypeOff | 0 | 关闭变声 |
| TXVoiceChangerTypeChild | 1 | 顽皮男孩 |
| TXVoiceChangerTypeLittleGirl | 2 | 少女 |
| TXVoiceChangerTypeUncle | 3 | 中年大叔 |
| TXVoiceChangerTypeHeavyMetal | 4 | 重金属 |
| TXVoiceChangerTypeCold | 5 | 感冒 |
| TXVoiceChangerTypePunk | 6 | 朋克 |
| TXVoiceChangerTypeBeast | 7 | 狂暴动物 |
| TXVoiceChangerTypeFatty | 8 | 肥仔 |
| TXVoiceChangerTypeStrongCurrent | 9 | 强电流 |
| TXVoiceChangerTypeRobot | 10 | 机器人 |
| TXVoiceChangerTypeEthereal | 11 | 空灵 |
