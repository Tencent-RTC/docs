> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 浏览器环境检测

在开始音视频通话之前，建议您先使用 {@link TRTC.isSupported TRTC.isSupported} 接口检测 SDK 是否支持当前网页。如果 SDK 不支持当前浏览器，建议用户使用最新版的 Chrome 浏览器、Edge 浏览器、Safari 浏览器、Firefox 浏览器。

```javascript
TRTC.isSupported().then(checkResult => {
  // 不支持使用 SDK，引导用户使用最新版的 Chrome 浏览器。
  if (!checkResult.result) {}
  // 不支持发布视频
  if (!checkResult.detail.isH264EncodeSupported) {}
  // 不支持拉视频
  if (!checkResult.detail.isH264DecodeSupported) {}
})
```

⚠️ `TRTC.isSupported` 返回的检测结果为 false 时，可能是以下原因：

1. 网页使用了 http 协议。浏览器不允许 http 协议的网站采集摄像头和麦克风，您需要使用 https 协议部署您的网页。
2. 当前浏览器不支持 WebRTC 相关能力，您需要引导用户使用推荐的浏览器 {@tutorial 05-info-browser}
3. Firefox 浏览器安装完成后需要动态加载 H264 编解码器，因此会出现短暂的检测结果为 false 的情况，请稍等再试或引导使用其他浏览器。

## 通话前设备检测

### 方案一：使用设备检测插件（包括默认 UI）

检测用户的媒体设备（摄像头、麦克风、扬声器）以及网络，建议在用户进房之前使用，支持移动端适配。

> - 该插件需要：TRTC Web SDK 版本 >= 5.8.0。
> - 该插件包括默认的 UI，若 UI 不符合您的产品设计需求，可以参考方案二自定义 UI 实现。

#### 实现流程

```javascript
import { DeviceDetector } from 'trtc-sdk-v5/plugins/device-detector';
const trtc = TRTC.create({ plugins: [DeviceDetector] });

// 1. Test Media Device Only
const result = await trtc.startPlugin('DeviceDetector');

// 2. Test Media Device & Network Quality
const options = { 
    networkDetect: { sdkAppId, userId, userSig }
}
const resultWithNetwork = await trtc.startPlugin('DeviceDetector', options);
```

#### API

##### trtc.startPlugin('DeviceDetector', options)

第一个参数为'DeviceDetector'字符串，用于开启设备检测插件。

##### options

| Name            | Type   | Attributes | Description                                                  |
| --------------- | ------ | ---------- | ------------------------------------------------------------ |
| language       | 'auto' \| 'zh' \| 'en' | 选填        | 选填。设置 UI 的语言，支持中文和英文。默认为 'auto'，表示根据当前浏览器语言自动判断。'zh' 表示使用中文, 'en' 表示使用英文。|
| allowSkip       | boolean | 选填        | 选填。默认为 true，会显示跳过按钮。若为 false，则不显示跳过按钮，用户必须完成每一步检测才能进行下一步。|
| cameraDetect    | object  | 选填        | 选填。摄像头检测选项，详见下文 cameraDetect 选项|
| networkDetect   | object  | 选填        | 选填。网络检测选项，详见下文 networkDetect 选项|

###### options.cameraDetect(optional)
| Name            | Type   | Attributes | Description                                                  |
| --------------- | ------ | ---------- | ------------------------------------------------------------ |
| mirror          | boolean | 选填        | 选填。为true时，在摄像头检测环节会开启画面镜像，并显示镜像画面的复选框 |

###### options.networkDetect(optional)

