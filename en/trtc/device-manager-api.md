> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXDeviceManager 设备管理 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXDeviceManager 设备管理 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TXDeviceManager

## Enumeration

| Enumeration Types | Description |
| --- | --- |
| TXSystemVolumeType | System Volume Type (Only applicable to mobile devices) |
| TXAudioRoute | Audio routing (i.e., the sound playback mode) |
| TXMediaDeviceType | Device type (only for desktop platform) |
| TXMediaDeviceState | Device operations |
| TXCameraCaptureMode | Camera capture preferences |

## isFrontCamera

#### Determine whether the current camera is front-facing (only for mobile terminal)

## switchCamera

#### Switch between front and rear cameras (only for mobile terminal)

## getCameraZoomMaxRatio

#### Get the maximum zoom level of the camera (only for mobile terminal)

## setCameraZoomRatio

#### Set the zoom level of the camera (only for mobile terminal)
| Parameters | Description |
| --- | --- |
| zoomRatio | Value range 1 - 5, with 1 representing the farthest view (normal lens) and 5 representing the closest view (magnifier lens). The recommended maximum value is 5; exceeding 5 may cause video data to become blurry. |

## isAutoFocusEnabled

#### Check if automatic face position recognition is supported (only for mobile terminal)

## enableCameraAutoFocus

#### Enable autofocus feature (only for mobile terminal)

Once enabled, the SDK will automatically detect the face position in the frame and keep the camera focus on the face position.

## setCameraFocusPosition

#### Set the focus position of the camera (only for mobile terminal)
You can achieve the following interactions with this interface:
1. Allow users to click on the local camera preview screen.
2. Display a rectangular box at the click position to indicate that the camera will focus on this point.
3. Then, pass the coordinates of the user's click position through this interface to the SDK. The SDK will control the camera to focus on the user's desired location.
| Parameters | Description |
| --- | --- |
| position | Focus position, please pass the coordinates of the desired focus point |
#### Return value description:
0: Operation successful; Negative value: Operation failed.

> **注意**
> **Note**
> The prerequisite for using this interface is to disable the auto-focus feature through enableCameraAutoFocus.

## enableCameraTorch

#### Turn the flash on/off, also known as flashlight mode (only for mobile terminal)

## setAudioRoute

#### Set audio routing (only for mobile terminal)

A phone has two audio playback devices: one is the earpiece at the top of the phone, and the other is the stereo speaker at the bottom of the phone.

When the audio routing is set to the earpiece, the sound is relatively low, and you need to bring your ear close to hear it clearly, ensuring better privacy. It is suitable for phone calls.

When the audio routing is set to the speaker, the sound is relatively loud. You can hear it clearly without holding the phone to your face, thus enabling the "hands-free" feature.

## getDevicesList
> **适用平台：** iOS, C++

#### Get the device list (only for desktop terminal)
| Parameters | Description |
| --- | --- |
| type | Device type, specify which type of device list to retrieve. See TXMediaDeviceType Definition for details. |

> **Note**
> -  After use, please call the release method to release resources so that the SDK can maintain the lifecycle of the ITXDeviceCollection object.
> -  Do not use delete to release the returned Collection object. Deleting the ITXDeviceCollection* pointer will cause an abnormal crash.
> -  type only supports TXMediaDeviceTypeMic, TXMediaDeviceTypeSpeaker, TXMediaDeviceTypeCamera.
> -  This interface only supports Mac and Windows platforms.

## setCurrentDevice
> **适用平台：** iOS, C++

#### Set the current device to use (only for desktop terminal)
| Parameters | Description |
| --- | --- |
| deviceId | Device ID, you can obtain the device ID through the getDevicesList interface. |
| type | Device type, see TXMediaDeviceType Definition for details. |
#### Return value description:
0: Operation successful; Negative value: Operation failed.

## getCurrentDevice
> **适用平台：** iOS, C++

#### Get the current device in use (only for desktop terminal)

## setCurrentDeviceVolume
> **适用平台：** iOS, C++

#### Set the current device volume (only for desktop terminal)

The volume here refers to the capture volume of the microphone or the playback volume of the speaker. The camera does not support setting the volume.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0-100. Default value: 100. |

## getCurrentDeviceVolume
> **适用平台：** iOS, C++

#### Get the current device volume (only for desktop terminal)

The volume here refers to the capture volume of the microphone or the playback volume of the speaker. The camera does not support retrieving the volume.

## setCurrentDeviceMute
> **适用平台：** iOS, C++

#### Set the mute status of the current device (only for desktop terminal)

The volume here refers to the microphone and speaker, and the camera does not support silent operation.

## getCurrentDeviceMute
> **适用平台：** iOS, C++

#### Get the mute status of the current device (only for desktop terminal)

The volume here refers to the microphone and speaker, and the camera does not support silent operation.

