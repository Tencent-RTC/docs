> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能简介

TRTC Web SDK v5 提供了插件系统，支持按需加载各种音视频增强能力。插件系统的优势：

- **模块化设计**：按需引入，减少主包体积
- **独立升级**：插件版本独立管理，便于迭代
- **统一接口**：所有插件使用相同的启动、更新、停止 API
- **灵活组合**：可同时使用多个插件，满足复杂业务场景

## 插件列表

### 视频增强类

| 插件名称 | 功能说明 | 版本要求 | 套餐要求 | 静态资源 | 文档链接 |
|---------|---------|---------|---------|---------|---------|
| **Beauty** | 专业美颜，支持磨皮、美白、瘦脸等 6 种参数 + 10 种 2D 特效贴纸 | ≥ 5.5.0 | 专业版 | - | {@tutorial 28-advanced-beauty} |
| **BasicBeauty** | 基础美颜，支持美颜、明亮、红润 3 种参数 | ≥ 5.7.0 | 无 | - | {@tutorial 38-advanced-basic-beauty} |
| **VirtualBackground** | 虚拟背景，支持背景虚化、图片背景、人脸居中 | ≥ 5.2.0 | 旗舰版 | 需要 | {@tutorial 36-advanced-virtual-background} |
| **Watermark** | 视频水印，支持添加图片水印 | ≥ 5.3.2 | 无 | - | {@tutorial 29-advanced-water-mark} |
| **VideoMixer** | 视频合流，将摄像头、屏幕、文字、图片、视频合成一路 | ≥ 5.12.0 | 无 | - | {@tutorial 40-advanced-video-mixer} |
| **FaceDetection** | 人脸检测，实时检测摄像头中的人脸状态变化 | ≥ 5.15.0 | 无 | 需要 | {@tutorial 43-advanced-face-detection} |

### 音频增强类

