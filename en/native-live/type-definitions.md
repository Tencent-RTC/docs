# V2TXLiveDef 类型定义

适用平台：Android / iOS / Flutter

## Supported Protocols

### V2TXLiveMode

Supported Protocols for Live Streaming Push

| 枚举值 | 说明 |
|--------|------|
| TXLiveMode_RTMP | Supported protocol: RTMP |
| TXLiveMode_RTC | Supported protocol: RTC |
**Protocol Details**:
- RTMP: Standard protocol for live streaming
- RTC: Real-Time Communication (TRTC) protocol

## Video Type Definitions

### V2TXLiveVideoResolution
Video Resolution Enumeration

| Resolution   | Bitrate Range | Frame Rate | Description                                 |
|--------------|--------------|------------|---------------------------------------------|
| 160×160      | 100–150 Kbps | 15 fps     | Ultra-low resolution, ideal for voice calls |
| 270×270      | 200–300 Kbps | 15 fps     | Low resolution                              |
| 480×480      | 350–525 Kbps | 15 fps     | Standard square resolution                  |
| 320×240      | 250–375 Kbps | 15 fps     | 4:3 aspect ratio, standard resolution       |
| 480×360      | 400–600 Kbps | 15 fps     | 4:3 aspect ratio, medium resolution         |
| 640×480      | 600–900 Kbps | 15 fps     | SD resolution                               |
| 320×180      | 250–400 Kbps | 15 fps     | 16:9 aspect ratio, low resolution           |
| 480×270      | 350–550 Kbps | 15 fps     | 16:9 aspect ratio, standard resolution      |
| 640×360      | 500–900 Kbps | 15 fps     | 16:9 aspect ratio, medium resolution        |
| 960×540      | 800–1500 Kbps| 15 fps     | HD resolution                               |
| 1280×720     | 1000–1800 Kbps| 15 fps    | 720p HD                                     |
| 1920×1080    | 2500–3000 Kbps| 15 fps    | 1080p Full HD                               |

### V2TXLiveVideoResolutionMode
Video Orientation Mode
Video resolution orientation:

| Enum Value | Description |
|------------|-------------|
| `v2TXLiveVideoResolutionModeLandscape` | Landscape orientation |
| `v2TXLiveVideoResolutionModePortrait` | Portrait orientation |
**Note**: The orientation affects the actual resolution dimensions. For example:
- Landscape: 640×360 = 640×360
- Portrait: 640×360 = 360×640

### V2TXLiveVideoEncoderParam

Video Encoder Parameter Configuration

| 字段 | 类型 | 说明 |
|------|------|------|
| videoResolution | V2TXLiveVideoResolution | Video resolution |
| videoResolutionMode | V2TXLiveVideoResolutionMode | Orientation mode |
| videoFps | int | Capture frame rate |
| videoBitrate | int | Target video bitrate |
| minVideoBitrate | int | Minimum video bitrate |
**Recommended Settings**:
- **Frame Rate**: 15fps or 20fps. Using less than 5fps can cause severe playback stuttering; above 20fps may result in unnecessary bandwidth usage.
- **Adaptive Bitrate**: Set both `videoBitrate` and `minVideoBitrate` to define the range for bitrate adaptation.

### V2TXLiveMirrorType

Local Camera Mirror Mode

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveMirrorTypeAuto | System default: front camera mirrored, rear camera not mirrored | 全平台 |
| V2TXLiveMirrorTypeEnable | Mirror mode enabled for both front and rear cameras | Android/iOS |
| V2TXLiveMirrorTypeDisable | Mirror mode disabled for both front and rear cameras | Android/iOS |
| v2TXLiveMirrorTypeEnable | Mirror mode for both front and rear cameras | Flutter |
| v2TXLiveMirrorTypeDisable | No mirror mode for either camera | Flutter |

### V2TXLiveFillMode

Video Fill Mode

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveFillModeFill | Image fills the view, cropping excess; some content may be lost | Android/iOS |
| V2TXLiveFillModeFit | Image's longer side fits the view, shorter side padded with black; full content retained | Android/iOS |
| V2TXLiveFillModeScaleFill | Image stretched to fill view; aspect ratio may not be preserved | Android/iOS |
| v2TXLiveFillModeFill | Image fills the screen; excess is cropped, some content may be lost | Flutter |
| v2TXLiveFillModeFit | Long edge fills the screen; short edge is padded with black, full content retained | Flutter |
| v2TXLiveFillModeScaleFill | Image is stretched to fill; aspect ratio may not be preserved | Flutter |

