> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TRTCStatistics 统计信息 文档

## 概述
本文档是 实时音视频(TRTC SDK) TRTCStatistics 统计信息 的跨平台 API 参考，适用于 Android/iOS/C++ 平台。

## TRTCStatistics
## TRTCLocalStatistics

#### Local Audio and Video Statistics
| Enumeration Types | Description |
| --- | --- |
| audioBitrate | [Field Meaning] Local audio bitrate, that is, how much new audio data is generated per second, in Kbps. |
| audioCaptureState | [Field Meaning] Audio device collection status (used to detect the health of audio peripherals). 0: Collection Device Status Normal; 1: Long Period of Silence Detected; 2: Crackling Sound Detected; 3: Abnormal Sound Intermittence Detected. |
| audioSampleRate | [Field Meaning] Local audio sampling rate, in Hz. |
| frameRate | [Field Meaning] Local video frame rate, that is, how many video frames per second, unit: FPS. |
| height | [Field Meaning] Local video height, in px. |
| streamType | [Field Meaning] Video stream type (HD large screen \\| Low-definition Small Picture \\| Auxiliary Stream Picture). |
| videoBitrate | [Field Meaning] Local video bitrate, that is, how much new video data is generated per second, in Kbps. |
| width | [Field Meaning] Local video width, in pixels (px). |

## TRTCRemoteStatistics

#### Remote Audio and Video Statistics
| Enumeration Types | Description |
| --- | --- |
| audioBitrate | [Field Meaning] Local audio bitrate, in Kbps |
| audioBlockRate | [Field Meaning] Audio playback stuttering rate, in percentage (%) Audio playback stuttering rate (audioBlockRate) = cumulative stuttering duration during audio playback (audioTotalBlockTime) / total duration of audio playback |
| audioPacketLoss | [Field Meaning] Total audio stream packet loss rate (%). audioPacketLoss represents the packet loss rate at the audience end after an audio stream completes the transmission chain from ` Broadcaster > Cloud > Audience `. The smaller the audioPacketLoss, the better. A packet loss rate of 0 means all audio stream data has been completely received by the audience. If downLoss == 0 but audioPacketLoss != 0 occurs, it indicates that the audio stream has no packet loss on the "cloud=>audience" segment, but there is irrecoverable packet loss on the ` host>cloud ` segment. |
| audioSampleRate | [Field Meaning] Local audio sampling rate, in Hz. |
| audioTotalBlockTime | [Field Meaning] Cumulative stuttering duration during audio playback, in milliseconds (ms) |
| finalLoss | [Field Meaning] Overall packet loss rate of the audio and video stream (%). Deprecated; it is recommended to use audioPacketLoss and videoPacketLoss instead. |
| frameRate | [Field Meaning] Remote video frame rate, in FPS. |
| height | [Field Meaning] Remote video height, in pixels (px). |
| jitterBufferDelay | [Field Meaning] Playback latency, in milliseconds (ms) To avoid audio and screen freezing caused by network jitter and packet disorder, TRTC manages a playback buffer at the playback end to organize received network packets. The size of this buffer is adaptively adjusted based on current network quality. This buffer size is translated into a time length in milliseconds, referred to as jitterBufferDelay. |
| point2PointDelay | [Field Meaning] End-to-end latency, in milliseconds (ms) point2PointDelay represents the delay from "Anchors => Cloud => Audience". More precisely, it represents the full-link latency of "Collection => Encoding => Network Transmission => Receive => Buffering => Decoding => Play". point2PointDelay requires both local and remote SDKs to be version 8.5 or above to be effective. If the remote user is on a version before 8.5, this value will always be 0, meaning it is not applicable. |
| remoteNetworkRTT | [Field Meaning] The round-trip time from the SDK to the cloud, in milliseconds (ms) This value represents the total time it takes for a network packet to travel from the SDK to the cloud and back, summing up the "SDK => Cloud => SDK" time. The smaller this value, the better: if remoteNetworkRTT < 50ms, it indicates low audio and video call latency; if remoteNetworkRTT > 200ms, it suggests high audio and video call latency. It is important to note that remoteNetworkRTT represents the total time for "SDK => Cloud => SDK", so there is no need to differentiate between remoteNetworkUpRTT and remoteNetworkDownRTT |
| remoteNetworkUplinkLoss | [Field Meaning] The uplink packet loss rate from the SDK to the cloud, in percentage (%) The smaller the value, the better. If remoteNetworkUplinkLoss is 0%, it means the uplink network quality is good, with almost no data packet loss when uploading to the cloud. If remoteNetworkUplinkLoss is 30%, it means that 30% of the audio and video data packets sent by the SDK to the cloud are lost in the transmission link. |
| streamType | [Field Meaning] Video stream type (HD large screen \\| Low-definition Small Picture \\| Auxiliary Stream Picture). |
| userId | [Field Meaning] User ID |
| videoBitrate | [Field Meaning] Remote video bitrate, in Kbps. |
| videoBlockRate | [Field Meaning] Video playback stutter rate, in percentage (%). Video playback stutter rate (videoBlockRate) = cumulative stutter duration during video playback (videoTotalBlockTime) / total duration of video playback. |
| videoPacketLoss | [Field Meaning] Total packet loss rate of the video stream (%). videoPacketLoss represents the packet loss rate at the audience end after the video stream completes the transmission chain from ` Anchors > Cloud > Audience `. The smaller the videoPacketLoss, the better. A packet loss rate of 0 means all video stream data has been completely received by the audience. If downLoss == 0 but videoPacketLoss != 0 occurs, it indicates that the video stream has no packet loss on the ` Cloud > Audience ` segment, but there is irrecoverable packet loss on the ` Anchors > Cloud ` segment. |
| videoTotalBlockTime | [Field Meaning] Cumulative stutter duration during video playback, in milliseconds (ms) |
| width | [Field Meaning] Remote video width, in pixels (px). |

