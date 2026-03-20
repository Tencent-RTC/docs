# V2TXLiveCode 错误码

跨平台错误码参考（Android/iOS/Flutter）。各平台值完全一致，描述以最详细的平台为准。

## 平台声明语法
- **Android**: `public static final int <NAME> = <VALUE>;`
- **iOS**: 枚举值 `<NAME> = <VALUE>`
- **Flutter**: `static const int <NAME> = <VALUE>;`

## General Error Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_OK` | 0 | Operation Successful |
| `V2TXLIVE_ERROR_FAILED` | -1 | General Error |
| `V2TXLIVE_ERROR_INVALID_PARAMETER` | -2 | Invalid Parameter |
| `V2TXLIVE_ERROR_REFUSED` | -3 | API Call Refused |
| `V2TXLIVE_ERROR_NOT_SUPPORTED` | -4 | API Not Supported |
| `V2TXLIVE_ERROR_INVALID_LICENSE` | -5 | Invalid License |
| `V2TXLIVE_ERROR_REQUEST_TIMEOUT` | -6 | Request Timeout |
| `V2TXLIVE_ERROR_SERVER_PROCESS_FAILED` | -7 | Server Processing Failed |
| `V2TXLIVE_ERROR_DISCONNECTED` | -8 | Connection Disconnected |
| `V2TXLIVE_ERROR_NO_AVAILABLE_HEVC_DECODERS` | -2304 | No Available HEVC Decoders |

## Network-Related Warning Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_WARNING_NETWORK_BUSY` | 1101 | Poor Network Condition |
| `V2TXLIVE_WARNING_VIDEO_BLOCK` | 2105 | Video Playback Stuttering |

## Camera-Related Warning Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_WARNING_CAMERA_START_FAILED` | -1301 | Camera Start Failed |
| `V2TXLIVE_WARNING_CAMERA_OCCUPIED` | -1316 | Camera Occupied |
| `V2TXLIVE_WARNING_CAMERA_NO_PERMISSION` | -1314 | Camera Permission Not Granted |

## Microphone-Related Warning Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_WARNING_MICROPHONE_START_FAILED` | -1302 | Microphone Start Failed |
| `V2TXLIVE_WARNING_MICROPHONE_OCCUPIED` | -1319 | Microphone Occupied |
| `V2TXLIVE_WARNING_MICROPHONE_NO_PERMISSION` | -1317 | Microphone Permission Not Granted |

## Screen Sharing Warning Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_WARNING_SCREEN_CAPTURE_NOT_SUPPORTED` | -1309 | Screen Sharing Not Supported by System |
| `V2TXLIVE_WARNING_SCREEN_CAPTURE_START_FAILED` | -1308 | Failed to Start Screen Recording |
| `V2TXLIVE_WARNING_SCREEN_CAPTURE_INTERRUPTED` | -7001 | Screen Recording Interrupted by System |

## Codec Warning Codes
| 错误码 | 值 | 含义 |
|---|---|---|
| `V2TXLIVE_WARNING_CURRENT_ENCODE_TYPE_CHANGED` | 1104 | Encoder Type Changed |
| `V2TXLIVE_WARNING_CURRENT_DECODE_TYPE_CHANGED` | 2008 | Decoder Type Changed |
| `V2TXLIVE_WARNING_ENCODER_START_FAILED` | -1303 | Video Encoder Start Failed |
| `V2TXLIVE_WARNING_DECODER_START_FAILED` | -1304 | Video Decoder Start Failed |
