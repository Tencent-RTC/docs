> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

The in-room chat feature allows members within a meeting to interact in real-time through text, emojis, and other forms. This feature is implemented based on the IM component capabilities of `tuikit-atomicx-vue3`, providing users with a convenient instant messaging experience.

## Use Cases

- **Online Seminars:** When speakers give video presentations, participants can ask questions or discuss through text.

- **Remote Collaboration:** Team members can send text, images, files, etc. during communication.

- **Live Teaching:** Students can send bullet comments or questions in the chat area, and teachers can view and answer in real-time, enhancing classroom interaction.

## Prerequisites

Before integrating this feature, please ensure the following conditions are met:
- **User Status:** The user has completed login authentication through useLoginState, refer to Integration Overview, and is already in a room as the room owner or member, refer to Room Management.

- **Environment Dependencies:** The project has correctly installed and imported `tuikit-atomicx-vue3`.

## Implementing In-Room Chat Feature

This feature provides two integration solutions. You can choose the most suitable one according to business needs:
- **Solution 1 (Recommended):** Quick integration using UI components. Directly import the message list component ([MessageList](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageList)) and message input component ([MessageInput](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageInput)) provided by `tuikit-atomicx-vue3`, with the lowest development cost.

- **Solution 2 (Advanced):** Custom integration using underlying API. Based on atomicx-core SDK API [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) and [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) state hooks to implement UI and interaction logic yourself, with the highest flexibility.

   

   In-room chat

## Solution 1: Quick Integration Using UI Components

`tuikit-atomicx-vue3` provides two core components:
- [**MessageList**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageList)**:** Message list component that displays chat history and supports operations such as copying, recalling, and deleting messages.

- [**MessageInput**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageInput)**:** Message input component that supports sending text, emojis, images, and files, and can be automatically disabled based on mute status.

### Step 1: Data Initialization

After the user successfully joins a room, you need to call the [setActiveConversation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setActiveConversation) interface to initialize chat data. It is recommended to listen for changes in [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) to handle this automatically.

