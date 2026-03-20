> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationActions` component is responsible for row operations on a single session, supported by default with delete conversation, pin conversation to top/unpin, and mute/unmute features.

## Basic Usage

When using `ConversationActions` in `ConversationList`, you can customize it directly through the top-level `actionsConfig` parameter of `ConversationList`. `actionsConfig` supports default conversation operation feature toggles, event response, adding new custom action items, and basic UI customization.

For advanced customization, you can create new components through `ConversationActions`.

### Using ActionsConfig Basic Customization
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

### Custom ConversationActions Component
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

`ConversationActions` component API type `ConversationActionsProps` is based on `ConversationActionsConfig` API and expanded. 
| ##### **ConversationActionsProps** |  |  |  |
| --- | --- | --- | --- |
| **Parameter Name** | **Type** | **Default Value** | **Note** |
| **conversation*****(Required)*** | ConversationModel | - | This parameter is required and represents the session for the current rendered conversation operation. |
| className | String | - | Custom root element class name. |
| style | React.CSSProperties | - | Custom root element style. |
| ##### ConversationActionsConfig |  |  |  |
| **Parameter Name** | **Type** | **Default Value** | **Note** |
| enablePin | Boolean | true | Whether to display the pin to top feature button. |
| enableMute | Boolean | true | Whether to display the do not disturb feature button. |
| enableDelete | Boolean | true | Whether to display the deletion feature button. |
| onConversationPin | (conversation: ConversationModel, e?: React.MouseEvent) => void | - | Customize the behavior of pinning/unpinning conversations. |
| onConversationMute | (conversation: ConversationModel, e?: React.MouseEvent) => void | - | Customize the behavior of do not disturb/cancel Do Not Disturb session. |
| onConversationDelete | (conversation: ConversationModel, e?: React.MouseEvent) => void | - | Customize the behavior of deleting conversations. |
| customConversationActions | Record<string, ConversationActionItem> | - | Custom session operation items. |
| PopupIcon | ReactElement | - | Custom action pop-up icon. |
| PopupElements | ReactElement[] | - | Content of the custom action popup. |
| onClick | (e: React.MouseEvent, key?: string, conversation?: ConversationModel) => void | - | Callback function for click action. |

## Customize Component

### Basic Feature Switch

By setting the `enablePin`, `enableDelete`, and `enableMute` parameters, you can flexibly control the display of conversation pinning, mute message notification, and conversation deletion in `ConversationActions`.
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

### Event Response

`ConversationActions` component supports delete conversation, pin conversation to top/unpin, and mute/unmute features by default. If the event response of existing functionality does not meet your needs, you can customize the event response handling function and override it. In addition to customizing feature event responses, you can also obtain basic click event response through onClick.
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

`customConversationActions` is used to add new custom action items to the ConversationActions list.
| ##### ConversationActionItem |  |  |  |
| --- | --- | --- | --- |
| **Parameter Name** | **Type** | **Default Value** | **Note** |
| enable | Boolean | true | Whether to enable custom action items. |
| label | String | - | Display content of custom action items. |
| onClick | (conversation: ConversationModel, e?: React.MouseEvent) => void | - | Callback function for clicking a custom action item. |

Here is an example of adding new custom action items using `customConversationActions`:
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
| **Before modification** | **After modification** |
| --- | --- |
|  |  |

### UI Interface Customization

You can customize the wakeup popup button style through the `PopupIcon` parameter and the popup content through `PopupElements`.

The following is example code for secondary development on the basis of the default `ConversationActions` component to customize a new awakening button style:
``` typescript
import { ConversationList, ConversationActions, ConversationActionsProps } from '@tencentcloud/chat-uikit-react';
import type { ConversationActionsProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationActions = (props: ConversationActionsProps) => {
  const customIcon = <div>Custom Icon</div>
  return (
    <ConversationActions {...props} PopupIcon={customIcon} />
  );
};
     
<ConversationList ConversationActions={CustomConversationActions} />
```
| **Before modification** | **After modification** |
| --- | --- |
|  |  |

---

## Platform: Vue

## Overview

The ConversationActions component handles operations for a single conversation, supporting delete, pin, mute, and mark unread/read by default.

## Basic Usage

In `ConversationList`, you can quickly customize conversation actions using the `actionsConfig` prop.
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

## Advanced Usage

For deeper customization, you can directly replace the `ConversationActions` prop of `ConversationList`.
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

The `ConversationActionsProps` interface type for the ConversationActions component extends from the `ConversationActionsConfig` interface.

### ConversationActionsProps
| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| conversation(Required) | ConversationModel | - | Required parameter, represents the conversation for the current rendered conversation action |
| className | string | - | Custom class name for root element |
| style | CSSProperties | - | Custom style for root element |

### ConversationActionsConfig
| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| enablePin | boolean | true | Whether to show the pin action button |
| enableMute | boolean | true | Whether to show the mute action button |
| enableDelete | boolean | true | Whether to show the delete action button |
| enableMarkUnread | boolean | true | Whether to show the mark unread action button |
| onMarkConversationUnread | (conversation: ConversationModel, e?: Event) => void | - | Custom callback for marking conversation as unread/read |
| onConversationPin | (conversation: ConversationModel, e?: Event) => void | - | Custom callback for pinning/unpinning conversation |
| onConversationMute | (conversation: ConversationModel, e?: Event) => void | - | Custom callback for muting/unmuting conversation |
| onConversationDelete | (conversation: ConversationModel, e?: Event) => void | - | Custom callback for deleting conversation |
| customConversationActions | Record<string, ConversationActionItem> | - | Custom conversation action items |
| PopupIcon | any | - | Custom icon for action popup |
| PopupElements | any[] | - | Custom content for action popup |
| onClick | (e: Event, key?: string, conversation?: ConversationModel) => void | - | Callback when clicking action item |
| onClose | () => void | - | Callback when closing action popup (H5 only) |

### ConversationActionItem
| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| enable | boolean | true | Whether to enable the custom action item |
| label | string | - | Display content for custom action item |
| onClick | (conversation: ConversationModel, e?: Event) => void | - | Callback when clicking custom action item |

## Events
| Event Name | Parameters | Description |
| --- | --- | --- |
| click | (e: Event, key?: string, conversation?: ConversationModel) | Triggered when clicking an action item |
| close | () | Triggered when closing the action popup |
| markConversationUnread | (conversation: ConversationModel, e?: Event) | Triggered when marking conversation as unread/read |
| conversationPin | (conversation: ConversationModel, e?: Event) | Triggered when pinning/unpinning conversation |
| conversationMute | (conversation: ConversationModel, e?: Event) | Triggered when muting/unmuting conversation |
| conversationDelete | (conversation: ConversationModel, e?: Event) | Triggered when deleting conversation |

## Custom Components

### Basic Feature Toggles

By setting `enablePin`, `enableDelete`, `enableMute`, and `enableMarkUnread` parameters, you can flexibly control the display of conversation pin, mute, delete, and mark unread in `ConversationActions`.
``` typescript
<template>
  <!-- Disable pin feature -->
  <ConversationActions :enablePin="false" :conversation="conversation" />

  <!-- Disable delete feature -->
  <ConversationActions :enableDelete="false" :conversation="conversation" />

  <!-- Disable mute feature -->
  <ConversationActions :enableMute="false" :conversation="conversation" />

  <!-- Disable mark unread feature -->
  <ConversationActions :enableMarkUnread="false" :conversation="conversation" />
</template>
```
| `enablePin: false` | `enableDelete: false` | `enableMute: false` | enableMarkUnread="false" |
| --- | --- | --- | --- |
|  |  |  |  |

### Event Handling

The ConversationActions component supports conversation delete, pin/unpin, mute/unmute, and mark unread/read by default. If the existing event handling doesn't meet your needs, you can customize and override the event handling functions. In addition to customizing event handling, you can also get basic click events through `@click`.
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

### Custom Actions

customConversationActions is used to add custom action items to ConversationActions.

**Example: Add Custom Action Item**
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

### UI Customization

You can customize the popup trigger button style through the `PopupIcon` parameter, and customize the popup content through `PopupElements`.

**Example: Custom Trigger Button**
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
