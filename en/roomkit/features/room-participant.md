> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

This document will guide you on how to manage participants in a room. Atomicx provides an out-of-the-box list component `RoomParticipantList` for quick integration, and exposes the [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) Hook to facilitate custom member control logic implementation.

## Feature Introduction

The member management module covers a complete set of capabilities for displaying user information and handling permissions in the room:
- **Real-time List Display:** Dynamically presents nicknames, roles, audio/video status, and volume fluctuation of all members in the room.

- **Permission Control:** Supports room owner or administrator to perform management operations such as kicking out, muting all, and transferring ownership.

- **Tiered Management:** Supports differentiated permission strategies for room owners, administrators, and regular members.

## Prerequisites

- User has completed login authentication through [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState), please refer to Integration Overview.

- User has entered the room through [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState), please refer to Room Management.

## Integrating Member Management Component

If you need to quickly build a member list interface, it's recommended to directly use the `RoomParticipantList` component. `RoomParticipantList` is an out-of-the-box participant list component with built-in complete member management UI and interactions.

Example code for integrating `RoomParticipantList` component:
``` typescript
<template>
  <div class="room-page">
    <!-- Participant list sidebar -->
    <aside class="sidebar">
      <div class="sidebar-header">
        <span>Participants ({{ currentRoom.participantCount }})</span>
        <IconClose />
      </div>

      <!-- Participant list component -->
      <RoomParticipantList />
    </aside>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { IconClose } from '@tencentcloud/uikit-base-component-vue3';
import { 
  RoomView,
  RoomParticipantList,
  useRoomState,
  useRoomParticipantState 
} from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { participantList } = useRoomParticipantState();
</script>

<style scope>
.room-page {
  width: 100vw;
  height: 100vh;
  min-width: 1150px;
  display: flex;
  flex-direction: row;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  position: relative;
  overflow: hidden;
}

.sidebar {
  position: absolute;
  top: 0;
  right: 0;
  width: 400px;
  height: 100%;
  box-sizing: border-box;
}

.sidebar-header {
  display: flex;
  height: 60px;
  justify-content: space-between;
  align-items: center;
  padding: 0px 20px;
}
</style>
```

