> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

# Component Overview

MessageList is a powerful chat message list component with complete message display, interaction, and custom function. The component support includes message grouping, read receipt, message manipulation, scroll control, and other core chat features, while providing various custom options to satisfy different business needs.

## Props Configuration
| Field | Type | Default Value | Description |
| --- | --- | --- | --- |
| alignment | 'left' \\| 'right' \\| 'two-sided' | 'two-sided' | Message alignment mode. - left: Align all messages to the left. - right: Align all messages to the right. - two-sided: Right-align sent messages and left-align received messages. |
| enableReadReceipt | boolean | false | Whether to enable the read receipt feature. |
| messageActionList | IMessageAction[] | undefined | Custom message operation list, such as copy, revoke, delete. |
| messageAggregationTime | number | 300 (5 minutes) | Message aggregation interval (seconds). Consecutive messages from the same sender within this period will be aggregated and displayed. |
| filter | (message: IMessageModel) => boolean | undefined | Message filtering function, used to control which messages to display. |
| className | string | undefined | Custom CSS class name. |
| style | React.CSSProperties | undefined | Custom inline style. |
| Message | React.ComponentType | Message | Custom message component. |
| MessageTimeDivider | React.ComponentType | MessageTimeDivider | Custom time dividing line component. |

## Prop field explanation

### alignment

Parameter type: `'left' | 'right' | 'two-sided'`

alignment is used to set the message alignment mode, supporting three modes: `left`, `right`, and `two-sided`. Default value is `'two-sided'`.
- `two-sided`: Non-person messages are on the left, my messages are on the right

- `left`: All messages are on the left

- `right`: All messages are on the right

### enableReadReceipt

Parameter type: `boolean`

enableReadReceipt is used to set whether the message read receipt feature is enabled. Default value: `false`.

### messageActionList

Parameter type: `IMessageAction[]`

The messageActionList is used to set the actions list of messages, such as copy, recall, and delete. The default value is the complete message operation list `['copy', 'recall', 'quote', 'forward', 'delete']`.
``` tsx
interface IMessageAction {
  key: 'copy' | 'recall' | 'quote' | 'forward' | 'delete' | string;
  label?: string;
  icon?: React.ReactNode;
  onClick?: (message: IMessageModel) => void;
  visible?: ((message: IMessageModel) => boolean) | boolean;
  component?: React.ComponentType<{ message: IMessageModel }>;
  style?: React.CSSProperties;
}
```

Use `useMessageAction` to obtain the complete message operation list, then customize as needed.

#### Example 1: Change the order of the action list

Assuming the message actions list is displayed in the order: `forward`, `copy`, `recall`, `quote`, `delete`.
``` tsx
import { Chat, MessageList, useMessageActions } from '@tencentcloud/chat-uikit-react';

const App = () => {
  const actions = useMessageActions(['forward', 'copy', 'recall', 'quote', 'delete']);
  return (
    <Chat>
      <MessageList messageActionList={actions} />
    </Chat>
  );
}
```

#### Example 2: Only show some message actions

Only show `forward`, `copy`, `recall` operations.
``` tsx
import { Chat, MessageList, useMessageActions } from '@tencentcloud/chat-uikit-react';

const App = () => {
  const actions = useMessageActions(['forward', 'copy', 'recall']);
  return (
    <Chat>
      <MessageList messageActionList={actions} />
    </Chat>
  );
}

```

#### Example 3: Customize the style of message actions

Here is an example of a custom recall operation:
1. Change the tag to 'recall ⚠️'

2. Change the color to orange

3. Only text messages can be withdrawn

   ``` tsx
   import TUIChatEngine from '@tencentcloud/chat-uikit-engine';
   import { Chat, MessageList, useMessageActions } from '@tencentcloud/chat-uikit-react';
   
   const ChatApp = () => {
     const actions = useMessageActions(['copy', {
       key: 'recall',
       label: 'Recall ⚠️',
       style: {
         color: 'orange'
       },
       visible: (message) => message.type === TUIChatEngine.TYPES.MSG_TEXT,
     }, 'quote', 'forward', 'delete']);
   
     return (
       <Chat>
         <MessageList messageActionList={actions} />
       </Chat>
     );
   }
   ```

#### Example 4: Add new custom message actions

