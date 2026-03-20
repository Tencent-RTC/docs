> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'react'
title: 'React UIKit Integration and Frequently Asked Questions'
keywords: ['react', 'initialization', 'components', 'integration', 'TUIKit']
---

# React TUIKit Integration and FAQ

## Module 1: Environment Setup and Quick Initialization (Topic: Setup)

### Q: How do I install TUIKit in a React project?

**A:** TUIKit is built on top of the Web SDK. You need to install `@tencentcloud/chat-uikit-react`

```bash
# Install TUIKit React version
npm install @tencentcloud/chat-uikit-react@latest
```

## Module 2: Frequently Asked Questions (Topic: Question)

### Q: Does TUIKit support React 17/19 versions?

**A:** Currently, React versions 17.x and 19.x are not supported. Only React v18 is supported.

---

## Platform: Vue

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'vue'
title: 'Vue TUIKit Integration and FAQ Solutions'
keywords: ['vue', 'Initialization', 'Component', 'Integration', 'TUIKit']
---

Vue TUIKit Integration and Common Problem Solutions (FAQ)

Module 1: Environment Setup and Quick Initialization (Topic: Setup)

Q: How to install TUIKit in a Vue project?

A: TUIKit is developed based on the Web SDK. You need to install @tencentcloud/chat-uikit-vue3.

```bash
# Install TUIKit Vue version
npm install @tencentcloud/chat-uikit-vue3@latest
```

Module 2: Common Problem Solutions (Topic: Question)

Q: Can I use third-party component libraries, such as Element-Plus, within TUIKit?

A: Glue code between the core components can use other component libraries, which can be seen in the example code. For instance, you can encapsulate <ChatSetting /> into a full-screen drawer component. However, components already existing inside the core components are not supported for modification at this time.
```typescript
const isChatSettingShow = ref(false);
<el-drawer
  v-model="isChatSettingShow"
  title="Settings"
  <ChatSetting />
</el-drawer>

```

---

## Platform: Web SDK

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'sdk'
title: 'Web JS SDK Error Code Reference'
keywords: ['error code', 'error', 'login failure']
---

# Web JS SDK Error Code Reference

## Web JS SDK Error Codes (Topic: SDK Error Code)

## Error Code 2024
**Cause:** User not logged in.
**Solution:** Check if the login interface was called correctly.

## Error Code 2025
**Cause:** Duplicate login. Multiple login requests were triggered within 15 seconds, and the SDK blocked them.
**Solution:** Check the timing and trigger mechanism of the login interface calls.

## Error Code 2301
**Cause:** Audio file size exceeds the limit.
**Solution:** Maximum audio message size is 20MB.

## Error Code 2252
**Cause:** Image message sending failed due to invalid image format.
**Solution:** Supported image formats: 'jpg', 'jpeg', 'gif', 'png', 'bmp', 'image', 'webp'.

## Error Code 2253
**Cause:** Image size exceeds the limit.
**Solution:** Maximum image message size is 20MB.

## Error Code 2351
**Cause:** Video size exceeds the limit.
**Solution:** Maximum video message size is 100MB.

## Error Code 2352
**Cause:** Video message sending failed due to invalid video format.
**Solution:** Supported video formats: 'mp4', 'quicktime', 'mov'.

## Error Code 2401
**Cause:** File does not exist.
**Solution:** Please check if the file path is correct.

## Error Code 2402
**Cause:** File size exceeds the limit.
**Solution:** Maximum file message size is 100MB.

---

## Platform: Web SDK

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'sdk'
title: 'Web JS SDK Frequently Asked Questions'
keywords: ['sdk integration', 'login API', 'create group']
---

# Web JS SDK FAQ

## Important Notice
**SDK Version Information:**  
 - v3: `@tencentcloud/chat`
 When providing code examples, always use the latest version `@tencentcloud/chat` for demonstration.

## Web JS SDK Frequently Asked Questions (Topic: SDK FAQ)

