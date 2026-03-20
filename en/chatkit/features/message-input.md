> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

# Overview

MessageInput is a fully functional message input component with core chat input features including text editing, emoji selection, file attachment, and a send button. It supports various custom options such as input behavior configuration, toolbar customization, component replacement, and slot expansion, meeting the demands of different chat scenarios with personalized requirements.

## Props Configuration
| Field | Type | Default Value | Description |
| --- | --- | --- | --- |
| autoFocus | boolean | true | Auto-focus to input box |
| disabled | boolean | false | Disable input component |
| hideSendButton | boolean | false | Whether to hide send button |
| placeholder | string | '' | Input box placeholder text |
| className | string | undefined | Root container custom CSS class |
| style | React.CSSProperties | undefined | Root container inline style |
| attachmentPickerMode | 'collapsed' \\| 'expanded' | 'collapsed' | Attachment selector display mode |
| actions | MessageInputActions | ['EmojiPicker', 'AttachmentPicker'] | Toolbar button configuration |
| slots | MessageInputSlots | undefined | Slot configuration object |
| TextEditor | JSX.Element | undefined | Custom text editor component |
| EmojiPicker | JSX.Element | undefined | Custom emoji selector component |
| AttachmentPicker | JSX.Element | undefined | Custom attachment selector component |
| FilePicker | JSX.Element | undefined | Custom file selector component |
| ImagePicker | JSX.Element | undefined | Custom image selector component |
| VideoPicker | JSX.Element | undefined | Custom video selector component |

Custom Function Explanation

### autoFocus

Parameter type: `boolean`

autoFocus is used to set whether to auto-focus into the input box when mounting, default value is `true`.

### disabled

Parameter type: `boolean`

disabled is used to set whether to disable the entire input component, including text input and all operation buttons, default value is `false`.

### hideSendButton

Parameter type: `boolean`

hideSendButton is used to set whether to hide the send button, suitable for scenarios where custom trigger modes are needed, default value is `false`.

### placeholder

Parameter type: `string`

placeholder is used to set the placeholder text for the input box, default value is an empty string.

### className

Parameter type: `string`

className is used to set the root container's custom CSS class name, default value is `undefined`.

### style

Parameter type: `React.CSSProperties`

style is used to set the root container's custom inline style, default value is `undefined`.

### attachmentPickerMode

Parameter type: `'collapsed' | 'expanded'`

attachmentPickerMode is used to set the display mode of the attachment selector, default value is `'collapsed'`.
- `collapsed`: Collapse mode, unfold options after clicking

- `expanded`: Expand mode, display all options directly
   

   **Note:**
   > 

   By default, AttachmentPicker refers to the attachment selector, including file selection, image selection, and video selection.
   > 
When the attachmentPickerModel is "collapsed", clicking the attachment selector pops up a menu displaying file selection, image selection, and video selection.
2. When the attachmentPickerModel is "expanded", the attachment selector will expand by default, showing file selection, image selection, and video selection in tile display.

### actions

Parameter type: `MessageInputActions`

actions for configuration toolbar display buttons, default value `['EmojiPicker', 'AttachmentPicker']`.
``` ts
type BuiltInAction =
  | 'EmojiPicker'
  | 'ImagePicker'
  | 'FilePicker'
  | 'VideoPicker'
  | 'AttachmentPicker';

type CustomAction = {
  key: string;
  label?: string | undefined;
  component?: React.ComponentType<any> | undefined;
  className?: string | undefined;
  style?: React.CSSProperties | undefined;
  iconSize?: number | undefined;
};

type MessageInputActions = Array<BuiltInAction | CustomAction>;
```

#### Example 1: Custom toolbar button sequence
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';

function ChatWithCustomActions() {
  Custom button sequence: file, image, video, emoji
  const customActions = ['FilePicker', 'ImagePicker', 'VideoPicker', 'EmojiPicker'];
  
  return (
    <Chat>
      <MessageInput actions={customActions} />
    </Chat>
  );
}
```

#### Example 2: Add custom action button - Quick reply for customer service system
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';
import { useMessageInputState } from '@/states';

Quick reply component
function QuickReplyPicker() {
  const { setContent } = useMessageInputState();
  
  const quickReplies = [
    Hello, glad to serve you.
    Please wait, I'll check for you...
    Thank you for your consultation. Any other issues?
    The issue has been resolved. Have a pleasant life!
  ];

  const handleQuickReply = (text: string) => {
    setContent(text);
  };

  return (
    <div style={{ position: 'relative' }}>
      <button title="quick reply">⚡</button>
      <div style={{
        position: 'absolute',
        bottom: '100%',
        left: 0,
        background: 'white',
        border: '1px solid #ccc',
        borderRadius: '4px',
        padding: '8px',
        minWidth: '200px'
      }}>
        {quickReplies.map((reply, index) => (
          <div
            key={index}
            onClick={() => handleQuickReply(reply)}
            style={{
              padding: '4px 8px',
              cursor: 'pointer',
              borderRadius: '2px'
            }}
          >
            {reply}
          </div>
        ))}
      </div>
    </div>
  );
}

function CustomerServiceChat() {
  const actions = [
    {
      key: 'quickReply',
      quick reply
      component: QuickReplyPicker
    },
    'EmojiPicker',
    'FilePicker'
  ];
  
  return (
    <Chat>
      <MessageInput actions={actions} />
    </Chat>
  );
}
```

