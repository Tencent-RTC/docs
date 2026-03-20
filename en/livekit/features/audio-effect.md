> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

This document provides guidance on integrating audio effect controls into your live streaming application using the `AudioEffectStore` and `DeviceStore` modules from the **AtomicXCore** framework. These modules enable features such as microphone volume control, ear monitoring, and a variety of voice changing and reverb effects.

## Core Features
By combining `AudioEffectStore` and DeviceStore, you can implement the following core features:
- **Microphone Volume Adjustment**: Control the local microphone (capture) volume, which determines the volume your audience hears.

- **Ear Monitoring**: Allow hosts or co-hosts to hear their own voice in real time through headphones for monitoring and pronunciation correction.

- **Voice Effects**: Apply various voice changing effects, such as "Little Girl", "Deep Male", "Naughty Child", and more.

- **Reverb Effects**: Simulate different acoustic environments, such as KTV, metallic, deep, bright, and others.

## Core Concepts

| **概念** | **职责描述** | **Android** | **iOS** |
| --- | --- | --- | --- |
| `AudioChangerType` | Enumerates available voice effects, such as "child", "man", etc. | `enum` | `enum` |
| `AudioReverbType` | Enumerates available reverb effects, such as "ktv", "hall", etc. | `enum` | `enum` |
| `AudioEffectState` | Represents the current state of the audio effect module, typically used for UI rendering. Includes voice effect state, reverb state, ear monitoring enabled state, and ear monitor volume. | `data` | `struct` |
| `AudioEffectStore` | Singleton data management class for audio effects. Use it to invoke audio effect APIs. After calling an API, the corresponding state property is automatically updated. Subscribe to this reactive state to receive and monitor updates. | `abstract` | `class` |
| `DeviceState` | Represents the current state of the device module, typically used for UI rendering. Core properties include camera and microphone status. | `data` | `struct` |
| `DeviceStore` | Singleton data management class for device operations. Use it to control the camera and microphone. After calling an API, the corresponding state property is automatically updated. Subscribe to this reactive state to receive and monitor updates. | `abstract` | `class` |

## Platform: Android

## Implementation

### Step 1: Component Integration
- **Video Live Streaming**: Please refer to Quick Start to integrate **AtomicXCore.**

- **Voice Chat Room**: Please refer to Quick Start to integrate **AtomicXCore**.

### Step 2: Obtain Store Instances

`AudioEffectStore` and `DeviceStore` are singleton objects. Access their instances anywhere in your project via the shared property. For a complete implementation example, see [AudioEffectPanel.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/component/audioeffect/AudioEffectPanel.kt) in the TUILiveKit open-source UI demo.
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

val audioEffectStore = AudioEffectStore.shared()
val deviceStore = DeviceStore.shared()
```

### Step 3: Implement In-Ear Monitoring

Ear monitoring allows users to hear their own microphone audio in real time. Manage ear monitoring using the following APIs.

> **Note：**Ear monitoring typically requires headphones to function properly.
> 

#### Implementation
1. **Toggle Ear Monitoring**: Use a `Switch` control to enable or disable ear monitoring.

2. **Set Ear Monitor Volume**: Use a `SeekBar` (or similar control) to adjust the ear monitor volume. Map the UI control's value to the [0, 150] range and call setVoiceEarMonitorVolume(volume).

3. **Listen for State Updates**: Subscribe to ear monitor state changes to update the UI in real time.

#### Code Example
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// 1. Enable ear monitoring (prompt the user to connect headphones)
audioEffectStore.setVoiceEarMonitorEnable(true)

// 2. Set ear monitor volume
audioEffectStore.setVoiceEarMonitorVolume(80)

// 3. Disable ear monitoring
audioEffectStore.setVoiceEarMonitorEnable(false)

// 4. Listen for state updates
CoroutineScope(Dispatchers.Main).launch {
  audioEffectStore.audioEffectState.earMonitorVolume.collect {
    print("Ear monitor volume: $it")
    // Update UI with the value
  }
}
```

