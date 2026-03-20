> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

`BaseBeautyStore` is the module in **AtomicXCore** responsible for managing basic portrait beautification effects. This module enables you to easily add natural beautification features to your live streaming or video calling applications.

## Core Features

### Android

- **Skin Smoothing Adjustment**: Configure smoothing intensity (0–9).

- **Whitening Adjustment**: Configure whitening intensity (0–9).

- **Rosy Adjustment**: Configure rosy intensity (0–9).

- **Effect Reset**: Instantly restore all beautification parameters to their default values.

- **State Monitoring:** Access real-time values of the currently applied beautification parameters.
### iOS

- **Skin Smoothing**: Configure smoothing intensity (0–9).

- **Skin Whitening:** Configure whitening intensity (0–9).

- **Rosy Cheeks**: Configure rosy intensity (0–9).

- **Effect Reset: **Instantly restore all beautification parameters to default.

- **State Monitoring:** Real-time access to currently applied beautification settings.
### Flutter

- **Skin smoothing adjustment**: Set the skin smoothing strength (0-9).

- **Brightening effect adjustment**: Set the brightening strength (0-9).

- **Rosy effect adjustment**: Set the rosy strength (0-9).

- **Effect reset**: One-click restore all beauty parameters to default value.

- **Listen for status**: Get currently effective beauty parameters in real time.

## Core Concepts

#### Android

| **Core Concept** | **Type** | **Core Responsibilities and Description** |
| --- | --- | --- |
| `BaseBeautyState` | `data` | - Represents the current state of the basic beautification module.  - Contains the currently applied intensity values for smoothing (smoothLevel), whitening (whitenessLevel), and rosy (ruddyLevel). |
| `BaseBeautyStore` | `abstract` | - The core management class for interacting with basic beautification features. -  Implemented as a global singleton (shared), responsible for setting, resetting, and synchronizing all basic beautification parameters. |

#### iOS

| **Core Concept** | **Type** | **Core Responsibilities and Description** |
| --- | --- | --- |
| `BaseBeautyState` | `struct` | - Represents the current state of the basic beautification module.  - Contains the currently applied intensity values for smoothing (smoothLevel), whitening (whitenessLevel), and rosy (ruddyLevel). |
| `BaseBeautyStore` | `class` | - The core management class for interacting with basic beautification features.  - Implemented as a global singleton (`shared`). Responsible for setting parameters, resetting effects, and synchronizing state across the app. |

#### Flutter

| **Core Concepts** | **Type** | **Core Responsibilities and Description** |
| --- | --- | --- |
| BaseBeautyState | `class` | Represents the current status of the beauty module. It includes the intensity values of currently effective skin smoothing (`smoothLevel`), whitening (`whitenessLevel`), and rosy (`ruddyLevel`). All attributes are `ValueListenable<double>` data type and support subscription listening. |
| BaseBeautyStore | `class` | This is the core management class for interacting with the basic beauty filter feature. It is a Global Singleton (`shared`), responsible for ALL basic beauty parameter settings, reset and state synchronization. |

## Implementation

### Step 1: Component Integration

- **Video Live Streaming**: Please refer to Quick Start to integrate AtomicXCore and complete the setup of LiveCoreView..

- **Voice Chat Room**: Please refer to Quick Start to integrate AtomicXCore and complete the setup of LiveCoreView.

### Step 2: Obtain the Instance and Subscribe to State

Obtain the global singleton instance of `BaseBeautyStore` and subscribe to receive real-time updates of the current beautification parameter state.

#### Implementation
1. **Obtain Singleton**: Use `BaseBeautyStore.shared()` to access the global instance.

2. **Subscribe to State**: Subscribe to `baseBeautyStore.baseBeautyState` to receive real-time updates of BaseBeautyState.

#### Code Example
#### Android

