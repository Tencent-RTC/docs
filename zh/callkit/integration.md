> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, React, Vue**

本文将介绍如何快速接入 TUICallKit 组件。您可以在 10 分钟内完成以下关键步骤，最终获得一个功能完备的音视频通话界面。

## 快速接入
### 步骤1：导入组件

在 [GitHub](https://github.com/Tencent-RTC/TUIKit_Android) 中克隆或下载代码，然后将 `TUIKit_Android` 目录下的 `atomic_x` 和 `call` 目录下的 `tuicallkit-kt` 子目录复制到您当前工程的 app 同级目录中，如下图所示。

### 步骤2：工程配置
1. 在工程根目录下找到`settings.gradle.kts`（或 `settings.gradle )`文件，增加如下代码，导入`tuicallkit-kt`、`atomic_x` 组件到项目中。

   

【setting.gradle.kts】
``` java
include(":tuicallkit-kt")
include(":atomic_x")
```

【settings.gradle】
``` java
include ':tuicallkit-kt'
include ':atomic_x'
```

2. 在 app 目录下找到 `build.gradle.kts(或build.gradle)` 文件，在`dependencies` 中增加如下代码，声明当前 app 对组件的依赖。

   

【build.gradle.kts】
``` java
dependencies {
    api(project(":tuicallkit-kt"))
}
```

【build.gradle】
``` java
dependencies {
    api project(':tuicallkit-kt')
}
```
   

   > **说明：**
   > 

   > TUICallKit 工程内部已经默认依赖：`TRTC SDK`、`IM SDK`、`tuicallengine` 以及公共库 `tuicore`，不需要开发者单独配置。如需进行版本升级，则修改`tuicallkit-kt/build.gradle`文件中的版本号即可。
   > 

3. 由于 SDK 内部使用了 Java 反射特性，需要将部分类加入不混淆名单。请在 app 目录下的 `proguard-rules.pro` 文件末尾添加如下代码。添加完后，点击右上角的 **Sync Now**，同步代码。

   ``` java
   -keep class com.tencent.** { *; }
   ```
4. 在 app 目录下找到`AndroidManifest.xml` 文件，在 application 节点中添加 `tools:replace="android:allowBackup"`，覆盖组件内的设置，使用自己的设置。

   ``` java
     // app/src/main/AndroidManifest.xml
      <application
       android:name=".BaseApplication"
       android:allowBackup="false"
       android:icon="@drawable/app_ic_launcher"
       android:label="@string/app_name"
       android:largeHeap="true"
       android:theme="@style/AppTheme"
       tools:replace="android:allowBackup">
   ```   

   > **说明：**
   > 

   > 如果您集成 tuicallkit-kt 后报如下错误：
   > 
   

   > 原因是 AGP 8.0+ 要求 Gradle 运行时使用 Java 17 或更高版本。您可以在 Android Studio 中点击 Tools -> SDK Manager -> Build, Execution,Deployment -> Build Tools -> Gradle 配置 Gradle JDK 版本为 17 解决该问题
   > 
   

### 步骤3：登录

在您的项目中添加如下代码，调用 `login` 方法实现 TUI 组件的登录。这一步骤至关重要，只有在成功登录之后，您才能正常使用 TUICallKit 提供的各项功能。

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuicore.TUILogin
import com.tencent.qcloud.tuicore.interfaces.TUICallback
import com.tencent.qcloud.tuikit.tuicallkit.debug.GenerateTestUserSig

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        val userId = "denny"         // 请替换为您的UserId
        val sdkAppId = 0             // 请替换为在控制台得到的SDKAppID
        val secretKey = "****"       // 请替换为在控制台得到的SecretKey
        val userSig = GenerateTestUserSig.genTestUserSig(userId, sdkAppId, secretKey) 
        TUILogin.login(this, sdkAppId, userId, userSig, object : TUICallback() {
            override fun onSuccess() {
            }
            override fun onError(errorCode: Int, errorMessage: String) {
            }
         }) 
    }
}
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuicore.TUILogin;
import com.tencent.qcloud.tuicore.interfaces.TUICallback;
import com.tencent.qcloud.tuikit.tuicallkit.debug.GenerateTestUserSig;

public class MainActivity extends AppCompatActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        String userId = "denny";     // 请替换为您的UserId
        int sdkAppId = 0;            // 请替换为第一步在控制台得到的SDKAppID
        String secretKey = "****";   // 请替换为第一步在控制台得到的SecretKey
        String userSig = GenerateTestUserSig.genTestUserSig(userId, sdkAppId, secretKey);
        TUILogin.login(this, sdkAppId, userId, userSig, new TUICallback() {
            @Override
            public void onSuccess() {
            }
            
            @Override
            public void onError(int errorCode, String errorMessage) {
            }
        });
    }
}

