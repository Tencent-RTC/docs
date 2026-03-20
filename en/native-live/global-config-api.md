# V2TXLivePremier 高级设置

SDK 全局配置与管理类，提供 Licence 授权、日志配置、音频数据监听等能力。

**适用平台：** Android / iOS / Flutter

## 平台声明语法
- **Android**: `V2TXLivePremier.methodName(params)`
- **iOS**: `[V2TXLivePremier methodName:params]`
- **Flutter**: `await V2TXLivePremier.methodName(params)`

## V2TXLivePremier
## getSDKVersionStr
> **适用平台：** Android / iOS

**Return Value**: Returns the SDK version as a string, for example: "10.7.0.12345"

**When to Call**: Can be called at any time

**Typical Use Cases**:
- Checking version compatibility
- Providing version information for troubleshooting
- Collecting statistics and monitoring

**Example**:

```java
// Android
public static String getSDKVersionStr()
```
```objectivec
// iOS
+ (NSString *)getSDKVersionStr;
```

## setObserver
> **适用平台：** Android / iOS

**Parameters**:
- `observer`: An object that implements the V2TXLivePremierObserver interface

**When to Call**: Set this during app startup; applies globally

**Notes**:
- Set the observer once at app startup
- The observer instance must remain valid for the entire app lifecycle

```java
// Android
public static void setObserver(V2TXLivePremierObserver observer)
```
```objectivec
// iOS
+ (void)setObserver:(id<V2TXLivePremierObserver>)observer;
```

## setLogConfig
> **适用平台：** Android / iOS

**Parameters**:
- `config`: Log configuration object, including log level, storage path, and other settings

**When to Call**: Set during app startup; applies globally

**Typical Use Cases**:
- Enable detailed logs for debugging
- Reduce log output in production
- Customize the log storage path

**Example**:

```java
// Android
public static void setLogConfig(V2TXLiveDef.V2TXLiveLogConfig config)
```
```objectivec
// iOS
+ (V2TXLiveCode)setLogConfig:(V2TXLiveLogConfig *)config;
```

## setEnvironment
> **适用平台：** Android / iOS

**Parameters**:
- `env`: Environment identifier. Supported values:
  - `"default"`: Default environment; the SDK automatically selects the optimal global access point
  - `"GDPR"`: GDPR environment; audio and video data will not pass through servers in Mainland China

**Note**: Only use this API if you have specific requirements

```java
// Android
public static void setEnvironment(String env)
```
```objectivec
// iOS
+ (V2TXLiveCode)setEnvironment:(const char *)env;
```

## setLicence
> **适用平台：** Android / iOS

**Parameters**:
- `context`: Android context
- `url`: License URL
- `key`: License key

**When to Call**: Must be called during app startup and before starting any streaming or publishing

**Documentation**: https://cloud.tencent.com/document/product/454/34750

**Example**:

```java
// Android
public static void setLicence(Context context, String url, String key)
```
```objectivec
// iOS
+ (void)setLicence:(NSString *)url key:(NSString *)key;
```

## setSocks5Proxy
> **适用平台：** Android / iOS

**Parameters**:
- `host`: Socks5 proxy server address
- `port`: Socks5 proxy server port
- `username`: Proxy server username
- `password`: Proxy server password
- `config`: Socks5 proxy configuration

**When to Call**: Set during app startup

```java
// Android
public static void setSocks5Proxy(String host, int port, String username, String password, V2TXLiveDef.V2TXLiveSocks5ProxyConfig config)
```
```objectivec
// iOS
+ (V2TXLiveCode)setSocks5Proxy:(NSString *)host 
                          port:(NSInteger)port 
                      username:(NSString *)username 
                      password:(NSString *)password 
                        config:(V2TXLiveSocks5ProxyConfig *)config;
```

## enableAudioCaptureObserver
> **适用平台：** Android / iOS

**Parameters**:
- `enable`: Enable or disable the callback (default: false)
- `format`: Audio frame callback format

**When to Call**: Must be called before `V2TXLivePusher#startPush`

```java
// Android
public static void enableAudioCaptureObserver(boolean enable, V2TXLiveDef.V2TXLiveAudioFrameObserverFormat format)
```
```objectivec
// iOS
+ (V2TXLiveCode)enableAudioCaptureObserver:(BOOL)enable 
                                   format:(V2TXLiveAudioFrameObserverFormat *)format;
```

## enableAudioPlayoutObserver
> **适用平台：** Android / iOS