#### setVoiceEarMonitorEnable Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `enable` | `Boolean` | Whether to enable ear monitoring. - true: Enable - false: Disable |

#### setVoiceEarMonitorVolume Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `Int` | Ear monitor volume. - Range: [0, 150] - Default: 100 |

### Step 4: Implement Microphone Volume Adjustment

To adjust the microphone (capture) volume, call `setCaptureVolume(volume) `on `DeviceStore` with the desired value.

#### Implementation
1. **Set Capture Volume**: Use a `SeekBar` or similar control to adjust the microphone volume. Map the UI control's value to the [0, 150] range and call `setCaptureVolume(volume)`.

2. **Listen for State Updates**: Subscribe to capture volume state changes to update the UI in real time.

#### Code Example
``` java
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

// 1. Adjust microphone volume
deviceStore.setCaptureVolume(80)

// 2. Listen for volume changes
CoroutineScope(Dispatchers.Main).launch {
   deviceStore.deviceState.captureVolume.collect {
     print("Capture volume: $it")
     // Update UI with the value
   }
}
```

#### setCaptureVolume Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `Int` | Microphone (capture) volume. - Range: [0, 150] - Default: 100 |

### Step 5: Implement Voice Effects

To apply a voice effect, call `setAudioChangerType(type)` with the desired `AudioChangerType` enum value.

#### Code Example
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.AudioChangerType

// Set voice effect to "Little Girl"
audioEffectStore.setAudioChangerType(AudioChangerType.LITTLE_GIRL)

// Disable voice effect
audioEffectStore.setAudioChangerType(AudioChangerType.NONE)
```

#### setAudioChangerType Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | `AudioChangerType` | Voice effect enum value. |

### Step 6: Implement Reverb Effects

To apply a reverb effect, call `setAudioReverbType(type)` with the desired `AudioReverbType` enum value.
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.AudioReverbType

// Set reverb to "KTV"
audioEffectStore.setAudioReverbType(AudioReverbType.KTV)

// Disable reverb
audioEffectStore.setAudioReverbType(AudioReverbType.NONE)
```

#### setAudioReverbType Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | `AudioReverbType` | Reverb effect enum value. |

### Step 7: Reset Audio Effect Settings

> **Note：**
> 

> `AudioEffectStore` and `DeviceStore` are singletons, so audio effect and device settings are globally applied. To prevent settings from one live room affecting others, reset audio effect and device settings when the current live room ends.
> 

Reset audio effect settings in the following scenarios:
1. When the current live room ends.

2. To provide a "one-click restore" feature, call the`reset()`() method on each store.

   ``` java
   import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   
   // One-click reset for all voice and reverb effects
   AudioEffectStore.shared().reset()
   DeviceStore.shared().reset()
   ```

## API Documentation

For detailed information on all public interfaces and properties of AudioEffectStore, DeviceStore, and their related classes, refer to the official API documentation for the AtomicXCore framework.
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| AudioEffectStore | Manage audio effects: configure and retrieve real-time audio effect status. |  |
| DeviceStore | Manage devices: control camera and microphone, and retrieve real-time device status. |  |

## Platform: iOS

## Implementation

### Step 1: Component Integration
- **Video Live Streaming**: Please refer to Quick Start to integrate **AtomicXCore**.

- **Voice Chat Room**: Please refer to Quick Start to integrate **AtomicXCore**.

### Step 2: Obtain Store Instances

`AudioEffectStore` and `DeviceStore` are singleton objects. Access their instances via the shared property anywhere in your project. For a complete implementation example, see [AudioEffectView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Component/AudioEffect/AudioEffectView.swift) in the `TUILiveKit` open-source UI demo project.
``` swift
import UIKit
import AtomicXCore
import Combine

let audioEffectStore = AudioEffectStore.shared
let deviceStore = DeviceStore.shared
```

### Step 3: Implement In-ear Monitoring

In-ear monitoring allows users to hear their microphone audio in real time. Manage in-ear monitoring by calling the interfaces described below.

> **Note:**
> 

> In-ear monitoring typically requires headphones to function properly.
> 

