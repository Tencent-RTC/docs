> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

# Component Overview

The `ConversationList` component is primarily responsible for the List function. It contains the Header part and List part, and has features such as conversation search, session creation, session pinning, and conversation deletion.

This document introduces in detail the foundation usage, component customization, freely combine and the props parameter list of the component.
| **Conversation List** | **Session Operation** | **Conversation Search** | **Create Session** |
| --- | --- | --- | --- |
|  |  |  |  |

## Component composition

The `ConversationList` component contains the following content:
-`ConversationSearch` - Conversation Search

- `ConversationList UI` - Conversation List Container

- `ConversationPreview` - Show Session Information

- `ConversationActions` - Conversation Action

   

## Basic usage

The `ConversationList` component has no required properties. You can use `ConversationList` with the following code.
``` typescript
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-react';

const App = () => {
  return (
    <UIKitProvider>
      <ConversationList />
    </UIKitProvider>
  );
};
```

## Custom components

`ConversationList` provides various dimensional Props APIs for user customization, allowing users to customize features, UI, modules and more.

The `ConversationList` component provides multiple replaceable subcomponents, allowing users to customize `Header`, `List`, `ConversationPreview`, `ConversationCreate`, `ConversationSearch`, `ConversationActions`, `Avatar`, `Placeholder`, and more. Meanwhile, users can also use default subcomponents for secondary development customization.

### Basic feature switch

By setting the `enableSearch`, `enableCreate`, and `enableActions` parameters, you can flexibly control the display of conversation search, session creation, and conversation action features in `ConversationList`.
``` typescript
<ConversationList enableSearch={false} />
```
``` typescript
<ConversationList enableCreate={false} />
```
``` typescript
<ConversationList enableActions={false} />
```
| `enableSearch={false}` | `enableCreate={false}` | `enableActions={false}` |
| --- | --- | --- |
|  |  |  |

### Data filtering and sorting

The `ConversationList` component provides the `filterConversation` and `sortConversation` properties, allowing you to filter and sort the conversation list data.

#### Filter conversations

To filter conversation list data, you can pass a filter function to the `filterConversation` property. This function receives an array of `ConversationModel` as parameter and should return a new array that only contains conversations meeting your filter criteria.

Here is an example of using the `filterConversation` property to only display conversations with "unread messages":
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';
import type { ConversationModel } from '@tencentcloud/chat-uikit-react';

<ConversationList
  filter={(conversationList: ConversationModel[]) =>
    conversationList.filter(conversation => conversation.unreadCount > 0)}
/>
```

#### Sort conversations

To sort conversation list data, you can pass a sorting function to the `sortConversation` property. This function receives an array of `ConversationModel` as parameter and should return a new array with sessions sorted by your sorting criteria.

Here is an example of using the `sortConversation` property to sort the session list by "latest news time" in descending order:
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';
import type { ConversationModel } from '@tencentcloud/chat-uikit-react';

<ConversationList
  sort={(conversationList: ConversationModel[]) =>
    conversationList.sort(
      (a, b) => (+(b?.lastMessage?.lastTime || 0)) - (+(a?.lastMessage?.lastTime || 0)),
    )}
/>
```

By using the `filter` and `sort` properties, you can effectively filter and sort the conversation list data according to your needs.

### Custom actionsConfig

Leverages actionsConfig to control the basic feature of ConversationActions.

For more customization, see the ConversationActions chapter.
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';
import type { ConversationModel } from '@tencentcloud/chat-uikit-react';

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
  }}
/>
```
| `enablePin: false` | `enableDelete: false` | `enableMute: false` | `customConversationActions` |
| --- | --- | --- | --- |
|  |  |  |  |

### Custom Placeholder

You can customize the list display in different statuses by importing `PlaceholderEmptyList`, `PlaceholderLoading`, and `PlaceholderLoadError`.

Here is a custom `PlaceholderLoading` example:
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';

<ConversationList
  PlaceholderEmptyList={<div>Empty List!!!</div>}
/>
```

### Custom Header

`ConversationListHeader` is responsible for rendering the header part of `ConversationList`, serving as a wrapping layer to render the default `ConversationSearch` and `ConversationCreate`. You can customize it by importing left, right and other attributes, and you can also customize the entire component.