## enableFollowingDefaultAudioDevice
> **适用平台：** iOS, C++

#### Set SDK to use the audio device according to the default system device (only for desktop terminal)

Only microphone and speaker types are supported. The camera does not yet support following the system default device
| Parameters | Description |
| --- | --- |
| enable | Whether to follow the system default audio device. -  true: Follow. When the system default audio device changes or a new audio device is plugged in, the SDK will immediately switch the audio device. -  false: Do not follow. When the system default audio device changes or a new audio device is plugged in, the SDK will not switch the audio device. |
| type | Device type, see TXMediaDeviceType Definition for details. |

## startCameraDeviceTest
> **适用平台：** iOS, C++

#### Start camera testing (only for desktop terminal)
This interface supports custom rendering, which means you can take over the camera's rendered image by connecting to the ITRTCVideoRenderCallback Callback Interface.

> **注意**
> **Note**
> During testing, you can use the setCurrentDevice interface to switch cameras.

## stopCameraDeviceTest
> **适用平台：** iOS, C++

#### End camera testing (only for desktop terminal)

## startMicDeviceTest
> **适用平台：** iOS, C++

#### Start mic testing (only for desktop terminal)
This interface can test whether the microphone is working properly. The volume of the microphone's audio stream will be notified to you via callback, with the volume range being 0 to 100.
| Parameters | Description |
| --- | --- |
| interval | Microphone volume callback interval. |
| playback | Whether to enable microphone sound playback. When enabled, users will hear their own voice during microphone testing. |

> **Note**
> After invoking this interface, the recorded microphone sound will be played back through the speaker by default.

## stopMicDeviceTest
> **适用平台：** iOS, C++

#### End mic testing (only for desktop terminal)

## startSpeakerDeviceTest
> **适用平台：** iOS, C++

#### Start speaker testing (only for desktop terminal)

This interface tests the playback device by playing a specified audio file. If the user hears sound during the test, it means the playback device is working properly.
| Parameters | Description |
| --- | --- |
| filePath | Path to the audio file |

## stopSpeakerDeviceTest
> **适用平台：** iOS, C++

#### End speaker testing (only for desktop terminal)

## setApplicationPlayVolume
> **适用平台：** C++

#### Set the volume of the current process in the Windows system volume synthesizer (only applicable for Windows system)

## getApplicationPlayVolume
> **适用平台：** C++

#### Get the volume of the current process in the Windows system volume synthesizer (only applicable for Windows system)

## setApplicationMuteState
> **适用平台：** C++

#### Set the mute status of the current process in the Windows system volume synthesizer (only applicable for Windows system)

## getApplicationMuteState
> **适用平台：** C++

#### Get the mute status of the current process in the Windows system volume synthesizer (only applicable for Windows system)

## setCameraCapturerParam

#### Set camera capture preferences

## setDeviceObserver
> **适用平台：** C++

#### Set onDeviceChanged event callback

## setSystemVolumeType

#### Set system volume type (only for mobile terminal)

@deprecated Not recommended starting from version 9.5. It is advised to use the startLocalAudio(quality) interface in ` TRTCCloud ` as a replacement, and decide the sound quality via the quality parameter.

## TXSystemVolumeType

#### System Volume Type (Only applicable to mobile devices)
@deprecated Not recommended starting from version 9.5.
Modern smartphones generally have two types of system volume: "Call Volume" and "Media Volume".
-  Call Volume: A volume type designed specifically for making and receiving phone calls, featuring built-in echo cancellation (AEC), and supporting microphone input via Bluetooth headphones. However, the sound quality is relatively average.
If you press the volume button on the side of your phone to lower the volume and it cannot be set to zero (completely silent), it indicates that your phone is currently in Call Volume mode.
-  Media Volume: A volume type designed specifically for music scenarios, which cannot use the system's AEC feature, and does not support microphone input via Bluetooth headphones, but provides better music playback quality.
If you press the volume button on the side of your phone to lower the volume and it can be set to completely silent, it indicates that your phone is currently in Media Volume mode.
The SDK currently offers three control modes for system volume types: Automatic Switching Mode, Full Call Volume Mode, Full Media Volume Mode.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXSystemVolumeTypeAuto | Not Defined | Automatic Switching Mode |
| TXSystemVolumeTypeMedia | Not Defined | Full Media Volume |
| TXSystemVolumeTypeVOIP | Not Defined | Full Call Volume |

## TXAudioRoute

