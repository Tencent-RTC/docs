> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, uni-app**

本篇文档旨在指导您如何使用 `AtomicXCore` 框架中的  `AudioEffectStore` 和 `DeviceStore` 模块，为您的直播/语聊房应用快速集成音效控制功能，包括麦克风音量、耳返监听，并添加多种趣味变声效果和混响效果。

## 核心功能
通过组合 `AudioEffectStore` 和 `DeviceStore`，您将能实现以下核心功能：
- **人声音量调节**：控制本地音频上行的采集音量大小，即观众听到的您的声音大小。

- **耳返功能**：允许主播或连麦用户通过耳机实时听到自己的声音，用于监听和矫正发音。

- **变声效果**：提供多种趣味变声选项，例如萝莉、大叔、熊孩子等。

- **混响效果**：模拟不同环境的声场效果，例如 KTV、金属声、低沉、洪亮等。

## API 文档
关于 AudioEffectStore 和 DeviceStore 及其相关类的所有公开接口、属性的详细信息，请参阅随 AtomicXCore 框架的官方 API 文档。
| **Store/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **AudioEffectStore** | 音效管理：进行音效设置，并获取实时音效状态。 |  |
| **DeviceStore ** | 设备管理：进行摄像头、麦克风操作，并获取实时设备状态。 |  |

## Platform: Android

## 概念说明
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| `AudioChangerType` | `enum class` | 变声效果枚举，例如，“小孩”，“男人”等。 |
| `AudioReverbType` | `enum class` | 混响效果枚举，例如，“ktv”，“礼堂”等。 |
| `AudioEffectState` | `data class` | 代表音效模块的当前状态，通常用于 UI 渲染。包括变声状态、混响状态、耳返开启状态、耳返音量。 |
| `AudioEffectStore` | `abstract class` | 代表音效模块**单例数据管理类**。通过它您可以调用修改音效接口，当您调用接口后，相应的 `state` 属性会自动更新，可以通过订阅这个 state 响应式数据来接收和监听状态更新。 |
| `DeviceState` | `data class` | 代表设备模块的当前状态，通常用于 UI 渲染。核心属性包括摄像头、麦克风的设备状态。 |
| `DeviceStore` | `abstract class` | 代表设备模块**单例数据管理类**。通过它您可以调用操作摄像头、麦克风接口，当您调用接口后，相应的`state`属性会自动更新，可以通过订阅这个 state 响应式数据来接收和监听状态更新。 |

## 实现步骤

### 步骤1：组件集成

请参考 快速接入 集成 **AtomicXCore。**

### 步骤2：获取 Store 实例

`AudioEffectStore` 和 `DeviceStore`是单例对象，您可以直接通过 shared 属性在项目的任何位置获取实例。您也可以参考 TUILiveKit 开源UI demo项目中的 [AudioEffectPanel.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/component/audioeffect/AudioEffectPanel.kt) 文件来了解完整的实现逻辑。
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

val audioEffectStore = AudioEffectStore.shared()
val deviceStore = DeviceStore.shared()
```

### 步骤3：实现耳返功能

耳返功能允许用户实时监听自己的麦克风声音，可以通过调用本节介绍的接口管理耳返功能。

> **注意：**
> 

> 耳返功能通常需要连接耳机才能正常工作。
> 

#### 实现方式:
1. **开关耳返**：您可以使用`Switch`控件实现开关耳返功能。

2. **设置耳返音量值**：您可以使用 `SeekBar` 控件实现左右拖动滑块来调节音量的功能，并将 `SeekBar` 的值映射为音量值，调用`setOutputVolume(volume)。`请注意，该接口接收的参数范围是 `[0, 150]，`所以您需要将 UI 控件的值（例如 UISlider 的 `0 - 1.0`）映射到 `0 - 150` 的范围。

3. **监听状态更新 UI**：您可以订阅实时的耳返状态值更新 `UI`。

#### 代码示例:
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// 1. 开启耳返（建议提示用户插入耳机）
audioEffectStore.setVoiceEarMonitorEnable(true)

// 2. 调节耳返音量
audioEffectStore.setVoiceEarMonitorVolume(80)

// 3. 关闭耳返
audioEffectStore.setVoiceEarMonitorEnable(false)

// 4. 监听状态
CoroutineScope(Dispatchers.Main).launch {
  audioEffectStore.audioEffectState.earMonitorVolume.collect {
    print("耳返音量: $it")
    //更新UI 显示数值
   }
}
```

