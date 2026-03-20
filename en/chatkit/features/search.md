> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

## Component Overview

Search is a complete search solution with a full set of components, including search bar, result display, advanced search, and other features. It is suitable for user, group, and message search in instant messaging, online meeting, and online education scenarios.

> **Note:**
> 

> Note: This feature belongs to VAS. You need to purchase cloud search plugin. Please click [purchase](https://console.trtc.io/chat/plugin/TUICloudSearch).
> 

## Component Architecture
``` bash
Search (main component)
├── SearchBar              # Search bar component
├── SearchResults          # Search result component
├── SearchAdvanced         # Advanced search component
└── SearchResultsItem      # Search result item component
    ├── User               # User result item
    ├── Group              # Group result item
    ├── Message            # Message result item
    └── Conversation       # Session result item
```

## Search Mode
| **Mini Mode** | **Standard Mode** | **Embedded Mode** |
| --- | --- | --- |
| - Suitable for sidebar or small container - Display finite number of results - Support expand to view more | - Suitable for full-screen search UI - Feature demonstration - Advanced Search Support | - Suitable for chat UI - Focus on message search - Optimized layout |
|  |  |  |

## Props
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| variant | VariantType | VariantType.MINI | Search mode: mini, standard, embedded |
| SearchBar | React.ComponentType<SearchBarProps> | DefaultSearchBar | Custom search box component |
| SearchResults | React.ComponentType<SearchResultsProps> | DefaultSearchResults | Custom search result component |
| SearchAdvanced | React.ComponentType<SearchAdvancedProps> | DefaultSearchAdvanced | Custom advanced search component |
| SearchResultsPresearch | React.ComponentType | - | Search placeholder component |
| SearchResultsLoading | React.ComponentType | - | Loading placeholder component |
| SearchResultsEmpty | React.ComponentType | - | Empty result placeholder component |
| SearchResultItem | React.ComponentType<ResultItemProps> | - | Custom search result item component |
| debounceTime | number | 300 | Search debounce time (ms) |
| autoFocus | boolean | false | Auto-focus search box |
| className | string | - | Custom style class name |
| style | React.CSSProperties | - | custom style |
| onKeywordChange | (keyword: string) => void | - | search keyword change callback |
| onSearchComplete | (results: Map<SearchType, SearchResult>) => void | - | search complete callback |
| onResultItemClick | (data: SearchResultItem, type: SearchType) => void | - | search result click callback |
| onError | (error: Error) => void | - | search error callback |

## Basic Usage
``` typescript
import { Search, VariantType } from '@tencentcloud/chat-uikit-react';

function App() {
  return (
    <Search
      variant={VariantType.STANDARD}
      onResultItemClick={(data, type) => {
        console.log('Search result click:', data, type);
      }}
    />
  );
}
```

## Custom Capabilities

`Search` provides users with various and dimensional Props APIs, allowing them to customize features, UI, and modules.

`Search` component provides multiple replaceable subcomponents, allowing users to customize `SearchBar`, `SearchResults`, `SearchAdvanced`, `SearchResultItem`, `SearchResultsPresearch`, `SearchResultsLoading`, `SearchResultsEmpty`. Users can also leverage default subcomponents for secondary development customization.

【Custom SearchBar】

**Props**
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| data | SearchResultItem | - | search result data |
| type | SearchType | - | search result type |
| keyword | string | - | search keyword |
| onClick | (data: SearchResultItem, type: SearchType) => void | - | click callback |
| className | string | - | Custom style class name |

**Example**
``` typescript
const CustomSearchBar = ({ value, onChange, onClear, placeholder }) => (
  <div className="custom-search-bar">
    <input
      type="text"
      value={value}
      onChange={onChange}
      placeholder={placeholder}
    />
    {value && <button onClick={onClear}>Clear</button>}
  </div>
);

<Search SearchBar={CustomSearchBar} />
```

【Custom Search Results】

**Props**
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| results | `Map<SearchType, SearchResult>` | - | search result data |
| isLoading | `boolean` | - | whether loading |
| error | `Error \\| null` | - | Error Message |
| keyword | `string` | - | search keyword |
| typeLabels | `Record<SearchType, string>` | - | search type tag |
| onLoadMore | `(type: SearchType) => void` | - | Load more callback |
| onResultItemClick | `(data: SearchResultItem, type: SearchType) => void` | - | result item click callback |
| SearchResultsLoading | `React.ComponentType` | `Loading` | Custom load component |
| SearchResultsPresearch | `React.ComponentType` | - | Search placeholder component |
| SearchResultsEmpty | `React.ComponentType` | `EmptyResult` | Empty result placeholder component |
| SearchResultItem | `React.ComponentType<ResultItemProps>` | `DefaultSearchResultsItem` | Custom result item component |
| variant | `VariantType` | `VariantType.STANDARD` | Display Mode |
| searchType | `SearchType \\| 'all'` | `'all'` | Current search type |

**Example**
``` typescript
const CustomSearchResults = ({ results, keyword, onResultItemClick }) => (
  <div className="custom-results">
    {Array.from(results.entries()).map(([type, result]) => (
      <div key={type}>
        <h3>{type}</h3>
        {result.resultList.map((item, index) => (
          <div key={index} onClick={() => onResultItemClick(item, type)}>
            {/* Custom result item */}
          </div>
        ))}
      </div>
    ))}
  </div>
);

<Search SearchResults={CustomSearchResults} />
```

【Custom Search Advanced】

**Props**
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| variant | `VariantType` | - | Display Mode |
| searchType | `SearchTabType` | - | Current search type |
| advancedParams | `Map<SearchType, SearchParamsMap[SearchType]>` | - | Advanced search parameters |
| onAdvancedParamsChange | `(type: SearchType, params: SearchParamsMap[SearchType]) => void` | - | Parameter change callback |

**Example**
``` typescript
const CustomSearchAdvanced = ({ variant, searchType, advancedParams, onAdvancedParamsChange }) => (
  <div className="custom-advanced">
  {/* Custom advanced search function */}
  </div>
);

<Search SearchAdvanced={CustomSearchAdvanced} />
```

【Custom Search Result Item】

**Props**
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| data | `SearchResultItem` | - | search result data |
| type | `SearchType` | - | search result type |
| keyword | `string` | - | search keyword |
| onClick | `(data: SearchResultItem, type: SearchType) => void` | - | click callback |
| className | `string` | - | Custom style class name |

**Example**
``` typescript
const CustomSearchItem = ({ data, type, keyword, onClick }) => (
  <div className="custom-item">
    {type === SearchType.MESSAGE && (
        <Conversation
          data={data}
          keyword={keyword}
          onClick={onClick}
        />
      )}
      {type === SearchType.USER && (
        <User
          data={data}
          keyword={keyword}
          onClick={onClick}
        />
      )}
      {type === SearchType.GROUP && (
        <Group
          data={data}
          keyword={keyword}
          onClick={onClick}
        />
      )}
      {type === SearchType.CHAT_MESSAGE && (
        <Message
          data={data}
          keyword={keyword}
          onClick={onClick}
        />
      )}
  </div>
);

<Search SearchResultItem={CustomSearchItem} />
```

【Custom Placeholder Component】

**Props**
| Attribute Name | Type | Default Value | Description |
| --- | --- | --- | --- |
| SearchResultsPresearch | `React.ComponentType` | - | Search placeholder component |
| SearchResultsLoading | `React.ComponentType` | - | Loading placeholder component |
| SearchResultsEmpty | `React.ComponentType` | - | Empty result placeholder component |

**Example**
``` typescript
<Search
  SearchResultsPresearch={() => <div>Enter keywords to start search</div>}
  SearchResultsLoading={() => <div>Searching...</div>}
  SearchResultsEmpty={() => <div>No result found</div>}
/>
```

## FAQs

### How to Customize the Search Result Display Format

A: Use the `SearchResultItem` property to import a customize component.

### How to Handle Search Performance Issues

A: Adjust `debounceTime`, use React.memo, and consider result cache.

### How to Optimize Mobile Terminal

A: The component automatically adapts to mobile terminals and can be further customized via CSS.

### How to Support Multilingual

A: The component has built-in internationalization support. Use `useUIKit` to get translations.

---

## Platform: Vue

## Overview

Search is a complete search solution that includes a comprehensive component set for search box, search results display, advanced search, and more. It's suitable for user, group, and message search scenarios in instant messaging, online meetings, online education, and more.

> **Note:**
> 

> This feature is a value-added service that requires you to purchase the Cloud Search Plugin. Please click [Purchase](https://console.trtc.io/chat/plugin/TUICloudSearch).
> 

## Search Modes
| **Mini Mode (MINI)** | **Standard Mode (STANDARD)** | **Embedded Mode (EMBEDDED)** |
| --- | --- | --- |
| Suitable for sidebar or small containers Displays limited number of results Supports expanding to view more | Suitable for full-screen search interface Complete feature display Supports advanced search | Suitable for chat interface Focuses on message search Optimized layout |

## Props
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| variant | VariantType | VariantType.MINI | Search mode: mini (Mini), standard (Standard), embedded (Embedded) |
| SearchBar | Component<SearchBarProps> | DefaultSearchBar | Custom search bar component |
| SearchResults | Component<SearchResultsProps> | DefaultSearchResults | Custom search results component |
| SearchAdvanced | Component<SearchAdvancedProps> | DefaultSearchAdvanced | Custom advanced search component |
| SearchResultsPresearch | Component | - | Pre-search placeholder component |
| SearchResultsLoading | Component | - | Loading placeholder component |
| SearchResultsEmpty | Component | - | Empty results placeholder component |
| SearchResultItem | Component<ResultItemProps> | - | Custom search result item component |
| debounceTime | number | 300 | Search debounce time (milliseconds) |
| autoFocus | boolean | false | Whether to auto-focus search box |
| className | string | - | Custom style class name |
| style | CSSProperties | - | Custom styles |
| onKeywordChange | (keyword: string) => void | - | Search keyword change callback |
| onSearchComplete | (results: Map<SearchType, SearchResult>) => void | - | Search complete callback |
| onResultItemClick | (data: SearchResultItem, type: SearchType) => void | - | Search result click callback |
| onError | (error: Error) => void | - | Search error callback |

## Basic Usage
``` typescript
<template>
  <div class="app">
    <Search
      :variant="VariantType.STANDARD"
      @result-item-click="handleResultClick"
      @keyword-change="handleKeywordChange"
      @error="handleError"
    />
  </div>
</template>

<script setup lang="ts">
import { Search, VariantType } from '@tencentcloud/chat-uikit-vue3';
import type { SearchResultItemType, SearchType } from '@tencentcloud/chat-uikit-vue3';

const handleResultClick = (data: SearchResultItemType, type: SearchType) => {
  console.log('Search result clicked:', data, type);
};

const handleKeywordChange = (keyword: string) => {
  console.log('Search keyword changed:', keyword);
};

const handleError = (error: Error) => {
  console.error('Search error:', error);
};
</script>
```

## Customization

The `Search` component provides multiple replaceable child components, allowing users to customize `SearchBar`, `SearchResults`, `SearchAdvanced`, `SearchResultItem`, `SearchResultsPresearch`, `SearchResultsLoading`, `SearchResultsEmpty`, etc. Users can also utilize default child components for further customization.

【Custom SearchBar】

**Props**
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| value | string | - | Search box value |
| onChange | (e: Event) => void | - | Input change callback |
| onKeyDown | (e: KeyboardEvent) => void | - | Keyboard event callback |
| onClear | () => void | - | Clear callback |
| placeholder | string | - | Placeholder text |
| SearchIcon | Component | - | Custom search icon |
| ClearIcon | Component | - | Custom clear icon |
| className | string | - | Custom style class name |
| style | CSSProperties | - | Custom styles |
| onFocus | (e: FocusEvent) => void | - | Focus callback |
| onBlur | (e: FocusEvent) => void | - | Blur callback |
| autoFocus | boolean | false | Whether to auto-focus |
| variant | VariantType | - | Search mode |

**Example**
``` typescript
<template>
  <div class="custom-search-bar">
    <div class="search-bar-header">
      <span class="search-icon">🔍</span>
      <span class="search-title">Smart Search</span>
    </div>
    <div class="search-input-wrapper">
      <input
        type="text"
        :value="value"
        :placeholder="placeholder || 'Enter keywords to start searching...'"
        @input="handleChange"
        class="enhanced-search-input"
      />
      <button
        v-if="value"
        @click="handleClear"
        class="enhanced-clear-btn"
      >
        ✕
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
interface SearchBarProps {
  value?: string;
  onChange?: (event: Event) => void;
  onClear?: () => void;
  placeholder?: string;
  variant?: string;
}

const props = defineProps<SearchBarProps>();

const emit = defineEmits<{
  change: [event: Event];
  clear: [];
}>();

const handleChange = (event: Event) => {
  if (props.onChange) {
    props.onChange(event);
  }
  emit('change', event);
};

const handleClear = () => {
  if (props.onClear) {
    props.onClear();
  }
  emit('clear');
};
</script>

<style scoped>
.custom-search-bar {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.search-bar-header {
  display: flex;
  align-items: center;
  gap: 8px;
}

.search-icon {
  font-size: 16px;
}

.search-title {
  font-weight: 500;
  color: #333;
}

.search-input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.enhanced-search-input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  outline: none;
}

.enhanced-search-input:focus {
  border-color: #007bff;
}

.enhanced-clear-btn {
  position: absolute;
  right: 8px;
  background: none;
  border: none;
  cursor: pointer;
  color: #999;
  font-size: 16px;
}

.enhanced-clear-btn:hover {
  color: #666;
}
</style>

```
``` typescript
<template>
  <Search :SearchBar="CustomSearchBar" />
</template>

<script setup lang="ts">
import { Search } from '@tencentcloud/chat-uikit-vue3';
import CustomSearchBar from './CustomSearchBar.vue';
</script>

```

【Custom SearchResults】

**Props**
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| results | Map<SearchType, SearchResult> | - | Search result data |
| isLoading | boolean | - | Whether loading |
| error | Error \\| null | - | Error information |
| keyword | string | - | Search keyword |
| typeLabels | Record<SearchType, string> | - | Search type labels |
| onLoadMore | (type: SearchType) => void | - | Load more callback |
| onResultItemClick | (data: SearchResultItemType, type: SearchType) => void | - | Result item click callback |
| SearchResultsLoading | Component | Loading | Custom loading component |
| SearchResultsPresearch | Component | - | Pre-search placeholder component |
| SearchResultsEmpty | Component | EmptyResult | Empty results placeholder component |
| SearchResultItem | Component<ResultItemProps> | DefaultSearchResultsItem | Custom result item component |
| variant | VariantType | VariantType.STANDARD | Display mode |
| searchType | SearchType \\| 'all' | 'all' | Current search type |

**Example**
``` typescript
<template>
  <div class="custom-search-results">
    <div class="search-results-header">
      <span class="results-icon">📋</span>
      <span class="results-title">Search Results</span>
    </div>
    <div class="search-results-content">
      <div v-if="!results || results.size === 0" class="no-results">
        No search results
      </div>
      <div v-else class="results-list">
        <div
          v-for="(item, key) in results.keys()"
          :key="key"
          class="result-item"
        >
          <div class="result-group">
            <div class="result-title">
              {{ item }}
            </div>
            <ul v-if="item === SearchType.MESSAGE" class="result-description">
              <li
                v-for="(data, index) in results.get(item)?.resultList"
                :key="index"
                @click="handleResultClick(data)"
              >
                {{ (data as SearchCloudMessagesResultItem).conversation?.getShowName() }}
              </li>
            </ul>
            <ul v-if="item === SearchType.USER" class="result-description">
              <li
                v-for="(data, index) in results.get(item)?.resultList"
                :key="index"
                @click="handleResultClick(data)"
              >
                {{ (data as SearchCloudUsersResultItem).profile.nick
                 || (data as SearchCloudUsersResultItem).profile.userID }}
              </li>
            </ul>
            <ul v-if="item === SearchType.GROUP" class="result-description">
              <li
                v-for="(data, index) in results.get(item)?.resultList"
                :key="index"
                @click="handleResultClick(data)"
              >
                {{ (data as SearchCloudGroupsResultItem).groupInfo.name 
                || (data as SearchCloudGroupsResultItem).groupInfo.groupID }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { SearchType } from '@tencentcloud/chat-uikit-vue3';
import type {
  SearchCloudGroupsResultItem,
  SearchCloudMessagesResultItem,
  SearchCloudUsersResultItem,
  SearchResult,
  SearchResultItemType,
} from '@tencentcloud/chat-uikit-vue3';

interface SearchResultsProps {
  results?: Map<SearchType, SearchResult<any>>;
  loading?: boolean;
  keyword?: string;
  searchType?: SearchType;
}

const props = defineProps<SearchResultsProps>();

const emit = defineEmits<{
  'result-item-click': [data: SearchResultItemType, type: any];
}>();

const handleResultClick = (item: SearchResultItemType) => {
  emit('result-item-click', item, props.searchType || 'ALL');
};
</script>

<style scoped>
.custom-search-results {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.search-results-header {
  display: flex;
  align-items: center;
  gap: 8px;
}

.results-icon {
  font-size: 16px;
}

.results-title {
  font-weight: 500;
  color: #333;
}

.search-results-content {
  max-height: 400px;
  overflow-y: auto;
}

.no-results {
  padding: 20px;
  text-align: center;
  color: #999;
  font-size: 14px;
}

.results-list {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.result-item {
  padding: 12px;
  border: 1px solid #eee;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.result-item:hover {
  background-color: #f5f5f5;
}

.result-content {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.result-title {
  font-weight: 500;
  color: #333;
  font-size: 14px;
}

.result-description {
  color: #666;
  font-size: 12px;
  line-height: 1.4;
}
</style>

```
``` typescript
<template>
  <Search :SearchResults="CustomSearchResults" />
</template>

<script setup lang="ts">
import { Search } from '@tencentcloud/chat-uikit-vue3';
import CustomSearchResults from './CustomSearchResults.vue';
</script>
```

【Custom SearchAdvanced】

**Props**
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| variant | VariantType | - | Display mode |
| searchType | SearchTabType | - | Current search type |
| advancedParams | Map<SearchType, SearchParamsMap[SearchType]> | - | Advanced search parameters |
| onAdvancedParamsChange | (type: SearchType, params: SearchParamsMap[SearchType]) => void | - | Parameter change callback |

**Example**
``` typescript
<template>
  <div class="custom-search-advanced">
    <div class="search-advanced-header">
      <span class="advanced-icon">⚙️</span>
      <span class="advanced-title">Advanced Search</span>
    </div>
    <div class="search-advanced-content">
      <div class="search-type-section">
        <div class="section-title">
          Search Type
        </div>
        <div class="search-type-options">
          <label
            v-for="type in searchTypes"
            :key="type.value"
            class="type-option"
          >
            <input
              type="radio"
              :value="type.value"
              :checked="searchType === type.value"
              @change="handleSearchTypeChange(type.value as SearchType)"
            >
            <span class="type-label">{{ type.label }}</span>
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { SearchType, useSearchState } from '@tencentcloud/chat-uikit-vue3';
import type { VariantType } from '@tencentcloud/chat-uikit-vue3';

interface CustomSearchAdvancedProps {
  variant?: VariantType;
  searchType?: SearchType;
}

const props = defineProps<CustomSearchAdvancedProps>();
const { setSelectedType } = useSearchState();

const emit = defineEmits<{
  'search-type-change': [type: SearchType | 'all'];
}>();

const searchTypes = [
  { value: 'all', label: 'All' },
  { value: SearchType.MESSAGE, label: 'Messages' },
  { value: SearchType.USER, label: 'Users' },
  { value: SearchType.GROUP, label: 'Groups' },
];

const searchType = ref<SearchType | 'all'>(props.searchType || 'all');

const handleSearchTypeChange = (type: SearchType) => {
  searchType.value = type;
  setSelectedType(type);
  emit('search-type-change', type);
};
</script>

<style scoped>
.custom-search-advanced {
  display: flex;
  flex-direction: column;
  gap: 16px;
  padding: 16px;
  border: 1px solid #eee;
  border-radius: 8px;
  background-color: #fff;
}

.search-advanced-header {
  display: flex;
  align-items: center;
  gap: 8px;
  padding-bottom: 12px;
  border-bottom: 1px solid #f0f0f0;
}

.advanced-icon {
  font-size: 16px;
}

.advanced-title {
  font-weight: 500;
  color: #333;
  font-size: 16px;
}

.search-advanced-content {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.section-title {
  font-weight: 500;
  color: #333;
  font-size: 14px;
  margin-bottom: 8px;
}

.search-type-options {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.type-option {
  display: flex;
  align-items: center;
  gap: 6px;
  cursor: pointer;
}

.type-option input[type="radio"] {
  margin: 0;
}

.type-label {
  font-size: 14px;
  color: #666;
}
</style>

```
``` typescript
<template>
  <Search :SearchAdvanced="CustomSearchAdvanced" />
</template>

<script setup lang="ts">
import { Search } from '@tencentcloud/chat-uikit-vue3';
import CustomSearchAdvanced from './CustomSearchAdvanced.vue';
</script>
```

【Custom SearchResultItem】

**Props**
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| data | SearchResultItem | - | Search result data |
| type | SearchType | - | Search result type |
| keyword | string | - | Search keyword |
| onClick | (data: SearchResultItem, type: SearchType) => void | - | Click callback |
| className | string | - | Custom style class name |

**Example**
``` java
<template>
  <div class="custom-search-item">
    <div
      v-if="type === SearchType.USER"
      class="user-item"
      @click="handleClick"
    >
      <div class="user-avatar">
        <img :src="data.profile.avatar" :alt="data.profile.nick" />
      </div>
      <div class="user-info">
        <div class="user-name">{{ data.profile.nick }}</div>
        <div class="user-id">{{ data.profile.userID }}</div>
      </div>
    </div>

    <div
      v-else-if="type === SearchType.GROUP"
      class="group-item"
      @click="handleClick"
    >
      <div class="group-avatar">👥</div>
      <div class="group-info">
        <div class="group-name">{{ data.groupInfo.name }}</div>
        <div class="group-id">{{ data.groupInfo.groupID }}</div>
      </div>
    </div>

    <div
      v-else-if="type === SearchType.MESSAGE"
      class="message-item"
      @click="handleClick"
    >
      <div class="message-content">{{ data.conversation.getShowName() }}</div>
      <div class="message-num">{{ data.messageCount }}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { SearchType } from '@tencentcloud/chat-uikit-vue3';
import type { SearchResultItemType } from '@tencentcloud/chat-uikit-vue3';

interface CustomSearchItemProps {
  data: SearchResultItemType;
  type?: SearchType;
  onClick?: (data: SearchResultItemType, type: SearchType) => void;
}

const props = defineProps<CustomSearchItemProps>();

console.warn('CustomSearchItem', props);

const handleClick = () => {
  if (props.onClick && props.type) {
    props.onClick(props.data, props.type);
  }
};

const formatTime = (timestamp: number) => {
  return new Date(timestamp).toLocaleString();
};
</script>

<style scoped>
.custom-search-item {
  cursor: pointer;
  padding: 12px;
  border-bottom: 1px solid #f0f0f0;
  transition: background-color 0.2s;
}

.custom-search-item:hover {
  background-color: #f5f5f5;
}

.user-item,
.group-item,
.message-item {
  display: flex;
  align-items: center;
  gap: 12px;
}

.user-avatar img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

.group-avatar {
  width: 40px;
  height: 40px;
  border-radius: 8px;
  background-color: #e9ecef;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
}

.user-info,
.group-info {
  flex: 1;
}

.user-name,
.group-name {
  font-weight: 500;
  color: #333;
  font-size: 14px;
  margin-bottom: 4px;
}

.user-id,
.group-id {
  color: #666;
  font-size: 12px;
}

.message-content {
  flex: 1;
  color: #333;
  font-size: 14px;
  line-height: 1.4;
}

.message-num {
  color: #666;
  font-size: 12px;
  white-space: nowrap;
}
</style>
```
``` typescript
<template>
  <Search :SearchResultItem="CustomSearchItem" />
</template>

<script setup lang="ts">
import { Search } from '@tencentcloud/chat-uikit-vue3';
import CustomSearchItem from './CustomSearchItem.vue';
</script>
```

【Custom Placeholders】

**Props**
| Property | Type | Default Value | Description |
| --- | --- | --- | --- |
| SearchResultsPresearch | Component | - | Pre-search placeholder component |
| SearchResultsLoading | Component | - | Loading placeholder component |
| SearchResultsEmpty | Component | - | Empty results placeholder component |

**Example**
``` typescript
<template>
  <Search
    :search-results-presearch="PresearchComponent"
    :search-results-loading="LoadingComponent"
    :search-results-empty="EmptyComponent"
  />
</template>

<script setup lang="ts">
import { defineComponent, h } from 'vue';
import { Search } from '@tencentcloud/chat-uikit-vue3';

const PresearchComponent = defineComponent({
  name: 'PresearchComponent',
  setup() {
    return () => h('div', { class: 'presearch-placeholder' }, [
      h('div', { class: 'placeholder-icon' }, '🔍'),
      h('div', { class: 'placeholder-text' }, 'Enter keywords to start searching')
    ]);
  }
});

const LoadingComponent = defineComponent({
  name: 'LoadingComponent',
  setup() {
    return () => h('div', { class: 'loading-placeholder' }, [
      h('div', { class: 'loading-spinner' }),
      h('div', { class: 'loading-text' }, 'Searching...')
    ]);
  }
});

const EmptyComponent = defineComponent({
  name: 'EmptyComponent',
  setup() {
    return () => h('div', { class: 'empty-placeholder' }, [
      h('div', { class: 'empty-icon' }, '😔'),
      h('div', { class: 'empty-text' }, 'No results found'),
      h('div', { class: 'empty-hint' }, 'Try searching with different keywords')
    ]);
  }
});
</script>

<style scoped>
.presearch-placeholder,
.loading-placeholder,
.empty-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 60px 20px;
  color: #999;
}

.placeholder-icon,
.empty-icon {
  font-size: 48px;
  margin-bottom: 16px;
}

.loading-spinner {
  width: 32px;
  height: 32px;
  border: 3px solid #f3f3f3;
  border-top: 3px solid #1890ff;
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 16px;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.placeholder-text,
.loading-text,
.empty-text {
  font-size: 16px;
  margin-bottom: 8px;
}

.empty-hint {
  font-size: 14px;
  color: #bbb;
}
</style>
```
