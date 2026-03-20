> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本篇文档将指导您使用 Atomicx 的核心视图组件 `RoomView` 实现实时音视频房间的视频画面渲染。通过配置布局模板和利用组件插槽，您可以快速实现视频布局的切换与个性化定制。

`RoomView` 是 Atomicx 中负责视频画面渲染的主容器组件。它会自动处理视频流的订阅、渲染和释放，您只需关注其布局配置和 UI 增强。

## 核心能力

`RoomView` 作为多人音视频房间的核心视图容器，内置了多项企业级音视频处理能力：
- **智能性能管理**：支持视口内视频流懒加载，并能根据流区域尺寸，远端流数量自动进行高低清流切换，显著降低系统能耗与带宽占用。

- **精细化排序策略**：系统根据角色（主持人优先）与媒体状态（音视频开启优先）自动调整画面顺序。

- **交互式演讲者模式**：支持双击锁定主画面，并在侧边栏或顶部栏布局中支持收起非核心区域，聚焦演示内容。

- **沉浸式视觉设计**：所有视频窗口强制保持 `16:9` 的标准比例，并配合交互显示的工具栏，提供无干扰的多人音视频房间体验。

## 可定制范围

`RoomView` 仅聚焦于底层视频流的播放，而将视图层的所有“表现权”交给了开发者。使用 `RoomView` 您可以自由定义：
- **布局切换**：根据业务场景（例如演讲模式或讨论模式）实时变换布局。

- **视频挂件 UI：**包含昵称标签、音量动态波形、角色徽章等。

## **前提条件**

- 用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