Assuming custom message actions are required, for example, only messages sent by others can be tagged as 'like' and inserted after the 'recall' operation.
``` tsx
import { useMessageAction } from '@tencentcloud/chat-uikit-react';
import { yourApi } from '@/api/yourApi';

const customActions = {
  key: 'like',
  label: 'Like',
  icon: <span>👍</span>,
  style: {
    color: '#E53888',
  },
  visible: (message) => message.flow === 'in',
  onClick: (message) => {
    yourApi.likeMessage(message.ID);
  }
}

function ChatApp() {
  const actions = useMessageAction(['forward', 'copy', 'recall', customActions, 'quote', 'delete']);
  return <MessageList messageActionList={actions} />;
}
```

### messageAggregationTime

Parameter type: `number`

messageAggregationTime is used to set the message aggregation interval. Consecutive messages from the same sender will be aggregated and displayed. Default value: `300` (seconds).

### filter

Parameter type: `(message: IMessageModel) => boolean`

filter is used to set the message filtering function to control which messages to display. Default value: `undefined`.

#### Example: Filter out error messages sent by robots
``` tsx
import { MessageList } from '@/components/MessageList';
import TUIChatEngine from '@tencentcloud/chat-uikit-engine';

const messageFilter = (message) => {
  // Filter out text messages where sender nickname contains '_robot' and content contains 'operation-failed'
  if (
    message.nick?.includes('_robot') 
    && message.type === TUIChatEngine.TYPES.MSG_TEXT
    && message.payload?.text?.includes('operation-failed')
  ) {
    return false;
  }
  return true;
};

function ChatApp() {
  return <MessageList filter={messageFilter} />;
}
```

### className

Parameter type: `string`

className is used to set the root container's custom CSS class name. Default value: `undefined`.

### style

Parameter type: `React.CSSProperties`

style is used to set the root container's custom inline style. Default value: `undefined`.

### Message

Parameter type: `React.ComponentType`

Message is used to set the custom message component, replacing the default message rendering component. Default value: built-in `Message` component.

#### Example: Custom CUSTOM message click redirection component of messages
``` tsx
import TUIChatEngine from '@tencentcloud/chat-uikit-engine';
import { Chat, MessageList, Message } from '@tencentcloud/chat-uikit-react';

function CustomMessage(props) {
  const { message } = props;

  if (message.type === TUIChatEngine.TYPES.MSG_CUSTOM) {
    const { businessID, ...restData } = JSON.parse(message.payload.data);
    if (businessID === 'text_link') {
      return (
        <div>
          <div>{restData.text}</div>
          <a href={restData.link}>
            Redirect to external website address {restData.link}
          </a>
        </div>
      );
    }
  } else {
    Other message types use the default message component
    return <Message {...props} />;
  }
}

function ChatApp() {
  return (
    <Chat>
      <MessageList Message={CustomMessage} />
    </Chat>
  );
}
```

### MessageTimeDivider

Parameter type: `React.ComponentType`

MessageTimeDivider is used to set the custom time dividing line component. Default value: built-in `MessageTimeDivider` component.

#### Example: Case-sensitive working hour dividing line
``` tsx
import { Chat, MessageList, MessageTimeDivider } from '@tencentcloud/chat-uikit-react';

const BusinessTimeDivider: typeof MessageTimeDivider = ({ prevMessage, currentMessage }) => {
  if (!prevMessage || !currentMessage) return null;
  
  For each message to be rendered, get the current and previous message time, convert them to date objects, and determine whether they cross days or exceed a 4-hour interval.
  const currentTime = new Date(currentMessage.time * 1000);
  const prevTime = new Date(prevMessage.time * 1000);
  
  // Display only when crossing days or the interval exceeds 4 hours
  const shouldShow = currentTime.toDateString() !== prevTime.toDateString() ||
                    (currentTime.getTime() - prevTime.getTime()) > 4 * 60 * 60 * 1000;
  
  if (!shouldShow) return null;
  
  Check working hours (9:00-18:00, Monday through Friday)
  const isWorkingTime = () => {
    const hour = currentTime.getHours();
    const day = currentTime.getDay();
    return day >= 1 && day <= 5 && hour >= 9 && hour <= 18;
  };
  
  const timeLabel = isWorkingTime() ? 'working hour' : 'off hours';
  const timeColor = isWorkingTime() ? '#52c41a' : '#faad14';
  
  return (
    <div style={{ textAlign: 'center', margin: '16px 0' }}>
      <span style={{
        backgroundColor: timeColor,
        color: 'white',
        padding: '2px 8px',
        borderRadius: '12px',
        marginRight: '8px'
      }}>
        {timeLabel}
      </span>
      {currentTime.toLocaleString()}
    </div>
  );
};

function ChatApp() {
  return (
    <Chat>
      <MessageList MessageTimeDivider={BusinessTimeDivider} />
    </Chat>
  );
}
```

