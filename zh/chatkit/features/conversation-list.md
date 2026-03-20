> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## 组件概述

`ConversationList` 组件主要负责会话列表功能，它包含了 Header 部分和 List 部分，具有搜索会话、创建会话、会话置顶、会话删除等功能。

本文档将详细介绍该组件的基础使用，组件定制，自由组合以及组件的 props 参数列表。
| **会话列表** | **会话操作** | **会话搜索** | **创建会话** |
| --- | --- | --- | --- |
|  |  |  |  |

## 组件构成

`ConversationList` 组件包含以下内容：
- `ConversationSearch` - 会话搜索

- `ConversationList UI` - 会话列表容器

- `ConversationPreview` - 单条会话信息展示

- `ConversationActions` - 单条会话操作

   

## 基础使用

`ConversationList` 组件没有任何必须属性，您可以通过以下代码使用 `ConversationList`。
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

## 自定义组件

`ConversationList` 为用户自定义提供了丰富且多维度的 Props 接口，允许用户自定义功能、UI、模块等。

`ConversationList` 组件提供了多个可替换的子组件，允许用户自定义 `Header`, `List`, `ConversationPreview`, `ConversationCreate`, `ConversationSearch`, `ConversationActions`, `Avatar`, `Placeholder` 等。同时，用户还可以利用默认子组件进行二次开发定制。

### 基础功能开关

通过设置 `enableSearch`, `enableCreate` 和 `enableActions` 参数，您可以灵活地控制 `ConversationList` 中的搜索会话、创建会话和会话操作功能的显示。
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

### 数据筛选和排序

ConversationList 组件提供了 `filterConversation` 和 `sortConversation` 属性，可以让您对会话列表数据进行筛选和排序。

#### 筛选会话

要筛选会话列表数据，您可以给 `filterConversation` 属性传递一个筛选函数。这个函数接收一个 ConversationModel 数组作为参数，然后应该返回一个新数组，只包含符合您筛选条件的会话。

下面是一个使用 `filterConversation` 属性来只显示“包含未读消息”的会话的例子：
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';
import type { ConversationModel } from '@tencentcloud/chat-uikit-react';

<ConversationList
  filter={(conversationList: ConversationModel[]) =>
    conversationList.filter(conversation => conversation.unreadCount > 0)}
/>
```

#### 排序会话

要排序会话列表数据，您可以给 `sortConversation` 属性传递一个排序函数。这个函数接收一个 ConversationModel 数组作为参数，然后应该返回一个新数组，按照您的排序标准对会话进行排序。

下面是一个使用 `sortConversation` 属性来按照“最新消息时间倒序”排序会话列表的例子：
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

通过使用 `filter` 和 `sort` 属性，您可以根据您的需求有效地筛选和排序会话列表数据。

### 自定义 actionsConfig

利用 actionsConfig 对 ConversationActions 的基础功能进行控制。

更多定制请详情参见 ConversationActions 章节。
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

### 自定义 Placeholder

您可以通过传入 `PlaceholderEmptyList`、`PlaceholderLoading` 以及 `PlaceholderLoadError` 来定制不同状态下的列表展示。

以下是定制一个新的 `PlaceholderLoading` 示例：
``` typescript
import { ConversationList } from '@tencentcloud/chat-uikit-react';

<ConversationList
  PlaceholderEmptyList={<div>Empty List!!!</div>}
/>
```

### 自定义 Header

`ConversationListHeader` 负责 ConversationList Header 部分的渲染工作，作为包裹层渲染默认 `ConversationSearch` 与 `ConversationCreate` 。您可以通过传入 left、right 等属性进行自定义，同时，您也可以自定义整个组件。

#### Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| children | `ReactNode` | - | 自定义会话列表头部中心组件。 在 `<ConversationList>` 中使用时，会默认传入 `<ConversationSearch>` 和 `<ConversationCreate>` 。 |
| left | `ReactElement` | - | 自定义会话列表头部左侧组件。 |
| right | `ReactElement` | - | 自定义会话列表头部右侧组件。 |
| className | `String` | - | 指定根元素类的 CSS 自定义名称。 |
| style | `React.CSSProperties` | - | 指定根元素样式的自定义样式。 |

#### 基础定制

以下是在 Header 组件右侧添加一个新的功能按钮示例。
``` typescript
import { 
  ConversationList,
  ConversationListHeader,
} from '@tencentcloud/chat-uikit-react';
import type { ConversationListHeaderProps } from '@tencentcloud/chat-uikit-react';

