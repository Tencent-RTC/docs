> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

虚拟背景功能允许用户在视频通话或直播中，将真实背景替换为自定义图片或进行模糊处理。通过该功能，用户可以有效保护隐私，隐藏杂乱的背景，或者通过特定背景展示企业形象。

## 适用场景

- **远程办公/会议：** 在家中或公共场所参会时，使用背景虚化或虚拟背景隐藏真实环境，保护个人隐私。

- **在线教育：** 教师使用统一的教学背景，营造专业的课堂氛围。

- **品牌展示：** 企业员工在对外会议中使用带有公司 Logo 的背景图，提升品牌形象。

- **趣味互动：** 社交场景下，用户更换有趣的背景图片，增加互动乐趣。

## 前提条件

- **用户状态：** 用户已经通过 useLoginState 完成登录鉴权，参考 接入概览，处于**已登录**状态。

- **环境依赖：** 项目已引入 `tuikit-atomicx-vue3`。

- **设备要求：** 需要有可用的摄像头设备，并且用户已授权摄像头访问权限。如果用户拒绝授权，虚拟背景功能将无法使用。

- **资源依赖：** 虚拟背景功能依赖 AI 模型文件，为保证浏览器可以正常加载和运行这些文件，您需要完成以下步骤。

  - 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中，详情可见 [SDK 文档](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html)。

  - 若选择 方案一：使用 UI 组件快速集成，在使用 [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) 组件时，需要通过 props 传入 `assetsPath` 静态资源地址。

  - 若选择 方案二：使用底层 API 自定义集成，在使用 [initVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#initVirtualBackground) 接口时，需要传入 `assetsPath` 静态资源地址。

## 实现虚拟背景功能

本功能提供两种集成方案，您可以根据业务需求选择最适合的一种：
- **方案一（推荐）：** 使用 UI 组件快速集成。直接引入 `tuikit-atomicx-vue3` 提供的虚拟背景面板组件（[VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel)），将其嵌入到您的应用弹窗或侧边栏中，开发成本最低。

- **方案二（高级）：** 使用底层 API 自定义集成。基于 atomicx-core SDK API [useVirtualBackgroundState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) 状态钩子自行实现 UI 和交互逻辑，灵活度最高。

   

   虚拟背景

## 方案一：使用 UI 组件快速集成

`tuikit-atomicx-vue3` 提供了核心设置面板组件：
- [**VirtualBackgroundPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel)**：** 虚拟背景的配置面板，包含背景虚化、图片选择和预览功能。

### 步骤1：引入组件

``` typescript
import { VirtualBackgroundPanel } from 'tuikit-atomicx-vue3/room';
```

### 步骤2：使用组件

通常建议将 [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) 放置在一个弹窗（Dialog）或模态框（Modal）中，通过按钮点击触发显示。

**以下示例展示了如何实现一个点击按钮弹出虚拟背景设置的完整交互：**

> **注意：**
> - 将 `node_modules/trtc-sdk-v5/assets` 目录发布至 CDN 或者静态资源服务器中，详情可见 [SDK 文档](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html)。
> - 在使用 [VirtualBackgroundPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/tree/main/packages/tuikit-atomicx-vue3/src/components/VirtualBackgroundPanel) 组件时，通过 props 传入 `assetsPath` 静态资源地址。
> - 所有 TUIKit 组件必须包裹在 UIKitProvider 内部，否则将无法获取上下文依赖，导致样式异常。

``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="toolbar-container">
      <!-- 1. 触发按钮 -->
      <button @click="openSettings">设置虚拟背景</button>

      <!-- 2. 设置弹窗 -->
      <div v-if="showSettings" class="modal-mask">
        <div class="modal-content">
          <div class="modal-header">
            <span>虚拟背景</span>
            <button @click="showSettings = false">关闭</button>
          </div>
          
          <div class="modal-body">
            <!-- 3. 引入面板组件 -->
            <!-- @close 事件：当用户点击面板内的关闭按钮（如有）时触发 -->
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
  // 1. 检查浏览器是否支持虚拟背景
  if (!isSupported()) {
    alert('当前浏览器不支持虚拟背景');
    return;
  }
  
  // 2. 检查是否有可用摄像头
  if (cameraList.value.length === 0) {
    alert('未检测到摄像头，无法设置虚拟背景');
    return;
  }

  // 3. 打开设置面板
  showSettings.value = true;
};
</script>

