> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TRTC 类型定义 文档

## 概述
本文档是 实时音视频(TRTC SDK) TRTC 类型定义 的跨平台 API 参考，适用于 Android/C++ 平台。

## TRTCTypeDef
## Enumeration

| Enumeration Types | Description |
| --- | --- |
| TRTCVideoResolution | Video Resolution |
| TRTCVideoResolutionMode | Video aspect ratio mode |
| TRTCVideoStreamType | Video stream type |
| TRTCVideoFillMode | Video fill mode |
| TRTCVideoRotation | Video rotation direction |
| TRTCBeautyStyle | Beauty Filter (Skin Smoothing) algorithm |
| TRTCVideoPixelFormat | Video pixel format |
| TRTCVideoBufferType | Video data transfer method |
| TRTCVideoMirrorType | Video mirroring type |
| TRTCSnapshotSourceType | Data source for local video screenshot |
| TRTCAppScene | Use Cases |
| TRTCRoleType | Roles |
| TRTCQosControlMode(Deprecated) | Traffic control mode (deprecated) |
| TRTCVideoQosPreference | Image quality preference |
| TRTCQuality | Network quality |
| TRTCAVStatusType | Audio/Video status type |
| TRTCAVStatusChangeReason | Audio/Video status change reason type |
| TRTCAudioSampleRate | Audio sample rate |
| TRTCAudioQuality | Sound quality |
| TRTCAudioRoute | Audio routing (i.e., the sound playback mode) |
| TRTCReverbType | Sound Reverb Mode |
| TRTCVoiceChangerType | Voice Changing Type |
| TRTCSystemVolumeType | System Volume Type (Only applicable to mobile devices) |
| TRTCAudioFrameFormat | Content format of audio frame |
| TRTCAudioCapabilityType | System-supported audio capability types (Android devices only) |
| TRTCAudioFrameOperationMode | Audio Callback Data Read/Write Mode |
| TRTCLogLevel | Log Level |
| TRTCGSensorMode | Gravity Sensor Switch (Only applicable to mobile devices) |
| TRTCTranscodingConfigMode | Layout Mode for Cloud Mixed Streaming |
| TRTCRecordType | Media Recording Type |
| TRTCMixInputType | Mixed Streaming Input Type |
| TRTCDebugViewLevel | Debug information displayed on rendering control |
| TRTCAudioRecordingContent | Audio Recording Content Type |
| TRTCPublishMode | Media Stream Publication Mode |
| TRTCEncryptionAlgorithm | Encryption Algorithm |
| TRTCSpeedTestScene | Speed Test Scenario |
| TRTCGravitySensorAdaptiveMode | Set Adaptation Mode for Gravity Sensor (Only applicable to mobile devices) |

## TRTCVideoResolution

#### Video Resolution
Only Definition is in landscape resolution (e.g., 640 × 360). To use portrait resolution (e.g., 360 × 640), you also need to set TRTCVideoResolutionMode to Portrait.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_RESOLUTION_120_120 | 1 | Aspect Ratio 1:1; Resolution 120x120; Suggested Bitrate (VideoCall) 80kbps; Suggested Bitrate (LIVE) 120kbps. |
| TRTC_VIDEO_RESOLUTION_160_160 | 3 | Aspect Ratio 1:1; Resolution 160x160; Suggested Bitrate (VideoCall) 100kbps; Suggested Bitrate (LIVE) 150kbps. |
| TRTC_VIDEO_RESOLUTION_270_270 | 5 | Aspect Ratio 1:1; Resolution 270x270; Suggested Bitrate (VideoCall) 200kbps; Suggested Bitrate (LIVE) 300kbps. |
| TRTC_VIDEO_RESOLUTION_480_480 | 7 | Aspect Ratio 1:1; Resolution 480x480; Suggested Bitrate (VideoCall) 350kbps; Suggested Bitrate (LIVE) 500kbps. |
| TRTC_VIDEO_RESOLUTION_160_120 | 50 | Aspect Ratio 4:3; Resolution 160x120; Suggested Bitrate (VideoCall) 100kbps; Suggested Bitrate (LIVE) 150kbps. |
| TRTC_VIDEO_RESOLUTION_240_180 | 52 | Aspect Ratio 4:3; Resolution 240x180; Suggested Bitrate (VideoCall) 150kbps; Suggested Bitrate (LIVE) 250kbps. |
| TRTC_VIDEO_RESOLUTION_280_210 | 54 | Aspect Ratio 4:3; Resolution 280x210; Suggested Bitrate (VideoCall) 200kbps; Suggested Bitrate (LIVE) 300kbps. |
| TRTC_VIDEO_RESOLUTION_320_240 | 56 | Aspect Ratio 4:3; Resolution 320x240; Suggested Bitrate (VideoCall) 250kbps; Suggested Bitrate (LIVE) 375kbps. |
| TRTC_VIDEO_RESOLUTION_400_300 | 58 | Aspect Ratio 4:3; Resolution 400x300; Suggested Bitrate (VideoCall) 300kbps; Suggested Bitrate (LIVE) 450kbps. |
| TRTC_VIDEO_RESOLUTION_480_360 | 60 | Aspect Ratio 4:3; Resolution 480x360; Suggested Bitrate (VideoCall) 400kbps; Suggested Bitrate (LIVE) 600kbps. |
| TRTC_VIDEO_RESOLUTION_640_480 | 62 | Aspect Ratio 4:3; Resolution 640x480; Suggested Bitrate (VideoCall) 600kbps; Suggested Bitrate (LIVE) 900kbps. |
| TRTC_VIDEO_RESOLUTION_960_720 | 64 | Aspect Ratio 4:3; Resolution 960x720; Suggested Bitrate (VideoCall) 1000kbps; Suggested Bitrate (LIVE) 1500kbps. |
| TRTC_VIDEO_RESOLUTION_160_90 | 100 | Aspect Ratio 16:9; Resolution 160x90; Suggested Bitrate (VideoCall) 150kbps; Suggested Bitrate (LIVE) 250kbps. |
| TRTC_VIDEO_RESOLUTION_256_144 | 102 | Aspect Ratio 16:9; Resolution 256x144; Suggested Bitrate (VideoCall) 200kbps; Suggested Bitrate (LIVE) 300kbps. |
| TRTC_VIDEO_RESOLUTION_320_180 | 104 | Aspect Ratio 16:9; Resolution 320x180; Suggested Bitrate (VideoCall) 250kbps; Suggested Bitrate (LIVE) 400kbps. |
| TRTC_VIDEO_RESOLUTION_480_270 | 106 | Aspect Ratio 16:9; Resolution 480x270; Suggested Bitrate (VideoCall) 350kbps; Suggested Bitrate (LIVE) 550kbps. |
| TRTC_VIDEO_RESOLUTION_640_360 | 108 | Aspect Ratio 16:9; Resolution 640x360; Suggested Bitrate (VideoCall) 500kbps; Suggested Bitrate (LIVE) 900kbps. |
| TRTC_VIDEO_RESOLUTION_960_540 | 110 | Aspect Ratio 16:9; Resolution 960x540; Suggested Bitrate (VideoCall) 850kbps; Suggested Bitrate (LIVE) 1300kbps. |
| TRTC_VIDEO_RESOLUTION_1280_720 | 112 | Aspect Ratio 16:9; Resolution 1280x720; Suggested Bitrate (VideoCall) 1200kbps; Suggested Bitrate (LIVE) 1800kbps. |
| TRTC_VIDEO_RESOLUTION_1920_1080 | 114 | Aspect Ratio 16:9; Resolution 1920x1080; Suggested Bitrate (VideoCall) 2000kbps; Suggested Bitrate (LIVE) 3000kbps. |

