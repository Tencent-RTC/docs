# 腾讯云直播 SDK V2TXLiveCode 错误码定义 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## V2TXLIVE_OK

```java
public static final int V2TXLIVE_OK = 0;
```
```objc
V2TXLIVE_OK = 0
```
```dart
static const int V2TXLIVE_OK = 0;
```

**含义**: 没有错误，操作成功完成

**使用场景**:
- API调用成功返回
- 操作执行成功确认
- 状态检查正常

## V2TXLIVE_ERROR_FAILED

```java
public static final int V2TXLIVE_ERROR_FAILED = -1;
```
```objc
V2TXLIVE_ERROR_FAILED = -1
```
```dart
static const int V2TXLIVE_ERROR_FAILED = -1;
```

**含义**: 暂未归类的通用错误

**触发场景**:
- 内部处理异常

## V2TXLIVE_ERROR_INVALID_PARAMETER

```java
public static final int V2TXLIVE_ERROR_INVALID_PARAMETER = -2;
```
```objc
V2TXLIVE_ERROR_INVALID_PARAMETER = -2
```
```dart
static const int V2TXLIVE_ERROR_INVALID_PARAMETER = -2;
```

**含义**: 调用API时传入的参数不合法

**常见场景**:
- 推流地址格式错误
- 设置的参数不支持

## V2TXLIVE_ERROR_REFUSED

```java
public static final int V2TXLIVE_ERROR_REFUSED = -3;
```
```objc
V2TXLIVE_ERROR_REFUSED = -3
```
```dart
static const int V2TXLIVE_ERROR_REFUSED = -3;
```

**含义**: API调用被拒绝

**触发场景**:
- 调用时机不正确

## V2TXLIVE_ERROR_NOT_SUPPORTED

```java
public static final int V2TXLIVE_ERROR_NOT_SUPPORTED = -4;
```
```objc
V2TXLIVE_ERROR_NOT_SUPPORTED = -4
```
```dart
static const int V2TXLIVE_ERROR_NOT_SUPPORTED = -4;
```

**含义**: 当前API不支持调用

**常见场景**:
- 平台不支持的功能
- SDK版本过低
- 硬件不支持的特性

## V2TXLIVE_ERROR_INVALID_LICENSE

```java
public static final int V2TXLIVE_ERROR_INVALID_LICENSE = -5;
```
```objc
V2TXLIVE_ERROR_INVALID_LICENSE = -5
```
```dart
static const int V2TXLIVE_ERROR_INVALID_LICENSE = -5;
```

**含义**: License不合法，调用失败

**触发场景**:
- License文件未设置
- License已过期
- License与包名不匹配

## V2TXLIVE_ERROR_REQUEST_TIMEOUT

```java
public static final int V2TXLIVE_ERROR_REQUEST_TIMEOUT = -6;
```
```objc
V2TXLIVE_ERROR_REQUEST_TIMEOUT = -6
```
```dart
static const int V2TXLIVE_ERROR_REQUEST_TIMEOUT = -6;
```

**含义**: 请求服务器超时

**常见场景**:
- 网络连接不稳定
- 服务器响应缓慢

## V2TXLIVE_ERROR_SERVER_PROCESS_FAILED

```java
public static final int V2TXLIVE_ERROR_SERVER_PROCESS_FAILED = -7;
```
```objc
V2TXLIVE_ERROR_SERVER_PROCESS_FAILED = -7
```
```dart
static const int V2TXLIVE_ERROR_SERVER_PROCESS_FAILED = -7;
```

**含义**: 服务器无法处理您的请求

**触发场景**:
- 服务器内部错误
- 请求格式不正确
- 服务器资源不足

## V2TXLIVE_ERROR_DISCONNECTED

```java
public static final int V2TXLIVE_ERROR_DISCONNECTED = -8;
```
```objc
V2TXLIVE_ERROR_DISCONNECTED = -8
```
```dart
static const int V2TXLIVE_ERROR_DISCONNECTED = -8;
```

**含义**: 网络连接断开

**常见场景**:
- 推流网络断开
- 拉流网络断开

## V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS

```java
public static final int V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS = -2304;
```
```objc
V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS = -2304
```
```dart
static const int V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS = -2304;
```

**含义**: 找不到可用的HEVC解码器

**触发场景**:
- 设备不支持HEVC硬解
- HEVC解码器初始化失败
- 系统版本过低

## V2TXLIVE_WARNING_NETWORK_BUSY

```java
public static final int V2TXLIVE_WARNING_NETWORK_BUSY = 1101;
```
```objc
V2TXLIVE_WARNING_NETWORK_BUSY = 1101
```
```dart
static const int V2TXLIVE_WARNING_NETWORK_BUSY = 1101;
```

**含义**: 网络状况不佳，上行带宽太小，上传数据受阻

**监控指标**:
- 上行带宽限制
- 网络延迟增加
- 数据包丢失率升高

## V2TXLIVE_WARNING_VIDEO_BLOCK

```java
public static final int V2TXLIVE_WARNING_VIDEO_BLOCK = 2105;
```
```objc
V2TXLIVE_WARNING_VIDEO_BLOCK = 2105
```
```dart
static const int V2TXLIVE_WARNING_VIDEO_BLOCK = 2105;
```

