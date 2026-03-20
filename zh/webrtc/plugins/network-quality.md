> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

在进房之前或在通话过程中，检测用户的网络质量，可以判断用户当下的网络质量情况。若用户网络质量太差，应建议用户检查网络或尝试更换网络，以保证正常通话质量。

本文主要介绍如何基于 [NETWORK_QUALITY](./module-EVENT.html#.NETWORK_QUALITY) 事件实现通话过程及通话前网络质量检测。

## 通话过程中的网络质量检测

参考： {@link module:EVENT.NETWORK_QUALITY NETWORK_QUALITY}

```js
const trtc = TRTC.create();
trtc.on(TRTC.EVENT.NETWORK_QUALITY, event => {
   console.log(`network-quality, uplinkNetworkQuality:${event.uplinkNetworkQuality}, downlinkNetworkQuality: ${event.downlinkNetworkQuality}`)
   console.log(`uplink rtt:${event.uplinkRTT} loss:${event.uplinkLoss}`)
   console.log(`downlink rtt:${event.downlinkRTT} loss:${event.downlinkLoss}`)
})
```

## 通话前的网络质量检测

通话前的网络质量检测需要借助 {@link module:EVENT.NETWORK_QUALITY NETWORK_QUALITY} 事件实现。

### 实现流程

1. 调用 [TRTC.create](./TRTC.html#.createTRTC) 创建两个 trtc 对象，分别称为 uplinkTRTC 和 downlinkTRTC。
2. 这两个 TRTC 都进入同一个房间。
3. 使用 uplinkTRTC 进行推流，监听 [NETWORK_QUALITY](./module-EVENT.html#.NETWORK_QUALITY) 事件来检测上行网络质量。
4. 使用 downlinkTRTC 进行拉流，监听 [NETWORK_QUALITY](./module-EVENT.html#.NETWORK_QUALITY) 事件来检测下行网络质量。
5. 整个过程可持续 15s 左右，最后取平均网络质量，从而大致判断出上下行网络情况。

> !
> - 检测过程将产生少量的[基础服务费用](https://cloud.tencent.com/document/product/647/17157#.E5.9F.BA.E7.A1.80.E6.9C.8D.E5.8A.A1)。如果未指定推流分辨率，则默认以 640*480 的分辨率推流。

### API 调用时序

![network-quality-call-sequence](./assets/network-quality-call-sequence.png)

### 代码示例

```js
let uplinkTRTC = null; // 用于检测上行网络质量
let downlinkTRTC = null; // 用于检测下行网络质量
let localStream = null; // 用于测试的流
let testResult = {
  // 记录上行网络质量数据
  uplinkNetworkQualities: [],
  // 记录下行网络质量数据
  downlinkNetworkQualities: [],
  average: {
    uplinkNetworkQuality: 0,
    downlinkNetworkQuality: 0
  }
}

// 1. 检测上行网络质量
async function testUplinkNetworkQuality() {
  uplinkTRTC = TRTC.create();

  uplinkTRTC.enterRoom({
    roomId: 8080,
    sdkAppId: 0, // 填写 sdkAppId
    userId: 'user_uplink_test',
    userSig: '', // uplink_test 的 userSig
    scene: 'rtc'
  })

  uplinkTRTC.on(TRTC.EVENT.NETWORK_QUALITY, event => {
    const { uplinkNetworkQuality } = event;
    testResult.uplinkNetworkQualities.push(uplinkNetworkQuality);
  });

  uplinkTRTC.startLocalVideo();
}

// 2. 检测下行网络质量
async function testDownlinkNetworkQuality() {
  downlinkTRTC = TRTC.create();
  downlinkTRTC.enterRoom({
    roomId: 8080,
    sdkAppId: 0, // 填写 sdkAppId
    userId: 'user_downlink_test',
    userSig: '', // userSig
    scene: 'rtc',
    autoReceiveVideo: true
  });

  downlinkTRTC.on(TRTC.EVENT.NETWORK_QUALITY, event => {
    const { downlinkNetworkQuality } = event;
    testResult.downlinkNetworkQualities.push(downlinkNetworkQuality);
  })
}

// 3. 开始检测
testUplinkNetworkQuality();
testDownlinkNetworkQuality();

// 4. 15s 后停止检测，计算平均网络质量
setTimeout(() => {
  // 计算上行平均网络质量
  const validUplinkNetworkQualitiesList = testResult.uplinkNetworkQualities.filter(value => value >= 1 && value <= 5);
  if (validUplinkNetworkQualitiesList.length > 0) {
    testResult.average.uplinkNetworkQuality = Math.ceil(
      validUplinkNetworkQualitiesList.reduce((value, current) => value + current, 0) / validUplinkNetworkQualitiesList.length
    );
  }
  
  const validDownlinkNetworkQualitiesList = testResult.uplinkNetworkQualities.filter(value => value >= 1 && value <= 5);
  if (validDownlinkNetworkQualitiesList.length > 0) {
    // 计算下行平均网络质量
    testResult.average.downlinkNetworkQuality = Math.ceil(
      validDownlinkNetworkQualitiesList.reduce((value, current) => value + current, 0) / validDownlinkNetworkQualitiesList.length
    );
  }
    
  // 检测结束，清理相关状态。
  uplinkTRTC.exitRoom();
  downlinkTRTC.exitRoom();
}, 15 * 1000);
```

## 结果分析

经过上述步骤，可以拿到上行平均网络质量、下行平均网络质量。网络质量的枚举值如下所示：

| 数值 | 含义                                                         |
| :--- | :----------------------------------------------------------- |
| 0    | 网络状况未知，表示当前 TRTC 实例还没有建立上行/下行连接    |
| 1    | 网络状况极佳                                                 |
| 2    | 网络状况较好                                                 |
| 3    | 网络状况一般                                                 |
| 4    | 网络状况差                                                   |
| 5    | 网络状况极差                                                 |
| 6    | 网络连接已断开 注意：若下行网络质量为此值，则表示所有下行连接都断开了 |

> 建议：当网络质量大于3时，应引导用户检查网络并尝试更换网络环境，否则难以保证正常的音视频通话。
> 也可通过下述策略来降低带宽消耗：
> - 若上行网络质量大于3，则可通过 [TRTC.updateLocalVideo()](./TRTC.html#updateLocalVideo) 接口降低码率 或 [TRTC.stopLocalVideo()](./TRTC.html#stopLocalVideo) 方式关闭视频，以降低上行带宽消耗。
> - 若下行网络质量大于3，则可通过订阅小流（参考：[开启大小流传输](./tutorial-27-advanced-small-stream.html)）或者只订阅音频的方式，以降低下行带宽消耗。
