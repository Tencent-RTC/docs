> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: Android

### TUIKit 源码是否支持 Androidx？

TUIKit 最新源码已经支持 Androidx。

### 登录报错 6012 或  TLSSDK exchange ticket fail ？

如果您当前是即时通信 IM 体验版，需要升级为专业版，升级后可正常登录，可以到 [即时通信 IM 购买页面](https://buy.cloud.tencent.com/avc) 进行购买升级，详细价格说明请参阅 [产品价格](https://cloud.tencent.com/document/product/269/11673)。

### 出现 6013 SDK 未初始化错误？

如果出现 6013 SDK 未初始化错误，您可以尝试以下方式排查：
1. 查看是否没有登录成功就进行收发消息等其他操作；

2. 查看是否登录时被其它终端踢掉，IM SDK 默认一个账号仅能在一个终端上登录。处理方式请参考 [多终端同时登录](https://cloud.tencent.com/document/product/269/3665#.E5.A4.9A.E7.AB.AF.E7.99.BB.E5.BD.95) 文档；

3. Android 请关注库文件是否未能全部加载，或是使用过程中被系统回收。

### code: 6205 desc: QALSERVICE not ready？

在初始化后调用了 `stopQALService` 导致此错误，可以参考客户分享的 [配置方法](https://blog.csdn.net/qq_16131393/article/details/54895733)。

### 发送表情，消息列表显示为空、或者乱码?

即时通信 IM 不提供表情包，具体的解析需要自己对齐。
表情使用方式有两种方式：
- 一种是使用 TIMFaceElem 中的 index，标识表情的索引，例如 Android 和 iOS 两端都有同一套表情图，索引2为笑脸，index=2 就表示笑脸，两端发送和接收都显示同一张索引表情图片即可。

- 另一种是使用 TIMFaceElem 中的 data，例如表情图片是由字符串命名，smile 表示笑脸，可在 data 中存储 smile，iOS 和 Android 两端都通过 data 作为 key 找到对应表情图片进行显示。

   另外也可以两个字段都使用，如 data 表示哪一套表情，index 表示这套表情的哪个索引，可以实现类似 QQ 的多种不同表情效果。甚至可以在 data 数据中存储更为复杂的数据结构，只要多端解析规则一致即可。

### 有时进行操作时返回错误码：10002?

在需要进行服务端验证的操作时，如果网络异常、超时或者票据切换失败，就会返回此状态码，遇到此状态码时稍后重试即可。

### 收发消息时，收到错误码 6200 或 6201？

返回此状态码时，是客户端在网络离线、超时或无网络访问时出现，6200 的定义为请求时没有网络，6201 的定义为响应时没有网络，遇到此状态码时，请检查网络或等待网络恢复后重试。

### 收发消息时返回错误码：20003？

请检查用户账号（UserID）是否已在腾讯云导入，当 UserID 无效或 UserID 未导入腾讯即时通信 IM 时，会返回此错误码。

### 语音消息播放语音时返回错误码：6010？

通常情况是语音消息超过了漫游保存有效期，请求失败导致，可 [延长漫游消息时间](https://cloud.tencent.com/document/product/269/38656#.E5.8E.86.E5.8F.B2.E6.B6.88.E6.81.AF.E5.AD.98.E5.82.A8.E6.97.B6.E9.95.BF.E9.85.8D.E7.BD.AE) 或获取语音文件到本地播放（已过期的文件无法恢复）。但不同版本的 SDK 支持延长历史消息存储时长的消息类型不同，详情请参见 [消息存储](https://cloud.tencent.com/document/product/269/3571#MsgType)。

### 账号鉴权时返回错误码 70001 或 70003 或 70009 或 70013？

这些状态码对应的原因是 UserID 与 UserSig 不匹配，请检查 UserID 及 UserSig 的有效性。其中，70001 定义为 UserSig 已过期，70003 定义为 UserSig 被截断导致校验失败，70009 定义为 UserSig 验证失败（可能是因为生成 UserSig 时混用了其他 SDKAppID 的密钥或私钥导致），70013 定义为 UserID 不匹配。

### Web 端使用 IM SDK 时返回错误码-2或-5？
- -2：Web 端请求服务器失败，通常为网络问题，Web 端使用 HTTP Long Polling 方式向服务端请求，网络存在问题时会返回此状态码，请检查网络或重试。

- -5：登录操作未完成，SDK 未获取到服务器返回的 a2key 和 tinyID 时，直接调用其它接口会出现“tinyid或a2为空”的错误提示信息和-5的错误码。

### 在 armeabi 平台上 SDK 报"java.lang.UnsatisfiedLinkError: No implementation found for"错误该如何处理？

拷贝 imsdk 的 aar 里面的 jni/armeabi-v7a/libImSDK.so 到源码工程的 src/main/jniLibs/armeabi 目录里，并在 build.gradle 中加载即可。

### 上架 App Store 时，出现 x86_64, i386 架构错误该如何解决？

该问题是由于 App Store 不支持 x86_64, i386 架构引起的，具体解决方法如下：
1. 清空项目编译缓存：
 选择**Product**>**clean**，按住Alt变成 clean build Folder...，等待操作完成。

2. 剔除 App Store 不支持的 x86_64 和 i386 架构：
 a. 选择**TARGETS**>**Build Phases**。
 b. 单击加号，选择**New Run Script Phase**。
 c. 添加如下代码：

   ``` bash
   APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"  
   
   # This script loops through the frameworks embedded in the application and  
   
   # removes unused architectures.  
   
    find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK  
    do  
    FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)  
    FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"  
    echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"  
   
    EXTRACTED_ARCHS=()  
   
    for ARCH in $ARCHS  
    do  
    echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"  
    lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"  
    EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")  
    done  
   
    echo "Merging extracted architectures: ${ARCHS}"  
    lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"  
    rm "${EXTRACTED_ARCHS[@]}"  
   
    echo "Replacing original executable with thinned version"  
    rm "$FRAMEWORK_EXECUTABLE_PATH"  
    mv "$FRAMEWORK_EXECUTABLE_PATH-merged" "$FRAMEWORK_EXECUTABLE_PATH"  
   
    done
   ```
   

3. 重新打包上传。

### IM 各端 Demo/TUIKit 工程中演示用的表情包，开发者是否可以直接使用？

为了尊重版权，IM Demo/TUIKit 工程中默认不包含大表情元素切图。在正式上线商用前，请您替换为自己设计或拥有版权的其他表情包。请注意，下图所示的**默认小黄脸表情包版权属于腾讯云**，您可以通过升级至 [IM 企业版套餐](https://buy.cloud.tencent.com/avc) 免费使用该表情包。

---

## Platform: Android SDK

> **注意：**
> 

> 新老版本 API 请勿混合使用。
> 

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ac905c315726b517ba62421471bbecf56) | 初始化 SDK |
| [unInitSDK](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8ac73b4f71f9d9a1ca01551c919d3cdd) | unsubscribeUserInfo 反初始化 SDK |
| [addIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a2f0297e96d365013e7923275ce2a5d4e) | 添加 IM 监听 |
| [removeIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9b98e6b9ac0f883f055ef82563467b43) | 移除 IM 监听 |
| [getVersion](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8142d4e71e0ee1b8d2ec99740e2cb1ca) | 获取版本号 |
| [getServerTime](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a0f95b1e166f22d261e73fbf01987fb0f) | 获取服务器当前时间 |
| [login](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a73fc0e14c5f2f5fc06a80081479fb416) | 登录 |
| [logout](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a0398924fa1b62a8f5cc9b51673273b48) | 登出 |
| [getLoginStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a1836146275265b2a120412f18961db95) | 获取登录状态 |
| [getLoginUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ad4b2e5a7df5e786ba369054ac582007f) | 获取当前登录用户的 UserID |

## 简单消息收发接口

如果您只需要使用文本和信令（即一段自定义buffer）消息，只需要使用这套简单消息收发接口即可。
| API | 描述 |
| --- | --- |
| [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd96fd1591e41f031421c0655d8e5d6b) | 设置基本消息（文本消息和自定义消息）的事件监听器，请不要同  [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aaccdec10b9fbee5e43eaf908e359c823)  混用 |
| [removeSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a86ac462d87f652960d2600a52009849a) | 移除基本消息（文本消息和自定义消息）的事件监听器 |
| [sendC2CTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a59a8ba6e4a973b4c40a09ae7dfdc6981) | 发送单聊（C2C）普通文本消息 |
| [sendC2CCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af3e08b936df77210c6cdd0ce5c7fa87f) | 发送单聊（C2C）自定义（信令）消息 |
| [sendGroupTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a56359fd1ce0a96f289dcd4bef522fb52) | 发送群聊普通文本消息 |
| [sendGroupCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afbce8ff97be0a3a42c7dc826d316f2c2) | 发送群聊自定义（信令）消息 |

## 信令接口
| API | 描述 |
| --- | --- |
| [addSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a862073ac16de7f02e5f97b8cbe7eb028) | 添加信令监听 |
| [removeSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a72f6c032de1b0dbaabb227be54d0bcfc) | 移除信令监听 |
| [invite](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a3c0592962ef89e1075f3136fc7117da0) | 邀请某个人 |
| [inviteInGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a4d166ca73210308fbd79fca748145671) | 邀请群内的某些人 |
| [cancel](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a9d69707620f038d6e47356cdaa3ab9bd) | 邀请方取消邀请 |
| [accept](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a4cd3629a0952db7c59186e0c222e17a0) | 接收方接收邀请 |
| [reject](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ad9510bf8a333189fd1a0c1eafbde2266) | 接收方拒绝邀请 |
| [getSignalingInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ab303f20f53de134e6f6ebe5f9f9bcad0) | 获取信令信息 |
| [addInvitedSignaling](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ac50301e05e1672b771dc2c92fadff8de) | 添加邀请信令（可以用于群离线推送消息触发的邀请信令） |
| [modifyInvitation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a5f3eba1a04cfd667b409e0b90478c895) | 修改邀请信令 |

## 高级消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（简单消息接口和高级消息接口请不要混用）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aaccdec10b9fbee5e43eaf908e359c823) | 设置高级消息的事件监听器，请不要同 [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd96fd1591e41f031421c0655d8e5d6b) 混用 |
| [removeAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a44e1e9126bf5b30234330fe19259cd93) | 移除高级消息的事件监听器 |
| [createTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a3ea254cd12aa0bcfd004f26f759b76a0) | 创建文本消息 |
| [createCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a313b1ea616f082f535946c83edd2cc7f) | 创建自定义消息 |
| [createImageMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#adef5bc7a67b9a69f70f6417fd810d4b1) | 创建图片消息 |
| [createSoundMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7e661ce2b4eba1535bd04f3b6539b9dc) | 创建语音消息 |
| [createVideoMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ada17dbc78e9876a8f3a9fd24a73752b5) | 创建视频消息 |
| [createFileMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a39e4b6609321fd188a2e156a00bb3135) | 创建文件消息 |
| [createLocationMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a67cebe27192392080fc80a86c80a4321) | 创建地理位置消息 |
| [createFaceMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7ad0f3b7eff3978c12d8c912ca164a5d) | 创建表情消息 |
| [createMergerMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#acebe275789ab49cc8abe6af5e07aa3b0) | 创建合并转发消息 |
| [createForwardMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#af8f609bfbfe99a0c65611b14159b6b4d) | 创建单条转发消息 |
| [createTargetedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a4def1515746b2840e4b82047a53b91a2) | 创建定向群消息 |
| [createAtSignedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a23c5c1305996f09c7ff004854b551877) | 创建带 @ 标记的群消息 |
| [sendMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a28e01403acd422e53e999f21ec064795) | 发送消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [setC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6524143895cdee25fabd9aeeae73a3c5) | 设置单聊消息免打扰 |
| [getC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a9693dd66432f931ac0a1f2168d899501) | 获取单聊消息免打扰状态 |
| [setGroupReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a2735427ac22485626aea278a9d465b3e) | 设置群聊消息免打扰状态 |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a21424ee9df839376ad67d824f95ceb51) | 设置全局消息免打扰状态（可实现按天重复） |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5664f8d53ae660f98c84ff2877e9e036) | 设置全局消息免打扰状态 |
| [getAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a0a0f68cf02affa07963e13e388400f51) | 获取全局消息免打扰状态 |
| [getC2CHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#afedccbe0e5229ae15e0e07b722ea39df) | 获取单聊（C2C）历史消息 |
| [getGroupHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a671e8737fcea0c05dc661c753e5b3597) | 获取群组历史消息 |
| [getHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a97fe2d6a7bab8f45b758f84df48c0b12) | 获取历史消息高级接口 |
| [revokeMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad0dfce6be749165cd90a9ff67a1308b1) | 撤回消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [modifyMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5464602189e6af536540e86e8bcbbe73) | 消息变更 |
| [markC2CMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7c09d0ba4a8018f5f9eec4760c4c7b9b) | 设置单聊（C2C）消息已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [markGroupMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ac0a65f18d361abde8a0ac16132027e69) | 设置群组消息已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [markAllMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad097a0da2ea0002f2b0f2d1d11f3a4ab) | 标记所有会话为已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [deleteMessageFromLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa31e3b48fb666b970120fc0bc6343534) | 删除本地消息 |
| [deleteMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#adb346fede13d493e415f6574df911e9a) | 删除本地及云端的消息 |
| [clearC2CHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a29aa6e75c2238c35cc609bef0e5a46ce) | 清空单聊本地及云端的消息 |
| [clearGroupHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6e1a1dce441243d0bd5ac2f8bcecb3d9) | 清空群聊本地及云端的消息 |
| [insertGroupMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a04a3f6c250f9d6c0053fd71be74f047f) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5afe4461b4a47205d2865ea94317d4aa) | 向单聊消息列表中添加一条消息 |
| [findMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad0dbaec04bc389d01f815f46c550e2fd) | 根据 msgID 查找本地消息 |
| [searchLocalMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a9364c8a0c6a0899b17c0a479b8ca848a) | 搜索本地消息 |
| [searchCloudMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a16a4a38b3f08bf7707d949ba9674102f) | 搜索云端消息 |
| [sendMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a66ec09cb444ddca989e9518d5118275d) | 发送消息已读回执 |
| [getMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a50e3bc679e196866057415a7c192abf6) | 获取消息已读回执 |
| [getGroupMessageReadMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a93c48782f3e127e8a50aef1bf8829099) | 获取群消息已读群成员列表 |
| [setMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a155f6219c2bbf7bc510beec9e905d5db) | 设置消息扩展 |
| [getMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a832534327c08326d045c44b02f7ddbb7) | 获取消息扩展 |
| [deleteMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a25c02e90cd0940d34fe3bbfd803cc278) | 删除消息扩展 |
| [addMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#af47c3e1fb1ac73f7e2ba7bf739c9452a) | 添加消息回应 |
| [removeMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a8af85ef9a47c6d498c601dad1370de32) | 删除消息回应 |
| [getMessageReactions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa6d5f0421950c7354dd4f0de48814881) | 批量拉取多条消息回应信息 |
| [getAllUserListOfMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6af80059a956df9948286dc3007d4c1f) | 分页拉取消息回应全部用户资料 |
| [translateText](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a1e1806c27bc7b76a3b816492ed9cbe5c) | 翻译文本消息 |
| [pinGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa345a7876bf0df29491429329a10f469) | 设置群消息置顶 |
| [getPinnedGroupMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a35c28770388fa0b1c51946b314287786) | 获取已置顶的群消息列表 |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work）	：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public）	：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。该功能支持终端 SDK 5.8.1668增强版及以上版本、Web SDK 2.17.0及以上版本，需 [购买旗舰版或企业版套餐包](https://buy.cloud.tencent.com/avc?from=17182) 并在 [控制台](https://console.cloud.tencent.com/im) > **功能配置 **> **群组配置 **> **群功能配置 **> **社群**中开通。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [addGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8dde0362f00a454945e7811c8a726a38) | 添加群组监听器 |
| [removeGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ab4d57bd5fd4b2d71c29daaeee8b9b760) | 移除群组监听器 |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af836e4912f668dddf6cc679233cfb0bb) | 创建群组（简单版本） |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a121d53137a38d0fc0bc8a8e0a9c55647) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ad64a09bea508672d6d5a402b3455b564) | 加入群组 |
| [quitGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a6d140dbeb44906de9cb69f69c2ce5919) | 退出群组 |
| [dismissGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd0221c0c842a6dcfa0acc657e50caeb) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a0199b7cacc6919938d8342474bd252da) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ada614335043d548c11f121500e279154) | 拉取群资料 |
| [searchGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a94a72082b7e2682942f35196a7e28023) | 搜索本地群资料 |
| [searchCloudGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a322641b6386d4698dd1611fee69891e0) | 搜索云端群资料 |
| [setGroupInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad4ceef92975fa00c4a5dddc8f7e1edcf) | 修改群资料 |
| [initGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a17569b57abc77adb6be9356b9eb70182) | 初始化群属性 |
| [setGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a3ec31101e4763dab7a1c99a71bc3da08) | 设置群属性 |
| [deleteGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a45f211bafddc58bf5e199e18a6814578) | 删除群属性 |
| [getGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ade2155fb24ed1c0b8eb976e146c14e3d) | 获取群属性 |
| [getGroupOnlineMemberCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a56840105a4b3371eeab2046d8c300bce) | 获取群在线人数 |
| [setGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ab2359bff0ebe5a07a87242023206989f) | 设置群计数器 |
| [getGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a3f70b0f1054a7bf78a9069a01b842cad) | 获取群计数器 |
| [increaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad770cab3620a21671d7f83776d56814e) | 递增群计数器 |
| [decreaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad5841f8f77442c8d0cf1a209a55db6c2) | 递减群计数器 |
| [getGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a69fc0831aacaa0585c1855f4c91320be) | 获取群成员列表 |
| [getGroupMembersInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#adb08e1c4fa9aff407c7b2678757f66d5) | 获取指定的群成员资料 |
| [searchGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a493fb73258019961f3ca8934ff625b0a) | 搜索本地群成员资料 |
| [searchCloudGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a1db4218077a0db6c623a19d66ba72b5a) | 搜索云端群成员资料 |
| [setGroupMemberInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a6f1cf8ede41348b4cd7b63b8e4caa77b) | 修改指定的群成员资料 |
| [muteGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a450230c4d129611e1b0519827ec0f8b5) | 禁言 |
| [muteAllGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ab3f057b439e976b17dcc0e2e9be5e7d1) | 禁言全体群成员，只有管理员或群主能够调用 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a6da6755c6e0c46e96cb02575074a5333) | 踢人 |
| [setGroupMemberRole](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a34ebf60528d02626834f022b4ebabfa8) | 切换群成员的角色 |
| [markGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#abdd5f157904dd50012c7a93f66f85dba) | 标记群成员 |
| [transferGroupOwner](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ac16d66c8e293c2ee95c7b673e5ad80c4) | 转让群主 |
| [inviteUserToGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#afd219107653b877e446c149531d65e92) | 邀请他人入群 |
| [getGroupApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a240db7bdc023ad6fc63e9ee9b72714c4) | 获取加群的申请列表 |
| [acceptGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad743008d30c909ef0be0f8aa91102e07) | 同意某一条加群申请 |
| [refuseGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#aa518c54e77f7c0f6e7dd129d6c433acd) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a498d9d76217cbae0aa235ac928ad02d8) | 标记申请列表为已读 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#acb37b83f357fc7ee04905f8bcd5a5c67) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a52eed1b07ad64a3aa3d3561d8cd147f0) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a77c4502346e800e43c22a0f15138d699) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#acaff2edad6eb208478be9ab06d30035d) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a5d2b18a76cff650cb9bb2abf2ef07306) | 获取话题列表 |

## 社群话题相关接口

如果您需要在社群下创建话题，请使用这套接口。社群用来管理群成员，社群下的所有话题不仅可以共享社群成员，还可以独立收发消息而不相互干扰。
| API | 描述 |
| --- | --- |
| [addCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a431aca797c3047dee3b5f8db4722902d) | 添加社群监听器 |
| [removeCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a96411e5a6c3896aff3bdbcf86387e928) | 移除社群监听器 |
| [createCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acde81364ecec194dfa20720f670d8537) | 创建支持话题的社群 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acb37b83f357fc7ee04905f8bcd5a5c67) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a52eed1b07ad64a3aa3d3561d8cd147f0) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a77c4502346e800e43c22a0f15138d699) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acaff2edad6eb208478be9ab06d30035d) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a5d2b18a76cff650cb9bb2abf2ef07306) | 获取话题列表 |
| [createPermissionGroupInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a92908f8d46063504d6f8b0d0f04f3a6b) | 创建社群权限组 |
| [deletePermissionGroupFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a74e98c5144576fca599378e672b54d1f) | 删除社群权限组 |
| [modifyPermissionGroupInfoInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a2a5d15a05b455c73f5e8e8c7eab7ad0d) | 修改社群权限组 |
| [getJoinedPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#af3b2e3c995525d3f6be17fbd4f42ad2b) | 获取已加入的社群权限组列表 |
| [getPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a9f6e4a2c4f4380db7b7066f9c8a13379) | 获取社群权限组列表 |
| [addCommunityMembersToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a79757d4803e94cdfe15deb4e7789e043) | 向社群权限组添加成员 |
| [removeCommunityMembersFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a5d48a2141728bd791129eefb9d88e262) | 从社群权限组删除成员 |
| [getCommunityMemberListInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a2d8f6477d76254d4dec9bd0f1a4c8051) | 获取社群权限组成员列表 |
| [addTopicPermissionToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a7e7d5b6960571ddbf61ba5a6f7390bc9) | 向权限组添加话题权限 |
| [deleteTopicPermissionFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#af3ee95182ef6b9520cb2d1c2729e575f) | 从权限组中删除话题权限 |
| [modifyTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a52852fc6b1c18b6a3379d25ea4001acf) | 修改权限组中的话题权限 |
| [getTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a380f75b59c0ee32911a7cba6a2a88971) | 获取权限组中的话题权限 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [addConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a806534684e5d4d01b94126cd1397fee4) | 添加会话监听器 |
| [removeConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a661d479b6f704a2e319d0c744b8ad2bc) | 移除会话监听器 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf71156b8b6423e98943e25a77dc1967) | 获取会话列表 |
| [getConversationListByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf71156b8b6423e98943e25a77dc1967) | 获取会话高级接口，可以指定会话类型、标记类型、分组名等 |
| [getConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a619aaff2bb5664e094d2341819b95096) | 获取指定单个会话 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a1bb5ba2beecb4f68146e7f664124fd8b) | 获取指定多个会话 |
| [deleteConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a7a6e38c5a7431646bd4c0c4c66279077) | 删除会话 |
| [deleteConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#acd95bca253be53ce89395f77c1b42c70) | 删除会话列表 |
| [setConversationDraft](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ae7f2f52bf375dae69368eae42edb28ab) | 设置会话草稿 |
| [setConversationCustomData](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ac11ca7227145e3f359f6a3473ed600a5) | 设置会话自定义数据 |
| [pinConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a4da7467f54c891c4929152260e42f4b6) | 置顶会话 |
| [markConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#aa1dab66f08df9aef4acb0aad8cb77d72) | 标记会话 |
| [getTotalUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a08bdd15d7ee2737335a01285d7f9c44a) | 获取会话总未读数 |
| [getUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ad68a1302b6cb775e09c4b4277bcb0c96) | 获取按会话 filter 过滤的未读总数 |
| [subscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a27b7c9477c5a2fb4c4f05b2e499d6f61) | 注册监听指定 filter 的会话未读总数变化 |
| [unsubscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#adbf33f34fa911bbeec55cf3a4f320ba7) | 取消监听指定 filter 的会话未读总数变化 |
| [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) | 清理会话的未读消息计数 |
| [createConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a280dff193ef770efd5d878ca3e3821d5) | 创建会话分组 |
| [getConversationGroupList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ab469fbf92cfdf27d7b268e494028b589) | 获取会话分组列表 |
| [deleteConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5ec09de4e1fb5e898e4c0800b06a63bc) | 删除会话分组 |
| [renameConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a0eba052e8f21602b5dbd249ada0c18eb) | 重命名会话分组 |
| [addConversationsToGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf0cd490796ff60730aa0a8fec037d87) | 添加会话到一个会话分组 |
| [deleteConversationsFromGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a9ca6ea0ac6d8f61c7d0f8a85f14a91b9) | 从一个会话分组中删除会话 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a7ca8c0f71a9875021fc35dfcaff68d1e) | 获取用户资料 |
| [setSelfInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af004ab2f1d1458de354883f1995b678a) | 修改个人资料 |
| [subscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a4d14dda5e99d821c84dea4ae25e403af) | 订阅用户资料 |
| [unsubscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af42e383f564f7d5bb9b4d39a3e00d4d9) | 取消订阅用户资料 |
| [getUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a2428c7f87859dd85bed1730ad8d3b92a) | 查询用户状态 |
| [setSelfStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a7520045679f1493c890f2b3b5eee7b84) | 设置自己的状态 |
| [subscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9c6deb154d0042d5472ec55cfe0962bb) | 订阅用户状态 |
| [unsubscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9254db13bd53dc48a04d05ba5f116d39) | 取消订阅用户状态 |
| [searchUsers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a20281f80a6dddc7b5dd61c203a6efadb) | 搜索云端用户资料 |
| [addToBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a8804c7f47000bf1c26aa6ab744a53456) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a3dcd8f1c70dceafa94ab48796c2f26aa) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6269df2d96c910648ab2f0c43e1931c6) | 获取黑名单列表 |

## 离线推送相关接口

如果想要在 App 切后台时依然能够实时收到 IM 消息，可以使用离线推送服务。由于大陆境内尚没有统一的推送服务，Android 的离线推送需要针对不同厂商的手机进行 [逐一适配](https://cloud.tencent.com/document/product/269/75428)。
| API | 描述 |
| --- | --- |
| [setOfflinePushConfig](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a494d6cafe50ba25503979a4e0f14c28e) | 设置离线推送配置信息 |
| [doBackground](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a2b191294ac4d68a2d69e482eae1b638f) | APP 检测到应用退后台时可以调用此接口，可以用作桌面应用角标的初始化未读数量。 |
| [doForeground](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a4c2ff4eea609da1d0950648905fbf6aa) | APP 检测到应用进前台时可以调用此接口 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) >**功能配置**>**登录与消息**>**好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [addFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af09d0d2297fe73cc81b8e8941bcd35b2) | 添加关系链监听器 |
| [removeFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6a823459f109bcd8744806f8f1b8bfea) | 移除关系链监听器 |
| [getFriendList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ae478de55db21d42b72a6c5a6a5d16624) | 获取好友列表 |
| [getFriendsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a8c4e51e508d140e4a7ce5f4caf49c870) | 获取指定好友资料 |
| [setFriendInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a151b7de6219d966b4194ad7fcc8450fe) | 设置指定好友资料 |
| [searchFriends](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a3e657c9ec5d68a4c423a64d71f5f9c6e) | 搜索好友列表 |
| [addFriend](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a19d0f22aaea285e8cee85a5dd6ed9208) | 添加好友 |
| [deleteFromFriendList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af7ecf8641b58462d038a9c97bfbd4d61) | 删除好友 |
| [checkFriend](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a96bb74f3bbd1aef147ec914a81104d11) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a2dbf4da19f3b9b170089b8e8cb210166) | 获取好友申请列表 |
| [acceptFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ab69ed69330428caff6f468b7df5259fa) | 同意好友申请 |
| [refuseFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af1bcbc196015de8e7a94b1575c466f89) | 拒绝好友申请 |
| [deleteFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a5c869e3358ca74f9cd2eaebfc7298490) | 删除好友申请 |
| [setFriendApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a022b4817a31dd25707f14b8184c42675) | 设置好友申请已读 |
| [createFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#afe729e7a74d1e7fd06a5f23c155a08ae) | 新建好友分组 |
| [getFriendGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a0043ca81fdeec5d3e842e85278003d1e) | 获取分组信息 |
| [deleteFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ac9f06f447ee4452aa12e078b48023cee) | 删除好友分组 |
| [renameFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a5345957f4d75d8e57ea3b4cff9adee13) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6de9168d476ac14e21025ec5c26251df) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ae367dfec88522e96d96c5ab942e50653) | 从好友分组中删除好友 |
| [subscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a561a3334dd1f4b75de9099d161ed7f5b) | 订阅公众号 |
| [unsubscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a7569dd95b90eab4293a6f7e99dba2c6f) | 取消订阅公众号 |
| [getOfficialAccountsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a7493324aced496edd8ca7a9feac3055e) | 获取公众号列表 |
| [followUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a136b5a3e39002162738972e891c7e82d) | 关注用户 |
| [unfollowUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a4342b42c4cf9fc8bc2bea86d60f2d9c9) | 取消关注用户 |
| [getMyFollowingList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#aaa2fde74e3558d19bd6535e434ccf8ec) | 获取我的关注列表 |
| [getMyFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a614faf44b87c209825de3e65fa90ff58) | 获取我的粉丝列表 |
| [getMutualFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ac0eaedf339566bfcbd0ad5b5f2080c64) | 获取我的互关列表 |
| [getUserFollowInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a86ff97aed8e6476ff6393660c8433f5d) | 获取指定用户的 关注/粉丝/互关 数量信息 |
| [checkFollowType](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#aeb364a11188590a7e6093522f5bad415) | 检查指定用户的关注类型 |

---

## Platform: Android SDK

> **注意：**
> 

> 新老版本 API 请勿混合使用。
> 

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ac905c315726b517ba62421471bbecf56) | 初始化 SDK |
| [unInitSDK](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8ac73b4f71f9d9a1ca01551c919d3cdd) | unsubscribeUserInfo 反初始化 SDK |
| [addIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a2f0297e96d365013e7923275ce2a5d4e) | 添加 IM 监听 |
| [removeIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9b98e6b9ac0f883f055ef82563467b43) | 移除 IM 监听 |
| [getVersion](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8142d4e71e0ee1b8d2ec99740e2cb1ca) | 获取版本号 |
| [getServerTime](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a0f95b1e166f22d261e73fbf01987fb0f) | 获取服务器当前时间 |
| [login](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a73fc0e14c5f2f5fc06a80081479fb416) | 登录 |
| [logout](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a0398924fa1b62a8f5cc9b51673273b48) | 登出 |
| [getLoginStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a1836146275265b2a120412f18961db95) | 获取登录状态 |
| [getLoginUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ad4b2e5a7df5e786ba369054ac582007f) | 获取当前登录用户的 UserID |

## 简单消息收发接口

如果您只需要使用文本和信令（即一段自定义buffer）消息，只需要使用这套简单消息收发接口即可。
| API | 描述 |
| --- | --- |
| [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd96fd1591e41f031421c0655d8e5d6b) | 设置基本消息（文本消息和自定义消息）的事件监听器，请不要同  [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aaccdec10b9fbee5e43eaf908e359c823)  混用 |
| [removeSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a86ac462d87f652960d2600a52009849a) | 移除基本消息（文本消息和自定义消息）的事件监听器 |
| [sendC2CTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a59a8ba6e4a973b4c40a09ae7dfdc6981) | 发送单聊（C2C）普通文本消息 |
| [sendC2CCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af3e08b936df77210c6cdd0ce5c7fa87f) | 发送单聊（C2C）自定义（信令）消息 |
| [sendGroupTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a56359fd1ce0a96f289dcd4bef522fb52) | 发送群聊普通文本消息 |
| [sendGroupCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afbce8ff97be0a3a42c7dc826d316f2c2) | 发送群聊自定义（信令）消息 |

## 信令接口
| API | 描述 |
| --- | --- |
| [addSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a862073ac16de7f02e5f97b8cbe7eb028) | 添加信令监听 |
| [removeSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a72f6c032de1b0dbaabb227be54d0bcfc) | 移除信令监听 |
| [invite](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a3c0592962ef89e1075f3136fc7117da0) | 邀请某个人 |
| [inviteInGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a4d166ca73210308fbd79fca748145671) | 邀请群内的某些人 |
| [cancel](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a9d69707620f038d6e47356cdaa3ab9bd) | 邀请方取消邀请 |
| [accept](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a4cd3629a0952db7c59186e0c222e17a0) | 接收方接收邀请 |
| [reject](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ad9510bf8a333189fd1a0c1eafbde2266) | 接收方拒绝邀请 |
| [getSignalingInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ab303f20f53de134e6f6ebe5f9f9bcad0) | 获取信令信息 |
| [addInvitedSignaling](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#ac50301e05e1672b771dc2c92fadff8de) | 添加邀请信令（可以用于群离线推送消息触发的邀请信令） |
| [modifyInvitation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMSignalingManager.html#a5f3eba1a04cfd667b409e0b90478c895) | 修改邀请信令 |

## 高级消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（简单消息接口和高级消息接口请不要混用）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aaccdec10b9fbee5e43eaf908e359c823) | 设置高级消息的事件监听器，请不要同 [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd96fd1591e41f031421c0655d8e5d6b) 混用 |
| [removeAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a44e1e9126bf5b30234330fe19259cd93) | 移除高级消息的事件监听器 |
| [createTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a3ea254cd12aa0bcfd004f26f759b76a0) | 创建文本消息 |
| [createCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a313b1ea616f082f535946c83edd2cc7f) | 创建自定义消息 |
| [createImageMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#adef5bc7a67b9a69f70f6417fd810d4b1) | 创建图片消息 |
| [createSoundMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7e661ce2b4eba1535bd04f3b6539b9dc) | 创建语音消息 |
| [createVideoMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ada17dbc78e9876a8f3a9fd24a73752b5) | 创建视频消息 |
| [createFileMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a39e4b6609321fd188a2e156a00bb3135) | 创建文件消息 |
| [createLocationMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a67cebe27192392080fc80a86c80a4321) | 创建地理位置消息 |
| [createFaceMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7ad0f3b7eff3978c12d8c912ca164a5d) | 创建表情消息 |
| [createMergerMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#acebe275789ab49cc8abe6af5e07aa3b0) | 创建合并转发消息 |
| [createForwardMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#af8f609bfbfe99a0c65611b14159b6b4d) | 创建单条转发消息 |
| [createTargetedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a4def1515746b2840e4b82047a53b91a2) | 创建定向群消息 |
| [createAtSignedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a23c5c1305996f09c7ff004854b551877) | 创建带 @ 标记的群消息 |
| [sendMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a28e01403acd422e53e999f21ec064795) | 发送消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [setC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6524143895cdee25fabd9aeeae73a3c5) | 设置单聊消息免打扰 |
| [getC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a9693dd66432f931ac0a1f2168d899501) | 获取单聊消息免打扰状态 |
| [setGroupReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a2735427ac22485626aea278a9d465b3e) | 设置群聊消息免打扰状态 |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a21424ee9df839376ad67d824f95ceb51) | 设置全局消息免打扰状态（可实现按天重复） |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5664f8d53ae660f98c84ff2877e9e036) | 设置全局消息免打扰状态 |
| [getAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a0a0f68cf02affa07963e13e388400f51) | 获取全局消息免打扰状态 |
| [getC2CHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#afedccbe0e5229ae15e0e07b722ea39df) | 获取单聊（C2C）历史消息 |
| [getGroupHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a671e8737fcea0c05dc661c753e5b3597) | 获取群组历史消息 |
| [getHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a97fe2d6a7bab8f45b758f84df48c0b12) | 获取历史消息高级接口 |
| [revokeMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad0dfce6be749165cd90a9ff67a1308b1) | 撤回消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [modifyMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5464602189e6af536540e86e8bcbbe73) | 消息变更 |
| [markC2CMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a7c09d0ba4a8018f5f9eec4760c4c7b9b) | 设置单聊（C2C）消息已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [markGroupMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ac0a65f18d361abde8a0ac16132027e69) | 设置群组消息已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [markAllMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad097a0da2ea0002f2b0f2d1d11f3a4ab) | 标记所有会话为已读 (待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) 接口) |
| [deleteMessageFromLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa31e3b48fb666b970120fc0bc6343534) | 删除本地消息 |
| [deleteMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#adb346fede13d493e415f6574df911e9a) | 删除本地及云端的消息 |
| [clearC2CHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a29aa6e75c2238c35cc609bef0e5a46ce) | 清空单聊本地及云端的消息 |
| [clearGroupHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6e1a1dce441243d0bd5ac2f8bcecb3d9) | 清空群聊本地及云端的消息 |
| [insertGroupMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a04a3f6c250f9d6c0053fd71be74f047f) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a5afe4461b4a47205d2865ea94317d4aa) | 向单聊消息列表中添加一条消息 |
| [findMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#ad0dbaec04bc389d01f815f46c550e2fd) | 根据 msgID 查找本地消息 |
| [searchLocalMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a9364c8a0c6a0899b17c0a479b8ca848a) | 搜索本地消息 |
| [searchCloudMessages](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a16a4a38b3f08bf7707d949ba9674102f) | 搜索云端消息 |
| [sendMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a66ec09cb444ddca989e9518d5118275d) | 发送消息已读回执 |
| [getMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a50e3bc679e196866057415a7c192abf6) | 获取消息已读回执 |
| [getGroupMessageReadMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a93c48782f3e127e8a50aef1bf8829099) | 获取群消息已读群成员列表 |
| [setMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a155f6219c2bbf7bc510beec9e905d5db) | 设置消息扩展 |
| [getMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a832534327c08326d045c44b02f7ddbb7) | 获取消息扩展 |
| [deleteMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a25c02e90cd0940d34fe3bbfd803cc278) | 删除消息扩展 |
| [addMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#af47c3e1fb1ac73f7e2ba7bf739c9452a) | 添加消息回应 |
| [removeMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a8af85ef9a47c6d498c601dad1370de32) | 删除消息回应 |
| [getMessageReactions](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa6d5f0421950c7354dd4f0de48814881) | 批量拉取多条消息回应信息 |
| [getAllUserListOfMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a6af80059a956df9948286dc3007d4c1f) | 分页拉取消息回应全部用户资料 |
| [translateText](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a1e1806c27bc7b76a3b816492ed9cbe5c) | 翻译文本消息 |
| [pinGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#aa345a7876bf0df29491429329a10f469) | 设置群消息置顶 |
| [getPinnedGroupMessageList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMMessageManager.html#a35c28770388fa0b1c51946b314287786) | 获取已置顶的群消息列表 |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work）	：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public）	：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。该功能支持终端 SDK 5.8.1668增强版及以上版本、Web SDK 2.17.0及以上版本，需 [购买旗舰版或企业版套餐包](https://buy.cloud.tencent.com/avc?from=17182) 并在 [控制台](https://console.cloud.tencent.com/im) > **功能配置 **> **群组配置 **> **群功能配置 **> **社群**中开通。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [addGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a8dde0362f00a454945e7811c8a726a38) | 添加群组监听器 |
| [removeGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ab4d57bd5fd4b2d71c29daaeee8b9b760) | 移除群组监听器 |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af836e4912f668dddf6cc679233cfb0bb) | 创建群组（简单版本） |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a121d53137a38d0fc0bc8a8e0a9c55647) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#ad64a09bea508672d6d5a402b3455b564) | 加入群组 |
| [quitGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a6d140dbeb44906de9cb69f69c2ce5919) | 退出群组 |
| [dismissGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#afd0221c0c842a6dcfa0acc657e50caeb) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a0199b7cacc6919938d8342474bd252da) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ada614335043d548c11f121500e279154) | 拉取群资料 |
| [searchGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a94a72082b7e2682942f35196a7e28023) | 搜索本地群资料 |
| [searchCloudGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a322641b6386d4698dd1611fee69891e0) | 搜索云端群资料 |
| [setGroupInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad4ceef92975fa00c4a5dddc8f7e1edcf) | 修改群资料 |
| [initGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a17569b57abc77adb6be9356b9eb70182) | 初始化群属性 |
| [setGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a3ec31101e4763dab7a1c99a71bc3da08) | 设置群属性 |
| [deleteGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a45f211bafddc58bf5e199e18a6814578) | 删除群属性 |
| [getGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ade2155fb24ed1c0b8eb976e146c14e3d) | 获取群属性 |
| [getGroupOnlineMemberCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a56840105a4b3371eeab2046d8c300bce) | 获取群在线人数 |
| [setGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ab2359bff0ebe5a07a87242023206989f) | 设置群计数器 |
| [getGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a3f70b0f1054a7bf78a9069a01b842cad) | 获取群计数器 |
| [increaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad770cab3620a21671d7f83776d56814e) | 递增群计数器 |
| [decreaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad5841f8f77442c8d0cf1a209a55db6c2) | 递减群计数器 |
| [getGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a69fc0831aacaa0585c1855f4c91320be) | 获取群成员列表 |
| [getGroupMembersInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#adb08e1c4fa9aff407c7b2678757f66d5) | 获取指定的群成员资料 |
| [searchGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a493fb73258019961f3ca8934ff625b0a) | 搜索本地群成员资料 |
| [searchCloudGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a1db4218077a0db6c623a19d66ba72b5a) | 搜索云端群成员资料 |
| [setGroupMemberInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a6f1cf8ede41348b4cd7b63b8e4caa77b) | 修改指定的群成员资料 |
| [muteGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a450230c4d129611e1b0519827ec0f8b5) | 禁言 |
| [muteAllGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ab3f057b439e976b17dcc0e2e9be5e7d1) | 禁言全体群成员，只有管理员或群主能够调用 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a6da6755c6e0c46e96cb02575074a5333) | 踢人 |
| [setGroupMemberRole](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a34ebf60528d02626834f022b4ebabfa8) | 切换群成员的角色 |
| [markGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#abdd5f157904dd50012c7a93f66f85dba) | 标记群成员 |
| [transferGroupOwner](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ac16d66c8e293c2ee95c7b673e5ad80c4) | 转让群主 |
| [inviteUserToGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#afd219107653b877e446c149531d65e92) | 邀请他人入群 |
| [getGroupApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a240db7bdc023ad6fc63e9ee9b72714c4) | 获取加群的申请列表 |
| [acceptGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#ad743008d30c909ef0be0f8aa91102e07) | 同意某一条加群申请 |
| [refuseGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#aa518c54e77f7c0f6e7dd129d6c433acd) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a498d9d76217cbae0aa235ac928ad02d8) | 标记申请列表为已读 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#acb37b83f357fc7ee04905f8bcd5a5c67) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a52eed1b07ad64a3aa3d3561d8cd147f0) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a77c4502346e800e43c22a0f15138d699) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#acaff2edad6eb208478be9ab06d30035d) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMGroupManager.html#a5d2b18a76cff650cb9bb2abf2ef07306) | 获取话题列表 |

## 社群话题相关接口

如果您需要在社群下创建话题，请使用这套接口。社群用来管理群成员，社群下的所有话题不仅可以共享社群成员，还可以独立收发消息而不相互干扰。
| API | 描述 |
| --- | --- |
| [addCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a431aca797c3047dee3b5f8db4722902d) | 添加社群监听器 |
| [removeCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a96411e5a6c3896aff3bdbcf86387e928) | 移除社群监听器 |
| [createCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acde81364ecec194dfa20720f670d8537) | 创建支持话题的社群 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acb37b83f357fc7ee04905f8bcd5a5c67) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a52eed1b07ad64a3aa3d3561d8cd147f0) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a77c4502346e800e43c22a0f15138d699) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#acaff2edad6eb208478be9ab06d30035d) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a5d2b18a76cff650cb9bb2abf2ef07306) | 获取话题列表 |
| [createPermissionGroupInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a92908f8d46063504d6f8b0d0f04f3a6b) | 创建社群权限组 |
| [deletePermissionGroupFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a74e98c5144576fca599378e672b54d1f) | 删除社群权限组 |
| [modifyPermissionGroupInfoInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a2a5d15a05b455c73f5e8e8c7eab7ad0d) | 修改社群权限组 |
| [getJoinedPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#af3b2e3c995525d3f6be17fbd4f42ad2b) | 获取已加入的社群权限组列表 |
| [getPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a9f6e4a2c4f4380db7b7066f9c8a13379) | 获取社群权限组列表 |
| [addCommunityMembersToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a79757d4803e94cdfe15deb4e7789e043) | 向社群权限组添加成员 |
| [removeCommunityMembersFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a5d48a2141728bd791129eefb9d88e262) | 从社群权限组删除成员 |
| [getCommunityMemberListInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a2d8f6477d76254d4dec9bd0f1a4c8051) | 获取社群权限组成员列表 |
| [addTopicPermissionToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a7e7d5b6960571ddbf61ba5a6f7390bc9) | 向权限组添加话题权限 |
| [deleteTopicPermissionFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#af3ee95182ef6b9520cb2d1c2729e575f) | 从权限组中删除话题权限 |
| [modifyTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a52852fc6b1c18b6a3379d25ea4001acf) | 修改权限组中的话题权限 |
| [getTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMCommunityManager.html#a380f75b59c0ee32911a7cba6a2a88971) | 获取权限组中的话题权限 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [addConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a806534684e5d4d01b94126cd1397fee4) | 添加会话监听器 |
| [removeConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a661d479b6f704a2e319d0c744b8ad2bc) | 移除会话监听器 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf71156b8b6423e98943e25a77dc1967) | 获取会话列表 |
| [getConversationListByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf71156b8b6423e98943e25a77dc1967) | 获取会话高级接口，可以指定会话类型、标记类型、分组名等 |
| [getConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a619aaff2bb5664e094d2341819b95096) | 获取指定单个会话 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a1bb5ba2beecb4f68146e7f664124fd8b) | 获取指定多个会话 |
| [deleteConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a7a6e38c5a7431646bd4c0c4c66279077) | 删除会话 |
| [deleteConversationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#acd95bca253be53ce89395f77c1b42c70) | 删除会话列表 |
| [setConversationDraft](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ae7f2f52bf375dae69368eae42edb28ab) | 设置会话草稿 |
| [setConversationCustomData](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ac11ca7227145e3f359f6a3473ed600a5) | 设置会话自定义数据 |
| [pinConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a4da7467f54c891c4929152260e42f4b6) | 置顶会话 |
| [markConversation](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#aa1dab66f08df9aef4acb0aad8cb77d72) | 标记会话 |
| [getTotalUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a08bdd15d7ee2737335a01285d7f9c44a) | 获取会话总未读数 |
| [getUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ad68a1302b6cb775e09c4b4277bcb0c96) | 获取按会话 filter 过滤的未读总数 |
| [subscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a27b7c9477c5a2fb4c4f05b2e499d6f61) | 注册监听指定 filter 的会话未读总数变化 |
| [unsubscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#adbf33f34fa911bbeec55cf3a4f320ba7) | 取消监听指定 filter 的会话未读总数变化 |
| [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5887d1954d32fad68bc569e6f136770d) | 清理会话的未读消息计数 |
| [createConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a280dff193ef770efd5d878ca3e3821d5) | 创建会话分组 |
| [getConversationGroupList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#ab469fbf92cfdf27d7b268e494028b589) | 获取会话分组列表 |
| [deleteConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a5ec09de4e1fb5e898e4c0800b06a63bc) | 删除会话分组 |
| [renameConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a0eba052e8f21602b5dbd249ada0c18eb) | 重命名会话分组 |
| [addConversationsToGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#abf0cd490796ff60730aa0a8fec037d87) | 添加会话到一个会话分组 |
| [deleteConversationsFromGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMConversationManager.html#a9ca6ea0ac6d8f61c7d0f8a85f14a91b9) | 从一个会话分组中删除会话 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a7ca8c0f71a9875021fc35dfcaff68d1e) | 获取用户资料 |
| [setSelfInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af004ab2f1d1458de354883f1995b678a) | 修改个人资料 |
| [subscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a4d14dda5e99d821c84dea4ae25e403af) | 订阅用户资料 |
| [unsubscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af42e383f564f7d5bb9b4d39a3e00d4d9) | 取消订阅用户资料 |
| [getUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a2428c7f87859dd85bed1730ad8d3b92a) | 查询用户状态 |
| [setSelfStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a7520045679f1493c890f2b3b5eee7b84) | 设置自己的状态 |
| [subscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9c6deb154d0042d5472ec55cfe0962bb) | 订阅用户状态 |
| [unsubscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a9254db13bd53dc48a04d05ba5f116d39) | 取消订阅用户状态 |
| [searchUsers](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a20281f80a6dddc7b5dd61c203a6efadb) | 搜索云端用户资料 |
| [addToBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a8804c7f47000bf1c26aa6ab744a53456) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a3dcd8f1c70dceafa94ab48796c2f26aa) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6269df2d96c910648ab2f0c43e1931c6) | 获取黑名单列表 |

## 离线推送相关接口

如果想要在 App 切后台时依然能够实时收到 IM 消息，可以使用离线推送服务。由于大陆境内尚没有统一的推送服务，Android 的离线推送需要针对不同厂商的手机进行 [逐一适配](https://cloud.tencent.com/document/product/269/75428)。
| API | 描述 |
| --- | --- |
| [setOfflinePushConfig](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a494d6cafe50ba25503979a4e0f14c28e) | 设置离线推送配置信息 |
| [doBackground](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a2b191294ac4d68a2d69e482eae1b638f) | APP 检测到应用退后台时可以调用此接口，可以用作桌面应用角标的初始化未读数量。 |
| [doForeground](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMOfflinePushManager.html#a4c2ff4eea609da1d0950648905fbf6aa) | APP 检测到应用进前台时可以调用此接口 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) >**功能配置**>**登录与消息**>**好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [addFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af09d0d2297fe73cc81b8e8941bcd35b2) | 添加关系链监听器 |
| [removeFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6a823459f109bcd8744806f8f1b8bfea) | 移除关系链监听器 |
| [getFriendList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ae478de55db21d42b72a6c5a6a5d16624) | 获取好友列表 |
| [getFriendsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a8c4e51e508d140e4a7ce5f4caf49c870) | 获取指定好友资料 |
| [setFriendInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a151b7de6219d966b4194ad7fcc8450fe) | 设置指定好友资料 |
| [searchFriends](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a3e657c9ec5d68a4c423a64d71f5f9c6e) | 搜索好友列表 |
| [addFriend](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a19d0f22aaea285e8cee85a5dd6ed9208) | 添加好友 |
| [deleteFromFriendList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af7ecf8641b58462d038a9c97bfbd4d61) | 删除好友 |
| [checkFriend](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a96bb74f3bbd1aef147ec914a81104d11) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a2dbf4da19f3b9b170089b8e8cb210166) | 获取好友申请列表 |
| [acceptFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ab69ed69330428caff6f468b7df5259fa) | 同意好友申请 |
| [refuseFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#af1bcbc196015de8e7a94b1575c466f89) | 拒绝好友申请 |
| [deleteFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a5c869e3358ca74f9cd2eaebfc7298490) | 删除好友申请 |
| [setFriendApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a022b4817a31dd25707f14b8184c42675) | 设置好友申请已读 |
| [createFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#afe729e7a74d1e7fd06a5f23c155a08ae) | 新建好友分组 |
| [getFriendGroups](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a0043ca81fdeec5d3e842e85278003d1e) | 获取分组信息 |
| [deleteFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ac9f06f447ee4452aa12e078b48023cee) | 删除好友分组 |
| [renameFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a5345957f4d75d8e57ea3b4cff9adee13) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a6de9168d476ac14e21025ec5c26251df) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ae367dfec88522e96d96c5ab942e50653) | 从好友分组中删除好友 |
| [subscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a561a3334dd1f4b75de9099d161ed7f5b) | 订阅公众号 |
| [unsubscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a7569dd95b90eab4293a6f7e99dba2c6f) | 取消订阅公众号 |
| [getOfficialAccountsInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a7493324aced496edd8ca7a9feac3055e) | 获取公众号列表 |
| [followUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a136b5a3e39002162738972e891c7e82d) | 关注用户 |
| [unfollowUser](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a4342b42c4cf9fc8bc2bea86d60f2d9c9) | 取消关注用户 |
| [getMyFollowingList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#aaa2fde74e3558d19bd6535e434ccf8ec) | 获取我的关注列表 |
| [getMyFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a614faf44b87c209825de3e65fa90ff58) | 获取我的粉丝列表 |
| [getMutualFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#ac0eaedf339566bfcbd0ad5b5f2080c64) | 获取我的互关列表 |
| [getUserFollowInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#a86ff97aed8e6476ff6393660c8433f5d) | 获取指定用户的 关注/粉丝/互关 数量信息 |
| [checkFollowType](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMFriendshipManager.html#aeb364a11188590a7e6093522f5bad415) | 检查指定用户的关注类型 |
