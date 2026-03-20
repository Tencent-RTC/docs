> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationActions` 组件负责对于单条会话进行操作，默认支持会话删除、会话置顶/取消置顶、会话免打扰/取消免打扰功能。

## 基础使用

当在 `ConversationList` 使用 `ConversationActions` 时，您可以通过 `ConversationList` 顶层 `actionsConfig` 参数直接进行自定义，`actionsConfig` 支持对于默认会话操作功能开关、事件响应、新增自定义操作项、基础 UI 定制等。

如您需要更高阶的定制，您可以通过 `ConversationActions` 自定义新的组件。

### 使用 actionsConfig 基础定制
``` typescript
import { UIKitProvider, ConversationList } from "@tencentcloud/chat-uikit-react";
import type { ConversationModel } from "@tencentcloud/chat-uikit-react";

const App = () => {
  return (
    <UIKitProvider>
      <ConversationList
        actionsConfig={{
          enablePin: false,
          onConversationDelete: (conversation: ConversationModel) => { console.log('Delete conversation success'); },
          customConversationActions: {
            'custom-actions-1': {
               label: 'custom-actions',
               onClick: (conversation: ConversationModel) => { console.log(conversation); },
             },
          },
      }}/>
    </UIKitProvider>
  );
}
```

### 自定义 ConversationActions 组件
``` typescript
import { UIKitProvider, ConversationList, ConversationActions } from "@tencentcloud/chat-uikit-react";
import type { ConversationActionsProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationActions = (props: ConversationActionsProps) => {
  return <ConversationActions {...props} enableDelete={false} />;
};

const App = () => {
  return (
    <UIKitProvider>
      <ConversationList
        style={{ maxWidth: '300px', height: '600px' }}
        ConversationActions={CustomConversationActions}
      />
    </UIKitProvider>
  );
}
```

## Props

`ConversationActions` 组件的接口类型 `ConversationActionsProps` 基于 `ConversationActionsConfig` 接口进行扩展。 
| ##### **IConversationActionsProps** |  |  |  |
| --- | --- | --- | --- |
| **参数名** | **类型** | **默认值** | **说明** |
| **conversation*****(Required)*** | `ConversationModel` | - | 必选参数，表示当前渲染会话操作对应的会话。 |
| className | `String` | - | 自定义根元素的类名。 |
| style | `React.CSSProperties` | - | 自定义根元素的样式。 |
| ##### IConversationActionsConfig |  |  |  |
| **参数名** | **类型** | **默认值** | **说明** |
| enablePin | `Boolean` | `true` | 是否显示置顶功能按钮。 |
| enableMute | `Boolean` | `true` | 是否显示免打扰功能按钮。 |
| enableDelete | `Boolean` | `true` | 是否显示删除功能按钮。 |
| onConversationPin | `(conversation: ConversationModel, e?: React.MouseEvent) => void` | - | 自定义置顶/取消置顶会话的行为。 |
| onConversationMute | `(conversation: ConversationModel, e?: React.MouseEvent)` `=> void` | - | 自定义免打扰/取消免打扰会话的行为。 |
| onConversationDelete | `(conversation: ConversationModel, e?: React.MouseEvent) => void` | - | 自定义删除会话的行为。 |
| customConversationActions | `Record<string, ``ConversationActionItem``>` | - | 自定义会话操作项。 |
| PopupIcon | `ReactElement` | - | 自定义动作弹窗的图标。 |
| PopupElements | `ReactElement[]` | - | 自定义动作弹窗的内容。 |
| onClick | `(e: React.MouseEvent, key?: string, conversation?: ConversationModel) => void` | - | 点击动作项时的回调函数。 |

## 自定义组件

### 基础功能开关

通过设置 `enablePin`, `enableDelete` 和 `enableMute` 参数，您可以灵活地控制 `ConversationActions` 中的会话置顶、会话免打扰和会话删除的显示。
``` typescript
<ConversationActions enablePin={false} />
```
``` typescript
<ConversationActions enableDelete={false} />
```
``` typescript
<ConversationActions enableMute={false} />
```
| `enablePin={false}` | `enableDelete={false}` | `enableMute={false} ` |
| --- | --- | --- |
|  |  |  |

### 事件响应

`ConversationActions` 组件默认支持会话删除、会话置顶/取消置顶、会话免打扰/取消免打扰功能，如已有功能的事件响应不符合您的需求，您可以自定义事件响应处理函数并进行覆盖; 除了支持对于功能事件响应的自定义外，您还可以通过 onClick 获取基础点击事件响应。
``` typescript
import { ConversationList, ConversationActions, Toast } from '@tencentcloud/chat-uikit-react';
import type { ConversationActionsProps, ConversationModel } from '@tencentcloud/chat-uikit-react';

const CustomConversationActions = (props: ConversationActionsProps) => {
  return (
    <ConversationActions
      {...props}
      onConversationDelete={(conversation: ConversationModel) => {
        conversation.deleteConversation().then(() => {
          Toast({ text: 'delete conversation successfully!', type: 'info' });
        }).catch(() => {
          Toast({ text: 'delete conversation failed!', type: 'error' });
        });
      }}
    />
  );
};

<ConversationList ConversationActions={CustomConversationActions} />
```

### customConversationActions

`customConversationActions` 用于在 ConversationActions 上新增自定义操作项列表。
| ##### ConversationActionItem |  |  |  |
| --- | --- | --- | --- |
| **参数名** | **类型** | **默认值** | **说明** |
| enable | `Boolean` | `true` | 是否启用自定义操作项。 |
| label | `String` | - | 自定义操作项的展示内容。 |
| onClick | `(conversation: ConversationModel, e?: React.MouseEvent) => void` | - | 点击自定义操作项时的回调函数。 |

以下是使用 `customConversationActions` 新增自定义操作项的示例：
``` typescript
import { ConversationList, ConversationActions } from '@tencentcloud/chat-uikit-react';
import type { ConversationActionsProps, ConversationModel } from '@tencentcloud/chat-uikit-react';

const CustomConversationActions = (props: ConversationActionsProps) => {
  return (
    <ConversationActions 
      {...props} 
      customConversationActions={{
        'custom-actions-1': {
           label: 'custom-actions',
           onClick: (conversation: ConversationModel) => { console.log(conversation); },
         },
      }}
    />
  );
};
     
<ConversationList ConversationActions={CustomConversationActions} />
```
| **修改前** | **修改后** |
| --- | --- |
|  |  |

### UI 界面定制

您可以通过 `PopupIcon` 参数定制唤醒弹出按钮样式，通过 `PopupElements` 定制弹出内容。

以下是在默认的 `ConversationActions` 组件的基础上进行二次开发，为其定制新的唤起按钮样式代码示例：
``` typescript
import { ConversationList, ConversationActions, ConversationActionsProps } from '@tencentcloud/chat-uikit-react';
import type { ConversationActionsProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationActions = (props: ConversationActionsProps) => {
  const customIcon = <div> 自定义 Icon </div>
  return (
    <ConversationActions {...props} PopupIcon={customIcon} />
  );
};
     
<ConversationList ConversationActions={CustomConversationActions} />
```
| **修改前** | **修改后** |
| --- | --- |
|  |  |

---

## Platform: Vue

## 概述

ConversationActions 组件负责单条会话的操作，默认支持删除、置顶、免打扰和标记未读/已读。

## 基础用法

在 `ConversationList` 中，可以通过 `actionsConfig` 属性快速自定义会话操作。
``` typescript
<template>
  <UIKitProvider>
    <ConversationList :actions-config="actionsConfig" />
  </UIKitProvider>
</template>

<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';

const actionsConfig = {
  enablePin: false,
  onConversationDelete: (conversation: ConversationModel) => { 
    console.log('Delete conversation success'); 
  },
  customConversationActions: {
    'custom-actions-1': {
      label: 'custom-actions',
      onClick: (conversation: ConversationModel) => { 
        console.log(conversation); 
      },
    },
  },
};
</script>
```

## 高阶用法

如果需要更深度的定制，可以直接替换 `ConversationList` 的 `ConversationActions` 属性。
``` typescript
<template>
  <UIKitProvider>
    <ConversationList
      :ConversationActions="CustomConversationActions"
    />
  </UIKitProvider>
</template>

<script setup lang="ts">
import { defineComponent } from 'vue';
import { UIKitProvider, ConversationList, ConversationActions } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationActionsProps } from '@tencentcloud/chat-uikit-vue3';

const CustomConversationActions = defineComponent({
  name: 'CustomConversationActions',
  setup(props: ConversationActionsProps) {
    return () => h(ConversationActions, {
      ...props,
      enableDelete: false
    });
  }
});
</script>
```

## Props

ConversationActions 组件的接口类型 `ConversationActionsProps` 基于 `ConversationActionsConfig` 接口进行扩展。

### ConversationActionsProps
| 参数名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| conversation(Required) | ConversationModel | - | 必选参数，表示当前渲染会话操作对应的会话 |
| className | string | - | 自定义根元素的类名 |
| style | CSSProperties | - | 自定义根元素的样式 |

### ConversationActionsConfig
| 参数名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| enablePin | boolean | true | 是否显示置顶功能按钮 |
| enableMute | boolean | true | 是否显示免打扰功能按钮 |
| enableDelete | boolean | true | 是否显示删除功能按钮 |
| enableMarkUnread | boolean | true | 是否显示标记未读功能按钮 |
| onMarkConversationUnread | (conversation: ConversationModel, e?: Event) => void | - | 自定义标记未读/已读会话的行为 |
| onConversationPin | (conversation: ConversationModel, e?: Event) => void | - | 自定义置顶/取消置顶会话的行为 |
| onConversationMute | (conversation: ConversationModel, e?: Event) => void | - | 自定义免打扰/取消免打扰会话的行为 |
| onConversationDelete | (conversation: ConversationModel, e?: Event) => void | - | 自定义删除会话的行为 |
| customConversationActions | Record<string, ConversationActionItem> | - | 自定义会话操作项 |
| PopupIcon | any | - | 自定义动作弹窗的图标 |
| PopupElements | any[] | - | 自定义动作弹窗的内容 |
| onClick | (e: Event, key?: string, conversation?: ConversationModel) => void | - | 点击动作项时的回调函数 |
| onClose | () => void | - | 关闭动作弹窗时的回调函数（H5 专用） |

### ConversationActionItem
| 参数名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| enable | boolean | true | 是否启用自定义操作项 |
| label | string | - | 自定义操作项的展示内容 |
| onClick | (conversation: ConversationModel, e?: Event) => void | - | 点击自定义操作项时的回调函数 |

## Events
| 事件名 | 参数 | 说明 |
| --- | --- | --- |
| click | (e: Event, key?: string, conversation?: ConversationModel) | 点击操作项时触发 |
| close | () | 关闭操作弹窗时触发 |
| markConversationUnread | (conversation: ConversationModel, e?: Event) | 标记会话未读/已读时触发 |
| conversationPin | (conversation: ConversationModel, e?: Event) | 置顶/取消置顶会话时触发 |
| conversationMute | (conversation: ConversationModel, e?: Event) | 免打扰/取消免打扰会话时触发 |
| conversationDelete | (conversation: ConversationModel, e?: Event) | 删除会话时触发 |

## 自定义组件

### 基础功能开关

通过设置 `enablePin`、`enableDelete`、`enableMute` 和 `enableMarkUnread` 参数，您可以灵活地控制 `ConversationActions` 中的会话置顶、会话免打扰、会话删除和标记未读的显示。
``` typescript
<template>
  <!-- 禁用置顶功能 -->
  <ConversationActions :enablePin="false" :conversation="conversation" />

  <!-- 禁用删除功能 -->
  <ConversationActions :enableDelete="false" :conversation="conversation" />

  <!-- 禁用免打扰功能 -->
  <ConversationActions :enableMute="false" :conversation="conversation" />

  <!-- 禁用标记未读功能 -->
  <ConversationActions :enableMarkUnread="false" :conversation="conversation" />
</template>
```
| `enablePin: false` | `enableDelete: false` | `enableMute: false` | enableMarkUnread="false" |
| --- | --- | --- | --- |
|  |  |  |  |

### 事件响应

ConversationActions 组件默认支持会话删除、会话置顶/取消置顶、会话免打扰/取消免打扰、标记未读/已读功能，如已有功能的事件响应不符合您的需求，您可以自定义事件响应处理函数并进行覆盖；除了支持对于功能事件响应的自定义外，您还可以通过 `@click` 获取基础点击事件响应。
``` typescript
<template>
  <ConversationList :conversation-actions="CustomConversationActions" />
</template>

<script setup lang="ts">
import { defineComponent, h } from 'vue';
import { ConversationActions, Toast } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationActionsProps, ConversationModel } from '@tencentcloud/chat-uikit-vue3';

