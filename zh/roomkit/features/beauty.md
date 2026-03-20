> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

基础美颜功能允许用户在视频通话或直播中，对人像进行磨皮、美白和红润处理。

## 适用场景

- **视频会议：** 弱化皮肤瑕疵，提亮肤色，使参会者看起来更加专业和精神。

- **在线直播：** 主播通过美颜功能提升画面观感，吸引更多观众。

- **在线教育：** 教师和学生保持整洁自然的形象，营造良好的课堂氛围。

## 前提条件

- **用户状态：** 用户已经通过 useLoginState 完成登录鉴权，参考 接入概览，处于 **已登录** 状态。

- **环境依赖：** 项目已引入 `tuikit-atomicx-vue3`。

- **设备要求：** 需要有可用的摄像头设备，并且用户已授权摄像头访问权限。如果用户拒绝授权，虚拟背景功能将无法使用。

## 实现基础美颜功能

本功能提供两种集成方案，您可以根据业务需求选择最适合的一种：
- **方案一（推荐）：** 使用 UI 组件快速集成。直接引入 `tuikit-atomicx-vue3` 提供的美颜面板组件（[FreeBeautyPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/FreeBeautyPanel)），将其嵌入到您的应用弹窗中，开发成本最低。

- **方案二（高级）：** 使用底层 API 自定义集成。基于 atomicx-core SDK API [useFreeBeautyState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) 状态钩子自行实现 UI 和交互逻辑，灵活度最高。

   

## 方案一：使用 UI 组件快速集成

`tuikit-atomicx-vue3` 提供了核心美颜设置面板组件：
- [**FreeBeautyPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/FreeBeautyPanel)**：** 基础美颜配置面板，包含磨皮、美白、红润的参数调节功能。

### 步骤1：引入组件

``` typescript
import { FreeBeautyPanel } from 'tuikit-atomicx-vue3/room';
```

### 步骤2：使用组件

通常建议将 `FreeBeautyPanel` 放置在一个弹窗（Dialog）或模态框（Modal）中，通过按钮点击触发显示。

**以下示例展示了如何实现一个点击按钮弹出美颜设置的完整交互：**

> **注意：**
> 

> 所有 TUIKit 组件必须包裹在 UIKitProvider 内部，否则将无法获取上下文依赖，导致样式异常。
> 

``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="toolbar-container">
      <!-- 1. 触发按钮 -->
      <button @click="openBeautySettings">设置美颜</button>

      <!-- 2. 设置弹窗 -->
      <div v-if="showSettings" class="modal-mask">
        <div class="modal-content">
          <div class="modal-header">
            <span>基础美颜</span>
            <button @click="showSettings = false">关闭</button>
          </div>
          
          <div class="modal-body">
            <!-- 3. 引入面板组件 -->
            <!-- @close 事件：当用户点击面板内的关闭按钮（如有）时触发 -->
            <FreeBeautyPanel @close="showSettings = false" />
          </div>
        </div>
      </div>
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
import { FreeBeautyPanel, useDeviceState } from 'tuikit-atomicx-vue3/room';

const showSettings = ref(false);
const { cameraList } = useDeviceState();

const openBeautySettings = () => {
  // 1. 检查是否有可用摄像头
  if (cameraList.value.length === 0) {
    alert('未检测到摄像头，无法设置美颜');
    return;
  }

  // 2. 打开设置面板
  showSettings.value = true;
};
</script>

