> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文档详细介绍了如何使用 TRTC Web SDK 的 VideoMixer 插件，将摄像头、屏幕共享、文字、图片和视频等多个输入源合成为一个视频轨道，实现自定义布局的推流。开发者可以灵活控制每个源的布局（位置、大小、层级 zIndex）。

![The Workflow of Video Mixer](./assets/videomixer-workflow.png)

## 前提条件

- TRTC Web SDK version >= 5.12.0.

- 兼容性：

  | 浏览器         | 兼容性           |
  | -------------- | ---------------- |
  | PC Chrome  | ✅                |
  | PC Safari  | ✅                |
  | PC Firefox | ✅                |
  | PC Edge    | ✅                |
  | 移动端         | ❌暂不支持合流功能 |

## 实现流程

### 1. 引入并注册插件

```javascript
import { VideoMixer } from 'trtc-sdk-v5/plugins/video-effect/video-mixer';

const trtc = TRTC.create({ plugins: [VideoMixer] });
```

### 2. 开启合流

通过调用 `trtc.startPlugin('VideoMixer', options)` 开启合流插件。您必须在 options 中设置合流画布的宽高 [canvasInfo](#canvasinfo)，其余参数都是可选参数。

```javascript
const options = {
  view: 'local_preview_div', // 预览
  canvasInfo: { width: 1920, height: 1080 },
  camera: [{ // 添加一个摄像头输入源
      id: 'camera1',
      layout: { x: 0, y: 0, width: 640, height: 480, zIndex: 1 }
    }]
  // ...
}
const { track } = await trtc.startPlugin('VideoMixer', options);
// track 即为合流输出的 MediaStreamTrack，可用于推流
```

### 3. 合流后推流

合流插件只负责生成合流后的视频轨道，推流操作仍由 TRTC SDK 控制。将 startPlugin 返回的 track 作为参数传入 startLocalVideo 接口即可进行推流。

```js
await trtc.startLocalVideo({ option: { 
  videoTrack: track,
  profile: { width: 1920, height: 1080, bitrate: 2000 } // 设置编码参数
}});
```

### 4. 管理输入源（更新和移除）

通过调用 `trtc.updatePlugin('VideoMixer', options)` 动态添加、更新或移除合流输入源。

**更新规则：**

* **输入源说明：** 输入源分为：`camera, screen, text, image, video`。都是数组，每一个数组元素代表一个输入源，通过 id 属性做唯一标识。
* **更新输入源：** 传入相同 id，并修改其他参数。  
* **添加输入源：** 传入新的 id 对象。
* **移除输入源：** 从对应的输入源数组中去掉该对象。

#### 摄像头

可以控制摄像头的采集和布局。

```javascript
// 更新 id 为 'camera1' 的摄像头布局  
await trtc.updatePlugin('VideoMixer', {
  camera: [
    {
      id: 'camera1',
      profile: { width: 640, height: 480, frameRate: 15 }, // 更新摄像头采集参数
      layout: { x: 100, y: 0, width: 640, height: 480, zIndex: 1, rotation: 90, mirror: true } // 更新摄像头布局参数
    }
  ]
});

// 移除所有摄像头  
await trtc.updatePlugin('VideoMixer', { camera: [] });
// 如果只是想临时隐藏画布上的输入源，而不是物理关闭采集，可以在 layout 参数中设置 hidden: true。其他输入源同理。
```

#### 屏幕共享

```js
// 添加屏幕共享
await trtc.updatePlugin('VideoMixer', {
  screen: [{
    id: 'screen1',
    profile: { width: 1920, height: 1080, frameRate: 15 }, // 屏幕共享采集参数
    layout: {
      x: 0,
      y: 0,
      width: 1920,
      height: 1080,
      zIndex: 0,      
    },
    systemAudio: true, // 是否采集系统音频，默认不采集 v5.15 版本新增
  }],
  // 如果用户未通过 SDK 而是通过浏览器按钮取消了屏幕共享，你可以通过该回调监听
  onScreenShareStop: (id: string) => console.log(`屏幕共享已停止，id: ${id}`);
});
```

#### 文字

可以添加文字输入源，`text`参数数组中传入一个带有唯一 id 的对象代表合流一个文字源：

> 注：文字内容过多时超出 layout 区域的部分会被裁剪，业务侧需根据文字内容自行调整 layout 宽高。

```js
await trtc.updatePlugin('VideoMixer', {
  text: [
    {
      id: 'text1',
      content: 'MultiLine\nTest',
      font: 'bold 60px SimHei',
      color: 'red',
      layout: {
        x: 200,
        y: 300,
        width: 300,
        height: 150,
        zIndex: 6,
      }
    }
  ]
});
```

#### 图片和视频

可添加图片（如 Logo）或本地视频文件作为合流输入源。

```js
await trtc.updatePlugin('VideoMixer', {
  image: [{ 
    id: 'img1', 
    url: './image.png',
    layout: { x: 0, y: 500, width: 800, height: 400, zIndex: 4 }
  }],
  video: [{ 
    id: 'video1', 
    url: './video.mp4',
    layout: { x: 0, y: 0, width: 1000, height: 500, zIndex: 5 }
  }]
});
```

### 5. 关闭合流

移除所有输入源并关闭合流：

```javascript
await trtc.stopPlugin('VideoMixer');
```

## 错误处理

合流插件的错误处理策略旨在保障部分输入源错误时不会影响其他正常输入源的合流。

| 场景 | 接口行为 | 错误信息返回方式 |
| :---- | :---- | :---- |
| **部分成功，部分失败** | 接口不会抛出错误（try...catch 无法捕获） | 以返回值 MixParseResult 中的 result.failedDetails 告知失败信息 |
| **全部失败或关键错误** | 接口抛出错误（try...catch 可捕获） | 错误对象中携带失败信息，如 error?.data?.failedDetails |

业务侧逻辑示例：

```ts
try {
  const { result } = await trtc.updatePlugin('VideoMixer', {
    camera: [/* ... */],
    screen: [/* ... */],
    text: [/* ... */],
    image: [/* ... */],
    video: [/* ... */],
  });
  // 场景一：部分失败时的错误处理
  result.failedDetails.forEach(({id, error}: {id: string, error: any}) => {
    console.error(`${id} mix failed`, error);
  })
  console.log(result.successOptions) // 本次更新成功应用的参数
} catch (error: any) {
  // 场景二：全部失败时的错误捕获 (捕获抛出的错误)  
  console.error(error);
  error?.data?.failedDetails.forEach(({ id, error }: { id: string, error: any }) => {
    console.error(`${id} mix failed`, error);
  })  
}
```

**关于 successOptions 的详细说明**

该返回值告知业务侧本次更新后合流底层真正应用的参数。当部分更新失败时，successOptions 会包括成功更新的部分和沿用旧配置的失败部分，便于业务侧同步 UI 或数据状态。

## API 说明

### trtc.startPlugin('VideoMixer', options)

用于开启合流插件并初始化画布和输入源。

<a name="options"></a>**Options**

| Name              | Type                                | Required | Description                                                                                                          |
| ----------------- | ----------------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------- |
| view              | string \| HTMLElement \| null | 选填     | 合流视频预览的 HTMLElement 实例或者 Id， 如果不传或传入 null， 则不会播放合流视频。                                  |
| canvasInfo        | [CanvasInfo](#canvasinfo)           | 必填     | 设置合流画布的参数                                                                                                   |
| camera            | [CameraSource](#camerasource)[]     | 选填     | 控制摄像头输入源的参数                                                                                               |
| screen            | [ScreenSource](#screensource)[]     | 选填     | 控制共享屏幕输入源的参数                                                                                             |
| text              | [TextSource](#textsource)[]         | 选填     | 控制文字输入源的参数                                                                                                 |
| image             | [ImageSource](#imagesource)[]       | 选填     | 控制图片输入源的参数                                                                                                 |
| video             | [VideoSource](#videosource)[]       | 选填     | 控制视频输入源的参数                                                                                                 |
| onScreenShareStop | `(id: string) => {}`                | 选填     | 合流共享屏幕停止时调用的回调，用户未通过 sdk 而是通过浏览器按钮取消屏幕分享时可能会用到，回调参数为停止采集的屏幕 id |

**Returns: Promise\<MixParseResult\>**

`MixParseResult`

| Name   | Type              | Description                                                                                                                                                                                                                                                                  |
| ------ | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| track  | [MediaStreamTrack](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack)  | 合流输出的视频轨道                                                                                                                                                                                                                                                           |
| systemAudioTrackList  | object | 系统音频轨道列表。在设置 ScreenSource 参数 systemAudio:true 并且采集成功后会返回系统音频的 audio track。数据格式为：{key: audioTrack}，其中 key 为 ScreenSource 配置的 ID，例如： {id1: audioTrack，id2: audioTrack}                                                                                                                                                                                                                                                         |
| result | object            | 本次传入的参数中执行成功或失败的相关信息，结构如下：| Name | Type | Description |
| --- | --- | --- |
| successOptions | [Options](#options) | 本次更新成功执行的 options |
| failedDetails | MixFailedDetail[] | 本次更新执行失败详细信息 | |

`MixFailedDetail`

| Name  | Type     | Description         |
| ----- | -------- | ------------------- |
| id    | string | 更新失败的输入源 id |
| error | Error  | 更新失败的原因      |

### trtc.updatePlugin('VideoMixer', options)

更新合流输入源和布局。options 结构与 startPlugin 一致，update 时 canvasInfo 不是必填项。

### trtc.stopPlugin('VideoMixer')

停止合流。

## 核心参数结构

### <a name="canvasinfo"></a>CanvasInfo

| Name        | Type                                          | Required | Description                                                                                                                   |
| ----------- | --------------------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------- |
| canvasColor | string \| [CanvasGradient](https://developer.mozilla.org/en-US/docs/Web/API/CanvasGradient) \| [CanvasPattern](https://developer.mozilla.org/en-US/docs/Web/API/CanvasPattern) | 选填     | 合流背景色，参考 [canvas fillStyle](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle) 属性 |
| width       | number                                        | 必填     | 合流画布宽度                                                                                                                  |
| height      | number                                        | 必填     | 合流画布高度                                                                                                                  |
| frameRate   | number                                        | 选填     | 手动指定合流画面帧率。一般情况下最好由合流内部自动计算帧率，手动指定可能会损失部分性能                                        |

### <a name="camerasource"></a>CameraSource

| Name       | Type                                                                                         | Required | Description                         |
| ---------- | -------------------------------------------------------------------------------------------- | -------- | ----------------------------------- |
| id         | string                                                                                       | 必填     | 标识输入源的唯一 id                 |
| layout     | [LayerOption](#layeroption)                                                                  | 必填     | 布局参数                            |
| cameraId   | string                                                                                       | 选填     | 采集的摄像头 id，指定采集哪个摄像头 |
| videoTrack | MediaStreamTrack                                                                             | 选填     | 自定义采集的 videoTrack             |
| profile    | [VideoProfile](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#VideoProfile) | 选填     | 视频采集参数。默认值：`480p_2`。    |

### <a name="screensource"></a>ScreenSource

| Name                 | Type                                                                                                     | Required | Description                                                                                                                                                                 |
| -------------------- | -------------------------------------------------------------------------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id                   | string                                                                                                   | 必填     | 标识输入源的唯一 id                                                                                                                                                         |
| layout               | [LayerOption](#layeroption)                                                                              | 必填     | 布局参数                                                                                                                                                                    |
| profile              | [ScreenShareProfile](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#ScreenShareProfile) | 选填     | 屏幕采集参数。默认值：`1080p`。                                                                                                                                             |
| captureElement       | HTMLElement                                                                                              | 选填     | 采集当前页面中的某个 HTMLElement，支持 Chrome 104+。                                                                                                                        |
| preferDisplaySurface | 'current-tab' \| 'tab' \| 'window' \| 'monitor'                                                          | 选填     | 控制屏幕分享预选框中的不同类型采集的显示优先级，默认是 monitor，即：在屏幕分享采集预选框中，优先展示屏幕采集。若填 'current-tab'，预选框只会展示当前页面。支持 Chrome 94+。 |
| systemAudio          | boolean                                                                                                   | 选填     | 是否采集系统音频，与屏幕采集的 systemAudio 功能一致，默认不采集，v5.15+支持 |

### <a name="textsource"></a>TextSource

| Name    | Type                                          | Required | Description                                                                                                                 |
| ------- | --------------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------- |
| id      | string                                        | 必填     | 标识输入源的唯一 id                                                                                                         |
| layout  | [LayerOption](#layeroption)                   | 必填     | 布局参数                                                                                                                    |
| content | string                                        | 必填     | 文字内容                                                                                                                    |
| color   | string \| [CanvasGradient](https://developer.mozilla.org/en-US/docs/Web/API/CanvasGradient) \| [CanvasPattern](https://developer.mozilla.org/en-US/docs/Web/API/CanvasPattern)| 选填     | 文字颜色，参考 [canvas fillStyle](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle) 属性 |
| font    | string                                        | 选填     | 字体，参考 [canvas font](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/font) 属性               |

### <a name="imagesource"></a>ImageSource

| Name   | Type                        | Required | Description         |
| ------ | --------------------------- | -------- | ------------------- |
| id     | string                      | 必填     | 标识输入源的唯一 id |
| layout | [LayerOption](#layeroption) | 必填     | 布局参数            |
| url    | string                      | 必填     | 图片url             |

### <a name="videosource"></a>VideoSource

| Name   | Type                        | Required | Description         |
| ------ | --------------------------- | -------- | ------------------- |
| id     | string                      | 必填     | 标识输入源的唯一 id |
| layout | [LayerOption](#layeroption) | 必填     | 布局参数            |
| url    | string                      | 必填     | 视频url             |

### <a name="layeroption"></a>LayerOption

| Name     | Type                         | Required | Description                                                                                                                                               |
| -------- | ---------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| x        | number                       | 必填     | 相对画布左上角的横向坐标，可以为负数                                                                                                                      |
| y        | number                       | 必填     | 相对画布左上角的纵向坐标，可以为负数                                                                                                                      |
| width    | number                       | 必填     | 输入源的布局宽度，需要 > 0                                                                                                                                |
| height   | number                       | 必填     | 输入源的布局高度，需要 > 0                                                                                                                                |
| zIndex   | number                       | 必填     | 输入源层级，每个输入源的层级不应重复                                                                                                                      |
| fillMode | 'contain' \| 'cover' \| 'fill' | 选填     | 画面填充布局的模式，摄像头默认`cover`，其余输入源默认`contain`，参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| mirror   | boolean                      | 选填     | 是否镜像                                                                                                                                                  |
| rotation | 0 \| 90 \| 180 \| 270          | 选填     | 画面旋转角度                                                                                                                                              |
| hidden   | boolean                      | 选填     | 是否暂时隐藏画布上的输入源，使用场景：不想物理层面关闭摄像头/屏幕、临时隐藏输入源而不销毁资源                                                             |

## 最佳实践与常见问题

### 1. 帧率与性能优化

* **帧率设置：**  
  * **不推荐**手动设置 canvasInfo.frameRate。  
  * **推荐**由插件内部自动计算：当存在摄像头或屏幕分享时，取两者中的最大帧率；否则固定为 15 帧。手动指定可能导致性能损失。  
* **性能考量：**  
  * 画布分辨率越大、合流的输入源越多，渲染开销越大。业务侧应根据用户设备性能情况，决定合适的画布参数和输入源数量。

### 2. 分辨率、码率怎么设置？

- 分辨率就是画布宽高
- 码率不由合流插件控制，业务侧推流时通过 `startLocalVideo/updateLocalVideo` 设置。

### 3. 为什么输入源传参都是数组形式，如果只想更新数组中某一类输入源怎么做？

- 每种类型的输入源都是全量更新的方式，目的是为了支持批量更新以及删除（通过对比前后传入的数组）。
- 每种输入源参数都是可选的，比如只想更新 camera 时，updatePlugin 时只传入 camera 数组，其他类型输入源的数组不传，或者一并传入但是保留原参数。示例如下：

  ```js
  await trtc.startPlugin('VideoMixer', { // 假设已经合流了摄像头和屏幕
    camera: [/* config */],
    screen: [/* config */]
  });
  ```

  此时如果只想更新 camera 而不更新 screen，可以仅传入 camera，不传入 screen

  ```js
  await trtc.updatePlugin('VideoMixer', {
    camera: [/* newConfig */], // 只传入camera数组
  });
  ```

### 4. 传入数组的前提下，只想更新某一个输入源怎么办？

比如更新某一个摄像头(id = 1)，那么传入的 camera 数组只改动 id = 1 的元素的参数，其他元素虽然不用更新，但是参数需要保持原样传入。

### 5. 采集摄像头和屏幕时设置的码率为什么无效？

采集摄像头和屏幕时设置的码率会被忽略，上行码率由业务侧推流时调用的接口（`start/updateLocalVideo`）设置。

### 6. 设置旋转角度不是围绕中心旋转？

合流旋转的原理是将画面旋转后，画面左上角仍保持 layout 传入的坐标再绘制，如果要实现中心旋转，业务侧需自行调整坐标。

### 7. 如果合流屏幕共享期间用户不是通过sdk而是点击浏览器的按钮停止屏幕共享，业务侧如何感知？

startPlugin 的参数中提供了`onScreenShareStop`回调，使用示例：

```js
await trtc.startPlugin('VideoMixer', {
  // ...
  onScreenShareStop: (id: string) => {
    console.log(`screen share stop, id: ${id}`);
  }
});
```