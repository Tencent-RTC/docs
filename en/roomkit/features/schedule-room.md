> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

The scheduled room feature allows users to plan meeting times, topics, and entry passwords in advance. Through this feature, users can create future meeting plans, generate exclusive meeting numbers and entry links, making it convenient to notify participants in advance.

## Use Cases

- **Corporate Regular Meetings:** Schedule weekly and monthly meetings in advance to ensure participants receive notifications ahead of time.

- **Online Class Scheduling:** Teachers can set up course schedules for the week in advance.

- **Remote Interview Appointments:** HR can schedule specific time slots for interviewees and interviewers.

## Prerequisites

- **User Status:** The user has completed login authentication through useLoginState, refer to Integration Overview, and must be in **logged in** status to initiate scheduling or view scheduled list.

- **Environment Dependencies:** The project has imported `tuikit-atomicx-vue3`.

## Implementing Scheduled Room Feature

This feature provides two integration solutions. You can choose the most suitable one according to business needs:
- **Solution 1 (Recommended):** Quick integration using UI components. Directly import the schedule room panel ([ScheduleRoomPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduleRoomPanel.vue)) and scheduled room list component ([ScheduledRoomList](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduledRoomList.vue)) provided by `tuikit-atomicx-vue3`, with the lowest development cost.

- **Solution 2 (Advanced):** Custom integration using underlying API. Based on atomicx-core SDK API `useRoomState` state hook to implement UI and interaction logic yourself, with the highest flexibility.

   

## Solution 1: Quick Integration Using UI Components

`tuikit-atomicx-vue3` provides two core components:
- [**ScheduleRoomPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduleRoomPanel.vue)**:** Configuration panel for scheduling meetings, including time selection, member invitation, and other functions.

- [**ScheduledRoomList**](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduledRoomList.vue)**:** Displays the current user's scheduled meeting list, supports clicking to join, modify, and cancel schedules.

### Step 1: Import Components

``` typescript
import { ScheduleRoomPanel, ScheduledRoomList } from 'tuikit-atomicx-vue3/room';
```

### Step 2: Use Components

In your page, you can directly combine these two components.
``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="schedule-container">
      <!-- Schedule button, displays schedule dialog after clicking -->
      <button @click="showSchedulePanel = true">
        Schedule Meeting
      </button>

      <!-- Schedule list component -->
      <div class="list-container">
        <!-- Listen to join-room event to handle joining logic -->
        <ScheduledRoomList @join-room="handleJoinRoom" />
      </div>

      <!-- Schedule panel dialog -->
      <!-- It is recommended to wrap it in a Dialog or Modal component -->
      <div v-if="showSchedulePanel" class="modal-mask">
        <div class="modal-content">
          <ScheduleRoomPanel
            @confirm="handleScheduleConfirm"
            @cancel="showSchedulePanel = false"
          />
        </div>
      </div>
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
import { ScheduleRoomPanel, ScheduledRoomList, useRoomState } from 'tuikit-atomicx-vue3/room';

const { joinRoom } = useRoomState();

const showSchedulePanel = ref(false);

const handleScheduleConfirm = (roomId: string, options: Record<string, any>) => {
  console.log('Schedule successful', roomId, options);
  showSchedulePanel.value = false;
  // Optional: Display invitation dialog or copy link
};

const handleJoinRoom = async (roomInfo: { roomId: string }) => {
  console.log('Click to join', roomInfo.roomId);
  await joinRoom(roomInfo.roomId); 
};
</script>