```kotlin
import io.trtc.tuikit.atomicxcore.api.device.BaseBeautyState
import io.trtc.tuikit.atomicxcore.api.device.BaseBeautyStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import kotlinx.coroutines.flow.MutableStateFlow

class BeautyManager() {
    // 1. Obtain singleton
    private val baseBeautyStore = BaseBeautyStore.shared()
    private val coroutineScope = CoroutineScope(Dispatchers.Main)

    private val _smoothLevel = MutableStateFlow(0f)
    private val _whitenessLevel = MutableStateFlow(0f)
    private val _ruddyLevel = MutableStateFlow(0f)

    // Expose beauty state externally
    val baseBeautyState = BaseBeautyState(
        smoothLevel = _smoothLevel,
        whitenessLevel = _whitenessLevel,
        ruddyLevel = _ruddyLevel
    )

    init {
        // 2. Subscribe to state
        subscribeToBeautyState()
    }

    private fun subscribeToBeautyState() {
        coroutineScope.launch(Dispatchers.Main) {
            baseBeautyStore.baseBeautyState.smoothLevel.collect { smoothLevel ->
                _smoothLevel.value = smoothLevel
            }
        }

        coroutineScope.launch(Dispatchers.Main) {
            baseBeautyStore.baseBeautyState.whitenessLevel.collect { whitenessLevel ->
                _whitenessLevel.value = whitenessLevel
            }
        }

        coroutineScope.launch(Dispatchers.Main) {
            baseBeautyStore.baseBeautyState.ruddyLevel.collect { ruddyLevel ->
                _ruddyLevel.value = ruddyLevel
            }
        }
    }
    // ... subsequent methods
}
```

#### iOS

```swift
import Foundation
import AtomicXCore 
import Combine     

class BeautyManager {
    // 1. Get the singleton instance
    private let baseBeautyStore = BaseBeautyStore.shared
    private var cancellables = Set<AnyCancellable>()
    
    // Expose beauty status externally
    let beautyStatePublisher = CurrentValueSubject<BaseBeautyState, Never>(BaseBeautyState())

    init() {
        // 2. Subscribe to the state
        subscribeToBeautyState()
    }
    
    private func subscribeToBeautyState() {
        baseBeautyStore.state
            .subscribe()
            .receive(on: DispatchQueue.main)
            .assign(to: \.value, on: beautyStatePublisher)
            .store(in: &cancellables)
    }
    
    // ... Subsequent methods
}
```

### Step 3: Set Beautification Parameters

When users adjust a beautification slider or select a preset, call the corresponding API to set the desired intensity.

#### Implementation
1. **Get Intensity Value**: 

- Retrieve the value from the UI control (e.g., `SeekBar`). 

- Note that the SDK interface accepts a parameter range of `[0, 9]`, where 0 means the effect is off, and 9 means the effect is most prominent. You need to map the UI control value (e.g., `0.0 - 1.0` for a `UISlider`) to the `0 - 9` range.

2. **Call API**: Use `setSmoothLevel(smoothLevel:)`, `setWhitenessLevel(whitenessLevel:)`, and `setRuddyLevel(ruddyLevel:)` to set the smoothing, whitening, and rosy intensities.

#### Code Example
#### Android

```kotlin
class BeautyManager() {
    private val baseBeautyStore = BaseBeautyStore.shared()
    
    // Set smoothing level (input range 0–100, internally converted to 0–9)
    fun updateSmoothLevel(uiLevel: Int) {
        val sdkLevel = (uiLevel / 100.0f * 9.0f)
        baseBeautyStore.setSmoothLevel(sdkLevel)
        println("Set smoothing level: UI=$uiLevel, SDK=$sdkLevel")
    }

    // Set whitening level (input range 0–100, internally converted to 0–9)
    fun updateWhitenessLevel(uiLevel: Int) {
        val sdkLevel = (uiLevel / 100.0f * 9.0f)
        baseBeautyStore.setWhitenessLevel(sdkLevel)
        println("Set whitening level: UI=$uiLevel, SDK=$sdkLevel")
    }

    // Set rosy level (input range 0–100, internally converted to 0–9)
    fun updateRuddyLevel(uiLevel: Int) {
        val sdkLevel = (uiLevel / 100.0f * 9.0f)
        baseBeautyStore.setRuddyLevel(sdkLevel)
        println("Set rosy level: UI=$uiLevel, SDK=$sdkLevel")
    }
}
```

#### iOS

