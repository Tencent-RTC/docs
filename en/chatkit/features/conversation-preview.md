> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationPreview` is used for previewing session content in the list. The component displays session information, unread count, and provides conversation action feature.

With the aid of atomic information display components, you can freely design and combine your desired `ConversationPreview` layout.

Meanwhile, you can also use the `onConversationSelect` function to define selected session behavior.

### Basic Usage

You can use the `Preview` property of `ConversationList` to customize the preview item for each individual conversation in the list. If the `Preview` property is not specified, the system will automatically adopt the `ConversationPreviewUI` component as the default value.
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
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| **conversation*****(Required)*** | ConversationModel | - | Required parameter to indicate the currently rendered conversation list item. |
| isSelected | Boolean | false | Control if the conversation list item UI is in selected status. |
| enableActions | Boolean | true | Control whether to display the conversation operation feature. |
| actionsConfig | ConversationActionsConfig | - | For custom session operation configuration. |
| highlightMatchString | String | - | Conversation list item Title highlights matching keywords, commonly used for Conversation Search results. |
| Title | `String ｜ JSX.Element` | ConversationPreviewTitle | Render the title area of the conversation list item. |
| LastMessageAbstract | `String ｜ JSX.Element` | ConversationPreviewAbstract | Render the latest message abstract area of the conversation list item. |
| LastMessageTimestamp | `String ｜ JSX.Element` | ConversationPreviewTimestamp | Render the latest message timestamp area of the conversation list item. |
| Unread | `String ｜ JSX.Element` | ConversationPreviewUnread | Render the unread indicator area of the conversation list item. |
| ConversationActions | `ReactElement` | ConversationActions | Render the conversation operations area of the conversation list item. |
| Avatar | ReactElement | Avatar | Render the avatar area of the conversation list item. |
| onConversationSelect | (conversation: ConversationModel) => void; | - | Specify the attributes of receiving callback when selecting a dialogue in the conversation list. |
| className | String | - | Set a custom name for the root element class in CSS. |
| style | React.CSSProperties | - | Set custom styles for the root element. |

## Custom Case

### Discord-Like Style

Discord is a popular chat application, similar to Skype or Telegram. The message content in Discord is as shown below:

By customizing the `ConversationPreview` layout, features, and style, we can quickly achieve a Discord-like effect.

【React】
1. Customize the `ConversationListPreview`

2. Switch theme to dark mode

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

`ConversationListPreview` effect as follows after customization:
| **Before Modification** | **Modified** |
| --- | --- |
|  |  |

###

---

## Platform: Vue

## Overview

The ConversationPreview component is used to preview a single conversation in the conversation list. It is responsible for displaying conversation information, unread count, and providing conversation operation functionality.

With this component, you can freely design and combine the ConversationPreview layout you expect. Additionally, you can customize the behavior when selecting a conversation through the `@select-conversation` event.

## Custom Usage

You can use the `Preview` prop of `ConversationList` to customize the preview item for each conversation in the conversation list. If the `Preview` prop is not specified, the system will use the `ConversationPreviewUI` component by default.
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
| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| **conversation***(Required)* | ConversationModel | - | Required parameter, identifies the current rendered conversation list item |
| isSelected | boolean | false | Controls whether the conversation list item UI is in selected state |
| enableActions | boolean | true | Controls whether conversation actions are displayed |
| actionsConfig | ConversationActionsConfig | - | Used to customize conversation actions configuration |
| highlightMatchString | string | - | Highlight match keyword for conversation list item title, commonly used for conversation search result highlighting |
| Preview | Component | ConversationPreviewUI | Custom preview component |
| Avatar | Component | Avatar | Renders conversation list item avatar area |
| ConversationActions | Component | ConversationActions | Renders conversation list item actions area |
| className | string | - | CSS custom class name for root element |
| style | CSSProperties | - | Custom style for root element |

### ConversationPreviewUIProps
| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| **conversation***(Required)* | ConversationModel | - | Required parameter, identifies the current rendered conversation list item |
| isSelected | boolean | false | Controls whether the conversation list item UI is in selected state |
| enableActions | boolean | true | Controls whether conversation actions are displayed |
| actionsConfig | ConversationActionsConfig | - | Used to customize conversation actions configuration |
| highlightMatchString | string | - | Highlight match keyword for conversation list item title |
| Title | Component | ConversationPreviewTitle | Renders conversation list item title area |
| LastMessageAbstract | Component | ConversationPreviewAbstract | Renders conversation list item last message abstract area |
| LastMessageTimestamp | Component | ConversationPreviewTimestamp | Renders conversation list item last message timestamp area |
| Unread | Component | ConversationPreviewUnread | Renders conversation list item unread badge area |
| ConversationActions | Component | ConversationActions | Renders conversation list item actions area |
| Avatar | Component | Avatar | Renders conversation list item avatar area |
| className | string | - | CSS custom class name for root element |
| style | CSSProperties | - | Custom style for root element |

## Events
| Event Name | Parameters | Description |
| --- | --- | --- |
| select-conversation | (conversation: ConversationModel) | Event triggered when selecting a conversation in the conversation list |

## Example: Discord-like Style Customization

Discord is a popular chat application, similar to Skype or Telegram. The chat content in Discord is shown in the image below:

By customizing the layout, functionality, and styles of `ConversationPreview`, we can quickly achieve a Discord-like effect.
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

The customized `ConversationListPreview` effect is as follows:
| **Before** | **After** |
| --- | --- |
|  |  |