- 用户已经通过 [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 进入房间，参考 房间管理。

- 已在 `App.vue` 中配置全局 `UIKitProvider`，具体请参考 [配置 App.vue](https://cloud.tencent.com/document/product/647/81962#ce98ee15-b0cb-43af-81da-70f51231430e)。

## 实现视频布局功能

### 步骤1：引入并渲染基础布局

在您的会议页面（例如 RoomLayoutView.vue）中引入 `RoomView` 及布局枚举 `RoomLayoutTemplate`。
``` typescript
<template>
  <div class="room-container">
    <RoomView />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { RoomView } from 'tuikit-atomicx-vue3/room';
</script>

<style lang="scss">
.room-container {
 width: 100%;
 height: 100%;
}
</style>
```

> **注意：**
> 

> `RoomView` 会自动占满父容器的高度和宽度，请确保其父容器具备明确的尺寸。
>

### 步骤2：切换布局模式

您可以根据业务逻辑（例如点击切换按钮）动态更新 `RoomView` 属性 `layoutTemplate` 的值。
| 属性名 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `layoutTemplate` | `RoomLayoutTemplate` | `RoomLayoutTemplate.GridLayout` | 视频流布局 |

`RoomLayoutTemplate` 可选值解析：
| 布局模式 | 枚举值 | 描述 |
| --- | --- | --- |
| 宫格布局 | `RoomLayoutTemplate.GridLayout` | 默认布局。所有参会者画面均匀排列，参会者超出 9 人时支持翻页。 |
| 侧边栏布局 | `RoomLayoutTemplate.SidebarLayout` | 主画面突出显示，其余参会者画面以侧边栏的形式排列，适用于演讲、报告等场景。 |
| 顶部栏布局 | `RoomLayoutTemplate.CinemaLayout` | 主画面突出显示，其余参会者画面以顶部栏的形式排列，适用于演讲、报告等场景。 |

``` typescript
<template>
  <!-- 添加布局切换按钮 -->
  <div class="layout-switcher">
    <button @click="switchLayout(RoomLayoutTemplate.GridLayout)">宫格</button>
    <button @click="switchLayout(RoomLayoutTemplate.SidebarLayout)">侧边栏</button>
    <button @click="switchLayout(RoomLayoutTemplate.CinemaLayout)">顶部栏</button>
  </div>
  <div class="room-container">
    <RoomView :layoutTemplate="currentLayout" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { RoomView, RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
// 默认使用宫格布局
const currentLayout = ref(RoomLayoutTemplate.GridLayout);

// 切换布局
const switchLayout = (layout: RoomLayoutTemplate) => {
  currentLayout.value = layout;
};
</script>

<style lang="scss">
.room-container {
 width: 100%;
 height: 100%;
}
</style>
```

### 步骤3：实现视频流挂件信息

您可以通过使用 `participantViewUI` 插槽，为视频画面定制 UI 显示内容（例如设置昵称，添加自定义图标）。

| 插槽参数 | 类型 | 说明 |
| --- | --- | --- |
| `participant` | `RoomParticipant` | 当前视频流对应的参会者对象，包含用户的 `userId`、`userName`、`role` 等实时状态信息。 |
| `streamType` | `VideoStreamType` | 区分当前是摄像头流（Camera）还是屏幕分享流（Screen） |

#### 准备视觉资源

为了提供完整的交互反馈，建议您提前准备以下图标：
- 麦克风状态图标：区分开启、静音及动态音量条。

- 身份标识图标：用于区分房主（Owner）、管理员（Admin）与普通成员。

- 网络信号图标：展示实时的上/下行网络质量。
   

   > **特别说明：**
   > 

   > 我们在示例项目的 @tencentcloud/uikit-base-component-vue3 npm 包中提供了免版权的图标，您可以直接引入使用。
   > 

#### 实现代码示例
1. 新增文件 `ParticipantViewUI.vue` 实现单个视频流挂件层 UI 渲染逻辑。

   ``` typescript
   <template>
     <div class="stream-cover-container">
       <div class="corner-user-info-container">
         <div
           v-if="showMasterIcon || showAdminIcon"
           :class="{ 'master-icon': showMasterIcon, 'admin-icon': showAdminIcon }"
         >
           <IconUser />
         </div>
         <div v-if="!isScreenStream" :class="['audio-icon-container']">
           <div class="audio-level-container">
             <div class="audio-level" :style="audioLevelStyle" />
           </div>
           <IconMicOff v-if="!isMicrophoneOn" class="audio-icon" size="20" />
           <IconMicOn v-else class="audio-icon" size="20" />
         </div>
         <span class="user-name" :title="displayName">
           {{ displayName }}
         </span>
       </div>
     </div>
   </template>
   
   <script setup lang="ts">
   import { computed, defineProps } from 'vue';
   import {
     IconUser,
     IconMicOff,
     IconMicOn,
   } from '@tencentcloud/uikit-base-component-vue3';
   import { RoomParticipantRole, DeviceStatus, VideoStreamType, useRoomParticipantState } from 'tuikit-atomicx-vue3/room';
   import type { RoomParticipant } from 'tuikit-atomicx-vue3/room';
   
   interface Props {
     participant: RoomParticipant;
     streamType: VideoStreamType;
   }
   const props = defineProps<Props>();
   
   const { speakingUsers } = useRoomParticipantState();
   const speakingAudioVolume = computed(() => speakingUsers.value.get(props.participant.userId) || 0);
   const audioLevelStyle = computed(() => {
     if (props.participant.microphoneStatus === DeviceStatus.Off || !speakingAudioVolume.value) {
       return '';
     }
     return `height: ${speakingAudioVolume.value * 4}%`;
   });
   
   const isMicrophoneOn = computed(() => props.participant.microphoneStatus === DeviceStatus.On);
   const displayName = computed(() => props.participant.nameCard || props.participant.userName || props.participant.userId);
   
   const showMasterIcon = computed(() => {
     const { role } = props.participant;
     return role === RoomParticipantRole.Owner && props.streamType === VideoStreamType.Camera;
   });
   
   const showAdminIcon = computed(() => {
     const { role } = props.participant;
     return (role === RoomParticipantRole.Admin && props.streamType === VideoStreamType.Camera);
   });
   </script>
   
   <style lang="scss" scoped>
   .stream-cover-container {  position: absolute;  top: 0;  left: 0;  width: 100%;  height: 100%;  border-radius: 12px;  pointer-events: none;  .corner-user-info-container {    position: absolute;    bottom: 8px;    left: 8px;    display: flex;    align-content: center;    align-items: center;    box-sizing: border-box;    min-width: 118px;    max-width: calc(100% - 24px);    padding-right: 10px;    height: 32px;    overflow: hidden;    font-size: 14px;    color: var(--uikit-color-white-1);    border-radius: 16px;    background-color: var(--uikit-color-black-5);    .master-icon,    .admin-icon {      display: flex;      flex-shrink: 0;      align-items: center;      justify-content: center;      width: 32px;      height: 32px;      margin-left: 0;      border-radius: 50%;      background-color: var(--button-color-primary-default);    }    .admin-icon {      background-color: var(--text-color-warning);    }    .audio-icon-container {      margin-left: 4px;      position: relative;      width: 20px;      height: 20px;      flex-shrink: 0;      min-width: 20px;      &:first-child {        margin-left: 8px;      }      .audio-level-container {        position: absolute;        top: 2px;        left: 6px;        display: flex;        flex-flow: column-reverse wrap;        justify-content: space-between;        width: 8px;        height: 12px;        overflow: hidden;        border-radius: 4px;        .audio-level {          width: 100%;          background-color: var(--text-color-success);          transition: height 0.2s;        }      }      .audio-icon {        position: absolute;        top: 0;        left: 0;      }    }    .user-name {      margin-left: 4px;      overflow: hidden;      text-overflow: ellipsis;      white-space: nowrap;      min-width: 0;    }    .screen-icon {      flex-shrink: 0;      min-width: 0;      color: var(--uikit-color-white-1);      margin-left: 4px;      margin-right: 2px;    }    .screen-info {      margin-left: 4px;      font-size: 12px;      color: var(--uikit-color-white-1);      flex-shrink: 0;      min-width: 0;    }  }}
   </style>
   ```
2. 配置 `RoomView` 插槽，在页面中渲染视频流挂件内容。

   ``` typescript
   <template>
     <div class="room-container">
       <RoomView :layout-template="currentLayout">
         <template #participantViewUI="{ participant, streamType }">
           <ParticipantViewUI :participant="participant" :stream-type="streamType" />
         </template>
       </RoomView>
     </div>
   </template>
   
   <script setup lang="ts">
   import { ref } from 'vue';
   import { RoomView, RoomLayoutTemplate } from 'tuikit-atomicx-vue3/room';
   // 引入新增的 ParticipantViewUI.vue 文件
   import ParticipantViewUI from './ParticipantViewUI.vue';
   
   // 默认使用宫格布局
   const currentLayout = ref(RoomLayoutTemplate.GridLayout);
   </script>
   ```

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useRoomParticipantState** | 包含房间内用户数据，用户管理接口。 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) |

## 常见问题

### **RoomView 是否包含屏幕分享流的渲染？**

`RoomView` 内置远端屏幕分享流的渲染，对于本地屏幕共享您需要通过 `participantViewUI` 插槽完成本地屏幕分享的占位 UI。

### RoomView 是否默认包含 UI 挂件？

`RoomView` 内部无默认 UI 挂件，您可以参考 [RoomView 开源使用示例](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts/src/components/RoomLayoutView) 快速完成自定义 UI 挂件。

### RoomView 提供的布局均不满足需求，是否可以提供更多的可选布局？

`RoomView` 暂时支持宫格布局、侧边栏和顶部栏布局。如果您有定制需求请 [联系我们](https://cloud.tencent.com/document/product/647/19906)。
