> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationPreview` 用于对于会话列表中单条会话内容进行预览，组件负责显示会话信息、未读计数以及提供会话操作功能。

借助原子化的信息展示组件，您可以自由地设计和组合出您所期望的 `ConversationPreview` 布局。

同时，您还可以通过 `onConversationSelect` 函数来自定义选中会话时的行为。

### 基础使用

您可以使用 `ConversationList` 的 `Preview` 属性来自定义会话列表中每个单独会话的预览项，如果没有指定 `Preview` 属性，系统将自动采用 `ConversationPreviewUI` 组件作为默认值。
``` typescript
import { ConversationList, ConversationPreviewUI } from '@tencentcloud/chat-uikit-react';
import type { ConversationPreviewUIProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationPreview = (props: ConversationPreviewUIProps) => {
  const { Title } = props;
  return (
    <ConversationPreviewUI {...props}>
      {Title}
      <div>Your custom preview UI</div>
    </ConversationPreviewUI>
  );
};

<ConversationList Preview={CustomConversationPreviewUI}></ConversationList>
```

### Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| **conversation*****(Required)*** | `ConversationModel` | - | 必选参数，标识当前渲染的会话列表项。 |
| isSelected | `Boolean` | `false` | 控制会话列表项 UI 是否处于选中状态。 |
| enableActions | `Boolean` | `true` | 控制会话操作功能是否显示。 |
| actionsConfig | ConversationActionsConfig | - | 用于自定义会话操作配置。 |
| highlightMatchString | `String` | - | 会话列表项 Title 高亮匹配关键词，常用于会话搜索结果高亮。 |
| Title | `String \\| JSX.Element` | `ConversationPreviewTitle` | 渲染会话列表项标题区域。 |
| LastMessageAbstract | `String \\| JSX.Element` | `ConversationPreviewAbstract` | 渲染会话列表项最新消息摘要区域。 |
| LastMessageTimestamp | `String \\| JSX.Element` | `ConversationPreviewTimestamp` | 渲染会话列表项最新消息时间戳区域。 |
| Unread | `String \\| JSX.Element` | `ConversationPreviewUnread` | 渲染会话列表项消息未读标识区域。 |
| ConversationActions | `ReactElement` | `ConversationActions` | 渲染会话列表项会话操作区域。 |
| Avatar | `ReactElement` | `Avatar` | 渲染会话列表项头像区域。 |
| onConversationSelect | `(conversation: ConversationModel) => void;` | - | 指定在选择对话列表中的对话时接收回调的属性。 |
| className | `String` | - | 指定根元素类的 CSS 自定义名称。 |
| style | `React.CSSProperties` | - | 指定根元素样式的自定义样式。 |

## 自定义案例

### 类 Discord 风格

Discord 是一个流行的聊天应用程序，类似于 Skype 或 Telegram。Discord 中的聊天内容如下图所示:

通过对 `ConversationPreview` 布局、功能以及样式的定制，我们可以快速实现类 Discord 效果。

【React】
1. 定制 `ConversationListPreview`

2. 切换主题到深色模式

``` typescript
import { UIKitProvider, ConversationList, ConversationPreviewUI } from '@tencentcloud/chat-uikit-react';
import type { ConversationPreviewUIProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationPreview = (props: ConversationPreviewUIProps) => {
  const { Title } = props;
  return (
    <ConversationPreviewUI {...props}>
      <span> # </span>
      <span>{Title}</span>
    </ConversationPreviewUI>
  );
};

const App = () => {
    <UIKitProvider theme={'dark'}>
      <ConversationList 
        style={{ maxWidth: '300px', height: '600px' }} 
        Preview={CustomConversationPreviewUI}
       />
      ...
    </UIKitProvider>
}
```

【CSS】
``` scss
.custom-preview-ui {
  height: 34px;
  border-radius: 6px;
  padding: 10px;
  margin: 0 10px;
  .custom-preview-ui__tag {
    margin-right: 10px;
    font-size: 16px;
    color: #b3b3b4;
  }
  .custom-preview-ui__title {
    font-size: 14px;
    color: #b3b3b4;
  }
  &.uikit-conversation-preview--active {
    background-color: #3b3d43;
    .custom-preview-ui__tag {
      color: #ffffff;
    }
    .custom-preview-ui__title {
      .uikit-conversation-preview__title {
        color: #ffffff;
      }
    }
  }
}
```

`ConversationListPreview` 定制后效果如下：
| **修改前** | **修改后** |
| --- | --- |
|  |  |

---

## Platform: Vue

## 概述

ConversationPreview 组件用于预览会话列表中的单个会话。它负责显示会话信息、未读数，并提供会话操作功能。

借助该组件，您可以自由地设计和组合出您所期望的 ConversationPreview 布局。此外，您还可以通过 `@select-conversation` 事件自定义选中会话时的行为。

## 自定义用法

您可以使用 `ConversationList `的 `Preview` prop 来自定义会话列表中每个会话的预览项。如果未指定 `Preview` prop，系统将默认使用 `ConversationPreviewUI` 组件。
``` typescript
<template>
  <div class="custom-preview">
    <div class="content">
      <h4>Custom Preview</h4>
      <p>{{ conversation?.getShowName() }}</p>
    </div>
  </div>
</template>

<script lang="ts" setup>
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';

interface Props {
  conversation?: ConversationModel;
}

const props = withDefaults(defineProps<Props>(), {});
</script>
```
``` typescript
<template>
  <ConversationList :Preview="CustomConversationPreview" />
</template>

<script setup lang="ts">
import { ConversationList } from '@tencentcloud/chat-uikit-vue3';
import CustomConversationPreview from './CustomConversationPreview.vue';
</script>
```

## Props

### ConversationPreviewProps
| 参数名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| **conversation***(Required)* | ConversationModel | - | 必选参数，标识当前渲染的会话列表项 |
| isSelected | boolean | false | 控制会话列表项 UI 是否处于选中状态 |
| enableActions | boolean | true | 控制会话操作功能是否显示 |
| actionsConfig | ConversationActionsConfig | - | 用于自定义会话操作配置 |
| highlightMatchString | string | - | 会话列表项 Title 高亮匹配关键词，常用于会话搜索结果高亮 |
| Preview | Component | ConversationPreviewUI | 自定义预览组件 |
| Avatar | Component | Avatar | 渲染会话列表项头像区域 |
| ConversationActions | Component | ConversationActions | 渲染会话列表项会话操作区域 |
| className | string | - | 指定根元素类的 CSS 自定义名称 |
| style | CSSProperties | - | 指定根元素样式的自定义样式 |

### ConversationPreviewUIProps
| 参数名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| **conversation***(Required)* | ConversationModel | - | 必选参数，标识当前渲染的会话列表项 |
| isSelected | boolean | false | 控制会话列表项 UI 是否处于选中状态 |
| enableActions | boolean | true | 控制会话操作功能是否显示 |
| actionsConfig | ConversationActionsConfig | - | 用于自定义会话操作配置 |
| highlightMatchString | string | - | 会话列表项 Title 高亮匹配关键词 |
| Title | Component | ConversationPreviewTitle | 渲染会话列表项标题区域 |
| LastMessageAbstract | Component | ConversationPreviewAbstract | 渲染会话列表项最新消息摘要区域 |
| LastMessageTimestamp | Component | ConversationPreviewTimestamp | 渲染会话列表项最新消息时间戳区域 |
| Unread | Component | ConversationPreviewUnread | 渲染会话列表项消息未读标识区域 |
| ConversationActions | Component | ConversationActions | 渲染会话列表项会话操作区域 |
| Avatar | Component | Avatar | 渲染会话列表项头像区域 |
| className | string | - | 指定根元素类的 CSS 自定义名称 |
| style | CSSProperties | - | 指定根元素样式的自定义样式 |

## Events
| 事件名 | 参数 | 说明 |
| --- | --- | --- |
| select-conversation | (conversation: ConversationModel) | 指定在选择对话列表中的对话时接收回调的事件 |

## 示例：自定义类 Discord 风格

Discord 是一个流行的聊天应用程序，类似于 Skype 或 Telegram。Discord 中的聊天内容如下图所示:

通过对 `ConversationPreview` 布局、功能以及样式的定制，我们可以快速实现类 Discord 效果。
``` java
<template>
  <div
    :class="[
      'discord-preview-ui',
      {
        'conversationPreview--active': activeConversation?.conversationID === props.conversation.conversationID,
      }
    ]"
    @click="handleSelectConversation"
  >
    <label class="discord-preview-ui__tag">
      #
    </label>
    <span class="discord-preview-ui__title">{{ conversation?.getShowName() }}</span>
  </div>
</template>

<script lang="ts" setup>
import { useConversationListState } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';

const { setActiveConversation, activeConversation } = useConversationListState();

interface Props {
  conversation: ConversationModel;
}

const emit = defineEmits<{
  selectConversation: [conversation: ConversationModel];
}>();

const props = withDefaults(defineProps<Props>(), {});

const handleSelectConversation = () => {
  setActiveConversation(props.conversation.conversationID);
  emit('selectConversation', props.conversation);
};
</script>
<style scoped>
.discord-style-app {
  background-color: #2f3136;
  padding: 20px;
  border-radius: 8px;
}

.discord-conversation-list {
  background-color: #2f3136;
}

.discord-preview-ui {
  height: 34px;
  border-radius: 4px;
  padding: 6px 8px;
  margin: 1px 8px;
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: all 0.15s ease;
  position: relative;
}

.discord-preview-ui__tag {
  margin-right: 6px;
  font-size: 16px;
  color: #8e9297;
  font-weight: 600;
  line-height: 1;
}

.discord-preview-ui__title {
  font-size: 16px;
  color: #8e9297;
  font-weight: 500;
  line-height: 1.2;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  flex: 1;
}

.discord-preview-ui:hover {
  background-color: #393c43;
}

.discord-preview-ui:hover .discord-preview-ui__tag,
.discord-preview-ui:hover .discord-preview-ui__title {
  color: #dcddde;
}

.conversationPreview--active {
  background-color: #404249 !important;
}

.conversationPreview--active .discord-preview-ui__tag,
.conversationPreview--active .discord-preview-ui__title {
  color: #ffffff !important;
}

.discord-preview-ui::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 8px;
  background-color: #ffffff;
  border-radius: 0 4px 4px 0;
  opacity: 0;
  transition: opacity 0.15s ease;
}
</style>

```
``` typescript
<template>
  <ConversationList :Preview="CustomConversationPreview" />
</template>

<script setup lang="ts">
import { ConversationList } from '@tencentcloud/chat-uikit-vue3';
import CustomConversationPreview from './CustomConversationPreview.vue';
</script>
```

`ConversationListPreview` 定制后效果如下：
| **修改前** | **修改后** |
| --- | --- |
|  |  |
