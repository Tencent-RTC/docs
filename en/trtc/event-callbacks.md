> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TRTCCloudDelegate 事件回调 文档

## 概述
本文档是 实时音视频(TRTC SDK) TRTCCloudDelegate 事件回调 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TRTCCloudCallback
## ITRTCVideoFrameCallback
> **适用平台：** C++

| Function List | Description |
| --- | --- |
| onGLContextCreated | Notification that the SDK's internal OpenGL environment has been created |
| onProcessVideoFrame | Video processing callback for integrating third-party beauty components |
| onGLContextDestroy | Notification that the SDK's internal OpenGL environment has been destroyed |

## ITRTCAudioFrameCallback
> **适用平台：** C++

| Function List | Description |
| --- | --- |
| onCapturedAudioFrame | Local audio data callback after preprocessing by the audio module |
| onLocalProcessedAudioFrame | Local audio data callback after preprocessing, sound-effect processing, and mixing BGM by the audio module |
| onPlayAudioFrame | Audio data of each remote user before mixing |
| onMixedPlayAudioFrame | Callback of mixed audio data before final submission to the system for playback |
| onMixedAllAudioFrame | Audio data mixed by the SDK (including collected and to-be-played audio) |

## ITRTCLogCallback
> **适用平台：** C++

| Function List | Description |
| --- | --- |
| onLog | Local LOG printing callback |

## onError

#### Error event callback

Error events indicate unrecoverable errors thrown by the SDK, such as room entry failure or device startup failure.

