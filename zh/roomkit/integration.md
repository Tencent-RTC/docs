> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

TUIRoomKit 是腾讯云提供的一站式多人音视频房间解决方案，集成了完整的 UI 组件和核心功能。通过本文档，开发者可以快速了解如何将 TUIRoomKit 集成到项目中，实现多人音视频房间功能。本文还将详细介绍如何快速替换图片资源、本地化文案等个性化配置，帮助开发者打造符合品牌特色的音视频应用。
| 房间准备页 | 标准房间主页 | 大型研讨会房间主页 | 成员管理 |
| --- | --- | --- | --- |
|  |  |  |  |

> **Vue 差异：** TUIRoomKit 是腾讯云推出的音视频房间 UI 应用。本次升级引入了全新的原子化组件接入方案，开发者可以灵活组合这些原子化组件，以构建个性化的音视频房间界面。本文档介绍如何将 TUIRoomKit 集成到现有的 Vue3 项目中，同时介绍如何对 TUIRoomKit 的样式、布局及功能进行自定义修改。- 桌面端多人会议。cn/100027337918/31045da2d73111f097cb5254005ef0f7。png)。- H5 多人会议。cn/100027337918/1d8af2f1fd3611f0b5c0525400380f7d。png)

> **注意**
> **推荐使用更高效的 AI 集成助手**
> 我们为您提供了全新的 AI 集成方式，只需要描述您的需求，即可自动生成集成代码，大幅提升开发效率。
> [点击这里，立即体验 AI 集成](https://cloud.tencent.com/document/product/647/127873)

## 前提条件

### 开通服务

请参考 [开通服务](https://cloud.tencent.com/document/product/647/104842) 领取 TUIRoomKit 体验版，并在 [应用管理](https://console.cloud.tencent.com/trtc) 页面获取以下信息:
- **SDKAppID**: 应用标识，腾讯云基于 `SDKAppID` 完成计费统计。
- **SDKSecretKey**: 应用密钥，用于初始化配置文件的密钥信息。

### 环境准备

| 平台 | 版本 |
| --- | --- |
| Flutter | 3.29.3 及以上版本。 |
| Android | 最低兼容 Android 5.0 （SDK API Level 21）及以上版本。 |
| iOS | iOS 14.0 及更高。 |

> **Android 差异：** - `Android` 5。0 (SDK API level 21)及以上。- `Gradle` 8。0 及以上。- `Android` 5。0 以上的设备。- 需使用 `JDK` 17、18 或 19 版本
> **iOS 差异：** 在开始运行 Demo 之前，请确保您的开发环境满足以下要求：。- **Xcode**：需使用 **Xcode 15 **或更高版本。- **iOS 系统**：支持 **iOS 13。0 **或更高版本的设备。- **CocoaPods 环境**：已安装 CocoaPods 环境。如果您尚未安装，请参见 [CocoaPods官网安装](https://guides.cocoapods.org/using/getting-started.html)，或按以下步骤操作：。- **使用 gem 安装 CocoaPods**：在终端中执行 `sudo gem install cocoapods` 命令进行安装。> **提示：**。>。> `sudo gem install cocoapods` 安装过程中可能需要输入电脑密码，按提示输入管理员密码即可。
> **Vue 差异：** - **Node。js**: ≥ 18。19。1 (推荐使用官方 LTS 版本)。- **Vue**: ≥ 3。4。21。- **现代浏览器**：支持 [WebRTC APIs](https://cloud.tencent.com/document/product/647/17249#.E6.94.AF.E6.8C.81.E7.9A.84.E5.B9.B3.E5.8F.B0) 的现代浏览器。- **设备**：摄像头、麦克风、扬声器

## 快速接入

### **步骤1：下载 TUIRoomKit 组件**

1. **添加 Pod 依赖：**
  - **若项目已有 Podfile 文件。**
      在您项目的 `Podfile` 文件中添加 `pod 'TUIRoomKit'` 依赖。例如：
      ``` ruby
        target 'YourProjectTarget' do  
          # 其他已有的 Pod 依赖...  
          # 添加 pod 'TUIRoomKit' 依赖
          pod 'TUIRoomKit'
        end
      ```
  - **若项目没有 Podfile 文件。**
      在终端中通过 `cd` 命令切换到您的 `.xcodeproj` 目录下，然后执行 `pod init` 命令创建 `Podfile` 文件，创建完成后，在您的 `Podfile` 文件中添加 `pod 'TUIRoomKit'` 依赖。例如：
      ``` bash
      // 如果您的项目目录是 /Users/yourusername/Projects/YourProject
      // 1. cd 到您的.xcodeproj 工程目录下
      cd /Users/yourusername/Projects/YourProject
      // 2. 执行 pod init，此命令运行完后，会在您的工程目录下生成一个 Podfile 文件。
      pod init
      // 3. 在生成的 Podfile 文件中添加 pod 'TUIRoomKit' 依赖
        target 'YourProjectTarget' do  
          # 添加 pod 'TUIRoomKit' 依赖
          pod 'TUIRoomKit'
        end
      ```
2. **安装组件**：
   在终端中 `cd` 到 `Podfile` 文件所在的目录，然后执行以下命令安装组件。
   ``` bash
   pod install
   ```
如果无法安装 `TUIRoomKit` 最新版本，请按以下步骤操作：
1. 在 Podfile 所在目录下删除 `Podfile.lock`和 `Pods`，您可以选择手动删除或终端执行以下命令：
   ``` bash
   // cd 到 Podfile 所在目录下
   rm -rf Pods/
   rm Podfile.lock
   ```
2. 在 Podfile 所在目录下执行 `pod install --repo-update`：
   ``` bash
   // cd 到 Podfile 所在目录下
   pod install --repo-update
   ```

> **Android 差异：** 在 [GitHub](https://github.com/Tencent-RTC/TUIKit_Android) 中克隆/下载代码，然后将 `room` 子目录和 `atomic_x` 子目录拷贝到您当前 `Android` 项目的 `app` 文件夹同级目录中。
> **Flutter 差异：** 在您的工程 `pubspec。yaml` 文件中，添加 [tencent_conference_uikit](https://pub.dev/packages/tencent_conference_uikit)插件依赖。执行以下命令安装组件：

**代码示例：**

**Vue（主示例）：**
``` typescript
<template>
  <ConferenceMainView v-if="isPC"></ConferenceMainView>
  <ConferenceMainViewH5 v-else></ConferenceMainViewH5>
</template>
<script setup>
import { ref } from 'vue';
import { ConferenceMainView, ConferenceMainViewH5 } from '@tencentcloud/roomkit-web-vue3';
import { getPlatform } from '@tencentcloud/universal-api';

const isPC = ref(getPlatform() === 'pc');
</script>

```

**Flutter（主示例）：**
``` yaml
dependencies:   
    tencent_conference_uikit: 最新版本
```

``` plaintext
flutter pub get
```

### 步骤2：工程配置

- 使用 **Xcode **打开您的工程，选择**项目 **> **Build Settings **> **Deployment**，将其下的 **Strip Style** 设置为 `Non-Global Symbols`，以保留所需要的全局符号信息。
- 若您需要在 **iOS **端使用音视频功能，需要授权麦克风和摄像头的使用权限（`Android`端已在 SDK 中声明相关权限，您无需手动进行相关配置）。
   在您 App 的`Info.plist`（路径：`ios/Runner/Info.plist`）中添加以下两项，分别对应麦克风和摄像头在系统弹出授权对话框时的提示信息。
   ``` xml
   <key>NSCameraUsageDescription</key>
   <string>TUIRoomKit 需要访问您的相机权限</string>
   <key>NSMicrophoneUsageDescription</key>
   <string>TUIRoomKit 需要访问您的麦克风权限</string>
   ```
   完成以上添加后，在您的 **ios/Podfile **中添加以下预处理器定义，用于启用相机与麦克风权限。
   ``` ruby
   post_install do |installer|
     installer.pods_project.targets.each do |target|
       flutter_additional_ios_build_settings(target)
         target.build_configurations.each do |config|
           config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
         '$(inherited)',
         'PERMISSION_MICROPHONE=1',
         'PERMISSION_CAMERA=1',
         ]
       end
     end
   end
   ```

> **Android 差异：** #### 1。导入 TUIRoomKit 组件。在工程根目录下的 `settings。gradle。kts` 或 `settings。gradle`文件中，增加以下代码以导入 `tuiroomkit` 组件。【settings。gradle。kts】。【settings。gradle】。#### 2。添加组件依赖。在 `app` 目录下找到 `build。gradle。kts`（或 `build。gradle`）文件，并在文件中增加以下代码，该步骤的作用是声明当前 App 对新加入的 **tuiroomkit** 组件的依赖。【build。gradle。kts】。【build。gradle】。#### 3。配置混淆规则。由于 SDK 内部使用了 Java 反射，请在 `proguard-rules。pro`文件中添加以下代码，将相关类加入不混淆名单，以确保功能正常运行。#### 4。修改 AndroidManifest。xml。为了防止编译时`AndroidManifest`合并过程中产生属性冲突，您需要在 `app/src/main/AndroidManifest。xml` 文件的 `<application>` 节点中，添加 `tools:replace="android:allowBackup"` 和 `android:allowBackup="false"`，用以覆盖组件内的设置。#### 5。完成项目同步。在您完成上述步骤后，通常情况下 Android Studio 会自动为您弹出 **Sync Now **按钮，您需要点击该同步按钮完成项目的同步，若 IDE 没有自动弹出同步按钮，请您手动点击工具栏中的同步按钮进行项目的同步，同步后 IDE 会完成项目的构建配置和索引等工作，您就可以在您的项目中正常使用 TUIRoomKit 组件了。
> **iOS 差异：** 为了使用音视频功能，您的应用需要获取麦克风和摄像头的权限。请在应用的 `Info。plist` 文件中添加以下两项，并填写对应的使用说明，这些说明将在系统请求权限时向用户显示：。

> **注意**
> **说明：**
> TUIRoomKit 内部已默认依赖 TRTC SDK、IM SDK 等公共库，您无需单独配置。

**代码示例：**

**Android（主示例）：**
``` java
include(":tuiroomkit")
project(":tuiroomkit").projectDir = File(settingsDir, "room/tuiroomkit")

include(":atomic_x")
project(":atomic_x").projectDir = File(settingsDir, "atomic_x")
```

``` java
include ':tuiroomkit'
project(':tuiroomkit').projectDir = new File(settingsDir, "room/tuiroomkit")

include(":atomic_x")
project(':atomic_x').projectDir = new File(settingsDir, "atomic_x")
```

``` java
dependencies {
    // 添加 tuiroomkit 依赖
    api(project(":tuiroomkit"))
}
```

``` java
dependencies {
    // 添加 tuiroomkit 依赖
    api project(':tuiroomkit')
}
```

``` java
-keep class com.tencent.** { *; }
-keep class com.tencent.beacon.** { *; }
-keep class com.tencent.cloud.iai.lib.** { *; }
-keep class com.tencent.qimei.** { *; }
-keep class com.tencent.xmagic.** { *; }
-keep class com.tcmediax.** { *; }

# Google 序列化/反序列化框架 Gson 库相关混淆
-keep class com.google.gson.** { *; }

```

``` xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <!-- 添加如下配置覆盖 依赖的 sdk 中的配置 -->
    <application
        android:allowBackup="false"
        tools:replace="android:allowBackup" />
</manifest>
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | - | - | - |

### 步骤3：登录

代码集成完成后，需要完成登录操作，这是**使用 **`TUIRoomKit`** 的关键步骤**。只有在登录成功后，才能正常使用 `TUIRoomKit` 的各项功能，因此请仔细检查相关参数配置是否正确：
【Swift】
**登录接口参数说明：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `sdkAppID` | `Int32` | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| `userID` | `String` | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| `userSig` | `String` | 用于腾讯云鉴权的票据。更多信息请参见 如何计算及使用 UserSig。 **注意：** - 开发环境：可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 userSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 userSig 的方式。详细信息请参考 服务端生成 UserSig。 |

> **注意**
> **说明：**
> **Android：** 在示例代码中，直接进行了登录接口的调用。但在实际项目场景下，强烈推荐**您在完成自己的用户身份验证等相关登录操作后，再调用 AtomicXCore 的登录服务**。这样可以避免因过早调用登录服务，导致业务逻辑混乱或数据不一致的问题，同时也能更好地适配您项目中现有的用户管理和权限控制体系。
> **iOS：** 在示例代码中，登录操作是在 `didFinishLaunchingWithOptions` 方法内完成的。但在实际项目场景下，强烈推荐**在完成自己的用户身份验证等相关登录操作后，再调用 AtomicXCore 的登录服务**。这样可以避免因过早调用登录服务，导致业务逻辑混乱或数据不一致的问题，同时也能更好地适配您项目中现有的用户管理和权限控制体系。
> **Flutter：** 在示例代码中，直接进行了登录接口的调用。但在实际项目场景下，强烈推荐**您在完成自己的用户身份验证等相关登录操作后，再调用 TUIRoomKit 的登录服务**。这样可以避免因过早调用登录服务，导致业务逻辑混乱或数据不一致的问题，同时也能更好地适配您项目中现有的用户管理和权限控制体系。

**代码示例：**

**Android（主示例）：**
``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.login.LoginStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import android.util.Log

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        LoginStore.shared.login(
            this,              // context
            1400000001,        // 替换为项目的 sdkAppID
            "test_001",        // 替换为项目的 userID
            "xxxxxxxxxxx",     // 替换为项目的 userSig
            object : CompletionHandler {
                override fun onSuccess() {
                    // 登录成功处理
                    Log.d("Login", "login success");
                }

                override fun onFailure(code: Int, desc: String) {
                    // 登录失败处理
                    Log.e("Login", "login failed, code: $code, error: $desc");
                }
            }
        )
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool { | - | Closure + Result |
| Flutter | if (loginResult.isSuccess) { | - | Promise / async-await |

### 步骤4：设置头像和昵称

首次登录的用户没有头像和昵称信息，需要通过 `LoginStore` 的 `setSelfInfo` 接口设置个人信息：
**setSelfInfo接口参数说明：**
| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `userProfile` | `UserProfile` | 是 | 个人用户信息核心模型，包含： - userID：要设置用户信息的用户 ID。 - nickname：昵称信息。 - avatarURL：头像链接。 |
| `completion` | `CompletionClosure` | 否 | 设置个人信息接口的结果回调。若失败会返回错误码和错误信息。 |

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.login.LoginStore
import io.trtc.tuikit.atomicxcore.api.login.UserProfile

private val TAG = "Test"

fun setSelfInfo() {
    val userProfile = UserProfile(
        userID = "test_001",        // 您已经登录的userID
        nickname = "tom",           // 设置昵称
        avatarURL = "" // 设置头像URL
    )

    LoginStore.shared.setSelfInfo(userProfile, object : CompletionHandler {
        override fun onSuccess() {
            Log.d(TAG, "setSelfInfo success")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e(TAG, "setSelfInfo failed code:$code, message:$desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func setSelfInfo() { | - | Closure + Result |
| Flutter | if (result.isSuccess) { | - | Future<Result> |

### 步骤5：创建房间

在`TUIRoomKit` 组件中，`RoomMainView`是集成了完整的多人音视频房间功能的核心界面，以下将对开发者演示如何以房主身份将`RoomMainView`嵌入到应用中。
#### **实现方式：**
1. **加载创建视图页面**：懒加载方式实例化 `RoomMainView `。
2. **构造进房的配置项**：进入房间后是否需要自动打开音视频设备的配置项。
3. **初始化房间主页面**：通过房主身份初始化房间主页面。
4. **将视图添加到视图 Activity**：在`Activity`的 `onCreate` 方法中，将 `RoomMainView` 添加到`Activity`中，并使用布局使其充满整个视图区域。
   > **说明：**
   > 
   > 移动端仅支持创建标准房间，如您需要创建大型研讨会房间，请参考 Web 快速接入 使用 Web 端创建。
   > 
#### **示例代码：**
**RoomMainView init 函数参数详细说明****：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `roomID` | `String` | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| `behavior` | `RoomBehavior` | 房间主页面初始化来源。 - Create: 房主身份创建房间。此方式需要构造创建房间配置项，关于 CreateRoomOptions 的详细用法请参考：CreateRoomOptions 结构体详细说明。 - Join：参与者身份加入房间。 |
| `config` | `ConnectConfig` | 进房后音视频设备控制的配置项。 |
**ConnectConfig 参数详细说明****：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| autoEnableMicrophone | `Boolean` | 进房后是否自动开启麦克风 - true：自动开启 （默认值）。 - false：不自动开启。 |
| autoEnableCamera | `Boolean` | 进房后是否自动开启摄像头。 - true：自动开启 （默认值）。 - false：不自动开启。 |
| autoEnableSpeaker | `Boolean` | 进房后是否自动开启扬声器。 - true：自动开启 （默认值）。 - false：不自动开启。 |
##### CreateRoomOptions 结构体详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomName` | `String` | 否 | - 房间名称，可以不设置，默认为空字符串。 - 限制长度为 0-60 字节。 - 支持中英文、数字、特殊字符。 |
| `password` | `String` | 否 | - 房间密码，空字符串 "" 通常表示该房间不设密码。 - 限制长度为 0-32 字节。 - 推荐使用 4-8 位纯数字，方便移动端输入。设置后，其他用户加入房间时需输入密码。建议不要存储明文敏感信息。 |
| `isAllMicrophoneDisabled` | `Boolean` | 否 | 是否全员禁止打开麦克风。开启后，除房主/管理员外，普通参与者默认禁止打开麦克风。 - true：禁止。 - false：取消禁止 （默认值）。 |
| `isAllCameraDisabled` | `Boolean` | 否 | 是否全员禁止打开摄像头。开启后，除房主/管理员外，普通参与者默认禁止打开摄像头。 - true：禁止。 - false：取消禁止（默认值）。 |
| `isAllScreenShareDisabled` | `Boolean` | 否 | 是否全员禁止发起屏幕共享。开启后，仅房主/管理员可进行屏幕共享。 - true：禁止。 - false：取消禁止（默认值）。 |
| `isAllMessageDisabled` | `Boolean` | 否 | 是否全员禁止发送聊天消息（禁言）。开启后，普通参与者无法在房间内发送文字消息。 - true：禁止。 - false：取消禁止（默认值）。 |

> **iOS 差异：** 在 `TUIRoomKit` 组件中，`RoomMainView` 是集成了完整的多人音视频房间功能的核心界面，以下将向开发者演示如何以房主身份将 `RoomMainView` 嵌入到应用中。**遵守路由导航协议**：`TUIRoomKit` 内部使用 `RouterContext` 协议管理页面间的路由跳转。宿主视图控制器需要声明遵循该协议，以确保组件内部的导航操作（如结束房间、返回上一页）能够正确执行。**懒加载创建视图页面**：在控制器内部以懒加载方式实例化 `RoomMainView`，并为其设置 `routerContext` 属性。该属性使得视图能够通过协议方法调用宿主控制器的导航功能。5。**将视图添加到控制器**：在控制器的 `viewDidLoad` 方法中，将 `RoomMainView` 加入视图层级，并使用约束布局使其充满整个控制器视图区域。**RoomMainView 构造函数参数详细说明：**。<br>- create：房主身份创建房间的方式需要构造创建房间配置项。<br>  - 关于 `CreateRoomOptions` 的详细用法请参考：CreateRoomOptions 结构体详细说明。<br>- join：参与者身份加入房间。<td rowspan="1" colSpan="1" >`Bool`</td>。<td rowspan="1" colSpan="1" >进房后是否自动开启麦克风。<td rowspan="1" colSpan="1" >`Bool`</td>。<td rowspan="1" colSpan="1" >`Bool`</td
> **Flutter 差异：** 在 `TUIRoomKit` 组件中，`RoomMainWidget` 是集成了完整的多人音视频房间功能的核心界面，以下将向开发者演示如何以房主身份将 `RoomMainWidget` 嵌入到应用中。**配置房间创建选项**：配置房间名称等信息。**导航到房间页面**：将 `RoomMainWidget` 推入导航栈。com/document/93119862767460352) 使用Web端创建。**RoomMainWidget 构造函数参数详细说明：**。<td rowspan="1" colSpan="1" >- 房间主页面初始化来源。<br>- 参数含义：<br>  - create: 房主身份创建房间，此方式需要构造创建房间配置项，关于CreateRoomOptions的详细用法请参考：CreateRoomOptions 结构体详细说明。<br>  - enter：参与者身份加入房间。<td rowspan="1" colSpan="1" >`bool`</td>。<td rowspan="1" colSpan="1" >- 进房后是否自动开启麦克风。<br>- 参数含义：<br>  - true: 自动开启 （默认值）。<br>  - false: 不自动开启。<td rowspan="1" colSpan="1" >`bool`</td>。<td rowspan="1" colSpan="1" >- 进房后是否自动开启摄像头。<br>- 参数含义：<br>  - true: 自动开启 （默认值）。<br>  - false: 不自动开启。<td rowspan="1" colSpan="1" >`bool`</td>。<td rowspan="1" colSpan="1" >- 进房后是否自动开启扬声器。<br>- 参数含义：<br>  - true: 自动开启 （默认值）。<br>  - false: 不自动开启

> **注意**
> - `TUIRoomKit` 的内部页面跳转机制基于 `UINavigationController` 的标准导航方法（push/pop）实现。为确保页面路由功能正常工作，开发者的视图控制器必须作为 `UINavigationController` 的根视图控制器或位于其导航栈内。若未将控制器包装至 `UINavigationController` 中，SDK 内部的跳转操作将因缺少有效的导航上下文而失效，导致页面无法正常切换。
> - 移动端仅支持创建标准房间，如果您需要创建大型研讨会房间，请参考 Web 快速接入 使用 Web 端创建。

**代码示例：**

**Android（主示例）：**
``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.trtc.uikit.roomkit.view.RoomMainView
import io.trtc.tuikit.atomicxcore.api.room.CreateRoomOptions

// MainActivity 代表您加载房间主页面的 Activity
class MainActivity : AppCompatActivity() {

    // 1 加载创建视图页面
    private val roomView: RoomMainView by lazy {
        RoomMainView(this).apply {
            // 2 构造进房的配置项
            val config = RoomMainView.ConnectConfig(
                autoEnableMicrophone = true, // 进房后是否自动开启麦克风
                autoEnableCamera = true,     // 进房后是否自动开启摄像头
                autoEnableSpeaker = true    // 进房后是否自动开启扬声器
            )

            val options = CreateRoomOptions(roomName = "roomName") // 房间名称
            val behavior = RoomMainView.RoomBehavior.Create(options)
            // 3 初始化房间主页面
            init("roomID", behavior, config)
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 4. 将视图添加到视图Activity
        setContentView(roomView)
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | - | - | - |
| Flutter | void _createRoom(BuildContext context) { | onPressed: () => _createRoom(context), | - |

### 步骤6：加入房间

以下示例将向开发者演示如何以参与者身份将`RoomMainView`嵌入到应用中。
#### **实现方式：**
1. **遵守路由导航协议**：`TUIRoomKit` 内部使用 `RouterContext` 协议管理页面间的路由跳转。宿主视图控制器需要声明遵循该协议，以确保组件内部的导航操作（如结束房间、返回上一页）能够正确执行。
2. **懒加载创建视图页面**：在控制器内部以懒加载方式实例化 `RoomMainView`，并为其设置 `routerContext` 属性。该属性使得视图能够通过协议方法调用宿主控制器的导航功能。
3. **构造进房的配置项**：进入房间后是否需要自动打开音视频设备的配置项。
4. **初始化房间主页面**：通过参与者身份初始化房间主页面。
5. **将视图添加到控制器**：在控制器的 `viewDidLoad` 方法中，将 `RoomMainView` 加入视图层级，并使用约束布局使其充满整个控制器视图区域。
#### 代码示例：
[RoomMainView 构造函数参数详细说明]( 构造函数参数详细说明)和 [ConnectConfig 参数详细说明]( 参数详细说明)
> **Android 差异：** **加载创建视图页面**：懒加载方式实例化 `RoomMainView `。**将视图添加到视图 Activity**：在`Activity`的 `onCreate` 方法中，将 `RoomMainView` 添加到`Activity`中，并使用布局使其充满整个视图区域。> **说明：**。> 使用 **TUIRoomKit** 组件进入大型研讨会房间时，需确保 Web 端创建的研讨会房间的房间 ID 以 `webinar_` 开头
> **Flutter 差异：** 以下示例将向开发者演示如何以参与者身份将`RoomMainWidget`嵌入到应用中。**导航到房间页面**：将 `RoomMainWidget` 推入导航栈。[RoomMainWidget 构造函数参数详细说明]( 构造函数参数详细说明)和 [ConnectConfig 参数详细说明](

**参数详细说明请参考：**RoomMainView init 函数参数详细说明和 ConnectConfig 参数详细说明。

> **注意**
> **说明：**
> **iOS：** 使用 **TUIRoomKit** 组件进入大型研讨会房间时，需确保Web端创建的研讨会房间的房间ID以 `webinar_` 开头。
> **Flutter：** 使用TUIRoomKit组件进入大型研讨会房间时，需确保Web端创建的研讨会房间的房间ID以 `webinar_` 开头。

**代码示例：**

**Android（主示例）：**
``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.trtc.uikit.roomkit.view.RoomMainView

// MainActivity 代表您加载房间主页面的 Activity
class MainActivity : AppCompatActivity() {

    // 1 加载创建视图页面
    private val roomView: RoomMainView by lazy {
        RoomMainView(this).apply {
            // 2 构造进房的配置项
            val config = RoomMainView.ConnectConfig(
                autoEnableMicrophone = true, // 进房后是否自动开启麦克风
                autoEnableCamera = true,     // 进房后是否自动开启摄像头
                autoEnableSpeaker = true    // 进房后是否自动开启扬声器
            )

            val behavior = RoomMainView.RoomBehavior.Join
            // 3 初始化房间主页面
            init("roomID", behavior, config)
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 4. 将视图添加到视图Activity
        setContentView(roomView)
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | - | - | - |
| Flutter | void _enterRoom(BuildContext context) { | onPressed: () => _enterRoom(context), | - |

## 核心功能

接入`RoomMainWidget`代码后，开发者将得到一个完整的多人音视频页面，内部包含成员管理，音视频设备控制，房间信息展示等功能，这是 `TUIRoomKit` 组件的核心。
| 标准房间 | 大型研讨会房间 |
| --- | --- |
|  |  |

## 界面定制

`RoomMainView`房间主页面内功能丰富，可定制化度高，开发者可以根据实际产品需求，对 UI 进行定制化修改，以适配业务交互场景。以下将详细为开发者展示`RoomMainView`页面中视图组件，便于开发者快速修改。
| 标准房间 | 大型研讨会房间 |
| --- | --- |
|  |  |
`RoomMainView`**中组件详细说明**
| 组件 | 功能描述 | 定制化建议 |
| --- | --- | --- |
| [RoomMainView](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/room/tuiroomkit/src/main/java/com/trtc/uikit/roomkit/view/RoomMainView.kt) | 房间主视图容器，负责协调各子组件的布局与数据流转。 | 可调整整体背景、安全区域适配、组件显隐逻辑。 |
| [RoomTopBarView](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/room/tuiroomkit/src/main/java/com/trtc/uikit/roomkit/view/main/RoomTopBarView.kt) | 顶部导航栏，包含房间信息、摄像头和声音控制、退出房间功能入口。 | 可替换图标、调整背景透明度、添加自定义按钮（例如录制、 窗口化）。 |
| [RoomView](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/room/tuiroomkit/src/main/java/com/trtc/uikit/roomkit/view/main/RoomView.kt) | 视频流展示区域，采用瀑布流布局管理多个用户视频画面。 | 可修改布局算法（行列数、间距）、分页指示器样式、空状态视图。 |
| [RoomVideoNameOverlayView](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/room/tuiroomkit/src/main/java/com/trtc/uikit/roomkit/view/main/roomview/RoomVideoNameOverlayView.kt) | 单个视频流单元格挂件，承载用户视频画面与基本信息。 | 可自定义视频渲染层、用户信息面板（头像、徽章）、互动控件（语音波形）。 |
| [RoomBottomBarView](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/room/tuiroomkit/src/main/java/com/trtc/uikit/roomkit/view/main/RoomBottomBarView.kt) | 底部工具栏，集成麦克风、摄像头、成员管理操作按钮。 | 可重新排列按钮顺序、修改按钮样式（颜色、尺寸）、添加业务相关功能（例如屏幕分享、会中呼叫、美颜）。 |

> **注意**
> **注意：**
> **推荐解决方案：**
> **iOS：** 界面定制、图标定制、文案定制这部分章节内容，涉及到需要修改`TUIRoomKit` 组件源码，通过 **CocoaPods** 依赖管理器集成`TUIRoomKit`将被视为“不可变”的依赖项。每次执行 pod install 时，**CocoaPods** 会： - 检查 Podfile.lock 中记录的版本号。 - 从远程仓库重新下载对应版本的源码。 - 用下载的原始代码覆盖 Pods/ 目录下的现有文件。 **因此，任何对 Pods/ 目录的手动修改都只是临时性的，下次执行安装命令时即被还原。** - Fork [TUIRoomKit](https://github.com/Tencent-RTC/TUIKit_iOS) 的官方仓库。 - 在 fork 的仓库中修复问题并提交。 - 修改 Podfile，指向自定义仓库和分支。 `pod 'TUIRoomKit', :git => 'https://github.com/your-username/TUIRoomKit.git', :branch => 'your-branch'`
> **Flutter：** 界面定制、图标定制、文案定制这部分章节内容，涉及到需要修改 TUIRoomKit 组件源码，通过 pub 依赖管理器集成 TUIRoomKit 为"不可变"的依赖项。每次执行 flutter pub get 时，Flutter 会： 1. 检查 pubspec.lock 中记录的版本号。 2. 从远程仓库重新下载对应版本的源码。 3. 用下载的原始代码覆盖 .pub-cache/ 目录下的现有文件。 **因此，任何对缓存目录的手动修改都只是临时性的，下次执行安装命令时即被还原。** 4. Fork [TUIKit_Flutter](https://github.com/Tencent-RTC/TUIKit_Flutter/tree/main) 的官方仓库。 5. 在 fork 的仓库中修改并提交。 6. 修改 pubspec.yaml ，指向**自定义仓库和分支。** `dependencies:` `  tencent_conference_uikit:` `    git:` `      url: https://github.com/your-username/TUIKit_Flutter.git` `      ref: your-branch` `      path: room`

## 图标定制

接入 TUIRoomKit 组件后，开发者可以根据实际 UI 交互需求直接替换组件下的图标资源，以适配开发者的业务场景。
TUIRoomKit 使用 `assets/images/` 目录管理 UI 所需的图片资源，您可以直接替换该目录下的图片文件来自定义界面所需的图标。

**常用的图片文件列表**
| 图标 | 文件名 | 说明 |
| --- | --- | --- |
|  | room_camera_off.png | 摄像头关闭图标 |
|  | room_camera_on.png | 摄像头开启图标 |
|  | room_mic_off.png | 麦克风关闭图标 |
|  | room_mic_on_empty.png | 麦克风开启图标 |
|  | room_administrator_icon.png | 管理员身份标识图标 |
|  | room_owner_icon.png | 房主身份标识图标 |

## 文案定制

`TUIRoomKit` 使用更为方便的 [Apple Strings Catalog](https://developer.apple.com/documentation/xcode/localizing-and-varying-text-with-a-string-catalog) 工具来管理 UI 所需的文案显示，您可以很直观的通过  Xcode 视图化管理工具修改需要调整的文案：

> **Android 差异：** TUIRoomKit 使用 Android 通用的 XML 资源文件来管理 UI 所需的文案显示，您可以直接通过  `XML` 文件修改需要调整的文案：。cn/100027501523/0204ff61fcbf11f092385254001d6acc
> **Flutter 差异：** TUIRoomKit 使用 Flutter 标准的 ARB 文件来管理 UI 所需的文案显示，您可以直接修改 `lib/base/language/i10n/` 目录下的语言文件来调整需要的文案：。cn/100033051146/3c212786fcbf11f0b18e525400a31896

> **注意**
> **说明：**
> [Apple Strings Catalog](https://developer.apple.com/documentation/xcode/localizing-and-varying-text-with-a-string-catalog) (.xcstrings) 是在 Xcode 15 中引入的本地化格式。它增强了开发者管理本地化字符串的方式，支持处理复数、设备特定变体等的结构化格式。这种格式正成为管理 iOS 和 macOS 应用程序本地化的推荐方法。

## 常见问题

#### **TUIRoomKit 在本地开发时使用正常，但部署到线上环境后无法**正常采集用户的摄像头或麦克风设备**？**
- **原因分析：**浏览器出于安全和隐私保护的考虑，对音视频设备（麦克风、摄像头）的采集有着严格限制。只有在安全环境下，采集操作才会被允许。安全环境协议包括：https://、localhost、file:// 等。HTTP 协议被视为不安全，浏览器会默认禁止访问媒体设备。

- **解决方案：**若您在本地（localhost）测试一切正常，但部署后出现采集失败，请立即检查您的网页是否部署在 HTTP 协议上。您必须使用 HTTPS 协议部署您的网页，并确保具备有效的安全证书。

- **相关资源：**更多关于 URL 域名及协议的限制详情，请参见 [URL 域名及协议限制说明](https://cloud.tencent.com/document/product/647/17249#URL-.E5.9F.9F.E5.90.8D.E5.8D.8F.E8.AE.AE.E9.99.90.E5.88.B6)。

#### **TUIRoomKit 是否支持使用 iframe 集成?**

支持。使用 iframe 集成 TUIRoomKit 时, 需要在 iframe 标签中配置 allow 属性以授予必要的浏览器权限（麦克风、摄像头、屏幕共享、全屏等），示例如下: 
``` typescript
// 开启麦克风、摄像头、屏幕分享、全屏权限
<iframe allow="microphone; camera; display-capture; display; fullscreen;">
```

#### TUIRoomKit 是否支持设置内网代理？

支持。请参考如下指引设置内网代理参数。更多内网代理信息请参考 [应对防火墙受限](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-34-advanced-proxy.html)。
``` typescript
// 请在进房前设置内网代理参数
import TUIRoomEngine from '@tencentcloud/tuiroom-engine-js';
import { useRoomEngine } from 'tuikit-atomicx-vue3/room';
const { roomEngine } = useRoomEngine();

TUIRoomEngine.once('ready', () => {
  const trtcCloud = roomEngine.instance?.getTRTCCloud();
  trtcCloud.callExperimentalAPI(JSON.stringify({
    api: 'setNetworkProxy',
    params: {
      websocketProxy: "wss://proxy.example.com/ws/",
      turnServer: [{
        url: '14.3.3.3:3478',
        username: 'turn',
        credential: 'turn',
      }],
      iceTransportPolicy: 'relay',
    },
  }));
});
```

### 每次进房都需要调用登录吗？

不需要。通常您只需要完成一次 `LoginStore.shared.login` 调用即可，我们建议您将 `LoginStore.shared.login` 和 
`LoginStore.shared.logout` 与自己的登录业务关联。

### Android 14 以上机型切到后台后， 采集音视频无画面/无声音？

Android 14 及以上版本，应用在后台采集摄像头或麦克风数据时，必须启动前台服务并声明对应的服务类型，否则无法正常采集，解决方案可按照下面步骤：
1. AndroidManifest.xml 声明 `FOREGROUND_SERVICE` 、 `FOREGROUND_SERVICE_CAMERA` 、 `FOREGROUND_SERVICE_MICROPHONE` 权限和 service 声明。

   ``` xml
   <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
   <uses-permission android:name="android.permission.FOREGROUND_SERVICE_CAMERA" />
   <uses-permission android:name="android.permission.FOREGROUND_SERVICE_MICROPHONE" />
   
   <service
       android:name=".MediaCaptureService"
       android:foregroundServiceType="camera|microphone" />
   ```
2. 在界面启动后开启后台采集服务。

   ``` java
   import android.app.NotificationChannel
   import android.app.NotificationManager
   import android.app.Service
   import android.content.Intent
   import android.content.pm.ServiceInfo
   import android.os.Bundle
   import android.os.IBinder
   import androidx.appcompat.app.AppCompatActivity
   import androidx.core.app.NotificationCompat
   
   class MainActivity : AppCompatActivity() {
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           startForegroundService(Intent(this, MediaCaptureService::class.java))
       }
   }
   
   class MediaCaptureService : Service() {
       override fun onCreate() {
           super.onCreate()
           // 1. 创建通知渠道
           val channel = NotificationChannel("media", "媒体采集", NotificationManager.IMPORTANCE_LOW)
           getSystemService(NotificationManager::class.java).createNotificationChannel(channel)
   
           // 2. 构建通知
           val notification = NotificationCompat.Builder(this, "media")
               .setContentTitle("音视频通话中")
               .setSmallIcon(android.R.drawable.ic_menu_call)
               .build()
   
           // 3. 启动前台服务，指定 camera 和 microphone 类型（Android 14+ 必须）
           startForeground(1, notification,
               ServiceInfo.FOREGROUND_SERVICE_TYPE_CAMERA or ServiceInfo.FOREGROUND_SERVICE_TYPE_MICROPHONE)
       }
   
       override fun onBind(intent: Intent?): IBinder? = null
   }
   
   ```

### Podfile 文件有没有示例配置可以参考？

您可以参考 [GitHub TUIKit_iOS Example](https://github.com/Tencent-RTC/TUIKit_iOS/tree/main/application) 工程 `Podfile` 示例文件。

### 步骤3：配置页面导航和多语言功能

为了确保 TUIRoomKit 能够正常管理页面导航和显示多语言界面，需要在 Flutter 应用框架中进行以下配置：
- 在 `navigatorObservers` 中添加 `RoomNavigatorObserver.instance`，用于监听页面路由变化和管理组件生命周期。

- 在 `localizationsDelegates` 中添加相关本地化代理，确保界面文案能够根据系统语言正确显示。

- 使用 `ComponentTheme` 包裹在 `MaterialApp` 外层，确保所有 RoomKit 组件都能访问主题配置。

   以 MaterialApp 框架为例，示例代码如下：

   ``` java
   import 'package:tencent_conference_uikit/tencent_conference_uikit.dart';
   
   // 您自己的APP主类
   class XXX extends StatelessWidget {
     const XXX({super.key});
   
    @override
     Widget build(BuildContext context) {
       return ComponentTheme(  // 使用 ComponentTheme 包裹在 MaterialApp 外层，确保所有 RoomKit 组件都能访问主题配置。
         child: MaterialApp(
           // 添加 RoomKit 导航观察者，用于监听页面路由变化和生命周期管理
           navigatorObservers: [RoomNavigatorObserver.instance],
           // 添加本地化代理，支持多语言文案显示
           localizationsDelegates: [
             ...RoomLocalizations.localizationsDelegates,
             ...BarrageLocalizations.localizationsDelegates,
           ],
           supportedLocales: const [Locale('en'), Locale('zh')],
           // 您APP的其他配置
           ......
         ),
       );
     }
   }
   ```

   配置后，组件将支持多语言并能正确处理页面跳转。

### iOS在release下运行/打包后无法进房？

使用 Xcode 打开您的工程，选择项目 > Build Settings > Deployment，将其下的 Strip Style 设置为 `Non-Global Symbols`，以保留所需要的全局符号信息。

### pubspec.yaml文件有没有示例配置可以参考？

您可以参考 [GitHub TUIKit_Flutter Example](https://github.com/Tencent-RTC/TUIKit_Flutter/tree/main/application) 工程 `pubspec.yaml` 示例文件。

### 进房后无画面？

请前往**手机设置** > **App >****相机**，检查摄像头权限是否开启，可参考如下示例：
| iOS | Android |
| --- | --- |
|  |  |

## 源码接入

### 步骤1：安装项目依赖

在您的业务项目中安装以下依赖。

【npm】
``` bash
npm install @tencentcloud/roomkit-web-vue3@latest tuikit-atomicx-vue3 @tencentcloud/uikit-base-component-vue3 @tencentcloud/universal-api 
```

【pnpm】
``` bash
pnpm install @tencentcloud/roomkit-web-vue3@latest tuikit-atomicx-vue3 @tencentcloud/uikit-base-component-vue3 @tencentcloud/universal-api
```

【yarn】
``` bash
yarn add @tencentcloud/roomkit-web-vue3@latest tuikit-atomicx-vue3 @tencentcloud/uikit-base-component-vue3 @tencentcloud/universal-api
```

### 步骤3：配置 App.vue 文件

将以下内容合并到项目的 App.vue 文件中，以配置全局 UI 样式和用户登录逻辑：
- 配置全局 `UIKitProvider`：引入 `UIKitProvider` 组件，并设置默认主题和语言；

- 用户登录： 在 `onMounted` 钩子中执行 `login` 和 `setSelfInfo`；

   ``` typescript
   <template>
     <!-- Step 1: 配置全局 UIKitProvider -->
     <UIKitProvider theme="light" language="zh-CN">
       <!-- 您包含 ConferenceMainView 的业务组件 -->
     </UIKitProvider>
   </template>
    
   <script setup>
   import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
   import { onMounted } from 'vue';
   import { useLoginState } from 'tuikit-atomicx-vue3/room';
   
   // step 2: 用户登陆
   const { login, setSelfInfo } = useLoginState();
   const SDKAppID = 0;    // 注意：请替换为实时音视频控制台获取的 SDKAppID 信息
   const userId = '填入您真实的 userId';    // 注意：请替换为真实的 userId
   const userName = '填入您真实的 userName';  // 注意：请替换为真实的 userName
   const userSig = '填入您真实的 userSig';    // 注意：生成临时 userSig 请参考 https://cloud.tencent.com/document/product/647/17275#console
   onMounted(async () => {
     try {
       await login({
         userId,
         userSig,
         sdkAppId: SDKAppID,
       });
       await setSelfInfo({
         userName,
         avatarUrl: '',
       });
     } catch (error: any) {
       console.error('Login failed:', error);
     }
   });
   </script>
   
   <!-- Step 3: 配置全局样式（此样式用来覆盖脚手架搭建的新项目的默认样式，可按需配置）-->
   <style>
   html, body {
     padding: 0 !important;
     margin: 0 !important;
   }
   
   #app {
     width: 100% !important;
     height: 100% !important;
     padding: 0 !important;
     margin: 0 !important;
     max-width: 100% !important;
     max-height: 100% !important;
     text-align: left !important;
     overflow: hidden;
   }
   </style>
   ```

### 步骤4：发起新的会议

会议主持人可以通过调用 `conference.start`  接口来发起一场新的会议，其他参会者可以参见 步骤6 的描述，调用 `conference.join` 接口加入该会议。

> **注意：**
> 请参考步骤 3 在用户登录成功后调用 `conference.start `接口发起会议。
``` typescript
import { conference } from '@tencentcloud/roomkit-web-vue3';
const startConference = async () => {  
  await conference.start({
    roomId: '123456',   // 请替换为您真实的 roomId
    options: {
      roomName: '我的临时会议',
    },
  });
}
startConference();
```

### 步骤5：进入已有会议

参与者可以通过调用 `conference.join` 接口，填写对应的 roomId 参数，来加入由会议主持人在 步骤4 中发起的会议。

> **注意：**
> 

> 请参考步骤 3 在用户登录成功后调用 `conference.join `接口加入会议。
> 

``` typescript
import { conference } from '@tencentcloud/roomkit-web-vue3';
const joinConference = async () => {
  await conference.join({
    roomId: '123456',   // 请替换为您真实的 roomId
  });
}
joinConference();
```

## 启动项目

【npm】
``` bash
npm run dev
```

【pnpm】
``` bash
pnpm run dev
```

【yarn】
``` bash
yarn run dev
```

启动成功后，请在浏览器中打开调试地址（通常为 http://localhost:5173）访问应用。您将看到如下所示的会议界面：

## UI 快速自定义

### 主题及语言

通过配置 App.vue 中 `UIKitProvider` 的入参，修改主题及语言的默认值。
| UIKitProvider 参数 | 可选值 | 默认值 |
| --- | --- | --- |
| theme | "light" \\| "dark" | "light" |
| language | "zh-CN" \\| "en-US" | "en-US" |

``` typescript
<UIKitProvider theme="light" language="zh-CN">
  <router-view />
</UIKitProvider>
 
<script setup lang="ts">
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
```

### 配置用户联系人列表

TUIRoomKit 预定会议用户选择列表和会中呼叫用户选择列表默认展示用户关系链列表，批量导入用户关系链请参考以下步骤：

步骤1：使用 [账号管理 > 导入多个账号](https://cloud.tencent.com/document/product/269/4919) REST API 接口批量导入用户账号；

步骤2：使用 [好友管理 > 导入好友](https://cloud.tencent.com/document/product/269/8301) REST API 接口批量导入用户关系链；

## TUIRoomKit 界面定制

### 源码导出

【Vue3】
1. 执行源码导出脚本，默认拷贝路径为 `./src/components/RoomKit`

``` bash
  node ./node_modules/@tencentcloud/roomkit-web-vue3/scripts/eject.js
```
2. 根据脚本提示确认是否要将 TUIRoomKit 源码拷贝到 `./src/components/RoomKit` 目录。如您需要自定义拷贝目录请输入 'y', 否则输入'n'。

3. 源码导出后，在您指定的项目路径中会新增 TUIRoomKit 源码。此时，您需要手动将 ConferenceMainView 组件，ConferenceMainViewH5 组件的 conference 对象的引用从 npm 包地址更改为 TUIRoom 源码的相对路径地址。

``` typescript
- import { ConferenceMainView, conference } from '@tencentcloud/roomkit-web-vue3';
// 替换引用路径为 TUIRoomKit 源码的真实路径
+ import { ConferenceMainView, ConferenceMainViewH5, conference } from './components/RoomKit/index.ts';
```
4. 配置 ESLint 校验

如果您导出 TUIRoomKit 源码后运行项目出现 ESLint 报错，您可以在`.eslintignore`文件中添加 RoomKit 文件夹忽略 ESLint 检测。
``` javascript
// 请替换为 TUIRoomKit 源码真实路径
src/components/RoomKit
```

### 自定义房间流布局

TUIRoomKit 目前支持九宫格、侧边栏及顶部栏三种流布局，默认为九宫格布局。
``` typescript
// RoomKit/views/ConferenceMainView/index.vue
import { RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
// 修改默认视频流布局为侧边栏布局
const participantViewLayout = ref(RoomLayoutTemplate.SidebarLayout);
function handleLayoutUpdate(layout: RoomLayoutTemplate) {
  participantViewLayout.value = layout;
}

// 修改默认视频流布局为顶部栏布局
const participantViewLayout = ref(RoomLayoutTemplate.CinemaLayout);
function handleLayoutUpdate(layout: RoomLayoutTemplate) {
  participantViewLayout.value = layout;
}
```

### 自定义视频流挂件

TUIRoomKit 默认视频流挂件如图所示，如需自定义视频流挂件请修改 `RoomKit/components/RoomLayoutView/ParticipantViewUI/ParticipantViewUI.vue` 组件。

