> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

The in-room call feature allows users within a meeting (inviter) to send real-time invitations to users outside the room (invitees). This feature is implemented based on `Atomicx State Hooks` and helps developers quickly build interactive scenarios with proactive invitation capabilities.

## Use Cases

- **Collaborative Office:** During remote meetings, if cross-departmental decisions are involved, you can invite external experts to join the meeting with one click, shortening communication paths.

- **Live Teaching:** During the teaching process, if teachers find that some students are absent, they can directly invite them to join the class.

- **Internet Medical Consultation:** During case analysis, the primary physician can invite senior doctors to enter the virtual consultation room for multi-party consultation through the call feature, or doctors can call patients in the waiting room for consultations.

## Prerequisites

Before integrating this feature, please ensure the following conditions are met:
- **Inviter (User A):** The user has completed login authentication through `useLoginState`, refer to Integration Overview, and is already in a room as the room owner or member, refer to Room Lifecycle.

- **Invitee (User B):** The user has completed login authentication through `useLoginState`, refer to Integration Overview, to receive signaling events.

- **Environment Dependencies:** The project has correctly installed and imported `tuikit-atomicx-vue3`.

## Implementing In-Room Call Feature

The core process for implementing in-room calls is as follows:

In-room call sequence diagram

### Step 1: Initiate Call (Inviter)

The inviter calls the [callUserToRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#callUserToRoom) interface to initiate an invitation. Please ensure the inviter is currently in the room (i.e., [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) is not empty), otherwise the call cannot be initiated.
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom, callUserToRoom } = useRoomState();

const inviteUser = async () => {
  // In-room calls must be associated with an existing roomId, so check the current user's room status first
  if (!currentRoom.value) {
    console.warn('Please enter a room first before initiating an invitation');
    return;
  }

  try {
    const resultMap = await callUserToRoom({
      roomId: currentRoom.value.roomId, // Room ID the invitee will join
      userIdList: ['user1', 'user2'],    // Target user ID array, supports single or multiple users
      timeout: 60,                        // Invitation validity period (seconds), both parties will trigger onCallTimeout after timeout
      // extensionInfo can be used to pass business custom data, such as invitation type, additional messages, etc.
      extensionInfo: JSON.stringify({ type: 'emergency', priority: 'high' }) 
    });
    
    // resultMap returns the status corresponding to each userId, such as Success, AlreadyInRoom (already in room) or AlreadyInCalling (in call)
    console.log('Invitation sent successfully, detailed status map:', resultMap);
  } catch (error) {
    console.error('Failed to send call request, please check network or login status:', error);
  }
};
```

### Step 2: Listen for Call Events (Invitee)

The invitee needs to listen for the `onCallReceived` event. It is recommended to implement this in long-lived components such as `App.vue` to ensure the user can respond regardless of which page they are on.
``` typescript
import { ref, onMounted, onUnmounted } from 'vue';
import { useRoomState, RoomEvent } from 'tuikit-atomicx-vue3/room';

const { 
  subscribeEvent, 
  unsubscribeEvent, 
  acceptCall, 
  rejectCall, 
  joinRoom, 
  currentRoom 
} = useRoomState();

const showInviteDialog = ref(false); // Control UI dialog visibility
const currentCallInfo = ref(null);   // Cache the received invitation context

const handleCallReceived = async ({ roomInfo, call, extensionInfo }) => {
  // Conflict handling: If the user is currently in a meeting, according to business policy, usually should directly reject new invitations to prevent interference
  if (currentRoom.value?.roomId) {
    await rejectCall({ roomId: roomInfo.roomId });
    return;
  }

  // Record invitation details for displaying inviter name or business transparent data in UI
  currentCallInfo.value = { roomInfo, call };
  showInviteDialog.value = true;
};

// Accept invitation logic
const onUserAccept = async () => {
  if (!currentCallInfo.value) return;
  const { roomId } = currentCallInfo.value.roomInfo;

  try {
    // Key process A: Respond to signaling. Inform the inviter that it has been accepted so they can update UI (e.g., display "Other party answered")
    await acceptCall({ roomId });
    
    // Key process B: Execute room entry. Only by calling joinRoom will the audio and video push-pull stream link be truly opened
    await joinRoom({ roomId }); 
  } catch (error) {
    console.error('Room entry operation failed after accepting call:', error);
  } finally {
    closeDialog();
  }
};

// Reject invitation logic
const onUserReject = async () => {
  if (currentCallInfo.value) {
    // Inform the inviter that it has been rejected, the inviter will receive onCallRejected event
    await rejectCall({ roomId: currentCallInfo.value.roomInfo.roomId });
  }
  closeDialog();
};

const closeDialog = () => {
  showInviteDialog.value = false;
  currentCallInfo.value = null;
};

// Event full lifecycle management: Ensure listening on component mount and removal on destroy to prevent event overflow
onMounted(() => {
  subscribeEvent(RoomEvent.onCallReceived, handleCallReceived);
  
  // Listen for various exceptions/sync events that cause invitation to end, ensuring UI dialog can close in time
  subscribeEvent(RoomEvent.onCallCancelled, closeDialog); // Inviter cancelled the call
  subscribeEvent(RoomEvent.onCallTimeout, closeDialog);   // Call timed out without response
  subscribeEvent(RoomEvent.onCallHandledByOtherDevice, closeDialog); // User already handled the call on another device
});