Reference document: [Error Code Table](https://cloud.tencent.com/document/product/647/38308)
| Parameters | Description |
| --- | --- |
| errCode | Error Code |
| errMsg | Error Message |
| extInfo | Expand Information Field, some error codes may carry additional information to help pinpoint issues |

## onWarning

#### Warning event callback

Warning events indicate prompt issues thrown by the SDK, such as video stuttering or high CPU utilization.

Reference document: [Error Code Table](https://cloud.tencent.com/document/product/647/38308)
| Parameters | Description |
| --- | --- |
| extInfo | Expand Information Field, some warning codes may carry additional information to help pinpoint issues |
| warningCode | Warning Codes |
| warningMsg | Warning Information |

## onEnterRoom

#### Event callback for room entry success or failure
After calling the enterRoom() interface in TRTCCloud to perform the room entry operation, you will receive the onEnterRoom(result) callback from ITRTCCloudCallback:
-  If the entry is successful, the result will be a positive number (result > 0), representing the time taken to enter the room, in milliseconds (ms).
-  If the entry fails, the result will be a negative number (result < 0), representing the error code of the failure reason.
For the meaning of error codes of room entry failure, please see [Error Code Table](https://cloud.tencent.com/document/product/647/38308).
| Parameters | Description |
| --- | --- |
| result | When result > 0, it means the time taken to enter the room (ms). When result < 0, it means the error code for entering the room. |

> **注意**
> **Note**
> 1. In versions before Ver6.6, only successful room entry would throw the onEnterRoom(result) callback; failure would be thrown by the onError() callback.
> 2. In versions after Ver6.6: regardless of success or failure, the onEnterRoom(result) callback will be thrown, and room entry failure will also throw the onError() callback.

## onExitRoom

#### Event callback for leaving the room

Calling the exitRoom interface in TRTCCloud will execute the related logic of leaving the room, such as releasing audio/video device resources and codec resources.

After all resources occupied by the SDK are released, the SDK will trigger the onExitRoom() callback to notify you.

If you want to call enterRoom again or switch to another audio and video SDK, please wait for the onExitRoom callback before performing related operations. Otherwise, you may encounter various abnormal issues, such as the camera and microphone equipment being forcibly occupied.
| Parameters | Description |
| --- | --- |
| reason | Reasons for leaving the room: 0: Actively call exitRoom to leave the room; 1: Kicked out of the current room by the server; 2: The entire current room is dissolved. |

## onSwitchRole

#### Event callback for switching roles

Calling the switchRole() interface in TRTCCloud will switch the roles of anchor and audience. This operation will involve a circuit switching process, and once the SDK completes the switch, it will trigger the onSwitchRole() event callback.
| Parameters | Description |
| --- | --- |
| errCode | Error Code, ERR_NULL represents successful switching. For others, please refer to [ Error Code ](https://cloud.tencent.com/document/product/647/32257). |
| errMsg | Error message. |

## onSwitchRoom

#### Event callback for switching rooms result

Calling the switchRoom() interface in TRTCCloud allows the user to quickly switch from one room to another. After the SDK completes the switch, it will trigger the onSwitchRoom() event callback.
| Parameters | Description |
| --- | --- |
| errCode | Error Code, ERR_NULL represents successful switching. For others, please refer to [ Error Code ](https://cloud.tencent.com/document/product/647/32257). |
| errMsg | Error message. |

## onConnectOtherRoom

#### Event callback for cross-room communication request result

Calling the connectOtherRoom() interface in TRTCCloud will initiate a video call between broadcasters in two different rooms, which is the so-called “Host PK” feature.

The caller will receive the onConnectOtherRoom() callback to learn whether the cross-room communication is successful

If successful, all users in both rooms will receive the onUserVideoAvailable() callback from the PK anchor in the other room.
| Parameters | Description |
| --- | --- |
| errCode | Error Code, ERR_NULL represents successful switching. For others, please refer to [ Error Code ](https://cloud.tencent.com/document/product/647/32257). |
| errMsg | Error message. |
| userId | User ID of the anchor in the other room for cross-room communication. |

## onDisconnectOtherRoom
> **适用平台：** iOS, C++

#### Event callback for ending cross-room communication result

## onUpdateOtherRoomForwardMode

#### Event callback for modifying cross-room broadcaster uplink capability result

## onRemoteUserEnterRoom

#### A user has joined the current room
For performance considerations, the behavior of this notification differs in two different TRTC application scenarios (i.e., AppScene, specified by the second parameter when calling enterRoom):
-  Live streaming scenarios (TRTC_APP_SCENE_LIVE and TRTC_APP_SCENE_VOICE_CHATROOM): In this scenario, users are distinguished between hosts and audience. This notification is only triggered when a host enters the room. Audience members entering the room will not trigger this notification.
-  Call scenarios (TRTC_APP_SCENE_VIDEOCALL and TRTC_APP_SCENE_AUDIOCALL): In this scenario, users are not distinguished by roles (all users can be considered as hosts). Any user entering the room will trigger this notification.
| Parameters | Description |
| --- | --- |
| userId | User Identifier of the remote user. |
> **注意**
> **Note**
> 1. The event callbacks onRemoteUserEnterRoom and onRemoteUserLeaveRoom are only applicable for maintaining the "user list" in the current room. The presence of these callbacks does not necessarily imply video streams.
> 2. To display the remote screen, please listen for the onUserVideoAvailable() event callback, which indicates whether a specific user has video footage.

## onRemoteUserLeaveRoom

#### A user has left the current room
Corresponding to onRemoteUserEnterRoom, the behavior of this notification differs in two different application scenarios (i.e., AppScene, specified by the second parameter when calling enterRoom):
-  In live streaming scenarios (TRTC_APP_SCENE_LIVE and TRTC_APP_SCENE_VOICE_CHATROOM): This notification is triggered only when a host leaves the room. Audience members leaving the room will not trigger this notification.
-  In call scenarios (TRTC_APP_SCENE_VIDEOCALL and TRTC_APP_SCENE_AUDIOCALL): In this scenario, users are not distinguished by roles. Any user leaving the room will trigger this notification.
| Parameters | Description |
| --- | --- |
| reason | Reason for leaving: 0 indicates the user left the room voluntarily, 1 indicates a timeout exit, 2 indicates the user was kicked out of the room, and 3 indicates the anchor switched to audience mode and left the room. |
| userId | User Identifier of the remote user. |

## onUserVideoAvailable

#### A remote user has started/stopped publishing the main video stream
**The primary video** is generally used to carry the camera's video feed. When you receive the onUserVideoAvailable(userId, true) notification, it indicates that video frames are available for playback on this channel.
At this point, you need to call the startRemoteView interface to subscribe to the user's remote screen. Upon successful subscription, you will continue to receive the user's first frame rendering callback onFirstVideoFrame(userId).
When you receive the onUserVideoAvailable(userId, false) notification, it means that the remote video on this channel has been turned off. The shutdown may be due to the user invoking muteLocalVideo or stopLocalPreview.
| Parameters | Description |
| --- | --- |
| available | Whether the user has published (or cancelled publishing) the Main Road Picture, true: published; false: cancelled. |
| userId | User Identifier of the remote user. |

## onUserSubStreamAvailable

#### A remote user has started/stopped publishing the auxiliary video stream
"Auxiliary Road Picture" is generally used to carry the screen sharing content. When you receive the onUserSubStreamAvailable(userId, true) notification, it indicates that video frames are available for playback on this channel.
At this point, you need to call the startRemoteView interface to subscribe to the user's remote screen. Upon successful subscription, you will continue to receive the user's first frame rendering callback onFirstVideoFrame(userId).
| Parameters | Description |
| --- | --- |
| available | Whether the user has published (or cancelled publishing) the auxiliary video content, true: published; false: cancelled. |
| userId | User Identifier of the remote user. |
> **注意**
> **Note**
> The function used to display the auxiliary road picture is startRemoteView, not startRemoteSubStreamView. The latter has been deprecated.

## onUserAudioAvailable

#### A remote user has started/stopped publishing their audio
When you receive the onUserAudioAvailable(userId, true) notification, it means the user has published their audio. At this time, the SDK behaves as follows:
-  In Automatic Subscription Mode, you do not need to take any action; the SDK will automatically play the user's audio.
-  In manual subscription mode, you can use muteRemoteAudio(userid, false) to play the user's audio.
| Parameters | Description |
| --- | --- |
| available | Whether the user has published (or cancelled publishing) their audio, true: published; false: cancelled. |
| userId | User Identifier of the remote user. |
> **注意**
> **Note**
> The SDK uses automatic subscription mode by default. You can set it to manual subscription by calling setDefaultStreamRecvMode, but this must be done before you enter the room.

## onFirstVideoFrame

#### The SDK has started rendering the first frame of the local or remote user's video
The SDK triggers this event when rendering the first video frame of the local or remote user. You can use the userId parameter in the callback event to determine whether the event is from "Local" or "Remote".
-  If userId is empty, it means the SDK has started rendering your local video footage, but you must have called startLocalPreview or startScreenCapture.
-  If userId is not empty, it means the SDK has started rendering the remote user's video footage. However, you must have called startRemoteView to subscribe to the user's video footage.
| Parameters | Description |
| --- | --- |
| height | Height of the video footage. |
| streamType | Video stream type: Main is generally used for carrying camera footage, while Sub is generally used for carrying screen sharing footage. |
| userId | The user identifier of local or remote. If userId is empty, it means your local first video frame has arrived. If userId is not empty, it means the remote user's first video frame has arrived. |
| width | Width of the video footage. |
> **注意**
> **Note**
> 1. The local first video frame event callback is triggered only after you call startLocalPreview or startScreenCapture.
> 2. The remote user's first video frame event callback is triggered only after you call startRemoteView or startRemoteSubStreamView.

## onFirstAudioFrame

#### The SDK has started playing the remote user's first audio frame

The SDK will throw this event when playing the remote user's first frame of audio. The first frame event of local audio is not thrown for now.
| Parameters | Description |
| --- | --- |
| userId | User Identifier of the remote user. |

## onSendFirstLocalVideoFrame

#### The first local video frame has been published
After successfully joining the room and starting local video capture via startLocalPreview or startScreenCapture (the order of starting capture and joining the room does not matter), the SDK will start video encoding and publish the local video data to the cloud using its network module. Once the SDK successfully sends the first frame of local video data to the cloud, the onSendFirstLocalVideoFrame event callback will be triggered.
| Parameters | Description |
| --- | --- |
| streamType | Video stream type: Main is generally used for carrying camera footage, while Sub is generally used for carrying screen sharing footage. |

## onSendFirstLocalAudioFrame

#### The first local audio frame has been published
After successfully joining the room and starting local audio capture via startLocalAudio (the order of starting capture and joining the room does not matter), the SDK will start audio encoding and publish the local audio data to the cloud using its network module. Once the SDK successfully sends the first frame of local audio data to the cloud, the onSendFirstLocalAudioFrame event callback will be triggered.

## onRemoteVideoStatusUpdated

#### Event callback for remote video status changes

You can use this event callback to obtain the playback status of each remote video stream (including three states: Playing, Loading, and Stopped) for corresponding UI display.
| Parameters | Description |
| --- | --- |
| extrainfo | Additional Information. |
| reason | Reasons for video status changes. |
| status | Video Status: Including Playing, Loading, and Stopped states. |
| streamType | Video stream type: Main is generally used for carrying camera footage, while Sub is generally used for carrying screen sharing footage. |
| userId | User identifier. |

## onRemoteAudioStatusUpdated

#### Event callback for remote audio status changes

You can use this event callback to obtain the playback status of each remote audio stream (including three states: Playing, Loading, and Stopped) for corresponding UI display.
| Parameters | Description |
| --- | --- |
| extrainfo | Additional Information. |
| reason | Reasons for audio status changes. |
| status | Audio Status: Including Playing, Loading, and Stopped states. |
| userId | User identifier. |

## onUserVideoSizeChanged

#### Callback for changes in user video size

When you receive the onUserVideoSizeChanged(userId, streamtype, newWidth, newHeight) notification, it indicates that the video size has changed, possibly because the user has called setVideoEncoderParam or setSubStreamEncoderParam to reset the video dimensions.
| Parameters | Description |
| --- | --- |
| newHeight | The height of the video stream (in pixels). |
| newWidth | The width of the video stream (in pixels). |
| streamType | Video stream type: Main is generally used for carrying camera footage, while Sub is generally used for carrying screen sharing footage. |
| userId | User identifier. |

## onNetworkQuality

#### Real-time statistics callback for network quality

This statistical callback is thrown every 2 seconds to notify the SDK of the perceived current network's uplink and downlink quality.

The SDK uses a set of proprietary algorithms to assess the current network's latency, bandwidth, and stability, and calculates an evaluation result: An evaluation result of 1 (Excellent) indicates that the current network condition is very good, while a result of 6 (Down) indicates that the current network cannot support normal calls on TRTC.
| Parameters | Description |
| --- | --- |
| localQuality | Uplink network quality. |
| remoteQuality | Downlink network quality, represents the data quality finally gathered at the client-end after the data stream travels through a complete transmission link from "remote-end -> cloud -> client-end". Thus, the downlink network quality represents the result influenced by both the remote uplink and the client downlink. |

> **Note**
> It is temporarily impossible to assess the remote user's uplink quality through this interface alone.
## onStatistics

#### Real-time statistics callback for audio and video technical indicators
This statistical callback is thrown every 2 seconds, notifying the SDK internal audio, video, and network-related technical indicators. This information is listed in TRTCStatistics:
-  Video statistical information: Video resolution, frame rate (FPS), and bitrate information.
-  Audio statistical information: Audio sample rate, channel, and bitrate information.
-  Network Statistics: Network Latency (RTT), Packet Loss Rate (loss), Upstream Traffic (sentBytes), Downstream Traffic (receivedBytes), and other information from a round-trip (SDK > Cloud > SDK).
| Parameters | Description |
| --- | --- |
| statistics | Statistics including local and remote user statistics, for details refer to TRTCStatistics. |

> **注意**
> **Note**
> If you just need to know the current network quality and do not want to spend too much time studying this statistical callback, it is recommended to use onNetworkQuality to solve the problem.

## onSpeedTestResult

#### Callback for internet speed test results
This statistical callback is triggered by startSpeedTest:.
| Parameters | Description |
| --- | --- |
| result | Internet Speed Test results, including packet loss, round-trip delay, upstream and downstream bandwidth rates; for details, refer to TRTCSpeedTestResult. |

## onConnectionLost

#### The connection between the SDK and the cloud has been disconnected.
The SDK will throw this event callback when the connection to the cloud is disconnected. The main reasons for disconnection are network unavailability or network switching, such as when the user enters an elevator during a call.
After throwing this event, the SDK will attempt to re-establish the connection with the cloud. During the reconnection process, it will throw onTryToReconnect, and once the connection is restored, it will throw onConnectionRecovery.
Thus, the SDK will switch among the following three connection-related events in the following manner:

## onTryToReconnect

#### The SDK is trying to reconnect to the cloud.
The SDK will throw onConnectionLost when the connection to the cloud is disconnected, then it will attempt to re-establish the connection with the cloud and throw this event. Once the connection is restored, it will throw onConnectionRecovery.

## onConnectionRecovery

#### The connection between the SDK and the cloud has been restored.
The SDK will throw onConnectionLost when the connection to the cloud is disconnected. It will then attempt to re-establish the connection and throw onTryToReconnect. Once the connection is restored, this event callback will be triggered.

## onCameraDidReady

#### Camera is ready
When you call startLocalPreivew, the SDK will attempt to enable the camera. If successful, this event will be thrown.
If enabling the camera fails, it is most likely because the current application does not have access permissions to the camera or the camera is currently being used exclusively by another application.
You can capture the onError event callback to understand these exceptional conditions and prompt users through the UI interface.

## onMicDidReady

#### Microphone is ready
When you call startLocalAudio, the SDK will attempt to enable the microphone. If successful, this event will be thrown.
If enabling the microphone fails, it is most likely because the current application does not have access permissions to the microphone or the microphone is currently being used exclusively by another application.
You can capture the onError event callback to understand these exceptional conditions and prompt users through the UI interface.

## onUserVoiceVolume

#### Callback for volume feedback
The SDK can assess the volume of each audio stream and periodically trigger this event callback. You can provide corresponding UI indications based on the volume, such as `waveform chart` or `volume slider`.
To implement this feature, you need to call enableAudioVolumeEvaluation to enable this capability and set the time interval for event triggers.
It is important to note that, regardless of whether anyone is speaking in the current room, the SDK will periodically trigger this event callback at the interval you set.
| Parameters | Description |
| --- | --- |
| totalVolume | The total volume level of all remote users, with a range of 0 to 100. |
| userVolumes | It is an array that holds the volume levels of all speaking users, with a range of 0 to 100. |
> **注意**
> **Note**
> userVolumes is an array. For each element in the array, if the userId is empty, it represents the volume level collected by the local microphone. If the userId is not empty, it represents the volume level of remote users.

## onDeviceChange
> **适用平台：** C++

#### Local device connection status has changed (applicable only to desktop systems)

When local devices (including camera, microphone, and speaker) are plugged in or unplugged, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| deviceId | Device ID. |
| deviceType | Device Type. 0: Microphone device; 1: Speaker device; 2: Camera device. |
| state | On/Off status, 0: Device added; 1: Device removed; 2: Device enabled. |

## onAudioDeviceCaptureVolumeChanged
> **适用平台：** iOS, C++

#### Current system mic capture volume has changed
On desktop operating systems like Mac or Windows, users can find the sound settings panel in the settings center and set the microphone's capture volume level.
The larger the microphone capture volume set by the user, the larger the original volume of the sound captured by the microphone will be; conversely, the smaller it will be.
On some models of keyboards and laptops, users can also mute the microphone by pressing the `Disable Microphone` button (the icon is a `Microphone` with a slash representing disable).
When the user sets the microphone capture volume of the operating system through the system settings interface or shortcut keys on the keyboard, the SDK will trigger this event.
| Parameters | Description |
| --- | --- |
| muted | Is the microphone disabled by the user: true for disabled, false for enabled. |
| volume | System capture volume, range 0 - 100, can be adjusted by dragging on the sound settings panel of the system. |
> **注意**
> **Note**
> You need to call the enableAudioVolumeEvaluation interface and set the interval greater than 0 to enable this event callback, set the interval to 0 to disable this event callback.

## onAudioDevicePlayoutVolumeChanged
> **适用平台：** iOS, C++

#### Current system playback volume has changed
On desktop operating systems like Mac or Windows, users can find the sound settings panel in the settings center and set the system playback volume.
On some models of keyboards and laptops, users can also mute the system by pressing the "Mute" button (the icon is a speaker with a slash representing disable).
When the user sets the system playback volume through the system settings interface or shortcut keys on the keyboard, the SDK will trigger this event.
| Parameters | Description |
| --- | --- |
| muted | Is the system muted by the user: true for muted, false for restored. |
| volume | System playback volume, range 0 - 100. Users can adjust it by dragging on the system's sound settings panel. |
> **注意**
> **Note**
> You need to call the enableAudioVolumeEvaluation interface and set the interval greater than 0 to enable this event callback, set the interval to 0 to disable this event callback.

## onSystemAudioLoopbackError
> **适用平台：** iOS, C++

#### Event callback for successfully turning on system sound acquisition (applicable only to desktop systems)
On macOS, you can call startSystemAudioLoopback to install an audio driver on the current system, allowing the SDK to capture the sound played by the macOS system.
On Windows, you can call startSystemAudioLoopback to let the SDK capture the sound played by the Windows system.
When used for film teaching or music live streaming, the teacher can use this feature to allow the SDK to capture the sound played in the movie, so that students in the same room can hear the movie's sound.
The SDK will throw an event callback with the result of whether the system sound capture was successfully enabled, and you need to pay attention to the error code in the parameters.
| Parameters | Description |
| --- | --- |
| err | ERR_NULL indicates success, any other value indicates failure. |

## onTestMicVolume
> **适用平台：** C++

#### Volume callback while testing the microphone

When you call startMicDeviceTest to test if the microphone is working properly, the SDK will continuously throw this callback. The parameter volume represents the volume level currently captured by the microphone.

If there are fluctuations in volume during the test, it indicates that the microphone is in good condition; if the volume value remains at 0, it indicates that the microphone is abnormal and the user should be prompted to replace it.
| Parameters | Description |
| --- | --- |
| volume | The volume value captured by the microphone, range 0 - 100. |

## onTestSpeakerVolume
> **适用平台：** C++

#### Volume callback while testing the speaker

When you call startSpeakerDeviceTest to test if the speaker is working properly, the SDK will continuously throw this callback.

The parameter volume represents the volume level of the sound the SDK submits to the system speaker for playback. If this value continues to change but the user reports hearing no sound, it indicates a problem with the speaker.
| Parameters | Description |
| --- | --- |
| volume | The volume of the sound the SDK submits to the speaker for playback, range 0 - 100. |

## onRecvCustomCmdMsg
> **适用平台：** Android, C++

#### Receipt of a custom Definition message
When a user in a room uses sendCustomCmdMsg to send a Definition UDP Message, other users in the room can receive the message through the onRecvCustomCmdMsg event callback.
| Parameters | Description |
| --- | --- |
| cmdID | Command ID. |
| message | Message Data. |
| seq | Message serial number. |
| userId | User identifier. |

## onMissCustomCmdMsg
> **适用平台：** Android, C++

#### Event callback for lost custom Definition messages
When you use sendCustomCmdMsg to send a Definition UDP message, even if reliable transmission is set, it cannot guarantee 100% message delivery, but the probability of message loss is very low and meets conventional reliability requirements.
After setting reliable transmission on the sender side, the SDK will notify the number of lost Definition messages during the past time period (usually 5 seconds) through this callback.
| Parameters | Description |
| --- | --- |
| cmdID | Command ID. |
| errCode | Error code. |
| missed | Number of lost messages. |
| userId | User identifier. |
> **注意**
> **Note**
> Only if reliable transmission is set on the sender side can the receiver receive the message loss callback.

## onRecvSEIMsg

#### Callback for receiving SEI message
When a user in the room sends an SEI message through the video frame using sendSEIMsg, other users in the room can receive the message via the onRecvSEIMsg event callback.
| Parameters | Description |
| --- | --- |
| message | Data. |
| userId | User identifier. |

## onStartPublishing

#### Event callback for starting to publish audio and video streams to Tencent CSS CDN

When you call startPublishing to start publishing audio and video streams to the Tencent CSS CDN, the SDK will immediately synchronize this command to the cloud server.

The SDK will then receive the processing results from the cloud server and notify you of the execution results of the command through this event callback.
| Parameters | Description |
| --- | --- |
| err | 0 indicates success, other values indicate failure. |
| errMsg | Specific error reasons. |

## onStopPublishing

#### Event callback for stopping the publishing of audio and video streams to Tencent CSS CDN

When you call stopPublishing to stop publishing audio and video streams to the Tencent CSS CDN, the SDK will immediately synchronize this command to the cloud server.

The SDK will then receive the processing results from the cloud server and notify you of the execution results of the command through this event callback.
| Parameters | Description |
| --- | --- |
| err | 0 indicates success, other values indicate failure. |
| errMsg | Specific error reasons. |

## onStartPublishCDNStream

#### Event callback for starting to publish audio and video streams to a non-Tencent Cloud CDN

When you call startPublishCDNStream to start publishing audio and video streams to a non-Tencent CSS CDN, the SDK will immediately synchronize this command to the cloud server. The SDK will then receive the processing results from the cloud server and notify you of the execution results of the command through this event callback.
| Parameters | Description |
| --- | --- |
| err | 0 indicates success, other values indicate failure. |
| errMsg | Specific error reasons. |

> **Note**
> When you receive a successful event callback, it only indicates that your publishing command has been synchronized to the Tencent Cloud backend server, but if the target CDN provider's server does not accept the video stream, it may still result in a publishing failure.
## onStopPublishCDNStream

#### Event callback for stopping the publishing of audio and video streams to a non-Tencent Cloud CDN

When you call stopPublishCDNStream to stop publishing audio and video streams to a non-Tencent CSS CDN, the SDK will immediately synchronize this command to the cloud server. The SDK will then receive the processing results from the cloud server and notify you of the execution results of the command through this event callback.
| Parameters | Description |
| --- | --- |
| err | 0 indicates success, other values indicate failure. |
| errMsg | Specific error reasons. |

## onSetMixTranscodingConfig

#### Event callback for setting the layout and transcoding parameters of Cloud Mixed Streaming

When you call setMixTranscodingConfig to adjust the layout and transcoding parameters of cloud mixed streaming, the SDK will immediately sync this adjustment command to the cloud server. The SDK will then receive the process results from the cloud server and notify you of the execution results through this event callback.
| Parameters | Description |
| --- | --- |
| err | Error code: 0 indicates success, other values indicate failure. |
| errMsg | Specific error reason. |

## onStartPublishMediaStream

#### Event callback for starting to publish the media stream
When you call startPublishMediaStream to start publishing the media stream to TRTC backend services, the SDK will immediately sync this command to the cloud server. The SDK will then receive the process results from the cloud server and notify you of the execution results through this event callback.
| Parameters | Description |
| --- | --- |
| code | Callback result, 0 indicates success, other values indicate failure. |
| extraInfo | Expand information field, some error codes may carry additional information to help locate the problem. |
| message | Specific callback information. |
| taskId | When the request is successful, the TRTC backend will provide the taskId of this task in the callback. Subsequently, you can use this taskId in combination with updatePublishMediaStream and stopPublishMediaStream to update and stop. |

## onUpdatePublishMediaStream

#### Event callback for updating the media stream
When you call the media stream publishing interface (updatePublishMediaStream) to start updating the media stream to the TRTC backend services, the SDK will immediately sync this command to the cloud server. The SDK will then receive the process results from the cloud server and notify you of the execution results through this event callback.
| Parameters | Description |
| --- | --- |
| code | Callback result, 0 indicates success, other values indicate failure. |
| extraInfo | Expand information field, some error codes may carry additional information to help locate the problem. |
| message | Specific callback information. |
| taskId | The taskId you provide when calling the media stream publishing interface (updatePublishMediaStream) will be brought back to you through this callback, to identify which update request this callback belongs to. |

## onStopPublishMediaStream

#### Event callback for stopping the media stream
When you call stop publishing media stream (stopPublishMediaStream) to stop the media stream to TRTC backend service, the SDK will immediately synchronize this command to the cloud server. Subsequently, the SDK will receive the process results from the cloud server and notify you of the execution results of the command through this event callback.
| Parameters | Description |
| --- | --- |
| code | Callback result, 0 indicates success, other values indicate failure. |
| extraInfo | Expand information field, some error codes may carry additional information to help locate the problem. |
| message | Specific callback information. |
| taskId | The taskId you passed in when you called stop publishing media stream (stopPublishMediaStream) will be brought back to you through this callback, identifying which stop request this callback belongs to. |

## onCdnStreamStateChanged

#### RTMP/RTMPS streaming status change callback
When you call startPublishMediaStream to start publishing media stream to TRTC backend service, the SDK will immediately synchronize this command to the cloud service.
If you set the URL configuration for publishing audio and video streaming to Tencent or third-party CDN in the target stream configuration (TRTCPublishTarget), the specific RTMP or RTMPS streaming status will be synchronized to you through this callback.
| Parameters | Description |
| --- | --- |
| cdnUrl | The URL you passed in through the target stream configuration (TRTCPublishTarget) when you called startPublishMediaStream will be synchronized to you through this callback when the streaming status changes. |
| code | Stream Push Result, 0 means success, other values indicate an error. |
| extraInfo | Expand information field, some error codes may carry additional information to help locate the problem. |
| message | Specific Stream Push Information. |
| status | Streaming Status. -  0: Streaming not started or already ended. This status is returned when you call stopPublishMediaStream. -  1: Connecting to TRTC server and CDN server. If not immediately successful, TRTC backend service will retry multiple times and return this status (callback every 5s). If successful, it enters status 2; if server error occurs or streaming is not successful within 60 seconds, it enters status 4. -  2: CDN streaming is in progress. This status is returned when streaming is successful. -  3: TRTC server and CDN server streaming interruption, recovering. When CDN has an issue or streaming briefly interrupts, the TRTC backend service will automatically attempt to recover streaming and return this status (callback every 5 seconds). If streaming recovers successfully, it will transition to status 2; if server error occurs or recovery fails within 60 seconds, it will transition to status 4. -  4: TRTC server and CDN server streaming interrupted, and recovery or connection timeout. At this point, streaming failed, you can call updatePublishMediaStream again to attempt streaming. -  5: Disconnecting TRTC server and CDN server. When you call stopPublishMediaStream, the TRTC backend service will transition to status 5 and then status 0. |

## onScreenCaptureStarted

#### Event callback for starting screen sharing
When you start screen sharing through startScreenCapture or related interfaces, the SDK will trigger this event callback.

## onScreenCapturePaused

#### Event callback for pausing screen sharing
When you pause screen sharing through pauseScreenCapture, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| reason | Reason. -  0: User-initiated pause. -  1: Note that the meaning of this field slightly differs on MAC and Windows platforms. Screen window not visible pause (Mac). Pause due to setting screen sharing parameters (Windows). -  2: Pause due to screen sharing window being minimized (Windows only). -  3: Pause due to screen sharing window being hidden (Windows only). |

## onScreenCaptureResumed

#### Event callback for resuming screen sharing
When you resume screen sharing through resumeScreenCapture, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| reason | Reason for recovery. -  0: User-initiated recovery. -  1: Note that the meaning of this field slightly differs between MAC and Windows platforms. For MAC, the screen window becomes visible again to resume sharing. For Windows, the screen sharing parameters are automatically restored after configuration. -  2: Indicates that the screen sharing window is restored from being minimized (Windows only). -  3: Indicates that the screen sharing window is restored from being hidden (Windows only). |

## onScreenCaptureStoped
> **适用平台：** iOS, C++

#### Event callback for stopping screen sharing
When you stop screen sharing by calling stopScreenCapture, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| reason | Stop Reason, 0: User proactively stopped; 1: Screen window closed causing stop; 2: Indicates a change in the display screen status for screen sharing (e.g., interface unplugged, change in projection mode, etc.). |

## onScreenCaptureCovered
> **适用平台：** C++

#### Event callback for when the target window of screen sharing is blocked (only applicable for Windows operating system)

When the target window for screen sharing is occluded and cannot be captured normally, the SDK will throw this event callback. Upon receiving this event callback, you can prompt the user to move the occluding window through some UI changes.

## onLocalRecordBegin

#### Event callback for the local recording task that has started
When you start a local media recording task by calling startLocalRecording, the SDK will trigger this event callback to notify you whether the recording task has successfully started.
| Parameters | Description |
| --- | --- |
| errCode | Status Code. -  0: Recording task started successfully. -  -1: Internal error caused the recording task to fail to start. -  -2: Incorrect file suffix name (e.g., unsupported recording format). -  -6: Recording has already started, needs to stop recording first. -  -7: Recording file already exists, needs to delete the file first. -  -8: No write permission in the recording directory, please check directory permissions. |
| storagePath | CFS path for recording. |

## onLocalRecording

#### Progress event callback for an ongoing local recording task
After you successfully start a local media recording task by calling startLocalRecording, the SDK will periodically trigger this event callback.
You can capture this event callback to know the health status of the recording task.
You can set the interval for this event callback when you call startLocalRecording.
| Parameters | Description |
| --- | --- |
| duration | The cumulative recorded duration in milliseconds. |
| storagePath | CFS path for recording. |

## onLocalRecordFragment

#### Event callback for local recording shard

When you enable shard recording, each completed shard will trigger a callback through this interface.
| Parameters | Description |
| --- | --- |
| storagePath | Shard CFS path. |

## onLocalRecordComplete

#### Event callback for the local recording task that has ended
When you call stopLocalRecording to stop the local media recording task, the SDK will trigger this event callback to notify you of the final result of the recording task.
| Parameters | Description |
| --- | --- |
| errCode | Status Code. -   0: Successfully ended the recording task. -  -1: Recording failed. -  -2: Changing resolution or switching between portrait and landscape mode caused the recording to end. -  -3: The recording duration is too short, or no video or audio data was captured. Please check the recording duration, or whether audio and video capture is enabled. |
| storagePath | CFS path for recording. |

## onSnapshotComplete
> **适用平台：** Android, C++

#### Event callback for local screenshot completion
| Parameters | Description |
| --- | --- |
| bmp | Screenshot result, if bmp is null it means the screenshot operation failed. |
| data | Screenshot data, nullptr means the screenshot failed. |
| format | Screenshot data format, currently only supports TRTCVideoPixelFormat_BGRA32. |
| height | Height of the screenshot. |
| length | Length of the screenshot data, for BGRA32, length = width * height * 4. |
| type | Video stream type. |
| userId | User Identifier, if userId is an empty string, it means the local screen was captured. |
| width | Width of the screenshot. |

> **Note**
> 

> The parameters for the C++ interface and the Java interface across all platforms are different. The C++ interface uses 7 parameters to describe a screenshot, while the Java interface uses only one Bitmap to describe a screenshot.
>

## onUserEnter

#### A host has joined the current room (deprecated)
@deprecated Starting from the new version, it is recommended to use onRemoteUserEnterRoom instead.

## onUserExit

#### A host has left the current room (deprecated)
@deprecated Starting from the new version, it is recommended to use onRemoteUserLeaveRoom instead.

## onAudioEffectFinished

#### Sound effect playback has ended (deprecated)
@deprecated Starting from the new version, it is recommended to use the ITXAudioEffectManager interface instead.
In the new interface, background music and sound effects are no longer distinguished and are unified with startPlayMusic instead.

## onPlayBGMBegin
> **适用平台：** C++

#### Start playing background music (deprecated)

@deprecated Starting from the new version, it is recommended to use the ITXMusicPlayObserver interface instead.

In the new interface, background music and sound effects are no longer distinguished and are unified with startPlayMusic instead.

## onPlayBGMProgress
> **适用平台：** C++

#### Playback progress callback for background music (deprecated)

@deprecated Starting from the new version, it is recommended to use the ITXMusicPlayObserver interface instead.

In the new interface, background music and sound effects are no longer distinguished and are unified with startPlayMusic instead.

## onPlayBGMComplete
> **适用平台：** C++

#### Background music playback has ended (deprecated)

@deprecated Starting from the new version, it is recommended to use the ITXMusicPlayObserver interface instead.

In the new interface, background music and sound effects are no longer distinguished and are unified with startPlayMusic instead.

## onSpeedTest
> **适用平台：** Android, C++

#### Callback for server speed test results (deprecated)
@deprecated Starting from the new version, it is recommended to use the onSpeedTestResult interface instead.

## onRenderVideoFrame

#### Self-definition video rendering callback

When you set a local or remote self-definition video rendering callback, the SDK will deliver the video frames, originally intended for the rendering controls, to you through this callback interface for self-definition rendering.
| Parameters | Description |
| --- | --- |
| frame | Information on video frames to be rendered. |
| streamType | Frequency stream type: Main (generally used for carrying camera footage) and Sub (generally used for carrying screen sharing footage). |
| userId | UserId of the video source. If it's a local video callback (setLocalVideoRenderDelegate), this parameter can be ignored. |

## onGLContextCreated

#### Notification that the SDK's internal OpenGL environment has been created

## onProcessVideoFrame

#### Video processing callback for integrating third-party beauty components
If you have purchased a third-party beauty filter component, you need to set the third-party beauty filter callback in TRTCCloud. TRTC will then deliver the video frames that need preprocessing through this callback interface.
You can then hand over the video frames from TRTC to the third-party beauty filter component for image processing. Since the data is readable and writable, the results from the third-party beauty filter can be synchronized back to TRTC for subsequent encoding and transmission.
Scenario 1: The beauty filter component generates a new texture on its own.
If the beauty filter component you are using generates a new texture (to carry the processed image) during the image processing, please set dstFrame.textureId to the new texture ID in the callback function:
Scenario 2: The beauty filter component requires you to provide the target texture.
If the third-party beauty filter module does not generate a new texture but requires an input texture and an output texture, you can consider the following solution:
| Parameters | Description |
| --- | --- |
| dstFrame | Used to receive video frames processed by the third-party beauty filter. |
| srcFrame | Used to carry camera footage collected by TRTC. |

> **注意**
> **Note**
> Currently supports only OpenGL texture schemes (PC supports only TRTCVideoBufferType_Buffer format).

**代码示例：**

**Android：**
``` java
private final TRTCVideoFrameListener mVideoFrameListener = new TRTCVideoFrameListener() {
    @Override
    public void onGLContextCreated() {
        mFURenderer.onSurfaceCreated();
        mFURenderer.setUseTexAsync(true);
    }
    @Override
    public int onProcessVideoFrame(TRTCVideoFrame srcFrame, TRTCVideoFrame dstFrame) {
        dstFrame.texture.textureId = mFURenderer.onDrawFrameSingleInput(srcFrame.texture.textureId, srcFrame.width, srcFrame.height);
        return 0;
    }
    @Override
    public void onGLContextDestory() {
        mFURenderer.onSurfaceDestroyed();
    }
};
```
``` java
int onProcessVideoFrame(TRTCCloudDef.TRTCVideoFrame srcFrame, TRTCCloudDef.TRTCVideoFrame dstFrame) {
    thirdparty_process(srcFrame.texture.textureId, srcFrame.width, srcFrame.height, dstFrame.texture.textureId);
    return 0;
}
```

**iOS：**
``` objectivec
uint32_t onProcessVideoFrame(TRTCVideoFrame * _Nonnull)srcFrame dstFrame:(TRTCVideoFrame * _Nonnull)dstFrame{
    self.frameID += 1;
    dstFrame.pixelBuffer = [[FURenderer shareRenderer] renderPixelBuffer:srcFrame.pixelBuffer
                                                             withFrameId:self.frameID
                                                                   items:self.renderItems
                                                               itemCount:self.renderItems.count];
    return 0;
}
```
``` objectivec
uint32_t onProcessVideoFrame(TRTCVideoFrame * _Nonnull)srcFrame dstFrame:(TRTCVideoFrame * _Nonnull)dstFrame{
    thirdparty_process(srcFrame.textureId, srcFrame.width, srcFrame.height, dstFrame.textureId);
    return 0;
}
```

**C++：**
``` cpp
int onProcessVideoFrame(TRTCVideoFrame * srcFrame, TRTCVideoFrame *dstFrame) {
    dstFrame->textureId = mFURenderer.onDrawFrameSingleInput(srcFrame->textureId);
    return 0;
}
```
``` cpp
int onProcessVideoFrame(TRTCVideoFrame *srcFrame, TRTCVideoFrame *dstFrame) {
    thirdparty_process(srcFrame->textureId, srcFrame->width, srcFrame->height, dstFrame->textureId);
    return 0;
}
```

## onGLContextDestroy
> **适用平台：** C++

#### Notification that the SDK's internal OpenGL environment has been destroyed

## onCapturedAudioFrame

#### Local audio data callback after preprocessing by the audio module

After you set the audio data self-definition callback, the SDK will deliver the freshly collected and preprocessed (ANS, AEC, AGC) data to you in PCM format through this interface callback.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  For example, with TRTC's default audio recording format of 48000 sampling rate, mono channel, and 16-bit sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |

> **Note**
> 1. Please avoid any time-consuming operations in this callback function, as the SDK processes one frame of audio data every 20ms. If your processing time exceeds 20ms, it will cause sound anomalies.
> 2. The audio data from this interface callback is read-write, meaning you can modify the audio data synchronously within the callback function, but ensure that the processing time is efficient.
> 

> 3. The audio data callback from this interface has undergone preprocessing (ANS, AEC, AGC) but does ** not include ** background music, sound effects, reverb, etc., with low latency.
>

## onLocalProcessedAudioFrame

#### Local audio data callback after preprocessing, sound-effect processing, and mixing BGM by the audio module
After you set the audio data self-definition callback, the SDK will deliver the freshly collected and preprocessed audio data, processed with sound effects and mixed BGM, in PCM format through this interface callback before final network encoding.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.
-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.
-  Taking TRTC's default audio recording format as an example: 48000 sampling rate, mono, 16 sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.
Special note:
You can achieve the signaling transmission purpose by setting the ` TRTCAudioFrame.extraData ` field in the interface. Since the data block at the head of the audio frame cannot be too large, it is recommended that when you write ` extraData `, try to control the signaling to be within a few bytes. If it exceeds 100 bytes, the written data will not be sent.
Other users in the room can receive data through the callback of the  TRTCAudioFrameDelegate  in the ` onRemoteUserAudioFrame ` field's ` TRTCAudioFrame.extraData ` field.
| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |

> **注意**
> **Note**
> 1. Please avoid any time-consuming operations in this callback function, as the SDK processes one frame of audio data every 20ms. If your processing time exceeds 20ms, it will cause sound anomalies.
> 2. The audio data from this interface callback is read-write, meaning you can modify the audio data synchronously within the callback function, but ensure that the processing time is efficient.
> 3. The data from this callback interface has undergone preprocessing (ANS, AEC, AGC), sound effects and mixed BGM processing, resulting in higher sound delay compared to  onCapturedAudioFrame .

## onPlayAudioFrame
> **适用平台：** C++

#### Audio data of each remote user before mixing

After you set the audio data self-definition callback, the SDK will deliver the raw data of each remote source to you in PCM format through this interface callback before final mixing.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  Taking TRTC's default audio recording format as an example: 48000 sampling rate, mono, 16 sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |
| userId | User identifier. |

> **Note**
> The audio data from this callback interface is read-only and cannot be modified.
## onMixedPlayAudioFrame

#### Callback of mixed audio data before final submission to the system for playback

After you set the audio data self-definition callback, the SDK will deliver the mixed audio data of each audio track to you in PCM format through this interface callback before submitting it for system playback.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  For example, with TRTC's default audio recording format of 48000 sampling rate, mono channel, and 16-bit sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |

> **Note**
> 

> 1. Please avoid any time-consuming operations in this callback function, as the SDK processes one frame of audio data every 20ms. If your processing time exceeds 20ms, it will cause sound anomalies.
> 

> 2. The audio data from this interface callback is read-write, meaning you can modify the audio data synchronously within the callback function, but ensure that the processing time is efficient.
> 

> 3. This interface callback delivers the mixed data of each audio track to be played, but it does not include ear return audio data.
>

## onMixedAllAudioFrame

#### Audio data mixed by the SDK (including collected and to-be-played audio)

After you set the audio data self-definition callback, the SDK will mix all the collected and to-be-played audio data and deliver it to you in PCM format through this interface callback for your self-definition recording.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  For example, with TRTC's default audio recording format of 48000 sampling rate, mono channel, and 16-bit sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |

> **Note**
> 

> 1. This interface callback delivers the mixed data of all audio data from the SDK, including: local audio after 3A preprocessing, special effects overlay, and background music mixing; all remote audio; but not including ear return audio.
> 

> 2. The audio data from this interface callback does not support modification.
>

## onLog

#### Local LOG printing callback

If you wish to capture the SDK's local log printing behavior, you can set a log callback, allowing the SDK to send all logs to you through this callback interface.
| Parameters | Description |
| --- | --- |
| level | For log levels, please refer to TRTC_LOG_LEVEL. |
| log | Log content. |
| module | Reserved field; currently has no specific meaning and is fixed at TXLiteAVSDK. |

## TRTCVideoRenderListener
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onRenderVideoFrame | Self-definition video rendering callback |

## TRTCVideoFrameListener
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onGLContextCreated | Notification that the SDK's internal OpenGL environment has been created |
| onProcessVideoFrame | Video processing callback for integrating third-party beauty components |
| onGLContextDestory | Notification that the SDK's internal OpenGL environment has been destroyed |

## TRTCAudioFrameListener
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onCapturedAudioFrame | Local audio data callback after preprocessing by the audio module |
| onLocalProcessedAudioFrame | Local audio data callback after preprocessing, sound-effect processing, and mixing BGM by the audio module |
| onRemoteUserAudioFrame | Audio data of each remote user before mixing |
| onMixedPlayAudioFrame | Callback of mixed audio data before final submission to the system for playback |
| onMixedAllAudioFrame | Audio data mixed by the SDK (including collected and to-be-played audio) |
| onVoiceEarMonitorAudioFrame | Audio data for ear return |

## TRTCLogListener
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onLog | Local LOG printing callback |

## onGLContextDestory
> **适用平台：** Android, iOS

#### Notification that the SDK's internal OpenGL environment has been destroyed

## onRemoteUserAudioFrame
> **适用平台：** Android, iOS

#### Audio data of each remote user before mixing

After you set the audio data self-definition callback, the SDK will deliver the raw data of each remote source to you in PCM format through this interface callback before final mixing.
-  The time frame length of the audio callback from this interface is fixed at 0.02s, in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  Taking TRTC's default audio recording format as an example: 48000 sampling rate, mono, 16 sampling point width, the byte frame length is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |
| userId | User identifier. |

> **Note**
> 

> The audio data from this callback interface is read-only and cannot be modified.
>

## onVoiceEarMonitorAudioFrame
> **适用平台：** Android, iOS

#### Audio data for ear return

After you set the audio data self-definition callback, the SDK will deliver the ear return audio data to you in PCM format through this interface callback before playback.
-  The audio time frame length from this interface callback is not fixed and is in PCM format.

-  The formula to convert time frame length to byte frame length is ` Sampling Rate × Time Frame Length × Number of Channels × Sampling Point Width `.

-  Taking the default audio recording format of TRTC with a sampling rate of 48000, mono, and 16-bit sample depth as an example, the byte frame length of 0.02s audio data is ` 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes `.

| Parameters | Description |
| --- | --- |
| frame | Audio data frame in PCM format. |

> **Note**
> 

> 1. Please avoid performing any time-consuming operations in this callback function, as it may cause sound anomalies.
> 

> 2. The audio data from this interface callback is read-write, meaning you can modify the audio data synchronously within the callback function, but ensure that the processing time is efficient.
>

## onDisConnectOtherRoom
> **适用平台：** Android

#### Event callback for ending cross-room communication result

## onAudioRouteChanged
> **适用平台：** Android, iOS

#### Current audio routing has changed (applicable only to mobile devices)

The term "Audio Routing" refers to whether the sound is played through the phone's speaker or receiver. A change in audio routing means a change in the playback location of the sound.
-  When the audio routing is set to receiver, the sound is quieter, and you need to bring the phone close to your ear to hear clearly. This offers better privacy and is suitable for phone calls.

-  When the audio routing is set to speaker, the sound is louder, allowing for clear listening without holding the phone to your face, thus enabling the "hands-free" feature.

-  When the audio routing is set to wired headphones.

-  When the audio routing is set to Bluetooth headphones.

-  When the audio routing is set to USB professional sound card device.

| Parameters | Description |
| --- | --- |
| fromRoute | Audio routing before the change. |
| route | Audio routing, which refers to where the sound is output (speaker, receiver). |

## onScreenCaptureStopped
> **适用平台：** Android

#### Event callback for stopping screen sharing

When you stop screen sharing by calling stopScreenCapture, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| reason | Stop Reason, 0: User proactively stopped; 1: Screen window closed causing stop; 2: Indicates a change in the display screen status for screen sharing (e.g., interface unplugged, change in projection mode, etc.). |

## TRTCVideoRenderDelegate
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| onRenderVideoFrame:userId:streamType: | Self-definition video rendering callback |

## TRTCVideoFrameDelegate
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| onGLContextCreated | Notification that the SDK's internal OpenGL environment has been created |
| onProcessVideoFrame:dstFrame: | Video processing callback for integrating third-party beauty components |
| onGLContextDestory | Notification that the SDK's internal OpenGL environment has been destroyed |

## TRTCAudioFrameDelegate
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| onCapturedAudioFrame: | Local audio data callback after preprocessing by the audio module |
| onLocalProcessedAudioFrame: | Local audio data callback after preprocessing, sound-effect processing, and mixing BGM by the audio module |
| onRemoteUserAudioFrame:userId: | Audio data of each remote user before mixing |
| onMixedPlayAudioFrame: | Callback of mixed audio data before final submission to the system for playback |
| onMixedAllAudioFrame: | Audio data mixed by the SDK (including collected and to-be-played audio) |
| onVoiceEarMonitorAudioFrame: | Audio data for ear return |

## TRTCLogDelegate
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| onLog:LogLevel:WhichModule: | Local LOG printing callback |

## onDevice
> **适用平台：** iOS

#### Local device connection status has changed (applicable only to desktop systems)

When local devices (including camera, microphone, and speaker) are plugged in or unplugged, the SDK will trigger this event callback.
| Parameters | Description |
| --- | --- |
| deviceId | Device ID. |
| deviceType | Device Type. |
| state | On/Off status, 0: Device disconnected; 1: Device connected. |

## onRecvCustomCmdMsgUserId
> **适用平台：** iOS

#### Receipt of a custom Definition message

When a user in a room uses sendCustomCmdMsg to send a Definition UDP Message, other users in the room can receive the message through the onRecvCustomCmdMsg event callback.
| Parameters | Description |
| --- | --- |
| cmdID | Command ID. |
| message | Message Data. |
| seq | Message serial number. |
| userId | User identifier. |

## onMissCustomCmdMsgUserId
> **适用平台：** iOS

#### Event callback for lost custom Definition messages

When you use sendCustomCmdMsg to send a Definition UDP message, even if reliable transmission is set, it cannot guarantee 100% message delivery, but the probability of message loss is very low and meets conventional reliability requirements.

After setting reliable transmission on the sender side, the SDK will notify the number of lost Definition messages during the past time period (usually 5 seconds) through this callback.
| Parameters | Description |
| --- | --- |
| cmdID | Command ID. |
| errCode | Error code. |
| missed | Number of lost messages. |
| userId | User identifier. |

> **Note**
> 

> Only if reliable transmission is set on the sender side can the receiver receive the message loss callback.
>