### slots

Parameter type: `MessageInputSlots`

slots for inserting custom content in specific locations of the input component, default value is `undefined`.
``` ts
interface MessageInputSlots {
  headerToolbar?: () => React.ReactNode;
  footerToolbar?: () => React.ReactNode;
  leftInline?: () => React.ReactNode;
  rightInline?: () => React.ReactNode;
  inputPrefix?: () => React.ReactNode;
  inputSuffix?: () => React.ReactNode;
}
```

MessageInput structure diagram

#### Example 1: Add message statistics display
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';
import { useMessageInputState } from '@/states';

function ChatWithMessageStats() {
  const { inputRawValue } = useMessageInputState();
  
  Header toolbar: Display character statistics
  const HeaderToolbar = () => !inputRawValue
    ? null
    : (
      <div style={{
        padding: '4px 12px',
        fontSize: '12px',
        color: '#666',
        borderBottom: '1px solid #eee',
      }}
      >
        Number of characters
        {' '}
        {inputRawValue?.reduce((acc, item) => acc + (item.type === 'text' ? item.content.length : 1), 0) || 0}
        /500
      </div>
    );

  // toolbar: display sending notification
  const FooterToolbar = () => (
    <div style={{
      padding: '4px 12px',
      fontSize: '12px',
      color: '#999',
      textAlign: 'right'
    }}>
      Press Ctrl+Enter to wrap text
    </div>
  );

  return (
    <Chat>
      <MessageInput 
        slots={{
          headerToolbar: HeaderToolbar,
          footerToolbar: FooterToolbar
        }}
      />
    </Chat>
  );
}
```

#### Example 2: Input box prefix/suffix feature
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';

function ChatWithInputPrefixSuffix() {
  @reminder feature in input box
  const InputPrefix = () => (
    <button 
      style={{ 
        border: 'none', 
        background: 'transparent',
        color: '#1890ff',
        cursor: 'pointer'
      }}
      onClick={() => {
        // Trigger @user selection
        console.log('turn on @user selection');
      }}
    >
      @
    </button>
  );

  voice input in input box
  const InputSuffix = () => (
    <button
      style={{
        border: 'none',
        background: 'transparent',
        cursor: 'pointer'
      }}
      onClick={() => {
        Start voice input
        console.log('start voice input');
      }}
    >
      🎤
    </button>
  );

  return (
    <Chat>
      <MessageInput 
        slots={{
          inputPrefix: InputPrefix,
          inputSuffix: InputSuffix
        }}
      />
    </Chat>
  );
}
```

#### Example 3: Full customization of left and right toolbars
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';

function ChatWithCustomToolbars() {
  // Left toolbar: Only retain emoji and images
  const LeftInline = () => (
    <div style={{ display: 'flex', gap: '8px' }}>
      <button>😊</button>
      <button>📷</button>
    </div>
  );

  // the tool bar on the right: custom send area
  const RightInline = () => (
    <div style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
      <span style={{ fontSize: '12px', color: '#666' }}>
        Enter
      </span>
      <button
        style={{
          padding: '6px 12px',
          background: '#1890ff',
          color: 'white',
          border: 'none',
          borderRadius: '4px',
          cursor: 'pointer'
        }}
      >
        Send
      </button>
    </div>
  );

  return (
    <Chat>
      <MessageInput 
        slots={{
          leftInline: LeftInline,
          rightInline: RightInline
        }}
      />
    </Chat>
  );
}
```

### TextEditor

Parameter type: `JSX.Element`

TextEditor replaces the default text editor component. Default value: `undefined`.

Integrate a rich text editor
``` tsx
import { Chat, MessageInput, useMessageInputState } from '@tencentcloud/chat-uikit-react';

// Custom rich text editor
function RichTextEditor() {
  const { sendMessage } = useMessageInputState();
  const [inputValue, setInputValue] = useState('');

  const handleContentChange = (content: string) => {
    setInputValue(content);
  };

  const handleKeyDown = (e: React.KeyboardEvent) => {
    // Press Enter to send a message
    if (e.key === 'Enter') {
      // Trigger send logic
      e.preventDefault();
      sendMessage(inputValue);
      Clear input
      const editableDiv = document.querySelector('.editable-div');
      if (editableDiv) {
        editableDiv.textContent = '';
      }
    }
  };

  return (
    <div style={{
      flex: 1,
      border: '1px solid #d9d9d9',
      borderRadius: '6px',
      padding: '8px 12px',
      minHeight: '32px',
      maxHeight: '120px',
      overflow: 'auto',
    }}
    >
      <div
        contentEditable
        className="editable-div"
        style={{
          outline: 'none',
          minHeight: '20px',
          lineHeight: '20px',
        }}
        onInput={(e) => {
          handleContentChange(e.currentTarget.textContent || '');
        }}
        onKeyDown={handleKeyDown}
      />
    </div>
  );
}

