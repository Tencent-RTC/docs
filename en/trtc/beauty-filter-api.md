> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXBeautyManager 美颜管理 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXBeautyManager 美颜管理 的跨平台 API 参考，适用于 Android/iOS 平台。

## TXBeautyManager
## setBeautyStyle

#### Set skin smoothing algorithm

TRTC offers multiple skin smoothing algorithms for you to choose the one that best fits your product positioning:
| Parameters | Description |
| --- | --- |
| beautyStyle | Beauty Styles, TXBeautyStyleSmooth: Smooth; TXBeautyStyleNature: Natural; TXBeautyStylePitu: Pitu. |

## setBeautyLevel

#### Set beauty filter effect level
| Parameters | Description |
| --- | --- |
| beautyLevel | Beauty filter level, value range: 0-9; 0 means off, 9 means the most noticeable effect. |

## setWhitenessLevel

#### Set brightening filter effect level
| Parameters | Description |
| --- | --- |
| whitenessLevel | Whitening level, value range: 0-9; 0 means off, 9 means the most noticeable effect. |

## enableSharpnessEnhancement

#### Enable clarity enhancement

## setRuddyLevel

#### Set rosy filter effect level
| Parameters | Description |
| --- | --- |
| ruddyLevel | Rosy level, value range: 0-9; 0 means off, 9 means the most noticeable effect. |

## setFilter

#### Set color filter effect

Color Filter, an image that contains the color mapping relationship of the color lookup table. You can find several pre-prepared filter images in our official Demo.

The SDK will perform secondary processing on the original video captured by the camera based on the mapping relationship in the lookup table, to achieve the desired filter effect.
| Parameters | Description |
| --- | --- |
| image | The image containing the color mapping relationship must be in PNG format. |

## setFilterStrength

#### Set the intensity of color filter

The higher the value, the more obvious the intensity of the color filter and the greater the color difference between the filtered video and the original image.

My default filter intensity is 0.5. If you find the default filter effect is not obvious, you can set it above 0.5, up to a maximum value of 1.
| Parameters | Description |
| --- | --- |
| strength | From 0 to 1, the greater the value, the more obvious the filter effect. Default value: 0.5. |

## setGreenScreenFile

#### Set green screen background video

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)). The green screen feature enabled by this interface does not have the capability to intelligently remove backgrounds; it requires a green backdrop behind the subject to assist in creating effects.
| Parameters | Description |
| --- | --- |
| path | Path to MP4 format video file; set to null to disable the effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setEyeScaleLevel

#### Set big eyes filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| eyeScaleLevel | Big eye level, value range 0-9; 0 means off, 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setFaceSlimLevel

#### Set face slimming filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| faceSlimLevel | Slim face level, value range 0-9; 0 means off, 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setFaceVLevel

#### Set V face filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| faceVLevel | V face level, value range 0-9; 0 means off, 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setChinLevel

#### Set chin stretch or shrink

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| chinLevel | Effect level of the chin lengthening/shortening filter. Value range: -9–9; 0 indicates that the filter is disabled, a value smaller than 0 indicates that the chin is shortened, while a value greater than 0 indicates that the chin is lengthened. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setFaceShortLevel

#### Set short face filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| faceShortLevel | Effect level of the face shortening filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setFaceNarrowLevel

#### Set narrow face filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| level | Effect level of the narrow face filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setNoseSlimLevel

#### Set nose slimming filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| noseSlimLevel | Effect level of the nose slimming filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setEyeLightenLevel

#### Set eye-catching filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| eyeLightenLevel | Effect level of the eye brightening filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setToothWhitenLevel

#### Set teeth whitening filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| toothWhitenLevel | Effect level of the teeth whitening filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setWrinkleRemoveLevel

#### Set wrinkle removal filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| wrinkleRemoveLevel | Effect level of the wrinkle removal filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setPounchRemoveLevel

#### Set puffiness removal filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| pounchRemoveLevel | Effect level of the eye bag removal filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setSmileLinesRemoveLevel

#### Set nasolabial fold removal filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| smileLinesRemoveLevel | Effect level of the nasolabial folds filter. Value range: 0–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setForeheadLevel

#### Set hairline adjustment filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| foreheadLevel | Effect level of the hairline lowering filter. Value range: -9–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setEyeDistanceLevel

#### Set interpupillary distance

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| eyeDistanceLevel | Effect level of the eye distance adjustment filter. Value range: -9–9; 0 indicates that the filter is disabled, a value smaller than 0 indicates a wider eye distance, while a value greater than 0 indicates a shorter eye distance. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setEyeAngleLevel

#### Set eye corner adjustment filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| eyeAngleLevel | Effect level of the eye corner adjustment filter. Value range: -9–9; 0 indicates that the filter is disabled, and 9 means the most noticeable effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setMouthShapeLevel

#### Set mouth shape adjustment filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| mouthShapeLevel | Effect level of the mouth shape filter. Value range: -9–9; 0 indicates that the filter is disabled, a value smaller than 0 indicates stretching, while a value greater than 0 indicates narrowing. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setNoseWingLevel

#### Set nose wing adjustment filter effect level

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| noseWingLevel | Effect level of the nose wing adjustment filter. Value range: -9–9; 0 indicates that the filter is disabled, a value smaller than 0 indicates stretching, while a value greater than 0 indicates narrowing. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setNosePositionLevel

#### Set nose position

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| nosePositionLevel | Nose position level. Value range: -9–9; 0 indicates that the effect is disabled, a value smaller than 0 indicates raising, while a value greater than 0 indicates lowering. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setLipsThicknessLevel

#### Set lip thickness

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| lipsThicknessLevel | Effect level of the lip thickness filter. Value range: -9–9; 0 indicates that the filter is disabled, a value smaller than 0 indicates stretching, while a value greater than 0 indicates narrowing. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setFaceBeautyLevel

#### Set face shape

This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| faceBeautyLevel | Beauty level. Value range: 0–9; 0 indicates that the filter is disabled, and the greater the value, the more noticeable the effect. |

#### Return value description:

0: Success; -5: The current License does not support the corresponding feature.

## setMotionTmpl

#### Select AI animation pendant
This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
| Parameters | Description |
| --- | --- |
| tmplDir | Directory where the animated effect material files are located. |
| tmplName | Animation pendant name. |

## setMotionMute

#### Mute while playing animation material
This interface is only effective in the Enterprise Edition SDK (the old version has been discontinued; for advanced beauty feature in the new version SDK, please refer to [Tencent Beauty Effects SDK](https://cloud.tencent.com/document/product/647/68504)).
Some pendants have sound effects. This API can mute the sound effects during playback.
| Parameters | Description |
| --- | --- |
| motionMute | YES: mutes; NO: does not mute. |

## TXBeautyStyle

#### Beauty Filter (Skin Smoothing) algorithm
TRTC offers various skin smoothing algorithms. You can choose the one that best fits your product positioning.
| Enumeration | Value | Description |
| --- | --- | --- |
| TXBeautyStyleSmooth | 0 | Smooth: This aggressive algorithm results in a more pronounced skin smoothing effect, suitable for show live streaming. |
| TXBeautyStyleNature | 1 | Natural: This algorithm retains more facial details, resulting in a more natural smoothing effect, suitable for most live streaming scenarios. |
| TXBeautyStylePitu | 2 | YouTu: Provided by YouTu Laboratory, this algorithm balances between smooth and natural, retaining more skin details than smooth and providing more smoothing than natural. |
