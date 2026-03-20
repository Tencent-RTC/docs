> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXAudioEffectManager 音效管理 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXAudioEffectManager 音效管理 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TXAudioEffectManager
## ITXMusicPlayObserver
> **适用平台：** C++

## 结构体类型

## 枚举类型

## onLoadProgress
> **适用平台：** Android, C++

#### 背景音乐预加载进度

## onLoadError
> **适用平台：** Android, C++

#### 背景音乐预加载出错
| 参数 | 描述 |
| --- | --- |
| errorCode | -4001:打开文件失败，如音频数据无效，FFMPEG 协议未找到等；-4002:解码失败，如音频文件损坏，网络音频文件服务器无法访问等；-4003:预加载数量超上限，请先调用 stopPlayMusic 释放无用的预加载；-4005:非法路径导致打开文件失败，请检查您传入的路径参数是否指向一个合法的音乐文件；-4006:非法URL导致打开文件失败，请用浏览器检查您传入的 URL 地址是否可以下载到期望的音乐文件；-4007:无音频流导致打开文件失败，请确认您传入的文件是否是合法的音频文件，以及文件是否被损坏；-4008:格式不支持导致打开文件失败，请确认您传入的文件格式是否是支持的文件格式，移动端支持【mp3，aac，m4a，wav，ogg，mp4，mkv】，桌面端支持 【mp3，aac，m4a，wav，mp4，mkv】。 |

## onStart
> **适用平台：** Android, C++

#### 背景音乐开始播放

在背景音乐开始播放成功或者失败时调用。
| 参数 | 描述 |
| --- | --- |
| errCode | 错误码。0: 开始播放成功；-4001:打开文件失败，如音频数据无效，FFMPEG 协议未找到等；-4005:非法路径导致打开文件失败，请检查您传入的路径参数是否指向一个合法的音乐文件；-4006:非法URL导致打开文件失败，请用浏览器检查您传入的 URL 地址是否可以下载到期望的音乐文件，如果操作系统为 iOS 或 MacOS 请确保使用 https 链接；-4007:无音频流导致打开文件失败，请确认您传入的文件是否是合法的音频文件，以及文件是否被损坏；-4008:格式不支持导致打开文件失败，请确认您传入的文件格式是否是支持的文件格式，移动端支持【mp3，aac，m4a，wav，ogg，mp4，mkv】，桌面端支持 【mp3，aac，m4a，wav，mp4，mkv】；-4009:同时播放 bgm 数量超过限定值，如当前同时播放 bgm 数量超过 10 后提示该错误，请检查并发播放 bgm 数量。 |
| id | 音乐 ID。 |

## onPlayProgress
> **适用平台：** Android, C++

#### 背景音乐的播放进度

## onComplete
> **适用平台：** Android, C++

#### 背景音乐已经播放完毕

在背景音乐播放完毕或播放错误时调用。
| 参数 | 描述 |
| --- | --- |
| errCode | 错误码。0: 播放结束；-4002: 解码失败，如音频文件损坏，网络音频文件服务器无法访问等。 |
| id | 音乐 ID。 |

## enableVoiceEarMonitor