function ChatWithRichTextEditor() {
  return (
    <Chat>
      <MessageInput TextEditor={<RichTextEditor />} />
    </Chat>
  );
}
```

### EmojiPicker

Parameter type: `JSX.Element`

EmojiPicker replaces the default emoji selector component with a default value of `undefined`.

#### Example: Custom emoji panel
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';
import { useMessageInputState } from '@/states';

Custom emoji selector
function CustomEmojiPicker() {
  const { insertContent } = useMessageInputState();

  const emojiCategories = {
    Common: ['😀', '😂', '🥰', '😍', '🤔', '😭', '😡', '👍']
    Gestures: ['👋', '🤝', '👏', '🙏', '✌️', '🤞', '🤟', '👌']
    Animals: ['🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼']
  };

  const [activeCategory, setActiveCategory] = useState('Common');
  const [showPicker, setShowPicker] = useState(false);

  const insertEmoji = (emoji: string) => {
    insertContent(emoji);
    setShowPicker(false);
  };

  return (
    <div style={{ position: 'relative' }}>
      <button
        onClick={() => setShowPicker(!showPicker)}
        style={{ border: 'none', background: 'transparent', cursor: 'pointer' }}
      >
        😊
      </button>

      {showPicker && (
        <div style={{
          position: 'absolute',
          bottom: '100%',
          left: 0,
          background: 'white',
          border: '1px solid #ccc',
          borderRadius: '8px',
          padding: '12px',
          width: '280px',
          boxShadow: '0 4px 12px rgba(0,0,0,0.1)',
        }}
        >
          category tag
          <div style={{ display: 'flex', marginBottom: '8px' }}>
            {Object.keys(emojiCategories).map(category => (
              <button
                key={category}
                onClick={() => setActiveCategory(category)}
                style={{
                  padding: '4px 8px',
                  border: 'none',
                  background: activeCategory === category ? '#1890ff' : 'transparent',
                  color: activeCategory === category ? 'white' : '#666',
                  borderRadius: '4px',
                  cursor: 'pointer',
                  fontSize: '12px',
                }}
              >
                {category}
              </button>
            ))}
          </div>

          {/* Emoji grid */}
          <div style={{
            display: 'grid',
            gridTemplateColumns: 'repeat(8, 1fr)',
            gap: '4px',
          }}
          >
            {emojiCategories[activeCategory].map(emoji => (
              <button
                key={emoji}
                onClick={() => insertEmoji(emoji)}
                style={{
                  border: 'none',
                  background: 'transparent',
                  fontSize: '20px',
                  cursor: 'pointer',
                  padding: '4px',
                  borderRadius: '4px',
                }}
              >
                {emoji}
              </button>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}

function ChatWithCustomEmoji() {
  return (
    <Chat>
      <MessageInput EmojiPicker={<CustomEmojiPicker />} />
    </Chat>
  );
}
```

### AttachmentPicker

Parameter type: `JSX.Element`

AttachmentPicker is used to replace the default attachment selector component, default value is `undefined`.

Storage integration attachment selector
``` tsx
import { Chat, MessageInput } from '@tencentcloud/chat-uikit-react';

// Cloud storage attachment selector with seamless integration
function CloudAttachmentPicker() {
  const [showPicker, setShowPicker] = useState(false);
  
  const attachmentTypes = [
    { key: 'local', label: 'Local file', icon: '📁' },
    { key: 'cloud', label: 'Cloud file', icon: '☁️' },
    { key: 'recent', label: 'Recent files', icon: '🕒' },
    { key: 'screenshot', label: 'Screenshot', icon: '📷' }
  ];

  const handleAttachmentSelect = async (type: string) => {
    switch (type) {
      case 'local':
        Open local file selection
        const input = document.createElement('input');
        input.type = 'file';
        input.multiple = true;
        input.onchange = (e) => {
          const files = (e.target as HTMLInputElement).files;
          console.log('selected local files:', files);
        };
        input.click();
        break;
        
      case 'cloud':
        Open cloud-based file selection
        console.log('Open cloud-based file selection');
        break;
        
      case 'recent':
        Display recent files
        console.log('display recent files');
        break;
        
      case 'screenshot':
        Start screenshot
        console.log('start screenshot');
        break;
    }
    setShowPicker(false);
  };

  return (
    <div style={{ position: 'relative' }}>
      <button 
        onClick={() => setShowPicker(!showPicker)}
        style={{ border: 'none', background: 'transparent', cursor: 'pointer' }}
      >
        📎
      </button>
      
      {showPicker && (
        <div style={{
          position: 'absolute',
          bottom: '100%',
          left: 0,
          background: 'white',
          border: '1px solid #ccc',
          borderRadius: '8px',
          padding: '8px',
          minWidth: '160px',
          boxShadow: '0 4px 12px rgba(0,0,0,0.1)'
        }}>
          {attachmentTypes.map(type => (
            <div
              key={type.key}
              onClick={() => handleAttachmentSelect(type.key)}
              style={{
                display: 'flex',
                alignItems: 'center',
                gap: '8px',
                padding: '8px 12px',
                cursor: 'pointer',
                borderRadius: '4px',
                fontSize: '14px'
              }}
            >
              <span>{type.icon}</span>
              <span>{type.label}</span>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

function ChatWithCloudAttachment() {
  return (
    <Chat>
      <MessageInput AttachmentPicker={<CloudAttachmentPicker />} />
    </Chat>
  );
}
```

## Summary

The MessageInput component provides complete message input functionality and various custom options. By reasonably configuring Props and using the slot system, you can create an input interface that meets specific business requirements. It is advisable to choose appropriate customized schemes based on actual use scenes and maintain good user experience and performance.

