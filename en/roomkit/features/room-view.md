> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

This document will guide you in using Atomicx's core view component `RoomView` to implement video rendering for real-time audio and video rooms. By configuring layout templates and utilizing component slots, you can quickly implement video layout switching and personalized customization.

`RoomView` is the main container component responsible for video rendering in Atomicx. It automatically handles video stream subscription, rendering, and release. You only need to focus on its layout configuration and UI enhancement.

## Core Capabilities

`RoomView`, as the core view container for multi-party audio and video rooms, has built-in multiple enterprise-grade audio and video processing capabilities:
- **Intelligent Performance Management**: Supports lazy loading of video streams within the viewport, and can automatically switch between high and low-definition streams based on stream area size and number of remote streams, significantly reducing system energy consumption and bandwidth usage.

- **Fine-grained Sorting Strategy**: The system automatically adjusts frame order based on roles (host priority) and media status (audio and video enabled priority).

- **Interactive Speaker Mode**: Supports double-clicking to lock the main screen, and in sidebar or top bar layouts, supports collapsing non-core areas to focus on presentation content.

- **Immersive Visual Design**: All video windows are forced to maintain a standard `16:9` ratio, combined with interactive display toolbars, providing an interference-free multi-party audio and video room experience.

## Customizable Range

`RoomView` focuses only on underlying video stream playback, while giving developers all "presentation rights" at the view layer. Using `RoomView`, you can freely define:
- **Layout Switching**: Transform layouts in real-time according to business scenarios (e.g., speaker mode or discussion mode).

- **Video Widget UI:** Including name tags, volume dynamic waveforms, role badges, etc.

## **Prerequisites**

- The user has completed login authentication through [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState), please refer to Integration Overview.

- The user has entered a room through [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState), refer to Room Management.