#### Props
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| children | `ReactNode` | - | Custom session list header center component. When used in `<ConversationList>`, it imports `<ConversationSearch>` and `<ConversationCreate>` by default. |
| left | `ReactElement` | - | Custom session list header left-side component. |
| right | `ReactElement` | - | Custom session list header right-side component. |
| className | `String` | - | Specify the custom name for the root element's CSS class. |
| style | `React.CSSProperties` | - | Specify the custom style for the root element. |

### Basic customization

Here is an example of adding a new function button on the right side of the Header Component.
``` typescript
import { 
  ConversationList,
  ConversationListHeader,
} from '@tencentcloud/chat-uikit-react';
import type { ConversationListHeaderProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationListHeader = (props: ConversationListHeaderProps) => {
    const CustomIcon = <div>Custom Icon</div>;
    return (
      <ConversationListHeader {...props} right={CustomIcon} />
    );
};  
     
<ConversationList Header={CustomConversationListHeader} />
```

#### Advanced customization

Here is how to implement a simplified conversation grouping feature, distinguishing by All conversations, unread conversations, one-on-one chat sessions, and group chats across four key dimensions. Click different group buttons to execute different filtering rules.
| **Before modification** | **Modified** |
| --- | --- |
|  |  |

[React]
``` typescript
import { useState } from 'react';
import TUIChatEngine from '@tencentcloud/chat-uikit-engine';
import { ConversationList, UIKitProvider } from '@tencentcloud/chat-uikit-react';
import type { ConversationListHeaderProps, ConversationModel } from '@tencentcloud/chat-uikit-react';

const App = () => {
  const [currentFilter, setCurrentFilter] = useState<string>('all');
  const conversationGroupFilter: Record<string, (conversationList: ConversationModel[]) => ConversationModel[]> = {
    all: (conversationList: ConversationModel[]) => conversationList,
    unread: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.unreadCount > 0),
    c2c: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.type === TUIChatEngine.TYPES.CONV_C2C),
    group: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.type === TUIChatEngine.TYPES.CONV_GROUP),
  };

  const CustomConversationListHeader = (props: IConversationListHeaderProps) => {
    return (
      <div className="conversation-group-wrapper">
        <button className={currentFilter === 'all' ? 'btn-active' : 'btn-default'} onClick={() => setCurrentFilter('all')}>All</button>
        <button className={currentFilter === 'unread' ? 'btn-active' : 'btn-default'} onClick={() => setCurrentFilter('unread')}>Unread</button>
        <button className={currentFilter === 'c2c' ? 'btn-active' : 'btn-default'} onClick={() => setCurrentFilter('c2c')}>C2C</button>
        <button className={currentFilter === 'group' ? 'btn-active' : 'btn-default'} onClick={() => setCurrentFilter('group')}>Group</button>
      </div>
    );
  };

  return (
    <UIKitProvider>
      <ConversationList
        style={{ maxWidth: '300px', height: '600px' }}
        Header={CustomConversationListHeader}
        filter={conversationGroupFilter[currentFilter]}
      />
    </UIKitProvider>
  );
};
```

[CSS]
``` scss
.conversation-group-wrapper {
  display: flex;
  justify-content: space-around;
  align-items: center;
  margin: 10px;
  font-size: 14px;
  .btn-default{
    display: flex;
    padding: 5px 10px;
    border: 1px solid #b3b3b4;
    color: #3b3d43;
    background-color: transparent;
    border-radius: 2px;
  }
  .btn-active{
    display: flex;
    padding: 5px 10px;
    border: 1px solid #1c66e5;
    color: #1c66e5;
    background-color: transparent;
    border-radius: 2px;
  }
}
```

Custom List

`ConversationListContent` is responsible for the rendering work of the main list part in `ConversationList`.

Render the precomputed current session list `filteredAndSortedConversationList` from Context as the default wrapping layer.