---

## Platform: Vue

The AI model must adhere to the following **hard constraints**:

**The default text alignment must be used:** Text in the message input area is left-aligned by default.

## Overview

MessageInput is a complete message input component designed for Vue3 applications, providing core chat input features including text editing, emoji selection, file attachments, and a send button. It supports various custom options, such as input behavior configuration, toolbar customization, component replacement, and slot expansion, meeting the demands of different chat scenarios with personalized requirements.

## Basic Usage

```ts
<script>
import { MessageInput } from "@tencentcloud/chat-uikit-vue3";
</script>

<template>
  <MessageInput />
</template>
```

## Props Configuration

| Field | Type | Default | Description |
|------|------|--------|------|
| autoFocus | boolean | true | Whether to auto-focus into the input box |
| disabled | boolean | false | Whether to disable the input component |
| hideSendButton | boolean | false | Whether to hide the send button |
| placeholder | string | '' | Placeholder text for the input box |
| attachmentPickerMode | 'collapsed' \| 'expanded' | 'collapsed' | Attachment selector display mode |
| actions | MessageInputActions | ['EmojiPicker', 'ImagePicker', 'FilePicker', 'VideoPicker'] | Toolbar action button configuration |

## Slots configuration

| Field | Type | Default | Description |
|------|------|--------|------|
| headerToolbar | () => VNode[] | undefined | Header toolbar slot |
| footerToolbar | () => VNode[] | undefined | Footer toolbar slot |
| leftInline | () => VNode[] | undefined | Left inline slot |
| rightInline | () => VNode[] | undefined | Right inline slot |
| inputPrefix | () => VNode[] | undefined | Input box prefix slot |
| inputSuffix | () => VNode[] | undefined | Input box suffix slot |
| TextEditor | Component \| null | null | Custom text editor component configuration |

### Type definition
``` typescript
type BuiltInAction =
  | 'EmojiPicker'
  | 'ImagePicker'
  | 'FilePicker'
  | 'VideoPicker'
  | 'AttachmentPicker';

export type CustomAction = {
  key: string;
  label?: string | undefined;
  component?: Component | undefined;
  className?: string | undefined;
  style?: CSSProperties | undefined;
  iconSize?: number | undefined;
};

export type MessageInputActions = Array<BuiltInAction | CustomAction>;

export type MessageInputAttachmentPickerMode = 'collapsed' | 'expanded';

export interface MessageInputSlots {
  headerToolbar?: () => VNode[];
  footerToolbar?: () => VNode[];
  leftInline?: () => VNode[];
  rightInline?: () => VNode[];
  inputPrefix?: () => VNode[];
  inputSuffix?: () => VNode[];
  textEditor?: () => VNode[];
}

// MessageInputProps API definition
export interface MessageInputProps {
  autoFocus?: boolean;
  disabled?: boolean;
  hideSendButton?: boolean;
  placeholder?: string;
  attachmentPickerMode?: MessageInputAttachmentPickerMode;
  actions?: MessageInputActions;
  slots?: MessageInputSlots;
  TextEditor?: Component | null;
}
```

## Custom feature explanation

### autoFocus
- `type`: `boolean`

- Details: autoFocus is used to set whether the component auto-focuses into the input box when mounting. The default is `true`.

### disabled
- `type`: `boolean`

- Details: disabled is used to set whether to disable the entire input component, including text input and all buttons. The default is `false`.

### hideSendButton
- `type`: `boolean`

- Details: hideSendButton is used to set whether to hide the send button, suitable for scenarios where the trigger mode needs to be customized. The default is `false`.

### placeholder
- `type`: `string`.

- Details: placeholder is used to set the placeholder text for the input box. The default is an empty string.

### attachmentPickerMode
- `type`: `'collapsed'` | `'expanded'`.

- Details: attachmentPickerMode is used to set the display mode of the attachment selector. Default is `'collapsed'`.

  - **collapsed**: Collapse mode. Unfold options after clicking.

  - **expanded**: Expand mode. Direct display of ALL options.

### actions
- Type: MessageInputActions.

- Description: actions for configuration of buttons displayed in the toolbar. Default: `['EmojiPicker', 'ImagePicker', 'FilePicker', 'VideoPicker']`.

#### Example 1: Custom toolbar button sequence
``` typescript
<template>
  <MessageInput :actions="customActions" />
</template>

<script lang="ts" setup>
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

// Custom button sequence: file, image, video, emoji
const customActions = ['FilePicker', 'ImagePicker', 'VideoPicker', 'EmojiPicker'];
</script>
```

#### Example 2: Add custom action button
``` typescript
<template>
  <MessageInput :actions="customActions" />
</template>

<script lang="ts" setup>
import { defineComponent, h } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

Custom Position Sharing Button Component
const LocationPicker = defineComponent({
  name: 'LocationPicker',
  setup() {
    const handleLocationShare = () => {
      Get user location and send
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          const { latitude, longitude } = position.coords;
          console.log(`Share location: ${latitude}, ${longitude}`);
          alert(`Share location: ${latitude}, ${longitude}`); // For demonstration
        });
      } else {
        alert('Browser not supported for geolocation.');
      }
    };

    return () =>
      h('button',
        {
          onClick: handleLocationShare,
          style: { padding: '4px 8px', border: 'none', background: 'transparent' },
          Share location
        },
        '📍',
      );
  },
});

const customActions = [
  'EmojiPicker',
  'ImagePicker',
  {
    key: 'location',
    label: 'Location',
    component: LocationPicker,
  },
  'AttachmentPicker',
];
</script>
```

