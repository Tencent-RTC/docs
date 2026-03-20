> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本篇文档将指导您如何在用户加入房间前提供设备预览与测试功能。通过 Atomicx 提供的进房前测试接口，您可以引导用户检查并调整音视频设备状态，确保以最佳状态进入多人音视频房间。

## 功能介绍

设备预览是保障音视频房间顺利运行的重要环节。Atomicx 专为进房前场景提供了一套独立的测试接口，其核心能力包括：
- **视频画面预览**：在不推流的情况下启动摄像头，验证画面清晰度与光线。

- **麦克风检测**：通过实时音量反馈，确认麦克风采集是否正常。

- **扬声器校准**：通过播放测试音，确认输出设备及其音量设置。

## 前提条件

- 用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

## 实现设备检测功能

### 步骤1：摄像头测试

使用 [startCameraTest](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#startCameraTest) 方法启动摄像头测试。该方法仅用于本地预览，不会产生网络流量。

**代码示例：**
``` typescript
<template>
  <div class="preview-container">
    <!-- 摄像头预览区域 -->
    <div id="camera-preview" class="camera-preview">
      <!-- 加载状态 -->
      <div v-if="isCameraTestLoading" class="loading-overlay">
        <div class="loading-spinner" />
        <span>正在启动摄像头...</span>
      </div>
    </div>

    <!-- 摄像头选择 -->
    <div class="camera-selector">
      <label>选择摄像头</label>
      <select 
        :value="currentCamera?.deviceId" 
        @change="handleCameraChange"
        :disabled="isCameraTestLoading"
      >
        <option 
          v-for="camera in cameraList" 
          :key="camera.deviceId"
          :value="camera.deviceId"
        >
          {{ camera.deviceName }}
        </option>
      </select>
    </div>

    <!-- 控制按钮 -->
    <div class="control-buttons">
      <button @click="getCameraList">获取摄像头列表</button>
      <button 
        @click="toggleCameraTest"
        :disabled="isCameraTestLoading"
      >
        {{ isCameraTesting ? '停止预览' : '开始预览' }}
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue';
import { IconWarning } from '@tencentcloud/uikit-base-component-vue3';
import { 
  useLoginState,
  useDeviceState, 
  DeviceStatus,
  DeviceError,
} from 'tuikit-atomicx-vue3/room';

const { 
  isCameraTesting,
  isCameraTestLoading,
  cameraList,
  currentCamera,
  startCameraTest,
  stopCameraTest,
  getCameraList,
  setCurrentCamera
} = useDeviceState();

onUnmounted(() => {
  if (isCameraTesting.value) {
    stopCameraTest();
  }
});

const toggleCameraTest = async () => {
  if (isCameraTesting.value) {
    await stopCameraTest();
  } else {
    const previewElement = document.getElementById('camera-preview');
    if (previewElement) {
      await startCameraTest({ view: previewElement });
    }
  }
};

const handleCameraChange = async (event: Event) => {
  const deviceId = (event.target as HTMLSelectElement).value;
  try {
    await setCurrentCamera({ deviceId });
  } catch (error) {
    console.error('切换摄像头失败:', error);
  }
};
</script>

<style scoped>
.preview-container {  position: relative;  width: 100%;  max-width: 640px;  margin: 0 auto;}.camera-preview {  width: 100%;  position: relative;  aspect-ratio: 16 / 9;  background-color: #000;  border-radius: 8px;  overflow: hidden;}.loading-overlay {  position: absolute;  inset: 0;  display: flex;  flex-direction: column;  align-items: center;  justify-content: center;  gap: 12px;  background-color: rgba(0, 0, 0, 0.7);  color: white;  border-radius: 8px;}.loading-spinner {  width: 40px;  height: 40px;  border: 4px solid rgba(255, 255, 255, 0.3);  border-top-color: white;  border-radius: 50%;  animation: spin 0.8s linear infinite;}@keyframes spin {  to { transform: rotate(360deg); }}.error-message {  display: flex;  align-items: center;  gap: 8px;  padding: 12px;  margin-top: 12px;  background-color: #ffebee;  color: #c62828;  border-radius: 4px;  font-size: 14px;}.camera-selector {  margin-top: 16px;  display: flex;  align-items: center;  gap: 12px;}.camera-selector label {  font-weight: 500;  min-width: 80px;}.camera-selector select {  flex: 1;  padding: 8px 12px;  border: 1px solid #e0e0e0;  border-radius: 4px;  font-size: 14px;}.control-buttons {  margin-top: 16px;  display: flex;  gap: 12px;}.control-buttons button {  flex: 1;  padding: 12px 24px;  background-color: #1976d2;  color: white;  border: none;  border-radius: 4px;  font-size: 14px;  font-weight: 500;  cursor: pointer;  transition: background-color 0.3s;}.control-buttons button:hover:not(:disabled) {  background-color: #1565c0;}.control-buttons button:disabled {  background-color: #bdbdbd;  cursor: not-allowed;}
</style>
```

### 步骤2：麦克风测试

通过 [startMicrophoneTest](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#startMicrophoneTest) 启动麦克风测试，并利用 [testingMicVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#testingMicVolume) 实现 UI 音量条的实时波动。

**代码示例：**
``` typescript
<template>
  <div class="microphone-test">
    <!-- 音量显示 -->
    <div class="volume-display">
      <div class="volume-label">麦克风音量</div>
      <div class="volume-bar-container">
        <div 
          class="volume-bar" 
          :style="{ width: `${testingMicVolume}%` }"
          :class="volumeClass"
        />
      </div>
      <div class="volume-value">{{ testingMicVolume }}</div>
    </div>

    <!-- 麦克风选择 -->
    <div class="microphone-selector">
      <label>选择麦克风</label>
      <select 
        :value="currentMicrophone?.deviceId" 
        @change="handleMicrophoneChange"
        :disabled="isMicrophoneTesting"
      >
        <option 
          v-for="mic in microphoneList" 
          :key="mic.deviceId"
          :value="mic.deviceId"
        >
          {{ mic.deviceName }}
        </option>
      </select>
    </div>

    <!-- 控制按钮 -->
    <div class="control-buttons">
      <button 
        @click="getMicrophoneList"
      >
        获取设备
      </button>
      <button 
        @click="toggleMicrophoneTest"
      >
        {{ isMicrophoneTesting ? '停止测试' : '开始测试' }}
      </button>
    </div>

    <!-- 测试提示 -->
    <div v-if="isMicrophoneTesting" class="test-tip">
      <span>请对着麦克风说话，查看音量变化</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onMounted, onUnmounted } from 'vue';
import { 
  useDeviceState, 
  DeviceError,
} from 'tuikit-atomicx-vue3/room';

const { 
  isMicrophoneTesting,
  testingMicVolume,
  microphoneList,
  currentMicrophone,
  startMicrophoneTest,
  stopMicrophoneTest,
  getMicrophoneList,
  setCurrentMicrophone
} = useDeviceState();

// 音量条样式类
const volumeClass = computed(() => {
  const volume = testingMicVolume.value;
  if (volume === 0) return 'volume-zero';
  if (volume < 30) return 'volume-low';
  if (volume < 70) return 'volume-medium';
  return 'volume-high';
});

// 清理：停止测试
onUnmounted(() => {
  if (isMicrophoneTesting.value) {
    stopMicrophoneTest();
  }
});

// 切换麦克风测试
const toggleMicrophoneTest = async () => {
  if (isMicrophoneTesting.value) {
    await stopMicrophoneTest();
  } else {
    try {
      await startMicrophoneTest({ interval: 200 });
    } catch (error) {
      console.error('启动麦克风测试失败:', error);
    }
  }
};

// 切换麦克风设备
const handleMicrophoneChange = async (event: Event) => {
  const deviceId = (event.target as HTMLSelectElement).value;
  try {
    await setCurrentMicrophone({ deviceId });
  } catch (error) {
    console.error('切换麦克风失败:', error);
  }
};
</script>

<style scoped>
.microphone-test {  width: 100%;  max-width: 640px;  margin: 0 auto;}.volume-display {  padding: 24px;  background-color: #f5f5f5;  border-radius: 8px;  margin-bottom: 16px;}.volume-label {  font-size: 14px;  font-weight: 500;  color: #666;  margin-bottom: 12px;}.volume-bar-container {  width: 100%;  height: 24px;  background-color: #e0e0e0;  border-radius: 12px;  overflow: hidden;  margin-bottom: 8px;}.volume-bar {  height: 100%;  transition: width 0.1s ease;  border-radius: 12px;}.volume-bar.volume-zero {  background-color: #9e9e9e;}.volume-bar.volume-low {  background-color: #ff9800;}.volume-bar.volume-medium {  background-color: #4caf50;}.volume-bar.volume-high {  background-color: #2196f3;}.volume-value {  text-align: center;  font-size: 18px;  font-weight: 600;  color: #333;}.microphone-selector {  display: flex;  align-items: center;  gap: 12px;  margin-bottom: 16px;}.microphone-selector label {  font-weight: 500;  min-width: 80px;}.microphone-selector select {  flex: 1;  padding: 8px 12px;  border: 1px solid #e0e0e0;  border-radius: 4px;  font-size: 14px;}.error-message {  display: flex;  align-items: center;  gap: 8px;  padding: 12px;  margin-bottom: 16px;  background-color: #ffebee;  color: #c62828;  border-radius: 4px;  font-size: 14px;}.control-buttons {  margin-bottom: 12px; display: flex; gap: 8px; }.control-buttons button {  flex: 1; width: 100%;  padding: 12px 24px;  background-color: #1976d2;  color: white;  border: none;  border-radius: 4px;  font-size: 14px;  font-weight: 500;  cursor: pointer;  transition: background-color 0.3s;}.control-buttons button:hover:not(:disabled) {  background-color: #1565c0;}.control-buttons button:disabled {  background-color: #bdbdbd;  cursor: not-allowed;}.test-tip {  display: flex;  align-items: center;  gap: 8px;  padding: 12px;  background-color: #e3f2fd;  color: #1565c0;  border-radius: 4px;  font-size: 13px;}
</style>
```

### 步骤3：扬声器测试

使用 [startSpeakerTest](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#startSpeakerTest) 方法播放一段指定的音频文件来确认扬声器是否工作正常。

**代码示例：**
``` typescript
<template>
  <div class="speaker-test">
    <!-- 扬声器选择 -->
    <div class="speaker-selector">
      <label>选择扬声器</label>
      <select 
        :value="currentSpeaker?.deviceId" 
        @change="handleSpeakerChange"
        :disabled="isSpeakerTesting"
      >
        <option 
          v-for="speaker in speakerList" 
          :key="speaker.deviceId"
          :value="speaker.deviceId"
        >
          {{ speaker.deviceName }}
        </option>
      </select>
    </div>

    <!-- 控制按钮 -->
    <div class="control-buttons">
      <button @click="getSpeakerList">获取扬声器列表</button>
      <button 
        @click="toggleSpeakerTest"
        :disabled="isSpeakerTesting"
      >
        {{ isSpeakerTesting ? '正在播放...' : '播放测试音频' }}
      </button>
      <button 
        v-if="isSpeakerTesting"
        @click="stopSpeakerTest"
      >
        停止播放
      </button>
    </div>

    <!-- 测试提示 -->
    <div v-if="isSpeakerTesting" class="test-tip">
      <span>正在播放测试音频，请确认是否能听到声音</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import { useDeviceState } from 'tuikit-atomicx-vue3/room';

const { 
  isSpeakerTesting,
  speakerList,
  currentSpeaker,
  startSpeakerTest,
  stopSpeakerTest,
  getSpeakerList,
  setCurrentSpeaker,
} = useDeviceState();

// 测试音频文件路径
// 注意：此 URL 仅供开发测试使用，生产环境请使用您自己的音频文件，建议使用 3-5 秒的 MP3 格式音频文件
const testAudioPath = 'https://web.sdk.qcloud.com/trtc/electron/download/resources/media/TestSpeaker.mp3';

// 清理：停止测试
onUnmounted(() => {
  if (isSpeakerTesting.value) {
    stopSpeakerTest();
  }
});

// 切换扬声器测试
const toggleSpeakerTest = async () => {
  if (isSpeakerTesting.value) {
    await stopSpeakerTest();
  } else {
    try {
      await startSpeakerTest({ filePath: testAudioPath });
    } catch (error) {
      console.error('启动扬声器测试失败:', error);
    }
  }
};

// 切换扬声器设备
const handleSpeakerChange = async (event: Event) => {
  const deviceId = (event.target as HTMLSelectElement).value;
  try {
    await setCurrentSpeaker({ deviceId });
  } catch (error) {
    console.error('切换扬声器失败:', error);
  }
};
</script>

<style scoped>
.speaker-test {  width: 100%;  max-width: 640px;  margin: 0 auto;}.speaker-selector {  display: flex;  align-items: center;  gap: 12px;  margin-bottom: 24px;}.speaker-selector label {  font-weight: 500;  min-width: 80px;}.speaker-selector select {  flex: 1;  padding: 8px 12px;  border: 1px solid #e0e0e0;  border-radius: 4px;  font-size: 14px;}.volume-slider-container {  display: flex;  align-items: center;  gap: 12px;}.volume-slider {  flex: 1;  height: 6px;  border-radius: 3px;  background: #e0e0e0;  outline: none;  -webkit-appearance: none;}.volume-slider::-webkit-slider-thumb {  -webkit-appearance: none;  width: 18px;  height: 18px;  border-radius: 50%;  background: #1976d2;  cursor: pointer;}.volume-slider::-moz-range-thumb {  width: 18px;  height: 18px;  border-radius: 50%;  background: #1976d2;  cursor: pointer;  border: none;}.volume-value {  min-width: 50px;  text-align: right;  font-weight: 500;  color: #333;}.control-buttons {  display: flex;  gap: 12px;  margin-bottom: 12px;}button {  flex: 1;  padding: 12px 24px;  background-color: #1976d2;  color: white;  border: none;  border-radius: 4px;  font-size: 14px;  font-weight: 500;  cursor: pointer;  transition: background-color 0.3s;}.control-buttons button:hover:not(:disabled) {  background-color: #1565c0;}.control-buttons button:disabled {  background-color: #bdbdbd;  cursor: not-allowed;}.test-tip {  display: flex;  align-items: center;  gap: 8px;  padding: 12px;  background-color: #e3f2fd;  color: #1565c0;  border-radius: 4px;  font-size: 13px;}
</style>
```

## API 文档

| **State/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **useDeviceState** | 包含音视频设备状态，音视频设备列表及操作接口。 | [API 文档](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) |

## 常见问题

### 为什么摄像头预览没有画面？

可能的原因包括：
- **权限被拒绝：**检查浏览器是否允许摄像头权限。

- **设备被占用：**关闭其他使用摄像头的应用。

- **设备未连接：**检查摄像头是否正确连接。

- **浏览器不支持：**使用 Chrome、Edge 等现代浏览器。

### 麦克风测试时音量一直为 0？

可能的原因包括：
- **麦克风权限未授予：**检查浏览器权限设置。

- **麦克风被静音：**检查系统麦克风设置。

- **设备选择错误：**切换到正确的麦克风设备。