### V2TXLiveRotation

Video Rotation Angle

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveRotation0 | No rotation | 全平台 |
| V2TXLiveRotation90 | 90° clockwise rotation | Android/iOS |
| V2TXLiveRotation180 | 180° clockwise rotation | Android/iOS |
| V2TXLiveRotation270 | 270° clockwise rotation | Android/iOS |
| v2TXLiveRotation90 | Rotate 90 degrees clockwise | Flutter |
| v2TXLiveRotation180 | Rotate 180 degrees clockwise | Flutter |
| v2TXLiveRotation270 | Rotate 270 degrees clockwise | Flutter |

### V2TXLivePixelFormat

Video Frame Pixel Format

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLivePixelFormatUnknown | Unknown pixel format | Android/iOS |
| V2TXLivePixelFormatI420 | YUV420P I420 format | 全平台 |
| V2TXLivePixelFormatTexture2D | OpenGL 2D texture format | 全平台 |
| V2TXLivePixelFormatNV12 | NV12 | iOS |
| V2TXLivePixelFormatBGRA32 | BGRA32 | iOS |
| v2TXLivePixelFormatUnknown | Unknown | Flutter |
| v2TXLivePixelFormatNV12 | YUV420SP NV12 format | Flutter |
| v2TXLivePixelFormatBGRA32 | BGRA8888 format | Flutter |

### V2TXLiveBufferType

Video Data Buffer Type

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveBufferTypeUnknown | Unknown buffer type | Android/iOS |
| V2TXLiveBufferTypeByteBuffer | DirectBuffer, stores I420 and other buffers, used at the native layer | Android |
| V2TXLiveBufferTypeByteArray | byte[], stores I420 and other buffers, used at the Java layer | Android |
| V2TXLiveBufferTypeTexture | Operates directly on texture ID for optimal performance and minimal quality loss | Android/iOS |
| V2TXLiveBufferTypePixelBuffer | PixelBuffer (native iOS video format) | iOS |
| V2TXLiveBufferTypeNSData | NSData (used for I420 and other buffer types) | iOS |
| v2TXLiveBufferTypeUnknown | Unknown | Flutter |
| v2TXLiveBufferTypeByteBuffer | DirectBuffer for I420 and similar buffers, used in native layer | Flutter |
| v2TXLiveBufferTypeByteArray | byte[] for I420 and similar buffers, used in Java layer | Flutter |
| v2TXLiveBufferTypeTexture | Operates directly on texture ID, optimal performance, minimal quality loss | Flutter |

### V2TXLiveTexture

> **适用平台：** Android（iOS、Flutter 不支持）

Video Texture Wrapper

| 字段 | 类型 | 说明 |
|------|------|------|
| textureId | int | Video texture ID |
| eglContext10 | javax.microedition.khronos.egl.EGLContext | OpenGL EGLContext (v1.0) |
| eglContext14 | android.opengl.EGLContext | Android EGLContext (v1.4) |

### V2TXLiveVideoFrame

Video Frame Information

| 字段 | 类型 | 说明 | 平台 |
|------|------|------|------|
| pixelFormat | V2TXLivePixelFormat | Pixel format | 全平台 |
| bufferType | V2TXLiveBufferType | Buffer type | 全平台 |
| texture | V2TXLiveTexture | Texture wrapper | Android |
| data | byte[] | Video data | 全平台 |
| buffer | ByteBuffer | Video data buffer | Android |
| width | int | Frame width | 全平台 |
| height | int | Frame height | 全平台 |
| rotation | int | Rotation angle | 全平台 |
| pixelBuffer | CVPixelBufferRef | PixelBuffer data | iOS |
| textureId | GLuint | Texture ID | iOS/Flutter |

## Audio Type Definitions

### V2TXLiveAudioQuality

