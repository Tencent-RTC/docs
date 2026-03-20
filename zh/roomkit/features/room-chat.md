> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

会中聊天功能允许会议内的成员通过文字、表情等形式进行实时互动。该功能基于 `tuikit-atomicx-vue3` 的 IM 组件能力实现，为用户提供便捷的即时通讯体验。

## 适用场景

- **在线研讨会：**主讲人进行视频演讲时，参会者可以通过文字提问或讨论。

- **远程协作：**团队成员在沟通时，可发送文字、图片、文件等。

- **直播教学：**学生可以在聊天区发送弹幕或问题，老师实时查看并解答，增强课堂互动性。

## 前提条件

在接入功能前，请确保满足以下条件：
- **用户状态：**用户已经通过 useLoginState 完成登录鉴权，参考 接入概览，并已作为一个房间的房主或成员在房间内，参考 房间管理。

- **环境依赖：**项目已正确安装并引入 `tuikit-atomicx-vue3`。

## 实现会中聊天功能

本功能提供两种集成方案，您可以根据业务需求选择最适合的一种：
- **方案一（推荐）：**使用 UI 组件快速集成。直接引入 `tuikit-atomicx-vue3` 提供的消息列表组件（[MessageList](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageList)）和消息输入组件（[MessageInput](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageInput)），开发成本最低。

- **方案二（高级）：**使用底层 API 自定义集成。基于 atomicx-core SDK API [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) 和 [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) 状态钩子自行实现 UI 和交互逻辑，灵活度最高。

   

   会中聊天

## 方案一：使用 UI 组件快速集成

`tuikit-atomicx-vue3` 提供了两个核心组件：
- [**MessageList**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageList)**：** 消息列表组件，展示聊天记录，支持消息的复制、撤回、删除等操作。

- [**MessageInput**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/MessageInput)**：** 消息输入组件，支持文本、表情、图片、文件发送，并可根据禁言状态自动禁用。

### 步骤1：数据初始化

在用户加入房间成功后，需要调用 [setActiveConversation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setActiveConversation) 接口初始化聊天数据。建议监听 [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) 的变化来自动处理。

