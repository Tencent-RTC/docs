> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要描述如何使用 CDNStreaming 插件，实现云端混流、转推 CDN、回推混流到 TRTC 房间的功能。

## 前提条件

1. [TRTC SDK](https://www.npmjs.com/package/trtc-sdk-v5) 版本需要 >= 5.1。

2. 开通腾讯 [云直播](https://console.cloud.tencent.com/live) 服务。应国家相关部门的要求，直播播放必须配置播放域名，具体操作请参考 [域名管理](https://console.cloud.tencent.com/live/livestat)。

3. 在 [实时音视频控制台](https://console.cloud.tencent.com/trtc) 左侧导航栏选择【应用管理】，单击目标应用所在行的【配置】，打开【启用旁路推流】的开关。

    - "全局自动旁路"模式下，TRTC 所有上行音视频流都会被自动转推到腾讯云 CDN。
    - "指定流旁路"模式下，您可以通过该插件或者 [服务端 REST API](https://cloud.tencent.com/document/product/647/84721) 将指定音视频流推送到 CDN 且指定播放地址。

    <div align="center">
    <img src="assets/relayed-push.png" />
    </div>

## 快速开始

### 安装与注册

```js
import TRTC from 'trtc-sdk-v5';
import { CDNStreaming, PublishMode } from 'trtc-sdk-v5/plugins/cdn-streaming';

const trtc = TRTC.create({ plugins: [CDNStreaming] });
```

### 插件模式概览

本插件提供四种推流转码模式：

| 模式 | PublishMode | 说明 |
|------|-------------|------|
| A | `PublishMainStreamToCDN` | 转推摄像头麦克风流到 CDN |
| B | `PublishSubStreamToCDN` | 转推屏幕分享流到 CDN |
| C | `PublishMixStreamToCDN` | 混合多路流并转推到 CDN |
| D | `PublishMixStreamToRoom` | 混合多路流并回推到 TRTC 房间 |

对于每一种模式，均满足以下生命周期：

- `trtc.startPlugin('CDNStreaming', options)` - 启动插件
- `trtc.updatePlugin('CDNStreaming', options)` - 更新参数（0 或 N 次）
- `trtc.stopPlugin('CDNStreaming', options)` - 停止插件

详细的 `options` 参数可见 [CDNStreamingOptions](./global.html#CDNStreamingOptions)。

## 转推单流到 CDN

将摄像头流（模式 A）或屏幕分享流（模式 B）转推到腾讯云 CDN。

### 步骤 1：选择旁路推流方式

在 [实时音视频控制台](https://console.cloud.tencent.com/trtc) 选择旁路推流方式：

**全局自动旁路模式：**

TRTC 房间里的摄像头流、屏幕分享流将**自动**被推流到腾讯云 CDN，播放地址格式如下：

`http://[播放域名]/live/[streamId].flv`

- `[播放域名]`：云直播中配置的播放域名，查看已有的域名请参考 [域名管理](https://console.cloud.tencent.com/live/livestat)
- 摄像头流的 `[streamId]` 默认值为 `${sdkAppId}_${roomId}_${userId}_main`
- 屏幕分享流的 `[streamId]` 默认值为 `${sdkAppId}_${roomId}_${userId}_aux`
- 当 roomId 或 userId 包含特殊字符时，streamId 使用 MD5：`${sdkAppId}_${md5(${roomId}_${userId}_main)}`

**手动旁路模式：**

房间里的流**不会**被自动推流到腾讯云 CDN，请继续步骤 2 手动开启推流。

### 步骤 2：开启转推

```javascript
// 开启摄像头流转推
const options = {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN
  }
}
try {
  await trtc.startPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming start failed', error);
}
```

播放地址：`http://[播放域名]/live/${sdkAppId}_${roomId}_${userId}_main.flv`

### 步骤 3：修改 StreamId（可选）

```javascript
// 自定义播放地址
const options = {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
    streamId: 'stream001'
  }
}
try {
  await trtc.updatePlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming update failed', error);
}
```

新播放地址：`http://[播放域名]/live/stream001.flv`

### 步骤 4：停止转推

```javascript
const options = {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
  }
}
try {
  await trtc.stopPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming stop failed', error);
}
```

可通过 [流管理界面](https://console.cloud.tencent.com/live/streammanage) 查看目前的流状态。

## 转推混流到 CDN

将房间内多路音视频流混合为一路并转推到 CDN。

其他端实现方式可参考 [云端混流转码](https://cloud.tencent.com/document/product/647/16827)。

### 工作原理

本插件会向腾讯云的转码服务器发送一条指令，将房间里的多路音视频流混合为一路。您可以通过以下参数自定义：
- `encoding`：输出流质量（分辨率、码率、帧率）
- `mix`：混流布局配置

<img src="" style="width: 1000px">

### 使用流程示例

1. 调用 `startPlugin`，混流布局如图 A 所示
2. 调用 `updatePlugin` 新增屏幕分享，混流布局如图 B 所示
3. 不需要混流时调用 `stopPlugin`

<img src="" style="width: 600px">

### 配置说明

1. 将 `target.publishMode` 设置为 `PublishMode.PublishMixStreamToCDN`

2. 配置 `encoding` 参数设置输出质量：videoWidth、videoHeight、videoBitrate、videoFramerate、videoGOP、audioSampleRate、audioBitrate、audioChannels

3. 配置 `mix.backgroundColor` 和 `mix.backgroundImage` 设置背景
   > 背景图需要在 "[实时音视频控制台](https://console.cloud.tencent.com/trtc) > 应用管理 > 功能配置 > 素材管理" 中上传。上传成功后获取"图片ID"，将其转换为字符串设置到 backgroundImage（如 "63"）。

4. 配置 `mix.audioMixUserList` 设置音频混流（为空时默认混流全部用户音频）

5. 配置 `mix.videoLayoutList` 设置视频布局

> 坐标系说明：原点 (0, 0) 位于输出画布的**左上角**，单位为像素。locationX/locationY 是每路流的左上角位置；width/height 是布局区域大小。坐标和尺寸与 encoding.videoWidth/videoHeight 相关联——如果更改分辨率，需要相应地缩放布局参数以避免错位。
> 完整参数详情请参考 [CDNStreamingOptions](./global.html#CDNStreamingOptions)。

### 开启混流

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToCDN,
    streamId: 'mix-stream',
  },
  encoding: {
    videoWidth: 1280,
    videoHeight: 720,
    videoBitrate: 1500,
    videoFramerate: 15,
  },
  mix: {
    audioMixUserList: [
      { userId: 'current_user_id' }
    ],
    videoLayoutList: [
      {
        fixedVideoUser: { userId: 'current_user_id' },
        width: 640,
        height: 480,
        locationX: 0,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_MAIN,
        zOrder: 1
      },
    ]
  }
};
try {
  await trtc.startPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming start failed', error);
}
```

播放地址：`http://[播放域名]/live/mix-stream.flv`

### 更新布局

```javascript
// 添加屏幕分享到混流
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToCDN,
    streamId: 'mix-stream',
  },
  encoding: {
    videoWidth: 1280,
    videoHeight: 720,
    videoBitrate: 1500,
    videoFramerate: 15,
  },
  mix: {
    audioMixUserList: [
      { userId: 'current_user_id' }
    ],
    videoLayoutList: [
      {
        fixedVideoUser: { userId: 'current_user_id' },
        width: 640,
        height: 480,
        locationX: 0,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_MAIN,
        zOrder: 1
      },
      {
        fixedVideoUser: { userId: 'current_user_id' },
        width: 640,
        height: 480,
        locationX: 640,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_SUB,
        zOrder: 1
      },
    ]
  }
}
try {
  await trtc.updatePlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming update failed', error);
}
```

### 停止混流

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToCDN,
  }
}
try {
  await trtc.stopPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming stop failed', error);
}
```

## 推流到指定 CDN

将流推送到任意云服务商的 CDN。

### 步骤 1：获取 CDN 推流地址

在任意云服务商获取 CDN 推流地址 url。

### 步骤 2：获取 bizId 和 appId

在 [实时音视频控制台](https://console.cloud.tencent.com/trtc)【应用管理-配置-旁路转推配置】查看：
- 默认推流域名的前 6 个数字是 `bizId`
- 应用的 SDKAppId 是 `appId`

<div align="center">
  <img src="assets/stream-manager.png" style="width: 600px">
</div>

### 步骤 3：开始推流

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
    appId: 0,    // 你的 appId
    bizId: 0,    // 你的 bizId
    url: ''      // 你的 CDN 推流地址
  }
};
try {
  await trtc.startPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming start customCDN failed', error);
}
```

### 停止推流

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMainStreamToCDN,
  }
}
try {
  await trtc.stopPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming stop customCDN failed ', error);
}
```

## 回推混流到 TRTC 房间

将混流以机器人身份推送到指定 TRTC 房间。

> **注意：**
> - 此功能会自动在当前房间进入一个 userId 为 `mcu_robot_${roomId}_${userId}` 的拉流机器人，房间内其他 userId 要避免相同导致互踢
> - 此功能混流会有转码费用
> - audioMixUserList 为空时默认混所有人，若不需要混流可传入不存在的 userId
> - 完整参数详情请参考 [CDNStreamingOptions](./global.html#CDNStreamingOptions)

### 开启回推

```javascript
// 3333 房间的用户 user_123 发起该任务
// 混流将以 push_back_user_321 机器人身份发布到 4444 房间
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToRoom,
    robotUser: {
      userId: 'push_back_user_321', // 建议动态生成
      roomId: 4444,
    }
  },
  encoding: {
    videoWidth: 1280,
    videoHeight: 720,
    videoBitrate: 1500,
    videoFramerate: 15,
  },
  mix: {
    audioMixUserList: [
      { userId: 'user_123', roomId: 3333 }
    ],
    videoLayoutList: [
      {
        fixedVideoUser: { userId: 'user_123', roomId: 3333 },
        width: 640,
        height: 480,
        locationX: 0,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_MAIN,
        zOrder: 1
      },
      {
        fixedVideoUser: { userId: 'user_123', roomId: 3333 },
        width: 640,
        height: 480,
        locationX: 640,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_SUB,
        zOrder: 1
      },
    ]
  }
};
try {
  await trtc.startPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming start push stream to room failed', error);
}
```

### 更新布局

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToRoom
  },
  mix: {
    videoLayoutList: [
      {
        fixedVideoUser: { userId: 'user_123', roomId: 3333 },
        width: 640,
        height: 480,
        locationX: 640,
        locationY: 0,
        fixedVideoStreamType: TRTC.TYPE.STREAM_TYPE_MAIN,
        zOrder: 1
      }
    ]
  }
};
try {
  await trtc.updatePlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming update push stream to room failed', error);
}
```

### 停止回推

```javascript
let options = {
  target: {
    publishMode: PublishMode.PublishMixStreamToRoom,
  }
}
try {
  await trtc.stopPlugin('CDNStreaming', options);
} catch (error) {
  console.error('CDNStreaming stop push stream to room failed ', error);
}
```

## API 说明

对于 `startPlugin('CDNStreaming', options)` 与 `updatePlugin('CDNStreaming', options)`，参数类型可见 [CDNStreamingOptions](./global.html#CDNStreamingOptions)。

对于 `stopPlugin('CDNStreaming', options)`，参数只需要填入 `options.target.publishMode` 字段即可。

## 注意事项

1. 录制混合后的音频流，请参考 [云端录制](https://cloud.tencent.com/document/product/647/50768#3158718a-df0d-4590-b3d4-a4796d4c6561)。

2. 云端混流转码需要对输入 MCU 集群的音视频流进行解码后重新编码输出，将产生额外的服务成本，因此 TRTC 将向使用 MCU 集群进行云端混流转码的用户收取额外的增值费用。云端混流转码费用根据**转码输出的分辨率大小**和**转码时长**进行计费，转码输出的分辨率越高、转码输出的时间越长，费用越高。详情请参见 [云端混流转码计费说明](https://cloud.tencent.com/document/product/647/49446)。

3. 音视频时长计费说明，参考 [计费说明](https://cloud.tencent.com/document/product/647/44248)。

4. 退出房间后不会继续计费，但在重新进房时会自动继续开启，若想直接终止转推 CDN 和混流，请执行 stopPlugin 对应的 publishMode。

## 常见问题

Q. 无法停止推流？

请检查是否在控制台开启了"全局自动旁路"，该模式下无法通过接口停止。

Q. 如何查看目前所有的流？

可通过 [流管理界面](https://console.cloud.tencent.com/live/streammanage) 查看目前的流状态。

Q. 屏幕分享模糊？

SDK 默认使用 `1080p` 参数配置来采集屏幕分享，具体参考接口：{@link TRTC#startScreenShare startScreenShare}