Audio Quality Settings

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveAudioQualitySpeech | Speech: 16kHz sample rate, mono, 16 Kbps, suitable for voice calls | Android/iOS |
| V2TXLiveAudioQualityDefault | Default: 48kHz sample rate, mono, 50 Kbps, SDK default | Android/iOS |
| V2TXLiveAudioQualityMusic | Music: 48k sample rate, stereo, full band, 128kbps; suitable for high-fidelity music | Android/iOS |
| v2TXLiveAudioQualitySpeech | Speech quality: 16k sample rate, mono, 16kbps bitrate, ideal for voice calls | Flutter |
| v2TXLiveAudioQualityDefault | Default quality: 48k sample rate, mono, 50kbps bitrate (SDK default) | Flutter |
| v2TXLiveAudioQualityMusic | Music quality: 48k sample rate, stereo/full band, 128kbps bitrate, suitable for high-fidelity music | Flutter |

### V2TXLiveAudioFrame

Audio Frame Data

| 字段 | 类型 | 说明 |
|------|------|------|
| data | byte[] | Audio data |
| sampleRate | int | Sample rate (default: 48000 Hz) |
| channel | int | Audio channel count (default: 1, mono) |
| timestamp | long | Timestamp (ms) |

### V2TXLiveAudioFrameOperationMode
> **适用平台：** Android / iOS（Flutter 不支持）

Audio Callback Data Access Mode

| 枚举值 | 说明 | 平台 |
|--------|------|------|
| V2TXLiveAudioFrameOperationModeReadWrite | Read/write mode: access and modify callback audio data (default) | Android/iOS |
| V2TXLiveAudioFrameOperationModeReadOnly | Read-only mode: access callback audio data only | Android/iOS |

### V2TXLiveAudioFrameObserverFormat
> **适用平台：** Android / iOS（Flutter 不支持）

Audio Frame Callback Format

| 字段 | 类型 | 说明 |
|------|------|------|
| sampleRate | int | Sample rate (default: 48000 Hz) |
| channel | int | Audio channel count (default: 1) |
| samplesPerCall | int | Samples per callback |
| mode | V2TXLiveAudioFrameOperationMode | Data access mode |
**Parameter Notes**:
- **samplesPerCall**: Must be an integer multiple of sampleRate/100

## Statistics Data Definitions

### V2TXLivePusherStatistics

Pusher Statistics

| 字段 | 类型 | 说明 |
|------|------|------|
| appCpu | int | App CPU usage (%) |
| systemCpu | int | System CPU usage (%) |
| width | int | Video width |
| height | int | Video height |
| fps | int | Frame rate (fps) |
| videoBitrate | int | Video bitrate (Kbps) |
| audioBitrate | int | Audio bitrate (Kbps) |
| rtt | int | SDK to cloud Round-Trip Time (RTT) (ms) |
| netSpeed | int | Uplink speed (Kbps) |

### V2TXLivePlayerStatistics

Player Statistics

| 字段 | 类型 | 说明 |
|------|------|------|
| appCpu | int | App CPU usage (%) |
| systemCpu | int | System CPU usage (%) |
| width | int | Video width |
| height | int | Video height |
| fps | int | Frame rate (fps) |
| videoBitrate | int | Video bitrate (Kbps) |
| audioBitrate | int | Audio bitrate (Kbps) |
| audioPacketLoss | int | Audio packet loss rate (%) |
| videoPacketLoss | int | Video packet loss rate (%) |
| jitterBufferDelay | int | Playback delay (ms) |
| audioTotalBlockTime | int | Total audio playback stuttering time (ms) |
| audioBlockRate | int | Audio playback stuttering rate (%) |
| videoTotalBlockTime | int | Total video playback stuttering time (ms) |
| videoBlockRate | int | Video playback stuttering rate (%) |
| rtt | int | Round-Trip Time (RTT) (ms) |
| netSpeed | int | Download speed (Kbps) |

## Connection Status Enumerations

### V2TXLivePushStatus

Live Stream Connection Status

| 枚举值 | 说明 |
|--------|------|
| V2TXLivePushStatusDisconnected | Disconnected from server |
| V2TXLivePushStatusConnecting | Connecting to server |
| V2TXLivePushStatusConnectSuccess | Connected to server |
| V2TXLivePushStatusReconnecting | Reconnecting to server |

## Cloud Stream Mixing Configuration

### V2TXLiveMixInputType

Stream Mixing Input Type

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveMixInputTypeAudioVideo | Mix audio and video |
| V2TXLiveMixInputTypePureVideo | Mix video only |
| V2TXLiveMixInputTypePureAudio | Mix audio only |