<style scoped>
.modal-mask{position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.5);display:flex;justify-content:center;align-items:center;z-index:1000}.modal-content{background:white;width:600px;border-radius:8px;overflow:hidden}.modal-header{padding:16px;display:flex;justify-content:space-between;border-bottom:1px solid #eee}
</style>
```

## 方案二：使用底层 API 自定义集成

本节主要介绍如何通过 atomicx-core SDK API [**useVirtualBackgroundState**](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) 接口来实现虚拟背景。

### 步骤1：初始化与检测

在调用虚拟背景功能前，必须先检查浏览器是否支持，并初始化资源路径。

> **注意：**
> - 将 node_modules/trtc-sdk-v5/assets 目录发布至 CDN 或者静态资源服务器中，详情可见 [SDK 文档](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html)。
> - 在使用 [initVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#initVirtualBackground) 接口时，传入 assetsPath 静态资源地址。
> - 虚拟背景功能依赖摄像头设备。可使用 [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) 检查设备状态。
> - [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList)：当前可用的摄像头设备列表，如果列表为空，说明未检测到摄像头或摄像头权限被禁止。

``` typescript
import { onMounted } from 'vue';
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { isSupported, initVirtualBackground } = useVirtualBackgroundState();

onMounted(async () => {
  // 1. 检查浏览器兼容性
  if (!isSupported()) {
    console.warn('当前浏览器不支持虚拟背景');
    return;
  }

  // 2. 初始化资源
  try {
    await initVirtualBackground({ assetsPath: './assets' });
    console.log('虚拟背景初始化完成');
  } catch (error) {
    console.error('初始化失败', error);
  }
});
```

### 步骤2：设置预览

在用户确认应用背景前，通常需要先预览效果。使用 [setVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setVirtualBackground) 方法可以设置预览效果，此时不会应用到远端可见的视频流中。

> **说明：**
> 

> 调用 `setVirtualBackground` 仅在本地预览画面生效，远端用户不会看到虚拟背景效果，需调用 `saveVirtualBackground` 后才会同步到远端视频流。
> 

``` typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { setVirtualBackground } = useVirtualBackgroundState();

// 开启背景虚化预览
const previewBlur = async () => {
  await setVirtualBackground({
    enable: true,
    type: 'blur',
    level: 0.5 // 虚化程度，可选
  });
};

// 开启背景图片预览
const previewImage = async (imageUrl: string) => {
  await setVirtualBackground({
    enable: true,
    type: 'image',
    source: imageUrl // 图片 URL，支持本地或网络路径
  });
};
```

### 步骤3：保存并生效

用户确认预览效果满意后，调用 [saveVirtualBackground](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#saveVirtualBackground) 将配置真正应用到当前的视频流中，此时远端用户将看到效果。
``` typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { saveVirtualBackground } = useVirtualBackgroundState();

const confirmSettings = async () => {
  try {
    await saveVirtualBackground();
    console.log('虚拟背景已生效');
  } catch (error) {
    console.error('保存失败', error);
  }
};
```

### 步骤4：关闭虚拟背景

如果需要关闭虚拟背景，恢复真实背景，可以将其 `enable` 属性设为 `false` 并保存。
``` typescript
import { useVirtualBackgroundState } from 'tuikit-atomicx-vue3/room';

const { setVirtualBackground, saveVirtualBackground } = useVirtualBackgroundState();

const disableVirtualBackground = async () => {
  // 1. 设置为关闭状态
  await setVirtualBackground({ enable: false });
  // 2. 保存生效
  await saveVirtualBackground();
};
```

## 开发注意事项

- **资源文件路径：**`initVirtualBackground` 中的 `assetsPath` 必须正确指向 AI 模型文件的目录。如果路径错误，会导致虚拟背景无法启动。请查阅 [SDK 文档](https://web.sdk.qcloud.com/trtc/webrtc/doc/zh-cn/tutorial-36-advanced-virtual-background.html) 获取资源文件包。

- **性能消耗：** 开启虚拟背景会占用较高的 CPU 和 GPU 资源。在低端设备上可能会导致视频掉帧或发热，建议在检测到设备性能不足时提示用户关闭。

- **浏览器兼容性：** 并非所有浏览器都支持虚拟背景（依赖 WebAssembly 和 SIMD 等特性）。务必在 UI 渲染前调用 `isSupported()` 进行检查。

- **图片格式：** 自定义背景图片建议使用 `jpg` 或 `png` 格式，且分辨率不宜过大（建议 1920x1080 以内），以免影响加载速度和内存占用。

## 常见问题

### **在 Chrome 中运行 Demo 发现画面颠倒且卡顿？**

虚拟背景插件使用 GPU 进行加速，您需要在浏览器设置中找到使用硬件加速模式并启用。可以将 `chrome://settings/system` 复制到浏览器地址栏，并且打开硬件加速模式。

### **为什么开启虚拟背景后，电脑发热严重或风扇高速运转？**

虚拟背景功能需要实时识别人物轮廓并进行图像处理，对 CPU 和 GPU 算力有一定要求。建议在无需使用时关闭该功能，或在低端设备上避免开启。

### **初始化失败，报错 "assets load failed" 怎么办？**

请检查 `initVirtualBackground` 中传入的 `assetsPath` 是否正确。确保该路径下包含必要的 AI 模型文件，且能够通过浏览器直接访问。如果是跨域资源，需要配置 CORS 策略。

### **虚拟背景支持移动端浏览器吗？**

由于移动端浏览器的性能限制和 WebAssembly 支持情况差异较大，体验可能不如桌面端稳定。建议优先在桌面端 Chrome（推荐 94+）、Edge 等主流浏览器中使用，并做好兼容性判断 `isSupported()`。

### **本地开发时无法使用虚拟背景？**

WebRTC 相关功能通常要求在 HTTPS 环境下运行，或者是 `localhost` / `127.0.0.1`。请确保您的开发环境满足安全上下文要求。

## **示例项目**

腾讯云在 GitHub 上提供了示例项目 [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) ，您可以参考以实现完整的 RoomKit 功能。

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useVirtualBackgroundState** | 虚拟背景管理 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-VirtualBackgroundState) |
