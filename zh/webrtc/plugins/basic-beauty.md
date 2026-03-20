> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用基础美颜插件。[在线体验](https://web.sdk.qcloud.com/trtc/webrtc/v5/test/qer/basic-beauty/index.html)

## 前提条件

- TRTC Web SDK 版本 >= 5.7。
- 需要浏览器支持 WebGL 2.0 与 WebAssembly: Chrome 56+, Edge 79+, Safari 15+, Firefox 51+, Chrome for Android 126+。

## 实现流程

### 1. 引入并注册插件

```javascript
import { BasicBeauty } from 'trtc-sdk-v5/plugins/video-effect/basic-beauty';

let trtc = TRTC.create({ plugins: [BasicBeauty] });
```

### 2. 开启本地摄像头

```javascript
await trtc.startLocalVideo({
  view: 'local-video'
});
```

### 3. 使用基础美颜插件

```javascript
await trtc.startPlugin('BasicBeauty', {
  beauty: 0.5, // 美颜
  brightness: 0.5, // 明亮
  ruddy: 0.5, // 红润
});

// 开启后可调用 updatePlugin 更新参数
await trtc.updatePlugin('BasicBeauty', {
  beauty: 0.5, // 美颜
  brightness: 0.5, // 明亮
  ruddy: 0.5, // 红润
});

await trtc.stopPlugin('BasicBeauty');
```

## API 说明

### trtc.startPlugin('BasicBeauty', options)

用于开启美颜效果

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| beauty     | `number`  |    选填          | 美颜程度，取值范围 [0, 1] 默认为 0.5 |
| brightness    | `number`  |     选填         | 明亮程度，取值范围 [0, 1] 默认为 0.5       |
| ruddy   | `number` | 选填 | 红润程度，取值范围 [0, 1] 默认为 0.5        |

**Example:**

```javascript
await trtc.startPlugin('BasicBeauty', {
  beauty: 0.5, // 美颜
  brightness: 0.5, // 明亮
  ruddy: 0.5, // 红润
});
```

### trtc.updatePlugin('BasicBeauty', options)

可修改美颜效果参数

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| beauty     | `number`  |    选填          | 美颜程度，取值范围 [0, 1] 默认为 0.5 |
| brightness    | `number`  |     选填         | 明亮程度，取值范围 [0, 1] 默认为 0.5       |
| ruddy   | `number` | 选填 | 红润程度，取值范围 [0, 1] 默认为 0.5        |

**Example:**

```javascript
await trtc.updatePlugin('BasicBeauty', {
  beauty: 0.5, // 美颜
  brightness: 0.5, // 明亮
  ruddy: 0.5, // 红润
});
```

### trtc.stopPlugin('BasicBeauty')

关闭美颜效果
