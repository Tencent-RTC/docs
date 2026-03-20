> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

TUIRoomKit is an audio/video room UI application launched by Tencent Cloud. This upgrade introduces a brand new atomic component integration solution, allowing developers to flexibly combine these atomic components to build personalized audio/video room interfaces.

> **Recommended: Use More Efficient AI Integration Assistant**
> 

> We provide you with a brand new AI integration method. Simply describe your requirements to automatically generate integration code, greatly improving development efficiency.
> 

> [Click here to experience AI integration immediately](https://cloud.tencent.com/document/product/647/127873)
> 

This document describes how to integrate TUIRoomKit into existing Vue3 projects, and how to customize TUIRoomKit's styles, layouts, and functionality.
- Desktop Multi-person Conference

  

- H5 Multi-person Conference

  

## Prerequisites

### Service Activation

Please refer to [Service Activation](https://cloud.tencent.com/document/product/647/104842) to get the TUIRoomKit trial version, and obtain the following information from the [Application Management](https://console.cloud.tencent.com/trtc) page:
- **SDKAppID**: Application identifier, Tencent Cloud completes billing statistics based on `SDKAppID`.

- **SDKSecretKey**: Application secret key, used for key information in initialization configuration files.

### Environment Preparation

- **Node.js**: ≥ 18.19.1 (Recommended to use official LTS version).

- **Vue**: ≥ 3.4.21.

- **Modern Browsers**: Modern browsers that support [WebRTC APIs](https://cloud.tencent.com/document/product/647/17249#.E6.94.AF.E6.8C.81.E7.9A.84.E5.B9.B3.E5.8F.B0).

- **Devices**: Camera, microphone, speakers.

## Source Code Integration

### Step 1: Install Project Dependencies

Install the following dependencies in your business project.

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

### Step 2: **Import **TUIRoomKit** Components**

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

### Step 3: Configure App.vue File

Merge the following content into your project's App.vue file to configure global UI styles and user login logic:
- Configure global `UIKitProvider`: Import the `UIKitProvider` component and set default theme and language;

- User Login: Execute `login` and `setSelfInfo` in the `onMounted` hook;

  ``` typescript
  <template>
    <!-- Step 1: Configure global UIKitProvider -->
    <UIKitProvider theme="light" language="en-US">
      <!-- Your business component containing ConferenceMainView -->
    </UIKitProvider>
  </template>
   
  <script setup>
  import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
  import { onMounted } from 'vue';
  import { useLoginState } from 'tuikit-atomicx-vue3/room';
  
  // step 2: User login
  const { login, setSelfInfo } = useLoginState();
  const SDKAppID = 0;    // Note: Please replace with SDKAppID obtained from TRTC Console
  const userId = 'Enter your real userId';    // Note: Please replace with real userId
  const userName = 'Enter your real userName';  // Note: Please replace with real userName
  const userSig = 'Enter your real userSig';    // Note: For generating temporary userSig, please refer to https://cloud.tencent.com/document/product/647/17275#console
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
  
  <!-- Step 3: Configure global styles (These styles override default styles of scaffolded new projects, configure as needed)-->
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

### Step 4: Start New Conference

Conference hosts can start a new conference by calling the `conference.start` interface. Other participants can refer to the description in Step 6 and call the `conference.join` interface to join the conference.

> **Note:**
> 

> Please refer to Step 3 and call the `conference.start` interface to start a conference after successful user login.
> 

``` typescript
import { conference } from '@tencentcloud/roomkit-web-vue3';
const startConference = async () => {  
  await conference.start({
    roomId: '123456',   // Please replace with your real roomId
    options: {
      roomName: 'My Temporary Conference',
    },
  });
}
startConference();
```

### Step 5: Join Existing Conference

Participants can join a conference started by the conference host in Step 4 by calling the `conference.join` interface and filling in the corresponding roomId parameter.

> **Note:**
> 

> Please refer to Step 3 and call the `conference.join` interface to join a conference after successful user login.
> 

``` typescript
import { conference } from '@tencentcloud/roomkit-web-vue3';
const joinConference = async () => {
  await conference.join({
    roomId: '123456',   // Please replace with your real roomId
  });
}
joinConference();
```

## Start Project

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

After successful startup, please open the debug address (usually http://localhost:5173) in your browser to access the application. You will see the conference interface as shown below:

## Quick UI Customization

### Theme and Language

Modify the default values of theme and language by configuring the parameters of `UIKitProvider` in App.vue.
| UIKitProvider Parameter | Available Values | Default Value |
| --- | --- | --- |
| theme | "light" \\| "dark" | "light" |
| language | "zh-CN" \\| "en-US" | "en-US" |

``` typescript
<UIKitProvider theme="light" language="en-US">
  <router-view />
</UIKitProvider>
 
<script setup lang="ts">
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
```

### Configure User Contact List

TUIRoomKit scheduled meeting user selection list and in-meeting call user selection list display user relationship chain list by default. Please refer to the following steps to batch import user relationship chains:

Step 1: Use [Account Management > Import Multiple Accounts](https://cloud.tencent.com/document/product/269/4919) REST API interface to batch import user accounts;

Step 2: Use [Friend Management > Import Friends](https://cloud.tencent.com/document/product/269/8301) REST API interface to batch import user relationship chains;

## TUIRoomKit Interface Customization

### Source Code Export

【Vue3】
1. Execute source code export script, default copy path is `./src/components/RoomKit`

``` bash
  node ./node_modules/@tencentcloud/roomkit-web-vue3/scripts/eject.js
```
2. Confirm according to script prompt whether to copy TUIRoomKit source code to `./src/components/RoomKit` directory. If you need to customize the copy directory, please enter 'y', otherwise enter 'n'.

3. After source code export, TUIRoomKit source code will be added to your specified project path. At this time, you need to manually change the references of ConferenceMainView component, ConferenceMainViewH5 component and conference object from npm package address to the relative path address of TUIRoom source code.

``` typescript
- import { ConferenceMainView, conference } from '@tencentcloud/roomkit-web-vue3';
// Replace reference path to the real path of TUIRoomKit source code
+ import { ConferenceMainView, ConferenceMainViewH5, conference } from './components/RoomKit/index.ts';
```
4. Configure ESLint Validation

If you encounter ESLint errors when running the project after exporting TUIRoomKit source code, you can add the RoomKit folder to the `.eslintignore` file to ignore ESLint detection.
``` javascript
// Please replace with the real path of TUIRoomKit source code
src/components/RoomKit
```

### Custom Room Stream Layout

TUIRoomKit currently supports three stream layouts: grid, sidebar, and top bar, with grid layout as default.
``` typescript
// RoomKit/views/ConferenceMainView/index.vue
import { RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
// Change default video stream layout to sidebar layout
const participantViewLayout = ref(RoomLayoutTemplate.SidebarLayout);
function handleLayoutUpdate(layout: RoomLayoutTemplate) {
  participantViewLayout.value = layout;
}

// Change default video stream layout to top bar layout
const participantViewLayout = ref(RoomLayoutTemplate.CinemaLayout);
function handleLayoutUpdate(layout: RoomLayoutTemplate) {
  participantViewLayout.value = layout;
}
```

### Custom Video Stream Widgets

TUIRoomKit default video stream widgets are shown in the figure. To customize video stream widgets, please modify the `RoomKit/components/RoomLayoutView/ParticipantViewUI/ParticipantViewUI.vue` component.

## Frequently Asked Questions

#### **TUIRoomKit works normally during local development, but cannot properly capture user's camera or microphone devices after deployment to online environment?**
- **Root Cause Analysis:** Browsers have strict restrictions on the capture of audio and video devices (microphone, camera) for security and privacy protection considerations. Capture operations are only allowed in secure environments. Secure environment protocols include: https://, localhost, file://, etc. HTTP protocol is considered insecure, and browsers will prohibit access to media devices by default.

- **Solution:** If everything works normally in your local (localhost) testing, but capture fails after deployment, please immediately check whether your webpage is deployed on HTTP protocol. You must use HTTPS protocol to deploy your webpage and ensure you have valid security certificates.

- **Related Resources:** For more details about URL domain and protocol restrictions, please refer to [URL Domain and Protocol Restrictions](https://cloud.tencent.com/document/product/647/17249#URL-.E5.9F.9F.E5.90.8D.E5.8D.8F.E8.AE.AE.E9.99.90.E5.88.B6).

#### **Does TUIRoomKit support iframe integration?**

Yes. When integrating TUIRoomKit using iframe, you need to configure the allow attribute in the iframe tag to grant necessary browser permissions (microphone, camera, screen sharing, fullscreen, etc.), as shown in the example below: 
``` typescript
// Enable microphone, camera, screen sharing, fullscreen permissions
<iframe allow="microphone; camera; display-capture; display; fullscreen;">
```

#### Does TUIRoomKit support setting internal network proxy?

Yes. Please refer to the following guidance to set internal network proxy parameters. For more internal network proxy information, please refer to [Dealing with Firewall Restrictions](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-34-advanced-proxy.html).
``` typescript
// Please set internal network proxy parameters before entering room
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