#### setVoiceEarMonitorEnable 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `enable` | `Boolean` | 是否开启耳返。 - true：开启 - false：关闭 |

#### setVoiceEarMonitorVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `Int` | 耳返音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤4：实现人声音量调节

当主播希望调节自己的上行采集音量大小时，调用`DeviceStore`的`setCaptureVolume(volume)`方法传入音量值即可。

#### 实现方式:
1. **设置采集音量值**：您可以使用 `SeekBar` 控件实现左右拖动滑块来调节音量的功能，并将 `SeekBar` 的值映射成音量值后调用`setCaptureVolume(80)。`请注意，该接口接收的参数范围是 `[0, 150]，`所以您需要将 `UI` 控件的值（例如 `SeekBar` 的 `0 - 150`）映射到 `0 - 150` 的范围。

2. **监听状态更新 UI**：您可以订阅实时的音量状态值更新 UI。

#### 示例代码：
``` java
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

// 1. 调节音量
deviceStore.setCaptureVolume(80)

// 2. 监听数据变化
CoroutineScope(Dispatchers.Main).launch {
   deviceStore.deviceState.captureVolume.collect {
     //更新显示数值
     print("采集音量: $it")
   }
}
```

#### setCaptureVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `Int` | 采集音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤5：实现变声效果

当主播希望改变自己的声音时，您可以调用 `setAudioChangerType(type)`方法，传入相应的Type 枚举值即可切换效果。

#### 示例代码：
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.AudioChangerType

// 示例：设置变声为“萝莉”
audioEffectStore.setAudioChangerType(AudioChangerType.LITTLE_GIRL)

// 示例：关闭变声
audioEffectStore.setAudioChangerType(AudioChangerType.NONE)
```

#### setAudioChangerType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | `AudioChangerType` | 变声效果枚举。 |

### 步骤6：实现混响效果

当主播希望给自己的声音添加混响效果时，您可以调用`setAudioReverbType(type)` 方法，传入相应的Type 枚举值即可切换效果。
``` java
import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
import io.trtc.tuikit.atomicxcore.api.device.AudioReverbType

// 示例：设置混响为“KTV”
audioEffectStore.setAudioReverbType(AudioReverbType.KTV)

// 示例：关闭混响
audioEffectStore.setAudioReverbType(AudioReverbType.NONE)
```

#### setAudioReverbType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | `AudioReverbType` | 混响效果枚举。 |

### 步骤7：重置音效设置

> **重要：**
> 

> `AudioEffectStore` 和 `DeviceStore` 是单例，这意味着音效和设备设置是全局生效的，如果您在当前直播间的设置不想影响到您创建的其他直播间，就需要在当前直播间结束的时候重置音效和设备设置。
> 

以下两种情况您可能需要重置音效设置：
1. 当前直播间结束时，需要重置音效设置。

2. 您需要提供“一键恢复”的功能，可以通过调用 `store` 的 `reset()` 方法实现。

   ``` java
   import io.trtc.tuikit.atomicxcore.api.device.AudioEffectStore
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   
   // 一键重置所有变声和混响效果
   AudioEffectStore.shared().reset()
   DeviceStore.shared().reset()
   
   ```

## Platform: iOS

## 概念说明
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| `AudioChangerType` | `enum` | 变声效果枚举，例如，“小孩”，“男人”等。 |
| `AudioReverbType` | `enum` | 混响效果枚举，例如，“ktv”，“礼堂”等。 |
| `AudioEffectState` | `struct` | 代表音效模块的当前状态，通常用于UI渲染。包括变声状态、混响状态、耳返开启状态、耳返音量。 |
| `AudioEffectStore` | `class` | 代表音效模块**单例数据管理类**。通过它您可以调用修改音效接口，当您调用接口后，相应的`state`属性会自动更新，可以通过订阅这个state响应式数据来接收和监听状态更新。 |
| `DeviceState` | `struct` | 代表设备模块的当前状态，通常用于UI渲染。核心属性包括摄像头、麦克风的设备状态。 |
| `DeviceStore` | `class` | 代表设备模块**单例数据管理类**。通过它您可以调用操作摄像头、麦克风接口，当您调用接口后，相应的`state`属性会自动更新，可以通过订阅这个state响应式数据来接收和监听状态更新。 |

## 实现步骤

### 步骤1：组件集成

请参考 快速接入 集成 **AtomicXCore。**

### 步骤2：获取 Store 实例

`AudioEffectStore` 和 `DeviceStore`是单例对象，您可以直接通过 shared 属性在项目的任何位置获取实例。您也可以参考 TUILiveKit 开源UI demo项目中的 [AudioEffectView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Component/AudioEffect/AudioEffectView.swift) 文件来了解完整的实现逻辑。
``` swift
import UIKit
import AtomicXCore
import Combine