<style scoped>
.schedule-container{padding:24px;max-width:1000px;margin:0 auto;font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,'Helvetica Neue',Arial,sans-serif}button{background-color:#006eff;color:#fff;border:none;padding:10px 20px;border-radius:4px;cursor:pointer;font-size:14px;font-weight:500;transition:background-color .2s;margin-bottom:24px}button:hover{background-color:#0056cc}.list-container{background:#fff;border-radius:8px;box-shadow:0 2px 8px rgba(0,0,0,.05);padding:16px}.modal-mask{position:fixed;top:0;left:0;right:0;bottom:0;background-color:rgba(0,0,0,.4);backdrop-filter:blur(4px);display:flex;align-items:center;justify-content:center;z-index:1000}.modal-content{background-color:#fff;border-radius:8px;box-shadow:0 4px 16px rgba(0,0,0,.1);padding:24px;width:100%;max-width:480px}
</style>
```

## Solution 2: Custom Integration Using Underlying API

This section mainly introduces how to implement the core logic of scheduled rooms through atomicx-core SDK API [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) interface.

Scheduled room sequence diagram

### Step 1: Initiate Schedule

Use the [scheduleRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#scheduleRoom) method to create a new scheduled meeting. You need to specify the meeting's start time, end time, room name, and other information.
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { scheduleRoom } = useRoomState();

const createSchedule = async () => {
  try {
    // roomId restriction: string type, required parameter, recommended to generate randomly
    const roomId = '123456'; 
    
    // Note: Timestamp unit must be **seconds** (Date.getTime() gets milliseconds, need to divide by 1000)
    const startTime = Math.floor(new Date().getTime() / 1000) + 3600; // Start after 1 hour
    const duration = 1800; // 30 minutes

    const options = {
      roomName: 'Product Requirements Review Meeting',
      scheduleStartTime: startTime, // Unit: seconds
      scheduleEndTime: startTime + duration, // Unit: seconds
      scheduleAttendees: ['userA', 'userB'], // Invited participant ID list
      password: '123', // Optional: Set entry password
      isAllMicrophoneDisabled: false, // Optional: Mute all microphones
      isAllCameraDisabled: false,     // Optional: Disable all cameras
    };

    await scheduleRoom({ roomId, options });
    console.log('Schedule successful', roomId);
  } catch (error) {
    console.error('Schedule failed', error);
  }
};
```

### Step 2: Get Scheduled List

Use the [getScheduledRoomList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#getScheduledRoomList) method to retrieve the current user's scheduled meeting list. The list data will be reactively updated to the [scheduledRoomList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#scheduledRoomList) state.
``` typescript
import { onMounted, watch } from 'vue';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { 
  scheduledRoomList,      // Reactive data: scheduled meeting list
  getScheduledRoomList    // Method: Retrieve list
} = useRoomState();

// Initialize and load list
onMounted(async () => {
  // cursor indicates where to start pulling data, empty string means start from beginning
  // getScheduledRoomList return value includes new cursor
  const { cursor } = await getScheduledRoomList({ cursor: '' }); 
  // If cursor is not empty, it means there is more data, can continue calling to retrieve
});

// Listen for list changes and update UI in real-time
watch(scheduledRoomList, (list) => {
  console.log('Current scheduled list:', list);
});
```

### Step 3: Modify Schedule

If you need to adjust the time or topic of a scheduled meeting, you can use the [updateScheduledRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateScheduledRoom) method.
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { updateScheduledRoom } = useRoomState();

const updateRoom = async (roomId: string) => {
  try {
    const newStartTime = Math.floor(new Date().getTime() / 1000) + 7200; // Postpone by 2 hours
    const options = {
      roomName: 'Product Requirements Review Meeting (Rescheduled)',
      scheduleStartTime: newStartTime,
      scheduleEndTime: newStartTime + 1800,
    };

    await updateScheduledRoom({ roomId, options });
    console.log('Modification successful');
  } catch (error) {
    console.error('Modification failed', error);
  }
};
```

### Step 4: Cancel Schedule

If you need to cancel a scheduled meeting, you can call the [cancelScheduledRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cancelScheduledRoom) method.
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { cancelScheduledRoom } = useRoomState();

const cancelRoom = async (roomId: string) => {
  try {
    await cancelScheduledRoom({ roomId });
    console.log('Cancellation successful');
    // After successful cancellation, scheduledRoomList will automatically update, no need to manually retrieve again
  } catch (error) {
    console.error('Cancellation failed', error);
  }
};
```

### Step 5: Handle Entry Password

For scheduled meetings with passwords, when calling the [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) interface to join, you need to catch authentication failure errors and guide users to enter the password.
``` typescript
import { useRoomState, TUIErrorCode } from 'tuikit-atomicx-vue3/room';

const { joinRoom } = useRoomState();

const enterRoom = async (roomId: string, password?: string) => {
  try {
    await joinRoom({ roomId, password });
  } catch (error: any) {
    // Catch specific error code, TUIErrorCode.ERR_NEED_PASSWORD (-2010)
    if (error.code === TUIErrorCode.ERR_NEED_PASSWORD) {
      console.log('This room requires a password, please guide user to enter');
      // 1. Pop up password input box
      // 2. Get user entered password inputPassword
      // 3. Re-call enterRoom(roomId, inputPassword)
    } else if (error.code === TUIErrorCode.ERR_WRONG_PASSWORD) {
      console.error('Incorrect password, please re-enter');
    } else {
      console.error('Join failed', error);
    }
  }
};
```

### Step 6: Listen for Schedule Events

In addition to passively responding to data changes, you can also listen to specific schedule events to handle business logic, such as popping up reminders when meetings are about to start.
``` typescript
import { onMounted, onUnmounted } from 'vue';
import { useRoomState, RoomEvent } from 'tuikit-atomicx-vue3/room';

const { subscribeEvent, unsubscribeEvent } = useRoomState();

// Meeting starting soon notification (default is 5 minutes before start)
const handleRoomStartingSoon = (info: { roomInfo: any }) => {
  console.log('Meeting starting soon:', info.roomInfo.roomName);
  // Can implement Toast reminder or system notification here
};

// Meeting cancelled notification
const handleRoomCancelled = (info: { roomInfo: any; operateUser: any }) => {
  console.log(`Meeting ${info.roomInfo.roomName} has been cancelled by ${info.operateUser.userName}`);
};

// Received new meeting invitation notification
const handleRoomAdded = (info: { roomInfo: any }) => {
  console.log('Received new meeting invitation:', info.roomInfo.roomName);
};

// Removed from meeting notification
const handleRemovedFromRoom = (info: { roomInfo: any; operateUser: any }) => {
  console.log(`You have been removed from meeting ${info.roomInfo.roomName} by ${info.operateUser.userName}`);
};

onMounted(() => {
  // Register event listeners
  subscribeEvent(RoomEvent.onScheduledRoomStartingSoon, handleRoomStartingSoon);
  subscribeEvent(RoomEvent.onScheduledRoomCancelled, handleRoomCancelled);
  subscribeEvent(RoomEvent.onAddedToScheduledRoom, handleRoomAdded);
  subscribeEvent(RoomEvent.onRemovedFromScheduledRoom, handleRemovedFromRoom);
});

onUnmounted(() => {
  // Remove event listeners
  unsubscribeEvent(RoomEvent.onScheduledRoomStartingSoon, handleRoomStartingSoon);
  unsubscribeEvent(RoomEvent.onScheduledRoomCancelled, handleRoomCancelled);
  unsubscribeEvent(RoomEvent.onAddedToScheduledRoom, handleRoomAdded);
  unsubscribeEvent(RoomEvent.onRemovedFromScheduledRoom, handleRemovedFromRoom);
});
```

## Development Notes

1. **Timestamp Unit**: All time parameters involved in the SDK (e.g., `scheduleStartTime`, `scheduleEndTime`) are in **seconds**, not milliseconds as in JavaScript's `Date` object by default. Please make sure to perform `/ 1000` operation before passing parameters.

2. **Style Import**: When using UI components, make sure to import `UIKitProvider`, otherwise component styles will not render properly.

3. **RoomID Recommendation**: Although `scheduleRoom` allows the frontend to pass in `roomId`, to avoid ID conflicts, it is recommended that this ID be generated by the business backend and ensure global uniqueness.

4. **Pagination Handling**: The scheduled list may be very long, and the `getScheduledRoomList` interface supports paginated retrieval. Please pay attention to the `cursor` field in the return value. If it is not empty, it means there is more data.

## Common Questions

1. **Schedule successful but list not updating?**

   Please confirm that you are logged in and have called `getScheduledRoomList` on the first screen (e.g., `cursor: ''`). If the returned `cursor` is not empty, you need to continue retrieving; the list is reactive and UI will synchronize after updates.

2. `joinRoom`** how to pass parameters?**

   It is recommended to use object parameters uniformly: `joinRoom({ roomId, password })`, avoid directly passing strings which may cause type mismatch or limit subsequent extensions.

3. **Using milliseconds for timestamp causes appointment failure?**

   All time-related parameters must be in seconds, `Date.now()` needs `/ 1000`, otherwise it will return time conflict error.

4. **How to handle rooms with passwords?**

   Catch `TUIErrorCode.ERR_NEED_PASSWORD` / `ERR_WRONG_PASSWORD`, pop up password input box, re-call `joinRoom({ roomId, password })`; after multiple errors, you can prompt to contact room owner to reset.

5. **roomId conflict or duplication?**

   It is recommended that roomId be generated by the backend to ensure global uniqueness; if generated by the frontend, please check for conflicts before scheduling and have a retry strategy.

6. **How to continue paginated retrieval?**

   When the `cursor` returned by `getScheduledRoomList` is not empty, continue passing it in; empty means all data has been retrieved.

## **Sample Project**

Tencent Cloud provides a sample project [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) on GitHub, which you can refer to for implementing complete RoomKit functionality.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useRoomState** | Room state management (create, join, schedule, etc.) | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) |
