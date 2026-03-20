> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Room'
tech_stack: 'roomKit'
framework: 'common'
title: 'roomKit 错误码大全'
keywords: ['错误码', '报错', 'roomKit', 'room 套餐问题', '麦克风权限问题', '摄像头/麦克风问题']
---

# RoomKit 错误码大全

## RoomKit 错误码 (Topic: RoomKit Error Code)

## 错误码 -2
**原因：** 操作频率超限，请求过于频繁，请稍后重试。
**解决方案：** 操作频率超限，请求过于频繁，请稍后重试。

## 错误码 -1002
**原因：** SDK 未初始化。
**解决方案：** 请先调用初始化接口完成 SDK 初始化。

## 错误码 -1004
**原因：** 未购买套餐包或者已欠费。
**解决方案：** 请前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买。首次使用，可以[开通体验版](https://cloud.tencent.com/document/product/647/105439)</a>。

## 错误码 -1101
**原因：** 摄像头权限被拒绝。
**解决方案：** 请检查浏览器设置，确保已允许使用您的摄像头。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 -1105
**原因：** 麦克风权限被拒绝。
**解决方案：** 请检查浏览器设置，确保已允许使用您的麦克风。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 -1100
**原因：** 打开摄像头失败。
**解决方案：** 请检查浏览器设置，确保已允许使用您的摄像头。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 -1104
**原因：** 打开麦克风失败。
**解决方案：** 请检查浏览器设置，确保已允许使用您的麦克风。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 100007
**原因：** 
**解决方案：** 未购买套餐包或者已欠费，请前往[腾讯云控制台购买](https://buy.cloud.tencent.com/trtc)。首次使用，可以[开通体验版](https://cloud.tencent.com/document/product/647/105439)。

## 错误码 70001
**原因：** 用户签名已过期。
**解决方案：** 建议生成的签名有效期不少于 24 小时。可参阅[生成 UserSig(用户鉴权)](https://cloud.tencent.com/document/product/269/32688)</a>。

## 错误码 70003
**原因：** 非法的用户签名。
**解决方案：** 请重新生成。可参阅[生成 UserSig(用户鉴权)](https://cloud.tencent.com/document/product/269/32688)</a>。

## 错误码 70014
**原因：** SDKAppID 与用户签名不匹配。
**解决方案：** 请检查。可前往[腾讯云控制台](https://console.cloud.tencent.com/trtc/usersigtool)验证签名。

## 错误码 5300
**原因：** 获取设备或者采集音视频出现异常。
**解决方案：** 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 5301
**原因：** 未检测到摄像头/麦克风设备。
**解决方案：** 请确保您的摄像头/麦克风已连接，并检查系统或浏览器设置中摄像头/麦克风是否可用。

## 错误码 5302
**原因：** 摄像头/麦克风权限被拒绝。
**解决方案：** 请检查浏览器设置，确保已允许使用您的摄像头和麦克风。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html
