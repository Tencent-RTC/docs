> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Live'
tech_stack: 'liveKit'
framework: 'common'
title: 'liveKit 错误码大全'
keywords: ['错误码', '报错', 'live', 'liveKit', '直播间异常', 'pk 报错', '连麦报错']
---

# LiveKit 错误码大全

## LiveKit 错误码 (Topic: LiveKit Error Code)

## 错误码 100002
**原因：** 可能原因如下：
- 您当前的套餐包不支持开播，可能是套餐包过期、未开通等原因。
- 直播间名称不能超过100个字节，一个汉字占2字节。
- 直播间 ID 不能超过 48 个字符。
- 直播间 ID 只能包含字母、数字、下划线。
- 只有 live 类型的房间支持该接口。
- 当前布局模板无需改变。
- PK请求时长超过了最大限制（默认为 5 分钟）。
**解决方案：** 
- 检查您使用的套餐，首次使用，可以[开通体验版](https://cloud.tencent.com/document/product/647/105439)；也可以前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买或者升级套餐包。
- 检查直播间名称、直播间ID是否符合要求，确认房间类型是否为 live。
- 检查布局模板参数，确认是否需要更改布局。
- 检查PK请求时长，缩短 PK 的时间。

## 错误码 70001
**原因：** 用户签名已过期。
**解决方案：** 建议生成的签名有效期不少于 24 小时。可参阅[生成 UserSig(用户鉴权)](https://cloud.tencent.com/document/product/269/32688)</a>。

## 错误码 70003
**原因：** 非法的用户签名。
**解决方案：** 建议重新生成的签名，可参阅[生成 UserSig(用户鉴权)](https://cloud.tencent.com/document/product/269/32688)</a>。

## 错误码 70013
**原因：** 用户 ID 与用户签名不匹配。
**解决方案：** 请检查用户 ID 与用户签名是否匹配，。可前往[腾讯云控制台](https://console.cloud.tencent.com/trtc/usersigtool)验证签名。

## 错误码 100004
**原因：** 房间不存在。
**解决方案：** 请检查使用的房间号是否正确。

## 错误码 100006
**原因：** 没有操作权限。
**解决方案：** 请检查当前角色是否有该权限。

## 错误码 100007
**原因：** 可能原因如下：
- 未购买套餐包或者已欠费。
- 直播间之间的跨房间连麦功能不可用。
- 直播间之间的PK功能不可用。
**解决方案：** 
- 套餐问题请前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买。首次使用，可以[开通体验版](https://cloud.tencent.com/document/product/647/105439)。
- 直播间之间的跨房间连麦功能不可用问题，请前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买或者升级套餐包。
- 直播间之间的PK功能不可用问题，请前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买或者升级套餐包。

## 错误码 100012
**原因：** 请求频繁。
**解决方案：** 请求频繁，请稍后再试。

## 错误码 100203
**原因：** 您已经在麦位上。
**解决方案：** 请检查当前用户是否已经在麦位上。

## 错误码 100206
**原因：** 用户不在麦位上。
**解决方案：** 请检查用户是否在麦上。

## 错误码 100400
**原因：** 当前房间未开启直播间连麦。
**解决方案：** 需要请先进行直播间连麦后再操作。

## 错误码 100418
**原因：** 当前房间还未开启 PK。
**解决方案：** 当前房间需要请先进行 PK 后再操作。

## 错误码 100419
**原因：** 当前房间正在 PK 中。
**解决方案：** 当前房间正在 PK 中，无法进行该操作，请稍后再试。

## 错误码 100421
**原因：** 当前房间 PK 已结束。
**解决方案：** 当前房间 PK 已结束，无法进行该操作，请稍后再试。

## 错误码 101011
**原因：** 当前房间人数已达到套餐上限。
**解决方案：** 请前往[腾讯云控制台](https://buy.cloud.tencent.com/trtc)购买或者升级套餐包。