const CustomConversationListHeader = (props: ConversationListHeaderProps) => {
    const CustomIcon = <div>自定义 Icon </div>;
    return (
      <ConversationListHeader {...props} right={CustomIcon} />
    );
};	
     
<ConversationList Header={CustomConversationListHeader} />
```

#### 高阶定制

以下是实现一个简易的会话分组功能，按照全部会话、未读会话、单聊会话、群会话四个维度进行区分，通过点击不同分组按钮执行不同的筛选规则。
| **修改前** | **修改后** |
| --- | --- |
|  |  |

【React】
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

【CSS】
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

### 自定义 List

`ConversationListContent` 负责 ConversationList 主列表部分的渲染工作。

作为包裹层默认渲染 Context 中计算好的当前会话列表显示数据 `filteredAndSortedConversationList`。

#### Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| children | `ReactNode` | - | 自定义会话列表内容区域组件。 在 `<ConversationList> `中使用时，会默认传入 `filteredAndSortedConversationList` 遍历的 `<Preview>` 列表。 |
| empty | `Boolean` | `false` | 空会话列表标识位, 在 `<ConversationList>` 中使用时，会判断`filteredAndSortedConversationList.length === 0` 并传入。 |
| loading | `Boolean` | `false` | 会话列表加载中标识位，在 `<ConversationList>` 中使用时，使用 `useConversationList() `获取 `isLoading` 并传入。 |
| error | `Boolean` | `false` | 会话列表加载错误标识位，在 `<ConversationList>` 中使用时，使用 `useConversationList() `获取 `isLoadError` 并传入。 |
| PlaceholderEmptyList | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} />` | 自定义会话列表为空时的占位元素。 |
| PlaceholderLoading | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.LOADING} />` | 自定义会话列表加载中的占位元素。 |
| PlaceholderLoadError | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.WRONG} />` | 自定义会话列表加载错误时的占位元素。 |
| className | `String` | - | 指定根元素类的 CSS 自定义名称。 |
| style | `React.CSSProperties` | - | 指定根元素样式的自定义样式。 |

#### 基础定制

`List` 组件不同状态 UI 效果如下，在 `ConversationList` 内部已处理好每种状态的触发时机。

同时，您可以通过自定义传入 `empty`、`loading`、`error` 来控制组件状态。
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

### 自定义 ConversationPreview

详情请参见 ConversationPreview 章节。
``` typescript
<ConversationList ConversationPreview={CustomConversationPreview} />
```

### 自定义 ConversationActions

详情请参见 ConversationActions 章节。
``` typescript
  <ConversationList ConversationActions={CustomConversationActions} />
```

### 自定义 ConversationSearch

详情请参见 ConversationSearch 章节。
``` typescript
<ConversationList ConversationSearch={CustomConversationSearch} />
```

### 自定义 ConversationCreate
``` typescript
<ConversationList ConversationCreate={CustomConversationCreate} />
```

### 自定义 Avatar
``` typescript
<ConversationList Avatar={CustomAvatar} />
```