#### Audio routing (i.e., the sound playback mode)
Audio routing, that is, whether the sound is played through the phone's speaker or earpiece. Therefore, this interface is only applicable to mobile devices.
A mobile phone has two speakers: one is the earpiece located at the top of the phone, and the other is the stereo speaker located at the bottom.
-  When the audio routing is set to the earpiece, the sound is relatively low, and you need to bring your ear close to hear it clearly, ensuring better privacy. It is suitable for phone calls.
-  When the audio routing is set to the speaker, the sound is relatively loud. You can hear it clearly without holding the phone to your face, thus enabling the "hands-free" feature.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXAudioRouteSpeakerphone | Not Defined | Speakerphone: Play sound through the speaker (i.e., "hands-free"), which is located at the bottom of the phone. The volume tends to be loud, suitable for playing music out loud. |
| TXAudioRouteEarpiece | Not Defined | Earpiece: Plays through the earpiece, the earpiece is located at the top of the phone, the sound is small, suitable for privacy-protected call scenarios. |

## TXMediaDeviceType
> **适用平台：** iOS, C++

#### Device type (only for desktop platform)
This enumeration value is used for the three types of audio and video devices in Definition: camera, microphone, and speaker, allowing a single device management interface to control different types of devices.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXMediaDeviceTypeUnknown | -1 | Undefined device type |
| TXMediaDeviceTypeAudioInput | 0 | Microphone-type device |
| TXMediaDeviceTypeAudioOutput | 1 | Speaker-type device |
| TXMediaDeviceTypeVideoCamera | 2 | Camera-type device |

## TXMediaDeviceState
> **适用平台：** iOS, C++

#### Device operations
This enumeration value is used for local device status change notifications onDeviceChanged.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXMediaDeviceStateAdd | 0 | Device inserted |
| TXMediaDeviceStateRemove | 1 | Device removed |
| TXMediaDeviceStateActive | 2 | Device enabled |
| TXMediaDefaultDeviceChanged | 3 | System default device changed |

## TXCameraCaptureMode

#### Camera capture preferences
This enumeration type is used for camera collection parameter settings.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXCameraResolutionStrategyAuto | Not Defined | Auto-adjust collection parameters. The SDK selects appropriate camera output parameters based on the performance of the actual capture device and network conditions, maintaining a balance between device performance and video preview quality. |
| TXCameraResolutionStrategyPerformance | Not Defined | Prioritize device performance. The SDK selects the closest camera output parameters based on the encoder resolution and frame rate set by the user, thus ensuring device performance. |
| TXCameraResolutionStrategyHighQuality | Not Defined | Priority is given to ensuring video preview quality. The SDK selects higher camera output parameters to improve the quality of preview video. In this case, more CPU and memory will be consumed for video preprocessing. |
| TXCameraCaptureManual | Not Defined | Users are allowed to set the video aspect ratio captured by the local camera. |

## TXCameraCaptureParam

#### Camera collection parameters
This setting determines the image quality of the local preview.
| Enumeration Types | Description |
| --- | --- |
| height | **Field Definition:** Captured Image Width |
| mode | **Field Definition:** Camera Capture Preferences, refer to TXCameraCaptureMode |
| width | **Field Definition:** Captured Image Length |

## TXMediaDeviceInfo
> **适用平台：** iOS, C++

#### Information related to audio and video equipment (only for desktop platform)
This structure is used to describe key information of an audio and video device, such as Device ID, Device Name, etc., so that users can select their desired audio and video device in the user interface.
| Enumeration Types | Description |
| --- | --- |
| deviceId | Device ID (UTF-8) |
| deviceName | Device Name (UTF-8) |
| deviceProperties | Device Attributes |
| type | Device Type |

## ITXDeviceCollection
> **适用平台：** C++

#### Device Information List (only for desktop platform)

This structure functions similarly to std::vector<ITXDeviceInfo>, used to solve binary compatibility issues with different versions of STL containers.
| Enumeration Types | Description |
| --- | --- |
| getCount() | Number of Devices |
| index) | Device Information (JSON Format) **Note** Example: {"SupportedResolution":[{"width":640,"height":480},{"width":320,"height":240}]} param index Device Index, value is 0,getCount). Return JSON Format Device Information |
| release() | Release Device List, please do not use delete to release resources!!! |

## setExposureCompensation
> **适用平台：** Android, iOS

#### Set the exposure parameters of the camera, ranging from -1 to 1

## TXDeviceObserver
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| [onDeviceChanged:type:state: | Local device connection status has changed (applicable only to desktop systems) |

## onDeviceChanged
> **适用平台：** iOS

#### Local device connection status has changed (applicable only to desktop systems)

When local devices (including camera, microphone, and speaker) are plugged in or unplugged, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| deviceId | Device ID |
| state | On/Off status, 0: Device added; 1: Device removed; 2: Device enabled. |
| type | Device Type |

## isCameraZoomSupported
> **适用平台：** iOS

#### Check if the current camera supports scaling (only for mobile terminal)

## isCameraTorchSupported
> **适用平台：** iOS

#### Check if the flash can be turned on (only for mobile terminal)

## setObserver
> **适用平台：** iOS

#### Device hot swap callback (only for macOS System)
