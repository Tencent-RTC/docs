> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文将介绍如何在通话过程中实现虚拟背景的功能。

| 原始摄像头                                                                                | 背景虚化                                                                                  | 背景图片                                                                                  |
| ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| <img src="" /> | <img src="" /> | <img src="" /> |

## 前提条件

- 需要使用虚拟背景功能的 sdkAppId 已开通 TRTC 旗舰版包月套餐。包月套餐相关说明请参见文档 [包月套餐计费说明](https://cloud.tencent.com/document/product/647/85386)
- TRTC Web SDK 版本 >= 5.2.0
- 各平台系统及配置要求如下表：

| 平台 | 操作系统 | 浏览器版本 | fps | 推荐配置 | 备注 |
| --- | --- | --- | --- | --- | --- |
| Web | Windows | Chrome 90+ Firefox 90+ Edge 97+ | 30 | 内存：16GB CPU：i5-10500 GPU：独显 2GB | 建议使用最新版 Chrome 浏览器  <b> （开启浏览器硬件加速）</b> |
| 15 | 内存：8GB CPU：i3-8300 GPU：intel 核显 1GB |  |  |  |  |
| Mac | Chrome 98+ Firefox 96+ Safari 15+ | 30 | 2019年 MacBook 内存：16GB(2667MHz) CPU：i7(6核 2.60GHz) GPU：AMD Radeon 5300M |  |  |

## 实现流程

### 部署静态资源

该插件依赖一些静态资源文件，为保证浏览器可以正常加载和运行这些文件，你需要完成以下步骤：
  1. 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中。
  2. 在创建 trtc 实例时传入您的 CDN 地址给 assetsPath 参数，例如： `TRTC.create({ assetsPath: '' })`，SDK 会按需加载相关资源文件。
  > - 若您的版本低于 `v5.10.0`，您需要下载解压 [assets.zip](https://web.sdk.qcloud.com/trtc/webrtc/v5/assets/assets.zip) 后，发布至 CDN 或者静态资源服务器中。
  > - 如果目录下文件的 Host URL 与网页应用的 Host URL 不一致，则需要开启访问文件域名的 CORS 策略。
  > - 不能把目录文件放在 HTTP 服务下，因为在 HTTPS 域名下加载 HTTP 资源会被浏览器安全策略禁止。
  3. 如果您使用 `v5.11.0`版本之后的**人脸居中**功能，请重新部署 assets。

### 检查浏览器是否支持(v5.8.1+)
```javascript
import { VirtualBackground } from 'trtc-sdk-v5/plugins/video-effect/virtual-background';
if (!VirtualBackground.isSupported()) {
    // 当前浏览器不支持该插件
}
```
### 引入并注册插件

```javascript
import { VirtualBackground } from 'trtc-sdk-v5/plugins/video-effect/virtual-background';

const trtc = TRTC.create({ plugins: [VirtualBackground], assetsPath: '' });
```

### 开启本地摄像头

```javascript
await trtc.startLocalVideo();
```

### 开启虚拟背景插件

```javascript
await trtc.startPlugin('VirtualBackground', {
  sdkAppId: 123123,
  userId: 'userId_123',
  userSig: 'your_userSig'
});
```

> 注意：开启本功能需要加载计算资源，如未处理请参考：[部署静态资源](#部署静态资源)

### 按需更新参数

```javascript
// 改为图片背景
await trtc.updatePlugin('VirtualBackground', {
  type: 'image',
  src: ''
});
```

### 关闭虚拟背景

```javascript
await trtc.stopPlugin('VirtualBackground');
```

## API 说明

### trtc.startPlugin('VirtualBackground', options)

用于开启虚拟背景

#### options

| Name      | Type            | Attributes             | Description                                                                                                                                                           |
| --------- | --------------- | ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sdkAppId  | `number`        | 必填                   | 当前应用 ID |
| userId    | `string`        | 必填                   | 当前用户 ID |
| userSig   | `string`        | 必填                   | 用户 ID 对应的 UserSig |
| type      | `string`        | 选填                   | - `image` 图片背景 <br> - `blur` 虚化背景（默认）|
| enableFaceCentering    | `boolean`        | 选填 | `false`（默认），(Since v5.11.0) ，设置为 true 开启人脸自动居中 |
| enableEffectOptimization    | `boolean`        | 选填 | `false`（默认），(Since v5.13.0)，设置为 true，可以减少人物边缘的黑边和锯齿，目前仅支持 PC 端，实验性功能 |
| blurLevel | `number`        | 选填 | - `blur` 虚化背景时可用，用于调节虚化程度，默认为 3，取值范围为 [1,10]|
| src       | `string`        | type 为 `image` 时必填 | 图片地址，如 ``|
| onAbort   | `(error) => {}` | 选填 | 运行过程中发生错误 <br> 推荐处理方法可参考文末常见问题 |

**Example:**

```javascript
await trtc.startPlugin('VirtualBackground', {
  sdkAppId: 123123,
  userId: 'userId_123',
  userSig: 'your_userSig',
  type: 'image',
  src: ''
});
```

### trtc.updatePlugin('VirtualBackground', options)

可修改虚拟背景参数

#### options

| Name      | Type     | Attributes             | Description                                                            |
| --------- | -------- | ---------------------- | ---------------------------------------------------------------------- |
| type      | `string` | 必填                   | - `image` 图片背景 <br> - `blur` 虚化背景                              |
| enableFaceCentering (Since v5.11.0)      | `boolean`        | 选填                   |`false`（默认）    
| blurLevel | `number` | 选填                   | - `blur` 虚化背景时可用，用于调节虚化程度，默认为 3，取值范围为 [1,10] |
| src       | `string` | type 为 `image` 时必填 | 图片地址，如 ``               |

**Example:**

```javascript
await trtc.updatePlugin('VirtualBackground', {
  type: 'blur',
  blurLevel: 6
});
```

### trtc.stopPlugin('VirtualBackground')

关闭虚拟背景

## 常见问题

**1. 在 Chrome 中运行 Demo 发现画面颠倒且卡顿**

本插件使用 GPU 进行加速，您需要在浏览器设置中找到使用硬件加速模式并启用。可以将 `chrome://settings/system` 复制到浏览器地址栏，并且打开硬件加速模式。

**2. 当设备性能不足中途停止**

可通过监听事件，手动终止或者重新启用。

```javascript
async function onAbort(error) {
  await trtc.stopPlugin('VirtualBackground'); // 关闭插件
  // 或者降低分辨率后重新启用
}

await trtc.startPlugin('VirtualBackground', {
  ...// 其他参数
  onAbort,
});
```