#### Example 3: Quick reply in customer service system
``` typescript
<template>
  <MessageInput :actions="actions" />
</template>

<script lang="ts" setup>
import { defineComponent, h, ref } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

quick reply component
const QuickReplyPicker = defineComponent({
  name: 'QuickReplyPicker',
  setup() {
    const { setContent } = useMessageInputState();
    const showPicker = ref(false);

    const quickReplies = [
      Hello, glad to help you.
      Please wait a moment, I'll check for you...
      Thank you for your consultation. Also, any other issues?
      The issue has been resolved. Have a pleasant life.
    ];

    const handleQuickReply = (text: string) => {
      setContent(text);
      showPicker.value = false;
    };

    return () =>
      h('div', { style: { position: 'relative' } }, [
        h('button',
          {
            title: 'Quick reply'
            onClick: () => (showPicker.value = !showPicker.value),
          },
          '⚡',
        ),
        showPicker.value &&
          h('div',
            {
              style: {
                position: 'absolute',
                bottom: '100%',
                left: 0,
                background: 'white',
                border: '1px solid #ccc',
                borderRadius: '4px',
                padding: '8px',
                minWidth: '200px',
                zIndex: 100, // Ensure it's above other elements
              },
            },
            quickReplies.map((reply, index) =>
              h('div',
                {
                  key: index,
                  onClick: () => handleQuickReply(reply),
                  style: {
                    padding: '4px 8px',
                    cursor: 'pointer',
                    borderRadius: '2px',
                    '&:hover': { backgroundColor: '#f0f0f0' },
                  },
                },
                reply,
              ),
            ),
          ),
      ]);
  },
});

const actions = [
  {
    key: 'quickReply',
    label: 'Quick reply'
    component: QuickReplyPicker,
  },
  'EmojiPicker',
  'FilePicker',
];
</script>
```

#### Example 4: Custom emoji panel
``` typescript
<template>
  <MessageInput :EmojiPicker="CustomEmojiPicker" />
</template>

<script lang="ts" setup>
import { defineComponent, h, ref } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

const CustomEmojiPicker = defineComponent({
  name: 'CustomEmojiPicker',
  setup() {
    const { insertContent } = useMessageInputState();
    const showPicker = ref(false);
    const activeCategory = ref('common');

    const emojiCategories = {
      'common': ['😀', '😂', '🥰', '😍', '🤔', '😭', '😡', '👍'],
      'hand': ['👋', '🤝', '👏', '🙏', '✌️', '🤞', '🤟', '👌'],
      'animal': ['🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼'],
    };

    const insertEmoji = (emoji: string) => {
      insertContent(emoji);
      showPicker.value = false;
    };

    return () =>
      h('div', { style: { position: 'relative' } }, [
        h('button',
          {
            onClick: () => (showPicker.value = !showPicker.value),
            style: { border: 'none', background: 'transparent', cursor: 'pointer' },
          },
          '😊',
        ),
        showPicker.value &&
          h('div',
            {
              style: {
                position: 'absolute',
                bottom: '100%',
                left: 0,
                background: 'white',
                border: '1px solid #ccc',
                borderRadius: '8px',
                padding: '12px',
                width: '280px',
                boxShadow: '0 4px 12px rgba(0,0,0,0.1)',
                zIndex: 100, // Ensure it's above other elements
              },
            },
            [
              category tag
              h('div', { style: { display: 'flex', marginBottom: '8px' } },
                Object.keys(emojiCategories).map((category) =>
                  h('button',
                    {
                      key: category,
                      onClick: () => (activeCategory.value = category),
                      style: {
                        padding: '4px 8px',
                        border: 'none',
                        background: activeCategory.value === category ? '#1890ff' : 'transparent',
                        color: activeCategory.value === category ? 'white' : '#666',
                        borderRadius: '4px',
                        cursor: 'pointer',
                        fontSize: '12px',
                      },
                    },
                    category,
                  ),
                ),
              ),
              emoji grid
              h('div',
                {
                  style: {
                    display: 'grid',
                    gridTemplateColumns: 'repeat(8, 1fr)',
                    gap: '4px',
                  },
                },
                emojiCategories[activeCategory.value].map((emoji) =>
                  h('button',
                    {
                      key: emoji,
                      onClick: () => insertEmoji(emoji),
                      style: {
                        border: 'none',
                        background: 'transparent',
                        fontSize: '20px',
                        cursor: 'pointer',
                        padding: '4px',
                        borderRadius: '4px',
                      },
                    },
                    emoji,
                  ),
                ),
              ),
            ],
          ),
      ]);
  },
});
</script>
```

## Slots explanation
- Type: MessageInputSlots.

-Detailed instructions: Insert custom content at a specific location in the input component.

   ``` typescript
   interface MessageInputSlots {
     headerToolbar?: () => VNode[];
     footerToolbar?: () => VNode[];
     leftInline?: () => VNode[];
     rightInline?: () => VNode[];
     inputPrefix?: () => VNode[];
     inputSuffix?: () => VNode[];
     textEditor?: () => VNode[];
   }
   ```

