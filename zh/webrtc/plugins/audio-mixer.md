> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文将介绍如何在通话过程中增加背景音乐的功能，点击 [体验 demo](https://web.sdk.qcloud.com/trtc/webrtc/demo/api-sample/improve-audio-mixer.html?roomID=43222) 进行体验。

## 前提条件

- TRTC 版本 > 5.1.0
- 支持在通话中添加背景音乐的平台如下

  |  操作系统   | 浏览器类型            | 浏览器最低版本要求 |
  |:-------:|:-----------------|:---------:|
  | Mac OS  | 桌面版 Chrome 浏览器   |    56+    |
  | Mac OS  | 桌面版 Safari 浏览器   |    11+    |
  | Mac OS  | 桌面版 Firefox 浏览器  |    56+    |
  | Mac OS  | 桌面版 Edge         |    80+    |
  | Windows | 桌面版 Chrome 浏览器   |    56+    |
  | Windows | 桌面版 QQ 浏览器（极速模式） |   10.4+   |
  | Windows | 桌面版 Firefox 浏览器  |    56+    |
  | Windows | 桌面版 Edge         |    80+    |
  |   iOS   | 移动版 Safari 浏览器   |    14+    |
  |   iOS   | 微信内嵌网页           |     ✖     |
  | Android | 移动版 Chrome 浏览器   |    81+    |
  | Android | 微信内嵌网页(TBS 内核)   |     ✔     |
  | Android | 移动版 QQ 浏览器       |     ✖     |

## 实现流程

### 1. 进房并打开麦克风

```js
const trtc = TRTC.create();
await trtc.enterRoom({ roomId: 8888, sdkAppId, userId, userSig });
await trtc.startLocalAudio();
```

### 2. 开启一首背景音乐

如开启 `../assets/count.mp3`，使用 `trtc.startPlugin` 方法，将其 id 设置为 `count`，后续操作这个 id 即操作这个音乐。

```javascript
await trtc.startPlugin('AudioMixer', {
  id: 'count',
  url: '../assets/count.mp3'
})
```

除此之外还可以传入 loop、volume、事件监听等参数，详见后文 API 说明部分。

### 3. 按需更新背景音乐

如果需要对音乐进行进一步操作，可以根据上一步注册的音乐 id，调用 `trtc.updatePlugin` 接口来进行。

除 loop、volume、事件监听等参数之外，updatePlugin 还可以执行音乐暂停、恢复、跳转操作。详见后文 API 说明部分。

```javascript
// 取消循环播放
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  loop: false
})
// 设置音量
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  volume: 1
})
// 将 count 音乐暂停
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  operation: 'pause'
})
// 将 count 音乐恢复
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  operation: 'resume'
})
// 将 count 音乐从第 5s 开始播放
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  seekFrom: 5
})
```

### 4. 关闭背景音乐

注意，音乐播放一旦 start 后，无法自行 stop，因此需要此接口来关闭某个不需要使用的音乐。关闭后方可再次 start。

```javascript
await trtc.stopPlugin('AudioMixer', {
  id: 'count'
})
```

```js
// bad: 连续 start 相同 id 两次
startPlugin({ id: 'bgm1', url: './xxx.mp3' })
startPlugin({ id: 'bgm1', url: './xxx.mp3' }) 

// good: start 后用 update 
startPlugin({ id: 'bgm1', url: './xxx.mp3' })
updatePlugin({ id: 'bgm1' }) 

// good: 可以同时开启两个背景音乐
startPlugin({ id: 'bgm1' })
startPlugin({ id: 'bgm2' }) 

// good: stop 后下次可以正常再开启
startPlugin({ id: 'bgm1' })
stopPlugin({ id: 'bgm1' }) 
startPlugin({ id: 'bgm1' }) 
```

## API 说明

### trtc.startPlugin('AudioMixer', options)

用于增加一个背景音乐

#### options

| Name   | Type      | Attributes   | Description                         |
|--------|-----------|--------------|-------------------------------------|
| id     | `string`  |              | 给当前传入的音乐 url 自定义一个唯一的 ID， 例如： 'bgm' |
| url    | `string`  |              | 音乐文件地址，与下方的 track 字段必须二选一填入，若同时传入，以 url 为准                            |
| track | `MediaStreamAudioTrack`  | | 自 v5.4.3+ 支持，与 url 二选一，可以传入从 `<audio/>` 标签中提取的 ，若同时传入，以 url 为准                  |
| loop   | `boolean` | `<optional>` | 是否循环播放，默认为 false                     |
| volume | `number`  | `<optional>` | 设置初始音量，默认为 1 (0-1)。暂不支持 iOS。               |
| playbackRate | `number`  | `<optional>` | 设置播放速率，默认为 1.0                  |
| onDurationChange | `function`  | `<optional>` | 回调函数，音乐加载完会执行，参数为 (duration: number) 标识音乐总时长 |
| onTimeUpdate | `function`  | `<optional>` | 回调函数，音乐播放过程中会持续触发，参数为 (currentTime: number, duration: number) 标识音乐当前进度和总时长 |
| onEnded | `function`  | `<optional>` | 回调函数，音乐结束会执行（注意 loop 时每次播放到音乐末尾不会执行），无参数 |

**Example:**

```javascript
await trtc.startPlugin('AudioMixer', {
  id: 'count',
  url: '../assets/count.mp3',
  loop: true,
  volume: 0.2,
  playbackRate: 1.5
})
```

使用回调：

```js
await trtc.startPlugin('AudioMixer', {
  id: 'wow',
  url: '../assets/music/wow.mp3',
  onDurationChange: (duration) => {
    console.warn(`duration: ${duration}`);
  },
  onTimeUpdate: (currentTime, duration) => {
    console.warn(`currentTime/duration: ${currentTime}/${duration}`);
  },
  onEnded: () => {
    console.warn('ended');
  }
});
```

### trtc.updatePlugin('AudioMixer', options)

#### options

| Name      | Type      | Attributes   | Description                           |
|-----------|-----------|--------------|---------------------------------------|
| id        | `string`  |              | 调用 startPlugin 时定义的背景音乐文件的 ID         |
| loop      | `boolean` | `<optional>` | 是否循环播放，默认为false                       |
| volume    | `number`  | `<optional>` | 设置初始音量，默认为1 (0-1)                     |
| seekFrom  | `number`  | `<optional>` | 从第几秒开始 seek 播放，此功能需要背景音乐资源文件的请求支持[范围请求](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Range_requests)                        |
| operation | `number`  | `<optional>` | 对背景音乐的操作: 'pause' ｜ 'resume' ｜ 'stop' |
| playbackRate | `number`  | `<optional>` | 设置播放速率，默认为 1.0                  |
| onDurationChange | `function`  | `<optional>` | 回调函数，音乐加载完会执行，参数为 (duration: number) 标识音乐总时长 |
| onTimeUpdate | `function`  | `<optional>` | 回调函数，音乐播放过程中会持续触发，参数为 (currentTime: number, duration: number) 标识音乐当前进度和总时长 |
| onEnded | `function`  | `<optional>` | 回调函数，音乐结束会执行（注意 loop 时每次播放到音乐末尾不会执行），无参数 |

**Example:**

```javascript
await trtc.updatePlugin('AudioMixer', {
  id: 'count',
  loop: true,
  volume: 0.2,
  operation: 'pause'
})
```

### trtc.stopPlugin('AudioMixer', options)

关闭某个背景音乐。

#### options

| Name      | Type      | Attributes   | Description                   |
|-----------|-----------|--------------|-------------------------------|
| id        | `string`  |              | 调用 startPlugin 时定义的背景音乐文件的 ID |

**Example:**

```javascript
await trtc.stopPlugin('AudioMixer', {
  id: 'count'
})
```

## 常见问题

**1. 出现跨域错误**

例如：Access to audio at XXX from origin XXX has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is
present on the requested resource.

解决：您需要配置线上音乐文件的 CORS 协议。

**2. 音频格式不对，浏览器无法播放**

例如：NotSupportedError: The operation is not supported.

解决：您需要使用浏览器支持的音频格式。
