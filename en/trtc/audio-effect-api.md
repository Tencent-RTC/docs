> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXAudioEffectManager 音效管理 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXAudioEffectManager 音效管理 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TXAudioEffectManager
## ITXMusicPlayObserver
> **适用平台：** C++

| Function List | Description |
| --- | --- |
| onStart | Start playing background music |
| onPlayProgress | Background music playback progress |
| onComplete | Background music playback completed |

## Struct
> **适用平台：** Android, C++

| Function List | Description |
| --- | --- |
| AudioMusicParam | Playback control information for background music |

## Enumeration

| Enumeration Types | Description |
| --- | --- |
| TXVoiceReverbType | Reverb effects |
| TXVoiceChangerType | Voice changing effects |

## onLoadProgress
> **适用平台：** Android, C++

#### Background music preloading progress

## onLoadError
> **适用平台：** Android, C++

#### Error in background music preloading
| Parameters | Description |
| --- | --- |
| errorCode | -4001: Failed to open file due to invalid audio data or FFmpeg protocol not found, etc.; -4002: Decoding failed due to corrupted audio file or inaccessible network audio file server, etc.; -4003: Preloading quantity exceeds the limit. Please call stopPlayMusic to release unused preloads first; -4005: Failed to open file due to invalid path. Please check if your path parameter points to a valid music file; -4006: Failed to open file due to illegal URL. Please check if the URL provided can download the expected music file using a browser; -4007: Failed to open file due to no audio stream. Please confirm if the file you provided is a legal audio file and whether it is damaged; -4008: Failed to open file due to unsupported format. Please confirm if the file format you provided is supported. Supported formats on mobile are [mp3, aac, m4a, wav, ogg, mp4, mkv], and supported formats on desktop are [mp3, aac, m4a, wav, mp4, mkv]. |

## onStart
> **适用平台：** Android, C++

#### Start playing background music

Called when background music starts playing successfully or fails to play.
| Parameters | Description |
| --- | --- |
| errCode | Error Code. 0: Play started successfully; -4001: Failed to open file, such as invalid audio data, FFMPEG protocol not found, etc.; -4005: Invalid path, check if the provided path points to a valid music file; -4006: Invalid URL, check in the browser if the provided URL can download the expected music file; -4007: No audio stream, confirm if the provided file is a valid audio file and is not corrupted; -4008: Unsupported format, verify if the provided file is in a supported format. Mobile supports [mp3, aac, m4a, wav, ogg, mp4, mkv], desktop supports [mp3, aac, m4a, wav, mp4, mkv]. |
| id | Music ID. |

## onPlayProgress
> **适用平台：** Android, C++

#### Background music playback progress

## onComplete
> **适用平台：** Android, C++

#### Background music playback completed

Called when background music finishes playing or encounters an error.
| Parameters | Description |
| --- | --- |
| errCode | Error Code. 0: Play ended; -4002: Decoding failed, such as corrupted audio file, or unreachable network audio file server. |
| id | Music ID. |

## enableVoiceEarMonitor

#### Enable ear return
After the host enables ear return, they can hear their own voice collected by the microphone in the headphones. This effect is suitable for scenarios where the host is singing.
It should be noted that due to the high hardware latency of Bluetooth headphones, this effect cannot be enabled when the host is wearing Bluetooth headphones. Please try to prompt the host to wear wired headphones in the user interface.
Also, please note that not all mobile phones can achieve excellent ear return effects after enabling this feature. We have blocked this effect for phones with poor ear return performance.
| Parameters | Description |
| --- | --- |
| enable | true: on; false: off. |

> **注意**
> **Note**
> This effect can only be enabled when the host is wearing headphones. Please also prompt the host to wear wired headphones.

## setVoiceEarMonitorVolume

#### Set ear return volume

Through this interface, you can set the volume of the sound in the ear return effect.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0 - 100. Default value: 100. |

> **Note**
> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
## setVoiceReverbType

#### Set vocal reverb effect
Through this interface, you can set the vocal reverb effect. For specific effects, please refer to the enumeration Definition TXVoiceReverbType.