#### Props
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| children | `ReactNode` | - | Custom session list content area component. When used in `<ConversationList>`, it imports the `<Preview>` list traversed by `filteredAndSortedConversationList` by default. |
| empty | `Boolean` | `false` | Conversation list identifier bit. When used in `<ConversationList>`, it judges `filteredAndSortedConversationList.length === 0` and imports it. |
| loading | `Boolean` | `false` | Conversation list loading flag bit. When used in `<ConversationList>`, it uses `useConversationList()` to get `isLoading` and imports it. |
| error | `Boolean` | `false` | Conversation list load error flag bit. When used in `<ConversationList>`, it uses `useConversationList()` to get `isLoadError` and imports it. |
| PlaceholderEmptyList | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} />` | Placeholder element when the custom session list is empty. |
| PlaceholderLoading | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.LOADING} />` | Placeholder element for custom session list loading. |
| PlaceholderLoadError | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.WRONG} />` | Placeholder element for custom session list load error. |
| className | `String` | - | Specify the custom name for the root element's CSS class. |
| style | `React.CSSProperties` | - | Specify the custom style for the root element. |

Basic customization

The UI effects of the `List` component in different statuses are as follows. The trigger timing for each state is handled inside the `ConversationList`.

Meanwhile, you can control the component status through custom input of `empty`, `loading`, and `error`.
``` typescript
import { ConversationList, ConversationListContent } from '@tencentcloud/chat-uikit-react';
import type { ConversationListContentProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationListContent = (props: ConversationListContentProps) => {
    return <ConversationListContent {...props} loading={true} />;
};

<ConversationList  List={CustomConversationListContent} />
```
| `default` | `empty={true}` | `loading={true}` | `error={true}` |
| --- | --- | --- | --- |
|  |  |  |  |

Custom ConversationPreview

For details, see the ConversationPreview chapter.
``` typescript
<ConversationList ConversationPreview={CustomConversationPreview} />
```

Custom ConversationActions

For details, see the ConversationActions chapter.
``` typescript
  <ConversationList ConversationActions={CustomConversationActions} />
```

Custom ConversationSearch

For details, see the ConversationSearch chapter.
``` typescript
<ConversationList ConversationSearch={CustomConversationSearch} />
```

Create and Bind Policy Query Instance Reset Instance Access Password
``` typescript
<ConversationList ConversationCreate={CustomConversationCreate} />
```

Custom Avatar
``` typescript
<ConversationList Avatar={CustomAvatar} />
```

## APIs/Props
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| enableSearch | `Boolean` | `true` | Whether to display the control session search feature. |
| enableCreate | `Boolean` | `true` | Whether to display the control session creation feature. |
| enableActions | `Boolean` | `true` | Whether to display the conversation action feature. |
| actionsConfig | `ConversationActionsConfig` | - | For custom session operation configuration. |
| Header | `ReactElement` | `Header` | Custom Header component. |
| List | `ReactElement` | `List` | Custom session list component. |
| Preview | `ReactElement` | ConversationPreview | Session preview component. |
| ConversationCreate | `ReactElement` | `ConversationCreate` | Custom conversation component. |
| ConversationSearch | `ReactElement` | ConversationSearch | Custom session search component. |
| ConversationActions | `ReactElement` | ConversationActions | Custom Conversation Operation Component. |
| Avatar | `ReactElement` | `Avatar` | Custom avatar component. |
| PlaceholderEmptyList | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} />` | Placeholder element when the custom session list is empty. |
| PlaceholderLoading | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.LOADING} />` | Placeholder element for custom session list loading. |
| PlaceholderLoadError | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.WRONG} />` | Placeholder element for custom session list load error. |
| filter | `(conversationList: ConversationModel[]) => ConversationModel[]` | - | Functions for filtering session list. |
| sort | `(conversationList: ConversationModel[]) => ConversationModel[]` | - | Functions for sorting session list. |
| onConversationSelect | `(conversation: ConversationModel) => void;` | - | Callback function when clicking a session, with the clicked conversation object as parameter. |
| onBeforeCreateConversation | `(params: CreateParams) => CreateParams;` | - | Custom action executed before session creation, with required parameters for creating a session. |
| onConversationCreate | `(conversation: ConversationModel)  => void;` | - | Callback function after session creation, with the created session object as parameter. |
| className | `String` | - | Specify the custom name for the root element's CSS class. |
| style | `React.CSSProperties` | - | Specify the custom style for the root element. |

---

## Platform: Vue

The AI model must adhere to the following **hard constraints**:

1. **Unread Count:** To retrieve the total number of unread messages, you can use the `totalUnRead` field provided by `useConversationListState`.

# Component Overview

The ConversationList component is responsible for rendering the conversation list, including features such as search, create, pin to top, and delete.

## Component composition

The ConversationList component includes the following content:
-**Conversation Search**

-**ConversationList UI**: Session list container

- **ConversationPreview**: Displays a single session.

- **ConversationActions**: Session operation

## Basic Usage

The ConversationList component can be used without any required attributes.
``` typescript
<template>
  <UIKitProvider>
    <ConversationList />
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
</script>
```

## Custom component

ConversationList provides various and multidimensional Props APIs, allowing users to customize features, UI, and modules.