## TRTCVideoResolutionMode

#### Video aspect ratio mode
In TRTCVideoResolution, only landscape resolution (e.g., 640 × 360) is defined. To use portrait resolution (e.g., 360 × 640), you also need to set TRTCVideoResolutionMode to Portrait.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_RESOLUTION_MODE_LANDSCAPE | 0 | Landscape resolution, e.g., TRTCVideoResolution_640_360 + TRTCVideoResolutionModeLandscape = 640 × 360. |
| TRTC_VIDEO_RESOLUTION_MODE_PORTRAIT | 1 | Portrait resolution, e.g., TRTCVideoResolution_640_360 + TRTCVideoResolutionModePortrait = 360 × 640. |

## TRTCVideoStreamType

#### Video stream type
There are three different video streams in TRTC:
-  HD large screen: Generally used for transmitting video data from the camera.
-  Low-definition small picture: The content of the small picture is the same as the large picture, but the resolution and bitrate are lower than those of the large picture, resulting in lower clarity.
-  Auxiliary stream picture: Generally used for screen sharing. Only one user can publish auxiliary stream video in the same room at the same time. Other users must wait until the current user stops before they can publish their own auxiliary stream.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_STREAM_TYPE_BIG | 0 | HD large screen: Generally used for transmitting video data from the camera. |
| TRTC_VIDEO_STREAM_TYPE_SMALL | 1 | Low-definition small picture: The content of the small picture is the same as the large picture, but the resolution and bitrate are lower than those of the large picture, resulting in lower clarity. |
| TRTC_VIDEO_STREAM_TYPE_SUB | 2 | Auxiliary stream picture: Generally used for screen sharing. Only one user can publish auxiliary stream video in the same room at the same time. Other users must wait until the current user stops before they can publish their own auxiliary stream. |

> **注意**
> **Note**
> Low-definition small picture cannot be enabled independently. The small picture must rely on the large picture, and the SDK will automatically set the resolution and bitrate of the low-definition small picture.

## TRTCVideoFillMode

#### Video fill mode
When the aspect ratio of the video display area is not equal to the aspect ratio of the video content, you need to specify the fill mode.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_RENDER_MODE_FILL | 0 | Fill mode: The content is scaled to fill the entire display area while maintaining the aspect ratio. The parts that exceed the display area will be cropped, so the image may be incomplete. |
| TRTC_VIDEO_RENDER_MODE_FIT | 1 | Adaptive mode: The image is scaled according to the long side to fit the display area. The short sides will be filled with black bars, so the image will be complete but may have black bars. |

## TRTCVideoRotation

#### Video rotation direction
TRTC provides APIs for setting the rotation angle of local and remote video. The angles listed below are in a clockwise direction.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_ROTATION_0 | 0 | No rotation. |
| TRTC_VIDEO_ROTATION_90 | 1 | Rotate clockwise by 90 degrees. |
| TRTC_VIDEO_ROTATION_180 | 2 | Rotate clockwise by 180 degrees. |
| TRTC_VIDEO_ROTATION_270 | 3 | Rotate clockwise by 270 degrees. |

## TRTCBeautyStyle

#### Beauty Filter (Skin Smoothing) algorithm
TRTC offers various skin smoothing algorithms. You can choose the one that best fits your product positioning.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_BEAUTY_STYLE_SMOOTH | 0 | Smooth: This aggressive algorithm results in a more pronounced skin smoothing effect, suitable for show live streaming. |
| TRTC_BEAUTY_STYLE_NATURE | 1 | Natural: This algorithm retains more facial details, resulting in a more natural smoothing effect, suitable for most live streaming scenarios. |
| TRTC_BEAUTY_STYLE_PITU | 2 | YouTu: Provided by YouTu Laboratory, this algorithm balances between smooth and natural, retaining more skin details than smooth and providing more smoothing than natural. |

## TRTCVideoPixelFormat

#### Video pixel format
TRTC provides custom data collection and custom rendering features for video:
-  In the custom data collection feature, you can use the following enumeration values to describe the pixel format of your captured video.
-  In the custom rendering feature, you can specify the pixel format you expect the SDK to call back.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_PIXEL_FORMAT_UNKNOWN | 0 | Undefined format. |
| TRTC_VIDEO_PIXEL_FORMAT_I420 | 1 | YUV420P (I420) format. |
| TRTC_VIDEO_PIXEL_FORMAT_Texture_2D | 2 | OpenGL 2D texture format. |
| TRTC_VIDEO_PIXEL_FORMAT_TEXTURE_EXTERNAL_OES | 3 | OES external texture format (Android platform only). |
| TRTC_VIDEO_PIXEL_FORMAT_NV21 | 4 | NV21 format. |
| TRTC_VIDEO_PIXEL_FORMAT_RGBA | 5 | RGBA format. |

## TRTCVideoBufferType