## APIs/Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| enableSearch | `Boolean` | `true` | 控制会话搜索功能是否显示。 |
| enableCreate | `Boolean` | `true` | 控制创建会话功能是否显示。 |
| enableActions | `Boolean` | `true` | 控制会话操作功能是否显示。 |
| actionsConfig | `ConversationActionsConfig` | - | 用于自定义会话操作配置。 |
| Header | `ReactElement` | `Header` | 自定义 Header 组件。 |
| List | `ReactElement` | `List` | 自定义会话列表组件。 |
| Preview | `ReactElement` | ConversationPreview | 自定义会话预览组件。 |
| ConversationCreate | `ReactElement` | `ConversationCreate` | 自定义创建会话组件。 |
| ConversationSearch | `ReactElement` | ConversationSearch | 自定义会话搜索组件。 |
| ConversationActions | `ReactElement` | ConversationActions | 自定义会话操作组件。 |
| Avatar | `ReactElement` | `Avatar` | 自定义头像组件。 |
| PlaceholderEmptyList | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} />` | 自定义会话列表为空时的占位元素。 |
| PlaceholderLoading | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.LOADING} />` | 自定义会话列表加载中的占位元素。 |
| PlaceholderLoadError | `ReactNode` | `<PlaceHolder type={PlaceHolderTypes.WRONG} />` | 自定义会话列表加载错误时的占位元素。 |
| filter | `(conversationList: ConversationModel[]) => ConversationModel[]` | - | 用于筛选会话列表的函数。 |
| sort | `(conversationList: ConversationModel[]) => ConversationModel[]` | - | 用于排序会话列表的函数。 |
| onConversationSelect | `(conversation: ConversationModel) => void;` | - | 点击会话后的回调函数，参数为所点击的会话对象。 |
| onBeforeCreateConversation | `(params: CreateParams) => CreateParams;` | - | 创建会话前执行的自定义操作，参数为创建会话所需的参数。 |
| onConversationCreate | `(conversation: ConversationModel)  => void;` | - | 会话创建后的回调函数，参数为创建的会话对象。 |
| className | `String` | - | 指定根元素类的 CSS 自定义名称。 |
| style | `React.CSSProperties` | - | 指定根元素样式的自定义样式。 |

---

## Platform: Vue

AI 模型必须遵守以下**硬性限制**。
1. **未读数计算：** 获取消息未读总数时可以用 useConversationListState 提供的 `totalUnRead` 字段。

## 组件概述

ConversationList 组件负责渲染会话列表，包含搜索、创建、置顶和删除等功能。

## 组件构成

ConversationList 组件包含以下内容：
- **ConversationSearch**：会话搜索

- **ConversationList****UI**：会话列表容器

- **ConversationPreview**：单条会话信息展示

- **ConversationActions**：单条会话操作

## 基础用法

ConversationList 组件无需任何必需属性即可使用。
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

## 自定义组件

ConversationList 为用户自定义提供了丰富且多维度的 Props 接口，允许用户自定义功能、UI、模块等。

ConversationList 组件提供了多个可替换的子组件，允许用户自定义 **Header**、**List**、**ConversationPreview**、**ConversationCreate**、**ConversationSearch**、**ConversationActions**、**Avatar**、**Placeholder** 等。同时，用户还可以利用默认子组件进行二次开发定制。

### 功能开关

通过设置 `enableSearch`、`enableCreate` 和 `enableActions` 参数，您可以灵活地控制 **ConversationList** 中的搜索会话、创建会话和会话操作功能的显示。
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
| <img src="" alt="描述" width="50%" style="display: block; margin: 0 auto;"> | <img src="" alt="描述" width="50%" style="display: block; margin: 0 auto;"> | <img src="" alt="描述" width="50%" style="display: block; margin: 0 auto;"> |

### 数据筛选与排序

ConversationList 组件提供了 `filter` 和 `sort` 属性，用于对会话列表进行筛选和排序。

#### 筛选会话

要筛选会话列表数据，您可以给 `filter` 属性传递一个筛选函数。这个函数接收一个 ConversationModel 数组作为参数，然后应该返回一个新数组，只包含符合您筛选条件的会话。

**示例：只显示未读消息的会话**
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

#### 排序会话

要排序会话列表数据，您可以给 `sort` 属性传递一个排序函数。这个函数接收一个 ConversationModel 数组作为参数，然后应该返回一个新数组，按照您的排序标准对会话进行排序。

