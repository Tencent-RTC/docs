> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Live'
tech_stack: 'liveKit'
framework: 'common'
title: 'LiveKit Error Code Reference'
keywords: ['error code', 'error', 'live', 'liveKit', 'live room error', 'pk error', 'co-hosting error']
---

# LiveKit Error Code Reference

## LiveKit Error Codes (Topic: LiveKit Error Code)

## Error Code 100002
**Cause:** Possible reasons include:
- Your current package does not support live streaming, possibly due to package expiration or not being activated.
- Live room name cannot exceed 100 bytes. One Chinese character occupies 2 bytes.
- Live room ID cannot exceed 48 characters.
- Live room ID can only contain letters, numbers, and underscores.
- Only live-type rooms support this interface.
- Current layout template does not need to be changed.
- PK request duration exceeded the maximum limit (default is 5 minutes).
**Solution:** 
- Check your package. For first-time use, you can [activate the trial version](https://trtc.io/document/60033?product=live&menulabel=uikit&platform=web) or visit the [Tencent Cloud Console](https://console.trtc.io/subscription/buy/live) to purchase or upgrade your package.
- Verify that the live room name and live room ID meet requirements, and confirm that the room type is live.
- Check the layout template parameters to confirm if layout changes are needed.
- Check the PK request duration and reduce the PK time.

## Error Code 70001
**Cause:** User signature has expired.
**Solution:** It is recommended to set the signature validity period to no less than 24 hours. Refer to [Generate UserSig (User Authentication)](https://trtc.io/document/35166?product=live&menulabel=uikit&platform=web).

## Error Code 70003
**Cause:** Invalid user signature.
**Solution:** Regenerate the signature. Refer to [Generate UserSig (User Authentication)](https://trtc.io/document/35166?product=live&menulabel=uikit&platform=web).

## Error Code 70013
**Cause:** User ID does not match the user signature.
**Solution:** Please verify that the user ID matches the user signature. You can verify the signature at the [Tencent Cloud Console](https://console.trtc.io/usersig).

## Error Code 100004
**Cause:** Room does not exist.
**Solution:** Please verify that the room ID being used is correct.

## Error Code 100006
**Cause:** No operation permission.
**Solution:** Please check if the current role has the required permission.

## Error Code 100007
**Cause:** Possible reasons include:
- No package purchased or account is in arrears.
- Cross-room co-hosting feature is not available between live rooms.
- PK feature is not available between live rooms.
**Solution:** 
- For package issues, visit the [Tencent Cloud Console](https://console.trtc.io/subscription/buy/live) to purchase. For first-time use, you can [activate the trial version](https://trtc.io/document/60033?product=live&menulabel=uikit&platform=web).
- For cross-room co-hosting unavailability, visit the [Tencent Cloud Console](https://console.trtc.io/subscription/buy/live) to purchase or upgrade your package.
- For PK feature unavailability, visit the [Tencent Cloud Console](https://console.trtc.io/subscription/buy/live) to purchase or upgrade your package.

## Error Code 100012
**Cause:** Request too frequent.
**Solution:** Requests are too frequent, please try again later.

## Error Code 100203
**Cause:** You are already on the microphone seat.
**Solution:** Please check if the current user is already on the microphone seat.

## Error Code 100206
**Cause:** User is not on the microphone seat.
**Solution:** Please check if the user is on the microphone seat.

## Error Code 100400
**Cause:** Current room has not enabled live room co-hosting.
**Solution:** Please enable live room co-hosting before performing this operation.

## Error Code 100418
**Cause:** Current room has not started PK yet.
**Solution:** Please start PK in the current room before performing this operation.

## Error Code 100419
**Cause:** Current room is currently in PK.
**Solution:** The current room is in PK mode and cannot perform this operation. Please try again later.

## Error Code 100421
**Cause:** Current room PK has ended.
**Solution:** The current room PK has ended and cannot perform this operation. Please try again later.

## Error Code 101011
**Cause:** Current room has reached the package participant limit.
**Solution:** Visit the [Tencent Cloud Console](https://console.trtc.io/subscription/buy/live) to purchase or upgrade your package.
