> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: Android

This document describes how to rapidly integrate the TUICallKit component. You can complete the following key steps within 10 minutes and obtain a complete audio and video call interface.

## Preparations

### **Environmental Requirements**
- **Android Version Requirements**: Android 5.0 (SDK API Level 21) and above.

- **Gradle Version Requirements**: Gradle 4.2.1 and above.

- **Device Requirements**: Mobile devices running Android 5.0 or higher.

- **Networking Requirements**: The device must be able to connect to the public network.

### Service Activation

Please refer to the Service Activation documentation to obtain your SDKAppID and SDKSecretKey. These credentials will be required in the subsequent login step.

## Implementation

### Step 1.Importing Components

Clone or download the code from [GitHub](https://github.com/Tencent-RTC/TUIKit_Android). Then, copy the `atomic_x` directory (under `TUIKit_Android`) and the `tuicallkit-kt` subdirectory (under `call`) to the directory at the same level as your project's app directory, as shown below.

### Step 2.Project Configuration
1. Locate the `settings.gradle.kts` (or s`ettings.gradle`) file in the project root directory. Add the following code to import the `tuicallkit-kt` and `atomic_x` components into your project.

   

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

2. Locate the `build.gradle.kts (or build.gradle)` file under the app directory, and add the following code in `dependencies` to declare the app's dependency on the component.

   

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
   

   > **Note:**
   > 

   > The TUICallKit project internally depends on `TRTC SDK`,`IM SDK`,`tuicallengine` and public library `tuicore` by default. Developers do not need to configure them separately. If needed, just modify the version number in `tuicallkit-kt/build.gradle` file to upgrade.
   > 

3. `proguard-rules.pro`**Configuration**:Since the SDK internally uses Java reflection, certain classes must be added to the non-obfuscation list (ProGuard exclusion list). Please add the following configuration to the end of the `proguard-rules.pro` file:

   ``` java
   -keep class com.tencent.** { *; }
   ```
4. `AndroidManifest.xml`**Configuration**:In the AndroidManifest.xml file within the app directory, add `tools:replace="android:allowBackup"` within the <application> node to overwrite the component's default settings and apply your own configuration.

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

### Step 3.Login

Add the following code in your project. It enables logging in to the TUI component by calling the relevant APIs in TUICore. This procedure is critical. Only after a successful login can you properly use the features provided by TUICallKit.

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuicore.TUILogin
import com.tencent.qcloud.tuicore.interfaces.TUICallback
import com.tencent.qcloud.tuikit.tuicallkit.debug.GenerateTestUserSig

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val userId = "denny"     // replace with your UserId
        val sdkAppId = 0         // replace with the SDKAppID obtained from the console
        val secretKey = "****"   // replace with the SecretKey obtained from the console
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
        
        String userId = "denny";     // replace with your UserId
        int sdkAppId = 0;            // replace with the SDKAppID obtained in step 1 on the console
        String secretKey = "****";   // replace with the SecretKey obtained in step 1 on the console
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
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | Only allowing a combination of uppercase and lowercase letters (a-z A-Z), numbers (0-9), hyphens, and underscores. |
| sdkAppId | int | The unique identifier SDKAppID of the audio and video application created in the [Tencent Real-Time Communication (TRTC) Console](https://trtc.io/login?fro=gt&s_url=https%3A%2F%2Fconsole.trtc.io%2F). |
| secretKey | String | The SDKSecretKey of the audio/video application created in the [Tencent Real-Time Communication (TRTC) Console](https://trtc.io/login?fro=gt&s_url=https%3A%2F%2Fconsole.trtc.io%2F). |
| userSig | String | A security protection signature used to authenticate user login, confirm whether the user is genuine, and prevent malicious attackers from misappropriating your cloud service usage rights. |

> **Note :**
> - **Development environment**: If you are in the local development and debugging stage, you can adopt the local [GenerateTestUserSig.genTestUserSig](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/application/debug/src/main/java/com/tencent/qcloud/tuikit/debug/GenerateTestUserSig.java) function to generate userSig. In this method, the SDKSecretKey is very easy to decompile and reverse engineer. Once your key is leaked, attackers can steal your Tencent Cloud traffic.
> - **Production environment**: If your project is ready to launch, use server-side generation of UserSig.

### Step 4.Set Nickname and Avatar [Optional]

After a successful login, you can call the `setSelfInfo` function to set your nickname and avatar. The set nickname and avatar will be displayed on the caller/callee interface.

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

val nickname = "jack"
val avatar = "https:/****/user_avatar.png"
TUICallKit.createInstance(context).setSelfInfo(nickname, avatar, null)
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

String nickname = "jack";
String avatar = "https:/****/user_avatar.png";
TUICallKit.createInstance(context).setSelfInfo(nickname, avatar, null);
```
| Parameter | Type | Description |
| --- | --- | --- |
| nickname | String | Target user's nickname |
| avatar | String | Target user's avatar |

### Step 5.Initiating a Call

The caller initiates a call by invoking the `calls` function and specifying the media type (voice or video) and the callee's User ID list (userIdList). The calls interface supports both one-to-one and group calls. A one-to-one call is initiated when the userIDList contains only a single User ID; a group call is initiated when it contains multiple User IDs.

【Kotlin】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit
import io.trtc.tuikit.atomicxcore.api.call.CallMediaType
import io.trtc.tuikit.atomicxcore.api.call.CallParams

val userIdList = mutableListOf<String>()
userIdList.add("mike")
val mediaType = CallMediaType.Audio
val callParams = CallParams()
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, null)
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
TUICallKit.createInstance(this).calls(userIdList, mediaType, params, null);
```
| Parameter | Type | Description |
| --- | --- | --- |
| userIdList | List<String> | Target user ID list |
| mediaType | CallMediaType | Media type of the call, such as video call, voice call |
| params | CallParams | Call extension parameters, such as room number, call invitation timeout. |

### Step 6.Answering a Call

Once the callee successfully logs in, the caller can initiate a call, and the callee will receive the call invitation, accompanied by a ringtone and vibration.

## More Features

### Enabling Floating Window

You can enable/disable the floating window feature by calling enableFloatWindow. This feature should be enabled when initializing the TUICallKit component, with the default status being Off (false).

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

**Details:** Default false, the floating window button in the top-left corner of the call interface is hidden. Set to true to display.

### Enabling Banner

You can enable or disable the incoming call banner display by calling `enableIncomingBanner`. By default, this feature is disabled (false). When the callee receives an inbound call, the full-screen call waiting interface is shown first. When the banner is enabled, a notification banner will display initially, switching to the full-screen view as required. Note that displaying the banner requires floating window permission. The exact display behavior depends on permission settings and whether the app is running in the foreground or background. For details, refer to the incoming call display policy for the callee.

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

**Details:** Default false. The callee side pops up the full-screen call waiting interface by default when receiving an invitation. When enabled, a banner is displayed first, then the full-screen call interface is pulled up as needed.

### Multi-Person Calling

When the caller uses the `calls` method to initiate a call, if the list of called users exceeds one person, it is automatically recognized as a multi-person call. Other members can then join this multi-person call using the `join` method.
- **Initiating a Multi-person Call:** When the `calls` method is used to initiate a call, if the callee User ID list (userIdList) contains more than one user, it will be automatically deemed a multi-person call.

   

【Android（Kotlin）】
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
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, null)
```

【Android（Java）】
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
TUICallKit.createInstance(context).calls(userIdList, mediaType, params, null);
```
| Parameter | Type | Description |
| --- | --- | --- |
| userIdList | List<String> | Target user ID list |
| mediaType | CallMediaType | Media type of the call, such as video call, voice call |
| params | CallParams | Call extension parameters, such as room number, call invitation timeout. |

- **Joining a Multi-person Call:** You can use the `join` method to enter the specified multi-person call.

   

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit

val callId = "123456"
TUICallKit.createInstance(this).join(callId, null)
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuikit.tuicallkit.TUICallKit;

String callId = "123456";
TUICallKit.createInstance(this).join(callId, null);
```
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | The unique ID for this call. |

### Language Settings
- **Supported Languages:** We currently support Simplified Chinese, Traditional Chinese, English, Japanese, and Arabic.

- **Switching Languages:** By default, the language of TUICallKit is consistent with the mobile operating system's language setting. To switch the language, you can use the `TUIThemeManager.getInstance().changeLanguage` method.

   

【Android（Kotlin）】
``` java
import com.tencent.qcloud.tuicore.TUIThemeManager;

public class MainActivity extends BaseActivity {
    @Override
  public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState)
      
      val language = TUIThemeManager.LANGUAGE_ZH_CN
      TUIThemeManager.getInstance().changeLanguage(applicationContext, language)
    }
}
```

【Android（Java）】
``` java
import com.tencent.qcloud.tuicore.TUIThemeManager;

