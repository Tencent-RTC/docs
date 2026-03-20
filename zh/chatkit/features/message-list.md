> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## 组件概述

MessageList 是一个功能强大的聊天消息列表组件，提供了完整的消息展示、交互和自定义功能。该组件支持消息分组、已读回执、消息操作、滚动控制等核心聊天功能，同时提供了丰富的自定义选项来满足不同的业务需求。

## Props 配置
| 字段 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| alignment | 'left' \\| 'right' \\| 'two-sided' | 'two-sided' | 消息对齐方式。 - left: 所有消息左对齐。 - right: 所有消息右对齐。 - two-sided: 发送消息右对齐，接收消息左对齐。 |
| enableReadReceipt | boolean | false | 是否启用已读回执功能。 |
| messageActionList | IMessageAction[] | undefined | 自定义消息操作列表，例如复制、撤回、删除等。 |
| messageAggregationTime | number | 300 (5分钟) | 消息聚合时间间隔（秒），相同发送者在此时间内的连续消息会被聚合显示。 |
| filter | (message: IMessageModel) => boolean | undefined | 消息过滤函数，用于控制哪些消息需要显示。 |
| className | string | undefined | 自定义 CSS 类名。 |
| style | React.CSSProperties | undefined | 自定义内联样式。 |
| Message | React.ComponentType | Message | 自定义消息组件。 |
| MessageTimeDivider | React.ComponentType | MessageTimeDivider | 自定义时间分隔线组件。 |

## Prop 字段详解

### alignment

参数类型: `'left' | 'right' | 'two-sided'`

alignment 用于设置消息对齐方式，支持 `left`、`right`、`two-sided` 三种模式。默认值为 `'two-sided'`。
- `two-sided`: 非本人的消息位于左侧，本人的消息位于右侧

- `left`: 所有消息位于左侧

- `right`: 所有消息位于右侧

### enableReadReceipt

参数类型: `boolean`

enableReadReceipt 用于设置是否开启单条消息已读回执功能，默认值为 `false`。

### messageActionList

参数类型: `IMessageAction[]`

messageActionList 用于设置消息操作列表，例如复制、撤回、删除等操作，默认值为完整的消息操作列表 `['copy', 'recall', 'quote', 'forward', 'delete']`。
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

可以使用 `useMessageAction` 获取完整的消息操作列表，然后根据需要进行自定义。

#### 示例1: 变更消息操作列表的顺序

假设按照 `forward`、`copy`、`recall`、`quote`、`delete` 的顺序显示消息操作列表。
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

#### 示例2: 仅展示部分消息操作

假设仅展示 `forward`、`copy`、`recall` 三个操作。
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

#### 示例3: 自定义消息操作的样式

下面是一个自定义撤回操作的示例：
1. 将标签修改为'撤回 ⚠️'

2. 将颜色修改为橙色

3. 只有文本消息可以撤回

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

#### 示例4: 新增自定义消息操作

假设需要自定义消息操作，如仅其他人发送的消息可以标记'喜欢'，且插入在'撤回'操作之后。
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

参数类型: `number`

messageAggregationTime 用于设置消息聚合时间间隔，将相同发送者的连续消息聚合显示，默认值为 `300`（秒）。

### filter

参数类型: `(message: IMessageModel) => boolean`

filter 用于设置消息过滤函数，控制哪些消息需要显示，默认值为 `undefined`。

#### 示例: 过滤掉机器人发送的错误消息
``` tsx
import { MessageList } from '@/components/MessageList';
import TUIChatEngine from '@tencentcloud/chat-uikit-engine';

const messageFilter = (message) => {
  // 过滤掉发送者昵称包含 '_robot' 且内容包含 'operation-failed' 的文本消息
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

参数类型: `string`

className 用于设置根容器自定义 CSS 类名，默认值为 `undefined`。

### style

参数类型: `React.CSSProperties`

style 用于设置根容器自定义内联样式，默认值为 `undefined`。

### Message

参数类型: `React.ComponentType`

Message 用于设置自定义消息组件，替换默认的消息渲染组件，默认值为内置的 `Message` 组件。

#### 示例: 自定义 CUSTOM 消息点击跳转的消息组件
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
            跳转到外部网址 {restData.link}
          </a>
        </div>
      );
    }
  } else {
    // 其他消息类型使用默认消息组件
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

参数类型: `React.ComponentType`

MessageTimeDivider 用于设置自定义时间分隔线组件，默认值为内置的 `MessageTimeDivider` 组件。

#### 示例: 区分工作时间的时间分隔线
``` tsx
import { Chat, MessageList, MessageTimeDivider } from '@tencentcloud/chat-uikit-react';

