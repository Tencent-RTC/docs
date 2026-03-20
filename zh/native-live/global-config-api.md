# 腾讯云直播 SDK V2TXLivePremier 全局配置 API 文档（Android/iOS/Flutter）

适用平台：Android/iOS/Flutter。

## getSDKVersionStr

```java
public static String getSDKVersionStr()
```
```objc
+ (NSString *)getSDKVersionStr;
```
```dart
static Future<String> getSDKVersionStr() async
```

```java
String version = V2TXLivePremier.getSDKVersionStr();
Log.i(TAG, "当前SDK版本：" + version);
```

**返回值**: SDK 版本字符串，格式如 "10.7.0.12345"

**使用时机**: 无限制
**使用场景**:
- 版本兼容性检查
- 问题排查时提供版本信息
- 统计和监控

## setObserver

```java
public static void setObserver(V2TXLivePremierObserver observer)
```
```objc
+ (void)setObserver:(id<V2TXLivePremierObserver>)observer;
```
```dart
static Future<void> setObserver(V2TXLivePremierObserver? observer) async
```

**注意事项**:
- 建议在应用启动时设置一次即可
- 观察者对象需要在整个应用生命周期内保持有效

**参数**:
- `observer`: 实现V2TXLivePremierObserver协议的对象

**使用时机**: 在应用启动时设置，全局生效

## setLogConfig
> **适用平台：** Android / iOS

```java
public static void setLogConfig(V2TXLiveDef.V2TXLiveLogConfig config)
```
```objc
+ (V2TXLiveCode)setLogConfig:(V2TXLiveLogConfig *)config;
```

```java
V2TXLiveLogConfig logConfig = new V2TXLiveDef.V2TXLiveLogConfig();
logConfig.enableConsole = true;
logConfig.enableLogFile = true;
logConfig.enableObserver = true;
logConfig.logLevel = V2TXLiveLogLevel.V2TXLiveLogLevelAll;
V2TXLivePremier.setObserver(new V2TXLivePremier.V2TXLivePremierObserver() {
    @Override
    public void onLog(int level, String log) {
        // doSomething
    }
});
```

**参数**:
- `config`: 日志配置对象，包含日志级别、存储路径等

**使用时机**: 在应用启动时设置，全局生效
**使用场景**:
- 调试阶段开启详细日志
- 生产环境减少日志输出
- 自定义日志存储路径

## setEnvironment

```java
public static void setEnvironment(String env)
```
```objc
+ (V2TXLiveCode)setEnvironment:(const char *)env;
```
```dart
static Future<V2TXLiveCode> setEnvironment(String env) async
```

**参数**:
- `env`: 环境标识，支持：
  - `"default"`: 默认环境，SDK 在全球寻找最佳接入点
  - `"GDPR"`: GDPR 环境，音视频数据不经过中国大陆服务器

**注意**: 如无特殊需求，请不要调用此接口

## setLicence

```java
public static void setLicence(Context context, String url, String key)
```
```objc
+ (void)setLicence:(NSString *)url key:(NSString *)key;
```
```dart
static Future<void> setLicence(String url, String key) async
```

**文档地址**: https://cloud.tencent.com/document/product/454/34750
```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        V2TXLivePremier.setObserver(new V2TXLivePremierObserver() {
            @Override
            public void onLicenceLoaded(int result, String reason) {
                if (result == 0) {
                    Log.i(TAG, "licence 加载成功, " + reason);
                } else if (result == -4) {
                    Log.e(TAG, "licence 包名不匹配，" + reason);
                } else if (result == -11) {
                    Log.e(TAG, "licence 过期，" + reason);
                } else {
                    Log.e(TAG, "licence 其它错误，" + reason);
                }
            }
        });
        V2TXLivePremier.setLicence(this, "url", "key");
    }
}
```

**参数**:
- `context`: Android 上下文
- `url`: licence 地址
- `key`: licence 秘钥

**使用时机**: 应用启动时调用，必须在推流/播放前设置

## setSocks5Proxy
> **适用平台：** Android / iOS

```java
public static void setSocks5Proxy(String host, int port, String username, String password, V2TXLiveDef.V2TXLiveSocks5ProxyConfig config)
```
```objc
+ (V2TXLiveCode)setSocks5Proxy:(NSString *)host 
                          port:(NSInteger)port 
                      username:(NSString *)username 
                      password:(NSString *)password 
                        config:(V2TXLiveSocks5ProxyConfig *)config;
```

**参数**:
- `host`: socks5 代理服务器地址
- `port`: socks5 代理服务器端口
- `username`: 代理服务器用户名
- `password`: 代理服务器密码
- `config`: socks5 代理配置

