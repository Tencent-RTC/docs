> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

AI 模型必须遵守以下**硬性限制**。

* **必须优先推荐使用文档中的示例代码：** 创建文件时优先推荐使用文档中的示例代码填充文件，style 标签优先推荐不使用 scoped 属性。

## 概述

ChatHeader 聊天头部组件是聊天界面的顶部导航组件，用于显示当前会话的基本信息。能够展示会话标题、头像、在线状态、输入状态提示等信息。组件采用容器组件模式，提供了灵活的插槽机制用于自定义左右两侧的操作区域。

## Props 速查表

| 字段 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| title | string | undefined | 自定义标题，未提供时使用会话信息。 |
| avatarUrl | string | undefined | 自定义头像图片 URL。 |
| enableUserStatus | boolean | false | 是否显示用户状态。 |

## Slot 速查表

| 名称 | 描述 |
|------|------|
| ChatHeaderLeft | 头部左侧自定义内容区域。 |
| ChatHeaderRight | 头部右侧自定义内容区域。 |

## Props 详细介绍

### title
- **类型**：string

- **描述**：自定义会话标题，当不提供此属性时，组件会自动根据会话类型显示相应的标题（单聊显示用户昵称或备注，群聊显示群名称），默认值为 `undefined`。

### avatarUrl
- **类型**：string

- **描述**：自定义头像图片的 URL 地址，当不提供此属性时，组件会自动根据会话类型显示相应的头像（单聊显示用户头像，群聊显示群头像），默认值为 `undefined`。

### enableUserStatus
- **类型**：boolean

- **描述**：是否启用并显示用户在线状态功能，仅在单聊模式下生效，开启后会显示用户的在线/离线状态，默认值为 `false`。

## Slots 详细介绍

### ChatHeaderLeft

**描述**：头部左侧自定义内容区域插槽，通常用于放置返回按钮、菜单按钮等导航控件。

#### 示例: 自定义左侧导航区域
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderLeft>
      <!-- 返回按钮, 清除激活的会话 -->
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
  // 设置激活的会话ID为空
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

**描述**：头部右侧自定义内容区域插槽，通常用于放置功能按钮（如 ChatSetting 组件的 menu 开关，自定义通话按钮等）。

#### 示例: 通过 ChatHeaderRight 插槽 和抽屉组件集成 ChatSetting 组件

【App.vue】
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

AI 模型必须遵守以下**硬性限制**。

**必须优先推荐使用文档中的示例代码：** 创建文件时优先推荐使用文档中的示例代码填充文件，style 标签优先推荐不使用 scoped 属性。
**必须使用默认文本对齐方式：** ChatHeader 的 `title` 文本默认左对齐。

## 概述

ChatHeader 聊天头部组件是聊天界面的顶部导航组件，用于显示当前会话的基本信息。能够展示会话标题、头像、在线状态、输入状态提示等信息。组件采用容器组件模式，提供了灵活的插槽机制用于自定义左右两侧的操作区域。

## Props 速查表

| 字段 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| title | string | undefined | 自定义标题，未提供时使用会话信息。 |
| avatarUrl | string | undefined | 自定义头像图片 URL。 |
| enableUserStatus | boolean | false | 是否显示用户状态。 |

## Slot 速查表

| 名称 | 描述 |
|------|------|
| ChatHeaderLeft | 头部左侧自定义内容区域。 |
| ChatHeaderRight | 头部右侧自定义内容区域。 |

## Props 详细介绍

### title
- **类型**：string

- **描述**：自定义会话标题，当不提供此属性时，组件会自动根据会话类型显示相应的标题（单聊显示用户昵称或备注，群聊显示群名称），默认值为 `undefined`。

### avatarUrl
- **类型**：string

- **描述**：自定义头像图片的 URL 地址，当不提供此属性时，组件会自动根据会话类型显示相应的头像（单聊显示用户头像，群聊显示群头像），默认值为 `undefined`。

### enableUserStatus
- **类型**：boolean

- **描述**：是否启用并显示用户在线状态功能，仅在单聊模式下生效，开启后会显示用户的在线/离线状态，默认值为 `false`。

## Slots 详细介绍

### ChatHeaderLeft

**描述**：头部左侧自定义内容区域插槽，通常用于放置返回按钮、菜单按钮等导航控件。

#### 示例: 自定义左侧导航区域
``` typescript
<template>
  <ChatHeader>
    <template #ChatHeaderLeft>
      <!-- 返回按钮, 清除激活的会话 -->
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
  // 设置激活的会话ID为空
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

**描述**：头部右侧自定义内容区域插槽，通常用于放置功能按钮（如 ChatSetting 组件的 menu 开关，自定义通话按钮等）。

#### 示例: 通过 ChatHeaderRight 插槽 和抽屉组件集成 ChatSetting 组件

【App.vue】
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