#### Video data transfer method
In custom data collection and custom rendering features, you need to use the following enumerated values to specify how you want to transmit video data:
-  Option 1: Transmit video data using Memory Buffer. This method is efficient on iOS, but less efficient on Android, and Windows currently only supports Memory Buffer transmission.
-  Option 2: Transmit video data using Texture. This method is highly efficient on both iOS and Android, but not supported on Windows. A certain level of OpenGL programming knowledge is required.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_BUFFER_TYPE_UNKNOWN | 0 | Undefined transmission method. |
| TRTC_VIDEO_BUFFER_TYPE_BYTE_BUFFER | 1 | Transmit video data using Memory Buffer, iOS: PixelBuffer; Android: Direct Buffer for JNI layer; Win: Memory Data Block. |
| TRTC_VIDEO_BUFFER_TYPE_BYTE_ARRAY | 2 | Transmit video data using Memory Buffer, iOS: More compact NSData type memory block after an extra rearrangement; Android: byte[] for JAVA layer. This transmission method is the least efficient of all the options. |
| TRTC_VIDEO_BUFFER_TYPE_TEXTURE | 3 | Transmit video data using OpenGL texture. |

## TRTCVideoMirrorType

#### Video mirroring type
Video mirroring refers to flipping the video content horizontally, especially for the local camera preview video. Enabling mirroring can provide the host with a familiar "mirror" experience.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_MIRROR_TYPE_AUTO | 0 | Automatic Mode: If using the front-facing camera, enable mirroring; if using the rear-facing camera, disable mirroring (only applicable to mobile devices). |
| TRTC_VIDEO_MIRROR_TYPE_ENABLE | 1 | Force On mirroring, regardless of whether the current camera is front-facing or rear-facing. |
| TRTC_VIDEO_MIRROR_TYPE_DISABLE | 2 | Force Off mirroring, regardless of whether the current camera is front-facing or rear-facing. |

## TRTCSnapshotSourceType

#### Data source for local video screenshot
The SDK supports capturing images from the following two data sources and saving them as local files:
-  Video Stream: Capture native video content from the video stream. The captured content is not controlled by the rendering controls.
-  Rendering Layer: Capture the displayed video content from the rendering controls, achieving a What You See Is What You Get (WYSIWYG) effect. However, if the display area is too small, the captured image will also be small.
-  Capture Layer: Capture the video content collected by the capture controls, allowing for the extraction of high-definition screenshots.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_SNAPSHOT_SOURCE_TYPE_STREAM | 0 | Capture native video content from the video stream. The captured content is not controlled by the rendering controls. |
| TRTC_SNAPSHOT_SOURCE_TYPE_VIEW | 1 | Capture the displayed video content from the rendering controls, achieving a What You See Is What You Get (WYSIWYG) effect. However, if the display area is too small, the captured image will also be small. |
| TRTC_SNAPSHOT_SOURCE_TYPE_CAPTURE | 2 | Capture the video content collected by the capture controls, allowing for the extraction of high-definition screenshots. |

## TRTCAppScene

#### Use Cases
TRTC has optimized for common audio and video application scenarios to meet the differentiated requirements of various vertical scenarios. The main scenarios can be categorized into two types:
-  Live Streaming (LIVE) Scenario: Includes LIVE and VoiceChatRoom. The former is audio + video, while the latter is audio only.
In the live streaming scenario, users are divided into two roles: "Anchors" and "Audience." A single room can support up to 100,000 people online simultaneously, making it suitable for live streaming scenarios with a large audience.
-  Real-Time (RTC) Scenario: Includes VideoCall and AudioCall. The former is audio + video, while the latter is audio only.
In the real-time scenario, there is no role differentiation among users, but a single room can support up to 300 people online simultaneously, making it suitable for small-scale real-time communication.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_APP_SCENE_VIDEOCALL | 0 | Video call scenario, supports 720p and 1080p HD quality, each room can have up to 300 concurrent users, with a maximum of 50 users speaking simultaneously. Applicable to [One-to-one video call], [300-person video conference], [Online consultation], [Educational small class], [Remote interview], and other business scenarios. |
| TRTC_APP_SCENE_LIVE | 1 | Video interactive live streaming, supports smooth mic switching without waiting, anchor delay less than 300 ms; supports up to 100,000 concurrent viewers, with playback latency as low as 1,000 ms. Applicable to [Low-latency interactive live streaming], [Large class], [Host PK], [Video blind dating], [Online interactive classroom], [Remote training], [Large-scale conferences], and other business scenarios. **Note** In this scenario, you must specify the current user's role through the role field in TRTCParams. |
| TRTC_APP_SCENE_AUDIOCALL | 2 | Voice call scenario, default uses SPEECH quality, each room can have up to 300 concurrent users, with a maximum of 50 users speaking simultaneously. Applicable to [One-to-one voice call], [300-person voice conference], [Voice chat], [Voice conference], [Online Werewolf Game], and other business scenarios. |
| TRTC_APP_SCENE_VOICE_CHATROOM | 3 | Voice interactive live streaming, supports smooth mic switching without waiting, anchor delay less than 300 ms; supports up to 100,000 concurrent viewers, with playback latency as low as 1,000 ms. Applicable to [Voice club], [Online KTV room], [Music live broadcast room], [FM radio station], and other business scenarios. **Note** In this scenario, you must specify the current user's role through the role field in TRTCParams. |

## TRTCRoleType

#### Roles
Only applicable to live streaming scenarios (i.e., TRTC_APP_SCENE_LIVE and TRTC_APP_SCENE_VOICE_CHATROOM), distinguishing users into two different identities:
-  Anchors: Can publish their own audio and video streams at any time, but the number of anchors is limited. A maximum of 50 anchors can publish their audio and video streams simultaneously in the same room.
-  Audience: Can only watch other users' audio and video streams. To publish audio and video streams, they need to switch their role to anchor using switchRole. A single room can accommodate up to 100,000 audience members.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCRoleAnchor | 20 | Anchors: Can publish their own audio and video streams at any time, but the number of anchors is limited. A maximum of 50 anchors can publish their audio and video streams simultaneously in the same room. |
| TRTCRoleAudience | 21 | Audience: Can only watch other users' audio and video streams. To publish audio and video streams, they need to switch their role to anchor using switchRole. A single room can accommodate up to 100,000 audience members. |