```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| userId | String | 只允许包含大小写英文字母(a-z A-Z)、数字(0-9)及下划线和连词符。 |
| sdkAppId | int | 在 [实时音视频 TRTC 控制台](https://console.cloud.tencent.com/trtc) 创建的音视频应用的唯一标识 SDKAppID。 |
| secretKey | String | 在 [实时音视频 TRTC 控制台](https://console.cloud.tencent.com/trtc) 创建的音视频应用的 SDKSecretKey。 |
| userSig | String | 一种安全保护签名，用于对用户进行登录鉴权认证，确认用户是否真实，阻止恶意攻击者盗用您的云服务使用权。 |

> **说明：**
> - **开发环境**：如果您在本地开发调试阶段，可以采用本地 [GenerateTestUserSig.genTestUserSig](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/application/debug/src/main/java/com/tencent/qcloud/tuikit/debug/GenerateTestUserSig.java) 函数生成 userSig。该方法中 secretKey 很容易被反编译逆向破解，一旦您的密钥泄露，攻击者就可以盗用您的腾讯云流量。
> - **生产环境**：如果您的项目要发布上线，请采用 服务端生成 UserSig 的方式。

### 步骤4：设置昵称和头像[可选]

首次登录的用户没有头像和昵称信息，您可以通过 `setSelfInfo` 接口设置头像和昵称：

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

val name = "mike" // 目标用户的昵称。
val avatar = "https:/****/user_avatar.png" // 目标用户的头像。
TUICallKit.createInstance(applicationContext).setSelfInfo(name, avatar, 
    object : CompletionHandler {
         override fun onSuccess() {
             // 设置成功
         }
         override fun onFailure(code: Int, desc: String) {
             // 设置失败
         }
     })
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

String name = "mike"; // 目标用户的昵称。
String avatar = "https:/****/user_avatar.png"; // 目标用户的头像。
TUICallKit.createInstance(applicationContext).setSelfInfo(name, avatar, 
    new CompletionHandler() {
        @Override
        public void onSuccess() {
            // 设置成功
        }

        @Override
        public void onFailure(int code, String desc) {
            // 设置失败
        }
    });
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| nickname | String | 目标用户的昵称。 |
| avatar | String | 目标用户的头像。 |
| completion | CompletionHandler | 异步操作的结果回调。 |

### 步骤5：发起通话

拨打方可以通过调用 `calls` 函数，并指定通话类型和被叫方的 userID，来发起语音或视频通话。`calls` 接口同时支持一对一通话和多人通话。当 userIDList 中包含一个 userID 时，为一对一通话；当 userIDList 包含多个 userID 时，则为多人通话。

【Kotlin】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit
import io.trtc.tuikit.atomicxcore.api.call.CallMediaType
import io.trtc.tuikit.atomicxcore.api.call.CallParams

val userIdList = mutableListOf<String>()
userIdList.add("mike")
val mediaType = CallMediaType.Audio
val callParams = CallParams()
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, object : CompletionHandler {
    override fun onFailure(code: Int, desc: String) {
       // 失败回调
    }
    override fun onSuccess() {
       // 成功回调
    }
})
```

【Java】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;
import io.trtc.tuikit.atomicxcore.api.call.CallMediaType;
import io.trtc.tuikit.atomicxcore.api.call.CallParams;

List<String> userIdList = new ArrayList<>();
userIdList.add("mike");
CallMediaType mediaType = CallMediaType.Audio;
CallParams params = new CallParams();
TUICallKit.createInstance(this).calls(userIdList, mediaType, params, new CompletionHandler() {
    @Override
    public void onFailure(int code, String desc) {
        // 失败回调
    }
    @Override
    public void onSuccess() {
        // 成功回调
    }
});