- Global `UIKitProvider` has been configured in `App.vue`, please refer to [Configure App.vue](https://cloud.tencent.com/document/product/647/81962#ce98ee15-b0cb-43af-81da-70f51231430e).

## Implementing Video Layout Feature

### Step 1: Import and Render Basic Layout

In your meeting page (e.g., RoomLayoutView.vue), import `RoomView` and the layout enum `RoomLayoutTemplate`.
``` typescript
<template>
  <div class="room-container">
    <RoomView />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { RoomView } from 'tuikit-atomicx-vue3/room';
</script>

<style lang="scss">
.room-container {
 width: 100%;
 height: 100%;
}
</style>
```

> **Note:**
> 

> `RoomView` will automatically fill the parent container's height and width, please ensure its parent container has explicit dimensions.
>

### Step 2: Switch Layout Mode

You can dynamically update the `layoutTemplate` attribute value of `RoomView` based on business logic (e.g., clicking a switch button).
| Property Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| `layoutTemplate` | `RoomLayoutTemplate` | `RoomLayoutTemplate.GridLayout` | Video stream layout |

`RoomLayoutTemplate` optional value analysis:
| Layout Mode | Enum Value | Description |
| --- | --- | --- |
| Grid Layout | `RoomLayoutTemplate.GridLayout` | Default layout. All participant screens are evenly arranged, supports pagination when participants exceed 9 people. |
| Sidebar Layout | `RoomLayoutTemplate.SidebarLayout` | Main screen is prominently displayed, with other participant screens arranged in a sidebar format, suitable for speeches, reports, and other scenarios. |
| Top Bar Layout | `RoomLayoutTemplate.CinemaLayout` | Main screen is prominently displayed, with other participant screens arranged in a top bar format, suitable for speeches, reports, and other scenarios. |

``` typescript
<template>
  <!-- Add layout switch buttons -->
  <div class="layout-switcher">
    <button @click="switchLayout(RoomLayoutTemplate.GridLayout)">Grid</button>
    <button @click="switchLayout(RoomLayoutTemplate.SidebarLayout)">Sidebar</button>
    <button @click="switchLayout(RoomLayoutTemplate.CinemaLayout)">Top Bar</button>
  </div>
  <div class="room-container">
    <RoomView :layoutTemplate="currentLayout" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { RoomView, RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
// Default to grid layout
const currentLayout = ref(RoomLayoutTemplate.GridLayout);

// Switch layout
const switchLayout = (layout: RoomLayoutTemplate) => {
  currentLayout.value = layout;
};
</script>

<style lang="scss">
.room-container {
 width: 100%;
 height: 100%;
}
</style>
```

### Step 3: Implement Video Stream Widget Information

You can customize UI display content for video screens (e.g., set nicknames, add custom icons) by using the `participantViewUI` slot.

| Slot Parameter | Type | Description |
| --- | --- | --- |
| `participant` | `RoomParticipant` | Participant object corresponding to the current video stream, containing real-time status information such as user's `userId`, `userName`, `role`, etc. |
| `streamType` | `VideoStreamType` | Distinguish whether the current is camera stream (Camera) or screen share stream (Screen) |

#### Prepare Visual Resources

To provide complete interactive feedback, it is recommended to prepare the following icons in advance:
- Microphone status icons: Distinguish between enabled, muted, and dynamic volume bars.

- Identity icons: Used to distinguish room owner (Owner), administrator (Admin), and regular members.

- Network signal icons: Display real-time uplink/downlink network quality.
   

   > **Special Note:**
   > 

   > We provide copyright-free icons in the @tencentcloud/uikit-base-component-vue3 npm package in the sample project, which you can directly import and use.
   > 

#### Implementation Code Example
1. Add file `ParticipantViewUI.vue` to implement UI rendering logic for a single video stream widget layer.

   ``` typescript
   <template>
     <div class="stream-cover-container">
       <div class="corner-user-info-container">
         <div
           v-if="showMasterIcon || showAdminIcon"
           :class="{ 'master-icon': showMasterIcon, 'admin-icon': showAdminIcon }"
         >
           <IconUser />
         </div>
         <div v-if="!isScreenStream" :class="['audio-icon-container']">
           <div class="audio-level-container">
             <div class="audio-level" :style="audioLevelStyle" />
           </div>
           <IconMicOff v-if="!isMicrophoneOn" class="audio-icon" size="20" />
           <IconMicOn v-else class="audio-icon" size="20" />
         </div>
         <span class="user-name" :title="displayName">
           {{ displayName }}
         </span>
       </div>
     </div>
   </template>
   
   <script setup lang="ts">
   import { computed, defineProps } from 'vue';
   import {
     IconUser,
     IconMicOff,
     IconMicOn,
   } from '@tencentcloud/uikit-base-component-vue3';
   import { RoomParticipantRole, DeviceStatus, VideoStreamType, useRoomParticipantState } from 'tuikit-atomicx-vue3/room';
   import type { RoomParticipant } from 'tuikit-atomicx-vue3/room';
   
   interface Props {
     participant: RoomParticipant;
     streamType: VideoStreamType;
   }
   const props = defineProps<Props>();
   
   const { speakingUsers } = useRoomParticipantState();
   const speakingAudioVolume = computed(() => speakingUsers.value.get(props.participant.userId) || 0);
   const audioLevelStyle = computed(() => {
     if (props.participant.microphoneStatus === DeviceStatus.Off || !speakingAudioVolume.value) {
       return '';
     }
     return `height: ${speakingAudioVolume.value * 4}%`;
   });
   
   const isMicrophoneOn = computed(() => props.participant.microphoneStatus === DeviceStatus.On);
   const displayName = computed(() => props.participant.nameCard || props.participant.userName || props.participant.userId);
   
   const showMasterIcon = computed(() => {
     const { role } = props.participant;
     return role === RoomParticipantRole.Owner && props.streamType === VideoStreamType.Camera;
   });
   
   const showAdminIcon = computed(() => {
     const { role } = props.participant;
     return (role === RoomParticipantRole.Admin && props.streamType === VideoStreamType.Camera);
   });
   </script>
   
   <style lang="scss" scoped>
   .stream-cover-container {  position: absolute;  top: 0;  left: 0;  width: 100%;  height: 100%;  border-radius: 12px;  pointer-events: none;  .corner-user-info-container {    position: absolute;    bottom: 8px;    left: 8px;    display: flex;    align-content: center;    align-items: center;    box-sizing: border-box;    min-width: 118px;    max-width: calc(100% - 24px);    padding-right: 10px;    height: 32px;    overflow: hidden;    font-size: 14px;    color: var(--uikit-color-white-1);    border-radius: 16px;    background-color: var(--uikit-color-black-5);    .master-icon,    .admin-icon {      display: flex;      flex-shrink: 0;      align-items: center;      justify-content: center;      width: 32px;      height: 32px;      margin-left: 0;      border-radius: 50%;      background-color: var(--button-color-primary-default);    }    .admin-icon {      background-color: var(--text-color-warning);    }    .audio-icon-container {      margin-left: 4px;      position: relative;      width: 20px;      height: 20px;      flex-shrink: 0;      min-width: 20px;      &:first-child {        margin-left: 8px;      }      .audio-level-container {        position: absolute;        top: 2px;        left: 6px;        display: flex;        flex-flow: column-reverse wrap;        justify-content: space-between;        width: 8px;        height: 12px;        overflow: hidden;        border-radius: 4px;        .audio-level {          width: 100%;          background-color: var(--text-color-success);          transition: height 0.2s;        }      }      .audio-icon {        position: absolute;        top: 0;        left: 0;      }    }    .user-name {      margin-left: 4px;      overflow: hidden;      text-overflow: ellipsis;      white-space: nowrap;      min-width: 0;    }    .screen-icon {      flex-shrink: 0;      min-width: 0;      color: var(--uikit-color-white-1);      margin-left: 4px;      margin-right: 2px;    }    .screen-info {      margin-left: 4px;      font-size: 12px;      color: var(--uikit-color-white-1);      flex-shrink: 0;      min-width: 0;    }  }}
   </style>
   ```
2. Configure `RoomView` slot to render video stream widget content on the page.

   ``` typescript
   <template>
     <div class="room-container">
       <RoomView :layout-template="currentLayout">
         <template #participantViewUI="{ participant, streamType }">
           <ParticipantViewUI :participant="participant" :stream-type="streamType" />
         </template>
       </RoomView>
     </div>
   </template>
   
   <script setup lang="ts">
   import { ref } from 'vue';
   import { RoomView, RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
   // Import the newly added ParticipantViewUI.vue file
   import ParticipantViewUI from './ParticipantViewUI.vue';
   
   // Default to grid layout
   const currentLayout = ref(RoomLayoutTemplate.GridLayout);
   </script>
   ```

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useRoomParticipantState** | Contains room user data and user management interface. | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |

## Common Questions

### **Does RoomView include screen share stream rendering?**

`RoomView` has built-in remote screen share stream rendering. For local screen sharing, you need to complete local screen share placeholder UI through the `participantViewUI` slot.

### Does RoomView include default UI widgets?

`RoomView` has no default UI widgets internally. You can refer to [RoomView Open Source Usage Example](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts/src/components/RoomLayoutView) to quickly complete custom UI widgets.

### If the layouts provided by RoomView do not meet requirements, can more optional layouts be provided?

`RoomView` currently supports grid layout, sidebar, and top bar layouts. If you have customization needs, please [contact us](https://cloud.tencent.com/document/product/647/19906).
