> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用实时传译插件来启动实时语音转文字（ASR）和翻译能力。该插件可以将房间内用户的语音实时转换为文字，并支持翻译成多种语言。

## 前提条件

- TRTC Web SDK 版本 >= 5.15.0

## 实现流程

### 引入并注册插件

```javascript
import TRTC from 'trtc-sdk-v5';
import RealtimeTranscriber from 'trtc-sdk-v5/plugins/realtime-transcriber';

const trtc = TRTC.create({ plugins: [RealtimeTranscriber] });
```

### 启动插件

启动插件后会返回一个 `transcriberRobotId`，用于后续停止该转录任务。

```javascript
const transcriberRobotId = await trtc.startPlugin('RealtimeTranscriber', {
  sourceLanguage: 'zh',
  translationLanguages: ['en', 'ja'],
  userIdsToTranscribe: 'all',
});
```

### 监听转录消息

通过监听 `REALTIME_TRANSCRIBER_MESSAGE` 事件来获取转录结果。

```javascript
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (event) => {
  console.log(`${event.speakerUserId}: ${event.sourceText}`);
  if (event.translationTexts) {
    Object.entries(event.translationTexts).forEach(([lang, text]) => {
      console.log(`  ${lang}: ${text}`);
    });
  }
});
```

### 监听状态变化

通过监听 `REALTIME_TRANSCRIBER_STATE_CHANGED` 事件来获取转录任务的状态变化。

```javascript
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_STATE_CHANGED, (event) => {
  console.log(`转录任务状态: ${event.state}, robotId: ${event.transcriberRobotId}`);
});
```

### 停止插件

```javascript
await trtc.stopPlugin('RealtimeTranscriber', { transcriberRobotId });
```

## 使用示例

### ASR 场景：转录房间内的音频为文字

```javascript
// 启动转录任务
const robotId = await trtc.startPlugin('RealtimeTranscriber', {
  sourceLanguage: 'zh'
});

// 监听转录消息
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (data) => {
  console.log(data.speakerUserId + ' says: ' + data.sourceText);
});
```

### 翻译场景：将指定用户的语音翻译成多种语言

```javascript
// 启动转录任务，转录 userA 的语音并翻译成法语和英语
const robotId = await trtc.startPlugin('RealtimeTranscriber', {
  sourceLanguage: 'zh',
  translationLanguages: ['fr', 'en'],
  userIdsToTranscribe: 'userA'
});

// 监听转录消息
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (data) => {
  console.log(`👤 ${data.speakerUserId}:`);
  console.log(`  原文: ${data.sourceText}`);
  
  if (data.translationTexts) {
    Object.entries(data.translationTexts).forEach(([lang, text]) => {
      console.log(`  ${lang.toUpperCase()}: ${text}`);
    });
  }
});
```

### 转录多个指定用户

```javascript
const robotId = await trtc.startPlugin('RealtimeTranscriber', {
  sourceLanguage: 'en',
  translationLanguages: ['zh', 'ja'],
  userIdsToTranscribe: ['userA', 'userB', 'userC']
});
```

## API 说明

### trtc.startPlugin('RealtimeTranscriber', options)

用于启动实时转录任务。

**options: RealtimeTranscriberStartOptions**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| sourceLanguage | `string` | 必填 | 源语言代码，目前仅支持 `'zh'`、`'en'` |
| translationLanguages | `string` \| `string[]` | 选填 | 翻译目标语言，支持单个语言或语言数组，如 `'en'` 或 `['en', 'ja']` |
| userIdsToTranscribe | `string` \| `string[]` | 选填 | 要转录的用户 ID，默认为 `'all'` 转录所有用户，也可指定单个或多个用户 ID |
| transcriberRobotId | `string` | 选填 | 自定义转录机器人 ID，不传则自动生成 |

**Returns: Promise\<string\>**

返回转录任务的 `transcriberRobotId`，用于后续停止任务。

**Example:**

```javascript
const robotId = await trtc.startPlugin('RealtimeTranscriber', {
  sourceLanguage: 'zh',
  translationLanguages: ['en', 'ja'],
  userIdsToTranscribe: 'all',
});
```

### trtc.stopPlugin('RealtimeTranscriber', options)

用于停止实时转录任务。

**options**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| transcriberRobotId | `string` | 必填 | 启动时返回的转录机器人 ID |

**Example:**

```javascript
await trtc.stopPlugin('RealtimeTranscriber', { transcriberRobotId: robotId });
```

## 事件说明

### TRTC.EVENT.REALTIME_TRANSCRIBER_STATE_CHANGED

转录任务状态变化事件。

**RealtimeTranscriberStateEvent**

