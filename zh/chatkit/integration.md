> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, React, Vue**

TUIKit Compose 是基于腾讯云 IM SDK 的一套 Jetpack Compose 组件库，提供了聊天、会话列表、联系人管理等即时通信功能的完整解决方案。本文介绍如何手动集成该组件并实现核心功能。

## 前提条件
- Android Studio Ladybug | 2024.2.1 及以上版本

- Android 5.0 及以上系统版本

- Gradle 8.9 及以上版本

- Android Gradle Plugin 8.6 及以上版本

- JDK 17 及以上版本

- Kotlin 1.9.0 及以上版本

- 一个有效的腾讯云账号及 Chat 应用。可参考 开通服务 从控制台获取以下信息：

  - SDKAppID：App 在控制台获取的 Chat 应用的 ID，为应用的唯一标识

  - SDKSecretKey：应用的密钥
      

      > **说明：**
      > 
>     - 本项目目前仅支持手动集成，暂不支持通过 Maven 等包管理器集成。
>     - 为确保构建环境稳定，请严格遵循官方兼容性要求进行配置：
>     - Gradle、Android Gradle Plugin、JDK 与 Android Studio 的兼容性，请参阅 Android 官方文档：[版本说明](https://developer.android.com/build/releases/gradle-plugin#updating-gradle)﻿。
>     - Kotlin、Android Gradle Plugin 与 Gradle 的版本对应关系，请参阅 Kotlin 官方文档：[Kotlin-Gradle 插件兼容性](https://kotlinlang.org/docs/gradle-configure-project.html#apply-the-plugin)。

      > 我们建议您根据上述指南，选择与项目要求完全匹配的版本组合。
      >

## Platform: Android

## 关键概念

TUIKit Compose 提供了一套完整的即时通信 UI 组件，向下依赖 AtomicXCore 数据层，向上可构建各种功能性 Page。
- Page 层：基于 TUIKit Compose 组件封装的完整功能页面：ChatPage、ContactsPage、ConversationsPage，您可以直接使用。

- TUIKit Compose（UI 组件层）：基于 AtomicXCore 构建的 Jetpack Compose 组件，您可将其嵌套到自己已有的 App 页面中。

- AtomicXCore（数据层）：提供数据管理和业务逻辑，包含各种 Store 和 Manager。

## 集成并引入组件

### 下载源码
1. 从 GitHub 下载 [TUIKit Compose](https://github.com/Tencent-RTC/TUIKit_android_compose) 源码：

   ``` bash
   git clone https://github.com/Tencent-RTC/TUIKit_Android_Compose.git
   ```

   项目目录结构说明：

   ``` bash
   atomic-x/
   ├── src/main/
   │   ├── java/io/trtc/tuikit/atomicx/     # UI 组件源码（必需集成）
   │       ├── messagelist/      # 消息列表组件
   │       ├── messageinput/     # 消息输入组件
   │       ├── conversationlist/ # 会话列表组件
   │       ├── contactlist/      # 联系人列表组件
   │       ├── basecomponent/    # 基础组件
   │       └── ...               # 其他 UI 组件
   │   # 资源文件（必需集成）
   │   ├── res/                    # 公共资源
   │   ├── res-message-list/       # 消息列表组件资源
   │   ├── res-message-input/      # 消息输入组件资源
   │   ├── res-conversation-list/  # 会话列表组件资源
   │   └── ...                     # 其他组件资源
   chat/
   ├── uikit/src/main/java/io/trtc/tuikit/chat/pages    # Page 组件（参考实现）
   │   ├── ChatPage.kt
   │   ├── ContactsPage.kt
   │   └── ConversationsPage.kt
   └── demo/                 # 示例应用（可选参考）
   ```

### 集成组件
1. 将组件目录完整复制到您的 Android 项目中，如下图所示，其中 ComposeDemo 是示例项目，TUIKit_Android_Compose 是从 GitHub 上下载的组件源码：

   

2. 在您的 settings.gradle.kts/settings.gradle 文件中添加 TUIKit Compose 组件：
   

   > **说明：**
   > 

   > TUIKit_Android_Compose 可以放在任何位置，只要在 settings.gradle 中正确设置相对路径即可。
   > 

   

【Kotlin DSL (推荐)】
``` java
// settings.gradle.kts
include(":atomicx")
project(":atomicx").projectDir = file("../TUIKit_Android_Compose/atomic-x")
```

【Groovy DSL】
``` java
// settings.gradle
include ':atomicx'
project(':atomicx').projectDir = new File(settingsDir, '../TUIKit_Android_Compose/atomic-x')
```

3. 在 app 模块的 build.gradle.kts/build.gradle 中添加下列依赖：

   

【Kotlin DSL (推荐)】
``` java
// app/build.gradle.kts
dependencies {
    implementation(project(":atomicx"))
}
```

【Groovy DSL】
``` java
// app/build.gradle
dependencies {
    api project(':atomicx')
}
```
   

   > **注意：**
   > 
>   - 使用本地集成方案时，如需升级时需要从 GitHub 获取最新的组件代码，覆盖您本地项目的 TUIKit Compose 目录。
>   - 当私有化修改和远端有冲突时，需要手动合并，处理冲突。

### 配置项目
1. 在 app 目录下找到 AndroidManifest.xml 文件，在 `application` 节点中添加 `tools:replace="android:allowBackup"`，覆盖组件内的设置，使用自己的设置。

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
2. 配置依赖项。

   在您的项目工程的 gradle/libs.versions.toml 文件中添加如下依赖项：

   ``` python
   # gradle/libs.versions.toml
   [versions]
   agp = "8.13.0"
   kotlin = "2.0.21"
   coreKtx = "1.10.1"
   junit = "4.13.2"
   junitVersion = "1.1.5"
   espressoCore = "3.5.1"
   appcompat = "1.7.1"
   material = "1.13.0"
   runner = "1.0.2"
   espressoCoreVersion = "3.0.2"
   appcompatV7 = "28.0.0"
   
   tuicore = "8.6.7021"
   lifecycleRuntimeKtx = "2.8.7"
   ui = "1.8.0"
   foundation = "1.8.0"
   composeBom = "2024.09.00"
   activityCompose = "1.10.1"
   activity = "1.8.0"
   constraintlayout = "2.2.1"
   
   [libraries]
   androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
   junit = { group = "junit", name = "junit", version.ref = "junit" }
   androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitVersion" }
   androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }
   androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
   material = { group = "com.google.android.material", name = "material", version.ref = "material" }
   runner = { group = "com.android.support.test", name = "runner", version.ref = "runner" }
   espresso-core = { group = "com.android.support.test.espresso", name = "espresso-core", version.ref = "espressoCoreVersion" }
   appcompat-v7 = { group = "com.android.support", name = "appcompat-v7", version.ref = "appcompatV7" }
   
   androidx-lifecycle-viewmodel-compose = { module = "androidx.lifecycle:lifecycle-viewmodel-compose", version.ref = "lifecycleRuntimeKtx" }
   androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "composeBom" }
   androidx-compose-foundation = { group = "androidx.compose.foundation", name = "foundation", version.ref = "foundation" }
   androidx-compose-ui = { group = "androidx.compose.ui", name = "ui", version.ref = "ui" }
   androidx-compose-ui-graphics = { group = "androidx.compose.ui", name = "ui-graphics" }
   androidx-ui-tooling = { group = "androidx.compose.ui", name = "ui-tooling" }
   androidx-compose-material3 = { group = "androidx.compose.material3", name = "material3" }
   androidx-activity-compose = { group = "androidx.activity", name = "activity-compose", version.ref = "activityCompose" }
   androidx-lifecycle-runtime-ktx = { group = "androidx.lifecycle", name = "lifecycle-runtime-ktx", version.ref = "lifecycleRuntimeKtx" }
   androidx-ui-tooling-preview = { group = "androidx.compose.ui", name = "ui-tooling-preview" }
   androidx-ui-test-manifest = { group = "androidx.compose.ui", name = "ui-test-manifest" }
   tuicore = { module = "com.tencent.liteav.tuikit:tuicore", version.ref = "tuicore" }
   com-tencent-mmkv = { group = "com.tencent", name = "mmkv", version = "1.3.14" }
   
   [plugins]
   android-application = { id = "com.android.application", version.ref = "agp" }
   kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
   android-library = { id = "com.android.library", version.ref = "agp" }
   
   kotlin-compose = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
   ```
3. 开启 Compose 支持。

   在 app 模块的 build.gradle.kts/build.gradle 中添加下列配置：

   

【Kotlin DSL (推荐)】
``` java
// app/build.gradle.kts
plugins {
    // 添加 Compose 插件
    alias(libs.plugins.kotlin.compose)
}
android {
    // 开启 Compose 
    buildFeatures {
        compose = true
    }
}
```

【Groovy DSL】
``` java
// app/build.gradle
plugins {
    // 添加 Compose 插件
    id libs.plugins.kotlin.compose.get().pluginId
}
android {
    // 开启 Compose 
    buildFeatures {
        compose true
    }
}
```

4. 配置混淆规则。

   在您的 app/proguard-rules.pro 文件中添加以下混淆规则：

   ``` java
   -keep class com.tencent.** { *; }
   ```
5. Android Studio 中点击 **File > Sync Project with Gradle Files** 集成组件。

## 接入步骤

### 步骤1：配置用户鉴权

在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im/tool-usersig) 根据 UserID 获取 UserSig 用于后续登录时的用户鉴权。

### 步骤2：用户登录

登录鉴权后才能正常使用组件的功能。您可以调用 login 接口，传入上文获取的 SDKAPPID、userSig 用于登录鉴权:
``` swift
// 用户登录
LoginStore.shared.login(context, SDKAPPID, userID, userSig,
    object : CompletionHandler {
        override fun onSuccess() {
              // 登录成功，可跳转聊天或会话页
        }
        override fun onFailure(code: Int, desc: String) {
              // 登录失败，可弹框报错
        }
    })
```

> **注意：**
> 

> 在正式的生产环境中，建议在您的服务端生成 UserSig，在需要 UserSig 时由您的 App 向业务服务器发起请求获取动态 UserSig 来进行鉴权。详见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
> 

### 步骤3： 构建会话列表界面

您可以基于 TUIKit Compose 中的 ConversationList 组件，构建一个会话列表页面。ConversationList 内建的功能是：
- 展示用户的会话列表，包含单聊和群聊会话

- 支持用户操作单个会话：置顶、删除、清空会话消息等

   您可以将 ConversationList 直接集成到现有的 App 页面中，也可以新构建一个完整的会话列表页，组装后的一种 UI 效果如下图所示：

   

   将 ConversationList 封装为会话列表页，可参考 `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ConversationsPage.kt` 文件里的实现，`ConversationsPage` 主要做了以下工作：

1. 在 ConversationList 上方添加了 headerView。

2. 传递点击会话列表 cell 事件。

   核心示例代码如下所示：

   ``` swift
   @Composable
   fun ConversationsPage(
       onConversationClick: (ConversationInfo) -> Unit = {},
       editContent: @Composable () -> Unit = {},
   ) {
       val colors = LocalTheme.current.colors
       Column(
           modifier = Modifier
               .fillMaxSize()
               .background(Colors.Transparent)
       ) {
   
           PageHeader(stringResource(R.string.chat_uikit_chat)) {
               editContent()
           }
   
           Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
               ConversationList(modifier = Modifier.navigationBarsPadding(), onConversationClick = onConversationClick)
           }
       }
   }
   ```

### 步骤4：构建聊天界面

您可以基于 TUIKit Compose 中的 MessageList、MessageInput  组件，构建一个聊天页面。

MessageList 内建的功能是：
- 展示单聊或群聊消息列表。

- 支持对单条消息操作：查看图片消息大图、播放视频或语音消息、复制文本消息、撤回消息、删除消息等。

   MessageInput 内建的功能是：

- 支持用户组建并发送多种类型的消息：文本、表情、图片、语音、视频、文件等。

   您可以将 MessageList 和  MessageInput 直接集成到现有的 App 页面中，也可以新构建一个完整的聊天页，组装后的一种 UI 效果如下图所示：

   

   将 MessageList 和 MessageInput 组装为聊天页，可参考  `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ChatPage.kt` 文件里的实现，`ChatPage` 主要做了以下工作：

1. 在最上方添加了 headerView，展示会话的名称。

2. 按照移动端用户使用习惯，上下拼接 MessageList 和 MessageInput。

   核心示例代码如下所示：

   ``` swift
   @Composable
   fun ChatPage(
       conversationID: String,
       locateMessage: MessageInfo? = null,
       onUserClick: (String) -> Unit,
       onChatHeaderClick: (String) -> Unit,
       onBackClick: () -> Unit,
   ) {
       ...
   
       val avatarUrl = conversationState.value?.avatarURL
       val displayName = conversationState.value?.title
       val colors = LocalTheme.current.colors
       Scaffold(
           modifier = Modifier
               .background(color = colors.bgColorOperate)
               .fillMaxSize()
               .wrapContentHeight(), topBar = {
               Box(
                   modifier = Modifier
                       .background(color = colors.bgColorOperate)
                       .fillMaxWidth()
                       .statusBarsPadding()
               ) {
                   ChatHeader(
                       avatarUrl = avatarUrl,
                       name = displayName ?: "",
                       onBackClick = onBackClick,
                       onAvatarClick = {
                           onChatHeaderClick(conversationID)
                       }
                   )
               }
           },
           bottomBar = {
               Box(
                   modifier = Modifier
                       .fillMaxWidth()
                       .background(color = colors.bgColorOperate)
               ) {
                   MessageInput(modifier = Modifier.navigationBarsPadding(), conversationID = conversationID)
               }
   
           }) { padding ->
           Box(
               modifier = Modifier
                   .padding(padding)
           ) {
               MessageList(conversationID = conversationID, locateMessage = locateMessage) {
                   onUserClick(it)
               }
           }
       }
   }
   ```

### 步骤5：构建联系人界面

您可以基于 TUIKit Compose 中的 ContactList 组件，构建一个联系人页面。ContactList 内建的功能是：
- 查看好友申请列表。

- 查看已加入的群列表。

- 处理群组邀请和申请。

- 管理黑名单列表。

- 查看好友。

   您可以将 ContactList 直接集成到现有的 App 中，也可以新构建一个完整的联系人列表页，组装后的一种 UI 效果如下图所示：

   

   将 ContactList 封装为联系人列表页，可参考 `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ContactsPage.kt `文件里的实现，`ContactsPage` 主要做了以下工作：

1. 在 ContactList 上方添加了 headerView。

2. 传递点击联系人列表 cell 事件。

   核心示例代码如下所示：

   ``` swift
   @Composable
   fun ContactsPage(
       onContactClick: (ContactInfo) -> Unit = {},
       onGroupClick: (ContactInfo) -> Unit = {},
       editContent: @Composable () -> Unit = {},
   ) {
   
       val colors = LocalTheme.current.colors
       Column(
           modifier = Modifier
               .fillMaxSize()
               .background(Colors.Transparent)
       ) {
           PageHeader(stringResource(R.string.chat_uikit_contacts)) {
               editContent()
           }
   
           Box(
               modifier = Modifier
                   .fillMaxSize()
                   .navigationBarsPadding(), contentAlignment = Alignment.Center
           ) {
               ContactList(onGroupClick = onGroupClick, onContactClick = onContactClick)
           }
       }
   }
   ```

### 步骤6：完善界面间的跳转逻辑

ConversationsPage、ChatPage、ContactsPage 中暴露了一些用户点击事件，您可以自定义事件来实现页面间的交互：
| Page | 回调 | 建议跳转逻辑 |
| --- | --- | --- |
| **ConversationsPage** | `onConversationClick: (ConversationInfo) -> Unit` | 点击会话列表中的会话时触发，建议跳转到聊天页面（ChatPage）。 |
| **ContactsPage** | `onContactClick: (ContactInfo) -> Unit` | 点击联系人 cell 时触发，建议跳转用户信息页（C2CChatSetting）。 |
| `onGroupClick: (ContactInfo) -> Unit` | 点击群组 cell 时触发，建议跳转群聊页面（ChatPage）。 |  |
| **ChatPage** | `onUserClick: (String) -> Unit` | 点击消息中的用户头像或昵称时触发，建议跳转用户信息页（C2CChatSetting）。 |
| `onChatHeaderClick: (String) -> Unit` | 点击导航栏中的头像时触发，建议跳转用户信息页（C2CChatSetting）或群聊信息页（GroupChatSetting） |  |
| `onBackClick: () -> Unit` | 点击返回按钮时触发，建议返回上一级页面。 |  |

**chat/demo/app/src/main/java/io/trtc/tuikit/chat/MainActivity.kt **和 **chat/demo/app/src/main/java/io/trtc/tuikit/chat/chat/ChatActivity.kt** 作为上述 Page 的粘合层，实现了各个 Page 的回调事件，核心示例代码如下：
``` java
// chat/demo/app/src/main/java/io/trtc/tuikit/chat/MainActivity.kt

// 点击会话列表 cell 跳转
ConversationsPage(onConversationClick = {
    activity?.startActivity(
        Intent(activity, ChatActivity::class.java).apply {
            putExtra("conversationID", it.conversationID)
        }
    )
})

// 点击联系人页面跳转
ContactsPage(onGroupClick = {
    ChatSettingActivity.start(context, "", it.contactID, true)
}, onContactClick = {
    ChatSettingActivity.start(context, it.contactID, "", true)
})
```
``` java
// chat/demo/app/src/main/java/io/trtc/tuikit/chat/chat/ChatActivity.kt

// 点击聊天页面跳转
ChatPage(
   conversationID = conversationID,
   locateMessage = message,
   onUserClick = {
       handleChatSettingNavigation(it)
   }, onChatHeaderClick = {
       val userID = getUserID(conversationID)
       val groupID = getGroupID(conversationID)
       handleChatSettingNavigation(userID, groupID)
   }, onBackClick = { finish() })
```

您可以参考上述回调说明及参考代码，实现 Page 之间交互逻辑。

## Platform: iOS

## 开发环境要求
- Xcode 10 及以上

- iOS 9.0 及以上

## CocoaPods 集成
1. 安装 CocoaPods。
在终端窗口中输入如下命令（需要提前在 Mac 中安装 Ruby 环境）：

   ``` bash
   sudo gem install cocoapods
   ```
2. 创建 Podfile 文件。
进入项目所在路径输入以下命令行，之后项目路径下会出现一个 Podfile 文件。

   ``` bash
   pod init
   ```
3. 根据业务需求在 Podfile 中添加对应的 TUIKit 组件。组件之间相互独立，添加或删除均不影响工程编译。您可以按需选择不同的 Podfile 集成方式：

  - 远程 CocoaPods 集成。

  - DevelopmentPods 本地集成。

      以上两种集成方式的优缺点如下表所示：

| 集成方式 | 适合场景 | 优点 | 缺点 |
| --- | --- | --- | --- |
| 远程 CocoaPods 集成 | 适合无源码修改时的集成。 | 当 TUIKit 有版本更新时，您只需再次` Pod update `即可完成更新。 | 当您有源码修改，使用 `Pod update` 更新时，新版本的 TUIKit 会覆盖您的修改。 |
| 本地 DevelopmentPods 集成 | 适合有涉及源码自定义修改的客户。 | 当您有自己的 git 仓库时，可以跟踪修改。修改源码后，使用 `Pod update` 更新其他远程 Pod 库时，不会覆盖您的修改。 | 您需要手动将 TUIKit 源码覆盖您本地 TUIKit 文件夹进行更新。 |

### 远程 CocoaPods 集成

您可以在 Podfile 中按需添加组件库：

> **注意：**
> 

> 从 8.5.6870 版本开始，TUIKit 支持 Swift 语言组件。
> 

【经典版】

【Swift 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# 防止 TUIKit 组件里的 *.xcassets 与您项目里面冲突。
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  use_frameworks!
  use_modular_headers!

  # 集成聊天功能
  pod 'TUIChat_Swift/UI_Classic' 
  # 集成会话功能
  pod 'TUIConversation_Swift/UI_Classic'
  # 集成关系链功能
  pod 'TUIContact_Swift/UI_Classic'
  # 集成搜索功能（需要购买旗舰版或企业版套餐）
  pod 'TUISearch_Swift/UI_Classic' 
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  # 集成投票插件
  pod 'TUIPollPlugin_Swift'
  # 集成群接龙插件
  pod 'TUIGroupNotePlugin_Swift'
  # 集成翻译插件（需开通增值功能，请联系腾讯云商务开通）
  pod 'TUITranslationPlugin_Swift'  
  # 集成会话分组插件
  pod 'TUIConversationGroupPlugin_Swift'
  # 集成会话标记插件
  pod 'TUIConversationMarkPlugin_Swift'
  # 集成语音转文字插件
  pod 'TUIVoiceToTextPlugin_Swift'
end

#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end

```

【Objective-C 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# 防止 TUIKit 组件里的 *.xcassets 与您项目里面冲突。
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  # 从 7.1 版本开始，新增了 TUIKit 插件（TUIXXXPlugin），TUIKit 插件是动态库依赖，需要开启此设置。
  # 如果您不依赖 TUIKit 插件，该设置可以保持关闭。
  use_frameworks!
  # 请按需开启 modular headers，开启后 Pod 模块才能使用 @import 导入，简化 Swift 引用 OC 的方式。
  # use_modular_headers!

  # 集成聊天功能
  pod 'TUIChat/UI_Classic' 
  # 集成会话功能
  pod 'TUIConversation/UI_Classic'
  # 集成关系链功能
  pod 'TUIContact/UI_Classic'
  # 集成搜索功能（需要购买旗舰版或企业版套餐）
  pod 'TUISearch/UI_Classic' 
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  # 集成快速会议
  pod 'TUIRoomKit'
  # 集成投票插件，从 7.1 版本开始支持
  pod 'TUIPollPlugin'
  # 集成群接龙插件，从 7.1 版本开始支持
  pod 'TUIGroupNotePlugin'
  # 集成翻译插件，从 7.2 版本开始支持（需开通增值功能，请联系腾讯云商务开通）
  pod 'TUITranslationPlugin'  
  # 集成会话分组插件，从 7.3 版本开始支持
  pod 'TUIConversationGroupPlugin'
  # 集成会话标记插件，从 7.3 版本开始支持
  pod 'TUIConversationMarkPlugin'
  # 集成语音转文字插件，从 7.5 版本开始支持
  pod 'TUIVoiceToTextPlugin'
  # 集成消息推送插件，从 7.6 版本开始支持
  pod 'TIMPush'
end

#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end

```

【简约版】

【Swift 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# 防止 TUIKit 组件里的 *.xcassets 与您项目里面冲突。
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  use_frameworks!
  use_modular_headers!

  # 集成聊天功能
  pod 'TUIChat_Swift/UI_Minimalist' 
  # 集成会话功能
  pod 'TUIConversation_Swift/UI_Minimalist'
  # 集成关系链功能
  pod 'TUIContact_Swift/UI_Minimalist'
  # 集成搜索功能（需要购买旗舰版或企业版、套餐）
  pod 'TUISearch_Swift/UI_Minimalist' 
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  # 集成翻译插件（需单独购买翻译插件）
  pod 'TUITranslationPlugin_Swift'  
  # 集成语音转文字插件
  pod 'TUIVoiceToTextPlugin_Swift'
  # 集成表情回应插件 
  pod 'TUIEmojiPlugin_Swift'
end
#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end
```

【Objective-C 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# 防止 TUIKit 组件里的 *.xcassets 与您项目里面冲突。
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  # 从 7.1 版本开始，新增了 TUIKit 插件（TUIXXXPlugin），TUIKit 插件是动态库依赖，需要开启此设置。
  # 如果您不依赖 TUIKit 插件，该设置可以保持关闭。
  use_frameworks!
  # 请按需开启 modular headers，开启后 Pod 模块才能使用 @import 导入，简化 Swift 引用 OC 的方式。
  # use_modular_headers!

  # 集成聊天功能
  pod 'TUIChat/UI_Minimalist' 
  # 集成会话功能
  pod 'TUIConversation/UI_Minimalist'
  # 集成关系链功能
  pod 'TUIContact/UI_Minimalist'
  # 集成搜索功能（需要购买旗舰版或企业版、套餐）
  pod 'TUISearch/UI_Minimalist' 
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  # 集成翻译插件，从 7.2 版本开始支持（需单独购买翻译插件）
  pod 'TUITranslationPlugin'  
  # 集成语音转文字插件，从 7.5 版本开始支持
  pod 'TUIVoiceToTextPlugin'
  # 集成消息推送插件，从 7.6 版本开始支持
  pod 'TIMPush'
  # 集成表情回应插件，从 7.8 版本开始支持  
  pod 'TUIEmojiPlugin'
end
#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end
```

> **说明：**
> 1. 如果您直接 `pod 'TUIChat'`，不指定经典版或简约版，默认会集成两套版本 UI 组件。 
> 2. 经典版和简约版 UI 不能混用，集成多个组件时，您必须同时全部选择经典版 UI 或简约版 UI。例如，经典版 `TUIChat` 组件必须与经典版 `TUIConversation`、`TUIContact` 组件搭配使用。同理，简约版 `TUIChat` 组件必须与简约版 `TUIConversation`、`TUIContact` 组件搭配使用。
> 3. 如果您使用的是 Swift，请开启 `use_modular_headers!` ，并将头文件引用改成 @import 模块名形式引用。

Podfile 修改完毕后，执行以下命令，安装 TUIKit 组件。
``` bash
pod install
```

如果无法安装 TUIKit 最新版本，执行以下命令更新本地的 CocoaPods 仓库列表。
``` bash
pod repo update
```

之后执行以下命令，更新组件库的Pod版本。
``` bash
pod update
```

集成全部的 TUIKit 组件后的项目结构：

> **注意：**
> 

> 若您操作遇到错误，可查阅文末的常见问题。
> 

### 本地 DevelopmentPods 源码集成
1. 从 GitHub 下载 TUIKit 源码，将 TUIKit 目录直接拖入您的工程目录下:

  - [Swift TUIKit 源码 Github 地址](https://github.com/Tencent-RTC/Chat_UIKit/tree/main/Swift/TUIKit)：

      

  - [Objective-C TUIKit 源码 Github 地址](https://github.com/TencentCloud/TIMSDK/tree/master/iOS/TUIKit)：

      

2. 修改您 Podfile 中每个组件的本地路径。path 是 TUIKit 文件夹相对于您工程 Podfile 文件的位置，常见的有：

  - TUIKit 文件夹位于您工程 Podfile 文件**父目录**： `pod 'TUICore', :path => "../TUIKit/TUICore"`

  - TUIKit 文件夹位于您工程 Podfile 文件**当前目录**： `pod 'TUICore', :path => "./TUIKit/TUICore"`

  - TUIKit 文件夹位于您工程 Podfile 文件**子目录**： ` pod 'TUICore', :path => "/TUIKit/TUICore"`

      以 TUIKit 文件夹位于您工程 Podfile 文件父目录为例：

      

【DevelopmentPodfile】

【Swift 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  use_frameworks!
  use_modular_headers!
  
  # 集成基础库（必选）
  pod 'TUICore', :path => "../TUIKit/TUICore"
  pod 'TIMCommon_Swift', :path => "../TUIKit/TIMCommon"
  # 集成TUIKit组件（可选）
  # 集成聊天功能
  pod 'TUIChat_Swift', :path => "../TUIKit/TUIChat"
  # 集成会话功能
  pod 'TUIConversation_Swift', :path => "../TUIKit/TUIConversation"
  # 集成关系链功能
  pod 'TUIContact_Swift', :path => "../TUIKit/TUIContact"
  # 集成搜索功能（需要购买旗舰版或企业版套餐）
  pod 'TUISearch_Swift', :path => "../TUIKit/TUISearch"
  
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  
  # 集成 TUIKitPlugin 插件 （可选）
  # 集成翻译插件（需单独购买插件）
  pod 'TUITranslationPlugin_Swift'
  # 集成语音转文字插件（需单独购买插件）
  pod 'TUIVoiceToTextPlugin_Swift'

  # 其他 Pod
  pod 'MJRefresh'
  pod 'SnapKit'
end
#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end
en
```

【Objective-C 组件】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
install! 'cocoapods', :disable_input_output_paths => true

# 请使用您的真实项目名称替换 your_project_name
target 'your_project_name' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  use_frameworks!
  use_modular_headers!
  
  # 集成基础库（必选）
  pod 'TUICore', :path => "../TUIKit/TUICore"
  pod 'TIMCommon', :path => "../TUIKit/TIMCommon"
  # 集成TUIKit组件（可选）
  # 集成聊天功能
  pod 'TUIChat', :path => "../TUIKit/TUIChat"
  # 集成会话功能
  pod 'TUIConversation', :path => "../TUIKit/TUIConversation"
  # 集成关系链功能
  pod 'TUIContact', :path => "../TUIKit/TUIContact"
  # 集成搜索功能（需要购买旗舰版或企业版套餐）
  pod 'TUISearch', :path => "../TUIKit/TUISearch"
  
  # 集成音视频通话功能
  pod 'TUICallKit_Swift'
  # 集成快速会议
  pod 'TUIRoomKit'
  
  # 集成TUIKitPlugin插件 （可选）
  # 集成翻译插件，从 7.2 版本开始支持（需单独购买翻译插件）
  pod 'TUITranslationPlugin'
  # 集成推送插件
  pod 'TIMPush'

  # 其他 Pod
  pod 'MJRefresh'
  pod 'Masonry'
end
#Pods config
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|                
            #Fix Xcode14 Bundle target error
            config.build_settings['EXPANDED_CODE_SIGN_IDENTITY'] = ""
            config.build_settings['CODE_SIGNING_REQUIRED'] = "NO"
            config.build_settings['CODE_SIGNING_ALLOWED'] = "NO"
            config.build_settings['ENABLE_BITCODE'] = "NO"
            config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = "13.0"
            #Fix Xcode15 other links  flag  -ld64
            xcode_version = `xcrun xcodebuild -version | grep Xcode | cut -d' ' -f2`.to_f
            if xcode_version >= 15
              xcconfig_path = config.base_configuration_reference.real_path
              xcconfig = File.read(xcconfig_path)
              if xcconfig.include?("OTHER_LDFLAGS") == false
                xcconfig = xcconfig + "\n" + 'OTHER_LDFLAGS = $(inherited) "-ld64"'
              else
                if xcconfig.include?("OTHER_LDFLAGS = $(inherited)") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS", "OTHER_LDFLAGS = $(inherited)")
                end
                if xcconfig.include?("-ld64") == false
                  xcconfig = xcconfig.sub("OTHER_LDFLAGS = $(inherited)", 'OTHER_LDFLAGS = $(inherited) "-ld64"')
                end
              end
              File.open(xcconfig_path, "w") { |file| file << xcconfig }
            end
        end
    end
en
```

3. Podfile 修改完毕后，执行以下命令，安装本地 TUIKit 组件。示例：

   ``` bash
   pod install
   ```   

   > **注意：**
   > 
>   1. 使用本地集成方案时，如需升级时需要从 Github 获取最新的组件代码，覆盖您本地项目的 TUIKit 目录。
>   2. 当私有化修改和远端有冲突时，需要手动合并，处理冲突。
>   3. TUIKit 插件需要依赖 TUICore 的版本，请确保插件版本和 "../TUIKit/TUICore/TUICore.spec" 中的 spec.version 一致。

## 第三方库依赖

TUIKit 依赖的第三方库的最低版本如下，如果您的版本较低，请升级到最新版本。

【Swift 组件】
``` plaintext
- MJExtension (3.4.1)
- MJRefresh (3.7.5)
- SnapKit (5.7.1)
- SSZipArchive (2.4.3)
- SDWebImage (5.18.11)
```

【Objective-C 组件】
``` plaintext
- Masonry (1.1.0)
- MJExtension (3.4.1)
- MJRefresh (3.7.5)
- ReactiveObjC (3.1.1)
- SDWebImage (5.18.11):
  - SDWebImage/Core (= 5.18.11)
- SDWebImage/Core (5.18.11)
- SnapKit (5.6.0)
- SSZipArchive (2.4.3)
```

## 快速搭建

常用的聊天软件都是由会话列表、聊天窗口、好友列表、音视频通话等几个基本的界面组成，参考下面步骤，您仅需几行代码即可在项目中快速搭建这些 UI 界面。

> **说明：**
> 

>  关于 TUIKit 组件模块功能：
> - 实操教学（Objective-C 组件版本）视频请参见：[极速集成 TUIKit（iOS）](https://cloud.tencent.com/edu/learning/course-3130-56699)。
> - 如果想了解更多，您还可以 [下载并运行 TUIKitDemo 源码](https://cloud.tencent.com/document/product/269/68228)，内含常见功能示例。

### 步骤1：组件登录

登录组件后才能正常使用组件的功能。用户在 App 上点击登录时，您可以登录 TUIKit 组件。
SDKAppID 需要在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 创建并获取，userSig 需要按规则计算，详细步骤请参考文档 [快速入门](https://cloud.tencent.com/document/product/269/68228#.E6.93.8D.E4.BD.9C.E6.AD.A5.E9.AA.A4)。

示例代码如下所示：

【Swift】
``` swift
@objc func loginSDK(_ userID: String, userSig: String, succ: TSucc?, fail: TFail?) {
    TUILogin.login(Int32(SDKAPPID), userID: userID, userSig: userSig, config: loginConfig, succ: {
        // update UI
        succ?()
    }, fail: { code, msg in
        // update UI
        fail?(code, msg)
    })
}
```

【Objective-C】
``` objectivec
#import "TUILogin.h"

- (void)loginSDK:(NSString *)userID userSig:(NSString *)sig succ:(TSucc)succ fail:(TFail)fail {
    [TUILogin login:SDKAppID userID:userID userSig:sig succ:^{
        NSLog(@"登录成功");
    } fail:^(int code, NSString *msg) {
        NSLog(@"登录失败");
    }];
}
```

### 步骤2：构建会话列表

会话列表只需要创建 `TUIConversationListController` 对象即可。会话列表会从数据库中读取最近联系人，当用户点击联系人时，`TUIConversationListController` 将事件 `didSelectConversation` 回调给上层。

示例代码如下所示：

【经典版】

【Swift】
``` swift
// ConversationController 为您自己的 ViewController
public class ConversationController: UIViewController, V2TIMSDKListener, TUIPopViewDelegate {
    override public func viewDidLoad() {
        super.viewDidLoad()
        setupNavigation()

        conv = TUIConversationListController()
        if let conv = conv {
            addChild(conv)
            view.addSubview(conv.view)
        }
    }
}
// 会话列表点击事件，可自定义，通常是打开聊天界面
public protocol TUIConversationListControllerListener: AnyObject {
    func conversationListController(_ conversationController: UIViewController, didSelectConversation conversation: TUIConversationCellData) -> Bool
}
```

【Objective-C】
``` objectivec
#import "TUIConversationListController.h"

// ConversationController 为您自己的 ViewController
@implementation ConversationController 
- (void)viewDidLoad {
    [super viewDidLoad];
    // TUIConversationListController
    TUIConversationListController *vc = [[TUIConversationListController alloc] init];
    vc.delegate = self;
    // 把 TUIConversationListController 添加到自己的 ViewController
    [self addChildViewController:vc];
    [self.view addSubview:vc.view];
}

- (void)conversationListController:(UIViewController *)conversationController 
             didSelectConversation:(TUIConversationCellData *)conversation {
    // 会话列表点击事件，可自定义，通常是打开聊天界面
}
@end
```

【简约版】

【Swift】
``` swift
// ConversationController 为您自己的 ViewController
public class ConversationController: UIViewController, V2TIMSDKListener, TUIPopViewDelegate {
    override public func viewDidLoad() {
        super.viewDidLoad()
        setupNavigation()

        conv = TUIConversationListController_Minimalist()
        if let conv = conv {
            addChild(conv)
            view.addSubview(conv.view)
        }
    }
}
// 会话列表点击事件，可自定义，通常是打开聊天界面
public protocol TUIConversationListControllerListener: AnyObject {
    func conversationListController(_ conversationController: UIViewController, didSelectConversation conversation: TUIConversationCellData) -> Bool
}
```

【Objective-C】
``` objectivec
#import "TUIConversationListController_Minimalist.h"

// ConversationController 为您自己的 ViewController
@implementation ConversationController
- (void)viewDidLoad {
    [super viewDidLoad];
    // TUIConversationListController_Minimalist
    TUIConversationListController_Minimalist *vc = [[TUIConversationListController_Minimalist alloc] init];
    vc.delegate = self;
    // 把 TUIConversationListController_Minimalist 添加到自己的 ViewController
    [self addChildViewController:vc];
    [self.view addSubview:vc.view];
}

- (void)conversationListController:(TUIConversationListController_Minimalist *)conversationController 
            didSelectConversation:(TUIConversationCell *)conversation
{
    // 会话列表点击事件，通常是打开聊天界面
}
@end
```

### 步骤3：构建聊天窗口

初始化聊天界面时，需要上层传入当前聊天界面对应的会话信息。

示例代码如下所示：

【经典版】

【Swift】
``` swift
let conversationData = TUIChatConversationModel()
conversationData.userID = userID ?? ""
conversationData.groupID = groupID ?? ""
if let chatVC = getChatViewController(conversationData) {
    navigationController?.pushViewController(chatVC, animated: true)
}

private func getChatViewController(_ model: TUIChatConversationModel) -> TUIBaseChatViewController? {
    var chat: TUIBaseChatViewController?
    if let userID = model.userID, !userID.isEmpty {
        chat = TUIC2CChatViewController()
    } else if let groupID = model.groupID, !groupID.isEmpty {
        chat = TUIGroupChatViewController()
    }
    chat?.conversationData = model
    return chat
}
```

【Objective-C】
``` objectivec
#import "TUIC2CChatViewController.h"

// ChatViewController 为您自己的 ViewController
@implementation ChatViewController
- (void)viewDidLoad {
  // 创建会话信息
  TUIChatConversationModel *data = [[TUIChatConversationModel alloc] init];
  data.userID = @"userID";    
  // TUIC2CChatViewController
  TUIC2CChatViewController *vc = [[TUIC2CChatViewController alloc] init];  
  [vc setConversationData:data];
  // 把 TUIC2CChatViewController 添加到自己的 ViewController
  [self addChildViewController:vc];
  [self.view addSubview:vc.view];
}
@end
```

> **说明：**
> 

> `TUIC2CChatViewController` 会自动拉取该用户的历史消息并展示出来。
> 

【简约版】

【Swift】
``` swift
let conversationData = TUIChatConversationModel()
conversationData.userID = userID ?? ""
conversationData.groupID = groupID ?? ""
if let chatVC = getChatViewController(conversationData) {
    navigationController?.pushViewController(chatVC, animated: true)
}

private func getChatViewController(_ model: TUIChatConversationModel) -> TUIBaseChatViewController_Minimalist? {
    var chat: TUIBaseChatViewController_Minimalist?
    if let userID = model.userID, !userID.isEmpty {
        chat = TUIC2CChatViewController_Minimalist()
    } else if let groupID = model.groupID, !groupID.isEmpty {
        chat = TUIGroupChatViewController_Minimalist()
    }
    chat?.conversationData = model
    return chat
}
```

【Objective-C】
``` objectivec
#import "TUIC2CChatViewController_Minimalist.h"

// ChatViewController 为您自己的 ViewController
@implementation ChatViewController
- (void)viewDidLoad {
  // 创建会话信息
  TUIChatConversationModel *data = [[TUIChatConversationModel alloc] init];
  data.userID = @"userID";    
  // 创建 TUIC2CChatViewController_Minimalist
  TUIC2CChatViewController_Minimalist *vc = [[TUIC2CChatViewController_Minimalist alloc] init];  
  [vc setConversationData:data];
  // 把 TUIC2CChatViewController 添加到自己的 ViewController
  [self addChildViewController:vc];
  [self.view addSubview:vc.view];
}
@end
```

> **说明**
> 

> `TUIC2CChatViewController_Minimalist` 会自动拉取该用户的历史消息并展示出来。
> 

### 步骤4：构建通讯录界面

通讯录界面不需要其它依赖，只需创建对象并显示出来即可。

【经典版】

【Swift】
``` swift
// ContactController 为您自己的 ViewController
public class ContactsController: UIViewController, TUIPopViewDelegate {
    override public func viewDidLoad() {
        super.viewDidLoad()

        contactVC = TUIContactController()
        if let contactVC = contactVC {
            addChild(contactVC)
            view.addSubview(contactVC.view)
        }
    }
}
```

【Objective-C】
``` objectivec
#import "TUIContactController.h"

// ContactController 为您自己的 ViewController
@implementation ContactController
- (void)viewDidLoad {    
  // 创建 TUIContactController
  TUIContactController *vc = [[TUIContactController alloc] init];
  // 把 TUIContactController 添加到自己的 ViewController
  [self addChildViewController:vc];
  [self.view addSubview:vc.view];
}
@end
```

注意，上面代码只能将 `TUIContactController` 初始化并展示出来，其中的点击行为（例如点击好友、添加好友等），TUIKit 会通过 `TUIContactControllerListener` 抛给上层处理：

【Swift】
``` swift
protocol TUIContactControllerListener: NSObjectProtocol {
    func onSelectFriend(_ cell: TUICommonContactCell) -> Bool
    func onAddNewFriend(_ cell: TUICommonTableViewCell) -> Bool
    func onGroupConversation(_ cell: TUICommonTableViewCell) -> Bool
}

```

【Objective-C】
``` objectivec
@protocol TUIContactControllerListener <NSObject>
@optional
- (void)onSelectFriend:(TUICommonContactCell *)cell;
- (void)onAddNewFriend:(TUICommonTableViewCell *)cell;
- (void)onGroupConversation:(TUICommonTableViewCell *)cell;
@end
```

【简约版】

【Swift】
``` swift
// ContactController 为您自己的 ViewController
public class ContactsController: UIViewController, TUIPopViewDelegate {
    override public func viewDidLoad() {
        super.viewDidLoad()

        contactVC = TUIContactController_Minimalist()
        if let contactVC = contactVC {
            addChild(contactVC)
            view.addSubview(contactVC.view)
        }
    }
}
```

【Objective-C】
``` objectivec
#import "TUIContactController_Minimalist.h"

// ContactController 为您自己的 ViewController
@implementation ContactController
- (void)viewDidLoad {    
  // 创建 TUIContactController_Minimalist
  TUIContactController_Minimalist *vc = [[TUIContactController_Minimalist alloc] init];
  // 把 TUIContactController_Minimalist 添加到自己的 ViewController
  [self addChildViewController:vc];
  [self.view addSubview:vc.view];
}
@end
```

注意，上面代码只能将 `TUIContactController_Minimalist` 初始化并展示出来，其中的点击行为（例如点击好友、添加好友等），TUIKit 会通过 `TUIContactControllerListener_Minimalist` 抛给上层处理：

【Swift】
``` swift
protocol TUIContactControllerListener_Minimalist: AnyObject {
    func onSelectFriend(_ cell: TUICommonContactCell_Minimalist) -> Bool
    func onAddNewFriend(_ cell: TUICommonTableViewCell) -> Bool
    func onGroupConversation(_ cell: TUICommonTableViewCell) -> Bool
}
```

【Objective-C】
``` objectivec
@protocol TUIContactControllerListener_Minimalist <NSObject>
@optional
- (void)onSelectFriend:(TUICommonContactCell *)cell;
- (void)onAddNewFriend:(TUICommonTableViewCell *)cell;
- (void)onGroupConversation:(TUICommonTableViewCell *)cell;
@end
```

例如，单击好友后，您可以将好友信息页展示出来：

【经典版】

【Swift】
``` swift
@objc private func onSelectFriend(_ cell: TUICommonContactCell) {
    if let delegate = delegate {
        if delegate.onSelectFriend(cell) { return }
    }
    let data = cell.contactData
    let vc = TUIFriendProfileController(style: .grouped)
    vc.friendProfile = data?.friendProfile
    navigationController?.pushViewController(vc, animated: true)
}
```

【Objective-C】
``` objectivec
#import "TUIFriendProfileController.h"

- (void)onSelectFriend:(TUICommonContactCell *)cell
{
    TUICommonContactCellData *data = cell.contactData;
    // 创建好友资料 vc
    TUIFriendProfileController *vc = [[TUIFriendProfileController alloc] init];
    vc.friendProfile = data.friendProfile;
    // 展示好友资料 vc
    [self.navigationController pushViewController:(UIViewController *)vc animated:YES];
}
```

【简约版】

【Swift】
``` swift
@objc func onSelectFriend(_ cell: TUICommonContactCell_Minimalist) {
    if let delegate = delegate {
        if delegate.onSelectFriend(cell) { return }
    }
    let data = cell.contactData
    let vc = TUIFriendProfileController_Minimalist()
    vc.friendProfile = data?.friendProfile
    navigationController?.pushViewController(vc, animated: true)
}
```

【Objective-C】
``` objectivec
#import "TUIFriendProfileController_Minimalist.h"

- (void)onSelectFriend:(TUICommonContactCell *)cell
{
    TUICommonContactCellData *data = cell.contactData;
    // 创建好友资料 vc
    TUIFriendProfileController_Minimalist *vc = [[TUIFriendProfileController_Minimalist alloc] init];
    vc.friendProfile = data.friendProfile;
    // 展示好友资料 vc
    [self.navigationController pushViewController:(UIViewController *)vc animated:YES];
}
```

### 步骤5：构建音视频通话功能

TUI 组件支持在聊天界面对用户发起音视频通话，仅需要简单几步就可以快速集成：
| 视频通话 | 语音通话 |
| --- | --- |
|  |  |

1. 开通音视频服务。

2. 登录 即时通信 IM 控制台 ，单击目标应用卡片，进入应用的基础配置页面。

3. 在开通腾讯实时音视频服务功能区，单击**免费体验**即可开通 TUICallKit 的 7 天免费试用服务。

4. 在弹出的开通实时音视频 TRTC 服务对话框中，单击确认，系统将为您在 [实时音视频控制台](https://console.cloud.tencent.com/trtc) 创建一个与当前 IM 应用相同 SDKAppID 的实时音视频应用，二者账号与鉴权可复用。

5. 集成 TUICallKit 组件。
在 podfile 文件中添加以下内容。

   ``` objectivec
   // 集成音视频通话组件
   pod 'TUICallKit_Swift'                  
   ```
6. 发起和接收视频或语音通话。

| 消息页发起通话 | 联系人页发起通话 |
| --- | --- |
|  |  |

   集成 TUICallKit 组件后，聊天界面和联系人资料界面默认会出现 “视频通话” 和 “语音通话” 两个按钮，当用户点击按钮时，TUIKit 会自动展示通话邀请 UI，并给对方发起通话邀请请求。

  - 当用户在线收到通话邀请时，TUIKit 会自动展示通话接收 UI，用户可以选择同意或者拒绝通话。

  - 当用户离线收到通话邀请时，如需唤起 App 通话，就要使用到离线推送能力，离线推送的实现请参见 [添加离线推送](https://cloud.tencent.com/document/product/269/109894)。

7. 添加离线推送。
在使用离线推送之前，您需要开通 [IM 离线推送](https://cloud.tencent.com/document/product/269/75429) 服务。

8. 关于 App 的配置，您可以参见文档：[集成 TUIOfflinePush 跑通离线推送功能](https://cloud.tencent.com/document/product/269/109894)。
   

   > **说明：**
   > 

   > 配置完成后，当单击接收到的**音视频通话离线推送通知**时， TUICallKit 会自动拉起**音视频通话邀请界面**。
   > 

9. 附加增值能力
集成 TUIChat 和 TUICallkit 的组件后，在聊天界面发送语音消息时，即可**录制带 AI 降噪和自动增益的语音消息**。该功能需要购买 [音视频通话能力](https://cloud.tencent.com/document/product/1640/79968) 进阶版及以上套餐，仅 IMSDK 7.0 及以上版本支持。当套餐过期后，录制语音消息会切换到系统 API 进行录音。
下面是使用两台华为 P10同时录制的语音消息对比：

<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/gateway/portal/css/global-20209142343.css?max_age=31536000&amp;t=20191128" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/gateway/portal/css/global-components.css?max_age=31536000&amp;t=20180817" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/gateway/documentation/documentation-v4/css/pandect-20219091610.css" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/gateway/documentation/documentation-v4/css/import-2-markdown-20219091610.css" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/platform/documents/css/documents-202205161915.css" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/open_proj/proj_qcloud_v2/gateway/documentation/documentation-v4/css/pandect-202012171130.css" />
<link rel="stylesheet" href="//qcloudimg.tencent-cloud.cn/static/document/qcloud-document-sdk.v0.0.17.css" />
<link rel="stylesheet" href="//cloudcache.tencentcs.com/qcloud/main/components/document-feedback/document-feedback.944df9c907.css" />
<style>
* {
  font-family: 'pingfang SC','helvetica neue',arial,'hiragino sans gb','microsoft yahei ui','microsoft yahei',simsun,sans-serif;
}
</style>
 
<div class="markdown-text-box">
<table style="text-align:center;vertical-align:middle;width:800px">
  <tr>
    <th style="text-align:center"><b>系统录制的语音消息<br /></b></th>
    <th style="text-align:center"><b>TUICallkit 录制的带 AI 降噪和自动增益的语音消息<br /></b></th>
  </tr>
  <tr>
    <td>
      <audio id="audio" controls preload="none">
	<source id="m4a" src=""></source>
      </audio>
    </td>
		
    <td>
      <audio id="audio" controls preload="none">
	<source id="m4a" src=""></source>
      </audio>
    </td>
  </tr>
</table>

</div>

### 步骤6：构建多人音视频功能

TUI 组件支持在聊天界面对用户发起多人音视频，仅需要简单几步就可以快速集成：

1. 开通音视频服务。

2. 登录 [即时通信 IM 控制台](https://console.cloud.tencent.com/im)，单击目标应用卡片，进入应用的基础配置页面。

3. 找到含 UI 低代码场景方案功能区，单击卡片下方的多人音视频（TUIRoomKit） > 免费体验。

4. 在弹窗中单击免费试用7天，即可成功开通多人音视频免费体验版。开通完成后即可参见 [集成指引](https://cloud.tencent.com/document/product/269/84296) 进行集成。

5. 集成 TUIRoomKit 组件。在 podfile 文件中添加以下内容。

   ``` ruby
   // 集成多人音视频组件
   pod 'TUIRoomKit'
   ```
6. 发起多人音视频会议

   集成 TUIRoomKit 组件后，聊天界面默认会出现 “快速会议” 按钮，当用户点击按钮时，TUIKit 会自动发起会议，并在聊天记录中展示一条会议邀请卡片。

   

## Xcode 集成常见问题

#### 1.  [Xcodeproj] Unknown object version (60). (RuntimeError)

使用 Xcode15 创建新工程来集成 TUIKit 时，输入**pod install** 后，可能会遇到此问题，原因是使用了较旧版本的 CocoaPods ，此时有两种解决办法：

解决方式一： 修改 Xcode 工程的 **Project Format **版本。

解决方式二： 升级本地的 CocoaPods 版本，升级方式本文不再赘述。

您可以在终端输入 **pod --version** 查看当前的 Pods 版本。

#### 2. -ld64链接器问题

Assertion failed: (false && "compact unwind compressed function offset doesn't fit in 24 bits"), function operator(), file Layout.cpp,

或是使用 XCode15 集成 TUIRoom 时，因最新链接器导致 TUIRoomEngine 的符号冲突，都属于该问题。

解决方式是：修改链接器配置

在 **Build Settings** 中的**Other Linker Flags** 种添加"**-ld64**"，即可解决。  参考资料： [https://developer.apple.com/forums/thread/735426](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Fforums%2Fthread%2F735426)。

#### 3. Rosetta 模拟器问题

使用苹果芯片（m1\m2等系列芯片）时会遇到， 原因是包括 SDWebImage 在内的三方库，并未支持 xcframework，不过苹果依旧给出了适配办法，就是在模拟器上开启 Rosetta 设置， 一般情况下编译时会自动弹出 Rosetta 选项。

#### 4. Xcode 15 开发者沙盒选项问题

Sandbox: bash(xxx) deny(1) file-write-create

当您使用 Xcode 15 创建一个新工程时， 可能会因为此选项导致编译运行失败，建议您关闭此选项。

### 5. Xcode 16 不支持 Framework 开启 bitcode 问题

**解决方案1：升级 SDK**
如果您使用的是包含 Bitcode 的旧版本 SDK（如 TXIMSDK_iOS），建议您按照本文档的指引，将 SDK 升级至 TXIMSDK_Plus_iOS_XCFramework。
**解决方式2: 修改 Podfile 配置**

在您的 Podfile 末尾新增如下配置，重新 Pod install。
``` java
post_install do |installer|
  bitcode_strip_path = 'xcrun --find bitcode_strip'.chop!
  def strip_bitcode_from_framework(bitcode_strip_path, framework_relative_path)
    framework_path = File.join(Dir.pwd, framework_relative_path)
    command = "#{bitcode_strip_path} #{framework_path} -r -o #{framework_path}"
    puts "Stripping bitcode: #{command}"
    system(command)
  end
  framework_paths = [
    "/Pods/TXIMSDK_iOS/ImSDK.framework/ImSDK",
  ]
  framework_paths.each do |framework_relative_path|
    strip_bitcode_from_framework(bitcode_strip_path, framework_relative_path)
   end
end
```

## CocoaPods 集成常见问题

#### 1. 使用远端集成时，Pod依赖版本不匹配问题

若您使用远端 Pods 集成时，出现 **Podfile.lock** 和 插件依赖的 TUICore 版本不一致时， 

此时请删除 **Podfile.lock** 文件， 并使用 **pod repo update **更新本地代码仓库， 之后使用 **pod update** 重新更新即可。

#### 2. 使用本地集成时，Pod依赖版本不匹配问题

若您使用DevelopPods集成时 出现  插件依赖的 **TUICore **版本较新，但本地 Pod 依赖的版本号是 **1.0.0** ，

此时请您参见 [Podfile_local](https://github.com/TencentCloud/TIMSDK/blob/master/iOS/Demo/Podfile_local) 和 [TUICore.spec](https://github.com/TencentCloud/TIMSDK/blob/master/iOS/TUIKit/TUICore/TUICore.podspec) ， 插件需要跟随版本，需要和 TUICore.spec 中一致，正常情况下我们会帮您修改为一致。

第一次使用本地集成时，建议您下载我们的示例 Demo 工程，将 Podfile 文件内容替换为 Podfile_local 的内容，执行 **Pod update **后相互参照。

---

## Platform: Flutter

## 关键概念

TUIKit 提供了一套完整的即时通信 UI 组件，向下依赖 AtomicXCore 数据层，向上可构建各种功能性 Page。
- Page 层：基于 TUIKit 组件封装的完整功能页面：ChatPage、ContactsPage、ConversationsPage，您可以直接使用。

- TUIKit Flutter（UI 组件层）：基于 AtomicXCore 构建的 Flutter 组件，您可将其嵌套到自己已有的 App 页面中。

- AtomicXCore（数据层）：提供数据管理和业务逻辑，包含各种 Store 和 Manager。

## 集成并引入组件

### 下载源码

1. 从 GitHub 下载 [TUIKit Flutter](https://github.com/Tencent-RTC/TUIKit_Flutter) 源码：
``` xml
git clone https://github.com/Tencent-RTC/TUIKit_Flutter.git
```

项目目录结构说明：
``` bash
atomic-x/
├── lib/                  # UI 组件源码（必需集成）
│   ├── messagelist/      # 消息列表组件
│   ├── messageinput/     # 消息输入组件
│   ├── conversationlist/ # 会话列表组件
│   ├── contactlist/      # 联系人列表组件
│   ├── basecomponent/    # 基础组件
│   └── ...               # 其他 UI 组件
├── call_assets/          # call 资源文件（必需集成）
├── chat_assets/          # chat 资源文件（必需集成）
chat/
├── uikit/                # Page 组件（参考实现）
│   ├── lib/
│      ├── chat_page.dart
│      ├── contacts_page.dart
│      └── conversations_page.dart
└── demo/                 # 示例应用（可选参考）
```

### 集成组件
1. 将组件目录完整复制到您的 Flutter 项目中，如下图所示，其中 flutter_demo 是示例项目，TUIKit_Flutter 是从 GitHub 上下载的组件源码：

   

2. 在您的 pubspec.yaml 文件中添加 TUIKit Flutter 组件。注意，TUIKit_Flutter 可以放在任何位置，只要在 pubspec.yaml 中正确设置相对路径即可：

   ``` bash
     tuikit_atomic_x:
       path: ../TUIKit_Flutter/atomic-x
   ```   

   > **注意：**
   > 
>   - 使用本地集成方案时，如需升级，您需要从 GitHub 获取最新的组件代码，覆盖您本地项目的 TUIKit_Flutter 目录。
>   - 当私有化修改和远端有冲突时，需要手动合并，处理冲突。

### 配置项目

在 iOS 端添加必要的权限配置。请打开 ios/Podfile ，在文件末尾新增如下权限代码：
``` objectivec
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
    target.build_configurations.each do |config|        
          config.build_settings['EXCLUDED_ARCHS[sdk=iphonesimulator*]'] = 'arm64'               
          config.build_settings['ENABLE_BITCODE'] = 'NO'        
          config.build_settings["ONLY_ACTIVE_ARCH"] = "NO"    
        end
    target.build_configurations.each do |config|
          config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
            '$(inherited)',
            'PERMISSION_MICROPHONE=1',
            'PERMISSION_CAMERA=1',
            'PERMISSION_PHOTOS=1',
          ]
        end
  end
end

```

## 接入步骤

### 步骤1：配置用户鉴权

在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im/tool-usersig) 根据 UserID 获取 UserSig 用于后续登录时的用户鉴权。

### 步骤2：用户登录

登录鉴权后才能正常使用组件的功能。您可以调用 login 接口，传入上文获取的 SDKAPPID、userID、userSig 用于登录鉴权：
``` java
final result = await LoginStore.shared.login(
      sdkAppID: SDKAPPID,
      userID: userID,
      userSig: userSig,
    );
if (result.errorCode == 0) {
    // 登录成功，可跳转聊天或会话页
} else {
    // 登录失败，可弹框报错
}
```

> **注意：**
> 

> 在正式的生产环境中，建议在您的服务端生成 UserSig，在需要 UserSig 时由您的 App 向业务服务器发起请求获取动态 UserSig 来进行鉴权。详见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
> 

### 步骤3：构建会话列表界面

您可以基于 TUIKit Flutter 中的 ConversationList 组件，构建一个会话列表页面。ConversationList 内建的功能是：
- 展示用户的会话列表，包含单聊和群聊会话

- 支持用户操作单个会话：置顶、删除、清空会话消息等

   您可以将 ConversationList 直接集成到现有的 App 页面中，也可以新构建一个完整的会话列表页，组装后的一种 UI 效果如下图所示：

   

   将 ConversationList 封装为会话列表页，可参考 `chat/uikit/lib/conversations_page.dart` 文件里的实现，`ConversationsPage` 主要做了以下工作：

1. 在 ConversationList 上方添加了 AppBar。

2. 实现点击会话列表 cell 事件。

3. 支持创建会话和群组功能：

  1. 点击右上角按钮可以创建新会话或群组

  2. 支持从联系人列表选择好友创建单聊

  3. 支持选择多个好友创建群聊

      核心示例代码如下所示：

      ``` java
        @override
        Widget build(BuildContext context) {
          return Scaffold(
            appBar: AppBar(
              backgroundColor: colorsTheme.bgColorOperate,
              title: Text(atomicLocale.chat, style: TextStyle(fontSize: 34, fontWeight: FontWeight.w600)),
              centerTitle: false,
              scrolledUnderElevation: 0,
              actions: [],
            ),
            body: Column(
              children: [
                Expanded(
                  child: ConversationList(
                    onConversationClick: (conversation) {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => ChatPage(
                            conversation: conversation,
                          ),
                        ),
                      );
                    },
                  ),
                ),
              ],
            ),
          );
        }
      ```

### 步骤4：构建聊天界面

您可以基于 TUIKit Flutter 中的 MessageList、MessageInput  组件，构建一个聊天页面。

MessageList 内建的功能是：
- 展示单聊或群聊消息列表。

- 支持对单条消息操作：查看图片消息的大图、播放视频或语音消息、复制文本消息、撤回消息、删除消息等。

   MessageInput 内建的功能是：

- 支持用户组建并发送多种类型的消息：文本、表情、图片、语音、视频、文件等。

   您可以将 MessageList 和  MessageInput 直接集成到现有的 App 页面中，也可以新构建一个完整的聊天页，组装后的一种 UI 效果如下图所示：

   

   将 MessageList 和 MessageInput 组装为聊天页，可参考  `chat/uikit/lib/chat_page.dart` 文件里的实现，`ChatPage` 主要做了以下工作：

1. 在最上方添加了 AppBar，展示会话的名称。

2. 按照移动端用户使用习惯，上下拼接 MessageList 和 MessageInput。

   核心示例代码如下所示：

   ``` java
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         appBar: AppBar(
             backgroundColor: colorsTheme.bgColorOperate,
             titleSpacing: 4.0,
             centerTitle: false,
             title: GestureDetector(
               onTap: _onTitleTap,
               child: Row(
                 mainAxisSize: MainAxisSize.min,
                 children: [
                   Padding(
                     padding: const EdgeInsets.only(right: 8.0),
                     child: Avatar.image(
                       name: widget.conversation.title,
                       url: widget.conversation.avatarURL,
                     ),
                   ),
                   Expanded(
                     child: Text(
                       widget.conversation.title ?? atomicLocale.chat,
                       style: TextStyle(fontSize: 14, fontWeight: FontWeight.w600),
                       overflow: TextOverflow.ellipsis,
                       maxLines: 1,
                     ),
                   ),
                 ],
               ),
             ),
             scrolledUnderElevation: 0.0,
             leading: IconButton.buttonContent(
               content: IconOnlyContent(Icon(Icons.arrow_back_ios, color: colorsTheme.buttonColorPrimaryDefault)),
               type: ButtonType.noBorder,
               size: ButtonSize.l,
               onClick: () => Navigator.of(context).pop(),
             ),
             actions: []),
         body: Column(
           children: [
             MessageList(
               conversationID: widget.conversation.conversationID,
               locateMessage: widget.message,
               onUserClick: (String userID) => _onUserClick(userID),
             ),
             MessageInput(
               conversationID: widget.conversation.conversationID,
             ),
           ],
         ),
       );
     }
   ```

### 步骤5：构建联系人界面

您可以基于 TUIKit Flutter 中的 ContactList 组件，构建一个联系人页面。ContactList 内建的功能是：
- 查看好友申请列表。

- 查看已加入的群列表。

- 处理群组邀请和申请。

- 管理黑名单列表。

- 查看好友。

   您可以将 ContactList 直接集成到现有的 App 中，也可以新构建一个完整的联系人列表页，组装后的一种 UI 效果如下图所示：

   

   将 ContactList 封装为联系人列表页，可参考 `chat/uikit/lib/contacts_page.dart `文件里的实现，`ContactsPage` 主要做了以下工作：

1. 在 ContactList 上方添加了 AppBar。

2. 实现点击联系人列表 cell 事件。

3. 支持添加联系人和添加群组：

  1. 点击右上角 "+" 按钮可以添加联系人或群组

  2. 支持根据 UserID 搜索目标联系人

  3. 支持根据 GroupID 搜索目标群组

      核心示例代码如下所示：

      ``` java
        @override
        Widget build(BuildContext context) {
          AtomicLocalizations atomicLocale = AtomicLocalizations.of(context);
          SemanticColorScheme colorsScheme = BaseThemeProvider.colorsOf(context);
          return Scaffold(
            appBar: AppBar(
              backgroundColor: colorsScheme.bgColorOperate,
              title: Text(atomicLocale.contact, style: TextStyle(fontSize: 34, fontWeight: FontWeight.w600),),
              centerTitle: false,
              scrolledUnderElevation: 0,
              actions: [],
            ),
            body: ContactList(
              onGroupClick: (ContactInfo contactInfo) {
                _onGroupClick(context, contactInfo);
              },
              onContactClick: (ContactInfo contactInfo) {
                _onContactClick(context, contactInfo);
              },
            ),
          );
        }
      ```

### 步骤6：完善界面间的跳转逻辑

ConversationsPage、ChatPage、ContactsPage 中实现了默认的用户点击事件，您可以自定义事件来实现页面间的交互：
| Page | 回调 | 建议跳转逻辑 |
| --- | --- | --- |
| **ConversationsPage** | `onConversationClick: (conversation) {}` | 点击会话列表中的会话时触发，建议跳转到聊天页面（ChatPage）。 |
| **ContactsPage** | `onContactClick: (ContactInfo contactInfo) {}` | 点击联系人 cell 时触发，建议跳转到用户信息页（C2CChatSetting）。 |
| `onGroupClick: (ContactInfo contactInfo) {}` | 点击群组 cell 时触发，建议跳转群聊页面（ChatPage）。 |  |
| **ChatPage** | `onUserClick: (String userID) {}` | 点击消息中的用户头像或昵称时触发，建议跳转用户信息页（C2CChatSetting）。 |
| `onChatHeaderClick: () {}` | 点击导航栏中的头像时触发，建议跳转用户信息页（C2CChatSetting）或群聊信息页（GroupChatSetting） |  |
| `onBackClick: () {}` | 点击返回按钮时触发，建议返回上一级页面。 |  |

您可以参考上述回调说明及参考代码，实现 Page 之间交互逻辑。

## Platform: React

## 关键概念

chat-uikit-react 主要分为 ConversationList、Chat、MessageList、ChatHeader、MessageInput、ChatSetting、Search、Contact 等核心 UI 组件，每个 UI 组件负责展示不同的内容。
1. ConversationList 提供会话列表组件。

2. Chat 提供会话的容器组件。

3. MessageList 提供会话的消息列表组件。

4. ChatHeader 提供会话的头部信息组件。

5. MessageInput 提供输入框组件。

6. ChatSetting 提供单聊和群聊的管理组件。

7. Search 提供云端搜索组件。

8. Contact 提供联系人组件。

## 创建项目

创建一个新的名为 **chat-example** 的 React 项目（创建项目时选择 **React 18**，并且使用 TypeScript 模板），并按照脚手架提示完成项目的初始化。

【shell】
``` bash
npm create rsbuild@latest 
# 初始化脚手架项目
cd chat-example
npm i
npm run dev
```

## 下载并导入组件

### 步骤 1. 安装依赖

通过 npm 方式下载 [chat-uikit-react](https://www.npmjs.com/package/@tencentcloud/chat-uikit-react) 并在项目中使用。

【shell】
``` bash
npm i @tencentcloud/chat-uikit-react
```

### 步骤 2. 引入组件

> **注意：**
> - 以下代码中未填入 `SDKAppID`、`userID` 和 `userSig`，需在 步骤3 中获取相关信息后进行替换。
> - 为了尊重版权，IM Demo/TUIKit 工程中默认不包含大表情元素切图。正式上线商用前请您替换为自己设计或拥有版权的其他表情包。下图所示默认的**小黄脸表情包版权归腾讯云所有**，可以有偿授权使用，如需获得授权您可以通过升级至 [IM 企业版套餐](https://buy.cloud.tencent.com/avc) 免费使用该表情包。

复制以下 `App.tsx` 代码并替换原有的 `src/App.tsx` 中的内容。

【src/App.tsx】
``` typescript
import { useEffect, useLayoutEffect, useState, useMemo } from "react";
import {
  UIKitProvider,
  useLoginState,
  LoginStatus,
  ConversationList,
  Chat,
  ChatHeader,
  MessageList,
  MessageInput,
  ContactList,
  ContactInfo,
  ChatSetting,
  Search,
  VariantType,
  Avatar,
  useUIKit,
  useConversationListState,
} from "@tencentcloud/chat-uikit-react";
import { IconChat, IconUsergroup, IconBulletpoint, IconSearch } from "@tencentcloud/uikit-base-component-react";
import './App.css';

function App() {
  // 语言支持 en-US(default) / zh-CN / ja-JP / ko-KR / zh-TW
  // 主题支持 light(default) / dark
  return (
    <UIKitProvider theme={'light'} language={'zh-CN'}>
      <ChatApp />
    </UIKitProvider>
  );
}

function ChatApp() {
  const [activeTab, setActiveTab] = useState<'conversations' | 'contacts'>('conversations');
  const [isChatSettingShow, setIsChatSettingShow] = useState(false);
  const [isSearchInChatShow, setIsSearchInChatShow] = useState(false);
  
  const { language, theme } = useUIKit();

  const isDark = theme === 'dark';

  const texts = useMemo(() => 
    language === 'zh-CN'
      ? { emptyTitle: '暂无会话', emptySub: '选择一个会话开始聊天', error: '请检查 SDKAppID, userID, userSig, 通过开发人员工具(F12)查看具体的错误信息', loading: '登录中...' }
      : { emptyTitle: 'No conversation', emptySub: 'Select a conversation to start chatting', error: 'Please check the SDKAppID, userID, and userSig. View the specific error information through the developer tools (F12).', loading: 'Logging in...'},
    [language]
  );

  const { status } = useLoginState({
    SDKAppID: 0, // number 类型
    userID: '',  // string 类型
    userSig: '', // string 类型
  });

  const { setActiveConversation, activeConversation } = useConversationListState();

  // 初始化默认会话 可删除
  useEffect(() => {
    if (status === LoginStatus.SUCCESS) {
      const userID = 'administrator';
      const conversationID = `C2C${userID}`;
      setActiveConversation(conversationID);
    }
  }, [status, setActiveConversation]);

  // 切换会话时自动关闭侧边栏
  useLayoutEffect(() => {
    setIsChatSettingShow(false);
    setIsSearchInChatShow(false);
  }, [activeConversation?.conversationID]);

  if (status === LoginStatus.ERROR) {
    return (
      <div className="loading-container">
        <div className="loading-spinner"></div>
        <div className="loading-text">{texts.error}</div>
      </div>
    );
  }

  if (status !== LoginStatus.SUCCESS) {
    return (
      <div className="loading-container">
        <div className="loading-spinner"></div>
        <div className="loading-text">{texts.loading}</div>
      </div>
    );
  }

  return (
    <div className={`chat-layout ${isDark ? 'dark' : ''}`}>
      {/* 左侧导航 */}
      <SideTab activeTab={activeTab} onTabChange={setActiveTab} />

      {/* 中间列表 会话列表-联系人列表 */}
      <div className="conversation-list-panel">
        {activeTab === 'conversations' ? <ConversationList /> : <ContactList className="contact-list" />}
      </div>

      {/* 右侧聊天 */}
      {activeTab === 'conversations' && (
        <Chat
          className="chat-content-panel"
          PlaceholderEmpty={
            <div className="empty-placeholder">
              <div className="empty-icon">💬</div>
              <div className="empty-title">{texts.emptyTitle}</div>
              <div className="empty-subtitle">{texts.emptySub}</div>
            </div>
          }
        >
          <ChatHeader
            ChatHeaderRight={
              <div className="header-actions">
                <button
                  className="icon-button"
                  onClick={() => setIsSearchInChatShow(!isSearchInChatShow)}
                >
                  <IconSearch size="20px" />
                </button>
                <button
                  className="icon-button"
                  onClick={() => setIsChatSettingShow(!isChatSettingShow)}
                >
                  <IconBulletpoint size="20px" />
                </button>
              </div>
            }
          />
          <MessageList />
          <MessageInput />
          {/* 聊天设置侧边栏 */}
          {isChatSettingShow && (
            <div className="chat-sidebar">
              <div className="chat-sidebar-header">
                <span className="chat-sidebar-title">设置</span>
                <button
                  className="icon-button"
                  onClick={() => setIsChatSettingShow(false)}
                >
                  ✕
                </button>
              </div>
              <ChatSetting />
            </div>
          )}

          {/* 会话内搜索侧边栏 */}
          {isSearchInChatShow && (
            <div className="chat-sidebar">
              <div className="chat-sidebar-header">
                <span className="chat-sidebar-title">群搜索</span>
                <button
                  className="icon-button"
                  onClick={() => setIsSearchInChatShow(false)}
                >
                  ✕
                </button>
              </div>
              <Search variant={VariantType.EMBEDDED} />
            </div>
          )}
        </Chat>
      )}

      {/* 联系人详情 */}
      {activeTab === 'contacts' && (
        <ContactInfo
          className="contact-detail-panel"
          onSendMessage={() => setActiveTab('conversations')}
          onEnterGroup={() => setActiveTab('conversations')}
        />
      )}
    </div>
  );
}

// SideTab 组件：左侧导航栏
interface SideTabProps {
  activeTab: 'conversations' | 'contacts';
  onTabChange: (tab: 'conversations' | 'contacts') => void;
}

function SideTab({ activeTab, onTabChange }: SideTabProps) {
  const { theme } = useUIKit();
  const { loginUserInfo } = useLoginState();
  const isDark = theme === 'dark';

  return (
    <div className={`side-tab ${isDark ? 'dark' : ''}`}>
      {/* 用户头像 */}
      <div className="avatar-wrapper">
        <Avatar src={loginUserInfo?.avatarUrl} />
        <div className="tooltip">
          <div className="tooltip-name">{loginUserInfo?.userName || loginUserInfo?.userId || '未命名'}</div>
          <div className="tooltip-id">ID: {loginUserInfo?.userId}</div>
        </div>
      </div>

      {/* Tab 切换 */}
      <div className="tabs">
        <div
          className={`tab-item ${activeTab === 'conversations' ? 'active' : ''}`}
          onClick={() => onTabChange('conversations')}
          title="会话"
        >
          <IconChat size="24px" />
        </div>

        <div
          className={`tab-item ${activeTab === 'contacts' ? 'active' : ''}`}
          onClick={() => onTabChange('contacts')}
          title="联系人"
        >
          <IconUsergroup size="24px" />
        </div>
      </div>
    </div>
  );
}

export default App;
```

复制以下 `App.css` 代码并替换同级目录的 `src/App.css` 样式文件。

【src/App.css】
``` java

body{margin:0;padding:0;font-family:Inter,Avenir,Helvetica,Arial,sans-serif;background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);min-height:100vh;box-sizing:border-box;}#root{height:100vh;display:flex;justify-content:center;align-items:center;}.chat-layout{height:60vh;aspect-ratio:16 / 9;margin:0 auto;display:flex;border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,0.3),0 0 0 1px rgba(255,255,255,0.1);overflow:hidden;backdrop-filter:blur(10px);}@media (max-width:1920px){.chat-layout{height:80vh;}}.conversation-list-panel{width:300px;display:flex;flex-direction:column;border-right:1px solid var(--uikit-theme-light-stroke-color-primary);overflow:hidden;}.dark .conversation-list-panel{border-right:1px solid var(--uikit-theme-dark-stroke-color-primary);}.contact-list{border-radius:0;}.chat-content-panel{flex:1;border-left:1px solid var(--uikit-theme-light-stroke-color-primary);}.dark .chat-content-panel{border-left:1px solid var(--uikit-theme-dark-stroke-color-primary);}.contact-detail-panel{flex:1;}.icon-button{padding:4px 6px;display:flex;align-items:center;justify-content:center;border:none;background:transparent;border-radius:4px;font-size:20px;color:var(--uikit-theme-light-text-color-link);cursor:pointer;transition:background-color 0.2s;}.dark .icon-button{color:var(--uikit-theme-dark-text-color-link);}.icon-button:hover{background-color:var(--uikit-theme-light-button-color-secondary-hover);}.dark .icon-button:hover{background-color:var(--uikit-theme-dark-button-color-secondary-hover);}.icon-button:active{background-color:var(--uikit-theme-light-button-color-secondary-active);}.dark .icon-button:active{background-color:var(--uikit-theme-dark-button-color-secondary-active);}.header-actions{display:flex;gap:8px;margin-left:8px;color:var(--uikit-theme-light-text-color-link);}.dark .header-actions{color:var(--uikit-theme-dark-text-color-link);}.message-toolbar{display:flex;justify-content:space-between;align-items:center;}.message-toolbar-actions{display:flex;align-items:center;gap:12px;}.chat-sidebar{position:absolute;right:0;top:0;bottom:0;min-width:300px;max-width:400px;display:flex;flex-direction:column;background-color:var(--uikit-theme-light-bg-color-operate);box-shadow:-2px 0 8px rgba(0,0,0,0.04),-4px 0 16px rgba(0,0,0,0.06),-8px 0 32px rgba(0,0,0,0.08);overflow:auto;z-index:1000;}.dark .chat-sidebar{background-color:var(--uikit-theme-dark-bg-color-operate);box-shadow:-2px 0 8px rgba(0,0,0,0.2),-4px 0 16px rgba(0,0,0,0.3),-8px 0 32px rgba(0,0,0,0.4),-1px 0 0 rgba(255,255,255,0.08);}.chat-sidebar-header{position:sticky;top:0;display:flex;align-items:center;justify-content:space-between;padding:12px 16px;background-color:var(--uikit-theme-light-bg-color-operate);border-bottom:1px solid var(--uikit-theme-light-stroke-color-primary);z-index:10;}.dark .chat-sidebar-header{background-color:var(--uikit-theme-dark-bg-color-operate);border-bottom:1px solid var(--uikit-theme-dark-stroke-color-primary);}.chat-sidebar-title{font-size:16px;font-weight:500;color:var(--uikit-theme-light-text-color-primary);}.dark .chat-sidebar-title{color:var(--uikit-theme-dark-text-color-primary);}.empty-placeholder{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;background-color:var(--uikit-theme-light-bg-color-operate);border-left:1px solid var(--uikit-theme-light-stroke-color-primary);color:#adb5bd;}.dark .empty-placeholder{background-color:var(--uikit-theme-dark-bg-color-operate);border-left:1px solid var(--uikit-theme-dark-stroke-color-primary);}.empty-icon{font-size:64px;opacity:0.3;}.empty-title{font-size:16px;font-weight:600;color:#6c757d;}.empty-subtitle{font-size:14px;color:#868e96;}.loading-container{display:flex;flex-direction:column;align-items:center;justify-content:center;height:100vh;gap:24px;}.loading-spinner{width:60px;height:60px;border:4px solid rgba(255,255,255,0.2);border-top-color:#ffffff;border-radius:50%;animation:spin 1s cubic-bezier(0.68,-0.55,0.265,1.55) infinite;box-shadow:0 4px 12px rgba(0,0,0,0.15);}.loading-text{color:#ffffff;font-size:18px;font-weight:500;letter-spacing:0.5px;animation:pulse 2s ease-in-out infinite;}@keyframes spin{0%{transform:rotate(0deg);}100%{transform:rotate(360deg);}}@keyframes pulse{0%,100%{opacity:1;}50%{opacity:0.6;}}.icon-image-effort{display:none;}.side-tab{width:72px;height:100%;background:var(--uikit-theme-light-bg-color-function);display:flex;flex-direction:column;align-items:center;padding:20px 0;transition:background 0.3s;}.side-tab.dark{background:var(--uikit-theme-dark-bg-color-function);}.avatar-wrapper{position:relative;margin-bottom:24px;cursor:pointer;}.avatar-wrapper:hover .tui-avatar{transform:scale(1.05);box-shadow:0 4px 12px rgba(0,0,0,0.15);}.tooltip{position:absolute;left:60px;top:50%;transform:translateY(-50%);padding:8px 12px;background:rgba(0,0,0,0.85);color:#fff;border-radius:6px;white-space:nowrap;opacity:0;visibility:hidden;pointer-events:none;transition:all 0.3s;z-index:1000;}.tooltip::before{content:'';position:absolute;left:-6px;top:50%;transform:translateY(-50%);border:6px solid transparent;border-right-color:rgba(0,0,0,0.85);}.avatar-wrapper:hover .tooltip{opacity:1;visibility:visible;}.tooltip-name{font-size:14px;font-weight:500;margin-bottom:4px;}.tooltip-id{font-size:12px;opacity:0.8;}.tabs{display:flex;flex-direction:column;gap:16px;}.tab-item{width:48px;height:48px;display:flex;align-items:center;justify-content:center;border-radius:12px;cursor:pointer;transition:all 0.3s;color:var(--uikit-theme-light-text-color-primary);}.dark .tab-item{color:var(--uikit-theme-dark-text-color-primary);}.tab-item:hover{background:rgba(0,0,0,0.05);}.tab-item.active{background:var(--uikit-theme-light-button-color-primary-default);color:var(--uikit-theme-light-text-color-button);}.dark .tab-item.active{background:var(--uikit-theme-dark-button-color-primary-default);color:var(--uikit-theme-dark-text-color-button);}.call-kit{position:fixed !important;top:50%;left:50%;z-index:999;transform:translate(-50%,-50%);max-width:800px;max-height:600px;}
```

### 步骤 3. 获取 SDKAppID、userID 和 userSig

在步骤2复制的 `App.tsx` 代码中，登录鉴权信息是空缺的，您还需要在 `useLoginState hook` 中填写您的腾讯云应用的鉴权信息，如下所示：
``` java
const { status } = useLoginState({
    SDKAppID: 0, // number 类型
    userID: '',  // string 类型
    userSig: '', // string 类型
});
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| SDKAppID | Number | SDKAppID 是腾讯云 IM 客户应用的唯一标识。您可以在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 创建新应用，获取 SDKAppID 。 **说明：** SDKAppID 是腾讯云 IM 客户应用的唯一标识。我们建议每一个独立的 App 都申请一个新的 SDKAppID。不同 SDKAppID 之间的消息是天然隔离的，不能互通。 |
| userID | String | 用户的唯一标识符，由您定义，只能包含大小写字母（a-z，A-Z）、数字（0-9）、下划线和连字符。 |
| userSig | String | 用户登录即时通信 IM 的密码，其本质是对 UserID 等信息加密后得到的密文。 **说明：** - 开发环境：快速跑通 Demo，可以通过 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 获取 UserSig。 - 生产环境：将 UserSig 的计算代码集成到您的服务端，并提供面向项目的接口， 正确的 UserSig 签发方式请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。 |

> **注意：**
> 

> 本文示例代码采用从 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 获取 UserSig 的方案，**该方法仅适合本地跑通功能调试**。 正确的 UserSig 签发方式请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
> 

- SDKAppID：在 [即时通信 IM 控制台 > 应用管理](https://console.cloud.tencent.com/im) 单击**创建新应用**，获取 SDKAppID。

   

- userID：单击 [即时通信 IM 控制台 > 消息服务 Chat > 账号管理](https://console.cloud.tencent.com/im/account-management)，切换至目标应用所在账号，您可以创建 2～3 个账号用于体验单聊、群聊的功能。

   

- userSig：单击 [即时通信 IM 控制台 > 开发工具 > UserSig生成校验](https://console.cloud.tencent.com/im/tool-usersig)，切换至目标应用所在账号，填写创建的 userID，即可生成 userSig。

   

## 运行和测试

填入登录鉴权信息后即可运行项目开始体验。

【shell】
``` bash
npm run dev
```

> **注意：**
> - 请确保 步骤3 代码中  SDKAppID、userID 和 userSig 均已成功替换，如未替换将会导致项目表现异常。
> - userID 和 userSig  为一一对应关系，具体参见 生成 UserSig（用户鉴权）。
> - 如遇到项目启动失败，请检查 开发环境要求 是否满足。

## 集成更多高级特性

### 音视频通话

> **说明：**
> 

> TUICallKit 是腾讯云推出一款音视频通话 UI 组件，通过集成该组件，您只需要编写几行代码就可以在聊天应用中体验音视频通话功能。
> 

> 更多详细信息可参考：音视频通话-开通服务。
> 

1. 安装 `@trtc/calls-uikit-react` 依赖

   ``` bash
   npm install @trtc/calls-uikit-react
   ```
2. 从 `@trtc/calls-uikit-react` 导出 `TUICallKit`，并挂载到 DOM 节点上。

   在 `src/App.tsx` 文件中继续补充下面的代码：

   ``` typescript
   // src/App.tsx
   import { TUICallKit } from '@trtc/calls-uikit-react';
   
   function App() {
     return (
       <UIKitProvider>
         // 导入 TUICallKit 并添加到代码中
         <TUICallKit className="call-kit" />
         <ChatApp />
       </UIKitProvider>
     );
   }
   ```
3. 在 `<ChatHeader />` 上启用 `enableCall` 属性

   ``` java
   <ChatHeader enableCall={true} />
   ```
4. 拨打语音通话

   

### 云端搜索

> **说明：**
> 

> 搜索，在客服、社交、在线教育、在线医疗、OA 等场景下是刚需功能，可帮助用户快速查找群组、用户、消息，提升产品使用体验和用户粘性。
> 

> 由于 Web 平台本地存储特殊性等原因，Vue **无法实现本地搜索**，为了更好的满足对于搜索能力的需求，推出了**云端搜索能力**。**云端搜索功能支持全局搜索和会话内搜索，同时支持搜索群组、用户和消息。**
> 

> 此功能属于增值服务，需要您购买云端搜索插件，请点击 [购买](https://console.cloud.tencent.com/im/plugin/TUICloudSearch)。
> 

云端搜索在“步骤 2”中已经默认集成，如果需要关闭云端搜索功能，请参考以下代码：
1. 注释 `ChatHeader` 中 `ChatHeaderRight` 属性，和会话内搜索侧边栏相关的代码

   ``` typescript
   
   <ChatHeader enableCall={true}
     ChatHeaderRight={
       <div className="header-actions">
         <button
           className="icon-button"
           onClick={() => setIsSearchInChatShow(!isSearchInChatShow)}
         >
           <IconSearch size="20px" />
         </button>
         {/* <button
           className="icon-button"
           onClick={() => setIsChatSettingShow(!isChatSettingShow)}
         >
           <IconBulletpoint size="20px" />
         </button> */}
       </div>
     }
   />
   ```

## 参考信息

### 相关文档
- 自定义组件 - UIKitProvider

- 自定义组件 - ConversationList

- 自定义组件 - MessageList

- 自定义组件 - MessageInput

- 自定义组件 - ContactList

- 自定义组件 - Search

- 自定义组件 - ChatSetting

- [chat-uikit-react npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-react)

- [GitHub Demo](https://github.com/TencentCloud/chat-uikit-react)

---

## Platform: Vue

## 关键概念

chat-uikit-vue3 主要分为 ConversationList、Chat、MessageList、ChatHeader、MessageInput、ChatSetting、Search、Contact 等核心 UI 组件，每个 UI 组件负责展示不同的内容。
1. ConversationList 提供会话列表组件。

2. Chat 提供会话的容器组件。

3. MessageList 提供会话的消息列表组件。

4. ChatHeader 提供会话的头部信息组件。

5. MessageInput 提供输入框组件。

6. ChatSetting 提供单聊和群聊的管理组件。

7. Search 提供云端搜索组件。

8. Contact 提供联系人组件。

## 创建项目

使用 Vite 创建一个新的名称为 **chat-example** 的 Vue3 项目。

> **说明：**
> 

> 高版本 Vite 需要使用最新的 Node.js 版本。
> 

【shell】
``` bash
npm create vite@latest
```

## 下载并导入组件

创建项目完成后，切换到项目所在目录。

【shell】
``` bash
cd chat-example
npm install
```

### 步骤1：安装依赖

【shell】
``` bash
npm i @tencentcloud/chat-uikit-vue3
```

### 步骤2：引入组件

> **注意：**
> - 以下代码中未填入 `SDKAppID`、`userID` 和 `userSig`，需在 步骤3 中获取相关信息后进行替换。
> - 为了尊重版权，下图所示的**默认小黄脸表情包版权属于腾讯云**，您可以通过升级至 [IM 企业版套餐](https://buy.cloud.tencent.com/avc) 免费使用该表情包。

#### 2.1 搭建主界面

例如：在  `src/App.vue` 页面中写入以下代码：
``` typescript
<template>
  <UIKitProvider language="zh-CN" theme="light">
    <div class="chat-layout">
      <!-- 左侧导航 -->
      <SideTab :active-tab="activeTab" @tab-change="handleTabChange" />
      
      <!-- 中间列表 -->
      <div class="conversation-list-panel">
        <ConversationList v-show="activeTab === 'conversations'" />
        <ContactList v-show="activeTab === 'contacts'" />
      </div>
      
      <!-- 右侧聊天 -->
      <Chat 
        v-show="activeTab === 'conversations'" 
        class="chat-content-panel" 
        :PlaceholderEmpty="EmptyChatTpl"
      >
        <ChatHeader>
          <template #ChatHeaderRight>
            <button class="icon-button" @click="isChatSettingShow = !isChatSettingShow">
              <IconMenu size="20" />
            </button>
          </template>
        </ChatHeader>
        <MessageList />
        <MessageInput class="message-input-container">
          <template #headerToolbar>
            <div class="message-toolbar">
              <div class="message-toolbar-actions">
                <EmojiPicker />
                <FilePicker />
                <VideoPicker />
                <ImagePicker />
                <!-- 参考文档开通音视频通话服务 -->
                <!-- <AudioCallPicker />
                <VideoCallPicker /> -->
              </div>
              <button class="icon-button" @click="isSearchInChatShow = !isSearchInChatShow">
                <IconSearch size="20" />
              </button>
            </div>
          </template>
        </MessageInput>

        <!-- 聊天设置侧边栏 -->
        <div v-show="isChatSettingShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
          <div class="chat-sidebar-header">
            <span class="chat-sidebar-title">设置</span>
            <button class="icon-button" @click="isChatSettingShow = false">✕</button>
          </div>
          <ChatSetting />
        </div>

        <!-- 会话内搜索侧边栏 -->
        <div v-show="isSearchInChatShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
          <div class="chat-sidebar-header">
            <span class="chat-sidebar-title">群搜索</span>
            <button class="icon-button" @click="isSearchInChatShow = false">✕</button>
          </div>
          <Search :variant="VariantType.EMBEDDED" />
        </div>
      </Chat>

      <!-- 联系人详情 -->
      <ContactInfo
        v-show="activeTab === 'contacts'" 
        class="contact-detail-panel" 
        @send-message="handleSendMessage" 
      />
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref, h, watch } from 'vue';
import {
  Chat, Search, ChatHeader, MessageList, MessageInput, ContactList, ContactInfo, ChatSetting, UIKitProvider, ConversationList, useUIKit, useLoginState, useConversationListState, EmojiPicker, FilePicker, VideoPicker, ImagePicker, VariantType, AudioCallPicker, VideoCallPicker,
} from '@tencentcloud/chat-uikit-vue3';
import { IconMenu, IconSearch, TUIToast } from '@tencentcloud/uikit-base-component-vue3';
import SideTab from './components/SideTab.vue';

const { theme } = useUIKit();
const { login } = useLoginState();
const { setActiveConversation, activeConversation } = useConversationListState();

const activeTab = ref<'conversations' | 'contacts'>('conversations');
const isChatSettingShow = ref(false);
const isSearchInChatShow = ref(false);

// 切换会话时关闭会话内侧边栏
watch(() => activeConversation.value?.conversationID, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    isChatSettingShow.value = false;
    isSearchInChatShow.value = false;
  }
});

// 登录
login({
  sdkAppId: 0, // SDKAppID, number 类型
  userId: '',  // UserID, string 类型
  userSig: '', // userSig, string 类型
})
.then(() => {
  const userID = 'administrator';
  const conversationID = `C2C${userID}`;
  setActiveConversation(conversationID);
})
.catch((err) => {
  TUIToast.error({
    message: err.message,
    duration: 5000,
  });
});

const handleTabChange = (tab: 'conversations' | 'contacts') => {
  activeTab.value = tab;
};

const handleSendMessage = () => {
  activeTab.value = 'conversations';
};

// 空会话占位组件
const EmptyChatTpl = h('div', { class: 'empty-placeholder' }, [
  h('div', { class: 'empty-icon' }, '💬'),
  h('div', { class: 'empty-title' }, '暂无会话'),
  h('div', { class: 'empty-subtitle' }, '选择一个会话开始聊天'),
]);
</script>

<style scoped>
.chat-layout{width:calc(100vw - 64px);height:calc(100vh - 64px);max-width:1600px;max-height:900px;display:flex;border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,0.3),0 0 0 1px rgba(255,255,255,0.1);overflow:hidden;backdrop-filter:blur(10px);}.conversation-list-panel{width:300px;display:flex;flex-direction:column;border-right:1px solid var(--stroke-color-primary);overflow:hidden;}.chat-content-panel{flex:1;}.contact-detail-panel{flex:1;}.icon-button{padding:4px 6px;display:flex;align-items:center;justify-content:center;border:none;background:transparent;border-radius:4px;font-size:20px;color:var(--text-color-primary);cursor:pointer;transition:background-color 0.2s;outline:none;}.icon-button:focus{outline:none;}.icon-button:hover{background-color:var(--button-color-secondary-hover);}.icon-button:active{background-color:var(--button-color-secondary-active);}.message-toolbar{display:flex;justify-content:space-between;align-items:center;}.message-toolbar-actions{display:flex;align-items:center;gap:4px;}.chat-sidebar{position:absolute;right:0;top:0;bottom:0;min-width:300px;max-width:400px;display:flex;flex-direction:column;background-color:var(--bg-color-operate);box-shadow:var(--shadow-color) 0 0 10px;overflow:auto;z-index:1000;}.chat-sidebar.dark{box-shadow:-4px 0 16px rgba(0,0,0,0.4),-1px 0 0 rgba(255,255,255,0.1);}.chat-sidebar-header{position:sticky;top:0;display:flex;align-items:center;justify-content:space-between;padding:12px 16px;background-color:var(--bg-color-operate);border-bottom:1px solid var(--stroke-color-primary);z-index:10;}.chat-sidebar-title{font-size:16px;font-weight:500;color:var(--text-color-primary);}.empty-placeholder{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;background-color:var(--bg-color-operate);border-left:1px solid rgba(0,0,0,0.08);color:#adb5bd;}.empty-icon{font-size:64px;opacity:0.3;}.empty-title{font-size:16px;font-weight:600;color:#6c757d;}.empty-subtitle{font-size:14px;color:#868e96;}img[alt="empty"]{display:none;}.message-input-container{border-top:1px solid var(--stroke-color-primary);}.call-kit{position:fixed;top:50%;left:50%;z-index:999;transform:translate(-50%,-50%);max-width:800px;max-height:600px;}
</style>

<style>
#app{background:linear-gradient(135deg,#667eea 0%,#764ba2 100%) !important;width:100vw !important;height:100vh !important;text-align:start !important;max-width:none !important;display:flex !important;align-items:center !important;justify-content:center !important}
</style>
```

#### 2.2 引入侧边导航

在 `src/component` 文件夹中新建一个 `SideTab.vue` 的文件，并写入以下内容：
``` typescript
<template>
  <div class="side-tab" :class="{ dark: isDark }">
    <!-- 用户头像 -->
    <div class="avatar-wrapper">
      <Avatar class="avatar" :src="loginUserInfo?.avatarUrl" />
      <div class="tooltip">
        <div class="tooltip-name">{{ loginUserInfo?.userName || '未命名' }}</div>
        <div class="tooltip-id">ID: {{ loginUserInfo?.userId }}</div>
      </div>
    </div>
    <!-- Tab 切换 -->
    <div class="tabs">
      <div 
        class="tab-item"
        :class="{ active: props.activeTab === 'conversations' }"
        @click="handleTabChange('conversations')"
        title="会话"
      >
        <IconChatNew size="24" />
      </div>
      <div 
        class="tab-item"
        :class="{ active: props.activeTab === 'contacts' }"
        @click="handleTabChange('contacts')"
        title="联系人"
      >
        <IconContacts size="24" />
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue';
import { useLoginState, useUIKit, Avatar } from '@tencentcloud/chat-uikit-vue3';
import { IconChatNew, IconContacts } from '@tencentcloud/uikit-base-component-vue3';

const { theme } = useUIKit();
const { loginUserInfo } = useLoginState();

const isDark = computed(() => theme.value === 'dark' || theme.value === 'serious');

interface Props {
  activeTab?: 'conversations' | 'contacts';
}

const props = withDefaults(defineProps<Props>(), {
  activeTab: 'conversations'
});

const emit = defineEmits<{
  tabChange: [tab: 'conversations' | 'contacts'];
}>();

const handleTabChange = (tab: 'conversations' | 'contacts') => {
  emit('tabChange', tab);
};
</script>

<style scoped>
.side-tab{width:72px;height:100vh;background:var(--bg-color-function);display:flex;flex-direction:column;align-items:center;padding:20px 0;transition:background 0.3s;}.avatar-wrapper{position:relative;margin-bottom:24px;cursor:pointer;}.avatar-wrapper:hover:deep(.avatar){transform:scale(1.05);box-shadow:0 4px 12px rgba(0,0,0,0.15);}.tooltip{position:absolute;left:60px;top:50%;transform:translateY(-50%);padding:8px 12px;background:rgba(0,0,0,0.85);color:#fff;border-radius:6px;white-space:nowrap;opacity:0;visibility:hidden;pointer-events:none;transition:all 0.3s;z-index:1000;}.tooltip::before{content:'';position:absolute;left:-6px;top:50%;transform:translateY(-50%);border:6px solid transparent;border-right-color:rgba(0,0,0,0.85);}.avatar-wrapper:hover .tooltip{opacity:1;visibility:visible;}.tooltip-name{font-size:14px;font-weight:500;margin-bottom:4px;}.tooltip-id{font-size:12px;opacity:0.8;}.tabs{display:flex;flex-direction:column;gap:16px;}.tab-item{width:48px;height:48px;display:flex;align-items:center;justify-content:center;border-radius:12px;cursor:pointer;transition:all 0.3s;color:var(--text-color-primary);}.tab-item:hover{background:rgba(0,0,0,0.05);}.tab-item.active{background:var(--button-color-primary-default);color:var(--text-color-button);}.side-tab.dark{background:#1a1a1a;}.side-tab.dark .avatar-wrapper:hover:deep(.avatar){box-shadow:0 4px 12px rgba(255,255,255,0.2);}.side-tab.dark .tooltip{background:rgba(255,255,255,0.95);color:#1a1a1a;}.side-tab.dark .tooltip::before{border-right-color:rgba(255,255,255,0.95);}.side-tab.dark .tab-item:hover{background:rgba(255,255,255,0.1);}.side-tab.dark .tab-item.active{background:#1890ff;color:#fff;}
</style>
```

### 步骤3：获取 SDKAppID、userID 和 userSig

在上一步的 `src/App.vue`文件中的 login 函数，填入登录信息 SDKAppID、userID 和 userSig。
``` java
// 登录
login({
  sdkAppId : , // SDKAppID, number 类型
  userId: '',  // UserID, string 类型
  userSig: '', // userSig, string 类型
});
```
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| SDKAppID | Number | SDKAppID 是腾讯云 IM 客户应用的唯一标识。您可以在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 创建新应用，获取 SDKAppID 。 **说明：** SDKAppID 是腾讯云 IM 客户应用的唯一标识。我们建议每一个独立的 App 都申请一个新的 SDKAppID。不同 SDKAppID 之间的消息是天然隔离的，不能互通。 |
| userID | String | 用户的唯一标识符，由您定义，只能包含大小写字母（a-z，A-Z）、数字（0-9）、下划线和连字符。 |
| userSig | String | 用户登录即时通信 IM 的密码，其本质是对 UserID 等信息加密后得到的密文。 **说明：** 开发环境：快速跑通 Demo，可以通过 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 获取 UserSig。 生产环境：将 UserSig 的计算代码集成到您的服务端，并提供面向项目的接口， 正确的 UserSig 签发方式请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。 |

> **注意：**
> 

> 本文示例代码采用在[即时通信 IM 控制台](https://console.cloud.tencent.com/im) 获取 UserSig 的方案，**该方法仅适合本地跑通功能调试**。 正确的 UserSig 签发方式请参见[服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
> 

- SDKAppID：在 [即时通信 IM 控制台 > 应用管理](https://console.cloud.tencent.com/im) 单击**创建新应用**，获取 SDKAppID。

   

- userID：单击 [即时通信 IM 控制台 > 消息服务 Chat > 账号管理](https://console.cloud.tencent.com/im/account-management)，切换至目标应用所在账号，您可以创建 2～3 个账号用于体验单聊、群聊的功能。

   

- userSig：单击 [即时通信 IM 控制台 > 开发工具 > UserSig生成校验](https://console.cloud.tencent.com/im/tool-usersig)，切换至目标应用所在账号，填写创建的 UserID，即可生成 UserSig。

   

## 运行和测试

使用以下命令运行项目

【shell】
``` bash
 npm run dev
```

> **注意：**
> 1. 请确保 步骤3 代码中  SDKAppID、userID 和 userSig 均已成功替换，如未替换将会导致项目表现异常。
> 2. userID 和 userSig  为一一对应关系。
> 3. 如遇到项目启动失败，请检查 开发环境要求 是否满足。

## 集成更多高级特性

### 音视频通话

> **说明：**
> 

> TUICallKit 是腾讯云推出一款音视频通话 UI 组件，通过集成该组件，您只需要编写几行代码就可以在聊天应用中体验音视频通话功能。
> 

> 更多详细信息可参考：音视频通话-开通服务。
> 

1. 安装 `@tencentcloud/call-uikit-vue` 依赖。

   ``` bash
     npm install @tencentcloud/call-uikit-vue
   ```
2. 从 `@tencentcloud/call-uikit-vue` 导出 `TUICallKit`，并挂载到 DOM 节点上。

   在 `src/App.vue` 文件中继续补充下面的代码：

   ``` typescript
   // src/App.vue
   <template>
     <UIKitProvider language="zh-CN" theme="light">
       <!-- 挂载音视频通话核心组件 -->
       <TUICallKit class="call-kit" />
       ...
     </UIKitProvider>
   <template>
   
   <script setup lang="ts">
   // 导入音视频通话核心组件
   import { TUICallKit } from '@tencentcloud/call-uikit-vue';
   </script>
   ```
3. 打开 `<MessageInput />` 组件中 `#headerToolbar` 插槽内的注释。

   ``` java
   <MessageInput class="message-input-container">
     <template #headerToolbar>
       <div class="message-toolbar">
         <div class="message-toolbar-actions">
           <EmojiPicker />
           <FilePicker />
           <VideoPicker />
           <ImagePicker />
           <AudioCallPicker />
           <VideoCallPicker />
         </div>
         <button class="icon-button" @click="isSearchInChatShow = !isSearchInChatShow">
           <IconSearch size="20" />
         </button>
       </div>
     </template>
   </MessageInput>
   ```
4. 拨打语音通话。

   

### 云端搜索

> **说明：**
> 

> 搜索，在客服、社交、在线教育、在线医疗、OA 等场景下是刚需功能，可帮助用户快速查找群组、用户、消息，提升产品使用体验和用户粘性。
> 

> 由于 Web 平台本地存储特殊性等原因，Vue **无法实现本地搜索**，为了更好的满足对于搜索能力的需求，推出了**云端搜索能力**。**云端搜索功能支持全局搜索和会话内搜索，同时支持搜索群组、用户和消息。**
> 

> 此功能属于增值服务，需要您购买云端搜索插件，请点击 [购买](https://console.cloud.tencent.com/im/plugin/TUICloudSearch)。
> 

云端搜索在“步骤 2”中已经默认集成，如果需要关闭云端搜索功能，请参考以下代码：
1. 设置 `ConversationList` 的 `enableSearch` 属性为 `false`

   ``` typescript
   <template>
     <ConversationList v-show="activeTab === 'conversations'" :enable-search="false" />
   </template>
   ```
2. 注释会话内搜索侧边栏相关的代码

   ``` typescript
   <template>
     <Chat>
       <!-- <div v-show="isSearchInChatShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
         <div class="chat-sidebar-header">
           <span class="chat-sidebar-title">搜索</span>
           <button class="icon-button" @click="isSearchInChatShow = false">✕</button>
         </div>
         <Search :variant="VariantType.EMBEDDED" />
       </div> -->
     </Chat>
   </template>
   ```

## 参考信息

### 官网文档
- 自定义组件 - UIKitProvider

- 自定义组件 - Chat

- 自定义组件 - ChatHeader

- 自定义组件 - ConversationList

- 自定义组件 - MessageList

- 自定义组件 - MessageInput

- 自定义组件 - ChatSetting

- 自定义组件 - ContactList

- 自定义组件 - Search

### NPM 包

[chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

### GitHub

[GitHub Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)

## 常见问题

### 功能常见问题

> **适用平台：Android**

**登录失败，提示签名错误**

请检查 SDKAppID 和 UserSig 是否正确，UserSig 是否已过期。可以参考上文 配置用户鉴权 重新生成 UserSig。

#### 1. TUICallKit 和自己集成的音视频库冲突了？

> **适用平台：iOS**

腾讯云的音视频库不能同时集成，可能存在符号冲突，可以按照下面的场景处理。
1. 如果您使用了 `TXLiteAVSDK_TRTC` 库，不会发生符号冲突。可直接在 Podfile 文件中添加依赖，

   ``` ruby
   pod 'TUICallKit_Swift'  
   ```
2. 如果您使用了 `TXLiteAVSDK_Professional` 库，会产生符号冲突。您可在 Podfile 文件中添加依赖，

   ``` ruby
   pod 'TUICallKit_Swift/Professional'
   ```
3. 如果您使用了 `TXLiteAVSDK_Enterprise` 库，会产生符号冲突。建议升级到 `TXLiteAVSDK_Professional` 后使用 TUICallKit_Swift/Professional。

#### 2. 通话邀请的超时时间默认是多久？

> **适用平台：iOS**

通话邀请的默认超时时间是 30 秒。

#### 3. 在邀请超时时间内，被邀请者如果离线再上线，能否立即收到邀请？

> **适用平台：iOS**

- 如果是单聊通话邀请，被邀请者离线再上线可以收到通话邀请，TUIKit 内部会自动唤起通话邀请界面。

- 如果是群聊通话邀请，被邀请者离线再上线后会自动拉取最近 30 秒内的邀请，TUIKit 会自动唤起群通话界面。

#### 4. 上架 Appstore 时打包失败，提示 Unsupported Architectures。

> **适用平台：iOS**

问题现象如下图，打包时提示 ImSDK_Plus.framework 中包含了 Appstore 不支持的 x86_64 模拟器版本。该问题是由于 IMSDK 为了方便开发者调试，发布时会默认带上模拟器版本。

您可以按照下面的步骤，在打包时去掉模拟器版本：
1. 选中您工程的 Target，并点击 Build Phases 选项，在当前面板中添加 Run Script；

   

2. 在新增的 Run Script 中，添加如下脚本：

   ``` bash
   #!/bin/sh
   
   # Strip invalid architectures
   strip_invalid_archs() {
       binary="$1"
       echo "current binary ${binary}"
       # Get architectures for current file
       archs="$(lipo -info "$binary" | rev | cut -d ':' -f1 | rev)"
       stripped=""
       for arch in $archs; do
           if ! [[ "${ARCHS}" == *"$arch"* ]]; then
               if [ -f "$binary" ]; then
                   # Strip non-valid architectures in-place
                   lipo -remove "$arch" -output "$binary" "$binary" || exit 1
                   stripped="$stripped $arch"
               fi
           fi
       done
       if [[ "$stripped" ]]; then
           echo "Stripped $binary of architectures:$stripped"
       fi
   }
   
   APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
   
   # This script loops through the frameworks embedded in the application and
   # removes unused architectures.
   find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
   do
       FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
       FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
       echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
       strip_invalid_archs "$FRAMEWORK_EXECUTABLE_PATH"
   done
   ```

### **集成 tuikit_atomic_x 组件后，有 **`theme_state`** 和 **`atomic_localizations`** 的报错怎么办？**

> **适用平台：Flutter**

组件支持主题颜色和国际化字符串切换，需要在项目根目录的 `main.dart` 文件中使用 `ComponentTheme` 来包装根 `widget`，并且需要在 `MaterialApp` 的 `localizationsDelegates` 中增加 `AtomicLocalizations.delegate` 的配置，可参考源码 Demo 中的 `main.dart` 文件的使用。

### **登录失败，提示签名错误怎么办？**

> **适用平台：Flutter**

请检查 SDKAppID 和 UserSig 是否正确，UserSig 是否已过期。可以参考上文 配置用户鉴权 重新生成 UserSig。

### 什么是 UserSig？如何生成 UserSig？

> **适用平台：React / Vue**

> **React：** UserSig 是用户登录即时通信 IM 的密码，其本质是对 UserID 等信息加密后得到的密文。UserSig 签发方式是需将 UserSig 的计算代码集成到您的服务端，并提供面向项目的接口，在需要 UserSig 时由您的项目向业务服务器发起请求获取动态 UserSig。更多详情请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
> **Vue：** UserSig 是用户登录即时通信 IM 的密码，其本质是对 UserID 等信息加密后得到的密文。UserSig 签发方式是将 UserSig 的计算代码集成到您的服务端，并提供面向项目的接口，在需要 UserSig 时由您的项目向业务服务器发起请求获取动态 UserSig。更多详情请参见 [服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。

> **注意：**
> 

> 本文示例代码采用在 [即时通信 IM 控制台](https://console.cloud.tencent.com/im) 获取 UserSig 的方案，**该方法仅适合本地跑通功能调试**。 正确的 UserSig 签发方式请参见[服务端生成 UserSig](https://cloud.tencent.com/document/product/269/32688)。
>

### 是否支持 react 17/19 版本？

> **适用平台：React**

目前不支持 17.x/19.x 版本，仅支持 React v18。

### 我能不能使用第三方组件库，例如 Ant-Design？

> **适用平台：React**

核心组件之间的粘连代码可以使用其他组件库，这一点在示例代码中也可以看到，例如您可以将 `<ChatSetting />` 封装成全屏抽屉组件。但是核心组件内部已经存在的组件暂时不支持修改。
``` typescript
import { Drawer } from 'antd';

const [isChatSettingShow, setIsChatSettingShow] = useState(false);

function onChatSettingClose() {
  setIsChatSettingShow(false);
}

<Drawer
  title="设置"
  onClose={onClose}
  open={isChatSettingShow}
  <ChatSetting />
</Drawer>
```

### 我能不能使用第三方组件库，例如 Element-Plus？

> **适用平台：Vue**

核心组件之间的粘连代码可以使用其他组件库，这一点在示例代码中也可以看到，例如您可以将 `<ChatSetting />` 封装成全屏抽屉组件。但是核心组件内部已经存在的组件暂时不支持修改。
``` java
const isChatSettingShow = ref(false);

<el-drawer
  v-model="isChatSettingShow"
  title="设置"
    <ChatSetting />
</el-drawer>
```
