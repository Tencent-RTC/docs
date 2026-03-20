> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Call'
tech_stack: 'callKit'
framework: 'common'
title: 'callKit 错误码大全'
keywords: ['错误码', '报错', 'callKit 使用报错', '通话呼叫失败']
---

# CallKit 错误码大全

## CallKit 错误码 (Topic: CallKit Error Code)

## 错误码 -1001
**原因：** 您的应用还未开通音视频通话（TUICallKit）能力。
**解决方案：** 您可以去控制台申请免费体验: https://console.cloud.tencent.com/im/detail 或购买通话能力套餐包：https://buy.cloud.tencent.com/avc?addRavLicense=1&position=1600110000&ravLicenseType=1&regionId=1

## 错误码 -1002
**原因：** 您暂不支持使用该能力。
**解决方案：** 请前往购买页购买开通: https://buy.cloud.tencent.com/avc?addRavLicense=1&position=1600110000&ravLicenseType=1&regionId=1

## 错误码 -1101
**原因：** 摄像头/麦克风权限被拒绝。
**解决方案：** 请检查浏览器设置，确保已允许使用您的摄像头和麦克风。如果您确认已授予权限， 请前往 [音视频能力检测工具] 检查您的设备和环境是否支持通话： https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 -1201
**原因：** TUICallEngine 尚未完成初始化或登录。
**解决方案：** 请确保在调用此功能前，已成功执行 init 或 login 操作。解决方案：https://cloud.tencent.com/document/product/647/78769#3a61f42b-e06f-49af-88bf-362d40025887

## 错误码 60003
**原因：** 未检测到麦克风设备。
**解决方案：** 请确保您的设备已连接麦克风，并检查系统或浏览器设置中麦克风是否可用。

## 错误码 60004
**原因：** 未检测到摄像头设备。
**解决方案：** 请确保您的摄像头已连接，并检查系统或浏览器设置中摄像头是否可用。

## 错误码 60006
**原因：** 当前浏览器环境不支持 WebRTC 功能。 
**解决方案：** 请前往 [音视频能力检测工具] 检查您的设备和环境兼容性：https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html

## 错误码 30000
**原因：** 检测到当前页面正处于 http 协议下。
**解决方案：** 为确保生产环境用户顺畅接入和体验 TUICallEngine SDK 的全部功能，请使用 https 协议(或 localhost)访问音视频应用页面。详情请前往：https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-05-info-browser.html#h2-3

## 错误码 20007
**原因：** 呼叫失败：您已被对方拉黑或您拉黑了对方。
**解决方案：** 检查账号是否被对方拉黑。