#### 开启耳返
主播开启耳返后，可以在耳机里听到麦克风采集到的自己发出的声音，该特效适用于主播唱歌的应用场景中。
需要您注意的是，由于蓝牙耳机的硬件延迟非常高，所以在主播佩戴蓝牙耳机时无法开启此特效，请尽量在用户界面上提示主播佩戴有线耳机。
同时也需要注意，并非所有的手机开启此特效后都能达到优秀的耳返效果，我们已经对部分耳返效果不佳的手机屏蔽了该特效。目前 12.2 及其以上的版本针对硬件耳返的支持扩展到了荣耀、OPPO/一加、小米/红米等主流的机型。
全新的耳返效果可以在 [Demo 体验](https://cloud.tencent.com/document/product/647/17021) 中的 KTV 合唱场景来体验。
**OPPO**
在 12.6 及其以上的版本，使用 OPPO 硬件耳返的客户需要在 [OPPO 开放平台](https://open.oppomobile.com/new/developmentDoc/info?id=10889) 中申请开通耳返的授权码，将自己应用的授权码配置到 ` AndroidManifest.xml ` 中。
这个文档中其他的配置如 ` 环境准备中 ` 的 ` build.gradle ` 配置、` 修改 Module 的 build.gradle 文件 `中的 ` build.gradle `修改 以及 ` 权限添加 ` 都不需要配置。配置完成后，此时打开耳返时会弹出悬浮球，关闭耳返后悬浮球消失，耳返的音量以及音效可以通过悬浮球来调节。
否则在打开耳返时会因为服务鉴权失败而自动切换为软件耳返。
**荣耀**
在 12.2 及其以上的版本，使用荣耀硬件耳返的客户需要在 ` AndroidManifest.xml ` 中配置如下字段。
否则在打开耳返的时候会偶现耳返无声现象，可能需要用户重新开关下硬件耳返才能正常听到耳返的声音。
**VIVO**
部分 VIVO 手机在使用 USB 数字耳机的时候，会出现硬件耳返无声的问题，这里可以将耳机更换为 3.5mm 的圆孔耳机来进行体验，目前 VIVO 厂商没有对 USB 的耳机全部适配，建议首选 VIVO 品牌的耳机进行 K 歌体验。
| 参数 | 描述 |
| --- | --- |
| enable | true：开启；false：关闭。 |

> **注意**
> 仅在主播佩戴耳机时才能开启此特效，同时请您提示主播佩戴有线耳机。

**代码示例：**

**Android：**
``` java
<meta-data
    android:name="com.coloros.ocs.media.AUTH_CODE"
    android:value="请替换为自身授权码" />
```
``` java
<meta-data
    android:name="com.hihonor.hcs.client.appid"
    android:value="请替换为自身的 appid" />
```

## setVoiceEarMonitorVolume

#### 设置耳返音量。

通过该接口您可以设置耳返特效中声音的音量大小。
| 参数 | 描述 |
| --- | --- |
| volume | 音量大小，取值范围为 [0, 150]，默认值：100。 |

> **注意**
> 耳返的音量大小是影响用户耳返体验的重要因素之一，考虑到不同用户对音量的敏感度不同，强烈建议您在 UI 界面上开放耳返音量的调节滑杆，调节范围建议在 0~150 之间，其中 100~150 的增益效果对于部分采集音量较小的 Android 手机很有帮助。
## setVoiceReverbType

#### 设置人声的混响效果。
通过该接口您可以设置人声的混响效果，具体特效请参见枚举定义 TXVoiceReverbType。

> **注意**
> 设置的效果在退出房间后会自动失效，如果下次进房还需要对应特效，需要调用此接口再次进行设置。

## setVoiceChangerType

#### 设置人声的变声特效。
通过该接口您可以设置人声的变声特效，具体特效请参见枚举定义 TXVoiceChangeType。
> **注意**
> 设置的效果在退出房间后会自动失效，如果下次进房还需要对应特效，需要调用此接口再次进行设置。

## setVoiceCaptureVolume
> **适用平台：** Android, C++

#### 设置语音音量。
该接口可以设置语音音量的大小，一般配合音乐音量的设置接口 setAllMusicVolume 协同使用，用于调谐语音和音乐在混音前各自的音量占比。
| 参数 | 描述 |
| --- | --- |
| volume | 音量大小，取值范围为 [0 - 150]，默认值：100。 |
> **注意**
> 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。

## setVoicePitch

#### 设置语音音调。

该接口可以设置语音音调，用于实现变调不变速的目的。
| 参数 | 描述 |
| --- | --- |
| pitch | 音调，取值范围[-1.0f, 1.0f]，默认值：0.0f。 |

## setMusicObserver
> **适用平台：** Android, C++

#### 设置背景音乐的事件回调接口。
请在播放背景音乐之前使用该接口设置播放事件回调，以便感知背景音乐的播放进度。
| 参数 | 描述 |
| --- | --- |
| musicId | 音乐 ID。 |
| observer | 具体参考 TXMusicPlayObserver 中定义接口。 |
> **注意**
> 1. 如果该 ID 不需要使用，observer 可设置为 NULL 彻底释放。

## startPlayMusic

#### 开始播放背景音乐。
每个音乐都需要您指定具体的 ID，您可以通过该 ID 对音乐的开始、停止、音量等进行设置。
| 参数 | 描述 |
| --- | --- |
| completeBlock | 播放结束回调。 |
| musicParam | 音乐参数。 |
| progressBlock | 播放进度回调。 |
| startBlock | 播放开始回调。 |
> **注意**
> 1. 如果要多次播放同一首背景音乐，请不要每次播放都分配一个新的 ID，我们推荐使用相同的 ID。
> 2. 若您希望同时播放多首不同的音乐，请为不同的音乐分配不同的 ID 进行播放。
> 3. 如果使用同一个 ID 播放不同音乐，SDK 会先停止播放旧的音乐，再播放新的音乐。

## stopPlayMusic

#### 停止播放背景音乐。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |

## pausePlayMusic

#### 暂停播放背景音乐。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |

## resumePlayMusic

#### 恢复播放背景音乐。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |

## setAllMusicVolume

#### 设置所有背景音乐的本地音量和远端音量的大小。

该接口可以设置所有背景音乐的本地音量和远端音量。
-  本地音量：即主播本地可以听到的背景音乐的音量大小。

-  远端音量：即观众端可以听到的背景音乐的音量大小。

| 参数 | 描述 |
| --- | --- |
| volume | 音量大小，取值范围为 [0, 150]，默认值：60。 |

> **注意**
> 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。
## setMusicPublishVolume

#### 设置某一首背景音乐的远端音量的大小。

该接口可以细粒度地控制每一首背景音乐的远端音量，也就是观众端可听到的背景音乐的音量大小。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| volume | 音量大小，取值范围为 [0, 150]；默认值：60。 |

> **注意**
> 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。
## setMusicPlayoutVolume

#### 设置某一首背景音乐的本地音量的大小。

该接口可以细粒度地控制每一首背景音乐的本地音量，也就是主播本地可以听到的背景音乐的音量大小。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| volume | 音量大小，取值范围为 [0, 150]，默认值：60。 |

> **注意**
> 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。
## setMusicPitch

#### 调整背景音乐的音调高低。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| pitch | 音调，取值范围为 [-1.0f, 1.0f] 之间的浮点数，默认值：0.0f。 |

## setMusicSpeedRate

#### 调整背景音乐的变速效果。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| speedRate | 速度，取值范围为 [0.5f, 2.0f] 之间的浮点数，默认值：1.0f。 |

## getMusicCurrentPosInMS

#### 获取背景音乐的播放进度（单位：毫秒）。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |

#### 返回值说明：

成功返回当前播放时间，单位：毫秒，失败返回 -1。

## getMusicDurationInMS

#### 获取背景音乐的总时长（单位：毫秒）。
| 参数 | 描述 |
| --- | --- |
| path | 音乐文件路径。 |

#### 返回值说明：

成功返回时长，失败返回 -1。

## seekMusicToPosInTime
> **适用平台：** C++

#### 设置背景音乐的播放进度（单位：毫秒）。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| pts | 单位: 毫秒。 |

> **注意**
> 请尽量避免过度频繁地调用该接口，因为该接口可能会再次读写音乐文件，耗时稍高。
> 因此，当用户拖拽音乐的播放进度条时，请在用户完成拖拽操作后再调用本接口。
> 因为 UI 上的进度条控件往往会以很高的频率反馈用户的拖拽进度，如不做频率限制，会导致较差的用户体验。
>

## setMusicScratchSpeedRate

#### 调整搓碟的变速效果。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| scratchSpeedRate | 搓碟速度，取值范围为 [-12.0, 12.0] 之间的浮点数，默认值是 1.0f，速度值正/负表示方向正/反，绝对值大小表示速度快慢。 |

> **注意**
> 前置条件 preloadMusic 成功。
## setPreloadObserver
> **适用平台：** Android, C++

#### 设置预加载事件回调。
请在预加载背景音乐之前使用该接口设置回调，以便感知背景音乐的预加载进度。
| 参数 | 描述 |
| --- | --- |
| observer | 具体参考 TXMusicPreloadObserver 中定义接口。 |

## preloadMusic

#### 预加载背景音乐。
每个音乐都需要您指定具体的 ID，您可以通过该 ID 对音乐的开始、停止、音量等进行设置。
| 参数 | 描述 |
| --- | --- |
| preloadParam | 预加载音乐参数。 |

> **注意**
> 1. 预先加载最多同时支持2个不同 ID 的预加载，且预加载时长不超过10分钟，使用完需调用 stopPlayMusic，否则内存不释放。
> 2. 若该ID对应的音乐正在播放中，预加载会失败，需先调用 stopPlayMusic。
> 3. 当 ` musicParam ` 和传入 startPlayMusic 的 ` musicParam ` 完全相同时，预加载有效。

## getMusicTrackCount

#### 获取背景音乐的音轨数量。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |

## setMusicTrack

#### 指定背景音乐的播放音轨。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| index | 默认播放第一个音轨。取值范围0, 音轨总数)。 |

> **注意**
> 音轨总数量可通过 getMusicTrackCount 接口获取。
## TXVoiceReverbType

#### 混响特效
混响特效可以作用于人声之上，通过声学算法对声音进行叠加处理，模拟出各种不同环境下的临场感受，目前支持如下几种混响效果：
0：关闭；1：KTV；2：小房间；3：大会堂；4：低沉；5：洪亮；6：金属声；7：磁性；8：空灵；9：录音棚；10：悠扬 11：录音棚2。
| 枚举 | 取值 | 描述 |
| --- | --- | --- |
| TXLiveVoiceReverbType_0 | 0 | 关闭特效 |
| TXLiveVoiceReverbType_1 | 1 | KTV |
| TXLiveVoiceReverbType_2 | 2 | 小房间 |
| TXLiveVoiceReverbType_3 | 3 | 大会堂 |
| TXLiveVoiceReverbType_4 | 4 | 低沉 |
| TXLiveVoiceReverbType_5 | 5 | 洪亮 |
| TXLiveVoiceReverbType_6 | 6 | 金属声 |
| TXLiveVoiceReverbType_7 | 7 | 磁性 |
| TXLiveVoiceReverbType_8 | 8 | 空灵 |
| TXLiveVoiceReverbType_9 | 9 | 录音棚 |
| TXLiveVoiceReverbType_10 | 10 | 悠扬 |
| TXLiveVoiceReverbType_11 | 11 | 录音棚2 |

## TXVoiceChangeType

#### 变声特效
变声特效可以作用于人声之上，通过声学算法对人声进行二次处理，以获得与原始声音所不同的音色，目前支持如下几种变声特效：
0：关闭；1：熊孩子；2：萝莉；3：大叔；4：重金属；5：感冒；6：外语腔；7：困兽；8：肥宅；9：强电流；10：重机械；11：空灵；12：猪八戒；13：绿巨人。
| 枚举 | 取值 | 描述 |
| --- | --- | --- |
| TXLiveVoiceChangerType_0 | 0 | 关闭 |
| TXLiveVoiceChangerType_1 | 1 | 熊孩子 |
| TXLiveVoiceChangerType_2 | 2 | 萝莉 |
| TXLiveVoiceChangerType_3 | 3 | 大叔 |
| TXLiveVoiceChangerType_4 | 4 | 重金属 |
| TXLiveVoiceChangerType_5 | 5 | 感冒 |
| TXLiveVoiceChangerType_6 | 6 | 外语腔 |
| TXLiveVoiceChangerType_7 | 7 | 困兽 |
| TXLiveVoiceChangerType_8 | 8 | 肥宅 |
| TXLiveVoiceChangerType_9 | 9 | 强电流 |
| TXLiveVoiceChangerType_10 | 10 | 重机械 |
| TXLiveVoiceChangerType_11 | 11 | 空灵 |
| TXLiveVoiceChangerType_12 | 12 | 猪八戒 |
| TXLiveVoiceChangerType_13 | 13 | 绿巨人 |

## TXAudioMusicParam

#### 背景音乐的播放控制信息
该信息用于在接口 [startPlayMusic 中指定背景音乐的相关信息，包括播放 ID、文件路径和循环次数等：
1. 如果要多次播放同一首背景音乐，请不要每次播放都分配一个新的 ID，我们推荐使用相同的 ID。
2. 若您希望同时播放多首不同的音乐，请为不同的音乐分配不同的 ID 进行播放。
3. 如果使用同一个 ID 播放不同音乐，SDK 会先停止播放旧的音乐，再播放新的音乐。
| 枚举类型 | 描述 |
| --- | --- |
| endTimeMS | 【字段含义】音乐结束播放时间点，单位毫秒，0表示播放至文件结尾。 |
| id | 【字段含义】音乐 ID。 【特殊说明】SDK 允许播放多路音乐，因此需要使用 ID 进行标记，用于控制音乐的开始、停止、音量等。 |
| isShortFile | 【字段含义】播放的是否为短音乐文件。 【推荐取值】true：需要重复播放的短音乐文件；false：正常的音乐文件。默认值：false。 |
| loopCount | 【字段含义】音乐循环播放的次数。 【推荐取值】取值范围为 [0, 任意正整数]，默认值：0。0 表示播放音乐一次；1 表示播放音乐两次；以此类推。 |
| path | 【字段含义】音效文件的完整路径或 URL 地址。支持的音频格式包括 MP3、AAC、M4A、WAV。 |
| publish | 【字段含义】是否将音乐传到远端。 【推荐取值】true：音乐在本地播放的同时，远端用户也能听到该音乐；false：主播只能在本地听到该音乐，远端观众听不到。默认值：false。 |
| startTimeMS | 【字段含义】音乐开始播放时间点，单位：毫秒。 |

## TXMusicPreloadObserver
> **适用平台：** Android

## TXMusicPlayObserver
> **适用平台：** Android

## seekMusicToPosInMS
> **适用平台：** Android, iOS

#### 设置背景音乐的播放进度（单位：毫秒）。
| 参数 | 描述 |
| --- | --- |
| id | 音乐 ID。 |
| pts | 单位: 毫秒。 |

> **注意**
> 请尽量避免过度频繁地调用该接口，因为该接口可能会再次读写音乐文件，耗时稍高。
> 因此，当用户拖拽音乐的播放进度条时，请在用户完成拖拽操作后再调用本接口。
> 因为 UI 上的进度条控件往往会以很高的频率反馈用户的拖拽进度，如不做频率限制，会导致较差的用户体验。
>

## setVoiceVolume
> **适用平台：** iOS

#### 设置语音音量。

该接口可以设置语音音量的大小，一般配合音乐音量的设置接口 setAllMusicVolume 协同使用，用于调谐语音和音乐在混音前各自的音量占比。
| 参数 | 描述 |
| --- | --- |
| volume | 音量大小，取值范围为 [0 - 150]，默认值：100。 |

> **注意**
> 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。