| Name            | Type   | Attributes | Description                                                  |
| --------------- | ------ | ---------- | ------------------------------------------------------------ |
| sdkAppId        | string | 必填       | 从控制台获得，你的sdkAppId                                   |
| userId          | string | 必填       | 上行用户ID, 自行决定，与downlinkUserId不一致，与用于生成userSig的userId一致 |
| userSig         | string | 必填       | userSig 签名，由sdkAppId，userId，sdkSecretKey作为参数生成，生产环境由向服务器请求生成，参考：[UserSig](https://trtc.io/document/35166) |
| downlinkUserId  | string | 选填       | 下行用户ID, 自行决定，与userId不一致，与用于生成downlinkUserSig的userId一致。填写 downlinkUserId 和 downlinkUserSig将进行下行网络测试 |
| downlinkUserSig | string | 选填       | userSig 签名，由sdkAppId，downlinkUserId，sdkSecretKey作为参数生成，生产环境由向服务器请求生成，参考：[UserSig](https://trtc.io/document/35166) |
| roomId          | number | 选填       | 选填，默认为8080，数字类型的房间号，取值要求为 [1, 4294967294] 的整数 |

##### startPlugin 返回结果

| Name           | Type     | Description                                                  |
| -------------- | -------- | ------------------------------------------------------------ |
| **camera**     | `object` | { isSuccess: boolean, device: { deviceId: string, groupId: string, kind: "videoinput", label: string }} |
| **microphone** | `object` | { isSuccess: boolean, device: { deviceId: string, groupId: string, kind: "audioinput", label: string }} |
| **speaker**    | `object` | { isSuccess: boolean, device: { deviceId: string, groupId: string, kind: "audiooutput", label: string }} |
| **network**    | `object` | 网络检测结果(startPlugin传入networkDetect才检测返回)<br />{ isSuccess: boolean, result: { quality: number , rtt: number}}﻿ <br />quality为网络质量，默认为上行网络质量，传入下行userId和userSig时为上下行网络质量中较差值<br />**网络质量**<br />0：网络状况未知，表示当前 TRTC 实例还没有建立上行/下行连接 <br />1：网络状况极佳 <br />2：网络状况较好 <br />3：网络状况一般 <br />4：网络状况差 <br />5：网络状况极差 <br />6：网络连接已断开 注意：若下行网络质量为此值，则表示所有下行连接都断开了 |

#### 插件 Demo

<iframe allow="microphone; camera; display-capture;" height="700" style="width:100%" scrolling="no" title="Device Detector" src="" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="">
  Device Detector</a> by TRTC (<a href="">@TRTC</a>)
  on <a href="">CodePen</a>.
</iframe>

### 方案二：基于 SDK API 自行实现设备检测（自定义 UI）

若方案一的设备检测插件的默认 UI 不符合您的需求，你可以选择方案二来自定义 UI。

#### 1. 检查设备权限

从 v5.13.0+ 开始，SDK 提供了 `TRTC.getPermissions` 接口，用于检查、请求和监听设备权限。

```javascript
const { camera, microphone } = await TRTC.getPermissions({
  request: true, // 如果为 true，调用该接口可能会短暂打开摄像头来获取摄像头权限，以保证正常获取摄像头列表，随后 SDK 会自动释放采集。
  types: ['camera', 'microphone'] // 请求获取的设备类型。
});
console.log(camera, microphone); // 'granted' | 'denied' | 'prompt' | null. null 表示不支持查询。

// 监听设备权限变化
trtc.on(TRTC.EVENT.PERMISSION_STATE_CHANGE, event => {
  console.log(event.camera, event.microphone);
});
// 释放监听
trtc.off(TRTC.EVENT.PERMISSION_STATE_CHANGE);
```

#### 2. 设备连接

设备连接的目的是检测用户使用的机器是否有摄像头、麦克风或扬声器，是否在联网状态。如果有摄像头，麦克风或扬声器设备，尝试获取音视频流并引导用户授予摄像头，麦克风的访问权限。

+ 判断设备是否有摄像头，麦克风或扬声器

  ```javascript
  import TRTC from 'trtc-sdk-v5';
  
  const cameraList = await TRTC.getCameraList();
  const micList = await TRTC.getMicrophoneList();
  const hasCameraDevice = cameraList.some(camera => camera.deviceId !== '');
  const hasMicrophoneDevice = micList.some(mic => mic.deviceId !== '');
  // 移动端设备不支持获取扬声器设备，移动端检测扬声器可以不获取扬声器
  const speakerList = await TRTC.getSpeakerList();
  const hasSpeakerDevice = speakerList.some(speaker => speaker.deviceId !== '');
  ```

#### 3. 摄像头检测

​检测原理：打开摄像头，并在页面中渲染摄像头画面。您可以在页面中提供 2 个按钮，让用户确认是否能看到摄像头画面。

+ 打开摄像头

  ```javascript
  trtc.startLocalVideo({ view: 'camera-video', publish: false });
  ```

+ 切换摄像头

  ```javascript
  trtc.updateLocalVideo({ option: { cameraId } });
  ```

+ 检测完成后关闭摄像头

  ```javascript
  trtc.stopLocalVideo();
  ```

#### 4. 麦克风检测

​检测原理：打开麦克风，并获取麦克风音量。监听麦克风音量事件，并将实时音量渲染到页面中，引导用户说话，确认是否能看到音量条变化。

+ 打开麦克风

  ```javascript
  trtc.on(TRTC.EVENT.AUDIO_VOLUME, event => {
    event.result.forEach(({ userId, volume }) => {
        const isMe = userId === ''; // 当 userId 为空串时，代表本地麦克风音量。
        if (isMe) {
            console.log(`my volume: ${volume}`);
            // 将麦克风音量渲染到页面中
        }
    })
  });
  // 开启音量回调，并设置每 200ms 触发一次事件
  trtc.enableAudioVolumeEvaluation(200);
  trtc.startLocalAudio({ publish: false });
  ```

+ 切换麦克风

  ```javascript
  trtc.updateLocalAudio({ option: { microphoneId }});
  ```

+ 检测完成后释放麦克风占用

  ```javascript
  trtc.stopLocalAudio();
  ```

#### 5. 扬声器检测

​	检测原理：通过 audio 标签播放一个 mp3 媒体文件

+ 创建 audio 标签，提醒用户调高播放音量，播放 mp3 确认扬声器设备是否正常。

  ```html
  <audio id="audio-player" src="xxxxx" controls></audio>
  ```

+ 检测结束后停止播放

  ```javascript
  const audioPlayer = document.getElementById('audio-player');
  if (!audioPlayer.paused) {
      audioPlayer.pause();
  }
  audioPlayer.currentTime = 0;
  ```

#### 6. 网络检测

参考：[通话前的网络质量检测](./tutorial-24-advanced-network-quality.html)

## TRTC 能力检测页面

您可以在当前使用 TRTC SDK 的地方，使用 [TRTC 检测页面](https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html) ，可用于探测当前环境，还可以点击生成报告按钮，得到当前环境的报告，用于环境检测，或者问题排查。
