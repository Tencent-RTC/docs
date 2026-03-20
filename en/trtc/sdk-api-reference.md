> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TRTCCloud 文档

## 概述
本文档是 实时音视频(TRTC SDK) TRTCCloud 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TRTCCloud
## Overview

`TRTCCloud.java` is the API reference for TRTC Real-Time Communication (TRTC SDK) on Android. It provides detailed documentation for features including stream publishing, stream playback, stream mixing, and recording.

## getTRTCShareInstance
> **平台 API 名称：** Android: `sharedInstance` | iOS: `sharedInstance`

#### Create a `TRTCCloud` Instance (Singleton Pattern)
| Parameters | Description |
| --- | --- |
| context | Only applies to the Android platform. The SDK internally converts it to the Android platform's ApplicationContext to call Android System API. If the passed context parameter is NULL, the SDK will automatically obtain the current process's ApplicationContext. |

> **Note**
> 

> 1. Using delete ITRTCCloud* will cause compilation errors. Please use destroyTRTCCloud to release the object pointer.
> 

> 2. On Windows, Mac, and iOS platforms, please call the getTRTCShareInstance() interface.
> 

> 3. On the Android platform, please call the getTRTCShareInstance(void *context) interface.
>

## destroyTRTCShareInstance
> **平台 API 名称：** Android: `destroySharedInstance` | iOS: `destroySharedInstance`

#### Terminate `TRTCCloud` Instance (Singleton Pattern)

## addCallback
> **平台 API 名称：** Android: `addListener` | iOS: `addDelegate`

#### Add TRTC Event Callback
You can receive various event notifications from the SDK (such as error codes, warning codes, audio and video status parameters, etc.) through TRTCCloudListener.

## removeCallback
> **平台 API 名称：** Android: `removeListener` | iOS: `removeDelegate`

#### Remove TRTC Event Callback

## enterRoom