> **Note:**
> - `GROUP` is a fixed prefix, the `setActiveConversation` parameter must be ``GROUP${roomId}``, otherwise messages cannot be sent or received normally.
> - `GROUP` is the fixed prefix for IM SDK group conversations (refer to [IM Conversation Types](https://web.sdk.qcloud.com/im/doc/zh-cn/Conversation.html)). Room chat is based on IM groups, so the conversation ID must be in the ``GROUP${roomId}`` format.

``` typescript
import { watch } from 'vue';
import { useConversationListState } from 'tuikit-atomicx-vue3/chat';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { setActiveConversation } = useConversationListState();

// Listen for room ID changes and automatically switch to the corresponding IM group conversation
watch(() => currentRoom.value?.roomId, (roomId) => {
  if (roomId) {
    // Key step: Set the current active conversation ID, the rule is "GROUP" + room ID
    setActiveConversation(`GROUP${roomId}`);
  }
}, { immediate: true });
```

### Step 2: Use Components

In your page, you can directly combine these two components. Also combine [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) to get the current user's mute status and control the availability of the input box.

> **Note:**
> 

> All TUIKit components must be wrapped inside UIKitProvider, otherwise they will not be able to obtain context dependencies, causing style anomalies.
> 

``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="room-chat-container">
      <!-- Message list component -->
      <!-- messageActionList: Configure operations supported by long-pressing messages, such as copy, recall, delete -->
      <MessageList
        class="message-list"
        :messageActionList="['copy', 'recall', 'delete']"
      />

      <!-- Message input component -->
      <!-- disabled: Disable input box when user is muted -->
      <!-- hideSendButton: Optional, hide send button, use enter to send -->
      <MessageInput
        class="message-input"
        :placeholder="inputPlaceholder"
        :disabled="isMessageDisabled"
        hideSendButton
      />
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
import { MessageInput, MessageList } from 'tuikit-atomicx-vue3/chat';
import { useRoomParticipantState } from 'tuikit-atomicx-vue3/room';

const { localParticipant } = useRoomParticipantState();

// Get current user's mute status, isMessageDisabled is synchronized in real-time by the room backend, no need for frontend active polling, frontend only needs to bind this state to automatically respond to mute changes.
const isMessageDisabled = computed(() => localParticipant.value?.isMessageDisabled);

// Dynamically display placeholder prompt based on status
const inputPlaceholder = computed(() => 
  isMessageDisabled.value ? 'You have been muted' : 'Please enter message...'
);
</script>

<style scoped>
.room-chat-container{display:flex;flex-direction:column;height:100%;padding:8px;gap:8px}.message-list{flex:1;overflow:hidden}.message-input{flex-shrink:0;border:1px solid #e0e0e0;border-radius:8px}
</style>
```

### Step 3: Handle Unread Message Count (Optional)

If the chat window is usually collapsed or closed, you need to listen for changes in [messageList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#messageList) to count unread messages and provide prompts on the UI (such as chat icon badges).
``` typescript
import { ref, watch } from 'vue';
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { messageList } = useMessageListState();
const unreadCount = ref(0);
const isChatWindowOpen = ref(false); // Need to maintain this state according to your business logic

watch(() => messageList.value?.length, (newLength, oldLength) => {
  // If this is the first load or data is empty, do not count
  if (!newLength || newLength === 0) return;

  // Only increase unread count when chat window is closed and new messages are received
  if (!isChatWindowOpen.value && newLength > (oldLength || 0)) {
    unreadCount.value += (newLength - (oldLength || 0));
  }
});

// Clear unread count when user opens chat window
const openChatWindow = () => {
  isChatWindowOpen.value = true;
  unreadCount.value = 0;
};
```

## Solution 2: Custom Integration Using Underlying API

This section mainly introduces how to implement a custom chat interface through atomicx-core SDK API [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) and [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) interfaces.

> **Note:**
> 

> `MessageInputState` supports integration with `@tiptap/vue-3` editor by default. If you only need a simple text input box (e.g., `<input>` or `<textarea>`), please **ignore** `setEditorInstance`, `setContent` and other APIs bound to editor instances, and directly use `updateRawValue` and `sendMessage`.
> 

#### Step 1: Data Initialization

After the user successfully joins a room, you need to call the [setActiveConversation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setActiveConversation) interface to initialize chat data. It is recommended to listen for changes in [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) to handle this automatically.

> **Note:**
> - `GROUP` is a fixed prefix, the `setActiveConversation` parameter must be ``GROUP${roomId}``, otherwise messages cannot be sent or received normally.
> - When switching rooms or exiting a room, it is recommended to reset or clear the active conversation to avoid sending messages to the wrong conversation.

``` typescript
import { watch } from 'vue';
import { useConversationListState } from 'tuikit-atomicx-vue3/chat';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { setActiveConversation } = useConversationListState();

// Listen for room ID changes and automatically switch to the corresponding IM group conversation
watch(() => currentRoom.value?.roomId, (roomId) => {
  if (roomId) {
    // Key step: Set the current active conversation ID, the rule is "GROUP" + room ID
    setActiveConversation(`GROUP${roomId}`);
  }
}, { immediate: true });
```

#### Step 2: Get Message List

Use [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) to get the message list of the current conversation. `messageList` is reactive data and will automatically update with newly received messages.
``` typescript
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { 
  messageList,      // Reactive message list
  loadMoreOlderMessage, // Method to load more history messages
  hasMoreOlderMessage,   // Whether there are more history messages
} = useMessageListState();

// Example: Render message list
// <div v-for="msg in messageList" :key="msg.ID">
//   {{ msg.payload.text }}
// </div>
```

#### Step 3: Send Messages

Use [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) to manage input box content and send messages.
``` typescript
import { useMessageInputState } from 'tuikit-atomicx-vue3/chat';

const { 
  inputRawValue,  // Reactive data: current input content
  updateRawValue, // Method: Update input content (will automatically trigger "other party is typing" status)
  sendMessage     // Method: Send message
} = useMessageInputState();

const handleSend = async () => {
  // Validate non-empty
  if (!inputRawValue.value) return;
  
  try {
    // 1. Send message
    // Method A: Send the content of current inputRawValue
    await sendMessage();
    
    // Method B: Directly send specified text (not dependent on inputRawValue)
    // await sendMessage('Hello World');

    console.log('Sent successfully');
    
    // 2. Clear input box after successful send
    // Key point: In non-rich text mode, must use updateRawValue('') to clear
    // Do not use setContent(''), this method only takes effect after binding an editor instance
    updateRawValue('');
  } catch (error) {
    console.error('Send failed', error);
  }
};

// Example: Bind to native input box
// <input 
//   :value="inputRawValue" 
//   @input="(e) => updateRawValue(e.target.value)" 
//   @keyup.enter="handleSend" 
// />
```

#### Step 4: Load History Messages

Use the [loadMoreOlderMessage](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) method to paginate and pull history messages. Usually triggered when scrolling to the top of the list.

> **Note:**
> 

> After loading history messages, you usually need to manually adjust the scroll position to keep the current viewing perspective from jumping.
> 

``` typescript
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { 
  hasMoreOlderMessage,
  loadMoreOlderMessage
} = useMessageListState();

const loadHistory = async () => {
  if (hasMoreOlderMessage.value) {
    // Record scroll height before loading
    // const previousHeight = scrollContainer.value.scrollHeight;
    
    await loadMoreOlderMessage();
    
    // Restore scroll position after DOM update
    // nextTick(() => {
    //   const currentHeight = scrollContainer.value.scrollHeight;
    //   scrollContainer.value.scrollTop += (currentHeight - previousHeight);
    // });
  }
};
```

#### Step 5: Advanced Features (Read Receipts)

If you need read receipt functionality, you can use [setEnableReadReceipt](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setEnableReadReceipt).
``` typescript
const { 
  setEnableReadReceipt, // Enable/disable read receipts
} = useMessageListState();

// Enable read receipts
setEnableReadReceipt(true);
```

## Development Notes

- **Mute Status Synchronization**

   `localParticipant.isMessageDisabled` is controlled by room administrators. When administrators enable "mute all" or individually mute a user, this property will automatically update. Please be sure to bind this property to the input box's `disabled` state to restrict user speaking at the frontend level.

- **Conversation Switch Timing**

   Ensure `setActiveConversation` is called after successfully entering the room and when `roomId` has a value. If called before entering the room, it may fail because the SDK has not finished initialization or is not logged in.

- **Container Layout Requirements (Important)**

   The message list component internally handles scrolling logic, so **the parent container must have a fixed height** (e.g., `height: 100%` or specific pixel value) and set `overflow: hidden`, otherwise the list will stretch infinitely and cannot scroll.

- **Message Type Handling**

   `messageList` contains multiple types of messages (text, images, files, etc.). When customizing rendering, you need to distinguish and handle according to `msg.type`.

- **Input State Management**

   Using `updateRawValue` to update input content will automatically trigger "other party is typing" status notification (if supported). Using only `inputRawValue.value = 'xxx'` may not trigger this status.

- **History Message Scroll Maintenance**

   In custom development, after calling `loadMoreOlderMessage` to load history messages, the DOM height will increase. To prevent view jumping, you need to record `scrollHeight` before loading, and adjust `scrollTop` after loading (`nextTick`) by calculating the height difference.

- **New Message Scroll Strategy**

   It is recommended to implement "smart scroll" logic: when users are at the bottom of the list, automatically scroll to the bottom when receiving new messages; when users are browsing history messages, keep the current position unchanged and display a "new message" prompt bubble.

- **Conversation ID Format Requirements**

   When calling `setActiveConversation`, you must use the ``GROUP${roomId}`` format (GROUP is a fixed prefix). This is the standard format for IM SDK group conversations, cannot be customized or prefix omitted, otherwise messages cannot be sent or received normally.

## Common Questions

**Failed to switch to group conversation or message list is empty?**

Ensure you call `setActiveConversation('GROUP' + roomId)` after successful login and room entry; not logged in or empty roomId will cause failure.

**Cannot receive messages or list is blank?**

Check if the current active conversation is correct (`GROUP` prefix + roomId), and whether network/authentication is normal; if necessary, reset the active conversation `setActiveConversation` to refresh.

**Unread count not clearing?**

You need to manually maintain unread count logic. Update count when opening chat window, or when receiving new messages but window is closed.

**Can still click send when muted?**

Please ensure frontend UI disables send button and input box according to `isMessageDisabled` status. Although the server will also intercept, frontend disable provides better experience.

**Custom avatar/nickname display?**

Can use `MessageList` slot or custom Message component, prioritize displaying `nameCard` within the room, and customize avatar styles, etc.

**How to send images or files in custom solution?**

The `sendMessage` method supports passing an `InputContent[]` array. You can construct an array containing `type: 'image'` or `type: 'file'` along with the corresponding `file` object to implement rich media message sending.
``` typescript
import { useMessageInputState } from 'tuikit-atomicx-vue3/chat';

const { sendMessage } = useMessageInputState();

// Example: Send image
// const file = ...; // File object obtained from input type="file"
await sendMessage([{ type: 'image', file }]);
```

**How to modify default styles of UI components?**

Component class names in `tuikit-atomicx-vue3` are usually stable. You can use CSS deep selectors (e.g., Vue's `:deep()`) to override default styles. For example `.message-list :deep(.tui-message-bubble) { background-color: #f0f0f0; }`.

## **Sample Project**

Tencent Cloud provides a sample project [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) on GitHub, which you can refer to for implementing complete RoomKit functionality.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useRoomState** | Room state management (create, join, schedule, etc.) | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) |
| **useRoomParticipantState** | Room participant management (member list, permission control) | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |
| **useConversationListState** | Conversation list management | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-ConversationListState) |
| **useMessageListState** | Message list management | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) |
| **useMessageInputState** | Message input management | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) |