const BusinessTimeDivider: typeof MessageTimeDivider = ({ prevMessage, currentMessage }) => {
  if (!prevMessage || !currentMessage) return null;
  
  // 对于即将渲染的每一条消息获取当前消息和上一消息的时间，并转换为日期对象，判断是否跨天或间隔超过4小时
  const currentTime = new Date(currentMessage.time * 1000);
  const prevTime = new Date(prevMessage.time * 1000);
  
  // 跨天或间隔超过4小时才显示
  const shouldShow = currentTime.toDateString() !== prevTime.toDateString() ||
                    (currentTime.getTime() - prevTime.getTime()) > 4 * 60 * 60 * 1000;
  
  if (!shouldShow) return null;
  
  // 判断工作时间 (9:00-18:00, 周一到周五)
  const isWorkingTime = () => {
    const hour = currentTime.getHours();
    const day = currentTime.getDay();
    return day >= 1 && day <= 5 && hour >= 9 && hour <= 18;
  };
  
  const timeLabel = isWorkingTime() ? '工作时间' : '非工作时间';
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

## 总结

MessageList 组件提供了完整的消息列表功能和丰富的自定义选项。通过合理配置 Props 和自定义组件，您可以创建出符合业务需求的聊天界面。建议在实际使用中根据具体场景选择合适的配置。

---

## Platform: Vue

## 概述

MessageList 组件是聊天界面的核心组件，负责展示消息列表并提供丰富的交互功能。它支持消息聚合、自动滚动、历史消息加载、已读回执等高级特性，适用于构建高度可定制的聊天界面。组件内部处理滚动行为、消息加载逻辑和消息分组展示，为用户提供流畅的消息浏览体验。

## 功能特性
- 消息展示：支持多种消息类型（文本、图片、视频、文件、自定义消息等）的渲染和显示。

- 消息聚合：根据时间间隔和发送者自动聚合相邻消息。

- 智能滚动：自动滚动到底部，支持手动滚动控制。

- 历史加载：上加载更多历史消息。

- 已读回执：支持消息已读状态显示。

- 消息操作：支持撤回、删除等消息操作。

- 自定义渲染：支持自定义消息组件和时间分割线组件。

## 快速使用

以下示例代码是 MessageList 最简单的用法。
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

> **说明：**
> 

> 实践建议：
> 1. MessageList 组件需要放在 UIKitProvider、Chat 容器中使用。
> 2. MessageList 建议和 MessageInput 组件搭配使用。
> 3. MessageList 会默认读取当前激活的会话，如果您不集成 ConversationList 组件，请参考 仅集成聊天 文档。

## Props 字段速查表
| 字段 | 类型 | 默认值 | 描述 |
| --- | --- | --- | --- |
| alignment | 'left' \\| 'right' \\| 'two-sided' | 'two-sided' | 消息的对齐方式，支持左对齐、右对齐和双边对齐。 |
| messageAggregationTime | number | 300 (秒) | 消息聚合的时间间隔（秒），在此时间内发送的同一发送者的消息会被聚合。设置为 0 可禁用消息聚合。 |
| enableReadReceipt | boolean | false | 是否启用消息已读回执功能。 |
| messageActionList | MessageAction[] | undefined | 自定义消息操作列表，例如撤回、删除等。 |
| filter | (message: MessageModel) => boolean | undefined | 自定义消息过滤函数，用于控制哪些消息应该显示。 |
| Message | Component | undefined | 自定义消息组件，用于渲染单条消息。 |
| MessageTimeDivider | Component | undefined | 自定义消息时间分割线组件。 |

## Props 详细介绍

### alignment
- 类型：'left' | 'right' | 'two-sided'

- 详细说明：消息在列表中的对齐方式。`'left'` 表示所有消息左对齐，`'right'` 表示所有消息右对齐，`'two-sided'` 表示发送方消息右对齐，接收方消息左对齐。默认值为 `'two-sided'`。

### messageAggregationTime
- 类型：number

- 详细说明：消息聚合的时间间隔，单位为秒。当同一发送者在指定时间间隔内连续发送多条消息时，这些消息将被聚合显示。设置为 `0` 或更小的值将禁用消息聚合功能，每条消息都会独立显示。默认值为 `300` (5分钟)。

### enableReadReceipt
- 类型：boolean

- 详细说明：是否启用消息的已读回执功能。当设置为 `true` 时，组件将尝试显示消息的已读状态。默认值为 `false`。

### messageActionList
- 类型：MessageAction[]

- 详细说明：用于定义在消息上可以执行的自定义操作列表，例如复制、撤回、转发、删除等。这是一个复杂类型，可以通过传入一个 `MessageAction` 对象数组来定制消息的操作行为。默认值为 `undefined`，组件内部会根据 `useMessageActions` 钩子提供默认的操作列表。

   目前默认的顺序是： `['copy', 'recall', 'quote', 'forward', 'delete']`。

   **MessageAction** 类型定义:

   ``` typescript
   import type { Component } from 'vue';
   import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';
   
   interface MessageAction {
     /** 唯一的操作标识符 */
     key: string;
     /** 操作的显示文本 */
     label: string;
     /** 操作的图标组件或图标名称 */
     icon?: Component | string;
     /** 操作点击时的回调函数 */
     onClick?: (message: MessageModel) => void;
     /** 控制操作的可见性，可以是布尔值或一个根据消息决定可见性的函数 */
     visible?: boolean | ((message: MessageModel) => boolean);
     /** 自定义组件，如果提供了，将替代默认的标签和图标 */
     component?: Component;
     /** 自定义类名 */
     className?: string;
     /** 自定义样式 */
     style?: Record<string, any>;
   }
   ```

   可以使用 `useMessageActions` 钩子获取完整的消息操作列表，然后根据需要进行自定义。`useMessageActions` 钩子接收一个可选参数，可以是一个包含操作键（`string`）或完整 `MessageAction` 对象的数组。如果传入 `string` 数组，则会使用默认操作的配置；如果传入 `MessageAction` 对象，则会合并并覆盖默认配置。

#### 代码示例1：变更消息操作列表的顺序

下面的代码将 `forward`操作放到消息操作列表的第一个。
``` typescript
<template>
  <MessageList :messageActionList="actions" />
</template>

<script lang="ts" setup>
import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';

const actions = useMessageActions(['forward', 'copy', 'recall', 'quote', 'delete']);
</script>
```

示例效果：

#### 代码示例2：仅展示部分消息操作

仅展示 `copy`、`recall` 操作，其他操作隐藏。
``` typescript
<template>
  <MessageList :messageActionList="actions" />
</template>

<script lang="ts" setup>
import { MessageList, useMessageActions } from '@tencentcloud/chat-uikit-vue3';

const actions = useMessageActions(['copy', 'recall']);
</script>
```

示例效果：

#### 代码示例3：自定义消息操作的样式和逻辑

下面是一个自定义转发操作的示例：
1. 将标签修改为 '转发 ⚠️'

2. 将颜色修改为橙色

3. 只有别人发的消息可以转发

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

   示例效果：

   

#### 代码示例4：新增自定义消息操作

假设需要自定义消息操作，例如仅其他人发送的消息可以标记'喜欢'，且插入在'撤回'操作之后。
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
    // 在这里实现点赞逻辑，例如调用后端API
  },
};

