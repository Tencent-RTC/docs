> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 简介

浏览器为了防止网页自动播放音视频对用户造成干扰，对音视频的自动播放功能做了限制：**在用户与网页产生交互（例如点击、触摸页面等）之前，网页将被禁止播放带有声音的媒体。在 iOS 10+ 中，如果开启了省电模式，[无声音的媒体也会被禁止播放](https://discussions.apple.com/thread/254541299?sortBy=best)。**

受上述浏览器自动播放策略影响，在使用 TRTC Web SDK 时，可能会出现播放无声的问题。最常见的场景是：您的产品设计中，支持用户刷新页面后，无需用户点击，可自动进房拉流播放。

本文主要介绍**如何解决因自动播放策略限制，导致播放失败**的问题。

## 方案一：使用 SDK 提供的自动播放弹窗

默认情况下，当出现自动播放失败时，SDK 会弹窗引导用户与页面产生交互。当产生交互后，SDK 会主动调用接口恢复播放。

- 优点：业务侧无需做任何处理，简单高效。

- 缺点：SDK 提供的弹窗不一定符合业务产品设计要求，此时可考虑使用方案二。

SDK 提供的弹窗适配桌面端和移动端浏览器，样式如下：

<div style="display:flex;align-items:center;">
  <img src="./assets/autoplay-dialog-pc-cn.png" style="width:40%;max-width:450px;margin-right:16px;" />
  <img src="./assets/autoplay-dialog-cn.png" style="width:30%;max-width:300px;" />
</div>

- SDK 会根据 [navigator.language](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/language) 的值来切换中英文弹窗提示。

- 当用户点击"确认"时，SDK 自动调用相关接口来恢复播放。

- 为尽可能保证弹窗处于最上层展示，其 z-index 值为 1500。

## 方案二：自定义处理自动播放限制

如果您希望完全控制自动播放失败时的用户体验，可以按照以下步骤来实现：

### 步骤 1：关闭 SDK 默认弹窗

在调用 [trtc.enterRoom](./TRTC.html#enterRoom) 接口时，将 `enableAutoPlayDialog` 参数设置为 `false`：

```javascript
await trtc.enterRoom({
  // ... 其他参数
  enableAutoPlayDialog: false
});
```

### 步骤 2：监听自动播放失败事件（必做）

监听 [AUTOPLAY_FAILED](./module-EVENT.html#.AUTOPLAY_FAILED) 事件来处理自动播放失败的情况。SDK 会自动监听用户的点击行为，在用户点击后自动恢复播放。

```javascript
trtc.on(TRTC.EVENT.AUTOPLAY_FAILED, event => {
  // 在此处添加您的自定义处理逻辑
  // 例如：显示自定义提示框，引导用户点击页面
});
```

### 步骤 3：主动恢复播放（可选）

> 从版本 5.9.0 开始支持

在某些特殊场景下（如在 iframe 中或使用特定前端框架时），您可能需要主动调用恢复播放方法。此时可以使用事件中提供的 `resume` 方法：

```javascript
trtc.on(TRTC.EVENT.AUTOPLAY_FAILED, (event) => {
  showCustomDialog(event);
});
function showCustomDialog(event) {
  const { userId, resume } = event;
  // 受限的用户 id 为 userId，resume 是恢复这个用户播放的函数
  // 需要显示一个自定义按钮，提示 userId 播放受限，在按钮点击回调里调用 'resume()'
}
```

### 重要提示

1. 某些浏览器可能在每次播放前都会检查用户交互状态，因此可能多次触发自动播放失败事件。

2. 在 iOS 微信浏览器和小程序 webview 中有特殊要求：
   - 首次播放远端流时，必须在用户点击事件的回调函数中直接调用播放接口
   - 不能使用 setTimeout 等异步方式调用播放接口，否则会导致播放失败

## Webview 中关闭自动播放策略限制

在 Android 和 iOS Webview 中的默认自动播放策略可能和浏览器中的不一致，您可以参考如下文档，在您的 App 中关闭自动播放限制。

- Android Webview 关闭自动播放限制：调用 [setMediaPlaybackRequiresUserGesture(false)](https://developer.android.com/reference/android/webkit/WebSettings#setMediaPlaybackRequiresUserGesture(boolean)) 关闭自动播放限制。
- iOS Webview 关闭自动播放限制：[设置 mediaTypesRequiringUserActionForPlayback 为 WKAudiovisualMediaTypeNone](https://developer.apple.com/documentation/webkit/wkwebviewconfiguration/1851524-mediatypesrequiringuseractionfor)