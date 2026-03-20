> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用美声插件。

## 前提条件
自 2023 年 4 月 1 日起，使用该功能需开通 TRTC [包月套餐](https://cloud.tencent.com/document/product/647/85386) 旗舰版及更高版本。

- TRTC Web SDK 版本 >= 5.10。
- 支持的浏览器: Chrome 66+, Edge 79+, Safari 15+, Firefox 76+, Chrome for Android 126+。

## 实现流程

### 1. 引入并注册插件

```javascript
import { VoiceChanger } from 'trtc-sdk-v5/plugins/voice-changer';

let trtc = TRTC.create({ plugins: [VoiceChanger] });
```

### 2. 开启麦克风

```javascript
await trtc.startLocalAudio();
```

### 3. 使用美声插件

```javascript
await trtc.startPlugin('VoiceChanger', {
  voiceType: 1,
  sdkAppId: 123456,
  userId: 'user_123',
  userSig: 'XXXXXXXX'
});

// 开启后可调用 updatePlugin 更新参数
await trtc.updatePlugin('VoiceChanger', {
  voiceType: 2,
});

// 关闭麦克风采集前关闭美声插件
await trtc.stopPlugin('VoiceChanger');
await trtc.stopLocalAudio();
```

## API 说明

### trtc.startPlugin('VoiceChanger', options)

用于开启美声效果

#### options
| Name       | Type     | Attributes                                                     | Description |
|------------|----------|----------------------------------------------------------------|-------------|
| sdkAppId   | `number` | 当前应用的 sdkAppId                                                 |
| userId     | `string` | 当前用户的 userId                                                   |
| userSig    | `string` | 当前用户的 userSig                                                  |
| voiceType  | `number` | 1-熊孩子 2-萝莉 3-大叔 4-重金属 5-感冒 6-外语腔 7-困兽 8-肥宅 9-强电流 10-重机械 11-空灵。 |

**Example:**

```javascript
await trtc.startLocalAudio();
await trtc.startPlugin('VoiceChanger', {
  voiceType: 1,
  sdkAppId: 123456,
  userId: 'user_123',
  userSig: 'XXXXXXXX'
});
```

### trtc.updatePlugin('VoiceChanger', options)

修改美声效果

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| voiceType  | `number` | 1-熊孩子 2-萝莉 3-大叔 4-重金属 5-感冒 6-外语腔 7-困兽 8-肥宅 9-强电流 10-重机械 11-空灵。 |

**Example:**

```javascript
await trtc.updatePlugin('VoiceChanger', {
  voiceType: 2,
});
```

### trtc.stopPlugin('VoiceChanger')

关闭美声效果
```javascript
// 关闭麦克风采集前关闭美声插件
await trtc.stopPlugin('VoiceChanger');
await trtc.stopLocalAudio();
```