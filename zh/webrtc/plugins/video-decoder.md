> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍如何使用 TRTCVideoDecoder 插件来启动视频解码器降级能力。该插件支持常规解码失败后降级到软解码方式渲染视频。

## 前提条件

- TRTC Web SDK 版本 >= 5.9.0

- [联系我们](https://cloud.tencent.com/document/product/647/19906)开通解码器降级能力

## 实现流程

### 部署静态资源

该插件依赖一些静态资源文件，为保证浏览器可以正常加载和运行这些文件，你需要完成以下步骤：
  1. 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中。
  2. 在创建 trtc 实例时传入您的 CDN 地址给 assetsPath 参数，例如： `TRTC.create({ assetsPath: '' })`，SDK 会按需加载相关资源文件。
  > - 若您的版本低于 `v5.10.0`，您需要将 `node_modules/trtc-sdk-v5/plugins/video-decoder` 目录下的`videodec.wasm` 和 `videodec_simd.wasm` 发布至 CDN 中，并处于同一公共目录，例如: `xxx/assets`。
  > - 如果目录下文件的 Host URL 与网页应用的 Host URL 不一致，则需要开启访问文件域名的 CORS 策略。
  > - 不能把目录文件放在 HTTP 服务下，因为在 HTTPS 域名下加载 HTTP 资源会被浏览器安全策略禁止。

### 引入并注册插件

```javascript
import TRTCVideoDecoder from 'trtc-sdk-v5/plugins/video-decoder';

const trtc = TRTC.create({ plugins: [TRTCVideoDecoder], assetsPath: '' });
```

## 插件启动

插件功能将在 SDK 常规解码失败下自动启动降级逻辑，采用软解码方式解码。