#### Implementation
1. **Toggle Ear Monitoring**: Use a `UISwitch` control to enable or disable in-ear monitoring.

2. **Set Ear Monitor Volume**: Use a `UISlider` control to adjust the volume. Map the `UISlider` value to the range `[0, 150]` and call `setVoiceEarMonitorVolume(_:)`.

3. **Subscribe to State Updates**: Listen for real-time ear monitor state changes to update the UI.

#### Code Example
``` swift
// 1. Enable ear monitoring
audioEffectStore.setVoiceEarMonitorEnable(enable: true)

// 2. Adjust ear monitor volume
audioEffectStore.setVoiceEarMonitorVolume(volume: 80)

// 3. Disable ear monitoring
audioEffectStore.setVoiceEarMonitorEnable(enable: false)

// 4. Listen for state changes
audioEffectStore.state
    .subscribe(StatePublisherSelector(keyPath: \AudioEffectState.earMonitorVolume))
    .removeDuplicates()
    .sink { currentVolume in
        print("Ear monitor volume: \(currentVolume)")
        valueLabel.text = "\(currentVolume)" // Update UILabel to display the value
    }
    .store(in: &cancellables)
```

#### setVoiceEarMonitorEnable Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `enable` | `Bool` | Whether to enable ear monitoring. - true: Enable - false: Disable |

#### setVoiceEarMonitorVolume Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `Int` | Ear monitor volume. - Value range: [0, 150] - Default: 100 |

### Step 4: Implement Microphone Volume Adjustment

To adjust the microphone (capture) volume, call the `setCaptureVolume(volume:)` method of DeviceStore with the desired value.

#### Implementation
1. **Set Capture Volume**: Use a `UISlider` control to adjust the volume. Map the `UISlider` value to the range `[0, 150]` and call `setCaptureVolume(volume:)`.

2. **Subscribe to State Updates**: Listen for real-time volume state changes to update the UI.

#### Code Example
``` swift
// 1. Adjust capture volume
deviceStore.setCaptureVolume(volume: 80)

// 2. Listen for state changes
deviceStore.state
    .subscribe(StatePublisherSelector(keyPath: \DeviceState.captureVolume))
    .removeDuplicates()
    .sink { captureVolume in
        print("Capture volume: \(captureVolume)")
        valueLabel.text = "\(captureVolume)" // Update UILabel to display the value
    }
    .store(in: &cancellables)
```

#### setCaptureVolume Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `Int` | Microphone (capture) volume. - Value range: [0, 150] - Default: 100 |

### Step 5: Implement Voice Effects

To apply a voice changing effect, call the `setAudioChangerType(type:)` method and pass the desired enum value.

#### Code Example
``` swift
// Set voice changer effect to "child"
audioEffectStore.setAudioChangerType(type: .child)
// Disable voice changer effect
audioEffectStore.setAudioChangerType(type: .none)
```

#### setAudioChangerType Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | `AudioChangerType` | Voice changer effect enumeration. |

### Step 6: Implement Reverb Effects

To apply a reverb effect, call the `setAudioReverbType(type:) `method and pass the desired enum value.
``` swift
// Set reverb effect to "KTV"
audioEffectStore.setAudioReverbType(type: .ktv)
// Disable reverb effect
audioEffectStore.setAudioReverbType(type: .none)
```

#### setAudioReverbType Parameters
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | `AudioReverbType` | Reverb effect enumeration. |

### Step 7: Reset Audio Effect Settings

> **Note：**
> 

> AudioEffectStore and DeviceStore are singletons, so audio effect and device settings apply globally. To prevent settings from one live room affecting others, reset the audio effect and device settings when the current live room ends.
> 

Reset audio effect settings in the following scenarios:
1. When the current live room ends.

2. To provide a "one-click restore" feature, call the store's `reset()` method.

   ``` swift
   class YourMainViewController: UIViewController {    
       func reset() {
           AudioEffectStore.shared.reset()
           DeviceStore.shared.reset()
       }
   }
   ```

## API Documentation