## TRTCQosControlMode

#### Traffic control mode (deprecated)
| Enumeration | Value | Description |
| --- | --- | --- |
| VIDEO_QOS_CONTROL_CLIENT | 0 | Local Control, used for SDK development internal debugging, please do not use for customers. |
| VIDEO_QOS_CONTROL_SERVER | 1 | Cloud Control, the default mode, is recommended. |

## TRTCVideoQosPreference

#### Image quality preference
TRTC offers two adjustment modes under a weak network environment: "Prioritize Clear Picture" or "Prioritize Smooth Picture". Both modes will primarily ensure audio data transmission.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VIDEO_QOS_PREFERENCE_SMOOTH | 1 | Smooth Priority: When the current network is insufficient to transmit both clear and smooth images, it prioritizes the smoothness of the picture, which results in a blurrier image with more mosaics. |
| TRTC_VIDEO_QOS_PREFERENCE_CLEAR | 2 | Clarity Priority (default): When the current network is insufficient to transmit both clear and smooth images, it prioritizes the clarity of the picture, which results in more stuttering. |

## TRTCQuality

#### Network quality
TRTC evaluates the current network quality every two seconds, with six evaluation levels: Excellent indicating the best, and Down indicating the worst.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_QUALITY_UNKNOWN | 0 | Undefined. |
| TRTC_QUALITY_Excellent | 1 | The current network is excellent. |
| TRTC_QUALITY_Good | 2 | The current network is good. |
| TRTC_QUALITY_Poor | 3 | The current network is average. |
| TRTC_QUALITY_Bad | 4 | The current network is poor. |
| TRTC_QUALITY_Vbad | 5 | The current network is very poor. |
| TRTC_QUALITY_Down | 6 | The current network does not meet the minimum requirements of TRTC. |

## TRTCAVStatusType

#### Audio/Video status type
This enumeration type is used for audio status change callback interfaces (onRemoteAudioStatusUpdated) and video status change callback interfaces (onRemoteVideoStatusUpdated) to specify the current audio or video status.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCAVStatusStopped | 0 | Stops playback. |
| TRTCAVStatusPlaying | 1 | Playing. |
| TRTCAVStatusLoading | 2 | Loading. |

## TRTCAVStatusChangeReason

#### Audio/Video status change reason type
This enumeration type is used for audio status change callback interfaces (onRemoteAudioStatusUpdated) and video status change callback interfaces (onRemoteVideoStatusUpdated) to specify the reason for the current audio or video status change.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCAVStatusChangeReasonInternal | 0 | Default value. |
| TRTCAVStatusChangeReasonBufferingBegin | 1 | Network buffering. |
| TRTCAVStatusChangeReasonBufferingEnd | 2 | End buffering. |
| TRTCAVStatusChangeReasonLocalStarted | 3 | Locally starts playing the audio or video stream. |
| TRTCAVStatusChangeReasonLocalStopped | 4 | Locally stops playing the audio or video stream. |
| TRTCAVStatusChangeReasonRemoteStarted | 5 | Starts (or resumes) the remote audio or video stream. |
| TRTCAVStatusChangeReasonRemoteStopped | 6 | Stops (or interrupts) the remote audio or video stream. |

## TRTCAudioQuality

#### Sound quality
TRTC provides three carefully calibrated modes to meet the diverse pursuit of audio quality in various vertical scenarios:
-  Vocal Mode (Speech): Suitable for applications focused on voice communication. This mode offers strong audio transmission resistance, ensuring optimal smoothness in weak network environments through various voice processing techniques.
-  Music Mode (Music): Suitable for scenarios with high vocal demands. This mode transmits a large amount of audio data, ensuring high-fidelity detail reproduction of music signals across all frequency bands through various technologies.
-  Default Mode (Default): Positioned between Speech and Music, this mode offers better music reproduction than the Vocal Mode but transmits much less data than the Music Mode. It is well-suited for various scenarios.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_AUDIO_QUALITY_SPEECH | 1 | Vocal Mode: Sampling Rate: 16k; Mono; Encoding Bitrate: 16kbps; Offers the strongest network resistance among the modes, suitable for voice-focused scenarios such as online meetings and voice calls. |
| TRTC_AUDIO_QUALITY_DEFAULT | 2 | Default Mode: Sampling Rate: 48k; Mono; Encoding Bitrate: 50kbps; A tier between Speech and Music, the default level of the SDK, recommended. |
| TRTC_AUDIO_QUALITY_MUSIC | 3 | Music Mode: Sampling Rate: 48k; Full-band Stereo; Encoding Bitrate: 128kbps; Suitable for scenarios requiring high-fidelity music transmission, such as online karaoke and music live streaming. |

## TRTCAudioFrameFormat

#### Content format of audio frame
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCAudioFrameFormatNone | 0 | None |
| TRTCAudioFrameFormatPCM | Not Defined | Audio data in PCM format. |

## TRTCAudioFrameOperationMode

#### Audio Callback Data Read/Write Mode
TRTC provides two operation modes for audio callback data.
-  Read-Write Mode: You can retrieve and modify the callback audio data. This is the default mode.
-  Read-Only Mode: You can only retrieve the audio data from the callback.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_AUDIO_FRAME_OPERATION_MODE_READWRITE | 0 | Read-Write Mode: You can retrieve and modify the callback audio data. |
| TRTC_AUDIO_FRAME_OPERATION_MODE_READONLY | 1 | Read-Only Mode: You can only retrieve the audio data from the callback. |

## TRTCLogLevel

#### Log Level
Different log levels define different levels of detail and log quantities. It is recommended to set the log level to TRTCLogLevelInfo under normal circumstances.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_LOG_LEVEL_VERBOSE | 0 | Output all levels of logs. |
| TRTC_LOG_LEVEL_DEBUG | 1 | Output DEBUG, INFO, WARNING, ERROR, and FATAL levels of logs. |
| TRTC_LOG_LEVEL_INFO | 2 | Output INFO, WARNING, ERROR, and FATAL levels of logs. |
| TRTC_LOG_LEVEL_WARN | 3 | Output WARNING, ERROR, and FATAL levels of logs. |
| TRTC_LOG_LEVEL_ERROR | 4 | Output ERROR and FATAL levels of logs. |
| TRTC_LOG_LEVEL_FATAL | 5 | Output only FATAL level logs. |
| TRTC_LOG_LEVEL_NULL | 6 | Do not output any SDK logs. |