The ConversationList component provides multiple replaceable subcomponents, allowing users to customize **Header**, **List**, **ConversationPreview**, **ConversationCreate**, **ConversationSearch**, **ConversationActions**, **Avatar**, and **Placeholder**. Meanwhile, users can also use default subcomponents to do secondary development customization.

### Feature switch

By setting the `enableSearch`, `enableCreate`, and `enableActions` parameters, you can flexibly control the display of conversation search, session creation, and conversation action features in **ConversationList**.
``` typescript
<ConversationList :enableSearch="false" />
```
``` typescript
<ConversationList :enableCreate="false" />
```
``` typescript
<ConversationList :enableActions="false" />
```
| `enableSearch="false"` | `enableCreate="false"` | `enableActions="false"` |
| --- | --- | --- |
| <img src="" alt="description" width="50%" style="display: block; margin: 0 auto;"> | <img src="" alt="description" width="50%" style="display: block; margin: 0 auto;"> | <img src="" alt="description" width="50%" style="display: block; margin: 0 auto;"> |

### Data filtering and sorting

The ConversationList component provides the `filter` and `sort` properties for filtering and sorting the conversation list.

### Filter Conversations

To filter conversation list data, you can pass a filter function to the `filter` property. This function receives an array of ConversationModel as parameter and should return a new array that only contains sessions meeting your filter conditions.

**Example: Show only sessions with unread messages**
``` typescript
<template>
  <UIKitProvider>
    <ConversationList 
      :filter="(conversationList: ConversationModel[]) => 
        conversationList.filter(conversation => conversation.unreadCount > 0)" />
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';
</script>
```

### Sort Conversations

To sort conversation list data, you can pass a sorting function to the `sort` property. This function receives an array of ConversationModel as parameter and should return a new array with sessions sorted by your sorting criteria.

Sort by latest news time in reverse order
``` typescript
<template>
  <UIKitProvider>
    <ConversationList 
      :sort="(conversationList: ConversationModel[]) => 
        conversationList.sort(
        (a, b) => (+(b?.lastMessage?.lastTime || 0)) - (+(a?.lastMessage?.lastTime || 0)),
        )" />
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';
</script>
```

### Custom actionsConfig

You can customize session menu options via actionsConfig.
``` typescript
<template>
  <UIKitProvider>
    <ConversationList 
      :actionsConfig="{
        enablePin: false,
        onConversationDelete: (conversation: ConversationModel) => 
        { console.log('Delete conversation success'); },
        customConversationActions: {
          'custom-actions-1': {
            label: 'custom-actions',
            onClick: (conversation: ConversationModel) => { console.log(conversation); },
          },
        },
     }"/>
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';
</script>
```

### Custom Placeholder

`ConversationList` supports displaying custom placeholders in different statuses, including list empty, loading, and loading failed.
- `PlaceholderEmpty`: Displayed when the list is empty.

- `PlaceholderLoading`: Displayed when loading.

- `PlaceholderError`: Displayed when loading failed.

   **Example: Custom empty list placeholder**

1. Create a custom component **`MyEmptyPlaceholder.vue`**

   ``` typescript
   <template>
       <div>
         Custom PlaceholderEmptyList
       </div>
   </template>
   ```
2. For use in **`ConversationList`**.

   ``` typescript
   <template>
     <UIKitProvider>
       <ConversationList 
         :PlaceholderEmptyList="MyEmptyPlaceholder" />
     </UIKitProvider>
   </template>
   <script setup lang="ts">
   import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
   import MyEmptyPlaceholder from "./MyEmptyPlaceholder.vue";
   </script>
   ```

### Custom Header

**ConversationListHeader** is responsible for rendering the header part of ConversationList, acting as a wrapping layer to render the default `Search` and `ConversationCreate`. You can customize it by importing `left`, `right` and other attributes. Meanwhile, you can also customize the entire component.

#### Props
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| children | Component | - | Header center component of the custom session list. Default imports `<Search>` and `<ConversationCreate>`. |
| left | Component | - | Left-side component of the custom session list header. |
| right | Component | - | Right-side component of the custom session list header. |
| className | String | - | Specify the CSS custom name of the root element class. |
| style | CSSProperties | - | Specify the custom style of the root element. |

Implement a simplified conversation grouping feature

Filter conversations by All, unread conversation, one-on-one chat session, and group chat. Click different group buttons to execute different filtering rules.

