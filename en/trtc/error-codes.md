> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXLiteAVCode 错误码 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXLiteAVCode 错误码 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TXLiteAVCode
## TXLiteAVError

#### 错误码。
| Enumeration | Value | Description |
| --- | --- | --- |
| ERR_NULL | 0 | No error |
| ERR_FAILED | -1 | A common error not yet classified |
| ERR_INVALID_PARAMETER | -2 | An invalid parameter was passed in during API calling |
| ERR_REFUSED | -3 | The API call was rejected |
| ERR_NOT_SUPPORTED | -4 | Does not support API call currently |
| ERR_INVALID_LICENSE | -5 | Failed to call the API due to invalid license |
| ERR_REQUEST_SERVER_TIMEOUT | -6 | The server request timed out |
| ERR_SERVER_PROCESS_FAILED | -7 | The server could not handle your request |
| ERR_DISCONNECTED | -8 | Disconnect from user |
| ERR_CAMERA_START_FAIL | -1301 | Failed to enable the camera; for example, the configuration program (driver) of the camera on Windows or macOS was exceptional. In this case, please try to disable and then enable the camera again, restart the device, or update the configuration program |
| ERR_CAMERA_NOT_AUTHORIZED | -1314 | The camera is not authorized. This error usually occurs on mobile devices and may be caused by permission denial by user |
| ERR_CAMERA_SET_PARAM_FAIL | -1315 | Incorrect camera parameter settings (unsupported value or other causes) |
| ERR_CAMERA_OCCUPY | -1316 | The camera is being used. Please try to enable another camera |
| ERR_SCREEN_CAPTURE_START_FAIL | -1308 | Failed to start screen sharing. If this error occurs on a mobile device, it may be caused by permission denial by user; if on Windows or macOS, please check whether parameters of the screen sharing API meet the requirements |
| ERR_SCREEN_CAPTURE_UNSURPORT | -1309 | Screen sharing failed. Please use Android 5.0 or above if the device is on Android or use iOS 11.0 or above if the device is on iOS |
| ERR_SCREEN_CAPTURE_STOPPED | -7001 | Screencapturing was stopped by the system |
| ERR_SCREEN_SHARE_NOT_AUTHORIZED | -102015 | No permission for uplink auxiliary route |
| ERR_SCREEN_SHRAE_OCCUPIED_BY_OTHER | -102016 | Other users are upstreaming substream |
| ERR_VIDEO_ENCODE_FAIL | -1303 | Failed to encode video frames; for example, when a user switches from an iOS device to another device, the hardware encoder may be released by the system; and after the user switches back, this error may be thrown before the hardware encoder is restarted |
| ERR_UNSUPPORTED_RESOLUTION | -1305 | Unsupported video resolution |
| ERR_PIXEL_FORMAT_UNSUPPORTED | -1327 | Custom Video Capture: Unsupported pixel format |
| ERR_BUFFER_TYPE_UNSUPPORTED | -1328 | Custom Video Capture: Unsupported buffer type |
| ERR_NO_AVAILABLE_HEVC_DECODERS | -2304 | No available HEVC decoder found |
| ERR_MIC_START_FAIL | -1302 | Failed to enable the mic; for example, the configuration program (driver) of the mic on Windows or macOS was exceptional. In this case, please try to disable and then enable the mic again, restart the device, or update the configuration program |
| ERR_MIC_NOT_AUTHORIZED | -1317 | No access to the microphone. This usually occurs on mobile devices and may be because the user denied the access. |
| ERR_MIC_OCCUPY | -1319 | The microphone is occupied. This occurs when, for example, the user is currently having a call on the mobile device. |
| ERR_MIC_STOP_FAIL | -1320 | Failed to stop the microphone. |
| ERR_SPEAKER_START_FAIL | -1321 | Failed to enable the speaker; for example, the configuration program (driver) of the speaker on Windows or macOS was exceptional. In this case, please try to disable and then enable the speaker again, restart the device, or update the configuration program |
| ERR_SPEAKER_SET_PARAM_FAIL | -1322 | Failed to set speaker parameters. |
| ERR_SPEAKER_STOP_FAIL | -1323 | Failed to stop the speaker. |
| ERR_AUDIO_PLUGIN_START_FAIL | -1330 | console.log('Failed to enable system sound recording; for example, the audio driver plugin was unavailable'); |
| ERR_AUDIO_PLUGIN_INSTALL_NOT_AUTHORIZED | -1331 | Installation of the audio driver plugin was unauthorized |
| ERR_AUDIO_PLUGIN_INSTALL_FAILED | -1332 | Failed to install the audio driver plugin |
| ERR_AUDIO_PLUGIN_INSTALLED_BUT_NEED_RESTART | -1333 | Successfully installed the virtual sound card plugin, but the feature is temporarily unavailable after the first installation. This is a macOS system limitation. Please prompt the user to restart the current app upon receiving this error code |
| ERR_AUDIO_ENCODE_FAIL | -1304 | Failed to encode audio frames; for example, the SDK could not process the custom audio data passed in |
| ERR_UNSUPPORTED_SAMPLERATE | -1306 | Unsupported audio sample rate |
| ERR_SYSTEM_LOOPBACK_START_FAILED | -1307 | System mixing startup error. Common causes and solutions: 1. The current system version is incompatible. Specifying or excluding specific processes for mixing requires Windows version 10.0.19042 (20H2) or higher. Specifying a path for mixing (x64 version) also requires Windows version 10.0.19042 (20H2) or higher. Please check your current system version. 2. Invalid input parameters (including empty parameters, non-existent paths, non-existent process IDs, and non-existent device names). Please check if the input parameters are valid. 3. No available speaker devices. Please check if any are available. |
| ERR_SYSTEM_LOOPBACK_THIRD_PARTY_APP_EXIT | -1324 | An error occurred during the mixing and acquisition process of the third-party application. This error may occur after the third-party application's mixing process starts successfully, but the specified process or application unexpectedly exits during the sound acquisition process. Please check the status of the specified process or application. You can try retrying by re-entering the process ID. |
| ERR_TRTC_ENTER_ROOM_FAILED | -3301 | Failed to enter the room. Please view -3301 in onError to confirm the message for the reason of the failure. |
| ERR_TRTC_REQUEST_IP_TIMEOUT | -3307 | Request for IP and Sig timed out. Please check whether your network is functioning properly and whether UDP is unblocked in your network firewall. You can try to access the following IPs: 162.14.22.165:8000, 162.14.6.105:8000 and the domain: default-query.trtc.tencent-cloud.com:8000 |
| ERR_TRTC_CONNECT_SERVER_TIMEOUT | -3308 | Request for room entry timed out. Please check your network connection or whether you are on a VPN. You can also try switching to 4G for confirmation. |
| ERR_TRTC_ROOM_PARAM_NULL | -3316 | Join Room Parameters are empty. Please check: enterRoom:appScene: whether the valid parameters were passed during the interface call |
| ERR_TRTC_INVALID_SDK_APPID | -3317 | Join Room Parameters sdkAppId is incorrect. Please check whether TRTCParams.sdkAppId is empty |
| ERR_TRTC_INVALID_ROOM_ID | -3318 | Join Room Parameters roomId is incorrect. Please check whether TRTCParams.roomId or TRTCParams.strRoomId is empty. Note that roomId and strRoomId cannot be used interchangeably |
| ERR_TRTC_INVALID_USER_ID | -3319 | Join Room Parameters userId is incorrect. Please check whether TRTCParams.userId is empty |
| ERR_TRTC_INVALID_USER_SIG | -3320 | Join Room Parameters userSig is incorrect. Please check whether TRTCParams.userSig is empty |
| ERR_TRTC_ENTER_ROOM_REFUSED | -3340 | Join Room request was denied. Please check whether you continuously invoked enterRoom for the same ID room |
| ERR_TRTC_INVALID_PRIVATE_MAPKEY | -100006 | You have enabled Advanced Permission Control, but the parameter TRTCParams.privateMapKey failed to verify You can refer to [Advanced Permission Control](https://cloud.tencent.com/document/product/647/32240) for checks |
| ERR_TRTC_SERVICE_SUSPENDED | -100013 | Unavailable service. Please check whether the remaining validity period in minutes in the package is greater than 0 and whether the Tencent Cloud account is in arrears. You can refer to [Package management](https://cloud.tencent.com/document/product/647/50492) for viewing and configuration |
| ERR_TRTC_USER_SIG_CHECK_FAILED | -100018 | UserSig verification failed. Please check whether the parameters TRTCParams.userSig are filled correctly or have expired. You can refer to [UserSig Generation and Verification](https://cloud.tencent.com/document/product/647/50686) for verification |
| ERR_TRTC_PUSH_THIRD_PARTY_CLOUD_TIMEOUT | -3321 | The relayed push request timed out |
| ERR_TRTC_MIX_TRANSCODING_TIMEOUT | -3322 | The On-Cloud MixTranscoding request timed out |
| ERR_TRTC_PUSH_THIRD_PARTY_CLOUD_FAILED | -3323 | Exception in Relay to CDN Callback |
| ERR_TRTC_MIX_TRANSCODING_FAILED | -3324 | Exception in Cloud Mixed Streaming Callback |
| ERR_TRTC_START_PUBLISHING_TIMEOUT | -3333 | Signaling timed out when starting pushing to Tencent Cloud LVB CDN |
| ERR_TRTC_START_PUBLISHING_FAILED | -3334 | Signaling was exceptional when starting pushing to Tencent Cloud LVB CDN |
| ERR_TRTC_STOP_PUBLISHING_TIMEOUT | -3335 | Signaling timed out when stopping pushing to Tencent Cloud LVB CDN |
| ERR_TRTC_STOP_PUBLISHING_FAILED | -3336 | Signaling was exceptional when stopping pushing to Tencent Cloud LVB CDN |
| ERR_TRTC_CONNECT_OTHER_ROOM_TIMEOUT | -3326 | Request for mic connection timed out |
| ERR_TRTC_DISCONNECT_OTHER_ROOM_TIMEOUT | -3327 | Request to exit mic connection timed out |
| ERR_TRTC_CONNECT_OTHER_ROOM_INVALID_PARAMETER | -3328 | Invalid parameter |
| ERR_TRTC_CONNECT_OTHER_ROOM_AS_AUDIENCE | -3330 | Currently in audience role. Cannot request or disconnect cross-room mic connection. Please first ` switchRole ` to anchor |
| ERR_BGM_OPEN_FAILED | -4001 | Failed to open file, such as invalid audio data, FFMPEG protocol not found, etc |
| ERR_BGM_DECODE_FAILED | -4002 | Audio file decoding failed |
| ERR_BGM_OVER_LIMIT | -4003 | Quantity exceeds the limit, such as preloading two background music tracks simultaneously |
| ERR_BGM_INVALID_OPERATION | -4004 | Invalid operation, such as calling preload operation after starting playback |
| ERR_BGM_INVALID_PATH | -4005 | Invalid path causing file opening failure. Please check if the path parameter points to a valid music file |
| ERR_BGM_INVALID_URL | -4006 | Invalid URL causing file opening failure. Please check in the browser if the provided URL address can download the intended music file |
| ERR_BGM_NO_AUDIO_STREAM | -4007 | No audio stream causing file opening failure. Please confirm the file is a valid audio file and is not corrupted |
| ERR_BGM_FORMAT_NOT_SUPPORTED | -4008 | Unsupported format causing file opening failure. Please confirm if the file format is supported. Mobile supports [mp3, aac, m4a, wav, ogg, mp4, mkv], and desktop supports [mp3, aac, m4a, wav, mp4, mkv] |
| ERR_CONCURRENT_BGM_OVER_LIMIT | -4009 | The number of background music tracks (BGMs) playing simultaneously has exceeded the limit. If this error message appears after the number of BGMs playing simultaneously exceeds 10, please check the number of concurrently playing BGMs. |

## TXLiteAVWarning

#### 警告码。
| Enumeration | Value | Description |
| --- | --- | --- |
| WARNING_HW_ENCODER_START_FAIL | 1103 | Issue with starting hardware encoding, automatically switched to software encoding |
| WARNING_CURRENT_ENCODE_TYPE_CHANGED | 1104 | Indicates a change in the encoder. You can get the current encoding format and type through the relevant fields in the extended information of the onWarning function. A value of 0 in the 'type' field represents H.264 encoding, and 1 represents H.265 encoding A value of 0 in the 'hardware' field represents software encoding, and 1 represents hardware encoding A value of 0 in the 'stream' field represents a large stream, 1 represents a small stream, and 2 represents an auxiliary stream |
| WARNING_VIDEO_ENCODER_SW_TO_HW | 1107 | The current CPU utilization is too high to meet software encoding needs, and the hardware encoder was automatically switch to |
| WARNING_INSUFFICIENT_CAPTURE_FPS | 1108 | Insufficient frame rate for video capturing through camera. This error may occur in Android mobile devices with an in-built beauty filter algorithm |
| WARNING_SW_ENCODER_START_FAIL | 1109 | Failed to enable the software encoder |
| WARNING_REDUCE_CAPTURE_RESOLUTION | 1110 | Camera resolution reduced for balance between frame rate and performance. |
| WARNING_CAMERA_DEVICE_EMPTY | 1111 | No available camera device detected |
| WARNING_CAMERA_NOT_AUTHORIZED | 1112 | The current application is not authorized to use the camera |
| WARNING_OUT_OF_MEMORY | 1113 | Insufficient memory, some features may not function properly. |
| WARNING_CAMERA_IS_OCCUPIED | 1114 | The camera is being used. |
| WARNING_CAMERA_DEVICE_ERROR | 1115 | Device anomaly with the camera. |
| WARNING_CAMERA_DISCONNECTED | 1116 | Unable to connect to the camera. |
| WARNING_CAMERA_START_FAILED | 1117 | Failed to start the camera. |
| WARNING_CAMERA_SERVER_DIED | 1118 | System anomaly. |
| WARNING_SCREEN_CAPTURE_NOT_AUTHORIZED | 1206 | The current application is not authorized to use screen recording |
| WARNING_CURRENT_DECODE_TYPE_CHANGED | 2008 | Indicates a change in the decoder. You can get the current decoding format through the 'type' field in the extended information of the onWarning function. Among them, 1 represents 265 decoding, and 0 represents 264 decoding. Note that this error code's extended information is not supported on Windows. |
| WARNING_VIDEO_FRAME_DECODE_FAIL | 2101 | Failed to decode the current video frame |
| WARNING_HW_DECODER_START_FAIL | 2106 | Failed to enable the hardware decoder, and the software decoder was used |
| WARNING_VIDEO_DECODER_HW_TO_SW | 2108 | The hardware decoder failed to decode the first I frame of the current stream, and the SDK automatically switched to the software decoder |
| WARNING_SW_DECODER_START_FAIL | 2109 | Failed to enable the software decoder |
| WARNING_VIDEO_RENDER_FAIL | 2110 | Failed to render video |
| WARNING_VIRTUAL_BACKGROUND_DEVICE_UNSURPORTED | 8001 | Virtual background device not supported |
| WARNING_VIRTUAL_BACKGROUND_NOT_AUTHORIZED | 8002 | Virtual background not authorized |
| WARNING_VIRTUAL_BACKGROUND_INVALID_PARAMETER | 8003 | Virtual background parameter exception |
| WARNING_VIRTUAL_BACKGROUND_PERFORMANCE_INSUFFICIENT | 8004 | Insufficient virtual background performance |
| WARNING_MICROPHONE_DEVICE_EMPTY | 1201 | No available mic device detected |
| WARNING_SPEAKER_DEVICE_EMPTY | 1202 | No available speaker device detected |
| WARNING_MICROPHONE_NOT_AUTHORIZED | 1203 | The current application is not authorized to use the mic |
| WARNING_MICROPHONE_DEVICE_ABNORMAL | 1204 | The audio capture device is unavailable (for example, it is in use or the PC determines it as an invalid device) |
| WARNING_SPEAKER_DEVICE_ABNORMAL | 1205 | The audio playback device is unavailable (for example, it is in use or the PC determines it as an invalid device) |
| WARNING_BLUETOOTH_DEVICE_CONNECT_FAIL | 1207 | Bluetooth device connection failed (for example, other applications occupying the audio channel through setting the call volume) |
| WARNING_MICROPHONE_IS_OCCUPIED | 1208 | Audio capture device is occupied |
| WARNING_AUDIO_FRAME_DECODE_FAIL | 2102 | Failed to decode the current audio frame |
| WARNING_AUDIO_RECORDING_WRITE_FAIL | 7001 | Failed to write the recorded audio to a file |
| WARNING_MICROPHONE_HOWLING_DETECTED | 7002 | Howling detected while recording audio |
| WARNING_IGNORE_UPSTREAM_FOR_AUDIENCE | 6001 | Currently in audience role, publishing audio and video is not supported. Please switch to the anchor role first |
| WARNING_UPSTREAM_AUDIO_AND_VIDEO_OUT_OF_SYNC | 6006 | Abnormal timestamp of audio and video transmission, which may cause audio and video out of sync issues |
| WARNING_RECONNECT_ON_SERVER_STATUS_ABNORMAL | 6007 | The server is experiencing an error and is reconnecting |

## 枚举类型
> **适用平台：** iOS

| Enumeration Types | Description |
| --- | --- |
| TXLiteAVError | Error Code |
| TXLiteAVWarning | Warning Codes |