## TRTCScreenCaptureSourceType
> **适用平台：** C++

#### Target Type for Screen Sharing (Only applicable to desktop devices)

## TRTCTranscodingConfigMode

#### Layout Mode for Cloud Mixed Streaming
TRTC's cloud mixed streaming service can combine multiple audio and video streams in a room into one stream. Therefore, you need to specify a layout plan for the screen. We offer the following layout modes.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCTranscodingConfigMode_Unknown | 0 | Undefined. |
| TRTCTranscodingConfigMode_Manual | 1 | Fully Manual Layout Mode. In this mode, you need to specify the exact layout position for each screen. This mode offers the highest degree of freedom but is the least user-friendly: -  You need to fill in all parameters in TRTCTranscodingConfig, including the position coordinates for each screen (TRTCMixUser). -  You need to monitor the onUserVideoAvailable() and onUserAudioAvailable() event callbacks in ITRTCCloudCallback, and continuously adjust the mixUsers parameters based on the audio and video status of users on each mic in the current room. |
| TRTCTranscodingConfigMode_Template_PureAudio | 2 | Pure Audio Mode. This mode is suitable for voice call (AudioCall) and voice chat room (VoiceChatRoom) audio-only applications. -  After entering the room, you only need to set up the mixing configuration once through the setMixTranscodingConfig interface. The SDK will then automatically mix the audio of all on-mic users in the room into the current user's live stream. -  You don't need to set the mixUsers parameters in TRTCTranscodingConfig. Just set the audioSampleRate, audioBitrate, and audioChannels parameters. |
| TRTCTranscodingConfigMode_Template_PresetLayout | 3 | Pre-layout Mode. The most popular layout mode because it supports pre-setting the positions of each screen using placeholders, and then the SDK will automatically adapt and adjust based on the number of screens in the room. In this mode, you still need to set the mixUsers parameters, but you can set the userId to "Placeholder". The available placeholders are: -  "$PLACE_HOLDER_REMOTE$"     :  Represents the remote user's screen. Multiple instances can be set. -  "$PLACE_HOLDER_LOCAL_MAIN$" : Represents the local camera screen. Only one instance is allowed. -  "$PLACE_HOLDER_LOCAL_SUB$"  :  Represents the local screen sharing. Only one instance is allowed. In this mode, you do not need to monitor the onUserVideoAvailable and onUserAudioAvailable callbacks in ITRTCCloudCallback for real-time adjustment. You just need to call setMixTranscodingConfig once after successfully entering the room; thereafter, the SDK will automatically fill the real userId into the placeholders you set. |
| TRTCTranscodingConfigMode_Template_ScreenSharing | 4 | Screen Sharing Mode. Suitable for online education scenarios and other screen sharing-centric applications, this mode is only supported on Windows and Mac SDKs. In this mode, the SDK will first build a canvas based on the target resolution you set via the videoWidth and videoHeight parameters, -  If the teacher does not activate the screen sharing, the SDK will proportionally stretch and draw the teacher's camera feed onto the canvas; -  When the teacher activates screen sharing, the SDK will draw the screen sharing feed onto the same canvas. The purpose of this layout is to ensure that the output resolution of the stream mixing module is consistent, avoiding screen tearing issues during course playback and web viewing (web players do not support variable resolutions). Additionally, the students' audio will be mixed into the teacher's audio and video stream by default. Since video content in teaching mode is primarily screen sharing, simultaneously transmitting the camera feed and the screen sharing feed is very bandwidth-intensive. The recommended approach is to directly render the camera feed onto the current screen via the setLocalVideoRenderCallback interface. In this mode, you don't need to set the mixUsers parameters in TRTCTranscodingConfig. The SDK will not mix the students' screens to prevent interference with the screen sharing effect. You can set the width x height in TRTCTranscodingConfig to 0px × 0px. The SDK will automatically calculate a suitable resolution based on the user's current screen aspect ratio -  If the teacher's current screen width is <= 1920px, the SDK will use the actual resolution of the teacher's current screen. -  If the teacher's current screen width is > 1920px, the SDK will choose one of the three resolutions based on the current screen aspect ratio: 1920x1080 (16:9), 1920x1200 (16:10), or 1920x1440 (4:3). |

## TRTCRecordType

#### Media Recording Type
This enumeration type is used for the local media recording interface startLocalRecording to specify whether to record audio and video files or pure audio files.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCLocalRecordType_Audio | 0 | Audio recording only. |
| TRTCLocalRecordType_Video | 1 | Video recording only. |
| TRTCLocalRecordType_Both | 2 | Record both audio and video. |

## TRTCMixInputType

#### Mixed Streaming Input Type
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_MixInputType_Undefined | 0 | Default value. Considering compatibility with older versions, if you specify the inputType as Undefined, the SDK will determine the mixed input type based on the value of another parameter, pureAudio. |
| TRTC_MixInputType_AudioVideo | 1 | Mix both audio and video. |
| TRTC_MixInputType_PureVideo | 2 | Mix video only. |
| TRTC_MixInputType_PureAudio | 3 | Mix audio only. |
| TRTC_MixInputType_Watermark | 4 | Mix watermarks. In this case, you do not need to specify the userId field but need to specify the image field. Using png format images is recommended. |

## TRTCWaterMarkSrcType
> **适用平台：** C++

#### Watermark image source type

## TRTCAudioRecordingContent

#### Audio Recording Content Type
This enumeration type is used for the audio recording interface startAudioRecording to specify the content of the audio to be recorded.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_AudioRecordingContent_All | 0 | Record all local and remote audio. |
| TRTC_AudioRecordingContent_Local | 1 | Record local audio only. |
| TRTC_AudioRecordingContent_Remote | 2 | Record remote audio only. |

## TRTCPublishMode