> **说明：**
> - `GROUP` 为固定前缀，`setActiveConversation` 参数必须为 ``GROUP${roomId}``，否则无法正常收发消息。
> - `GROUP` 为 IM SDK 群组会话的固定前缀（参考 [IM 会话类型](https://web.sdk.qcloud.com/im/doc/zh-cn/Conversation.html)）。房间聊天基于 IM 群组实现，因此会话 ID 必须为 ``GROUP${roomId}`` 格式。

``` typescript
import { watch } from 'vue';
import { useConversationListState } from 'tuikit-atomicx-vue3/chat';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { setActiveConversation } = useConversationListState();

// 监听房间 ID 变化，自动切换到对应的 IM 群组会话
watch(() => currentRoom.value?.roomId, (roomId) => {
  if (roomId) {
    // 关键步骤：设置当前活跃会话 ID，规则为 "GROUP" + 房间ID
    setActiveConversation(`GROUP${roomId}`);
  }
}, { immediate: true });
```

### 步骤2： 使用组件

在您的页面中，可以直接组合使用这两个组件。同时结合 [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) 获取当前用户的禁言状态，控制输入框的可用性。

> **注意：**
> 

> 所有 TUIKit 组件必须包裹在 UIKitProvider 内部，否则将无法获取上下文依赖，导致样式异常。
> 

``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="room-chat-container">
      <!-- 消息列表组件 -->
      <!-- messageActionList: 配置长按消息支持的操作，例如复制、撤回、删除 -->
      <MessageList
        class="message-list"
        :messageActionList="['copy', 'recall', 'delete']"
      />

      <!-- 消息输入组件 -->
      <!-- disabled: 当用户被禁言时禁用输入框 -->
      <!-- hideSendButton: 可选，隐藏发送按钮，使用回车发送 -->
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

// 获取当前用户的禁言状态，isMessageDisabled 由房间后台实时同步，无需前端主动轮询，前端只需绑定该状态即可自动响应禁言变更。
const isMessageDisabled = computed(() => localParticipant.value?.isMessageDisabled);

// 根据状态动态显示占位符提示
const inputPlaceholder = computed(() => 
  isMessageDisabled.value ? '您已被禁言' : '请输入消息...'
);
</script>

<style scoped>
.room-chat-container{display:flex;flex-direction:column;height:100%;padding:8px;gap:8px}.message-list{flex:1;overflow:hidden}.message-input{flex-shrink:0;border:1px solid #e0e0e0;border-radius:8px}
</style>
```

### 步骤3：处理未读消息计数（可选）

如果聊天窗口平时是折叠或关闭状态，您需要通过监听 [messageList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#messageList) 的变化来统计未读消息数，并在 UI 上（如聊天图标角标）进行提示。
``` typescript
import { ref, watch } from 'vue';
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { messageList } = useMessageListState();
const unreadCount = ref(0);
const isChatWindowOpen = ref(false); // 需根据您的业务逻辑维护此状态

watch(() => messageList.value?.length, (newLength, oldLength) => {
  // 如果是首次加载或数据为空，不进行计数
  if (!newLength || newLength === 0) return;

  // 仅当聊天窗口关闭且收到新消息时，增加未读计数
  if (!isChatWindowOpen.value && newLength > (oldLength || 0)) {
    unreadCount.value += (newLength - (oldLength || 0));
  }
});

// 当用户打开聊天窗口时，清空未读计数
const openChatWindow = () => {
  isChatWindowOpen.value = true;
  unreadCount.value = 0;
};
```

## 方案二：使用底层 API 自定义集成

本节主要介绍如何通过 atomicx-core SDK API [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) 和 [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) 接口来实现自定义的聊天界面。

> **注意：**
> 

> `MessageInputState` 默认支持与 `@tiptap/vue-3` 编辑器集成。如果您仅需要简单的文本输入框（例如 `<input>` 或 `<textarea>`），请**忽略**`setEditorInstance`、`setContent` 等与编辑器实例绑定的 API，直接使用 `updateRawValue` 和 `sendMessage` 即可。
> 

#### 步骤1：数据初始化

在用户加入房间成功后，需要调用 [setActiveConversation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setActiveConversation) 接口初始化聊天数据。建议监听 [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) 的变化来自动处理。

> **说明：**
> - `GROUP` 为固定前缀，`setActiveConversation` 参数必须为 ``GROUP${roomId}``，否则无法正常收发消息。
> - 在房间切换或退房时，建议重置或清空活跃会话，避免消息错发至错误会话。

``` typescript
import { watch } from 'vue';
import { useConversationListState } from 'tuikit-atomicx-vue3/chat';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { setActiveConversation } = useConversationListState();

// 监听房间 ID 变化，自动切换到对应的 IM 群组会话
watch(() => currentRoom.value?.roomId, (roomId) => {
  if (roomId) {
    // 关键步骤：设置当前活跃会话 ID，规则为 "GROUP" + 房间ID
    setActiveConversation(`GROUP${roomId}`);
  }
}, { immediate: true });
```

#### 步骤2：获取消息列表

使用 [useMessageListState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) 获取当前会话的消息列表。`messageList` 是响应式数据，会自动更新新收到的消息。
``` typescript
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { 
  messageList,      // 响应式消息列表
  loadMoreOlderMessage, // 加载更多历史消息方法
  hasMoreOlderMessage,   // 是否还有更多历史消息
} = useMessageListState();

// 示例：渲染消息列表
// <div v-for="msg in messageList" :key="msg.ID">
//   {{ msg.payload.text }}
// </div>
```

#### 步骤3：发送消息

使用 [useMessageInputState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) 管理输入框内容并发送消息。
``` typescript
import { useMessageInputState } from 'tuikit-atomicx-vue3/chat';

const { 
  inputRawValue,  // 响应式数据：当前输入内容
  updateRawValue, // 方法：更新输入内容（会自动触发"对方正在输入"状态）
  sendMessage     // 方法：发送消息
} = useMessageInputState();

const handleSend = async () => {
  // 校验非空
  if (!inputRawValue.value) return;
  
  try {
    // 1. 发送消息
    // 方式 A：发送当前 inputRawValue 的内容
    await sendMessage();
    
    // 方式 B：直接发送指定文本（不依赖 inputRawValue）
    // await sendMessage('Hello World');

    console.log('发送成功');
    
    // 2. 发送成功后清空输入框
    // 关键点：非富文本模式下，务必使用 updateRawValue('') 清空
    // 请勿使用 setContent('')，该方法仅在绑定了 editor 实例后生效
    updateRawValue('');
  } catch (error) {
    console.error('发送失败', error);
  }
};

// 示例：绑定原生输入框
// <input 
//   :value="inputRawValue" 
//   @input="(e) => updateRawValue(e.target.value)" 
//   @keyup.enter="handleSend" 
// />
```

#### 步骤4：加载历史消息

通过 [loadMoreOlderMessage](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) 方法分页拉取历史消息。通常在滚动到列表顶部时触发。

> **注意：**
> 

> 在加载历史消息后，通常需要手动调整滚动条位置，以保持当前浏览视角不发生跳变。
> 

``` typescript
import { useMessageListState } from 'tuikit-atomicx-vue3/chat';

const { 
  hasMoreOlderMessage,
  loadMoreOlderMessage
} = useMessageListState();

const loadHistory = async () => {
  if (hasMoreOlderMessage.value) {
    // 记录加载前的滚动高度
    // const previousHeight = scrollContainer.value.scrollHeight;
    
    await loadMoreOlderMessage();
    
    // DOM 更新后恢复滚动位置
    // nextTick(() => {
    //   const currentHeight = scrollContainer.value.scrollHeight;
    //   scrollContainer.value.scrollTop += (currentHeight - previousHeight);
    // });
  }
};
```

#### 步骤5：高级功能（已读回执）

如果您需要已读回执功能，可以使用 [setEnableReadReceipt](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setEnableReadReceipt)。
``` typescript
const { 
  setEnableReadReceipt, // 开启/关闭已读回执
} = useMessageListState();

// 开启已读回执
setEnableReadReceipt(true);
```

## 开发注意事项

- **禁言状态同步**

   `localParticipant.isMessageDisabled` 是由房间管理员控制的。当管理员开启“全员禁言”或单独禁言某用户时，该属性会自动更新。请务必将此属性绑定到输入框的 `disabled` 状态上，以在前端层面限制用户发言。

- **会话切换时机**

   确保 `setActiveConversation` 在进房成功后且 `roomId` 有值时调用。如果在进房前调用，可能会因为 SDK 尚未初始化完毕或未登录而失败。

- **容器布局要求（重要）**

   消息列表组件内部处理了滚动逻辑，因此**父容器必须有固定的高度**（例如 `height: 100%` 或具体像素值）并设置 `overflow: hidden`，否则会导致列表无限拉长、无法滚动。

- **消息类型处理**

   `messageList` 中包含多种类型的消息（文本、图片、文件等），自定义渲染时需根据 `msg.type` 进行区分处理。

- **输入状态管理**

   使用 `updateRawValue` 更新输入内容会自动触发 "对方正在输入" 的状态通知（如果支持）。如果仅使用 `inputRawValue.value = 'xxx'` 可能不会触发该状态。

- **历史消息滚动维持**

   在自定义开发中，调用 `loadMoreOlderMessage` 加载历史消息后，DOM 高度会增加。为了防止视图跳动，需要在加载前记录 `scrollHeight`，加载后（`nextTick`）计算高度差并调整 `scrollTop`。

- **新消息滚动策略**

   建议实现“智能滚动”逻辑：当用户位于列表底部时，收到新消息自动滚动到底部；当用户正在浏览历史消息时，保持当前位置不动，并显示“有新消息”的提示气泡。

- **会话 ID 格式要求**

   调用 `setActiveConversation` 时，必须使用 ``GROUP${roomId}`` 格式（GROUP 为固定前缀）。这是 IM SDK 群组会话的标准格式，不可自定义或省略前缀，否则无法正常收发消息。

## 常见问题

**切到群组会话失败或消息列表无内容？**

确保已登录并进房成功后再调用 `setActiveConversation('GROUP' + roomId)`；未登录或 roomId 为空会导致失败。

**消息收不到或列表空白？**

检查当前活跃会话是否正确（`GROUP` 前缀 + roomId），以及网络/鉴权是否正常；必要时重新设置活跃会话 `setActiveConversation` 刷新。

**未读计数不清零？**

需要手动维护未读计数逻辑。打开聊天窗口时，或收到新消息但窗口关闭时进行计数更新。

**被禁言仍能点击发送？**

请确保前端 UI 根据 `isMessageDisabled` 状态禁用了发送按钮和输入框。虽然服务端也会拦截，但前端禁用体验更好。

**自定义头像/昵称展示？**

可通过 `MessageList` 插槽或自定义 Message 组件，优先显示房间内的 `nameCard`，并自定义头像样式等。

**自定义方案如何发送图片或文件？**

`sendMessage` 方法支持传入 `InputContent[]` 数组。您可以构造包含 `type: 'image'` 或 `type: 'file'` 以及对应 `file` 对象的数组来实现富媒体消息发送。
``` typescript
import { useMessageInputState } from 'tuikit-atomicx-vue3/chat';

const { sendMessage } = useMessageInputState();

// 示例：发送图片
// const file = ...; // 从 input type="file" 获取的 File 对象
await sendMessage([{ type: 'image', file }]);
```

**如何修改 UI 组件的默认样式？**

`tuikit-atomicx-vue3` 的组件类名通常保持稳定。您可以使用 CSS 深度选择器（例如 Vue 的 `:deep()`）来覆盖默认样式。例如 `.message-list :deep(.tui-message-bubble) { background-color: #f0f0f0; }`。

## **示例项目**

腾讯云在 GitHub 上提供了示例项目 [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) ，您可以参考以实现完整的 RoomKit 功能。

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useRoomState** | 房间状态管理（创建、加入、预约等） | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) |
| **useRoomParticipantState** | 房间参与者管理（成员列表、权限控制） | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |
| **useConversationListState** | 会话列表管理 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-ConversationListState) |
| **useMessageListState** | 消息列表管理 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageListState) |
| **useMessageInputState** | 消息输入管理 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-MessageInputState) |