For detailed information on all public interfaces and properties of AudioEffectStore, DeviceStore, and related classes, refer to the official API documentation for the AtomicXCore framework.
| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| DeviceStore | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. |  |
| AudioEffectStore | Audio effects: voice changing (child/male), reverb (KTV, etc.), ear monitoring adjustment, and real-time effect switching. |  |

## Platform: Flutter

## Conceptual Introduction
| **Core Concept** | **Type** | **Core Responsibilities and Description** |
| --- | --- | --- |
| AudioEffectState | `class` | Represents the current status of the audio effect module, usually used for UI rendering. Includes voice-changing status, reverberation status, ear return enabled status, ear return volume. All attributes are `ValueListenable` data type, support subscription listen. |
| AudioEffectStore | `class` | Represents the **singleton data management class** of the audio effect module. Through it you can call the audio effect API to modify sound effects. When you call the API, the appropriate `audioEffectState` property will auto-update. You can subscribe to this state's responsive data to receive and listen for status updates. |
| DeviceState | `class` | Represents the current status of the device module, usually used for UI rendering. Core attributes include device status of camera, microphone, capturing volume. All attributes are `ValueListenable` data type, support subscription listen. |
| DeviceStore | `class` | Represents the **singleton data management class** of the device module. Through it you can call the API to operate camera and microphone. When you call the API, the appropriate `state` property will auto-update. You can subscribe to this state's responsive data to receive and listen for status updates. |

## Implementation Steps

### Step 1: Integrating the Component
- **Live streaming**: Refer to quick integration to integrate **AtomicXCore** and complete service access.

- **Voice chat room**: Refer to quick integration to integrate **AtomicXCore** and complete service access.

### Step 2: Obtain a Store Instance

`AudioEffectStore` and `DeviceStore` are singleton objects. You can directly get instance via the `shared` property in any location in the project. You can also refer to the [audio_effect_panel_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/component/audio_effect/audio_effect_panel_widget.dart) file in the TUILiveKit open-source UI demo project to learn about the complete implementation logic.
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class _AudioEffectPanelWidgetState {
  final _audioEffectStore = AudioEffectStore.shared;
  final _deviceStore = DeviceStore.shared;
}
```

### Step 3: Implement the In-Ear Monitoring Feature

Ear monitoring function allows users to monitor their own microphone sound in real time. Calling the API introduced in this section can manage the ear monitoring function.

> **Note:**
> 

> The in-ear monitoring feature typically needs to connect to headphones to work properly.
> 

#### Implementation Method
1. **Enable/Disable Ear Monitoring**: You can use the `Switch` control to implement the ear monitoring function.

2. **Set the Volume of IEMs**: You can use the `Slider` control to implement the function of dragging the slider left and right to adjust volume, and map the `Slider` value to the volume value, then call `setVoiceEarMonitorVolume(volume)`. Please note, this API accepts a parameter range of `[0, 150]`, so you need to map the UI control value (such as the Slider's `0.0 - 1.0`) to the `0 - 150` range.

3. **Listen for Status Updates to Update UI**: You can subscribe to real-time ear monitoring status value updates to refresh the UI.

#### Sample Code
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// Ear Monitoring Control Component
class EarMonitorWidget extends StatelessWidget {
  const EarMonitorWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        // 1. Toggle ear monitoring
        ValueListenableBuilder<bool>(
          valueListenable: audioEffectStore.audioEffectState.isEarMonitorOpened,
          builder: (context, isOpened, child) {
            return SwitchListTile(
              title: const Text('Ear Monitoring'),
              subtitle: const Text('Insert a headset')
              value: isOpened,
              onChanged: (value) {
                audioEffectStore.setVoiceEarMonitorEnable(value);
              },
            );
          },
        ),
        // 2. Adjust ear return volume
        ValueListenableBuilder<int>(
          valueListenable: audioEffectStore.audioEffectState.earMonitorVolume,
          builder: (context, volume, child) {
            return ListTile(
              title: const Text('Ear Monitoring Volume'),
              subtitle: Slider(
                value: volume.toDouble(),
                min: 0,
                max: 150,
                divisions: 150,
                onChanged: (value) {
                  audioEffectStore.setVoiceEarMonitorVolume(value.toInt());
                },
              ),
              trailing: Text('$volume'),
            );
          },
        ),
      ],
    );
  }
}
```