> **注意**
> **Note**
> The set effect will automatically become invalid after leaving the room. If the corresponding effect is needed next time you enter the room, you need to call this interface to set it again.

## setVoiceChangerType

#### Set vocal voice changing effect
Through this interface, you can set the voice changing effect. For specific effects, please refer to the enumeration Definition TXVoiceChangeType.
> **注意**
> **Note**
> The set effect will automatically become invalid after leaving the room. If the corresponding effect is needed next time you enter the room, you need to call this interface to set it again.

## setVoiceCaptureVolume
> **适用平台：** Android, C++

#### Set speech volume
This interface allows you to set the voice volume. It is generally used in conjunction with the music volume setting interface setAllMusicVolume, to balance the respective volumes of voice and music before mixing.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0-100. Default value: 100. |
> **注意**
> **Note**
> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to
> 1. However, a volume above 100 carries a risk of pop noise, so proceed with caution.

## setVoicePitch

#### Set speech tone

This interface can set the voice pitch, achieving pitch change without speed variation.
| Parameters | Description |
| --- | --- |
| pitch | Pitch, value range: -1.0f to 1.0f, default value: 0.0f. |

## setMusicObserver
> **适用平台：** Android, C++

#### Set the event callback interface for background music

Ensure to set the playback event callback using this API before playing the background music. This allows to be aware of the background music's playback progress.
| Parameters | Description |
| --- | --- |
| musicId | Music ID. |
| observer | Refer to ITXMusicPlayObserver Definition interface for details. |

> **Note**
> 1. If the ID does not need to be used, the observer can be set to NULL to completely release it.
## startPlayMusic

#### Start playing background music
Each piece of music requires a specific ID. With this ID, you can control the start, stop, volume, etc., of the music.
| Parameters | Description |
| --- | --- |
| completeBlock | Play end callback. |
| musicParam | Music parameters. |
| progressBlock | Playback progress callback. |
| startBlock | Playback start callback. |

> **注意**
> **Note**
> 1. If you need to play the same background music multiple times, do not assign a new ID each time. We recommend using the same ID.
> 2. If you want to play different pieces of music simultaneously, assign different IDs for each piece.
> 3. If you use the same ID to play different music, the SDK will stop the old music before playing the new one.

## stopPlayMusic

#### Stop playing background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |

## pausePlayMusic

#### Pause background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |

## resumePlayMusic

#### Resume background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |

## setAllMusicVolume

#### Set the local and remote volume of all background music

This interface allows you to set both the local and remote volume of all background music.
-  Local Volume: The volume of background music that the host can hear locally.

-  Remote Volume: The volume of background music that the audience can hear.

| Parameters | Description |
| --- | --- |
| volume | Volume range, value range is 0-100, default value: 60. |

> **Note**
> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
## setMusicPublishVolume

#### Set the remote volume of a specific background music track

This interface can finely control the remote volume for each background music track, which is the volume the audience can hear.
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| volume | Volume range, value range is 0-100; default value: 60. |

> **Note**
> 

> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
>

## setMusicPlayoutVolume

#### Set the local volume of a specific background music track

This interface can finely control the local volume for each background music track, which is the volume the host can hear locally.
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| volume | Volume range, value range is 0-100, default value: 60. |

> **Note**
> 

> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
>

## setMusicPitch

#### Adjust the pitch of background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| pitch | Pitch, default value is 0.0f, value range is a floating point between [-1 ~ 1]. |

## setMusicSpeedRate

#### Adjust the variable speed effect of background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| speedRate | Speed, default value is 1.0f, value range is a floating point between [0.5 ~ 2]. |

## getMusicCurrentPosInMS

#### Get the playback progress of background music (unit: milliseconds)
| Parameters | Description |
| --- | --- |
| id | Music ID. |

#### Return value description:

On success, returns current playback time in milliseconds, on failure returns -1.

## getMusicDurationInMS

#### Get the total duration of background music (unit: milliseconds)
| Parameters | Description |
| --- | --- |
| path | Music file path. |

