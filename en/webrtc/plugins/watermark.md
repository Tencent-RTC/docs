> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

## Function Description

This article introduces how to use the watermark plugin to add image watermarks on camera streams.

## Prerequisites

- TRTC Web SDK version >= 5.3.0.

## Implementation Steps

### Install Watermark Plugin

```javascript
import { Watermark } from 'trtc-sdk-v5/plugins/video-effect/watermark';

let trtc = TRTC.create({ plugins: [Watermark] });
```

### Open Camera

```javascript
await trtc.startLocalVideo({
  view: 'local-video'
  option: {
    mirror: false
  }
});
```

### Start Watermark Plugin

```javascript
await trtc.startPlugin('Watermark', {
  imageUrl: 'https://web.sdk.qcloud.com/trtc/webrtc/test/qer-test/watermark/trtc-watermark-test.png'
});
```

After testing, you can replace the test image URL with your own watermark image. It is recommended to use a transparent PNG format.

### Stop Watermark Plugin

```javascript
await trtc.stopPlugin('Watermark');
```

## API Description

### trtc.startPlugin('Watermark', options)

Start watermark plugin.

#### options

| Name      | Type                             | Attributes | Description                                                                                                                                                                                                                                                                                  |
|-----------|----------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| imageUrl  | `string`                         | Required   | Image watermark URL                                                                                                                                                                                                                                                                           |
| x         | `string`                         | Optional   | Watermark left margin                                                                                                                                                                                                                                                                         |
| y         | `string`                         | Optional   | Watermark top margin                                                                                                                                                                                                                                                                          |
| size      | `string \| number \| object`     | Optional   | When passing a string: <br> - `"cover"` scales the background image to fully cover the background area, which may cause parts of the background image to be invisible. <br> - `"contain"` scales the background image to fit entirely within the background area, possibly leaving some areas blank. <br> When passing a number: <br> - `x` scales the background image by x times, e.g., 0.5, with a valid range of `(0,1]` <br> When passing an object: <br> - You can specify manually by passing `{width: 200, height: 300}` <br> The default is `cover` |

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

### trtc.updatePlugin('Watermark', options)

Update the watermark image, position, and size.

#### options

| Name      | Type                             | Attributes | Description                                                                                                                                                                                                                                                                                  |
|-----------|----------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| imageUrl  | `string`                         | Required   | Image watermark URL                                                                                                                                                                                                                                                                           |
| x         | `string`                         | Optional   | Watermark left margin                                                                                                                                                                                                                                                                         |
| y         | `string`                         | Optional   | Watermark top margin                                                                                                                                                                                                                                                                          |
| size      | `string \| number \| object`     | Optional   | When passing a string: <br> - `"cover"` scales the background image to fully cover the background area, which may cause parts of the background image to be invisible. <br> - `"contain"` scales the background image to fit entirely within the background area, possibly leaving some areas blank. <br> When passing a number: <br> - `x` scales the background image by x times, e.g., 0.5, with a valid range of `(0,1]` <br> When passing an object: <br> - You can specify manually by passing `{width: 200, height: 300}` <br> The default is `cover` |

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

Stop watermark plugin.

**Example:**

```javascript
await trtc.stopPlugin('Watermark');
```

## Q&A

**1. Is the watermark mirrored?**

The local preview image is enabled by default, so the watermark will also be mirrored. You can disable the local preview mirror image.

```javascript
await trtc.updateLocalVideo({
  option: {
    mirror: false
  }
});
```

**2. Why does the screen flashes black briefly after updating the watermark?**

Update SDK to v5.8.6+.