#### SetVoiceEarMonitorEnable API Parameter
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `enable` | `bool` | Enable earpiece feedback. -  true: Enable -  false: Disable |

#### SetVoiceEarMonitorVolume API Parameter
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `int` | Ear return volume. - Value ranges from 0 to 150. - Default value: 100. |

### Step 4: Implement Vocal Volume Adjustment

When the Anchor hopes to adjust their own uplink capturing volume, call the `DeviceStore`'s `setCaptureVolume(volume)` method to input the volume value.

#### Implementation Method
1. **Set Capturing Volume**: You can use the `Slider` control to drag the slider left and right to adjust volume, and map the `Slider` value to the volume value before calling `setCaptureVolume(volume)`. Please note, this API accepts a parameter range of `[0, 150]`, so you need to map the UI control value (such as the Slider's `0.0 - 1.0`) to the `0 - 150` range.

2. **Listen for Status Updates to Update UI**: You can subscribe to real-time volume level status value updates to refresh the UI.

#### Sample Code
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// Voice Volume Control Component
class CaptureVolumeWidget extends StatelessWidget {
  const CaptureVolumeWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final deviceStore = DeviceStore.shared;

    return ValueListenableBuilder<int>(
      valueListenable: deviceStore.state.captureVolume,
      builder: (context, volume, child) {
        return ListTile(
          title: const Text('Voice Volume'),
          subtitle: Slider(
            value: volume.toDouble(),
            min: 0,
            max: 150,
            divisions: 150,
            onChanged: (value) {
              deviceStore.setCaptureVolume(value.toInt());
            },
          ),
          trailing: Text('$volume'),
        );
      },
    );
  }
}
```

#### SetCaptureVolume API Parameter
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `volume` | `int` | Capture volume size. - Value ranges from 0 to 150. - Default value: 100. |

### Step 5: Implement Voice-Changing Effects

When the Anchor hopes to change their own sound, you can call the `setAudioChangerType(type)` method, input the appropriate Type enumeration value to switch effect.

#### Sample Code
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// Voice changing effect selection component
class VoiceChangerWidget extends StatelessWidget {
  const VoiceChangerWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        const Padding(
          padding: EdgeInsets.all(16),
          child: Text('Voice-changing effect', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        ),
        ValueListenableBuilder<AudioChangerType>(
          valueListenable: audioEffectStore.audioEffectState.audioChangerType,
          builder: (context, changerType, child) {
            return Padding(
              padding: const EdgeInsets.symmetric(horizontal: 16),
              child: Wrap(
                spacing: 8,
                runSpacing: 8,
                children: AudioChangerType.values.map((type) {
                  return ChoiceChip(
                    label: Text(_getChangerTypeName(type)),
                    selected: changerType == type,
                    onSelected: (selected) {
                      if (selected) {
                        audioEffectStore.setAudioChangerType(type);
                      }
                    },
                  );
                }).toList(),
              ),
            );
          },
        ),
      ],
    );
  }

  String _getChangerTypeName(AudioChangerType type) {
    switch (type) {
      case AudioChangerType.none:
        return 'Disable';
      case AudioChangerType.child:
        return 'mischievous child';
      case AudioChangerType.girl:
        return 'loli';
      case AudioChangerType.uncle:
        return 'uncle';
      case AudioChangerType.ethereal:
        return 'ethereal';
    }
  }
}
```

#### SetAudioChangerType API Parameter
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | AudioChangerType | Voice-changing effects enumeration. |

### Step 6: Implement Reverb Effect

When the Anchor hopes to add reverb effect to their own sound, you can call the `setAudioReverbType(type)` method, input the appropriate Type enumeration value to switch effect.