### V2TXLiveMixStream

Cloud Stream Mixing Sub-Screen Layout

| 字段 | 类型 | 说明 |
|------|------|------|
| userId | String | userId participating in cloud stream mixing |
| streamId | String | Push streamId |
| x | int | X coordinate (absolute pixels) |
| y | int | Y coordinate (absolute pixels) |
| width | int | Layer width (absolute pixels) |
| height | int | Layer height (absolute pixels) |
| zOrder | int | Layer z-order (1–15, must be unique) |
| inputType | V2TXLiveMixInputType | Input type |

### V2TXLiveTranscodingConfig

Cloud Stream Mixing MixTranscoding Configuration

| 字段 | 类型 | 说明 | 平台 |
|------|------|------|------|
| videoWidth | int | Output video width after transcoding | 全平台 |
| videoHeight | int | Output video height after transcoding | 全平台 |
| videoBitrate | int | Output video bitrate after transcoding (Kbps) | 全平台 |
| videoFramerate | int | Output video frame rate after transcoding (fps) | 全平台 |
| videoGOP | int | Keyframe interval (seconds) | 全平台 |
| backgroundColor | int | Mixed screen background color | 全平台 |
| backgroundImage | String | Mixed screen background image | 全平台 |
| audioSampleRate | int | Output audio sample rate after transcoding | 全平台 |
| audioBitrate | int | Output audio bitrate after transcoding (Kbps) | 全平台 |
| audioChannels | int | Output audio channel count after transcoding | 全平台 |
| mixStreams | ArrayList<V2TXLiveMixStream> | Sub-screen layout information | Android/Flutter |
| outputStreamId | String | Output stream ID for CDN | 全平台 |

## Local Recording Configuration

### V2TXLiveRecordMode

Local Recording Mode

| 枚举值 | 说明 |
|--------|------|
| V2TXLiveRecordModeBoth | Record both audio and video |

### V2TXLiveLocalRecordingParams

Local Recording Parameters

| 字段 | 类型 | 说明 |
|------|------|------|
| filePath | String | Recording file path (required) |
| recordMode | V2TXLiveRecordMode | Recording mode |
| interval | int | Update interval for recording info (ms) |
**Parameter Notes**:
- **filePath**: Must include filename and extension (currently supports MP4 only)
- **interval**: Valid range is 1000–10000ms; set to -1 to disable recording info callback

## Log Configuration

### V2TXLiveLogLevel

Log Level Enumeration

| 常量名 | 值 | 说明 |
|--------|-----|------|
| V2TXLiveLogLevelAll | 0 | Output all log levels |
| V2TXLiveLogLevelDebug | 1 | Output DEBUG and above |
| V2TXLiveLogLevelInfo | 2 | Output INFO and above |
| V2TXLiveLogLevelWarning | 3 | Output WARNING and above |
| V2TXLiveLogLevelError | 4 | Output ERROR and above |
| V2TXLiveLogLevelFatal | 5 | Output only FATAL |
| V2TXLiveLogLevelNULL | 6 | Disable all SDK logs |

### V2TXLiveLogConfig

Log Configuration

| 字段 | 类型 | 说明 |
|------|------|------|
| logLevel | int | Log level |
| enableObserver | boolean | Enable log observer callback |
| enableConsole | boolean | Print logs to console |
| enableLogFile | boolean | Enable local log files |
| logPath | String | Local log directory |
**Default Storage Path**: On iOS/macOS, logs are stored in the Documents/log directory by default.
**Field Descriptions**:
- **logLevel**: Controls the verbosity of SDK log output
- **enableObserver**: Receive log info via Observer for custom handling
- **enableConsole**: Print log info to the editor console for debugging
- **enableLogFile**: Enable local log file for troubleshooting
- **logPath**: Directory for storing logs; uses default path if not specified

## Additional Configurations

### V2TXLiveSocks5ProxyConfig

SOCKS5 Proxy Configuration

| 字段 | 类型 | 说明 |
|------|------|------|
| supportHttps | boolean | Enable HTTPS support |
| supportTcp | boolean | Enable TCP support |
| supportUdp | boolean | Enable UDP support |

### V2TXLiveStreamInfo

Adaptive Stream Switching Info