let audioEffectStore = AudioEffectStore.shared
let deviceStore = DeviceStore.shared
```

### 步骤3：实现耳返功能

耳返功能允许用户实时监听自己的麦克风声音，可以通过调用本节介绍的接口管理耳返功能。

> **注意：**
> 

> 耳返功能通常需要连接耳机才能正常工作。
> 

#### 实现方式:
1. **开关耳返**：您可以使用`UISwitch`控件实现开关耳返功能。

2. **设置耳返音量值**：您可以使用 `UISlider` 控件实现左右拖动滑块来调节音量的功能，并将 `UISlider` 的值映射成音量值，调用`setOutputVolume(_ volume:)。`请注意，该接口接收的参数范围是 `[0, 150]，`所以您需要将 UI 控件的值（例如 UISlider 的 `0 - 1.0`）映射到 `0 - 150` 的范围。

3. **监听状态更新 UI**：您可以订阅实时的耳返状态值更新 UI。

#### 代码示例:
``` swift
// 1. 开启耳返
audioEffectStore.setVoiceEarMonitorEnable(enable: true)

// 2. 调节耳返音量
audioEffectStore.setVoiceEarMonitorVolume(volume: 80)

// 3. 关闭耳返
audioEffectStore.setVoiceEarMonitorEnable(enable: false)

// 4. 监听状态
audioEffectStore.state
    .subscribe(StatePublisherSelector(keyPath: \AudioEffectState.earMonitorVolume))
    .removeDuplicates()
    .sink { currentVolume in
        print("耳返音量: \(currentVolume)")
        valueLabel.text = "\(currentVolume)" //更新UILabel显示数值
    }
    .store(in: &cancellables)
```

#### setVoiceEarMonitorEnable 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `enable` | `Bool` | 是否开启耳返。 - true：开启 - false：关闭 |

#### setVoiceEarMonitorVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `Int` | 耳返音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤4：实现人声音量调节

当主播希望调节自己的上行采集音量大小时，调用`DeviceStore`的`setCaptureVolume(volume: )`方法传入音量值即可。

#### 实现方式:
1. **设置采集音量值**：您可以使用 `UISlider` 控件实现左右拖动滑块来调节音量的功能，并将 `UISlider` 的值映射成音量值后调用`setCaptureVolume(volume: 80)。`请注意，该接口接收的参数范围是 `[0, 150]，`所以您需要将 UI 控件的值（例如 UISlider 的 `0 - 1.0`）映射到 `0 - 150` 的范围。

2. **监听状态更新 UI**：您可以订阅实时的音量状态值更新 UI。

#### 示例代码：
``` swift
// 1. 调节音量
deviceStore.setCaptureVolume(volume: 80)

// 2. 监听状态
deviceStore.state
    .subscribe(StatePublisherSelector(keyPath: \DeviceState.captureVolume))
    .removeDuplicates()
    .sink { captureVolume in
        print("采集音量: \(captureVolume)")
        valueLabel.text = "\(captureVolume)" //更新UILabel显示数值
    }
    .store(in: &cancellables)
