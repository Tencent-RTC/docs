> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## Component Overview

`ChatSetting` is an intelligent chat settings component that can automatically render the appropriate settings interface based on the currently activated session type. This component internally integrates two modes: C2C (one-on-one) chat settings and group chat settings, providing users with a uniform chat settings portal.

Core features of components:
- **Automatic adaptation** - The settings interface is automatically switched based on session type

- **Status-driven** - Auto-update content based on the current active session status

## Props Parameters
| Parameter | Type | Default Value | Description |
| --- | --- | --- | --- |
| className | string \\| undefined | undefined | Custom CSS class name |
| style | React.CSSProperties \\| undefined | undefined | Custom inline style |

## Quick Usage

`ChatSetting` is an independent component that can be used freely. By default, the switch control is on the ChatHeader Component. Alternatively use the useUIOpenControlState Hook to customize the ChatSetting switch.
``` tsx
function App() {
  const { isChatSettingOpen, setChatSettingOpen } = useUIOpenControlState();
  
  function toggleChatSettingOpen() {
    setChatSettingOpen(!isChatSettingOpen);
  }
  
  return (
     <UIKitProvider>
        <div style={{ maxWidth: '300px' }}>
          <ConversationList />
        </div>
        <Chat
          PlaceholderEmpty={null}
          style={{
            display: 'flex',
            flexDirection: 'row',
            flex: 1,
            maxHeight: '100vh',
          }}
        >
          <div style={{
            display: 'flex',
            flexDirection: 'column',
            flex: 1,
            minWidth: '0',
          }}
          >
            <button onClick={toggleChatSetting}>toggle</button>
            <MessageList />
            <MessageInput />
          </div>
          {isChatSettingOpen && <ChatSetting style={{ width: '350px' }} />}
        </Chat>
      </UIKitProvider>
  );
}
```

---

## Platform: Vue

## Component Overview

ChatSetting is an intelligent chat settings component that automatically renders the appropriate settings interface based on the currently active conversation type. The component internally integrates two modes: C2C (1-to-1) chat settings and group chat settings, providing users with a unified chat settings entry point.

Core features of the component:
- **Auto-adaptive**: Automatically switches the settings interface based on conversation type (C2C or group chat).

- **State-driven**: Automatically updates content based on the currently active conversation state.
   

   > **Note:**
   > 

   > Will not be displayed if there is no active conversation.
   > 

## Quick Start

ChatSetting is a standalone component that can be used freely. For flexibility in component usage, UIKit does not provide a default usage pattern.

The recommended approach is to use the `ChatHeaderRight` slot of the ChatHeader component to control the display of `ChatSetting`. Here's the sample code:
``` typescript
<template>
  <UIKitProvider>
    <Chat>
      <ChatHeader>
        <template #ChatHeaderRight>
          <button @click="toggleChatSetting">
            ⚙️ Settings
          </button>
        </template>
      </ChatHeader>
      <MessageList />
      <MessageInput />
    </Chat>
    <Drawer 
      :open="isSettingVisible"
      title="Chat Settings"
    >
      <ChatSetting />
    </Drawer>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ChatHeader } from '@tencentcloud/chat-uikit-vue3';
// Assuming you have a Drawer component
import { Drawer } from 'your-ui-library';

const isSettingVisible = ref(false);

const toggleChatSetting = () => {
  isSettingVisible.value = !isSettingVisible.value;
};
</script>
```

## Related Documentation

- [chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

- [GitHub Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)