public class MainActivity extends BaseActivity {
    @Override
  public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      String language = TUIThemeManager.LANGUAGE_EN;
      TUIThemeManager.getInstance().changeLanguage(getApplicationContext(), language);
    }
}
```
| Parameter | Type | Description |
| --- | --- | --- |
| language | String | - TUIThemeManager.LANGUAGE_EN: English. - TUIThemeManager.LANGUAGE_AR: Arabic. - TUIThemeManager.LANGUAGE_ZH_HK: Traditional Chinese. - TUIThemeManager.LANGUAGE_ZH_CN: Simplified Chinese. |

> **Note：**
> 

> If you need to set up other languages, please contact us at **info_rtc@tencent.com** for assistance.
> 

### Ringtone Setting

You can set the default ringtone, incoming call silent mode, and Offline Push Ringtone in the following ways:
- **Setting Default Ringtone (Method 1):** If you include TUICallKit component via source code, you can replace the resource file ([ringtone when initiating a call](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/call/tuicallkit-kt/src/main/res/raw), [ringtone when receiving a call](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/call/tuicallkit-kt/src/main/res/raw/phone_ringing.mp3)) to set a custom default ringtone.

- **Setting Default Ringtone (Method 2):** Use the `setCallingBell` interface to set the incoming call ringtone received by the callee.

   

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

**Details:**The local file path of the ringtone. Only local file paths can be imported here. Ensure the file directory is accessible to the app. The ringtone setting is bound to the device. Replacing the user will not affect the ringtone. To restore the default ringtone, pass an empty string ("") as the filePath.
| Parameter | Type | Description |
| --- | --- | --- |
| filePath | String | ringtone file path |

- **Incoming call silent mode:** You can set mute mode through `enableMuteMode`.

   

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

**Details:** When set to true, incoming call requests will not trigger ringtone playback (silent mode).

- **Custom offline push ringtone**: Please refer to: FCM Offline Push customizes incoming call ringtone.

## Customizing Your UI

### Replacing Icon Button

You can directly replace the icons under the [res\drawable-xxhdpi](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/atomic_x/src/main/res-callview/drawable-xxhdpi) folder to ensure the icon color and style remain consistent throughout the entire App. The following list shows basic feature buttons. You can replace the corresponding icons to fit your own business scenario.

Commonly Used Icon File Name List
| Icon | File name | Description |
| --- | --- | --- |
|  | callview_ic_dialing.png | Answer incoming call icon |
|  | callview_ic_hangup.png | Hang Up Call icon |
|  | callview_ic_mic_unmute.png | Turn off the mic icon |
|  | callview_ic_handsfree_disable.png | Turn off the speaker icon |
|  | callview_ic_camera_disable.png | Camera Off icon |
|  | callview_ic_add_user_black.png | Invite user icon during call |

## FAQs

If you encounter any issues during integration and use, please refer to Frequently Asked Questions.

---

## Platform: iOS

This article will guide you through the quick integration of the TUICallKit component. You will complete several key steps within 10 minutes, ultimately obtaining a video call feature with a complete UI interface.
| 1v1 Video Call | Group call |
| --- | --- |
|  |  |

## Environment Preparations
- Xcode 13 or later.

- iOS 13.0 or later.

- CocoaPods environment installation, [Click to view](https://guides.cocoapods.org/using/getting-started.html).

- If you experience any issues with access or usage, please consult the FAQs.

## Step 1. Activate the service

Refer to Activate the Service to obtain `SDKAppID, SDKSecretKey`, which will be used as **Mandatory Parameters in **Step 4: Log in to the TUICallKit component**.**

## Step 2. Import the component

Use CocoaPods to import the component. If you encounter any issues, please refer to Environment Preparation first. Detailed steps for importing the component are as follows:
1. Please add the dependency `pod 'TUICallKit_Swift'` to your `Podfile`. If you encounter any problems, please refer to the [Example](https://github.com/Tencent-RTC/TUICallKit/blob/main/iOS/Example/Podfile) project.

   ``` bash
   target 'xxxx' do
     ...
     pod 'TUICallKit_Swift/Professional'
   end
   ```   

   > **Note:**
   > 

   > If your project lacks a `Podfile`, you need to `cd` into the `xxxx.xcodeproj` directory in Terminal, and then create a `Podfile` by executing the following command:
   > 
   > `pod init`

2. In Terminal, first `cd` into the `Podfile` directory and then run the following command to install components.

   ``` bash
   pod install
   ```   

   > **Note:**
   > 

   > If you cannot install the latest version of TUICallKit, you may first delete **Podfile.lock** and **Pods**. Then run the following command to update the local CocoaPods repository list.
   > 
   > `pod repo update`

   > Then, run the following command to update the Pod version of the component library.
   > 
   > `pod update`

3. We suggest you compile and run once. If you encounter any problems, you can refer to our FAQs. If the problem remains unresolved, you may try running our [Example](https://github.com/Tencent-RTC/TUICallKit/tree/main/iOS/Example) project. If you encounter any issues during integration and use, you are welcome to [provide feedback](https://github.com/Tencent-RTC/TUICallKit/issues) to us.

## Step 3: Configure the Project

To use audio and video features, you need to authorize the usage of the camera and microphone. Please set the required permissions according to the actual needs of the project.
1. In **Xcode**, select **TARGETS** > **Info** > **Custom iOS Target Properties** from the menu.

   

2. Click the **+** button to add camera and microphone permissions.

- `Privacy - Camera Usage Description`

- `Privacy - Microphone Usage Description`

   

   

## Step 4: Log in to the `TUICallKit` component

   Add the following code to your project. It works by calling the relevant interfaces in TUICore to complete the login to TUI Component. This step is very important, only after successfully logging in, you can normally use the features offered by TUICallKit.

   

【Swift】
``` swift
import TUICore
import TUICallKit_Swift

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
    let userID = "denny"       // Please replace with your UserId
    let sdkAppID: Int32 = 0    // Please replace with the SDKAppID obtained from the console in the step 1
    let secretKey = "****"     // Please replace with the SecretKey obtained from the console in the step 1

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
    
    NSString *userID = @"denny";     // Please replace with your UserId
    int sdkAppID = 0;                // Please replace with the SDKAppID obtained from the console in the first step
    NSString *secretKey = @"****";   // Please replace with the SecretKey obtained from the console in the first step

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