## Summary

The MessageList component provides complete message list functionality and various custom options. By reasonably configuring Props and custom components, you can create a chat interface that meets business requirements. It is advisable to select appropriate configuration based on specific scenarios in actual use.

---

## Platform: Vue

# Overview

The MessageList component is the core component of the chat UI, responsible for showing the message list and providing abundant interaction features. It supports advanced features like message aggregation, auto-scroll, historical message loading, and read receipts, ideal for building highly customizable chat interfaces. Within the component, it handles scrolling behavior, message loading logic, and message grouping display, delivering a smooth browsing experience for users.

## Features
-Message display: support various message types (text, image, video, file, custom message) for rendering and display.

- Message aggregation: auto-group adjacent messages based on time interval and sender.

- Smart scroll: auto-scroll to the bottom with manual scroll control.

- History load: load more historical messages.

- Read receipt: support message read status display.

- Message operations: support actions such as recall and delete.

- Custom rendering: support custom message component and time segmentation component.

## Quick and Easy to Use

The following example shows the simplest usage of MessageList.
``` typescript
<template>
  <UIKitProvider>
    <Chat>
      <MessageList />
      <MessageInput />
    </Chat>
  </UIKitProvider>
</template>

<script lang="ts" setup>
import { UIKitProvider, Chat, MessageList, MessageInput } from '@tencentcloud/chat-uikit-vue3';
</script>

```

**Note:**
Practical advice:
> 
> 1. The MessageList component must be placed in UIKitProvider and Chat containers.
> 2. MessageList is recommended to be used in conjunction with the MessageInput component.
> 3. MessageList will default read the currently active session. If you do not integrate the ConversationList component, see the integrated chat only () document.

## Props
| Field | Type | Default Value | Description |
| --- | --- | --- | --- |
| alignment | 'left' \\| 'right' \\| 'two-sided' | 'two-sided' | Message alignment mode, supports align left, align right and justify. |
| messageAggregationTime | number | 300 (seconds) | Message aggregation interval (seconds). Messages from the same sender sent within this time will be aggregated. Set to 0 to disable message aggregation. |
| enableReadReceipt | boolean | false | Whether to enable the message read receipt feature. |
| messageActionList | MessageAction[] | undefined | Custom message operations list, such as recall and delete. |
| filter | (message: MessageModel) => boolean | undefined | Custom message filter function, used to control which messages should display. |
| Message | Component | undefined | Custom message component for rendering individual messages. |
| MessageTimeDivider | Component | undefined | Custom message time segmentation component. |

## Props detailed introduction

### alignment
-Type: 'left' | 'right' | 'two-sided'

- Details: Message alignment mode in the list. `'left'` means all messages align left, `'right'` means all messages align right, `'two-sided'` means sender messages align right and recipient messages align left. Default value is `'two-sided'`.

### messageAggregationTime
- Type: number

- Details: Message aggregation interval in seconds. When the same sender sends multiple messages consecutively within the specified time interval, these messages will be aggregated and displayed. Set to `0` or a negative value to disable the aggregation function, and each message will be displayed independently. Default value is `300` (5 minutes).

### enableReadReceipt
- Type: boolean
- Details: Whether to enable the read receipt feature. When set to `true`, the component will attempt to display the read status of messages. Default value is `false`.

### messageActionList
- Type: MessageAction[]

- Details: Defines a list of custom actions that can be performed on messages, such as copy, recall, forward, and delete. This is a complex type that can be customized by importing an array of `MessageAction` objects. Default value is `undefined`, and the component will provide a default action list based on the `useMessageActions` hook.

   The current default sequence is: `['copy', 'recall', 'quote', 'forward', 'delete']`.

   **MessageAction** type definition:

   ``` typescript
   import type { Component } from 'vue';
   import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';
   
   interface MessageAction {
     /** Unique operation identifier */
     key: string;
     /** Display text of the operation */
     label: string;
     /** Icon component or icon name for the operation */
     icon?: Component | string;
     /** Callback function when the operation is clicked */
     onClick?: (message: MessageModel) => void;
     /** Controls the visibility of the operation, can be a boolean value or a function that determines visibility based on the message */
     visible?: boolean | ((message: MessageModel) => boolean);
     /** Customize component, if provided, will replace the default tag and icon */
     component?: Component;
     /** Custom class name */
     className?: string;
     /** Custom style */
     style?: Record<string, any>;
   }
   ```

   Use the `useMessageActions` hook to obtain the complete message actions list, then customize as needed. The `useMessageActions` hook accepts an optional parameter, which can be an array of action keys (`string`) or complete `MessageAction` objects. If a `string` array is input, the default operation configuration will be used. If `MessageAction` objects are input, they will merge and override the default configuration.

