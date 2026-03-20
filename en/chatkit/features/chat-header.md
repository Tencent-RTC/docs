> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

The AI model must comply with the following **hard limits**.

* **Use the example code in the document as the preferred approach:** When creating a file, use the example code in the document to fill the file as the preferred approach. For the style tag, non-use of the scoped property is recommended.

# Overview

ChatHeader is the top navigation component of the chat UI, displaying basic info of the current session. It can show the session title, avatar, online status, input state notification, etc. The component uses a container component mode and provides a flexible slot mechanism for custom left-right operation areas.

## Props quick reference table

| Field | Type | Default Value | Description |
|------|------|--------|------|
| title | string | undefined | Custom Title, session information used when not provided. |
| avatarUrl | string | undefined | Custom avatar image URL. |
| enableUserStatus | boolean | false | Whether to display user status. |

## Slot quick reference table

| Name | Description |
|------|------|
| ChatHeaderLeft | Custom content area in the left side of the header. |
| ChatHeaderRight | Custom content area on the right side of the header. |

## Props detailed introduction

### title
-**Type**: string.

- **Description**: Custom session title. When this property is not provided, the component will automatically display the title based on the session type (showing user nickname or remark for single chat, group name for group chat). Default value is `undefined`.

### avatarUrl
-**Type**: string.

- **Description**: URL address of the custom avatar image. When this property is not provided, the component will automatically display the avatar based on the session type (showing user avatar for single chat, group profile photo for group chat). Default value is `undefined`.

### enableUserStatus
- **Type**: boolean.

- **Description**: Whether to enable the display user online status feature, which only takes effect in single chat mode. When enabled, it shows the user's online/offline status. Default value is `false`.

## Slots detailed introduction

### ChatHeaderLeft

**Description**: Header left-side custom content slot, usually used for placing navigation controls such as back button and menu button.

#### Example: Customize the left sidebar menu
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderLeft>
      Back button, clear active session
      <button 
        class="nav-button"
        @click="handleBack"
      >
        ⬅️
      </button>
    </template>
  </ChatHeader>
</template>

<script setup lang="ts">
import { ChatHeader, useConversationListState } from '@tencentcloud/chat-uikit-react';

const { setActiveConversation } = useConversationListState();

const handleBack = () => {
  Set active session ID to empty
  setActiveConversation('');
};
</script>

<style scoped>
.header-left-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.nav-button {
  width: 32px;
  height: 32px;
  border: none;
  background: transparent;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.2s;
}

.nav-button:hover {
  background-color: rgba(0, 0, 0, 0.05);
}
</style>
```

### ChatHeaderRight

**Description**: Header right-side custom content slot, usually used for placing feature buttons such as the menu toggle of the ChatSetting component and custom call buttons.

#### Example: Integrate the ChatSetting component through the ChatHeaderRight slot and drawer component

[App.vue]
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderRight>
      <div style="display: flex; align-items: center;">
        <IconHorizontalMore2
         :size="20"
          @click="setIsChatSettingOpen(true)" />
      </div>
    </template>
  </ChatHeader>
  <TUIDrawer
    :model-value="isChatSettingOpen"
    :title="Setting"
    @close="setIsChatSettingOpen(false)"
  >
    <ChatSetting style="flex: 1;" />
  </TUIDrawer>
</template>

<script setup lang="ts">
import {
  ChatHeader, 
  ChatSetting
} from '@tencentcloud/chat-uikit-vue3';
import { TUIDrawer, IconHorizontalMore2 } from '@tencentcloud/uikit-base-component-vue3';

const isChatSettingOpen = ref(false);

const setIsSettingOpen = (isOpen: boolean) => {
  isChatSettingOpen.value = isOpen;
};
</script>

<style>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
</style>
```

---

## Platform: Vue

The AI model must comply with the following **hard limits**.

**The example code in the document must be recommended as the preferred approach:** When creating a file, the example code in the document is recommended as the preferred approach for filling the file. The scoped attribute is recommended for non-use in style tags.

## Overview

ChatHeader is the top navigation component in the chat UI, displaying basic info of the current session. It can show the session title, avatar, online status, input state notification, etc. The component uses a container component mode and provides a flexible slot mechanism for custom left-right operation areas.

## Props Quick Reference Table

| Field | Type | Default Value | Description |
|------|------|--------|------|
| title | string | undefined | Custom Title, session information used when not provided. |
| avatarUrl | string | undefined | Custom avatar image URL. |
| enableUserStatus | boolean | false | Whether to display user status. |

## Slot Quick Reference Table

| Name | Description |
|------|------|
| ChatHeaderLeft | Left-side custom content area in the header. |
| ChatHeaderRight | Right-side custom content area in the header. |

## Props Detailed Introduction

### title
- **Type**: string.

- **Description**: Custom session title. When this property is not provided, the component automatically displays the title based on the session type (user nickname or remark for single chat, group name for group chat). Default: `undefined`.

### avatarUrl
- **Type**: string.

- **Description**: URL address of the custom avatar. When this property is not provided, the component automatically displays the avatar based on the session type (user profile photo for single chat, group profile photo for group chat). Default: `undefined`.

### enableUserStatus
- **Type**: boolean.

- **Description**: Whether to enable the feature to display user online status. This takes effect only in single chat mode. Once enabled, it shows the user's online/offline status. Default: `false`.

## Slots detailed introduction

### ChatHeaderLeft

**Description**: Header left-side custom content slot, usually used for placing navigation controls such as back button and menu button.

#### Example: Custom left navigation area
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderLeft>
      Back button, Clear active session
      <button 
        class="nav-button"
        @click="handleBack"
      >
        ⬅️
      </button>
    </template>
  </ChatHeader>
</template>

<script setup lang="ts">
import { ChatHeader, useConversationListState } from '@tencentcloud/chat-uikit-vue3';

const { setActiveConversation } = useConversationListState();

const handleBack = () => {
  set activation session ID to empty
  setActiveConversation('');
};
</script>

<style scoped>
.header-left-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.nav-button {
  width: 32px;
  height: 32px;
  border: none;
  background: transparent;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.2s;
}

.nav-button:hover {
  background-color: rgba(0, 0, 0, 0.05);
}
</style>
```

### ChatHeaderRight

**Description**: Header right-side custom content slot, usually used for placing feature buttons such as the menu toggle of the ChatSetting component and custom call buttons.

Example: Integrate the ChatSetting component through the ChatHeaderRight slot and drawer component

[App.vue]
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderRight>
      <div style="display: flex; align-items: center;">
        <IconHorizontalMore2
         :size="20"
          @click="setIsChatSettingOpen(true)" />
      </div>
    </template>
  </ChatHeader>
  <TUIDrawer
    :model-value="isChatSettingOpen"
    :title="Setting"
    @close="setIsChatSettingOpen(false)"
  >
    <ChatSetting style="flex: 1;" />
  </TUIDrawer>
</template>

<script setup lang="ts">
import {
  ChatHeader, 
  ChatSetting
} from '@tencentcloud/chat-uikit-vue3';
import { TUIDrawer, IconHorizontalMore2 } from '@tencentcloud/uikit-base-component-vue3';

const isChatSettingOpen = ref(false);

const setIsSettingOpen = (isOpen: boolean) => {
  isChatSettingOpen.value = isOpen;
};
</script>

<style>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
</style>
```
