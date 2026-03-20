> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能简介

AI 降噪插件能够降低通话中的噪声，减少环境噪声对通话的影响。可以更好地抑制通话过程中的键盘声、碰撞声、敲击声等噪声，特别适用于对瞬时噪声敏感的场景，例如会议场景中的短时噪声。

本文将介绍在如何在开发 TRTC 应用中给本地音频流应用 AI 降噪的方法。[点击在线体验](https://web.sdk.qcloud.com/trtc/webrtc/demo/api-sample/improve-ai-denoiser.html) 降噪 demo 。

## 前提条件

自 2023 年 4 月 1 日起，使用 AI 降噪功能需开通 TRTC [包月套餐](https://cloud.tencent.com/document/product/647/85386) 尊享版及更高版本。

支持的浏览器：Chrome 66+, Edge 79+, Safari 14.1+, Firefox 76+，其中，安卓系统中 Chrome 版本需要在 92 以上。

为了更好的使用 AI 降噪，建议您使用最新版本的 Chrome 浏览器。

**注意：**

如果您麦克风采集的有背景音乐，降噪插件可能会将其当做噪音进行消除。

## 实现流程

#### 部署静态资源

该插件依赖一些静态资源文件，为保证浏览器可以正常加载和运行这些文件，你需要完成以下步骤：
  1. 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中。
  2. 在创建 trtc 实例时传入您的 CDN 地址给 assetsPath 参数，例如： `TRTC.create({ assetsPath: '' })`，SDK 会按需加载相关资源文件。
  > - 若您版本低于 `v5.10.0`，您需要将 `node_modules/trtc-sdk-v5/plugins/ai-denoiser/denoiser-wasm.js` 发布至 CDN 中，并处于同一公共目录，例如: `xxx/assets`。
  > - 如果目录下文件的 Host URL 与网页应用的 Host URL 不一致，则需要开启访问文件域名的 CORS 策略。
  > - 不能把目录文件放在 HTTP 服务下，因为在 HTTPS 域名下加载 HTTP 资源会被浏览器安全策略禁止。

#### 开启降噪

```javascript
const trtc = TRTC.create({ assetsPath: '' });

await trtc.startLocalAudio();
await trtc.startPlugin('AIDenoiser', {
  sdkAppId: 123456,
  userId: 'user_123',
  userSig: 'XXXXXXXX'
});
```

#### 更新降噪

```javascript
await trtc.updatePlugin('AIDenoiser', {
  mode: 1 // 开启远场消除模式
});
```

#### 关闭降噪
```javascript
await trtc.stopPlugin('AIDenoiser');
```

## API 说明

### trtc.startPlugin('AIDenoiser', options)

用于开启降噪

#### options:

| Name       | Type     | Required | Attributes                                                     | Description |
|------------|----------|---|----------------------------------------------------------------|-------------|
| sdkAppId   | `number` |true| 当前应用的 sdkAppId                                                 |
| userId     | `string` |true| 当前用户的 userId                                                   |
| userSig    | `string` |true| 当前用户的 userSig                                                  |
| assetsPath | `string` |false| denoiser-wasm.js 文件存放的位置。若您的版本低于 `v5.5.2`，你需通过该参数传入 assets 资源目录。|
| mode   | `number` |false| 当前降噪的模式                                                 | v5.13.0+ 开始，支持选择 AI 降噪的模式:<br /> - `0`: 默认值，适用于大部分通用场景 <br /> - `1`: 远场消除模式，针对 ASR 场景容易被打断、误识别等问题，消除远场人声，提高识别准确率|

### trtc.updatePlugin('AIDenoiser')

| Name       | Type     | Required | Attributes                                                     | Description |
|------------|----------|---|----------------------------------------------------------------|-------------|
| mode   | `number` |false| 当前降噪的模式                                                 | v5.13.0+ 开始，支持选择 AI 降噪的模式:<br /> - `0`: 默认值，适用于大部分通用场景 <br /> - `1`: 远场消除模式，针对 ASR 场景容易被打断、误识别等问题，消除远场人声，提高识别准确率|

用于更新降噪

### trtc.stopPlugin('AIDenoiser')

用于关闭降噪