#### Return value description:

On success, returns duration, on failure returns -1.

## seekMusicToPosInTime
> **适用平台：** C++

#### Set the playback progress of background music (unit: milliseconds)
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| pts | Unit: milliseconds. |

> **Note**
> 

> Please avoid calling this interface too frequently as it may read and write music files again, which can be time-consuming.
> 

> Therefore, when users drag the playback progress bar of the music, please call this interface after the user completes the drag operation.
> 

> Because the progress bar control on the UI often feeds back the user's drag progress at a high frequency, it would lead to a poor user experience without frequency limitations.
>

## setMusicScratchSpeedRate

#### Adjust the variable speed effect of scratching
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| scratchSpeedRate | Scratching speed, the default value is 1.0f, the range is a floating point number between [-12.0 ~ 12.0], the positive/negative value represents the forward/reverse direction, and the absolute value represents the speed. |

> **Note**
> 

> Precondition: preloadMusic is successful.
>

## setPreloadObserver
> **适用平台：** Android, C++

#### Set the preloading event callback

Set the callback using this API before preloading background music to monitor the preload progress.
| Parameters | Description |
| --- | --- |
| observer | Refer to ITXMusicPreloadObserver Definition interface for details. |

## preloadMusic

#### Preload background music

Each piece of music requires a specific ID. With this ID, you can control the start, stop, volume, etc., of the music.
| Parameters | Description |
| --- | --- |
| preloadParam | Preload music parameters. |

> **Note**
> 

> 1. Preloading supports up to 2 different IDs simultaneously, and the preload duration does not exceed 10 minutes. After use, stopPlayMusic must be called to prevent memory from not being released.
> 

> 2. If the music corresponding to this ID is playing, preloading will fail. You need to call stopPlayMusic first.
> 

> 3. When the musicParam is exactly the same as the musicParam passed to startPlayMusic, preloading is effective.
>

## getMusicTrackCount

#### Obtain the number of background music tracks
| Parameters | Description |
| --- | --- |
| id | Music ID. |

## setMusicTrack

#### Specify the playback track of background music
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| index | By default, the first audio track is played. The range of values is 0, total number of tracks). |

> **Note**
> 

> The total number of tracks can be obtained through the getMusicTrackCount interface.
>

## TXVoiceReverbType

#### Reverb effects
Reverb effects can be applied to vocals, using acoustic algorithms to overlap the sound and simulate various immersive experiences in different environments. Currently, the following reverb effects are supported:
0:Off; 1:KTV; 2:Small Room; 3:Hall; 4:Deep; 5:Loud; 6:Metallic; 7:Magnetic; 8:Ethereal; 9:Studio; 10:Melodious; 11:Studio2.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXLiveVoiceReverbType_0 | 0 | Turn off effects |
| TXLiveVoiceReverbType_1 | 1 | KTV |
| TXLiveVoiceReverbType_2 | 2 | Small Room |
| TXLiveVoiceReverbType_3 | 3 | Hall |
| TXLiveVoiceReverbType_4 | 4 | Low-pitched |
| TXLiveVoiceReverbType_5 | 5 | Loud |
| TXLiveVoiceReverbType_6 | 6 | Metallic sound |
| TXLiveVoiceReverbType_7 | 7 | Magnetic |
| TXLiveVoiceReverbType_8 | 8 | Ethereal voice |
| TXLiveVoiceReverbType_9 | 9 | Recording studio |
| TXLiveVoiceReverbType_10 | 10 | Melodious |
| TXLiveVoiceReverbType_11 | 11 | Recording studio 2 |

## TXVoiceChangeType

