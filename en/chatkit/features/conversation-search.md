> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

`ConversationSearch` uses the Search component, supporting search functionality for users, groups, and messages. It integrates features such as the search bar, advanced search, and search result display.

## Basic Usage
``` typescript
import { 
   UIKitProvider, 
   ConversationList,
   ConversationSearch,
} from '@tencentcloud/chat-uikit-react';

import type {
   ConversationSearchProps,
   SearchResultItem,
   SearchType,
} from '@tencentcloud/chat-uikit-react';

const CustomConversationSearch = (props: ConversationSearchProps) => {
  const onSelectResult = (data: SearchResultItem, type: SearchType) => {
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
| **Parameter Name** | **Type** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| visible | boolean | true | Whether it is visible. |
| Search | ComponentType<SearchProps> | - | custom search component |
| variant | VariantType | VariantType.MINI | Search mode: mini, standard, embedded |
| SearchBar | React.ComponentType<SearchBarProps> | DefaultSearchBar | custom search bar component |
| SearchResults | React.ComponentType<SearchResultsProps> | DefaultSearchResults | custom search result component |
| SearchAdvanced | React.ComponentType<SearchAdvancedProps> | DefaultSearchAdvanced | custom advanced search component |
| SearchResultsPresearch | React.ComponentType | - | search placeholder component |
| SearchResultsLoading | React.ComponentType | - | loading placeholder component |
| SearchResultsEmpty | React.ComponentType | - | empty result placeholder component |
| SearchResultItem | React.ComponentType<ResultItemProps> | - | custom search result item component |
| debounceTime | number | 300 | search debounce time (ms) |
| autoFocus | boolean | false | Whether to auto-focus the search box |
| className | string | - | custom style class name |
| style | React.CSSProperties | - | custom style |
| onKeywordChange | (keyword: string) => void | - | searchsearch keywords change callback'] |
| onSearchComplete | (results: Map<SearchType, SearchResult>) => void | - | search completed callback |
| onResultItemClick | (data: SearchResultItem, type: SearchType) => void | - | search result click callback |
| onError | (error: Error) => void | - | error callback |

###

---

## Platform: Vue

ConversationSearch uses the **Search** component, supporting search for users, groups, and messages. It integrates search box, advanced search, and search results display features.

## Basic Usage
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
| **Parameter** | **Type** | **Default** | **Description** |
| --- | --- | --- | --- |
| visible | boolean | true | Whether visible |
| Search | ComponentType<SearchProps> | - | Custom search component |
| variant | VariantType | VariantType.MINI | Search mode: - mini: Mini - standard: Standard - embedded: Embedded |
| SearchBar | Component<SearchBarProps> | DefaultSearchBar | Custom search bar component |
| SearchResults | Component<SearchResultsProps> | DefaultSearchResults | Custom search results component |
| SearchAdvanced | Component<SearchAdvancedProps> | DefaultSearchAdvanced | Custom advanced search component |
| SearchResultsPresearch | Component | - | Pre-search placeholder component |
| SearchResultsLoading | Component | - | Loading placeholder component |
| SearchResultsEmpty | Component | - | Empty results placeholder component |
| SearchResultItem | Component<ResultItemProps> | - | Custom search result item component |
| debounceTime | number | 300 | Search debounce time (ms) |
| autoFocus | boolean | false | Whether to auto-focus the search box |
| className | string | - | Custom class name |
| style | CSSProperties | - | Custom style |
| onKeywordChange | (keyword: string) => void | - | Search keyword change callback |
| onSearchComplete | (results: Map<SearchType, SearchResult>) => void | - | Search complete callback |
| onResultItemClick | (data: SearchResultItem, type: SearchType) => void | - | Search result item click callback |
| onError | (error: Error) => void | - | Search error callback |