```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| userIdList | List<String> | 目标用户的 userId 列表。 |
| mediaType | CallMediaType | 通话的媒体类型，例如视频通话、语音通话。 - Audio : 语音通话。 - Video : 视频通话。 |
| params | CallParams | 通话扩展参数，例如房间号、通话邀请超时时间等。 - roomId : 此次通话的房间 ID。 - timeout : 发起通话等待的最长时间，超过该时间通话结束。 - userData : 发起通话时自定义扩展字段。 - chatGroupId : 与 chat 结合使用，Chat 群组的群 ID 。 |
| completion | CompletionHandler | 异步操作的结果回调。 |

### 步骤6：接听通话

接听端完成登录后，拨打端发起通话，接收端就可以收到通话邀请，同时伴随铃声和振动。

## Platform: Android

## 准备工作

### **环境要求**
- **Android 版本要求**：Android 5.0（SDK API Level 21）及以上版本。

- **Gradle 版本要求：**Gradle 4.2.1 及以上的版本。

- **设备需满足**：Android 5.0 及以上的手机设备。

### **开通服务**

在使用腾讯云提供的音视频服务前，您需要前往控制台，为应用开通音视频服务。具体步骤参见 开通服务。开通服务后，请记录`SDKAppID` 和`SDKSecretKey`，在后续的步骤（登录）中会用到。

## 更多功能

### AI 转录与翻译

您可以通过调用 `enableAITranscriber` 开启/关闭 AI 转录与翻译功能。

> **注意：**
> 1. 该功能默认为开启状态（enable = true）。如需关闭，请在发起通话前调用 `enableAITranscriber`(enable: false)。
> 2. AI 实时转录与翻译为增值服务。体验版赠送免费额度，可直接体验。如需开通正式版，请在 [实时音视频控制台](https://console.cloud.tencent.com/trtc) 开通套餐包，详见 [计费说明](https://cloud.tencent.com/document/product/647/111976)。

【Kotlin】
``` swift
TUICallKit.createInstance(context).enableAITranscriber(true)
```

【Java】
``` objectivec
TUICallKit.createInstance(context).enableAITranscriber(true);
```

**详情：**默认为 true，通话界面上显示 AI 翻译相关页面，设置为 false 后关闭。

### 开启悬浮窗功能

您可以通过调用 `enableFloatWindow` 开启/关闭悬浮窗功能，在初始化 TUICallKit 组件时启用该功能，默认状态为关闭（false）。可通过点击通话界面左上角的悬浮窗按钮，将通话界面缩小为悬浮窗形式。

【Kotlin】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

TUICallKit.createInstance(this).enableFloatWindow(true)
```

【Java】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

TUICallKit.createInstance(this).enableFloatWindow(true);
```

**详情：**默认为 false，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。

### 开启来电横幅

您可以通过调用 `enableIncomingBanner` 开启/关闭来电横幅显示，该功能默认为关闭状态（false）。当被叫端收到来电时，默认展示全屏通话等待界面。启用此功能后，将首先显示一个横幅通知，然后根据需要切换到全屏通话界面。请注意，显示来电横幅需要授予悬浮窗权限，具体的显示策略将根据权限设置及应用当前是否在前台或后台运行来决定，具体策略可参考：被叫端来电显示策略。

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

TUICallKit.createInstance(context).enableIncomingBanner(true)
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

TUICallKit.createInstance(context).enableIncomingBanner(true);
```

**详情：**默认为 false，被叫端收到邀请后默认弹出全屏通话等待界面。开启后先展示一个横幅，然后根据需要拉起全屏通话界面。

### 实现多人通话

主叫方使用 `calls` 方法发起通话时，若被叫用户列表超过一人，则自动视为群组通话。其他成员可通过 `join` 方法加入该多人通话。
- **发起多人通话：**使用 `calls` 方法发起通话时，若被叫用户列表（`userIdList`）超过一人，则自动视为群组通话。

   

【Kotlin】
``` java
import com.tencent.cloud.tuikit.engine.call.TUICallDefine
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit
import io.trtc.tuikit.atomicxcore.api.call.CallMediaType
import io.trtc.tuikit.atomicxcore.api.call.CallParams

val userIdList = mutableListOf<String>()
userIdList.add("mike")
userIdList.add("tate")
val mediaType = CallMediaType.Audio
val params = CallParams()
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, object : CompletionHandler {
    override fun onFailure(code: Int, desc: String) {
       // 失败回调
    }
    override fun onSuccess() {
       // 成功回调
    }
})
```