const CustomConversationActions = defineComponent({
  name: 'CustomConversationActions',
  setup(props: ConversationActionsProps) {
    const handleConversationDelete = (conversation: ConversationModel) => {
      conversation.deleteConversation().then(() => {
        console.log('delete conversation successfully!');
      }).catch(() => {
        console.log('delete conversation failed!');
      });
    };

    return () => h(ConversationActions, {
      ...props,
      onConversationDelete: handleConversationDelete
    });
  }
});
</script>
```

### 自定义操作

customConversationActions 用于在 ConversationActions 上新增自定义操作项列表。

**示例：新增自定义操作项**
``` typescript
<template>
  <ConversationList :ConversationActions="CustomConversationActions" />
</template>

<script setup lang="ts">
import { defineComponent, h } from 'vue';
import { ConversationActions } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationActionsProps, ConversationModel } from '@tencentcloud/chat-uikit-vue3';

const CustomConversationActions = defineComponent({
  name: 'CustomConversationActions',
  setup(props: ConversationActionsProps) {
    return () => h(ConversationActions, {
      ...props,
      customConversationActions: {
        'custom-actions-1': {
          label: 'custom-actions',
          onClick: (conversation: ConversationModel) => { 
            console.log(conversation); 
          },
        },
      }
    });
  }
});
</script>
```

### UI 界面定制

您可以通过 `PopupIcon` 参数定制唤醒弹出按钮样式，通过 `PopupElements` 定制弹出内容。

**示例：自定义唤起按钮**
``` typescript
<template>
  <ConversationList :ConversationActions="CustomConversationActions" />
</template>

<script setup lang="ts">
import { defineComponent, h } from 'vue';
import { ConversationActions } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationActionsProps } from '@tencentcloud/chat-uikit-vue3';

const CustomConversationActions = defineComponent({
  name: 'CustomConversationActions',
  setup(props: ConversationActionsProps) {
    const customIcon = h('div', '自定义 Icon');

    return () => h(ConversationActions, {
      ...props,
      PopupIcon: customIcon
    });
  }
});
</script>
```
