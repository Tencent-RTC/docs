> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本文档指导您如何使用 Atomicx 实现屏幕分享能力，并根据屏幕分享状态处理房间内交互。

## 前提条件

- 用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

- 用户已经通过 [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 进入房间，请参考 房间管理。

- 若在 `iframe` 中集成，需在标签中声明权限。

   ``` typescript
   <iframe allow="display-capture; fullscreen;"></iframe>
   ```

## 实现屏幕分享功能

### 步骤1：开始屏幕分享

使用 [startScreenShare](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#startScreenShare) 方法开始屏幕分享。在 Web 环境下，您将看到浏览器的屏幕选择器，可以选择分享整个屏幕、应用窗口或浏览器标签页。

``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { startScreenShare } = useDeviceState();

// 尝试开始屏幕分享，并尝试分享系统音频
const handleStartShare = async () => {
  try {
    await startScreenShare({ screenAudio: true });
  } catch (error) {
    // 处理用户拒绝或浏览器不支持等错误
    console.error('开始屏幕分享失败:', error);
  }
};
```

### 步骤2：分享系统音频

通过设置 `screenAudio: true` 参数，可以在屏幕分享时同时分享系统音频。这在分享视频、演示、音乐等包含音频内容时非常有用。

``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { startScreenShare } = useDeviceState();

// 开始屏幕分享并分享系统音频
const handleStartScreenShareWithAudio = async () => {
  try {
    await startScreenShare({ screenAudio: true });
    console.log('屏幕分享（含音频）已开始');
  } catch (error) {
    console.error('开始屏幕分享失败:', error);
  }
};
```

**浏览器兼容性说明：**
| 浏览器 | 系统音频分享支持 | 备注 |
| --- | --- | --- |
| Chrome 74+ | 支持 | 需要在选择器中勾选**分享音频** |
| Edge 79+ | 支持 | 基于 Chromium，体验同 Chrome |
| Firefox | 不支持 | 暂不支持系统音频分享 |
| Safari | 不支持 | 暂不支持系统音频分享 |

> **注意：**
> 

> 系统音频分享需要浏览器支持，且用户在屏幕选择器中勾选**分享音频**选项。如果浏览器不支持系统音频分享，`screenAudio` 参数会被忽略，但屏幕画面分享功能不受影响，可以正常使用。
>

### 步骤3：停止屏幕分享

用户可以通过以下几种方式停止屏幕分享：
1. **调用 **[**stopScreenShare()**](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#stopScreenShare)** 接口**

   ``` typescript
   import { useDeviceState, DeviceStatus } from 'tuikit-atomicx-vue3/room';
   const { screenStatus, stopScreenShare } = useDeviceState();
   
   // 停止屏幕分享
   const handleStopScreenShare = async () => {
     try {
       await stopScreenShare();
       console.log('屏幕分享已停止');
     } catch (error) {
       console.error('停止屏幕分享失败:', error);
     }
   };
   ```
2. **点击浏览器原生的"停止分享"按钮**

   

3. **关闭被分享的窗口或标签页**
   

   > **说明：**
   > 

   > 用户使用任意一种方式停止屏幕分享时，Atomicx 都会自动更新 [screenStatus](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#screenStatus) 的状态。
   >

### 步骤4：权限控制与状态监听

#### **设置屏幕分享权限**

您可以通过 [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) 提供的成员管理接口控制房间内的屏幕管理权限。
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { disableAllDevices } = useRoomParticipantState();

// 限制仅房主/管理员可以发起屏幕分享
async function handleDisableAllScreen() {
  await disableAllDevices({
    deviceType: DeviceType.Screen,
    disable: true,
  });
}

// 允许房间内所有用户发起屏幕分享
async function handleEnableAllScreen() {
  await disableAllDevices({
    deviceType: DeviceType.Screen,
    disable: false,
  });
}
```

#### **自动布局切换**

当检测到房间中有用户开始屏幕分享时，建议切换到侧边栏布局（`RoomLayoutTemplate.SidebarLayout`），将分享画面放大显示，其他参会者画面显示在侧边栏。
``` typescript
<template>
  <div class="room-container">
    <RoomView :layout-template="layoutTemplate">
      <template #participantViewUI="{ participant, streamType }">
        <ParticipantViewUI 
          :participant="participant" 
          :stream-type="streamType" 
        />
      </template>
    </RoomView>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';
import { 
  RoomView, 
  RoomLayoutTemplate,
  useRoomParticipantState
} from 'tuikit-atomicx-vue3/room';

const layoutTemplate = ref(RoomLayoutTemplate.GridLayout);
const { participantWithScreen } = useRoomParticipantState();

// 监听屏幕分享状态，自动切换布局
watch(participantWithScreen, (participant) => {
  if (participant) {
    // 有人分享屏幕，切换到侧边栏布局
    layoutTemplate.value = RoomLayoutTemplate.SidebarLayout;
  } else {
    // 没有人分享屏幕，切换回网格布局
    layoutTemplate.value = RoomLayoutTemplate.GridLayout;
  }
});
</script>
```

#### **限制仅一人屏幕分享**

在房间中应该只允许一个人分享屏幕，当有人正在分享时，禁用其他人的分享按钮：
``` typescript
<script setup>
import { computed } from 'vue';
import { useRoomParticipantState } from 'tuikit-atomicx-vue3/room';

const { participantWithScreen, localParticipant } = useRoomParticipantState();

const isOtherSharing = computed(() => {
  const sharer = participantWithScreen.value;
  if (!sharer) return false;
  return sharer.userId !== localParticipant.value?.userId;
});

const canStartScreenShare = computed(() => {
  return !isOtherSharing.value;
});
</script>

// 在模板中使用
<template>
  <button :disabled="!canStartScreenShare" @click="handleStartShare">
    开始屏幕分享
  </button>
</template>
```

### 步骤5：处理屏幕分享错误

当屏幕分享发生错误时，根据错误信息提示用户做出相应的操作。

**代码示例：**
``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
import { TUIToast } from '@tencentcloud/uikit-base-component-vue3';

const { startScreenShare } = useDeviceState();

try {
  await startScreenShare();
} catch (error: any) {
  let message = '';
  switch (error.name) {
    case 'NotReadableError':
      message = '系统禁止当前浏览器访问屏幕内容，请启用屏幕分享权限。';
      break;
    case 'NotAllowedError':
      if (error.message.includes('Permission denied by system')) {
        message = '系统禁止当前浏览器访问屏幕内容，请启用屏幕分享权限。';
      } else {
        message = '用户取消屏幕分享';
      }
      break;
    default:
      message = '屏幕分享过程中发生未知错误，请重试。';
      break;
  }
  TUIToast.warning({
    message,
  });
}
```

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useDeviceState** | 包含音视频设备状态，音视频设备列表及操作接口。 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) |
| **useRoomParticipantState** | 包含房间内用户数据，用户管理接口。 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |

## 常见问题

### 为什么系统音频分享不生效？

- **平台限制：**音频分享目前仅在 Chrome、Edge 等现代浏览器上支持。

- **操作细节：**用户必须在浏览器弹出的屏幕选择器中，手动选择**分享音频**。

- **系统限制：**部分操作系统不支持分享特定**应用窗口**的音频，建议优先选择**整个屏幕**或**浏览器标签页**。

### 如何感知用户通过点击浏览器的“停止分享”按钮后结束屏幕分享？

Atomicx 会自动监听浏览器的停止分享事件，并更新 `screenStatus` 状态。您只需要监听 `screenStatus` 的变化即可：
``` typescript
watch(screenStatus, (newStatus, oldStatus) => {
  if (oldStatus === DeviceStatus.On && newStatus === DeviceStatus.Off) {
    console.log('用户已停止屏幕分享。');
  }
});
```

### 移动端浏览器是否支持屏幕分享？

仅桌面端浏览器支持屏幕分享，移动端浏览器不支持屏幕分享能力。

### 为什么用户开启屏幕分享音频后，其他参会者看到该用户的麦克风状态显示为“开启”？

这是符合预期的系统行为，原因如下：

**技术原理：**`microphoneStatus`表示当前设备是否正在对外传输音频信号。当开启屏幕分享并**勾选采集系统音频**时，Atomicx SDK 会启动一条音频传输通道。由于远端接收到的是连续的音频数据流，系统无法区分音频来源是物理麦克风还是系统播放声音，因此会将音频采集状态统一标识为 `DeviceStatus.On`。

**隐私说明：**请放心，这并不代表您的麦克风已经在采集说话声音。 麦克风声音是否在推送仅和麦克风控制接口（ `openLocalMicrophone` 和 `unmuteLocalMicrophone` ）有关，与屏幕分享音频采集是独立的两个通道。即使屏幕分享音频正在传输，只要未调用麦克风相关接口，您的麦克风声音不会被采集和推送。
