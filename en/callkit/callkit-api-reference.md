> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## Important Notice

**Documentation Content Description:**
- The documentation covers two parts: `TUICallEngine` API and `TUICallKit` UI component API.
- For `TUICallEngine` API documentation, please refer to **Part One**
- For `TUICallKit` UI component API documentation, please refer to **Part Two**

---

## Part One: TUICallEngine API Documentation

**TUICallEngine API Overview**
- When users query `TUICallEngine` API, you must first consult the official TUICallEngine API documentation, then provide correct API usage examples. **Do not speculate on API usage.**
- Below are the documentation links for each platform. Please access the documentation links directly to obtain API usage:
  - [TUICallEngine Web API List Documentation](https://trtc.io/document/51016?product=call&menulabel=uikit&platform=web)
  - [TUICallEngine Web API Usage Examples Documentation](https://web.sdk.qcloud.com/component/trtccalling/doc/TUICallEngine/web/en/TUICallEngine.html)

---

## Part Two: TUICallKit UI Component API Documentation

The TUICallKit API is the **audio and video call component that includes a UI interface**. With the TUICallKit API, you can swiftly develop audio and video call scenarios reminiscent of WeChat through simple interfaces. For further comprehensive steps to access this, please refer to: [Integrating TUICallKit](https://trtc.io/document/50993).

## API Overview
| API | Description |
| --- | --- |
| [<TUICallKit/>](https://www.tencentcloud.com/document/product/647/51015#TUICallKit) | The core UI call component. |
| [init](https://www.tencentcloud.com/document/product/647/51015#init) | Initialize TUICallKit. |
| [calls](https://www.tencentcloud.com/document/product/647/51015#calls) | Initiate a one-to-one or multi-person call. |
| [join](https://www.tencentcloud.com/document/product/647/51015#join) | Proactively join a call. |
| [setCallingBell](https://www.tencentcloud.com/document/product/647/51015#setCallingBell) | Customize user's ringtone. |
| [setSelfInfo](https://www.tencentcloud.com/document/product/647/51015#setSelfInfo) | Set your own nickname and avatar. |
| [enableMuteMode](https://www.tencentcloud.com/document/product/647/51015#enableMuteMode) | Turn on/off ringtone. |
| [enableFloatWindow](https://www.tencentcloud.com/document/product/647/51015#enableFloatWindow) | Turn on/off the floating window function. |
| [enableVirtualBackground](https://www.tencentcloud.com/document/product/647/51015#enableVirtualBackground) | Turn on/off the blurred background function button. |
| [setLanguage](https://www.tencentcloud.com/document/product/647/51015#setLanguage) | Set the call language for the TUICallKit component. |
| [hideFeatureButton](https://www.tencentcloud.com/document/product/647/51015#hideFeatureButton) | Hidden Button. |
| [setLocalViewBackgroundImage](https://www.tencentcloud.com/document/product/647/51015#setLocalViewBackgroundImage) | Set the background image for the local user's call interface. |
| [setRemoteViewBackgroundImage](https://www.tencentcloud.com/document/product/647/51015#setRemoteViewBackgroundImage) | Set the background image for the remote user's call interface. |
| [setLayoutMode](https://www.tencentcloud.com/document/product/647/51015#setLayoutMode) | Set the call interface layout mode. |
| [setCameraDefaultState](https://www.tencentcloud.com/document/product/647/51015#setCameraDefaultState) | et whether the camera is opened by default. |
| [destroyed](https://www.tencentcloud.com/document/product/647/51015#destroyed) | Destroy TUICallKit. |
| [getTUICallEngineInstance](https://www.tencentcloud.com/document/product/647/51015#getTUICallEngineInstance) | Get TUICallEngine instance. |

## `<TUICallKit/>` attributes

### Attribute Overview
| Attribute | Description | Type | Required | Default Value | Vue | React |
| --- | --- | --- | --- | --- | --- | --- |
| allowedMinimized | Is the floating window permitted? | boolean | No | false | √ | √ |
| allowedFullScreen | Whether to permit full screen mode for the call interface | boolean | No | true | √ | √ |
| [videoDisplayMode](https://www.tencentcloud.com/document/product/647/51015#videoDisplayMode) | Display mode for the call interface | VideoDisplayMode | No | VideoDisplayMode.COVER | √ | √ |
| [videoResolution](https://www.tencentcloud.com/document/product/647/51015#videoResolution) | Call Resolution | VideoResolution | No | VideoResolution.RESOLUTION_480P | √ | √ |
| beforeCalling | This function will be executed prior to making a call and before receiving an invitation to talk | function(type, error) | No | - | √ | √ |
| afterCalling | This function will be executed after the termination of the call | function() | No | - | √ | √ |
| onMinimized | This function will be executed when the component switches to a minimized state. The explanation for the STATUS value is | function(oldStatus, newStatus) | No | - | √ | √ |
| kickedOut | The events thrown by the component occur when the current logged-in user is ejected. The call will also automatically terminate | function() | No | - | √ | × |
| statusChanged | Event thrown by the component; this event is triggered when the call status changes. For detailed types of call status, refer to STATUS value description | function({oldStatus, newStatus}) | No | - | √ | × |

### Sample code

【React】
``` bash
import { TUICallKit, VideoDisplayMode, VideoResolution } from "@trtc/calls-uikit-react";

<TUICallKit
    videoDisplayMode={VideoDisplayMode.CONTAIN}
    videoResolution={VideoResolution.RESOLUTION_1080P}
    beforeCalling={handleBeforeCalling} 
    afterCalling={handleAfterCalling} 
/>

function handleBeforeCalling(type: string, error: any) {
  console.log("[TUICallkit Demo] handleBeforeCalling:", type, error);
}
function handleAfterCalling() {
  console.log("[TUICallkit Demo] handleAfterCalling");
}
```

【Vue3】
``` bash
<template>
   <TUICallKit
    :allowedMinimized="true"
    :allowedFullScreen="true"
    :videoDisplayMode="VideoDisplayMode.CONTAIN"
    :videoResolution="VideoResolution.RESOLUTION_1080P"
    :beforeCalling="beforeCalling"
    :afterCalling="afterCalling"
    :onMinimized="onMinimized"
    :kickedOut="handleKickedOut"
    :statusChanged="handleStatusChanged"
  />
</template>
<script lang="ts" setup>
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue";

function beforeCalling(type: string, error: any) {
  console.log("[TUICallkit Demo] beforeCalling:", type, error);
}
function afterCalling() {
  console.log("[TUICallkit Demo] afterCalling");
}
function onMinimized(oldStatus: string, newStatus: string) {
  console.log("[TUICallkit Demo] onMinimized: " + oldStatus + " -> " + newStatus);
}
function kickedOut() {
  console.log("[TUICallkit Demo] kickedOut");
}
function statusChanged(args: { oldStatus: string; newStatus: string; }) {
  const { oldStatus, newStatus } = args;
  if (newStatus === STATUS.CALLING_C2C_VIDEO) {
    console.log(`[TUICallkit Demo] statusChanged: ${oldStatus} -> ${newStatus}`);
  }
}
</script>
```

## Detailed information on TUICallKitAPI API

【React】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react"; 
```

【Vue3】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue"; 
```

### init

Initialize TUICallKit.
``` javascript
try {
  await TUICallKitAPI.init({ SDKAppID, userID, userSig });
  // If you already have a tim instance in your project, you need to pass it in here
  // await TUICallKitAPI.init({ tim, SDKAppID, userID, userSig}); 
  console.log("[TUICallKit] Initialization succeeds.");
} catch (error: any) {
  console.error(`[TUICallKit] Initialization failed. Reason: ${error}`);
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| SDKAppID | Number | Yes | The SDKAppID of your app, you can find your SDKAppID in the Live Audio and Video console. For details, see Activating Services |
| userID | String | Yes | The current user's ID is of string type, only allowing for the inclusion of English letters (a-z and A-Z), digits (0-9), hyphens (-) and underscores (_) |
| userSig | String | Yes | Use SDKSecretKey to encrypt SDKAppID, UserID and other information to obtain userSig. It is an authentication ticket used by Tencent Cloud to identify whether the current user can use TRTC services. For how to obtain it, please refer to How to Calculate UserSig |
| tim | TencentCloudChat | No | tim is an instance of [TencentCloudChat](https://www.npmjs.com/package/@tencentcloud/chat) SDK. |

### calls

Initiate a one-to-one or multi-person call.
``` javascript
try {
  await TUICallKitAPI.calls({ 
    userIDList: ['jack', 'tom'], 
    type: CallMediaType.VIDEO 
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the groupCall API. Reason:${error}`);
}
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| userIDList | Array<String> | Yes | List of called users |
| type | [CallMediaType](https://www.tencentcloud.com/document/product/647/51015#Type) | Yes | The type of media for the call, you can refer to CallMediaType for the explanation of parameter values |
| chatGroupID | String | Yes | chat group Id. |
| roomID | Number | No | Numerical Room ID, range [1, 2147483647] |
| strRoomID | String | No | String room ID. **v3.3.1+ supported** **range：** Limited to 64 bytes in length. The supported character set range is as follows (a total of 89 characters): - Lowercase and uppercase English letters.（a-zA-Z）； - Number（0-9）； - `Spaces`、`!`、`#`、`$`、`%`、`&`、`(`、`)`、`+`、`-`、`:`、`;`、`<`、`=`、`.`、`>`、`?`、`@`、`[`、`]`、`^`、`_`、`{`、`}`、`\\|`、`~`、`,`。 1. roomID and strRoomID are mutually exclusive, if you use strRoomID, then roomID should be 0. If you use both, the SDK will prioritize the roomID. 2. 2. don't mix roomID and strRoomID, because they are not interchangeable, for example, the number 123 and the string "123" are two completely different rooms. |
| timeout | Number | No | Call timeout, default: 30s, unit: seconds. timeout = 0, set to no timeout |
| userData | String | No | Customize the extended fields when initiating a call. The called user has this parameter in the ON_CALL_RECEIVED event. |
| [offlinePushInfo](https://www.tencentcloud.com/document/product/647/51015#offlinePushInfo) | Object | No | Customize offline message push parameters |

### join

Proactively join a call.
``` javascript
try {
  await TUICallKitAPI.join({ 
    callId: 'xx'
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the join API. Reason:${error}`);
}
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| callId | String | Yes | Unique ID for this call |

### setLanguage

Set language, currently supports: Chinese, English, Japanese.
``` javascript
TUICallKitAPI.setLanguage("zh-cn"); // "en" | "zh-cn" | "ja_JP"
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| lang | String | Yes | Language type `en`,`zh-cn` and `ja_JP`. |

### setSelfInfo

Set your own nickname and avatar.

> **Note: **
> 

> **If you use this interface to modify user information during a call, the UI will not be updated immediately, and you will need to wait until the next call to see the changes.**
> 

``` javascript
try {
  await TUICallKitAPI.setSelfInfo({ nickName: "xxx", avatar: "" });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the setSelfInfo API. Reason: ${error}`;
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| nickName | String | Yes | own nickname |
| avatar | String | Yes | own avatar address |

### setCallingBell
- Customize the user's incoming call ringtone.

- The input is restricted to the local MP3 format file address. It is imperative to ensure that the application has access to this file directory.

- Use the import method to import the ringtone file.

- If you need to restore the default ringtone, just pass empty filePath.

   ``` javascript
   import filePath from '../assets/phone_ringing.mp3';
   try {
     await TUICallKitAPI.setCallingBell(filePath);
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the setCallingBell API. Reason: ${error}`);
   }
   ```

   The parameters are described below:

| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| filePath | String | Yes | Ringtone file path |

### enableFloatWindow

Turn on/off the floating window function. The default is false. The floating window button in the upper left corner of the call interface is hidden. It will be displayed after setting it to true.
``` javascript
try {
  const enable = true;
  await TUICallKitAPI.enableFloatWindow(enable);
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableFloatWindow API. Reason: ${error}`);
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| enable | Boolean | Yes | Turn on/off the floating window function. default false. |

### enableMuteMode

Turn on/off the ringtone for incoming calls. When turned on, the incoming call ringtone will not be played when a call request is received.
``` javascript
try {
  const enable = true;
  await TUICallKitAPI.enableMuteMode(enable);
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableMuteMode API. Reason: ${error}`);
}
```

### enableVirtualBackground

Turn on/off the blurred background function. If you want to set the picture background to be blurry see Web. By calling the interface, you can display the blurred background function button on the UI, and click the button to directly enable the blurred background function.
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react";
const enable = true;
TUICallKitAPI.enableVirtualBackground(enable);
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| enable | boolean | Yes | - enable = true, show blur background button - enable = false, don't show blur background button |

### destroyed
- Terminate the TUICallKit instance.

- This method won't automatically log out of `tim`, manual logging out is required: `tim.logout();`.

   ``` javascript
   try {
     await TUICallKitAPI.destroyed();
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the destroyed API. Reason: ${error}`);
   }
   ```

### hideFeatureButton

Hidden feature buttons, currently only support Camera, Microphone, and Switch Camera Button.
``` typescript
TUICallKitAPI.hideFeatureButton(buttonName: FeatureButton);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| buttonName | [FeatureButton](https://www.tencentcloud.com/document/product/647/51015#FeatureButton) | Yes | Button Name |

### setLocalViewBackgroundImage

Set the background image for the local user's call interface.
``` typescript
TUICallKitAPI.setLocalViewBackgroundImage(url: string);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| url | string | Yes | Image Address (supports Local Path and Network Address) |

### setRemoteViewBackgroundImage

Set the background image for the remote user's call interface.
``` typescript
TUICallKitAPI.setRemoteViewBackgroundImage(userId: string, url: string);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| userId | string | Yes | Remote User userId, setting to '*' means it applies to all Remote Users |
| url | string | Yes | Image Address (supports Local Path and Network Address) |

### setLayoutMode

Set the call interface layout mode.

【Vue】
``` javascript
import { TUICallKitAPI, LayoutMode } from "@trtc/calls-uikit-vue";
TUICallKitAPI.setLayoutMode(LayoutMode.LocalInLargeView);
```

【React】
``` javascript
import { TUICallKitAPI, LayoutMode } from "@trtc/calls-uikit-react";
TUICallKitAPI.setLayoutMode(LayoutMode.LocalInLargeView);
```

Parameter list:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| layoutMode | [LayoutMode](https://www.tencentcloud.com/document/product/647/51015#LayoutMode) | Yes | User flow layout mode |

### setCameraDefaultState

Set whether the camera is on by default.
``` javascript
TUICallKitAPI.setCameraDefaultState(true);
```

Parameter list:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| isOpen | boolean | Yes | Whether to enable the camera |

### getTUICallEngineInstance

Get TUICallEngine instance.
``` javascript
TUICallKitAPI.getTUICallEngineInstance();
```

## TUICallKit Type Definition

### videoDisplayMode

There are three values for the `videoDisplayMode` display mode:
- `VideoDisplayMode.CONTAIN`

- `VideoDisplayMode.COVER`

- `VideoDisplayMode.FILL`, the default value is `VideoDisplayMode.COVER`.

| Attribute | Value | Description |
| --- | --- | --- |
| videoDisplayMode | VideoDisplayMode.CONTAIN | - Ensuring the full display of video content is our top priority. - The dimensions of the video are scaled proportionally until one side aligns with the frame of the viewing window. - In case of discrepancy in sizes between the video and the display window, the video is scaled - on the premise of maintaining the aspect ratio - to fill the window, resulting in a black border around the scaled video. |
| VideoDisplayMode.COVER | - Priority is given to ensure that the viewing window is filled. - The video size is scaled proportionally until the entire window is filled. - If the video's dimensions are different from those of the display window, the video stream will be cropped or stretched to match the window's ratio. |  |
| VideoDisplayMode.FILL | - Ensuring that the entire video content is displayed while filling the window does not guarantee preservation of the original video's proportion. - The dimensions of the video will be stretched to match those of the window. |  |

### videoResolution

The resolution `videoResolution` has three possible values:
- `VideoResolution.RESOLUTION_480P`

- `VideoResolution.RESOLUTION_720P`

- `VideoResolution.RESOLUTION_1080P`, the default value is `VideoResolution.RESOLUTION_480P`.

   **Resolution Explanation:**

| Video Profile | Resolution (W x H) | Frame Rate (fps) | Bitrate (Kbps) |
| --- | --- | --- | --- |
| 480p | 640 × 480 | 15 | 900 |
| 720p | 1280 × 720 | 15 | 1500 |
| 1080p | 1920 × 1080 | 15 | 2000 |

   **Frequently Asked Questions:**

- iOS 13&14 does not support encoding videos higher than 720P. It is suggested to limit the highest collection to 720P on these two system versions. Refer to [iOS Safari known issue case 12](https://web.sdk.qcloud.com/trtc/webrtc/doc/en/tutorial-02-info-webrtc-issues.html#h2-4).

- Firefox does not permit the customization of video frame rates (default is set to 30fps).

- Due to the influence of system performance usage, camera collection capabilities, browser restrictions, and other factors, the actual values of video resolution, frame rate, and bit rate may not necessarily match the set values exactly. In such scenarios, the browser will automatically adjust the Profile to get as close to the set values as feasible.

### STATUS
| STATUS attribute value | Description |
| --- | --- |
| STATUS.IDLE | Idle status |
| STATUS.BE_INVITED | Received an Audio/Video Call Invite |
| STATUS.DIALING_C2C | Initiating a one-to-one call |
| STATUS.DIALING_GROUP | Initiating a group call |
| STATUS.CALLING_C2C_AUDIO | Engaged in a 1v1 Audio Call |
| STATUS.CALLING_C2C_VIDEO | In the midst of a one-to-one video call |
| STATUS.CALLING_GROUP_AUDIO | Engaged in Group Audio Communication |
| STATUS.CALLING_GROUP_VIDEO | Engaged in group video call |

### **CallMediaType**
| CallMediaType Type | Description |
| --- | --- |
| CallMediaType.AUDIO_CALL | Audio Call |
| CallMediaType.VIDEO | Video Call |

### **offlinePushInfo**
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| offlinePushInfo.title | String | No | Offline Push Title (Optional) |
| offlinePushInfo.description | String | No | Offline Push Content (Optional) |
| offlinePushInfo.androidOPPOChannelID | String | No | Setting the channel ID for OPPO phones with 8.0 system and above for offline pushes (Optional) |
| offlinePushInfo.extension | String | No | Offline push through content. Can be used to set Android Notification mode and VoIP mode.** Default**: Notification mode, it will be a notification from the system; VoIP mode is required to pass the field. |
| offlinePushInfo.ignoreIOSBadge | Boolean | No | Ignore badge count for offline push (only for iOS), if set to true, the message will not increase the unread count of the app icon on the iOS receiver's side. |
| offlinePushInfo.iOSSound | String | No | Offline push sound setting (only for iOS). |
| offlinePushInfo.androidSound | String | No | Offline push sound setting. |
| offlinePushInfo.androidVIVOClassification | Number | No | Classification of VIVO push messages (deprecated interface, VIVO push service will optimize message classification rules on April 3, 2023. It is recommended to use setAndroidVIVOCategory to set the message category). 0: Operational messages, 1: System messages. The default value is 1. |
| offlinePushInfo.androidXiaoMiChannelID | String | No | Set the channel ID for Xiaomi phones with Android 8.0 and above systems. |
| offlinePushInfo.androidFCMChannelID | String | No | Set the channel ID for google phones with Android 8.0 and above systems. |
| offlinePushInfo.androidHuaWeiCategory | String | No | Classification of Huawei push messages. |
| offlinePushInfo.isDisablePush | Boolean | No | Whether to turn off push notifications (default is on). |
| offlinePushInfo.iOSPushType | Number | No | iOS offline push type，default is 0。0-APNs；1-VoIP. |

#### Android Notification Mode
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, VoIP; 1, notification
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

#### Android VoIP Mode
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, data; 1, notification
    fcmNotificationType: 1, // 0, TIMPush implementation； 1, business implementation after transparent transmission 
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

### FeatureButton
| FeatureButton Type | Description |
| --- | --- |
| FeatureButton.Camera | Camera Button |
| FeatureButton.Microphone | Microphone Button |
| FeatureButton.SwitchCamera | Switches between the front and rear cameras. |
| FeatureButton.InviteUser | Invite users button |

### LayoutMode
| LayoutMode type | Description |
| --- | --- |
| LayoutMode.LocalInLargeView | Local user in large window display |
| LayoutMode.RemoteInLargeView | Remote user in large window display |

---

## Platform: Vue

## Important Notice

**Documentation Content Description:**
- The documentation covers two parts: `TUICallEngine` API and `TUICallKit` UI component API.
- For `TUICallEngine` API documentation, please refer to **Part One**
- For `TUICallKit` UI component API documentation, please refer to **Part Two**

---

## Part One: TUICallEngine API Documentation

**TUICallEngine API Overview**
- When users query `TUICallEngine` API, you must first consult the official TUICallEngine API documentation, then provide correct API usage examples. **Do not speculate on API usage.**
- Below are the documentation links for each platform. Please access the documentation links directly to obtain API usage:
  - [TUICallEngine Web API List Documentation](https://trtc.io/document/51016?product=call&menulabel=uikit&platform=web)
  - [TUICallEngine Web API Usage Examples Documentation](https://web.sdk.qcloud.com/component/trtccalling/doc/TUICallEngine/web/en/TUICallEngine.html)

---

## Part Two: TUICallKit UI Component API Documentation

The TUICallKit API is the **audio and video call component that includes a UI interface**. With the TUICallKit API, you can swiftly develop audio and video call scenarios reminiscent of WeChat through simple interfaces. For further comprehensive steps to access this, please refer to: [Integrating TUICallKit](https://trtc.io/document/50993).

## API Overview
| API | Description |
| --- | --- |
| [<TUICallKit/>](https://www.tencentcloud.com/document/product/647/51015#TUICallKit) | The core UI call component. |
| [init](https://www.tencentcloud.com/document/product/647/51015#init) | Initialize TUICallKit. |
| [calls](https://www.tencentcloud.com/document/product/647/51015#calls) | Initiate a one-to-one or multi-person call. |
| [join](https://www.tencentcloud.com/document/product/647/51015#join) | Proactively join a call. |
| [setCallingBell](https://www.tencentcloud.com/document/product/647/51015#setCallingBell) | Customize user's ringtone. |
| [setSelfInfo](https://www.tencentcloud.com/document/product/647/51015#setSelfInfo) | Set your own nickname and avatar. |
| [enableMuteMode](https://www.tencentcloud.com/document/product/647/51015#enableMuteMode) | Turn on/off ringtone. |
| [enableFloatWindow](https://www.tencentcloud.com/document/product/647/51015#enableFloatWindow) | Turn on/off the floating window function. |
| [enableVirtualBackground](https://www.tencentcloud.com/document/product/647/51015#enableVirtualBackground) | Turn on/off the blurred background function button. |
| [setLanguage](https://www.tencentcloud.com/document/product/647/51015#setLanguage) | Set the call language for the TUICallKit component. |
| [hideFeatureButton](https://www.tencentcloud.com/document/product/647/51015#hideFeatureButton) | Hidden Button. |
| [setLocalViewBackgroundImage](https://www.tencentcloud.com/document/product/647/51015#setLocalViewBackgroundImage) | Set the background image for the local user's call interface. |
| [setRemoteViewBackgroundImage](https://www.tencentcloud.com/document/product/647/51015#setRemoteViewBackgroundImage) | Set the background image for the remote user's call interface. |
| [setLayoutMode](https://www.tencentcloud.com/document/product/647/51015#setLayoutMode) | Set the call interface layout mode. |
| [setCameraDefaultState](https://www.tencentcloud.com/document/product/647/51015#setCameraDefaultState) | et whether the camera is opened by default. |
| [destroyed](https://www.tencentcloud.com/document/product/647/51015#destroyed) | Destroy TUICallKit. |
| [getTUICallEngineInstance](https://www.tencentcloud.com/document/product/647/51015#getTUICallEngineInstance) | Get TUICallEngine instance. |

## `<TUICallKit/>` attributes

### Attribute Overview
| Attribute | Description | Type | Required | Default Value | Vue | React |
| --- | --- | --- | --- | --- | --- | --- |
| allowedMinimized | Is the floating window permitted? | boolean | No | false | √ | √ |
| allowedFullScreen | Whether to permit full screen mode for the call interface | boolean | No | true | √ | √ |
| [videoDisplayMode](https://www.tencentcloud.com/document/product/647/51015#videoDisplayMode) | Display mode for the call interface | VideoDisplayMode | No | VideoDisplayMode.COVER | √ | √ |
| [videoResolution](https://www.tencentcloud.com/document/product/647/51015#videoResolution) | Call Resolution | VideoResolution | No | VideoResolution.RESOLUTION_480P | √ | √ |
| beforeCalling | This function will be executed prior to making a call and before receiving an invitation to talk | function(type, error) | No | - | √ | √ |
| afterCalling | This function will be executed after the termination of the call | function() | No | - | √ | √ |
| onMinimized | This function will be executed when the component switches to a minimized state. The explanation for the STATUS value is | function(oldStatus, newStatus) | No | - | √ | √ |
| kickedOut | The events thrown by the component occur when the current logged-in user is ejected. The call will also automatically terminate | function() | No | - | √ | × |
| statusChanged | Event thrown by the component; this event is triggered when the call status changes. For detailed types of call status, refer to STATUS value description | function({oldStatus, newStatus}) | No | - | √ | × |

### Sample code

【React】
``` bash
import { TUICallKit, VideoDisplayMode, VideoResolution } from "@trtc/calls-uikit-react";

<TUICallKit
    videoDisplayMode={VideoDisplayMode.CONTAIN}
    videoResolution={VideoResolution.RESOLUTION_1080P}
    beforeCalling={handleBeforeCalling} 
    afterCalling={handleAfterCalling} 
/>

function handleBeforeCalling(type: string, error: any) {
  console.log("[TUICallkit Demo] handleBeforeCalling:", type, error);
}
function handleAfterCalling() {
  console.log("[TUICallkit Demo] handleAfterCalling");
}
```

【Vue3】
``` bash
<template>
   <TUICallKit
    :allowedMinimized="true"
    :allowedFullScreen="true"
    :videoDisplayMode="VideoDisplayMode.CONTAIN"
    :videoResolution="VideoResolution.RESOLUTION_1080P"
    :beforeCalling="beforeCalling"
    :afterCalling="afterCalling"
    :onMinimized="onMinimized"
    :kickedOut="handleKickedOut"
    :statusChanged="handleStatusChanged"
  />
</template>
<script lang="ts" setup>
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue";

function beforeCalling(type: string, error: any) {
  console.log("[TUICallkit Demo] beforeCalling:", type, error);
}
function afterCalling() {
  console.log("[TUICallkit Demo] afterCalling");
}
function onMinimized(oldStatus: string, newStatus: string) {
  console.log("[TUICallkit Demo] onMinimized: " + oldStatus + " -> " + newStatus);
}
function kickedOut() {
  console.log("[TUICallkit Demo] kickedOut");
}
function statusChanged(args: { oldStatus: string; newStatus: string; }) {
  const { oldStatus, newStatus } = args;
  if (newStatus === STATUS.CALLING_C2C_VIDEO) {
    console.log(`[TUICallkit Demo] statusChanged: ${oldStatus} -> ${newStatus}`);
  }
}
</script>
```

## Detailed information on TUICallKitAPI API

【React】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react"; 
```

【Vue3】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue"; 
```

### init

Initialize TUICallKit.
``` javascript
try {
  await TUICallKitAPI.init({ SDKAppID, userID, userSig });
  // If you already have a tim instance in your project, you need to pass it in here
  // await TUICallKitAPI.init({ tim, SDKAppID, userID, userSig}); 
  console.log("[TUICallKit] Initialization succeeds.");
} catch (error: any) {
  console.error(`[TUICallKit] Initialization failed. Reason: ${error}`);
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| SDKAppID | Number | Yes | The SDKAppID of your app, you can find your SDKAppID in the Live Audio and Video console. For details, see Activating Services |
| userID | String | Yes | The current user's ID is of string type, only allowing for the inclusion of English letters (a-z and A-Z), digits (0-9), hyphens (-) and underscores (_) |
| userSig | String | Yes | Use SDKSecretKey to encrypt SDKAppID, UserID and other information to obtain userSig. It is an authentication ticket used by Tencent Cloud to identify whether the current user can use TRTC services. For how to obtain it, please refer to How to Calculate UserSig |
| tim | TencentCloudChat | No | tim is an instance of [TencentCloudChat](https://www.npmjs.com/package/@tencentcloud/chat) SDK. |

### calls

Initiate a one-to-one or multi-person call.
``` javascript
try {
  await TUICallKitAPI.calls({ 
    userIDList: ['jack', 'tom'], 
    type: CallMediaType.VIDEO 
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the groupCall API. Reason:${error}`);
}
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| userIDList | Array<String> | Yes | List of called users |
| type | [CallMediaType](https://www.tencentcloud.com/document/product/647/51015#Type) | Yes | The type of media for the call, you can refer to CallMediaType for the explanation of parameter values |
| chatGroupID | String | Yes | chat group Id. |
| roomID | Number | No | Numerical Room ID, range [1, 2147483647] |
| strRoomID | String | No | String room ID. **v3.3.1+ supported** **range：** Limited to 64 bytes in length. The supported character set range is as follows (a total of 89 characters): - Lowercase and uppercase English letters.（a-zA-Z）； - Number（0-9）； - `Spaces`、`!`、`#`、`$`、`%`、`&`、`(`、`)`、`+`、`-`、`:`、`;`、`<`、`=`、`.`、`>`、`?`、`@`、`[`、`]`、`^`、`_`、`{`、`}`、`\\|`、`~`、`,`。 1. roomID and strRoomID are mutually exclusive, if you use strRoomID, then roomID should be 0. If you use both, the SDK will prioritize the roomID. 2. 2. don't mix roomID and strRoomID, because they are not interchangeable, for example, the number 123 and the string "123" are two completely different rooms. |
| timeout | Number | No | Call timeout, default: 30s, unit: seconds. timeout = 0, set to no timeout |
| userData | String | No | Customize the extended fields when initiating a call. The called user has this parameter in the ON_CALL_RECEIVED event. |
| [offlinePushInfo](https://www.tencentcloud.com/document/product/647/51015#offlinePushInfo) | Object | No | Customize offline message push parameters |

### join

Proactively join a call.
``` javascript
try {
  await TUICallKitAPI.join({ 
    callId: 'xx'
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the join API. Reason:${error}`);
}
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| callId | String | Yes | Unique ID for this call |

### setLanguage

Set language, currently supports: Chinese, English, Japanese.
``` javascript
TUICallKitAPI.setLanguage("zh-cn"); // "en" | "zh-cn" | "ja_JP"
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| lang | String | Yes | Language type `en`,`zh-cn` and `ja_JP`. |

### setSelfInfo

Set your own nickname and avatar.

> **Note: **
> 

> **If you use this interface to modify user information during a call, the UI will not be updated immediately, and you will need to wait until the next call to see the changes.**
> 

``` javascript
try {
  await TUICallKitAPI.setSelfInfo({ nickName: "xxx", avatar: "" });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the setSelfInfo API. Reason: ${error}`;
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| nickName | String | Yes | own nickname |
| avatar | String | Yes | own avatar address |

### setCallingBell
- Customize the user's incoming call ringtone.

- The input is restricted to the local MP3 format file address. It is imperative to ensure that the application has access to this file directory.

- Use the import method to import the ringtone file.

- If you need to restore the default ringtone, just pass empty filePath.

   ``` javascript
   import filePath from '../assets/phone_ringing.mp3';
   try {
     await TUICallKitAPI.setCallingBell(filePath);
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the setCallingBell API. Reason: ${error}`);
   }
   ```

   The parameters are described below:

| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| filePath | String | Yes | Ringtone file path |

### enableFloatWindow

Turn on/off the floating window function. The default is false. The floating window button in the upper left corner of the call interface is hidden. It will be displayed after setting it to true.
``` javascript
try {
  const enable = true;
  await TUICallKitAPI.enableFloatWindow(enable);
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableFloatWindow API. Reason: ${error}`);
}
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| enable | Boolean | Yes | Turn on/off the floating window function. default false. |

### enableMuteMode

Turn on/off the ringtone for incoming calls. When turned on, the incoming call ringtone will not be played when a call request is received.
``` javascript
try {
  const enable = true;
  await TUICallKitAPI.enableMuteMode(enable);
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableMuteMode API. Reason: ${error}`);
}
```

### enableVirtualBackground

Turn on/off the blurred background function. If you want to set the picture background to be blurry see Web. By calling the interface, you can display the blurred background function button on the UI, and click the button to directly enable the blurred background function.
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react";
const enable = true;
TUICallKitAPI.enableVirtualBackground(enable);
```

The parameters are described below:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| enable | boolean | Yes | - enable = true, show blur background button - enable = false, don't show blur background button |

### destroyed
- Terminate the TUICallKit instance.

- This method won't automatically log out of `tim`, manual logging out is required: `tim.logout();`.

   ``` javascript
   try {
     await TUICallKitAPI.destroyed();
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the destroyed API. Reason: ${error}`);
   }
   ```

### hideFeatureButton

Hidden feature buttons, currently only support Camera, Microphone, and Switch Camera Button.
``` typescript
TUICallKitAPI.hideFeatureButton(buttonName: FeatureButton);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| buttonName | [FeatureButton](https://www.tencentcloud.com/document/product/647/51015#FeatureButton) | Yes | Button Name |

### setLocalViewBackgroundImage

Set the background image for the local user's call interface.
``` typescript
TUICallKitAPI.setLocalViewBackgroundImage(url: string);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| url | string | Yes | Image Address (supports Local Path and Network Address) |

### setRemoteViewBackgroundImage

Set the background image for the remote user's call interface.
``` typescript
TUICallKitAPI.setRemoteViewBackgroundImage(userId: string, url: string);
```

The parameters are described below:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| userId | string | Yes | Remote User userId, setting to '*' means it applies to all Remote Users |
| url | string | Yes | Image Address (supports Local Path and Network Address) |

### setLayoutMode

Set the call interface layout mode.

【Vue】
``` javascript
import { TUICallKitAPI, LayoutMode } from "@trtc/calls-uikit-vue";
TUICallKitAPI.setLayoutMode(LayoutMode.LocalInLargeView);
```

【React】
``` javascript
import { TUICallKitAPI, LayoutMode } from "@trtc/calls-uikit-react";
TUICallKitAPI.setLayoutMode(LayoutMode.LocalInLargeView);
```

Parameter list:
| **Parameter** | **Type** | **Required** | **Meaning** |
| --- | --- | --- | --- |
| layoutMode | [LayoutMode](https://www.tencentcloud.com/document/product/647/51015#LayoutMode) | Yes | User flow layout mode |

### setCameraDefaultState

Set whether the camera is on by default.
``` javascript
TUICallKitAPI.setCameraDefaultState(true);
```

Parameter list:
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| isOpen | boolean | Yes | Whether to enable the camera |

### getTUICallEngineInstance

Get TUICallEngine instance.
``` javascript
TUICallKitAPI.getTUICallEngineInstance();
```

## TUICallKit Type Definition

### videoDisplayMode

There are three values for the `videoDisplayMode` display mode:
- `VideoDisplayMode.CONTAIN`

- `VideoDisplayMode.COVER`

- `VideoDisplayMode.FILL`, the default value is `VideoDisplayMode.COVER`.

| Attribute | Value | Description |
| --- | --- | --- |
| videoDisplayMode | VideoDisplayMode.CONTAIN | - Ensuring the full display of video content is our top priority. - The dimensions of the video are scaled proportionally until one side aligns with the frame of the viewing window. - In case of discrepancy in sizes between the video and the display window, the video is scaled - on the premise of maintaining the aspect ratio - to fill the window, resulting in a black border around the scaled video. |
| VideoDisplayMode.COVER | - Priority is given to ensure that the viewing window is filled. - The video size is scaled proportionally until the entire window is filled. - If the video's dimensions are different from those of the display window, the video stream will be cropped or stretched to match the window's ratio. |  |
| VideoDisplayMode.FILL | - Ensuring that the entire video content is displayed while filling the window does not guarantee preservation of the original video's proportion. - The dimensions of the video will be stretched to match those of the window. |  |

### videoResolution

The resolution `videoResolution` has three possible values:
- `VideoResolution.RESOLUTION_480P`

- `VideoResolution.RESOLUTION_720P`

- `VideoResolution.RESOLUTION_1080P`, the default value is `VideoResolution.RESOLUTION_480P`.

   **Resolution Explanation:**

| Video Profile | Resolution (W x H) | Frame Rate (fps) | Bitrate (Kbps) |
| --- | --- | --- | --- |
| 480p | 640 × 480 | 15 | 900 |
| 720p | 1280 × 720 | 15 | 1500 |
| 1080p | 1920 × 1080 | 15 | 2000 |

   **Frequently Asked Questions:**

- iOS 13&14 does not support encoding videos higher than 720P. It is suggested to limit the highest collection to 720P on these two system versions. Refer to [iOS Safari known issue case 12](https://web.sdk.qcloud.com/trtc/webrtc/doc/en/tutorial-02-info-webrtc-issues.html#h2-4).

- Firefox does not permit the customization of video frame rates (default is set to 30fps).

- Due to the influence of system performance usage, camera collection capabilities, browser restrictions, and other factors, the actual values of video resolution, frame rate, and bit rate may not necessarily match the set values exactly. In such scenarios, the browser will automatically adjust the Profile to get as close to the set values as feasible.

### STATUS
| STATUS attribute value | Description |
| --- | --- |
| STATUS.IDLE | Idle status |
| STATUS.BE_INVITED | Received an Audio/Video Call Invite |
| STATUS.DIALING_C2C | Initiating a one-to-one call |
| STATUS.DIALING_GROUP | Initiating a group call |
| STATUS.CALLING_C2C_AUDIO | Engaged in a 1v1 Audio Call |
| STATUS.CALLING_C2C_VIDEO | In the midst of a one-to-one video call |
| STATUS.CALLING_GROUP_AUDIO | Engaged in Group Audio Communication |
| STATUS.CALLING_GROUP_VIDEO | Engaged in group video call |

### **CallMediaType**
| CallMediaType Type | Description |
| --- | --- |
| CallMediaType.AUDIO_CALL | Audio Call |
| CallMediaType.VIDEO | Video Call |

### **offlinePushInfo**
| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| offlinePushInfo.title | String | No | Offline Push Title (Optional) |
| offlinePushInfo.description | String | No | Offline Push Content (Optional) |
| offlinePushInfo.androidOPPOChannelID | String | No | Setting the channel ID for OPPO phones with 8.0 system and above for offline pushes (Optional) |
| offlinePushInfo.extension | String | No | Offline push through content. Can be used to set Android Notification mode and VoIP mode.** Default**: Notification mode, it will be a notification from the system; VoIP mode is required to pass the field. |
| offlinePushInfo.ignoreIOSBadge | Boolean | No | Ignore badge count for offline push (only for iOS), if set to true, the message will not increase the unread count of the app icon on the iOS receiver's side. |
| offlinePushInfo.iOSSound | String | No | Offline push sound setting (only for iOS). |
| offlinePushInfo.androidSound | String | No | Offline push sound setting. |
| offlinePushInfo.androidVIVOClassification | Number | No | Classification of VIVO push messages (deprecated interface, VIVO push service will optimize message classification rules on April 3, 2023. It is recommended to use setAndroidVIVOCategory to set the message category). 0: Operational messages, 1: System messages. The default value is 1. |
| offlinePushInfo.androidXiaoMiChannelID | String | No | Set the channel ID for Xiaomi phones with Android 8.0 and above systems. |
| offlinePushInfo.androidFCMChannelID | String | No | Set the channel ID for google phones with Android 8.0 and above systems. |
| offlinePushInfo.androidHuaWeiCategory | String | No | Classification of Huawei push messages. |
| offlinePushInfo.isDisablePush | Boolean | No | Whether to turn off push notifications (default is on). |
| offlinePushInfo.iOSPushType | Number | No | iOS offline push type，default is 0。0-APNs；1-VoIP. |

#### Android Notification Mode
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, VoIP; 1, notification
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

#### Android VoIP Mode
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, data; 1, notification
    fcmNotificationType: 1, // 0, TIMPush implementation； 1, business implementation after transparent transmission 
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

### FeatureButton
| FeatureButton Type | Description |
| --- | --- |
| FeatureButton.Camera | Camera Button |
| FeatureButton.Microphone | Microphone Button |
| FeatureButton.SwitchCamera | Switches between the front and rear cameras. |
| FeatureButton.InviteUser | Invite users button |

### LayoutMode
| LayoutMode type | Description |
| --- | --- |
| LayoutMode.LocalInLargeView | Local user in large window display |
| LayoutMode.RemoteInLargeView | Remote user in large window display |

---

## Platform: Android SDK

## TUICallObserver APIs

`TUICallObserver` is the callback class of `TUICallEngine`. You can use it to listen for events.

## Overview
| API | Description |
| --- | --- |
| onError | A call occurred during the call. |
| onCallReceived | A call invitation was received. |
| onCallBegin | The call was connected. |
| onCallEnd | The call ended. |
| onCallNotConnected | The call not connected. |
| onUserReject | A user declined the call. |
| onUserNoResponse | A user didn't respond. |
| onUserLineBusy | A user was busy. |
| onUserInviting | A user is invited to join a call. |
| onUserJoin | A user joined the call. |
| onUserLeave | A user left the call. |
| onUserVideoAvailable | Whether a user had a video stream. |
| onUserAudioAvailable | Whether a user had an audio stream. |
| onUserVoiceVolumeChanged | The volume levels of all users. |
| onUserNetworkQualityChanged | The network quality of all users. |
| onKickedOffline | The current user was kicked offline. |
| onUserSigExpired | The user sig is expired. |

## Details

### onError

An error occurred.

> **Note:**
> 

> This callback indicates that the SDK encountered an unrecoverable error. Such errors must be listened for, and UI reminders should be sent to users if necessary.
> 

``` java
void onError(int code, String message);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| code | int | The error code. |
| message | String | The error message. |

### onCallReceived

A call invitation was received. This callback is received by an invitee. You can listen for this event to determine whether to display the incoming call view.
``` java
void onCallReceived(String callId, String callerId, List<String> calleeIdList, 
                    TUICallDefine.MediaType mediaType, TUICallDefine.CallObserverExtraInfo info);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| callerId | String | Caller ID |
| calleeIdList | List<String> | Callee ID List |
| mediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| info | TUICallDefine.CallObserverExtraInfo | Extended information |

### onCallBegin

The call was connected. This callback is received by both the inviter and invitees. You can listen for this event to determine whether to start on-cloud recording, content moderation, or other tasks.
``` java
void onCallBegin(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallObserverExtraInfo info);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| info | TUICallDefine.CallObserverExtraInfo | Extended information |

### onCallEnd

The call ended. This callback is received by both the inviter and invitees. You can listen for this event to determine when to display call information such as call duration and call type, or stop on-cloud recording.
``` java
void onCallEnd(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallEndReason reason, 
               String userId, long totalTime, TUICallDefine.CallObserverExtraInfo info);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| reason | TUICallDefine.CallEndReason | Call end reason |
| userId | String | ​​Ended by user ID |
| totalTime | long | The call duration. |
| info | TUICallDefine.CallObserverExtraInfo | Extended information |

> **Note:**
> 

> Client-side callbacks are often lost when errors occur, for example, when the process is closed. If you need to measure the duration of a call for billing or other purposes, we recommend you use the RESTful API.
> 

### onCallNotConnected

The call was canceled by the inviter or timed out. This callback is received by an invitee. You can listen for this event to determine whether to show a missed call message.

This indicates that the call was canceled by the caller, timed out by the callee, rejected by the callee, or the callee was busy. There are multiple scenarios involved. You can listen to this event to achieve UI logic such as missed calls and resetting UI status. 
- Call cancellation by the caller: The caller receives the callback (userId is himself); the callee receives the callback (userId is the ID of the caller) 

- Callee timeout: the caller will simultaneously receive the onUserNoResponse and onCallNotConnected callbacks (userId is his own ID); the callee receives the onCallNotConnected callback (userId is his own ID) 

- Callee rejection: The caller will simultaneously receive the onUserReject and onCallNotConnected callbacks (userId is his own ID); the callee receives the onCallNotConnected callback (userId is his own ID) 

- Callee busy: The caller will simultaneously receive the onUserLineBusy and onCallNotConnected callbacks (userId is his own ID); 

- Abnormal interruption: The callee failed to receive the call ，he receives this callback (userId is his own ID).

   ``` java
   void onCallNotConnected(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallEndReason reason, 
                           String userId, TUICallDefine.CallObserverExtraInfo info);
   ```

   The parameters are described below:

| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| reason | TUICallDefine.CallEndReason | Call end reason |
| userId | String | ​​Not connected by user ID |
| info | TUICallDefine.CallObserverExtraInfo | Extended information |

### onUserReject

The call was rejected. In a one-to-one call, only the inviter will receive this callback. In a group call, all invitees will receive this callback.
``` java
void onUserReject(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invitee who rejected the call. |

### onUserNoResponse

A user did not respond.
``` java
void onUserNoResponse(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invitee who did not answer. |

### onUserInviting

A user is invited to join a call.
``` java
void onUserInviting(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invite. |

### onUserLineBusy

A user is busy.
``` java
void onUserLineBusy(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invitee who is busy. |

### onUserJoin

A user joined the call.
``` java
void onUserJoin(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The ID of the user who joined the call. |

### onUserLeave

A user left the call.
``` java
void onUserLeave(String userId);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The ID of the user who left the call. |

### onUserVideoAvailable

Whether a user is sending video.
``` java
void onUserVideoAvailable(String userId, boolean isVideoAvailable);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID. |
| isVideoAvailable | boolean | Whether the user has video. |

### onUserAudioAvailable

Whether a user is sending audio.
``` java
void onUserAudioAvailable(String userId, boolean isAudioAvailable);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID. |
| isAudioAvailable | boolean | Whether the user has audio. |

### onUserVoiceVolumeChanged

The volumes of all users.
``` java
void onUserVoiceVolumeChanged(Map<String, Integer> volumeMap);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| volumeMap | Map | The volume table, which includes the volume of each user (`userId`). Value range: 0-100. |

### onUserNetworkQualityChanged

The network quality of all users.
``` java
void onUserNetworkQualityChanged(List<TUICallDefine.NetworkQualityInfo> networkQualityList);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| networkQualityList | List | The current network conditions for all users (`userId`). |

### onKickedOffline

The current user was kicked offline: At this time, you can prompt the user with a UI message and then invoke `init` again.
``` java
void onKickedOffline();
```

### onUserSigExpired

The user sig is expired: At this time, you need to generate a new `userSig`, and then invoke `init` again.
``` java
void onUserSigExpired();
```

## Deprecated Interface

### onCallCancelled

This indicates that the call was canceled by the caller, timed out by the callee, rejected by the callee, or the callee was busy. There are multiple scenarios involved. You can listen to this event to achieve UI logic such as missed calls and resetting UI status. 
- Call cancellation by the caller: The caller receives the callback (userId is himself); the callee receives the callback (userId is the ID of the caller) 

- Callee timeout: the caller will simultaneously receive the onUserNoResponse and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) 

- Callee rejection: The caller will simultaneously receive the onUserReject and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) 

- Callee busy: The caller will simultaneously receive the onUserLineBusy and onCallCancelled callbacks (userId is his own ID); 

- Abnormal interruption: The callee failed to receive the call ，he receives this callback (userId is his own ID).

   ``` java
   void onCallCancelled(String userId);
   ```

   The parameters are described below:

| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the inviter. |

### onCallMediaTypeChanged

The call type changed.
``` java
void onCallMediaTypeChanged(TUICallDefine.MediaType oldCallMediaType,TUICallDefine.MediaType newCallMediaType);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| oldCallMediaType | TUICallDefine.MediaType | The call type before the change. |
| newCallMediaType | TUICallDefine.MediaType | The call type after the change. |

---

## Platform: Android SDK

## TUICallKit APIs

`TUICallKit` is an audio/video call component that **includes UI elements**. You can use its APIs to quickly implement an audio/video call application similar to WeChat. For directions on integration, see [Integrating TUICallKit](https://www.tencentcloud.com/document/product/647/50991).

## API Overview
| API | Description |
| --- | --- |
| createInstance | Creates a `TUICallKit` instance (singleton mode). |
| setSelfInfo | Sets the alias and profile photo. |
| calls | Initiate a one-to-one or multi-person call. |
| join | Proactively join a call. |
| setCallingBell | Sets the ringtone. |
| enableMuteMode | Sets whether to turn on the mute mode. |
| enableFloatWindow | Sets whether to enable floating windows. |
| enableIncomingBanner | Sets whether to display incoming banner. |

## Details

### createInstance

This API is used to create a `TUICallKit` singleton.

【Kotlin】
``` java
fun createInstance(context: Context): TUICallKit
```

【Java】
``` java
TUICallKit createInstance(Context context)
```

### setSelfInfo

This API is used to set the alias and profile photo. The alias cannot exceed 500 bytes, and the profile photo is specified by a URL.

【Kotlin】
``` java
fun setSelfInfo(nickname: String?, avatar: String?, callback: TUICommonDefine.Callback?)
```

【Java】
``` java
void setSelfInfo(String nickname, String avatar, TUICommonDefine.Callback callback)
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userIdList | List<String> | The target user IDs. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| params | TUICallDefine.CallParams | An additional parameter. such as roomID, call timeout, offline push info,etc |

### calls

Initiate a one-to-one or multi-person call.

【kotlin】
``` java
 fun calls(
     userIdList: List<String?>?, mediaType: TUICallDefine.MediaType?, 
     params: TUICallDefine.CallParams?, callback: TUICommonDefine.Callback?
)
```

【Java】
``` java
 void calls(List<String> userIdList, TUICallDefine.MediaType mediaType,
            TUICallDefine.CallParams params, TUICommonDefine.Callback callback)
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userIdList | List<String> | The target user IDs. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| params | TUICallDefine.CallParams | An additional parameter. such as roomID, call timeout, offline push info,etc |

### join

Proactively join a call.

【Kotlin】
``` java
 fun join(callId: String?, callback: TUICommonDefine.Callback?) 
```

【Java】
``` java
void join(String callId, TUICommonDefine.Callback callback)
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID for this call |

### setCallingBell

This API is used to set the ringtone. `filePath` must be an accessible local file URL.

The ringtone set is associated with the device and does not change with user.

To reset the ringtone, pass in an empty string for `filePath`.

【Kotlin】
``` java
fun setCallingBell(filePath: String?)
```

【Java】
``` java
void setCallingBell(String filePath);
```

### enableMuteMode

This API is used to set whether to play the music when user received a call. 

【Kotlin】
``` java
fun enableMuteMode(enable: Boolean)
```

【Java】
``` java
void enableMuteMode(boolean enable);
```

### enableFloatWindow

This API is used to set whether to enable floating windows.

The default value is `false`, and the floating window button in the top left corner of the call view is hidden. If it is set to `true`, the button will become visible.

【Kotlin】
``` java
fun enableFloatWindow(enable: Boolean)
```

【Java】
``` java
void enableFloatWindow(boolean enable);
```

### enableIncomingBanner

The API is used to set whether show incoming banner when user received a new call invitation.

The default value is `false`, The callee will pop up a full-screen call view by default when receiving the invitation. If it is set to `true`, the callee will display a banner first.
``` java
fun enableIncomingBanner(enable: Boolean)
```

## Deprecated Interface

### call

This API is used to make a (one-to-one) call. **Note:  it is recommended to use the calls API.**

【Kotlin】
``` java
fun call(userId: String, callMediaType: TUICallDefine.MediaType)
```

【Java】
``` java
 void call(String userId, TUICallDefine.MediaType callMediaType)
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The target user ID. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |

### call

This API is used to make a (one-to-one) call, Support for custom room ID, call timeout, offline push content, etc. **Note:  it is recommended to use the calls API.**

【Kotlin】
``` java
fun call(
    userId: String, callMediaType: TUICallDefine.MediaType,
    params: CallParams?, callback: TUICommonDefine.Callback?
)
```

【Java】
``` java
 void call(String userId, TUICallDefine.MediaType callMediaType, 
           TUICallDefine.CallParams params, TUICommonDefine.Callback callback)
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The target user ID. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| params | TUICallDefine.CallParams | Call extension parameters, such as roomID, call timeout, offline push info,etc |

### groupCall

This API is used to make a group call. **Note:  it is recommended to use the calls API.**

> **Note:**
> 

> Before making a group call, you need to create an Chat group first.
> 

> For details about how to create a group, see [Chat Group Management](https://www.tencentcloud.com/zh/document/product/1047/48466#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84), or you can use [Chat UIKit](https://www.tencentcloud.com/zh/document/product/1047/60520) to integrate chat, call and other scenarios.
> 

【Kotlin】
``` java
fun groupCall(groupId: String, userIdList: List<String?>?, callMediaType: TUICallDefine.MediaType)
```

【Java】
``` java
void groupCall(String groupId, List<String> userIdList, TUICallDefine.MediaType callMediaType);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| groupId | String | The group ID. |
| userIdList | List | The target user IDs. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |

### groupCall

This API is used to make a group call, Support for custom room ID, call timeout, offline push content, etc. **Note:  it is recommended to use the calls API.**

> **Note:**
> 

> Before making a group call, you need to create an Chat group first.
> 

> For details about how to create a group, see [Chat Group Management](https://www.tencentcloud.com/zh/document/product/1047/48466#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84), or you can use [Chat UIKit](https://www.tencentcloud.com/zh/document/product/1047/60520) to integrate chat, call and other scenarios.
> 

【Kotlin】
``` java
fun groupCall(
    groupId: String, userIdList: List<String?>?,
    callMediaType: TUICallDefine.MediaType, params: CallParams?,
    callback: TUICommonDefine.Callback?
)
```

【Java】
``` java
void groupCall(String groupId, List<String> userIdList, TUICallDefine.MediaType callMediaType, 
               TUICallDefine.CallParams params, TUICommonDefine.Callback callback);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| groupId | String | The group ID. |
| userIdList | List | The target user IDs. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |
| params | TUICallDefine.CallParams | Call extension parameters, such as roomID, call timeout, offline push info,etc |

### joinInGroupCall

This API is used to join a group call. **Note:  it is recommended to use the join API.**

> **Note:**
> 

> Before joining a group call, you need to create or join an Chat group in advance, and there are already users in the group who are in the call.
> 

> For details about how to create a group, see [Chat Group Management](https://www.tencentcloud.com/zh/document/product/1047/48466#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84), or you can use [Chat UIKit](https://www.tencentcloud.com/zh/document/product/1047/60520) to integrate chat, call and other scenarios.
> 

【Kotlin】
``` java
fun joinInGroupCall(roomId: RoomId?, groupId: String?, callMediaType: TUICallDefine.MediaType?) 
```

【Java】
``` java
void joinInGroupCall(TUICommonDefine.RoomId roomId, String groupId, TUICallDefine.MediaType callMediaType);
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| roomId | TUICommonDefine.RoomId | The room ID. |
| groupId | String | The group ID. |
| callMediaType | TUICallDefine.MediaType | The call type, which can be video or audio. |

---

## Platform: iOS SDK

## TUICallObserver APIs

`TUICallObserver` is the callback class of `TUICallEngine`. You can use it to listen for events.

## Overview
| API | Description |
| --- | --- |
| [onError](https://www.tencentcloud.com/document/product/647/51013#onError) | An error occurred during the call. |
| [onCallReceived](https://www.tencentcloud.com/document/product/647/51013#onCallReceived) | A call was received. |
| [onCallBegin](https://www.tencentcloud.com/document/product/647/51013#onCallBegin) | The call was connected. |
| [onCallEnd](https://www.tencentcloud.com/document/product/647/51013#onCallEnd) | The call ended. |
| [onCallNotConnected](https://www.tencentcloud.com/document/product/647/51013#onCallNotConnected) | The call not connected. |
| [onUserReject](https://www.tencentcloud.com/document/product/647/51013#onUserReject) | A user declined the call. |
| [onUserNoResponse](https://www.tencentcloud.com/document/product/647/51013#onUserNoResponse) | A user didn't respond. |
| [onUserLineBusy](https://www.tencentcloud.com/document/product/647/51013#onUserLineBusy) | A user was busy. |
| [onUserInviting](https://www.tencentcloud.com/document/product/647/51013#onUserInviting) | A user is invited to join a call. |
| [onUserJoin](https://www.tencentcloud.com/document/product/647/51013#onUserJoin) | A user joined the call. |
| [onUserLeave](https://www.tencentcloud.com/document/product/647/51013#onUserLeave) | A user left the call. |
| [onUserVideoAvailable](https://www.tencentcloud.com/document/product/647/51013#onUserVideoAvailable) | Whether a user had a video stream. |
| [onUserAudioAvailable](https://www.tencentcloud.com/document/product/647/51013#onUserAudioAvailable) | Whether a user had an audio stream. |
| [onUserVoiceVolumeChanged](https://www.tencentcloud.com/document/product/647/51013#onUserVoiceVolumeChanged) | The volume levels of all users. |
| [onUserNetworkQualityChanged](https://www.tencentcloud.com/document/product/647/51013#onUserNetworkQualityChanged) | The network quality of all users. |
| [onKickedOffline](https://www.tencentcloud.com/document/product/647/51013#onKickedOffline) | The current user was kicked offline. |
| [onUserSigExpired](https://www.tencentcloud.com/document/product/647/51013#onUserSigExpired) | The user sig is expired. |

## Details

### onError

An error occurred.

> **Note:**
> 

> This callback indicates that the SDK encountered an unrecoverable error. Such errors must be listened for, and UI reminders should be sent to users if necessary.
> 

``` objc
- (void)onError:(int)code message:(NSString * _Nullable)message;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| code | int | The error code. |
| message | NSString | The error message. |

### onCallReceived

A call invitation was received. This callback is received by an invitee. You can listen for this event to determine whether to display the incoming call view.
``` objc
- (void)onCallReceived:(NSString *)callId callerId:(NSString *)callerId calleeIdList:(NSArray<NSString *> *)calleeIdList mediaType:(TUICallMediaType)mediaType info:(TUICallObserverExtraInfo *)info;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | NSString | Unique ID of this call |
| callerId | NSString | Caller ID |
| calleeIdList | NSArray | Callee ID List |
| mediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type, which can be video or audio. |
| info | [TUICallObserverExtraInfo](https://www.tencentcloud.com/document/product/647/54902#ObserverExtraInfo) | Extended information |

### onCallBegin

The call was connected. This callback is received by both the inviter and invitees. You can listen for this event to determine whether to start on-cloud recording, content moderation, or other tasks.
``` swift
- (void)onCallBegin:(NSString *)callId mediaType:(TUICallMediaType)mediaType info:(TUICallObserverExtraInfo *)info;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | NSString | Unique ID of this call |
| mediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type, which can be video or audio. |
| info | [TUICallObserverExtraInfo](https://www.tencentcloud.com/document/product/647/54902#ObserverExtraInfo) | Extended information |

### onCallEnd

The call ended. This callback is received by both the inviter and invitees. You can listen for this event to determine when to display call information such as call duration and call type, or stop on-cloud recording.
``` swift
- (void)onCallEnd:(NSString *)callId mediaType:(TUICallMediaType)mediaType reason:(TUICallEndReason)reason userId:(NSString *)userId totalTime:(float)totalTime info:(TUICallObserverExtraInfo *)info;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| callId | NSString | Unique ID of this call |
| mediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type, which can be video or audio. |
| reason | [TUICallEndReason](https://www.tencentcloud.com/document/product/647/54902#EndReason) | Call end reason |
| userId | NSString | ​​Ended by user ID |
| totalTime | float | The call duration. |
| info | [TUICallObserverExtraInfo](https://www.tencentcloud.com/document/product/647/54902#ObserverExtraInfo) | Extended information |

> **Note:**
> 

> Client-side callbacks are often lost when errors occur, for example, when the process is closed. If you need to measure the duration of a call for billing or other purposes, we recommend you use the RESTful API.
> 

### onCallNotConnected

This indicates that the call was canceled by the caller, timed out by the callee, rejected by the callee, or the callee was busy. There are multiple scenarios involved. You can listen to this event to achieve UI logic such as missed calls and resetting UI status. 
- Call cancellation by the caller: The caller receives the callback (userId is himself); the callee receives the callback (userId is the ID of the caller) 

- Callee timeout: the caller will simultaneously receive the [onUserNoResponse](https://www.tencentcloud.com/document/product/647/54902#onUserNoResponse) and onCallNotConnected callbacks (userId is his own ID); the callee receives the onCallNotConnected callback (userId is his own ID) 

- Callee rejection: The caller will simultaneously receive the [onUserReject](https://www.tencentcloud.com/document/product/647/54902#onUserReject) and onCallNotConnected callbacks (userId is his own ID); the callee receives the onCallNotConnected callback (userId is his own ID) 

- Callee busy: The caller will simultaneously receive the [onUserLineBusy](https://www.tencentcloud.com/document/product/647/54902#onUserLineBusy) and onCallNotConnected callbacks (userId is his own ID); 

- Abnormal interruption: The callee failed to receive the call ，he receives this callback (userId is his own ID).

   ``` objc
   - (void)onCallNotConnected:(NSString *)callId mediaType:(TUICallMediaType)mediaType reason:(TUICallEndReason)reaso userId:(NSString *)userId info:(TUICallObserverExtraInfo *)info
   ```

   The parameters are described below:

| Parameter | Type | Description |
| --- | --- | --- |
| callId | NSString | Unique ID of this call |
| mediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type, which can be video or audio. |
| reason | [TUICallEndReason](https://www.tencentcloud.com/document/product/647/54902#TUICallEndReason) | Call not connected reason |
| userId | NSString | ​​Not connected by user ID |
| info | [TUICallObserverExtraInfo](https://www.tencentcloud.com/document/product/647/54902#CallObserverExtraInfo) | Extended information |

### onUserReject

The call was rejected. In a one-to-one call, only the inviter will receive this callback. In a group call, all invitees will receive this callback.
``` swift
- (void)onUserReject:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID of the invitee who rejected the call. |

### onUserNoResponse

A user did not respond.
``` swift
- (void)onUserNoResponse:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID of the invitee who did not answer. |

### onUserLineBusy

A user is busy.
``` swift
- (void)onUserLineBusy:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID of the invitee who is busy. |

### onUserInviting

A user is invited to join a call.
``` objectivec
- (void)onUserInviting:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID of the invite. |

### onUserJoin

A user joined the call.
``` swift
- (void)onUserJoin:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The ID of the user who joined the call. |

### onUserLeave

A user left the call.
``` swift
- (void)onUserLeave:(NSString *)userId;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The ID of the user who left the call. |

### onUserVideoAvailable

Whether a user is sending video.
``` swift
- (void)onUserVideoAvailable:(NSString *)userId isVideoAvailable:(BOOL)isVideoAvailable;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID. |
| isVideoAvailable | BOOL | Whether the user has video. |

### onUserAudioAvailable

Whether a user is sending audio.
``` swift
- (void)onUserAudioAvailable:(NSString *)userId isAudioAvailable:(BOOL)isAudioAvailable;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | NSString | The user ID. |
| isAudioAvailable | BOOL | Whether the user has audio. |

### onUserVoiceVolumeChanged

The volumes of all users.
``` swift
- (void)onUserVoiceVolumeChanged:(NSDictionary <NSString *, NSNumber *> *)volumeMap;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| volumeMap | NSDictionary | The volume table, which includes the volume of each user (`userId`). Value range: 0-100. |

### onUserNetworkQualityChanged

The network quality of all users.
``` swift
- (void)onUserNetworkQualityChanged:(NSArray<TUINetworkQualityInfo *> *)networkQualityList;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| networkQualityList | NSArray | The current network conditions for all users (`userId`). |

### onKickedOffline

The current user was kicked offline：At this time, you can prompt the user with a UI message and then invoke `init` again.
``` objectivec
- (void)onKickedOffline;
```

### onUserSigExpired

The user sig is expired：At this time, you need to generate a new `userSig`, and then invoke `init` again.
``` objectivec
- (void)onUserSigExpired;
```

## Deprecated Interface

### onCallCancelled

The call was canceled by the inviter or timed out. This callback is received by an invitee. You can listen for this event to determine whether to show a missed call message.

This indicates that the call was canceled by the caller, timed out by the callee, rejected by the callee, or the callee was busy. There are multiple scenarios involved. You can listen to this event to achieve UI logic such as missed calls and resetting UI status. 
- Call cancellation by the caller: The caller receives the callback (userId is himself); the callee receives the callback (userId is the ID of the caller) 

- Callee timeout: the caller will simultaneously receive the [onUserNoResponse](https://www.tencentcloud.com/document/product/647/51013#onUserNoResponse) and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) 

- Callee rejection: The caller will simultaneously receive the [onUserReject](https://www.tencentcloud.com/document/product/647/51013#onUserReject) and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) 

- Callee busy: The caller will simultaneously receive the [onUserLineBusy](https://www.tencentcloud.com/document/product/647/51013#onUserLineBusy) and onCallCancelled callbacks (userId is his own ID); 

- Abnormal interruption: The callee failed to receive the call ，he receives this callback (userId is his own ID).

   ``` objc
   - (void)onCallCancelled:(NSString *)callerId;
   ```

   The parameters are described below:

| Parameter | Type | Description |
| --- | --- | --- |
| callerId | NSString | The user ID of the inviter. |

### onCallMediaTypeChanged

The call type changed.
``` swift
- (void)onCallMediaTypeChanged:(TUICallMediaType)oldCallMediaType newCallMediaType:(TUICallMediaType)newCallMediaType;
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| oldCallMediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type before the change. |
| newCallMediaType | [TUICallMediaType](https://www.tencentcloud.com/document/product/647/54902#MediaType) | The call type after the change. |

---

## Platform: iOS SDK

## TUICallKit (UI Included)

TUICallKit is an audio/video call component that **includes UI elements**. You can use its APIs to quickly implement an audio/video call application similar to WeChat.
| API | Description |
| --- | --- |
| [createInstance](https://www.tencentcloud.com/document/product/647/51011#createInstance) | Create a TUICallKit instance (singleton mode). |
| [setSelfInfo](https://www.tencentcloud.com/document/product/647/51011#setSelfInfo) | Set the user's profile picture and nickname. |
| [calls](https://www.tencentcloud.com/document/product/647/51011#calls) | Initiate a one-to-one or multi-person call |
| [join](https://www.tencentcloud.com/document/product/647/51011#join) | Proactively join a call |
| [setCallingBell](https://www.tencentcloud.com/document/product/647/51011#setCallingBell) | Set the ringtone. |
| [enableMuteMode](https://www.tencentcloud.com/document/product/647/51011#enableMuteMode) | Set whether to turn on the mute mode. |
| [enableFloatWindow](https://www.tencentcloud.com/document/product/647/51011#enableFloatWindow) | Set whether to enable floating windows. |
| [enableIncomingBanner](https://www.tencentcloud.com/document/product/647/51011#enableIncomingBanner) | Set whether to display incoming banner. |

## TUICallEngine (No UI)

`TUICallEngine` is an audio/video call component that **does not include UI elements**. If `TUICallKit` does not meet your requirements, you can use the APIs of `TUICallEngine` to customize your project.
| API | Description |
| --- | --- |
| [createInstance](https://www.tencentcloud.com/document/product/647/51012#createInstance) | Create a TUICallEngine instance (singleton). |
| [destroyInstance](https://www.tencentcloud.com/document/product/647/51012#destroyInstance) | Destroy TUICallEngine instance (singleton). |
| [Init](https://www.tencentcloud.com/document/product/647/51012#Init) | Authenticates the basic audio/video call capabilities. |
| [addObserver](https://www.tencentcloud.com/document/product/647/51012#addObserver) | Add listener. |
| [removeObserver](https://www.tencentcloud.com/document/product/647/51012#removeObserver) | Remove listener. |
| [calls](https://www.tencentcloud.com/document/product/647/51012#calls) | Initiate a one-to-one or multi-person call |
| [join](https://www.tencentcloud.com/document/product/647/51012#join) | Proactively join a call |
| [accept](https://www.tencentcloud.com/document/product/647/51012#accept) | Accept call. |
| [reject](https://www.tencentcloud.com/document/product/647/51012#reject) | Reject call. |
| [hangup](https://www.tencentcloud.com/document/product/647/51012#hangup) | Hang up call. |
| [ignore](https://www.tencentcloud.com/document/product/647/51012#hangup) | Ignore call. |
| [inviteUser](https://www.tencentcloud.com/document/product/647/51012#inviteUser) | Invite users to the current call. |
| [switchCallMediaType](https://www.tencentcloud.com/document/product/647/51012#switchCallMediaType) | Switch the call media type, such as from video call to audio call. |
| [startRemoteView](https://www.tencentcloud.com/document/product/647/51012#startRemoteView) | Subscribe to the video stream of a remote user. |
| [stopRemoteView](https://www.tencentcloud.com/document/product/647/51012#stopRemoteView) | Unsubscribe from the video stream of a remote user. |
| [openCamera](https://www.tencentcloud.com/document/product/647/51012#openCamera) | Turn on the camera. |
| [closeCamera](https://www.tencentcloud.com/document/product/647/51012#closeCamera) | Turn off the camera. |
| [switchCamera](https://www.tencentcloud.com/document/product/647/51012#switchCamera) | Switch camera. |
| [openMicrophone](https://www.tencentcloud.com/document/product/647/51012#openMicrophone) | Enable microphone. |
| [closeMicrophone](https://www.tencentcloud.com/document/product/647/51012#closeMicrophone) | Disable the microphone. |
| [selectAudioPlaybackDevice](https://www.tencentcloud.com/document/product/647/51012#selectAudioPlaybackDevice) | Select the audio playback device (Earpiece/Speakerphone). |
| [setSelfInfo](https://www.tencentcloud.com/document/product/647/51012#setSelfInfo) | Set the user's profile picture and nickname. |
| [enableMultiDeviceAbility](https://www.tencentcloud.com/document/product/647/51012#enableMultiDeviceAbility) | Sets whether to enable multi-device login for TUICallEngine (supported by the [Group Call package](https://trtc.io/document/54632?platform=ios&product=call&menulabel=web)). |
| [setVideoRenderParams](https://www.tencentcloud.com/document/product/647/51012#setVideoRenderParams) | Set the rendering mode of video. |
| [setVideoEncoderParams](https://www.tencentcloud.com/document/product/647/51012#setVideoEncoderParams) | Set the encoding parameters of video encoder. |
| [getTRTCCloudInstance](https://www.tencentcloud.com/document/product/647/51012#getTRTCCloudInstance) | Advanced features. |
| [setBeautyLevel](https://www.tencentcloud.com/document/product/647/51012#setBeautyLevel) | Set beauty level, support turning off default beauty. |

### TUICallObserver

`TUICallObserver` is the callback class of `TUICallEngine`. You can use it to listen for events.
| API | Description |
| --- | --- |
| [onError](https://www.tencentcloud.com/document/product/647/51013#onError) | An error occurred during the call. |
| [onCallReceived](https://www.tencentcloud.com/document/product/647/51013#onCallReceived) | A call was received. |
| [onCallCancelled](https://www.tencentcloud.com/document/product/647/51013#onCallCancelled) | The call was canceled. |
| [onCallBegin](https://www.tencentcloud.com/document/product/647/51013#onCallBegin) | The call was connected. |
| [onCallEnd](https://www.tencentcloud.com/document/product/647/51013#onCallEnd) | The call ended. |
| [onCallMediaTypeChanged](https://www.tencentcloud.com/document/product/647/51013#onCallMediaTypeChanged) | The call type changed. |
| [onUserReject](https://www.tencentcloud.com/document/product/647/51013#onUserReject) | A user declined the call. |
| [onUserNoResponse](https://www.tencentcloud.com/document/product/647/51013#onUserNoResponse) | A user didn't respond. |
| [onUserLineBusy](https://www.tencentcloud.com/document/product/647/51013#onUserLineBusy) | A user was busy. |
| [onUserJoin](https://www.tencentcloud.com/document/product/647/51013#onUserJoin) | A user joined the call. |
| [onUserLeave](https://www.tencentcloud.com/document/product/647/51013#onUserLeave) | A user left the call. |
| [onUserVideoAvailable](https://www.tencentcloud.com/document/product/647/51013#onUserVideoAvailable) | Whether a user has a video stream. |
| [onUserAudioAvailable](https://www.tencentcloud.com/document/product/647/51013#onUserAudioAvailable) | Whether a user has an audio stream. |
| [onUserVoiceVolumeChanged](https://www.tencentcloud.com/document/product/647/51013#onUserVoiceVolumeChanged) | The volume levels of all users. |
| [onUserNetworkQualityChanged](https://www.tencentcloud.com/document/product/647/51013#onUserNetworkQualityChanged) | The network quality of all users. |
| [onKickedOffline](https://www.tencentcloud.com/document/product/647/51013#onKickedOffline) | The current user was kicked offline. |
| [onUserSigExpired](https://www.tencentcloud.com/document/product/647/51013#onUserSigExpired) | The user sig is expired. |

## Definitions of Key Typ
| API | Description |
| --- | --- |
| TUICallMediaType | Call media type, Enumeration type: Unknown, Video, and Audio. |
| TUICallRole | Call role, Enumeration type: None, Call, and Called. |
| TUICallStatus | Call status, Enumeration type: None, Waiting, and Accept. |
| TUIRoomId | The room ID, which can be a number or string. |
| TUICallCamera | The camera type. Enumeration type: Front camera and Back camera. |
| TUIAudioPlaybackDevice | The audio playback device type. Enumeration type: Earpiece and Speakerphone. |
| TUINetworkQualityInfo | The current network quality. |

---

## Platform: Flutter SDK

## Common structures

### TUIResult

The return value of calling the API.
| Value | Type | Description |
| --- | --- | --- |
| code | String | If the code is empty "", it means the call succeeded, if the code is not empty "", it means the call failed. |
| message | String? | Error message |

### TUIRoomId

Room ID for audio and video in a call.

**Note：**

(1) `intRoomId` and `strRoomId` are mutually exclusive. If you choose to use `strRoomId`, `intRoomId` needs to be filled in as 0. If both are filled in, the SDK will prioritize `intRoomId`.

(2) Do not mix `intRoomId` and `strRoomId` because they are not interchangeable. For example, the number 123 and the string "123" represent two completely different rooms.
| Value | Type | Description |
| --- | --- | --- |
| intRoomId | int | Numeric room ID. |
| strRoomId | String | String room number. |

### VideoRenderParams

Video render parameters.
| Value | Type | Description |
| --- | --- | --- |
| fillMode | FillMode | Video image fill mode |
| rotation | Rotation | Video image rotation direction |

### VideoEncoderParams

Video encoding parameters.
| Value | Type | Description |
| --- | --- | --- |
| resolution | Resolution | Video resolution |
| resolutionMode | ResolutionMode | Video aspect ratio mode |

### TUICallParams

Call params.
| Value | Type | Description |
| --- | --- | --- |
| roomId | TUIRoomId | Room Id. |
| offlinePushInfo | TUIOfflinePushInfo | Offline push vendor configuration information. |
| timeout | String | Call timeout period, default: 30s, unit: seconds. |
| userData | String | An additional parameter. |
| chatGroupId | String | Group ID. |

### TUIOfflinePushInfo

Offline push vendor configuration information，please refer to:Offline call push.
| Value | Type | Description |
| --- | --- | --- |
| title | String | offlinepush notification title |
| desc | String | offlinepush notification description |
| ignoreIOSBadge | bool | Ignore badge count for offline push (only for iOS), if set to true, the message will not increase the unread count of the app icon on the iOS receiver's side. |
| iOSSound | String | Offline push sound setting (only for iOS). When sound = IOS_OFFLINE_PUSH_NO_SOUND , there will be no sound played when the message is received. When  sound = IOS_OFFLINE_PUSH_DEFAULT_SOUND , the system sound will be played when the message is received. If you want to customize the iOSSound, you need to link the audio file into the Xcode project first, and then set the audio file name (with extension) to the iOSSound. |
| androidSound | String | Offline push sound setting (only for Android, supported by IMSDK 6.1 and above). Only Huawei and Google phones support setting sound prompts. For Xiaomi phones, please refer to:  Xiaomi custom ringtones . In addition, for Google phones, in order to set sound prompts for FCM push on Android 8.0 and above systems, you must call  setAndroidFCMChannelID to set the channelID for it to take effect. |
| androidOPPOChannelID | String | Set the channel ID for OPPO phones with Android 8.0 and above systems. |
| androidVIVOClassification | int | Classification of VIVO push messages (deprecated interface, VIVO push service will optimize message classification rules on April 3, 2023. It is recommended to use setAndroidVIVOCategory to set the message category). 0: Operational messages, 1: System messages. The default value is 1. |
| androidXiaoMiChannelID | String | Set the channel ID for Xiaomi phones with Android 8.0 and above systems. |
| androidFCMChannelID | String | Set the channel ID for google phones with Android 8.0 and above systems. |
| androidHuaWeiCategory | String | Classification of Huawei push messages, please refer to: [Huawei message classification standard.](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/message-classification-0000001149358835) |
| isDisablePush | bool | Whether to turn off push notifications (default is on). |
| iOSPushType | TUICallIOSOfflinePushType | iOS offline push type，default is APNs |

### TUICallRecords

Call recording information.
| Value | Type | Description |
| --- | --- | --- |
| callId | String | Call recording ID. |
| inviter | String | Inviter ID. |
| inviteList | List<String> | List of invited user IDs. |
| scene | TUICallScene | Call scenario. |
| mediaType | TUICallMediaType | Media type. |
| groupId | String | Group ID. |
| role | TUICallRole | Role. |
| result | TUICallResultType | Call result type. |
| beginTime | int | Start time. |
| totalTime | int | Total time. |

### TUICallRecentCallsFilter

Call recording filtering conditions.
| Value | Type | Description |
| --- | --- | --- |
| begin | double | Start time. |
| end | double | End time. |
| resultType | TUICallResultType | Call result type. |

### CallObserverExtraInfo

Callback extended information.
| Type | Type | Description |
| --- | --- | --- |
| roomId | TUIRoomId | room ID |
| role | TUICallRole | Call role |
| userData | String | Custom extended field when initiating a call. For details, see TUICallParams. |
| chatGroupId | String | Group ID |

## **enum definition**

### TUICallMediaType

Call media type.
| Type | Description |
| --- | --- |
| none | Unknown |
| audio | Audio call |
| video | Video call |

### TUICallRole

Call role.
| Type | Description |
| --- | --- |
| none | Unknown |
| caller | Caller（inviter） |
| called | Callee（invitee） |

### TUICallStatus

Call status.
| Type | Description |
| --- | --- |
| none | Unknown |
| waiting | The call is currently waiting |
| accept | The call has been accepted |

### TUICallScene

Call scene.
| Type | Description |
| --- | --- |
| groupCall | Group call |
| singleCall | one to one call |

### TUINetworkQuality

Network quality.
| Type | Description |
| --- | --- |
| unknown | Unknown |
| excellent | Excellent |
| good | Good |
| poor | Poor |
| bad | Bad |
| vBad | Very bad |
| down | Down |

### FillMode

If the aspect ratio of the video display area is not equal to that of the video image, you need to specify the fill mode:
| Type | Description |
| --- | --- |
| fill | Fill mode: the video image will be centered and scaled to fill the entire display area, where parts that exceed the area will be cropped. The displayed image may be incomplete in this mode. |
| fit | Fit mode: the video image will be scaled based on its long side to fit the display area, where the short side will be filled with black bars. The displayed image is complete in this mode, but there may be black bars. |

### Rotation

We provides rotation angle setting APIs for local and remote images. The following rotation angles are all clockwise.
| Type | Description |
| --- | --- |
| rotation_0 | No rotation |
| rotation_90 | Clockwise rotation by 90 degrees |
| rotation_180 | Clockwise rotation by 180 degrees |
| rotation_270 | Clockwise rotation by 270 degrees |

### ResolutionMode

Video aspect ratio mode.
| Type | Description |
| --- | --- |
| landscape | Landscape resolution, such as Resolution.Resolution_640_360 + ResolutionMode.Landscape = 640 × 360. |
| portrait | Portrait resolution, such as Resolution.Resolution_640_360 + ResolutionMode.Portrait = 360 × 640. |

### Resolution

Video resolution.
| Type | Description |
| --- | --- |
| resolution_640_360 | Aspect ratio: 16:9；resolution: 640x360；recommended bitrate: 500kbps |
| resolution_960_540 | Aspect ratio: 16:9；resolution: 960x540；recommended bitrate: 850kbps |
| resolution_1280_720 | Aspect ratio: 16:9；resolution: 1280x720；recommended bitrate: 1200kbps |
| resolution_1920_1080 | Aspect ratio: 16:9；resolution: 1920x1080；recommended bitrate: 2000kbps |

### TUICallIOSOfflinePushType

iOS offline push type.
| Type | Description |
| --- | --- |
| APNs | APNs |
| VoIP | VoIP |

### TUICamera

Camera type.
| Type | Description |
| --- | --- |
| front | Front camera. |
| back | Rear camera. |

### TUIAudioPlaybackDevice

Audio playback device.
| Type | Description |
| --- | --- |
| speakerphone | Speaker |
| earpiece | Earpiece |

### TUICallResultType

Call recording type.
| Type | Description |
| --- | --- |
| unknown | Unknown |
| missed | Missed |
| incoming | Incoming call |
| outgoing | Outgoing Call |

### CallEndReason

Call end reason.
| Type | Description |
| --- | --- |
| unknown | Unknown |
| hangup | Hang up |
| reject | Deny |
| noResponse | No response |
| offline | Offline |
| lineBusy | Busy Line |
| canceled | Cancel call |
| otherDeviceAccepted | Other device answers |
| otherDeviceReject | Other device denies |
| endByServer | Backend ends |

---

## Platform: Flutter SDK

## TUICallObserver API

`TUICallObserver` is the callback class of `TUICallEngine`. You can use it to listen for events.

## Overview
| API | Description |
| --- | --- |
| onError | A call occurred during the call. |
| onUserInviting | Callback when a user is invited to join a call. |
| onCallReceived | A call invitation was received. |
| onCallNotConnected | Callback for call cancellation |
| onCallBegin | The call was connected. |
| onCallEnd | The call ended. |
| onCallMediaTypeChanged | The call type changed. |
| onUserReject | A user declined the call. |
| onUserNoResponse | A user didn't respond. |
| onUserLineBusy | A user was busy. |
| onUserJoin | A user joined the call. |
| onUserLeave | A user left the call. |
| onUserVideoAvailable | Whether a user had a video stream. |
| onUserAudioAvailable | Whether a user had an audio stream. |
| onUserVoiceVolumeChanged | The volume levels of all users. |
| onUserNetworkQualityChanged | The network quality of all users. |
| onKickedOffline | The current user was kicked offline. |
| onUserSigExpired | The user sig is expired. |

## Details

Listen to the events thrown by the Flutter plugin through addObserver.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onError: (int code, String message) { 
     
    },onCallCancelled: (String callerId) {  
     
    }, onCallBegin: (TUIRoomId roomId, TUICallMediaType callMediaType, TUICallRole callRole) {
       
    }, onCallEnd: (TUIRoomId roomId, TUICallMediaType callMediaType, TUICallRole callRole, double totalTime) {  
     
    }, onCallMediaTypeChanged: (TUICallMediaType oldCallMediaType, TUICallMediaType newCallMediaType) { 
    
    }, onUserReject: (String userId) {  
  
    }, onUserNoResponse: (String userId) { 
    
    }, onUserLineBusy: (String onUserLineBusy) { 
   
    }, onUserJoin: (String userId) {
  
    }, onUserLeave: (String userId) { 
      
    }, onUserVideoAvailable: (String userId, bool isVideoAvailable) { 
         
    }, onUserAudioAvailable: (String userId, bool isAudioAvailable) {  
       
    }, onUserNetworkQualityChanged: (List<TUINetworkQualityInfo> networkQualityList) {
  
    }, onCallReceived: (String callerId, List<String> calleeIdList, String groupId, TUICallMediaType callMediaType) {
  
    }, onUserVoiceVolumeChanged: (Map<String, int> volumeMap) {  
  
    }, onKickedOffline: () { 
   
    }, onUserSigExpired: () {  
   
    }  
));
```

### onError

An error occurred.

> **Note:**
> 

> This callback indicates that the SDK encountered an unrecoverable error. Such errors must be listened for, and UI reminders should be sent to users if necessary.
> 

``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onError: (int code, String message) {
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| code | int | The error code. |
| message | String | The error message. |

### onUserInviting

Callback when a user is invited to join a call.
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserInviting: (String userId) {
      // TODO
    }
));
```

The parameters are listed in the table below.
| Parameter | Type | Meaning |
| --- | --- | --- |
| userId | String | ID of the invited user |

### onCallReceived

Received a new incoming call request callback, the callee will receive it. You can listen to this event to determine whether to display the call answering interface.
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallReceived: (String callId, String callerId, List<String> calleeIdList, TUICallMediaType mediaType, CallObserverExtraInfo info) {
      // TODO
    }
));
```

The parameters are listed in the table below.
| Parameter | Type | Meaning |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| callerId | String | Calling ID (inviter) |
| calleeIdList | List<String> | Called ID list (invitees) |
| mediaType | TUICallMediaType | Media type of the call. For example: `TUICallMediaType.video` or `TUICallMediaType.audio` |
| info | CallObserverExtraInfo | Extended information |

### onCallNotConnected

Callback for call cancellation.
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
  onCallNotConnected: (String callId, TUICallMediaType mediaType, CallEndReason reason,
    String userId, CallObserverExtraInfo info) {
    // TODO
  }
));
```

The parameters are listed in the table below.
| Parameter | Type | Meaning |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallMediaType | Media type of the call. For example: `TUICallMediaType.video` or `TUICallMediaType.audio` |
| reason | CallEndReason | The causes for call termination |
| userId | String | User ID ending the call |
| info | CallObserverExtraInfo | Extended information |

### onCallBegin

Indicates that the call is connected, and both the caller and callee can receive it. You can listen to this event to start processes such as cloud recording and content review.
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallBegin: (String callId, TUICallMediaType mediaType, CallObserverExtraInfo info) {
    // TODO
  }
));
```

The parameters are listed in the table below.
| Parameter | Type | Meaning |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallMediaType | Media type of the call. For example: `TUICallMediaType.video` or `TUICallMediaType.audio` |
| info | CallObserverExtraInfo | Extended information |

### onCallEnd

Indicates that the call is connected, and both the caller and callee can receive it. You can listen to this event to display information such as call duration and call type, or to stop the cloud recording process.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallEnd: (String callId, TUICallMediaType mediaType, CallEndReason reason,
        String userId, double totalTime, CallObserverExtraInfo info) {
    // TODO
  }
));
```

The parameters are listed in the table below.
| Parameter | Type | Meaning |
| --- | --- | --- |
| callId | String | Unique ID of this call |
| mediaType | TUICallMediaType | Media type of the call. For example: `TUICallMediaType.video` or `TUICallMediaType.audio` |
| reason | CallEndReason | The causes for call termination |
| userId | String | User ID ending the call |
| totalTime | double | Duration of this call, unit ms |
| info | CallObserverExtraInfo | Extended information |

> **Note:**
> 

> Events on the client side are generally lost due to exceptions such as process termination. If you need to complete billing logic by monitoring call duration, it is recommended to use REST API to complete such processes.
> 

### onCallMediaTypeChanged

The call type changed.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallMediaTypeChanged: (TUICallMediaType oldCallMediaType, TUICallMediaType newCallMediaType) {  
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| oldCallMediaType | TUICallMediaType | The call type before the change. |
| newCallMediaType | TUICallMediaType | The call type after the change. |

### onUserReject

The call was rejected. In a one-to-one call, only the inviter will receive this callback. In a group call, all invitees will receive this callback.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserReject: (String userId) {    
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| res.userId | String | The user ID of the invitee who rejected the call. |

### onUserNoResponse

A user did not respond.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserNoResponse: (String userId) { 
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invitee who did not answer. |

### onUserLineBusy

A user is busy.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserLineBusy: (String onUserLineBusy) {   
    },
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the invitee who is busy. |

### onUserJoin

A user joined the call.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserJoin: (String userId) {  
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The ID of the user who joined the call. |

### onUserLeave

A user left the call.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserLeave: (String userId) {     
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The ID of the user who left the call. |

### onUserVideoAvailable

Whether a user is sending video.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserVideoAvailable: (String userId, bool isVideoAvailable) {   
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID. |
| isVideoAvailable | bool | Whether the user has video. |

### onUserAudioAvailable

Whether a user is sending audio.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserAudioAvailable: (String userId, bool isAudioAvailable) {       
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID. |
| isAudioAvailable | bool | Whether the user has audio. |

### onUserVoiceVolumeChanged

The volumes of all users.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserVoiceVolumeChanged: (Map<String, int> volumeMap) {  
    }
));
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| volumeMap | Map<String, int> | The volume table, which includes the volume of each user (userId). Value range: 0-100. |

### onUserNetworkQualityChanged

The network quality of all users.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserNetworkQualityChanged: (List<TUINetworkQualityInfo> networkQualityList) {
    }
));

class TUINetworkQualityInfo {    
    String userId;  
    TUINetworkQuality quality;  
    TUINetworkQualityInfo({required this.userId, required this.quality});
}

enum TUINetworkQuality {  
  unknown,  
  excellent,
  good,  
  poor,  
  bad,  
  vBad,  
  down
}
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| networkQualityList | List<TUINetworkQualityInfo> | The current network conditions for all users (userId). |

### onKickedOffline

The current user was kicked offline：At this time, you can prompt the user with a UI message and then invoke `init`again.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onKickedOffline: () { 
    }
));
```

### onUserSigExpired

The user sig is expired：At this time, you need to generate a new `userSig`, and then invoke `init`again.
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserSigExpired: () {    
    }  
));
```

## Deprecated interfaces

### onCallCancelled

The call was canceled by the inviter or timed out. This callback is received by an invitee. You can listen for this event to determine whether to show a missed call message.

This indicates that the call was canceled by the caller, timed out by the callee, rejected by the callee, or the callee was busy. There are multiple scenarios involved. You can listen to this event to achieve UI logic such as missed calls and resetting UI status. 
- Call cancellation by the caller: The caller receives the callback (userId is himself); the callee receives the callback (userId is the ID of the caller) .

- Callee timeout: the caller will simultaneously receive the onUserNoResponse and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) .

- Callee rejection: The caller will simultaneously receive the onUserReject and onCallCancelled callbacks (userId is his own ID); the callee receives the onCallCancelled callback (userId is his own ID) .

- Callee busy: The caller will simultaneously receive the onUserLineBusy and onCallCancelled callbacks (userId is his own ID); 

- Abnormal interruption: The callee failed to receive the call ，he receives this callback (userId is his own ID).

   ``` cpp
   TUICallEngine.instance.addObserver(TUICallObserver(
       onCallCancelled: (String userId) {    
       }
   ));
   ```

   The parameters are described below:

| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The user ID of the inviter. |

---

## Platform: Flutter SDK

## TUICallKit APIs

`TUICallKit` is an audio/video call component that **includes UI elements**. You can use its APIs to quickly implement an audio/video call application similar to WeChat. For directions on integration, see Integrating TUICallKit.

## API overview
| API | Description |
| --- | --- |
| login | Log in |
| logout | Sign out |
| setSelfInfo | Sets the user nickname and profile photo. |
| calls | Start a call. |
| join | Join the call. |
| enableMuteMode | Sets whether to turn on the mute mode. |
| enableFloatWindow | Sets whether to enable floating windows. |
| setCallingBell | Custom ringtone. |
| enableVirtualBackground | Turn On/Off the Virtual Background feature |

## Details

### login
``` cpp
Future<TUIResult> login(int sdkAppId, String userId, String userSig)
```
| Parameter | Type | Description |
| --- | --- | --- |
| sdkAppId | int | User SDKAppID |
| userId | String | User ID, a string type, can only include English letters (a-z and A-Z), numbers (0-9), hyphens (-), and underscores (_). |
| userSig | String | User Signature. UserSig is obtained by encrypting information such as sdkAppId and userId using the SDKSecretKey([Signature calculation method](https://www.tencentcloud.com/document/product/647/35166)). It serves as a ticket for authentication, enabling Tencent Cloud to determine if the current user is authorized to use TRTC services. |
| return value | TUIResult | Contains code and message information: code is empty ("") means the call is successful; code is not empty ("") means the call failed, see message for the reason of failure |

### logout
``` cpp
Future<void> logout()
```

### setSelfInfo

This API is used to set the alias and profile photo. The alias cannot exceed 500 bytes, and the profile photo is specified by a URL.
``` cpp
Future<TUIResult> setSelfInfo(String nickname, String avatar)
```
| Parameter | Type | Description |
| --- | --- | --- |
| nickName | String | The nick name. |
| avatar | String | The profile photo. |
| return value | TUIResult | Contains code and message information: code is empty ("") means the call is successful; code is not empty ("") means the call failed, see message for the reason of failure |

### calls

Initiate a call. **Supported by v2.9+.**
``` java
Future<TUIResult> calls(List<String> userIdList, TUICallMediaType mediaType, TUICallParams params)
```
| Parameter | Type | Description |
| --- | --- | --- |
| userIdList | List<String> | The target user IDs. |
| callMediaType | TUICallMediaType | The call type, which can be video or audio. |
| params | TUICallParams | **Optional **Extended call parameters, such as room number, call invitation timeout, offline push of custom content, etc. |

### join

Actively join the call. **Supported by v2.9+.**
``` java
Future<void> join(String callId)
```
| Parameter | Type | Description |
| --- | --- | --- |
| callId | String | Unique ID for this call. |

### enableMuteMode

This API is used to set whether to turn on the mute mode.
``` javascript
Future<void> enableMuteMode(bool enable)
```
| Parameter | Type | Description |
| --- | --- | --- |
| enable | bool | Turn on and off the mute; true means to turn on the mute |

### enableFloatWindow

This API is used to set whether to enable floating windows. The default value is `false`, and the floating window button in the top left corner of the call view is hidden. If it is set to `true`, the button will become visible.
``` javascript
Future<void> enableFloatWindow(bool enable)
```
| Parameter | Type | Description |
| --- | --- | --- |
| enable | bool | The default value is false, and the floating window button in the top left corner of the call view is hidden. If it is set to true, the button will become visible. |

### setCallingBell

Custom ringtone.
``` javascript
Future<void> setCallingBell(String assetName)
```
| Parameter | Type | Description |
| --- | --- | --- |
| assetName | String | The path of the ringtone. The ringtone file needs to be added to the assets resource of the main project. |

### enableVirtualBackground

Turn On/Off the Virtual Background feature. After enabling the Virtual Background feature, you can display the Blurry Background feature button on the UI. Clicking the button will directly enable the Blurry Background feature.
``` javascript
Future<void> enableVirtualBackground(bool enable)
```
| Parameter | Type | Meaning |
| --- | --- | --- |
| enable | bool | Turn on, turn off mute; true means mute is on |

## Deprecated interfaces

### call

This API is used to make a (one-to-one) call.

> **Note：**
> 

> This interface has been deprecated in v2.9+. It is recommended to use the calls interface instead.
> 

``` cpp
Future<void> call(String userId, TUICallMediaType callMediaType, [TUICallParams? params])
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| userId | String | The target user ID. |
| callMediaType | TUICallMediaType | `The call type, which can be video or audio.` |

### groupCall

This API is used to make a group call.

> **Note：**
> 

> This interface has been deprecated in v2.9+. It is recommended to use the calls interface instead.
> 

``` javascript
Future<void> groupCall(String groupId, List<String> userIdList, TUICallMediaType callMediaType,[TUICallParams? params])
```

The parameters are described below:
| Parameter | Type | Description |
| --- | --- | --- |
| groupId | String | The group ID. |
| userIdList | List<String> | The target user IDs. |
| callMediaType | TUICallMediaType | `The call type, which can be video or audio.` |

### joinInGroupCall

This API is used to join a group call.Before making a group call, you need to create an IM group first.

> **Note：**
> 

> This interface has been deprecated in v2.9+. It is recommended to use the join interface instead.
> 

``` javascript
Future<void> joinInGroupCall(TUIRoomId roomId, String groupId, TUICallMediaType callMediaType)
```
| Parameter | Type | Description |
| --- | --- | --- |
| roomId | TUIRoomID | The room ID. |
| groupId | String | The group ID. |
| callMediaType | TUICallMediaType | `The call type, which can be video or audio.` |