#### Media Stream Publication Mode
This enumeration type is used for the media stream publishing interface startPublishMediaStream. TRTC's media stream publishing service can mix multiple audio and video streams in the room into one stream and publish it to a CDN or push it back to the room. You can also publish your current audio and video stream to Tencent or a third-party CDN. Therefore, you need to specify the publishing mode of the corresponding media stream. We provide the following modes.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_PublishMode_Unknown | 0 | Undefined. |
| TRTC_PublishBigStream_ToCdn | 1 | You can set this parameter to publish the main stream of your room (TRTC_VIDEO_STREAM_TYPE_BIG) to Tencent or a third-party live streaming CDN service provider (only supports the standard RTMP protocol). |
| TRTC_PublishSubStream_ToCdn | 2 | You can set this parameter to publish the auxiliary stream of your room (TRTC_VIDEO_STREAM_TYPE_SUB) to Tencent or a third-party live streaming CDN service provider (only supports the standard RTMP protocol). |
| TRTC_PublishMixStream_ToCdn | 3 | You can set this parameter, together with encoding output parameters (TRTCStreamEncoderParam) and mix stream transcoding parameters (TRTCStreamMixingConfig), to transcode and publish your specified multiple audio and video streams to Tencent or third-party live streaming CDN services (only supports standard RTMP protocol). |
| TRTC_PublishMixStream_ToRoom | 4 | You can set this parameter, together with media stream encoding output parameters (TRTCStreamEncoderParam) and mix stream transcoding parameters (TRTCStreamMixingConfig), to transcode and publish your specified multiple audio and video streams into your specified room. -  Specify the robot information for the pushback room through TRTCPublishTarget TRTCUser. |

## TRTCEncryptionAlgorithm

#### Encryption Algorithm
This enumeration type is used for the selection of the private encryption algorithm for media streams.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_EncryptionAlgorithm_Aes_128_Gcm | 0 | AES GCM 128. |
| TRTC_EncryptionAlgorithm_Aes_256_Gcm | 1 | AES GCM 256. |

## TRTCSpeedTestScene

#### Speed Test Scenario
This enumeration type is used for selecting the speed test scenario.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_SpeedTestScene_Delay_Testing | 1 | Latency test. |
| TRTC_SpeedTestScene_Delay_Bandwidth_Testing | 2 | Latency and bandwidth test. |
| TRTC_SpeedTestScene_Online_Chorus_Testing | 3 | Online chorus test. |

## TRTCGravitySensorAdaptiveMode

#### Set Adaptation Mode for Gravity Sensor (Only applicable to mobile devices)
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_DISABLE | 0 | Turn off gravity sensing. Based on the current captured resolution and the set encoding resolution, if they are inconsistent, rotate by 90 degrees to ensure the maximum frame size. |
| TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_FILL_BY_CENTER_CROP | 1 | Enable gravity sensing. Always ensure the remote screen image is correct. When there is a resolution inconsistency, use the centered cropping mode. |
| TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_FIT_WITH_BLACK_BORDER | 2 | Enable gravity sensing. Always ensure the remote screen image is correct. When there is a resolution inconsistency, use the black border overlay mode. |

## TRTCParams

#### Room entry parameters
As an entry parameter for the TRTC SDK, only if this parameter is filled in correctly can you successfully enter the audio and video room specified by roomId or strRoomId.
Due to historical reasons, TRTC supports two types of room numbers, numeric and string, which are roomId and strRoomId respectively.
Please note: Do not mix roomId and strRoomId, as they are not interchangeable. For example, the number 123 and the string "123" are considered two completely different rooms in TRTC.

## TRTCVideoEncParam

#### Video encoding parameters
This setting determines the picture quality seen by remote users and also the picture quality of the video files recorded on the cloud.

## TRTCNetworkQosParam

#### Network flow control (Qos) parameter set
Network traffic control parameters, this setting determines the SDK's adjustment strategy in poor network conditions (for example: "Clarity Priority" or "Smoothness Priority").

## TRTCRenderParams

#### Rendering parameters for video footage
You can control the screen's rotation angle, fill mode, and left-right mirror mode by setting this parameter.

## TRTCQualityInfo

#### Network quality

Represents the quality of the network. This value can be used to display each user's network quality on the user interface.

## TRTCVolumeInfo

#### Volume level
Represents the evaluation value of voice volume. This value can be used to display each user's volume on the user interface.

## TRTCSpeedTestParams

#### Speed test parameters
You can test the network speed before the user enters the room through the startSpeedTest interface (Note: Do not call during a call).

## TRTCSpeedTestResult

#### Network speed test results
You can test the network speed before the user enters the room through the startSpeedTest interface (Note: Do not call during a call).

## TRTCTexture

#### Video texture data

## TRTCVideoFrame

#### Video frame information
TRTCVideoFrame describes the raw data of a video frame, which is the video frame data before encoding or after decoding.

## TRTCAudioFrame

#### Audio frame data

## TRTCMixUser

#### Description information of each stream in Cloud Mixed Streaming
TRTCMixUser is used to specify the position, size, layer, stream type, etc., of each video screen in Cloud Mixed Streaming.

## TRTCTranscodingConfig

#### Layout and transcoding parameters for Cloud Mixed Streaming
For specifying the layout position and encoding parameters of each screen during mix streaming and on-cloud transcoding.

## TRTCPublishCDNParam

#### Retweet parameters when publishing audio and video streams to non-Tencent Cloud CDN
The TRTC backend service supports publishing audio and video streams to third-party live streaming CDN service providers using the standard RTMP protocol.
If you are using Tencent CSS CDN Service, you can disregard this parameter and use the startPublish interface directly.

## TRTCAudioRecordingParams

#### Recording parameters for local audio files
This parameter is used to specify recording parameters in the audio recording interface startAudioRecording.

## TRTCLocalRecordingParams

#### Recording parameters for local media files
This parameter is used to specify recording-related parameters in the local media file recording interface startLocalRecording.
The interface startLocalRecording is an enhanced version of the interface startAudioRecording. The former can record video files, while the latter can only record audio files.

## TRTCSwitchRoomConfig

#### Room Switching Parameters
This parameter is used for the room switching interface switchRoom, allowing users to quickly switch from one room to another.

## TRTCAudioFrameDelegateFormat

#### Format parameters for audio custom callback

This parameter is used in the audio self Definition callback related interfaces to set the format of the audio data retrieved by the SDK callback (including sampling rate, number of channels, etc.).

## TRTCImageBuffer
> **适用平台：** C++