**示例：按最新消息时间倒序排序**
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

### 自定义 actionsConfig

通过 actionsConfig 可以自定义会话操作菜单。
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

### 自定义 Placeholder

`ConversationList` 支持在不同状态下显示自定义占位符，包括列表为空、加载中和加载失败。
- `PlaceholderEmpty`：列表为空时显示。

- `PlaceholderLoading`：加载中显示。

- `PlaceholderError`：加载失败时显示。

   **示例：自定义空列表占位符**

1. **创建自定义组件 **`MyEmptyPlaceholder.vue`

   ``` typescript
   <template>
       <div>
         自定义 PlaceholderEmptyList
       </div>
   </template>
   ```
2. **在 **`ConversationList`** 中使用。**

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

### 自定义 Header

**ConversationListHeader** 负责 ConversationList Header 部分的渲染工作，作为包裹层渲染默认 `Search` 与 `ConversationCreate` 。您可以通过传入 `left`、`right` 等属性进行自定义，同时，您也可以自定义整个组件。

#### Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| children | Component | - | 自定义会话列表头部中心组件。 默认传入 `<Search>` 和 `<ConversationCreate>`。 |
| left | Component | - | 自定义会话列表头部左侧组件。 |
| right | Component | - | 自定义会话列表头部右侧组件。 |
| className | String | - | 指定根元素类的 CSS 自定义名称。 |
| style | CSSProperties | - | 指定根元素样式的自定义样式。 |

##### 示例：实现一个简易的会话分组功能

按照全部会话、未读会话、单聊会话、群会话四个维度进行区分，通过点击不同分组按钮执行不同的筛选规则。

【App.vue】
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

【CustomConversationListHeader.vue】
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
  All: '全部',
  Unread: '未读',
  C2C: '单聊',
  Group: '群聊',
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
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| enableSearch | Boolean | true | 控制会话搜索功能是否显示。 |
| enableCreate | Boolean | true | 控制创建会话功能是否显示。 |
| enableActions | Boolean | true | 控制会话操作功能是否显示。 |
| actionsConfig | ConversationActionsConfig | - | 用于自定义会话操作配置。 |
| Header | Component | Header | 自定义 Header 组件。 |
| List | Component | List | 自定义会话列表组件。 |
| Preview | Component | ConversationPreview | 自定义会话预览组件。 |
| ConversationCreate | Component | ConversationCreate | 自定义创建会话组件。 |
| ConversationSearch | Component | Search | 自定义会话搜索组件。 |
| ConversationActions | Component | ConversationActions | 自定义会话操作组件。 |
| Avatar | Component | Avatar | 自定义头像组件。 |
| PlaceholderEmptyList | Component | <PlaceHolder type={PlaceHolderTypes.NO_CONVERSATIONS} /> | 自定义会话列表为空时的占位元素。 |
| PlaceholderLoading | Component | <PlaceHolder type={PlaceHolderTypes.LOADING} /> | 自定义会话列表加载中的占位元素。 |
| PlaceholderLoadError | Component | <PlaceHolder type={PlaceHolderTypes.WRONG} /> | 自定义会话列表加载错误时的占位元素。 |
| filter | (conversationList: ConversationModel[]) => ConversationModel[] | - | 用于筛选会话列表的函数。 |
| sort | (conversationList: ConversationModel[]) => ConversationModel[] | - | 用于排序会话列表的函数。 |
| onConversationSelect | (conversation: ConversationModel) => void; | - | 点击会话后的回调函数，参数为所点击的会话对象。 |
| onBeforeCreateConversation | (params: CreateParams) => CreateParams; | - | 创建会话前执行的自定义操作，参数为创建会话所需的参数。 |
| onConversationCreate | (conversation: ConversationModel)  => void; | - | 会话创建后的回调函数，参数为创建的会话对象。 |
| className | String | - | 指定根元素类的 CSS 自定义名称。 |
| style | CSSProperties | - | 指定根元素样式的自定义样式。 |