## TRTCStatistics

#### Summary Statistics of Network and Performance
| Enumeration Types | Description |
| --- | --- |
| appCpu | [Field Meaning] Current application CPU utilization, in percentage (%); not supported on Android 8.0 and above. |
| appMemoryUsageInMB | [Field Meaning] Current application memory usage, in MB. |
| downLoss | [Field Meaning] Downlink packet loss rate from the cloud to SDK, in percentage (%) The smaller the value, the better. If downLoss is 0%, it means the downlink network quality is good, with almost no data packet loss from the cloud. If downLoss is 30%, it means that 30% of the audio and video data packets transmitted from the cloud to the SDK are lost in the transmission link. |
| gatewayRtt | [Field Meaning] Round-trip latency from the SDK to the local router, in milliseconds (ms). This value represents the total time it takes for a network packet to travel from the SDK to the local router gateway and back, summing up the [SDK -> Gateway -> SDK] time. The smaller the value, the better. If gatewayRtt < 50ms, it means lower audio and video call latency. If gatewayRtt > 200ms, it means higher audio and video call latency. This value is invalid when the network type is cellular. |
| localStatisticsArray | [Field Meaning] Local audio and video statistical information Since there may be three local audio and video streams (i.e., high-definition main screen, low-definition small screen, and auxiliary screen), the local audio and video statistical information is an array. |
| localStatisticsArraySize | [Field Meaning] Size of the array localStatisticsArray |
| receivedBytes | [Field Meaning] Total received bytes (including signaling data and audio and video data), in bytes. |
| remoteStatisticsArray | [Field Meaning] Remote audio and video statistical information Since there may be multiple remote users at the same time, and each remote user may simultaneously have multiple audio and video streams (i.e., high-definition main screen, low-definition small screen, and auxiliary screen), the remote audio and video statistical information is an array. |
| remoteStatisticsArraySize | [Field Meaning] Size of the array remoteStatisticsArray. |
| rtt | [Field Meaning] Round-trip latency from the SDK to the cloud, in milliseconds (ms) This value represents the total time it takes for a network packet to travel from the SDK to the cloud and back, summing up the "SDK => Cloud => SDK" time. The smaller the value, the better: if RTT < 50ms, it means lower audio and video call latency; if RTT > 200ms, it means higher audio and video call latency. It needs to be specifically explained that RTT represents the total time for "SDK => Cloud => SDK," so there is no need to distinguish between upRtt and downRtt. |
| sentBytes | [Field Meaning] Total bytes sent (including signaling data and audio and video data), in bytes (Bytes). |
| systemCpu | [Field Meaning] Current system CPU utilization, in percentage (%); not supported on Android 8.0 and above. |
| systemMemoryInMB | [Field Meaning] Current system memory, in MB. |
| systemMemoryUsageInMB | [Field Meaning] Current system memory usage, in MB, not supported on iOS/MAC. |
| upLoss | [Field Meaning] Uplink packet loss rate from the SDK to the cloud, in percentage (%) The smaller the value, the better. If upLoss is 0%, it means the uplink network quality is good, with almost no data packet loss when uploading to the cloud. If upLoss is 30%, it means that 30% of the audio and video data packets sent by the SDK to the cloud are lost in the transmission link. |