#### Enter Room
All TRTC users must enter a room to "publish" or "subscribe" to audio and video streams. "Publish" means pushing your audio and video to the cloud, and "subscribe" means pulling audio and video streams from other users in the room from the cloud.
To call this interface, you need to specify your application scenario TRTCAppScene for the best audio and video transmission experience. These scenarios can be divided into two major categories:
**Real-time Call:**
Including the two options TRTC_APP_SCENE_VIDEOCALL and TRTC_APP_SCENE_AUDIOCALL, which are video call and voice call respectively. This mode is suitable for 1-on-1 audio and video calls or online meetings with fewer than 300 participants.
**Online Live Streaming:**
Including the two options TRTC_APP_SCENE_LIVE and TRTC_APP_SCENE_VOICE_CHATROOM, which are video live streaming and voice live streaming respectively. This mode is suitable for online live streaming scenarios with less than 100,000 participants. However, you need to specify the **role** field in the TRTCParams parameter introduced next, distinguishing users in the room as **Anchors** (TRTCRoleAnchor) and **Audience** (TRTCRoleAudience).
After calling this interface, you will receive the onEnterRoom(result) callback from TRTCCloudListener
-  If entering the room is successful, the result parameter will be a positive number (result > 0), indicating the time taken from the function call to entering the room, in milliseconds (ms).
-  If entering the room fails, the result parameter will be a negative number (result < 0), indicating the failure [Error Code](https://cloud.tencent.com/document/product/647/32257).
| Parameters | Description |
| --- | --- |
| param | Room entry parameters are used to specify the user's identity, role, and security token information. For details, please refer to TRTCParams. |
| scene | Application scenario parameters are used to specify your business scenario. All users in the same room need to set the same TRTCAppScene. |

> **注意**
> **Note**
> 1. All users in the same room need to set the same scene. Different scenes can occasionally lead to anomalies.
> 2. Please ensure that enterRoom and exitRoom are paired before and after use, i.e., ensure "exit the previous room before entering the next room," otherwise, many anomalies may occur.

## exitRoom

#### Leave Room
Calling this interface will make the user leave the audio and video room they are in and release resources such as the camera, microphone, and speaker.
After the resources are fully released, the SDK will notify you via the ITRTCCloudCallback in the onExitRoom callback.
If you want to call enterRoom again or switch to another provider's SDK, it is recommended to wait for the onExitRoom callback before performing subsequent operations to avoid camera or microphone occupation issues.

## switchRole

#### Switch Role (Supports Setting Permission Bit)
This interface allows users to switch between the roles of `Anchor` and `Audience`.
Due to video live streaming and voice chat rooms needing to support up to 100,000 viewers simultaneously, a rule has been established that "only the host can publish their own audio and video".
Therefore, when some viewers wish to publish their own audio and video streams (in order to interact with the host), they need to first switch their role to "host".
When entering a room, you can predefine the user's role using the role field in TRTCParams, or dynamically switch roles after entering the room using the switchRole interface.
| Parameters | Description |
| --- | --- |
| privateMapKey | Permission tickets are used for permission control. When you want to restrict a specific room to allow only specific userIds to enter or upload videos, you need to use privateMapKey for permission protection. -  It is recommended only for customers with high-level security requirements. For more details, please refer to [Enabling Advanced Permission Control](https://cloud.tencent.com/document/product/647/32240). |
| role | Role, defaults to "Anchor": - TRTCRoleAnchor: Anchor, can publish their own audio and video. A maximum of 50 anchors can simultaneously publish audio and video in the same room. - TRTCRoleAudience: Audience cannot publish their own audio and video streams and can only watch other anchors' audio and video in the room. If you want to publish your own audio and video, you need to switch to "Anchor" through switchRole. A maximum of 100,000 audience members can be accommodated in the same room. |
> **注意**
> **Note**

## switchRoom

#### Switch Room
This interface allows users to quickly switch from one room to another.
-  If the user's role is "Audience", the effect of calling this interface is equivalent to exitRoom (current room) + enterRoom (new room).
-  If the user's role is "Anchor", this interface will maintain the user's audio and video publishing status while switching rooms. As a result, the preview of the camera and the collection of sound will not be interrupted during the room switching process.
This interface is suitable for scenarios in online education where supervising teachers need to switch quickly between multiple rooms. In this scenario, using switchRoom can achieve better smoothness and less code volume compared to using exitRoom + enterRoom.
The result of the interface call will be notified to you through the onSwitchRoom(errCode, errMsg) callback in ITRTCCloudCallback.
| Parameters | Description |
| --- | --- |
| config | For room parameters, please refer to TRTCSwitchRoomConfig. |
> **注意**
> **Note**
> Due to the need for compatibility with older SDK versions, the config parameters contain both roomId and strRoomId. The specifications for filling these parameters are particularly important, so please take note of the following:
> 1. If you choose strRoomId, then roomId needs to be set to
> 2. If both are filled, roomId will be selected by default.
> 3. All rooms must either use strRoomId or roomId consistently. Mixing both will result in unexpected bugs.

## connectOtherRoom

#### Request Cross-room Communication
By default, only users within the same room can make audio and video calls. Audio and video streams in different rooms are isolated.
However, you can use this interface to publish the audio and video streams of a host in another room to your own room. At the same time, this interface will also publish your own audio and video streams to the target host's room.
In other words, you can use this interface to enable cross-room audio and video stream sharing between hosts in two different rooms, allowing audiences in each room to watch the audio and video streams of both hosts. This feature can be used to implement a PK feature between hosts.
The result of the cross-room communication request will be notified to you via the TRTCCloudDelegate callback onConnectOtherRoom.
For example, when Host A in room "101" establishes a cross-room call with Host B in room "102" using connectOtherRoom(),
-  users in room "101" will receive the onRemoteUserEnterRoom(B) and onUserVideoAvailable(B, YES) event callbacks for Host B, allowing users in room "101" to subscribe to Host B's audio and video streams.
-  users in room "102" will receive the onRemoteUserEnterRoom(A) and onUserVideoAvailable(A, YES) event callbacks for Host A, allowing users in room "102" to subscribe to Host A's audio and video streams.

To consider compatibility issues with subsequent extension fields, the parameters for cross-room communication temporarily use JSON format:
**Case 1: Numeric Room Number**
If Host A in room '101' wants to connect with Host B in room '102', Host A needs to pass in the following when calling the interface: {"roomId": 102, "userId": "userB"}
Below is the sample code:
**Case 2: String Room Number**
If you are using a string room number, be sure to replace "roomId" in the JSON with "strRoomId": {"strRoomId": "102", "userId": "userB"}
Below is the sample code:
| Parameters | Description |
| --- | --- |
| param | You need to pass in a JSON-formatted string argument, where roomId represents the room number in numeric format, strRoomId represents the room number in string format, and userId represents the target broadcaster's user ID. |

**代码示例：**

**Android：**
``` java
  JSONObject jsonObj = new JSONObject();
  jsonObj.put("roomId", 102);
  jsonObj.put("userId", "userB");
  trtc.ConnectOtherRoom(jsonObj.toString());
```
``` java
  JSONObject jsonObj = new JSONObject();
  jsonObj.put("strRoomId", "102");
  jsonObj.put("userId", "userB");
  trtc.ConnectOtherRoom(jsonObj.toString());
```

**iOS：**
``` objectivec
  NSMutableDictionaryjsonDict = [[NSMutableDictionary alloc] init];
  [jsonDict setObject:@(102) forKey:@"roomId"];
  [jsonDict setObject:@"userB" forKey:@"userId"];
  NSData* jsonData = [NSJSONSerialization dataWithJSONObject:jsonDict options:NSJSONWritingPrettyPrinted error:nil];
  NSString* jsonString = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
  [trtc connectOtherRoom:jsonString];
```
``` objectivec
  NSMutableDictionaryjsonDict = [[NSMutableDictionary alloc] init];
  [jsonDict setObject:@"102" forKey:@"strRoomId"];
  [jsonDict setObject:@"userB" forKey:@"userId"];
  NSData* jsonData = [NSJSONSerialization dataWithJSONObject:jsonDict options:NSJSONWritingPrettyPrinted error:nil];
  NSString* jsonString = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
  [trtc connectOtherRoom:jsonString];
```

**C++：**
``` cpp
  Json::Value jsonObj;
  jsonObj["roomId"] = 102;
  jsonObj["userId"] = "userB";
  Json::FastWriter writer;
  std::string params = writer.write(jsonObj);
  trtc.ConnectOtherRoom(params.c_str());
```
``` cpp
  Json::Value jsonObj;
  jsonObj["strRoomId"] = "102";
  jsonObj["userId"] = "userB";
  Json::FastWriter writer;
  std::string params = writer.write(jsonObj);
  trtc.ConnectOtherRoom(params.c_str());
```

## disconnectOtherRoom

#### Exit Cross-room Communication
The exit result will be notified to you via the ITRTCCloudCallback in the onDisconnectOtherRoom callback.

## setDefaultStreamRecvMode

#### Set Subscription Mode (Needs to be set before entering the room to take effect)
You can switch between "auto-subscribe" and "manual subscribe" modes via this interface:
-  Automatic Subscription: Default mode where users receive audio and video streams from the room immediately after entering. Audio plays automatically and video starts decoding automatically (you still need to bind the rendering control through the startRemoteView interface).
-  Manual subscription: After the user enters the room, they need to manually call the startRemoteView interface to start the subscription and decoding of the video stream. They need to manually call the muteRemoteAudio(false) interface to start the playback of the sound.
In most scenarios, users entering a room will subscribe to all the audio and video streams from the broadcasters within that room. Therefore, TRTC defaults to an automatic subscription mode to achieve the best "instant start experience".
If your application scenario involves many audio and video streams being published in each room and each user only wants to selectively subscribe to 1-2 of them, it is recommended to use the "manual subscription" mode to save on bandwidth costs.
| Parameters | Description |
| --- | --- |
| autoRecvAudio | true: Automatically subscribe to audio; false: You need to manually call muteRemoteAudio(false) to subscribe to audio. Default value: true. |
| autoRecvVideo | true: Automatically subscribe to video; false: You need to manually call startRemoteView to subscribe to video. Default value: true. |
> **注意**
> **Note**
> 1. This setting needs to be called before entering the room to take effect.
> 2. In automatic subscription mode, if the user does not call startRemoteView to subscribe to the video stream after entering the room, the SDK will automatically stop subscribing to the video stream to save traffic.

## createSubCloud

#### Create Sub-room Instances (For Multi-room Concurrent Viewing)
TRTCCloud was initially designed as a Singleton Pattern, limiting the ability to watch multiple rooms concurrently.
By calling this interface, you can create multiple TRTCCloud instances to watch audio and video streams from multiple rooms simultaneously.
However, please note that the ability to publish audio and video streams across multiple TRTCCloud instances is subject to certain restrictions.
This feature is primarily used in a business scenario called "super small class" in online education settings, aimed at addressing the limitation that "each TRTC room can only have up to 50 people broadcasting their audio and video streams simultaneously".
Below is the sample code:
#### Return value description:
Sub TRTCCloud instances

> **注意**
> **Note**
> -  The same user can enter multiple rooms with different roomIds using the same userId.
> -  Two different terminal devices cannot use the same userId to enter the same roomId simultaneously.
> -  The same user can stream on multiple TRTCCloud instances and call interfaces related to local audio and video in sub-instances. However, please note:
> -  The audio for multiple instances must be either microphone capture or custom data collection, and invoking interfaces related to audio devices will be based on the last call
> -  Calls related to the camera will take the last one as definitive: startLocalPreview.

**代码示例：**

**Android：**
``` java
    //In the small room that needs interaction, enter the room as an anchor and push audio and video streams
    TRTCCloud mainCloud = TRTCCloud.sharedInstance(mContext);
    TRTCCloudDef.TRTCParams mainParams = new TRTCCloudDef.TRTCParams();
    //Fill your params
    mainParams.role = TRTCCloudDef.TRTCRoleAnchor;
    mainCloud.enterRoom(mainParams, TRTCCloudDef.TRTC_APP_SCENE_LIVE);
    //...
    mainCloud.startLocalPreview(true, videoView);
    mainCloud.startLocalAudio(TRTCCloudDef.TRTC_AUDIO_QUALITY_DEFAULT);

    //In the large room that only needs to watch, enter the room as an audience and pull audio and video streams
    TRTCCloud subCloud = mainCloud.createSubCloud();
    TRTCCloudDef.TRTCParams subParams = new TRTCCloudDef.TRTCParams();
    //Fill your params
    subParams.role = TRTCCloudDef.TRTCRoleAudience;
    subCloud.enterRoom(subParams, TRTCCloudDef.TRTC_APP_SCENE_LIVE);
    //...
    subCloud.startRemoteView(userId, TRTCCloudDef.TRTC_VIDEO_STREAM_TYPE_BIG, view);
    //...
    //Exit from new room and release it.
    subCloud.exitRoom();
    mainCloud.destroySubCloud(subCloud);
```

**iOS：**
``` objectivec
    //In the small room that needs interaction, enter the room as an anchor and push audio and video streams
    TRTCCloud *mainCloud = [TRTCCloud sharedInstance];
    TRTCParams *mainParams = [[TRTCParams alloc] init];
    //Fill your params
    mainParams.role = TRTCRoleAnchor;
    [mainCloud enterRoom:mainParams appScene:TRTCAppSceneLIVE)];
    //...
    [mainCloud startLocalPreview:YES view:videoView];
    [mainCloud startLocalAudio:TRTCAudioQualityDefault];

    //In the large room that only needs to watch, enter the room as an audience and pull audio and video streams
    TRTCCloud *subCloud = [mainCloud createSubCloud];
    TRTCParams *subParams = [[TRTCParams alloc] init];
    //Fill your params
    subParams.role = TRTCRoleAudience;
    [subCloud enterRoom:subParams appScene:TRTCAppSceneLIVE)];
    //...
    [subCloud startRemoteView:userId streamType:TRTCVideoStreamTypeBig view:videoView];
    //...
    //Exit from new room and release it.
    [subCloud exitRoom];
    [mainCloud destroySubCloud:subCloud];
```

**C++：**
``` cpp
    //In the small room that needs interaction, enter the room as an anchor and push audio and video streams
    ITRTCCloud *mainCloud = getTRTCShareInstance();
    TRTCParams mainParams;
    //Fill your params
    mainParams.role = TRTCRoleAnchor;
    mainCloud->enterRoom(mainParams, TRTCAppSceneLIVE);
    //...
    mainCloud->startLocalAudio(TRTCAudioQualityDefault);
    mainCloud->startLocalPreview(renderView);
    
    //In the large room that only needs to watch, enter the room as an audience and pull audio and video streams
    ITRTCCloud *subCloud = mainCloud->createSubCloud();
    TRTCParams subParams;
    //Fill your params
    subParams.role = TRTCRoleAudience;
    subCloud->enterRoom(subParams, TRTCAppSceneLIVE);
    //...
    subCloud->startRemoteView(userId, TRTCVideoStreamTypeBig, renderView);
    //...
    //Exit from new room and release it.
    subCloud->exitRoom();
    mainCloud->destroySubCloud(subCloud);
```

## destroySubCloud

#### Terminate Sub-room Instances
| Parameters | Description |
| --- | --- |
| subCloud | Sub-room instances |

## updateOtherRoomForwardMode

#### Modify the uplink capability of cross-room broadcasters in this room
Usually, after calling the connectOtherRoom interface to conduct cross-room communication with another room's broadcaster, all viewers in this room will receive the audio and video stream published by that broadcaster.
You can call this interface to restrict the uplink capability of a cross-room broadcaster in this room, either forbidding or allowing them to publish audio/main video/auxiliary video. This action will affect all users in the room.
After disabling certain uplink capabilities of the cross-room broadcaster, all users in this room will not be able to receive the corresponding audio and video streams, nor will they be able to subscribe to them.
It should be noted that this interface can only be called by the broadcaster conducting cross-room communication, and the restrictions set through this interface will reset if the cross-room communication is interrupted or the corresponding broadcaster exits the room.
The result of this interface call will be notified to you through the onUpdateOtherRoomForwardMode callback in TRTCCloudDelegate.
For example:
In room "101", there is Host A and Audience B, while in room "102", there is Host C, who is normally publishing audio and video streams. Host A establishes a cross-room call with Host C using connectOtherRoom().
-  At this time, Host A and Audience B will receive three event callbacks for Host C: onRemoteUserEnterRoom(C), onUserVideoAvailable(C, YES), and onUserAudioAvailable(C, YES), allowing them to subscribe to Host C's audio and video.
Later, Host A calls this interface to disable Host C's ability to publish audio in the current room.
-  Afterward, Host C's audio stream will not be published to room "101". Host A and Audience B will receive the onUserAudioAvailable(C, NO) event callback and will no longer be able to subscribe to Host C's audio through muteRemoteAudio(C, NO).
-  Host C's video stream will not be affected. Other audience members in room "102" are also unaffected and can normally subscribe to Host C's audio.
To consider compatibility issues with subsequent extension fields, the parameters for this interface temporarily use JSON format. The example is as follows:
**Case 1: Numeric Room Number**
**Case 2: String Room Number**
| Parameters | Description |
| --- | --- |
| param | You need to pass in a JSON-formatted string argument where roomId represents the room number in numeric format, strRoomId represents the room number in string format, userId represents the target broadcaster's user ID. The options muteAudio, muteVideo, and muteSubStream denote whether to disable or allow the cross-room broadcaster to publish audio, main stream video, and sub-stream video respectively. |

**代码示例：**
``` java
{
  "roomId":102,
  "userId":"userC",
  "muteAudio":false,
  "muteVideo":true,
  "muteSubStream":false
}
```
``` java
{
  "strRoomId":"102",
  "userId":"userC",
  "muteAudio":false,
  "muteVideo":true,
  "muteSubStream":false
}
```

## startPublishMediaStream

#### Start publishing media stream
This interface sends an instruction to the TRTC server to relay/transcode the current user's audio and video streams to the Live CDN or push them back into the TRTC room. You can specify the publishing mode through TRTCPublishTarget configuration and TRTCPublishMode
| Parameters | Description |
| --- | --- |
| config | Media stream transcoding configuration parameters. Refer to TRTCStreamMixingConfig for detailed configuration. This is required when transcoding and pushing back to the TRTC room. You need to specify your desired transcoding configuration parameters. It is invalid in relay mode. |
| params | Media stream encoding output parameters. Refer to TRTCStreamEncoderParam for detailed configuration. This is required when transcoding and pushing back to the TRTC room. You need to specify your desired transcoding output parameters. For better relay stability and CDN compatibility, it is also recommended to configure this during relay. |
| target | The target address for media stream publishing. Refer to TRTCPublishTarget for detailed configuration. Supports relay/transcoding to Tencent or third-party CDNs, and also supports transcoding pushback to the TRTC room. |
> **注意**
> **Note**
> 1. The SDK will provide the task identifier (taskId) for the backend startup through the callback onStartPublishMediaStream.
> 2. The same task (both TRTCPublishMode and TRTCPublishCdnUrl are identical) can only be started once. If you need to update or stop this task later, you must record and use the returned taskId to operate through updatePublishMediaStream or stopPublishMediaStream.
> 3. The target supports configuring multiple CDN URLs simultaneously (up to 10). If the same relay/transcoding task needs to be published to multiple CDNs, you only need to configure multiple CDN URLs in the target. Even if there are multiple relay addresses for the same transcoding task, only one transcoding fee will be charged.
> 4. When using, do not push multiple tasks to the same URL addresses simultaneously to avoid abnormal streaming status. A recommended scheme is to use "sdkappid_roomid_userid_main" in the URL as a distinguishing identifier. This naming convention is easy to recognize and will not cause conflicts in your multiple applications.

## updatePublishMediaStream

#### Update the published media stream.
This interface will send directives to the TRTC server to update the media stream started by startPublishMediaStream
| Parameters | Description |
| --- | --- |
| config | Media stream transcoding configuration parameters. Refer to TRTCStreamMixingConfig for detailed configuration. This is required when transcoding and pushing back to the TRTC room. You need to specify your desired transcoding configuration parameters. It is invalid in relay mode. |
| params | Media stream encoding output parameters. Refer to TRTCStreamEncoderParam for detailed configuration. This is required when transcoding and pushing back to the TRTC room. You need to specify your desired transcoding output parameters. For better relay stability and CDN compatibility, it is also recommended to configure this during relay. |
| target | The target address for media stream publishing. Refer to TRTCPublishTarget for detailed configuration. Supports relay/transcoding to Tencent or third-party CDNs, and also supports transcoding pushback to the TRTC room. |
| taskId | It will provide you with the task identifier (i.e., taskId) for the background start task through the onStartPublishMediaStream callback |
> **注意**
> **Note**
> 1. You can use this interface to update the published CDN URL (supports add and delete, up to 10 at the same time), but ensure you do not push multiple tasks to the same URL addresses simultaneously to avoid abnormal streaming status.
> 2. You can update or adjust relay/transcoding tasks using taskId. For example, in PK business, you can initiate relay through startPublishMediaStream first, then use taskId and this interface to update the relay to a transcoding task when the anchor initiates PK. At this time, CDN playback will be continuous and without stream disconnection (you need to keep the media stream encoding output parameters consistent).
> 3. The same task does not support switching between Audio Only, Audio and Video, and Pure Video.

## stopPublishMediaStream

#### Stop publishing media stream.
This interface will send directives to the TRTC server to stop the media stream started by startPublishMediaStream
| Parameters | Description |
| --- | --- |
| taskId | It will provide you with the task identifier (i.e., taskId) for the background start task through the onStartPublishMediaStream callback |
> **注意**
> **Note**
> 1. If your business backend has not saved the taskId, after your host re-enters the room following an abnormal exit, you can call startPublishMediaStream again to start the task. TRTC backend will return a task start failure and provide you with the taskId from the last attempt
> 2. If the taskId is an empty string, all media streams started by startPublishMediaStream for that user will stop. If you have only started one media stream or want to stop all media streams you started, this method is recommended.

## startLocalPreview

#### Enable the preview screen of the local camera (Mobile Terminal)
Call this function before enterRoom. The SDK will only enable the camera and wait until you call enterRoom to start streaming.
After calling this function after enterRoom, the SDK will enable the Camera and automatically start Video Push Streaming. When the first frame of the camera image starts to render, you will receive the onCameraDidReady callback notification from ITRTCCloudCallback.
| Parameters | Description |
| --- | --- |
| frontCamera | true: Front-facing camera; false: Rear-facing camera. |
| view | Control carrying the video screen. |

> **注意**
> **Note**
> If you wish to preview the camera screen and use BeautyManager to adjust the beauty parameters before streaming, you can:
> -  Option 1: Call startLocalPreview before enterRoom.

## updateLocalView

#### Update the preview screen of the local camera

## stopLocalPreview

#### Stop camera preview

## muteLocalVideo

#### Suspend/Resume publishing local video stream
This interface can suspend (or recover) the publishing of local video screens. After suspension, other users in the same room will not be able to see your screen.
This interface is equivalent to start/stopLocalPreview when specifying TRTCVideoStreamTypeBig, but has better response speed.
Because start/stopLocalPreview requires turning on and off the camera, and turning on and off the camera is a hardware-related operation, which is very time-consuming.
In comparison, muteLocalVideo only needs to suspend or pass the data stream at the software layer. Therefore, it is more efficient and more suitable for scenarios requiring frequent openings and closings.
When suspending/recovering the publishing of the specified TRTCVideoStreamTypeBig, other users in the same room will receive the onUserVideoAvailable callback notification.
When suspending/recovering the publishing of the specified TRTCVideoStreamTypeSub, other users in the same room will receive the onUserSubStreamAvailable callback notification.
| Parameters | Description |
| --- | --- |
| mute | YES: Suspension; NO: Recovery. |
| streamType | The video stream type to be suspended/recovered (only supports TRTCVideoStreamTypeBig and TRTCVideoStreamTypeSub). |

## setVideoMuteImage

#### Set the alternative image during the suspension of the local screen
When you call muteLocalVideo(YES) to suspend the local screen, you can set an alternative image through this interface. After setting, other users in the room will see this alternative image instead of a black screen.
| Parameters | Description |
| --- | --- |
| fps | Set the frame rate for the alternative image, with a minimum value of 5 and a maximum value of 10. The default is 5. |
| image | Set the alternative image. A null value means no video stream data will be sent after muteLocalVideo. The default is null. |

## startRemoteView

#### Subscribe to the video stream of a remote user and bind the video rendering control
By calling this API, the SDK can pull the video stream of the specified user ID and render it on the rendering control specified by the parameter view. You can set the display mode of the screen through setRemoteRenderParams.
-  If you already know the user ID of a user who has a video stream in the room, you can directly call startRemoteView to subscribe to that user's video feed.
-  If you do not know which users in the room are publishing video, you can wait for a notification from onUserVideoAvailable after entering the room.
Calling this API only starts pulling the video stream, at this point the screen still needs to load and buffer. When buffering is complete, you will receive a notification from onFirstVideoFrame.
| Parameters | Description |
| --- | --- |
| streamType | Specify the type of video stream of the user you want to watch. -  HD main video stream: TRTC_VIDEO_STREAM_TYPE_BIG. -  Low-res sub video stream: TRTC_VIDEO_STREAM_TYPE_SMALL (effective only if the remote user has enabled dual encoding through enableEncSmallVideoStream). -  Substream video (typically used for screen sharing): TRTC_VIDEO_STREAM_TYPE_SUB. |
| userId | Specify the remote user's ID. |
| view | Rendering control used to display the video screen. |
> **注意**
> **Note**
> Note a few rules:
> 1. The SDK supports watching the main stream and substream of a certain user ID simultaneously, or watching the substream and small stream of a certain user ID simultaneously, but does not support watching the main stream and small stream simultaneously.
> 2. When the small stream of the specified user ID does not exist, the SDK will default to the user's main stream.

## updateRemoteView

#### Update the video rendering control of a remote user
This interface can be used to update the rendering control of a remote video image and is commonly used in scenarios that involve switching display areas.
| Parameters | Description |
| --- | --- |
| streamType | The type of stream to set the preview window for (only supports TRTC_VIDEO_STREAM_TYPE_BIG and TRTC_VIDEO_STREAM_TYPE_SUB). |
| userId | Specify the remote user's ID. |
| view | Control carrying the video screen. |

## stopRemoteView

#### Unsubscribe from the remote user's video stream and release the rendering control
Calling this interface will make the SDK stop receiving the video stream of the user and release the decoding and rendering resources of that video stream.
| Parameters | Description |
| --- | --- |
| streamType | Specify the type of video stream of the user you want to watch. -  HD main video stream: TRTC_VIDEO_STREAM_TYPE_BIG. -  Low-res sub video stream: TRTC_VIDEO_STREAM_TYPE_SMALL. -  Substream video (typically used for screen sharing): TRTC_VIDEO_STREAM_TYPE_SUB. |
| userId | Specify the remote user's ID. |

## stopAllRemoteView

#### Unsubscribe from all remote users' video streams and release all rendering resources

Calling this interface will make the SDK stop receiving all remote video streams and release all decoding and rendering resources.

> **Note**
> If there is a currently displayed substream (screen sharing), it will also be stopped.
## muteRemoteVideoStream

#### Suspend/Resume subscription of the remote user's video stream
This interface only pauses/resumes receiving the video stream of a designated user but does not release the display resources. The video image will be frozen on the last frame at the time of the interface call.
| Parameters | Description |
| --- | --- |
| mute | Pause receiving or not. |
| streamType | The video stream type to be paused/resumed. -  HD main video stream: TRTC_VIDEO_STREAM_TYPE_BIG. -  Low-res sub video stream: TRTC_VIDEO_STREAM_TYPE_SMALL. -  Substream video (typically used for screen sharing): TRTC_VIDEO_STREAM_TYPE_SUB. |
| userId | Specify the remote user's ID. |

> **注意**
> **Note**
> This interface supports calling before entering the room (enterRoom), and the paused state will be reset after leaving the room (exitRoom).

## muteAllRemoteVideoStreams

#### Suspend/Resume subscription of all remote users' video streams
This interface only pauses/resumes receiving all users' video streams but does not release the display resources. The video image will be frozen on the last frame at the time of the interface call.
| Parameters | Description |
| --- | --- |
| mute | Pause receiving or not. |
> **注意**
> **Note**
> This interface supports calling before entering the room (enterRoom), and the paused state will be reset after leaving the room (exitRoom).

## setVideoEncoderParam

#### Set encoding parameters for the video encoder
This setting determines the screen quality seen by remote users, and also affects the screen quality of the recorded video file on the cloud.
| Parameters | Description |
| --- | --- |
| param | Used to set the video encoder parameters. For details, see TRTCVideoEncParam. |
> **注意**
> **Note**
> Starting from version v11.5, the encoding output resolution will align with width 8 height 2 bytes, and it will be adjusted downwards; eg: input resolution 540x960, actual encoding output resolution 536x960.

## setNetworkQosParam

#### Set network quality control parameters
This setting determines the quality control strategy under poor network conditions, such as "Quality Priority" or "Smoothness Priority" strategies.
| Parameters | Description |
| --- | --- |
| param | Used to set the network quality control parameters. For details, see TRTCNetworkQosParam. |

## setLocalRenderParams

#### Set rendering parameters for the local screen
Configurable parameters include: rotation angle, fill mode, and left-right mirroring of the picture.
| Parameters | Description |
| --- | --- |
| params | Picture render parameters, see TRTCRenderParams for details. |

## setRemoteRenderParams

#### Set rendering mode for the remote screen
Configurable parameters include: rotation angle, fill mode, and left-right mirroring of the picture.
| Parameters | Description |
| --- | --- |
| params | Picture render parameters, see TRTCRenderParams for details. |
| streamType | It can be set as the main road picture (TRTC_VIDEO_STREAM_TYPE_BIG) or the auxiliary stream picture (TRTC_VIDEO_STREAM_TYPE_SUB). |
| userId | Specify the remote user's ID. |

## enableSmallVideoStream
> **平台 API 名称：** Android: `enableEncSmallVideoStream` | iOS: `enableEncSmallVideoStream`

#### Enable picture-in-picture dual-stream encoding mode
After enabling the dual-stream encoding mode, the current user's encoder will output both [HD large screen] and [low-definition small picture] video streams (but only one audio stream).
This way, other users in the room can choose to subscribe to [HD large screen] or [low-definition small screen] based on their network conditions or screen size.
| Parameters | Description |
| --- | --- |
| enable | Whether to enable small picture encoding, default value: false. |
| smallVideoEncParam | Video parameters for the substream. |
#### Return value description:
0: Success; -1: The current main screen has been set to lower quality, making dual-channel encoding unnecessary.

> **注意**
> **Note**
> When the dual-stream encoding mode is enabled, it consumes more CPU and network bandwidth. Therefore, it may be considered for use on Mac, Windows, or high-performance Pads. It is not recommended for mobile devices.

## setRemoteVideoStreamType

#### Switch picture-in-picture for the specified remote user
After a host in the room has enabled dual-channel encoding, the default screen subscribed by other users in the room through startRemoteView will be [HD large screen].
You can use this interface to select whether you want to subscribe to the large screen or small screen. This interface is effective whether called before or after startRemoteView.
| Parameters | Description |
| --- | --- |
| streamType | Video stream type, i.e., choose to watch the large screen or the small screen, default is large screen. |
| userId | Specify the remote user's ID. |
> **注意**
> **Note**

## snapshotVideo

#### Take a video screenshot
You can use this interface to capture the local video footage, the main stream picture of a remote user, and the auxiliary stream (screen sharing) picture of a remote user.
| Parameters | Description |
| --- | --- |
| sourceType | Source of the picture can be chosen as captured video stream (TRTCSnapshotSourceTypeStream), video rendering picture (TRTCSnapshotSourceTypeView), or captured picture (TRTCSnapshotSourceTypeCapture). Captured picture screenshots are clearer. |
| streamType | Video stream type, you can choose to capture the main stream picture (TRTCVideoStreamTypeBig, commonly used for cameras) or the auxiliary stream picture (TRTCVideoStreamTypeSub, commonly used for screen sharing). |
| userId | User ID, if left blank, indicates capturing the local video footage. |
> **注意**
> **Note**

## setGravitySensorAdaptiveMode

#### Set gravity sensing adaptive mode (11.7 and above)
After gravity sensing is enabled, if the collection end device rotates, both the collection end and audience side will render the image accordingly to ensure the image in the view is always upright.
It only takes effect in the SDK internal camera capture scene and only works on the mobile terminal.
1. This interface only affects the collection end. If you are just watching the image in the room, enabling this interface is ineffective
2. When the collection end device rotates 90 degrees or 270 degrees, the image seen by the collection end or audience side may be cropped to maintain aspect ratio coordination
| Parameters | Description |
| --- | --- |
| mode | Gravity sensing mode: for details, please refer to TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_DISABLE, TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_FILL_BY_CENTER_CROP, and TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_FIT_WITH_BLACK_BORDER. Default value: TRTC_GRAVITY_SENSOR_ADAPTIVE_MODE_DISABLE. |

## startLocalAudio

#### Enable collection and publishing of local audio
The SDK does not enable the microphone by default. When users need to publish local audio, they need to call this interface to enable microphone collection and encoding and publish the audio to the current room.
After enabling the collection and publishing of local audio, other users in the room will receive a onUserAudioAvailable(userId, YES) notification.
| Parameters | Description |
| --- | --- |
| quality | Sound quality - TRTCAudioQualitySpeech, Fluent: Sample rate: 16k; Mono; Audio bitrate: 16kbps; Suitable for voice call-dominated scenarios, such as online meetings and voice calls. - TRTCAudioQualityDefault, Default: Sample rate: 48k; Mono; Audio bitrate: 50kbps; The default audio quality of the SDK, recommended unless there are special requirements. - TRTCAudioQualityMusic, High Quality: Sample rate: 48k; Stereo + full band; Audio bitrate: 128kbps; Suitable for high-fidelity music transmission scenarios, such as online KTV and music live streaming. |
> **注意**
> **Note**
> This function checks the microphone permissions. If the current app does not have microphone permissions, the SDK will automatically request microphone permissions from the user.

## stopLocalAudio

#### Stop collection and publishing of local audio
After stopping the collection and publishing of local audio, other users in the room will receive onUserAudioAvailable(userId, false) notification.

## muteLocalAudio

#### Pause/Resume publishing of the local audio stream
After you pause the publication of the local audio stream, other users in the room will receive a notification onUserAudioAvailable(userId, NO).
After you resume the publication of the local audio stream, other users in the room will receive a notification onUserAudioAvailable(userId, YES).
The difference from stopLocalAudio is that muteLocalAudio(YES) does not release the microphone permission but continues to send extremely low bitrate mute packets.
This is very suitable for scenarios that require cloud recording because video files in formats like MP4 demand high continuity of audio data, and using stopLocalAudio will make the recorded MP4 files difficult to play.
Therefore, in scenarios where the quality of the recorded files is highly required, it is recommended to choose muteLocalAudio instead of stopLocalAudio.
| Parameters | Description |
| --- | --- |
| mute | YES: Mute; NO: Resume. |

## muteRemoteAudio

#### Pause/Resume playing of the remote audio stream
When you mute a user's remote audio, the SDK will stop playing the specified user's sound and will also stop pulling that user's audio data.
| Parameters | Description |
| --- | --- |
| mute | true: Mute; false: Unmute. |
| userId | Used to specify the remote user's ID. |
> **注意**
> **Note**

## muteAllRemoteAudio

#### Pause/Resume playing of all remote users' audio streams
When you mute all users' remote audio, the SDK will stop playing all remote audio streams and will also stop pulling all users' audio data.
| Parameters | Description |
| --- | --- |
| mute | true: Mute; false: Unmute. |
> **注意**
> **Note**

## setRemoteAudioVolume

#### Set the playback volume for a specific remote user

You can mute a remote user's sound by using setRemoteAudioVolume(userId, 0).
| Parameters | Description |
| --- | --- |
| userId | Used to specify the remote user's ID. |
| volume | Volume. Value range: 0-100. Default value: 100. |

> **Note**
> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
## setAudioCaptureVolume

#### Sets the local audio capture volume.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0-100. Default value: 100. |

> **Note**
> 

> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
>

## getAudioCaptureVolume

#### Obtains the local audio capture volume.

## setAudioPlayoutVolume

#### Sets the remote audio playback volume.

This interface controls the volume that the SDK finally delivers to the system for playback. The adjustment will affect the volume of the local audio recording file, but it will not affect the volume of the ear return.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0-100. Default value: 100. |

> **Note**
> 

> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
>

## getAudioPlayoutVolume

#### Obtains the remote audio playback volume.

## enableAudioVolumeEvaluation

#### Enable volume reminder
After enabling this feature, the SDK will provide feedback on the local or remote user's audio volume assessment information, including volume level, voice detection, audio spectrum, etc., in the ITRTCCloudCallback callback onUserVoiceVolume.
| Parameters | Description |
| --- | --- |
| enable | Whether to enable volume prompts, disabled by default. |
| params | For volume assessment and other related parameters, please refer to TRTCAudioVolumeEvaluateParams. |

## startAudioRecording

#### Start recording
When you call this interface, the SDK will mix and record all local and remote audio (including local audio, remote audio, background music, and sound effects) into a local file.
This interface can be called both before and after entering the room. If the recording task is not stopped by stopAudioRecording before exiting the room, the task will automatically stop after exiting the room.
The start and completion status of this recording will be notified through local recording-related callbacks. See the TRTCCloud related callbacks.
| Parameters | Description |
| --- | --- |
| param | For recording parameters, please refer to TRTCAudioRecordingParams. |
#### Return value description:
0: Success; -1: Recording has already started; -2: File or directory creation failed; -3: The audio format specified by the extension is not supported.

> **注意**
> **Note**
> Starting from version 11.5, the status results of audio recording are uniformly adjusted to asynchronous callback notifications. See the TRTCCloud related callbacks.

## stopAudioRecording

#### Stop recording

If the recording task is not stopped by this interface before exiting the room, the recording task will automatically stop after exiting the room.

## startLocalRecording

#### Enable local media recording
After enabling, the audio and video content during the live stream will be recorded to a local file.
| Parameters | Description |
| --- | --- |
| params | For recording parameters, please refer to TRTCLocalRecordingParams. |

## stopLocalRecording

#### Stop local media recording

If the recording task is not stopped by this interface before exiting the room, the recording task will automatically stop after exiting the room.

## setRemoteAudioParallelParams

#### Set intelligent concurrent playback strategy for remote audio streams
Set intelligent concurrent playback strategy for remote audio streams, suitable for scenarios with a large number of people on the microphone.
| Parameters | Description |
| --- | --- |
| params | For audio concurrency parameters, please refer to TRTCAudioParallelParams. |

## enable3DSpatialAudioEffect

#### Enable 3D audio
Enable 3D audio effects. Note that smooth audio quality TRTC_AUDIO_QUALITY_SPEECH or default audio quality TRTC_AUDIO_QUALITY_DEFAULT is required.
| Parameters | Description |
| --- | --- |
| enabled | Whether to enable 3D audio effects, disabled by default. |

## updateSelf3DSpatialPosition

#### Set self coordinates and orientation information in 3D audio

Update your position and orientation in the world coordinate system. The SDK will calculate the relative position between yourself and remote users based on the parameters of this method and render spatial audio effects accordingly. Note that each parameter should be passed in as an array of length 3.
| Parameters | Description |
| --- | --- |
| axisForward | The unit vector of the front axis of the local coordinate system in the world coordinate system, with three values representing the front, right, and up coordinate values, respectively. |
| axisRight | The unit vector of the right axis of the local coordinate system in the world coordinate system, with three values representing the front, right, and up coordinate values, respectively. |
| axisUp | The unit vector of the up axis of the local coordinate system in the world coordinate system, with three values representing the front, right, and up coordinate values, respectively. |
| position | Your coordinate in the world coordinate system, with three values representing the front, right, and up coordinate values, respectively. |

> **Note**
> Please appropriately limit the invocation frequency. It is recommended to set coordinates at intervals of at least 100ms.
## updateRemote3DSpatialPosition

#### Set remote user coordinate information in 3D audio

Update the position of remote users in the world coordinate system. The SDK will render spatial audio effects based on the relative position between yourself and the remote users. Note that the parameter is an array of length 3.
| Parameters | Description |
| --- | --- |
| position | The coordinate of the remote user in the world coordinate system, with three values representing the front, right, and up coordinate values, respectively. |
| userId | Specify the remote user's ID. |

> **Note**
> 

> Please appropriately limit the invocation frequency. It is recommended to set coordinates for the same remote user at intervals of at least 100ms.
>

## set3DSpatialReceivingRange

#### Set the receivable range of sounds emitted by a specified user

After setting this range size, the specified user's voice will be audible within this range, and any sound beyond this range will be attenuated to 0.
| Parameters | Description |
| --- | --- |
| range | Maximum range at which the sound can be received. |
| userId | Specify the remote user's ID. |

## getDeviceManager

#### Get Device Management Class (TXDeviceManager)

## setBeautyStyle
> **适用平台：** C++

#### Set special effects like beauty filter, whitening, and rosy skin

The SDK integrates two different skin smoothing algorithms:

-"Smooth": This algorithm is more aggressive, providing a more noticeable smoothing effect, suitable for show live streaming.

-"Natural": This algorithm retains more facial details, offering a more natural smoothing effect, suitable for most live streaming scenarios.
| Parameters | Description |
| --- | --- |
| beautyLevel | Beauty level, value range 0 - 9, 0 means disabled, the greater the value from 1 - 9, the more obvious the effect. |
| ruddinessLevel | Rosy level, value range 0 - 9, 0 means disabled, the greater the value from 1 - 9, the more obvious the effect. |
| style | Skin smoothing algorithm, available in two types: "Smooth" and "Natural". |
| whitenessLevel | Whitening level, value range 0 - 9, 0 means disabled, the greater the value from 1 - 9, the more obvious the effect. |

## setWaterMark
> **平台 API 名称：** Android: `setWatermark` | iOS: `setWatermark`

#### Watermarking
The watermark position is specified by xOffset, yOffset, and fWidthRatio.
-  xOffset: The watermark's coordinate in the range of 0 - 1 as a floating point number.
-  yOffset: The watermark's coordinate in the range of 0 - 1 as a floating point number.
-  fWidthRatio: The watermark's size ratio in the range of 0 - 1 as a floating point number.
| Parameters | Description |
| --- | --- |
| fWidthRatio | The width of the watermark displayed as a proportion of the screen width (the watermark is proportionally scaled according to this parameter). |
| isVisibleOnLocalPreview | true: Watermark displayed in local preview; false: Watermark not displayed in local preview, effective only on win/mac. |
| nHeight | Watermark image pixel height (ignored if source data is file path). |
| nWidth | Watermark image pixel width (ignored if source data is file path). |
| srcData | Watermark image source data (set to nullptr to remove watermark). |
| srcType | Watermark image source data type. |
| streamType | Stream type to apply the watermark (TRTCVideoStreamTypeBig, TRTCVideoStreamTypeSub). |
| xOffset | X-axis offset for the top-left corner of watermark display. |
| yOffset | Y-axis offset for the top-left corner of watermark display. |

> **注意**
> **Note**

## getAudioEffectManager

#### Get Audio Effect Management Class (TXAudioEffectManager)
TXAudioEffectManager is the audio effect management interface. Through this interface, you can implement the following features:
-  Background Music: Supports online and local music, variable speed, pitch shift, and other special effects. Supports native and accompaniment playback as well as loop playback.
-  In-ear monitor feedback: The microphone captures the sound and plays it in real-time through the earphones, commonly used in music live streaming.
-  Reverb Effect: KTV, small room, hall, deep, resonant....
-  Voice changing effect: Lolita, uncle, heavy metal....
-  Short sound effects: short sound effect files such as applause, laughter, etc. (for files less than 10 seconds, please set the isShortFile parameter to true).

## startSystemAudioLoopback

#### Enable System Sound Capture (iOS not yet supported)
This interface captures audio data from the computer's sound card and mixes it into the current audio data stream of the SDK, so that other users in the room can also hear the sound played by the broadcaster's computer.
In online education scenarios, teachers can use this feature to allow the SDK to capture the audio from educational videos and broadcast it to students in the same room.
In music live streaming scenarios, the host can use this feature to allow the SDK to capture audio from the music player to add background music to their live stream.
| Parameters | Description |
| --- | --- |
| deviceName | You can set this parameter to nullptr, indicating the SDK captures the entire system sound. |

> **注意**
> **Note**

## stopSystemAudioLoopback

#### Stop System Sound Capture (iOS not yet supported)

## setSystemAudioLoopbackVolume
> **适用平台：** iOS, C++

#### Sets the Volume of System Sound Capture
| Parameters | Description |
| --- | --- |
| volume | Set the volume level, range: [0 ~ 150], default value is 100. |

## startScreenCapture

#### Start Screen Sharing
This interface supports capturing the entire Android system screen, enabling system-wide screen sharing similar to Tencent Meeting.
Refer to documentation: [Real-time Screen Sharing (Android)](https://cloud.tencent.com/document/product/647/45751)
Recommended screen sharing video encoding parameters for Android (TRTCVideoEncParam):
-  Resolution (videoResolution): 1280×720.
-  Frame Rate (videoFps): 10 FPS.
-  Bitrate (videoBitrate): 1200 kbps.
-  Resolution Adaptation (enableAdjustRes): false.
| Parameters | Description |
| --- | --- |
| encParams | For encoding parameters, please refer to TRTCVideoEncParam. If you set encParams to null, the SDK will automatically use the previously set encoding parameters. |
| shareParams | Please refer to TRTCScreenShareParams. You can pop up a floating window using the floatingView parameter (or use the Android system's WindowManager interface to pop it up yourself). |
| streamType | The circuit used for screen sharing can be set to primary channel (TRTC_VIDEO_STREAM_TYPE_BIG) or secondary channel (TRTC_VIDEO_STREAM_TYPE_SUB). |

> **注意**
> **Note**
> 1. The same user can publish only one main road (TRTCVideoStreamTypeBig) screen and one auxiliary road (TRTCVideoStreamTypeSub) screen at the same time.
> 2. By default, screen sharing uses the auxiliary road. If you use the main road for screen sharing, you need to stop local preview (stopLocalPreview) in advance to avoid conflicts.
> 3. Only one user can use the auxiliary road for screen sharing in the same room at any given time. In other words, only one user can enable the auxiliary road in the same room simultaneously.

## stopScreenCapture

#### Stopping Screen Sharing

## pauseScreenCapture

#### Pause Screen Sharing

> **Note**
> Starting from version 11.5, suspending screen capture will output the last frame at a frame rate of 1fps.
## resumeScreenCapture

#### Resume Screen Sharing

## getScreenCaptureSources
> **平台 API 名称：** iOS: `getScreenCaptureSourcesWithThumbnailSize`
> **适用平台：** iOS, C++

#### Enumerate shareable screens and windows (this interface supports only desktop systems)
When integrating the screen sharing feature on a desktop system, it is usually necessary to display an interface for selecting a sharing target. This allows users to choose whether to share the entire screen or a specific window.
Through this interface, you can query the current system for the ID, name, and thumbnail of available windows for sharing. We provide a default interface implementation in our Demo for your reference.
| Parameters | Description |
| --- | --- |
| iconSize | Specify the size of the window icon to be retrieved. |
| thumbnailSize | Specify the size of the window thumbnail to be retrieved. Thumbnails can be used to render on the window selection interface. |
#### Return value description:
The window list includes the screen.

> **注意**
> **Note**

## selectScreenCaptureTarget
> **适用平台：** iOS, C++

#### Select the screen or window to share (this interface supports only desktop systems)
After you retrieve the shareable screens and windows using getScreenCaptureSources, you can call this interface to select the desired target screen or window for sharing.
During screen sharing, you can also call this interface at any time to switch the sharing target.
Supports the following four cases:
-  Share the entire screen: The type in sourceInfoList is Screen, and captureRect is set to { 0, 0, 0, 0 }
-  Share a specified area: The type in sourceInfoList is Screen, and captureRect is set to a non-nullptr, for example, { 100, 100, 300, 300 }
-  Share the entire window: The type in sourceInfoList is Window, and captureRect is set to { 0, 0, 0, 0 }
-  Share a window area: The type in sourceInfoList is Window, and captureRect is set to a non-nullptr, for example, { 100, 100, 300, 300 }
| Parameters | Description |
| --- | --- |
| captureRect | Specify the capture area |
| property | Specify the properties of the screen sharing target, including capturing the mouse, highlighting the capture window, etc. For details, refer to the TRTCScreenCaptureProperty Definition |
| source | Specifying sharing source |
> **注意**
> **Note**
> Setting highlight border color and width parameter is not effective on Mac platform.

## setSubStreamEncoderParam

#### Set Video Encoding Parameters for Screen Sharing (Sub-Stream) (Supported on Both Desktop and Mobile Systems)
This interface can set the screen sharing (sub-stream) quality seen by remote users, and also determine the screen sharing quality in the cloud-recorded video file.
Please note the differences between the following two interfaces:
- setVideoEncoderParam is used to set video encoding parameters for the primary channel (TRTC_VIDEO_STREAM_TYPE_BIG, typically used for the camera).
- setSubStreamEncoderParam is used to set video encoding parameters for the secondary channel (TRTC_VIDEO_STREAM_TYPE_SUB, typically used for screen sharing).
| Parameters | Description |
| --- | --- |
| param | For auxiliary stream encoding parameters, please refer to TRTCVideoEncParam. |

## setSubStreamMixVolume
> **适用平台：** iOS, C++

#### Set the Mixing Volume for Screen Sharing (This Interface Supports Only Desktop Systems)

The higher this value, the higher the proportion of screen sharing volume, and the lower the proportion of microphone volume. Therefore, it is not recommended to set it too high, as it will suppress the microphone sound.
| Parameters | Description |
| --- | --- |
| volume | Set the mix volume level, ranging from 0 to 100. |

## addExcludedShareWindow
> **适用平台：** iOS, C++

#### Add the Specified Window to the Exclusion List of Screen Sharing (This Interface Supports Only Desktop Systems)
Windows added to the exclusion list will not be shared. A common use case is to add a window from an application to the exclusion list to avoid privacy issues.
Support setting filter windows before starting screen sharing, and also dynamically adding filter windows during screen sharing.
| Parameters | Description |
| --- | --- |
| windowID | Windows that you do not want to share |
> **注意**
> **Note**
> 1. This interface only takes effect when the type in TRTCScreenCaptureSourceInfo is specified as TRTCScreenCaptureSourceTypeScreen, meaning the exclusion feature only works when sharing the entire screen content.
> 2. Windows added to the exclusion list using this interface will be automatically cleared by the SDK upon exiting the room.
> 3. On Mac platform, please pass in the window ID (i.e., CGWindowID), which can be obtained from the sourceId member in TRTCScreenCaptureSourceInfo.

## removeExcludedShareWindow
> **适用平台：** iOS, C++

#### Remove the Specified Window from the Exclusion List of Screen Sharing (This Interface Supports Only Desktop Systems)
| Parameters | Description |
| --- | --- |
| windowID | The window ID to be excluded. |

## removeAllExcludedShareWindow
> **平台 API 名称：** iOS: `removeAllExcludedShareWindows`
> **适用平台：** iOS, C++

#### Remove All Windows from the Exclusion List of Screen Sharing (This Interface Supports Only Desktop Systems)

## addIncludedShareWindow
> **适用平台：** iOS, C++

#### Add the Specified Window to the Contains List of Screen Sharing (This Interface Supports Only Desktop Systems)
This interface only takes effect when the type in TRTCScreenCaptureSourceInfo is specified as TRTCScreenCaptureSourceTypeWindow. This means that the feature of including an additional specified window is only effective when sharing window content.
You can call it both before and after startScreenCapture.
| Parameters | Description |
| --- | --- |
| windowID | Windows desired to be shared (Window Handle: HWND on Windows platform) |
> **注意**
> **Note**
> Windows added to the inclusion list through this method will be automatically cleared by the SDK upon exiting the room.

## removeIncludedShareWindow
> **适用平台：** iOS, C++

#### Remove the Specified Window from the Contains List of Screen Sharing (This Interface Supports Only Desktop Systems)
This interface only takes effect when the type in TRTCScreenCaptureSourceInfo is specified as TRTCScreenCaptureSourceTypeWindow.
This means that the feature of including an additional specified window is only effective when sharing window content.
| Parameters | Description |
| --- | --- |
| windowID | Windows desired to be shared (Window ID on Mac platform; HWND on Windows platform) |

## removeAllIncludedShareWindow
> **平台 API 名称：** iOS: `removeAllIncludedShareWindows`
> **适用平台：** iOS, C++

#### Remove All Windows from the Contains List of Screen Sharing (This Interface Supports Only Desktop Systems)
This interface only takes effect when the type in TRTCScreenCaptureSourceInfo is specified as TRTCScreenCaptureSourceTypeWindow.
This means that the feature of including an additional specified window is only effective when sharing window content.

## enableCustomVideoCapture

#### Enable/Disable Custom Video Capturing Mode
After enabling this mode, the SDK will stop running the original video capture process. It will no longer collect data from the camera and apply beauty filters, retaining only video encoding and sending capabilities.
You need to continuously send self-collected video frames to the SDK via sendCustomVideoData.
| Parameters | Description |
| --- | --- |
| enable | Whether to enable, default value: false. |
| streamType | Used to specify video stream type, TRTC_VIDEO_STREAM_TYPE_BIG: high-definition large image; TRTC_VIDEO_STREAM_TYPE_SUB: secondary channel image. |

## sendCustomVideoData

#### Submit Self-collected Video Frames to the SDK
This interface allows you to submit self-collected video frames to the SDK. The SDK will encode the video frames and transmit them through its network module.
Parameter TRTCVideoFrame is recommended to be filled as follows (other fields do not need to be filled):
-  pixelFormat: Windows and Android platforms only support TRTCVideoPixelFormat_I420; iOS and Mac platforms support TRTCVideoPixelFormat_I420 and TRTCVideoPixelFormat_BGRA32.
-  bufferType: Recommended choice is TRTCVideoBufferType_Buffer.
-  data: Buffer used to carry video frame data.
-  length: Video frame data length. If pixelFormat is set to I420 format, length can be calculated as follows: length = width × height × 3 / 2.
-  width: Video image width, such as 640 px.
-  height: Video image height, such as 480 px.
-  timestamp: Timestamp, in milliseconds (ms). Please use the timestamp recorded when the video frame is captured (you can get the timestamp by calling generateCustomPTS after capturing a video frame).
Refer to documentation: [Custom Collection and Rendering](https://cloud.tencent.com/document/product/647/34066).
| Parameters | Description |
| --- | --- |
| frame | Video data, supports I420 format data. |
| streamType | Used to specify the video stream type,TRTCVideoStreamTypeBig: HD large screen;TRTCVideoStreamTypeSub: Auxiliary stream. |
> **注意**
> **Note**
> 1. It is recommended to call the generateCustomPTS interface to get the timestamp value of the frame after capturing a video frame, to achieve the best audio-video synchronization.
> 2. The final frame rate of the encoded video by the SDK is determined by the FPS you set in setVideoEncoderParam, not by the frequency at which you call this interface.
> 3. Please try to keep the intervals between calls to this interface uniform. Otherwise, it may cause unstable encoder output frame rates or audio-video asynchrony.

## enableCustomAudioCapture

#### Enable Custom Audio Capturing Mode
After enabling this mode, the SDK will no longer run the original audio capture process, meaning it will stop capturing audio data from the microphone and only retain audio encoding and sending capabilities.
You need to continuously feed the SDK with your own collected audio data through sendCustomAudioData.
| Parameters | Description |
| --- | --- |
| enable | Whether to enable, default value: false. |
> **注意**
> **Note**
> As Echo Cancellation (AEC) requires strict control over the timing of audio capture and playback, enabling custom audio capture may result in AEC capabilities becoming ineffective.

## sendCustomAudioData

#### Submit Self-collected Audio Data to the SDK
Parameter TRTCAudioFrame is recommended to be filled as follows (other fields do not need to be filled):
-  audioFormat: Audio data format, only supports TRTCAudioFrameFormatPCM.
-  data: Audio frame buffer. Audio frame data only supports PCM format, with frame lengths ranging from [5ms ~ 100ms]. A frame length of 20ms is recommended. Calculation method: [48000 sampling rate, mono channel frame length: 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes].
-  sampleRate: Sampling rate, supported values are 16000, 24000, 32000, 44100, 48000.
-  channel: Number of channels (if stereo, data is interleaved), mono: 1; stereo: 2.
-  timestamp: Timestamp, in milliseconds (ms). Please use the timestamp recorded when the audio frame is captured (you can get the timestamp by calling generateCustomPTS after capturing an audio frame).
Refer to documentation: [Custom Collection and Rendering](https://cloud.tencent.com/document/product/647/74692).
| Parameters | Description |
| --- | --- |
| frame | Audio Data |
> **注意**
> **Note**
> Please call this interface accurately at intervals corresponding to the duration of each frame. Feeding data at uneven intervals is likely to cause audio stutter.

## enableMixExternalAudioFrame

#### Enable/Disable Custom Audio Track
Once enabled, you can use this interface to mix a Custom Audio Track into the SDK. With two boolean parameters, you can control whether the track should play remotely and locally.
| Parameters | Description |
| --- | --- |
| enablePlayout | Control whether the mixed audio track should be played locally. Default value: false. |
| enablePublish | Control whether the mixed audio track should be played remotely. Default value: false. |
> **注意**
> **Note**

## mixExternalAudioFrame

#### Mix Custom Audio Track into the SDK
Before calling this interface, you need to enable the Custom Audio Track with enableMixExternalAudioFrame first. After that, you can use this interface to mix your own audio track into the SDK in PCM format.
Ideally, we expect your code to provide track data to the SDK at a very consistent rate. However, we understand that maintaining perfect call intervals is a significant challenge.
Therefore, the SDK will initiate a buffer for audio track data internally. This buffer acts like a "reservoir", capable of temporarily storing the audio track data you input, smoothing out the jitter caused by intervals between interface calls.
The return value of this interface represents the size of this track buffer zone, measured in milliseconds (ms). For example: if this interface returns 50, it means the current track buffer zone has 50ms worth of track data. Therefore, as long as you call this interface again within 50ms, the SDK can ensure the continuity of your mixed track data.
When you call this interface, if you find the return value > 100ms, you can wait for one frame of audio playback time before calling again; if the return value < 100ms, it means the buffer is relatively small, you can mix in some more audio track data to ensure the size of the audio track buffer remains above the "safe level".
Parameter TRTCAudioFrame is recommended to be filled as follows (other fields do not need to be filled):
-  data: Audio frame buffer. Audio frame data only supports PCM format, with frame lengths ranging from [5ms ~ 100ms]. A frame length of 20ms is recommended. Calculation method: [48000 sampling rate, mono channel frame length: 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes].
-  sampleRate: Sampling rate, supported values are 16000, 24000, 32000, 44100, 48000.
-  channel: Number of channels (if stereo, data is interleaved), mono: 1; stereo: 2.
-  timestamp: Timestamp, in milliseconds (ms). Please use the timestamp recorded when the audio frame is captured (you can get the timestamp by calling generateCustomPTS after capturing an audio frame).
| Parameters | Description |
| --- | --- |
| frame | Audio Data |
#### Return value description:
>= 0 Buffered length, unit: ms. < 0 Error (-1 mixExternalAudioFrame not enabled)

> **注意**
> **Note**
> Please call this interface accurately at intervals corresponding to the duration of each frame. Feeding data at uneven intervals is likely to cause audio stutter.

## setMixExternalAudioVolume

#### Set the Streaming Volume and Playback Volume for External Audio Mixed During Streaming
| Parameters | Description |
| --- | --- |
| playoutVolume | Set the playback volume level, ranging from 0 to 100. -1 means no change. |
| publishVolume | Set the streaming volume level, ranging from 0 to 100. -1 means no change. |

## generateCustomPTS

#### Generate Timestamp for Custom Capturing
This interface is only applicable to custom capturing mode, used to solve the issue of audio and video out of sync caused by the discrepancy between capture time and delivery time.
When you use interfaces such as sendCustomVideoData or sendCustomAudioData for custom video or audio capturing, please follow these steps to use this interface:
1. First, when capturing a video or audio frame, call this interface to obtain the PTS timestamp of that moment.
2. Then, you can send the video or audio frame to the preprocessing module you use (such as third-party beauty components or third-party audio effect components).
3. When actually calling sendCustomVideoData or sendCustomAudioData for streaming, please assign the PTS timestamp recorded during capture to the timestamp field in TRTCVideoFrame or TRTCAudioFrame.
#### Return value description:
Timestamp (unit: ms)

## setLocalVideoCustomProcessCallback
> **平台 API 名称：** Android: `setLocalVideoProcessListener` | iOS: `setLocalVideoProcessDelegete`

#### Set Video Data Callback for Third-party Beauty Filter
After setting this callback, the SDK will pass the collected video frames to the listener you set for third-party beauty filter components for secondary processing. After that, the SDK will encode and send the processed video frames.
| Parameters | Description |
| --- | --- |
| bufferType | Specify the data format for the callback. Currently supported: - TRTC_VIDEO_BUFFER_TYPE_TEXTURE: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_Texture_2D. - TRTC_VIDEO_BUFFER_TYPE_BYTE_BUFFER: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_I420. - TRTC_VIDEO_BUFFER_TYPE_BYTE_ARRAY: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_I420. |
| listener | Custom pre-processing callback, see TRTCVideoFrameListener for details. |
| pixelFormat | Specify the pixel format for the callback. Currently supported: - TRTC_VIDEO_PIXEL_FORMAT_Texture_2D: Graphics Memory Texture Solution. - TRTC_VIDEO_PIXEL_FORMAT_I420: Memory Data Solution. |
#### Return value description:
0: Success; < 0: Error

## setLocalVideoRenderCallback
> **平台 API 名称：** Android: `setLocalVideoRenderListener` | iOS: `setLocalVideoRenderDelegate`

#### Set Local Video Custom Rendering Callback
After setting this callback, the SDK will skip the original rendering process and return the captured data. You need to handle the rendering yourself.
-  pixelFormat specifies the data format for the callback. Currently supports Texture2D, I420, and RGBA formats.
-  bufferType specifies the type of buffer. BYTE_BUFFER is suitable for use in the JNI layer, while BYTE_ARRAY can be used for direct operations in the Java layer.
Refer to documentation: [Custom Collection and Rendering](https://cloud.tencent.com/document/product/647/34066).
| Parameters | Description |
| --- | --- |
| bufferType | Specify the data structure of the video frame: - TRTC_VIDEO_BUFFER_TYPE_TEXTURE: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_Texture_2D. - TRTC_VIDEO_BUFFER_TYPE_BYTE_BUFFER: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_I420 and TRTC_VIDEO_PIXEL_FORMAT_RGBA. - TRTC_VIDEO_BUFFER_TYPE_BYTE_ARRAY: Suitable when pixelFormat is set to TRTC_VIDEO_PIXEL_FORMAT_I420 and TRTC_VIDEO_PIXEL_FORMAT_RGBA. |
| listener | Custom video rendering callback, each video frame triggers a callback. |
| pixelFormat | Specify the pixel format of the video frame, such as: - TRTC_VIDEO_PIXEL_FORMAT_Texture_2D: OpenGL texture format, more suitable for GPU processing, high processing efficiency. - TRTC_VIDEO_PIXEL_FORMAT_I420: Standard I420 format, more suitable for CPU processing, low processing efficiency. - TRTC_VIDEO_PIXEL_FORMAT_RGBA: RGBA format, more suitable for CPU processing, low processing efficiency. |
#### Return value description:
0: Success; < 0: Error.

## setRemoteVideoRenderCallback
> **平台 API 名称：** Android: `setRemoteVideoRenderListener` | iOS: `setRemoteVideoRenderDelegate`

#### Sets the rendering callback for remote video self-definition
After setting this callback, the SDK will skip the original rendering process and return the captured data. You need to handle the rendering yourself.
-  pixelFormat specifies the data format for the callback, such as NV12, i420, and 32BGRA.
-  bufferType specifies the type of buffer. Using PixelBuffer has the highest efficiency; using NSData involves an internal memory conversion within the SDK, resulting in additional performance loss.
Refer to documentation: [Custom Collection and Rendering](https://cloud.tencent.com/document/product/647/34066).
| Parameters | Description |
| --- | --- |
| bufferType | Specify the data structure of the video frame, such as TRTC_VIDEO_BUFFER_TYPE_BYTE_BUFFER, TRTC_VIDEO_BUFFER_TYPE_BYTE_ARRAY. |
| listener | Custom video rendering callback, each video frame triggers a callback. |
| pixelFormat | Specify the pixel format of the video frame. Currently supports TRTC_VIDEO_PIXEL_FORMAT_I420, TRTC_VIDEO_PIXEL_FORMAT_RGBA. |
| userId | Specify the remote user's ID. |
#### Return value description:
0: Success; < 0: Error.

> **注意**
> **Note**

## setAudioFrameCallback
> **平台 API 名称：** Android: `setAudioFrameListener` | iOS: `setAudioFrameDelegate`

#### Sets the audio data self-definition callback
After setting this callback, the SDK will internally callback the audio data (PCM format), including:
- onCapturedAudioFrame: Audio data callback captured by the local microphone
- onLocalProcessedAudioFrame: Audio data callback captured locally and preprocessed by the audio module
- onRemoteUserAudioFrame: Audio data of each remote user before mixing
- onMixedPlayAudioFrame: Audio data callback after mixing various audio streams and finally to be played by the system
> **注意**
> **Note**
> Setting the callback to null means stopping the Definition audio callback; vice versa, setting the callback to non-null means starting the Definition audio callback.

## setCapturedAudioFrameCallbackFormat
> **平台 API 名称：** iOS: `setCapturedAudioFrameDelegateFormat`

#### Sets the callback format for audio frames captured by the local microphone
This interface is used to set the format of the AudioFrame callback for onCapturedAudioFrame:
-  sampleRate: Sampling rate, supported values are 16000, 32000, 44100, 48000.
-  channel: Number of channels (if stereo, data is interleaved), mono: 1; stereo: 2.
-  samplesPerCall: Number of sampling points, Definition callback data frame length. The frame length must be an integer multiple of 10ms.
If you want to calculate the callback frame length in milliseconds, the formula to convert milliseconds to sampling points is: sampling points = milliseconds * sampling rate / 1000.
Example: For a 48000 sampling rate and a desired callback frame length of 20ms, the number of sampling points should be: 960 = 20 * 48000 / 1000.
| Parameters | Description |
| --- | --- |
| format | Audio Data Callback Format. |
#### Return value description:
0: Success; <0: Error

> **注意**
> **Note**
> The final callback frame length is in bytes. The formula to convert sampling points to bytes is: Byte Count = Sampling Points * Channels * 2 (Bit Width). Example: For a 48000 sampling rate, stereo, and 20ms frame length, the number of sampling points is 960, and the byte count is 3840 = 960 * 2 * 2

## setLocalProcessedAudioFrameCallbackFormat
> **平台 API 名称：** iOS: `setLocalProcessedAudioFrameDelegateFormat`

#### Sets the callback format for local audio frames after preprocessing
This interface is used to set the format of the AudioFrame callback for onLocalProcessedAudioFrame:
-  sampleRate: Sampling rate, supported values are 16000, 32000, 44100, 48000.
-  channel: Number of channels (if stereo, data is interleaved), mono: 1; stereo: 2.
-  samplesPerCall: Number of sampling points, Definition callback data frame length. The frame length must be an integer multiple of 10ms.
If you want to calculate the callback frame length in milliseconds, the formula to convert milliseconds to sampling points is: sampling points = milliseconds * sampling rate / 1000.
Example: For a 48000 sampling rate and a desired callback frame length of 20ms, the number of sampling points should be: 960 = 20 * 48000 / 1000.
| Parameters | Description |
| --- | --- |
| format | Audio Data Callback Format. |
#### Return value description:
0: Success; <0: Error

> **注意**
> **Note**
> The final callback frame length is in bytes. The formula to convert sampling points to bytes is: Byte Count = Sampling Points * Channels * 2 (Bit Width). Example: For a 48000 sampling rate, stereo, and 20ms frame length, the number of sampling points is 960, and the byte count is 3840 = 960 * 2 * 2

## setMixedPlayAudioFrameCallbackFormat
> **平台 API 名称：** iOS: `setMixedPlayAudioFrameDelegateFormat`

#### Sets the callback format for audio frames that will eventually be played by the system
This interface is used to set the format of the AudioFrame callback for onMixedPlayAudioFrame:
-  sampleRate: Sampling rate, supported values are 16000, 32000, 44100, 48000.
-  channel: Number of channels (if stereo, data is interleaved), mono: 1; stereo: 2.
-  samplesPerCall: Number of sampling points, Definition callback data frame length. The frame length must be an integer multiple of 10ms.
If you want to calculate the callback frame length in milliseconds, the formula to convert milliseconds to sampling points is: sampling points = milliseconds * sampling rate / 1000.
Example: For a 48000 sampling rate and a desired callback frame length of 20ms, the number of sampling points should be: 960 = 20 * 48000 / 1000.
| Parameters | Description |
| --- | --- |
| format | Audio Data Callback Format. |
#### Return value description:
0: Success; <0: Error

> **注意**
> **Note**
> The final callback frame length is in bytes. The formula to convert sampling points to bytes is: Byte Count = Sampling Points * Channels * 2 (Bit Width). Example: For a 48000 sampling rate, stereo, and 20ms frame length, the number of sampling points is 960, and the byte count is 3840 = 960 * 2 * 2

## enableCustomAudioRendering

#### Enable self-definition audio playback
If you need to connect specific audio devices or want to control the audio playback logic yourself, you can enable Custom Playback through this interface.
After enabling Custom Playback, the SDK will no longer use the system's audio interface to play data. You need to get the audio frame that the SDK wants to play through getCustomAudioRenderingFrame and play it yourself.
| Parameters | Description |
| --- | --- |
| enable | Whether to enable Custom Playback is off by default. |
> **注意**
> **Note**
> You need to set it before entering the room for it to take effect. It is not supported to set it after entering the room.

## getCustomAudioRenderingFrame

#### Get playable audio data
Before calling this interface, you need to enable Custom Playback through enableCustomAudioRendering first.
Parameter TRTCAudioFrame is recommended to be filled as follows (other fields do not need to be filled):
-  sampleRate: Sampling rate, required, supported values are 16000, 24000, 32000, 44100, 48000.
-  channel: Number of channels, required. Fill in 1 for mono, 2 for stereo. The data is interleaved for stereo.
-  data: Buffer for obtaining audio data. You need to allocate the memory size of data according to the frame length of an audio frame. The obtained PCM data supports frame lengths of 10ms or 20ms. It is recommended to use a frame length of 20ms.
Calculation formula: Sampling rate x Playback duration x Number of channels x 2 (bytes per sample). For example, for a frame of audio with a sampling rate of 48000, mono channel, and a playback duration of 20ms, the buffer size is 48000 × 0.02s × 1 × 16bit = 15360bit = 1920 bytes.
| Parameters | Description |
| --- | --- |
| audioFrame | Audio data frame. |
> **注意**
> **Note**
> 1. Parameters sampleRate and channel in audioFrame should be set in advance, and the data space required for the frame length to be read should be allocated.
> 2. The SDK will automatically fill the data according to sampleRate and channel.
> 3. It is recommended to call this function driven by the system's audio playback thread. After playing one frame of audio, call this API to get the next playable frame of audio data.

## sendCustomCmdMsg

#### Sends a custom message using UDP channel to all users in a room
This interface allows you to use TRTC's UDP Channel to broadcast custom data to other users in the room, achieving the purpose of transmitting signaling.
Other users in the room can receive messages through the onRecvCustomCmdMsg callback in ITRTCCloudCallback.
| Parameters | Description |
| --- | --- |
| cmdID | Message ID, the value range is 1 - 10. |
| data | Message to be sent, the maximum length of a single message is limited to 1KB. |
| ordered | Whether orderly sending is enabled, i.e., whether the order of data packets at the receiver matches the order at the sender (this will cause some reception delay). |
| reliable | Whether reliable transmission is enabled. Reliable transmission offers a higher success rate but will cause higher reception delays compared to unreliable transmission. |
#### Return value description:
true: Message has been sent; false: Message sending failed.

> **注意**
> **Note**
> 1. Sending messages to all users in the room (currently not supporting Web/Mini program end), up to 30 messages per second.
> 2. Each data packet can be up to 1KB. If it exceeds this size, it is very likely to be discarded by intermediate routers or servers.
> 3. Each client can send up to 8 KB of data per second.
> 4. It is strongly recommended to set different types of messages with different cmdIDs. This can reduce message latency when ordering is required.
> 5. Currently, only anchor roles are supported.

## sendSEIMsg

#### Sends a custom message using SEI channel to all users in a room
This interface allows you to broadcast self-defined data to other users in the current room using TRTC's SEI channel to achieve signaling transmission.
There is a header data block called SEI in the video frame header. The principle of this interface is to use this SEI header data block to embed the self-defined signaling you want to send, so it is sent out along with the video frame.
Therefore, compared to sendCustomCmdMsg, signaling transmitted via the SEI channel has better compatibility: the signaling can be transmitted along with the video frame all the way to the live CDN.
However, because the data block in the video frame header cannot be too large, it is recommended to control the signaling size to just a few bytes when using this interface.
The most common usage is to embed a self-defined timestamp (timestamp) into the video frame using this interface to achieve perfect alignment of messages and screen (e.g., in educational scenarios for aligning courseware and video signals).
Other users in the room can receive messages through the onRecvSEIMsg callback in ITRTCCloudCallback.
| Parameters | Description |
| --- | --- |
| data | Data to be sent, which can contain up to 1 KB (1,000 bytes) of data |
| repeatCount | Number of data sends |
#### Return value description:
true: Message has passed the restriction, waiting for subsequent video frame to be sent; false: Message is restricted from being sent

> **注意**
> **Note**
> This interface has the following limitations:
> 1. Data will not be sent immediately after the API invocation, but will be sent along with the next video frame.
> 2. Send messages to all users in the room, up to 30 messages per second (shared limit with sendCustomCmdMsg).
> 3. Each package is a maximum of 1 KB. Sending large amounts of data will increase the video bitrate and possibly cause reduced image quality or lag (shared limit with sendCustomCmdMsg).
> 4. Each client can send up to 8 KB of data per second (shared limit with sendCustomCmdMsg).
> 5. If multiple sends are specified (repeatCount > 1), the data will be sent with the subsequent repeatCount number of video frames, which will also increase the video bitrate.
> 6. If repeatCount > 1 and there are multiple sends, the onRecvSEIMsg callback may receive the same message multiple times, and deduplication is needed.

## startSpeedTest

#### Start internet speed test (use before entering the room)
| Parameters | Description |
| --- | --- |
| params | Speed Test Options |

> **Note**
> 1. The speed test process will incur a small basic service fee. For details, refer to [Billing Overview > Basic Service](https://cloud.tencent.com/document/product/647/17157#.E5.9F.BA.E7.A1.80.E6.9C.8D.E5.8A.A1) documentation.
> 2. Please perform the internet speed test before entering the room. Conducting the test in the room will affect normal audio and video transmission and will also be inaccurate due to too much interference.
> 

> 3. Only one internet speed test task is allowed to run at the same time.
> 

#### Return value description:

API Invocation Result, < 0: Failure

## stopSpeedTest

#### Stops network speed testing

## getSDKVersion

#### Obtains the SDK version.

## setLogLevel

#### Sets the log output level.
| Parameters | Description |
| --- | --- |
| level | Refer to TRTCLogLevel, default value: TRTCLogLevelNone |

## setConsoleEnabled

#### Enables/Disables console log print.
| Parameters | Description |
| --- | --- |
| enabled | Specify whether to enable it. Default: Disabled. |

## setLogCompressEnabled

#### Enables/Disables local log compression

After enabling compression, the log storage volume is significantly reduced, but you need to use a Python script provided by Tencent Cloud to decompress and read it.

After disabling compression, logs are stored in plaintext, which can be opened and read directly with Notepad but occupy more space.
| Parameters | Description |
| --- | --- |
| enabled | Specify whether to enable it. Default: Enabled |

## setLogDirPath

#### Sets the save path for local logs

This interface allows you to change the default storage path of SDK local logs. The default local log storage locations are:
-  Windows platform: C:/Users/[system username]/AppData/Roaming/liteav/log, i.e., %appdata%/liteav/log.

-  iOS or Mac platform: sandbox Documents/log.

-  Android platform: /app private directory/files/log/liteav/.

| Parameters | Description |
| --- | --- |
| path | Storage path for logs |

> **Note**
> 

> Make sure to call this before all other interfaces and ensure that the specified directory exists and that your application has read and write permissions for that directory.
>

## setLogCallback
> **平台 API 名称：** Android: `setLogListener` | iOS: `setLogDelegate`

#### Log callback

## showDebugView

#### Show Dashboard

The "Dashboard" is a semi-transparent debug information overlay located above the video rendering control, used to display audio and video information and event information, aiding in integration and debugging.
| Parameters | Description |
| --- | --- |
| showType | 0: Do not display; 1: Display simplified edition (audio and video information only); 2: Display full version (including audio and video information and event information). |

## callExperimentalAPI

#### Calls an experimental API.

## enablePayloadPrivateEncryption

#### Enable or disable media stream private encryption
In scenarios with high security requirements, TRTC recommends calling the enablePayloadPrivateEncryption method to enable media stream private encryption before joining a room.
After the user leaves the room, the SDK will automatically disable private encryption. If you need to re-enable private encryption, you must call this method before the user rejoins the room.
| Parameters | Description |
| --- | --- |
| config | For configuring the media stream private encryption algorithm and key, refer to TRTCPayloadPrivateEncryptionConfig. |
| enabled | Whether to enable media stream private encryption. |
#### Return value description:
API invocation results: 0: Method call successful, -1: Invalid input parameter, -2: Feature expired. To unlock: Please visit the Chinese mainland site to activate the [TRTC Enterprise Edition Package](https://buy.cloud.tencent.com/trtc?tab=month&trtcversion=ultimate), and submit an [application](https://cloud.tencent.com/apply/p/diifjcmffhw). You can use it upon approval.

> **注意**
> **Note**
> TRTC already encrypts media streams before transmission. When media stream private encryption is enabled, it will use the key and initialization vector you provided for additional encryption.

## callbackQueue
> **平台 API 名称：** Android: `setListenerHandler` | iOS: `delegateQueue`
> **适用平台：** Android, iOS

#### Set the queue to drive TRTCCloudListener event callbacks
If you do not specify your own listenerHandler, MainQueue will be used as the default queue to drive TRTCCloudListener event callbacks.
This means that if you do not set the listenerHandler attribute, all callback functions in TRTCCloudListener are driven by MainQueue.
| Parameters | Description |
| --- | --- |
| listenerHandler | Event callback handler |
> **注意**
> **Note**

## setPerspectiveCorrectionPoints
> **适用平台：** Android, iOS

#### Set perspective correction coordinate settings for the video screen

You can specify the coordinate area for perspective correction of the rendered image through this interface.
| Parameters | Description |
| --- | --- |
| dstPoints | The target coordinates area to be corrected to, consists of 4 vertex coordinates. They need to be passed in the order of top-left, bottom-left, top-right, and bottom-right. All coordinates need to be normalized to the [0,1] range based on the image width and height; passing null will stop the perspective correction. |
| srcPoints | The original image coordinate area consists of 4 vertex coordinates. They need to be passed in the order of top-left, bottom-left, top-right, and bottom-right. All coordinates need to be normalized to the [0,1] range based on the image width and height; passing null will stop the perspective correction. |
| userId | User ID, if left blank, indicates correcting the local video footage. |

## setAudioRoute
> **适用平台：** Android, iOS

#### Sets audio routing.
Set "audio routing", i.e., whether the sound is played through the mobile phone's speaker or earpiece. Therefore, this interface is only applicable to mobile devices such as phones.
A mobile phone has two speakers: one is the earpiece located at the top of the phone, and the other is the stereo speaker located at the bottom.
When the audio routing is set to the earpiece, the sound is relatively low, and you need to bring your ear close to hear it clearly, ensuring better privacy. It is suitable for phone calls.
When the audio routing is set to the speaker, the sound is relatively loud. You can hear it clearly without holding the phone to your face, thus enabling the "hands-free" feature.
| Parameters | Description |
| --- | --- |
| route | Audio routing refers to where the sound is output (speaker, earpiece). Default value: TRTCAudioModeSpeakerphone. |

## getBeautyManager
> **适用平台：** Android, iOS

#### Get Beauty Management Class (TXBeautyManager)

Through beauty management, you can use the following features:
-  Set beauty effects such as "Skin Smoothing", "Whitening", "Rosy".

-  Set facial enhancement effects like "Big Eyes", "Slim Face", "V Face", "Chin", "Short Face", "Small Nose", "Bright Eyes", "White Teeth", "Remove Eye Bags", "Remove Wrinkles", "Remove Nasolabial Folds".

-  Set facial enhancement effects such as "Hairline", "Eye Distance", "Eye Tilt", "Mouth Shape", "Nasal Sides", "Nose Position", "Lip Thickness", "Face Shape".

-  Set makeup effects like "Eyeshadow", "Blush".

-  Set dynamic stickers and face pendants for animation effects.

## TRTCViewMargin
> **适用平台：** Android, iOS

#### Set the dashboard margin

Used to adjust the position of the dashboard within the video rendering control. Must be called before showDebugView to take effect.
| Parameters | Description |
| --- | --- |
| margin | Dashboard inner padding. Note that here it is based on the percentage of the parentView. Margin value range is 0 - 1. |
| userId | User ID. |

## startScreenCaptureInApp
> **适用平台：** iOS

#### Start In-App Screen Sharing (Supports only iOS 13.0 and above)

This interface will capture the real-time screen content of the current application and share it with other users in the same room. It is applicable to iOS systems 13.0 and above.

If you want to capture the entire iOS system's screen content (without being limited to the current application), it is recommended to use startScreenCaptureByReplaykit.

Recommended screen sharing video encoding parameters for iPhone (TRTCVideoEncParam):
-  Resolution (videoResolution): 1280 x 720.

-  Frame Rate (videoFps): 10 FPS.

-  Bitrate (videoBitrate): 1600 kbps.

-  Resolution adaptation (enableAdjustRes): NO.

| Parameters | Description |
| --- | --- |
| encParams | Set the video encoding parameters for screen sharing, it is recommended to use the above recommended configuration. If you specify encParams as nil, the SDK will use the video encoding parameters set before calling the startScreenCapture interface. |
| streamType | Screen sharing stream can be set to the main stream (TRTCVideoStreamTypeBig) or the substream (TRTCVideoStreamTypeSub). |

## startScreenCaptureByReplaykit
> **适用平台：** iOS

#### Start Full-System Screen Sharing (Supports only iOS 11.0 and above)

This interface supports capturing the entire iOS system screen, facilitating system-wide screen sharing similar to Tencent Meeting.

However, the integration process is slightly more complex than startScreenCaptureInApp, requiring a ReplayKit extension module for your application.

Reference document:[Real-time screen sharing (iOS)](https://cloud.tencent.com/document/product/647/45750)

Recommended screen sharing video encoding parameters for iPhone (TRTCVideoEncParam):
-  Resolution (videoResolution): 1280 x 720.

-  Frame Rate (videoFps): 10 FPS.

-  Bitrate (videoBitrate): 1600 kbps.

-  Resolution adaptation (enableAdjustRes): NO.

| Parameters | Description |
| --- | --- |
| appGroup | Specify the Application Group Identifier that your application and recording process share. You can specify this parameter as nil, but it is recommended to set it as instructed in the document for better reliability. |
| encParams | Set the video encoding parameters for screen sharing. It is recommended to use the above configuration. If you specify encParams as nil, the SDK will use the video encoding parameters set before calling the startScreenCapture interface. |
| streamType | Screen sharing stream can be set to the main stream (TRTCVideoStreamTypeBig) or the substream (TRTCVideoStreamTypeSub). |