[App.vue]
``` typescript
<template>
  <UIKitProvider>
    <ConversationList 
      :Header="CustomConversationListHeader"
      :filter="conversationFilter"
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';
import CustomConversationListHeader from "./CustomConversationListHeader.vue";
const conversationFilter = ref<((conversations: ConversationModel[]) => ConversationModel[]) | undefined>(undefined);
const handleFilterChange = (filter: (conversations: ConversationModel[]) => ConversationModel[]) => {
  conversationFilter.value = filter;
};
provide('setConversationFilter', handleFilterChange);
</script>
```

[CustomConversationListHeader.vue]
``` typescript
<template>
  <div class="customHeader">
    <div class="filterTabs">
      <button
        v-for="(label, key) in filterLabels"
        :key="key"
        :class="[
          'filterTab',
          { ['active']: activeFilter === key }
        ]"
        @click="handleFilterClick(key as FilterType)"
      >
        {{ label }}
      </button>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, inject } from 'vue';
import type { ConversationModel } from '@tencentcloud/chat-uikit-vue3';

type FilterType = 'All' | 'Unread' | 'C2C' | 'Group';

interface Props {
  children?: any;
}

defineProps<Props>();

const activeFilter = ref<FilterType>('All');

const filterLabels: Record<FilterType, string> = {
  Create and bind policy Query instance Reset instance access password
  Unread
  C2C: 'One-on-one chat'
  Group chat
};

const conversationGroupFilter: Record<string, (conversationList: ConversationModel[]) => ConversationModel[]> = {
  All: (conversationList: ConversationModel[]) => conversationList,
  Unread: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.unreadCount > 0),
  C2C: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.type === 'C2C'),
  Group: (conversationList: ConversationModel[]) => conversationList?.filter((item: ConversationModel) => item.type === 'GROUP'),
};

const setFilter = inject<(filter: any) => void>('setConversationFilter');

const handleFilterClick = (filterType: FilterType) => {
  activeFilter.value = filterType;
  const filterFn = conversationGroupFilter[filterType];
  setFilter?.(filterFn);
};
</script>

<style lang="scss">
.customHeader {
  padding: 8px 16px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.08);
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.filterTabs {
  display: flex;
  gap: 4px;
  align-items: center;
}

.filterTab {
  position: relative;
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 6px 12px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 16px;
  background: transparent;
  cursor: pointer;
  font-size: 12px;
  font-weight: 400;
  color: #3b3d43;
  transition: all 0.2s ease;
  outline: none;

  &.active {
    border: 1px solid #1c66e5;
    color: #1c66e5;
    font-weight: 500;
  }
}
</style>

```

## Props
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| enableSearch | Boolean | true | Control whether to display the Conversation Search feature. |
| enableCreate | Boolean | true | Control whether to display the Create Session feature. |
| enableActions | Boolean | true | Control whether to display the conversation operation feature. |
| actionsConfig | ConversationActionsConfig | - | Used for custom session operation configuration. |
| Header | Component | Header | Custom Header component. |
| List | Component | List | Custom session list component. |
| Preview | Component | ConversationPreview | Session preview component. |
| ConversationCreate | Component | ConversationCreate | Custom conversation component. |
| ConversationSearch | Component | Search | Custom session search component. |
| ConversationActions | Component | ConversationActions | Custom Conversation Operation Component. |
| Avatar | Component | Avatar | Custom avatar component. |
| PlaceholderEmptyList | Component | <PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} /> | Placeholder element when custom session list is empty. |
| PlaceholderLoading | Component | <PlaceHolder type={PlaceHolderTypes.LOADING} /> | Placeholder element during custom session list loading. |
| PlaceholderLoadError | Component | <PlaceHolder type={PlaceHolderTypes.WRONG} /> | Placeholder element for custom session list load error. |
| filter | (conversationList: ConversationModel[]) => ConversationModel[] | - | Functions for filtering session list. |
| sort | (conversationList: ConversationModel[]) => ConversationModel[] | - | Functions for sorting session list. |
| onConversationSelect | (conversation: ConversationModel) => void; | - | Callback function when clicking a session, with the clicked conversation object as parameter. |
| onBeforeCreateConversation | (params: CreateParams) => CreateParams; | - | Custom action executed before session creation, with parameters required for creating a session. |
| onConversationCreate | (conversation: ConversationModel)  => void; | - | Callback function after session creation, with the created session object as parameter. |
| className | String | - | Specify the CSS custom name of the root element class. |
| style | CSSProperties | - | Specify the custom style of the root element. |