```swift
extension BeautyManager {
    /// Set the smoothing level (Input range 0.0 ~ 1.0, converted internally to 0 ~ 9)
    func updateSmoothLevel(uiLevel: Float) {
        // Map UI's 0.0 ~ 1.0 to SDK's 0 ~ 9
        let sdkLevel = uiLevel * 9.0
        baseBeautyStore.setSmoothLevel(smoothLevel: sdkLevel)
    }

    /// Set the whitening level (Input range 0.0 ~ 1.0, converted internally to 0 ~ 9)
    func updateWhitenessLevel(uiLevel: Float) {
        let sdkLevel = uiLevel * 9.0
        baseBeautyStore.setWhitenessLevel(whitenessLevel: sdkLevel)
    }

    /// Set the rosiness level (Input range 0.0 ~ 1.0, converted internally to 0 ~ 9)
    func updateRuddyLevel(uiLevel: Float) {
        let sdkLevel = uiLevel * 9.0
        baseBeautyStore.setRuddyLevel(ruddyLevel: sdkLevel)
    }
}
```

### Step 4: Reset Beautification Effects

When users click "Reset" or "Disable Beauty," restore all beautification parameters to their default values (typically 0).

#### Implementation
- **Call API**: Call the `baseBeautyStore.reset()` method.

#### Code Example
#### Android

```kotlin
class BeautyManager() {
    private val baseBeautyStore = BaseBeautyStore.shared()

    // Reset all basic beautification effects
    fun resetBeautyEffects() {
        baseBeautyStore.reset()
    }
}
```

#### iOS

```swift
extension BeautyManager {
    /// Reset all basic beauty effects
    func resetBeautyEffects() {
        baseBeautyStore.reset()
    }
}
```

## Advanced Features

### Comparison: Basic vs. Advanced Beautification

AtomicXCore also provides advanced beautification features for more demanding scenarios:
#### Android

| **Comparison Item** | **Basic Beautification (BaseBeautyStore)** | **Advanced Beautification (TEBeautyKit, requires additional integration)** |
| --- | --- | --- |
| Core Features | Smoothing, Whitening, Rosy | Includes all basic features, plus V-shape face, eye distance adjustment, nose slimming, 3D stickers, filters, makeup, and more |
| Pricing | Free (included with AtomicXCore license) | Paid (requires separate Tencent Effects SDK license) |
| Integration Method | Built-in by default; use BaseBeautyStore.shared() directly | Requires additional integration of the TEBeautyKit component and authentication |
| Recommended Scenarios | Scenarios with basic beautification needs and rapid implementation requirements | Scenarios requiring advanced beautification, shaping, stickers, filters, and other premium features |

#### iOS

| **Comparison Item** | **Basic Beautification (BaseBeautyStore)** | **Advanced Beautification (TEBeautyKit)** |
| --- | --- | --- |
| Core Features | Smoothing, Whitening, Rosy | Includes all basic features, plus V-shape face, eye distance adjustment, nose slimming, 3D stickers, filters, makeup, and more |
| Pricing | Free (included in AtomicXCore license) | Paid (Requires separate license) |
| Integration | Built-in by default; use `BaseBeautyStore.shared() `directly | Requires additional integration of the TEBeautyKit component and authentication |
| Recommended Scenarios | Scenarios with basic beautification needs and rapid implementation requirements | Scenarios requiring advanced beautification, shaping, stickers, filters, and other premium features |

### Integrating Advanced Beautification