#### TRTC screen sharing icon information and mute image spacer
| Enumeration Types | Description |
| --- | --- |
| buffer | Contents of image storage, generally in BGRA structure. |
| height | Height of the image. |
| length | Size of the image data. |
| width | Width of the image. |

## TRTCScreenCaptureSourceInfo
> **适用平台：** C++

#### Target information for screen sharing (only applicable to desktop systems)

When users are sharing a screen, they can choose to capture the entire desktop or just a specific program window.

TRTCScreenCaptureSourceInfo describes the target information to be shared, including ID, name, thumbnail, etc. The field information in this structure is read-only.

## TRTCScreenCaptureProperty
> **适用平台：** C++

#### Advanced control parameters for screen sharing

This parameter is used for the screen sharing related interface selectScreenCaptureTarget, for setting a series of advanced control parameters when specifying the sharing target.

For example: whether to capture the mouse, whether to capture sub-windows, whether to draw a border around the shared target, etc.

## TRTCAudioParallelParams

#### Parameters for Intelligent Concurrent Playback Strategy of Remote Audio Stream
This parameter is used to set the intelligent concurrent playback strategy for remote audio streams.

## TRTCUser

#### User information on media stream publishing configuration
You can set this parameter, along with media stream target publishing parameters (TRTCPublishTarget) and stream mixing transcoding parameters (TRTCStreamMixingConfig), to transcode multiple audio and video streams you specify and publish them to the target publishing address you provide

## TRTCPublishCdnUrl

#### URL configuration for publishing audio and video streams to Tencent or third-party CDN
This configuration is for the target streaming configuration (TRTCPublishTarget) in the media stream publishing interface startPublishMediaStream

## TRTCPublishTarget

#### Target Push Configuration
This configuration is for the media stream publishing interface startPublishMediaStream.

## TRTCVideoLayout

#### Transcode Video Layout
This configuration is for the transcoding settings (TRTCStreamMixingConfig) in the media stream publishing interface (startPublishMediaStream).
For specifying the position, size, layer, and stream type of each video screen in the transcoded stream.

## TRTCWatermark

#### Watermark Layout
This configuration is used in the Transcoding Configuration (TRTCStreamMixingConfig) of the Media Stream Publishing Interface (startPublishMediaStream)

## TRTCStreamEncoderParam

#### Media Stream Encoding Output Parameters
[Field Meaning] This configuration is used in the Media Stream Publishing Interface (startPublishMediaStream).
[Special Note] When the mode configuration in your publishing target (TRTCPublishTarget) is set to TRTCPublish_MixStream_ToCdn or TRTCPublish_MixStream_ToRoom, this parameter is required.
[Special Explanation] When you use the retweet service (mode is TRTCPublish_BigStream_ToCdn or TRTCPublish_SubStream_ToCdn), to ensure better stability and CDN play compatibility, it is also recommended that you fill in the specific parameters of this configuration.

## TRTCStreamMixingConfig

#### Media Stream Transcoding Configuration Parameters
This configuration is used for the media stream publish interface (startPublishMediaStream).
For specifying the layout position information of each screen and the input audio information during transcoding.

## TRTCPayloadPrivateEncryptionConfig

#### Media Stream Private Encryption Configuration
This configuration is used to set the algorithm and key for private media stream encryption.

## TRTCAudioVolumeEvaluateParams

#### Volume Assessment and related parameter settings

This setting is used to enable voice detection and sound spectrum calculation.

## TRTCAudioSampleRate
> **适用平台：** Android

#### Audio sample rate

The audio sampling rate is used to measure sound fidelity. The higher the sampling rate, the better the fidelity. If your application scenario involves music, it is recommended to use TRTCAudioSampleRate48000.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCAudioSampleRate16000 | 16000 | 16k Sampling Rate. |
| TRTCAudioSampleRate32000 | 32000 | 32k Sampling Rate. |
| TRTCAudioSampleRate44100 | 44100 | 44.1k Sampling Rate. |
| TRTCAudioSampleRate48000 | 48000 | 48k Sampling Rate |

## TRTCAudioRoute
> **适用平台：** Android

#### Audio routing (i.e., the sound playback mode)

Audio routing, that is, whether the sound is played through the phone's speaker or earpiece. Therefore, this interface is only applicable to mobile devices.

A mobile phone has two speakers: one is the earpiece located at the top of the phone, and the other is the stereo speaker located at the bottom.
-  When the audio routing is set to the earpiece, the sound is relatively low, and you need to bring your ear close to hear it clearly, ensuring better privacy. It is suitable for phone calls.

-  When the audio routing is set to the speaker, the sound is relatively loud. You can hear it clearly without holding the phone to your face, thus enabling the "hands-free" feature.

-  Audio routing to wired headphones.

-  Audio routing to Bluetooth headphones.

-  Audio routing to USB professional sound card device.

| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_AUDIO_ROUTE_UNKNOWN | -1 | Unknown: The default routing device. |
| TRTC_AUDIO_ROUTE_SPEAKER | 0 | Speakerphone: Play sound through the speaker (i.e., "hands-free"), which is located at the bottom of the phone. The volume tends to be loud, suitable for playing music out loud. |
| TRTC_AUDIO_ROUTE_EARPIECE | 1 | Earpiece: Plays through the earpiece, the earpiece is located at the top of the phone, the sound is small, suitable for privacy-protected call scenarios. |
| TRTC_AUDIO_ROUTE_WIRED_HEADSET | 2 | WiredHeadset: Plays through wired headphones. |
| TRTC_AUDIO_ROUTE_BLUETOOTH_HEADSET | 3 | BluetoothHeadset: Plays through Bluetooth headphones. |
| TRTC_AUDIO_ROUTE_SOUND_CARD | 4 | SoundCard: Plays through USB sound card. |

## TRTCReverbType
> **适用平台：** Android

#### Sound Reverb Mode

This enumeration value is used to set the reverb mode in live streaming scenarios and is commonly used in live shows.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_REVERB_TYPE_0 | 0 | Disable Reverb. |
| TRTC_REVERB_TYPE_1 | 1 | KTV. |
| TRTC_REVERB_TYPE_2 | 2 | Small room. |
| TRTC_REVERB_TYPE_3 | 3 | Great Hall. |
| TRTC_REVERB_TYPE_4 | 4 | Deep. |
| TRTC_REVERB_TYPE_5 | 5 | Loud and Clear. |
| TRTC_REVERB_TYPE_6 | 6 | Metallic Sound. |
| TRTC_REVERB_TYPE_7 | 7 | Magnetic. |