#### Example code 1: Change the order of message actions list

The following code places the `forward` action first in the message actions list.
``` typescript
<template>
  <MessageList :messageActionList="actions" />
</template>

<script lang="ts" setup>
import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';

const actions = useMessageActions(['forward', 'copy', 'recall', 'quote', 'delete']);
</script>
```

example effect

#### Example code 2: Only show some message actions

Only show `copy` and `recall` operations, hide other operations.
``` typescript
<template>
  <MessageList :messageActionList="actions" />
</template>

<script lang="ts" setup>
import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';

const actions = useMessageActions(['copy', 'recall']);
</script>
```

example effect

#### Example code 3: Customize message action style and logic

Here is an example of a custom forward operation:
1. Change the tag to 'Forward ⚠️'

2. Change the color to orange

3. Only messages from others can be forwarded

   ``` typescript
   <template>
     <MessageList :messageActionList="actions" />
   </template>
   
   <script lang="ts" setup>
   import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';
   
   const actions = useMessageActions(['copy', {
     key: 'forward',
     label: 'Forward ⚠️',
     style: {
       color: 'orange',
     },
     visible: (message) => message.flow === 'in',
   }, 'quote', 'recall', 'delete']);
   </script>
   ```

   example effect

   

#### Example code 4: Add new custom message action

Assume messages need to be customized, such as only allowing others' sent messages to be tagged as 'like', and inserting this after the 'undo' operation.
``` typescript
<template>
  <MessageList :messageActionList="actions" />
</template>

<script lang="ts" setup>
import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';

const customLikeAction = {
  key: 'like',
  label: 'Like',
  icon: '🩷',
  visible: (message) => message.flow === 'in',
  onClick: (message) => {
    console.log('like message:', message.ID);
    Implement the like logic here, for example by calling the backend API
  },
};

const actions = useMessageActions(['forward', 'copy', 'recall', customLikeAction, 'quote', 'delete']);
</script>
```

example effect

### filter
- type:((message: MessageModel) => boolean) | undefined

-A custom filter function for filtering messages before list rendering. The function receives a `MessageModel` message object and returns a boolean value. If it returns `true`, the message will be displayed; if `false`, the message will not be displayed. The default value is `undefined`, meaning no additional filtering is performed (but the component will filter out messages with `isDeleted` set to `true` by default).

#### Example code: Filter out text messages sent by robots

This example shows how to use the `filter` prop to filter out specific messages sent by users.
``` typescript
<template>
  <MessageList :filter="customMessageFilter" />
</template>

<script lang="ts" setup>
import { MessageList, MessageType } from '@tencentcloud/chat-uikit-vue3';
import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';

// Define custom message filter function
const customMessageFilter = (message: MessageModel): boolean => {
  if (
    message.nick?.includes('_robot') 
    && message.type === MessageType.TEXT as any
  ) {
    return false;
  }
  return true;
};
</script>
```

### Message
- Type: Component | undefined

-Description: Custom render for individual messages in the message list.

   If you need full control over message display style and internal logic, you can pass in a Vue component to replace the default message rendering. The custom component will receive props like `message` (IMessageModel type), `alignment`, and `messageActionList`. By default, the internal default message component `Message` is used.

#### Example code: CUSTOM message component for message click redirection

This example shows how to create a custom message component to render specific `CUSTOM` messages (`businessID` as `text_link`) as clickable URLs.
``` typescript
<template>
  <MessageList :Message="CustomMessage" />
</template>

<script lang="ts" setup>
import { defineComponent, h } from 'vue';
import { MessageList, Message, MessageType } from '@tencentcloud/chat-uikit-vue3';
import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';

const CustomMessage = defineComponent({
  name: 'CustomMessage',
  props: {
    message: {
      type: Object as () => MessageModel,
      required: true,
    },
    // MessageList will pass other props, which can be received as needed, such as alignment, messageActionList
  },
  setup(props) {
    return () => {
      const { message } = props;

      if (message.type === MessageType.CUSTOM as any) {
        const { businessID, ...restData } = JSON.parse(message.payload.data);
        if (businessID === 'text_link') {
          return h('div', { class: 'custom-text-link-message' }, [
            h('div', restData.text),
            h('a', { href: restData.link, target: '_blank' }, `Redirect to external website address ${restData.link}`),
          ]);
        }
      }
      Other message types use the default message component
      return h(Message, props);
    };
  },
});
</script>

<style scoped>
.custom-text-link-message {
  padding: 8px;
  background-color: #f0f8ff;
  border-radius: 8px;
  max-width: 60%;
  margin-bottom: 4px;
}

.custom-text-link-message a {
  color: #1890ff;
  text-decoration: underline;
}
</style>
```