#### Sample Code
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// Reverb effect selection component
class ReverbWidget extends StatelessWidget {
  const ReverbWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        const Padding(
          padding: EdgeInsets.all(16),
          child: Text('Reverb effect', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
        ),
        ValueListenableBuilder<AudioReverbType>(
          valueListenable: audioEffectStore.audioEffectState.audioReverbType,
          builder: (context, reverbType, child) {
            return Padding(
              padding: const EdgeInsets.symmetric(horizontal: 16),
              child: Wrap(
                spacing: 8,
                runSpacing: 8,
                children: AudioReverbType.values.map((type) {
                  return ChoiceChip(
                    label: Text(_getReverbTypeName(type)),
                    selected: reverbType == type,
                    onSelected: (selected) {
                      if (selected) {
                        audioEffectStore.setAudioReverbType(type);
                      }
                    },
                  );
                }).toList(),
              ),
            );
          },
        ),
      ],
    );
  }

  String _getReverbTypeName(AudioReverbType type) {
    switch (type) {
      case AudioReverbType.none:
        return 'Disable';
      case AudioReverbType.ktv:
        return 'KTV';
      case AudioReverbType.room:
        return 'Small room';
      case AudioReverbType.hall:
        return 'Great hall';
      case AudioReverbType.deep:
        return 'deep';
      case AudioReverbType.loud:
        return 'resonant';
      case AudioReverbType.metallic:
        return 'metallic sound';
      case AudioReverbType.magnetic:
        return 'magnetic';
      case AudioReverbType.recordingStudio:
        return 'studio';
    }
  }
}
```

#### SetAudioReverbType API Parameter
| **Parameter Name** | **Type** | **Description** |
| --- | --- | --- |
| `type` | AudioReverbType | Reverb effects enumeration. |

### Step 7: Reset Sound Effect Settings

> **Important:**
> 

> AudioEffectStore and DeviceStore are singletons, so audio effects and device settings take effect globally. If you do not want the settings of the current live streaming room to affect other live streaming rooms created later, you need to reset the audio effects and device settings when the current live streaming room ends.
> 

You may need to reset sound effect settings in the following two scenarios:
1. **When the current live streaming room ends**, call the `store`'s `reset()` method to reset sound effects.

2. **When you need to reset all devices and sound effect settings**, call the `store`'s `reset()` method to reset sound effects.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// Sound effect reset component
   class AudioEffectResetWidget extends StatelessWidget {
     const AudioEffectResetWidget({super.key});
   
     void _resetAudioEffect() {
       AudioEffectStore.shared.reset();
       DeviceStore.shared.reset();
     }
   
     @override
     Widget build(BuildContext context) {
       return ElevatedButton(
         onPressed: _resetAudioEffect,
         child: const Text('Reset sound effect settings'),
       );
     }
   }
   ```

