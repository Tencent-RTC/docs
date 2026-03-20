> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本文档指导您如何使用 Atomicx 组件和 `useDeviceState` Hook 来管理会议中的音视频设备（摄像头、麦克风、扬声器）状态，以及如何获取本地网络质量信息。

## 功能介绍

- **设备枚举与切换：**支持实时获取系统可用设备列表，并实现无缝切换。

- **状态精细化控制：**提供开启/关闭、采集静音、音量调节及实时音量监测能力。

- **错误诊断：**实时反馈硬件冲突、系统权限缺失等异常状态。

- **网络质量监控：**提供包含延迟、丢包率在内的多维度网络信息。

## 前提条件

- 用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

- 若在 `iframe` 中集成，需在标签中声明权限。

   ``` typescript
   <iframe allow="microphone; camera;"></iframe>
   ```

## 实现摄像头管理

您可以通过 [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) 来控制本地摄像头的开关、切换设备，并监听采集状态和错误。

### 步骤1：开启/关闭摄像头

通过调用 [openLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalCamera) 和 [closeLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#closeLocalCamera) 方法来控制摄像头的开关。
``` typescript
import { useDeviceState, DeviceStatus } from 'tuikit-atomicx-vue3/room';
const { cameraStatus, openLocalCamera, closeLocalCamera } = useDeviceState();
const toggleCamera = async () => {
  if (cameraStatus.value === DeviceStatus.On) {
    await closeLocalCamera();
  } else {
    await openLocalCamera();
  }
};
```

### 步骤2：获取/切换摄像头设备

#### 桌面端获取和切换摄像头示例

您可以通过 [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList) 获取所有可用的摄像头设备，使用 [setCurrentCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentCamera) 方法切换当前使用的摄像头。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { cameraList, currentCamera, getCameraList, setCurrentCamera } = useDeviceState();

// 获取摄像头列表
await getCameraList();

// 遍历并显示可用摄像头
cameraList.value.forEach((camera) => {
  console.log(`摄像头: ${camera.deviceName} (${camera.deviceId})`);
});

// 切换到指定摄像头
const switchCamera = async (deviceId: string) => {
  try {
    // 切换设备（如果摄像头已开启，会自动使用新设备重新采集）
    await setCurrentCamera({ deviceId });
    console.log('摄像头切换成功');
  } catch (error) {
    console.error('摄像头切换失败:', error);
    // 切换失败时会保持使用原设备
  }
};

// 监听当前摄像头变化
watch(currentCamera, (camera) => {
  if (camera) {
    console.log('当前使用摄像头:', camera.deviceName);
  }
});
```

> **说明：**
> 

> 调用 [setCurrentCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentCamera) 切换设备时，如果摄像头已开启，Atomicx 会自动停止当前设备的采集，并使用新设备重新开始采集，视频流会无缝切换。如果切换失败，会保持使用原设备。
> 

#### 移动端切换摄像头示例

移动端使用 [switchCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#switchCamera) 切换前后置摄像头。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { isFrontCamera, switchCamera } = useDeviceState();

// 判断是否为移动端
const isMobile = /Android|webOS|iPhone|iPad|iPod/i.test(navigator.userAgent);

// 切换前后置摄像头（仅移动端支持）
const toggleCamera = async () => {
  if (!isMobile) {
    console.warn('前后置摄像头切换仅在移动端支持');
    return;
  }
  
  // 切换到相反的摄像头
  await switchCamera({ isFrontCamera: !isFrontCamera.value });
  console.log(`已切换到${isFrontCamera.value ? '前置' : '后置'}摄像头`);
};
```

### 步骤3：处理摄像头采集错误

当摄像头采集发生错误时，您可以通过 [cameraLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraLastError) 属性获取错误信息。

> 注意：
> 

> 常见的摄像头错误包括 `NoSystemPermission` (无系统权限) 和 `OccupiedError`（设备被占用）。您应基于此属性向用户展示明确的错误提示。
> 

``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
const { cameraLastError } = useDeviceState();

// 监听摄像头错误
watch(cameraLastError, (error) => {
  switch (error) {
    case DeviceError.NoSystemPermission:
      console.error('摄像头权限被拒绝，请在系统设置中开启摄像头权限');
      break;
    case DeviceError.NoDeviceDetected:
      console.error('未检测到摄像头设备');
      break;
    case DeviceError.OccupiedError:
      console.error('摄像头被其他应用占用');
      break;
    case DeviceError.NotSupportCapture:
      console.error('当前浏览器不支持摄像头采集');
      break;
    default:
      break;
  }
});
```

## 实现麦克风管理

您可以通过 [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) 来控制本地麦克风的开关、静音、音量调节和实时音量显示，并监听采集状态和错误。

### 步骤1：开启/关闭麦克风

``` typescript
import { useDeviceState, DeviceStatus } from 'tuikit-atomicx-vue3/room';
const { microphoneStatus, openLocalMicrophone, closeLocalMicrophone } = useDeviceState();
const toggleMicrophone = async () => {
  if (microphoneStatus.value === DeviceStatus.On) {
    await closeLocalMicrophone();
  } else {
    await openLocalMicrophone();
  }
};
```

### 步骤2：获取/切换麦克风设备

#### 桌面端浏览器获取/切换麦克风示例

您可以通过 [microphoneList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneList) 获取所有可用的麦克风设备，使用 [setCurrentMicrophone](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentMicrophone) 方法切换当前使用的麦克风。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';

const { 
  microphoneList, 
  currentMicrophone, 
  getMicrophoneList, 
  setCurrentMicrophone 
} = useDeviceState();

// 获取麦克风列表
await getMicrophoneList();

// 遍历并显示可用麦克风
microphoneList.value.forEach((mic) => {
  console.log(`麦克风: ${mic.deviceName} (${mic.deviceId})`);
});

// 切换到指定麦克风
const switchMicrophone = async (deviceId: string) => {
  await setCurrentMicrophone({ deviceId });
};

// 监听当前麦克风变化
watch(currentMicrophone, (mic) => {
  if (mic) {
    console.log('当前使用麦克风:', mic.deviceName);
  }
});
```

### 步骤3：设置采集音量

您可以使用 [setCaptureVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCaptureVolume) 方法来调整本地麦克风的采集音量。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { captureVolume, setCaptureVolume } = useDeviceState();

// 设置采集音量为 50%
await setCaptureVolume(50);

// 监听采集音量变化
watch(captureVolume, (volume) => {
  console.log('当前采集音量:', volume);
});
```

### 步骤4：显示实时音量

[currentMicVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentMicVolume) 属性会实时更新本地麦克风的采集音量，可用于音量条的动态显示。
``` typescript
<template>
  <div class="mic-volume">
    <label>麦克风音量</label>
    <div class="volume-bar-container">
      <div 
        class="volume-bar" 
        :style="{ width: `${currentMicVolume}%` }" 
      />
    </div>
    <span>{{ currentMicVolume }}</span>
  </div>
</template>

<script setup lang="ts">
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { currentMicVolume } = useDeviceState();
</script>

<style scoped>
.volume-bar-container {
  width: 200px;
  height: 8px;
  background-color: #e0e0e0;
  border-radius: 4px;
  overflow: hidden;
}

.volume-bar {
  height: 100%;
  background-color: #4caf50;
  transition: width 0.1s ease;
}
</style>
```

### 步骤5：处理麦克风采集错误

当麦克风采集发生错误时，您可以通过 [microphoneLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneLastError) 数据获取错误信息。
``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
const { microphoneLastError } = useDeviceState();

// 监听麦克风错误
watch(microphoneLastError, (error) => {
  switch (error) {
    case DeviceError.NoSystemPermission:
      console.error('麦克风权限被拒绝，请在系统设置中开启麦克风权限');
      break;
    case DeviceError.NoDeviceDetected:
      console.error('未检测到麦克风设备');
      break;
    case DeviceError.OccupiedError:
      console.error('麦克风被其他应用占用');
      break;
    case DeviceError.NotSupportCapture:
      console.error('当前浏览器不支持麦克风采集');
      break;
    default:
      break;
  }
});
```

## 实现扬声器管理

扬声器（播放设备）主要涉及切换设备和设置播放音量。

### 步骤1：获取/切换扬声器设备

您可以通过 [speakerList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#speakerList) 获取所有可用的扬声器设备，使用 [setCurrentSpeaker](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentSpeaker) 方法切换当前使用的扬声器。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { 
  speakerList, 
  currentSpeaker, 
  getSpeakerList, 
  setCurrentSpeaker 
} = useDeviceState();

// 获取扬声器列表
await getSpeakerList();

// 遍历并显示可用扬声器
speakerList.value.forEach((speaker) => {
  console.log(`扬声器: ${speaker.deviceName} (${speaker.deviceId})`);
});

// 切换到指定扬声器
const switchSpeaker = async (deviceId: string) => {
  await setCurrentSpeaker({ deviceId });
};

// 监听当前扬声器变化
watch(currentSpeaker, (speaker) => {
  if (speaker) {
    console.log('当前使用扬声器:', speaker.deviceName);
  }
});
```

### 步骤2：设置播放音量

您可以使用 [setOutputVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setOutputVolume) 方法来调整扬声器的播放音量。
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { outputVolume, setOutputVolume } = useDeviceState();

// 设置播放音量为 60%
await setOutputVolume(60);

// 监听播放音量变化
watch(outputVolume, (volume) => {
  console.log('当前播放音量:', volume);
});
```

## 实现网络质量监控

使用 [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) 中的 [networkInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#networkInfo) 数据，您可以实时监控本地用户的网络质量。

> **说明：**
> 

> 仅当本地用户通过 [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom)加入房间并打开麦克风或摄像头时，[networkInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#networkInfo) 中才可以拿到有效数据。
> 

``` typescript
<template>
  <div class="network-indicator" @click="showDetails = !showDetails">
    <TUIIcon v-if="networkIcon" :icon="networkIcon" />
    <span class="network-text">{{ networkText }}</span>
    <div v-if="showDetails" class="network-details">
      <div>延迟: {{ networkInfo?.delay }}ms</div>
      <div>上行丢包: {{ networkInfo?.upLoss }}%</div>
      <div>下行丢包: {{ networkInfo?.downLoss }}%</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import {
  IconNetworkStability,
  IconNetworkFluctuation,
  IconNetworkLag,
  IconNetworkDisconnected,
  IconArrowStrokeUp,
} from '@tencentcloud/uikit-base-component-vue3';
import { useDeviceState, NetworkQuality } from 'tuikit-atomicx-vue3/room';

const { networkInfo } = useDeviceState();
const showDetails = ref(false);

// 网络质量图标
const networkIcon = computed(() => {
  if (!networkInfo.value) return '';
  switch (networkInfo.value.quality) {
    case NetworkQuality.Excellent:
    case NetworkQuality.Good:
      return IconNetworkStability;
    case NetworkQuality.Poor:
      return IconNetworkFluctuation;
    case NetworkQuality.Bad:
    case NetworkQuality.VeryBad:
      return IconNetworkLag;
    case NetworkQuality.Down:
      return IconNetworkDisconnected;
    default:
      return '';
  }
});

// 网络质量文本
const networkText = computed(() => {
  if (!networkInfo.value) return '未知';
  switch (networkInfo.value.quality) {
    case NetworkQuality.Excellent:
      return '优秀';
    case NetworkQuality.Good:
      return '良好';
    case NetworkQuality.Poor:
      return '一般';
    case NetworkQuality.Bad:
      return '差';
    case NetworkQuality.VeryBad:
      return '很差';
    case NetworkQuality.Down:
      return '断开';
    default:
      return '未知';
  }
});
</script>

<style scoped>

.network-indicator {
  position: relative;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
}

.network-details {
  position: absolute;
  top: 100%;
  left: 0;
  margin-top: 4px;
  padding: 8px;
  background: white;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.15);
  font-size: 12px;
  white-space: nowrap;
  z-index: 2;
}
</style>
```

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useDeviceState** | 包含音视频设备状态，音视频设备列表及操作接口。 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) |

## 常见问题

### 如果用户的电脑无麦克风或扬声器设备，是否可以进入音视频房间？

可以进入房间。 用户在缺少麦克风、扬声器或摄像头的情况下，依然可以调用 `joinRoom` 接口成功进入音视频房间。但在这种状态下，用户将面临以下交互异常。

**功能限制：**
- **无法开启媒体采集：**由于系统底层无法驱动相关硬件，调用 [openLocalMicrophone](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalMicrophone) 或 [openLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalCamera) 接口时将触发异步错误。此时，[microphoneLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneLastError) 或 [cameraLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraLastError) 属性将返回 `DeviceError.NoDeviceDetected`（未检测到设备）。

- **采集端受限：**无法通过本端设备采集并传输声音（需麦克风）或影像（需摄像头）。

- **播放端受限：**由于缺少扬声器设备，用户将无法听到房间内其他参会者的音频。

   **仍然可以使用的功能：**

   仅观看模式：用户依然可以观看远端参会者分享的视频流或屏幕共享画面。

### 如何实现设备热插拔检测？

Atomicx 已经内置了设备热插拔检测功能，当媒体设备插拔时，设备列表和当前设备数据会自动更新。
