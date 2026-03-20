> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## 重要提醒
**文档内容说明：**  
 - 文档涉及到 `TUICallEngine` API 和 `TUICallKit` 含 UI 组件 API 两部分
 - `TUICallEngine` API 文档请参考 **Part One**
 - `TUICallKit`含 UI 组件 API 文档请参考 **Part Two**

## Part One
**TUICallEngine API 文档**
- TUICallEngine API 涉及到 web 和 小程序。
- 当用户查询 `TUICallEngine` API 时，必须先查阅 TUICallEngine 的官方 API 文档，然后再给出 API 的正确用法示例, 禁止自己推测 API 的用法。
- 以下是各平台 API 的文档链接，请直接访问文档链接获取 API 用法：
- [TUICallEngine Web API 列表文档](https://cloud.tencent.com/document/product/647/78757)
- [TUICallEngine Web API 使用示例文档](https://web.sdk.qcloud.com/component/trtccalling/doc/TUICallEngine/web/zh-cn/TUICallEngine.html)
- [TUICallEngine 小程序 API 文档](https://cloud.tencent.com/document/product/647/78761)

## Part Two
TUICallKit 是音视频通话组件的**含 UI 接口**，使用 TUICallKit API，您可以通过简单接口快速实现一个类微信的音视频通话场景；更详细的接入步骤，详见：快速接入 TUICallKit。

> **说明：**
> - 为提供更优质的音视频通信能力， TUICallKit npm 包 [@trtc/calls-uikit-react](https://www.npmjs.com/package/@trtc/calls-uikit-react)、[@trtc/calls-uikit-vue](https://www.npmjs.com/package/@trtc/calls-uikit-vue)、[@trtc/calls-uikit-vue2](https://www.npmjs.com/package/@trtc/calls-uikit-vue2)、[@trtc/calls-uikit-vue2.6](https://www.npmjs.com/package/@trtc/calls-uikit-vue2.6) 在 4.0.0 版本进行了重大升级。

## API 概览
| API | 描述 |
| --- | --- |
| [<TUICallKit />](https://cloud.tencent.com/document/product/647/81015#TUICallKit) | TUICallKit 通话 UI 组件。 |
| [init](https://cloud.tencent.com/document/product/647/81015#init) | 初始化 TUICallKit 组件实例。 |
| [calls](https://cloud.tencent.com/document/product/647/81015#calls) | 发起单人或多人通话。 |
| [join](https://cloud.tencent.com/document/product/647/81015#join) | 主动加入通话。 |
| [setCallingBell](https://cloud.tencent.com/document/product/647/81015#setCallingBell) | 设置自定义来电铃音。 |
| [setSelfInfo](https://cloud.tencent.com/document/product/647/81015#setSelfInfo) | 设置用户昵称和头像。 |
| [enableMuteMode](https://cloud.tencent.com/document/product/647/81015#enableMuteMode) | 开启/关闭来电铃声。 |
| [enableFloatWindow](https://cloud.tencent.com/document/product/647/81015#enableFloatWindow) | 开启/关闭悬浮窗功能。 |
| [enableVirtualBackground](https://cloud.tencent.com/document/product/647/81015#enableVirtualBackground) | 开启/关闭模糊背景的功能按钮。 |
| [setLanguage](https://cloud.tencent.com/document/product/647/81015#setLanguage) | 设置 TUICallKit 组件通话语言。 |
| [hideFeatureButton](https://cloud.tencent.com/document/product/647/81015#hideFeatureButton) | 隐藏按钮。 |
| [setLocalViewBackgroundImage](https://cloud.tencent.com/document/product/647/81015#setLocalViewBackgroundImage) | 设置本地用户通话界面背景图。 |
| [setRemoteViewBackgroundImage](https://cloud.tencent.com/document/product/647/81015#setRemoteViewBackgroundImage) | 设置远端用户通话界面背景图。 |
| [setLayoutMode](https://cloud.tencent.com/document/product/647/81015#setLayoutMode) | 设置通话界面布局模式。 |
| [setCameraDefaultState](https://cloud.tencent.com/document/product/647/81015#setCameraDefaultState) | 设置摄像头是否默认打开。 |
| [destroyed](https://cloud.tencent.com/document/product/647/81015#destroyed) | 销毁 TUICallKit 组件实例。 |
| [getTUICallEngineInstance](https://cloud.tencent.com/document/product/647/81015#getTUICallEngineInstance) | 获取 TUICallEngine 实例。 |

## <`TUICallKit />` 组件

### 属性概览
| 属性 | 描述 | 类型 | 是否必填 | 默认值 | 支持 vue | 支持 react |
| --- | --- | --- | --- | --- | --- | --- |
| allowedMinimized | 是否允许悬浮窗 | boolean | 否 | false | √ | √ |
| allowedFullScreen | 是否允许通话界面全屏 | boolean | 否 | true | √ | √ |
| [videoDisplayMode](https://cloud.tencent.com/document/product/647/81015#videoDisplayMode) | 通话界面显示模式 | VideoDisplayMode | 否 | VideoDisplayMode.COVER | √ | √ |
| [videoResolution](https://cloud.tencent.com/document/product/647/81015#videoResolution) | 通话分辨率 | VideoResolution | 否 | VideoResolution.RESOLUTION_480P | √ | √ |
| beforeCalling | 拨打电话前与收到通话邀请前会执行此函数 | function(type, error) | 否 | - | √ | √ |
| afterCalling | 结束通话后会执行此函数 | function() | 否 | - | √ | √ |
| onMinimized | 组件切换最小化状态时会执行此函数，[STATUS 值说明](https://cloud.tencent.com/document/product/647/81015#STATUS) | function(oldStatus, newStatus) | 否 | - | √ | √ |
| kickedOut | 组件抛出的事件，当前登录用户被踢出登录时会触发该事件，通话也会自动结束 | function() | 否 | - | √ | × |
| statusChanged | 组件抛出的事件，当通话状态发生变化时，会触发该事件，通话状态种类详见，[STATUS 值说明](https://cloud.tencent.com/document/product/647/81015#STATUS) | function({oldStatus, newStatus}) | 否 | - | √ | × |

### 示例代码

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

【Vue2.7】
``` javascript
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
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue2";

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

【Vue2.6】
``` javascript
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
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue2.6";

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

## TUICallKitAPI API 详情

【React】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react"; 
```

【Vue3】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue"; 
```

【Vue2.7】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue2"; 
```

【Vue2.6】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue2.6";
```

### init

初始化 TUICallKit。
``` javascript
try {
  await TUICallKitAPI.init({ SDKAppID, userID, userSig });
  // If you already have a tim instance in your project, you need to pass it in here.
  // await TUICallKitAPI.init({ tim, SDKAppID, userID, userSig}); 
  console.log("[TUICallKit] init succeeds.");
} catch (error: any) {
  console.error(`[TUICallKit] init failed. Reason: ${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| SDKAppID | Number | 是 | 应用的 SDKAppID，您可以在实时音视频控制台中找到您的 SDKAppID。具体详见：开通服务 |
| userID | String | 是 | 即用户名，只允许包含英文字母（a-z 和 A-Z）、数字（0-9）、连词符（-）和下划线（_）。 **注意：**不支持同一个 userID 在两台不同的设备上同时进入同一个房间通话，否则会相互干扰。 |
| userSig | String | 是 | 使用 SecretKey 对 SDKAppID、UserID 等信息进行加密，就可以得到 userSig。 它是一个鉴权用的票据，用于腾讯云识别当前用户是否能够使用 TRTC 的服务，获取方式请参见 [UserSig](https://cloud.tencent.com/document/product/647/17275) |
| tim | TencentCloudChat | 否 | tim 是 [TencentCloudChat](https://www.npmjs.com/package/@tencentcloud/chat) SDK 的实例 |

### calls

发起单人或多人通话。
``` javascript
try {
  await TUICallKitAPI.calls({ 
    userIDList: ['jack', 'tom'], 
    type: CallMediaType.VIDEO_CALL 
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the groupCall API. Reason:${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| userIDList | Array<String> | 是 | 被呼叫的用户列表 |
| type | [CallMediaType](https://cloud.tencent.com/document/product/647/81015#TUICallType) | 是 | 通话的媒体类型，参数值说明参见 [CallMediaType 通话类型](https://cloud.tencent.com/document/product/647/81015#TUICallType) 兼容旧版本：语音通话(type = 1)、视频通话(type = 2) |
| chatGroupID | String | 是 | 与 chat 结合使用时， chat 群组的群 ID |
| roomID | Number | 否 | 数字房间号，范围 [1, 2147483647] |
| strRoomID | String | 否 | 字符串房间号。**v3.3.1+ 支持** 1. roomID 与 strRoomID 是互斥的，若您选用 strRoomID，则 roomID 需要填写为 0。若两者都填，SDK 将优先选用 roomID。 2. 不要混用 roomID 和 strRoomID，因为它们之间是不互通的，例如数字 123 和字符串 "123" 是两个完全不同的房间。 |
| timeout | Number | 否 | 通话超时时间，默认：30s，单位：秒。设置范围：10s ~ 600s。如果传入0，默认30s。 |
| userData | String | 否 | 发起通话时自定义扩展字段，被呼叫用户在 ON_CALL_RECEIVED 事件中有该参数 |
| [offlinePushInfo](https://cloud.tencent.com/document/product/647/81015#offlinePushInfo) | Object | 否 | 自定义离线消息推送参数 |

### join

加入群组中已有的音视频通话。
``` javascript
try {
  await TUICallKitAPI.join({callId: xx});
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the join API. Reason: ${error}`);
}
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| callId | string | 是 | 此次通话的唯一 ID。 |

### setLanguage

设置语言，目前支持：中文、英文、日文。
``` javascript
TUICallKitAPI.setLanguage("zh-cn"); // "en" | "zh-cn" | "ja_JP"
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| lang | String | 是 | 语言类型`en`、`zh-cn` 和 `ja_JP`。 |

### setSelfInfo

设置当前用户昵称和头像。

> **注意：**
> 

> **通话中使用该接口修改用户信息，UI 不会立即更新，需要等到下次通话才能看到变化**。
> 

``` javascript
try {
  await TUICallKitAPI.setSelfInfo({ nickName: "xxx", avatar: "" });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the setSelfInfo API. Reason: ${error}`;
}
```

参数如下表所示：
| **参数** | **类型** | **是否必填** | **含义** |
| --- | --- | --- | --- |
| nickName | String | 是 | 自己的昵称 |
| avatar | String | 是 | 自己头像地址 |

### setCallingBell
- 自定义用户的来电铃音。

- 仅限传入本地 MP3 格式的文件地址，需要确保该文件目录是应用可以访问的。

- 使用 import 方式引入铃声文件。

- 如需恢复默认铃声，`filePath` 传空即可。

   ``` javascript
   try {
     await TUICallKitAPI.setCallingBell(filePath?: string)
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the setCallingBell API. Reason: ${error}`);
   }
   ```

   参数如下表所示：

| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| filePath | String | 是 | 铃声文件地址 |

### enableFloatWindow

开启/关闭悬浮窗功能。默认为 false，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。
``` javascript
try {
  await TUICallKitAPI.enableFloatWindow(enable: Boolean)
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableFloatWindow API. Reason: ${error}`);
}
```

### enableMuteMode

开启/关闭来电铃声。开启后，收到通话请求时，不会播放来电铃声。
``` javascript
try {
  await TUICallKitAPI.enableMuteMode(enable: boolean)
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableMuteMode API. Reason: ${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| enable | Boolean | 是 | 开启/关闭来电铃声。默认 false。 |

### enableVirtualBackground

开启/关闭模糊背景功能。如果想设置图片背景模糊参见 [Web](https://cloud.tencent.com/document/product/647/106071)。通过调用接口，您可以在 UI 上显示模糊背景的功能按钮，点击按钮可直接启用模糊背景功能。
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react";
const enable = true;
TUICallKitAPI.enableVirtualBackground(enable);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| enable | boolean | 是 | - enable = true 显示模糊背景按钮 - enable = false 不显示模糊背景按钮 |

### destroyed

销毁 TUICallKit 实例。

该方法不会自动退出`tim`，需要手动退出`tim.logout();`。
``` javascript
try {
  await TUICallKitAPI.destroyed();
} catch (error: any) {
  alert(`[TUICallKit] Failed to call the destroyed API. Reason: ${error}`);
}
```

### hideFeatureButton

隐藏功能按钮，目前仅支持 摄像头，麦克风和切换前后置按钮。

【Vue】
``` javascript
import { TUICallKitAPI, FeatureButton } from "@trtc/calls-uikit-vue";

TUICallKitAPI.hideFeatureButton(FeatureButton.Camera);
```

【React】
``` javascript
import { TUICallKitAPI, FeatureButton } from "@trtc/calls-uikit-react";

TUICallKitAPI.hideFeatureButton(FeatureButton.Camera);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| buttonName | [FeatureButton](https://cloud.tencent.com/document/product/647/81015#FeatureButton) | 是 | 按钮名称 |

### setLocalViewBackgroundImage

设置本地用户通话背景。
``` typescript
TUICallKitAPI.setLocalViewBackgroundImage('');
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| url | string | 是 | 图片地址（支持本地路径和网络地址） |

### setRemoteViewBackgroundImage

设置远端用户通话背景。
``` javascript
const userId = 'xxx';
const url = '';
TUICallKitAPI.setRemoteViewBackgroundImage(userId, url);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| userId | string | 是 | 远端用户 userId，设置为 '*' 表示对所有远端用户生效 |
| url | string | 是 | 图片地址（支持本地路径和网络地址） |

### setLayoutMode

设置通话界面布局模式。

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

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| layoutMode | [LayoutMode](https://cloud.tencent.com/document/product/647/81015#LayoutMode) | 是 | 用户流的布局模式 |

### setCameraDefaultState

设置摄像头是否默认打开。
``` javascript
TUICallKitAPI.setCameraDefaultState(true);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| isOpen | boolean | 是 | 是否开启摄像头 |

### getTUICallEngineInstance

获取 TUICallEngine 实例。
``` javascript
TUICallKitAPI.getTUICallEngineInstance();
```

## TUICallKit 类型定义

### videoDisplayMode

画面显示模式 `videoDisplayMode` 属性有三个值：
- `VideoDisplayMode.CONTAIN`

- `VideoDisplayMode.COVER`

- `VideoDisplayMode.FILL`，默认值是`VideoDisplayMode.COVER`。

| 属性 | 值 | 描述 |
| --- | --- | --- |
| videoDisplayMode | VideoDisplayMode.CONTAIN | - 优先保证视频内容全部显示。 - 视频尺寸等比缩放，直至视频窗口的一边与视窗边框对齐。 - 如果视频尺寸与显示视窗尺寸不一致，在保持长宽比的前提下，将视频进行缩放后填满视窗，缩放后的视频四周会有一圈黑边。 |
| VideoDisplayMode.COVER | - 优先保证视窗被填满。 - 视频尺寸等比缩放，直至整个视窗被视频填满。 - 如果视频长宽与显示窗口不同，则视频流会按照显示视窗的比例进行周边裁剪或图像拉伸后填满视窗。 |  |
| VideoDisplayMode.FILL | - 保证视窗被填满的同时保证视频内容全部显示，但是不保证视频尺寸比例不变。 - 视频的宽高会被拉伸至和视窗尺寸一致。 |  |

### videoResolution

分辨率 `videoResolution` 有三个值：
- `VideoResolution.RESOLUTION_480P`

- `VideoResolution.RESOLUTION_720P`

- `VideoResolution.RESOLUTION_1080P`，默认值是`VideoResolution.RESOLUTION_480P`。

   **分辨率说明：**

| 视频 Profile | 分辨率（宽 × 高） | 帧率（fps） | 码率（kbps） |
| --- | --- | --- | --- |
| 480p | 640 × 480 | 15 | 900 |
| 720p | 1280 × 720 | 15 | 1500 |
| 1080p | 1920 × 1080 | 15 | 2000 |

   **常见问题：**

- iOS 13&14不支持编码高于 720p 的视频，建议在这两个系统版本限制最高采集 720P。参见 [iOS Safari 已知问题 case 12](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-02-info-webrtc-issues.html#h2-4)。

- Firefox 不支持自定义视频帧率（默认为 30fps）。

- 受系统性能占用，摄像头采集能力和浏览器限制等因素的影响，视频分辨率、帧率、码率的实际值不一定能够完全匹配设定值，在这种情况下，浏览器会自动调整 Profile 尽可能匹配设定值。

### STATUS
| 属性值 | 描述 |
| --- | --- |
| STATUS.IDLE | 闲置状态 |
| STATUS.BE_INVITED | 收到通话邀请 |
| STATUS.DIALING_C2C | 正在 1v1 呼叫 |
| STATUS.DIALING_GROUP | 正在群组呼叫 |
| STATUS.CALLING_C2C_AUDIO | 正在 1v1 语音通话 |
| STATUS.CALLING_C2C_VIDEO | 正在 1v1 视频通话 |
| STATUS.CALLING_GROUP_AUDIO | 正在群组语音通话 |
| STATUS.CALLING_GROUP_VIDEO | 正在群组视频通话 |

### **CallMediaType**
| CallMediaType 类型 | 描述 |
| --- | --- |
| CallMediaType.AUDIO | 语音通话 |
| CallMediaType.VIDEO | 视频通话 |

### **offlinePushInfo**
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| offlinePushInfo.title | String | 否 | 离线推送标题（选填） |
| offlinePushInfo.description | String | 否 | 离线推送内容（选填） |
| offlinePushInfo.androidOPPOChannelID | String | 否 | 离线推送设置 OPPO 手机 8.0 系统及以上的渠道 ID（选填） |
| offlinePushInfo.extension | String | 否 | 离线推送透传内容，可以用于设置 Android [Notification 模式](https://cloud.tencent.com/document/product/647/81015#Android Notification) 和 [VoIP 模式](https://cloud.tencent.com/document/product/647/81015#Android VoIP)。**默认：**Notification 模式，会是系统的通知；VoIP 模式是需要传字段。 |
| offlinePushInfo.ignoreIOSBadge | Boolean | 否 | 离线推送忽略 badge 计数（仅对 iOS 生效）， 如果设置为 true，在 iOS 接收端，这条消息不会使 App 的应用图标未读计数增加。 |
| offlinePushInfo.iOSSound | String | 否 | 离线推送声音设置（仅对 iOS 生效）。 |
| offlinePushInfo.androidSound | String | 否 | 离线推送声音设置（仅对 Android 生效）。 |
| offlinePushInfo.androidVIVOClassification | Number | 否 | VIVO 推送消息分类。 |
| offlinePushInfo.androidXiaoMiChannelID | String | 否 | 小米手机 8.0 系统及以上的渠道 ID。 |
| offlinePushInfo.androidFCMChannelID | String | 否 | FCM 通道手机 8.0 系统及以上的渠道 ID。 |
| offlinePushInfo.androidHuaWeiCategory | String | 否 | 华为推送消息分类。 |
| offlinePushInfo.isDisablePush | Boolean | 否 | 是否关闭推送（默认开启推送）。 |
| offlinePushInfo.iOSPushType | Number | 否 | iOS 离线推送类型。0-普通推送；1-VoIP推送。默认：0。 |

#### Android Notification 模式
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, VoIP; 1, notification
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

#### Android VoIP 模式
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
| FeatureButton 类型 | 描述 |
| --- | --- |
| FeatureButton.Camera | 摄像头按钮 |
| FeatureButton.Microphone | 麦克风按钮 |
| FeatureButton.SwitchCamera | 切换前后置摄像头按钮 |
| FeatureButton.InviteUser | 邀请他人按钮 |

### LayoutMode
| LayoutMode 类型 | 描述 |
| --- | --- |
| LayoutMode.LocalInLargeView | 本地用户在大窗显示 |
| LayoutMode.RemoteInLargeView | 远端用户在大窗显示 |

---

## Platform: Vue

## 重要提醒
**文档内容说明：**  
 - 文档涉及到 `TUICallEngine` API 和 `TUICallKit` 含 UI 组件 API 两部分。
 - `TUICallEngine` API 文档请参考 **Part One**
 - `TUICallKit`含 UI 组件 API 文档请参考 **Part Two**

## Part One
**TUICallEngine API 文档**
- TUICallEngine API 涉及到 web 和 小程序。
- 当用户查询 `TUICallEngine` API 时，必须先查阅 TUICallEngine 的官方 API 文档，然后再给出 API 的正确用法示例, 禁止自己推测 API 的用法。
- 以下是各平台 API 的文档链接，请直接访问文档链接获取 API 用法：
- [TUICallEngine Web API 列表文档](https://cloud.tencent.com/document/product/647/78757)
- [TUICallEngine Web API 使用示例文档](https://web.sdk.qcloud.com/component/trtccalling/doc/TUICallEngine/web/zh-cn/TUICallEngine.html)
- [TUICallEngine 小程序 API 文档](https://cloud.tencent.com/document/product/647/78761)

## Part Two
TUICallKit 是音视频通话组件的**含 UI 接口**，使用 TUICallKit API，您可以通过简单接口快速实现一个类微信的音视频通话场景；更详细的接入步骤，详见：快速接入 TUICallKit。

> **说明：**
> - 为提供更优质的音视频通信能力， TUICallKit npm 包 [@trtc/calls-uikit-react](https://www.npmjs.com/package/@trtc/calls-uikit-react)、[@trtc/calls-uikit-vue](https://www.npmjs.com/package/@trtc/calls-uikit-vue)、[@trtc/calls-uikit-vue2](https://www.npmjs.com/package/@trtc/calls-uikit-vue2)、[@trtc/calls-uikit-vue2.6](https://www.npmjs.com/package/@trtc/calls-uikit-vue2.6) 在 4.0.0 版本进行了重大升级。

## API 概览
| API | 描述 |
| --- | --- |
| [<TUICallKit />](https://cloud.tencent.com/document/product/647/81015#TUICallKit) | TUICallKit 通话 UI 组件。 |
| [init](https://cloud.tencent.com/document/product/647/81015#init) | 初始化 TUICallKit 组件实例。 |
| [calls](https://cloud.tencent.com/document/product/647/81015#calls) | 发起单人或多人通话。 |
| [join](https://cloud.tencent.com/document/product/647/81015#join) | 主动加入通话。 |
| [setCallingBell](https://cloud.tencent.com/document/product/647/81015#setCallingBell) | 设置自定义来电铃音。 |
| [setSelfInfo](https://cloud.tencent.com/document/product/647/81015#setSelfInfo) | 设置用户昵称和头像。 |
| [enableMuteMode](https://cloud.tencent.com/document/product/647/81015#enableMuteMode) | 开启/关闭来电铃声。 |
| [enableFloatWindow](https://cloud.tencent.com/document/product/647/81015#enableFloatWindow) | 开启/关闭悬浮窗功能。 |
| [enableVirtualBackground](https://cloud.tencent.com/document/product/647/81015#enableVirtualBackground) | 开启/关闭模糊背景的功能按钮。 |
| [setLanguage](https://cloud.tencent.com/document/product/647/81015#setLanguage) | 设置 TUICallKit 组件通话语言。 |
| [hideFeatureButton](https://cloud.tencent.com/document/product/647/81015#hideFeatureButton) | 隐藏按钮。 |
| [setLocalViewBackgroundImage](https://cloud.tencent.com/document/product/647/81015#setLocalViewBackgroundImage) | 设置本地用户通话界面背景图。 |
| [setRemoteViewBackgroundImage](https://cloud.tencent.com/document/product/647/81015#setRemoteViewBackgroundImage) | 设置远端用户通话界面背景图。 |
| [setLayoutMode](https://cloud.tencent.com/document/product/647/81015#setLayoutMode) | 设置通话界面布局模式。 |
| [setCameraDefaultState](https://cloud.tencent.com/document/product/647/81015#setCameraDefaultState) | 设置摄像头是否默认打开。 |
| [destroyed](https://cloud.tencent.com/document/product/647/81015#destroyed) | 销毁 TUICallKit 组件实例。 |
| [getTUICallEngineInstance](https://cloud.tencent.com/document/product/647/81015#getTUICallEngineInstance) | 获取 TUICallEngine 实例。 |

## <`TUICallKit />` 组件

### 属性概览
| 属性 | 描述 | 类型 | 是否必填 | 默认值 | 支持 vue | 支持 react |
| --- | --- | --- | --- | --- | --- | --- |
| allowedMinimized | 是否允许悬浮窗 | boolean | 否 | false | √ | √ |
| allowedFullScreen | 是否允许通话界面全屏 | boolean | 否 | true | √ | √ |
| [videoDisplayMode](https://cloud.tencent.com/document/product/647/81015#videoDisplayMode) | 通话界面显示模式 | VideoDisplayMode | 否 | VideoDisplayMode.COVER | √ | √ |
| [videoResolution](https://cloud.tencent.com/document/product/647/81015#videoResolution) | 通话分辨率 | VideoResolution | 否 | VideoResolution.RESOLUTION_480P | √ | √ |
| beforeCalling | 拨打电话前与收到通话邀请前会执行此函数 | function(type, error) | 否 | - | √ | √ |
| afterCalling | 结束通话后会执行此函数 | function() | 否 | - | √ | √ |
| onMinimized | 组件切换最小化状态时会执行此函数，[STATUS 值说明](https://cloud.tencent.com/document/product/647/81015#STATUS) | function(oldStatus, newStatus) | 否 | - | √ | √ |
| kickedOut | 组件抛出的事件，当前登录用户被踢出登录时会触发该事件，通话也会自动结束 | function() | 否 | - | √ | × |
| statusChanged | 组件抛出的事件，当通话状态发生变化时，会触发该事件，通话状态种类详见，[STATUS 值说明](https://cloud.tencent.com/document/product/647/81015#STATUS) | function({oldStatus, newStatus}) | 否 | - | √ | × |

### 示例代码

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

【Vue2.7】
``` javascript
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
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue2";

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

【Vue2.6】
``` javascript
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
import { TUICallKit, TUICallKitAPI, VideoDisplayMode, VideoResolution, STATUS } from "@trtc/calls-uikit-vue2.6";

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

## TUICallKitAPI API 详情

【React】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react"; 
```

【Vue3】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue"; 
```

【Vue2.7】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue2"; 
```

【Vue2.6】
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-vue2.6";
```

### init

初始化 TUICallKit。
``` javascript
try {
  await TUICallKitAPI.init({ SDKAppID, userID, userSig });
  // If you already have a tim instance in your project, you need to pass it in here.
  // await TUICallKitAPI.init({ tim, SDKAppID, userID, userSig}); 
  console.log("[TUICallKit] init succeeds.");
} catch (error: any) {
  console.error(`[TUICallKit] init failed. Reason: ${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| SDKAppID | Number | 是 | 应用的 SDKAppID，您可以在实时音视频控制台中找到您的 SDKAppID。具体详见：开通服务 |
| userID | String | 是 | 即用户名，只允许包含英文字母（a-z 和 A-Z）、数字（0-9）、连词符（-）和下划线（_）。 **注意：**不支持同一个 userID 在两台不同的设备上同时进入同一个房间通话，否则会相互干扰。 |
| userSig | String | 是 | 使用 SecretKey 对 SDKAppID、UserID 等信息进行加密，就可以得到 userSig。 它是一个鉴权用的票据，用于腾讯云识别当前用户是否能够使用 TRTC 的服务，获取方式请参见 [UserSig](https://cloud.tencent.com/document/product/647/17275) |
| tim | TencentCloudChat | 否 | tim 是 [TencentCloudChat](https://www.npmjs.com/package/@tencentcloud/chat) SDK 的实例 |

### calls

发起单人或多人通话。
``` javascript
try {
  await TUICallKitAPI.calls({ 
    userIDList: ['jack', 'tom'], 
    type: CallMediaType.VIDEO_CALL 
  });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the groupCall API. Reason:${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| userIDList | Array<String> | 是 | 被呼叫的用户列表 |
| type | [CallMediaType](https://cloud.tencent.com/document/product/647/81015#TUICallType) | 是 | 通话的媒体类型，参数值说明参见 [CallMediaType 通话类型](https://cloud.tencent.com/document/product/647/81015#TUICallType) 兼容旧版本：语音通话(type = 1)、视频通话(type = 2) |
| chatGroupID | String | 是 | 与 chat 结合使用时， chat 群组的群 ID |
| roomID | Number | 否 | 数字房间号，范围 [1, 2147483647] |
| strRoomID | String | 否 | 字符串房间号。**v3.3.1+ 支持** 1. roomID 与 strRoomID 是互斥的，若您选用 strRoomID，则 roomID 需要填写为 0。若两者都填，SDK 将优先选用 roomID。 2. 不要混用 roomID 和 strRoomID，因为它们之间是不互通的，例如数字 123 和字符串 "123" 是两个完全不同的房间。 |
| timeout | Number | 否 | 通话超时时间，默认：30s，单位：秒。设置范围：10s ~ 600s。如果传入0，默认30s。 |
| userData | String | 否 | 发起通话时自定义扩展字段，被呼叫用户在 ON_CALL_RECEIVED 事件中有该参数 |
| [offlinePushInfo](https://cloud.tencent.com/document/product/647/81015#offlinePushInfo) | Object | 否 | 自定义离线消息推送参数 |

### join

加入群组中已有的音视频通话。
``` javascript
try {
  await TUICallKitAPI.join({callId: xx});
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the join API. Reason: ${error}`);
}
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| callId | string | 是 | 此次通话的唯一 ID。 |

### setLanguage

设置语言，目前支持：中文、英文、日文。
``` javascript
TUICallKitAPI.setLanguage("zh-cn"); // "en" | "zh-cn" | "ja_JP"
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| lang | String | 是 | 语言类型`en`、`zh-cn` 和 `ja_JP`。 |

### setSelfInfo

设置当前用户昵称和头像。

> **注意：**
> 

> **通话中使用该接口修改用户信息，UI 不会立即更新，需要等到下次通话才能看到变化**。
> 

``` javascript
try {
  await TUICallKitAPI.setSelfInfo({ nickName: "xxx", avatar: "" });
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the setSelfInfo API. Reason: ${error}`;
}
```

参数如下表所示：
| **参数** | **类型** | **是否必填** | **含义** |
| --- | --- | --- | --- |
| nickName | String | 是 | 自己的昵称 |
| avatar | String | 是 | 自己头像地址 |

### setCallingBell
- 自定义用户的来电铃音。

- 仅限传入本地 MP3 格式的文件地址，需要确保该文件目录是应用可以访问的。

- 使用 import 方式引入铃声文件。

- 如需恢复默认铃声，`filePath` 传空即可。

   ``` javascript
   try {
     await TUICallKitAPI.setCallingBell(filePath?: string)
   } catch (error: any) {
     console.error(`[TUICallKit] Failed to call the setCallingBell API. Reason: ${error}`);
   }
   ```

   参数如下表所示：

| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| filePath | String | 是 | 铃声文件地址 |

### enableFloatWindow

开启/关闭悬浮窗功能。默认为 false，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。
``` javascript
try {
  await TUICallKitAPI.enableFloatWindow(enable: Boolean)
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableFloatWindow API. Reason: ${error}`);
}
```

### enableMuteMode

开启/关闭来电铃声。开启后，收到通话请求时，不会播放来电铃声。
``` javascript
try {
  await TUICallKitAPI.enableMuteMode(enable: boolean)
} catch (error: any) {
  console.error(`[TUICallKit] Failed to call the enableMuteMode API. Reason: ${error}`);
}
```

参数如下表所示：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| enable | Boolean | 是 | 开启/关闭来电铃声。默认 false。 |

### enableVirtualBackground

开启/关闭模糊背景功能。如果想设置图片背景模糊参见 [Web](https://cloud.tencent.com/document/product/647/106071)。通过调用接口，您可以在 UI 上显示模糊背景的功能按钮，点击按钮可直接启用模糊背景功能。
``` javascript
import { TUICallKitAPI } from "@trtc/calls-uikit-react";
const enable = true;
TUICallKitAPI.enableVirtualBackground(enable);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| enable | boolean | 是 | - enable = true 显示模糊背景按钮 - enable = false 不显示模糊背景按钮 |

### destroyed

销毁 TUICallKit 实例。

该方法不会自动退出`tim`，需要手动退出`tim.logout();`。
``` javascript
try {
  await TUICallKitAPI.destroyed();
} catch (error: any) {
  alert(`[TUICallKit] Failed to call the destroyed API. Reason: ${error}`);
}
```

### hideFeatureButton

隐藏功能按钮，目前仅支持 摄像头，麦克风和切换前后置按钮。

【Vue】
``` javascript
import { TUICallKitAPI, FeatureButton } from "@trtc/calls-uikit-vue";

TUICallKitAPI.hideFeatureButton(FeatureButton.Camera);
```

【React】
``` javascript
import { TUICallKitAPI, FeatureButton } from "@trtc/calls-uikit-react";

TUICallKitAPI.hideFeatureButton(FeatureButton.Camera);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| buttonName | [FeatureButton](https://cloud.tencent.com/document/product/647/81015#FeatureButton) | 是 | 按钮名称 |

### setLocalViewBackgroundImage

设置本地用户通话背景。
``` typescript
TUICallKitAPI.setLocalViewBackgroundImage('');
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| url | string | 是 | 图片地址（支持本地路径和网络地址） |

### setRemoteViewBackgroundImage

设置远端用户通话背景。
``` javascript
const userId = 'xxx';
const url = '';
TUICallKitAPI.setRemoteViewBackgroundImage(userId, url);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| userId | string | 是 | 远端用户 userId，设置为 '*' 表示对所有远端用户生效 |
| url | string | 是 | 图片地址（支持本地路径和网络地址） |

### setLayoutMode

设置通话界面布局模式。

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

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| layoutMode | [LayoutMode](https://cloud.tencent.com/document/product/647/81015#LayoutMode) | 是 | 用户流的布局模式 |

### setCameraDefaultState

设置摄像头是否默认打开。
``` javascript
TUICallKitAPI.setCameraDefaultState(true);
```

参数列表：
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| isOpen | boolean | 是 | 是否开启摄像头 |

### getTUICallEngineInstance

获取 TUICallEngine 实例。
``` javascript
TUICallKitAPI.getTUICallEngineInstance();
```

## TUICallKit 类型定义

### videoDisplayMode

画面显示模式 `videoDisplayMode` 属性有三个值：
- `VideoDisplayMode.CONTAIN`

- `VideoDisplayMode.COVER`

- `VideoDisplayMode.FILL`，默认值是`VideoDisplayMode.COVER`。

| 属性 | 值 | 描述 |
| --- | --- | --- |
| videoDisplayMode | VideoDisplayMode.CONTAIN | - 优先保证视频内容全部显示。 - 视频尺寸等比缩放，直至视频窗口的一边与视窗边框对齐。 - 如果视频尺寸与显示视窗尺寸不一致，在保持长宽比的前提下，将视频进行缩放后填满视窗，缩放后的视频四周会有一圈黑边。 |
| VideoDisplayMode.COVER | - 优先保证视窗被填满。 - 视频尺寸等比缩放，直至整个视窗被视频填满。 - 如果视频长宽与显示窗口不同，则视频流会按照显示视窗的比例进行周边裁剪或图像拉伸后填满视窗。 |  |
| VideoDisplayMode.FILL | - 保证视窗被填满的同时保证视频内容全部显示，但是不保证视频尺寸比例不变。 - 视频的宽高会被拉伸至和视窗尺寸一致。 |  |

### videoResolution

分辨率 `videoResolution` 有三个值：
- `VideoResolution.RESOLUTION_480P`

- `VideoResolution.RESOLUTION_720P`

- `VideoResolution.RESOLUTION_1080P`，默认值是`VideoResolution.RESOLUTION_480P`。

   **分辨率说明：**

| 视频 Profile | 分辨率（宽 × 高） | 帧率（fps） | 码率（kbps） |
| --- | --- | --- | --- |
| 480p | 640 × 480 | 15 | 900 |
| 720p | 1280 × 720 | 15 | 1500 |
| 1080p | 1920 × 1080 | 15 | 2000 |

   **常见问题：**

- iOS 13&14不支持编码高于 720p 的视频，建议在这两个系统版本限制最高采集 720P。参见 [iOS Safari 已知问题 case 12](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-02-info-webrtc-issues.html#h2-4)。

- Firefox 不支持自定义视频帧率（默认为 30fps）。

- 受系统性能占用，摄像头采集能力和浏览器限制等因素的影响，视频分辨率、帧率、码率的实际值不一定能够完全匹配设定值，在这种情况下，浏览器会自动调整 Profile 尽可能匹配设定值。

### STATUS
| 属性值 | 描述 |
| --- | --- |
| STATUS.IDLE | 闲置状态 |
| STATUS.BE_INVITED | 收到通话邀请 |
| STATUS.DIALING_C2C | 正在 1v1 呼叫 |
| STATUS.DIALING_GROUP | 正在群组呼叫 |
| STATUS.CALLING_C2C_AUDIO | 正在 1v1 语音通话 |
| STATUS.CALLING_C2C_VIDEO | 正在 1v1 视频通话 |
| STATUS.CALLING_GROUP_AUDIO | 正在群组语音通话 |
| STATUS.CALLING_GROUP_VIDEO | 正在群组视频通话 |

### **CallMediaType**
| CallMediaType 类型 | 描述 |
| --- | --- |
| CallMediaType.AUDIO | 语音通话 |
| CallMediaType.VIDEO | 视频通话 |

### **offlinePushInfo**
| 参数 | 类型 | 是否必填 | 含义 |
| --- | --- | --- | --- |
| offlinePushInfo.title | String | 否 | 离线推送标题（选填） |
| offlinePushInfo.description | String | 否 | 离线推送内容（选填） |
| offlinePushInfo.androidOPPOChannelID | String | 否 | 离线推送设置 OPPO 手机 8.0 系统及以上的渠道 ID（选填） |
| offlinePushInfo.extension | String | 否 | 离线推送透传内容，可以用于设置 Android [Notification 模式](https://cloud.tencent.com/document/product/647/81015#Android Notification) 和 [VoIP 模式](https://cloud.tencent.com/document/product/647/81015#Android VoIP)。**默认：**Notification 模式，会是系统的通知；VoIP 模式是需要传字段。 |
| offlinePushInfo.ignoreIOSBadge | Boolean | 否 | 离线推送忽略 badge 计数（仅对 iOS 生效）， 如果设置为 true，在 iOS 接收端，这条消息不会使 App 的应用图标未读计数增加。 |
| offlinePushInfo.iOSSound | String | 否 | 离线推送声音设置（仅对 iOS 生效）。 |
| offlinePushInfo.androidSound | String | 否 | 离线推送声音设置（仅对 Android 生效）。 |
| offlinePushInfo.androidVIVOClassification | Number | 否 | VIVO 推送消息分类。 |
| offlinePushInfo.androidXiaoMiChannelID | String | 否 | 小米手机 8.0 系统及以上的渠道 ID。 |
| offlinePushInfo.androidFCMChannelID | String | 否 | FCM 通道手机 8.0 系统及以上的渠道 ID。 |
| offlinePushInfo.androidHuaWeiCategory | String | 否 | 华为推送消息分类。 |
| offlinePushInfo.isDisablePush | Boolean | 否 | 是否关闭推送（默认开启推送）。 |
| offlinePushInfo.iOSPushType | Number | 否 | iOS 离线推送类型。0-普通推送；1-VoIP推送。默认：0。 |

#### Android Notification 模式
``` javascript
const extension = {
  timPushFeatures: {        
    fcmPushType: 0, // 0, VoIP; 1, notification
  }
};
offlinePushInfo.extension = JSON.stringify(extension);
```

#### Android VoIP 模式
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
| FeatureButton 类型 | 描述 |
| --- | --- |
| FeatureButton.Camera | 摄像头按钮 |
| FeatureButton.Microphone | 麦克风按钮 |
| FeatureButton.SwitchCamera | 切换前后置摄像头按钮 |
| FeatureButton.InviteUser | 邀请他人按钮 |

### LayoutMode
| LayoutMode 类型 | 描述 |
| --- | --- |
| LayoutMode.LocalInLargeView | 本地用户在大窗显示 |
| LayoutMode.RemoteInLargeView | 远端用户在大窗显示 |

---

## Platform: Android SDK

## TUICallObserver API 简介

TUICallObserver 是 TUICallEngine 对应的回调事件类，您可以通过此回调，来监听自己感兴趣的回调事件。

## 回调事件概览
| API | 描述 |
| --- | --- |
| [onError](https://cloud.tencent.com/document/product/647/78751#onError) | 通话过程中错误回调 |
| [onCallReceived](https://cloud.tencent.com/document/product/647/78751#onCallReceived) | 通话请求的回调 |
| [onCallBegin](https://cloud.tencent.com/document/product/647/78751#onCallBegin) | 通话接通的回调 |
| [onCallEnd](https://cloud.tencent.com/document/product/647/78751#onCallEnd) | 通话结束的回调 |
| [onCallNotConnected](https://cloud.tencent.com/document/product/647/78751#onCallNotConnected) | 通话未接通的回调 |
| [onUserReject](https://cloud.tencent.com/document/product/647/78751#onUserReject) | xxxx 用户拒绝通话的回调 |
| [onUserNoResponse](https://cloud.tencent.com/document/product/647/78751#onUserNoResponse) | xxxx 用户不响应的回调 |
| [onUserLineBusy](https://cloud.tencent.com/document/product/647/78751#onUserLineBusy) | xxxx 用户忙线的回调 |
| [onUserInviting](https://cloud.tencent.com/document/product/647/78751#onUserInviting) | xxxx 用户被追加邀请加入通话时的回调 |
| [onUserJoin](https://cloud.tencent.com/document/product/647/78751#onUserJoin) | xxxx 用户加入通话的回调 |
| [onUserLeave](https://cloud.tencent.com/document/product/647/78751#onUserLeave) | xxxx 用户离开通话的回调 |
| [onUserVideoAvailable](https://cloud.tencent.com/document/product/647/78751#onUserVideoAvailable) | xxxx 用户是否有视频流的回调 |
| [onUserAudioAvailable](https://cloud.tencent.com/document/product/647/78751#onUserAudioAvailable) | xxxx 用户是否有音频流的回调 |
| [onUserVoiceVolumeChanged](https://cloud.tencent.com/document/product/647/78751#onUserVoiceVolumeChanged) | 所有用户音量大小的反馈回调 |
| [onUserNetworkQualityChanged](https://cloud.tencent.com/document/product/647/78751#onUserNetworkQualityChanged) | 所有用户网络质量的反馈回调 |
| [onKickedOffline](https://cloud.tencent.com/document/product/647/78751#onKickedOffline) | 当前用户被踢下线 |
| [onUserSigExpired](https://cloud.tencent.com/document/product/647/78751#onUserSigExpired) | 在线时票据过期 |

## 回调事件详情

### onError

错误回调。

> **说明：**
> 

> SDK 不可恢复的错误，一定要监听，并分情况给用户适当的界面提示。
> 

``` java
void onError(int code, String message);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| code | int | 错误码 |
| message | String | 错误信息 |

### onCallReceived

收到一个新的来电请求回调，被叫会收到，您可以通过监听这个事件，来决定是否显示通话接听界面。
``` java
void onCallReceived(String callId, String callerId, List<String> calleeIdList, 
                    TUICallDefine.MediaType mediaType, TUICallDefine.CallObserverExtraInfo info);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| callerId | String | 主叫 ID（邀请方） |
| calleeIdList | List<String> | 被叫 ID 列表（被邀请方） |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| info | [TUICallDefine.CallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90338#CallObserverExtraInfo) | 其他信息 |

### onCallBegin

表示通话接通，主叫和被叫都可以收到，您可以通过监听这个事件来开启云端录制、内容审核等流程。
``` java
void onCallBegin(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallObserverExtraInfo info);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| info | [TUICallDefine.CallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90338#CallObserverExtraInfo) | 其他信息 |

### onCallEnd

表示通话挂断，主叫和被叫都可以收到，您可以通过监听这个事件来显示通话时长、通话类型等信息，或者来停止云端的录制流程。
``` java
void onCallEnd(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallEndReason reason, 
               String userId, long totalTime, TUICallDefine.CallObserverExtraInfo info);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的音视频房间 ID |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| reason | [TUICallDefine.CallEndReason](https://cloud.tencent.com/document/product/647/90338#CallEndReason) | 通话结束原因 |
| userId | String | 结束通话的用户 ID |
| totalTime | long | 此次通话的时长，单位：秒 |
| info | [TUICallDefine.CallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90338#CallObserverExtraInfo) | 其他信息 |

> **注意：**
> 

> 客户端的事件一般都会随着杀进程等异常事件丢失掉，如果您需要通过监听通话时长来完成计费等逻辑，建议可以使用 REST API 来完成这类流程。
> 

### onCallNotConnected

表示此次通话主叫取消、被叫超时、拒接等，涉及多个场景，您可以通过监听这个事件来实现类似未接来电、重置 UI 状态等显示逻辑。
- 主叫取消：主叫收到该回调（userId 为自己）；被叫收到该回调（userId 为**主叫的 ID）。**

- 被叫超时：主叫会同时收到 [onUserNoResponse](https://cloud.tencent.com/document/product/647/78751#onUserNoResponse) 和 onCallNotConnected 回调（userId 是自己的 ID）；被叫收到 onCallNotConnected 回调（userId 是自己的 ID）。

- 被叫拒接：主叫会同时收到 [onUserReject](https://cloud.tencent.com/document/product/647/78751#onUserReject) 和 onCallNotConnected 回调（userId 是自己的 ID）；被叫收到 onCallNotConnected 回调（userId 是自己的 ID）。

- 被叫忙线：主叫会同时收到 [onUserLineBusy](https://cloud.tencent.com/document/product/647/78751#onUserLineBusy) 和 onCallNotConnected 回调（userId 是自己的 ID）。

- 异常中断：被叫接收通话失败，收到该回调（userId 是自己的 ID）。

   ``` java
   void onCallNotConnected(String callId, TUICallDefine.MediaType mediaType, TUICallDefine.CallEndReason reason, 
                           String userId, TUICallDefine.CallObserverExtraInfo info)
   ```

   参数如下表所示：

| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的音视频房间 ID |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| reason | [TUICallDefine.CallEndReason](https://cloud.tencent.com/document/product/647/90338#CallEndReason) | 通话未连接原因 |
| userId | String | 导致通话未连接的用户 ID |
| info | [TUICallDefine.CallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90338#CallObserverExtraInfo) | 其他信息 |

### onUserReject

通话被拒绝的回调，在1v1 通话中，只有主叫方会收到拒绝回调，在群组通话中，所有被邀请者都可以收到该回调。
``` java
void onUserReject(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 拒绝用户的 ID |

### onUserNoResponse

对方无回应的回调。
``` java
void onUserNoResponse(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 无响应用户的 ID |

### onUserInviting

用户被追加邀请加入通话时的回调。
``` java
void onUserInviting(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 被追加邀请用户的 ID |

### onUserLineBusy

通话忙线回调。
``` java
void onUserLineBusy(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 忙线用户的 ID |

### onUserJoin

有用户进入此次通话的回调。
``` java
void onUserJoin(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 加入当前通话的用户 ID |

### onUserLeave

有用户离开此次通话的回调。
``` java
void onUserLeave(String userId);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 离开当前通话的用户 ID |

### onUserVideoAvailable

用户是否开启视频上行回调。
``` java
void onUserVideoAvailable(String userId, boolean isVideoAvailable);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 通话用户 ID |
| isVideoAvailable | boolean | 用户视频是否可用 |

### onUserAudioAvailable

用户是否开启音频上行回调。
``` java
void onUserAudioAvailable(String userId, boolean isAudioAvailable);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 用户 ID |
| isAudioAvailable | boolean | 用户音频是否可用 |

### onUserVoiceVolumeChanged

用户通话音量的回调。
``` java
void onUserVoiceVolumeChanged(Map<String, Integer> volumeMap);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| volumeMap | Map<String, Integer> | 音量表，根据每个 userId 可以获取对应用户的音量大小，音量最小值为0，音量最大值为100 |

### onUserNetworkQualityChanged

用户网络质量的回调。
``` java
void onUserNetworkQualityChanged(List<TUICallDefine.NetworkQualityInfo> networkQualityList);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| networkQualityList | List | 网络状态，根据每个 userId 可以获取对应用户当前的网络质量 |

### onKickedOffline

当前用户被踢下线：此时可以 UI 提示用户，并再次重新调用初始化。
``` java
void onKickedOffline();
```

### onUserSigExpired

在线时票据过期：此时您需要生成新的 userSig，并再次重新调用初始化。
``` java
void onUserSigExpired();
```

## 废弃回调

### onCallCancelled

表示此次通话主叫取消、被叫超时、拒接等，涉及多个场景，您可以通过监听这个事件来实现类似未接来电、重置 UI 状态等显示逻辑。
- 主叫取消：主叫收到该回调（userId 为自己）；被叫收到该回调（userId 为**主叫的 ID）。**

- 被叫超时：主叫会同时收到 [onUserNoResponse](https://cloud.tencent.com/document/product/647/78751#onUserNoResponse) 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫拒接：主叫会同时收到 [onUserReject](https://cloud.tencent.com/document/product/647/78751#onUserReject) 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫忙线：主叫会同时收到 [onUserLineBusy](https://cloud.tencent.com/document/product/647/78751#onUserLineBusy) 和 onCallCancelled 回调（userId 是自己的 ID）。

- 异常中断：被叫接收通话失败，收到该回调（userId 是自己的 ID）。

   ``` java
   void onCallCancelled(String userId);
   ```

   参数如下表所示：

| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 用户的 ID |

### onCallMediaTypeChanged

表示通话的媒体类型发生变化。
``` java
void onCallMediaTypeChanged(TUICallDefine.MediaType oldCallMediaType,TUICallDefine.MediaType newCallMediaType);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| oldCallMediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 旧的通话类型 |
| newCallMediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 新的通话类型 |

---

## Platform: Android SDK

## TUICallKit API 简介

TUICallKit API 是音视频通话组件的**含 UI 接口**，使用 TUICallKit API，您可以通过简单接口快速实现一个类微信的音视频通话场景，更详细的接入步骤，详情请参见 [快速接入TUICallKit](https://cloud.tencent.com/document/product/647/78729)。

## API 概览
| API | 描述 |
| --- | --- |
| [createInstance](https://cloud.tencent.com/document/product/647/78750#createInstance) | 创建 TUICallKit 实例（单例模式） |
| [setSelfInfo](https://cloud.tencent.com/document/product/647/78750#setSelfInfo) | 设置用户的头像、昵称 |
| [calls](https://cloud.tencent.com/document/product/647/78750#calls) | 发起单人或多人通话 |
| [join](https://cloud.tencent.com/document/product/647/78750#join) | 主动加入通话 |
| [setCallingBell](https://cloud.tencent.com/document/product/647/78750#setCallingBell) | 设置自定义来电铃音 |
| [enableMuteMode](https://cloud.tencent.com/document/product/647/78750#enableMuteMode) | 开启/关闭静音模式 |
| [enableFloatWindow](https://cloud.tencent.com/document/product/647/78750#enableFloatWindow) | 开启/关闭悬浮窗功能 |
| [enableIncomingBanner](https://cloud.tencent.com/document/product/647/78750#enableIncomingBanner) | 开启/关闭来电横幅显示 |
| [setScreenOrientation](https://cloud.tencent.com/document/product/647/78750#setScreenOrientation) | 设置通话界面显示方向 |
| [enableVirtualBackground](https://cloud.tencent.com/document/product/647/78750#enableVirtualBackground) | 设置模糊背景 |

## API 详情

### createInstance

创建 TUICallKit 的单例。

【Kotlin】
``` java
fun createInstance(context: Context): TUICallKit
```

【Java】
``` java
TUICallKit createInstance(Context context)
```

### setSelfInfo

设置用户昵称、头像。用户昵称不能超过500字节，用户头像必须是 URL 格式。

【Kotlin】
``` java
fun setSelfInfo(nickname: String?, avatar: String?, callback: TUICommonDefine.Callback?)
```

【Java】
``` java
void setSelfInfo(String nickname, String avatar, TUICommonDefine.Callback callback)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| nickname | String | 目标用户的昵称 |
| avatar | String | 目标用户的头像 |

### calls

发起单人或多人通话。

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

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userIdList | List<String> | 目标用户的 userId 列表 |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallDefine.CallParams](https://cloud.tencent.com/document/product/647/90338#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |

### join

主动加入通话。

【Kotlin】
``` java
 fun join(callId: String?, callback: TUICommonDefine.Callback?) 
```

【Java】
``` java
void join(String callId, TUICommonDefine.Callback callback)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |

### setCallingBell

设置自定义来电铃音。
这里仅限传入本地文件地址，需要确保该文件目录是应用可以访问的。
- 铃声设置后与设备绑定，更换用户，铃声依旧会生效。

- 如需恢复默认铃声，`filePath`传空即可。

   

【Kotlin】
``` java
fun setCallingBell(filePath: String?)
```

【Java】
``` java
void setCallingBell(String filePath);
```

### enableMuteMode

开启/关闭静音模式。

开启后，收到通话请求，不会播放来电铃声。

【Kotlin】
``` java
fun enableMuteMode(enable: Boolean)
```

【Java】
``` java
void enableMuteMode(boolean enable);
```

### enableFloatWindow

开启/关闭悬浮窗功能。

默认为`false`，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。

【Kotlin】
``` java
fun enableFloatWindow(enable: Boolean)
```

【Java】
``` java
void enableFloatWindow(boolean enable);
```

### enableIncomingBanner

开启/关闭来电横幅显示。

默认为`false`，被叫端收到邀请后默认弹出全屏通话等待界面。开启后先展示一个横幅，然后根据需要拉起全屏通话界面。

【Kotlin】
``` java
fun enableIncomingBanner(enable: Boolean)
```

【Java】
``` java
void enableIncomingBanner(boolean enable);
```

### setScreenOrientation

设置通话界面显示方向。

默认为竖屏，orientation：0-Portrait、1-LandScape、2-Auto。

【Kotlin】
``` java
fun setScreenOrientation(orientation: Int) 
```

【Java】
``` java
void setScreenOrientation(int orientation);
```

### enableVirtualBackground

设置模糊背景。

默认值为：false。
``` java
fun enableVirtualBackground(enable: Boolean)
```

## 废弃接口

### call

拨打电话（1v1通话）。**注意：v2.9+版本废弃，建议使用 calls 接口。**

【Kotlin】
``` java
fun call(userId: String, mediaType: TUICallDefine.MediaType)
```

【Java】
``` java
 void call(String userId, TUICallDefine.MediaType mediaType)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 目标用户的 userId |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

### call

拨打电话（1v1通话），支持自定义房间号、通话邀请超时时间，离线推送内容等。**注意：v2.9+版本废弃，建议使用 calls 接口。**

【Kotlin】
``` java
fun call(
    userId: String, mediaType: TUICallDefine.MediaType,
    params: CallParams?, callback: TUICommonDefine.Callback?
)
```

【Java】
``` java
 void call(String userId, TUICallDefine.MediaType mediaType, 
           TUICallDefine.CallParams params, TUICommonDefine.Callback callback)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 目标用户的 userId |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallDefine.CallParams](https://cloud.tencent.com/document/product/647/90338#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |

### groupCall

发起群组通话。**注意：v2.9+版本废弃，建议使用 calls 接口。**

> **注意：**
> 

> 使用群组通话前需要创建 IM 群组，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Kotlin】
``` java
fun groupCall(groupId: String, userIdList: List<String?>?, mediaType: TUICallDefine.MediaType)
```

【Java】
``` java
void groupCall(String groupId, List<String> userIdList, TUICallDefine.MediaType mediaType);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| groupId | String | 此次群组通话的群 ID |
| userIdList | List | 目标用户的 userId 列表 |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

### groupCall

发起群组通话，支持自定义房间号、通话邀请超时时间，离线推送内容等。**注意：v2.9+版本废弃，建议使用 calls 接口。**

> **注意：**
> 

> 使用群组通话前需要创建 IM 群组，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Kotlin】
``` java
fun groupCall(
    groupId: String, userIdList: List<String?>?, mediaType: TUICallDefine.MediaType, 
    params: CallParams?, callback: TUICommonDefine.Callback?
)
```

【Java】
``` java
void groupCall(String groupId, List<String> userIdList, TUICallDefine.MediaType mediaType, 
               TUICallDefine.CallParams params, TUICommonDefine.Callback callback);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| groupId | String | 此次群组通话的群 ID |
| userIdList | List | 目标用户的 userId 列表 |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallDefine.CallParams](https://cloud.tencent.com/document/product/647/90338#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |

### joinInGroupCall

加入群组中已有的音视频通话。**注意：v2.9+版本废弃，建议使用 join 接口。**

> **注意：**
> 

> 加入群组中已有的音视频通话前，需要提前创建或加入 IM 群组，并且群组中已有用户在通话中，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Kotlin】
``` java
fun joinInGroupCall(roomId: RoomId?, groupId: String?, mediaType: TUICallDefine.MediaType?) 
```

【Java】
``` java
void joinInGroupCall(TUICommonDefine.RoomId roomId, String groupId, TUICallDefine.MediaType callMediaType);
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| roomId | [TUICommonDefine.RoomId](https://cloud.tencent.com/document/product/647/90338#RoomId) | 此次通话的音视频房间 ID |
| groupId | String | 此次群组通话的群 ID |
| mediaType | [TUICallDefine.MediaType](https://cloud.tencent.com/document/product/647/90338#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

---

## Platform: iOS SDK

## TUICallObserver API 简介

TUICallObserver 是 TUICallEngine 对应的回调事件类，您可以通过此回调，来监听自己感兴趣的回调事件。

## 回调事件概览
| API | 描述 |
| --- | --- |
| [onError](https://cloud.tencent.com/document/product/647/78755#onError) | 通话过程中错误回调 |
| [onCallReceived](https://cloud.tencent.com/document/product/647/78755#onCallReceived) | 通话请求的回调 |
| [onCallCancelled](https://cloud.tencent.com/document/product/647/78755#onCallCancelled) | 通话取消的回调 |
| [onCallEnd](https://cloud.tencent.com/document/product/647/78755#onCallEnd) | 通话结束的回调 |
| [onCallNotConnected](https://cloud.tencent.com/document/product/647/78755#onCallNotConnected) | 通话未接通的回调 |
| [onUserReject](https://cloud.tencent.com/document/product/647/78755#onUserReject) | xxxx 用户拒绝通话的回调 |
| [onUserNoResponse](https://cloud.tencent.com/document/product/647/78755#onUserNoResponse) | xxxx 用户不响应的回调 |
| [onUserLineBusy](https://cloud.tencent.com/document/product/647/78755#onUserLineBusy) | xxxx 用户忙线的回调 |
| [onUserInviting](https://cloud.tencent.com/document/product/647/78755#onUserInviting) | xxxx 用户被追加邀请加入通话时的回调 |
| [onUserJoin](https://cloud.tencent.com/document/product/647/78755#onUserJoin) | xxxx 用户加入通话的回调 |
| [onUserLeave](https://cloud.tencent.com/document/product/647/78755#onUserLeave) | xxxx 用户离开通话的回调 |
| [onUserVideoAvailable](https://cloud.tencent.com/document/product/647/78755#onUserVideoAvailable) | xxxx 用户是否有视频流的回调 |
| [onUserAudioAvailable](https://cloud.tencent.com/document/product/647/78755#onUserAudioAvailable) | xxxx 用户是否有音频流的回调 |
| [onUserVoiceVolumeChanged](https://cloud.tencent.com/document/product/647/78755#onUserVoiceVolumeChanged) | 所有用户音量大小的反馈回调 |
| [onUserNetworkQualityChanged](https://cloud.tencent.com/document/product/647/78755#onUserNetworkQualityChanged) | 所有用户网络质量的反馈回调 |
| [onKickedOffline](https://cloud.tencent.com/document/product/647/78755#onKickedOffline) | 当前用户被踢下线 |
| [onUserSigExpired](https://cloud.tencent.com/document/product/647/78755#onUserSigExpired) | 在线时票据过期 |

## 回调事件详情

### onError

错误回调。

> **说明：**
> 

> SDK 不可恢复的错误，一定要监听，并分情况给用户适当的界面提示。
> 

``` objectivec
- (void)onError:(int)code message:(NSString * _Nullable)message;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| code | int | 错误码 |
| message | NSString | 错误信息 |

### onCallReceived

收到一个新的来电请求回调，被叫会收到，您可以通过监听这个事件，来决定是否显示通话接听界面。
``` objectivec
- (void)onCallReceived:(NSString *)callId callerId:(NSString *)callerId calleeIdList:(NSArray<NSString *> *)calleeIdList mediaType:(TUICallMediaType)mediaType info:(TUICallObserverExtraInfo *)info;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | NSString | 此次通话的唯一 ID |
| callerId | NSString | 主叫 ID（邀请方） |
| calleeIdList | NSArray | 被叫 ID 列表（被邀请方） |
| mediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| info | [TUICallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90446#ObserverExtraInfo) | 其他信息 |

### onCallBegin

表示通话接通，主叫和被叫都可以收到，您可以通过监听这个事件来开启云端录制、内容审核等流程。
``` objectivec
- (void)onCallBegin:(NSString *)callId mediaType:(TUICallMediaType)mediaType info:(TUICallObserverExtraInfo *)info;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | NSString | 此次通话的唯一 ID |
| mediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| info | [TUICallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90446#ObserverExtraInfo) | 其他信息 |

### onCallEnd

表示通话挂断，主叫和被叫都可以收到，您可以通过监听这个事件来显示通话时长、通话类型等信息，或者来停止云端的录制流程。
``` objectivec
- (void)onCallEnd:(NSString *)callId mediaType:(TUICallMediaType)mediaType reason:(TUICallEndReason)reason userId:(NSString *)userId totalTime:(float)totalTime info:(TUICallObserverExtraInfo *)info;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | NSString | 此次通话的音视频房间 ID |
| mediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| reason | [TUICallEndReason](https://cloud.tencent.com/document/product/647/90446#EndReason) | 通话结束原因 |
| userId | NSString | 结束通话的用户 ID |
| totalTime | float | 此次通话的时长，单位：秒 |
| info | [TUICallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90446#ObserverExtraInfo) | 其他信息 |

> **注意：**
> 

> 客户端的事件一般都会随着杀进程等异常事件丢失掉，如果您需要通过监听通话时长来完成计费等逻辑，建议可以使用 REST API 来完成这类流程。
> 

### onCallNotConnected

表示此次通话主叫取消、被叫超时、拒接等，涉及多个场景，您可以通过监听这个事件来实现类似未接来电、重置 UI 状态等显示逻辑。
- 主叫取消：主叫收到该回调（userId 为自己）；被叫收到该回调（userId 为**主叫的 ID）。**

- 被叫超时：主叫会同时收到 [onUserNoResponse](https://cloud.tencent.com/document/product/647/78755#onUserNoResponse) 和 onCallNotConnected 回调（userId 是自己的 ID）；被叫收到 onCallNotConnected 回调（userId 是自己的 ID）。

- 被叫拒接：主叫会同时收到 [onUserReject](https://cloud.tencent.com/document/product/647/78755#onUserReject) 和 onCallNotConnected 回调（userId 是自己的 ID）；被叫收到 onCallNotConnected 回调（userId 是自己的 ID）。

- 被叫忙线：主叫会同时收到 [onUserLineBusy](https://cloud.tencent.com/document/product/647/78755#onUserLineBusy) 和 onCallNotConnected 回调（userId 是自己的 ID）。

- 异常中断：被叫接收通话失败，收到该回调（userId 是自己的 ID）。

   ``` objectivec
   - (void)onCallNotConnected:(NSString *)callId mediaType:(TUICallMediaType)mediaType reason:(TUICallEndReason)reaso userId:(NSString *)userId info:(TUICallObserverExtraInfo *)info
   ```

   参数如下表所示：

| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | NSString | 此次通话的音视频房间 ID |
| mediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，视频通话、语音通话 |
| reason | [TUICallEndReason](https://cloud.tencent.com/document/product/647/90446#EndReason) | 通话未连接原因 |
| userId | NSString | 导致通话未连接的用户 ID |
| info | [TUICallObserverExtraInfo](https://cloud.tencent.com/document/product/647/90446#ObserverExtraInfo) | 其他信息 |

### onUserReject

通话被拒绝的回调，在1v1 通话中，只有主叫方会收到拒绝回调，在群组通话中，所有被邀请者都可以收到该回调。
``` objectivec
- (void)onUserReject:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 拒绝用户的 ID |

### onUserNoResponse

对方无回应的回调。
``` objectivec
- (void)onUserNoResponse:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 无响应用户的 ID |

### onUserLineBusy

通话忙线回调。
``` objectivec
- (void)onUserLineBusy:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 忙线用户的 ID |

### onUserInviting

用户被追加邀请加入通话时的回调。
``` objectivec
- (void)onUserInviting:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 被追加邀请用户的 ID |

### onUserJoin

有用户进入此次通话的回调。
``` objectivec
- (void)onUserJoin:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 加入当前通话的用户 ID |

### onUserLeave

有用户离开此次通话的回调。
``` objectivec
- (void)onUserLeave:(NSString *)userId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 离开当前通话的用户 ID |

### onUserVideoAvailable

用户是否开启视频上行回调。
``` objectivec
- (void)onUserVideoAvailable:(NSString *)userId isVideoAvailable:(BOOL)isVideoAvailable;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 通话用户 ID |
| isVideoAvailable | BOOL | 用户视频是否可用 |

### onUserAudioAvailable

用户是否开启音频上行回调。
``` objectivec
- (void)onUserAudioAvailable:(NSString *)userId isAudioAvailable:(BOOL)isAudioAvailable;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 通话用户 ID |
| isAudioAvailable | BOOL | 用户音频是否可用 |

### onUserVoiceVolumeChanged

用户通话音量的回调。
``` objectivec
- (void)onUserVoiceVolumeChanged:(NSDictionary <NSString *, NSNumber *> *)volumeMap;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| volumeMap | NSDictionary | 音量表，根据每个 userId 可以获取对应的音量大小，音量最小值为0，音量最大值为100 |

### onUserNetworkQualityChanged

用户网络质量的回调。
``` objectivec
- (void)onUserNetworkQualityChanged:(NSArray<TUINetworkQualityInfo *> *)networkQualityList;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| networkQualityList | NSArray | 网络状态，根据每个 userId 可以获取对应用户当前的网络质量 |

### onKickedOffline

当前用户被踢下线：此时可以 UI 提示用户，并重新调用初始化。
``` objectivec
- (void)onKickedOffline;
```

### onUserSigExpired

在线时票据过期：此时您需要生成新的 userSig，并重新调用初始化。
``` objectivec
- (void)onUserSigExpired;
```

## 废弃回调

### onCallCancelled

表示此次通话主叫取消、被叫超时、拒接等，涉及多个场景，您可以通过监听这个事件来实现类似未接来电、重置 UI 状态等显示逻辑。
- 主叫取消：主叫收到该回调（userId 为自己）；被叫收到该回调（userId 为**主叫的 ID）。**

- 被叫超时：主叫会同时收到 [onUserNoResponse](https://cloud.tencent.com/document/product/647/78755#onUserNoResponse) 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫拒接：主叫会同时收到 [onUserReject](https://cloud.tencent.com/document/product/647/78755#onUserReject) 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫忙线：主叫会同时收到 [onUserLineBusy](https://cloud.tencent.com/document/product/647/78755#onUserLineBusy) 和 onCallCancelled 回调（userId 是自己的 ID）。

- 异常中断：被叫接收通话失败，收到该回调（userId 是自己的 ID）。

   ``` objectivec
   - (void)onCallCancelled:(NSString *)userId;
   ```

   参数如下表所示：

| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 用户的 ID |

### onCallMediaTypeChanged

表示通话的媒体类型发生变化。
``` objectivec
- (void)onCallMediaTypeChanged:(TUICallMediaType)oldCallMediaType newCallMediaType:(TUICallMediaType)newCallMediaType;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| oldCallMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 旧的通话类型 |
| newCallMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 新的通话类型 |

---

## Platform: iOS SDK

## TUICallKit API 简介

TUICallKit API 是音视频通话组件的**含 UI 接口**，使用TUICallKit API，您可以通过简单接口快速实现一个类微信的音视频通话场景，更详细的接入步骤，详情请参见 [快速接入TUICallKit](https://cloud.tencent.com/document/product/647/78730)。

## API 概览
| API | 描述 |
| --- | --- |
| [createInstance](https://cloud.tencent.com/document/product/647/78753#createInstance) | 创建 TUICallKit 实例（单例模式） |
| [setSelfInfo](https://cloud.tencent.com/document/product/647/78753#setSelfInfo) | 设置用户的昵称、头像 |
| [calls](https://cloud.tencent.com/document/product/647/78753#calls) | 发起单人或多人通话 |
| [join](https://cloud.tencent.com/document/product/647/78753#join) | 主动加入通话 |
| [setCallingBell](https://cloud.tencent.com/document/product/647/78753#setCallingBell) | 设置自定义来电铃音 |
| [enableMuteMode](https://cloud.tencent.com/document/product/647/78753#enableMuteMode) | 开启/关闭静音模式 |
| [enableFloatWindow](https://cloud.tencent.com/document/product/647/78753#enableFloatWindow) | 开启/关闭悬浮窗功能 |
| [enableIncomingBanner](https://cloud.tencent.com/document/product/647/78753#enableIncomingBanner) | 开启/关闭来电横幅显示 |

## API 详情

### createInstance

创建 TUICallKit 的单例。

【Objective-C】
``` objectivec
- (instancetype)createInstance;
```

【Swift】
``` swift
public static func createInstance() -> TUICallKit
```

### setSelfInfo

设置用户昵称、头像。用户昵称不能超过500字节，用户头像必须是 URL 格式。

【Objective-C】
``` objectivec
- (void)setSelfInfo:(NSString * _Nullable)nickname avatar:(NSString * _Nullable)avatar succ:(TUICallSucc)succ fail:(TUICallFail)fail;
```

【Swift】
``` swift
public func setSelfInfo(nickname: String, avatar: String, succ:@escaping TUICallSucc, fail: @escaping TUICallFail)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| nickname | NSString | 用户昵称 |
| avatar | NSString | 用户头像（格式为 URL） |
| succ | TUICallSucc | 成功回调 |
| fail | TUICallFail | 失败回调 |

### calls

发起通话。

【Swift】
``` swift
func calls(userIdList: [String], callMediaType: TUICallMediaType, params: TUICallParams,
                      succ: @escaping TUICallSucc, fail: @escaping TUICallFail)

```

【Objective-C】
``` objectivec
- (void)calls:(NSArray<NSString *> *)userIdList callMediaType:(TUICallMediaType)callMediaType params:(TUICallParams *)params succ:(TUICallSucc)succ fail:(TUICallFail)fail; 
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userIdList | NSArray | 目标用户的 userId 列表 |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallParams](https://cloud.tencent.com/document/product/647/90446#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |

### join

主动加入通话。

【Swift】
``` java
func join(callId: String)
```

【Objective-C】
``` java
- (void)join:(NSString *)callId;
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | NSString | 此次通话的唯一 ID |

### setCallingBell

设置自定义来电铃音。
这里仅限传入本地文件地址，需要确保该文件目录是应用可以访问的。
- 铃声设置后与设备绑定，更换用户，铃声依旧会生效。

- 如需恢复默认铃声，`filePath` 传空即可。

   

【Objective-C】
``` objectivec
- (void)setCallingBell:(NSString *)filePath;
```

【Swift】
``` swift
public func setCallingBell(filePath: String)
```

### enableMuteMode

开启后，收到通话请求，不会播放来电铃声。

【Objective-C】
``` objectivec
- (void)enableMuteMode:(BOOL)enable;
```

【Swift】
``` swift
public func enableMuteMode(enable: Bool)
```

### enableFloatWindow

开启/关闭悬浮窗功能。
默认为`false`，通话界面左上角的悬浮窗按钮隐藏，设置为 true 后显示。

【Objective-C】
``` objectivec
- (void)enableFloatWindow:(BOOL)enable;
```

【Swift】
``` swift
public func enableFloatWindow(enable: Bool)
```

### enableIncomingBanner

开启/关闭来电横幅显示。

默认为`false`，被叫端收到邀请后默认弹出全屏通话等待界面，开启后先展示一个横幅，然后根据需要拉起全屏通话界面。
``` swift
public func enableIncomingBanner(enable: Bool)
```

## 废弃接口

### call

拨打电话（1v1通话）。**注意：建议使用 calls 接口。**

【Objective-C】
``` objectivec
- (void)call:(NSString *)userId callMediaType:(TUICallMediaType)callMediaType;
```

【Swift】
``` swift
public func call(userId: String, callMediaType: TUICallMediaType)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 目标用户的 userId |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

### call

拨打电话（1v1通话），支持自定义房间号、通话邀请超时时间，离线推送内容等。**注意：建议使用 calls 接口。**

【Objective-C】
``` objectivec
- (void)call:(NSString *)userId callMediaType:(TUICallMediaType)callMediaType params:(TUICallParams *)params succ:(TUICallSucc __nullable)succ fail:(TUICallFail __nullable)fail;
```

【Swift】
``` swift
public func call(userId: String, callMediaType: TUICallMediaType, params: TUICallParams,
                     succ: @escaping TUICallSucc, fail: @escaping TUICallFail)
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | NSString | 目标用户的 userId |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallParams](https://cloud.tencent.com/document/product/647/90446#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |
| succ | TUICallSucc | 成功回调 |
| fail | TUICallFail | 失败回调 |

### groupCall

发起群组通话。**注意：建议使用 calls 接口。**

> **注意：**
> 

> 使用群组通话前需要创建 IM 群组，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Objective-C】
``` objectivec
- (void)groupCall:(NSString *)groupId userIdList:(NSArray<NSString *> *)userIdList callMediaType:(TUICallMediaType)callMediaType;
```

【Swift】
``` swift
public func groupCall(groupId: String, userIdList: [String], callMediaType: TUICallMediaType)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| groupId | NSString | 此次群组通话的群 ID |
| userIdList | NSArray | 目标用户的 userId 列表 |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

### groupCall

发起群组通话，支持自定义房间号、通话邀请超时时间，离线推送内容等。**注意：建议使用 calls 接口。**

> **注意：**
> 

> 使用群组通话前需要创建 IM 群组，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Objective-C】
``` objectivec
- (void)groupCall:(NSString *)groupId userIdList:(NSArray<NSString *> *)userIdList callMediaType:(TUICallMediaType)callMediaType params:(TUICallParams *)params succ:(TUICallSucc __nullable)succ fail:(TUICallFail __nullable)fail;
```

【Swift】
``` swift
public func groupCall(groupId: String, userIdList: [String], callMediaType: TUICallMediaType, params: TUICallParams, succ: @escaping TUICallSucc, fail: @escaping TUICallFail)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| groupId | NSString | 此次群组通话的群 ID |
| userIdList | NSArray | 目标用户的 userId 列表 |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |
| params | [TUICallParams](https://cloud.tencent.com/document/product/647/90446#CallParams) | 通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |
| succ | TUICallSucc | 成功回调 |
| fail | TUICallFail | 失败回调 |

### joinInGroupCall

加入群组中已有的音视频通话。**注意：建议使用 join 接口。**

> **注意：**
> 

> 加入群组中已有的音视频通话前，需要提前创建或加入 IM 群组，并且群组中已有用户在通话中，如果已经创建，请忽略。
> 

> 群组的创建详见：[IM 群组管理](https://cloud.tencent.com/document/product/269/75394#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84) ，或者您也可以直接使用 [IM TUIKit](https://cloud.tencent.com/document/product/269/37059)，一站式集成聊天、通话等场景。
> 

【Objective-C】
``` objectivec
- (void)joinInGroupCall:(TUIRoomId *)roomId groupId:(NSString *)groupId callMediaType:(TUICallMediaType)callMediaType;
```

【Swift】
``` swift
public func joinInGroupCall(roomId: TUIRoomId, groupId: String, callMediaType: TUICallMediaType)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| roomId | [TUIRoomId](https://cloud.tencent.com/document/product/647/90446#RoomId) | 此次通话的音视频房间 ID |
| groupId | NSString | 此次群组通话的群 ID |
| callMediaType | [TUICallMediaType](https://cloud.tencent.com/document/product/647/90446#MediaType) | 通话的媒体类型，比如视频通话、语音通话 |

---

## Platform: Flutter SDK

## **常用结构**

### TUIResult

调用 API 的返回值
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| code | String | code 为空 "" 表示调用成功，code 不为空 "" 表示调用失败 |
| message | String | 失败原因 |

### TUIRoomId

房间 ID
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| intRoomId | int | 数字房间号 |
| strRoomId | String | 字符串房间号 |

### VideoRenderParams

视频画面的渲染参数
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| fillMode | FillMode | 视频画面填充模式 |
| rotation | Rotation | 视频画面旋转方向 |

### VideoEncoderParams

视频编码参数
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| resolution | Resolution | 视频分辨率 |
| resolutionMode | ResolutionMode | 视频宽高比模式 |

### TUICallParams

通话参数
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| roomId | TUIRoomId | 房间 ID |
| offlinePushInfo | TUIOfflinePushInfo | 厂商离线推送配置信息 |
| timeout | String | 超时时常 |
| userData | String | 用户自定义数据 |
| chatGroupId | String | 群组 ID |

### TUIOfflinePushInfo

厂商离线推送配置信息，详情配置步骤请参考：离线唤醒。
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| title | String | 离线推送展示通知栏标题 |
| desc | String | 离线推送展示通知栏内容 |
| ignoreIOSBadge | bool | 离线推送忽略 badge 计数（仅对 iOS 生效）， 如果设置为 true，在 iOS 接收端，这条消息不会使 APP 的应用图标未读计数增加 |
| iOSSound | String | 离线推送声音设置（仅对 iOS 生效）。 当 sound = IOS_OFFLINE_PUSH_NO_SOUND，表示接收时不会播放声音。 当 sound = IOS_OFFLINE_PUSH_DEFAULT_SOUND，表示接收时播放系统声音。 如果要自定义 iOSSound，需要先把语音文件链接进 Xcode 工程，然后把语音文件名（带后缀）设置给 iOSSound |
| androidSound | String | 离线推送声音设置（仅对 Android 生效, IMSDK 6.1 及以上版本支持）。 只有华为和谷歌手机支持设置声音提示，小米手机设置声音提示，请您参照：[小米自定义铃声](https://dev.mi.com/console/doc/detail?pId=1278%23_3_0#_3_0)。 另外，谷歌手机 FCM 推送在 Android 8.0 及以上系统设置声音提示，必须调用 setAndroidFCMChannelID 设置好 channelID，才能生效 |
| androidOPPOChannelID | String | 离线推送设置 OPPO 手机 8.0 系统及以上的渠道 ID |
| androidVIVOClassification | int | VIVO 推送消息分类 (待废弃接口，VIVO 推送服务于 2023 年 4 月 3 日优化消息分类规则，推荐使用 setAndroidVIVOCategory 设置消息类别)。0：运营消息 1：系统消息，默认取值为 1 |
| androidXiaoMiChannelID | String | 小米手机 8.0 系统及以上的渠道 ID |
| androidFCMChannelID | String | FCM 通道手机 8.0 系统及以上的渠道 ID |
| androidHuaWeiCategory | String | 华为推送消息分类，详见：[华为消息分类标准](https://developer.huawei.com/consumer/cn/doc/development/HMSCore-Guides/message-classification-0000001149358835) |
| isDisablePush | bool | 是否关闭推送（默认开启推送） |
| iOSPushType | TUICallIOSOfflinePushType | iOS 离线推送类型，默认：APNs |

### TUICallRecords

通话记录信息
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| callId | String | 通话记录 ID |
| inviter | String | 邀请者 ID |
| inviteList | List<String> | 被邀请用户 ID 列表 |
| scene | TUICallScene | 通话场景 |
| mediaType | TUICallMediaType | 通话媒体类型 |
| groupId | String | 群组 ID |
| role | TUICallRole | 通话角色 |
| result | TUICallResultType | 通话结果类型 |
| beginTime | int | 开始时间 |
| totalTime | int | 总时间 |

### TUICallRecentCallsFilter

通话记录过滤条件
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| begin | double | 开始时间 |
| end | double | 结束时间 |
| resultType | TUICallResultType | 通话记录类型 |

### CallObserverExtraInfo

回调扩展信息
| 类型 | 类型 | 描述 |
| --- | --- | --- |
| roomId | TUIRoomId | 房间 ID |
| role | TUICallRole | 通话角色 |
| userData | String | 发起通话时自定义扩展字段，详情见 TUICallParams |
| chatGroupId | String | 群组 ID |

## **枚举定义**

### TUICallMediaType

通话类型
| 类型 | 描述 |
| --- | --- |
| none | 未知类型 |
| audio | 语音通话 |
| video | 视频通话 |

### TUICallRole

通话角色
| 类型 | 描述 |
| --- | --- |
| none | 未知类型 |
| caller | 主叫（邀请方） |
| called | 被叫（被邀请方） |

### TUICallStatus

通话状态
| 类型 | 描述 |
| --- | --- |
| none | 未知类型 |
| waiting | 通话等待中 |
| accept | 通话已接听 |

### TUICallScene

通话场景
| 类型 | 描述 |
| --- | --- |
| singleCall | 单人通话 |
| groupCall | 群组通话 |

### TUINetworkQuality

网络质量
| 类型 | 描述 |
| --- | --- |
| unknown | 未定义 |
| excellent | 当前网络非常好 |
| good | 当前网络比较好 |
| poor | 当前网络一般 |
| bad | 当前网络较差 |
| vBad | 当前网络很差 |
| down | 当前网络不满足通话的最低要求 |

### FillMode

视频画面填充模式
| 类型 | 描述 |
| --- | --- |
| fill | 填充模式：即将画面内容居中等比缩放以充满整个显示区域，超出显示区域的部分将会被裁剪掉，此模式下画面可能不完整。 |
| fit | 适应模式：即按画面长边进行缩放以适应显示区域，短边部分会被填充为黑色，此模式下图像完整但可能留有黑边。 |

### Rotation

视频画面旋转方向
| 类型 | 描述 |
| --- | --- |
| rotation_0 | 不旋转 |
| rotation_90 | 顺时针旋转90度 |
| rotation_180 | 顺时针旋转180度 |
| rotation_270 | 顺时针旋转270度 |

### ResolutionMode

视频宽高比模式
| 类型 | 描述 |
| --- | --- |
| landscape | 横屏分辨率，例如：Resolution.Resolution_640_360 + ResolutionMode.Landscape = 640 × 360 |
| portrait | 竖屏分辨率，例如：Resolution.Resolution_640_360 + ResolutionMode.Portrait = 360 × 640 |

### Resolution

视频分辨率
| 类型 | 描述 |
| --- | --- |
| resolution_640_360 | 宽高比 16:9；分辨率 640x360；建议码率（VideoCall）500kbps |
| resolution_960_540 | 宽高比 16:9；分辨率 960x540；建议码率（VideoCall）850kbps |
| resolution_1280_720 | 宽高比 16:9；分辨率 1280x720；建议码率（VideoCall）1200kbps |
| resolution_1920_1080 | 宽高比 16:9；分辨率 1920x1080；建议码率（VideoCall）2000kbps |

### TUICallIOSOfflinePushType

iOS 离线推送类型
| 类型 | 描述 |
| --- | --- |
| APNs | 普通推送 |
| VoIP | VoIP 推送 |

### TUICamera

摄像头类型
| 类型 | 描述 |
| --- | --- |
| front | 前置摄像头 |
| back | 后置摄像头 |

### TUIAudioPlaybackDevice

音频播放路由
| 类型 | 描述 |
| --- | --- |
| speakerphone | 扬声器 |
| earpiece | 耳麦 |

### TUICallResultType

通话记录类型
| 类型 | 描述 |
| --- | --- |
| unknown | 未知 |
| missed | 未接 |
| incoming | 接入 |
| outgoing | 拨出 |

### CallEndReason

通话结束原因
| 类型 | 描述 |
| --- | --- |
| unknown | 未知 |
| hangup | 挂断 |
| reject | 拒绝 |
| noResponse | 无响应 |
| offline | 离线 |
| lineBusy | 忙线 |
| canceled | 取消通话 |
| otherDeviceAccepted | 其他设备接听 |
| otherDeviceReject | 其他设备拒绝 |
| endByServer | 后台结束 |

---

## Platform: Flutter SDK

## TUICallObserver API 简介

TUICallObserver 是 TUICallKit 对应的回调事件类，您可以通过此回调，来监听自己感兴趣的回调事件。

## 回调事件概览
| API | 描述 |
| --- | --- |
| onError | 通话过程中错误回调 |
| onUserInviting | 当用户被邀请加入通话时的回调 |
| onCallReceived | 通话请求的回调 |
| onCallNotConnected | 当前设备未接通通话。 |
| onCallBegin | 通话接通的回调 |
| onCallEnd | 通话结束的回调 |
| onUserReject | xxxx 用户拒绝通话的回调 |
| onUserNoResponse | xxxx 用户不响应的回调 |
| onUserLineBusy | xxxx 用户忙线的回调 |
| onUserJoin | xxxx 用户加入通话的回调 |
| onUserLeave | xxxx 用户离开通话的回调 |
| onUserVideoAvailable | xxxx 用户是否有视频流的回调 |
| onUserAudioAvailable | xxxx 用户是否有音频流的回调 |
| onUserVoiceVolumeChanged | 所有用户音量大小的反馈回调 |
| onUserNetworkQualityChanged | 所有用户网络质量的反馈回调 |
| onKickedOffline | 当前用户被移下线 |
| onUserSigExpired | 在线时票据过期 |

## 回调事件详情

通过 addObserver 监听Flutter插件抛出的事件。
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onError: (int code, String message) {

    },
    onUserInviting: (String userId) {

    },
    onCallReceived: (String callId, String callerId, List<String> calleeIdList,
        TUICallMediaType mediaType, CallObserverExtraInfo info) {

    },
    onCallNotConnected: (String callId, TUICallMediaType mediaType, CallEndReason reason,
        String userId, CallObserverExtraInfo info) {
      
    },
    onCallBegin: (String callId, TUICallMediaType mediaType, CallObserverExtraInfo info) {

    },
    onCallEnd: (String callId, TUICallMediaType mediaType, CallEndReason reason,
        String userId, double totalTime, CallObserverExtraInfo info) {

    },
    onCallMediaTypeChanged: (TUICallMediaType oldCallMediaType, TUICallMediaType newCallMediaType) {

    },
    onUserReject: (String userId) {

    },
    onUserNoResponse: (String userId) {

    },
    onUserLineBusy: (String onUserLineBusy) {

    },
    onUserJoin: (String userId) {

    },
    onUserLeave: (String userId) {

    },
    onUserVideoAvailable: (String userId, bool isVideoAvailable) {

    },
    onUserAudioAvailable: (String userId, bool isAudioAvailable) {

    },
    onUserNetworkQualityChanged: (List<TUINetworkQualityInfo> networkQualityList) {

    },
    onUserVoiceVolumeChanged: (Map<String, int> volumeMap) {

    },
    onKickedOffline: () {

    },
    onUserSigExpired: () {

    }
));
```

### onError

错误事件回调。

> **说明**
> 

> SDK 不可恢复的错误，一定要监听，并分情况给用户适当的界面提示。
> 

``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onError: (int code, String message) {
      // TODO    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| code | int | 错误码 |
| message | String | 错误信息 |

### onUserInviting

当用户被邀请加入通话时的回调
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserInviting: (String userId) {
      // TODO
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 被邀请的用户 ID |

### onCallReceived

收到一个新的来电请求回调，被叫会收到，您可以通过监听这个事件，来决定是否显示通话接听界面。
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallReceived: (String callId, String callerId, List<String> calleeIdList, TUICallMediaType mediaType, CallObserverExtraInfo info) {
      // TODO
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| callerId | String | 主叫 ID（邀请方） |
| calleeIdList | List<String> | 被叫 ID 列表（被邀请方） |
| mediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |
| info | CallObserverExtraInfo | 扩展信息 |

### onCallNotConnected

通话取消的回调
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
  onCallNotConnected: (String callId, TUICallMediaType mediaType, CallEndReason reason,
    String userId, CallObserverExtraInfo info) {
    // TODO
  }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| mediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |
| reason | CallEndReason | 通话结束的原因 |
| userId | String | 结束通话的用户 ID |
| info | CallObserverExtraInfo | 扩展信息 |

### onCallBegin

表示通话接通，主叫和被叫都可以收到，您可以通过监听这个事件来开启云端录制、内容审核等流程。
``` java
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallBegin: (String callId, TUICallMediaType mediaType, CallObserverExtraInfo info) {
    // TODO
  }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| mediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |
| info | CallObserverExtraInfo | 扩展信息 |

### onCallEnd

表示通话接通，主叫和被叫都可以收到，您可以通过监听这个事件来显示通话时长、通话类型等信息，或者来停止云端的录制流程。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallEnd: (String callId, TUICallMediaType mediaType, CallEndReason reason,
        String userId, double totalTime, CallObserverExtraInfo info) {
    // TODO
  }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |
| mediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |
| reason | CallEndReason | 通话结束的原因 |
| userId | String | 结束通话的用户 ID |
| totalTime | double | 此次通话的时长，单位 ms |
| info | CallObserverExtraInfo | 扩展信息 |

> **注意：**
> 

> 客户端的事件一般都会随着杀进程等异常事件丢失掉，如果您需要通过监听通话时长来完成计费等逻辑，建议可以使用 REST API 来完成这类流程。
> 

### onUserReject

通话被拒绝的回调，在1v1 通话中，只有主叫方会收到拒绝回调，在群组通话中，所有被邀请者都可以收到该回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserReject: (String userId) {  
      //您的回调处理逻辑    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| res.userId | String | 拒绝用户的 ID |

### onUserNoResponse

对方无回应的回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserNoResponse: (String userId) { 
      //您的回调处理逻辑    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 无响应用户的 ID |

### onUserLineBusy

通话忙线回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserLineBusy: (String onUserLineBusy) { 
      //您的回调处理逻辑    
    },
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 忙线用户的 ID |

### onUserJoin

有用户进入此次通话的回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserJoin: (String userId) {
      //您的回调处理逻辑    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 加入当前通话的用户 ID |

### onUserLeave

有用户离开此次通话的回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserLeave: (String userId) { 
      //您的回调处理逻辑       
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 离开当前通话的用户 ID |

### onUserVideoAvailable

用户是否开启视频上行回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserVideoAvailable: (String userId, bool isVideoAvailable) { 
      //您的回调处理逻辑       
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 通话用户 ID |
| isVideoAvailable | bool | 用户视频是否可用 |

### onUserAudioAvailable

用户是否开启音频上行回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserAudioAvailable: (String userId, bool isAudioAvailable) {  
      //您的回调处理逻辑       
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 用户 ID |
| isAudioAvailable | bool | 用户音频是否可用 |

### onUserVoiceVolumeChanged

用户通话音量的回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserVoiceVolumeChanged: (Map<String, int> volumeMap) {  
      //您的回调处理逻辑    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| volumeMap | Map<String, int> | 音量表，根据每个 userId 可以获取对应用户的音量大小，音量最小值为0，音量最大值为100 |

### onUserNetworkQualityChanged

用户网络质量的回调。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserNetworkQualityChanged: (List<TUINetworkQualityInfo> networkQualityList) {
      //您的回调处理逻辑    
    }
));

//TUINetworkQualityInfo的定义如下：
class TUINetworkQualityInfo {    
    String userId;  
    TUINetworkQuality quality;  
    TUINetworkQualityInfo({required this.userId, required this.quality});
}
// TUINetworkQuality的定义如下：
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

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| networkQualityList | List<TUINetworkQualityInfo> | 网络状态，根据每个 userId 可以获取对应用户当前的网络质量 |

### onKickedOffline

当前用户被踢下线：此时可以 UI 提示用户，并再次重新调用初始化。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onKickedOffline: () { 
      //您的回调处理逻辑    
    }
));
```

### onUserSigExpired

在线时票据过期：此时您需要生成新的 userSig，并再次重新调用初始化。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onUserSigExpired: () {  
      //您的回调处理逻辑    
    }  
));
```

## 废弃回调

### onCallCancelled

表示此次通话主叫取消、被叫超时、拒接等，涉及多个场景，您可以通过监听这个事件来实现类似未接来电、重置 UI 状态等显示逻辑。
- 主叫取消：主叫收到该回调（userId 为自己）；被叫收到该回调（userId 为**主叫的 ID）。**

- 被叫超时：主叫会同时收到 onUserNoResponse 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫拒接：主叫会同时收到 onUserReject 和 onCallCancelled 回调（userId 是自己的 ID）；被叫收到 onCallCancelled 回调（userId 是自己的 ID）。

- 被叫忙线：主叫会同时收到 onUserLineBusy 和 onCallCancelled 回调（userId 是自己的 ID）。

- 异常中断：被叫接收通话失败，收到该回调（userId 是自己的 ID）。

   ``` cpp
   TUICallEngine.instance.addObserver(TUICallObserver(
       onCallCancelled: (String userId) {  
         //您的回调处理逻辑    
       }
   ));
   ```

   参数如下表所示：

| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 用户的 ID |

### onCallMediaTypeChanged

表示通话的媒体类型发生变化。
``` cpp
TUICallEngine.instance.addObserver(TUICallObserver(
    onCallMediaTypeChanged: (TUICallMediaType oldCallMediaType, TUICallMediaType newCallMediaType) { 
      //您的回调处理逻辑    
    }
));
```

参数如下表所示：
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| oldCallMediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |
| newCallMediaType | TUICallMediaType | 通话的媒体类型，示例：`TUICallMediaType.video` 或 `TUICallMediaType.audio` |

---

## Platform: Flutter SDK

## TUICallKit API 简介

TUICallKit API 是音视频通话组件的**含 UI 接口**，使用TUICallKit API，您可以通过简单接口快速实现一个类微信的音视频通话场景，更详细的接入步骤，详情请参见 快速接入（TUICallKit）。

## API 概览
| API | 描述 |
| --- | --- |
| login | 登录 |
| logout | 登出 |
| setSelfInfo | 设置用户的昵称、头像 |
| calls | 发起通话。 |
| join | 主动加入通话。 |
| enableMuteMode | 开启/关闭静音模式 |
| enableFloatWindow | 开启/关闭悬浮窗功能 |
| setCallingBell | 设置自定义来电铃音 |
| enableVirtualBackground | 开启/关闭虚拟背景功能 |

## API 详情

### login

登录。
``` cpp
Future<TUIResult> login(int sdkAppId, String userId, String userSig)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| sdkAppId | int | 用户 SDKAppID |
| userId | String | 用户 ID |
| userSig | String | 用户签名 userSig |
| 返回值 | TUIResult | 包含 code 和 message 信息：code 为空 ("") 表示调用成功；code 不为空 ("") 表示调用失败，失败原因见 message |

### logout

登出。
``` cpp
Future<void> logout()
```

### setSelfInfo

设置用户昵称、头像。用户昵称不能超过500字节，用户头像必须是URL格式。
``` cpp
Future<TUIResult> setSelfInfo(String nickname, String avatar)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| nickName | String | 目标用户的昵称，非必填 |
| avatar | String | 目标用户的头像，非必填 |
| 返回值 | TUIResult | 包含 code 和 message 信息：code 为空 ("") 表示调用成功；code 不为空 ("") 表示调用失败，失败原因见 message |

### calls

发起通话。**v2.9+ 版本支持。**
``` java
Future<TUIResult> calls(List<String> userIdList, TUICallMediaType mediaType, TUICallParams params)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userIdList | List<String> | 目标用户的userId 列表 |
| callMediaType | TUICallMediaType | 通话的媒体类型，比如：`TUICallMediaType.video `或  `TUICallMediaType.audio` |
| params | TUICallParams | **可选**通话扩展参数，例如：房间号、通话邀请超时时间，离线推送自定义内容等 |

### join

主动加入通话。**v2.9+ 版本支持。**
``` java
Future<void> join(String callId)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| callId | String | 此次通话的唯一 ID |

### enableMuteMode

开启后，收到通话请求，不会播放来电铃声。
``` javascript
Future<void> enableMuteMode(bool enable)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| enable | bool | 开启、关闭静音；true 表示开启静音 |

### enableFloatWindow

开启/关闭悬浮窗功能，设置为false后，通话界面左上角的悬浮窗按钮会隐藏。
``` javascript
Future<void> enableFloatWindow(bool enable)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| enable | bool | 开启、关闭悬浮窗功能；true 表示开启浮窗 |

### setCallingBell

自定义来电铃声：将铃声文件添加至主工程的`assets`资源中，传入资源文件名称即可。
``` javascript
Future<void> setCallingBell(String assetName)
```

### enableVirtualBackground

开启/关闭虚拟背景功能，开启虚拟背景功能后，您可以在 UI 上显示模糊背景的功能按钮，点击按钮可直接启用模糊背景功能。
``` javascript
Future<void> enableVirtualBackground(bool enable)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| enable | bool | 开启、关闭静音；true 表示开启静音 |

## 废弃接口

### call

拨打电话（1v1通话）。

> **注意：**
> 

> 该接口已在 v2.9+ 版本废弃，建议使用 calls 接口替代。
> 

``` cpp
Future<void> call(String userId, TUICallMediaType callMediaType, [TUICallParams? params])

```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| userId | String | 目标用户的 userID |
| callMediaType | TUICallMediaType | 通话的媒体类型，比如：`TUICallMediaType.video `或 `TUICallMediaType.audio` |

### groupCall

发起群组通话，注意：使用群组通话前需要创建 IM 群组，如果已经创建，请忽略。

> **注意：**
> 

> 该接口已在 v2.9+ 版本废弃，建议使用 calls 接口替代。
> 

``` javascript
Future<void> groupCall(String groupId, List<String> userIdList, TUICallMediaType callMediaType,[TUICallParams? params])
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| groupId | String | 此次群组通话的群 ID |
| userIdList | List<String> | 目标用户的 userId 列表 |
| callMediaType | TUICallMediaType | 通话的媒体类型，比如：`TUICallMediaType.video `或  `TUICallMediaType.audio` |

### joinInGroupCall

加入群组中已有的音视频通话。

> **注意：**
> 

> 该接口已在 v2.9+ 版本废弃，建议使用 join 接口替代。
> 

``` javascript
Future<void> joinInGroupCall(TUIRoomId roomId, String groupId, TUICallMediaType callMediaType)
```
| 参数 | 类型 | 含义 |
| --- | --- | --- |
| roomId | TUIRoomID | 此次通话的音视频房间 ID |
| groupId | String | 此次群组通话的群 ID |
| callMediaType | TUICallMediaType | 通话的媒体类型，比如：`TUICallMediaType.video `或 `TUICallMediaType.audio` |