## TRTCVoiceChangerType
> **适用平台：** Android

#### Voice Changing Type

This enumeration value is used to set the voice change mode in live streaming scenarios and is commonly used in live shows.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_VOICE_CHANGER_TYPE_0 | 0 | Disable Voice Change. |
| TRTC_VOICE_CHANGER_TYPE_1 | 1 | Mischievous Child. |
| TRTC_VOICE_CHANGER_TYPE_2 | 2 | Lolita. |
| TRTC_VOICE_CHANGER_TYPE_3 | 3 | Uncle. |
| TRTC_VOICE_CHANGER_TYPE_4 | 4 | Heavy Metal. |
| TRTC_VOICE_CHANGER_TYPE_5 | 5 | Cold. |
| TRTC_VOICE_CHANGER_TYPE_6 | 6 | Foreigner. |
| TRTC_VOICE_CHANGER_TYPE_7 | 7 | Trapped Beast. |
| TRTC_VOICE_CHANGER_TYPE_8 | 8 | Fat Kid. |
| TRTC_VOICE_CHANGER_TYPE_9 | 9 | Strong electric current. |
| TRTC_VOICE_CHANGER_TYPE_10 | 10 | Heavy machinery. |
| TRTC_VOICE_CHANGER_TYPE_11 | 11 | Ethereal voice. |

## TRTCSystemVolumeType
> **适用平台：** Android

#### System Volume Type (Only applicable to mobile devices)

Modern smartphones generally have two types of system volume: "Call Volume" and "Media Volume".
-  Call Volume: This type of volume is specifically designed for making and receiving calls. It includes a built-in Echo Cancellation (AEC) feature and supports audio pickup through the microphone of a Bluetooth headset. However, the sound quality is generally average. If you lower the phone volume using the side volume buttons and cannot turn it to zero (i.e., mute it completely), it indicates that your phone is currently in call volume mode.

-  Media Volume: This type of volume is specifically designed for music scenarios. It does not support the system's AEC feature and cannot use the Bluetooth headset's microphone for audio pickup, but it provides better music playback quality. If you lower the phone volume using the side volume buttons and can turn it to silence completely, it indicates that your phone is currently in media volume mode.

The SDK currently offers three control modes for system volume types: Automatic Switching Mode, Full Call Volume Mode, Full Media Volume Mode.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCSystemVolumeTypeAuto | 0 | Automatic Switching Mode: Also known as "Call Volume during Mic On, Media Volume during Mic Off". It uses call volume when the host is on the mic and media volume when the audience is off the mic, suitable for online live streaming scenarios. If you choose TRTC_APP_SCENE_LIVE or TRTC_APP_SCENE_VOICE_CHATROOM when entering the room, the SDK will automatically use this mode. |
| TRTCSystemVolumeTypeMedia | 1 | Full Media Volume: Using media volume throughout the call is not a commonly used volume type; it is suitable for music scenarios with stringent sound quality requirements. If most of your users use external devices (such as external sound cards), you can use this mode; otherwise, please use it cautiously. |
| TRTCSystemVolumeTypeVOIP | 2 | Full call volume: The advantage of this solution is that the audio module does not need to switch operation modes when users join/leave the mic, achieving seamless mic transitions. It is suitable for applications where users need to join/leave the mic frequently. If you choose TRTCAppSceneVideoCall or TRTCAppSceneAudioCall when entering the room, the SDK will automatically use this mode. |

## TRTCAudioCapabilityType
> **适用平台：** Android

#### System-supported audio capability types (Android devices only)

The SDK currently provides two system audio capability types queries: Low-Latency Chorus Capability, Low-Latency Ear Return Capability.
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTCAudioCapabilityLowLatencyChorus | 1 | Low-Latency Chorus Capability. |
| TRTCAudioCapabilityLowLatencyEarMonitor | 2 | Low latency ear return capability. |

## TRTCGSensorMode
> **适用平台：** Android

#### Gravity Sensor Switch (Only applicable to mobile devices)
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_GSENSOR_MODE_DISABLE | 0 | No gravity sensing adaptation. This mode is the default on the Desktop Platform. In this mode, the video published by the current user is not affected by changes in gravity sensing direction. |
| TRTC_GSENSOR_MODE_UIAUTOLAYOUT | 1 | Adapt gravity sensing. This mode is the default on the Mobile Platform. In this mode, the video published by the current user will adjust according to the gravity sensing direction of the device, while the local preview screen remains unchanged. One adaptation mode currently supported by the SDK is: when the phone or Pad is upside down, to ensure that the remote user sees the correct direction, the SDK will automatically rotate the published screen 180 degrees. If your app's interface layer has gravity sensing auto-adaptation enabled, it is recommended to use UIFixLayout mode. |
| TRTC_GSENSOR_MODE_UIFIXLAYOUT | 2 | Adapt gravity sensing In this mode, the video published by the current user will adjust according to the gravity sensing direction of the device, and the local preview screen will also rotate accordingly. One feature currently supported is that when the phone or Pad is upside down, to ensure that the remote user sees the correct direction, the SDK will automatically rotate the published screen 180 degrees. If your app's interface layer does not support gravity sensing auto-adaptation and you want the SDK's video to adapt to gravity sensing direction, it is recommended to use UIFixLayout mode. @deprecated From version v11.5, TRTCGSensorMode_UIFixLayout is no longer supported, only the above two modes are supported. |

## TRTCDebugViewLevel
> **适用平台：** Android

#### Debug information displayed on rendering control
| Enumeration | Value | Description |
| --- | --- | --- |
| TRTC_DEBUG_VIEW_LEVEL_GONE | 0 | Do not display debug information on the rendering control. |
| TRTC_DEBUG_VIEW_LEVEL_STATUS | 1 | Display audio and video statistical information on the rendering control. |
| TRTC_DEBUG_VIEW_LEVEL_ALL | 2 | Display audio and video statistical information and key historical events on the rendering control. |

## TRTCScreenShareParams
> **适用平台：** Android

#### Screen sharing parameters (Android platform only)

This parameter is used to specify information such as the floating window during screen sharing in the startScreenCapture API.