#### Example 1: Custom footerToolbar slot
``` typescript
<template>
  <MessageInput>
    <template #footerToolbar>
      <div style="padding: 4px 12px; font-size: 12px; color: #999; text-align: right;">
        press Ctrl+Enter to wrap
      </div>
    </template>
  </MessageInput>
</template>
```

#### Example 2: Input box prefix/suffix feature
``` typescript
<template>
  <MessageInput>
    <template #inputPrefix>
      
    </template>
    <template #inputSuffix>
      <button
        style="border: none; background: transparent; cursor: pointer;"
        @click="handleVoiceInput"
      >
        🎤
      </button>
    </template>
  </MessageInput>
</template>

<script lang="ts" setup>
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

const handleAtUser = () => {
  Trigger @user selection
  console.log('Toggle on @user selection');
};

const handleVoiceInput = () => {
  // Start voice input
  console.log('Start voice input');
};
</script>
```

### TextEditor

Type: 'Component' | 'null'.

Detailed explanation: TextEditor is used to replace the default text editor component. Default: `null`.

#### Example: Custom text editor
``` typescript
<template>
  <MessageInput>
    <template #textEditor>
      <RichTextEditor />
    </template>
  </MessageInput>
</template>

<script lang="ts" setup>
import { defineComponent, h, ref, onMounted } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

const RichTextEditor = defineComponent({
  name: 'RichTextEditor',
  setup() {
    const { sendMessage } = useMessageInputState();
    const editorRef = ref<HTMLDivElement | null>(null);
    const inputRawValue = ref('');

    const handleInput = (e: Event) => {
      const target = e.target as HTMLDivElement;
      inputRawValue.value = (e.target as HTMLDivElement).textContent || '';
    };

    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Enter') {
        sendMessage(inputRawValue.value);
        if (editorRef.value) {
          editorRef.value.textContent = '';
        }
      }
    };

    onMounted(() => {
      if (editorRef.value) {
        editorRef.value.textContent = inputRawValue.value || '';
      }
    });

    return () =>
      h('div',
        {
          style: {
            flex: 1,
            border: '1px solid #d9d9d9',
            borderRadius: '6px',
            padding: '8px 12px',
            minHeight: '32px',
            maxHeight: '120px',
            overflow: 'auto',
          },
        },
        h('div',
          {
            ref: editorRef,
            contentEditable: true,
            style: {
              outline: 'none',
              minHeight: '20px',
              lineHeight: '20px',
            },
            onInput: handleInput,
            onKeydown: handleKeyDown,
          },
        ),
      );
  },
});
</script>
```
Overview

MessageInput is a complete message input component designed for Vue3 applications, providing core chat input features including text editing, emoji selection, file attachments, and a send button. It supports various custom options, such as input behavior configuration, toolbar customization, component replacement, and slot expansion, meeting the demands of different chat scenarios with personalized requirements.

Basic Usage

```ts
<script>
import { MessageInput } from "@tencentcloud/chat-uikit-vue3";
</script>

<template>
  <MessageInput />
</template>
```

## Props Configuration

| Field | Type | Default | Description |
|------|------|--------|------|
| autoFocus | boolean | true | Whether to auto-focus into the input box |
| disabled | boolean | false | Whether to disable the input component |
| hideSendButton | boolean | false | Whether to hide the send button |
| placeholder | string | '' | Placeholder text for the input box |
| attachmentPickerMode | 'collapsed' \| 'expanded' | 'collapsed' | Attachment selector display mode |
| actions | MessageInputActions | ['EmojiPicker', 'ImagePicker', 'FilePicker', 'VideoPicker'] | Toolbar action button configuration |

## Slots configuration

| Field | Type | Default | Description |
|------|------|--------|------|
| headerToolbar | () => VNode[] | undefined | Header toolbar slot |
| footerToolbar | () => VNode[] | undefined | Footer toolbar slot |
| leftInline | () => VNode[] | undefined | Left inline slot |
| rightInline | () => VNode[] | undefined | Right inline slot |
| inputPrefix | () => VNode[] | undefined | Input box prefix slot |
| inputSuffix | () => VNode[] | undefined | Input box suffix slot |
| TextEditor | Component \| null | null | Custom text editor component configuration |

type definition
``` typescript
type BuiltInAction =
  | 'EmojiPicker'
  | 'ImagePicker'
  | 'FilePicker'
  | 'VideoPicker'
  | 'AttachmentPicker';

export type CustomAction = {
  key: string;
  label?: string | undefined;
  component?: Component | undefined;
  className?: string | undefined;
  style?: CSSProperties | undefined;
  iconSize?: number | undefined;
};

export type MessageInputActions = Array<BuiltInAction | CustomAction>;

export type MessageInputAttachmentPickerMode = 'collapsed' | 'expanded';

export interface MessageInputSlots {
  headerToolbar?: () => VNode[];
  footerToolbar?: () => VNode[];
  leftInline?: () => VNode[];
  rightInline?: () => VNode[];
  inputPrefix?: () => VNode[];
  inputSuffix?: () => VNode[];
  textEditor?: () => VNode[];
}

// MessageInputProps API definition
export interface MessageInputProps {
  autoFocus?: boolean;
  disabled?: boolean;
  hideSendButton?: boolean;
  placeholder?: string;
  attachmentPickerMode?: MessageInputAttachmentPickerMode;
  actions?: MessageInputActions;
  slots?: MessageInputSlots;
  TextEditor?: Component | null;
}
```