To use advanced beautification features, refer to the [Advanced Beauty](https://trtc.io/document/60196?product=beautyar&menulabel=core%20sdk&platform=android) documentation. After integrating and authenticating TEBeautyKit, use its APIs to control all beautification effects.

## API Documentation

For detailed information on all public interfaces, properties, and methods of BaseBeautyStore and related classes, see the official API documentation for the AtomicXCore framework. The relevant stores referenced in this guide are:
#### Android

| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| `LiveCoreView` | Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. |  |
| `BaseBeautyStore` | Basic beautification: adjust smoothing, whitening, and rosy (levels 0–9), reset status, synchronize effect parameters. |  |
| `DeviceStore` | Audio and video device control: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, real-time device status monitoring. |  |

#### iOS

| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| `LiveCoreView` | - Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. |  |
| `DeviceStore` | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. |  |
| `BaseBeautyStore` | Basic beauty filters: adjust smoothing/whitening/rosiness (0-100), reset beauty status, and synchronize effect parameters. |  |

## FAQs

### I set the beautification parameters, but why is there no effect?

**Check the following:**
1. **Camera is enabled**:  Ensure the camera is turned on (e.g., via DeviceStore.shared().openLocalCamera). Beauty effects cannot apply to a black screen or audio-only stream.

2. **Using advanced beautification**: If you have integrated `TEBeautyKit` (advanced beautification), ensure you are using the APIs provided by `TEBeautyKit` to adjust beautification effects.

3. **Parameter range**: If you are using the basic beautification feature, ensure the intensity values you provide are within the valid range (float values between 0 and 9).

### I adjusted the parameters, but there is no effect on the video. (iOS)

**Check the following:**
1. **Camera Status**: Ensure the camera is turned on (e.g., via DeviceStore.shared().openLocalCamera). Beauty effects cannot apply to a black screen or audio-only stream.

2. **SDK Conflict**: If you have integrated `TEBeautyKit`, `BaseBeautyStore` APIs may be overridden. Use the `TEBeautyKit` APIs instead.

3. **Value Range**: Verify that you are passing values between 0 and 9. Passing 0 will result in no visible effect.

## Implementation Steps (Flutter)

### Step 1: Integrating the Component (Flutter)

Refer to Quick Connection for seamless integration with **AtomicXCore** and access completed.

### Step 2: Getting the Instance and Listening to the Status (Flutter)

Get the Global Singleton of `BaseBeautyStore` and set a listener to get the current beauty effect parameter status in real time.

#### Implementation Method
1. **Get the singleton**: Directly use `BaseBeautyStore.shared` to get the globally unique `BaseBeautyStore` instance.

2. **Subscription status**: Listen for changes in the `baseBeautyState` properties via `addListener` to get beauty parameter updates in real time.

#### Sample Code
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class BeautyManager {
  // 1. Get a singleton
  final _baseBeautyStore = BaseBeautyStore.shared;
  late final VoidCallback _smoothLevelChangedListener = _onSmoothLevelChanged;
  late final VoidCallback _whitenessLevelChangedListener = _onWhitenessLevelChanged;
  late final VoidCallback _ruddyLevelChangedListener = _onRuddyLevelChanged;

  // 2. Subscription status
  void subscribeToBeautyState() {
    _baseBeautyStore.baseBeautyState.smoothLevel.addListener(_smoothLevelChangedListener);
    _baseBeautyStore.baseBeautyState.whitenessLevel.addListener(_whitenessLevelChangedListener);
    _baseBeautyStore.baseBeautyState.ruddyLevel.addListener(_ruddyLevelChangedListener);
  }

  void _onSmoothLevelChanged() {
    final level = _baseBeautyStore.baseBeautyState.smoothLevel.value;
    debugPrint('Skin smoothing strength adjustment: $level');
  }

  void _onWhitenessLevelChanged() {
    final level = _baseBeautyStore.baseBeautyState.whitenessLevel.value;
    debugPrint('Whitening strength adjustment: $level');
  }

  void _onRuddyLevelChanged() {
    final level = _baseBeautyStore.baseBeautyState.ruddyLevel.value;
    debugPrint('Rosy strength adjustment: $level');
  }

  // Execute dispose before logging out of the App
  void dispose() {
    _baseBeautyStore.baseBeautyState.smoothLevel.removeListener(_smoothLevelChangedListener);
    _baseBeautyStore.baseBeautyState.whitenessLevel.removeListener(_whitenessLevelChangedListener);
    _baseBeautyStore.baseBeautyState.ruddyLevel.removeListener(_ruddyLevelChangedListener);
  }
}
```

### Step 3: Set Beauty Parameter (Flutter)

When the user drags the beauty effect slider or clicks the preset button, call the appropriate API to set the intensity of beauty effects.

#### Implementation Method
1. **Get intensity value**: Obtain the intensity value set by the user from the UI control (such as `Slider`). The parameter range accepted by the SDK API is `0-9`, where `0` means disabling the effect and `9` indicates the most obvious effect. You need to map the value of the UI control (for example, `0.0 - 1.0` of the Slider) to the range of `0 - 9`.

2. **API call**: Call `setSmoothLevel()`, `setWhitenessLevel()`, and `setRuddyLevel()` to set the intensity of skin smoothing, whitening, and rosy effect.

#### Sample Code
``` java
extension BeautyManagerExtension on BeautyManager {
  /// Set the skin smoothing strength (input range 0.0 ~ 1.0, internal conversion to 0 ~ 9)
  void updateSmoothLevel(double uiLevel) {
    // Map the UI's 0.0 ~ 1.0 to the SDK's 0 ~ 9
    final sdkLevel = uiLevel * 9.0;
    _baseBeautyStore.setSmoothLevel(sdkLevel);
  }

  /// Set the whitening strength (input range 0.0 ~ 1.0, internal conversion to 0 ~ 9)
  void updateWhitenessLevel(double uiLevel) {
    final sdkLevel = uiLevel * 9.0;
    _baseBeautyStore.setWhitenessLevel(sdkLevel);
  }

  /// Set the ruddy level (input range 0.0 ~ 1.0, internal conversion to 0 ~ 9)
  void updateRuddyLevel(double uiLevel) {
    final sdkLevel = uiLevel * 9.0;
    _baseBeautyStore.setRuddyLevel(sdkLevel);
  }
}
```

### Step 4: Reset Beauty Effect (Flutter)

When the user clicks the "Reset" or "Disable Beauty Effects" button, restore all beauty parameters to the default value (usually 0).

#### Implementation Method

**Calling the API**: call directly the `baseBeautyStore.reset()` method.

#### Sample Code
``` java
extension BeautyManagerReset on BeautyManager {
  /// Reset ALL basic beauty effects
  void resetBeautyEffects() {
    _baseBeautyStore.reset();
  }
}
```

## Building a Beauty Effect UI (Flutter)

Flutter recommended using `ValueListenableBuilder` to build responsive UI, which auto-updates the interface when beauty effect parameters change:
``` java
class BeautySliderWidget extends StatelessWidget {
  final beautyStore = BaseBeautyStore.shared;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // Skin smoothing slider
        ValueListenableBuilder<double>(
          valueListenable: beautyStore.baseBeautyState.smoothLevel,
          builder: (context, smoothLevel, child) {
            return Row(
              children: [
                Text('Skin smoothing')
                Expanded(
                  child: Slider(
                    value: smoothLevel,
                    min: 0,
                    max: 9,
                    divisions: 9,
                    onChanged: (value) {
                      beautyStore.setSmoothLevel(value);
                    },
                  ),
                ),
                Text('${smoothLevel.toInt()}'),
              ],
            );
          },
        ),
        // Whitening slider
        ValueListenableBuilder<double>(
          valueListenable: beautyStore.baseBeautyState.whitenessLevel,
          builder: (context, whitenessLevel, child) {
            return Row(
              children: [
                Text('Whitening')
                Expanded(
                  child: Slider(
                    value: whitenessLevel,
                    min: 0,
                    max: 9,
                    divisions: 9,
                    onChanged: (value) {
                      beautyStore.setWhitenessLevel(value);
                    },
                  ),
                ),
                Text('${whitenessLevel.toInt()}'),
              ],
            );
          },
        ),
        // rosy slider
        ValueListenableBuilder<double>(
          valueListenable: beautyStore.baseBeautyState.ruddyLevel,
          builder: (context, ruddyLevel, child) {
            return Row(
              children: [
                Text('Rosy')
                Expanded(
                  child: Slider(
                    value: ruddyLevel,
                    min: 0,
                    max: 9,
                    divisions: 9,
                    onChanged: (value) {
                      beautyStore.setRuddyLevel(value);
                    },
                  ),
                ),
                Text('${ruddyLevel.toInt()}'),
              ],
            );
          },
        ),
      ],
    );
  }
}
```

## Complete Example (Flutter)

``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class BeautySettingsPage extends StatelessWidget {
  final beautyStore = BaseBeautyStore.shared;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Beauty settings')),
      body: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          children: [
            // Skin smoothing
            _buildBeautySlider(
              title: 'Skin smoothing'
              valueListenable: beautyStore.baseBeautyState.smoothLevel,
              onChanged: (value) => beautyStore.setSmoothLevel(value),
            ),
            SizedBox(height: 20),
            // Whitening
            _buildBeautySlider(
              title: 'Whitening',
              valueListenable: beautyStore.baseBeautyState.whitenessLevel,
              onChanged: (value) => beautyStore.setWhitenessLevel(value),
            ),
            SizedBox(height: 20),
            // Rosy
            _buildBeautySlider(
              title: 'Rosy'
              valueListenable: beautyStore.baseBeautyState.ruddyLevel,
              onChanged: (value) => beautyStore.setRuddyLevel(value),
            ),
            SizedBox(height: 40),
            // reset button
            ElevatedButton(
              onPressed: () {
                beautyStore.reset();
              },
              child: Text('Reset beauty effect'),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildBeautySlider({
    required String title,
    required ValueListenable<double> valueListenable,
    required ValueChanged<double> onChanged,
  }) {
    return ValueListenableBuilder<double>(
      valueListenable: valueListenable,
      builder: (context, value, child) {
        return Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: [
                Text(title, style: TextStyle(fontSize: 16)),
                Text('${value.toInt()}', style: TextStyle(fontSize: 16)),
              ],
            ),
            Slider(
              value: value,
              min: 0,
              max: 9,
              divisions: 9,
              onChanged: onChanged,
            ),
          ],
        );
      },
    );
  }
}
```