const actions = useMessageActions(['forward', 'copy', 'recall', customLikeAction, 'quote', 'delete']);
</script>
```

示例效果：

### filter
- type：((message: MessageModel) => boolean) | undefined

- 详细说明：一个自定义的过滤函数，用于在消息列表渲染之前对消息进行筛选。该函数接收一个 `MessageModel` 类型的消息对象，并返回一个布尔值。如果返回 `true`，则消息会显示；如果返回 `false`，则消息不会显示。默认值为 `undefined`，表示不进行额外过滤（但组件内部会默认过滤掉 `isDeleted` 为 `true` 的消息）。

#### 代码示例：过滤掉机器人发送的文本消息

这个示例展示了如何使用 `filter` prop 来过滤掉某些用户发送的特定消息。
``` typescript
<template>
  <MessageList :filter="customMessageFilter" />
</template>

<script lang="ts" setup>
import { MessageList, MessageType } from '@tencentcloud/chat-uikit-vue3';
import type { MessageModel } from '@tencentcloud/chat-uikit-vue3';

// 定义自定义消息过滤函数
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
- 类型：Component | undefined

- 详细说明：用于自定义渲染消息列表中的单个消息组件。

   如果您需要完全控制消息的显示样式和内部逻辑，可以传入一个 Vue 组件来替代默认的消息渲染。自定义组件将接收 `message` (IMessageModel 类型)、`alignment`、`messageActionList` 等 props。默认值会使用内部的默认消息组件 `Message`。