**Parameters**:
- `enable`: Enable or disable the callback (default: false)
- `format`: Audio frame callback format

**Technical Details**:
- Audio frame duration is fixed at 0.02s
- PCM format audio data
- Callback provides mixed audio data for playback

```java
// Android
public static void enableAudioPlayoutObserver(boolean enable, V2TXLiveDef.V2TXLiveAudioFrameObserverFormat format)
```
```objectivec
// iOS
+ (V2TXLiveCode)enableAudioPlayoutObserver:(BOOL)enable 
                                  format:(V2TXLiveAudioFrameObserverFormat *)format;
```

## enableVoiceEarMonitorObserver
> **适用平台：** Android / iOS

**Parameters**:
- `enable`: Enable or disable the callback (default: false)

```java
// Android
public static void enableVoiceEarMonitorObserver(boolean enable)
```
```objectivec
// iOS
+ (V2TXLiveCode)enableVoiceEarMonitorObserver:(BOOL)enable;
```

## setUserId
> **适用平台：** Android / iOS

**Parameters**:
- `userId`: User or device ID managed by your application logic

**When to Call**: Set during app startup

```java
// Android
public static void setUserId(String userId)
```
```objectivec
// iOS
+ (void)setUserId:(NSString *)userId;
```

## callExperimentalAPI
> **适用平台：** Android / iOS

**Parameters**:
- `jsonStr`: JSON string describing the API and its parameters

**Return Values**:
- `V2TXLIVE_OK`: Success
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: Invalid parameter

```java
// Android
public static int callExperimentalAPI(String jsonStr)
```
```objectivec
// iOS
+ (V2TXLiveCode)callExperimentalAPI:(NSString *)jsonStr;
```

## Get SDK Version
> **适用平台：** Flutter

**Return Value**: SDK version string, e.g., "10.7.0.12345"

**When to Use**: No restrictions

**Typical Use Cases**:
- Checking version compatibility
- Providing version details for troubleshooting
- Reporting and monitoring

**Example**:

```dart
// Flutter
static Future<String> getSDKVersionStr() async
```

## Set Observer Callback
> **适用平台：** Flutter

**Parameter**:
- `observer`: An object that implements the V2TXLivePremierObserver protocol

**When to Use**: Set at app startup for global effect

**Notes**:
- Set the observer once during app startup
- The observer object must remain valid for the entire app lifecycle

**Example**:

```dart
// Flutter
static Future<void> setObserver(V2TXLivePremierObserver? observer) async
```

## Configure SDK Environment
> **适用平台：** Flutter

**Parameter**:
- `env`: Environment identifier. Options:
  - `"default"`: Default environment; SDK automatically selects the optimal access point
  - `"GDPR"`: GDPR-compliant environment; audio/video data does not pass through Mainland China servers

**Note**: Only call this API if you have specific requirements.

**Example**:

```dart
// Flutter
static Future<V2TXLiveCode> setEnvironment(String env) async
```

## Set License Authorization
> **适用平台：** Flutter

**Parameters**:
- `url`: License URL
- `key`: License key

**When to Use**: Call at app startup, before any streaming or playback

**Documentation**: https://cloud.tencent.com/document/product/454/34750

**Example**:

```dart
// Flutter
static Future<void> setLicence(String url, String key) async
```

## Call Experimental API
> **适用平台：** Flutter

**Parameter**:
- `jsonStr`: JSON string describing the API and its parameters

**Return Value**:
- `V2TXLIVE_OK`: Success
- `V2TXLIVE_ERROR_INVALID_PARAMETER`: Invalid parameter

**Example**:

```dart
// Flutter
static Future<V2TXLiveCode> callExperimentalAPI(String jsonStr) async
```

## SDK Initialization Example
> **适用平台：** Flutter

#### Experimental API Example

## License Authorization Management
> **适用平台：** Flutter

- **Required**: Call `setLicence()` before using the SDK; otherwise, features will be restricted
- **Timing**: Set during app startup, before any streaming or playback
- **Error Handling**: Monitor authorization status with the `onLicenceLoaded` callback

## Callback Registration Timing
> **适用平台：** Flutter

- **Early Registration**: Register observer callbacks before SDK initialization
- **Single Registration**: Set only once at app startup
- **Lifecycle**: The observer object must remain valid throughout the app lifecycle

## Thread Safety
> **适用平台：** Flutter

- **Main Thread Execution**: All callbacks in Flutter run on the UI main thread
- **No Thread Handling Required**: No extra thread synchronization needed
- **Async Safety**: Ensure asynchronous operations are handled correctly

