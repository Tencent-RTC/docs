> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter, React, Vue**

TUIKit Compose is a Jetpack Compose component library based on Tencent Cloud IM SDK, providing complete solutions for instant messaging features such as chat, conversation list, and contact management. This document describes how to manually integrate these components and implement core functionality.

## Prerequisites
- Android Studio Ladybug | 2024.2.1 and above

- Android 5.0 and above system version

- Gradle 8.9 and above

- Android Gradle Plugin 8.6 and above

- JDK 17 and above

- Kotlin 1.9.0 and above

- A valid Tencent Cloud account and Chat application. You can refer to [Enable Service](https://trtc.io/document/45907?product=chat&menulabel=uikit&platform=android) to get the following information from the console:

  - SDKAppID: The ID of the Chat application obtained from the console, which is the unique identifier of the application

  - SDKSecretKey: The secret key of the application
      

      > **Note:**
      > 
>     - This project currently only supports manual integration and does not support integration through package managers like Maven.
>     - To ensure a stable build environment, please strictly follow the official compatibility requirements for configuration:
>     - For compatibility between Gradle, Android Gradle Plugin, JDK and Android Studio, please refer to the Android official documentation: [Version Notes](https://developer.android.com/build/releases/gradle-plugin#updating-gradle).
>     - For version correspondence between Kotlin, Android Gradle Plugin and Gradle, please refer to the Kotlin official documentation: [Kotlin-Gradle Plugin Compatibility](https://kotlinlang.org/docs/gradle-configure-project.html#apply-the-plugin).

      > We recommend that you choose a version combination that fully matches your project requirements according to the above guidelines.
      >

## Platform: Android

## Key Concepts

TUIKit Compose provides a complete set of instant messaging UI components, depending on the AtomicXCore data layer below and capable of building various functional Pages above.
- Page Layer: Complete functional pages encapsulated based on TUIKit Compose components: ChatPage, ContactsPage, ConversationsPage, which you can use directly.

- TUIKit Compose (UI Component Layer): Jetpack Compose components built on AtomicXCore, which you can embed into your existing App pages.

- AtomicXCore (Data Layer): Provides data management and business logic, including various Stores and Managers.

## Integrate and Import Components

### Download Source Code
1. Download [TUIKit Compose](https://github.com/Tencent-RTC/TUIKit_android_compose) source code from GitHub:

   ``` bash
   git clone https://github.com/Tencent-RTC/TUIKit_Android_Compose.git
   ```

   Project directory structure description:

   ``` bash
   atomic-x/
   ├── src/main/
   │   ├── java/io/trtc/tuikit/atomicx/     # UI component source code (required integration)
   │       ├── messagelist/      # Message list component
   │       ├── messageinput/     # Message input component
   │       ├── conversationlist/ # Conversation list component
   │       ├── contactlist/      # Contact list component
   │       ├── basecomponent/    # Base components
   │       └── ...               # Other UI components
   │   # Resource files (required integration)
   │   ├── res/                    # Common resources
   │   ├── res-message-list/       # Message list component resources
   │   ├── res-message-input/      # Message input component resources
   │   ├── res-conversation-list/  # Conversation list component resources
   │   └── ...                     # Other component resources
   chat/
   ├── uikit/src/main/java/io/trtc/tuikit/chat/pages    # Page components (reference implementation)
   │   ├── ChatPage.kt
   │   ├── ContactsPage.kt
   │   └── ConversationsPage.kt
   └── demo/                 # Sample application (optional reference)
   ```

### Integrate Components
1. Copy the component directory completely to your Android project, as shown in the figure below, where ComposeDemo is the sample project and TUIKit_Android_Compose is the component source code downloaded from GitHub:

   

2. Add TUIKit Compose components in your settings.gradle.kts/settings.gradle file:
   

   > **Note:**
   > 

   > TUIKit_Android_Compose can be placed anywhere, as long as the relative path is correctly set in settings.gradle.
   > 

   

【Kotlin DSL (Recommended)】
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

3. Add the following dependencies in the build.gradle.kts/build.gradle of the app module:

   

【Kotlin DSL (Recommended)】
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
   

   > **Note:**
   > 
>   - When using the local integration solution, if you need to upgrade, you need to get the latest component code from GitHub and overwrite the TUIKit Compose directory in your local project.
>   - When there are conflicts between private modifications and remote changes, manual merging and conflict resolution are required.

### Configure Project
1. Find the AndroidManifest.xml file in the app directory, add `tools:replace="android:allowBackup"` in the `application` node to override the settings in the component and use your own settings.

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
2. Configure dependencies.

   Add the following dependencies in your project's gradle/libs.versions.toml file:

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
3. Enable Compose support.

   Add the following configuration in the build.gradle.kts/build.gradle of the app module:

   

【Kotlin DSL (Recommended)】
``` java
// app/build.gradle.kts
plugins {
    // Add Compose plugin
    alias(libs.plugins.kotlin.compose)
}
android {
    // Enable Compose 
    buildFeatures {
        compose = true
    }
}
```

【Groovy DSL】
``` java
// app/build.gradle
plugins {
    // Add Compose plugin
    id libs.plugins.kotlin.compose.get().pluginId
}
android {
    // Enable Compose 
    buildFeatures {
        compose true
    }
}
```

4. Configure obfuscation rules.

   Add the following obfuscation rules to your app/proguard-rules.pro file:

   ``` java
   -keep class com.tencent.** { *; }
   ```
5. Click **File > Sync Project with Gradle Files** in Android Studio to integrate components.

## Integration Steps

### Step 1: Configure User Authentication

Get UserSig based on UserID in [Chat Console](https://console.trtc.io/usersig) for subsequent user authentication during login.

### Step 2: User Login

You can only use the component functions normally after login authentication. You can call the login interface and pass in the SDKAPPID and userSig obtained above for login authentication:
``` swift
// User login
LoginStore.shared.login(context, SDKAPPID, userID, userSig,
    object : CompletionHandler {
        override fun onSuccess() {
              // Login successful, can navigate to chat or conversation page
        }
        override fun onFailure(code: Int, desc: String) {
              // Login failed, can show error dialog
        }
    })
```

> **Note:**
> 

> In a formal production environment, it is recommended to generate UserSig on your server side. When UserSig is needed, your App should send a request to the business server to get dynamic UserSig for authentication. For details, see [Server-side UserSig Generation](https://console.trtc.io/usersig).
> 

### Step 3: Build Conversation List Interface

You can build a conversation list page based on the ConversationList component in TUIKit Compose. The built-in functions of ConversationList are:
- Display user's conversation list, including single chat and group chat conversations

- Support user operations on individual conversations: pin, delete, clear conversation messages, etc.

   You can integrate ConversationList directly into existing App pages, or build a complete new conversation list page. One assembled UI effect is shown in the figure below:

   

   To encapsulate ConversationList as a conversation list page, you can refer to the implementation in the `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ConversationsPage.kt` file. `ConversationsPage` mainly does the following:

1. Added headerView above ConversationList.

2. Pass click conversation list cell events.

   The core sample code is shown below:

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

### Step 4: Build Chat Interface

You can build a chat page based on the MessageList and MessageInput components in TUIKit Compose.

The built-in functions of MessageList are:
- Display single chat or group chat message lists.

- Support operations on individual messages: view large images of image messages, play video or voice messages, copy text messages, recall messages, delete messages, etc.

   The built-in functions of MessageInput are:

- Support users to compose and send various types of messages: text, emoji, images, voice, video, files, etc.

   You can integrate MessageList and MessageInput directly into existing App pages, or build a complete new chat page. One assembled UI effect is shown in the figure below:

   

   To assemble MessageList and MessageInput into a chat page, you can refer to the implementation in the `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ChatPage.kt` file. `ChatPage` mainly does the following:

1. Added headerView at the top to display the conversation name.

2. Vertically concatenated MessageList and MessageInput according to mobile user habits.

   The core sample code is shown below:

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

### Step 5: Build Contact Interface

You can build a contact page based on the ContactList component in TUIKit Compose. The built-in functions of ContactList are:
- View friend request list.

- View joined group list.

- Handle group invitations and applications.

- Manage blacklist.

- View friends.

   You can integrate ContactList directly into existing Apps, or build a complete new contact list page. One assembled UI effect is shown in the figure below:

   

   To encapsulate ContactList as a contact list page, you can refer to the implementation in the `chat/uikit/src/main/java/io/trtc/tuikit/chat/pages/ContactsPage.kt` file. `ContactsPage` mainly does the following:

1. Added headerView above ContactList.

2. Pass click contact list cell events.

   The core sample code is shown below:

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

### Step 6: Improve Navigation Logic Between Interfaces

ConversationsPage, ChatPage, and ContactsPage expose some user click events. You can customize events to implement interactions between pages:
| Page | Callback | Suggested Navigation Logic |
| --- | --- | --- |
| **ConversationsPage** | `onConversationClick: (ConversationInfo) -> Unit` | Triggered when clicking a conversation in the conversation list, suggest navigating to chat page (ChatPage). |
| **ContactsPage** | `onContactClick: (ContactInfo) -> Unit` | Triggered when clicking a contact cell, suggest navigating to user info page (C2CChatSetting). |
| `onGroupClick: (ContactInfo) -> Unit` | Triggered when clicking a group cell, suggest navigating to group chat page (ChatPage). |  |
| **ChatPage** | `onUserClick: (String) -> Unit` | Triggered when clicking user avatar or nickname in messages, suggest navigating to user info page (C2CChatSetting). |
| `onChatHeaderClick: (String) -> Unit` | Triggered when clicking avatar in navigation bar, suggest navigating to user info page (C2CChatSetting) or group chat info page (GroupChatSetting) |  |
| `onBackClick: () -> Unit` | Triggered when clicking back button, suggest returning to previous page. |  |

**chat/demo/app/src/main/java/io/trtc/tuikit/chat/MainActivity.kt** and **chat/demo/app/src/main/java/io/trtc/tuikit/chat/chat/ChatActivity.kt** serve as the glue layer for the above Pages, implementing callback events for each Page. The core sample code is as follows:
``` java
// chat/demo/app/src/main/java/io/trtc/tuikit/chat/MainActivity.kt

// Click conversation list cell to navigate
ConversationsPage(onConversationClick = {
    activity?.startActivity(
        Intent(activity, ChatActivity::class.java).apply {
            putExtra("conversationID", it.conversationID)
        }
    )
})

// Click contact page to navigate
ContactsPage(onGroupClick = {
    ChatSettingActivity.start(context, "", it.contactID, true)
}, onContactClick = {
    ChatSettingActivity.start(context, it.contactID, "", true)
})
```
``` java
// chat/demo/app/src/main/java/io/trtc/tuikit/chat/chat/ChatActivity.kt

// Click chat page to navigate
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

You can refer to the above callback descriptions and reference code to implement interaction logic between Pages.

---

## Platform: iOS

## Environment Requirements
- Xcode 10 or later

- iOS 9.0 or later

## CocoaPods Integration
1. Install CocoaPods
Enter the following command in a terminal (you need to install Ruby on your Mac first):

   ``` bash
   sudo gem install cocoapods
   ```
2. Create a Podfile
Go to the path where the project is located and run the following command. Then, a Podfile will appear under the project path.

   ``` bash
   pod init
   ```
3. Add the corresponding TUIKit components to your Podfile according to your needs. Components are independent of each other, and adding or removing them will not affect project compilation. You can choose different Podfile integration methods as needed:

  - Remote CocoaPods Integration

  - Local Integration of DevelopmentPods

      The pros and cons of the above two integration methods are shown in the following table:

| Integration methods | Suitable Scenarios | Advantage | Disadvantage |
| --- | --- | --- | --- |
| Remote CocoaPods Integration | Suitable for integration without source code modifications. | When there is a version update of TUIKit, you only need to `Pod update` again to complete the update. | When you have modifications to the source code, using `Pod update` to update will overwrite your modifications with the new version of TUIKit. |
| Local DevelopmentPods Integration | Suitable for customers who have custom modifications to the source code | When you have your own git repository, you can track changes. After modifying the source code, using `Pod update` to update other remote Pod libraries will not overwrite your modifications. | You need to manually overwrite your local TUIKit folder with the TUIKit source code to update. |

### Remote CocoaPods

> **Note:**
> 

> Swift language component support was introduced in TUIKit version 8.5.6870.
> 

You can add components as needed in your Podfile:

【Minimalist version】

【Swift】
``` bash
# Uncomment the next line to define a global platform for your project
# ...
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# Prevent `*.xcassets` in TUIKit from conflicting with your project
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  use_frameworks!
  use_modular_headers!

  # Integrate the chat feature
  pod 'TUIChat_Swift/UI_Minimalist' 
  # Integrate the conversation list feature
  pod 'TUIConversation_Swift/UI_Minimalist'
  # Integrate the relationship chain feature
  pod 'TUIContact_Swift/UI_Minimalist'
  # Integrate the search feature (To use this feature, you need to purchase the  Pro edition 、Pro Plus edition or Enterprise edition)
  pod 'TUISearch_Swift/UI_Minimalist' 
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  # Integrate Translation Plugin(Value-added feature activation required, please contact Tencent Cloud Business to activate)
  pod 'TUITranslationPlugin_Swift'  
  # Integrate Session Tagging Plugin
  pod 'TUIConversationMarkPlugin_Swift'
  # Integrate Speech-to-Text Plugin
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
end
```

【Objective-C】
``` bash
# Uncomment the next line to define a global platform for your project
# ...
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# Prevent `*.xcassets` in TUIKit from conflicting with your project
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  # Comment the next line if you don't want to use dynamic frameworks
  # TUIKit components are dependent on static libraries. Therefore, you need to mask the configuration.
  # use_frameworks!

  # Enable modular headers as needed. Only after you enable modular headers, the Pod module can be imported using @import.
  # use_modular_headers!

  # Integrate the chat feature
  pod 'TUIChat/UI_Minimalist' 
  # Integrate the conversation list feature
  pod 'TUIConversation/UI_Minimalist'
  # Integrate the relationship chain feature
  pod 'TUIContact/UI_Minimalist'
  # Integrate the search feature (To use this feature, you need to purchase the  Pro edition 、Pro Plus edition or Enterprise edition)
  pod 'TUISearch/UI_Minimalist' 
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  # Integrate Translation Plugin, supported starting from version 7.2 (Value-added feature activation required, please contact Tencent Cloud Business to activate)
  pod 'TUITranslationPlugin'  
  # Integrate Session Tagging Plugin, supported starting from version 7.3
  pod 'TUIConversationMarkPlugin'
  # Integrate Speech-to-Text Plugin, supported starting from version 7.5
  pod 'TUIVoiceToTextPlugin'
  # Integrate message push plugin, supported starting from version 7.6
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
end
```

【Classic version】

【Swift】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# Prevent `*.xcassets` in TUIKit from conflicting with your project
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!
  use_modular_headers!

  # Integrate the chat feature
  pod 'TUIChat_Swift/UI_Classic' 
  # Integrate the conversation list feature
  pod 'TUIConversation_Swift/UI_Classic'
  # Integrate the relationship chain feature
  pod 'TUIContact_Swift/UI_Classic'
  # Integrate the search feature (To use this feature, you need to purchase the  Pro edition 、Pro Plus edition or Enterprise edition)
  pod 'TUISearch_Swift/UI_Classic' 
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  # Integrate Voting Plugin
  pod 'TUIPollPlugin_Swift'
  # Integrate Group Chain Plugin
  pod 'TUIGroupNotePlugin_Swift'
  # Integrate Translation Plugin (Value-added feature activation required, please contact Tencent Cloud Business to activate)
  pod 'TUITranslationPlugin_Swift'  
  # Integrate Session Grouping Plugin
  pod 'TUIConversationGroupPlugin_Swift'
  # Integrate Session Tagging Plugin
  pod 'TUIConversationMarkPlugin_Swift'
  # Integrate Speech-to-Text Plugin
  pod 'TUIVoiceToTextPlugin_Swift'
  # Integrate Customer Service Plugin
  pod 'TUICustomerServicePlugin_Swift'

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
end
```

【Objective-C】
``` bash
# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
# Prevent `*.xcassets` in TUIKit from conflicting with your project
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  # Comment the next line if you don't want to use dynamic frameworks
  # TUIKit components are dependent on static libraries. Therefore, you need to mask the configuration.
  # use_frameworks!

  # Enable modular headers as needed. Only after you enable modular headers, the Pod module can be imported using @import.
  # use_modular_headers!

  # Integrate the chat feature
  pod 'TUIChat/UI_Classic' 
  # Integrate the conversation list feature
  pod 'TUIConversation/UI_Classic'
  # Integrate the relationship chain feature
  pod 'TUIContact/UI_Classic'
  # Integrate the search feature (To use this feature, you need to purchase the  Pro edition 、Pro Plus edition or Enterprise edition)
  pod 'TUISearch/UI_Classic' 
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  # Integrate Voting Plugin, supported starting from version 7.1
  pod 'TUIPollPlugin'
  # Integrate Group Chain Plugin, supported starting from version 7.1
  pod 'TUIGroupNotePlugin'
  # Integrate Translation Plugin, supported starting from version 7.2 (Value-added feature activation required, please contact Tencent Cloud Business to activate)
  pod 'TUITranslationPlugin'  
  # Integrate Session Grouping Plugin, supported starting from version 7.3
  pod 'TUIConversationGroupPlugin'
  # Integrate Session Tagging Plugin, supported starting from version 7.3
  pod 'TUIConversationMarkPlugin'
  # Integrate Speech-to-Text Plugin, supported starting from version 7.5
  pod 'TUIVoiceToTextPlugin'
  # Integrate Customer Service Plugin, supported starting from version 7.6
  pod 'TUICustomerServicePlugin'
  # Integrate message push plugin, supported starting from version 7.6
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
end
```

> **Indication**
> 1. If you directly use `pod 'TUIChat'` without specifying classic or minimalist, it will integrate both UI component versions by default. 
> 2. The Classic and Minimalist UI cannot be mixed. When integrating multiple components, you must choose classic UI or minimalist UI at the same time. For instance, the Classic TUIChat component must be used with the Classic versions of the TUIConversation, TUIContact, and TUIGroup. Similarly, the Minimalist version of the TUIChat component must be paired with the Minimalist versions of the TUIConversation, TUIContact.
> 3. If you are using Swift, please enable `use_modular_headers!`, and change the header file reference to @import reference.

After modifying the Podfile, run the following command to install TUIKit.
``` bash
pod install
```

If you cannot install the latest version of TUIKit, run the following command to update the local CocoaPods repository.
``` bash
pod repo update
```

Then run the following command to update the Pod version of the component.
``` bash
pod update
```

After all TUIKit components are integrated, the project structure is as follows:

> **Note:**
> 

> If you encounter any errors in the process, you can refer to the FAQs at the end of the document.
> 

### Local DevelopmentPods
1. Download TUIKit source code from GitHub. Drag it directly into your project directory: 

  1. [Swift TUIKit in Github](https://github.com/Tencent-RTC/Chat_UIKit/tree/main/Swift/TUIKit):

      

  2. [Objective-C TUIKit in Github](https://github.com/TencentCloud/chat-uikit-ios/tree/main/TUIKit):

      

2. Modify the local path of each component in your Podfile. The path is the location of the TUIKit folder relative to your project's Podfile, commonly including:

  - The TUIKit folder is located in the **parent directory** of your project's Podfile: `pod 'TUICore', :path => "../TUIKit/TUICore"`

  - The TUIKit folder is located in the **current directory** of your project's Podfile: `pod 'TUICore', :path => "./TUIKit/TUICore"`

  - The TUIKit folder is located in the **subdirectory** of your project's Podfile: ` pod 'TUICore', :path => "/TUIKit/TUICore"`

      Taking the TUIKit folder located in the parent directory of your project's Podfile as an example:

      

【DevelopmentPodfile】

【Swift】
``` bash
ment# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  use_frameworks!
  use_modular_headers!

  # Integrate the basic library (required)
  pod 'TUICore', :path => "../TUIKit/TUICore"
  pod 'TIMCommon_Swift', :path => "../TUIKit/TIMCommon"
  
  # Integrate TUIKit components (optional)
  # Integrate the chat feature
  pod 'TUIChat_Swift', :path => "../TUIKit/TUIChat"
  # Integrate the conversation list feature
  pod 'TUIConversation_Swift', :path => "../TUIKit/TUIConversation"
  # Integrate the relationship chain feature
  pod 'TUIContact_Swift', :path => "../TUIKit/TUIContact"
  # Integrate the search feature (To use this feature, you need to purchase the Ultimate edition)
  pod 'TUISearch_Swift', :path => "../TUIKit/TUISearch"
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  
  # Integrate the TUIKitPlugin plugin (optional)
  # Integrate translation plugin (Value-added feature activation is required. Please contact Tencent Cloud sales)
  pod 'TUITranslationPlugin_Swift'
  
  # Other Pods
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
end
```

【Objective-C】
``` bash
ment# Uncomment the next line to define a global platform for your project
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '13.0'
install! 'cocoapods', :disable_input_output_paths => true

# Replace `your_project_name` with your actual project name
target 'your_project_name' do
  # Uncomment the next line if you're using Swift or would like to use dynamic frameworks
  use_frameworks!
  use_modular_headers!

  # Integrate the basic library (required)
  pod 'TUICore', :path => "../TUIKit/TUICore"
  pod 'TIMCommon', :path => "../TUIKit/TIMCommon"
  
  # Integrate TUIKit components (optional)
  # Integrate the chat feature
  pod 'TUIChat', :path => "../TUIKit/TUIChat"
  # Integrate the conversation list feature
  pod 'TUIConversation', :path => "../TUIKit/TUIConversation"
  # Integrate the relationship chain feature
  pod 'TUIContact', :path => "../TUIKit/TUIContact"
  # Integrate the search feature (To use this feature, you need to purchase the Ultimate edition)
  pod 'TUISearch', :path => "../TUIKit/TUISearch"
  # Integrate the audio/video call feature
  pod 'TUICallKit_Swift'
  # Integrate the video conference feature
  pod 'TUIRoomKit'
  
  # Integrate the TUIKitPlugin plugin (optional)
  # Note: The TUIKitPlugin plugin version must be the same as the TUICore version.
  # Ensure that the plugin version matches spec.version in "../TUIKit/TUICore/TUICore.spec".
  
  # Integrate the voting plugin, supported from version 7.1
  pod 'TUIPollPlugin'
  # Integrate the group chain plugin, supported from version 7.1
  pod 'TUIGroupNotePlugin'
  # Integrate translation plugin, supported from version 7.2 (Value-added feature activation is required. Please contact Tencent Cloud sales)
  pod 'TUITranslationPlugin'
  # Integrate the session grouping plugin, supported from version 7.3
  pod 'TUIConversationGroupPlugin'
  # Integrate the session tagging plugin, supported from version 7.3
  pod 'TUIConversationMarkPlugin'
  # Integrate the offline push feature
  pod 'TIMPush'
  
  # Other Pods
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
end
```

3. After modifying the Podfile, run the following command to install the local TUIKit components. Example:

   ``` bash
   pod install
   ```   

   > **Note:**
   > 
>   4. When using the local integration solution, if you need to upgrade, you must obtain the latest souce code from Github and overwrite the TUIKit directory in your local project.
>   5. When private modifications conflict with the remote version, manual merging is required to resolve conflicts.
>   6. The TUIKit plugin relies on the version of TUICore, so please ensure that the plugin version matches the spec.version in "../TUIKit/TUICore/TUICore.spec".

## Third-party Library Dependencies

The minimum version requirements for third-party libraries depended on by TUIKit are as follows. If your version is lower, please upgrade to the latest version.

【Swift】
``` plaintext
- MJExtension (3.4.1)
- MJRefresh (3.7.5)
- SnapKit (5.7.1)
- SSZipArchive (2.4.3)
- SDWebImage (5.18.11)
```

【Objective-C】
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

## Build Basic Interfaces

After integrating TUIKit, if you want to continue building basic interfaces for chat, conversation list, etc., please refer to the document: [Build Chat](https://www.tencentcloud.com/document/product/1047/61215), [Build Conversation List.](https://www.tencentcloud.com/document/product/1047/61217)

## Platform: Flutter

## Key Concepts

TUIKit provides a complete set of instant messaging UI components, relying on the AtomicXCore data layer at the bottom and allowing construction of various functional Pages at the top.
- Page Layer: Complete functional pages encapsulated based on TUIKit components: ChatPage, ContactsPage, ConversationsPage, which you can use directly.

- TUIKit Flutter (UI Component Layer): Flutter components built on AtomicXCore, which you can embed into your existing App pages.

- AtomicXCore (Data Layer): Provides data management and business logic, including various Stores and Managers.

## Integration and Import Components

### Download Source Code

1. Download [TUIKit Flutter](https://github.com/Tencent-RTC/TUIKit_Flutter) source code from GitHub:
``` xml
git clone https://github.com/Tencent-RTC/TUIKit_Flutter.git
```

Project directory structure explanation:
``` bash
atomic-x/
├── lib/                  # UI component source code (required integration)
│   ├── messagelist/      # Message list component
│   ├── messageinput/     # Message input component
│   ├── conversationlist/ # Conversation list component
│   ├── contactlist/      # Contact list component
│   ├── basecomponent/    # Base components
│   └── ...               # Other UI components
├── call_assets/          # Call resource files (required integration)
├── chat_assets/          # Chat resource files (required integration)
chat/
├── uikit/                # Page components (reference implementation)
│   ├── lib/
│      ├── chat_page.dart
│      ├── contacts_page.dart
│      └── conversations_page.dart
└── demo/                 # Example application (optional reference)
```

### Integrate Components
1. Copy the complete component directory to your Flutter project, as shown below. Here flutter_demo is the example project, and TUIKit_Flutter is the component source code downloaded from GitHub:

   

2. Add TUIKit Flutter components to your pubspec.yaml file. Note that TUIKit_Flutter can be placed anywhere, as long as the relative path is correctly set in pubspec.yaml:

   ``` bash
     tuikit_atomic_x:
       path: ../TUIKit_Flutter/atomic-x
   ```   

   > **Note:**
   > 
>   - When using the local integration solution, if you need to upgrade, you need to obtain the latest component code from GitHub and overwrite the TUIKit_Flutter directory in your local project.
>   - When there are conflicts between private modifications and remote changes, you need to manually merge and resolve conflicts.

### Configure Project

Add necessary permission configurations on the iOS side. Open ios/Podfile and add the following permission code at the end of the file:
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

## Integration Steps

### Step 1: Configure User Authentication

Obtain UserSig according to UserID in the [Chat Console](https://console.trtc.io/usersig) for subsequent user authentication during login.

### Step 2: User Login

After login authentication, you can use the component features normally. You can call the login interface and pass the SDKAPPID, userID, and userSig obtained above for login authentication:
``` java
final result = await LoginStore.shared.login(
      sdkAppID: SDKAPPID,
      userID: userID,
      userSig: userSig,
    );
if (result.errorCode == 0) {
    // Login successful, can navigate to chat or conversation page
} else {
    // Login failed, can display error popup
}
```

> **Note:**
> 

> In a formal production environment, it is recommended to generate UserSig on your server. When UserSig is needed, your App should send a request to your business server to obtain dynamic UserSig for authentication. See [Generate UserSig on Server](https://trtc.io/document/34385).
> 

### Step 3: Build Conversation List Interface

You can build a conversation list page based on the ConversationList component in TUIKit Flutter. Built-in features of ConversationList include:
- Display the user's conversation list, including one-on-one and group conversations

- Support user operations on individual conversations: pin, delete, clear conversation messages, etc.

   You can integrate ConversationList directly into your existing App page, or build a complete new conversation list page. One assembled UI effect is shown below:

   

   To encapsulate ConversationList as a conversation list page, refer to the implementation in the `chat/uikit/lib/conversations_page.dart` file. `ConversationsPage` mainly does the following:

1. Adds AppBar above ConversationList.

2. Implements click events for conversation list cells.

3. Supports creating conversations and groups:

  1. Click the button in the upper right corner to create new conversations or groups

  2. Supports selecting friends from the contact list to create one-on-one chats

  3. Supports selecting multiple friends to create group chats

      Core example code is as follows:

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

### Step 4: Build Chat Interface

You can build a chat page based on the MessageList and MessageInput components in TUIKit Flutter.

Built-in features of MessageList include:
- Display one-on-one or group message lists.

- Support operations on individual messages: view large images for image messages, play video or voice messages, copy text messages, recall messages, delete messages, etc.

   Built-in features of MessageInput include:

- Support users in composing and sending various types of messages: text, emoji, image, voice, video, file, etc.

   You can integrate MessageList and MessageInput directly into your existing App page, or build a complete new chat page. One assembled UI effect is shown below:

   

   To assemble MessageList and MessageInput as a chat page, refer to the implementation in the `chat/uikit/lib/chat_page.dart` file. `ChatPage` mainly does the following:

1. Adds AppBar at the top to display the conversation name.

2. Vertically combines MessageList and MessageInput according to mobile user habits.

   Core example code is as follows:

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

### Step 5: Build Contact Interface

You can build a contact page based on the ContactList component in TUIKit Flutter. Built-in features of ContactList include:
- View friend request list.

- View list of joined groups.

- Handle group invitations and applications.

- Manage blocklist.

- View friends.

   You can integrate ContactList directly into your existing App, or build a complete new contact list page. One assembled UI effect is shown below:

   

   To encapsulate ContactList as a contact list page, refer to the implementation in the `chat/uikit/lib/contacts_page.dart` file. `ContactsPage` mainly does the following:

1. Adds AppBar above ContactList.

2. Implements click events for contact list cells.

3. Supports adding contacts and groups:

  1. Click the "+" button in the upper right corner to add contacts or groups

  2. Supports searching for target contacts by UserID

  3. Supports searching for target groups by GroupID

      Core example code is as follows:

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

### Step 6: Complete Navigation Logic Between Interfaces

ConversationsPage, ChatPage, and ContactsPage implement default user click events. You can customize events to implement interactions between pages:
| Page | Callback | Suggested Navigation Logic |
| --- | --- | --- |
| **ConversationsPage** | `onConversationClick: (conversation) {}` | Triggered when clicking a conversation in the conversation list, recommended to navigate to the chat page (ChatPage). |
| **ContactsPage** | `onContactClick: (ContactInfo contactInfo) {}` | Triggered when clicking a contact cell, recommended to navigate to the user info page (C2CChatSetting). |
| `onGroupClick: (ContactInfo contactInfo) {}` | Triggered when clicking a group cell, recommended to navigate to the group chat page (ChatPage). |  |
| **ChatPage** | `onUserClick: (String userID) {}` | Triggered when clicking a user avatar or nickname in a message, recommended to navigate to the user info page (C2CChatSetting). |
| `onChatHeaderClick: () {}` | Triggered when clicking the avatar in the navigation bar, recommended to navigate to the user info page (C2CChatSetting) or group chat info page (GroupChatSetting) |  |
| `onBackClick: () {}` | Triggered when clicking the back button, recommended to return to the previous page. |  |

You can refer to the above callback descriptions and reference code to implement interaction logic between Pages.

## Platform: React

## Create a project

Create a new React project named **chat-app** (select **React 18** during project creation and use the TypeScript Template), and complete project initialization as prompted by the scaffold.

【shell】
``` bash
npm create rsbuild@latest
```

## Installing and Importing Components

### Step 1: Dependency Installation

Download [chat-uikit-react](https://www.npmjs.com/package/@tencentcloud/chat-uikit-react) via npm and use it in the project.

【shell】
``` bash
npm i @tencentcloud/chat-uikit-react
```

### Step 2: Introducing the chat uikit react component

> **Note:**
> - The following code does not include `SDKAppID`, `userID`, and `UserSig`. You need to replace them with the relevant information obtained in Step 4.
> - To respect the copyright of emoji designs, the Chat Demo/TUIKit project does not include large emoji graphics. Please replace them with your own designs or other emoji packs for which you hold the copyright before officially launching for commercial use. **The default smiley face emoji pack shown below is copyrighted by Tencent RTC**, you can upgrade to [Chat Pro Plus Edition and Enterprise Edition](https://console.trtc.io/subscription/buy/chat?packType=pro) to use it for free.

Copy the following `App.tsx` code and replace the original content in `App.tsx`.

Copy the following `App.css` code and replace the `App.css` style file in the same directory.

【App.tsx】
``` typescript
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
  useUIKit,
  useConversationListState,
} from "@tencentcloud/chat-uikit-react";
import { useEffect, useState } from "react";
import './App.css';

function App() {
  // Language support en-US(default) / zh-CN / ja-JP / ko-KR / zh-TW
  // Theme supports light(default) / dark
  return (
    <UIKitProvider theme={'light'} language={'en-US'}>
      <ChatApp />
    </UIKitProvider>
  );
}

function ChatApp() {
  const [activeTab, setActiveTab] = useState('chats');
  
  const { language } = useUIKit();
  
const texts = language === 'zh-CN'
    ? { chats: '会话', contacts: '联系人', emptyTitle: '暂无会话', emptySub: '选择一个会话开始聊天', error: '请检查 SDKAppID, userID, userSig, 通过开发人员工具(F12)查看具体的错误信息', loading: '登录中...' }
    : { chats: 'Chats', contacts: 'Contacts', emptyTitle: 'No conversation', emptySub: 'Select a conversation to start chatting', error: 'Please check the SDKAppID, userID, and userSig. View the specific error information through the developer tools (F12).', loading: 'Logging in...'};

  const { status } = useLoginState({
    SDKAppID: 0, // type: number
    userID: '',  // type: string
    userSig: '', // type: string
  })
  const { setActiveConversation } = useConversationListState();

  useEffect(() => {
    async function init() {
      // You can switch to another created userID
      const userID = 'administrator';
      const conversationID = `C2C${userID}`;
      setActiveConversation(conversationID);
    }

    if (status === LoginStatus.SUCCESS) {
      init();
    }
  }, [status]);

  if (status === LoginStatus.ERROR) {
    return (
      <div className="loading-container">
        <div className="loading-spinner"></div>
        <div className="loading-text">{texts.error}</div>
      </div>
    )
  }

  if (status !== LoginStatus.SUCCESS) {
    return (
      <div className="loading-container">
        <div className="loading-spinner"></div>
        <div className="loading-text">{texts.loading}</div>
      </div>
    )
  }

  return (
    <div className="chat-app">
      <ul className="vertical-tabs">
        <li 
          className={`tab-item ${activeTab === 'chats' ? 'active' : ''}`}
          onClick={() => {setActiveTab('chats')}}
        >
          {texts.chats}
        </li>
        <li 
          className={`tab-item ${activeTab === 'contacts' ? 'active' : ''}`}
          onClick={() => {setActiveTab('contacts')}}
        >
          {texts.contacts}
        </li>
      </ul>
      {
        activeTab === 'chats' && (
          <>
            <ConversationList style={{ flex: '0 0 300px'}}/>
            <Chat
              className="chat-box"
              PlaceholderEmpty={
                <div className="empty-chat">
                  <div className="empty-icon">💬</div>
                  <div className="empty-title">{texts.emptyTitle}</div>
                  <div className="empty-subtitle">{texts.emptySub}</div>
                </div>
              }
            >
              <ChatHeader />
              <MessageList />
              <MessageInput />
            </Chat>
          </>
        )
      }
      {
        activeTab === 'contacts' && (
          <>
            <ContactList style={{ flex: '0 0 300px'}}/>
            <ContactInfo
              style={{ flex: '1'}}
              onSendMessage={() => {setActiveTab('chats')}}
              onEnterGroup={() => {setActiveTab('chats')}}
            />
          </>
        )
      }
    </div>
  );
}

export default App;
```

【App.css】
``` css
body {
  margin: 0;
  padding: 20px;
  font-family: Inter, Avenir, Helvetica, Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  box-sizing: border-box;
}

.chat-app {
  height: calc(100vh - 40px);
  max-width: 1400px;
  margin: 0 auto;
  display: flex;
  flex-direction: row;
  background: #ffffff;
  border-radius: 16px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3),
              0 0 0 1px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.vertical-tabs {
  list-style: none;
  margin: 0;
  padding: 0;
  width: 100px;
  background: linear-gradient(180deg, #f8f9fa 0%, #e9ecef 100%);
  border-right: 1px solid rgba(0, 0, 0, 0.08);
}

.tab-item {
  padding: 16px 20px;
  cursor: pointer;
  background: transparent;
  color: #495057;
  font-size: 14px;
  text-align: center;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
}

.tab-item::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 3px;
  height: 0;
  background: linear-gradient(180deg, #667eea 0%, #764ba2 100%);
  border-radius: 0 3px 3px 0;
  transition: height 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.tab-item:hover {
  background: rgba(102, 126, 234, 0.08);
  color: #667eea;
}

.tab-item.active {
  background: linear-gradient(90deg, rgba(102, 126, 234, 0.15) 0%, rgba(102, 126, 234, 0.05) 100%);
  color: #667eea;
}

.tab-item.active::before {
  height: 60%;
}

.chat-box {
  flex: 1;
  border-left: 1px solid rgba(0, 0, 0, 0.08);
}

.empty-chat {
  display: flex;
  flex: 1;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  color: #adb5bd;
  gap: 12px;
  text-align: center;
  border-left: 1px solid rgba(0, 0, 0, 0.08);
}

.empty-icon {
  font-size: 64px;
  opacity: 0.3;
  animation: float 3s ease-in-out infinite;
}

.empty-title {
  font-size: 16px;
  font-weight: 600;
  color: #6c757d;
}

.empty-subtitle {
  font-size: 14px;
  color: #868e96;
}

@keyframes float {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
}

.loading-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  gap: 24px;
}

.loading-spinner {
  width: 60px;
  height: 60px;
  border: 4px solid rgba(255, 255, 255, 0.2);
  border-top-color: #ffffff;
  border-radius: 50%;
  animation: spin 1s cubic-bezier(0.68, -0.55, 0.265, 1.55) infinite;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.loading-text {
  color: #ffffff;
  font-size: 18px;
  font-weight: 500;
  letter-spacing: 0.5px;
  animation: pulse 2s ease-in-out infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.6;
  }
}
```

### Step 3: Obtain SDKAppID, UserID and UserSig

In the previously copied `App.tsx` code, the login authentication information is empty. You need to fill in your Tencent Cloud application authentication information in the `useLoginState hook`, as shown below:
| Parameter | Type | Note |
| --- | --- | --- |
| userID | String | Unique identifier of the user, defined by you, it is allowed to contain only upper and lower case letters (a-z, A-Z), numbers (0-9), underscores, and hyphens. |
| SDKAppID | Number | The unique identifier for the audio and video application created in the [Tencent RTC Console](https://console.trtc.io/). |
| SDKSecretKey | String | The SDKSecretKey of the audio and video application created in the [Tencent RTC Console](https://console.trtc.io/). |
| userSig | String | A security protection signature used for user log in authentication to confirm the user's identity and prevent malicious attackers from stealing your cloud service usage rights. |

> **Explanation of UserSig：**
> 

> **Development environment**: If you are running a demo locally and developing or debugging, you can use the `genTestUserSig` (Refer to Step 3.2) function in the debug file to generate a 'userSig'. In this method, SDKSecretKey is vulnerable to decompilation and reverse engineering. Once your key is leaked, attackers can steal your Tencent Cloud traffic.
> 

> **Production environment**: If your project is going live, please use the [Server-side Generation of UserSig](https://trtc.io/document/34385) method.
> 

1. Log in to  [Chat Console](https://console.trtc.io/) .

2. Click  **Create Application**, enter your application name, then  click  **Create**.

   

3. After creation, you can view the Application Status, service version, SDKAppID, creation time, Tag, and expiration time of the new application on the console overview page.

   

4. [Go to the user management page](https://console.trtc.io/chat/account-management), create 2–3 test accounts for experience in C2C chat and group chat.

   

5. UserSig info. Click [IM console > development tool > UserSig tool](https://console.trtc.io/usersig), fill in the created userID, and just generate userSig.

### Step 4: Start the Project

Replace SDKAppID, UserID, and UserSig in App.tsx, then run the following command:

【shell】
``` bash
 npm run dev
```

> **Note:**
> 1. Please ensure that in Step 3 code, SDKAppID, UserID, and UserSig are all successfully replaced. Failure to replace them will result in abnormal project behavior.
> 2. A `userID` corresponds to a `userSig`, for more information, see [Generating UserSig](https://trtc.io/document/39074?product=consoleguide).
> 3. If the project fails to start, please check whether the development environment requirements are met.

### Step 6: Send your first message

Enter your message in the input box and press Enter to send.

## Communication and Feedback

Join the [Telegram Technical Exchange Group](https://t.me/tencent_imsdk) or [WhatsApp Exchange Group](https://chat.whatsapp.com/IVa11ZkVmKTEwSWsAzSyik) to enjoy support from professional engineers and solve your problems.

## Documentation

#### UIKit  Related:
- [chat-uikit-react npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-react)

- [Demo Source Code and Running Example](https://github.com/TencentCloud/chat-uikit-react)

#### For more features, refer to the ChatEngine API documentation:
- [chat-uikit-engine API Manual](https://web.sdk.qcloud.com/im/doc/chat-engine-lite/index.html)

- [chat-uikit-engine-lite npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-engine-lite)

---

## Platform: Vue

## Key Concepts

The `chat-uikit-vue3` library consists of core UI components including ConversationList, Chat, MessageList, ChatHeader, MessageInput, ChatSetting, Search, and Contact. Each component is responsible for displaying different content:

1. **ConversationList** - Provides the conversation list component.
2. **Chat** - Provides the container component for conversations.
3. **MessageList** - Provides the message list component for conversations.
4. **ChatHeader** - Provides the header information component for conversations.
5. **MessageInput** - Provides the input box component.
6. **ChatSetting** - Provides management components for one-on-one and group chats.
7. **Search** - Provides cloud-based search component.
8. **Contact** - Provides contacts component.

## Create Project

Create a new Vue 3 project named **chat-example** using Vite.

> **Note:**
> Higher versions of Vite require the latest Node.js version.
> 

**[shell]**
```bash
npm create vite@latest
```

## Download and Import Components

After creating the project, navigate to the project directory.

**[shell]**
```bash
cd chat-example
npm install
```

### Step 1: Install Dependencies

**[shell]**
```bash
npm i @tencentcloud/chat-uikit-vue3
```

### Step 2: Import Components

#### 2.1 Build the Main Interface

For example, add the following code to `src/App.vue`:

```typescript
<template>
  <UIKitProvider language="en-US" theme="light">
    <div class="chat-layout">
      <!-- Left Navigation -->
      <SideTab :active-tab="activeTab" @tab-change="handleTabChange" />
      
      <!-- Middle List Panel -->
      <div class="conversation-list-panel">
        <ConversationList v-show="activeTab === 'conversations'" />
        <ContactList v-show="activeTab === 'contacts'" />
      </div>
      
      <!-- Right Chat Panel -->
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
                <!-- Refer to documentation to enable audio/video call services -->
                <!-- <AudioCallPicker />
                <VideoCallPicker /> -->
              </div>
              <button class="icon-button" @click="isSearchInChatShow = !isSearchInChatShow">
                <IconSearch size="20" />
              </button>
            </div>
          </template>
        </MessageInput>

        <!-- Chat Settings Sidebar -->
        <div v-show="isChatSettingShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
          <div class="chat-sidebar-header">
            <span class="chat-sidebar-title">Settings</span>
            <button class="icon-button" @click="isChatSettingShow = false">✕</button>
          </div>
          <ChatSetting />
        </div>

        <!-- In-Chat Search Sidebar -->
        <div v-show="isSearchInChatShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
          <div class="chat-sidebar-header">
            <span class="chat-sidebar-title">Group Search</span>
            <button class="icon-button" @click="isSearchInChatShow = false">✕</button>
          </div>
          <Search :variant="VariantType.EMBEDDED" />
        </div>
      </Chat>

      <!-- Contact Details -->
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

// Close in-chat sidebars when switching conversations
watch(() => activeConversation.value?.conversationID, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    isChatSettingShow.value = false;
    isSearchInChatShow.value = false;
  }
});

// Login
login({
  sdkAppId: 0, // SDKAppID, number type
  userId: '',  // UserID, string type
  userSig: '', // userSig, string type
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

// Empty conversation placeholder component
const EmptyChatTpl = h('div', { class: 'empty-placeholder' }, [
  h('div', { class: 'empty-icon' }, '💬'),
  h('div', { class: 'empty-title' }, 'No conversation'),
  h('div', { class: 'empty-subtitle' }, 'Select a conversation to start chatting'),
]);
</script>

<style scoped>
.chat-layout{width:calc(100vw - 64px);height:calc(100vh - 64px);max-width:1600px;max-height:900px;display:flex;border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,0.3),0 0 0 1px rgba(255,255,255,0.1);overflow:hidden;backdrop-filter:blur(10px);}.conversation-list-panel{width:300px;display:flex;flex-direction:column;border-right:1px solid var(--stroke-color-primary);overflow:hidden;}.chat-content-panel{flex:1;}.contact-detail-panel{flex:1;}.icon-button{padding:4px 6px;display:flex;align-items:center;justify-content:center;border:none;background:transparent;border-radius:4px;font-size:20px;color:var(--text-color-primary);cursor:pointer;transition:background-color 0.2s;outline:none;}.icon-button:focus{outline:none;}.icon-button:hover{background-color:var(--button-color-secondary-hover);}.icon-button:active{background-color:var(--button-color-secondary-active);}.message-toolbar{display:flex;justify-content:space-between;align-items:center;}.message-toolbar-actions{display:flex;align-items:center;gap:4px;}.chat-sidebar{position:absolute;right:0;top:0;bottom:0;min-width:300px;max-width:400px;display:flex;flex-direction:column;background-color:var(--bg-color-operate);box-shadow:var(--shadow-color) 0 0 10px;overflow:auto;z-index:1000;}.chat-sidebar.dark{box-shadow:-4px 0 16px rgba(0,0,0,0.4),-1px 0 0 rgba(255,255,255,0.1);}.chat-sidebar-header{position:sticky;top:0;display:flex;align-items:center;justify-content:space-between;padding:12px 16px;background-color:var(--bg-color-operate);border-bottom:1px solid var(--stroke-color-primary);z-index:10;}.chat-sidebar-title{font-size:16px;font-weight:500;color:var(--text-color-primary);}.empty-placeholder{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;background-color:var(--bg-color-operate);border-left:1px solid rgba(0,0,0,0.08);color:#adb5bd;}.empty-icon{font-size:64px;opacity:0.3;}.empty-title{font-size:16px;font-weight:600;color:#6c757d;}.empty-subtitle{font-size:14px;color:#868e96;}img[alt="empty"]{display:none;}.message-input-container{border-top:1px solid var(--stroke-color-primary);}.call-kit{position:fixed;top:50%;left:50%;z-index:999;transform:translate(-50%,-50%);max-width:800px;max-height:600px;}
</style>

<style>
#app{background:linear-gradient(135deg,#667eea 0%,#764ba2 100%) !important;width:100vw !important;height:100vh !important;text-align:start !important;max-width:none !important;display:flex !important;align-items:center !important;justify-content:center !important}
</style>
```

#### 2.2 Add Side Navigation

Create a new file `SideTab.vue` in the `src/component` folder and add the following content:

```typescript
<template>
  <div class="side-tab" :class="{ dark: isDark }">
    <!-- User Avatar -->
    <div class="avatar-wrapper">
      <Avatar class="avatar" :src="loginUserInfo?.avatarUrl" />
      <div class="tooltip">
        <div class="tooltip-name">{{ loginUserInfo?.userName || 'Unnamed' }}</div>
        <div class="tooltip-id">ID: {{ loginUserInfo?.userId }}</div>
      </div>
    </div>
    <!-- Tab Switcher -->
    <div class="tabs">
      <div 
        class="tab-item"
        :class="{ active: props.activeTab === 'conversations' }"
        @click="handleTabChange('conversations')"
        title="Conversations"
      >
        <IconChatNew size="24" />
      </div>
      <div 
        class="tab-item"
        :class="{ active: props.activeTab === 'contacts' }"
        @click="handleTabChange('contacts')"
        title="Contacts"
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

### Step 3: Obtain SDKAppID, userID, and userSig

Fill in the login credentials SDKAppID, userID, and userSig in the login function in the `src/App.vue` file from the previous step.

```typescript
// Login
login({
  sdkAppId: 0, // SDKAppID, number type
  userId: '',  // UserID, string type
  userSig: '', // userSig, string type
});
```

| Parameter | Type | Description |
|-----------|------|-------------|
| SDKAppID | Number | SDKAppID is the unique identifier for Tencent Cloud Chat client applications. You can create a new application in the [Tencent Cloud Chat Console](https://console.trtc.io/) to obtain the SDKAppID.<br>**Note:**<br>SDKAppID is the unique identifier for Tencent Cloud Chat client applications. We recommend that each independent App apply for a new SDKAppID. Messages between different SDKAppIDs are naturally isolated and cannot communicate with each other. |
| userID | String | The unique identifier for the user, defined by you. It can only contain uppercase and lowercase letters (a-z, A-Z), numbers (0-9), underscores, and hyphens. |
| userSig | String | The password for users to log in to Tencent Cloud Chat, which is essentially a ciphertext obtained by encrypting UserID and other information.<br>**Note:**<br>Development environment: To quickly run the demo, you can obtain UserSig through the [Tencent Cloud Chat Console](https://console.trtc.io/).<br>Production environment: Integrate the UserSig calculation code into your server and provide a project-facing interface. For the correct UserSig issuance method, please refer to [Generating UserSig on the Server](https://trtc.io/document/34385). |

## Run and Test

Use the following command to run the project:

**[shell]**
```bash
npm run dev
```

> **Note:**
> 1. Please ensure that SDKAppID, userID, and userSig in [Step 3] have been successfully replaced. Failure to replace them will cause the project to behave abnormally.
> 2. userID and userSig have a one-to-one correspondence.
> 3. If the project fails to start, please check whether the development environment requirements are met.

## Integrate Advanced Features

### Audio/Video Calling

> **Note:**
> TUICallKit is an audio/video calling UI component launched by Tencent Cloud. By integrating this component, you only need to write a few lines of code to experience audio/video calling functionality in your chat application.
> 

1. Install the `@tencentcloud/call-uikit-vue` dependency.

   ```bash
   npm install @tencentcloud/call-uikit-vue
   ```

2. Export `TUICallKit` from `@tencentcloud/call-uikit-vue` and mount it to a DOM node.

   Continue to add the following code in the `src/App.vue` file:

   ```typescript
   // src/App.vue
   <template>
     <UIKitProvider language="en-US" theme="light">
       <!-- Mount audio/video call core component -->
       <TUICallKit class="call-kit" />
       ...
     </UIKitProvider>
   <template>
   
   <script setup lang="ts">
   // Import audio/video call core component
   import { TUICallKit } from '@tencentcloud/call-uikit-vue';
   </script>
   ```

3. Uncomment the code in the `#headerToolbar` slot of the `<MessageInput />` component.

   ```typescript
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

### Cloud Search

> **Note:**
> Search is an essential feature in customer service, social, online education, online medical, OA and other scenarios. It helps users quickly find groups, users, and messages, improving product user experience and user stickiness.
> Due to the special nature of local storage on the Web platform, Vue **cannot implement local search**. To better meet the demand for search capabilities, a **cloud search capability** has been launched. **Cloud search functionality supports global search and in-conversation search, and supports searching for groups, users, and messages.**
> This feature is a value-added service that requires you to purchase the cloud search plugin.
> 

Cloud search is integrated by default in "Step 2". If you need to disable the cloud search functionality, please refer to the following code:

1. Set the `enableSearch` property of `ConversationList` to `false`

   ```typescript
   <template>
     <ConversationList v-show="activeTab === 'conversations'" :enable-search="false" />
   </template>
   ```

2. Comment out the code related to the in-conversation search sidebar

   ```typescript
   <template>
     <Chat>
       <!-- <div v-show="isSearchInChatShow" class="chat-sidebar" :class="{ dark: theme === 'dark' }">
         <div class="chat-sidebar-header">
           <span class="chat-sidebar-title">Search</span>
           <button class="icon-button" @click="isSearchInChatShow = false">✕</button>
         </div>
         <Search :variant="VariantType.EMBEDDED" />
       </div> -->
     </Chat>
   </template>
   ```

## FAQs

### Xcode Issues

> **Platforms: iOS**

- **Integration error: [Xcodeproj] Unknown object version (60). (RuntimeError)**

   

   When integrating TUIKit in a new project created with Xcode15 and entering `pod install`, you may encounter this issue due to using an older version of CocoaPods. There are two solutions:

   Solution 1: Change the Xcode project's `Project Format` version to Xcode13.0.

   

   Solution 2: Upgrade your local version of CocoaPods. The upgrade method will not be elaborated here.

- **Assertion failed: (false && "compact unwind compressed function offset doesn't fit in 24 bits"), function operator(), file Layout.cpp.**

   

   Or, when integrating TUIRoom with XCode15, the latest linker causes symbol conflicts in TUIRoomEngine, which is also part of this issue.

   

   Solution: Modify the linker configuration. In `Build Settings`, add `-ld64` to `Other Linker Flags`.

   Official Documentation: [https://developer.apple.com/forums/thread/735426](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Fforums%2Fthread%2F735426)

   

- **Rosetta Simulator Issue**

   When using Apple Silicon (M1, M2, etc. series chips), you'll encounter this type of popup. The reason is that some third-party libraries, including SDWebImage, do not support xcframework. However, Apple has still provided an adaptation method, which is to enable Rosetta settings on the emulator. Generally, the Rosetta option will automatically pop up during compilation.

   

- **Xcode 15 Developer Sandbox Option Error: Sandbox: bash(xxx) deny(1) file-write-create**

   

   When you create a new project using Xcode 15, this option may cause compilation and execution failure. It is recommended that you turn off this option.

   

- **Xcode 16 does not support enabling bitcode for Frameworks**

   **Solution 1: Upgrade the SDK**

   If you are using an old version SDK with Bitcode (e.g., TXIMSDK_iOS), it is recommended to follow this document and upgrade the SDK to TXIMSDK_Plus_iOS_XCFramework.

   **Solution 2: Modify the Podfile**

   Add the following configuration to the end of your Podfile and run pod install again.

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

### CocoaPods Issues

> **Platforms: iOS**

- **When using remote integration, issues with mismatched Pod dependency versions**

   If you encounter a mismatch between the Podfile.lock and the plugin dependency version of TUICore while using remote CocoaPods integration, 

   please delete the Podfile.lock file and use `pod repo update`to update your local repository, then use `pod update` to refresh the updates.

   

- **When using local integration, issues with mismatched Pod dependency versions**

   When integrating local DevelopmentPods and the plugin dependency on TUICoreis newer, but the local Pod dependency version is 1.0.0,

   Please refer to [Podfile_local](https://github.com/TencentCloud/chat-uikit-ios/blob/main/Demo/Podfile_local) and [TUICore.spec](https://github.com/TencentCloud/chat-uikit-ios/blob/main/TUIKit/TUICore/TUICore.podspec) for modifications. The plugin needs to follow the version and match the one in TUICore.spec.

   When using local integration for the first time, we recommend you download our sample Demo project, replace the content of the Podfile with Podfile_local, and execute `Pod update` for cross-reference.

### Submission Issues

> **Platforms: iOS**

- **Packaging failure when launching on the Appstore, with an 'Unsupported Architectures' error message.**

   The issue is illustrated below, where packaging indicates the ImSDK_Plus.framework includes an x86_64 simulator version not supported by the Appstore. This is because the SDK, to facilitate developer debugging, defaults to including the simulator version upon release.

   

   You can follow the steps below to remove the simulator version during packaging:

  1. Select your project's Target and click on the `Build Phases` option, then add a `Run Script` to the current panel;

      

  2. In the newly added Run Script, insert the following script:

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

### TUICallKit Issues

> **Platforms: iOS**

- **What should I do if TUICallKit conflicts with an audio/video library that I have integrated?**

   Tencent Cloud's audio and video libraries cannot be integrated simultaneously due to possible symbol conflicts. These can be addressed as follows.

  1. If you have integrated the `TXLiteAVSDK_TRTC` library, a symbol conflict will not occur. You can directly add the dependency in Podfile:

      ``` ruby
      pod 'TUICallKit_Swift'  
      ```
  2. If you have integrated the `TXLiteAVSDK_Professional` library, a symbol conflict will occur. You can add the dependency in Podfile:

      ``` ruby
        pod 'TUICallKit_Swift/Professional'
      ```
  3. If you have integrated the `TXLiteAVSDK_Enterprise` library, a symbol conflict will occur. It is recommended to upgrade to `TXLiteAVSDK_Professional` and then use TUICallKit_Swift/Professional.

- **How long is the default duration for call invitation timeouts?**

   The default timeout duration for a call invitation is 30 seconds.

- **During the invitation timeout period, can the invitee immediately receive the invitation if they go offline and then online?**

   1.If the call invitation is started in a one-to-one chat, the invitee can receive the call invitation, and the TUIKit will automatically open the call invitation UI internally.

   2.If the call invitation is for a group chat, the invitee will automatically fetch invitations from the last 30 seconds upon going online again, and the TUIKit will automatically trigger the group call interface.3.

### **After integrating the tuikit_atomic_x component, what should I do about errors related to **`theme_state`** and **`atomic_localizations`**?**

> **Platforms: Flutter**

The component supports theme color and internationalized string switching. You need to use `ComponentTheme` to wrap the root `widget` in the `main.dart` file in the project root directory, and add the `AtomicLocalizations.delegate` configuration to the `localizationsDelegates` of `MaterialApp`. You can refer to the `main.dart` file in the source code Demo for usage.

#### What is UserSig?

> **Platforms: React**

A UserSig is a password for users to log in to Chat. It is essentially the ciphertext generated by encrypting information such as the UserID.

#### How can I generate a UserSig?

> **Platforms: React**

The issuance method for UserSig involves integrating the calculation code of UserSig into your server, and providing an interface oriented towards your project. When UserSig is needed, your project sends a request to the business server to access the dynamic UserSig. For more information, please see [How to Generate a UserSig on the Server](https://trtc.io/document/39074?product=consoleguide).

> **Note:**
> 

> The exemplary code provided in this document retrieves the UserSig by embedding the SECRETKEY in the client code. This approach makes the SECRETKEY highly susceptible to decompilation and reverse engineering. Once your encryption key is compromised, attackers can misappropriate your Tencent Cloud traffic. Hence, **this procedure is exclusively recommended for running functional debugging locally**. For the correct issuance of UserSig, please refer to the previous sections.
>

### Can I use third-party component libraries, such as Element-Plus?

> **Platforms: Vue**

You can use other component libraries for the glue code between core components, as shown in the example code. For example, you can wrap `<ChatSetting />` as a full-screen drawer component. However, components that already exist within core components are not currently supported for modification.

```typescript
const isChatSettingShow = ref(false);

<el-drawer
  v-model="isChatSettingShow"
  title="Settings"
    <ChatSetting />
</el-drawer>
```

### NPM Package

> **Platforms: Vue**

[chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

### GitHub

> **Platforms: Vue**

[GitHub Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)
