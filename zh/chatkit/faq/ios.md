> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: iOS

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

## Platform: iOS SDK

> **注意：**
> 

> **新老版本 API 请勿混合使用**。
> 

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a8035eed3a7c9b3b1c229196ac7bc5da6) | 初始化 |
| [unInitSDK](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a286e5358ec4cd0a8f9c66f4d2d7d4544) | 反初始化 |
| [addIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac569656a58908afba491710a8cb3c8d9) | 添加 IM 监听 |
| [removeIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a2e2a7e64bf51888c98636e5974a8aca7) | 移除 IM 监听 |
| [getVersion](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ae8281def98e669d701171ede7aa3c176) | 获取版本号 |
| [getServerTime](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a4adcf2642bcb706cd6cfe7e5c5f85f06) | 获取服务器当前时间 |
| [login](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a38c42943046acdaf615915c9422af07c) | 登录 |
| [logout](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a20b495d7f7a231ea33507ca4a79f811f) | 退出登录 |
| [getLoginUser](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a78ca7f39bca860e46620f8f766508fb0) | 获取登录用户 |
| [getLoginStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#acfd2f6366780badf80ebf66d95550f89) | 获取登录状态 |

## 简单消息收发接口

如果您只需要使用文本和信令（即一段自定义buffer）消息，只需要使用这套简单消息收发接口即可。
| API | 描述 |
| --- | --- |
| [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a149cdf7924aa13746692d18d605def88) | 设置基本消息（文本消息和自定义消息）的事件监听器，请不要同 [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acf794752cc6bfa786aea5cd7fabadfab) 混用 |
| [removeSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#afa3040f676105f3fb78d4835ee3c898b) | 移除基本消息（文本消息和自定义消息）的事件监听器 |
| [sendC2CTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a50d63810093eccc0491d058d0b883618) | 发送单聊（C2C）普通文本消息 |
| [sendC2CCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a5fc3b87e9782e679c08926d07e486b90) | 发送单聊（C2C）自定义（信令）消息 |
| [sendGroupTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a07788874071937fac6c7093185b145f7) | 发送群聊普通文本消息 |
| [sendGroupCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a537560a58d49aad36406f6d9db6ded65) | 发送群聊自定义（信令）消息 |

## 信令接口
| API | 描述 |
| --- | --- |
| [addSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a3a7fde0d4d5a342bd93299deaf98e1d1) | 添加信令监听 |
| [removeSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#ae730297ec335735eee3c2f3c464bde33) | 移除信令监听 |
| [invite](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a594071fa1a70373582ed6082c581b332) | 邀请某个人 |
| [inviteInGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#ac01a4e703c925aaf5f78df67faca15be) | 邀请群内的某些人 |
| [cancel](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#acaac35e5db28db783420b5eb39d53e6f) | 邀请方取消邀请 |
| [accept](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a1ffb6daba9deed8780f869205daf7771) | 接收方接收邀请 |
| [reject](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a39e685924aaa4d22daa88f2ec96aa827) | 接收方拒绝邀请 |
| [getSignalingInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a0b149836793b8f2d54889b1c3ae40362) | 获取信令信息 |
| [addInvitedSignaling](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#aedfb31fdd3289af36c092b55adeed231) | 添加邀请信令（可以用于群离线推送消息触发的邀请信令） |
| [modifyInvitation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a1f1d6f5053c07996a611e284ac15cbb5) | 修改邀请信令 |

## 高级消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（简单消息接口和高级消息接口请不要混用）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acf794752cc6bfa786aea5cd7fabadfab) | 设置高级消息的事件监听器，请不要同 [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a149cdf7924aa13746692d18d605def88) 混用 |
| [removeAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a28aeebff4a791c9bb8f91a4f61e020e6) | 移除高级消息监听器 |
| [createTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a609f4d4c374d9df3abf9974ff8112fc3) | 创建文本消息 |
| [createTextAtMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aaebbd8ed9b9766d01f996ec722744346) | 创建 @ 文本消息 |
| [createCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7a38c42f63a4e0c9e89f6c56dd0da316) | 创建自定义消息 |
| [createImageMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a23033a764f0d95ce83c52f3cdeea4137) | 创建图片消息 |
| [createSoundMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9073007806fa186b8999ce656555032a) | 创建语音消息 |
| [createVideoMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a233a9ee5ef2ea371206005d109757f18) | 创建视频消息 |
| [createFileMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e487ae9541111038ebed900ab639d4c) | 创建文件消息 |
| [createLocationMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2a997472dd62d794cfd4e3a42cfab930) | 创建地理位置消息 |
| [createFaceMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab7a593be2cca1c8eddd7e73255f3f34a) | 创建表情消息 |
| [createMergerMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2943bb31403aeb22f8582cd9966cf13e) | 创建合并转发消息 |
| [createForwardMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a05d088b7d9883e18af41355cdd3f4562) | 创建单条转发消息 |
| [createTargetedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a8bddd2f566a53362b4da5448fdd18fbc) | 创建定向群消息 |
| [createAtSignedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#adfb065794694a2061af2642f18c4aeb7) | 创建带 @ 标记的群消息 |
| [sendMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a681947465d6ab718da40f7f983740a21) | 发送消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [setC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ae628f19d856921d27081c3f40005e9d9) | 设置单聊消息免打扰 |
| [getC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a1c743a6fe1d17a21dc80e584fd1de2d1) | 获取单聊消息免打扰状态 |
| [setGroupReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a379eeef926e41ec5d48287e7fb55b80a) | 设置群聊消息免打扰状态 |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a6495713ce966d3e6cc769616cdfdb846) | 设置全局消息接收选项（可实现按天重复） |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a395aa64727b7743930366be50cc78073) | 设置全局消息接收选项 |
| [getAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#abca3b16a8f25149867108dad53d43ca0) | 获取登录用户全局消息接收选项 |
| [getC2CHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#abca63ad64f69aa4f424cf11849a9b89e) | 获取单聊（C2C）历史消息 |
| [getGroupHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e242ba327377fe74b83e8d5572d39a0) | 获取群组历史消息 |
| [getHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a99e8f00ee60df12e346548b743523218) | 获取历史消息高级接口 |
| [revokeMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2ef856a792923811e9d16ed7a101336a) | 撤回消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [modifyMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7609c2dd8550e43b23d24069200d37cb) | 消息变更 |
| [deleteMessageFromLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2bb42528f4d166ac826914094655841c) | 删除本地消息 |
| [deleteMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e394ea720ecdc10d497b63b6f2b22c4) | 删除本地及云端的消息 |
| [clearC2CHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a005c7767172d9a3980974b68c780c33b) | 清空单聊本地及云端的消息 |
| [clearGroupHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a16c01c19a285e2bd11443c868c8256e6) | 清空群聊本地及云端的消息 |
| [insertGroupMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9b312b67e4da19978b55a7b915815dfe) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acc1dccd310d1965248cff0d4fd5ca45f) | 向单聊消息列表中添加一条消息 |
| [findMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a4a0c47d706d8784656225c1e9065f6f1) | 根据 msgID 查找本地消息 |
| [searchLocalMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a10878d0bd326b07ec6a605c5695c7de1) | 搜索本地消息 |
| [searchCloudMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab672a5d549893b7e22c555593be40322) | 搜索云端消息 |
| [sendMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a375af7e0f3e0f0b3135ccd517de9fdd8) | 发送消息已读回执 |
| [getMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a69192bc43e551f34f5d483dae5e70410) | 获取消息已读回执 |
| [getGroupMessageReadMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aa345a87cfa4da2983f878bb5385d0b82) | 获取群消息已读群成员列表 |
| [setMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2e8b8f7ef94d02823cfab0cf5b1e1fea) | 设置消息扩展 |
| [getMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a3ae68d2d8aeff6abd21981914836dc1a) | 获取消息扩展 |
| [deleteMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#af3fad5575625e7597a482375d7a65fa6) | 删除消息扩展 |
| [addMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a425d818c392bef6c6daeddef8c9c0cc1) | 添加消息回应 |
| [removeMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a88d3bf59edf75dd06ef9067b08b7db00) | 删除消息回应 |
| [getMessageReactions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab3d6d68379e25750ba1b0154d956e62c) | 批量拉取多条消息回应信息 |
| [getAllUserListOfMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#af26823c8deb12d0b0c5dc885431da182) | 分页拉取消息回应全部用户资料 |
| [translateText](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aee0c1e26b0401576ee82967698da35f6) | 翻译文本消息 |
| [pinGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a4bd9a32e4525e75778708bfde5b95c62) | 设置群消息置顶 |
| [getPinnedGroupMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#afee75362418c0dbd3f2337fea5865179) | 获取已置顶的群消息列表 |
| [markC2CMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acb3a67bd2fa131b50c611a48fa78f34d) | 设置单聊（C2C）消息已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |
| [markGroupMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7fc79e30877b8d77fbdfa24e057376dc) | 设置群组消息已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |
| [markAllMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a53d889a6242b5551aa3655e40967a62f) | 标记所有会话为已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work） ：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public）   ：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。该功能支持终端 SDK 5.8.1668增强版及以上版本、Web SDK 2.17.0及以上版本，需 [购买旗舰版或企业版套餐包](https://buy.cloud.tencent.com/avc?from=17182) 并在 [控制台](https://console.cloud.tencent.com/im) > **功能配置 **> **群组配置 **> **群功能配置 **> **社群**中开通。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [setGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a74de68e55d787fd1d4ec83b99cd1fcab) | 设置群组监听器（待废弃接口，请使用 addGroupListener 和 removeGroupListener 接口） |
| [addGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#af583b113cdec570b08ae80d682fba52c) | 添加群组监听器 |
| [removeGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a2b2093489bf869f70c03be39f4ed08a1) | 移除群组监听器 |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a3bbcf819c1ec70e520b7f9a42cfbb989) | 创建群组（简单版本） |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a59824434b6096180b94d8152183dcd2c) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a4762156b7a98489eb4715de53028e12a) | 加入群组 |
| [quitGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac2a43b3ada447131df0c5f19e8079be5) | 退出群组 |
| [dismissGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a5bd55cb04867985253949d8cc78f860e) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a4599e99791c150cc9f3e2492e8b4ce04) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a9bca7e5318cfed44335566a783a6b568) | 拉取群资料 |
| [searchGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ac9a960921e512621340159d82a4b5259) | 搜索本地群资料 |
| [searchCloudGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a8c3f83c844be3c8bb13fe9cec6fd658a) | 搜索云端群资料 |
| [setGroupInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa9519a479493e56d7920e40aba796144) | 修改群资料 |
| [initGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a1b3a56dfc345f1ef2a575cb36156e745) | 初始化群属性 |
| [setGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a134342ddb51d1ee83f3981ed91d26885) | 设置群属性 |
| [deleteGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa504ffca9492580ca27a45f78a87e2cb) | 删除群属性 |
| [getGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ac8a74db230669d1b49da47bb0895cbf9) | 获取群属性 |
| [getGroupOnlineMemberCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ada2eadb44f6865e9848df94fb5bae4ae) | 获取群在线人数 |
| [setGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a712b2338d3ea7b8e810111db12709c35) | 设置群计数器 |
| [getGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ab4e9e7fd4c6db5f979faf7f103dc5bd6) | 获取群计数器 |
| [increaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a58647ae926410735a0e9b83c3ae05406) | 递增群计数器 |
| [decreaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a9fb85e6cf4ad0e538de955c46833bafb) | 递减群计数器 |
| [getGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a98681b9036e73acbe8f84737b5291326) | 获取群成员列表 |
| [getGroupMembersInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a1ab284b80811bcc697d689d7b97edf04) | 获取指定的群成员资料 |
| [searchGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a35ceb734976c833047cceb8b31055b18) | 搜索本地群成员资料 |
| [searchCloudGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ae2f92bf458c5b6ef0149d66de7a01896) | 搜索云端群成员资料 |
| [setGroupMemberInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a40b97ee4b138f93e1b2073d1bdff3756) | 修改指定的群成员资料 |
| [muteGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa8a0206f75d75400b517f7e0d80fe9ee) | 禁言 |
| [muteAllGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a7749c65c3adca4a48611ab4265407cf6) | 禁言全体群成员 |
| [inviteUserToGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a942d75fdea66e22cdbd8c131cf18e1ea) | 邀请他人入群 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0581f28fddf2ade890aa62e4318d7a97) | 踢人 |
| [setGroupMemberRole](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0f1c341a3dc53d6a6557a438b0c64b65) | 切换群成员的角色 |
| [markGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a21238536e7cb2c3fd086797e7dc1b970) | 标记群成员 |
| [transferGroupOwner](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a58a2ffae60505a43984fe21bf0bc1101) | 转让群主 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0581f28fddf2ade890aa62e4318d7a97) | 踢人 |
| [getGroupApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a29c36ad685159850a30d61a6b9c637e8) | 获取加群的申请列表 |
| [acceptGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a51bb9b4f965cb3d01546fef348ac75e4) | 同意某一条加群申请 |
| [refuseGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a46aa78c54986b2c0b7cc0012a3dc94ef) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ade11e93b96206ef851641c42788132d1) | 标记申请列表为已读 |

## 社群话题相关接口

如果您需要在社群下创建话题，请使用这套接口。社群用来管理群成员，社群下的所有话题不仅可以共享社群成员，还可以独立收发消息而不相互干扰。
| API | 描述 |
| --- | --- |
| [addCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a81eb155a16e6b43927ced7bbf656dd84) | 添加社群监听器 |
| [removeCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#ae6f37357363d362bb2f1348dec7c0cb4) | 移除社群监听器 |
| [createCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#afeb826964ea5882c5077bda33b8a5853) | 创建支持话题的社群 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a17350dec83b7cd32d308a1f2b2827fdd) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a8cc04d04254867787060cf1cae0fc5b8) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a31b726136637a58b5bb246eaac41187c) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a237e2fa6e16e55143c516c5428a23936) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#af93ad10e0e2b21d6ae3c8ec45021b159) | 获取话题列表 |
| [createPermissionGroupInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a476609aac093b8d0aa3404992c4919bd) | 创建社群权限组 |
| [deletePermissionGroupFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a63e60a9a9f671f176d570ce245f8ea5f) | 删除社群权限组 |
| [modifyPermissionGroupInfoInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a70b591fc96714320a4e7b12212cc4211) | 修改社群权限组 |
| [getJoinedPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a4cb129d433f107489f11e99354643d76) | 获取已加入的社群权限组列表 |
| [getPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a63b062f9695c3d6b0fe8129b6811b1e7) | 获取社群权限组列表 |
| [addCommunityMembersToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a581cfc8070a6c6aa37bf54c0f496a2c4) | 向社群权限组添加成员 |
| [removeCommunityMembersFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a266ab0a4ca2bf77daf84b96dadea5e93) | 从社群权限组删除成员 |
| [getCommunityMemberListInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#ae9099678b494dd0c442e0075b3abab99) | 获取社群权限组成员列表 |
| [addTopicPermissionToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a538a722d29fa2e0f2841b70cb6e050f4) | 向权限组添加话题权限 |
| [deleteTopicPermissionFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a62e4be9f54405ddb0f16a158fb1b410f) | 从权限组中删除话题权限 |
| [modifyTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#acad1c1d96c6860cf2a29643fbe8b6c7f) | 修改权限组中的话题权限 |
| [getTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a7370db7c5f09c8106cd27c142d386262) | 获取权限组中的话题权限 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [setConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a95bac7330779a8a970fc7689e436257f) | 设置会话监听器 （待废弃接口，请使用 addConversationListener 和 removeConversationListener 接口） |
| [addConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a39b4f352f1740171fb56143149201cd9) | 添加会话监听器 |
| [removeConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ab9e1627559fb4259228b4e547b192c83) | 移除会话监听器 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ab1f5e86e270b122cb725266d234d9dd5) | 获取会话列表 |
| [getConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ad4b7b80fbe0cff25027371b416ede9f9) | 获取指定单个会话 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#af94d9d44e90da448a395e6d92b4e512e) | 获取指定多个会话 |
| [ getConversationListByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ac1b77eedff7f2f8742a873cf766daec9) | 获取会话高级接口，可以指定会话类型、标记类型、分组名等 |
| [deleteConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a142f5289632f29a603937f1d770748c6) | 删除会话 |
| [deleteConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a4a82f8f6e9e6b56f14e57d096b16c73c) | 删除会话列表 |
| [setConversationDraft](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a462cd163c03cdce230ed3647b414382b) | 设置会话草稿 |
| [setConversationCustomData](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#af82623b98c07f36767a47c59f5a23927) | 设置会话自定义数据 |
| [pinConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a06cefb398f5a327dff4cefe6fb18c5b8) | 置顶会话 |
| [markConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a77c02a146f774979e1e04d7334cd2d06) | 标记会话 |
| [getTotalUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#abe76208f616713a09df582a6c1665d38) | 获取会话总未读数 |
| [getUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a6ddcab75125f4b09e8bfc4521817d09d) | 获取根据 filter 过滤的会话未读总数 |
| [subscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a123e1e578a92fa68b7087857c816fbd2) | 注册监听指定 filter 的会话未读总数变化 |
| [unsubscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ae87d91af763df945ed4750cbda079bf2) | 取消监听指定 filter 的会话未读总数变化 |
| [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) | 清理会话的未读消息计数 |
| [ createConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a2f5f4587c881aa26fbdce3b4d469aa0a) | 创建会话分组 |
| [ getConversationGroupList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a037b0973be9feef207a64f2e043792ab) | 获取会话分组列表 |
| [ deleteConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#aa7b91ded9e451335bc931525839ce736) | 删除会话分组 |
| [ renameConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a1a9492196c94450b2992079cffab96a6) | 重命名会话分组 |
| [ addConversationsToGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a37c78d27216882504d2710a066478db5) | 添加会话到一个会话分组 |
| [ deleteConversationsFromGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a16ee46fa4b7278a0386be9ff633fa552) | 从一个会话分组中删除会话 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac638c1dd6ec1e5204637e4b87129cd38) | 获取用户资料 |
| [setSelfInfo](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a328a0d2d07e8d34e12effb73937c3437) | 修改个人资料 |
| [subscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a4d14dda5e99d821c84dea4ae25e403af) | 订阅用户资料 |
| [unsubscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af42e383f564f7d5bb9b4d39a3e00d4d9) | 取消订阅用户资料 |
| [getUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#abe44a4c51c686248d483674d77d71053) | 订阅用户资料 |
| [setSelfStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a7d771431a61635888bdc4def438c4328) | 取消订阅用户资料 |
| [subscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a20e6cbe409549e0d2ff62be76914e0b3) | 订阅用户状态 |
| [unsubscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a3780734bb50398ebe65155c3223542f0) | 取消订阅用户状态 |
| [searchUsers](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a978df4d80011f4da38dc80f8ab217f48) | 搜索云端用户资料 |
| [addToBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a67d998da5085b5004bb6aa8d4322022c) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aa7e69a67185eaca658ba429cf6309a5f) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0d854d64c8ae936014a8424d55508fa3) | 获取黑名单列表 |

## 离线推送相关接口

如果想要在 App 切后台时依然能够实时收到 IM 消息，可以使用离线推送服务，详细配置请参考 [离线推送](https://cloud.tencent.com/document/product/269/75429)。
| API | 描述 |
| --- | --- |
| [setAPNSListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07APNS_08.html#a62e1694cf9e1d65b76f90064cbcbb683) | 设置 APNs 监听 |
| [setAPNS](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07APNS_08.html#a73bf19c0c019e5e27ec441bc753daa9e) | 配置 APNS 推送信息 |
| [setVOIP](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07VOIP_08.html#a0bd652eed597771ca1381d0d6ea67704) | 配置 VOIP 推送信息 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) >**功能配置**>**登录与消息**>**好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [setFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acd403f586f3df84f56b1b757efb2d443) | 设置关系链与好友资料监听器（待废弃接口，请使用 addFriendListener 和 removeFriendListener 接口） |
| [addFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a1de011b63b3c20b1be519dc7ba124704) | 添加关系链监听器 |
| [removeFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aed40ccbbc4e15be79154077a6d3ec085) | 移除关系链监听器 |
| [getFriendList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a81131d76924a03ec2b593addd6e4e101) | 获取好友列表 |
| [getFriendsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a39f6752da11b595e4a5b6dcb0eb6a584) | 获取指定好友资料 |
| [setFriendInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ac258312c000c1af69fcf51dd6898b74b) | 设置指定好友资料 |
| [searchFriends](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a9a036dcc1bd65474a3d2e90f2bb6b9c6) | 搜索好友列表 |
| [addFriend](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ac1ea1c7de2bfc2b0a25e5f6b3192e304) | 添加好友 |
| [deleteFromFriendList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#af967134fb177b32d4060fedf3663ace3) | 删除好友 |
| [checkFriend](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aad1b09fab6523d9a36147b4ed4efac67) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acda7968f1e9adc12261a7e533d709a1c) | 获取好友申请列表 |
| [acceptFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a51b913927028025e2e450e6e8ce848c0) | 同意好友申请 |
| [refuseFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a406b964a6513219cfe4803f87f0f00f8) | 拒绝好友申请 |
| [deleteFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a363e0622f653694999293c595d552896) | 删除好友申请 |
| [setFriendApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ad46c70c18b8f69bf272cbf3466477a85) | 设置好友申请已读 |
| [createFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a8b33edab15ae7d179e4e2d885e7d2b7d) | 新建好友分组 |
| [getFriendGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a63f3eaae567586077d5a8d27c31e2229) | 获取分组信息 |
| [deleteFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a2dc49f2abb1238fc2d47ce6d4f14c1e7) | 删除好友分组 |
| [renameFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a93f6ba132d9706db7c74daff97a2abd0) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0265241c39600c390406ca1f8f6ff75d) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a4a14a878816c8d6a20981d1903fcf359) | 从好友分组中删除好友 |
| [subscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0209825847b854bafdd337aab3c43f02) | 订阅公众号 |
| [unsubscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a44a804583ee8dd3eac2a6cab7c8d5fe1) | 取消订阅公众号 |
| [getOfficialAccountsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acdf22b26fe5b6d40b2d7d411a361f904) | 获取公众号列表 |
| [followUser](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a7cde546df61efd1327db38c37f905d31) | 关注用户 |
| [unfollowUser](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ad15c2c5f7ba04f58046ddc23fa84b697) | 取消关注用户 |
| [getMyFollowingList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aa4f6480569a8b98066e551ae2d9cf0cf) | 获取我的关注列表 |
| [getMutualFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ada4fc3b4b1e9a044e73a4b2330d93100) | 获取我的互关列表 |
| [getUserFollowInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a6a01145bcdfba7fe49d7882fac047b29) | 获取指定用户的 关注/粉丝/互关 数量信息 |
| [checkFollowType](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a6a01145bcdfba7fe49d7882fac047b29) | 检查指定用户的关注类型 |

---

## Platform: iOS SDK

> **注意：**
> 

> **新老版本 API 请勿混合使用**。
> 

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a8035eed3a7c9b3b1c229196ac7bc5da6) | 初始化 |
| [unInitSDK](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a286e5358ec4cd0a8f9c66f4d2d7d4544) | 反初始化 |
| [addIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac569656a58908afba491710a8cb3c8d9) | 添加 IM 监听 |
| [removeIMSDKListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a2e2a7e64bf51888c98636e5974a8aca7) | 移除 IM 监听 |
| [getVersion](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ae8281def98e669d701171ede7aa3c176) | 获取版本号 |
| [getServerTime](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a4adcf2642bcb706cd6cfe7e5c5f85f06) | 获取服务器当前时间 |
| [login](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a38c42943046acdaf615915c9422af07c) | 登录 |
| [logout](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a20b495d7f7a231ea33507ca4a79f811f) | 退出登录 |
| [getLoginUser](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a78ca7f39bca860e46620f8f766508fb0) | 获取登录用户 |
| [getLoginStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#acfd2f6366780badf80ebf66d95550f89) | 获取登录状态 |

## 简单消息收发接口

如果您只需要使用文本和信令（即一段自定义buffer）消息，只需要使用这套简单消息收发接口即可。
| API | 描述 |
| --- | --- |
| [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a149cdf7924aa13746692d18d605def88) | 设置基本消息（文本消息和自定义消息）的事件监听器，请不要同 [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acf794752cc6bfa786aea5cd7fabadfab) 混用 |
| [removeSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#afa3040f676105f3fb78d4835ee3c898b) | 移除基本消息（文本消息和自定义消息）的事件监听器 |
| [sendC2CTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a50d63810093eccc0491d058d0b883618) | 发送单聊（C2C）普通文本消息 |
| [sendC2CCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a5fc3b87e9782e679c08926d07e486b90) | 发送单聊（C2C）自定义（信令）消息 |
| [sendGroupTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a07788874071937fac6c7093185b145f7) | 发送群聊普通文本消息 |
| [sendGroupCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a537560a58d49aad36406f6d9db6ded65) | 发送群聊自定义（信令）消息 |

## 信令接口
| API | 描述 |
| --- | --- |
| [addSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a3a7fde0d4d5a342bd93299deaf98e1d1) | 添加信令监听 |
| [removeSignalingListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#ae730297ec335735eee3c2f3c464bde33) | 移除信令监听 |
| [invite](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a594071fa1a70373582ed6082c581b332) | 邀请某个人 |
| [inviteInGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#ac01a4e703c925aaf5f78df67faca15be) | 邀请群内的某些人 |
| [cancel](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#acaac35e5db28db783420b5eb39d53e6f) | 邀请方取消邀请 |
| [accept](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a1ffb6daba9deed8780f869205daf7771) | 接收方接收邀请 |
| [reject](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a39e685924aaa4d22daa88f2ec96aa827) | 接收方拒绝邀请 |
| [getSignalingInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a0b149836793b8f2d54889b1c3ae40362) | 获取信令信息 |
| [addInvitedSignaling](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#aedfb31fdd3289af36c092b55adeed231) | 添加邀请信令（可以用于群离线推送消息触发的邀请信令） |
| [modifyInvitation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Signaling_08.html#a1f1d6f5053c07996a611e284ac15cbb5) | 修改邀请信令 |

## 高级消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（简单消息接口和高级消息接口请不要混用）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acf794752cc6bfa786aea5cd7fabadfab) | 设置高级消息的事件监听器，请不要同 [addSimpleMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a149cdf7924aa13746692d18d605def88) 混用 |
| [removeAdvancedMsgListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a28aeebff4a791c9bb8f91a4f61e020e6) | 移除高级消息监听器 |
| [createTextMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a609f4d4c374d9df3abf9974ff8112fc3) | 创建文本消息 |
| [createTextAtMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aaebbd8ed9b9766d01f996ec722744346) | 创建 @ 文本消息 |
| [createCustomMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7a38c42f63a4e0c9e89f6c56dd0da316) | 创建自定义消息 |
| [createImageMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a23033a764f0d95ce83c52f3cdeea4137) | 创建图片消息 |
| [createSoundMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9073007806fa186b8999ce656555032a) | 创建语音消息 |
| [createVideoMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a233a9ee5ef2ea371206005d109757f18) | 创建视频消息 |
| [createFileMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e487ae9541111038ebed900ab639d4c) | 创建文件消息 |
| [createLocationMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2a997472dd62d794cfd4e3a42cfab930) | 创建地理位置消息 |
| [createFaceMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab7a593be2cca1c8eddd7e73255f3f34a) | 创建表情消息 |
| [createMergerMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2943bb31403aeb22f8582cd9966cf13e) | 创建合并转发消息 |
| [createForwardMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a05d088b7d9883e18af41355cdd3f4562) | 创建单条转发消息 |
| [createTargetedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a8bddd2f566a53362b4da5448fdd18fbc) | 创建定向群消息 |
| [createAtSignedGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#adfb065794694a2061af2642f18c4aeb7) | 创建带 @ 标记的群消息 |
| [sendMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a681947465d6ab718da40f7f983740a21) | 发送消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [setC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ae628f19d856921d27081c3f40005e9d9) | 设置单聊消息免打扰 |
| [getC2CReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a1c743a6fe1d17a21dc80e584fd1de2d1) | 获取单聊消息免打扰状态 |
| [setGroupReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a379eeef926e41ec5d48287e7fb55b80a) | 设置群聊消息免打扰状态 |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a6495713ce966d3e6cc769616cdfdb846) | 设置全局消息接收选项（可实现按天重复） |
| [setAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a395aa64727b7743930366be50cc78073) | 设置全局消息接收选项 |
| [getAllReceiveMessageOpt](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#abca3b16a8f25149867108dad53d43ca0) | 获取登录用户全局消息接收选项 |
| [getC2CHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#abca63ad64f69aa4f424cf11849a9b89e) | 获取单聊（C2C）历史消息 |
| [getGroupHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e242ba327377fe74b83e8d5572d39a0) | 获取群组历史消息 |
| [getHistoryMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a99e8f00ee60df12e346548b743523218) | 获取历史消息高级接口 |
| [revokeMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2ef856a792923811e9d16ed7a101336a) | 撤回消息，消息对象可以由 createXXXMessage 接口创建得来 |
| [modifyMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7609c2dd8550e43b23d24069200d37cb) | 消息变更 |
| [deleteMessageFromLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2bb42528f4d166ac826914094655841c) | 删除本地消息 |
| [deleteMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9e394ea720ecdc10d497b63b6f2b22c4) | 删除本地及云端的消息 |
| [clearC2CHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a005c7767172d9a3980974b68c780c33b) | 清空单聊本地及云端的消息 |
| [clearGroupHistoryMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a16c01c19a285e2bd11443c868c8256e6) | 清空群聊本地及云端的消息 |
| [insertGroupMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a9b312b67e4da19978b55a7b915815dfe) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acc1dccd310d1965248cff0d4fd5ca45f) | 向单聊消息列表中添加一条消息 |
| [findMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a4a0c47d706d8784656225c1e9065f6f1) | 根据 msgID 查找本地消息 |
| [searchLocalMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a10878d0bd326b07ec6a605c5695c7de1) | 搜索本地消息 |
| [searchCloudMessages](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab672a5d549893b7e22c555593be40322) | 搜索云端消息 |
| [sendMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a375af7e0f3e0f0b3135ccd517de9fdd8) | 发送消息已读回执 |
| [getMessageReadReceipts](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a69192bc43e551f34f5d483dae5e70410) | 获取消息已读回执 |
| [getGroupMessageReadMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aa345a87cfa4da2983f878bb5385d0b82) | 获取群消息已读群成员列表 |
| [setMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a2e8b8f7ef94d02823cfab0cf5b1e1fea) | 设置消息扩展 |
| [getMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a3ae68d2d8aeff6abd21981914836dc1a) | 获取消息扩展 |
| [deleteMessageExtensions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#af3fad5575625e7597a482375d7a65fa6) | 删除消息扩展 |
| [addMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a425d818c392bef6c6daeddef8c9c0cc1) | 添加消息回应 |
| [removeMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a88d3bf59edf75dd06ef9067b08b7db00) | 删除消息回应 |
| [getMessageReactions](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#ab3d6d68379e25750ba1b0154d956e62c) | 批量拉取多条消息回应信息 |
| [getAllUserListOfMessageReaction](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#af26823c8deb12d0b0c5dc885431da182) | 分页拉取消息回应全部用户资料 |
| [translateText](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#aee0c1e26b0401576ee82967698da35f6) | 翻译文本消息 |
| [pinGroupMessage](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a4bd9a32e4525e75778708bfde5b95c62) | 设置群消息置顶 |
| [getPinnedGroupMessageList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#afee75362418c0dbd3f2337fea5865179) | 获取已置顶的群消息列表 |
| [markC2CMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#acb3a67bd2fa131b50c611a48fa78f34d) | 设置单聊（C2C）消息已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |
| [markGroupMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a7fc79e30877b8d77fbdfa24e057376dc) | 设置群组消息已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |
| [markAllMessageAsRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Message_08.html#a53d889a6242b5551aa3655e40967a62f) | 标记所有会话为已读（待废弃接口，请使用 [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) 接口） |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work） ：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public）   ：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。该功能支持终端 SDK 5.8.1668增强版及以上版本、Web SDK 2.17.0及以上版本，需 [购买旗舰版或企业版套餐包](https://buy.cloud.tencent.com/avc?from=17182) 并在 [控制台](https://console.cloud.tencent.com/im) > **功能配置 **> **群组配置 **> **群功能配置 **> **社群**中开通。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [setGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a74de68e55d787fd1d4ec83b99cd1fcab) | 设置群组监听器（待废弃接口，请使用 addGroupListener 和 removeGroupListener 接口） |
| [addGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#af583b113cdec570b08ae80d682fba52c) | 添加群组监听器 |
| [removeGroupListener](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a2b2093489bf869f70c03be39f4ed08a1) | 移除群组监听器 |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a3bbcf819c1ec70e520b7f9a42cfbb989) | 创建群组（简单版本） |
| [createGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a59824434b6096180b94d8152183dcd2c) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a4762156b7a98489eb4715de53028e12a) | 加入群组 |
| [quitGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac2a43b3ada447131df0c5f19e8079be5) | 退出群组 |
| [dismissGroup](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a5bd55cb04867985253949d8cc78f860e) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a4599e99791c150cc9f3e2492e8b4ce04) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a9bca7e5318cfed44335566a783a6b568) | 拉取群资料 |
| [searchGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ac9a960921e512621340159d82a4b5259) | 搜索本地群资料 |
| [searchCloudGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a8c3f83c844be3c8bb13fe9cec6fd658a) | 搜索云端群资料 |
| [setGroupInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa9519a479493e56d7920e40aba796144) | 修改群资料 |
| [initGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a1b3a56dfc345f1ef2a575cb36156e745) | 初始化群属性 |
| [setGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a134342ddb51d1ee83f3981ed91d26885) | 设置群属性 |
| [deleteGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa504ffca9492580ca27a45f78a87e2cb) | 删除群属性 |
| [getGroupAttributes](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ac8a74db230669d1b49da47bb0895cbf9) | 获取群属性 |
| [getGroupOnlineMemberCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ada2eadb44f6865e9848df94fb5bae4ae) | 获取群在线人数 |
| [setGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a712b2338d3ea7b8e810111db12709c35) | 设置群计数器 |
| [getGroupCounters](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ab4e9e7fd4c6db5f979faf7f103dc5bd6) | 获取群计数器 |
| [increaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a58647ae926410735a0e9b83c3ae05406) | 递增群计数器 |
| [decreaseGroupCounter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a9fb85e6cf4ad0e538de955c46833bafb) | 递减群计数器 |
| [getGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a98681b9036e73acbe8f84737b5291326) | 获取群成员列表 |
| [getGroupMembersInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a1ab284b80811bcc697d689d7b97edf04) | 获取指定的群成员资料 |
| [searchGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a35ceb734976c833047cceb8b31055b18) | 搜索本地群成员资料 |
| [searchCloudGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ae2f92bf458c5b6ef0149d66de7a01896) | 搜索云端群成员资料 |
| [setGroupMemberInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a40b97ee4b138f93e1b2073d1bdff3756) | 修改指定的群成员资料 |
| [muteGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#aa8a0206f75d75400b517f7e0d80fe9ee) | 禁言 |
| [muteAllGroupMembers](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a7749c65c3adca4a48611ab4265407cf6) | 禁言全体群成员 |
| [inviteUserToGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a942d75fdea66e22cdbd8c131cf18e1ea) | 邀请他人入群 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0581f28fddf2ade890aa62e4318d7a97) | 踢人 |
| [setGroupMemberRole](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0f1c341a3dc53d6a6557a438b0c64b65) | 切换群成员的角色 |
| [markGroupMemberList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a21238536e7cb2c3fd086797e7dc1b970) | 标记群成员 |
| [transferGroupOwner](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a58a2ffae60505a43984fe21bf0bc1101) | 转让群主 |
| [kickGroupMember](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a0581f28fddf2ade890aa62e4318d7a97) | 踢人 |
| [getGroupApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a29c36ad685159850a30d61a6b9c637e8) | 获取加群的申请列表 |
| [acceptGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a51bb9b4f965cb3d01546fef348ac75e4) | 同意某一条加群申请 |
| [refuseGroupApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#a46aa78c54986b2c0b7cc0012a3dc94ef) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Group_08.html#ade11e93b96206ef851641c42788132d1) | 标记申请列表为已读 |

## 社群话题相关接口

如果您需要在社群下创建话题，请使用这套接口。社群用来管理群成员，社群下的所有话题不仅可以共享社群成员，还可以独立收发消息而不相互干扰。
| API | 描述 |
| --- | --- |
| [addCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a81eb155a16e6b43927ced7bbf656dd84) | 添加社群监听器 |
| [removeCommunityListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#ae6f37357363d362bb2f1348dec7c0cb4) | 移除社群监听器 |
| [createCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#afeb826964ea5882c5077bda33b8a5853) | 创建支持话题的社群 |
| [getJoinedCommunityList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a17350dec83b7cd32d308a1f2b2827fdd) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a8cc04d04254867787060cf1cae0fc5b8) | 创建话题 |
| [deleteTopicFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a31b726136637a58b5bb246eaac41187c) | 删除话题 |
| [setTopicInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a237e2fa6e16e55143c516c5428a23936) | 修改话题信息 |
| [getTopicInfoList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#af93ad10e0e2b21d6ae3c8ec45021b159) | 获取话题列表 |
| [createPermissionGroupInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a476609aac093b8d0aa3404992c4919bd) | 创建社群权限组 |
| [deletePermissionGroupFromCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a63e60a9a9f671f176d570ce245f8ea5f) | 删除社群权限组 |
| [modifyPermissionGroupInfoInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a70b591fc96714320a4e7b12212cc4211) | 修改社群权限组 |
| [getJoinedPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a4cb129d433f107489f11e99354643d76) | 获取已加入的社群权限组列表 |
| [getPermissionGroupListInCommunity](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a63b062f9695c3d6b0fe8129b6811b1e7) | 获取社群权限组列表 |
| [addCommunityMembersToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a581cfc8070a6c6aa37bf54c0f496a2c4) | 向社群权限组添加成员 |
| [removeCommunityMembersFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a266ab0a4ca2bf77daf84b96dadea5e93) | 从社群权限组删除成员 |
| [getCommunityMemberListInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#ae9099678b494dd0c442e0075b3abab99) | 获取社群权限组成员列表 |
| [addTopicPermissionToPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a538a722d29fa2e0f2841b70cb6e050f4) | 向权限组添加话题权限 |
| [deleteTopicPermissionFromPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a62e4be9f54405ddb0f16a158fb1b410f) | 从权限组中删除话题权限 |
| [modifyTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#acad1c1d96c6860cf2a29643fbe8b6c7f) | 修改权限组中的话题权限 |
| [getTopicPermissionInPermissionGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Community_08.html#a7370db7c5f09c8106cd27c142d386262) | 获取权限组中的话题权限 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [setConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a95bac7330779a8a970fc7689e436257f) | 设置会话监听器 （待废弃接口，请使用 addConversationListener 和 removeConversationListener 接口） |
| [addConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a39b4f352f1740171fb56143149201cd9) | 添加会话监听器 |
| [removeConversationListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ab9e1627559fb4259228b4e547b192c83) | 移除会话监听器 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ab1f5e86e270b122cb725266d234d9dd5) | 获取会话列表 |
| [getConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ad4b7b80fbe0cff25027371b416ede9f9) | 获取指定单个会话 |
| [getConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#af94d9d44e90da448a395e6d92b4e512e) | 获取指定多个会话 |
| [ getConversationListByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ac1b77eedff7f2f8742a873cf766daec9) | 获取会话高级接口，可以指定会话类型、标记类型、分组名等 |
| [deleteConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a142f5289632f29a603937f1d770748c6) | 删除会话 |
| [deleteConversationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a4a82f8f6e9e6b56f14e57d096b16c73c) | 删除会话列表 |
| [setConversationDraft](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a462cd163c03cdce230ed3647b414382b) | 设置会话草稿 |
| [setConversationCustomData](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#af82623b98c07f36767a47c59f5a23927) | 设置会话自定义数据 |
| [pinConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a06cefb398f5a327dff4cefe6fb18c5b8) | 置顶会话 |
| [markConversation](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a77c02a146f774979e1e04d7334cd2d06) | 标记会话 |
| [getTotalUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#abe76208f616713a09df582a6c1665d38) | 获取会话总未读数 |
| [getUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a6ddcab75125f4b09e8bfc4521817d09d) | 获取根据 filter 过滤的会话未读总数 |
| [subscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a123e1e578a92fa68b7087857c816fbd2) | 注册监听指定 filter 的会话未读总数变化 |
| [unsubscribeUnreadMessageCountByFilter](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#ae87d91af763df945ed4750cbda079bf2) | 取消监听指定 filter 的会话未读总数变化 |
| [cleanConversationUnreadMessageCount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a820c2f598f7554428fb980df3f7357f0) | 清理会话的未读消息计数 |
| [ createConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a2f5f4587c881aa26fbdce3b4d469aa0a) | 创建会话分组 |
| [ getConversationGroupList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a037b0973be9feef207a64f2e043792ab) | 获取会话分组列表 |
| [ deleteConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#aa7b91ded9e451335bc931525839ce736) | 删除会话分组 |
| [ renameConversationGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a1a9492196c94450b2992079cffab96a6) | 重命名会话分组 |
| [ addConversationsToGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a37c78d27216882504d2710a066478db5) | 添加会话到一个会话分组 |
| [ deleteConversationsFromGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Conversation_08.html#a16ee46fa4b7278a0386be9ff633fa552) | 从一个会话分组中删除会话 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#ac638c1dd6ec1e5204637e4b87129cd38) | 获取用户资料 |
| [setSelfInfo](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a328a0d2d07e8d34e12effb73937c3437) | 修改个人资料 |
| [subscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#a4d14dda5e99d821c84dea4ae25e403af) | 订阅用户资料 |
| [unsubscribeUserInfo](https://im.sdk.qcloud.com/doc/zh-cn/classcom_1_1tencent_1_1imsdk_1_1v2_1_1V2TIMManager.html#af42e383f564f7d5bb9b4d39a3e00d4d9) | 取消订阅用户资料 |
| [getUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#abe44a4c51c686248d483674d77d71053) | 订阅用户资料 |
| [setSelfStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a7d771431a61635888bdc4def438c4328) | 取消订阅用户资料 |
| [subscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a20e6cbe409549e0d2ff62be76914e0b3) | 订阅用户状态 |
| [unsubscribeUserStatus](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a3780734bb50398ebe65155c3223542f0) | 取消订阅用户状态 |
| [searchUsers](https://im.sdk.qcloud.com/doc/zh-cn/interfaceV2TIMManager.html#a978df4d80011f4da38dc80f8ab217f48) | 搜索云端用户资料 |
| [addToBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a67d998da5085b5004bb6aa8d4322022c) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aa7e69a67185eaca658ba429cf6309a5f) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0d854d64c8ae936014a8424d55508fa3) | 获取黑名单列表 |

## 离线推送相关接口

如果想要在 App 切后台时依然能够实时收到 IM 消息，可以使用离线推送服务，详细配置请参考 [离线推送](https://cloud.tencent.com/document/product/269/75429)。
| API | 描述 |
| --- | --- |
| [setAPNSListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07APNS_08.html#a62e1694cf9e1d65b76f90064cbcbb683) | 设置 APNs 监听 |
| [setAPNS](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07APNS_08.html#a73bf19c0c019e5e27ec441bc753daa9e) | 配置 APNS 推送信息 |
| [setVOIP](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07VOIP_08.html#a0bd652eed597771ca1381d0d6ea67704) | 配置 VOIP 推送信息 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) >**功能配置**>**登录与消息**>**好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [setFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acd403f586f3df84f56b1b757efb2d443) | 设置关系链与好友资料监听器（待废弃接口，请使用 addFriendListener 和 removeFriendListener 接口） |
| [addFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a1de011b63b3c20b1be519dc7ba124704) | 添加关系链监听器 |
| [removeFriendListener](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aed40ccbbc4e15be79154077a6d3ec085) | 移除关系链监听器 |
| [getFriendList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a81131d76924a03ec2b593addd6e4e101) | 获取好友列表 |
| [getFriendsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a39f6752da11b595e4a5b6dcb0eb6a584) | 获取指定好友资料 |
| [setFriendInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ac258312c000c1af69fcf51dd6898b74b) | 设置指定好友资料 |
| [searchFriends](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a9a036dcc1bd65474a3d2e90f2bb6b9c6) | 搜索好友列表 |
| [addFriend](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ac1ea1c7de2bfc2b0a25e5f6b3192e304) | 添加好友 |
| [deleteFromFriendList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#af967134fb177b32d4060fedf3663ace3) | 删除好友 |
| [checkFriend](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aad1b09fab6523d9a36147b4ed4efac67) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acda7968f1e9adc12261a7e533d709a1c) | 获取好友申请列表 |
| [acceptFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a51b913927028025e2e450e6e8ce848c0) | 同意好友申请 |
| [refuseFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a406b964a6513219cfe4803f87f0f00f8) | 拒绝好友申请 |
| [deleteFriendApplication](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a363e0622f653694999293c595d552896) | 删除好友申请 |
| [setFriendApplicationRead](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ad46c70c18b8f69bf272cbf3466477a85) | 设置好友申请已读 |
| [createFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a8b33edab15ae7d179e4e2d885e7d2b7d) | 新建好友分组 |
| [getFriendGroups](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a63f3eaae567586077d5a8d27c31e2229) | 获取分组信息 |
| [deleteFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a2dc49f2abb1238fc2d47ce6d4f14c1e7) | 删除好友分组 |
| [renameFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a93f6ba132d9706db7c74daff97a2abd0) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0265241c39600c390406ca1f8f6ff75d) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a4a14a878816c8d6a20981d1903fcf359) | 从好友分组中删除好友 |
| [subscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a0209825847b854bafdd337aab3c43f02) | 订阅公众号 |
| [unsubscribeOfficialAccount](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a44a804583ee8dd3eac2a6cab7c8d5fe1) | 取消订阅公众号 |
| [getOfficialAccountsInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#acdf22b26fe5b6d40b2d7d411a361f904) | 获取公众号列表 |
| [followUser](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a7cde546df61efd1327db38c37f905d31) | 关注用户 |
| [unfollowUser](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ad15c2c5f7ba04f58046ddc23fa84b697) | 取消关注用户 |
| [getMyFollowingList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#aa4f6480569a8b98066e551ae2d9cf0cf) | 获取我的关注列表 |
| [getMutualFollowersList](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#ada4fc3b4b1e9a044e73a4b2330d93100) | 获取我的互关列表 |
| [getUserFollowInfo](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a6a01145bcdfba7fe49d7882fac047b29) | 获取指定用户的 关注/粉丝/互关 数量信息 |
| [checkFollowType](https://im.sdk.qcloud.com/doc/zh-cn/categoryV2TIMManager_07Friendship_08.html#a6a01145bcdfba7fe49d7882fac047b29) | 检查指定用户的关注类型 |
