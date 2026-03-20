> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文档详细介绍了如何使用 TRTC Web SDK 的人脸检测插件，实时检测摄像头视频流中的人脸状态变化。该插件采用智能防抖逻辑，仅在人脸出现/消失状态真正变化时触发回调，避免因光线变化或短暂遮挡导致的误报。

## 前提条件

- TRTC Web SDK 版本 >= 5.15.0
- 需要先开启摄像头视频流

### 平台兼容性

| 平台 | 操作系统 | 浏览器版本 | 备注 |
|------|----------|------------|------|
| Web | Windows | Chrome 90+、Firefox 90+、Edge 97+ | 建议使用最新版 Chrome 浏览器（开启浏览器硬件加速） |
| Web | Mac | Chrome 98+、Firefox 96+、Safari 15+ | 2019年及以上 MacBook |
| Web | Android | 微信内嵌浏览器、Chrome、Firefox、小米浏览器| 建议使用微信内嵌、Chrome 或火狐浏览器等主流浏览器 |
| Web | iOS | 微信内嵌浏览器、Chrome、Safari、Firefox | 建议使用 Chrome 或 Safari 浏览器 |

## 实现流程

### 部署静态资源

该插件依赖一些静态资源文件，为保证浏览器可以正常加载和运行这些文件，你需要完成以下步骤：

1. 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中。
2. 在创建 trtc 实例时传入您的 CDN 地址给 assetsPath 参数，例如： `TRTC.create({ assetsPath: '' })`，SDK 会按需加载相关资源文件。

> - 若您的版本低于 `v5.10.0`，您需要下载解压 https://web.sdk.qcloud.com/trtc/webrtc/v5/assets/assets.zip 后，发布至 CDN 或者静态资源服务器中。
> - 如果目录下文件的 Host URL 与网页应用的 Host URL 不一致，则需要开启访问文件域名的 CORS 策略。
> - 不能把目录文件放在 HTTP 服务下，因为在 HTTPS 域名下加载 HTTP 资源会被浏览器安全策略禁止。

### 引入并注册插件

```javascript
import { FaceDetection } from 'trtc-sdk-v5/plugins/video-effect/face-detection';

const trtc = TRTC.create({ 
  plugins: [FaceDetection], 
  assetsPath: ''  // 替换为您的静态资源地址
});
```

### 开启本地摄像头

```javascript
await trtc.startLocalVideo();
```

### 开启人脸检测插件

```javascript
await trtc.startPlugin('FaceDetection', {
  onFaceDetectionStateChanged: (hasFace) => {
    if (hasFace) {
      console.log('检测到人脸出现');
      // 执行人脸出现时的业务逻辑
    } else {
      console.log('检测到人脸消失');
      // 执行人脸消失时的业务逻辑
    }
  },
  detectionInterval: 300,    // 检测间隔，单位ms (可选，默认300)
  minConfidence: 0.8,        // 最小置信度阈值 (可选，默认0.8)
  missingTimeout: 1000       // 人脸消失确认时间，单位ms (可选，默认1000)
});
```

### 按需更新参数

```javascript
// 更新检测参数 - 提高检测灵敏度
await trtc.updatePlugin('FaceDetection', {
  onFaceDetectionStateChanged: (hasFace) => {
    console.log('更新后的检测状态:', hasFace);
  },
  detectionInterval: 200,    // 缩短检测间隔，提高实时性
  minConfidence: 0.7,        // 降低置信度阈值，提高检测灵敏度
  missingTimeout: 1500       // 延长消失确认时间，减少误报
});
```

### 关闭人脸检测

```javascript
await trtc.stopPlugin('FaceDetection');
```

## 核心特性

### 智能防抖逻辑

插件采用**非对称防抖机制**，确保状态变化的准确性：

- **人脸出现（true）**：立即触发，实时响应
- **人脸消失（false）**：延迟触发，需满足 `missingTimeout` 条件才确认

该设计有效避免了因以下情况导致的误报：
- 人脸短暂转头或低头
- 光线瞬间变化
- 物体短暂遮挡

### 视频流状态同步

当本地视频流状态变化时，插件会自动处理：

| 场景 | 插件行为 | 回调触发 |
|------|----------|----------|
| 视频静音（Mute） | 暂停检测 | 触发一次 `hasFace: false` |
| 视频恢复（Unmute） | 恢复检测 | 根据实际状态触发相应回调 |

## API 说明

### trtc.startPlugin('FaceDetection', options)

用于开启人脸检测插件。

#### options

| Name | Type | Attributes | Description |
|------|------|------------|-------------|
| onFaceDetectionStateChanged | `(hasFace: boolean) => void` | 必填 | 人脸状态变化回调函数（仅在状态真正变化时触发） |
| detectionInterval | number | 选填 | 检测间隔时间(ms)，范围 100-5000，默认 300 |
| minConfidence | number | 选填 | 最小人脸置信度阈值，范围 0-1，默认 0.8 |
| missingTimeout | number | 选填 | 人脸消失确认时间(ms)，范围 0-5000，默认 1000 |

**Example:**

```javascript
await trtc.startPlugin('FaceDetection', {
  onFaceDetectionStateChanged: (hasFace) => {
    if (hasFace) {
      console.log('人脸出现');
    } else {
      console.log('人脸消失');
    }
  },
  detectionInterval: 300,
  minConfidence: 0.8,
  missingTimeout: 1000
});
```

### trtc.updatePlugin('FaceDetection', options)

可修改人脸检测参数。

#### options

| Name | Type | Attributes | Description |
|------|------|------------|-------------|
| onFaceDetectionStateChanged | `(hasFace: boolean) => void` | 选填 | 人脸状态变化回调函数 |
| detectionInterval | number | 选填 | 检测间隔时间(ms)，范围 100-5000 |
| minConfidence | number | 选填 | 最小人脸置信度阈值，范围 0-1 |
| missingTimeout | number | 选填 | 人脸消失确认时间(ms)，范围 0-5000 |

**Example:**

```javascript
await trtc.updatePlugin('FaceDetection', {
  detectionInterval: 200,
  minConfidence: 0.7,
  missingTimeout: 1500
});
```

### trtc.stopPlugin('FaceDetection')

关闭人脸检测。

## 常见问题

**1. 如何调整检测的灵敏度？**

可以通过以下参数组合调整：
- **提高灵敏度**：降低 `minConfidence`，缩短 `detectionInterval`
- **降低灵敏度**：提高 `minConfidence`，延长 `missingTimeout`

**2. 视频流恢复后需要重新开启检测吗？**

不需要。插件会自动处理视频流状态变化，恢复后会继续检测。