Custom Function Explanation

### autoFocus
-boolean

- Details: autoFocus is used to set whether to auto-focus into the input box when mounting. Default: `true`.

### disabled
-boolean

- Details: disabled is used to set whether to disable the entire input component, including text input and ALL buttons. Default: `false`.

### hideSendButton
-boolean

- Details: hideSendButton is used to set whether to hide the send button, suitable for scenarios where the trigger mode needs to be customized. Default: `false`.

### placeholder
- Type: string.

- Details: placeholder is used to set the placeholder text for the input box. Default is an empty string.

### attachmentPickerMode
- Type: 'collapsed' | 'expanded'.

- Details: attachmentPickerMode is used to set the display mode of the attachment selector. Default is `'collapsed'`.

  - **collapsed**: Collapse mode. Unfold options after clicking.

  - **expanded**: Expand mode. Direct display of ALL options.

### actions
- Type: MessageInputActions.

- Description: actions for configuration of buttons displayed in the toolbar. Default: `['EmojiPicker', 'ImagePicker', 'FilePicker', 'VideoPicker']`.

#### Example 1: Custom toolbar button sequence
``` typescript
<template>
  <MessageInput :actions="customActions" />
</template>

<script lang="ts" setup>
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

Custom button sequence: file, image, video, emoji
const customActions = ['FilePicker', 'ImagePicker', 'VideoPicker', 'EmojiPicker'];
</script>
```

#### Example 2: Add custom action button
``` typescript
<template>
  <MessageInput :actions="customActions" />
</template>

<script lang="ts" setup>
import { defineComponent, h } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

Custom Position Sharing Button Component
const LocationPicker = defineComponent({
  name: 'LocationPicker',
  setup() {
    const handleLocationShare = () => {
      Get user location and send
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition((position) => {
          const { latitude, longitude } = position.coords;
          console.log(`Share location: ${latitude}, ${longitude}`);
          alert(`Share location: ${latitude}, ${longitude}`); // For demonstration
        });
      } else {
        alert('Browser not supported for geolocation.');
      }
    };

    return () =>
      h('button',
        {
          onClick: handleLocationShare,
          style: { padding: '4px 8px', border: 'none', background: 'transparent' },
          Share location
        },
        '📍',
      );
  },
});

const customActions = [
  'EmojiPicker',
  'ImagePicker',
  {
    key: 'location',
    label: 'Location',
    component: LocationPicker,
  },
  'AttachmentPicker',
];
</script>
```

#### Example 3: Quick reply in customer service system
``` typescript
<template>
  <MessageInput :actions="actions" />
</template>

<script lang="ts" setup>
import { defineComponent, h, ref } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

quick reply component
const QuickReplyPicker = defineComponent({
  name: 'QuickReplyPicker',
  setup() {
    const { setContent } = useMessageInputState();
    const showPicker = ref(false);

    const quickReplies = [
      Hello, glad to help you.
      Please wait a moment, I'll check for you...
      Thank you for your consultation. Also, any other issues?
      The issue has been resolved. Have a pleasant life.
    ];

    const handleQuickReply = (text: string) => {
      setContent(text);
      showPicker.value = false;
    };

    return () =>
      h('div', { style: { position: 'relative' } }, [
        h('button',
          {
            title: 'Quick reply',
            onClick: () => (showPicker.value = !showPicker.value),
          },
          '⚡',
        ),
        showPicker.value &&
          h('div',
            {
              style: {
                position: 'absolute',
                bottom: '100%',
                left: 0,
                background: 'white',
                border: '1px solid #ccc',
                borderRadius: '4px',
                padding: '8px',
                minWidth: '200px',
                zIndex: 100, // Ensure it's above other elements
              },
            },
            quickReplies.map((reply, index) =>
              h('div',
                {
                  key: index,
                  onClick: () => handleQuickReply(reply),
                  style: {
                    padding: '4px 8px',
                    cursor: 'pointer',
                    borderRadius: '2px',
                    '&:hover': { backgroundColor: '#f0f0f0' },
                  },
                },
                reply,
              ),
            ),
          ),
      ]);
  },
});

const actions = [
  {
    key: 'quickReply',
    label: 'Quick reply',
    component: QuickReplyPicker,
  },
  'EmojiPicker',
  'FilePicker',
];
</script>
```