### Basic Beauty Filter Vs Advanced Beauty Effect (Flutter)

AtomicXCore also provided advanced beauty effect support to meet requirements of different scenes:
| **Comparison Item** | **Basic Beauty Filter (BaseBeautyStore)** | **Advanced Beauty Effect (TencentEffect Requires Additional Integration)** |
| --- | --- | --- |
| Core Features | Skin smoothing, whitening, rosy | Includes basic beauty filter, with various effects like V-face, eye distance adjustment, nose slimming, 3D stickers, filters, and makeup. |
| Billing | free (included in AtomicXCore license) | Payment (Tencent Effect SDK License must be bought extra). |
| integration method | default built-in, direct use `BaseBeautyStore.shared` | Additional integration of the `TencentEffect` component is required and authenticate. |
| Recommended scenarios | Scenarios with low beauty effect requirements that need quick implementation of basic beauty effect features | Scenarios with high beauty effect requirements that require various advanced functions such as beauty shaping, stickers, and filters. |

### Integrate Advanced Beauty Effect (Flutter)

If you need to use the advanced beauty function, refer to the [Advanced Beauty](https://trtc.io/document/73778?product=beautyar&menulabel=core%20sdk&platform=flutter) document for integration. After successful integration and authentication of `TencentEffect`, you can control ALL beauty effects through the API provided by `TencentEffect`.

## API Reference (Flutter)

### BaseBeautyStore (Flutter)

| **Property/Method** | **Type** | **Note** |
| --- | --- | --- |
| `shared` | `BaseBeautyStore` | Singleton instance |
| `baseBeautyState` | `BaseBeautyState` | Beauty effect status |
| `setSmoothLevel` | `void` | Set the skin smoothing strength (0-9). |
| `setWhitenessLevel` | `void` | Set the brightening strength (0-9). |
| `setRuddyLevel` | `void` | Set the rosy strength (0-9). |
| `reset` | `void` | Reset all beauty parameters to default value (0). |

### BaseBeautyState (Flutter)

| **Attribute** | **Type** | **Note** |
| --- | --- | --- |
| `smoothLevel` | `ValueListenable<double>` | Skin smoothing strength (0-9). |
| `whitenessLevel` | `ValueListenable<double>` | Brightening strength (0-9). |
| `ruddyLevel` | `ValueListenable<double>` | Rosy strength (0-9). |

### I Set Up the Beauty Parameter, Why No Effect? (Flutter)

Please check the following points:
1. **Is the camera enabled**: You must successfully turn on the camera (e.g. by `DeviceStore.shared.openLocalCamera()`) before the beauty effect can apply to the video stream.

2. **Whether to use advanced beauty effect**: If you integrated `TencentEffect` (advanced beauty), ensure you use the API provided by `TencentEffect` to adjust the beauty effect.

3. **Parameter range**: If you use basic beauty filter, confirm the strength value passed in is within the effective range (a double value between `0` and `9`).