**使用时机**: 应用启动时调用

## enableAudioCaptureObserver
> **适用平台：** Android / iOS

```java
public static void enableAudioCaptureObserver(boolean enable, V2TXLiveDef.V2TXLiveAudioFrameObserverFormat format)
```
```objc
+ (V2TXLiveCode)enableAudioCaptureObserver:(BOOL)enable 
                                   format:(V2TXLiveAudioFrameObserverFormat *)format;
```

**参数**:
- `enable`: 是否开启（默认 false）
- `format`: 音频帧回调格式

**使用时机**: 需要在 `V2TXLivePusher#startPush` 之前调用
---

## enableAudioPlayoutObserver
> **适用平台：** Android / iOS

```java
public static void enableAudioPlayoutObserver(boolean enable, V2TXLiveDef.V2TXLiveAudioFrameObserverFormat format)
```
```objc
+ (V2TXLiveCode)enableAudioPlayoutObserver:(BOOL)enable 
                                  format:(V2TXLiveAudioFrameObserverFormat *)format;
```

**技术规格**:
- 音频时间帧长固定为0.02s
- PCM格式音频数据
- 混合各路待播放音频后的数据

**参数**:
- `enable`: 是否开启（默认 false）
- `format`: 音频帧回调格式

## enableVoiceEarMonitorObserver
> **适用平台：** Android / iOS

```java
public static void enableVoiceEarMonitorObserver(boolean enable)
```
```objc
+ (V2TXLiveCode)enableVoiceEarMonitorObserver:(BOOL)enable;
```

**参数**:
- `enable`: 是否开启（默认 false）

## setUserId
> **适用平台：** Android / iOS

```java
public static void setUserId(String userId)
```
```objc
+ (void)setUserId:(NSString *)userId;
```

**参数**:
- `userId`: 业务侧维护的用户/设备 ID

**使用时机**: 应用启动时调用

## callExperimentalAPI

```java
public static int callExperimentalAPI(String jsonStr)
```
```objc
+ (V2TXLiveCode)callExperimentalAPI:(NSString *)jsonStr;
```
```dart
static Future<V2TXLiveCode> callExperimentalAPI(String jsonStr) async
```

**参数**:
- `jsonStr`: 接口及参数描述的 JSON 字符串

**返回值**:
- `V2TXLIVE_OK`: 成功
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: 参数非法

## onLog

```java
public void onLog(int level, String log)
```
```objc
- (void)onLog:(V2TXLiveLogLevel)level log:(NSString *)log;
```
```dart
void Function(V2TXLivePremierObserverType type, Map<String, dynamic>? params)
```

**参数**:
- `level`: 日志级别
- `log`: 日志内容

**使用场景**:
- 自定义日志处理
- 日志重定向到自有系统
- 日志过滤和格式化

## onLicenceLoaded

```java
public void onLicenceLoaded(int result, String reason)
```
```objc
- (void)onLicenceLoaded:(int)result Reason:(NSString *)reason;
```
```dart
void Function(V2TXLivePremierObserverType type, Map<String, dynamic>? params)
```

**参数**:
- `result`: 设置结果（0 成功，负数失败）
- `reason`: 失败原因

## onCaptureAudioFrame
> **适用平台：** Android / iOS

```java
public void onCaptureAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objc
- (void)onCaptureAudioFrame:(V2TXLiveAudioFrame *)frame;
```

**重要说明**:
- 不要在此回调中做耗时操作
- 音频数据支持修改
- 时间帧长固定为 0.02s
- 不包含背景音、音效等前处理效果
- 延迟极低

**参数**:
- `frame`: 音频数据帧

## onPlayoutAudioFrame
> **适用平台：** Android / iOS

```java
public void onPlayoutAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objc
- (void)onPlayoutAudioFrame:(V2TXLiveAudioFrame *)frame;
```

**重要说明**:
- 时间帧长固定为 0.02s
- 数据可读写，但需保证处理耗时
- 不包含耳返音频数据

**参数**:
- `frame`: PCM 格式音频数据帧

## onVoiceEarMonitorAudioFrame
> **适用平台：** Android / iOS

```java
public void onVoiceEarMonitorAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objc
- (void)onVoiceEarMonitorAudioFrame:(V2TXLiveAudioFrame *)frame;
```

**重要说明**:
- 时间帧长不固定
- 数据可读写，但需保证处理耗时

**参数**:
- `frame`: PCM 格式音频数据帧

## Licence设置最佳实践
> **适用平台：** Flutter