#### Voice changing effects
Voice changing effect can be applied to vocals. By using acoustic algorithms for secondary processing, the voice can acquire a different sound quality from the original. Currently, the following voice changing effects are supported:
0: Off; 1: Naughty kid; 2: Lolita; 3: Uncle; 4: Heavy metal; 5: Cold; 6: Foreign accent; 7: Trapped beast; 8: Fat nerd; 9: Strong current; 10: Heavy machinery; 11: Ethereal.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXLiveVoiceChangerType_0 | 0 | Off |
| TXLiveVoiceChangerType_1 | 1 | Naughty kid |
| TXLiveVoiceChangerType_2 | 2 | Lolita |
| TXLiveVoiceChangerType_3 | 3 | Uncle |
| TXLiveVoiceChangerType_4 | 4 | Heavy metal |
| TXLiveVoiceChangerType_5 | 5 | Cold |
| TXLiveVoiceChangerType_6 | 6 | Foreign accent |
| TXLiveVoiceChangerType_7 | 7 | Trapped beast |
| TXLiveVoiceChangerType_8 | 8 | Otaku |
| TXLiveVoiceChangerType_9 | 9 | Strong electric current |
| TXLiveVoiceChangerType_10 | 10 | Heavy Machinery |
| TXLiveVoiceChangerType_11 | 11 | Ethereal voice |

## TXAudioMusicParam

#### Playback control information for background music
This information is used to specify background music details in the interface [startPlayMusic, including play ID, file path, loop count, etc.:
1. If you need to play the same background music multiple times, do not assign a new ID each time. We recommend using the same ID.
2. If you want to play different pieces of music simultaneously, assign different IDs for each piece.
3. If you use the same ID to play different music, the SDK will stop the old music before playing the new one.
| Enumeration Types | Description |
| --- | --- |
| endTimeMS | [Field Meaning] Music play end time, in milliseconds. 0 means play until the end of the file. |
| id | [Field Meaning] Music ID. [Special Instructions] The SDK allows playing multiple music tracks simultaneously, so IDs are needed to control start, stop, volume, etc. |
| isShortFile | [Field Meaning] Whether the played file is a short music file. [Recommended Value] true: Requires repeating short music file; false: Normal music file. Default: false. |
| loopCount | [Field Meaning] The number of times the music loops. [Recommended Value] Value range is 0 to any positive integer. Default: 0. 0 means play the music once; 1 means play the music twice; and so on. |
| path | [Field Meaning] The full path or URL of the audio file. Supported audio formats include MP3, AAC, M4A, WAV. |
| publish | [Field Meaning] Whether to transmit the music to the remote end. [Recommended Value] true: Music can be heard by remote users while playing locally; false: Only the host can hear the music locally, remote audience cannot hear it. Default: false. |
| startTimeMS | [Field Meaning] Music start time, in milliseconds. |

## TXMusicPreloadObserver
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onLoadProgress | Background music preloading progress |
| onLoadError | Error in background music preloading |

## TXMusicPlayObserver
> **适用平台：** Android

| Function List | Description |
| --- | --- |
| onStart | Start playing background music |
| onPlayProgress | Background music playback progress |
| onComplete | Background music playback completed |

## seekMusicToPosInMS
> **适用平台：** Android, iOS

#### Set the playback progress of background music (unit: milliseconds)
| Parameters | Description |
| --- | --- |
| id | Music ID. |
| pts | Unit: milliseconds. |

> **Note**
> 

> Please avoid calling this interface too frequently as it may read and write music files again, which can be time-consuming.
> 

> Therefore, when users drag the playback progress bar of the music, please call this interface after the user completes the drag operation.
> 

> Because the progress bar control on the UI often feeds back the user's drag progress at a high frequency, it would lead to a poor user experience without frequency limitations.
>

## Structural
> **适用平台：** iOS

| Function List | Description |
| --- | --- |
| TXAudioMusicParam | Playback control information for background music |

## setVoiceVolume
> **适用平台：** iOS

#### Set speech volume

This interface allows you to set the voice volume. It is generally used in conjunction with the music volume setting interface setAllMusicVolume, to balance the respective volumes of voice and music before mixing.
| Parameters | Description |
| --- | --- |
| volume | Volume. Value range: 0-100. Default value: 100. |

> **Note**
> 

> If you still feel the volume is too low after setting it to 100, you can set the maximum volume to 150. However, a volume above 100 carries a risk of pop noise, so proceed with caution.
>
