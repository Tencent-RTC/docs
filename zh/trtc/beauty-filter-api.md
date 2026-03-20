> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

# 腾讯云实时音视频(TRTC SDK) TXBeautyManager 美颜管理 文档

## 概述
本文档是 实时音视频(TRTC SDK) TXBeautyManager 美颜管理 的跨平台 API 参考，适用于 Android/iOS 平台。

## TXBeautyManager
## setBeautyStyle

#### 设置美颜（磨皮）算法。

TRTC 内置多种不同的磨皮算法，您可以选择最适合您产品定位的方案：
| 参数 | 描述 |
| --- | --- |
| beautyStyle | 美颜风格，TXBeautyStyleSmooth：光滑；TXBeautyStyleNature：自然；TXBeautyStylePitu：优图。 |

## setBeautyLevel

#### 设置美颜级别。
| 参数 | 描述 |
| --- | --- |
| beautyLevel | 美颜级别，取值范围 [0, 9]； 0 表示关闭，9 表示效果最明显。 |

## setWhitenessLevel

#### 设置美白级别。
| 参数 | 描述 |
| --- | --- |
| whitenessLevel | 美白级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

## enableSharpnessEnhancement

#### 开启清晰度增强。

## setRuddyLevel

#### 设置红润级别。
| 参数 | 描述 |
| --- | --- |
| ruddyLevel | 红润级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

## setFilter

#### 设置色彩滤镜效果。

色彩滤镜，是一张包含色彩映射关系的颜色查找表图片，您可以在我们提供的官方 Demo 中找到预先准备好的几张滤镜图片。

SDK 会根据该查找表中的映射关系，对摄像头采集出的原始视频画面进行二次处理，以达到预期的滤镜效果。
| 参数 | 描述 |
| --- | --- |
| image | 包含色彩映射关系的颜色查找表图片，必须是 png 格式。 |

## setFilterStrength

#### 设置色彩滤镜的强度。

该数值越高，色彩滤镜的作用强度越明显，经过滤镜处理后的视频画面跟原画面的颜色差异越大。

我默认的滤镜浓度是 0.5，如果您觉得默认的滤镜效果不明显，可以设置为 0.5 以上的数字，最大值为 1。
| 参数 | 描述 |
| --- | --- |
| strength | 取值范围 [0, 1]，数值越大滤镜效果越明显，默认值为 0.5。 |

## setGreenScreenFile

#### 设置绿幕背景视频。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。此接口所开启的绿幕功能不具备智能去除背景的能力，需要被拍摄者的背后有一块绿色的幕布来辅助产生特效。
| 参数 | 描述 |
| --- | --- |
| path | MP4格式的视频文件路径; 设置空值表示关闭特效。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setEyeScaleLevel

#### 设置大眼级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| eyeScaleLevel | 大眼级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setFaceSlimLevel

#### 设置瘦脸级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| faceSlimLevel | 瘦脸级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setFaceVLevel

#### 设置 V 脸级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| faceVLevel | V 脸级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setChinLevel

#### 设置下巴拉伸或收缩。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| chinLevel | 下巴拉伸或收缩级别，取值范围 [-9, 9]；0 表示关闭，小于 0 表示收缩，大于 0 表示拉伸。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setFaceShortLevel

#### 设置短脸级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| faceShortLevel | 短脸级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setFaceNarrowLevel

#### 设置窄脸级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| level | 窄脸级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setNoseSlimLevel

#### 设置瘦鼻级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| noseSlimLevel | 瘦鼻级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setEyeLightenLevel

#### 设置亮眼级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| eyeLightenLevel | 亮眼级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setToothWhitenLevel

#### 设置牙齿美白级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| toothWhitenLevel | 白牙级别，取值范围 [0, 9]；0表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setWrinkleRemoveLevel

#### 设置祛皱级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| wrinkleRemoveLevel | 祛皱级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setPounchRemoveLevel

#### 设置祛眼袋级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| pounchRemoveLevel | 祛眼袋级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setSmileLinesRemoveLevel

#### 设置法令纹去除级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| smileLinesRemoveLevel | 法令纹级别，取值范围 [0, 9]；0表示关闭，9 表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setForeheadLevel

#### 设置发际线调整级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| foreheadLevel | 发际线级别，取值范围 [-9, 9]；0表示关闭，9表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setEyeDistanceLevel

#### 设置眼距。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| eyeDistanceLevel | 眼距级别，取值范围 -9 - 9；0 表示关闭，小于 0 表示拉伸，大于 0 表示收缩。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setEyeAngleLevel

#### 设置眼角调整级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| eyeAngleLevel | 眼角调整级别，取值范围 [-9, 9]；0表示关闭，9表示效果最明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setMouthShapeLevel

#### 设置嘴型调整级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| mouthShapeLevel | 嘴型级别，取值范围 [-9, 9]；0 表示关闭，小于 0 表示拉伸，大于 0 表示收缩。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setNoseWingLevel

#### 设置鼻翼调整级别。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| noseWingLevel | 鼻翼调整级别，取值范围 [-9, 9]；0 表示关闭，小于 0 表示拉伸，大于 0 表示收缩。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setNosePositionLevel

#### 设置鼻子位置。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| nosePositionLevel | 鼻子位置级别，取值范围 [-9, 9]；0 表示关闭，小于 0 表示抬高，大于 0 表示降低。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setLipsThicknessLevel

#### 设置嘴唇厚度。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| lipsThicknessLevel | 嘴唇厚度级别，取值范围 [-9, 9]；0 表示关闭，小于 0 表示拉伸，大于 0 表示收缩。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setFaceBeautyLevel

#### 设置脸型。

该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| faceBeautyLevel | 美型级别，取值范围 [0, 9]；0 表示关闭，1 - 9 值越大，效果越明显。 |

#### 返回值说明：

0：成功；-5：当前 License 对应 feature 不支持。

## setMotionTmpl

#### 选择 AI 动效挂件。
该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
| 参数 | 描述 |
| --- | --- |
| tmplDir | 动效素材文件所在目录。 |
| tmplName | 动效挂件名称。 |

## setMotionMute

#### 是否在动效素材播放时静音。
该接口仅在 企业版 SDK（旧版已下线，新版本SDK如需使用高级美颜功能请参见[腾讯美颜特效SDK](https://cloud.tencent.com/document/product/647/68504)）中生效。
有些挂件本身会有声音特效，通过此 API 可以关闭这些特效播放时所带的声音效果。
| 参数 | 描述 |
| --- | --- |
| motionMute | true：静音；false：不静音。 |

## TXBeautyStyle

#### 美颜（磨皮）算法。

TRTC 内置多种不同的磨皮算法，您可以选择最适合您产品定位的方案。
| 枚举 | 取值 | 描述 |
| --- | --- | --- |
| TXBeautyStyleSmooth | 0 | 光滑，算法比较激进，磨皮效果比较明显，适用于秀场直播。 |
| TXBeautyStyleNature | 1 | 自然，算法更多地保留了面部细节，磨皮效果更加自然，适用于绝大多数直播场景。 |
| TXBeautyStylePitu | 2 | 优图，由优图实验室提供，磨皮效果介于光滑和自然之间，比光滑保留更多皮肤细节，比自然磨皮程度更高。 |