#### 代码示例：自定义 CUSTOM 消息点击跳转的消息组件

这个示例展示了如何创建一个自定义消息组件，用于渲染特定 `CUSTOM` 消息（`businessID` 为 `text_link`）为可点击跳转的链接。
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
    // MessageList 会传递其他 props，这里可以按需接收，例如 alignment, messageActionList 等
  },
  setup(props) {
    return () => {
      const { message } = props;

      if (message.type === MessageType.CUSTOM as any) {
        const { businessID, ...restData } = JSON.parse(message.payload.data);
        if (businessID === 'text_link') {
          return h('div', { class: 'custom-text-link-message' }, [
            h('div', restData.text),
            h('a', { href: restData.link, target: '_blank' }, `跳转到外部网址 ${restData.link}`),
          ]);
        }
      }
      // 其他消息类型使用默认消息组件
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

示例效果：

### MessageTimeDivider
- 类型：Component | undefined

- 详细说明：用于自定义渲染时间分割线组件。

   您可以传入一个 Vue 组件来替代默认的时间分割线渲染。自定义组件将接收 `currentMessage` (当前时间分割线关联的消息)、`previousMessage` (上一条消息) 等 props，可以根据这些信息决定时间分割线的显示逻辑和样式。默认值会使用内部的默认时间分割线组件 `MessageTimeDivider`。

   

#### 代码示例：自定义时间分割线组件，根据时间展示是否为上班时间

这个示例展示了如何创建一个自定义时间分隔线组件，根据消息时间判断是否为工作时间，并显示相应的标签和颜色。
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

    // 跨天或间隔超过4小时才显示
    const shouldShow = currentTime.toDateString() !== prevTime.toDateString()
      || (currentTime.getTime() - prevTime.getTime()) > 4 * 60 * 60 * 1000;

    if (!shouldShow) {
      return () => null;
    }

    // 判断工作时间 (9:00-18:00, 周一到周五)
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

示例效果：

## 扩展资料

`MessageModel` 类型常用字段：
``` typescript
// MessageModel 接口定义，详细内容请参考 @tencentcloud/chat-uikit-engine 文档
// 这是一个复杂的消息模型，包含消息ID、类型、发送者、内容、时间、状态等属性。
// 示例结构 (非完整定义):
interface MessageModel {
  ID: string;
  conversationID: string;
  type: MessageType;
  payload: any;
  flow: 'in' | 'out';
  from: string;
  to: string;
  time: number; // Unix 时间戳 (秒)
  status: 'unSend' | 'success' | 'fail';
  isDeleted: boolean;
  isRevoked: boolean;
  hasRiskContent: boolean; // 是否包含风险内容
  // ... 其他属性
}

enum MessageType {
  TEXT,     // 文本
  IMAGE,    // 图像
  AUDIO,    // 音频
  VIDEO,    // 视频
  FILE,     // 文件
  FACE,     // 大表情
  LOCATION, // 定位
  GRP_TIP,  // 群提示
  CUSTOM,   // 自定义
  MERGER,   // 合并转发
}

```

MessageModel 的完整内容请参见：[TUIChatEngine 参考文档](https://web.sdk.qcloud.com/im/doc/chat-engine/IMessageModel.html)。

## 相关文档
- 自定义组件 - UIKitProvider

- 自定义组件 - ConversationList

- 自定义组件 - Chat

- 自定义组件 - MessageInput

- 自定义组件 - ChatSetting

- 自定义组件 - ContactList

- 自定义组件 - Search

- [chat-uikit-vue3 npm](https://www.npmjs.com/package/@tencentcloud/chat-uikit-vue3)

- [github Demo](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/demos/rtcube-vite-vue3)

## 交流与反馈

如遇任何问题，可联系 [官网售后](https://cloud.tencent.com/act/event/connect-service#/) 反馈，享有专业工程师的支持，解决您的难题。