**含义**: 当前视频播放出现卡顿

**卡顿原因分析**:
- 网络带宽不足
- 解码性能瓶颈
- 渲染性能问题

## V2TXLIVE_WARNING_CAMERA_START_FAILED

```java
public static final int V2TXLIVE_WARNING_CAMERA_START_FAILED = -1301;
```
```objc
V2TXLIVE_WARNING_CAMERA_START_FAILED = -1301
```
```dart
static const int V2TXLIVE_WARNING_CAMERA_START_FAILED = -1301;
```

**含义**: 摄像头打开失败

**失败原因**:
- 硬件故障
- 驱动问题
- 资源冲突

## V2TXLIVE_WARNING_CAMERA_OCCUPIED

```java
public static final int V2TXLIVE_WARNING_CAMERA_OCCUPIED = -1316;
```
```objc
V2TXLIVE_WARNING_CAMERA_OCCUPIED = -1316
```
```dart
static const int V2TXLIVE_WARNING_CAMERA_OCCUPIED = -1316;
```

**含义**: 摄像头正在被占用中

**占用场景**:
- 其他应用正在使用摄像头
- 系统相机应用运行中
- 视频通话进行中

## V2TXLIVE_WARNING_CAMERA_NO_PERMISSION

```java
public static final int V2TXLIVE_WARNING_CAMERA_NO_PERMISSION = -1314;
```
```objc
V2TXLIVE_WARNING_CAMERA_NO_PERMISSION = -1314
```
```dart
static const int V2TXLIVE_WARNING_CAMERA_NO_PERMISSION = -1314;
```

**含义**: 摄像头设备未授权，通常在移动设备出现，可能是权限被用户拒绝了

## V2TXLIVE_WARNING_MICROPHONE_START_FAILED

```java
public static final int V2TXLIVE_WARNING_MICROPHONE_START_FAILED = -1302;
```
```objc
V2TXLIVE_WARNING_MICROPHONE_START_FAILED = -1302
```
```dart
static const int V2TXLIVE_WARNING_MICROPHONE_START_FAILED = -1302;
```

**含义**: 麦克风打开失败

**故障原因**:
- 硬件连接问题
- 系统音频设置异常
- 音频会话初始化失败

## V2TXLIVE_WARNING_MICROPHONE_OCCUPIED

```java
public static final int V2TXLIVE_WARNING_MICROPHONE_OCCUPIED = -1319;
```
```objc
V2TXLIVE_WARNING_MICROPHONE_OCCUPIED = -1319
```
```dart
static const int V2TXLIVE_WARNING_MICROPHONE_OCCUPIED = -1319;
```

**含义**: 麦克风正在被占用中，例如移动设备正在通话时，打开麦克风会失败

**占用场景**:
- 设备正在通话中
- 其他音频应用占用麦克风
- 音频会话冲突

## V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION

```java
public static final int V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION = -1317;
```
```objc
V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION = -1317
```
```dart
static const int V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION = -1317;
```

**含义**: 麦克风设备未授权，通常在移动设备出现，可能是权限被用户拒绝了

## V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED

```java
public static final int V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED = -1309;
```
```objc
V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED = -1309
```
```dart
static const int V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED = -1309;
```

**含义**: 当前系统不支持屏幕分享

## V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED

```java
public static final int V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED = -1308;
```
```objc
V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED = -1308
```
```dart
static const int V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED = -1308;
```

**含义**: 开始录屏失败，如果在移动设备出现，可能是权限被用户拒绝了

## V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED

```java
public static final int V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED = -7001;
```
```objc
V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED = -7001
```
```dart
static const int V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED = -7001;
```

**含义**: 录屏被系统中断

## V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED

```java
public static final int V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED = 1104;
```
```objc
V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED = 1104
```
```dart
static const int V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED = 1104;
```

**含义**: 表示编码器发生改变，可以通过onWarning函数的扩展信息中的codec_type字段来获取当前的编码格式

## V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED

```java
public static final int V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED = 2008;
```
```objc
V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED = 2008
```
```dart
static const int V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED = 2008;
```

**含义**: 表示解码器发生改变，可以通过onWarning函数的扩展信息中的codec_type字段来获取当前的解码格式

**注意**: Windows端不支持此错误码的扩展信息

## V2TXLIVE_WARNING_ENCODER_START_FAILED
> **适用平台：** Flutter

```dart
static const int V2TXLIVE_WARNING_ENCODER_START_FAILED = -1303;
```

**含义**: 视频编码器启动失败

**触发场景**:
- 编码器硬件资源不足
- 编码参数设置不当
- 系统编解码器异常

**处理建议**:
- 检查编码参数设置
- 降低视频分辨率或码率
- 重启编码器或应用

## V2TXLIVE_WARNING_DECODER_START_FAILED
> **适用平台：** Flutter

```dart
static const int V2TXLIVE_WARNING_DECODER_START_FAILED = -1304;
```

**含义**: 视频解码器启动失败

**触发场景**:
- 解码器硬件资源不足
- 视频格式不支持
- 系统解码器异常

**处理建议**:
- 检查视频格式兼容性
- 降低视频分辨率或帧率
- 重启解码器或应用
