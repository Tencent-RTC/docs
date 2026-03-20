> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationSearch` 是使用的 Search 组件，支持用户、群组、消息的搜索功能。它集成了搜索框、高级搜索、搜索结果展示等功能。

## 基础使用
``` typescript
import { 
   UIKitProvider, 
   ConversationList,
   ConversationSearch,
} from '@tencentcloud/chat-uikit-react';

import type {
   ConversationSearchProps,
   SearchResultItem,
} from '@tencentcloud/chat-uikit-react';

const CustomConversationSearch = (props: ConversationSearchProps) => {
  const onSelectResult = (data: SearchResultItem, type: ISearchType) => {
    console.warn(`Select Search Result: ${JSON.stringify(data)}, type: ${type}`);
  };
  return (
    <ConversationSearch {...props} onResultItemClick={onSelectResult} />
  );
};

const App = () => {
  return (
    <UIKitProvider>
      <ConversationList
        style={{ maxWidth: '300px', height: '600px' }}
        ConversationSearch={CustomConversationSearch}
       />
     </UIKitProvider>
  );
};
```

## Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| visible | `boolean` | `true` | 是否可见 |
| Search | `ComponentType<SearchProps>` | - | 自定义搜索组件 |
| variant | `VariantType` | `VariantType.MINI` | 搜索模式：`mini`(迷你)、`standard`(标准)、`embedded`(嵌入式) |
| SearchBar | `React.ComponentType<SearchBarProps>` | `DefaultSearchBar` | 自定义搜索框组件 |
| SearchResults | `React.ComponentType<SearchResultsProps>` | `DefaultSearchResults` | 自定义搜索结果组件 |
| SearchAdvanced | `React.ComponentType<SearchAdvancedProps>` | `DefaultSearchAdvanced` | 自定义高级搜索组件 |
| SearchResultsPresearch | `React.ComponentType` | - | 搜索前占位符组件 |
| SearchResultsLoading | `React.ComponentType` | - | 加载中占位符组件 |
| SearchResultsEmpty | `React.ComponentType` | - | 空结果占位符组件 |
| SearchResultItem | `React.ComponentType<ResultItemProps>` | - | 自定义搜索结果项组件 |
| debounceTime | `number` | `300` | 搜索防抖时间(毫秒) |
| autoFocus | `boolean` | `false` | 是否自动聚焦搜索框 |
| className | `string` | - | 自定义样式类名 |
| style | `React.CSSProperties` | - | 自定义样式 |
| onKeywordChange | `(keyword: string) => void` | - | 搜索关键词变化回调 |
| onSearchComplete | `(results: Map<SearchType, SearchResult>) => void` | - | 搜索完成回调 |
| onResultItemClick | `(data: SearchResultItem, type: SearchType) => void` | - | 搜索结果点击回调 |
| onError | `(error: Error) => void` | - | 搜索错误回调 |

---

## Platform: Vue

ConversationSearch 是使用的 **Search** 组件，支持用户、群组、消息的搜索功能。它集成了搜索框、高级搜索、搜索结果展示等功能。

## 基础使用
``` typescript
<template>
  <ConversationSearch {...props} @ResultItemClick={onSelectResult} />
</template>
<script setup lang="ts">
import { ConversationSearch, SearchType } from '@tencentcloud/chat-uikit-vue3';
import type { SearchResultItem } from '@tencentcloud/chat-uikit-vue3';
const props = defineProps<ConversationSearchProps>();

const onSelectResult = (data: SearchResultItem, type: SearchType) => {
  console.warn(`Select Search Result: ${JSON.stringify(data)}, type: ${type}`);
};
</script>
```
``` typescript
<template>
  <UIKitProvider>
    <ConversationList ConversationSearch={CustomConversationSearch} />
  </UIKitProvider>
</template>
<script setup lang="ts">
import { UIKitProvider, ConversationList } from '@tencentcloud/chat-uikit-vue3';
import CustomConversationSearch from "./CustomConversationSearch.vue";
</script>
```

## Props
| **参数名** | **类型** | **默认值** | **说明** |
| --- | --- | --- | --- |
| visible | boolean | true | 是否可见 |
| Search | ComponentType<SearchProps> | - | 自定义搜索组件 |
| variant | VariantType | VariantType.MINI | 搜索模式： - mini：迷你 - standard：标准 - embedded：嵌入式 |
| SearchBar | Component<SearchBarProps> | DefaultSearchBar | 自定义搜索框组件 |
| SearchResults | Component<SearchResultsProps> | DefaultSearchResults | 自定义搜索结果组件 |
| SearchAdvanced | Component<SearchAdvancedProps> | DefaultSearchAdvanced | 自定义高级搜索组件 |
| SearchResultsPresearch | Component | - | 搜索前占位符组件 |
| SearchResultsLoading | Component | - | 加载中占位符组件 |
| SearchResultsEmpty | Component | - | 空结果占位符组件 |
| SearchResultItem | Component<ResultItemProps> | - | 自定义搜索结果项组件 |
| debounceTime | number | 300 | 搜索防抖时间(毫秒) |
| autoFocus | boolean | false | 是否自动聚焦搜索框 |
| className | string | - | 自定义样式类名 |
| style | CSSProperties | - | 自定义样式 |
| onKeywordChange | (keyword: string) => void | - | 搜索关键词变化回调 |
| onSearchComplete | (results: Map<SearchType, SearchResult>) => void | - | 搜索完成回调 |
| onResultItemClick | (data: SearchResultItem, type: SearchType) => void | - | 搜索结果点击回调 |
| onError | (error: Error) => void | - | 搜索错误回调 |