## Memory Management
> **适用平台：** Flutter

- **Timely Removal**: Remove observers when no longer needed
- **Avoid Cyclic References**: Do not hold strong references to the SDK in callbacks
- **Resource Release**: Release all resources when the app exits

## Log Configuration
> **适用平台：** Flutter

- **Debug Phase**: Enable detailed logs for troubleshooting
- **Production Environment**: Reduce log output for better performance
- **Custom Handling**: Use the `onLog` callback for custom log processing

## Environment Configuration
> **适用平台：** Flutter

- **Default Environment**: Use unless you have special requirements
- **GDPR Environment**: Use only if GDPR compliance is required
- **Proxy Settings**: Configure only if proxy access is needed

## License Setup Best Practices
> **适用平台：** Flutter

## V2TXLivePremierObserver 回调接口
| 回调 | 描述 | 平台 |
|---|---|---|
| onLog | Custom Log Callback | Android / iOS |
| onLicenceLoaded | License Load Callback | Android / iOS |
| onCaptureAudioFrame | Local Microphone Audio Callback | Android / iOS |
| onPlayoutAudioFrame | Mixed Audio Playback Callback | Android / iOS |
| onVoiceEarMonitorAudioFrame | Ear Monitor Audio Callback | Android / iOS |
| Log Output Callback | Log Output Callback | Flutter |
| License Load Callback | License Load Callback | Flutter |

## onLog
> **适用平台：** Android / iOS

**Parameters**:
- `level`: Log level
- `log`: Log message

**Typical Use Cases**:
- Custom log processing
- Redirect logs to your own logging system
- Log filtering and formatting

```java
// Android
public void onLog(int level, String log)
```
```objectivec
// iOS
- (void)onLog:(V2TXLiveLogLevel)level log:(NSString *)log;
```

## onLicenceLoaded
> **适用平台：** Android / iOS

**Parameters**:
- `result`: Result code (0 for success, negative values indicate failure)
- `reason`: Reason for failure

```java
// Android
public void onLicenceLoaded(int result, String reason)
```
```objectivec
// iOS
- (void)onLicenceLoaded:(int)result Reason:(NSString *)reason;
```

## onCaptureAudioFrame
> **适用平台：** Android / iOS

**Parameters**:
- `frame`: Audio data frame

**Important Notes**:
- Avoid time-consuming operations in this callback
- Audio data can be modified
- Frame duration is fixed at 0.02s
- Does not include background music, sound effects, or other pre-processing
- Ultra-low latency

```java
// Android
public void onCaptureAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objectivec
// iOS
- (void)onCaptureAudioFrame:(V2TXLiveAudioFrame *)frame;
```

## onPlayoutAudioFrame
> **适用平台：** Android / iOS

**Parameters**:
- `frame`: PCM format audio data frame

**Important Notes**:
- Frame duration is fixed at 0.02s
- Data is readable and writable; ensure timely processing
- Does not include ear monitor audio data

```java
// Android
public void onPlayoutAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objectivec
// iOS
- (void)onPlayoutAudioFrame:(V2TXLiveAudioFrame *)frame;
```

## onVoiceEarMonitorAudioFrame
> **适用平台：** Android / iOS

**Parameters**:
- `frame`: PCM format audio data frame

**Important Notes**:
- Frame duration may vary
- Data is readable and writable; ensure timely processing

```java
// Android
public void onVoiceEarMonitorAudioFrame(V2TXLiveDef.V2TXLiveAudioFrame frame)
```
```objectivec
// iOS
- (void)onVoiceEarMonitorAudioFrame:(V2TXLiveAudioFrame *)frame;
```

## Log Output Callback
> **适用平台：** Flutter

**Parameters**:
- `type`: Callback type, should be `V2TXLivePremierObserverType.onLog`
- `params`: Contains `level` and `log`

**Use Cases**:
- Custom log processing
- Redirect logs to external systems
- Log filtering and formatting

**Example**:

```dart
// Flutter
void Function(V2TXLivePremierObserverType type, Map<String, dynamic>? params)
```

## License Load Callback
> **适用平台：** Flutter

**Parameters**:
- `type`: Callback type, should be `V2TXLivePremierObserverType.onLicenceLoaded`
- `params`: Contains `result` and `reason`

**Example**:

```dart
// Flutter
void Function(V2TXLivePremierObserverType type, Map<String, dynamic>? params)
```