onUnmounted(() => {
  // Remove all listeners to avoid continuing to execute handleCallReceived after component destruction, causing memory leaks or errors
  unsubscribeEvent(RoomEvent.onCallReceived, handleCallReceived);
  unsubscribeEvent(RoomEvent.onCallCancelled, closeDialog);
  unsubscribeEvent(RoomEvent.onCallTimeout, closeDialog);
  unsubscribeEvent(RoomEvent.onCallHandledByOtherDevice, closeDialog);
});
```

## Practical Tutorial: Improving Call Connection Rate

To achieve higher call connection rates in business scenarios, in addition to implementing basic functions, it is recommended to refer to the following strategies to optimize user experience:

### 1. Enhanced Call Awareness (Avoid Users Missing Calls)

Due to Web characteristics, browser mute or page running in background state may cause users to miss calls.
- **Visual Reminder**: Use [Page Visibility API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) to detect page visibility status. If the page is in the background, you can implement title bar flashing prompts by cyclically modifying `document.title` (e.g.: "【New Invitation】UserA invites you to the meeting...") to attract user attention.

- **Audio Reminder**: Play high-priority alert ringtone when the `onCallReceived` event is triggered. Note: Must comply with browser's [Autoplay Policy](https://developer.mozilla.org/zh-CN/docs/Web/Media/Guides/Autoplay), most browsers will restrict audio autoplay in the absence of user interaction (such as clicks, touches, or key presses).

### 2. Enrich Invitation Context (Improve Answering Willingness)

Invitations lacking clear intent are easily rejected by users. It is recommended to fully utilize the `extensionInfo` field to pass key business information to improve users' willingness to answer.
- **Pass Business Context**: Use `extensionInfo` to transparently pass business fields such as `reason` (e.g.: "Project emergency review"), `level` (e.g.: "P0 troubleshooting"), etc., to inform users of the urgency and importance of the call.

### 3. Multi-Channel Reach Backup (Ensure Message Delivery)

Limited by Web mechanisms, when the browser process ends, real-time signaling cannot be received. It is recommended to adopt the following alternative solutions to ensure reach:
- **Browser System Notifications**: Guide users to authorize [Web Notification API](https://developer.mozilla.org/en-US/docs/Web/API/Notification). As long as the browser process is not closed (including minimized or in background tabs), OS-level pop-up notifications can be triggered to effectively remind users.

- **Multi-Channel Reach**: It is recommended to combine backend business logic to send meeting links through **SMS**, **Email**, or **Enterprise WeChat** and other external channels when detecting that users are offline or timeout without response, achieving all-around reach.

### 4. Intelligent Retry Mechanism (Solve "Room Entry Failed After Accepting Invitation")

In weak network or other unstable environments, calling `joinRoom` after users click accept invitation may experience occasional failures.
- **Automatic Retry**: It is recommended to introduce 2-3 automatic retry mechanisms (recommended combined with exponential backoff strategy) in the exception handling logic (`catch`) of `onUserAccept` to maximize ensuring users can successfully join the meeting after clicking "accept".

## Development Notes

1. **Accepting Invitation Does Not Equal Entering Room**

   `acceptCall` is only a signaling layer reply (informing the other party that the call has been answered). Users must manually call `await joinRoom({ roomId })` immediately after calling `acceptCall` to truly enter the audio and video room.

2. **Lifecycle Management**

   Please be sure to call `unsubscribeEvent` in the component destruction cycle (`onUnmounted`). If listeners are not cleaned up, when users switch pages, listeners from old pages will remain in memory, causing one invitation to pop up multiple windows or logic to execute repeatedly.

3. **Timeout Settings**

   If the `timeout` parameter is not set, in some network exceptions or program crash situations, invitations may remain in a suspended state for a long time, making it impossible to initiate another call to that user. It is recommended to set it to 30-60 seconds.

## Common Questions

1. **How to pass custom parameters when calling (e.g., "Emergency meeting from XX")?**

   Use the `extensionInfo` parameter of `callUserToRoom` to pass a JSON string. The invitee can parse the string to obtain business information in the `extensionInfo` field of the `onCallReceived` event.

2. **If the invitee is not online, can they receive the invitation after coming online?**

   No. The invitation event is an instantaneous signaling action, and the SDK currently does not resend call notifications received while offline.

3. **Can issued invitations be cancelled?**

   Yes. Call `cancelCall({ roomId, userIdList })` to cancel. At this time, the invitee will receive the `onCallCancelled` event, and you need to listen to this event to close the UI dialog on the invitee side.

4. **What to do if room entry fails after accepting?**

   Check if you have logged in to `TUIRoomEngine`, retry `joinRoom({ roomId })` and capture error prompts to the user; if necessary, provide a "retry" button on the UI.

5. **How to prompt when the other party is busy or already in another room?**

   If `AlreadyInRoom` / `AlreadyInCalling` appears in the return value `resultMap` of `callUserToRoom`, you should prompt on the inviter UI "The other party is busy/already in a meeting" and decide whether to retry later.

6. **Dialog not closing after timeout/cancellation?**

   Make sure to listen to `onCallTimeout`, `onCallCancelled`, `onCallHandledByOtherDevice`, and uniformly close the dialog and clear current invitation data in the callback.

7. **How to handle simultaneous ringing on multiple devices?**

   After accepting/rejecting on any device, other devices will receive `onCallHandledByOtherDevice`, and you need to close dialogs on other ends to avoid repeated processing.

8. **How should extensionInfo be agreed upon?**

   It is recommended to use a JSON string and agree on fields (e.g., `type`, `priority`, `from`), parse and display friendly text (e.g., "Emergency meeting from XX") in `handleCallReceived`.

## **Sample Project**

Tencent Cloud provides a sample project [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) on GitHub, which you can refer to for implementing complete RoomKit functionality.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useRoomState** | Room state management (create, join, schedule, etc.) | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) |