> **Note:**
> - `RoomParticipantList` will automatically fill the height and width of its parent container, please ensure its parent container has explicit dimensions.
> - When using the `RoomParticipantList` component, ensure global `UIKitProvider` is configured in `App.vue`, please refer to [Configure App.vue](https://cloud.tencent.com/document/product/647/81962#ce98ee15-b0cb-43af-81da-70f51231430e).

## Implementing Member Management Features

If you need to customize member list UI or trigger management actions in specific business scenarios, please use [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState).

### Step 1: Get Member List

Get reactive member data [participantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#participantList) through [getParticipantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#getParticipantList).
``` typescript
import { useRoomState, useRoomParticipantState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { participantList, participantListCursor, getParticipantList } = useRoomParticipantState();

// Get room user list after successfully joining room
watch(() => currentRoom.value?.roomId, async () => {
 await getParticipantList();
})

// Load more users when scrolling to bottom of list
const handleLoadMore = async () => {
  if (participantListCursor.value) {
    await getParticipantList({ cursor: participantListCursor.value });
  }
};
```

> **Note:**
> 

> Data successfully fetched by `getParticipantList` interface will be automatically maintained in `participantList`, UI only needs to render data from `participantList`.
>

### Step 2: Render Participant Information

Developers can use [participantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#participantList) data to customize rendering of user information list.
``` typescript
<template>
  <div class="participant-list">
    <div class="list-header">
      <h3>Participants ({{ participantList.length }})</h3>
      <button @click="handleRefresh">
        <IconRefresh />
      </button>
    </div>
    <div class="list-content">
      <div 
        v-for="participant in participantList" 
        :key="participant.userId"
        class="participant-item"
      >
        <!-- Avatar -->
        <Avatar 
          :user-id="participant.userId" 
          :avatar-url="participant.avatarUrl" 
        />

        <!-- User info -->
        <div class="user-info">
          <div class="user-name">
            <span>{{ participant.userName }}</span>
            <span v-if="participant.userId === localParticipant.userId" class="badge">Me</span>
            <span 
              v-if="participant.role === RoomParticipantRole.Owner" 
              class="role-badge owner"
            >
              Owner
            </span>
            <span 
              v-else-if="participant.role === RoomParticipantRole.Admin" 
              class="role-badge admin"
            >
              Admin
            </span>
          </div>
        </div>

        <!-- Device status -->
        <div class="device-status">
          <IconMicOn 
            size="20"
            v-if="participant.microphoneStatus === DeviceStatus.On" 
            class="icon-on"
          />
          <IconMicOff size="20" v-else class="icon-off" />
          <IconCameraOn
            size="20"
            v-if="participant.cameraStatus === DeviceStatus.On" 
            class="icon-on"
          />
          <IconCameraOff size="20" v-else class="icon-off" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { IconRefresh, IconMicOn, IconMicOff, IconCameraOn, IconCameraOff } from '@tencentcloud/uikit-base-component-vue3';
import { 
  useRoomParticipantState,
  Avatar,
  RoomParticipantRole,
  DeviceStatus
} from 'tuikit-atomicx-vue3/room';

const { 
  localParticipant, 
  participantList,
  getParticipantList,
  transferOwner
} = useRoomParticipantState();

const handleRefresh = async () => {
  await getParticipantList({ cursor: '' });
};
</script>

<style scoped>
.participant-list {  display: flex;  flex-direction: column;  width: 400px;  height: 100%;  background-color: white;}.list-header {  display: flex;  align-items: center;  justify-content: space-between;  padding: 16px;  border-bottom: 1px solid #e0e0e0;}.list-header h3 {  margin: 0;  font-size: 16px;  font-weight: 600;}.list-content {  flex: 1;  overflow-y: auto;  padding: 8px;}.participant-item {  display: flex;  align-items: center;  gap: 12px;  padding: 12px;  border-radius: 8px;  transition: background-color 0.2s;}.participant-item:hover {  background-color: #f5f5f5;}.participant-item.is-local {  background-color: #e3f2fd;}.user-info {  flex: 1;  min-width: 0;}.user-name {  display: flex;  align-items: center;  gap: 6px;  font-size: 14px;  font-weight: 500;}.badge {  padding: 2px 6px;  background-color: #4caf50;  color: white;  border-radius: 3px;  font-size: 12px;}.role-badge {  padding: 2px 6px;  border-radius: 3px;  font-size: 12px;}.role-badge.owner {  background-color: #ff9800;  color: white;}.role-badge.admin {  background-color: #2196f3;  color: white;}.device-status {  display: flex;  gap: 8px;}.icon-on {  color: #4caf50;}.icon-off {  color: #999;}.action-button {  padding: 4px 8px;  background: transparent;  border: none;  cursor: pointer;  border-radius: 4px;  color: #666;}.action-button:hover {  background-color: #f0f0f0;}
</style>
```

### Step 3: Set User Custom Information

Developers can use the [updateParticipantMetaData](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateParticipantMetaData) interface to set custom data for users to **implement job titles**, **purchase status** and other capabilities. After updating, the `metaData` information will be maintained in the reactive data of the corresponding user in `participantList`.
``` typescript
<template>
  <div v-for="participant in participantList" :key="participant.userId">
    <span>{{ participant.userName }}</span>
    <span v-if="participant.metaData">
      {{ JSON.parse(participant.metaData).level }}
    </span>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { IconRefresh, IconMicOn, IconMicOff, IconCameraOn, IconCameraOff } from '@tencentcloud/uikit-base-component-vue3';
import { 
  useRoomParticipantState,
} from 'tuikit-atomicx-vue3/room';

const { 
  updateParticipantMetaData,
} = useRoomParticipantState();

await updateParticipantMetaData({
 userId: '', // Specify user id
 metaData: JSON.stringify({ level: 1 }),   // Business custom information
})
</script>
```

### Step 4: Manage Member Roles

Room owner can change user role to administrator through [setAdmin](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setAdmin) interface, and revoke user administrator status through [revokeAdmin](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#revokeAdmin) method.
``` typescript
const { setAdmin, revokeAdmin } = useRoomParticipantState();

// Set regular user as administrator
await setAdmin({ userId });

// Change administrator to regular user
await revokeAdmin({ userId });
```

### Step 5: Manage Member Media Devices

Room owner and administrators can remotely control other members' media devices through [closeParticipantDevice](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#closeParticipantDevice) interface to maintain a quiet and orderly meeting environment.
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { closeParticipantDevice } = useRoomParticipantState();

// Force close specified member's microphone
async function handleCloseUserMicrophone(userId: string) {
  await closeParticipantDevice({
    userId,
    deviceType: DeviceType.Microphone,
  });
}

// Force close specified member's camera
async function handleCloseUserCamera(userId: string) {
  await closeParticipantDevice({
    userId,
    deviceType: DeviceType.Camera,
  });
}
```

### Step 6: Enable Global State Management

Room owner and administrators can enable room global state management through [disableAllDevices](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#disableAllDevices) and [disableAllMessages](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#disableAllMessages) interfaces.

#### **Disable media devices of all regular users in the room**
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { closeParticipantDevice } = useRoomParticipantState();

// Force close all regular members' microphones
async function handleDisableAllMicrophone() {
  await disableAllDevices({
    deviceType: DeviceType.Microphone,
    disable: true,
  });
}

// Remove microphone restriction for all
async function handleEnableAllMicrophone() {
  await disableAllDevices({
    deviceType: DeviceType.Microphone,
    disable: false,
  });
}

// Force close all regular members' cameras
async function handleDisableAllCamera() {
  await disableAllDevices({
    deviceType: DeviceType.Camera,
    disable: true,
  });
}

// Remove camera restriction for all
async function handleEnableAllCamera() {
  await disableAllDevices({
    deviceType: DeviceType.Camera,
    disable: false,
  });
}
```

#### **Disable in-meeting chat for all regular users in the room**
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { disableAllMessages } = useRoomParticipantState();

// Force disable chat capability for all regular members
async function handleDisableAllMessage() {
  await disableAllMessages({
    disable: true,
  });
}
```

### **Step 7:** Monitor Member Speaking Status

Through [speakingUsers](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#speakingUsers), you can get real-time information about users currently speaking in the room and their volume levels.
``` typescript
const { speakingUsers } = useRoomParticipantState();

// Check if user is speaking
const isSpeaking = (userId: string) => speakingUsers.value.has(userId);
// Get user real-time volume (0-100)
const getVolume = (userId: string) => speakingUsers.value.get(userId) || 0;
```

### Step 8: Kick Out Room Members

Room owner and administrators can remove users from the room through [kickParticipant](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#kickParticipant) interface.
``` typescript
const { kickParticipant } = useRoomParticipantState();

// Kick user out of room
await kickParticipant({ userId });
```

### Step 9: Invite Others to Join Room

In real business scenarios, you may need to initiate call invitations to users not yet in the room. Atomicx provides complete call system support, please see In-Meeting Call.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useRoomParticipantState** | Contains user data in the room and user management interfaces. | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |

## Development Recommendations

**Permission Check:** In the UI layer, it's recommended to determine the current user's operation permissions through `participant.role`, hiding **kick out** or **mute** buttons for users without management permissions.
``` typescript
<script setup>
import { RoomParticipantRole } from 'tuikit-atomicx-vue3/room';

const { localParticipant } = useRoomParticipantState();

// Check if current user has admin permissions
const hasAdminPermission = computed(() => {
  return localParticipant.value.role === RoomParticipantRole.Owner ||
         localParticipant.value.role === RoomParticipantRole.Admin;
});
</script>

// Use in template
<template>
<button v-if="hasAdminPermission" @click="handleKick">Kick Out</button>
</template>
```