example effect

### MessageTimeDivider
- Type: Component | undefined

- Description: for custom rendering time segmentation component.

   You can pass in a Vue component to replace the default time divider rendering. The custom component will receive props like `currentMessage` (the associated message of the current time divider) and `previousMessage` (the previous message), and can determine the display logic and style based on this information. By default, it uses the internal `MessageTimeDivider` component.

   

#### Example code: Custom time divider component to display whether it's working hours

This example shows how to create a custom time dividing line component, determine whether the time of the last message is a working hour, and display the appropriate tag and color.
``` typescript
<template>
  <MessageList :MessageTimeDivider="BusinessTimeDivider" />
</template>

<script lang="ts" setup>
import { defineComponent, h } from 'vue';
import { MessageList } from '@tencentcloud/chat-uikit-vue3';
import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';

const BusinessTimeDivider = defineComponent({
  name: 'BusinessTimeDivider',
  props: {
    previousMessage: {
      type: Object as () => MessageModel | undefined,
      default: undefined,
    },
    currentMessage: {
      type: Object as () => MessageModel,
      required: true,
    },
  },
  setup(props) {
    if (!props.previousMessage || !props.currentMessage) {
      return () => null;
    }

    const currentTime = new Date(props.currentMessage.time * 1000);
    const prevTime = new Date(props.previousMessage.time * 1000);

    // Display only when crossing days or the interval exceeds 4 hours
    const shouldShow = currentTime.toDateString() !== prevTime.toDateString()
      || (currentTime.getTime() - prevTime.getTime()) > 4 * 60 * 60 * 1000;

    if (!shouldShow) {
      return () => null;
    }

    Working hours (9:00-18:00, Monday through Friday)
    const isWorkingTime = () => {
      const hour = currentTime.getHours();
      const day = currentTime.getDay();
      return day >= 1 && day <= 5 && hour >= 9 && hour <= 18;
    };

    const timeLabel = isWorkingTime() ? 'Working' : 'Non working';
    const timeColor = isWorkingTime() ? '#52c41a' : '#faad14';

    return () =>
      h('div', { style: { textAlign: 'center', margin: '16px 0' } }, [
        h('span', {
          style: {
            backgroundColor: timeColor,
            color: 'white',
            padding: '2px 8px',
            borderRadius: '12px',
            marginRight: '8px',
          },
        }, timeLabel),
        currentTime.toLocaleString(),
      ]);
  },
});
</script>
```

example effect

supplementary information

Common fields of the `MessageModel` type:
``` typescript
// API definition for MessageModel, see the @tencentcloud/chat-uikit-engine document for details
// This is a complex message model containing properties such as message ID, event type, sender, content, time, and status.
Sample structure (not a complete definition)
interface MessageModel {
  ID: string;
  conversationID: string;
  type: MessageType;
  payload: any;
  flow: 'in' | 'out';
  from: string;
  to: string;
  time: number; // Unix timestamp (seconds)
  status: 'unSend' | 'success' | 'fail';
  isDeleted: boolean;
  isRevoked: boolean;
  hasRiskContent: boolean; // whether the content contains risk
  // ... other attributes
}

enum MessageType {
  TEXT,     // Text
  IMAGE
  AUDIO,    // Audio
  VIDEO,    // Video
  FILE,     // File
  FACE,     // Large Sticker
  LOCATION, // Location
  GRP_TIP,  // Group prompt
  CUSTOM,   // Custom
  MERGER,   // Combine and forward
}

```

For the complete content of MessageModel, see the [TUIChatEngine reference document](https://web.sdk.qcloud.com/im/doc/chat-engine/IMessageModel.html).

Reference
- Customize Component - UIKitProvider

- Customize Component - ConversationList

- Customize Component - Chat

- Customize Component - MessageInput

- Customize Component - ChatSetting

- Customize Component - ContactList

- Customize Component - Search

- [chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

- [github Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)
