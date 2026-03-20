> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# Virtual Background

Virtual background feature allows users to replace their real background with a custom image or apply blur effects during video calls or live streams. With this feature, users can effectively protect privacy, hide cluttered backgrounds, or showcase corporate image through specific backgrounds.

## Use Cases

- **Remote Work/Meetings:** When attending meetings at home or in public places, use background blur or virtual background to hide the real environment and protect personal privacy.

- **Online Education:** Teachers use unified teaching backgrounds to create a professional classroom atmosphere.

- **Brand Display:** Company employees use backgrounds with company logos in external meetings to enhance brand image.

- **Fun Interaction:** In social scenarios, users can change to interesting background images to increase interaction fun.

## Prerequisites

- **User Status:** User has completed login authentication through useLoginState, refer to Integration Overview, and is in **logged in** state.

- **Environment Dependencies:** The project has imported `tuikit-atomicx-vue3`.

- **Device Requirements:** A camera device must be available, and the user has granted camera access permission. If the user denies authorization, the virtual background feature will not be available.

- **Resource Dependencies:** Virtual background feature depends on AI model files. To ensure the browser can properly load and run these files, you need to complete the following steps:

  - Publish the `node_modules/trtc-sdk-v5/assets` directory to CDN or static resource server, see [SDK Documentation](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html) for details.

  - If choosing Solution 1: Quick Integration Using UI Components, when using the [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) component, you need to pass the `assetsPath` static resource address through props.

  - If choosing Solution 2: Custom Integration Using Underlying API, when using the [initVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#initVirtualBackground) interface, you need to pass the `assetsPath` static resource address.

## Implementing Virtual Background Feature

This feature provides two integration solutions, and you can choose the most suitable one based on your business requirements:

- **Solution 1 (Recommended):** Quick integration using UI components. Directly import the virtual background panel component ([VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel)) provided by `tuikit-atomicx-vue3`, and embed it into your application's dialog or sidebar with the lowest development cost.

- **Solution 2 (Advanced):** Custom integration using underlying API. Implement UI and interaction logic based on atomicx-core SDK API [useVirtualBackgroundState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) state hook with the highest flexibility.

   

   Virtual Background

## Solution 1: Quick Integration Using UI Components

`tuikit-atomicx-vue3` provides core settings panel components:

- [**VirtualBackgroundPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel)**:** Configuration panel for virtual background, including background blur, image selection, and preview functionality.

### Step 1: Import Component

```typescript
import { VirtualBackgroundPanel } from 'tuikit-atomicx-vue3/room';
```

### Step 2: Use Component

It is generally recommended to place [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) in a dialog or modal, triggered by clicking a button.

**The following example demonstrates how to implement a complete interaction to open virtual background settings by clicking a button:**

> **Note:**
> - Publish the `node_modules/trtc-sdk-v5/assets` directory to CDN or static resource server, see [SDK Documentation](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html) for details.
> - When using the [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) component, pass the `assetsPath` static resource address through props.
> - All TUIKit components must be wrapped inside UIKitProvider, otherwise they will not be able to obtain context dependencies, resulting in style abnormalities.

```typescript
<template>
  <UIKitProvider theme="light" language="en-US">
    <div class="toolbar-container">
      <!-- 1. Trigger button -->
      <button @click="openSettings">Set Virtual Background</button>

      <!-- 2. Settings dialog -->
      <div v-if="showSettings" class="modal-mask">
        <div class="modal-content">
          <div class="modal-header">
            <span>Virtual Background</span>
            <button @click="showSettings = false">Close</button>
          </div>
          
          <div class="modal-body">
            <!-- 3. Import panel component -->
            <!-- @close event: triggered when user clicks close button inside panel (if any) -->
            <VirtualBackgroundPanel assetsPath='' @close="showSettings = false" />
          </div>
        </div>
      </div>
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
import { VirtualBackgroundPanel, useVirtualBackgroundState, useDeviceState } from 'tuikit-atomicx-vue3/room';

const showSettings = ref(false);
const { isSupported } = useVirtualBackgroundState();
const { cameraList } = useDeviceState();

const openSettings = () => {
  // 1. Check if browser supports virtual background
  if (!isSupported()) {
    alert('Current browser does not support virtual background');
    return;
  }
  
  // 2. Check if camera is available
  if (cameraList.value.length === 0) {
    alert('No camera detected, unable to set virtual background');
    return;
  }

  // 3. Open settings panel
  showSettings.value = true;
};
</script>

<style scoped>
.modal-mask{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.5);display:flex;justify-content:center;align-items:center;z-index:1000}.modal-content{background:white;width:600px;border-radius:8px;overflow:hidden}.modal-header{padding:16px;display:flex;justify-content:space-between;border-bottom:1px solid #eee}
</style>
```

## Solution 2: Custom Integration Using Underlying API

This section mainly introduces how to implement virtual background through atomicx-core SDK API [**useVirtualBackgroundState**](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) interface.

### Step 1: Initialization and Detection

Before calling the virtual background feature, you must first check if the browser supports it and initialize the resource path.

> **Note:**
> - Publish the node_modules/trtc-sdk-v5/assets directory to CDN or static resource server, see [SDK Documentation](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html) for details.
> - When using the [initVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#initVirtualBackground) interface, pass the assetsPath static resource address.
> - Virtual background feature depends on camera devices. You can use [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) to check device status.
> - [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList): List of currently available camera devices. If the list is empty, it means no camera is detected or camera permission is denied.

```typescript
import { onMounted } from 'vue';
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { isSupported, initVirtualBackground } = useVirtualBackgroundState();

onMounted(async () => {
  // 1. Check browser compatibility
  if (!isSupported()) {
    console.warn('Current browser does not support virtual background');
    return;
  }

  // 2. Initialize resources
  try {
    await initVirtualBackground({ assetsPath: './assets' });
    console.log('Virtual background initialized');
  } catch (error) {
    console.error('Initialization failed', error);
  }
});
```

### Step 2: Set Preview

Before the user confirms applying the background, a preview effect is usually required. Using the [setVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setVirtualBackground) method can set the preview effect, which will not be applied to the video stream visible to remote users.

> **Note:**
> 

> Calling `setVirtualBackground` only takes effect in the local preview screen, remote users will not see the virtual background effect. You need to call `saveVirtualBackground` to synchronize it to the remote video stream.
> 

```typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { setVirtualBackground } = useVirtualBackgroundState();

// Enable background blur preview
const previewBlur = async () => {
  await setVirtualBackground({
    enable: true,
    type: 'blur',
    level: 0.5 // Blur level, optional
  });
};

// Enable background image preview
const previewImage = async (imageUrl: string) => {
  await setVirtualBackground({
    enable: true,
    type: 'image',
    source: imageUrl // Image URL, supports local or network path
  });
};
```

### Step 3: Save and Apply

After the user confirms the preview effect is satisfactory, call [saveVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#saveVirtualBackground) to actually apply the configuration to the current video stream, at which point remote users will see the effect.

```typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { saveVirtualBackground } = useVirtualBackgroundState();

const confirmSettings = async () => {
  try {
    await saveVirtualBackground();
    console.log('Virtual background applied');
  } catch (error) {
    console.error('Save failed', error);
  }
};
```

### Step 4: Disable Virtual Background

If you need to disable virtual background and restore the real background, you can set its `enable` property to `false` and save.

```typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { setVirtualBackground, saveVirtualBackground } = useVirtualBackgroundState();

const disableVirtualBackground = async () => {
  // 1. Set to disabled state
  await setVirtualBackground({ enable: false });
  // 2. Save and apply
  await saveVirtualBackground();
};
```

## Development Notes

- **Resource File Path:** The `assetsPath` in `initVirtualBackground` must correctly point to the directory of AI model files. If the path is incorrect, virtual background will not start. Please refer to [SDK Documentation](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html) to obtain the resource file package.

- **Performance Consumption:** Enabling virtual background consumes high CPU and GPU resources. On low-end devices, it may cause video frame drops or overheating. It is recommended to prompt users to disable it when device performance is insufficient.

- **Browser Compatibility:** Not all browsers support virtual background (depending on WebAssembly and SIMD features). Be sure to call `isSupported()` for checking before UI rendering.

- **Image Format:** Custom background images are recommended to use `jpg` or `png` format, and resolution should not be too large (recommended within 1920x1080) to avoid affecting loading speed and memory consumption.

## FAQ

### **Running Demo in Chrome shows flipped and laggy screen?**

Virtual background plugin uses GPU acceleration. You need to find and enable hardware acceleration mode in browser settings. You can copy `chrome://settings/system` to the browser address bar and enable hardware acceleration mode.

### **Why does the computer overheat or the fan runs at high speed after enabling virtual background?**

Virtual background feature requires real-time recognition of human contours and image processing, which has certain requirements for CPU and GPU computing power. It is recommended to disable this feature when not needed, or avoid enabling it on low-end devices.

### **Initialization failed with error "assets load failed", what to do?**

Please check if the `assetsPath` passed in `initVirtualBackground` is correct. Ensure that the path contains the necessary AI model files and can be directly accessed through the browser. If it is a cross-domain resource, CORS policy needs to be configured.

### **Does virtual background support mobile browsers?**

Due to performance limitations of mobile browsers and differences in WebAssembly support, the experience may not be as stable as desktop. It is recommended to use it primarily on desktop Chrome (recommended 94+), Edge, and other mainstream browsers, and perform proper compatibility checks with `isSupported()`.

### **Unable to use virtual background in local development?**

WebRTC-related features usually require running in an HTTPS environment, or `localhost` / `127.0.0.1`. Please ensure that your development environment meets secure context requirements.

## **Sample Project**

Tencent Cloud provides a sample project [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) on GitHub, which you can refer to implement complete RoomKit features.

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useVirtualBackgroundState** | Virtual background management | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) |