【Java】
``` java
import com.tencent.cloud.tuikit.engine.call.TUICallDefine;
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;
import io.trtc.tuikit.atomicxcore.api.call.CallMediaType;
import io.trtc.tuikit.atomicxcore.api.call.CallParams;

List<String> userIdList = new ArrayList<>();
userIdList.add("mike");
userIdList.add("tate");
CallMediaType mediaType = CallMediaType.Audio;
CallParams params = new CallParams();
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, new CompletionHandler() {
    @Override
    public void onFailure(int code, String desc) {
        // 失败回调
    }

    @Override
    public void onSuccess() {
        // 成功回调
    }
});
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| userIdList | List<String> | 目标用户的 userId 列表。 |
| mediaType | CallMediaType | 通话的媒体类型，例如视频通话、语音通话。 - Audio : 语音通话。 - Video : 视频通话。 |
| params | CallParams | 通话扩展参数，例如房间号、通话邀请超时时间等。 - roomId : 此次通话的房间 ID。 - timeout : 发起通话等待的最长时间，超过该时间通话结束。 - userData : 发起通话时自定义扩展字段。 - chatGroupId : 与 chat 结合使用，Chat 群组的群 ID 。 |
| completion | CompletionHandler | 异步操作的结果回调。 |

- **加入多人通话：**可使用 `join` 方法加入指定的群组通话。

   

【Kotlin】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

val callId = "123456" // 此次通话的 ID
TUICallKit.createInstance(this).join(callId, object : CompletionHandler {
    override fun onFailure(code: Int, desc: String) {
      // 失败回调
    }
    override fun onSuccess() {
      // 成功回调
    }
})
```

【Java】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

String callId = "123456"; // 此次通话的 ID
TUICallKit.createInstance(this).join(callId, new CompletionHandler() {
    @Override
    public void onFailure(int code, String desc) {
        // 失败回调
    }

    @Override
    public void onSuccess() {
        // 成功回调
    }
});
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| callId | String | 此次通话的唯一标识。 |
| completion | CompletionHandler | 异步操作的结果回调。 |

### 设置语言
- **支持的语言：**目前支持**简体中文、繁体中文、英文、日文和阿拉伯语。**

- **切换语言：**TUICallKit 默认语言与手机系统保持一致 。如果需要切换语言，可以使用 `TUIThemeManager.getInstance().changeLanguage `方法设置语言，以设置语言为英文的示例代码如下**。**

   

【Kotlin】
``` java
import com.tencent.qcloud.tuicore.TUIThemeManager;

public class MainActivity extends BaseActivity {
    @Override
  public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState)
      
      val language = TUIThemeManager.LANGUAGE_ZH_CN // 设置的语言
      TUIThemeManager.getInstance().changeLanguage(applicationContext, language)
    }
}
```

【Java】
``` java

import com.tencent.qcloud.tuicore.TUIThemeManager;

public class MainActivity extends BaseActivity {
    @Override
  public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      String language = TUIThemeManager.LANGUAGE_EN; // 设置的语言
      TUIThemeManager.getInstance().changeLanguage(getApplicationContext(), language);
    }
}

```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| language | String | 设置的语言： - TUIThemeManager.LANGUAGE_EN：英文。 - TUIThemeManager.LANGUAGE_AR：阿拉伯语。 - TUIThemeManager.LANGUAGE_ZH_HK：中文繁体。 - TUIThemeManager.LANGUAGE_ZH_CN：中文简体。 |

> **说明：**
> 