#### Example 4: Custom emoji panel
``` typescript
<template>
  <MessageInput :EmojiPicker="CustomEmojiPicker" />
</template>

<script lang="ts" setup>
import { defineComponent, h, ref } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

const CustomEmojiPicker = defineComponent({
  name: 'CustomEmojiPicker',
  setup() {
    const { insertContent } = useMessageInputState();
    const showPicker = ref(false);
    const activeCategory = ref('common');

    const emojiCategories = {
      'common': ['😀', '😂', '🥰', '😍', '🤔', '😭', '😡', '👍'],
      'hand': ['👋', '🤝', '👏', '🙏', '✌️', '🤞', '🤟', '👌'],
      'animal': ['🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼'],
    };

    const insertEmoji = (emoji: string) => {
      insertContent(emoji);
      showPicker.value = false;
    };

    return () =>
      h('div', { style: { position: 'relative' } }, [
        h('button',
          {
            onClick: () => (showPicker.value = !showPicker.value),
            style: { border: 'none', background: 'transparent', cursor: 'pointer' },
          },
          '😊',
        ),
        showPicker.value &&
          h('div',
            {
              style: {
                position: 'absolute',
                bottom: '100%',
                left: 0,
                background: 'white',
                border: '1px solid #ccc',
                borderRadius: '8px',
                padding: '12px',
                width: '280px',
                boxShadow: '0 4px 12px rgba(0,0,0,0.1)',
                zIndex: 100, // Ensure it's above other elements
              },
            },
            [
              category tag
              h('div', { style: { display: 'flex', marginBottom: '8px' } },
                Object.keys(emojiCategories).map((category) =>
                  h('button',
                    {
                      key: category,
                      onClick: () => (activeCategory.value = category),
                      style: {
                        padding: '4px 8px',
                        border: 'none',
                        background: activeCategory.value === category ? '#1890ff' : 'transparent',
                        color: activeCategory.value === category ? 'white' : '#666',
                        borderRadius: '4px',
                        cursor: 'pointer',
                        fontSize: '12px',
                      },
                    },
                    category,
                  ),
                ),
              ),
              emoji grid
              h('div',
                {
                  style: {
                    display: 'grid',
                    gridTemplateColumns: 'repeat(8, 1fr)',
                    gap: '4px',
                  },
                },
                emojiCategories[activeCategory.value].map((emoji) =>
                  h('button',
                    {
                      key: emoji,
                      onClick: () => insertEmoji(emoji),
                      style: {
                        border: 'none',
                        background: 'transparent',
                        fontSize: '20px',
                        cursor: 'pointer',
                        padding: '4px',
                        borderRadius: '4px',
                      },
                    },
                    emoji,
                  ),
                ),
              ),
            ],
          ),
      ]);
  },
});
</script>
```

## Slots explanation
- Type: MessageInputSlots.

-Detailed instructions: Insert custom content at a specific location in the input component.

   ``` typescript
   interface MessageInputSlots {
     headerToolbar?: () => VNode[];
     footerToolbar?: () => VNode[];
     leftInline?: () => VNode[];
     rightInline?: () => VNode[];
     inputPrefix?: () => VNode[];
     inputSuffix?: () => VNode[];
     textEditor?: () => VNode[];
   }
   ```

#### Example 1: Custom footerToolbar slot
``` typescript
<template>
  <MessageInput>
    <template #footerToolbar>
      <div style="padding: 4px 12px; font-size: 12px; color: #999; text-align: right;">
        press Ctrl+Enter to wrap
      </div>
    </template>
  </MessageInput>
</template>
```

#### Example 2: Input box prefix/suffix feature
``` typescript
<template>
  <MessageInput>
    <template #inputPrefix>
      
    </template>
    <template #inputSuffix>
      <button
        style="border: none; background: transparent; cursor: pointer;"
        @click="handleVoiceInput"
      >
        🎤
      </button>
    </template>
  </MessageInput>
</template>

<script lang="ts" setup>
import { MessageInput } from '@tencentcloud/uikit-component-vue3';

const handleAtUser = () => {
  Trigger @user selection
  console.log('Toggle on @user selection');
};

const handleVoiceInput = () => {
  // Start voice input
  console.log('Start voice input');
};
</script>
```

### TextEditor

Type: 'Component' | 'null'.

Detailed explanation: TextEditor is used to replace the default text editor component. Default: `null`.

#### Example: Custom text editor
``` typescript
<template>
  <MessageInput>
    <template #textEditor>
      <RichTextEditor />
    </template>
  </MessageInput>
</template>

<script lang="ts" setup>
import { defineComponent, h, ref, onMounted } from 'vue';
import { MessageInput } from '@tencentcloud/uikit-component-vue3';
import { useMessageInputState } from '@tencentcloud/uikit-component-vue3/src/states/MessageInputState';

const RichTextEditor = defineComponent({
  name: 'RichTextEditor',
  setup() {
    const { sendMessage } = useMessageInputState();
    const editorRef = ref<HTMLDivElement | null>(null);
    const inputRawValue = ref('');

    const handleInput = (e: Event) => {
      const target = e.target as HTMLDivElement;
      inputRawValue.value = (e.target as HTMLDivElement).textContent || '';
    };

    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Enter') {
        sendMessage(inputRawValue.value);
        if (editorRef.value) {
          editorRef.value.textContent = '';
        }
      }
    };

    onMounted(() => {
      if (editorRef.value) {
        editorRef.value.textContent = inputRawValue.value || '';
      }
    });

    return () =>
      h('div',
        {
          style: {
            flex: 1,
            border: '1px solid #d9d9d9',
            borderRadius: '6px',
            padding: '8px 12px',
            minHeight: '32px',
            maxHeight: '120px',
            overflow: 'auto',
          },
        },
        h('div',
          {
            ref: editorRef,
            contentEditable: true,
            style: {
              outline: 'none',
              minHeight: '20px',
              lineHeight: '20px',
            },
            onInput: handleInput,
            onKeydown: handleKeyDown,
          },
        ),
      );
  },
});
</script>
```
