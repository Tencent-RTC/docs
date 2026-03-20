> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用水印插件在视频流上添加图片水印。

## 前提条件

- TRTC Web SDK 版本 >= 5.3.2。

## 实现流程

### 引入并注册插件

```javascript
import { Watermark } from 'trtc-sdk-v5/plugins/video-effect/watermark';

let trtc = TRTC.create({ plugins: [Watermark] });
```

### 开启本地摄像头

```javascript
await trtc.startLocalVideo({
  view: 'local-video'
  option: {
    mirror: false
  }
});
```

### 开启水印插件

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/trtc-watermark-test.png'
});
```

跑通后可将测试图片 url 换为您自己的水印图片，推荐为可透明的 png 格式。

### 关闭水印

```javascript
await trtc.stopPlugin('Watermark');
```

## API 说明

### trtc.startPlugin('Watermark', options)

用于开启水印

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| imageUrl     | `string`  |    必填          | 图片水印地址 |
| x    | `string`  |     选填         | 水印左边距                              |
| y   | `string` | 选填 | 水印上边距                     |
| size     | `string \| number \| object` |    选填          | 传入字符串时：<br> - `"cover"` 缩放背景图片以完全覆盖背景区，可能背景图片部分看不见。 <br> - `"contain"` 缩放背景图片以完全装入背景区，可能背景区部分空白。 <br> 传入数字时： <br> - `x` 表示缩放背景图片 x 倍。如 0.5，取值范围为 `(0,1]` <br> 传入对象时：<br> - 可以传入 `{width: 200, height: 300}` 来手动指定 <br> 默认为 `cover`|

**Example:**

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/tv2.png',
  size: 'contain'
});
```

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/tv2.png',
  size: 'cover'
});
```

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/tv2.png',
  x: 0,
  y: 0,
  size: 0.5
});
```

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/tv2.png',
  x: 0,
  y: 0,
  size: {
    width: 100,
    height: 200
  }
});
```

### trtc.updatePlugin('Watermark')

更新水印的图片、位置、大小。

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| imageUrl     | `string`  |    必填          | 图片水印地址 |
| x    | `string`  |     选填         | 水印左边距                              |
| y   | `string` | 选填 | 水印上边距                     |
| size     | `string \| number \| object` |    选填          | 传入字符串时：<br> - `"cover"` 缩放背景图片以完全覆盖背景区，可能背景图片部分看不见。 <br> - `"contain"` 缩放背景图片以完全装入背景区，可能背景区部分空白。 <br> 传入数字时： <br> - `x` 表示缩放背景图片 x 倍。如 0.5，取值范围为 `(0,1]` <br> 传入对象时：<br> - 可以传入 `{width: 200, height: 300}` 来手动指定 <br> 默认为 `cover`|

```javascript
await trtc.updatePlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/tv2.png',
  x: 100,
  y: 200,
  size: {
    width: 50,
    height: 50
  }
});
```

### trtc.stopPlugin('Watermark')

关闭水印

**Example:**

```javascript
await trtc.stopPlugin('Watermark');
```

## 常见问题

**1. 水印是镜像的？**

本地预览的镜像是默认开启的，因此水印也会被镜像，可以将本地预览镜像关闭。

```javascript
await trtc.updateLocalVideo({
  option: {
    mirror: false
  }
});
```

**2. 更新水印画面会黑一下？**

更新 SDK 到 v5.8.6 以上。