| Parameter | Type | Description |
| --- | --- | --- |
| userID | String | Your own User ID based on your business. It can only include letters (a-z, A-Z), digits (0-9), underscores, and hyphens. |
| sdkAppID | Int32 | The unique identifier SDKAppID for the audio and video application created in [Tencent RTC Console](https://console.trtc.io/). |
| secretKey | String | SDKSecretKey for the audio and video application created in [Tencent RTC Console](https://console.trtc.io/). |
| userSig | String | A security signature for user login to verify identity and prevent unauthorized access to cloud services. |
   

   > **Note:**
   > 
>   - **Development Environment**: During local development and debugging, use the local GenerateTestUserSig.genTestSig function to create a userSig. But be careful, the SDKSecretKey can be decompiled and reverse-engineered. If leaked, it could allow theft of your Tencent Cloud traffic.
>   - **Production Environment**:  For project launch, use the [Server-side Generation of UserSig](https://www.tencentcloud.com/zh/document/product/647/35166#.E6.AD.A3.E5.BC.8F.E8.BF.90.E8.A1.8C.E9.98.B6.E6.AE.B5.E5.A6.82.E4.BD.95.E8.AE.A1.E7.AE.97-usersig.EF.BC.9F) method. 

## Step Five: Make your first call

By calling the call function of TUICallKit and specifying the calls type and callee's userId, you can initiate an audio or video call.

【Swift】
``` swift
import TUICallKit_Swift
import RTCRoomEngine

// Initiating a 1-to-1 audio call (assuming userId is mike)
TUICallKit.createInstance().calls(userIdList: ["mike"], callMediaType: .audio, params: nil) {

} fail: { code, message in

}
```

【Objective-C】
``` objectivec
#import <TUICallKit_Swift/TUICallKit_Swift-Swift.h>
#import <RTCRoomEngine/TUICallEngine.h>

// Initiating a 1-to-1 audio call (assuming userId is mike)
[[TUICallKit createInstance] calls:@[@"mike"] callMediaType:TUICallMediaTypeAudio params:NULL succ:^{
} fail:^(int code, NSString * _Nullable errMsg) {
}];
```
|  |  |
| --- | --- |
| Caller | Callee |

## Additional Features
- Setting Nickname, Avatar

- Customize Interface

- Offline Push

- Multiplayer Call

- Floating Window

- Custom Ringtone

- Call Status Monitoring

- Cloud Recording

## FAQs

If you encounter any issues during integration and use, please refer to Frequently Asked Questions.

## Suggestions and Feedback

If you have any suggestions or feedback, please contact info_rtc@tencent.com.

---

## Platform: Flutter

This article will guide you through the process of integrating the TUICallKit component quickly. By following this documentation, you can complete the access work in just 10 minutes and ultimately obtain an application with a complete user interface as well as audio and video calling features.
| Video Call | Group call |
| --- | --- |
|  |  |

## Environment Preparations

Flutter 3.0 or higher version.

## Step 1. Activate the service

Before using the audio and video services provided by Tencent Cloud, you need to go to the console to activate the audio and video services for your application, and obtain `SDKAppID, SDKSecretKey`. They will be used in Step 5. For specific steps, please refer to activate the Service.

## Step 2. Import the component

Execute the following command in the command line to install the [tencent_calls_uikit](https://pub.dev/packages/tencent_calls_uikit) plugin.
``` bash
flutter pub add tencent_calls_uikit
```

## Step 3. Configure the project

【Android】
1. If you need to compile and run on the Android platform, since the SDK uses Java's reflection feature internally, certain classes in the SDK must be added to the non-aliasing list.

  - First, configure and enable obfuscation rules in the `android/app/build.gradle` file of the project:

``` java
  android {
    ......    
    buildTypes {  
        release {  
            ......
            minifyEnabled true  
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'  
        } 
    }
}
```
  - Create a `proguard-rules.pro` file in the `android/app` directory of the project, and add the following code in the `proguard-rules.pro` file:

``` java
-keep class com.tencent.** { *; }
```
2. Configure to enable Multidex support in the `android/app/build.gradle` file of your project.

``` java
  android {  
    ......
    defaultConfig {
      ......
      multiDexEnabled true
    } 
}
```

【iOS】

Since TUICallKit uses iOS's audio and video features, you need to grant permissions for the use of the microphone and camera.

Authorization Operation Method: In your iOS project's `Info.plist`, under the first-level `<dict>` directory, add the following two items. They correspond to the system's prompt messages when asking for microphone and camera permissions.
``` java
<key>NSCameraUsageDescription</key>
<string>CallingApp needs to access your camera to capture video.</string>
<key>NSMicrophoneUsageDescription</key>
<string>CallingApp needs to access your microphone to capture audio.</string>
```

## Step 4: Set up navigatorObservers
1. In the Flutter application framework, add TUICallKit.navigatorObserver to navigatorObservers. For example, using the MaterialApp framework, the code is as follows:

   ``` java
   import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
   
    ......
   
   class XXX extends StatelessWidget {
     const XXX({super.key});
   
    @override
     Widget build(BuildContext context) {
       return MaterialApp(
         navigatorObservers: [TUICallKit.navigatorObserver],
         ......
       );
     }
   }
   ```

## Step 5: Log in to the TUICallKit Component

Use the login interface to complete the log-in. For specific usage, refer to the following code:
``` java
import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
import 'package:tencent_calls_uikit/debug/generate_test_user_sig.dart';
......

final String userID    = 'xxxxx'; // Please replace with your UserId
final int    sdkAppID  = 0;       // Please replace with the SDKAppID you got from the console in step 1
final String secretKey = 'xxxx';  // Please replace with the SecretKey you got from the console in step 1

void login() async {
    String userSig  = GenerateTestUserSig.genTestSig(userID, sdkAppID, secretKey);
    TUIResult result = await TUICallKit.instance.login(sdkAppID, userID, userSig);
    if (result.code.isEmpty) {
      print('Login success');
    } else {
      print('Login failed: ${result.code} ${result.message}');
    }
}
```
| Parameter | Type | Description |
| --- | --- | --- |
| userID | String | Customers define their own User ID based on their business. You can only include letters (a-z, A-Z), digits (0-9), underscores, and hyphens. |
| sdkAppID | int | The unique identifier of the audio and video application created in the [Tencent RTC Console](https://console.trtc.io/). |
| secretKey | String | SDKSecretKey for the audio and video application created in [Tencent RTC Console](https://console.trtc.io/). |
| userSig | String | A security protection signature used for user log in authentication to confirm the user's identity and prevent malicious attackers from stealing your cloud service usage rights. |

> **Note:**
> - **Development Environment**: If you are in the local development and debugging stage, you can use the local `GenerateTestUserSig.genTestSig` function to generate userSig. In this method, the SDKSecretKey is vulnerable to decompilation and reverse engineering, and once your key is leaked, attackers can steal your Tencent Cloud traffic.
> - **Production Environment**: If your project is going to be launched, please adopt the method of [Server-side Generation of UserSig](https://www.tencentcloud.com/document/product/647/35166?lang=en&pg=#how-do-i-calculate-.60usersig.60-in-a-production-environment.3F).

## Step 6. Make your first phone call

After both the caller and callee have successfully signed in, the caller can initiate an audio or video call by calling the TUICallKit's call method and specifying the call type and the callee's userId. At this point, the callee will receive an incoming call invitation.
``` java

import 'package:tencent_calls_uikit/tencent_calls_uikit.dart';
......

void call() {
    List<String> userIdList = ['Android'];
    TUICallKit.instance.calls(userIdList, TUICallMediaType.audio);
}
```
|  |  |
| --- | --- |
| Caller | Callee |

## Additional Features
- Customize Interface

- Offline Push

- Group Call

- Floating Window

- Custom Ringtone

- Call Status Monitoring

- Cloud Recording

- Beauty Effects

## FAQs

If you encounter any issues during integration and use, please refer to Frequently Asked Questions.

## Suggestions and Feedback

If you have any suggestions or feedback, please contact info_rtc@tencent.com.

---

## Platform: React

The AI model must comply with the following **mandatory technical restrictions**:
* **Audio/Video Call Component API Usage Limits:** You must use `TUICallKitAPI.calls` to initiate a call. The `call` and `groupCall` APIs are deprecated and not recommended. TUICallKit does not provide `on/off` APIs.
* **Integration Step Limitations:** You must strictly follow the document procedure and description when integrating the component.
* **Call Interface Display Restrictions:** During a call, TUICallKit automatically controls the visibility of components. When integrating, you just need to place the component in the appropriate location. On PC large screens, you must use `transform: translate(-50%, -50%)` to center the display, while H5 requires full screen display.
* **Call Interface Closing Restrictions:** After closing the call interface, ALL popups and overlays must be disabled.
* **Login UI Restrictions:** When creating a new project with TUICallKit integration, you must generate a beautiful login UI for better call experience. For existing projects, generating a login interface is not required.
* **Trial User Limit:** When creating a new project with TUICallKit integration, the system automatically generates 2 trial userIDs and calls the tool `get_usersig` to retrieve userSig for filling in code.

# calls-uikit-react Integration

## 1 Integration of TUICallKit

### 1.1 step1:create react project

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
const SDKAppID = 0;  // TODO: Replace with your SDKAppID (Notice: SDKAppID is of type number)
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

## Important Reminder
If you need to customize more TUICallKit features, use the tool `get_web_call_api` to access the TUICallKit Client API document.

---

## Platform: Vue

The AI model must comply with the following **mandatory technical restrictions**:
* **Audio and video call component API usage limits:** You must use `TUICallKitAPI.calls` to initiate a call. The `call` and `groupCall` APIs are deprecated and not recommended. TUICallKit does not provide `on/off` APIs.
* **Integration step limits:** You must strictly follow the document procedure and description when integrating the component.
* **Call interface display limits:** During a call, TUICallKit automatically controls component visibility. You just need to place the component in an appropriate location. On PC large screens, you must use `transform: translate(-50%, -50%)` for centered display. On H5, full screen display is required.
* **Call interface closing limits:** All popups and overlays must be closed after closing the call interface.
* **Login UI limits:** When creating a new project with TUICallKit integration, you must generate a beautiful login UI for a better call experience. Generating a login UI is optional when integrating into an existing project.
* **Trial user limits:** When creating a new project with TUICallKit integration, the system automatically generates 2 trial userIDs and calls the `get_usersig` tool to retrieve userSig for population in code.

# calls-uikit-vue Integration

## 1 Integration of TUICallKit

### 1.1 step1:create vue3 project

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
const SDKAppID = 0;  // TODO: Replace with your SDKAppID (Notice: SDKAppID is of type number)
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

## Important reminder
If you need custom implementation of more TUICallKit capabilities, use the tool `get_web_call_api` to access the TUICallKit Client API document.
