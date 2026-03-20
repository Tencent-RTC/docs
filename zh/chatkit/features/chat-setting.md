> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## 组件概述

ChatSetting 是一个聊天设置组件，它能根据当前激活的**会话类型**（单聊或群聊）自动渲染对应的设置界面。该组件通过统一的入口，无缝集成了 C2C（单聊） 和群聊两种模式的设置功能，为用户提供了流畅且一致的操作体验。

核心功能：
- 智能适配： 自动识别**会话类型**，并切换至相应的设置面板。

- 状态驱动： 基于当前会话状态，动态更新设置内容。
   

   > **注意：**
   > 

   > 如果当前没有激活的会话则不会显示。
   > 

## 快速使用
``` typescript
import {
  UIKitProvider,
  useLoginState,
  LoginStatus,
  ConversationList,
  Chat,
  ChatHeader,
  MessageList,
  MessageInput,
  useUIOpenControlState,
  ChatSetting,
  Search,
  VariantType,
  ContactList,
  ContactInfo,
} from "@tencentcloud/chat-uikit-react";
import { useState } from "react";

function App() {
  const { isChatSettingOpen, isChatSearchOpen } = useUIOpenControlState();
  const [activeTab, setActiveTab] = useState('chats');

  // 从控制台获取登录信息
  const { status } = useLoginState({
    SDKAppID: 0,
    userID: '',
    userSig: '',
  })

  if (status !== LoginStatus.SUCCESS) {
    return <div>Loading...</div>
  }
  
  // 语言支持 en-US(default) / zh-CN / ja-JP / ko-KR / zh-TW
  // 主题支持 light(default) / dark
  return (
    <UIKitProvider>
      <div style={{ height: '100vh', width: '100vw', display: 'flex', flexDirection: 'row' }}>
        <ul>
          <li onClick={() => {setActiveTab('chats')}}>chats</li>
          <li onClick={() => {setActiveTab('contacts')}}>contact</li>
        </ul>
        {
          activeTab === 'chats' && (
            <>
              <ConversationList style={{ minWidth: '300px', maxWidth: '350px' }}/>
              <Chat PlaceholderEmpty="No messages yet">
                <ChatHeader />
                <MessageList />
                <MessageInput />
              </Chat>
              {isChatSettingOpen && <ChatSetting />}
              {isChatSearchOpen &&  <Search style={{ minWidth: '300px'}} variant={VariantType.EMBEDDED} />}
            </>
          )
        }
        {
          activeTab === 'contacts' && (
            <>
              <ContactList style={{ minWidth: '300px', maxWidth: '350px' }}/>
              <ContactInfo 
                PlaceholderEmpty="No contact yet"
                onSendMessage={() => {setActiveTab('chats')}}
                onEnterGroup={() => {setActiveTab('chats')}}
              />
            </>
          )
        }
      </div>
    </UIKitProvider>
  );
}

export default App;
```

## 相关文档

**官方文档**
- 自定义组件 - UIKitProvider

- 自定义组件 - ConversationList

- 自定义组件 - MessageList

- 自定义组件 - MessageInput

- 自定义组件 - ContactList

- 自定义组件 - Search

   **NPM 包**

- [chat-uikit-react npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-react)

   **GitHub**

- [GitHub Demo](https://github.com/TencentCloud/chat-uikit-react)

## 交流与反馈

如遇任何问题，可联系 [官网售后](https://cloud.tencent.com/act/event/connect-service#/) 反馈，享有专业工程师的支持，解决您的难题。

---

## Platform: Vue

## 组件概述

ChatSetting 是一个智能的聊天设置组件，它能够根据当前激活的会话类型自动渲染相应的设置界面。该组件内部集成了 C2C（1 对 1）聊天设置和群聊设置两种模式，为用户提供了统一的聊天设置入口。

组件的核心特点：
- **自动适配**：根据会话类型（分为单聊和群聊）自动切换设置界面。

- **状态驱动**：基于当前激活会话状态自动更新内容。
   

   > **注意：**
   > 

   > 如果当前没有激活的会话则不会显示。
   > 

## 快速使用

ChatSetting 是一个可自由使用的独立组件，为了组件使用的灵活度，UIKit 并没有提供默认的使用方式。

推荐的用法是：结合 ChatHeader 组件的 `ChatHeaderRight` 插槽，来控制 `ChatSetting` 的显示。以下是示例代码：
``` typescript
<template>
  <UIKitProvider>
    <Chat>
      <ChatHeader>
        <template #ChatHeaderRight>
          <button @click="toggleChatSetting">
            ⚙️ 设置
          </button>
        </template>
      </ChatHeader>
      <MessageList />
      <MessageInput />
    </Chat>
    <Drawer 
      :open="isSettingVisible"
      title="聊天设置"
    >
      <ChatSetting />
    </Drawer>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ChatHeader } from '@tencentcloud/chat-uikit-vue3';
// 假设您有一个 Drawer 组件
import { Drawer } from 'your-ui-library';

const isSettingVisible = ref(false);

const toggleChatSetting = () => {
  isSettingVisible.value = !isSettingVisible.value;
};
</script>
```

## 相关文档
- 自定义组件 - UIKitProvider

- 自定义组件 - ConversationList

- 自定义组件 - Chat

- 自定义组件 - MessageInput

- 自定义组件 - MessageList

- 自定义组件 - ChatSetting

- 自定义组件 - C2CSetting State

- 自定义组件 - GroupSetting State

- 自定义组件 - ContactList

- 自定义组件 - Search

- [chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

- [GitHub Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)

## 交流与反馈

如遇任何问题，可联系 [官网售后](https://cloud.tencent.com/act/event/connect-service#/) 反馈，享有专业工程师的支持，解决您的难题。