| 插件名称 | 功能说明 | 版本要求 | 套餐要求 | 静态资源 | 文档链接 |
|---------|---------|---------|---------|---------|---------|
| **AudioMixer** | 背景音乐，支持同时播放多个音乐，可控制音量、循环、跳转等 | ≥ 5.1.0 | 无 | - | {@tutorial 22-advanced-audio-mixer} |
| **AIDenoiser** | AI 降噪，智能降低环境噪声，支持远场消除模式 | - | 尊享版 | 需要 | {@tutorial 35-advanced-ai-denoiser} |
| **VoiceChanger** | 美声变声，支持 11 种变声效果（熊孩子、萝莉、大叔等） | ≥ 5.10.0 | 旗舰版 | - | {@tutorial 37-advanced-voice-changer} |
| **RealtimeTranscriber** | 实时语音转录与翻译，将用户语音实时转换为文字并支持多语言翻译 | ≥ 5.15.0 | 无 | - | {@tutorial 42-advanced-realtime-transcriber} |
| **Chorus** | 合唱，支持 KTV 合唱场景，包含主唱/伴唱角色、歌词同步、打分 | - | [联系我们](https://cloud.tencent.com/document/product/647/19906)开通 | - | - |

### 推流与混流类

| 插件名称 | 功能说明 | 版本要求 | 套餐要求 | 静态资源 | 文档链接 |
|---------|---------|---------|---------|---------|---------|
| **CDNStreaming** | CDN 转推，支持云端混流、转推 CDN、回推混流到 TRTC 房间 | ≥ 5.1.0 | 无 | - | {@tutorial 26-advanced-publish-cdn-stream} |
| **CrossRoom** | 跨房连麦，支持不同房间主播间的音视频通话 | ≥ 5.8.0 | 无 | - | {@tutorial 30-advanced-cross-room-link} |

### 功能扩展类

| 插件名称 | 功能说明 | 版本要求 | 套餐要求 | 静态资源 | 文档链接 |
|---------|---------|---------|---------|---------|---------|
| **DeviceDetector** | 设备检测，检测摄像头、麦克风、扬声器及网络，提供默认 UI | ≥ 5.8.0 | 无 | - | {@tutorial 23-advanced-support-detection} |
| **Debug** | 调试模式，支持全量日志上传/导出、音视频转储 | ≥ 5.8.0 | 无 | - | {@tutorial 18-basic-debug} |
| **TRTCVideoDecoder** | 视频解码器降级，解码失败时自动降级到软解码 | ≥ 5.9.0 | [联系我们](https://cloud.tencent.com/document/product/647/19906)开通 | 需要 | {@tutorial 39-advanced-video-decoder} |
| **SmallStreamAutoSwitcher** | 大小流自动切换，弱网时自动切换小流保证流畅度 | ≥ 5.11.0 | 无 | - | {@tutorial 41-advanced-small-stream-auto-switcher} |
<!-- | **CustomEncryption** | 自定义加密，支持音视频端到端加密，可自定义或使用内置加密算法 | - | 需联系我们开通 | - | - | -->

> **说明：**
> - **版本要求**：使用该插件所需的 TRTC SDK 最低版本
> - **套餐要求**：使用该插件需开通的 TRTC 包月套餐版本，详见 [包月套餐计费说明](https://cloud.tencent.com/document/product/647/85386)
> - **静态资源**：插件是否需要部署 `node_modules/trtc-sdk-v5/assets` 目录到 CDN

## 统一使用流程

### 1. 引入并注册插件

在创建 TRTC 实例时通过 `plugins` 数组注册需要使用的插件：

```javascript
import TRTC from 'trtc-sdk-v5';
import { VirtualBackground } from 'trtc-sdk-v5/plugins/video-effect/virtual-background';
import { AIDenoiser } from 'trtc-sdk-v5/plugins/ai-denoiser';

const trtc = TRTC.create({ 
  plugins: [VirtualBackground, AIDenoiser],
  assetsPath: 'https://your-cdn/assets' // 如果插件需要静态资源
});
```

### 2. 开启插件

使用 `trtc.startPlugin(pluginName, options)` 开启插件：

```javascript
// 不需要用户认证的插件
await trtc.startPlugin('BasicBeauty', {
  beauty: 0.5,
  brightness: 0.5,
  ruddy: 0.5
});

// 需要用户认证的插件
await trtc.startPlugin('VirtualBackground', {
  sdkAppId: 123456,
  userId: 'user_123',
  userSig: 'XXXXXXXX',
  type: 'blur',
  blurLevel: 3
});
```

### 3. 更新参数（可选）

使用 `trtc.updatePlugin(pluginName, options)` 动态更新插件参数：

```javascript
await trtc.updatePlugin('BasicBeauty', {
  beauty: 0.8,
  brightness: 0.6
});
```

### 4. 停止插件

使用 `trtc.stopPlugin(pluginName)` 停止插件：

```javascript
await trtc.stopPlugin('BasicBeauty');
```

## 特殊用法说明

### AudioMixer（背景音乐）- 按 ID 分组

AudioMixer 通过唯一的 `id` 标识每个背景音乐，每个 id 都有独立的生命周期：

```javascript
// 开启第一个背景音乐
await trtc.startPlugin('AudioMixer', {
  id: 'bgm1',
  url: './music1.mp3',
  loop: true,
  volume: 0.5
});

// 同时开启第二个背景音乐（不同 id）
await trtc.startPlugin('AudioMixer', {
  id: 'bgm2',
  url: './music2.mp3',
  volume: 0.3
});

// 更新第一个背景音乐
await trtc.updatePlugin('AudioMixer', {
  id: 'bgm1',
  volume: 0.8,
  operation: 'pause' // pause | resume
});

// 停止第一个背景音乐
await trtc.stopPlugin('AudioMixer', {
  id: 'bgm1'
});
```

**特点：**
- 按 id 分组，每个 id 独立管理一个背景音乐
- 支持同时开启多个背景音乐（不同 id）
- 同一个 id 不能连续 start 两次，需先 stop
- 支持暂停、恢复、跳转等操作

### CDNStreaming（CDN 转推）- 按 Mode 分组

CDNStreaming 按 `publishMode` 分组，每个模式都有独立的生命周期。支持四种推流模式：

```javascript
import { PublishMode } from 'trtc-sdk-v5/plugins/cdn-streaming';

// 模式 1：转推摄像头流
await trtc.startPlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
    streamId: 'stream001'
  }
});

// 模式 2：转推屏幕分享流（可与模式 1 同时进行）
await trtc.startPlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishSubStreamToCDN,
    streamId: 'stream002'
  }
});

// 模式 3：转推混流
await trtc.startPlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishMixStreamToCDN,
    streamId: 'mix-stream'
  },
  encoding: { videoWidth: 1280, videoHeight: 720 },
  mix: {
    audioMixUserList: [{ userId: 'user_123' }],
    videoLayoutList: [/* 布局参数 */]
  }
});

// 模式 4：回推混流到 TRTC 房间
await trtc.startPlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishMixStreamToRoom,
    robotUser: { userId: 'robot_001', roomId: 1234 }
  },
  encoding: { videoWidth: 1280, videoHeight: 720 },
  mix: { /* 混流参数 */ }
});

// 更新模式 1 的参数
await trtc.updatePlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
    streamId: 'stream001_new'
  }
});

// 停止模式 1（只需传 publishMode）
await trtc.stopPlugin('CDNStreaming', {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN
  }
});
```

**特点：**
- 按 publishMode 分组，每个模式独立管理
- 四种模式可以同时运行（不同 publishMode）
- 同一个 publishMode 遵循 start-update-stop 生命周期
- 停止时只需传入 publishMode 参数

### VideoMixer（视频合流）- 多输入源管理

VideoMixer 插件支持动态管理多个输入源，通过 `id` 标识每个输入源：

```javascript
// 开启合流，返回合流后的 track
const { track } = await trtc.startPlugin('VideoMixer', {
  canvasInfo: { width: 1920, height: 1080 },
  camera: [{ id: 'camera1', layout: { x: 0, y: 0, width: 640, height: 480, zIndex: 1 } }]
});

// 动态添加屏幕共享
await trtc.updatePlugin('VideoMixer', {
  screen: [{ id: 'screen1', layout: { x: 640, y: 0, width: 1280, height: 1080, zIndex: 0 } }]
});

// 移除摄像头（传空数组）
await trtc.updatePlugin('VideoMixer', {
  camera: []
});

// 使用合流后的 track 推流
await trtc.startLocalVideo({ 
  option: { 
    videoTrack: track,
    profile: { width: 1920, height: 1080, bitrate: 2000 }
  }
});
```

**特点：**
- 支持多次 start/update
- 返回 `track` 用于自定义推流
- 通过数组管理多个输入源（camera、screen、text、image、video）
- 注意：仅支持 PC 端浏览器

### TRTCVideoDecoder（视频解码）- 自动启动

TRTCVideoDecoder 插件注册后会自动在解码失败时启动降级逻辑，无需手动调用 `startPlugin`：

```javascript
import TRTCVideoDecoder from 'trtc-sdk-v5/plugins/video-decoder';

const trtc = TRTC.create({ 
  plugins: [TRTCVideoDecoder],
  assetsPath: 'https://your-cdn/assets'
});

// 无需手动 startPlugin，插件会在解码失败时自动启动
```

### SmallStreamAutoSwitcher（大小流切换）- 无参数更新

SmallStreamAutoSwitcher 启动后自动运行，不支持 `updatePlugin`：

```javascript
// 使用默认策略
trtc.startPlugin('SmallStreamAutoSwitcher');

// 自定义策略（仅在 start 时设置）
trtc.startPlugin('SmallStreamAutoSwitcher', {
  maxAutoSwitchToSmallCount: 1, // 只允许切换 1 次
  poorDuration: 5000,            // 网络差持续 5 秒才切换
  goodDuration: 30000            // 网络好持续 30 秒才切回
});
```

**特点：**
- 不支持 `updatePlugin`
- 自动根据网络状况切换大小流
- 注意：需配合大小流功能使用

### FaceDetection（人脸检测）- 回调驱动

FaceDetection 通过回调函数获取检测结果，采用智能防抖逻辑：

```javascript
await trtc.startPlugin('FaceDetection', {
  onFaceDetectionStateChanged: (hasFace) => {
    if (hasFace) {
      console.log('检测到人脸出现');
    } else {
      console.log('检测到人脸消失'); // 延迟触发，避免误报
    }
  },
  detectionInterval: 300,    // 检测间隔（ms）
  minConfidence: 0.8,        // 最小置信度
  missingTimeout: 1000       // 人脸消失确认时间（ms）
});
```

**特点：**
- 智能防抖：人脸出现立即触发，消失延迟确认
- 自动同步视频流状态（Mute/Unmute）
- 注意：需先开启摄像头

### DeviceDetector（设备检测）- 返回检测结果

DeviceDetector 提供默认 UI，检测完成后返回设备和网络检测结果：

```javascript
import { DeviceDetector } from 'trtc-sdk-v5/plugins/device-detector';

const trtc = TRTC.create({ plugins: [DeviceDetector] });

// 仅检测设备
const result = await trtc.startPlugin('DeviceDetector');

// 检测设备 + 网络
const resultWithNetwork = await trtc.startPlugin('DeviceDetector', {
  networkDetect: { sdkAppId, userId, userSig }
});

// 返回结果示例
// result.camera: { isSuccess: boolean, device: {...} }
// result.microphone: { isSuccess: boolean, device: {...} }
// result.speaker: { isSuccess: boolean, device: {...} }
// result.network: { isSuccess: boolean, result: { quality, rtt } }
```

**特点：**
- 提供默认的检测 UI，支持中英文
- 返回检测结果，可用于后续逻辑判断
- 支持仅检测设备或同时检测网络

### Debug（调试模式）- 自动启动

Debug 插件用于日志上传/导出和音视频转储，v5.8.4 起已内置 SDK：

```javascript
// v5.8.4+：无需引入插件，URL 添加 ?trtcDebug=1 自动开启弹窗

// v5.8.3 及以下：需引入插件
import { Debug } from 'trtc-sdk-v5/plugins/debug';
const trtc = TRTC.create({ plugins: [Debug] });
// 开发环境自动开启，部署环境需 URL 添加 ?trtcDebug=1

// 关闭调试模式
trtc.stopPlugin('Debug');
```

**特点：**
- v5.8.4+ 内置 SDK，无需外部引入
- 开发环境自动开启弹窗
- 支持日志上传/导出、音视频转储

## 静态资源部署

部分插件需要部署静态资源文件（WebAssembly、模型文件等），操作步骤：

### 1. 发布资源文件

将 `node_modules/trtc-sdk-v5/assets` 目录发布到 CDN 或静态资源服务器：

```bash
# 示例：复制到 public 目录
cp -r node_modules/trtc-sdk-v5/assets ./public/assets

# 或上传到 CDN
# https://your-cdn.com/assets/
```

### 2. 配置 assetsPath

创建 TRTC 实例时传入资源路径：

```javascript
const trtc = TRTC.create({ 
  plugins: [VirtualBackground, AIDenoiser],
  assetsPath: 'https://your-cdn.com/assets'
});
```

### 3. 注意事项

- 如果资源文件域名与应用域名不一致，需开启 CORS 策略
- 资源文件必须部署在 HTTPS 服务下（HTTP 会被浏览器安全策略禁止）
- SDK 版本低于 v5.10.0 可能需要额外部署特定文件（参考各插件文档）

## 相关链接

- [TRTC Web SDK API 文档](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/index.html)
- [包月套餐计费说明](https://cloud.tencent.com/document/product/647/85386)
- [在线 Demo 体验](https://web.sdk.qcloud.com/trtc/webrtc/demo/api-sample/index.html)
