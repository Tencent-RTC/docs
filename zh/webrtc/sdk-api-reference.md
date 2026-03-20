> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

# TRTC

TRTC对象 通过 [TRTC.create()](#create) 创建，提供实时音视频的核心能力：

- 进入一个音视频房间 [enterRoom()](#enterroom)
- 退出当前音视频房间 [exitRoom()](#exitroom)
- 预览/发布 本地视频 [startLocalVideo()](#startlocalvideo)
- 采集/发布 本地音频 [startLocalAudio()](#startlocalaudio)
- 取消预览/发布 本地视频 [stopLocalVideo()](#stoplocalvideo)
- 取消采集/发布 本地音频 [stopLocalAudio()](#stoplocalaudio)
- 观看远端视频 [startRemoteVideo()](#startremotevideo)
- 取消观看远端视频 [stopRemoteVideo()](#stopremotevideo)
- 静默/取消静默 远端音频 [muteRemoteAudio()](#muteremoteaudio)

TRTC 生命周期如图所示：

![TRTC生命周期](./assets/client-life-cycle.png)

## Methods

### create()

创建一个 TRTC 对象，用于实现进房、预览、推流、拉流等功能。

**注意：**

- 您必须先创建 TRTC 对象，通过调用此对象方法和监听此对象事件才能实现业务所需要的各种功能。

#### 示例

```javascript
// 创建trtc对象
const trtc = TRTC.create();
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| options.plugins | Array |  | 注册插件的列表（选填）。 |
| options.enableSEI | boolean | false | 是否开启 SEI 收发功能（选填）。[参考文档](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/TRTC.html#sendSEIMessage) |
| options.assetsPath | string |  | 插件依赖静态资源文件的地址（选填）。<br>- 将 node_modules/trtc-sdk-v5/assets 目录发布至 CDN 或者静态资源服务器中。<br>- 设置 assetsPath 参数为 CDN 地址，例如： `TRTC.create({ assetsPath: '' })`，SDK 会按需加载相关资源文件。 |
| options.userDefineRecordId | string |  | 用于设置云端录制的 userDefineRecordId(选填）。<br>- 【推荐取值】限制长度为64字节，只允许包含大小写英文字母（a-zA-Z）、数字（0-9）及下划线和连词符。<br>- 【参考文档】[云端录制](https://cloud.tencent.com/document/product/647/16823)。 |

#### 返回值

trtc对象

**Type:** [TRTC](#)

### enterRoom(options)

进入一个音视频通话房间（以下简称"进房"）。

- 进房代表开始一个音视频通话会话，只有进房成功后才能和房间内的其他用户进行音视频通话。
- 可以通过 [startLocalVideo()](#startlocalvideo) 和 [startLocalAudio()](#startlocalaudio)发布本地音视频流，发布成功后，房间内其他用户会收到 [REMOTE_AUDIO_AVAILABLE](module-EVENT.html#.REMOTE_AUDIO_AVAILABLE) 和 [REMOTE_VIDEO_AVAILABLE](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件通知。
- 默认情况下 SDK 会自动播放远端音频，您需要在调用 [startRemoteVideo()](#startremotevideo) 来播放远端视频画面。

#### 示例

```javascript
const trtc = TRTC.create();
await trtc.enterRoom({ roomId: 8888, sdkAppId, userId, userSig });
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| options | object | **required** 进房参数 |

**options 属性：**

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| sdkAppId | number |  | **required** sdkAppId <br>在 [实时音视频控制台](https://console.cloud.tencent.com/trtc) 单击 **应用管理** > **创建应用** 创建新应用之后，即可在 **应用信息** 中获取 sdkAppId 信息。 |
| userId | string |  | **required** 用户ID <br>建议限制长度为32字节，只允许包含大小写英文字母(a-zA-Z)、数字(0-9)及下划线和连词符。 |
| userSig | string |  | **required** userSig 签名 <br>计算 userSig 的方式请参考 [UserSig 相关](https://cloud.tencent.com/document/product/647/17275)。 |
| roomId | number |  | 数字类型的房间号，取值要求为 [1, 4294967294] 的整数;<br><font color="red">如果需要使用字符串类型的房间号请使用 strRoomId 参数，roomId 和 strRoomId 必须填一个。若两者都填，则优先选择 roomId。</font> |
| strRoomId | string |  | 字符串类型的房间号，限制长度为64字节，且仅支持以下范围的字符集：<br>- 大小写英文字母（a-zA-Z）<br>- 数字（0-9）<br>- 空格 ! # $ % & ( ) + - : ; < = . > ? @ [ ] ^ _ { } | ~ ,<br><font color="red">注意：建议采用数字类型的 roomId，字符串类型的房间号 "123" 与 数字类型的房间号 123 不互通。</font> |
| scene | string |  | 应用场景，目前支持以下两种场景：<br>- [TRTC.TYPE.SCENE_RTC](module-TYPE.html#.SCENE_RTC)（默认）实时通话场景，该模式适合 1对1 的音视频通话，或者参会人数在 300 人以内的在线会议。[支持最大50人同时开麦](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-04-info-uplink-limits.html)。<br>- [TRTC.TYPE.SCENE_LIVE](module-TYPE.html#.SCENE_LIVE) 互动直播场景，该模式适合十万人以内的在线直播场景，但需要您在接下来介绍的 options 参数中指定 角色(role) 这个字段 |
| role | string |  | 用户角色，仅在 [TRTC.TYPE.SCENE_LIVE](module-TYPE.html#.SCENE_LIVE) 场景下有意义，[TRTC.TYPE.SCENE_RTC](module-TYPE.html#.SCENE_RTC) 场景无需指定 role，目前支持两种角色：<br>- [TRTC.TYPE.ROLE_ANCHOR](module-TYPE.html#.ROLE_ANCHOR)（默认） 主播<br>- [TRTC.TYPE.ROLE_AUDIENCE](module-TYPE.html#.ROLE_AUDIENCE) 观众<br>注意：观众角色没有发布本地音视频的权限，只有收看远端流的权限。如果观众想要连麦跟主播互动，请先通过 [switchRole()](#switchrole) 切换角色到主播后再发布本地音视频。 |
| autoReceiveAudio | boolean | true | 是否自动接收音频。当远端用户发布音频后，SDK 自动播放远端用户的音频。 |
| autoReceiveVideo | boolean | false | 是否自动接收视频。当远端用户发布视频后，SDK 自动拉流并解码远端视频，您需要调用 [startRemoteVideo](#startremotevideo) 播放远端视频。<br>- 自 v5.6.0 版本开始，该参数默认值变更为 `false`，参考：[v5.6.0 的 Breaking Changed](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-00-info-update-guideline.html) |
| enableAutoPlayDialog | boolean |  | 是否开启 SDK 自动播放失败弹窗，默认：true。<br>- 默认开启，当出现自动播放失败时，SDK 会弹窗引导用户点击页面，来恢复音视频播放。<br>- 可设置为 false 关闭，建议接入侧参考[自动播放受限处理建议](tutorial-21-advanced-auto-play-policy.html)来处理自动播放失败相关问题。 |
| proxy | string \| [ProxyServer](global.html#ProxyServer) |  | 设置代理服务器。参考最佳实践：[应对防火墙受限](tutorial-34-advanced-proxy.html)。 |
| privateMapKey | string |  | 进房钥匙，若需要权限控制请携带该参数（传空或传错会导致进房失败）。<br>[privateMapKey 权限设置](https://cloud.tencent.com/document/product/647/32240) |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [ENV_NOT_SUPPORTED](module-ERROR_CODE.html#.ENV_NOT_SUPPORTED)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### exitRoom()

退出当前音视频通话房间。

- 退房后将会关闭和远端用户的连接，不再接收和播放远端用户音视频，并且停止本地音视频的发布。
- 本地摄像头和麦克风的采集和预览不会因此而停止。您可以调用 [stopLocalVideo()](#stoplocalvideo) 和 [stopLocalAudio()](#stoplocalaudio) 停止本地音视频采集。

#### 示例

```javascript
await trtc.exitRoom();
```

#### 抛出错误

[OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### switchRoom(options)

为观众切换直播房间。

- 在直播场景中切换观众正在观看的房间。这比退出旧房间再进入新房间更快，可以优化直播等场景的打开时间。
- [联系我们](https://cloud.tencent.com/document/product/647/19906) 以启用此 API。

#### 示例

```javascript
const trtc = TRTC.create();
await trtc.enterRoom({
   ...
   roomId: 8888,
   scene: TRTC.TYPE.SCENE_LIVE,
   role: TRTC.TYPE.ROLE_AUDIENCE
});
await trtc.switchRoom({
   userSig,
   roomId: 9999
});
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| options | object | **required** 切换房间的参数 |

**options 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| userSig | string | **required** UserSig 签名 <br>计算 userSig 的方式请参考 [UserSig 相关](https://trtc.io/document/35166) <br /> |
| roomId | number | 数字类型的房间号，取值要求为 [1, 4294967294] 的整数;<br><font color="red">注意: <br>1. 要切换的房间和当前房间需为相同房间类型（同为数字房间或字符串房间）<br> 2. roomId 和 strRoomId 必须填一个。若两者都填，则优先选择 roomId。</font> |
| strRoomId | string | 字符串类型的房间号，限制长度为64字节，且仅支持以下范围的字符集：<br>- 大小写英文字母（a-zA-Z）<br>- 数字（0-9）<br>- 空格 ! # $ % & ( ) + - : ; < = . > ? @ [ ] ^ _ { } | ~ , |
| privateMapKey | boolean | 进房钥匙，若需要权限控制请携带该参数（传空或传错会导致进房失败）。<br>[privateMapKey 权限设置](https://cloud.tencent.com/document/product/647/32240). |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### switchRole(role, option)

切换用户角色，仅在 TRTC.TYPE.SCENE_LIVE 互动直播模式下生效。

互动直播模式下，一个用户可能需要在"观众"和"主播"之间来回切换。
您可以通过 [enterRoom()](#enterroom) 中的 role 字段确定角色，也可以通过 switchRole 在进房后切换角色。

- 观众切换为主播，调用 trtc.switchRole(TRTC.TYPE.ROLE_ANCHOR) 将用户角色转换为 TRTC.TYPE.ROLE_ANCHOR 主播角色，之后按需调用 [startLocalVideo()](#startlocalvideo) 和 [startLocalAudio()](#startlocalaudio) 发布本地音视频。
- 主播切换为观众，调用 trtc.switchRole(TRTC.TYPE.ROLE_AUDIENCE) 将用户角色转换为 TRTC.TYPE.ROLE_AUDIENCE 观众角色，此时如果有已发布的本地音视频，SDK 会取消发布本地音视频。

> 注意：
> - 该接口需要在进房成功后才可以调用。
> - 关闭摄像头和麦克风后，建议及时切换成观众角色，避免主播角色占用 50路上行的资源。

#### 示例

```javascript
// 进房成功后
// TRTC.TYPE.SCENE_LIVE 互动直播模式下，观众切换为主播
await trtc.switchRole(TRTC.TYPE.ROLE_ANCHOR);
// 观众角色切换为主播，开始推流
await trtc.startLocalVideo();
// TRTC.TYPE.SCENE_LIVE 互动直播模式下，主播切换为观众
await trtc.switchRole(TRTC.TYPE.ROLE_AUDIENCE);
```

```javascript
// Since v5.3.0+
await trtc.switchRole(TRTC.TYPE.ROLE_ANCHOR, { privateMapKey: 'your new privateMapKey' });
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| role | string | **required** 用户角色<br>- TRTC.TYPE.ROLE_ANCHOR 主播，可以发布本地音视频，单个房间里最多支持 50 个主播同时发布本地音视频。<br>- TRTC.TYPE.ROLE_AUDIENCE 观众，不能发布本地音视频，只能观看远端流，单个房间里的观众人数没有上限。 |
| option | object |  |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| privateMapKey | string | `支持 v5.3.0+` <br>privateMapKey 有可能会超时失效，您可以传入该参数更新 privateMapKey。 |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [INVALID_OPERATION](module-ERROR_CODE.html#.INVALID_OPERATION)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### destroy()

销毁 TRTC 实例 

在退房之后，若业务侧无需再使用 trtc 时，需调用该接口及时销毁 trtc 实例，释放相关资源。

注意：

- 销毁后的 trtc 实例不可再继续使用。
- 已进房的情况下，需先调用 [TRTC.exitRoom](#exitroom) 接口退房成功后，才能调用该接口销毁 trtc。

#### 示例

```javascript
// 通话结束时
await trtc.exitRoom();
// 若后续无需再使用该 trtc，则销毁 trtc，并释放引用。
trtc.destroy();
trtc = null;
```

#### 抛出错误

[OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)

### startLocalAudio(config)

开启本地麦克风采集，并发布到当前的房间中。

- 调用时机：进房前后均可调用，不可重复调用。
- 一个 trtc 实例只能开启一路麦克风，若您需要在已经开启一路麦克风的情况下，再开启一路麦克风用于测试，可以创建多个 trtc 实例实现。

#### 示例

```javascript
// 采集默认麦克风并发布
await trtc.startLocalAudio();
```

```javascript
// 如下是测试麦克风音量的代码示例，可用于麦克风音量检测。
trtc.enableAudioVolumeEvaluation();
trtc.on(TRTC.EVENT.AUDIO_VOLUME, event => { });
// 测试麦克风无需发布音频
await trtc.startLocalAudio({ publish: false });
// 测试完毕后，关闭麦克风
await trtc.stopLocalAudio();
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| config | object | 配置项 |

**config 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| publish | boolean | 是否将本地音频发布到房间中，默认为true。若在进房前调用该接口，并且 publish = true，则在进房后 SDK 会自动发布。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| mute | boolean | 是否静音麦克风。参考：[开关摄像头、麦克风](tutorial-15-basic-dynamic-add-video.html)。 |
| option | object | 本地音频选项 |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| microphoneId | string | 指定使用哪个麦克风 |
| audioTrack | MediaStreamTrack | 自定义采集的 audioTrack。[自定义采集与自定义播放渲染](tutorial-20-advanced-customized-capture-rendering.html)。 |
| captureVolume | number | 设置采集音量，默认值 100，设置小于 100 可以降低采集音量，设置大于 100 可以放大采集音量，注意有爆音风险。自 v5.2.1+ 支持。 |
| earMonitorVolume | number | 设置耳返音量，取值[0, 100]，本地麦克风默认静音播放。 |
| profile | string | 音频编码配置, 默认[TRTC.TYPE.AUDIO_PROFILE_STANDARD](module-TYPE.html#.AUDIO_PROFILE_STANDARD) |

#### 抛出错误

- [ENV_NOT_SUPPORTED](module-ERROR_CODE.html#.ENV_NOT_SUPPORTED)
- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### updateLocalAudio(config)

更新本地麦克风配置。

- 调用时机：该接口需在 [startLocalAudio()](#startlocalaudio) 成功后调用，可以多次调用。
- 本方法采用增量更新方式：只更新传入的参数，不传入的参数保持不变。

#### 示例

```javascript
// 切换麦克风
const microphoneList = await TRTC.getMicrophoneList();
if (microphoneList[1]) {
  await trtc.updateLocalAudio({ option: { microphoneId: microphoneList[1].deviceId }});
}
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| config | object |  |

**config 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| publish | boolean | 是否将本地音频发布到房间中，默认为 true。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| mute | boolean | 是否静音麦克风。参考：[开关摄像头、麦克风](tutorial-15-basic-dynamic-add-video.html)。 |
| option | object | 本地音频配置 |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| microphoneId | string | 指定使用哪个麦克风，用来切换麦克风。 |
| audioTrack | MediaStreamTrack | 自定义采集的 audioTrack。 [自定义采集与自定义播放渲染](tutorial-20-advanced-customized-capture-rendering.html)。 |
| captureVolume | number | 设置采集音量，默认值 100，设置小于 100 可以降低采集音量，设置大于 100 可以放大采集音量，注意有爆音风险。自 v5.2.1+ 支持。 |
| earMonitorVolume | number | 设置耳返音量，取值[0, 100]，本地麦克风默认静音播放。 |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### stopLocalAudio()

停止本地麦克风的采集及发布。

- 如果您只是想静音麦克风，请使用 updateLocalAudio({ mute: true })。参考：[开关摄像头、麦克风](tutorial-15-basic-dynamic-add-video.html)。

#### 示例

```javascript
await trtc.stopLocalAudio();
```

#### 抛出错误

[OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### startLocalVideo(config)

开启本地摄像头采集，在您指定的 HTMLElement 标签下播放摄像头画面，并将摄像头画面发布到当前所在房间中。

- 调用时机：进房前后均可调用，不可重复调用。
- 一个 trtc 实例只能开启一路摄像头。若您需要在已经开启一路摄像头的情况下，再开启一路摄像头用于测试，可以创建多个 trtc 实例实现。

#### 示例

**示例 1：预览及发布摄像头**

```javascript
// 预览及发布摄像头
await trtc.startLocalVideo({
  view: document.getElementById('localVideo'), // 在 DOM 中的 elementId 为 localVideo 的标签上预览视频。
});
```

**示例 2：测试摄像头——只预览不发布**

```javascript
// 只预览摄像头画面、不发布。可用于做摄像头测试。
const config = {
  view: document.getElementById('localVideo'), // 在 DOM 中的 elementId 为 localVideo 的标签上预览视频。
  publish: false // 不发布摄像头
}
await trtc.startLocalVideo(config);
// 当需要发布视频时调用 updateLocalVideo
await trtc.updateLocalVideo({ publish:true });
```

**示例 3：预览及发布指定的摄像头**

```javascript
// 使用指定的摄像头。
// 在 DOM 中的 elementId 为 localVideo 的标签上预览视频。
const view = document.getElementById('localVideo');
const cameraList = await TRTC.getCameraList();
if (cameraList[0]) {
  await trtc.startLocalVideo({
    view,
    option: {
      cameraId: cameraList[0].deviceId,
    }
  });
}
// 在移动端指定使用前置摄像头
await trtc.startLocalVideo({ view, option: { useFrontCamera: true }});
// 在移动端指定使用后置摄像头
await trtc.startLocalVideo({ view, option: { useFrontCamera: false }});
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| config | object |  |

**config 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null | 本地视频预览的 HTMLElement 实例或者 Id， 如果不传或传入 null， 则不会播放视频。 |
| publish | boolean | 是否将本地视频发布到房间中。默认为 true，若在进房前调用该接口，SDK 会在进房成功后自动发布（若 publish=true）。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| mute | boolean \| string | 是否临时关闭摄像头。支持传入图片 url 字符串，本地与远端将显示这张图片，房间内其他用户会收到 REMOTE_VIDEO_AVAILABLE 事件，不支持在摄像头关闭时调用。详细内容请参考：[开关摄像头、麦克风](tutorial-15-basic-dynamic-add-video.html). |
| option | object | 本地视频配置 |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| cameraId | string | 指定使用哪个摄像头，用于切换摄像头。 |
| useFrontCamera | boolean | 是否使用前置摄像头。 |
| videoTrack | MediaStreamTrack | 自定义采集的 videoTrack。[自定义采集与自定义播放渲染](tutorial-20-advanced-customized-capture-rendering.html)。 |
| mirror | 'view' \| 'publish' \| 'both' \| boolean | 视频镜像模式，默认为 'view'。可选参数如下：<br>- 'view': 你看自己是镜像，对方看你是非镜像的。<br>- 'publish': 你看自己是非镜像的，对方看你是镜像的。<br>- 'both': 你看自己是镜像的，对方看你是镜像的。<br>- false: 布尔值，代表没有任何镜像。<br><font color="orange"> 注意: 5.3.2 之前版本仅支持传入 boolean，true 代表本地预览镜像，false 代表没有任何镜像。</font> |
| fillMode | 'contain' \| 'cover' \| 'fill' | 视频填充模式。默认为 `cover`。参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| profile | [VideoProfile](global.html#VideoProfile) | 视频大流编码参数。默认值：`480p_2`。 |
| small | boolean \| [VideoProfile](global.html#VideoProfile) | 视频小流编码参数，参考：[多人视频通话](tutorial-27-advanced-small-stream.html) |
| qosPreference | QOS_PREFERENCE_SMOOTH \| QOS_PREFERENCE_CLEAR | 设置弱网时，视频编码策略。（默认）流畅度优先（[QOS_PREFERENCE_SMOOTH](module-TYPE.html#.QOS_PREFERENCE_SMOOTH)）或 清晰度优先（[QOS_PREFERENCE_CLEAR](module-TYPE.html#.QOS_PREFERENCE_CLEAR)） |
| avoidCropping | boolean | 在 PC Chrome 和 Firefox 中，采集 360p 180p 这类低分辨率 16/9 的视频时，可能会出现画幅被裁剪的问题。设置该参数为 true 可以避免裁剪。 |
| rotation | 0 \| 90 \| 180 \| 270 | 设置摄像头顺时针旋转的角度。Since `v5.11.0`。 |

#### 抛出错误

- [ENV_NOT_SUPPORTED](module-ERROR_CODE.html#.ENV_NOT_SUPPORTED)
- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### updateLocalVideo(config)

更新本地摄像头配置。

- 该接口需在 [startLocalVideo()](#startlocalvideo) 成功后调用。
- 该接口可以多次调用。
- 本方法采用增量更新方式：只更新传入的参数，不传入的参数保持不变。

#### 示例

**示例 1：动态切换摄像头**

```javascript
// 切换摄像头
const cameraList = await TRTC.getCameraList();
if (cameraList[1]) {
  await trtc.updateLocalVideo({ option: { cameraId: cameraList[1].deviceId }});
}
```

**示例 2：停止发布视频，但保持本地预览**

```javascript
// 停止发布视频，但保持本地预览
await trtc.updateLocalVideo({ publish:false });
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|-------------|
| config | object | 配置对象 |

**config 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null | 预览摄像头的 HTMLElement 实例或者 Id， 如果不传或传入null，则不会渲染视频，但仍然会推流并消耗带宽 |
| publish | boolean | 是否将本地视频发布到房间中。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| mute | boolean \| string | 是否临时关闭摄像头。支持传入图片 url 字符串，本地与远端将显示这张图片，房间内其他用户会收到 REMOTE_VIDEO_AVAILABLE 事件，不支持在摄像头关闭时调用。详细内容请参考：[开关摄像头、麦克风](tutorial-15-basic-dynamic-add-video.html). |
| option | object | 本地视频配置 |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| cameraId | string | 指定使用哪个摄像头， |
| useFrontCamera | boolean | 是否使用前置摄像头 |
| videoTrack | MediaStreamTrack | 自定义采集的 videoTrack，[自定义采集与自定义播放渲染](tutorial-20-advanced-customized-capture-rendering.html)。 |
| mirror | 'view' \| 'publish' \| 'both' \| boolean | 视频镜像模式，默认为 'view'。可选参数如下：<br>- 'view': 你看自己是镜像，对方看你是非镜像的。<br>- 'publish': 你看自己是非镜像的，对方看你是镜像的。<br>- 'both': 你看自己是镜像的，对方看你是镜像的。<br>- false: 布尔值，代表没有任何镜像。 |
| fillMode | 'contain' \| 'cover' \| 'fill' | 视频填充模式。参考 [ CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性 |
| profile | string \| [VideoProfile](global.html#VideoProfile) | 视频大流编码参数 |
| small | string \| boolean \| [VideoProfile](global.html#VideoProfile) | 视频小流编码参数，参考：[多人视频通话](tutorial-27-advanced-small-stream.html) |
| qosPreference | QOS_PREFERENCE_SMOOTH \| QOS_PREFERENCE_CLEAR | 设置弱网时，视频编码策略。（默认）流畅度优先（[QOS_PREFERENCE_SMOOTH](module-TYPE.html#.QOS_PREFERENCE_SMOOTH)）或 清晰度优先（[QOS_PREFERENCE_CLEAR](module-TYPE.html#.QOS_PREFERENCE_CLEAR)） |
| rotation | 0 \| 90 \| 180 \| 270 | 设置推流时摄像头顺时针旋转的角度。Since `v5.11.0`。 |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### stopLocalVideo()

停止本地摄像头的采集、预览及发布。

- 如果希望仅停止发布视频但保留本地摄像头预览，可以使用[updateLocalVideo({ publish:false }](#updatelocalvideo)方法。

#### 示例

```javascript
await trtc.stopLocalVideo();
```

#### 抛出错误

[OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### startScreenShare(config)

开启屏幕分享。

- 开启屏幕分享后，房间内其他用户会收到 [REMOTE_VIDEO_AVAILABLE](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件，streamType 为 [STREAM_TYPE_SUB](module-TYPE.html#.STREAM_TYPE_SUB)，其他用户可以通过 [startRemoteVideo](#startremotevideo) 播放屏幕分享。

#### 示例

```javascript
// 开始屏幕分享
await trtc.startScreenShare();
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| config | object |  |  |

**config 属性：**

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null |  | 预览本地屏幕分享的 HTMLElement 实例或 Id， 如果不传或传入 null， 则不会渲染本地屏幕分享。 |
| publish | boolean |  | 是否将屏幕分享发布到房间中。默认为 true，若在进房前调用该接口，SDK 会在进房成功后自动发布。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| streamType | TRTC.TYPE.STREAM_TYPE_MAIN \| TRTC.TYPE.STREAM_TYPE_SUB |  | **required** 流类型。一个 trtc 实例可以推一路主流（视频+音频）和一路辅流（视频），屏幕分享默认走辅流推流。<br>- [TRTC.TYPE.STREAM_TYPE_MAIN](module-TYPE.html#.STREAM_TYPE_MAIN): 主流，若设置成主流推屏幕分享，则摄像头无法再走主流推送。<br>- [TRTC.TYPE.STREAM_TYPE_SUB](module-TYPE.html#.STREAM_TYPE_SUB): 辅流(默认值)。 |
| muteSystemAudio | boolean | false | 是否 mute 系统音频，自 v5.12.0+ 支持。 |
| option | object |  | 屏幕分享配置 |

**option 属性：**

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| systemAudio | boolean |  | 是否采集系统声音，默认为 false。 |
| fillMode | 'contain' \| 'cover' \| 'fill' |  | 视频填充模式。默认为 `contain`，参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| profile | [ScreenShareProfile](global.html#ScreenShareProfile) |  | 屏幕分享编码配置。 默认值：`1080p`。 |
| qosPreference | QOS_PREFERENCE_SMOOTH \| QOS_PREFERENCE_CLEAR |  | 设置弱网时，视频编码策略。流畅度优先（[QOS_PREFERENCE_SMOOTH](module-TYPE.html#.QOS_PREFERENCE_SMOOTH)）或 （默认）清晰度优先（[QOS_PREFERENCE_CLEAR](module-TYPE.html#.QOS_PREFERENCE_CLEAR)） |
| videoTrack | MediaStreamTrack |  | 自定义采集的 videoTrack。[自定义采集与自定义播放渲染](tutorial-20-advanced-customized-capture-rendering.html)。 |
| captureElement | HTMLElement |  | 采集当前页面中的某个 HTMLElement，支持 Chrome 104+。 |
| preferDisplaySurface | 'current-tab' \| 'tab' \| 'window' \| 'monitor' | 'monitor' | 控制屏幕分享预选框中的不同类型采集的显示优先级，默认是 monitor，即：在屏幕分享采集预选框中，优先展示屏幕采集。若填 'current-tab'，预选框只会展示当前页面。支持 Chrome 94+。 |

#### 抛出错误

- [ENV_NOT_SUPPORTED](module-ERROR_CODE.html#.ENV_NOT_SUPPORTED)
- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### updateScreenShare(config)

更新屏幕分享配置

- 该接口需在 [startScreenShare()](#startscreenshare) 成功后调用。
- 该接口可以多次调用。
- 本方法采用增量更新方式：只更新传入的参数，不传入的参数保持不变。

#### 示例

```javascript
// 停止屏幕分享，但保持屏幕分享本地预览
await trtc.updateScreenShare({publish:false});
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| config | object |  |  |

**config 属性：**

| 名称 | 类型 | 默认值 | 描述 |
|------|------|---------|-------------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null |  | 屏幕分享预览的 HTMLElement 实例或 Id， 如果不传或传入 null， 则不会渲染屏幕分享。 |
| publish | boolean |  | 是否将屏幕分享发布到房间中。可监听该事件获取推流状态 [PUBLISH_STATE_CHANGED](module-EVENT.html#.PUBLISH_STATE_CHANGED)。 |
| muteSystemAudio | boolean | false | 是否 mute 系统音频，自 v5.12.0+ 支持。 |
| option | object |  | 屏幕分享配置 |

**option 属性：**

| 名称 | 类型 | 描述 |
|------|------|-------------|
| fillMode | 'contain' \| 'cover' \| 'fill' | 视频填充模式。默认为 `contain`，参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| qosPreference | QOS_PREFERENCE_SMOOTH \| QOS_PREFERENCE_CLEAR | 设置弱网时，视频编码策略。流畅度优先（[QOS_PREFERENCE_SMOOTH](module-TYPE.html#.QOS_PREFERENCE_SMOOTH)）或 （默认）清晰度优先（[QOS_PREFERENCE_CLEAR](module-TYPE.html#.QOS_PREFERENCE_CLEAR)） |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [DEVICE_ERROR](module-ERROR_CODE.html#.DEVICE_ERROR)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### stopScreenShare()

停止屏幕分享。

#### 示例

```javascript
await trtc.stopScreenShare();
```

#### 抛出错误

[OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### startRemoteVideo

`(async) startRemoteVideo(config)`

播放远端视频

- 调用时机：在收到 [TRTC.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE)](module-EVENT.html#.REMOTE_VIDEO_AVAILABLE) 事件后调用。

#### 示例

```javascript
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, ({ userId, streamType }) => {
  // 您需在 DOM 中提前放置视频容器，建议以 `${userId}_${streamType}` 作为 element id。
  trtc.startRemoteVideo({ userId, streamType, view: `${userId}_${streamType}` });
})
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config | object | 配置对象 |

##### config 属性

| 名称 | 类型 | 描述 |
|------|------|------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null | 用于播放远端视频的 HTMLElement 实例或者 Id，如果不传或传入null，则不会渲染视频，但会仍然会拉流消耗带宽 |
| userId | string | **required** 远端用户Id |
| streamType | TRTC.TYPE.STREAM_TYPE_MAIN \| TRTC.TYPE.STREAM_TYPE_SUB | **required** 远端流类型 |
| option | object | 远端视频配置 |

##### option 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| small | boolean |  | 是否拉小流 |
| mirror | boolean |  | 是否开启镜像 |
| fillMode | 'contain' \| 'cover' \| 'fill' |  | 视频填充模式。参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| receiveWhenViewVisible | boolean |  | 支持 v5.4.0+ <br>只在 view 可视时，才拉取视频流，不在可视区域内的流，SDK 会自动取消拉流，以达到节省流量的目的。参考：[多人视频通话](tutorial-27-advanced-small-stream.html)。 |
| viewRoot | HTMLElement | document.body | 支持 v5.4.0+ <br>view 的父级元素，用于计算 view 相对于 viewRoot 是否处于可视区域。建议传入 view 列表第一级父级元素，默认值是 document.body。参考：[多人视频通话](tutorial-27-advanced-small-stream.html)。 |
| poster | string |  | Since v5.10.0 <br> 视频的默认封面图。一般无需关心，少数浏览器会显示默认的自带图片，此时可以通过该参数自定义。参考：[HTMLVideoElement: poster property](https://developer.mozilla.org/en-US/docs/Web/API/HTMLVideoElement/poster) |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [INVALID_OPERATION](module-ERROR_CODE.html#.INVALID_OPERATION)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)
- [SERVER_ERROR](module-ERROR_CODE.html#.SERVER_ERROR)

### updateRemoteVideo

`(async) updateRemoteVideo(config)`

更新远端视频播放配置

- 该方法需 [startRemoteVideo](TRTC.html#startRemoteVideo) 成功后调用。
- 该方法可多次调用。
- 该方法采用增量更新的方式，只需要传入需要更新的配置项即可。

#### 示例

```javascript
const config = {
 view: document.getElementById(userId), // 你可以传入新的 view 来更新 video 播放位置。
 userId,
 streamType: TRTC.TYPE.STREAM_TYPE_MAIN
}
await trtc.updateRemoteVideo(config);
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config | object | 配置对象 |

##### config 属性

| 名称 | 类型 | 描述 |
|------|------|------|
| view | string \| HTMLElement \| Array.<HTMLElement> \| null | 用于播放远端视频的 HTMLElement 实例或者 Id，如果不传或传入null，则不会渲染视频，但会仍然会拉流消耗带宽 |
| userId | string | **required** 远端用户Id |
| streamType | TRTC.TYPE.STREAM_TYPE_MAIN \| TRTC.TYPE.STREAM_TYPE_SUB | **required** 远端流类型： |
| option | object | 远端视频配置 |

##### option 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| small | boolean |  | 是否拉小流，参考：[多人视频通话](tutorial-27-advanced-small-stream.html) |
| mirror | boolean |  | 是否开启镜像 |
| fillMode | 'contain' \| 'cover' \| 'fill' |  | 视频填充模式。参考 [CSS object-fit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/object-fit) 属性。 |
| receiveWhenViewVisible | boolean |  | 支持 v5.4.0+ <br> 只在 view 可视时，才拉取视频流，不在可视区域内的流，SDK 会自动取消拉流，以达到节省流量的目的。参考：[多人视频通话](tutorial-27-advanced-small-stream.html)。 |
| viewRoot | HTMLElement | document.body | 支持 v5.4.0+ <br>view 的父级元素，用于计算 view 相对于 viewRoot 是否处于可视区域。建议传入 view 列表第一级父级元素，默认值是 document.body。参考：[多人视频通话](tutorial-27-advanced-small-stream.html)。 |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [INVALID_OPERATION](module-ERROR_CODE.html#.INVALID_OPERATION)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### stopRemoteVideo

`(async) stopRemoteVideo(config)`

用于停止远端视频播放。

#### 示例

```javascript
// 停止播放所有远端用户
await trtc.stopRemoteVideo({ userId: '*' });
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config | object | **required** 远端视频配置 |

##### config 属性

| 名称 | 类型 | 描述 |
|------|------|------|
| userId | string | **required** 远端用户 userId，'*' 代表所有用户。 |
| streamType | TRTC.TYPE.STREAM_TYPE_MAIN \| TRTC.TYPE.STREAM_TYPE_SUB | 远端流类型，当 userId 不为 '*' 时，该字段必填。 |

#### 抛出错误

[OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### muteRemoteAudio

`(async) muteRemoteAudio(userId, mute)`

静音某个远端用户，并且不再拉取该用户的音频数据。仅对当前用户有效，房间内的其他用户依然可以听到被静音用户的声音。

注意：

- 默认情况下，在进房后，SDK 会自动播放远端音频。您可以调用该接口将远端用户静音及取消静音。
- 进房时如果传入参数 autoReceiveAudio = false，则不会自动播放远端音频。当需要播放音频时，需要调用该方法（mute 传入 false）播放远端音频。
- 在进入房间（enterRoom）之前或之后调用本接口均生效，静音状态在退出房间（exitRoom）之后会被重置为 false。
- 如果您希望继续拉取该用户的音频数据，仅仅是不播放，可以调用 setRemoteAudioVolume(userId, 0)

#### 示例

```javascript
// 静音所有远端用户
await trtc.muteRemoteAudio('*', true);
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| userId | string | **required** 远端用户 userId，'*' 代表所有用户。 |
| mute | boolean | **required** 是否静音 |

#### 抛出错误

- [INVALID_PARAMETER](module-ERROR_CODE.html#.INVALID_PARAMETER)
- [INVALID_OPERATION](module-ERROR_CODE.html#.INVALID_OPERATION)
- [OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- [OPERATION_ABORT](module-ERROR_CODE.html#.OPERATION_ABORT)

### setRemoteAudioVolume

`setRemoteAudioVolume(userId, volume)`

用于控制远端音频的播放音量。

- 自 v5.12.1 版本开始，该接口设置一次即可，远端重新进房无需重新设置。在 v5.12.1 之前版本，若远端用户重新进房，需要重新设置音量。
- 不支持 iOS Safari

#### 示例

```javascript
await trtc.setRemoteAudioVolume('123', 90);
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| userId | string | **required** 远端用户 userId。'*' 代表所有远端用户。 |
| volume | number | **required** 音量大小，取值范围为0 - 100，默认值为 100。<br>自 `v5.1.3+` 版本支持设置 volume 大于100。需注意，设置超过 100 可能有爆音风险。 |

### startPlugin

`(async) startPlugin(plugin, options) → {Promise.<void>}`

开启插件

| pluginName | 插件名称 | 参考教程 | 参数类型 |
|------------|----------|----------|----------|
| 'AudioMixer' | 背景音乐插件 | [背景音乐实现方案](tutorial-22-advanced-audio-mixer.html) | [AudioMixerOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#AudioMixerOptions) |
| 'AIDenoiser' | 降噪插件 | [开启 AI 降噪](tutorial-35-advanced-ai-denoiser.html) | [AIDenoiserOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#AIDenoiserOptions) |
| 'CDNStreaming' | CDN混流插件 | [云端混流与转推CDN](tutorial-26-advanced-publish-cdn-stream.html) | [CDNStreamingOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#CDNStreamingOptions) |
| 'VirtualBackground' | 虚拟背景插件 | [开启虚拟背景](tutorial-36-advanced-virtual-background.html) | [VirtualBackgroundOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#VirtualBackgroundOptions) |
| 'Watermark' | 水印插件 | [开启水印](tutorial-29-advanced-water-mark.html) | [WatermarkOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#WatermarkOptions) |
| 'BasicBeauty' | 基础美颜插件 | [开启基础美颜](tutorial-38-advanced-basic-beauty.html) | [BasicBeautyOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#BasicBeautyOptions) |
| 'SmallStreamAutoSwitcher' | 小流自动切换插件 | [开启大小流自动切换插件](tutorial-41-advanced-small-stream-auto-switcher.html) | [SmallStreamAutoSwitcherOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#SmallStreamAutoSwitcherOptions) |
| 'VoiceChanger' | 美声插件 | [开启美声效果](tutorial-37-advanced-voice-changer.html) | [VoiceChangerOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#VoiceChangerStartOptions) |

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| plugin | [PluginName](global.html#PluginName) | **required** |
| options | [AudioMixerOptions](global.html#AudioMixerOptions) \| [AIDenoiserOptions](global.html#AIDenoiserOptions) \| [CDNStreamingOptions](global.html#CDNStreamingOptions) \| [VirtualBackgroundOptions](global.html#VirtualBackgroundOptions) \| [WatermarkOptions](global.html#WatermarkOptions) \| [BasicBeautyOptions](global.html#BasicBeautyOptions) \| [SmallStreamAutoSwitcherOptions](global.html#SmallStreamAutoSwitcherOptions) \| [VoiceChangerStartOptions](global.html#VoiceChangerStartOptions) | **required** |

#### 返回值

**Type:** Promise.<void>

### updatePlugin

`(async) updatePlugin(plugin, options) → {Promise.<void>}`

更新插件

| pluginName | 插件名称 | 参考教程 | 参数类型 |
|------------|----------|----------|----------|
| 'AudioMixer' | 背景音乐插件 | [背景音乐实现方案](tutorial-22-advanced-audio-mixer.html) | [UpdateAudioMixerOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#UpdateAudioMixerOptions) |
| 'CDNStreaming' | CDN混流插件 | [云端混流与转推CDN](tutorial-26-advanced-publish-cdn-stream.html) | [CDNStreamingOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#CDNStreamingOptions) |
| 'Watermark' | 水印插件 | [开启水印](tutorial-29-advanced-water-mark.html) | [WatermarkOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#WatermarkOptions) |
| 'AIDenoiser' | 降噪插件 | [开启 AI 降噪](tutorial-35-advanced-ai-denoiser.html) | [AIDenoiserOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#UpdateDenoiserOptions) |
| 'VirtualBackground' | 虚拟背景插件 | [开启虚拟背景](tutorial-36-advanced-virtual-background.html) | [UpdateVirtualBackgroundOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#UpdateVirtualBackgroundOptions) |
| 'BasicBeauty' | 基础美颜插件 | [开启基础美颜](tutorial-38-advanced-basic-beauty.html) | [BasicBeautyOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#BasicBeautyOptions) |
| 'VoiceChanger' | 美声插件 | [开启美声效果](tutorial-37-advanced-voice-changer.html) | [VoiceChangerOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#VoiceChangerUpdateOptions) |

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| plugin | [PluginName](global.html#PluginName) | **required** |
| options | [UpdateAudioMixerOptions](global.html#UpdateAudioMixerOptions) \| [CDNStreamingOptions](global.html#CDNStreamingOptions) \| [WatermarkOptions](global.html#WatermarkOptions) \| [AIDenoiserOptions](global.html#AIDenoiserOptions) \| [UpdateVirtualBackgroundOptions](global.html#UpdateVirtualBackgroundOptions) \| [BasicBeautyOptions](global.html#BasicBeautyOptions) \| [VoiceChangerUpdateOptions](global.html#VoiceChangerUpdateOptions) | **required** |

#### 返回值

**Type:** Promise.<void>

### stopPlugin

`(async) stopPlugin(plugin, options) → {Promise.<void>}`

停止插件

| pluginName | 插件名称 | 参考教程 | 参数类型 |
|------------|----------|----------|----------|
| 'AudioMixer' | 背景音乐插件 | [背景音乐实现方案](tutorial-22-advanced-audio-mixer.html) | [StopAudioMixerOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/en/global.html#StopAudioMixerOptions) |
| 'AIDenoiser' | 降噪插件 | [开启 AI 降噪](tutorial-35-advanced-ai-denoiser.html) |  |
| 'CDNStreaming' | CDN混流插件 | [云端混流与转推CDN](tutorial-26-advanced-publish-cdn-stream.html) | [CDNStreamingOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/en/global.html#CDNStreamingOptions) |
| 'VirtualBackground' | 虚拟背景插件 | [开启虚拟背景](tutorial-36-advanced-virtual-background.html) |  |
| 'Watermark' | 水印插件 | [开启水印](tutorial-29-advanced-water-mark.html) |  |
| 'BasicBeauty' | 基础美颜插件 | [开启基础美颜](tutorial-38-advanced-basic-beauty.html) |  |
| 'SmallStreamAutoSwitcher' | 小流自动切换插件 | [开启大小流自动切换插件](tutorial-41-advanced-small-stream-auto-switcher.html) |  |
| 'VoiceChanger' | 美声插件 | [开启美声效果](tutorial-37-advanced-voice-changer.html) |  |

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| plugin | [PluginName](global.html#PluginName) | **required** |
| options | [StopAudioMixerOptions](global.html#StopAudioMixerOptions) \| [CDNStreamingOptions](global.html#CDNStreamingOptions) | **required** |

#### 返回值

**Type:** Promise.<void>

### enableAudioVolumeEvaluation

`enableAudioVolumeEvaluation(interval, enableInBackground)`

开启或关闭音量大小回调

- 开启此功能后，无论房间内是否有人说话，SDK 会定时抛出 [TRTC.on(TRTC.EVENT.AUDIO_VOLUME)](module-EVENT.html#.AUDIO_VOLUME) 事件，反馈每一个用户的的音量大小评估值。

#### 示例

```javascript
trtc.on(TRTC.EVENT.AUDIO_VOLUME, event => {
   event.result.forEach(({ userId, volume }) => {
       const isMe = userId === ''; // 当 userId 为空串时，代表本地麦克风音量。
       if (isMe) {
           console.log(`my volume: ${volume}`);
       } else {
           console.log(`user: ${userId} volume: ${volume}`);
       }
   })
});
// 开启音量回调，并设置每 1000ms 触发一次事件
trtc.enableAudioVolumeEvaluation(1000);
// 如需关闭音量回调，传入 interval 值小于等于0即可
trtc.enableAudioVolumeEvaluation(-1);
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| interval | number | 2000 | 用于设置音量回调事件定时触发的时间间隔。默认为 2000(ms)，最小值为100(ms)。若设置小于等于0时，则关闭音量大小回调。 |
| enableInBackground | boolean | false | 出于性能的考虑，当页面切换到后台时，SDK 不会抛出音量回调事件。如需在页面切后台时接收音量回调事件，可设置该参数为 true。 |

### on

`on(eventName, handler, context)`

监听 TRTC 事件

详细事件列表请参见：[TRTC.EVENT](module-EVENT.html)

#### 示例

```javascript
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, event => {
  // REMOTE_VIDEO_AVAILABLE event handler
});
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| eventName | string | **required** 事件名 |
| handler | function | **required** 事件回调函数 |
| context | context | **required** 上下文 |

### off

`off(eventName, handler, context)`

取消事件监听

#### 示例

```javascript
trtc.on(TRTC.EVENT.REMOTE_USER_ENTER, function peerJoinHandler(event) {
  // REMOTE_USER_ENTER event handler
  console.log('remote user enter');
  trtc.off(TRTC.EVENT.REMOTE_USER_ENTER, peerJoinHandler);
});
// 解除所有事件绑定
trtc.off('*');
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| eventName | string | **required** 事件名，传入通配符 '*' 会解除所有事件监听。 |
| handler | function | **required** 事件回调函数 |
| context | context | **required** 上下文 |

### getAudioTrack

`getAudioTrack(config) → {(nullable) MediaStreamTrack}`

获取音频轨道

#### 示例

```javascript
// Version before v5.4.3
trtc.getAudioTrack(); // 获取本地麦克风 audioTrack
trtc.getAudioTrack('remoteUserId'); // 获取远端 audioTrack
// Since v5.4.3+
trtc.getAudioTrack({ streamType: TRTC.STREAM_TYPE_SUB }); // 获取本地屏幕分享 audioTrack
// Since v5.8.2+
trtc.getAudioTrack({ processed: true }); // 获取处理后的 audioTrack
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config | Object \| string | 不传则获取本地的 audioTrack |

##### config 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| userId | string |  | 不传或传空串，代表获取本地的 audioTrack。传远端用户的 userId，代表获取远端用户的 audioTrack。 |
| streamType | STREAM_TYPE_MAIN \| STREAM_TYPE_SUB |  | stream type: |
| processed | boolean | false | 是否获取处理（包括 AI 降噪、增益、混音等）后的音频轨道,默认为 false。 |

#### 返回值

音频轨道

**Type:** MediaStreamTrack

### getVideoTrack

`getVideoTrack(config) → {MediaStreamTrack|null}`

获取视频轨道

#### 示例

```javascript
// 获取本地摄像头 videoTrack
const videoTrack = trtc.getVideoTrack();
// 获取本地屏幕分享 videoTrack
const screenVideoTrack = trtc.getVideoTrack({ streamType: TRTC.TYPE.STREAM_TYPE_SUB });
// 获取远端用户的主流 videoTrack
const remoteMainVideoTrack = trtc.getVideoTrack({ userId: 'test', streamType: TRTC.TYPE.STREAM_TYPE_MAIN });
// 获取远端用户的辅流 videoTrack
const remoteSubVideoTrack = trtc.getVideoTrack({ userId: 'test', streamType: TRTC.TYPE.STREAM_TYPE_SUB });
// Since v5.8.2+, 获取处理后的 videoTrack
const processedVideoTrack = trtc.getVideoTrack({ processed: true });
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config | string | 不传则获取本地摄像头 videoTrack |

##### config 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| userId | string |  | 不传或传空串，代表获取本地的 videoTrack。传远端用户的 userId，代表获取远端用户的 videoTrack。 |
| streamType | STREAM_TYPE_MAIN \| STREAM_TYPE_SUB |  | 远端流类型： |
| processed | boolean | false | 是否获取处理（虚拟背景、镜像、水印、旋转等）后的视频轨道,默认为 false。 |

#### 返回值

视频轨道

**Type:** MediaStreamTrack | null

### getVideoSnapshot

`getVideoSnapshot()`

获取视频帧 

注意事项: Stream 必须经过播放才可以获取视频帧，如果没有播放将会返回空字符串

**Since:** 5.4.0

#### 示例

```javascript
// 获取本地主流视频帧
trtc.getVideoSnapshot()
// 获取本地辅流视频帧
trtc.getVideoSnapshot({streamType:TRTC.TYPE.STREAM_TYPE_SUB})
// 获取远端主流视频帧
let frameData = trtc.getVideoSnapshot({userId: 'remote userId', streamType:TRTC.TYPE.STREAM_TYPE_MAIN})
// 显示视频帧
const img = document.createElement('img');
img.width = '640';
img.height = '480';
img.src = frameData;
document.body.appendChild(img);
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| config.userId | string | **required** Remote user ID |
| config.streamType | TRTC.TYPE.STREAM_TYPE_MAIN \| TRTC.TYPE.STREAM_TYPE_SUB | **required** 获取视频帧的 stream 类型 |

### sendSEIMessage

`sendSEIMessage(buffer, options)`

发送 SEI Message 

视频帧的头部有一个叫做 SEI 的头部数据块，该接口的原理就是利用这个被称为 SEI 的头部数据块，将您要发送的自定义信令嵌入其中，使其同视频帧一并发送出去。SEI 消息可以伴随着视频帧一直传输到直播 CDN 上。

适用场景：视频画面的精准布局、歌词同步、直播答题等。
调用时机：在 [trtc.startLocalVideo](TRTC.html#startLocalVideo) （如果设置了 toSubStream 为 true，则trtc.startLocalScreen） 后调用。

注意：

1. 单次最大发送1KB(Byte)，每秒最大调用次数30次，每秒最多发送8KB。
2. 支持的浏览器： Chrome 86+, Edge 86+, Opera 72+，Safari 15.4+，Firefox 117+。注意：自 v5.8.0 起支持 Safari 和 Firefox。
3. 由于 SEI 跟随视频帧一起发送，视频帧有丢失的可能，因此 SEI 也可能丢失。可在使用频次限制范围内，增加发送次数，业务侧需要在接收端做消息去重。
4. 没有推视频流（如果设置了 toSubStream 为 true 则是辅流）时，无法发送 SEI；没有订阅视频流时，无法接收 SEI。
5. 仅支持 H264 编码器发送 SEI。

**Since:** v5.3.0

**See:** [TRTC.EVENT.SEI_MESSAGE](module-EVENT.html#.SEI_MESSAGE)

#### 示例

```javascript
// 1. 开启 SEI
const trtc = TRTC.create({
   enableSEI: true // 开启 SEI 收发功能
})
// 2. 推流端发送 SEI
try {
 await trtc.enterRoom({
  userId: 'user_1',
  roomId: 12345,
})
 await trtc.startLocalVideo();
 const unit8Array = new Uint8Array([1, 2, 3]);
 trtc.sendSEIMessage(unit8Array.buffer);
 // 消息已通过限制，等待后续视频帧发送。
} catch(error) {
 // 发送失败，可能原因：当前浏览器不支持、参数校验未通过、未推流、没推视频流、调用次数超出限制、使用非 H264 编码器等。
 console.warn(error);
}
// 3. 拉流端接收 SEI
trtc.on(TRTC.EVENT.SEI_MESSAGE, event => {
 console.warn(`收到 ${event.userId} 的 sei ${event.data}`);
})
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| buffer | ArrayBuffer | **required** 待发送的 SEI 数据 |
| options | Object | 配置选项 |

##### options 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| seiPayloadType | Number |  | **required** 设置 SEI payload type。SDK 默认使用自定义 payloadType 243，业务侧可使用该参数将 payloadType 设置为标准的 5。当业务侧使用 5 payloadType 时，需按照规范确保 `buffer` 的前16字节是业务侧自定义的 uuid。 |
| toSubStream | Boolean | false | 设置通过辅流传输 SEI 数据。必须先调用 trtc.startLocalScreen 后才能生效。支持 v5.7.0+。 |

### sendCustomMessage

`sendCustomMessage(message)`

发送自定义消息，消息会广播给房间内的所有用户。 

Note:

1. 只有 [TRTC.TYPE.ROLE_ANCHOR](module-TYPE.html#.ROLE_ANCHOR) 角色可以调用 sendCustomMessage
2. 需要在进房成功后才能发送自定义消息
3. SDK 会保序和尽可能可靠地发送自定义消息，但是在弱网环境下是有可能丢消息的。接收端也会按顺序收到消息。

**Since:** v5.6.0

**See:** [TRTC.EVENT.CUSTOM_MESSAGE](module-EVENT.html#.CUSTOM_MESSAGE) 监听该事件以接收消息

#### 示例

```javascript
// send custom message
trtc.sendCustomMessage({
  cmdId: 1,
  data: new TextEncoder().encode('hello').buffer
});
// receive custom message
trtc.on(TRTC.EVENT.CUSTOM_MESSAGE, event => {
   // event.userId: 远端发消息的 userId
   // event.cmdId: 您自定义的消息 Id
   // event.seq: 消息的序号
   // event.data: 消息内容，ArrayBuffer 类型
   console.log(`received custom msg from ${event.userId}, message: ${new TextDecoder().decode(event.data)}`)
})
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| message | object | **required** 消息对象 |

##### message 属性

| 名称 | 类型 | 描述 |
|------|------|------|
| cmdId | number | **required** 消息 Id，整数，取值范围 1-10。你可以为不同类型的消息设置不同的 cmdId，以降低传输延迟。 |
| data | ArrayBuffer | **required** 消息内容 <br /> - 一次最大发送 1KB(Byte) 数据。<br /> - 每秒限频调用 30 次，每秒最多发送 8KB 数据。 |

### callExperimentalAPI

`(async) callExperimentalAPI(name, options)`

call experimental API

| APIName | name | param |
|---------|------|-------|
| 'enableAudioFrameEvent' | Config the pcm data of Audio Frame Event | [EnableAudioFrameEventOptions](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/global.html#EnableAudioFrameEventOptions) |

#### 示例

```javascript
// 监听远端用户 user_A 的音频数据，默认 pcm 数据为 48kHZ，单声道
await trtc.callExperimentalAPI('enableAudioFrameEvent', { enable: true, userId: 'user_A'})
// 监听所有远端音频数据，并设置 pcm 数据为 16kHZ，双声道
await trtc.callExperimentalAPI('enableAudioFrameEvent', { enable: true, userId: '*', sampleRate: 16000, channelCount: 2 })
// 设置本地麦克风数据回调的 MessagePort
await trtc.callExperimentalAPI('enableAudioFrameEvent', { enable: true, userId: '', port })
// 取消监听本地麦克风数据
await trtc.callExperimentalAPI('enableAudioFrameEvent', { enable: false, userId: '' })
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| name | string | **required** |
| options | [EnableAudioFrameEventOptions](global.html#EnableAudioFrameEventOptions) | **required** |

### TRTC.setLogLevel

`(static) setLogLevel(level, enableUploadLog)`

设置日志输出等级

建议在开发测试阶段设置为 DEBUG 等级，该日志等级包含详细的提示信息。
默认输出 INFO 日志等级，该日志等级包含 SDK 主要功能的日志信息。

#### 示例

```javascript
// 输出 DEBUG 以上日志等级
TRTC.setLogLevel(1);
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| level | 0-5 |  | 日志输出等级 0: TRACE 1: DEBUG 2: INFO 3: WARN 4: ERROR 5: NONE |
| enableUploadLog | boolean | true | 是否开启日志上传，默认开启。不建议关闭，关闭后将影响问题排障。<br /> - 日志上传至腾讯云的服务器，仅供开发人员排查问题使用。如果您需要获取日志信息，可以[联系我们](https://cloud.tencent.com/document/product/647/19906) |

### TRTC.isSupported

`(static) isSupported() → {Promise.<object>}`

检测 TRTC Web SDK 是否支持当前浏览器

- 参考：[浏览器与应用环境信息](tutorial-05-info-browser.html)。

#### 示例

```javascript
TRTC.isSupported().then((checkResult) => {
   if(!checkResult.result) {
      console.log('checkResult', checkResult.result, 'checkDetail', checkResult.detail);
      // SDK 不支持当前浏览器，引导用户使用最新版的 Chrome 浏览器。
   }
   const { isBrowserSupported, isWebRTCSupported, isMediaDevicesSupported, isH264EncodeSupported, isH264DecodeSupported } = checkResult.detail;
   // 不同的业务场景对检测的粒度要求不一样，有点场景只需推流、有的场景只需拉流
   // 判断浏览器是否支持采集摄像头、麦克风推流。
   const isPublishAudioStreamSupported = isBrowserSupported && isWebRTCSupported && isMediaDevicesSupported;
   const isPublishVideoStreamSupported = isBrowserSupported && isWebRTCSupported && isMediaDevicesSupported && isH264EncodeSupported;
   // 判断浏览器是否支持拉流。
   const isRemoteAudioStreamSupported = isBrowserSupported && isWebRTCSupported;
   const isRemoteVideoStreamSupported = isBrowserSupported && isWebRTCSupported && isH264DecodeSupported;
});
```

#### 返回值

Promise 返回检测结果

| Property | Type | Description |
|----------|------|-------------|
| checkResult.result | boolean | 检测结果, true 则代表当前环境支持最基本的采集摄像头、麦克风进行推拉流的能力。 |
| checkResult.detail.isBrowserSupported | boolean | 当前浏览器是否是 SDK 支持的浏览器 |
| checkResult.detail.isWebRTCSupported | boolean | 当前浏览器是否支持 WebRTC，若不支持则无法使用 WebRTC 进行推拉流 |
| checkResult.detail.isWebCodecsSupported | boolean | 当前浏览器是否支持 WebCodecs |
| checkResult.detail.isMediaDevicesSupported | boolean | 当前浏览器是否支持采集麦克风和摄像头 |
| checkResult.detail.isScreenShareSupported | boolean | 当前浏览器是否支持屏幕分享 |
| checkResult.detail.isSmallStreamSupported | boolean | 当前浏览器是否支持小流 |
| checkResult.detail.isH264EncodeSupported | boolean | 当前浏览器上行是否支持 H264 编码 |
| checkResult.detail.isH264DecodeSupported | boolean | 当前浏览器下行是否支持 H264 解码 |
| checkResult.detail.isVp8EncodeSupported | boolean | 当前浏览器上行是否支持 VP8 编码 |
| checkResult.detail.isVp8DecodeSupported | boolean | 当前浏览器下行是否支持 VP8 解码 |

**Type:** Promise.<object>

### TRTC.getPermissions

`(async, static) getPermissions(option) → {Promise.<object>}`

获取摄像头和麦克风的权限状态. 从 v5.13.0+ 支持。

#### 示例

```javascript
const { camera, microphone } = await TRTC.getPermissions({
  request: true, // 如果为 true，调用该接口可能会短暂打开摄像头来获取摄像头权限，以保证正常获取摄像头列表，随后 SDK 会自动释放采集。
  types: ['camera', 'microphone'] // 请求获取的设备类型。
});
console.log(camera, microphone); // 'granted' | 'denied' | 'prompt' | null. null 表示不支持查询。
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| option | PermissionOption | **required** 权限选项 |

##### option 属性

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| request | boolean | true | 是否请求获取设备权限。如果为 true，调用该接口可能会短暂打开摄像头来获取摄像头权限，以保证正常获取摄像头列表，随后 SDK 会自动释放采集。 |
| types | Array.<string> | ['camera', 'microphone'] | 请求获取的设备类型。 |

#### 返回值

Promise 返回摄像头和麦克风的权限状态

**Type:** Promise.<object>

### TRTC.getCameraList

`(static) getCameraList(requestPermission) → {Promise.<Array.<MediaDeviceInfo>>}`

返回摄像头设备列表

**Note**

- 该接口不支持在 http 协议下使用，请使用 https 协议部署您的网站。[Privacy and security](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia#Privacy_and_security)
- 调用该接口可能会短暂打开摄像头来获取摄像头权限，以保证正常获取摄像头列表，随后 SDK 会自动释放摄像头采集。
- 可以调用浏览器原生接口 [getCapabilities](https://developer.mozilla.org/en-US/docs/Web/API/InputDeviceInfo/getCapabilities) 获取摄像头支持的最大分辨率、帧率、移动端设备区分前后置摄像头等。该接口支持 Chrome 67+, Edge 79+, Safari 17+, Opera 54+ 浏览器。
  - 华为手机后摄可能会采集到长焦摄像头，此时可通过该接口获取到支持分辨率最大的后摄，一般手机的主摄的分辨率是最大的。

#### 示例

```javascript
const cameraList = await TRTC.getCameraList();
if (cameraList[0] && cameraList[0].getCapabilities) {
  const { width, height, frameRate, facingMode } = cameraList[0].getCapabilities();
  // 摄像头支持的最大分辨率、帧率。
  console.log(width.max, height.max, frameRate.max);
  // 在移动端区分该摄像头是前置还是后置。
  if (facingMode) {
    if (facingMode[0] === 'user') {
      // 前置摄像头
    } else if (facingMode[0] === 'environment') {
      // 后置摄像头
    }
  }
}
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| requestPermission | boolean | true | `Since v5.6.3`. 是否请求获取设备权限。如果为 true，调用该接口可能会短暂打开摄像头来获取摄像头权限，以保证正常获取摄像头列表，随后 SDK 会自动释放采集。 |

#### 返回值

Promise 返回 [MediaDeviceInfo](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo) 数组

**Type:** Promise.<Array.<MediaDeviceInfo>>

### TRTC.getMicrophoneList

`(static) getMicrophoneList(requestPermission) → {Promise.<Array.<MediaDeviceInfo>>}`

返回麦克风设备列表

**Note**

- 该接口不支持在 http 协议下使用，请使用 https 协议部署您的网站。[Privacy and security](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia#Privacy_and_security)
- 可以调用浏览器原生接口 [getCapabilities](https://developer.mozilla.org/en-US/docs/Web/API/InputDeviceInfo/getCapabilities) 获取麦克风能力信息，例如支持的最大声道数等。该接口支持 Chrome 67+, Edge 79+, Safari 17+, Opera 54+ 浏览器。
- Android 端一般会有多个麦克风，label 列表为： ['default', 'Speakerphone', 'Headset earpiece']，如果在 trtc.startLocalAudio 时，不指定麦克风，浏览器默认麦克风可能会采集到 Headset earpiece，此时声音会从听筒放出。若需要通过扬声器外放，则需要指定采集 label 为 Speakerphone 的麦克风。

#### 示例

```javascript
const microphoneList = await TRTC.getMicrophoneList();
if (microphoneList[0] && microphoneList[0].getCapabilities) {
  const { channelCount } = microphoneList[0].getCapabilities();
  console.log(channelCount.max);
}
```

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| requestPermission | boolean | true | `Since v5.6.3`. 是否请求获取设备权限。如果为 true，调用该接口可能会短暂打开麦克风来获取麦克风权限，以保证正常获取麦克风列表，随后 SDK 会自动释放麦克风采集。 |

#### 返回值

Promise 返回 [MediaDeviceInfo](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo) 数组

**Type:** Promise.<Array.<MediaDeviceInfo>>

### TRTC.getSpeakerList

`(static) getSpeakerList(requestPermission) → {Promise.<Array.<MediaDeviceInfo>>}`

返回扬声器设备列表

不支持移动端设备

#### 参数

| 名称 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| requestPermission | boolean | true | `Since v5.6.3`. 是否请求获取设备权限。如果为 true，调用该接口可能会短暂打开麦克风来获取麦克风权限，以保证正常获取扬声器列表，随后 SDK 会自动释放麦克风采集。 |

#### 返回值

Promise 返回 [MediaDeviceInfo](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo) 数组

**Type:** Promise.<Array.<MediaDeviceInfo>>

### TRTC.setCurrentSpeaker

`(async, static) setCurrentSpeaker(speakerId)`

设置当前音频播放的扬声器。

**注意**

- 仅支持 PC 和安卓端
- 安卓端要求：
  - SDK 版本号需大于等于 5.9.0
  - 支持切换扬声器和听筒，即传入 TRTC.TYPE.SPEAKER 或 TRTC.TYPE.HEADSET
  - 需要先调用 [startLocalAudio()](TRTC.html#startLocalAudio) 采集麦克风

#### 示例

```javascript
// 针对 PC
TRTC.setCurrentSpeaker('你的扬声器ID');
// 针对安卓设备（sdk 版本号 >= 5.9.0）
TRTC.setCurrentSpeaker(TRTC.TYPE.SPEAKER); // 切换扬声器
TRTC.setCurrentSpeaker(TRTC.TYPE.HEADSET); // 或切换听筒
```

#### 参数

| 名称 | 类型 | 描述 |
|------|------|------|
| speakerId | string \| TRTC.TYPE.SPEAKER \| TRTC.TYPE.HEADSET | **required** 扬声器 ID，支持传入扬声器 ID 或 TRTC.TYPE.SPEAKER 或 TRTC.TYPE.HEADSET |

## EVENT

**TRTC事件列表**

通过 [trtc.on(TRTC.EVENT.XXX)](TRTC.html#on) 监听指定的事件。您可以通过这些事件实现管理房间用户列表，以及管理用户的流状态，感知网络状态等功能，下面是事件的详细介绍。

> **注意：**
> - 事件需要在事件触发之前监听，这样才能收到相应的事件通知，因此建议在 trtc 进房前完成事件监听，这样才能确保不会漏掉事件通知。

### Example

```javascript
// 使用方式：
trtc.on(TRTC.EVENT.ERROR, () => {});
```

### Members

#### ERROR

**Default Value:** 'error'

错误事件，非 API 调用错误，SDK 在运行过程中出现了不可恢复的错误时抛出。

- 错误码(error.code)为：[ErrorCode.OPERATION_FAILED](module-ERROR_CODE.html#.OPERATION_FAILED)
- 可能的扩展错误码(error.extraCode)：5501, 5502

##### 示例

```javascript
trtc.on(TRTC.EVENT.ERROR, error => {
  console.error('trtc error observed: ' + error);
  const errorCode = error.code;
  const extraCode = error.extraCode;
});
```

#### AUTOPLAY_FAILED

**Default Value:** 'autoplay-failed'

自动播放失败，参考 [自动播放处理建议](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-21-advanced-auto-play-policy.html)

##### 示例

```javascript
trtc.on(TRTC.EVENT.AUTOPLAY_FAILED, event => {
  // 引导用户点击页面，当用户点击页面时，SDK 会自动恢复播放。
  // 自 v5.1.3+ 新增 userId 参数，表示哪个用户出现自动播放失败。
  console.log(event.userId);
  // 自 v5.11.0+ 新增 mediaType 参数，表示媒体类型，值：'audio'|'video'|'screen'。
  console.log(event.mediaType);
  // 自 v5.9.0+ 新增 resume 方法，当用户点击页面后，可以主动调用该方法恢复播放 event.userId 对应的流。
  event.resume();
});
```

#### KICKED_OUT

**Default Value:** 'kicked-out'

被踢出房间，原因如下：

- **kick**: 相同 userId 的用户同时进入同一个房间（以下简称为同名进房），先进入房间的用户会被后进入的用户踢出房间。
  - 同名进房是不允许的行为，可能会导致双方音视频通话异常，业务侧应避免出现这种情况。
  - TRTC 后台不保证观众角色同名进房互踢。即观众角色的用户，使用同 userId 进同一个房间，可能不会收到该事件。
- **banned**: 系统管理员通过[服务端 API](https://cloud.tencent.com/document/product/647/40496) 将该用户踢出房间。
- **room-disband**: 系统管理员通过[服务端 API](https://cloud.tencent.com/document/product/647/40496) 解散房间。

##### 示例

```javascript
trtc.on(TRTC.EVENT.KICKED_OUT, event => {
  console.log(event.reason)
});
```

#### REMOTE_USER_ENTER

**Default Value:** 'remote-user-enter'

远端用户进房事件。

- `rtc` 模式下，所有用户都会收到进退房通知。
- `live` 模式下，只有主播才有进退房通知，观众没有进退房通知，观众可以收到主播的进退房通知。

##### 示例

```javascript
trtc.on(TRTC.EVENT.REMOTE_USER_ENTER, event => {
  const userId = event.userId;
});
```

#### REMOTE_USER_EXIT

**Default Value:** 'remote-user-exit'

远端用户退房事件。

- `rtc` 模式下，所有用户都会收到进退房通知。
- `live` 模式下，只有主播才有进退房通知，观众没有进退房通知，观众可以收到主播的进退房通知。

##### 示例

```javascript
trtc.on(TRTC.EVENT.REMOTE_USER_EXIT, event => {
  const userId = event.userId;
});
```

#### REMOTE_AUDIO_AVAILABLE

**Default Value:** 'remote-audio-available'

远端用户发布了音频。当远端用户打开麦克风后，您会收到该通知。参考：[开关摄像头、麦克风](./tutorial-15-basic-dynamic-add-video.html)

- 默认情况下，SDK 会自动播放远端音频，您无需调用 API 来播放远端音频。可以监听该事件及 [REMOTE_AUDIO_UNAVAILABLE](#remote_audio_unavailable) 来更新"远端是否开启麦克风"的 UI icon。
- 需要注意的是：如果用户在进房前没有与页面产生过交互，自动播放音频可能会因为【浏览器的自动播放策略限制】而失败，您需参考[自动播放受限处理建议](./tutorial-21-advanced-auto-play-policy.html)进行处理。
- 若您不希望 SDK 自动播放音频，您可以在 [trtc.enterRoom()](TRTC.html#enterRoom) 时设置 `autoReceiveAudio` 为 `false` 关闭自动播放音频。
- 监听 [TRTC.EVENT.REMOTE_AUDIO_AVAILABLE](#remote_audio_available) 事件，记录有远端音频的 userId，在需要播放音频时，调用 [trtc.muteRemoteAudio(userId, false)](TRTC.html#muteRemoteAudio) 方法。

##### 示例

```javascript
// 在进房前监听
trtc.on(TRTC.EVENT.REMOTE_AUDIO_AVAILABLE, event => {
  const userId = event.userId;
});
```

#### REMOTE_AUDIO_UNAVAILABLE

**Default Value:** 'remote-audio-unavailable'

远端停止发布了音频。当远端用户关闭麦克风后，您会收到该通知。

##### 示例

```javascript
// 在进房前监听
trtc.on(TRTC.EVENT.REMOTE_AUDIO_UNAVAILABLE, event => {
  const userId = event.userId;
});
```

#### REMOTE_VIDEO_AVAILABLE

**Default Value:** 'remote-video-available'

远端用户发布了视频，当远端用户开启摄像头后，您会收到该通知。参考：[开关摄像头、麦克风](./tutorial-15-basic-dynamic-add-video.html)

- 可以监听该事件及 [REMOTE_VIDEO_UNAVAILABLE](#remote_video_unavailable) 来更新"远端是否开启摄像头"的 UI icon。

##### 示例

```javascript
// 在进房前监听
trtc.on(TRTC.EVENT.REMOTE_VIDEO_AVAILABLE, event => {
  const userId = event.userId;
  const streamType = event.streamType;
  trtc.startRemoteVideo({userId, streamType, view});
});
```

#### REMOTE_VIDEO_UNAVAILABLE

**Default Value:** 'remote-video-unavailable'

远端用户停止发布视频，当远端用户关闭摄像头后，您会收到该通知。

##### 示例

```javascript
// 在进房前监听
trtc.on(TRTC.EVENT.REMOTE_VIDEO_UNAVAILABLE, event => {
  const userId = event.userId;
  const streamType = event.streamType;
  // 此时 SDK 会自动停止播放，无需调用 stopRemoteVideo。
});
```

#### AUDIO_VOLUME

**Default Value:** 'audio-volume'

音量大小事件
调用 [enableAudioVolumeEvaluation](TRTC.html#enableAudioVolumeEvaluation) 接口开启音量大小回调后，SDK 会定时抛出该事件，通知每个 userId 的音量大小。

**Note**

- 回调中包含本地麦克风音量及远端用户的音量，无论是否有人说话，都会触发该回调。
- 回调 event.result 会根据音量大小，按大到小进行排序。
- 当 userId 为空串时，代表本地麦克风音量。
- volume 取值为 0-100 的正整数
- floatVolume 取值为 0-1.0 的浮点数。自 v5.11.1+ 支持。当麦克风采集异常无声时，该 floatVolume 值是 0，若是用户没说话，floatVolume 是非 0 的浮点数。

##### 示例

```javascript
trtc.on(TRTC.EVENT.AUDIO_VOLUME, event => {
   event.result.forEach(({ userId, volume, floatVolume }) => {
       const isMe = userId === ''; // 当 userId 为空串时，代表本地麦克风音量。
       if (isMe) {
           console.log(`my volume: ${volume}`);
       } else {
           console.log(`user: ${userId} volume: ${volume}`);
       }
   })
});
// 开启音量回调，并设置每 1000ms 触发一次事件
trtc.enableAudioVolumeEvaluation(1000);
```

#### NETWORK_QUALITY

**Default Value:** 'network-quality'

网络质量统计数据事件，进房后开始统计，每两秒触发一次，该数据反映的是您本地的上、下行的网络质量。

- 上行网络质量（uplinkNetworkQuality）指的是您上传本地流的网络情况（SDK 到腾讯云的上行连接网络质量）
- 下行网络质量（downlinkNetworkQuality）指的是您下载所有流的平均网络情况（腾讯云到 SDK 的所有下行连接的平均网络质量）

其枚举值及含义如下表所示：

| 数值 | 含义 |
|------|------|
| 0 | 网络状况未知，表示当前 trtc 实例还没有建立上行/下行连接 |
| 1 | 网络状况极佳 |
| 2 | 网络状况较好 |
| 3 | 网络状况一般 |
| 4 | 网络状况差 |
| 5 | 网络状况极差 |
| 6 | 网络连接已断开<br>注意：若下行网络质量为此值，则表示所有下行连接都断开了 |

- uplinkRTT，uplinkLoss 为上行 RTT(ms) 及上行丢包率。
- downlinkRTT，downlinkLoss 为所有下行连接的平均 RTT(ms) 及平均丢包率。

**Note**

- 如果您想知道对方的上下行网络情况，需要把对方的网络质量情况通过 IM 广播出去。

##### 示例

```javascript
trtc.on(TRTC.EVENT.NETWORK_QUALITY, event => {
   console.log(`network-quality, uplinkNetworkQuality:${event.uplinkNetworkQuality}, downlinkNetworkQuality: ${event.downlinkNetworkQuality}`)
   console.log(`uplink rtt:${event.uplinkRTT} loss:${event.uplinkLoss}`)
   console.log(`downlink rtt:${event.downlinkRTT} loss:${event.downlinkLoss}`)
});
```

#### CONNECTION_STATE_CHANGED

**Default Value:** 'connection-state-changed'

SDK 和腾讯云的连接状态变更事件，您可以利用该事件从总体上监听 SDK 与腾讯云的连接状态。

- 'DISCONNECTED'：连接断开
- 'CONNECTING'：正在连接中
- 'CONNECTED'：已连接

不同状态变更的含义：

- DISCONNECTED -> CONNECTING: 正在尝试建立连接，调用进房接口或者 SDK 自动重连时触发。
- CONNECTING -> DISCONNECTED: 连接建立失败，当正在连接时调用退房接口中断连接或者经过 SDK 重试后任然连接失败时触发。
- CONNECTING -> CONNECTED: 连接建立成功，连接成功时触发。
- CONNECTED -> DISCONNECTED: 连接中断，调用退房接口或者当网络异常导致连接断开时触发。

处理建议：可以监听该事件，在不同状态显示不同的 UI，提醒用户当前的连接状态。

##### 示例

```javascript
trtc.on(TRTC.EVENT.CONNECTION_STATE_CHANGED, event => {
  const prevState = event.prevState;
  const curState = event.state;
});
```

#### AUDIO_PLAY_STATE_CHANGED

**Default Value:** 'audio-play-state-changed'

音频播放状态变更事件

event.userId 当 userId 为空串时，代表本地用户，非空串代表远端用户。

event.state 取值如下：

- 'PLAYING'：开始播放
  - event.reason 为 'playing' 或者 'unmute'。
- 'PAUSED'：暂停播放
  - event.reason 为 'pause' 时，由 `<audio>` element 的 pause 事件触发，如下几种情况会触发：
    - 调用 HTMLMediaElement.pause 接口。
  - event.reason 为 'mute' 时。详见事件 [MediaStreamTrack.mute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/mute_event)
    - 若 userId 为自己时 触发该事件，表明音频采集暂停，通常是设备异常引起，如设备被其他应用抢占，此时需引导用户重新采集。
    - 若 userId 为他人时 触发该事件，表明收到的音频数据不足以播放。通常是网络抖动引起，接入侧无需做任何处理。当收到的数据足以播放时，会自动恢复。
- 'STOPPED'：停止播放
  - event.reason 为 'ended'。

event.reason 状态变化的原因，取值如下：

- 'playing'：开始播放，详见事件 [HTMLMediaElement.playing_event](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/playing_event)
- 'mute'：音频轨道暂时未能提供数据，详见事件 [MediaStreamTrack.mute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/mute_event)
- 'unmute'：音频轨道恢复提供数据，详见事件 [MediaStreamTrack.unmute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/unmute_event)
- 'ended'：音频轨道已被关闭
- 'pause'：播放暂停

##### 示例

```javascript
trtc.on(TRTC.EVENT.AUDIO_PLAY_STATE_CHANGED, event => {
  console.log(`${event.userId} player is ${event.state} because of ${event.reason}`);
});
```

#### VIDEO_PLAY_STATE_CHANGED

**Default Value:** 'video-play-state-changed'

视频播放状态变更事件

event.userId 当 userId 为空串时，代表本地用户，非空串代表远端用户。

event.streamType 流类型，取值：[TRTC.TYPE.STREAM_TYPE_MAIN](module-TYPE.html#.STREAM_TYPE_MAIN) [TRTC.TYPE.STREAM_TYPE_SUB](module-TYPE.html#.STREAM_TYPE_SUB)

event.state 取值如下：

- 'PLAYING'：开始播放
  - event.reason 为 'playing' 或者 'unmute'。
- 'PAUSED'：暂停播放
  - event.reason 为 'pause' 时，由 `<video>` element 的 pause 事件触发，如下几种情况会触发：
    - 调用 HTMLMediaElement.pause 接口。
    - 在播放成功后，从 DOM 中移除了播放视频的 view 容器。
  - event.reason 为 'mute' 时。详见事件 [MediaStreamTrack.mute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/mute_event)
    - 若 userId 为自己时 触发该事件，表明视频采集暂停，通常是设备异常引起，如设备被其他应用抢占，此时需引导用户重新采集。
    - 若 userId 为他人时 触发该事件，表明收到的视频数据不足以播放。通常是网络抖动引起，接入侧无需做任何处理。当收到的数据足以播放时，会自动恢复。
- 'STOPPED'：停止播放
  - event.reason 为 'ended'。

event.reason 状态变化的原因，取值如下：

- 'playing'：开始播放，详见事件 [HTMLMediaElement.playing_event](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/playing_event)
- 'mute'：视频轨道暂时未能提供数据，详见事件 [MediaStreamTrack.mute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/mute_event)
- 'unmute'：视频轨道恢复提供数据，详见事件 [MediaStreamTrack.unmute_event](https://developer.mozilla.org/en-US/docs/Web/API/MediaStreamTrack/unmute_event)
- 'ended'：视频轨道已被关闭
- 'pause'：播放暂停

##### 示例

```javascript
trtc.on(TRTC.EVENT.VIDEO_PLAY_STATE_CHANGED, event => {
  console.log(`${event.userId} ${event.streamType} video player is ${event.state} because of ${event.reason}`);
});
```

#### SCREEN_SHARE_STOPPED

**Default Value:** 'screen-share-stopped'

本地屏幕分享停止事件通知，仅对本地屏幕分享流有效。

##### 示例

```javascript
trtc.on(TRTC.EVENT.SCREEN_SHARE_STOPPED, () => {
  console.log('screen sharing was stopped');
});
```

#### DEVICE_CHANGED

**Default Value:** 'device-changed'

摄像头、麦克风等设备变化的通知事件。

- event.device 是一个 [MediaDeviceInfo](https://developer.mozilla.org/en-US/docs/Web/API/MediaDeviceInfo) 对象，属性：
  - deviceId：设备 Id
  - label：设备描述信息
  - groupId：设备 groupId
- event.type 值：`'camera'|'microphone'|'speaker'`
- event.action 值：
  - 'add' 设备已添加。
  - 'remove' 设备已被移除。
  - 'active' 设备已启动，例如：startLocalVideo 成功后，会触发该事件。

##### 示例

```javascript
trtc.on(TRTC.EVENT.DEVICE_CHANGED, (event) => {
  console.log(`${event.type}(${event.device.label}) ${event.action}`);
});
```

#### PUBLISH_STATE_CHANGED

**Default Value:** 'publish-state-changed'

推流状态变更事件。

- event.mediaType 媒体类型，值：`'audio'|'video'|'screen'`。
- event.state 当前的推流状态，值：
  - `'starting'` 正在尝试推流
  - `'started'` 推流成功
  - `'stopped'` 推流停止，原因见 event.reason 字段
- event.prevState 上一次事件触发时的推流状态，值和 event.state 相同。
- event.reason 推流状态变为 `'stopped'` 的原因，值：
  - `'timeout'` 推流超时，一般是由于网络抖动、防火墙拦截导致。SDK 会不断进行重试，业务侧可以在此时引导用户检查网络、更换网络。
  - `'error'` 推流出错，此时可从 event.error 中获取到具体错误信息，一般是由于浏览器不支持 H264 编码导致。
  - `'api-call'` 业务侧 api 调用导致推流停止，例如在 startLocalVideo 推流成功前，调用了 stopLocalVideo 停止了推流，属于正常行为，业务侧无需关注。
- event.error event.reason 为 `'error'` 时的错误信息。

##### 示例

```javascript
trtc.on(TRTC.EVENT.PUBLISH_STATE_CHANGED, (event) => {
  console.log(`${event.mediaType} ${event.state} ${event.reason}`);
});
```

#### TRACK

**Since:** v5.3.0

**Default Value:** 'track'

收到新的 MediaStreamTrack 对象

##### 示例

```javascript
trtc.on(TRTC.EVENT.TRACK, event => {
  const isLocal = event.userId === ''; // userId === '' means event.track is a local track, otherwise it's a remote track
  const isSubStream = event.streamType === TRTC.TYPE.STREAM_TYPE_SUB; // Usually the sub stream is a screen-sharing video stream.
  const mediaStreamTrack = event.track;
  const kind = event.track.kind; // audio or video
});
```

#### STATISTICS

**Since:** v5.2.0

**Default Value:** 'statistics'

通话相关数据指标。

- 监听该事件后，SDK 会以 2s 一次的频率定时抛出该事件。
- 您可以从该事件中可以获取通话的网络质量（rtt、丢包率）、上行和下行的音视频统计信息（音量大小、宽高、帧率、码率）等信息。详细参数说明请参考：[TRTCStatistics](global.html#TRTCStatistics)

##### 示例

```javascript
trtc.on(TRTC.EVENT.STATISTICS, statistics => {
   console.warn(statistics.rtt, statistics.upLoss, statistics.downLoss);
});
```

#### SEI_MESSAGE

**Since:** v5.3.0

**Default Value:** 'sei-message'

收到 SEI message

##### 示例

```javascript
trtc.on(TRTC.EVENT.SEI_MESSAGE, event => {
   console.log(`收到 ${event.userId} 的 sei message: ${event.data}`);
});
```

#### CUSTOM_MESSAGE

**Since:** v5.6.0

**Default Value:** 'custom-message'

收到自定义消息

##### 示例

```javascript
trtc.on(TRTC.EVENT.CUSTOM_MESSAGE, event => {
   // event.userId: 远端发消息的 userId
   // event.cmdId: 您自定义的消息 Id
   // event.seq: 消息的序号
   // event.data: 消息内容，ArrayBuffer 类型
});
```

#### FIRST_VIDEO_FRAME

**Since:** v5.9.0

**Default Value:** 'first-video-frame'

本地视频或者远端视频首帧渲染事件。

##### 示例

```javascript
trtc.on(TRTC.EVENT.FIRST_VIDEO_FRAME, event => {
   // event.height: 视频高度
   // event.width: 视频宽度
   // event.streamType: 视频流类型
   // event.userId: 用户 ID，若是空串，则代表是本地视频。
});
```

#### VIDEO_SIZE_CHANGED

**Since:** v5.13.0

**Default Value:** 'video-size-changed'

Video size changed event

##### 示例

```javascript
trtc.on(TRTC.EVENT.VIDEO_SIZE_CHANGED, event => {
   // event.newHeight: 变更后的视频高度
   // event.newWidth: 变更后的视频宽度
   // event.streamType: 视频流类型
   // event.userId: 本地或远端用户的用户 ID，为空表示本地视频且本地用户未进房间；不为空表示远端视频或本地用户已进房间。
});
```

## ERROR_CODE

TRTC SDK v5.0 定义了8种错误码类型，通过 RtcError 对象来获取 errorCode 并做相应的处理。

**参考文档：**
- [RtcError](RtcError.html)
- [TRTC.EVENT.ERROR](module-EVENT.html#.ERROR)

### 使用示例

```javascript
// 使用方式：
// 1. API 调用错误
trtc.startLocalVideo().catch(error => {
 if (error.code === TRTC.ERROR_CODE.DEVICE_ERROR) {}
});
// 2. 非 API 调用错误，SDK 内部经过重试后依然无法恢复的错误
trtc.on(TRTC.EVENT.ERROR, (error) => {
   if (error.code === TRTC.ERROR_CODE.OPERATION_FAILED) {}
});
```

### 错误码成员

#### INVALID_PARAMETER

**默认值：** 5000

调用接口时传入了不满足 API 要求的参数  
处理建议：请检查传入参数是否符合 API 的规范，例如参数类型是否正确。

| extraCode | 描述 |
|-----------|------|
| 5001 | 未填必填参数 |
| 5002 | 参数类型不对 |
| 5003 | 参数传了空值 |
| 5004 | 参数原型不对 |
| 5005 | 参数不在规定的取值范围 |
| 5006 | 参数要大于等于 0 |
| 5007 | 参数要大于最小值 |
| 5008 | 参数要小于最大值 |
| 5009 | 该 elementId 在页面上找不到，或者查找时不在页面上 |
| 5010 | 传入的参数不是 HTMLElement 类型 |
| 5011 | streamId 不符合规范 |
| 5012 | 传入 roomId 的范围不符合规范 [1, 4294967294] |
| 5013 | 传入的 strRoomId 类型不是合法的 string |
| 5014 | 当 userId 不是 '*'时，需要传入 streamType |
| 5015 | 未填写 roomId 或 strRoomId, roomId 和 strRoomId 必须填写一个 |
| 5016 | roomId 必须是数字类型，当前传入了字符串类型，如果需要使用字符串作为房间号，请使用 strRoomId |
| 5017 | arraybuffer 的 size 不能为 0，从这些 API 中抛出该错误： [TRTC.sendSEIMessage](TRTC.html#sendSEIMessage), [TRTC.sendCustomMessage](TRTC.html#sendCustomMessage) |
| 5018 | arraybuffer 的 size 超过允许的范围，从这些 API 中抛出该错误：[TRTC.sendSEIMessage](TRTC.html#sendSEIMessage), [TRTC.sendCustomMessage](TRTC.html#sendCustomMessage) |

#### INVALID_OPERATION

**默认值：** 5100

调用接口时，不满足 API 的前提要求。  
处理建议：请根据对应 API 文档检查调用逻辑是否符合 API 的前提要求。例如：1.未进房成功就进行切换角色，2.播放的远端用户和流不存在。

| extraCode | 描述 |
|-----------|------|
| 5101 | 在未进房的情况下调用 API，例如 startRemoteVideo muteRemoteAudio switchRole 等 API 需在进房后调用 |
| 5102 | 远端用户不存在，例如在 startRemoteVideo 时，传入的 userId 对应的远端用户不在房间内。 |
| 5103 | 远端流类型不存在，例如在 startRemoteVideo 时，传入的 streamType = TRTC.TYPE.STREAM_TYPE.SUB，但对应的远端用户并没有推屏幕分享。 |
| 5104 | 重复调用 API，例如在进房成功后，又调用 enterRoom 接口。 |
| 5105 | 在未开启摄像头的情况下，调用 API。例如虚拟背景插件需要在摄像头开启的情况下使用。 |
| 5106 | 在未开启麦克风的情况下，调用 API。例如 AI 降噪插件需要在麦克风开启的情况下使用。 |
| 5107 | 观众角色 [TRTC.TYPE.ROLE_AUDIENCE](module-TYPE.html#.ROLE_AUDIENCE) 不能调用此 API。 |
| 5108 | 你需要在 TRTC.create({ enableSEI: true }) 打开 SEI 功能 |
| 5109 | 你需要在发送 SEI 之前推视频流。 |

#### ENV_NOT_SUPPORTED

**默认值：** 5200

当前环境不支持该功能，表明当前浏览器不支持调用对应 API  
处理建议：通常使用 TRTC.isSupported 可感知当前浏览器支持哪些能力。如果浏览器不支持，需要引导用户使用支持该能力的浏览器，参考：[检测浏览器支持性](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-23-advanced-support-detection.html)

| extraCode | 描述 |
|-----------|------|
| 5201 | 当前页面协议非 HTTPS，不支持音视频采集（推流、连麦）能力 |
| 5202 | 当前浏览器不支持 WebRTC 能力，浏览器版本过低 |
| 5203 | 浏览器不支持 H264 编码能力 |
| 5204 | 浏览器不支持 H264 解码能力 |
| 5205 | 浏览器不支持屏幕分享能力 |
| 5206 | 浏览器不支持小流编码能力 |
| 5207 | 浏览器不支持 SEI 收发能力 |
| 5208 | 浏览器不支持 WebGL |
| 5209 | 当前版本的浏览器不支持该功能，请升级到最新版本 |
| 5210 | 当前版本的浏览器不支持该插件，请升级到最新版本 |

参考资料：[getUserMedia 异常](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia#%E5%BC%82%E5%B8%B8) 和 [getDisplayMedia 异常](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getDisplayMedia#%E5%BC%82%E5%B8%B8)

#### DEVICE_ERROR

**默认值：** 5300

获取设备或者采集音视频出现异常。这些接口出现异常时会抛出该错误码：[trtc.startLocalAudio](TRTC.html#startLocalAudio) [trtc.startLocalVideo](TRTC.html#startLocalVideo) [trtc.startScreenShare](TRTC.html#startScreenShare)。  
处理建议：引导用户检查设备是否有摄像头及麦克风、系统是否给浏览器授权以及浏览器是否给页面授权。建议增加进房前的设备检测流程，确认麦克风和摄像头是否存在，并且能正常采集，再进行下一步通话操作。通常经过设备检查后都能避免该异常。实现方式请参考: [通话前环境与设备检测](tutorial-23-advanced-support-detection.html)。

| extraCode | 描述 |
|-----------|------|
| 5301 | 由浏览器抛出的 NotFoundError(DOMException) <br> 原因：找不到满足请求参数的媒体设备类型（包括：音频、视频、屏幕分享）。例如：PC 没有摄像头，但是请求浏览器获取视频流，则会报此错误。 <br> 处理建议：建议在通话开始前引导用户检查通话所需的摄像头或麦克风等外设。 |
| 5302 | 由浏览器抛出的 NotAllowedError(DOMException) <br>原因：<li>用户拒绝了当前的浏览器实例的采集麦克风、摄像头、屏幕分享请求。</li><li>系统关闭了浏览器的麦克风、摄像头采集权限。</li><br> 处理建议：<li>提示用户授权摄像头/麦克风访问才可以进行音视频通话。</li><li>若是由于系统关闭了浏览器权限，rtcError 会有 [rtcError.handler](RtcError.html#.handler) 方法，调用该方法可以跳转到系统权限设置中，方便用户开启权限。</li> |
| 5303 | 由浏览器抛出的 NotReadableError(DOMException) <br> 原因：尽管用户已经授权使用相应的设备，但是由于操作系统上某个硬件、浏览器或者网页层面发生的错误或者其他应用占用了设备导致设备无法被浏览器访问。<br> 处理建议：根据浏览器的报错信息处理，并提示用户："暂时无法访问摄像头/麦克风，请确保当前没有其他应用请求访问摄像头/麦克风，并重试" |
| 5304 | 由浏览器抛出的 OverconstrainedError(DOMException) <br> 原因：cameraId/microphoneId 参数的值无效 <br> 处理建议：检查 cameraId/microphoneId 是否是通过获取设备信息接口返回的值 |
| 5305 | 由浏览器抛出的 InvalidStateError(DOMException) <br> 原因：当前页面未产生交互，页面不是完全激活的 <br> 处理建议：建议经过用户与页面产生点击交互后，再开启摄像头麦克风 |
| 5306 | 由浏览器抛出的 SecurityError(DOMException) <br> 原因：由于系统安全策略禁止使用设备<br> 处理建议：检查系统是否限制使用设备，并建议经过用户与页面产生点击交互后，再开启摄像头麦克风 |
| 5307 | 由浏览器抛出的 AbortError(DOMException) <br> 原因：由于某些未知原因导致设备无法被使用 <br> 处理建议：建议更换设备或者浏览器，重新检测设备是否正常 |
| 5308 | 摄像头采集异常，经过 SDK 重试任然无法恢复采集。 <br> 原因：摄像头异常或者用户手动关闭了浏览器的采集权限导致 <br> 处理建议：提示用户摄像头采集异常，引导用户检查摄像头是否正常、是否有采集权限 |
| 5309 | 麦克风采集异常，经过 SDK 重试任然无法恢复采集。 <br> 原因：麦克风异常或者用户手动关闭了浏览器的采集权限导致 <br> 处理建议：提示用户麦克风采集异常，引导用户检查麦克风是否正常、是否有采集权限 |

#### SERVER_ERROR

**默认值：** 5400

收到服务端返回的异常数据时抛出该错误码  
以下接口出现异常时会抛出该错误码：`enterRoom`，`startLocalVideo`, `startLocalAudio`, `startScreenShare`, `startRemoteVideo`, `switchRole`  
处理建议：服务端异常通常在开发阶段处理，常见的异常有：传入的 userSig 过期，腾讯云账号欠费，未开通TRTC服务等，服务端返回异常数据有以下原因。

| extraCode | 描述 |
|-----------|------|
| 5401 | 该功能需要购买套餐 |
| -8 | sdkAppId 不正确，请检查 sdkAppId 是否正确填写 |
| -10012 | 未传入 roomId 或者 roomId 不符合规范, 如需使用 string 类型的 roomId，请在调用 trtc.enterRoom 时使用 strRoomId |
| -10015 | 服务端获取服务器节点失败 |
| -10016 | 服务端内部通信超时，超时时间 3s |
| -10035 | 服务器切换房间失败 |
| -10037 | 主播角色不支持切换房间 |
| -100006 | 检查权限失败，启用高级权限控制后，请检查 [trtc.enterRoom](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/TRTC.html#enterRoom) 携带的 privateMapKey 参数是否正确。请查看 [开启高级权限设置](https://cloud.tencent.com/document/product/647/32240) |
| -100013 | 客户服务欠费, 请登录 [实时音视频控制台](https://console.cloud.tencent.com/trtc)，单击您创建的应用，单击【帐号信息】，在帐号信息面板即可确认服务状态 |
| -100021 | 服务端过载，进房失败 |
| -100022 | 服务器分配失败 |
| -100024 | 未开通 TRTC 服务导致进房失败，请到 [IM 控制台](https://console.cloud.tencent.com/im) 为您的应用开通 TRTC 服务 |
| -102006 | 流控定义的错误码（add user failed） |
| -102010 | 启用高级权限控制后，用户没有创建房间的权限，请查看 [开启高级权限设置](https://cloud.tencent.com/document/product/647/32240) |
| -102023 | 请求参数错误（后端接口服务产生的请求参数错误） |
| 70001 | userSig 过期，请尝试重新生成。如果是刚生成就过期，请检查有效期填写的是否过小或者误填为 0 |
| 70002 | userSig 长度为 0，请确认签名计算是否正确，访问 sign_src 获取计算签名的傻瓜式源码，核对参数，确保签名计算正确性 |
| 70003 | userSig 校验失败，请确认下 userSig 内容是否被截断，例如缓冲区长度不够导致的内容截断 |
| 70004 | userSig 校验失败，请确认下 userSig 内容是否被截断，例如缓冲区长度不够导致的内容截断 |
| 70005 | userSig 校验失败，通过工具来验证生成的 userSig 是否正确 |
| 70006 | userSig 校验失败，通过工具来验证生成的 userSig 是否正确 |
| 70007 | userSig 校验失败，通过工具来验证生成的 userSig 是否正确 |
| 70008 | userSig 校验失败，通过工具来验证生成的 userSig 是否正确 |
| 70009 | 用业务公钥验证 userSig 失败，请确认生成的 userSig 使用的私钥和 sdkAppId 是否对应 |
| 70010 | userSig 校验失败，通过工具来验证生成的 userSig 是否正确 |
| 70013 | userSig 中 userId 与请求时的 userId 不匹配，请检查登录时填写的 userId 与 userSig 中的是否一致 |
| 70014 | userSig 中 sdkAppId 与请求时的 sdkAppId 不匹配，请检查登录时填写的 sdkAppId 与 userSig 中的是否一致 |
| 70015 | 未找到该 sdkAppId 和帐号类型对应的验证方式，请确认是否已进行帐号集成操作 |
| 70016 | 拉取到的公钥长度为 0，请确认是否已上传公钥，如果是重新上传的公钥需要十分钟后再尝试 |
| 70017 | 内部第三方票据验证超时，请重试，如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70018 | 内部验证第三方票据失败 |
| 70019 | 通过 HTTPS 方式验证的票据字段为空，请正确填写 userSig |
| 70020 | sdkAppId 未找到，请确认是否已在腾讯云上配置 |
| 70052 | userSig 已经失效，请重新生成，再次尝试 |
| 70101 | 请求包信息为空 |
| 70102 | 请求包帐号类型错误 |
| 70103 | 电话号码格式错误 |
| 70104 | 邮箱格式错误 |
| 70105 | TLS 帐号格式错误 |
| 70106 | 非法帐号格式类型 |
| 70107 | userId 没有注册 |
| 70113 | 批量数量不合法 |
| 70114 | 安全原因被限制 |
| 70115 | uin 不是对应 sdkAppId 的开发者 uin |
| 70140 | sdkAppId 和 acctype 不匹配 |
| 70145 | 帐号类型错误 |
| 70169 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70201 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70202 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70203 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70204 | sdkAppId 没有对应的 acctype |
| 70205 | 查找 acctype 失败，请重试 |
| 70206 | 请求中批量数量不合法 |
| 70207 | 内部错误，请重试 |
| 70208 | 内部错误，请重试 |
| 70209 | 获取开发者 uin 标志失败 |
| 70210 | 请求中 uin 为非开发者 uin |
| 70211 | 请求中 uin 非法 |
| 70212 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70213 | 访问内部数据失败，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70214 | 验证内部票据失败 |
| 70221 | 登录状态无效，请使用 UserSig 重新鉴权 |
| 70222 | 内部错误，请重试 |
| 70225 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70231 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70236 | 验证 user signature 失败 |
| 70308 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70346 | 票据校验失败。 |
| 70347 | 票据因过期原因校验失败 |
| 70348 | 内部错误，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70362 | 内部超时，请重试。如果多次重试仍不成功，请联系 TLS 帐号支持，QQ：3268519604 |
| 70401 | 内部错误，请重试 |
| 70402 | 参数非法。请检查必填字段是否填充，或者字段的填充是否满足协议要求 |
| 70403 | 发起操作者不是 App 管理员，没有权限操作 |
| 70050 | 因失败且重试次数过多导致被限制，请检查票据是否正确，一分钟之后再试 |
| 70051 | 帐号已被拉入黑名单，请联系 TLS 帐号支持，QQ：3268519604 |

#### OPERATION_FAILED

**默认值：** 5500

在满足 API 调用要求的情况下，SDK 经过多次重试仍然无法解决的异常，通常是由于浏览器、网络的问题造成。  
以下接口出现异常时会抛出该错误码：`enterRoom`，`startLocalVideo`, `startLocalAudio`, `startScreenShare`, `startRemoteVideo`, `switchRole`  
处理建议：
- 确认通信必需的域名和端口是否满足您的网络环境要求，参考文档[应对防火墙限制及设置代理](https://cloud.tencent.com/document/product/647/34399#WebRTC-.E9.9C.80.E8.A6.81.E9.85.8D.E7.BD.AE.E5.93.AA.E4.BA.9B.E7.AB.AF.E5.8F.A3.E6.88.96.E5.9F.9F.E5.90.8D.E4.B8.BA.E7.99.BD.E5.90.8D.E5.8D.95.EF.BC.9F)
- 其他问题需要联系工程师处理 [在线客服](https://cloud.tencent.com/act/event/Online_service?from=doc_647)

| extraCode | 描述 |
|-----------|------|
| 5501 | 防火墙受限：SDK 经过多次重试后，依然无法建立媒体连接，会导致无法推流及无法拉流。<br />处理建议：参考教程 [应对防火墙受限](https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-34-advanced-proxy.html) |
| 5502 | 重进房失败：当用户经历超过 30s 的断网后，SDK 会尝试重进房恢复通话，但可能由于 userSig 过期导致重进房失败，此时会抛出该错误。<br />处理建议：遇到此错误时，您可以使用最新的 userSig 重新调用 [TRTC.enterRoom](TRTC.html#enterRoom) 进房 |
| 5503 | 重进房失败：当用户经历超过 30s 的断网后，SDK 会尝试重进房恢复通话，但可能由于 userSig 过期导致重进房失败，此时会抛出该错误。<br />处理建议：遇到此错误时，您可以使用最新的 userSig 重新调用 [TRTC.enterRoom](TRTC.html#enterRoom) 进房 |
| 5505 | 视频编码失败，通常是设备不支持 h264 编码 <br />处理建议：华为 Chrome 88 以下版本以及部分厂商的 Android 设备，不具备 H264 编码能力（即无法推流）。如果您希望在这些设备的浏览器中，使用 TRTC Web SDK 推流，请提交 [Web SDK 用户能力支持申请](https://cloud.tencent.com/apply/p/4ab2rutgukk) 开通 VP8 编解码能力 |
| 5506 | 音频编码失败 <br />处理建议：遇到此错误时，您可以升级到最新版本 SDK，或者提示用户升级到最新系统版本 |
| 5507 | 视频解码失败，通常是设备不支持 h264 解码 <br />处理建议：参考视频编码失败的处理方案 |
| 5508 | 音频解码失败 <br />处理建议：遇到此错误时，您可以升级到最新版本 SDK，或者提示用户升级到最新系统版本 |

#### OPERATION_ABORT

**默认值：** 5998

中止 API 执行时抛出该错误码。在不满足 API 生命周期的调用或重复调用时 API 会中止执行，避免无意义的操作。  
例如：连续调用 enterRoom，startLocalXxx等接口，在没有进房就调用退房。  
以下接口出现异常时会抛出该错误码：`enterRoom`，`startLocalVideo`, `startLocalAudio`, `startScreenShare`, `startRemoteVideo`, `switchRole`  
处理建议：捕获并识别该错误码，然后在业务逻辑规避不必要的调用，或者也可以不做任何处理，因为 SDK 做了无副作用处理，您只需在 catch 时识别该错误码并忽略。

#### UNKNOWN_ERROR

**默认值：** 5999

未知错误或者未被定义的错误  
处理建议：联系工程师处理 [在线客服](https://cloud.tencent.com/act/event/Online_service?from=doc_647)

## TYPE

**TRTC 常量**

### 使用示例

```javascript
// 使用方式：
TRTC.TYPE.SCENE_LIVE
```

### 成员常量

#### SCENE_LIVE

**默认值:** `'live'`

直播场景

#### SCENE_RTC

**默认值:** `'rtc'`

通话场景

#### ROLE_ANCHOR

**默认值:** `'anchor'`

主播角色

#### ROLE_AUDIENCE

**默认值:** `'audience'`

观众角色

#### STREAM_TYPE_MAIN

**默认值:** `'main'`

主流

- TRTC 有主路视频流（主流）和辅路视频流（辅流）
- 摄像头通过主流发布，屏幕分享通过辅流发布。
- 主路视频流包括：高清大画面和低清小画面两种，默认情况下，[TRTC.startRemoteVideo](TRTC.html#startRemoteVideo) 播放的是高清大画面，可以通过 small 参数播放低清小画面，参考：[开启大小流功能](./tutorial-27-advanced-small-stream.html)。

#### STREAM_TYPE_SUB

**默认值:** `'sub'`

辅流

#### AUDIO_PROFILE_STANDARD

**默认值:** `'standard'`

标准音质

| 音频 Profile | 采样率 | 声道 | 码率 (kbps) |
|-------------|--------|------|------------|
| TRTC.TYPE.AUDIO_PROFILE_STANDARD | 48000 | 单声道 | 40 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH | 48000 | 单声道 | 128 |
| TRTC.TYPE.AUDIO_PROFILE_STANDARD_STEREO | 48000 | 双声道 | 64 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH_STEREO | 48000 | 双声道 | 192 |

#### AUDIO_PROFILE_STANDARD_STEREO

**默认值:** `'standard-stereo'`

标准音质立体声

| 音频 Profile | 采样率 | 声道 | 码率 (kbps) |
|-------------|--------|------|------------|
| TRTC.TYPE.AUDIO_PROFILE_STANDARD | 48000 | 单声道 | 40 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH | 48000 | 单声道 | 128 |
| TRTC.TYPE.AUDIO_PROFILE_STANDARD_STEREO | 48000 | 双声道 | 64 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH_STEREO | 48000 | 双声道 | 192 |

#### AUDIO_PROFILE_HIGH

**默认值:** `'high'`

高音质

| 音频 Profile | 采样率 | 声道 | 码率 (kbps) |
|-------------|--------|------|------------|
| TRTC.TYPE.AUDIO_PROFILE_STANDARD | 48000 | 单声道 | 40 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH | 48000 | 单声道 | 128 |
| TRTC.TYPE.AUDIO_PROFILE_STANDARD_STEREO | 48000 | 双声道 | 64 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH_STEREO | 48000 | 双声道 | 192 |

#### AUDIO_PROFILE_HIGH_STEREO

**默认值:** `'high-stereo'`

高音质立体声

| 音频 Profile | 采样率 | 声道 | 码率 (kbps) |
|-------------|--------|------|------------|
| TRTC.TYPE.AUDIO_PROFILE_STANDARD | 48000 | 单声道 | 40 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH | 48000 | 单声道 | 128 |
| TRTC.TYPE.AUDIO_PROFILE_STANDARD_STEREO | 48000 | 双声道 | 64 |
| TRTC.TYPE.AUDIO_PROFILE_HIGH_STEREO | 48000 | 双声道 | 192 |

#### QOS_PREFERENCE_SMOOTH

**默认值:** `'smooth'`

弱网时，视频编码策略以流畅度优先，即优先保帧率。
摄像头默认流畅度优先，屏幕分享默认清晰度优先。

#### QOS_PREFERENCE_CLEAR

**默认值:** `'clear'`

弱网时，视频编码策略以清晰度优先，即优先保分辨率。
摄像头默认流畅度优先，屏幕分享默认清晰度优先。