<style scoped>
.modal-mask {
  position: fixed; top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(0,0,0,0.5); display: flex; justify-content: center; align-items: center; z-index: 1000;
}
.modal-content { background: white; width: 600px; border-radius: 8px; overflow: hidden; }
.modal-header { padding: 16px; display: flex; justify-content: space-between; border-bottom: 1px solid #eee; }
.modal-body { padding: 16px; }
</style>
```

该组件内部逻辑如下：
- **显示面板：** 用户点击按钮后弹出包含 `FreeBeautyPanel` 的弹窗。

- **调节参数：** 用户拖动面板内的滑块调节磨皮、美白、红润参数。

- **实时预览：** 调节过程中会实时调用预览接口预览美颜效果。

- **保存生效：** 关闭面板（触发 `@close` 事件）时自动保存当前设置，使其对远端生效。

## 方案二：使用底层 API 自定义集成

本节主要介绍如何通过 atomicx-core SDK API [useFreeBeautyState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) 接口来实现基础美颜。

### 步骤1：设置预览

在用户调节美颜参数（例如拖动滑块）时，使用 [setFreeBeauty](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setFreeBeauty) 进行实时预览。此时效果仅本地可见，不会传输给远端用户。

> **注意：**
> 

> 参数取值范围为 0-100。
> 

``` typescript
import { useFreeBeautyState } from 'tuikit-atomicx-vue3/room';

const { setFreeBeauty, beautyConfig } = useFreeBeautyState();

// 示例：调节美颜参数
const updatePreview = async (beauty: number, white: number, ruddy: number) => {
  // beautyConfig 中的值会自动更新，这里演示如何手动设置预览
  await setFreeBeauty({
    beautyLevel: beauty,      // 磨皮 (0-100)
    whitenessLevel: white,    // 美白 (0-100)
    ruddinessLevel: ruddy     // 红润 (0-100)
  });
};
```

### 步骤2：保存并生效

用户确认预览效果满意后，调用 [saveBeautySetting](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#saveBeautySetting) 将当前配置应用到视频流中。此时远端用户可以看到美颜效果。

> **注意：**
> 

> 虚拟背景功能依赖摄像头设备。可使用 [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) 检查设备状态。
> - [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList)：当前可用的摄像头设备列表，如果列表为空，说明未检测到摄像头或摄像头权限被禁止。

``` typescript
import { useFreeBeautyState } from 'tuikit-atomicx-vue3/room';

const { saveBeautySetting } = useFreeBeautyState();

const applyBeauty = async () => {
  // 保存当前预览的设置，使其对远端生效
  await saveBeautySetting();
  console.log('美颜已生效');
};
```

### 步骤3：关闭美颜效果

若需关闭美颜，只需将所有参数设置为 0 并保存。
``` typescript
const closeBeauty = async () => {
  // 1. 将所有参数设为 0 并预览
  await setFreeBeauty({
    beautyLevel: 0,
    whitenessLevel: 0,
    ruddinessLevel: 0
  });
  
  // 2. 保存生效
  await saveBeautySetting();
};
```

## 开发注意事项

- **参数范围：**`setFreeBeauty` 接收的参数（`beautyLevel`, `whitenessLevel`, `ruddinessLevel`）范围均为 0-100。

- **预览与生效：** 为了提供更好的用户体验，美颜调节分为**“预览”**和**“生效”**两个阶段。`setFreeBeauty` 仅用于本地预览，必须调用 `saveBeautySetting` 才会正式调用 `trtcCloud.setBeautyStyle` 应用到推流中。

- **设备依赖：** 美颜功能依赖于视频流，因此必须在摄像头开启的状态下才能看到效果。如果未检测到摄像头，建议禁用美颜入口。

- **性能消耗：** 开启美颜会增加一定的 CPU 和 GPU 负载，建议在低端设备上引导用户适度使用。

## 常见问题

### **为什么调节美颜后远端看不到效果？**

请确认是否调用了 `saveBeautySetting()`。`setFreeBeauty()` 仅用于本地预览，只有保存后才会应用到推流视频中。

### **没有摄像头时能设置美颜吗？**

不能。美颜功能是对视频流进行处理，如果没有采集到视频画面，美颜无法生效。建议在无摄像头时隐藏或禁用美颜入口。

### **离开房间或关闭摄像头后，需要手动重置美颜吗？**

不需要。`useFreeBeautyState` 内部已实现状态自动管理：
- 摄像头切换： 关闭摄像头时美颜暂停，重新开启后会自动恢复上次保存的设置。

- 进出房间： 退出房间时会自动重置所有美颜参数，无需开发者手动清理。

## **示例项目**

腾讯云在 GitHub 上提供了示例项目 [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) ，您可以参考以实现完整的 RoomKit 功能。

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useFreeBeautyState** | 免费美颜功能管理 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) |