| Name | Type | Description |
|------|------|-------------|
| state | `'started'` \| `'stopped'` | 转录任务状态 |
| roomId | `string` \| `number` | 房间 ID |
| transcriberRobotId | `string` | 转录机器人 ID |
| sourceLanguage | `string` | 源语言（可选） |
| error | `number` | 错误码（可选） |
| errorMessage | `string` | 错误信息（可选） |

**Example:**

```javascript
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_STATE_CHANGED, (event) => {
  if (event.state === 'started') {
    console.log('转录任务已启动');
  } else if (event.state === 'stopped') {
    console.log('转录任务已停止');
  }
});
```

### TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE

转录消息事件，当有新的转录结果时触发。

**RealtimeTranscriberMessageEvent**

| Name | Type | Description |
|------|------|-------------|
| segmentId | `string` | 语音片段 ID，用于标识一段连续的语音 |
| speakerUserId | `string` | 说话人的用户 ID |
| sourceText | `string` | 转录的原文文本 |
| translationTexts | `Array<{ language: string, text: string }>` | 翻译文本数组，如 `[{ language: 'en', text: 'hello' }, { language: 'ja', text: 'こんにちは' }]` |
| timestamp | `number` | 时间戳 |
| isCompleted | `boolean` | 该语音片段是否已完成。`false` 表示中间结果（实时更新），`true` 表示最终结果 |
| robotId | `string` | 转录机器人 ID |

**Example:**

```javascript
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (event) => {
  const { speakerUserId, sourceText, translationTexts, isCompleted } = event;
  
  // 显示实时转录结果
  console.log(`[${isCompleted ? '完成' : '进行中'}] ${speakerUserId}: ${sourceText}`);
  
  // 显示翻译结果
  if (translationTexts) {
    translationTexts.forEach(({ language, text }) => {
      console.log(`  ${language}: ${text}`);
    });
  }
});

// 取消监听
trtc.off(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, handler);
```

## 支持的语言

### ASR 源语言

| 语言 | 代码 |
|------|------|
| 中文 | `zh` |
| 英语 | `en` |

<!--
| 中文繁体 | `zh-tw` |
| 中国方言 | `zh-dialect` |
| 中国粤语 | `zh-yue` |
| 越南语 | `vi` |
| 日语 | `ja` |
| 韩语 | `ko` |
| 印度尼西亚语 | `id` |
| 泰语 | `th` |
| 葡萄牙语 | `pt` |
| 土耳其语 | `tr` |
| 阿拉伯语 | `ar` |
| 西班牙语 | `es` |
| 印地语 | `hi` |
| 法语 | `fr` |
| 马来语 | `ms` |
| 菲律宾语 | `fil` |
| 德语 | `de` |
| 意大利语 | `it` |
| 俄语 | `ru` |
| 瑞典语 | `sv` |
| 丹麦语 | `da` |
| 挪威语 | `no` |
-->

### 翻译目标语言

| 语言 | 代码 |
|------|------|
| 中文 | `zh` |
| 英语 | `en` |
| 越南语 | `vi` |
| 日语 | `ja` |
| 韩语 | `ko` |
| 印度尼西亚语 | `id` |
| 泰语 | `th` |
| 葡萄牙语 | `pt` |
| 阿拉伯语 | `ar` |
| 西班牙语 | `es` |
| 法语 | `fr` |
| 马来语 | `ms` |
| 德语 | `de` |
| 意大利语 | `it` |
| 俄语 | `ru` |

## 常见问题

**1. 如何区分中间结果和最终结果？**

通过 `REALTIME_TRANSCRIBER_MESSAGE` 事件中的 `isCompleted` 字段判断。当 `isCompleted` 为 `false` 时，表示当前是实时更新的中间结果；当 `isCompleted` 为 `true` 时，表示该语音片段已完成，返回最终结果。

```javascript
trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (event) => {
  if (event.isCompleted) {
    // 最终结果，可以存储或展示
    saveSubtitle(event);
  } else {
    // 中间结果，可以实时更新 UI
    updateLiveCaption(event);
  }
});
```

**2. 如何根据 segmentId 合并字幕？**

每个连续的语音片段会有相同的 `segmentId`，可以根据此 ID 进行字幕的更新和合并：

```javascript
const subtitleMap = new Map();

trtc.on(TRTC.EVENT.REALTIME_TRANSCRIBER_MESSAGE, (event) => {
  subtitleMap.set(event.segmentId, event);
  
  if (event.isCompleted) {
    // 语音片段完成，可以归档
    archiveSubtitle(subtitleMap.get(event.segmentId));
  }
});
```

**3. 如何节省带宽？**

当不需要接收转录消息时，移除 `REALTIME_TRANSCRIBER_MESSAGE` 事件的所有监听器，插件会自动暂停接收消息以节省带宽。当重新添加监听器时，会自动恢复接收消息。