## Complete Example
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// Sound effect settings page
class AudioEffectPage extends StatelessWidget {
  const AudioEffectPage({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;
    final deviceStore = DeviceStore.shared;

    return Scaffold(
      appBar: AppBar(title: const Text('Sound effect settings')),
      body: ListView(
        children: [
          // Voice volume
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('Voice volume', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<int>(
            valueListenable: deviceStore.state.captureVolume,
            builder: (context, volume, child) {
              return ListTile(
                title: const Text('Capture Volume'),
                subtitle: Slider(
                  value: volume.toDouble(),
                  min: 0,
                  max: 150,
                  divisions: 150,
                  onChanged: (value) {
                    deviceStore.setCaptureVolume(value.toInt());
                  },
                ),
                trailing: Text('$volume'),
              );
            },
          ),
          const Divider(),

          // Ear return settings
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('Ear monitoring', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<bool>(
            valueListenable: audioEffectStore.audioEffectState.isEarMonitorOpened,
            builder: (context, isOpened, child) {
              return SwitchListTile(
                title: const Text('Enable Ear Monitoring'),
                subtitle: const Text('Insert a headset')
                value: isOpened,
                onChanged: (value) {
                  audioEffectStore.setVoiceEarMonitorEnable(value);
                },
              );
            },
          ),
          ValueListenableBuilder<int>(
            valueListenable: audioEffectStore.audioEffectState.earMonitorVolume,
            builder: (context, volume, child) {
              return ListTile(
                title: const Text('Ear Monitoring Volume'),
                subtitle: Slider(
                  value: volume.toDouble(),
                  min: 0,
                  max: 150,
                  divisions: 150,
                  onChanged: (value) {
                    audioEffectStore.setVoiceEarMonitorVolume(value.toInt());
                  },
                ),
                trailing: Text('$volume'),
              );
            },
          ),
          const Divider(),

          // Voice changing settings
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('Voice-changing', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<AudioChangerType>(
            valueListenable: audioEffectStore.audioEffectState.audioChangerType,
            builder: (context, changerType, child) {
              return Padding(
                padding: const EdgeInsets.symmetric(horizontal: 16),
                child: Wrap(
                  spacing: 8,
                  runSpacing: 8,
                  children: AudioChangerType.values.map((type) {
                    return ChoiceChip(
                      label: Text(_getChangerTypeName(type)),
                      selected: changerType == type,
                      onSelected: (selected) {
                        if (selected) {
                          audioEffectStore.setAudioChangerType(type);
                        }
                      },
                    );
                  }).toList(),
                ),
              );
            },
          ),
          const SizedBox(height: 16),
          const Divider(),

          // Reverb settings
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('Reverb', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<AudioReverbType>(
            valueListenable: audioEffectStore.audioEffectState.audioReverbType,
            builder: (context, reverbType, child) {
              return Padding(
                padding: const EdgeInsets.symmetric(horizontal: 16),
                child: Wrap(
                  spacing: 8,
                  runSpacing: 8,
                  children: AudioReverbType.values.map((type) {
                    return ChoiceChip(
                      label: Text(_getReverbTypeName(type)),
                      selected: reverbType == type,
                      onSelected: (selected) {
                        if (selected) {
                          audioEffectStore.setAudioReverbType(type);
                        }
                      },
                    );
                  }).toList(),
                ),
              );
            },
          ),
          const SizedBox(height: 24),

          // reset button
          Padding(
            padding: const EdgeInsets.all(16),
            child: ElevatedButton(
              onPressed: () {
                audioEffectStore.reset();
                deviceStore.reset();
              },
              child: const Text('Reset sound effect settings'),
            ),
          ),
        ],
      ),
    );
  }

  String _getChangerTypeName(AudioChangerType type) {
    switch (type) {
      case AudioChangerType.none:
        return 'Disable';
      case AudioChangerType.child:
        return 'mischievous child';
      case AudioChangerType.girl:
        return 'loli';
      case AudioChangerType.uncle:
        return 'uncle';
      case AudioChangerType.ethereal:
        return 'ethereal';
    }
  }

  String _getReverbTypeName(AudioReverbType type) {
    switch (type) {
      case AudioReverbType.none:
        return 'Disable';
      case AudioReverbType.ktv:
        return 'KTV';
      case AudioReverbType.room:
        return 'Small room';
      case AudioReverbType.hall:
        return 'Great hall';
      case AudioReverbType.deep:
        return 'deep';
      case AudioReverbType.loud:
        return 'resonant';
      case AudioReverbType.metallic:
        return 'metallic sound';
      case AudioReverbType.magnetic:
        return 'magnetic';
      case AudioReverbType.recordingStudio:
        return 'studio';
    }
  }
}
```

## API documentation

For detailed information about all public interfaces and attributes of AudioEffectStore, DeviceStore and its related classes, see the official API documentation of the AtomicXCore framework.
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **AudioEffectStore** | Audio effect management: Perform audio effect settings and obtain real-time audio effect status. |  |
| **DeviceStore** | Device management: perform camera and microphone operations and obtain real-time device status. |  |

## FAQs

### Are there any restrictions on when to call audio effect and device APIs?

> **Platforms: Android / iOS**

> **Android：** No. `AudioEffectStore` and `DeviceStore` settings are globally effective. You can call relevant APIs (such as setting voice effects, reverb, or ear monitoring) at any time before or after entering a live room. Settings take effect immediately and persist.
> **iOS：** No. `AudioEffectStore` and `DeviceStore` settings are globally effective. You can call relevant APIs (such as setting voice changing effects, reverb, or ear monitoring) at any time before or after entering a live room. Settings take effect immediately and persist.

### What is the difference between "Microphone Volume" and "Ear Monitor Volume"?

> **Platforms: Android / iOS**

This is a common point of confusion:
- **Microphone Volume (Capture Volume)**: Set via `DeviceStore.setCaptureVolume()`. Controls the volume your audience hears.

- **Ear Monitor Volume**: Set via `AudioEffectStore.setVoiceEarMonitorVolume()`.  It only determines the volume level you hear in your in-ear monitor and does not affect the audience.

### Why does a newly created live room already have audio effects, or why is the volume too high or too low?

> **Platforms: Android / iOS**

Because `AudioEffectStore` and `DeviceStore` are singletons, audio effect and device settings are globally applied. If you previously set audio effects and did not reset them, those settings persist. Call the `reset()` method at the appropriate time to clear them.

### Why can't I hear my own voice after enabling "Ear Monitoring"?

> **Platforms: Android / iOS**

Check the following:
1. **Are headphones connected?** Ear monitoring generally requires headphones. Ensure wired or Bluetooth headphones are connected.

2. **Is the ear monitor volume set to 0?** Check that the ear monitor volume is above 0.

3. **Is the microphone enabled?** Confirm that the microphone is enabled in DeviceStore (i.e., openLocalMicrophone has been called).

### Can voice effects and reverb be enabled at the same time?

> **Platforms: Android / iOS**

Yes. `AudioChangerType` and `AudioReverbType` can be enabled simultaneously. For example, you can set both `AudioChangerType.LITTLE_GIRL`and `AudioReverbType.KTV` at the same time.

### Is There a Requirement for the Timing of Invocation of Sound Effects and Device Interfaces?

> **Platforms: Flutter**

No limit. The settings of `AudioEffectStore` and `DeviceStore` take effect globally. You can call APIs (such as setting voice-changing, reverb, or ear monitoring) at any time before or after entering the live room. The settings will take effect immediately and be reserved.

### What Is the Difference between Voice Volume and Ear Return Volume?

> **Platforms: Flutter**

This is a very important and confusing concept. The difference is as follows:
- **Voice Volume (CaptureVolume):** Set via `DeviceStore.shared.setCaptureVolume()`. It determines the Anchor's sound level heard by the audience.

- **Ear Return Volume (EarMonitorVolume):** Set via `AudioEffectStore.shared.setVoiceEarMonitorVolume()`. It only determines the Anchor's headphone monitoring volume and **does not** impact the audience.

### Why Does a Newly Created Live Streaming Room Have Sound Effects Directly or Issues Like Very High or Low Volume?

> **Platforms: Flutter**

`AudioEffectStore` and `DeviceStore` are singletons, which means audio effects and device settings take effect globally. This situation probably occurs because you set audio effects earlier but did not reset them. You need to call the `reset()` method in an appropriate place.

### Why Did I Enable "Ear Return" but Cannot Hear My Own Sound?

> **Platforms: Flutter**

Please check by the following steps:
1. **Whether to insert headphones**: The in-ear monitoring feature depends on headphones most of the time for the changes to take effect. Please ensure wired or Bluetooth headphones are connected.

2. **Whether the volume is 0**: Check if the "ear return volume" is set to 0.

3. **Is the microphone enabled**: Confirm that the microphone in `DeviceStore` is enabled (the `openLocalMicrophone()` call has been made).

### Can Voice-Changing Effects and Reverb Effects Take Effect at the Same Time?

> **Platforms: Flutter**

Yes. `AudioChangerType` and `AudioReverbType` can take effect in superposition. For example, you can simultaneously set `AudioChangerType.girl` voice-changing and `AudioReverbType.ktv` reverb.