```

#### setCaptureVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `Int` | 采集音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤5：实现变声效果

当主播希望改变自己的声音时，您可以调用 `setAudioChangerType(type:)`方法，传入相应的Type 枚举值即可切换效果。

#### 示例代码：
``` swift
// 示例：将变声效果设置为“小孩”
audioEffectStore.setAudioChangerType(type: .child)
// 示例：关闭变声效果
audioEffectStore.setAudioChangerType(type: .none)
```

#### setAudioChangerType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | `AudioChangerType` | 变声效果枚举。 |

### 步骤6：实现混响效果

当主播希望给自己的声音添加混响效果时，您可以调用`setAudioReverbType(type:)` 方法，传入相应的Type 枚举值即可切换效果。
``` swift
// 示例：设置为“KTV”混响效果
audioEffectStore.setAudioReverbType(type: .ktv)
// 示例：关闭混响效果
audioEffectStore.setAudioReverbType(type: .none)
```

#### setAudioReverbType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | `AudioReverbType` | 混响效果枚举。 |

### 步骤7：重置音效设置

> **重要：**
> 

> AudioEffectStore和DeviceStore是单例，这意味着音效和设备设置是全局生效的，如果您在当前直播间的设置不想影响到您创建的其他直播间，就需要在当前直播间结束的时候重置音效和设备设置。
> 

以下两种情况您可能需要重置音效设置：
1. 当前直播间结束时，需要重置音效设置。

2. 您需要提供“一键恢复”的功能，可以通过调用 store的`reset()` 方法实现。

   ``` swift
   class YourMainViewController: UIViewController {    
       func reset() {
           AudioEffectStore.shared.reset()
           DeviceStore.shared.reset()
       }
   }
   ```

## Platform: Flutter

## 概念说明
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| AudioEffectState | `class` | 代表音效模块的当前状态，通常用于UI渲染。包括变声状态、混响状态、耳返开启状态、耳返音量。所有属性均为 `ValueListenable` 类型，支持订阅监听。 |
| AudioEffectStore | `class` | 代表音效模块**单例数据管理类**。通过它您可以调用修改音效接口，当您调用接口后，相应的 `audioEffectState` 属性会自动更新，可以通过订阅这个 state 响应式数据来接收和监听状态更新。 |
| DeviceState | `class` | 代表设备模块的当前状态，通常用于UI渲染。核心属性包括摄像头、麦克风的设备状态、采集音量等。所有属性均为 `ValueListenable` 类型，支持订阅监听。 |
| DeviceStore | `class` | 代表设备模块**单例数据管理类**。通过它您可以调用操作摄像头、麦克风接口，当您调用接口后，相应的 `state` 属性会自动更新，可以通过订阅这个 state 响应式数据来接收和监听状态更新。 |

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：获取 Store 实例

`AudioEffectStore` 和 `DeviceStore` 是单例对象，您可以直接通过 `shared` 属性在项目的任何位置获取实例。您也可以参考 TUILiveKit 开源 UI demo 项目中的 [audio_effect_panel_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/component/audio_effect/audio_effect_panel_widget.dart) 文件来了解完整的实现逻辑。
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class _AudioEffectPanelWidgetState {
  final _audioEffectStore = AudioEffectStore.shared;
  final _deviceStore = DeviceStore.shared;
}
```

### 步骤3：实现耳返功能

耳返功能允许用户实时监听自己的麦克风声音，可以通过调用本节介绍的接口管理耳返功能。

> **注意：**
> 

> 耳返功能通常需要连接耳机才能正常工作。
> 

#### 实现方式
1. **开关耳返**：您可以使用 `Switch` 控件实现开关耳返功能。

2. **设置耳返音量值**：您可以使用 `Slider` 控件实现左右拖动滑块来调节音量的功能，并将 `Slider` 的值映射成音量值，调用 `setVoiceEarMonitorVolume(volume)`。请注意，该接口接收的参数范围是 `[0, 150]`，所以您需要将 UI 控件的值（例如 Slider 的 `0.0 - 1.0`）映射到 `0 - 150` 的范围。

3. **监听状态更新 UI**：您可以订阅实时的耳返状态值更新 UI。