> 如需设置其他语言，请[联系我们](https://cloud.tencent.com/document/product/647/19906)获取协助。
> 

### 设置铃声

您可通过以下方式设置默认铃声、来电静音模式、离线推送铃声：
- **设置默认铃声（方式1）：**如果您通过源码依赖 TUICallKit 组件，您可以替换铃声资源文件（[发起呼叫时的铃声](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/call/tuicallkit-kt/src/main/res/raw/phone_dialing.mp3)、[接到呼叫时的铃声](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/call/tuicallkit-kt/src/main/res/raw/phone_ringing.mp3)）设置默认铃声。

- **设置默认铃声（方式2）：**通过 [setCallingBell](https://cloud.tencent.com/document/product/647/78750#setCallingBell) 接口设置被叫端收到的来电铃声。

   

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

val filePath = "***/callingBell.mp3"
TUICallKit.createInstance(context).setCallingBell(filePath)
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

String filePath = "***/callingBell.mp3";
TUICallKit.createInstance(context).setCallingBell(filePath);
```

**详情：**这里仅限传入本地文件地址，需要确保该文件目录是应用可以访问的。铃声设置后与设备绑定，更换用户，铃声依旧会生效。如需恢复默认铃声，filePath传空即可。
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| filePath | String | 铃声文件的路径。 |

- **来电静音模式：**您可以通过 [enableMuteMode](https://cloud.tencent.com/document/product/647/78750#enableMuteMode) 设置静音模式。

   

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

TUICallKit.createInstance(context).enableMuteMode(true)
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

TUICallKit.createInstance(context).enableMuteMode(true);
```

**详情：**开启后，收到通话请求，不会播放来电铃声。

- **自定义离线推送铃声**：离线推送默认铃声是由系统决定的，可以在 手机设置 中，进入应用详情，查看或更改应用的通知铃声。

## 自定义您的 UI

### 替换图标按钮

您可以直接替换 [res\drawable-xxhdpi](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/atomic_x/src/main/res-callview/drawable-xxhdpi) 文件夹下的图标，以确保整个 App 中的图标色调风格保持一致。以下列出了基本的功能按钮，您可以替换对应的图标以适配自己的业务场景。

常用图标文件名列表
| 图标 | 文件名称 | 说明 |
| --- | --- | --- |
|  | callview_ic_dialing.png | 接听来电图标 |
|  | callview_ic_hangup.png | 挂断通话图标 |
|  | callview_ic_mic_unmute.png | 关闭麦克风图标 |
|  | callview_ic_handsfree_disable.png | 关闭扬声器图标 |
|  | callview_ic_camera_disable.png | 关闭摄像头图标 |
|  | callview_ic_add_user_black.png | 通话过程中邀请用户图标 |

## 交流与反馈

如果您在使用过程中，有什么建议或者意见，可以 [联系我们](https://cloud.tencent.com/document/product/647/19906)，感谢您的反馈。

---

## Platform: iOS

## 环境准备
- Xcode 13 及以上。

- iOS 13.0 及以上。

- CocoaPods 环境安装，单击 [查看](https://guides.cocoapods.org/using/getting-started.html)。

- 如果您的接入和使用中遇到问题，请参见 常见问题。

## 步骤一：开通服务

请参见 开通服务，获取 `SDKAppID、SDKSecretKey`，他们将在 步骤四：初始化 TUICallKit 组件 作为**必填参数**使用。

## 步骤二：导入组件

使用 CocoaPods 导入组件，如果您遇到问题，请先参见 环境准备。导入组件具体骤如下：
1. 请在您的 `Podfile` 文件中添加 `pod 'TUICallKit_Swift'` 依赖，建议指定`Subspec`为`Professional`，如果您遇到任何问题，请参见 [Example](https://github.com/Tencent-RTC/TUICallKit/blob/main/iOS/Example/Podfile) 工程。

   ``` bash
   target 'xxxx' do
     ...
     pod 'TUICallKit_Swift/Professional'
   end
   ```   

   > **说明：**
   > 

   > 如果您的项目中缺少`Podfile` 文件，您需要在终端中`cd` 到`xxxx.xcodeproj`目录，然后，通过执行以下命令来创建`Podfile`文件：
   > 
   > `pod init`

2. 在终端中，首先`cd`到`Podfile`目录下，然后执行以下命令，安装组件。

   ``` bash
   pod install
   ```   

   > **说明：**
   > 

   > 如果无法安装 TUICallKit 最新版本，可以先删除 **Podfile.lock **和 **Pods**。然后执行以下命令更新本地的 CocoaPods 仓库列表。
   > 
   > `pod repo update`

   > 之后执行以下命令，更新组件库的 Pod 版本。
   > 
   > `pod update`

3. 建议您编译并运行一次。如果遇到问题，可以参见我们的 常见问题。如果问题仍未解决，您可以尝试运行我们的 [Example](https://github.com/Tencent-RTC/TUICallKit/tree/main/iOS/Example) 工程。在接入和使用过程中，如果遇到问题，欢迎向我们 [反馈](https://github.com/Tencent-RTC/TUICallKit/issues)。

## 步骤三：工程配置

使用音视频功能，需要授权摄像头和麦克风的使用权限，请根据实际项目需要，设置项目所需权限。
1. 在 **Xcode** 中，选择 **TARGETS** > **Info** > **Custom iOS Target Properties **菜单。

   

2. 单击 **+**，添加摄像头和麦克风权限。

- `Privacy - Camera Usage Description`

- `Privacy - Microphone Usage Description`

   

   

## 步骤四：登录 TUI 组件

   在您的项目中添加如下代码，它的作用是通过调用 TUICore 中的相关接口完成 TUI 组件的登录。这一步骤至关重要，只有在成功登录之后，您才能正常使用 TUICallKit 提供的各项功能。

   

【Swift】
``` swift
import TUICore
import TUICallKit_Swift

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
    let userID = "denny"       // 请替换为您的 UserId
    let sdkAppID: Int32 = 0    // 请替换为第一步在控制台得到的 SDKAppID
    let secretKey = "****"     // 请替换为第一步在控制台得到的 SecretKey

    let userSig = GenerateTestUserSig.genTestUserSig(userID: userID, sdkAppID: sdkAppID, secretKey: secretKey)

    TUILogin.login(sdkAppID, userID: userID, userSig: userSig) {
      print("login success")
    } fail: { code, message in
      print("login failed, code: \(code), error: \(message ?? "nil")")
    }

    return true
}
```

【Objective-C】
``` objectivec
#import <TUICore/TUILogin.h>
#import <TUICallKit_Swift/TUICallKit_Swift-Swift.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    
    NSString *userID = @"denny";     // 请替换为您的 UserId
    int sdkAppID = 0;                // 请替换为第一步在控制台得到的 SDKAppID
    NSString *secretKey = @"****";   // 请替换为第一步在控制台得到的 SecretKey

    NSString *userSig = [GenerateTestUserSig genTestUserSigWithUserID:userID sdkAppID:sdkAppID secretKey:secretKey];

    [TUILogin login:sdkAppID
             userID:userID
            userSig:userSig
               succ:^{
        NSLog(@"login success");
    } fail:^(int code, NSString * _Nullable msg) {
        NSLog(@"login failed, code: %d, error: %@", code, msg);
    }];
    
    return YES;
}
```

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| userID | String | 客户根据自己的业务自定义用户 ID，只允许包含大小写英文字母(a-z A-Z)、数字(0-9)及下划线和连词符。 |
| sdkAppID | Int32 | 在 [实时音视频 TRTC 控制台](https://console.cloud.tencent.com/trtc) 创建的音视频应用的唯一标识 SDKAppID。 |
| secretKey | String | 在 [实时音视频 TRTC 控制台](https://console.cloud.tencent.com/trtc) 创建的音视频应用的 SDKSecretKey。 |
| userSig | String | 一种安全保护签名，用于对用户进行登录鉴权认证，确认用户是否真实，阻止恶意攻击者盗用您的云服务使用权。 |
   

   > **注意：**
   > 
>   - **开发环境**：如果您处于本地开发调试阶段，可以使用本地 GenerateTestUserSig.genTestSig 函数生成 userSig。但请注意，该方法中的 secretKey 容易被反编译逆向破解。一旦密钥泄露，攻击者可能盗用您的腾讯云流量。
>   - **生产环境**：如果您的项目要发布上线，请采用 服务端生成 UserSig 的方式。

## 步骤五：拨打您的第一通电话

通过调用 TUICallKit 的 calls 函数并指定通话类型和被叫方的 userId，就可以发起语音或者视频通话。

【Swift】
``` swift
import TUICallKit_Swift
import RTCRoomEngine

// 发起1对1语音通话(假设 userId 为 mike)
TUICallKit.createInstance().calls(userIdList: ["mike"], callMediaType: .audio, params: nil) {

} fail: { code, message in
 
}
```

【Objective-C】
``` objectivec
#import <TUICallKit_Swift/TUICallKit_Swift-Swift.h>
#import <RTCRoomEngine/TUICallEngine.h>

// 发起1对1语音通话(假设 userId 为 mike)
[[TUICallKit createInstance] calls:@[@"mike"] callMediaType:TUICallMediaTypeAudio params:NULL succ:^{
} fail:^(int code, NSString * _Nullable errMsg) {
}];
```
| 主叫方 | 被叫方 |
| --- | --- |
|  |  |

## 更多特性
- 界面定制

- 离线推送

- 多人通话

- 悬浮窗

- 美颜特效

- 自定义铃声

- 监听通话状态

- 云端录制

## 交流与反馈
- 如果您在使用过程中，有什么建议或者意见，可以在这里反馈：TUICallKit 产品反馈问卷，感谢您的反馈。

- 如果您是开发者，也欢迎您加入我们的 TUICallKit 技术交流平台 zhiliao，进行技术交流和产品沟通。

---

## Platform: Flutter

## 准备工作

### 环境要求

Flutter 3.24.0、Dart 3.5.0 及以上版本。

### 开通服务

在使用腾讯云提供的音视频服务前，您需要前往控制台，为应用开通音视频服务。具体步骤详细参见 开通服务。开通服务后，请记录`SDKAppID` 和`SDKSecretKey`，在后续的步骤（登录）中会用到。

## 更多功能

### 开启悬浮窗功能

您可以通过调用 enableFloatWindow 开启/关闭悬浮窗功能，在初始化 TUICallKit 组件时启用该功能，默认状态为关闭（false）。可通过点击通话界面左上角的悬浮窗按钮，将通话界面缩小为悬浮窗形式。

``` java
import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
......

void _enableFloatWindow() {
    TUICallKit.instance.enableFloatWindow(true);
}
```

**详情：**默认为 false，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。

### 开启来电横幅

您可以通过调用 `enableIncomingBanner` 开启/关闭来电横幅显示，该功能默认为关闭状态（false）。当被叫端收到来电时，默认展示全屏通话等待界面。启用此功能后，将首先显示一个横幅通知，然后根据需要切换到全屏通话界面。请注意，显示来电横幅需要授予悬浮窗权限，具体的显示策略将根据权限设置及应用当前是否在前台或后台运行来决定。

``` java
import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
......

void _enableFloatWindow() {
    TUICallKit.instance.enableIncomingBanner(true);
}
```

**详情：**默认为 false，被叫端收到邀请后默认弹出全屏通话等待界面。开启后先展示一个横幅，然后根据需要拉起全屏通话界面。

### 多人通话

主叫方使用 `calls` 方法发起通话时，若被叫用户列表超过一人，则自动视为群组通话。其他成员可通过 `join` 方法加入该多人通话。

**发起多人通话：**使用 `calls` 方法发起通话时，若被叫用户列表（`userIdList`）超过一人，则自动视为群组通话。

``` java

import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
......

void _call() {
    List<String> participantIds = ['vince','mike'];
    CallMediaType mediaType = CallMediaType.audio;
    CallParams callParams = CallParams();    
    TUICallKit.instance.calls(participantIds, mediaType, callParams);
}
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| userIdList | List<String> | 目标用户的 userId 列表。 |
| mediaType | CallMediaType | 通话的媒体类型，例如视频通话、语音通话。 |
| params | CallParams | 通话扩展参数，例如房间号、通话邀请超时时间，离线推送自定义内容等。 |

**加入多人通话：**可使用 `join` 方法加入指定的群组通话。

``` java
import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';

void join() {
    String callId = "123456";
    TUICallKit.instance.join(callId);
}
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| callId | String | 此次通话的唯一标识。 |

### 语言设置
- **支持的语言：**目前支持**简体中文、英文和日文，**默认语言为**英文 。**

- **切换语言：**TUICallKit 不提供单独的语言切换接口。其界面语言将自动跟随当前应用程序根组件（如 MaterialApp 或 CupertinoApp）所使用的语言设置而切换。 您只需更改应用根组件的语言配置即可实现通话界面的语言切换。
   

   > **说明：**
   > 

   > 如需设置其他语言，请[联系我们](https://cloud.tencent.com/document/product/647/19906)获取协助。
   > 

### 铃声设置

您可通过以下方式设置默认铃声、来电静音模式：
- **设置默认铃声：**如果您通过源码依赖 TUICallKit 组件，您可以替换铃声资源文件（[发起呼叫时的铃音](https://github.com/Tencent-RTC/TUICallKit/blob/main/Flutter/assets/audios/phone_dialing.mp3)、[接到呼叫时的铃音](https://github.com/Tencent-RTC/TUICallKit/blob/main/Flutter/assets/audios/phone_ringing.mp3)）设置默认铃声。

- **来电静音模式：**您可以通过 enableMuteMode 设置静音模式。

   
``` java
import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
......

void _setCallingBell() {
    TUICallKit.instance.enableMuteMode(true);
}
```

**详情：**开启后，收到通话请求，不会播放来电铃声。

## 自定义您的 UI 

### 替换图标按钮

您可以直接替换 [assets/images](https://github.com/Tencent-RTC/TUICallKit/tree/main/Flutter/assets/images) 文件夹下的图标，以确保整个 App 中的图标色调风格保持一致。以下列出了基本的功能按钮，您可以替换对应的图标以适配自己的业务场景。

常用图标文件名列表
| 图标 | 文件名称 | 说明 |
| --- | --- | --- |
|  | dialing.png | 接听来电图标 |
|  | hangup.png | 挂断通话图标 |
|  | mute_on.png | 关闭麦克风图标 |
|  | handsfree.png | 关闭扬声器图标 |
|  | camera_off.png | 关闭摄像头图标 |
|  | add_user.png | 通话过程中邀请用户图标 |

## 交流与反馈

如果您在使用过程中，有任何意见或建议，请[联系我们](https://cloud.tencent.com/document/product/647/19906)。

---

## Platform: React

## 1 集成 TUICallKit

### 1.1 step1：create react project

if you don't have a react project, you can use vite create a react project. otherwise, you can skip this step.

requirement: react: 18.2.0, nodejs: ^22.0.0.

if it is a new project, guide the user to use vite to create a react project manually, and then continue with step 1.2. if it is an existing project, skip this step and continue with step 1.2.

```bash
pnpm create vite@latest
# skip some settings
cd ${your-vue-project-name}
pnpm install
```

### 1.2 step2: install calls-uikit-react

```bash
npm install @trtc/calls-uikit-react@latest --legacy-peer-deps
```

### 1.3 step3: add main content to your project

```tsx
import { useState } from 'react';
import { TUICallKit, TUICallKitAPI, CallMediaType } from "@trtc/calls-uikit-react";

return (
  <>
    <span> caller's ID: </span>
    <input type="text" placeholder='input caller userID' value={callerUserID} onChange={(event) => setCallerUserID(event.target.value)} />
    <button onClick={init}> step1. init </button> <br />
    <span> callee's ID: </span>
    <input type="text" placeholder='input callee userID' value={calleeUserID} onChange={(event) => setCalleeUserID(event.target.value)} />
    <button onClick={call}> step2. call </button>
    <TUICallKit
      style={{ 
        position: 'fixed', 
        top: '50%', 
        left: '50%', 
        transform: 'translate(-50%, -50%)',
        width: '960px',
        minHeight: '640px',
        zIndex: 1000
      }}
    />
  </>
);

const [callerUserID, setCallerUserID] = useState('');
const [calleeUserID, setCalleeUserID] = useState('');
const SDKAppID = 0;  // TODO: Replace with your SDKAppID (Notice: SDKAppID is of type number）
const userSig = ''; // TODO: Replace with your userSig

// init TUICallKit
const init = async () => {
  await TUICallKitAPI.init({
    userID: callerUserID,
    userSig,
    SDKAppID,
  });
}
// Start a video call
const call = async () => {
  await TUICallKitAPI.calls({
    userIDList: [calleeUserID],
    type: CallMediaType.VIDEO,
  }); 
};
</script>
```

## 重要提醒
如果需要自定义实现 TUICallKit 的更多能力，请使用工具 `get_web_call_api` 获取 TUICallKit 客户端 API 文档。

---

## Platform: Vue

## 1 集成 TUICallKit

### 1.1 step1：create vue3 project

if you don't have a vue3 project, you can use vite create a vue3 project. otherwise, you can skip this step.

requirement: vue3: ^3.0.0, nodejs: ^22.0.0.

if it is a new project, guide the user to use vite to create a vue3 project manually, and then continue with step 1.2. if it is an existing project, skip this step and continue with step 1.2.

```bash
pnpm create vite@latest
# skip some settings
cd ${your-vue-project-name}
pnpm install
```

### 1.2 step2: install calls-uikit-vue

use npm or pnpm to install @trtc/calls-uikit-vue@latest

### 1.3 step3: add main content to your project

```vue
<template>
  <div>
    <span> caller's ID: </span>
    <input type="text" v-model="callerUserID"> 
    <button @click="init"> step1. init </button> <br>
    <span> callee's ID: </span>
    <input type="text" v-model="calleeUserID">
    <button @click="call"> step2. call </button>
    <Teleport to="body">
      <TUICallKit style="
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%) !important;
        z-index: 1000;"
      />
    </Teleport>
  </div>
</template>
<script setup>
import { ref } from 'vue';
import { TUICallKit, TUICallKitAPI, CallMediaType } from "@trtc/calls-uikit-vue";

const callerUserID = ref('');
const calleeUserID = ref('');
const SDKAppID = 0;  // TODO: Replace with your SDKAppID (Notice: SDKAppID is of type number）
const userSig = ''; // TODO: Replace with your userSig

// init TUICallKit
const init = async () => {
  await TUICallKitAPI.init({
    userID: callerUserID.value, 
    userSig, 
    SDKAppID,
  });
}

// Start a video call
const call = async () => {
  await TUICallKitAPI.calls({
    userIDList: [calleeUserID.value],
    type: CallMediaType.VIDEO,
  }); 
};
</script>
```

## 重要提醒
如果需要自定义实现 TUICallKit 的更多能力，请使用工具 `get_web_call_api` 获取 TUICallKit 客户端 API 文档。