### Q: How to receive streaming messages sent via Chat REST API (https://trtc.io/document/71131?product=chat&menulabel=core%20sdk&platform=javascript) through the SDK?
**A:** The SDK handles streaming messages through the message received event [TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://trtc.io/document/47996?product=chat&menulabel=core%20sdk&platform=javascript) and message modified event [TencentCloudChat.EVENT.MESSAGE_MODIFIED](https://trtc.io/document/48005?product=chat&menulabel=core%20sdk&platform=javascript). When REST API sends the first fragment, the SDK notifies the application through the message received event. Subsequent fragments are notified through the message modified event.

### Q: Why does SDK login fail?
**A:** Please provide the error message or error code.

### Q: Why can I still receive SDK events after calling the off interface to unregister event listeners?
**A:** For the same event, such as [TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://trtc.io/document/47996?product=chat&menulabel=core%20sdk&platform=javascript), when calling the [on](https://trtc.io/document/47967?platform=javascript&product=chat&menulabel=sdk#d39fb50f-f0f2-4b67-9b15-acd7e8608105) interface to listen for events and the [off](https://trtc.io/document/47967?platform=javascript&product=chat&menulabel=sdk#c4cb09c5-142a-4d8c-8c03-ea7f446aa844) interface to unregister event listeners, the handler parameter should point to the same function. Recommended approach:

```typescript
// Recommended approach
tim.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived, this);
tim.off(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived);

// Note: The following code has a bug and cannot unregister TencentCloudChat.EVENT.MESSAGE_RECEIVED because bind() returns a new function each time
tim.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, this.onMessageReceived.bind(this));
tim.off(TencentCloudChat.EVENT.MESSAGE_RECEIVED, this.onMessageReceived.bind(this));
```

### Q: Does the SDK automatically unregister events that were registered via the on interface when logout is called?
**A:** No. To unregister event listeners, you must actively call the [off](https://trtc.io/document/47967?platform=javascript&product=chat&menulabel=sdk#c4cb09c5-142a-4d8c-8c03-ea7f446aa844) interface.

### Q: After successful login, I cannot send messages and receive a prompt about improper API call timing?
**A:** SDK APIs can only be called after the SDK is ready. Listen for the [TencentCloudChat.EVENT.SDK_READY](https://trtc.io/document/47967?product=chat&menulabel=core%20sdk&platform=javascript#b7ca8a5b-e020-4c43-a442-71125e55da2d) event and only call authenticated interfaces like sending messages after the SDK is ready.

### Q: I want to implement like and flower-sending features in an audio-video chatroom. How do I do this?
**A:** Use the [createCustomMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-a-custom-message) and [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) interfaces to implement this.

### Q: How does Web IM SDK v2.x/v3.x/v4.x handle combined messages sent via REST API or legacy IM?
**A:** The content of combined messages is stored in the _elements property of the Message instance. You need to parse it yourself. Refer to [Message Format](https://trtc.io/document/47990?product=chat&menulabel=core%20sdk&platform=javascript) for parsing.
For non-combined messages, use the recommended approach: use the type and payload properties of the [Message](https://trtc.io/document/47990?product=chat&menulabel=core%20sdk&platform=javascript) instance.

### Q: Why don't I receive the TencentCloudChat.EVENT.MESSAGE_RECEIVED event after successfully sending a message via Web IM SDK's sendMessage?
**A:** Messages sent by yourself are not notified to the application through `TencentCloudChat.EVENT.MESSAGE_RECEIVED`. The [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) interface returns a `Promise`. Handle success or failure business logic through `Promise.then` or `Promise.catch`.

### Q: After merging messages retrieved via getMessageList with messages received through TencentCloudChat.EVENT.MESSAGE_RECEIVED event listener, I sometimes notice messages are out of order. Why?
**A:** When maintaining the conversation message list, ensure messages are enqueued in the correct order. If the message list is sorted from oldest to newest, historical messages retrieved via [getMessageList](https://trtc.io/document/47999?product=chat&menulabel=core%20sdk&platform=javascript) should be added to the head of the list, while real-time messages received via [TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://trtc.io/document/47996?product=chat&menulabel=core%20sdk&platform=javascript) should be added to the tail.

### Q: After creating a live streaming group via the createGroup interface, I cannot receive messages?
**A:** For live streaming groups, you need to actively join the group to start the long-polling message channel. After creating a live streaming group using the [createGroup](https://trtc.io/document/48465?product=chat&menulabel=core%20sdk&platform=javascript#creating-a-group) interface, you must call the [joinGroup](https://trtc.io/document/48465?product=chat&menulabel=core%20sdk&platform=javascript#joining-a-group) interface to join the group before you can send and receive messages.
```typescript
let promise = tim.joinGroup({ groupID: 'group1' });
promise.then(function(imResponse) {
  switch (imResponse.data.status) {
    case TencentCloudChat.TYPES.JOIN_STATUS_WAIT_APPROVAL:
      break;
    case TencentCloudChat.TYPES.JOIN_STATUS_SUCCESS:
      console.log(imResponse.data.group); // Joined group profile
      break;
    case TencentCloudChat.TYPES.JOIN_STATUS_ALREADY_IN_GROUP:
      break;
    default:
      break;
    }
  }).catch(function(imError){
    console.warn('joinGroup error:', imError);
});
```

### Q: Why don't live streaming groups have unread message counts? Why can't I retrieve message history?
**A:** By design: live streaming groups do not support unread message counts, and new members cannot view message history before joining. See [Group System](https://trtc.io/document/33529?product=chat&menulabel=core%20sdk&platform=javascript) for details. Ultimate or Enterprise editions support new members viewing message history before joining live streaming groups (SDK v2.16.0 or higher required).

### Q: Can I join two or more live streaming groups simultaneously?
**A:** No, a user can only join one live streaming group at a time. Joining multiple live streaming groups simultaneously would start multiple long-polling message channels, which can significantly impact web/H5 or mini-program performance.

### Q: How does the SDK implement message reactions?
**A:** The SDK supports message reaction capability. You need to purchase the Ultimate or Enterprise edition package. For API implementation, refer to the official documentation: https://trtc.io/document/60749?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to mark one-to-one messages as read?
**A:** To mark one-to-one messages as read and clear the conversation unread count, use [setMessageRead](https://trtc.io/document/48319?product=chat&menulabel=core%20sdk&platform=javascript#clearing-the-conversation-unread-count).

### Q: How to clear unread count for group messages?
**A:** To clear the unread count for group conversations, use [setMessageRead](https://trtc.io/document/48319?product=chat&menulabel=core%20sdk&platform=javascript#clearing-the-conversation-unread-count).

### Q: How to implement single message read status for one-to-one and group messages?
**A:** To implement single message read status, use the message read receipt feature. For details on how to enable read receipts, send read receipts, etc., refer to: https://trtc.io/document/48021?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to send image messages with Web SDK?
**A:** Use [createImageMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-an-image-message) to create an image message instance, then use [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) to send the image message.

### Q: How to send video messages with Web SDK?
**A:** Use [createVideoMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-a-video-message) to create a video message instance, then use [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) to send the video message.

### Q: How to send audio messages with Web SDK?
**A:** Use [createAudioMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-an-audio-message) to create an audio message instance, then use [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) to send the audio message.

### Q: How to send file messages with Web SDK?
**A:** Use [createFileMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-a-file-message) to create a file message instance, then use [sendMessage](https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#sending-a-message) to send the file message.

### Q: How to implement user online status feature in Chat?
**A:** 
1. Chat SDK provides **user status query, subscription, unSubscription APIs and status change event notifications**. For details, please refer to: https://trtc.io/document/49561.
2. For server-side **online status change callback**, please refer to: https://trtc.io/document/34357?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to implement message list feature without UI in Chat SDK?
**A:** To implement message list feature without UI, you need to combine SDK's `sendMessage`, `getMessageList` APIs and `MESSAGE_RECEIVED` event.

### Q: How to implement @all and @someone features without UI in Chat SDK?
**A:** SDK provides `createTextAtMessage` API for implementing @ mentions. For details, please refer to: https://trtc.io/document/47993?product=chat&menulabel=core%20sdk&platform=javascript#creating-an-.40-message.

### Q: How to implement search functionality in Chat SDK?
**A:** SDK provides **cloud search** capabilities, supporting message search, user search, group search, and group member search. API details can be found in the following links:
- Search messages: https://trtc.io/document/67677?product=chat&menulabel=core%20sdk&platform=javascript
- Search users: https://trtc.io/document/67679?product=chat&menulabel=core%20sdk&platform=javascript
- Search groups: https://trtc.io/document/67681?product=chat&menulabel=core%20sdk&platform=javascript
- Search group members: https://trtc.io/document/67683?product=chat&menulabel=core%20sdk&platform=javascript

### Q: How to create groups, add group members, and set administrators in Chat SDK?
**A:** For specific information about creating groups, adding group members, and setting administrators in SDK, please refer to the documentation below:
- Create group: https://trtc.io/document/48465?product=chat&menulabel=core%20sdk&platform=javascript
- Add group members: https://trtc.io/document/48180?product=chat&menulabel=core%20sdk&platform=javascript#49b805dd-4898-44a3-89a4-3b201f839fce
- Modify group member role (can set administrator): https://trtc.io/document/48180?product=chat&menulabel=core%20sdk&platform=javascript#f35fb570-648c-4b97-99a1-cef5b81b1284

### Q: How to create a C2C one-to-one conversation?
**A:** The UI layer can create a one-to-one conversation by querying conversation profile using `getConversationProfile`. The `conversationID` format is: `C2C${userID}`. After SDK successfully creates the conversation, it will notify the conversation list update through the `CONVERSATION_LIST_UPDATE` event.

### Q: How to get total unread message count and listen to real-time changes?
**A:** For detailed information about total unread message count, please refer to: https://trtc.io/document/48319?product=chat&menulabel=core%20sdk&platform=javascript.
1. To get total unread message count, call `getTotalUnreadMessageCount` API.
2. To listen for total unread count changes, monitor the `TOTAL_UNREAD_MESSAGE_COUNT_UPDATED` event.
Note: During initialization, it's recommended to listen to the `TOTAL_UNREAD_MESSAGE_COUNT_UPDATED` event to get the total unread message count, because the calculation of total unread count depends on the synchronization of cloud conversation list. Calling `getTotalUnreadMessageCount` directly after successful login may return 0 because the cloud conversation list hasn't finished synchronizing yet.

### Q: How to get original and thumbnail images in SDK image messages?
**A:** For image messages, high-resolution original image (type = 0), thumbnail (type = 1), and large image (type = 2) can be obtained. For details, please refer to the Message structure documentation: https://trtc.io/document/47990?product=chat&menulabel=core%20sdk&platform=javascript#893c75e0-e592-475a-bd9a-0c4ec30114b9.

### Q: How to handle message sending failures in SDK?
**A:** When SDK message sending fails, the `sendMessage` interface will throw an error, which needs to be caught using `sendMessage().catch()`. The error contains the complete message structure, where message.status is `fail`. You can resend the failed message using `resendMessage`.

### Q: In weak network environments, how does SDK ensure WebSocket stability and reconnection?
**A:** In weak network environments, if WebSocket disconnects, SDK will automatically reconnect. WebSocket status changes can be monitored through the `NET_STATE_CHANGE` event (https://trtc.io/document/47967?platform=javascript&product=chat&menulabel=sdk#d39fb50f-f0f2-4b67-9b15-acd7e8608105).

### Q: How to delete conversations in SDK?
**A:** Conversation deletion only supports one-way deletion. SDK delete conversation API is `deleteConversation`. Reference documentation: https://trtc.io/document/48313?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to get friend list in SDK?
**A:** Get friend list using `getFriendList` API. Friend list updates can be monitored through the `FRIEND_LIST_UPDATED` event. Reference documentation: https://trtc.io/document/48158?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to listen for friend online status updates in SDK?
**A:** Friend online status changes can be implemented in two ways:
1. If you have enabled automatic friend status notification in the IM Console, when a friend's status changes, SDK will dispatch the `USER_STATUS_UPDATED` event, where you can get the friend's latest status.
2. If you haven't enabled automatic friend status notification and still want to sense friend status changes, you need to call `subscribeUserStatus` to actively subscribe to friend status. When a friend's status changes, SDK will dispatch the `USER_STATUS_UPDATED` callback.
For detailed documentation, please refer to: https://trtc.io/document/49561?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to delete conversation history messages in SDK?
**A:** SDK provides the `clearHistoryMessage` API to clear conversation history messages. For detailed documentation, please refer to: https://trtc.io/document/53498?product=chat&menulabel=core%20sdk&platform=javascript.

### Q: How to get the number of unhandled group join requests and how to handle group join applications in Chat SDK and Chat Engine?
**A:** According to the customer's usage scenario, solutions are provided from both Chat SDK and Chat Engine. When users query such questions, please provide accurate answers based on the actual scenario.
## Scenario 1: Solution when the customer integrates Chat SDK without UI
- Get Group Application List During Initialization, API: [getGroupApplicationList](https://trtc.io/document/48465?product=chat&menulabel=core%20sdk&platform=javascript#c69d4d7c-3f0b-4486-b31a-190e6176a1c5)
- Get the Number of Unhandled Group Join Requests. Based on the list data returned by `getGroupApplicationList`, `applicationType = 0` represents the number of group join requests.
```typescript
let unhandledCount = 0;
let promise = chat.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    // item.applicant - Applicant's userID
    // item.applicantNick - Applicant's nickname
    // item.groupID - Group ID
    // item.groupName - Group name
    // item.applicationType - Application type: 0 for join group request, 2 for invitation to join group
    // item.userID - When applicationType = 2, this is the invited user's userID
    // item.note - Additional message, not supported for group invitations, returns empty string
    if (item.applicationType === 0) {
      unhandledCount++;
    }
  });
}).catch(function(imError) {});
```
- Update Unread Count When Receiving Group Join Requests Online
This requires listening to the [MESSAGE_RECEIVED](https://trtc.io/document/47967?product=chat&menulabel=core%20sdk&platform=javascript#d39fb50f-f0f2-4b67-9b15-acd7e8608105) event.
```typescript
// For Message data structure details, please refer to https://trtc.io/document/47990?product=chat&menulabel=core%20sdk&platform=javascript
let unhandledCount = 0;
let onMessageReceived = function(event) {
  const messageList = event.data;
  messageList.forEach((message) => {
    if (message?.type === 'TIMGroupSystemNoticeElem') {
      if (message.payload?.operationType === 1) {
        unhandledCount++;
      }
    }
  })
};
chat.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived);
```
- Handle Group Join Applications
API: [handleGroupApplication](https://trtc.io/document/48465?product=chat&menulabel=core%20sdk&platform=javascript#processing-(approving-or-rejecting)-a-group-join-request)

```typescript
// Handle group join requests by getting the pending list
let promise = chat.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    if (item.applicationType === 0) {
      chat.handleGroupApplication({
        handleAction: 'Agree',
        application: {...item},
      });
    }
  });
}).catch(function(imError) {});
```

## Scenario 2: Solution when the customer integrates Chat UIKit with UI or integrates ChatEngine
- Get Group Application List During Initialization, API: [getGroupApplicationList](https://web.sdk.qcloud.com/im/doc/chat-engine/en/ITUIGroupService.html#getGroupApplicationList)
- Get the Number of Unhandled Group Join Requests. Based on the list data returned by `getGroupApplicationList`, `applicationType = 0` represents the number of group join requests.
```typescript
import { TUIGroupService } from '@tencentcloud/chat-uikit-engine-lite';

let unhandledCount = 0;
let promise = TUIGroupService.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    // item.applicant - Applicant's userID
    // item.applicantNick - Applicant's nickname
    // item.groupID - Group ID
    // item.groupName - Group name
    // item.applicationType - Application type: 0 for join group request, 2 for invitation to join group
    // item.userID - When applicationType = 2, this is the invited user's userID
    // item.note - Additional message, not supported for group invitations, returns empty string
    if (item.applicationType === 0) {
      unhandledCount++;
    }
  });
}).catch(function(imError) {});
```
- Update Unread Count When Receiving Group Join Requests Online
This requires monitoring the [GroupStore](https://web.sdk.qcloud.com/im/doc/chat-engine/en/global.html#GroupStore) provided `groupSystemNoticeList` key through TUIStore.
```typescript
// For Message data structure details, please refer to https://trtc.io/document/47990?product=chat&menulabel=core%20sdk&platform=javascript
import { TUIStore, StoreName } from '@tencentcloud/chat-uikit-engine-lite';

let unhandledCount = 0;
let onGroupSystemNoticeListUpdated = function(messageList = []) {
  messageList.forEach((message) => {
    if (message?.type === 'TIMGroupSystemNoticeElem') {
      if (message.payload?.operationType === 1) {
        unhandledCount++;
      }
    }
  })
}

TUIStore.watch(StoreName.GRP, {
  groupSystemNoticeList: onGroupSystemNoticeListUpdated,
})
```
- Handle Group Join Applications, API: [handleGroupApplication](https://web.sdk.qcloud.com/im/doc/chat-engine/en/ITUIGroupService.html#handleGroupApplication)

```typescript
import { TUIGroupService } from '@tencentcloud/chat-uikit-engine-lite';

// Handle group join requests by getting the pending list
let promise = TUIGroupService.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    if (item.applicationType === 0) {
      TUIGroupService.handleGroupApplication({
        handleAction: 'Agree',
        application: {...item},
      });
    }
  });
}).catch(function(imError) {});
```
