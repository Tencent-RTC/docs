> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Call'
tech_stack: 'callKit'
framework: 'common'
title: 'CallKit Error Code Reference'
keywords: ['error code', 'error', 'callKit usage error', 'call initiation failure']
---

# CallKit Error Code Reference

## CallKit Error Codes (Topic: CallKit Error Code)

## Error Code -1001
**Cause:** Your application has not yet enabled audio/video call capability (TUICallKit).
**Solution:** You can apply for a free trial in the console: https://console.intl.cloud.tencent.com/im/detail  or purchase a call capability package: https://buy.cloud.tencent.com/avc?addRavLicense=1&position=1600110000&ravLicenseType=1&regionId=1

## Error Code -1002
**Cause:** You do not currently have access to this capability.
**Solution:** Please visit the purchase page to buy and activate: https://buy.cloud.tencent.com/avc?addRavLicense=1&position=1600110000&ravLicenseType=1&regionId=1

## Error Code -1101
**Cause:** Camera/microphone permission denied.
**Solution:** Please check your browser settings to ensure camera and microphone access is allowed. If you have confirmed permission is granted, please visit the [Audio/Video Capability Detection Tool](https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html) to check if your device and environment support calling.

## Error Code -1201
**Cause:** TUICallEngine has not completed initialization or login.
**Solution:** Please ensure that init or login operations have been successfully executed before calling this feature. Solution: https://cloud.tencent.com/document/product/647/78769#3a61f42b-e06f-49af-88bf-362d40025887

## Error Code 60003
**Cause:** Microphone device not detected.
**Solution:** Please ensure your device has a connected microphone and check if the microphone is available in system or browser settings.

## Error Code 60004
**Cause:** Camera device not detected.
**Solution:** Please ensure your camera is connected and check if the camera is available in system or browser settings.

## Error Code 60006
**Cause:** Current browser environment does not support WebRTC functionality.
**Solution:** Please visit the [Audio/Video Capability Detection Tool](https://web.sdk.qcloud.com/trtc/webrtc/demo/detect/index.html) to check your device and environment compatibility.

## Error Code 30000
**Cause:** Detected that the current page is using the http protocol.
**Solution:** To ensure smooth access and full functionality of TUICallEngine SDK for production environment users, please use https protocol (or localhost) to access the audio/video application page. Details: https://web.sdk.qcloud.com/trtc/webrtc/v5/doc/zh-cn/tutorial-05-info-browser.html#h2-3

## Error Code 20007
**Cause:** Call failed: You have been blocked by the other party or you have blocked the other party.
**Solution:** Check if the account has been blocked by the other party.
