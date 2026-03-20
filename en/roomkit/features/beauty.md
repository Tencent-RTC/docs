> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

The basic beauty feature allows users to apply skin smoothing, whitening, and ruddiness adjustments to portraits during video calls or live streaming.

## Use Cases

- **Video Conferencing:** Minimize skin blemishes, brighten skin tone, making participants look more professional and energetic.

- **Online Live Streaming:** Hosts use beauty features to enhance visual appeal and attract more viewers.

- **Online Education:** Teachers and students maintain a clean and natural appearance, creating a good classroom atmosphere.

## Prerequisites

- **User Status:** The user has completed login authentication through useLoginState, refer to Integration Overview, and is in **logged in** status.

- **Environment Dependencies:** The project has imported `tuikit-atomicx-vue3`.

- **Device Requirements:** A working camera device is required, and the user has authorized camera access. If the user denies authorization, the virtual background feature will not be available.

## Implementing Basic Beauty Feature

This feature provides two integration solutions. You can choose the most suitable one according to business needs:
- **Solution 1 (Recommended):** Quick integration using UI components. Directly import the beauty panel component ([FreeBeautyPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/FreeBeautyPanel)) provided by `tuikit-atomicx-vue3`, embed it into your application dialog, with the lowest development cost.

- **Solution 2 (Advanced):** Custom integration using underlying API. Based on atomicx-core SDK API [useFreeBeautyState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) state hook to implement UI and interaction logic yourself, with the highest flexibility.

   

## Solution 1: Quick Integration Using UI Components

`tuikit-atomicx-vue3` provides a core beauty settings panel component:
- [**FreeBeautyPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/FreeBeautyPanel)**:** Basic beauty configuration panel, including parameter adjustment functions for skin smoothing, whitening, and ruddiness.

### Step 1: Import Component

``` typescript
import { FreeBeautyPanel } from 'tuikit-atomicx-vue3/room';
```

### Step 2: Use Component

It is generally recommended to place `FreeBeautyPanel` in a dialog or modal, triggered by button click.

**The following example shows how to implement a complete interaction for clicking a button to open beauty settings:**

> **Note:**
> 

> All TUIKit components must be wrapped inside UIKitProvider, otherwise they will not be able to obtain context dependencies, causing style anomalies.
> 

``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="toolbar-container">
      <!-- 1. Trigger button -->
      <button @click="openBeautySettings">Beauty Settings</button>

      <!-- 2. Settings dialog -->
      <div v-if="showSettings" class="modal-mask">
        <div class="modal-content">
          <div class="modal-header">
            <span>Basic Beauty</span>
            <button @click="showSettings = false">Close</button>
          </div>
          
          <div class="modal-body">
            <!-- 3. Import panel component -->
            <!-- @close event: Triggered when user clicks the close button (if any) inside the panel -->
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
  // 1. Check if there is an available camera
  if (cameraList.value.length === 0) {
    alert('No camera detected, cannot set beauty effects');
    return;
  }

  // 2. Open settings panel
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

The internal logic of this component is as follows:
- **Display Panel:** After users click the button, a dialog containing `FreeBeautyPanel` pops up.

- **Adjust Parameters:** Users drag sliders inside the panel to adjust skin smoothing, whitening, and ruddiness parameters.

- **Real-time Preview:** During adjustment, the preview interface is called in real-time to preview beauty effects.

- **Save and Apply:** When closing the panel (triggering `@close` event), the current settings are automatically saved to take effect for remote users.

## Solution 2: Custom Integration Using Underlying API

This section mainly introduces how to implement basic beauty through atomicx-core SDK API [useFreeBeautyState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) interface.

### Step 1: Set Preview

When users adjust beauty parameters (e.g., dragging sliders), use [setFreeBeauty](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setFreeBeauty) for real-time preview. At this time, the effect is only visible locally and will not be transmitted to remote users.

> **Note:**
> 

> Parameter values range from 0-100.
> 

``` typescript
import { useFreeBeautyState } from 'tuikit-atomicx-vue3/room';

const { setFreeBeauty, beautyConfig } = useFreeBeautyState();

// Example: Adjust beauty parameters
const updatePreview = async (beauty: number, white: number, ruddy: number) => {
  // The values in beautyConfig will update automatically, here's how to manually set preview
  await setFreeBeauty({
    beautyLevel: beauty,      // Skin smoothing (0-100)
    whitenessLevel: white,    // Whitening (0-100)
    ruddinessLevel: ruddy     // Ruddiness (0-100)
  });
};
```

### Step 2: Save and Apply

After users confirm the preview effect is satisfactory, call [saveBeautySetting](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#saveBeautySetting) to apply the current configuration to the video stream. At this time, remote users can see the beauty effect.

> **Note:**
> 

> The virtual background feature depends on camera devices. You can use [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) to check device status.
> - [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList): List of currently available camera devices. If the list is empty, it means no camera was detected or camera permission was denied.

``` typescript
import { useFreeBeautyState } from 'tuikit-atomicx-vue3/room';

const { saveBeautySetting } = useFreeBeautyState();

const applyBeauty = async () => {
  // Save the currently previewed settings to take effect for remote users
  await saveBeautySetting();
  console.log('Beauty effects applied');
};
```

### Step 3: Disable Beauty Effects

To disable beauty, simply set all parameters to 0 and save.
``` typescript
const closeBeauty = async () => {
  // 1. Set all parameters to 0 and preview
  await setFreeBeauty({
    beautyLevel: 0,
    whitenessLevel: 0,
    ruddinessLevel: 0
  });
  
  // 2. Save to apply
  await saveBeautySetting();
};
```

## Development Notes

- **Parameter Range:**`setFreeBeauty` accepts parameters (`beautyLevel`, `whitenessLevel`, `ruddinessLevel`) all ranging from 0-100.

- **Preview and Apply:** To provide better user experience, beauty adjustment is divided into **"preview"** and **"apply"** stages. `setFreeBeauty` is only used for local preview, and you must call `saveBeautySetting` to formally apply by calling `trtcCloud.setBeautyStyle` to the stream.

- **Device Dependencies:** Beauty features depend on video streams, so effects can only be seen when the camera is enabled. If no camera is detected, it is recommended to disable the beauty entry point.

- **Performance Consumption:** Enabling beauty will increase CPU and GPU load. It is recommended to guide users to use moderately on low-end devices.

## Common Questions

### **Why can't remote users see the effect after adjusting beauty?**

Please confirm if `saveBeautySetting()` was called. `setFreeBeauty()` is only used for local preview, and effects will only be applied to the streaming video after saving.

### **Can beauty be set without a camera?**

No. Beauty features process video streams. If no video is captured, beauty cannot take effect. It is recommended to hide or disable the beauty entry point when there is no camera.

### **Do I need to manually reset beauty after leaving the room or turning off the camera?**

No. `useFreeBeautyState` has internally implemented automatic state management:
- Camera switch: Beauty pauses when camera is turned off, and automatically restores the last saved settings when reopened.

- Entering/exiting rooms: All beauty parameters are automatically reset when exiting a room, no need for developers to clean up manually.

## **Sample Project**

Tencent Cloud provides a sample project [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) on GitHub, which you can refer to for implementing complete RoomKit functionality.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useFreeBeautyState** | Free beauty feature management | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-FreeBeautyState) |