#### 代码示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// 耳返控制组件
class EarMonitorWidget extends StatelessWidget {
  const EarMonitorWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        // 1. 开关耳返
        ValueListenableBuilder<bool>(
          valueListenable: audioEffectStore.audioEffectState.isEarMonitorOpened,
          builder: (context, isOpened, child) {
            return SwitchListTile(
              title: const Text('耳返'),
              subtitle: const Text('需要插入耳机'),
              value: isOpened,
              onChanged: (value) {
                audioEffectStore.setVoiceEarMonitorEnable(value);
              },
            );
          },
        ),
        // 2. 调节耳返音量
        ValueListenableBuilder<int>(
          valueListenable: audioEffectStore.audioEffectState.earMonitorVolume,
          builder: (context, volume, child) {
            return ListTile(
              title: const Text('耳返音量'),
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

#### setVoiceEarMonitorEnable 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `enable` | `bool` | 是否开启耳返。 -  true：开启 -  false：关闭 |

#### setVoiceEarMonitorVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `int` | 耳返音量大小。 - 取值范围：[0, 150] - 默认值：100 |

### 步骤4：实现人声音量调节

当主播希望调节自己的上行采集音量大小时，调用 `DeviceStore` 的 `setCaptureVolume(volume)` 方法传入音量值即可。

#### 实现方式
1. **设置采集音量值**：您可以使用 `Slider` 控件实现左右拖动滑块来调节音量的功能，并将 `Slider` 的值映射成音量值后调用 `setCaptureVolume(volume)`。请注意，该接口接收的参数范围是 `[0, 150]`，所以您需要将 UI 控件的值（例如 Slider 的 `0.0 - 1.0`）映射到 `0 - 150` 的范围。

2. **监听状态更新 UI**：您可以订阅实时的音量状态值更新 UI。

#### 代码示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// 人声音量控制组件
class CaptureVolumeWidget extends StatelessWidget {
  const CaptureVolumeWidget({super.key});

  @override
  Widget build(BuildContext context) {
    final deviceStore = DeviceStore.shared;

    return ValueListenableBuilder<int>(
      valueListenable: deviceStore.state.captureVolume,
      builder: (context, volume, child) {
        return ListTile(
          title: const Text('人声音量'),
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

#### setCaptureVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `int` | 采集音量大小。 - 取值范围：[0, 150] - 默认值：100 |

### 步骤5：实现变声效果

当主播希望改变自己的声音时，您可以调用 `setAudioChangerType(type)` 方法，传入相应的 Type 枚举值即可切换效果。

#### 代码示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// 变声效果选择组件
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
          child: Text('变声效果', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
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
        return '关闭';
      case AudioChangerType.child:
        return '熊孩子';
      case AudioChangerType.girl:
        return '萝莉';
      case AudioChangerType.uncle:
        return '大叔';
      case AudioChangerType.ethereal:
        return '空灵';
    }
  }
}
```

#### setAudioChangerType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | AudioChangerType | 变声效果枚举。 |

### 步骤6：实现混响效果

当主播希望给自己的声音添加混响效果时，您可以调用 `setAudioReverbType(type)` 方法，传入相应的 Type 枚举值即可切换效果。

#### 代码示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// 混响效果选择组件
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
          child: Text('混响效果', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
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
        return '关闭';
      case AudioReverbType.ktv:
        return 'KTV';
      case AudioReverbType.room:
        return '小房间';
      case AudioReverbType.hall:
        return '大会堂';
      case AudioReverbType.deep:
        return '低沉';
      case AudioReverbType.loud:
        return '洪亮';
      case AudioReverbType.metallic:
        return '金属声';
      case AudioReverbType.magnetic:
        return '磁性';
      case AudioReverbType.recordingStudio:
        return '录音棚';
    }
  }
}
```

#### setAudioReverbType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `type` | AudioReverbType | 混响效果枚举。 |

### 步骤7：重置音效设置

> **重要：**
> 

> AudioEffectStore 和 DeviceStore 是单例，即音效和设备设置会全局生效，如果您不希望当前直播间的设置影响到之后创建的其他直播间，就需要在当前直播间结束的时候重置音效和设备设置。
> 

以下两种情况您可能需要重置音效设置：
1. **当前直播间结束时**，调用 `store` 的 `reset()` 方法实现音效重置。

2. **需要重置所有设备和音效设置时**，调用 `store` 的 `reset()` 方法实现音效重置。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// 音效重置组件
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
         child: const Text('重置音效设置'),
       );
     }
   }
   ```

## 完整示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// 音效设置页面
class AudioEffectPage extends StatelessWidget {
  const AudioEffectPage({super.key});

  @override
  Widget build(BuildContext context) {
    final audioEffectStore = AudioEffectStore.shared;
    final deviceStore = DeviceStore.shared;

    return Scaffold(
      appBar: AppBar(title: const Text('音效设置')),
      body: ListView(
        children: [
          // 人声音量
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('人声音量', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<int>(
            valueListenable: deviceStore.state.captureVolume,
            builder: (context, volume, child) {
              return ListTile(
                title: const Text('采集音量'),
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

          // 耳返设置
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('耳返', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
          ),
          ValueListenableBuilder<bool>(
            valueListenable: audioEffectStore.audioEffectState.isEarMonitorOpened,
            builder: (context, isOpened, child) {
              return SwitchListTile(
                title: const Text('开启耳返'),
                subtitle: const Text('需要插入耳机'),
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
                title: const Text('耳返音量'),
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

          // 变声设置
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('变声', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
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

          // 混响设置
          const Padding(
            padding: EdgeInsets.all(16),
            child: Text('混响', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
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

          // 重置按钮
          Padding(
            padding: const EdgeInsets.all(16),
            child: ElevatedButton(
              onPressed: () {
                audioEffectStore.reset();
                deviceStore.reset();
              },
              child: const Text('重置音效设置'),
            ),
          ),
        ],
      ),
    );
  }

  String _getChangerTypeName(AudioChangerType type) {
    switch (type) {
      case AudioChangerType.none:
        return '关闭';
      case AudioChangerType.child:
        return '熊孩子';
      case AudioChangerType.girl:
        return '萝莉';
      case AudioChangerType.uncle:
        return '大叔';
      case AudioChangerType.ethereal:
        return '空灵';
    }
  }

  String _getReverbTypeName(AudioReverbType type) {
    switch (type) {
      case AudioReverbType.none:
        return '关闭';
      case AudioReverbType.ktv:
        return 'KTV';
      case AudioReverbType.room:
        return '小房间';
      case AudioReverbType.hall:
        return '大会堂';
      case AudioReverbType.deep:
        return '低沉';
      case AudioReverbType.loud:
        return '洪亮';
      case AudioReverbType.metallic:
        return '金属声';
      case AudioReverbType.magnetic:
        return '磁性';
      case AudioReverbType.recordingStudio:
        return '录音棚';
    }
  }
}
```

## Platform: uni-app

## 实现步骤

### 步骤1：组件集成

请参考 快速接入 集成 **AtomicXCore。**

### 步骤2：获取 State 实例

`AudioEffectState` 和 `DeviceState`是单例对象，您可以在项目的任何位置获取实例。
``` typescript
import { useAudioEffectState } from '@/uni_modules/tuikit-atomic-x/state/AudioEffectState'
import { useDeviceState } from "@/uni_modules/tuikit-atomic-x/state/DeviceState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

// 通过 liveID 获取对应实例
useAudioEffectState(liveID)
useDeviceState(liveID)
```

### 步骤3：实现耳返功能

耳返功能允许用户实时监听自己的麦克风声音，可以通过调用本节介绍的接口管理耳返功能。

> **注意：**
> 

> 耳返功能通常需要连接耳机才能正常工作。
> 

#### 实现方式:
1. **开关耳返**：您可以在 `UI` 上自行实现开关耳返功能。

2. **设置耳返音量值**：您可以自行实现左右拖动滑块来调节音量的功能，并将您 `UI` 上的值映射成音量值，调用 `setVoiceEarMonitorVolume(volume:)。`请注意，该接口接收的参数范围是 `[0, 150]`，所以您需要将 UI 控件的值映射到 `0 - 150` 的范围。

3. **监听状态更新 UI**：您可以订阅实时的耳返状态值更新 UI。

#### 代码示例:
``` typescript
import { watch } from 'vue'
import { useAudioEffectState } from '@/uni_modules/tuikit-atomic-x/state/AudioEffectState'

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

// 通过 liveID 获取对应实例
const { setVoiceEarMonitorEnable, setVoiceEarMonitorVolume, earMonitorVolume } = useAudioEffectState(liveID)

// 1. 开启/关闭耳返
const toggleEarMonitor = () => {
    setVoiceEarMonitorEnable({
      enable: true // true 为开启， false 为关闭
     })
}

// 2. 调节耳返音量
const adjustEarMonitorVolume = () => {
    setVoiceEarMonitorVolume({
      volume: 80
     })
}

// 3.监听耳返音量变化
watch(earMonitorVolume, (newVolume) => {
  console.log('耳返音量:', newVolume);
});
```

#### setVoiceEarMonitorEnable 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `enable` | `boolean` | 是否开启耳返。 - true：开启 - false：关闭 |

#### setVoiceEarMonitorVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `number` | 耳返音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤4：实现人声音量调节

当主播希望调节自己的上行采集音量大小时，调用`DeviceState`的`setCaptureVolume(volume: )`方法传入音量值即可。

#### 实现方式:
1. **设置采集音量值**：您可自行实现调节音量的功能，并将 `UI `上的值映射成音量值后调用`setCaptureVolume(volume: 80)。`请注意，该接口接收的参数范围是 `[0, 150]`，所以您需要将 UI 控件的值映射到 `0 - 150` 的范围。

2. **监听状态更新 UI**：您可以订阅实时的音量状态值更新 UI。

#### 示例代码：
``` typescript
import { watch } from 'vue'
import { useDeviceState } from "@/uni_modules/tuikit-atomic-x/state/DeviceState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

/ 通过 liveID 获取对应实例
const { setCaptureVolume, captureVolume } = useDeviceState(liveID)

// 1. 调节音量
const adjustCaptureVolume = () => {
    setCaptureVolume({
      volume: 80
    })
}

// 2. 监听状态
watch(captureVolume, (newVolume) => {
  console.log('采集音量:', newVolume);
});
```

#### setCaptureVolume 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `volume` | `number` | 采集音量大小。 - 取值范围： [0, 150] - 默认值：100 |

### 步骤5：实现变声效果

当主播希望改变自己的声音时，您可以调用 `setAudioChangerType({type:})`方法，传入相应的枚举值即可切换效果。

#### 示例代码：
``` typescript
import { useAudioEffectState } from '@/uni_modules/tuikit-atomic-x/state/AudioEffectState'

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID
// 通过 liveID 获取对应实例
const { setAudioChangerType } = useAudioEffectState(liveID)

// 示例：将变声效果设置为"小孩"
const applyVoiceChanger = () => {
    setAudioChangerType({
      changerType: 'CHILD'
    })
}
```

#### setAudioChangerType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `changerType` | `string` | 变声效果枚举。 |

### 步骤6：实现混响效果

当主播希望给自己的声音添加混响效果时，您可以调用`setAudioReverbType({type:})` 方法，传入相应的枚举值即可切换效果。
``` typescript
import { useAudioEffectState } from '@/uni_modules/tuikit-atomic-x/state/AudioEffectState'

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

// 通过 liveID 获取对应实例
const { setAudioReverbType } = useAudioEffectState(liveID)

// 示例：设置为"KTV"混响效果
const applyReverbEffect = () => {
    setAudioReverbType({
      reverbType: 'KTV'
    })
}
```

#### setAudioReverbType 接口参数
| **参数名** | **类型** | **描述** |
| --- | --- | --- |
| `reverbType` | `string` | 混响效果枚举。 |

## 常见问题

### 音效和设备接口的调用时机有没有要求？

> **适用平台：Android / iOS / uni-app**

> **Android/iOS：** 没有限制。`AudioEffectStore`和`DeviceStore` 的设置是全局生效的，您可以在进入直播间之前或之后随时调用相关接口（例如设置变声、混响或耳返），设置会立即生效并被保留。
> **uni-app：** 没有限制。`AudioEffectState`和`DeviceState` 的设置是全局生效的，您可以在进入直播间之前或之后随时调用相关接口（例如设置变声、混响或耳返），设置会立即生效并被保留。

### **“人声音量” 和“耳返音量” 有什么区别？**

> **适用平台：Android / iOS / uni-app**

这是一个非常重要且易混淆的概念，区别如下：
> **Android/iOS：** - **人声音量 (CaptureVolume)：**通过 `DeviceStore.setCaptureVolume()` 设置**。它决定了观众**能听到的您的声音大小。
> **uni-app：** - **人声音量 (CaptureVolume)：**通过 `setCaptureVolume()` 设置**。它决定了观众**能听到的您的声音大小。

> **Android/iOS：** - **耳返音量 (EarMonitorVolume)：**通过 `AudioEffectStore.setVoiceEarMonitorVolume()`** 设置。它只**决定了**您自己**在耳机里听到的监听声音大小，**不会**影响观众。
> **uni-app：** - **耳返音量 (EarMonitorVolume)：**通过 `setVoiceEarMonitorVolume()`** 设置。它只**决定了**您自己**在耳机里听到的监听声音大小，**不会**影响观众。

### **新创建的直播间为什么直接就有音效、或者出现音量很大、很小等问题？**

> **适用平台：Android / iOS / Flutter / uni-app**

> **Android/iOS：** `AudioEffectStore`和`DeviceStore`是单例，这意味着音效和设备设置是全局生效的，出现这种情况很可能是因为您之前设置过音效但是没有重置，需要在合适的地方调用`reset()`方法重置。
> **Flutter：** `AudioEffectStore` 和 `DeviceStore` 是单例，这意味着音效和设备设置是全局生效的，出现这种情况很可能是因为您之前设置过音效但是没有重置，需要在合适的地方调用 `reset()` 方法重置。
> **uni-app：** `AudioEffectState`和`DeviceState`是单例，这意味着音效和设备设置是全局生效的，出现这种情况很可能是因为您之前设置过音效但是没有重置，需要在合适的地方将数据重置。

### **为什么我开启了“耳返”，但是听不到自己的声音？**

> **适用平台：Android / iOS / uni-app**

请您按照以下步骤检查：
1. **是否插入耳机**：耳返功能绝大多数情况下依赖耳机才能生效，请确保已连接有线或蓝牙耳机。

> **Android/iOS：** 2. **音量是否为0**：检查“耳返音量”是否被设置为了 0。
> **uni-app：** 2. **音量是否为0**：检查“耳返音量”是否被设置为 0。

> **Android/iOS：** 3. **麦克风是否开启**：确认 `DeviceStore` 中的麦克风是开启状态（即已调用 `openLocalMicrophone`）。
> **uni-app：** 3. **麦克风是否开启**：确认 `DeviceState` 中的麦克风是开启状态（即已调用 `openLocalMicrophone`）。

### **变声效果 和混响效果 可以同时生效吗？**

> **适用平台：Android / iOS / uni-app**

> **Android：** 可以。`AudioChangerType` 和 `AudioReverbType` 是可以叠加生效的。例如，您可以同时设置 `AudioChangerType.LITTLE_GIRL` 变声和 `AudioReverbType.KTV` 混响。
> **iOS：** 可以。`AudioChangerType` 和 `AudioReverbType` 是可以叠加生效的。例如，您可以同时设置 `.littleGirl` 变声和 `.ktv` 混响。
> **uni-app：** 可以。`AudioChangerType` 和 `AudioReverbType` 是可以叠加生效的。例如，您可以同时设置 `'LITTLEGIRL'` 变声和 `'KTV'`混响。

### 音效和设备接口的调用时机是否有要求？

> **适用平台：Flutter**

没有限制。`AudioEffectStore` 和 `DeviceStore` 的设置是全局生效的，您可以在进入直播间之前或之后随时调用相关接口（例如设置变声、混响或耳返），设置会立即生效并被保留。

### "人声音量" 和"耳返音量" 有什么区别？

> **适用平台：Flutter**

这是一个非常重要且易混淆的概念，区别如下：
- **人声音量 (CaptureVolume)：**通过 `DeviceStore.shared.setCaptureVolume()` 设置。它决定了观众能听到主播的声音大小。

- **耳返音量 (EarMonitorVolume)：**通过 `AudioEffectStore.shared.setVoiceEarMonitorVolume()` 设置。它只决定了主播在耳机里听到的监听声音大小，**不会**影响观众。

### 为什么我开启了"耳返"，但是听不到自己的声音？

> **适用平台：Flutter**

请您按照以下步骤检查：
1. **是否插入耳机**：耳返功能绝大多数情况下依赖耳机才能生效，请确保已连接有线或蓝牙耳机。

2. **音量是否为0**：检查“耳返音量"是否被设置为 0。

3. **麦克风是否开启**：确认 `DeviceStore` 中的麦克风是开启状态（即已调用 `openLocalMicrophone()`）。

### 变声效果和混响效果可以同时生效吗？

> **适用平台：Flutter**

可以。`AudioChangerType` 和 `AudioReverbType` 是可以叠加生效的。例如，您可以同时设置 `AudioChangerType.girl` 变声和 `AudioReverbType.ktv` 混响。