| 字段 | 类型 | 说明 |
|------|------|------|
| width | int | Video width |
| height | int | Video height |
| bitrate | int | Bitrate (bps) |
| framerate | float | Frame rate |
| url | String | Stream URL |
This document details all type definitions in `V2TXLiveDef.java` and serves as a comprehensive API reference for developing with Tencent Cloud Live Streaming.

## Video Types

### Picture

in-Picture State

> **适用平台：** iOS（Android、Flutter 不支持）

### V2TXLivePictureInPictureState

> **适用平台：** iOS（Android、Flutter 不支持）

Picture-in-Picture State Enumeration

| 枚举值 | 说明 |
|--------|------|
| V2TXLivePictureInPictureStateUndefined | Undefined state |
| V2TXLivePictureInPictureStateOccurError | Error occurred |
| V2TXLivePictureInPictureStateWillStart | Will start |
| V2TXLivePictureInPictureStateDidStart | Started |
| V2TXLivePictureInPictureStateWillStop | Will stop |
| V2TXLivePictureInPictureStateDidStop | Stopped |
| V2TXLivePictureInPictureStateRestoreUI | Restore UI from picture-in-picture |

## Connection Status

### V2TXAudioRoute

> **适用平台：** iOS（Android、Flutter 不支持）

Audio Output Mode

| 枚举值 | 说明 |
|--------|------|
| V2TXAudioRouteSpeakerphone | Play via the speaker (device speaker) |
| V2TXAudioRouteEarpiece | Play via the receiver (device earpiece) |

## Render View Definition

### V2TXLiveRenderView

> **适用平台：** Flutter（Android、iOS 不支持）

Defines the video rendering view type.

| 常量名 | 值 | 说明 |
|--------|-----|------|
| renderViewType | 'v2_live_view_factory' |  |

## Other Configurations

### TXSystemVolumeType

> **适用平台：** Flutter（Android、iOS 不支持）

System Volume Control Type

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXSystemVolumeTypeAuto | 0 | Auto mode switching |
| TXSystemVolumeTypeMedia | 1 | Media volume |
| TXSystemVolumeTypeVOIP | 2 | Call volume |

### TXAudioRoute

> **适用平台：** Flutter（Android、iOS 不支持）

Audio Routing Type

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXAudioRouteSpeakerphone | 0 | Play via the speaker |
| TXAudioRouteEarpiece | 1 | Play via the receiver |

## Audio Effects Processing

### TXVoiceReverbType

> **适用平台：** Flutter（Android、iOS 不支持）

Reverb Effect Types

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXVoiceReverbTypeOff | 0 | Disable reverb |
| TXVoiceReverbTypeKTV | 1 | KTV |
| TXVoiceReverbTypeSmallRoom | 2 | Small room |
| TXVoiceReverbTypeGreatHall | 3 | Great hall |
| TXVoiceReverbTypeDeep | 4 | Deep |
| TXVoiceReverbTypeLoud | 5 | Loud |
| TXVoiceReverbTypeMetallic | 6 | Metallic |
| TXVoiceReverbTypeMagnetic | 7 | Magnetic |
| TXVoiceReverbTypeEthereal | 8 | Ethereal |
| TXVoiceReverbTypeStudio | 9 | Studio |
| TXVoiceReverbTypeMelodious | 10 | Melodious |
| TXVoiceReverbTypeStudio2 | 11 | Studio 2 |

### TXVoiceChangerType

> **适用平台：** Flutter（Android、iOS 不支持）

Voice Changer Effect Types

| 常量名 | 值 | 说明 |
|--------|-----|------|
| TXVoiceChangerTypeOff | 0 | Disable voice changer |
| TXVoiceChangerTypeChild | 1 | Naughty boy |
| TXVoiceChangerTypeLittleGirl | 2 | Girl |
| TXVoiceChangerTypeUncle | 3 | Middle-aged man |
| TXVoiceChangerTypeHeavyMetal | 4 | Heavy metal |
| TXVoiceChangerTypeCold | 5 | Cold |
| TXVoiceChangerTypePunk | 6 | Punk |
| TXVoiceChangerTypeBeast | 7 | Beast |
| TXVoiceChangerTypeFatty | 8 | Fatty |
| TXVoiceChangerTypeStrongCurrent | 9 | Strong current |
| TXVoiceChangerTypeRobot | 10 | Robot |
| TXVoiceChangerTypeEthereal | 11 | Ethereal |
