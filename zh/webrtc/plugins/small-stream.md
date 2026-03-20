> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

在多人视频通话场景中，随着推流用户的增加，对于拉流的用户来说，带宽流量消耗及设备性能消耗都会随之上升，如果不做优化，在中低端机型的体验会大打折扣。

本文主要介绍多人视频场景的最佳实践，通过使用 大小流 + 只拉可视区域的视频流 功能，以降低带宽消耗及保障最佳的通话体验。

## 开启大小流

大小流是指在开启摄像头时，同时编码两路视频流，一路是正常分辨率的视频（称为大流），另一路是低分辨率的视频（称为小流）。在拉流端可以选择拉大流或者拉小流，以达到节省流量的目的。

适用场景：大小流适用于多人视频通话场景，一般在多人通话场景的页面中，页面布局会有一个大画面 + n个小画面，位于大画面的视频拉大流，位于小画面内的视频拉小流。该功能会少量增加上行带宽，但可以大大节省下行带宽。

### 实现步骤

#### 1. 推流端开启大小流

在 [startLocalVideo](./TRTC.html#startLocalVideo) 时，填写 small 参数，可以开启小流编码。

```js
await trtc.startLocalVideo({ option: { small: '120p' }});
```

自 v5.3.0+ 支持动态开关小流

```js
await trtc.startLocalVideo();

// 动态开启小流
await trtc.updateLocalVideo({ option: { small: '120p' }});

// 动态关闭小流
await trtc.updateLocalVideo({ option: { small: false }});
```

#### 2. 拉流端选择拉大流或小流

在 [startRemoteVideo](./TRTC.html#startRemoteVideo) 时，填写 small 参数，可以拉小流，否则默认订阅大流。

拉小流时，不需要提前判断远端用户是否有推小流，若远端没有推小流，则 SDK 会自动拉大流。后续若该远端用户推小流了，SDK 会自动切换到拉小流。

```js
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  trtc.startRemoteVideo({ 
    view: '', // 传入视频容器的 elementId 或者 element 实例对象。
    userId, 
    streamType, 
    option: { small: true }
  });
})
```

在 [updateRemoteVideo](./TRTC.html#updateRemoteVideo) 时，填写 small 参数，可以切换指定 userId 的大小流。常用于实现这样的功能：用户点击“小画面”时，切换到“大画面”播放；用户点击“大画面”时，切换到“小画面”播放。

```js
// 用户点击“小画面”时，切换到“大画面”播放
await trtc.updateRemoteVideo({ 
  view: '', // 传入“大画面”视频容器的 elementId 或者 element 实例对象。
  userId: '', // 传入远端用户的 userId 
  streamType: TRTC.TYPE.STREAM_TYPE_MAIN, 
  option: { small: false }
})
// 用户点击“大画面”时，切换到“小画面”播放。
await trtc.updateRemoteVideo({ 
  view: '', // 传入“小画面”视频容器的 elementId 或者 element 实例对象。
  userId: '', // 传入远端用户的 userId 
  streamType: TRTC.TYPE.STREAM_TYPE_MAIN, 
  option: { small: true }})
```

#### 3. 开启【自动切换小流】插件（可选）

使用该插件 SDK 版本需 >= 5.11.0。

该插件通常用在带宽受限的场景，如带宽不足，导致大流卡顿，则自动切换到小流，会降低分辨率但保证流畅度。

默认的切换策略为：
- 当网络持续 10s 内延迟大于 200ms 或 丢包率大于 20%，并且帧率波动较大时，则切换到小流
- 当网络持续 20s 内延迟小于 100ms 且 丢包率小于 10% 时，并且帧率恢复正常时，则切回到大流
- 只允许自动切换到小流 2 次，超过 2 次后，则不再自动切换到小流

引入并注册插件：

```js
import SmallStreamAutoSwitcher from 'trtc-sdk-v5/plugins/small-stream-auto-switcher';
const trtc = TRTC.create({ plugins: [SmallStreamAutoSwitcher] });
```

插件使用：

```js
// 启动插件
await trtc.startPlugin('SmallStreamAutoSwitcher');

// 关闭插件
await trtc.stopPlugin('SmallStreamAutoSwitcher');
```

#### 注意事项

1. TRTC 的视频流有主流（主路视频流）和辅流（辅路视频流）之分。主流包括了大流和小流，辅流一般用于屏幕分享。
2. 拉流时，主流默认拉大流，通过 small 参数可以指定拉小流。支持同时拉大流 + 辅流，小流 + 辅流，不支持同时拉大流 + 小流。大流和小流在同一个视频容器内播放。
3. 只有推流端开启了小流，拉流端将远端流切换成小流才会生效。若没有小流，则 SDK 会自动拉大流。
4. 切换大小流成功目前没有事件通知。
5. 设置的小流分辨率不可以比大流的分辨率还要大，否则设置无效，保持原来的小流分辨率（默认 120p）。
6. 设置小流分辨率后，实际编码的分辨率会按照大流的长宽比例进行修改，保持和大流的长宽比例一致。

## 开启【只拉可视区域的视频流】功能

通常来说，多人视频场景下，由于页面空间有限，拉流端不会将所有的视频都展示在页面上。因此，对于那些不需要在页面上展示的远端视频，就可以不用拉流。只有 view 可视时，才拉视频流。以达到节省流量和降低性能开销的目的。

而实现这个功能，并不是一件容易的事情。在 v5.4.0 版本开始，SDK 内置了该功能，您只需要在 [startRemoteVideo](./TRTC.html#startRemoteVideo) 时，设置 `receiveWhenViewVisible` 及 `viewRoot` 参数，即可开启该功能，非常方便快捷。SDK 会自动判断您在 startRemoteVideo 时传入的 view 的可视状态，当 view 可视时拉流，当 view 不视时，停止拉流。

### 代码示例

假设您用于播放视频的 DOM 结构如下

```html
<div id='view-list'>
  <div id='view1_main'></div>
  <div id='view2_main'></div>
  <div id='view3_main'></div>
</div>
```

```js
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  trtc.startRemoteVideo({ 
    view: 'view1_main',
    userId, 
    streamType, 
    option: { 
      small: true,
      receiveWhenViewVisible: true, // 开启【只订阅可视区域的视频流】功能
      viewRoot:  document.getElementById('view-list') // 传入所有 view list 的第一级父级元素，用于计算 view 和 viewRoot 的参考关系，判断 view 是否处于可视状态。默认值是 document.body
    }
  });
})
```

### 增加 Loading 效果

开启该功能后，在用户滚动视频列表时：

- view 从可视变为不可视时，SDK 会停止拉流。
- view 从不可视变为可视时，SDK 会恢复拉流。

由于恢复拉流到视频播放成功，是异步的过程，在这个过程中会出现黑屏，一般为了更好的交互体验，建议您在这个异步过程中，给视频区域增加 Loading 的效果。您可以参考如下代码判断是否要展示 Loading。

```js
let isLoading = true;

// 监听视频播放状态，根据视频播放状态来判断是否显示 Loading 效果。
trtc.on(TRTC.EVENT.VIDEO_PLAY_STATE_CHANGED, ({ userId, streamType, state }) => {
  // '' 代表本地的视频播放状态，此处需要非本地视频流（远端）的播放状态。
  if (userId === '') return;

  // 远端视频播放成功，则停止 Loading 效果
  isLoading = state !== 'PLAYING';

  // 通过 userId 和 streamType 判断是哪个远端用户的主流/辅流 播放状态变更。
});
await trtc.startRemoteVideo({ ... });

// 停止远端视频后，停止远端 Loading 效果。
trtc.stopRemoteVideo()
isLoading = false;
```

## 常见问题

1. SDK 如何判断 view 是否处于可视状态。

    当 view 有任意像素点出现在 viewRoot 区域时，即认为 view 是可视的；同时当 view 所有像素点都不在 viewRoot 区域时，则认为 view 是不可视的。

2. 大小流需要考虑浏览器兼容性吗？

    虽然在部分浏览器下不支持开启大小流（例如 iOS 系统的所有浏览器、Chrome 63 以下版本），但是开启大小流是没有副作用的，如果环境不支持，SDK 只会使用大流，通话可以正常进行。您无需写额外的 if else 判断。

    您可以调用 {@link TRTC.isSupported TRTC.isSupported} 获取 checkResult.detail.isSmallStreamSupported 来判断当前环境是否支持开启大小流。
