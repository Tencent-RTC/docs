> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

This document guides you on how to use Atomicx components and the `useDeviceState` Hook to manage audio/video device (camera, microphone, speaker) state in meetings, and how to obtain local network quality information.

## Feature Introduction

- **Device Enumeration & Switching:** Supports real-time retrieval of available system device lists and seamless switching.

- **Fine-grained State Control:** Provides capabilities for enabling/disabling, muting capture, volume adjustment, and real-time volume monitoring.

- **Error Diagnosis:** Real-time feedback on hardware conflicts, missing system permissions, and other abnormal states.

- **Network Quality Monitoring:** Provides multi-dimensional network information including latency and packet loss rate.

## Prerequisites

- User has completed login authentication through [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState), please refer to Integration Overview.

- If integrating within an `iframe`, permissions must be declared in the tag.

   ``` typescript
   <iframe allow="microphone; camera;"></iframe>
   ```

## Implementing Camera Management

You can control local camera on/off, switch devices, and monitor capture status and errors through [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState).

### Step 1: Enable/Disable Camera

Control camera on/off by calling [openLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalCamera) and [closeLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#closeLocalCamera) methods.
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

### Step 2: Get/Switch Camera Device

#### Desktop Camera Get and Switch Example

You can get all available camera devices through [cameraList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraList), and use [setCurrentCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentCamera) method to switch the currently used camera.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { cameraList, currentCamera, getCameraList, setCurrentCamera } = useDeviceState();

// Get camera list
await getCameraList();

// Iterate and display available cameras
cameraList.value.forEach((camera) => {
  console.log(`Camera: ${camera.deviceName} (${camera.deviceId})`);
});

// Switch to specified camera
const switchCamera = async (deviceId: string) => {
  try {
    // Switch device (if camera is already on, will automatically re-capture using new device)
    await setCurrentCamera({ deviceId });
    console.log('Camera switched successfully');
  } catch (error) {
    console.error('Camera switch failed:', error);
    // On switch failure, will continue using original device
  }
};

// Monitor current camera changes
watch(currentCamera, (camera) => {
  if (camera) {
    console.log('Currently using camera:', camera.deviceName);
  }
});
```

> **Note:**
> 

> When calling [setCurrentCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentCamera) to switch devices, if the camera is already on, Atomicx will automatically stop capturing from the current device and restart capture using the new device, with seamless video stream switching. If switching fails, it will continue using the original device.
> 

#### Mobile Camera Switch Example

Mobile devices use [switchCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#switchCamera) to switch between front and rear cameras.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { isFrontCamera, switchCamera } = useDeviceState();

// Determine if mobile device
const isMobile = /Android|webOS|iPhone|iPad|iPod/i.test(navigator.userAgent);

// Switch front/rear camera (only supported on mobile)
const toggleCamera = async () => {
  if (!isMobile) {
    console.warn('Front/rear camera switching is only supported on mobile devices');
    return;
  }
  
  // Switch to opposite camera
  await switchCamera({ isFrontCamera: !isFrontCamera.value });
  console.log(`Switched to ${isFrontCamera.value ? 'front' : 'rear'} camera`);
};
```

### Step 3: Handle Camera Capture Errors

When camera capture errors occur, you can get error information through the [cameraLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraLastError) property.

> Note:
> 

> Common camera errors include `NoSystemPermission` (no system permission) and `OccupiedError` (device occupied). You should display clear error messages to users based on this property.
> 

``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
const { cameraLastError } = useDeviceState();

// Monitor camera errors
watch(cameraLastError, (error) => {
  switch (error) {
    case DeviceError.NoSystemPermission:
      console.error('Camera permission denied, please enable camera permission in system settings');
      break;
    case DeviceError.NoDeviceDetected:
      console.error('No camera device detected');
      break;
    case DeviceError.OccupiedError:
      console.error('Camera is occupied by another application');
      break;
    case DeviceError.NotSupportCapture:
      console.error('Current browser does not support camera capture');
      break;
    default:
      break;
  }
});
```

## Implementing Microphone Management

You can control local microphone on/off, mute, volume adjustment, and real-time volume display through [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState), and monitor capture status and errors.

### Step 1: Enable/Disable Microphone

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

### Step 2: Get/Switch Microphone Device

#### Desktop Browser Get/Switch Microphone Example

You can get all available microphone devices through [microphoneList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneList), and use [setCurrentMicrophone](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentMicrophone) method to switch the currently used microphone.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';

const { 
  microphoneList, 
  currentMicrophone, 
  getMicrophoneList, 
  setCurrentMicrophone 
} = useDeviceState();

// Get microphone list
await getMicrophoneList();

// Iterate and display available microphones
microphoneList.value.forEach((mic) => {
  console.log(`Microphone: ${mic.deviceName} (${mic.deviceId})`);
});

// Switch to specified microphone
const switchMicrophone = async (deviceId: string) => {
  await setCurrentMicrophone({ deviceId });
};

// Monitor current microphone changes
watch(currentMicrophone, (mic) => {
  if (mic) {
    console.log('Currently using microphone:', mic.deviceName);
  }
});
```

### Step 3: Set Capture Volume

You can use the [setCaptureVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCaptureVolume) method to adjust the local microphone capture volume.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { captureVolume, setCaptureVolume } = useDeviceState();

// Set capture volume to 50%
await setCaptureVolume(50);

// Monitor capture volume changes
watch(captureVolume, (volume) => {
  console.log('Current capture volume:', volume);
});
```

### Step 4: Display Real-time Volume

The [currentMicVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentMicVolume) property updates the local microphone capture volume in real-time, and can be used for dynamic volume bar display.
``` typescript
<template>
  <div class="mic-volume">
    <label>Microphone Volume</label>
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

### Step 5: Handle Microphone Capture Errors

When microphone capture errors occur, you can get error information through [microphoneLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneLastError) data.
``` typescript
import { useDeviceState, DeviceError } from 'tuikit-atomicx-vue3/room';
const { microphoneLastError } = useDeviceState();

// Monitor microphone errors
watch(microphoneLastError, (error) => {
  switch (error) {
    case DeviceError.NoSystemPermission:
      console.error('Microphone permission denied, please enable microphone permission in system settings');
      break;
    case DeviceError.NoDeviceDetected:
      console.error('No microphone device detected');
      break;
    case DeviceError.OccupiedError:
      console.error('Microphone is occupied by another application');
      break;
    case DeviceError.NotSupportCapture:
      console.error('Current browser does not support microphone capture');
      break;
    default:
      break;
  }
});
```

## Implementing Speaker Management

Speaker (playback device) mainly involves switching devices and setting playback volume.

### Step 1: Get/Switch Speaker Device

You can get all available speaker devices through [speakerList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#speakerList), and use [setCurrentSpeaker](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setCurrentSpeaker) method to switch the currently used speaker.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { 
  speakerList, 
  currentSpeaker, 
  getSpeakerList, 
  setCurrentSpeaker 
} = useDeviceState();

// Get speaker list
await getSpeakerList();

// Iterate and display available speakers
speakerList.value.forEach((speaker) => {
  console.log(`Speaker: ${speaker.deviceName} (${speaker.deviceId})`);
});

// Switch to specified speaker
const switchSpeaker = async (deviceId: string) => {
  await setCurrentSpeaker({ deviceId });
};

// Monitor current speaker changes
watch(currentSpeaker, (speaker) => {
  if (speaker) {
    console.log('Currently using speaker:', speaker.deviceName);
  }
});
```

### Step 2: Set Playback Volume

You can use the [setOutputVolume](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setOutputVolume) method to adjust speaker playback volume.
``` typescript
import { useDeviceState } from 'tuikit-atomicx-vue3/room';
const { outputVolume, setOutputVolume } = useDeviceState();

// Set playback volume to 60%
await setOutputVolume(60);

// Monitor playback volume changes
watch(outputVolume, (volume) => {
  console.log('Current playback volume:', volume);
});
```

## Implementing Network Quality Monitoring

Using the [networkInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#networkInfo) data in [useDeviceState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState), you can monitor the local user's network quality in real-time.

> **Note:**
> 

> Valid data can only be obtained in [networkInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#networkInfo) when the local user has joined the room through [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) and turned on the microphone or camera.
> 

``` typescript
<template>
  <div class="network-indicator" @click="showDetails = !showDetails">
    <TUIIcon v-if="networkIcon" :icon="networkIcon" />
    <span class="network-text">{{ networkText }}</span>
    <div v-if="showDetails" class="network-details">
      <div>Latency: {{ networkInfo?.delay }}ms</div>
      <div>Uplink Loss: {{ networkInfo?.upLoss }}%</div>
      <div>Downlink Loss: {{ networkInfo?.downLoss }}%</div>
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

// Network quality icon
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

// Network quality text
const networkText = computed(() => {
  if (!networkInfo.value) return 'Unknown';
  switch (networkInfo.value.quality) {
    case NetworkQuality.Excellent:
      return 'Excellent';
    case NetworkQuality.Good:
      return 'Good';
    case NetworkQuality.Poor:
      return 'Fair';
    case NetworkQuality.Bad:
      return 'Poor';
    case NetworkQuality.VeryBad:
      return 'Very Poor';
    case NetworkQuality.Down:
      return 'Disconnected';
    default:
      return 'Unknown';
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

## API Documentation

| **State/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| **useDeviceState** | Contains audio/video device status, audio/video device list and operation interfaces. | [API Documentation](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-DeviceState) |

## FAQ

### Can users enter an audio/video room if their computer has no microphone or speaker devices?

Yes, they can enter the room. Users can still successfully call the `joinRoom` interface to enter an audio/video room even without a microphone, speaker, or camera. However, in this state, users will face the following interaction exceptions.

**Feature Limitations:**
- **Cannot enable media capture:** Since the system cannot drive the relevant hardware, calling [openLocalMicrophone](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalMicrophone) or [openLocalCamera](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#openLocalCamera) interfaces will trigger asynchronous errors. At this time, the [microphoneLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#microphoneLastError) or [cameraLastError](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cameraLastError) property will return `DeviceError.NoDeviceDetected` (no device detected).

- **Capture limitations:** Cannot capture and transmit audio (requires microphone) or video (requires camera) through local devices.

- **Playback limitations:** Due to lack of speaker devices, users will not be able to hear audio from other participants in the room.

   **Functions that can still be used:**

   View-only mode: Users can still watch video streams or screen sharing from remote participants.

### How to implement device hot-plug detection?

Atomicx has built-in device hot-plug detection. When media devices are plugged in or unplugged, the device list and current device data will be automatically updated.
