> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

This document guides you on how to use Atomicx to implement screen sharing capabilities and handle room interactions based on screen sharing status.

## Prerequisites

- The user has completed login authentication through [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState), please refer to Integration Overview.

- The user has entered a room through [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState), please refer to Room Management.

- If integrating in `iframe`, permissions need to be declared in the tag.

   ``` typescript
   <iframe allow="display-capture; fullscreen;"></iframe>
   ```

## Implementing Screen Sharing Feature

### Step 1: Start Screen Sharing

Use the [startScreenShare](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#startScreenShare) method to start screen sharing. In the Web environment, you will see the browser's screen selector where you can choose to share the entire screen, application window, or browser tab.

``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { startScreenShare } = useDeviceState();

// Try to start screen sharing and attempt to share system audio
const handleStartShare = async () => {
  try {
    await startScreenShare({ screenAudio: true });
  } catch (error) {
    // Handle errors such as user rejection or browser not supporting
    console.error('Failed to start screen sharing:', error);
  }
};
```

### Step 2: Share System Audio

By setting the `screenAudio: true` parameter, you can share system audio while screen sharing. This is very useful when sharing videos, presentations, music, and other audio content.

``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { startScreenShare } = useDeviceState();

// Start screen sharing and share system audio
const handleStartScreenShareWithAudio = async () => {
  try {
    await startScreenShare({ screenAudio: true });
    console.log('Screen sharing (with audio) started');
  } catch (error) {
    console.error('Failed to start screen sharing:', error);
  }
};
```

**Browser Compatibility Notes:**
| Browser | System Audio Sharing Support | Notes |
| --- | --- | --- |
| Chrome 74+ | Supported | Need to check **Share audio** in selector |
| Edge 79+ | Supported | Based on Chromium, same experience as Chrome |
| Firefox | Not Supported | System audio sharing not supported yet |
| Safari | Not Supported | System audio sharing not supported yet |

> **Note:**
> 

> System audio sharing requires browser support, and users need to check the **Share audio** option in the screen selector. If the browser doesn't support system audio sharing, the `screenAudio` parameter will be ignored, but screen video sharing will work normally.
>

### Step 3: Stop Screen Sharing

Users can stop screen sharing through the following methods:
1. **Call the **[**stopScreenShare()**](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#stopScreenShare)** interface**

   ``` typescript
   import { useDeviceState, DeviceStatus } from 'tuikit-atomicx-vue3/room';
   const { screenStatus, stopScreenShare } = useDeviceState();
   
   // Stop screen sharing
   const handleStopScreenShare = async () => {
     try {
       await stopScreenShare();
       console.log('Screen sharing stopped');
     } catch (error) {
       console.error('Failed to stop screen sharing:', error);
     }
   };
   ```
2. **Click the browser's native "Stop sharing" button**

   

3. **Close the shared window or tab**
   

   > **Note:**
   > 

   > When users stop screen sharing using any method, Atomicx will automatically update the [screenStatus](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#screenStatus) state.
   >

### Step 4: Permission Control and State Listening

#### **Set Screen Sharing Permissions**

You can control screen management permissions in the room through member management interfaces provided by [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState).
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { disableAllDevices } = useRoomParticipantState();

// Restrict screen sharing to only room owner/admin
async function handleDisableAllScreen() {
  await disableAllDevices({
    deviceType: DeviceType.Screen,
    disable: true,
  });
}

// Allow all users in the room to initiate screen sharing
async function handleEnableAllScreen() {
  await disableAllDevices({
    deviceType: DeviceType.Screen,
    disable: false,
  });
}
```

#### **Automatic Layout Switching**

When detecting that a user in the room has started screen sharing, it is recommended to switch to sidebar layout (`RoomLayoutTemplate.SidebarLayout`), enlarging the shared screen while displaying other participants' screens in the sidebar.
``` typescript
<template>
  <div class="room-container">
    <RoomView :layout-template="layoutTemplate">
      <template #participantViewUI="{ participant, streamType }">
        <ParticipantViewUI 
          :participant="participant" 
          :stream-type="streamType" 
        />
      </template>
    </RoomView>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';
import { 
  RoomView, 
  RoomLayoutTemplate,
  useRoomParticipantState
} from 'tuikit-atomicx-vue3/room';

const layoutTemplate = ref(RoomLayoutTemplate.GridLayout);
const { participantWithScreen } = useRoomParticipantState();

// Listen for screen sharing state and automatically switch layout
watch(participantWithScreen, (participant) => {
  if (participant) {
    // Someone is sharing screen, switch to sidebar layout
    layoutTemplate.value = RoomLayoutTemplate.SidebarLayout;
  } else {
    // No one is sharing screen, switch back to grid layout
    layoutTemplate.value = RoomLayoutTemplate.GridLayout;
  }
});
</script>
```

#### **Restrict to One Person Screen Sharing**

Only one person should be allowed to share screen in a room. When someone is sharing, disable other users' share buttons:
``` typescript
<script setup>
import { computed } from 'vue';
import { useRoomParticipantState } from 'tuikit-atomicx-vue3/room';

const { participantWithScreen, localParticipant } = useRoomParticipantState();

const isOtherSharing = computed(() => {
  const sharer = participantWithScreen.value;
  if (!sharer) return false;
  return sharer.userId !== localParticipant.value?.userId;
});

const canStartScreenShare = computed(() => {
  return !isOtherSharing.value;
});
</script>

// Use in template
<template>
  <button :disabled="!canStartScreenShare" @click="handleStartShare">
    Start Screen Sharing
  </button>
</template>
```

### Step 5: Handle Screen Sharing Errors

When screen sharing encounters errors, provide appropriate prompts to users based on error messages.

**Code Example:**
``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
import { TUIToast } from '@tencentcloud/uikit-base-component-vue3';

const { startScreenShare } = useDeviceState();

try {
  await startScreenShare();
} catch (error: any) {
  let message = '';
  switch (error.name) {
    case 'NotReadableError':
      message = 'System prohibits current browser from accessing screen content, please enable screen sharing permission.';
      break;
    case 'NotAllowedError':
      if (error.message.includes('Permission denied by system')) {
        message = 'System prohibits current browser from accessing screen content, please enable screen sharing permission.';
      } else {
        message = 'User cancelled screen sharing';
      }
      break;
    default:
      message = 'Unknown error occurred during screen sharing, please try again.';
      break;
  }
  TUIToast.warning({
    message,
  });
}
```

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useDeviceState** | Contains audio/video device status, audio/video device list, and operation interfaces. | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) |
| **useRoomParticipantState** | Contains room user data and user management interface. | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |

## Common Questions

### Why doesn't system audio sharing work?

- **Platform Restriction:** Audio sharing is currently only supported on modern browsers like Chrome and Edge.

- **Operational Details:** Users must manually select **Share audio** in the browser's screen selector.

- **System Restriction:** Some operating systems do not support sharing audio from specific **application windows**. It is recommended to prioritize selecting **entire screen** or **browser tab**.

### How to detect when users stop screen sharing by clicking the browser's "Stop sharing" button?

Atomicx automatically listens for the browser's stop sharing event and updates the `screenStatus` state. You just need to listen for changes in `screenStatus`:
``` typescript
watch(screenStatus, (newStatus, oldStatus) => {
  if (oldStatus === DeviceStatus.On && newStatus === DeviceStatus.Off) {
    console.log('User has stopped screen sharing.');
  }
});
```

### Do mobile browsers support screen sharing?

Only desktop browsers support screen sharing. Mobile browsers do not support screen sharing capabilities.

### Why does the user's microphone status display as "on" when they enable screen sharing audio?

This is expected system behavior for the following reasons:

**Technical Principle:**`microphoneStatus` indicates whether the current device is transmitting audio signals externally. When screen sharing is enabled and **system audio capture is checked**, Atomicx SDK will start an audio transmission channel. Since the remote end receives a continuous audio data stream, the system cannot distinguish whether the audio source is from a physical microphone or system playback sound, so the audio capture status is uniformly marked as `DeviceStatus.On`.

**Privacy Note:** Please rest assured, this does not mean your microphone is capturing voice. Whether the microphone voice is being pushed is only related to microphone control interfaces (`openLocalMicrophone` and `unmuteLocalMicrophone`), and is independent of screen sharing audio capture. Even if screen sharing audio is being transmitted, as long as microphone-related interfaces are not called, your microphone voice will not be captured and pushed.
