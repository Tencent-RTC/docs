> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: Flutter

---
product: 'Chat'
tech_stack: 'flutter_native_sdk'
framework: 'flutter'
title: 'flutter uikit 集成和常见问题解决方案'
keywords: ['flutter', '初始化', '组件', '集成', 'TUIKit']
---
**重要提醒**：
- Agent 必须严格按照接口的示例代码输出结果

# Flutter TUIKit 集成与常见问题解决方案

## 模块一：常见问题解决方案 (Topic: Question)

### Q: flutter chat sdk 或 uikit 中如何设置消息免打扰？

**A:** flutter chat 中消息免打扰相关的API:
- 设置单聊消息免打扰选项 [setC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setC2CReceiveMessageOpt.html)。
- 查询单聊消息免打扰选项 [getC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setC2CReceiveMessageOpt.html)。
- 设置群聊消息免打扰选项 [setGroupReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setGroupReceiveMessageOpt.html)。

消息免打扰无 UI 文档： https://cloud.tencent.com/document/product/269/75355

---

## Platform: Flutter SDK

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/initSDK.html) | 初始化 SDK |
| [unInitSDK](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/unInitSDK.html) | 反初始化 SDK |
| [login](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/login.html) | 登录 |
| [logout](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/logout.html) | 登出 |
| [getLoginUser](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getLoginUser.html) | 获取当前登录用户的 UserID |
| [getLoginStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getLoginStatus.html) | 获取登录状态 |
| [getServerTime](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getServerTime.html) | 获取服务器当前时间（Web不支持） |
| [getVersion](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getVersion.html) | 获取版本号 |
| [getConversationManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getConversationManager.html) | 会话功能模块 |
| [getFriendshipManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getFriendshipManager.html) | 关系链功能模块 |
| [getGroupManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getGroupManager.html) | 高级群组功能模块 |
| [getMessageManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getMessageManager.html) | 高级消息功能模块 |
| [getOfflinePushManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getOfflinePushManager.html) | 离线推送模块 |

## 创建消息接口

创建的消息会返回一个id字段，将id字段等传递给统一的发送接口（sendMessage）即可发送消息。
| API | 描述 |
| --- | --- |
| [createTextMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTextMessage.html) | 创建文本消息 |
| [createCustomMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createCustomMessage.html) | 创建定制化消息 |
| [createImageMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createImageMessage.html) | 创建图片消息 |
| [createSoundMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createSoundMessage.html) | 创建音频文件 |
| [createVideoMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createVideoMessage.html) | 创建视频文件 |
| [createTextAtMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTextAtMessage.html) | 创建AT消息 |
| [createFileMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFileMessage.html) | 创建文件消息 |
| [createLocationMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createLocationMessage.html) | 创建位置信息 |
| [createFaceMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFaceMessage.html) | 创建表情消息 |
| [createMergerMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createMergerMessage.html) | 创建合并消息 |
| [createForwardMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createForwardMessage.html) | 创建转发消息 |
| [createTargetedGroupMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTargetedGroupMessage.html) | 创建一条定向群消息 |
| [appendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/appendMessage.html) | 添加多Element消息 |

## 消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（原3.6.0前的高级消息部分接口已弃用，请使用新版创建消息接口后调用发送消息接口）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/addAdvancedMsgListener.html) | 设置高级消息的事件监听器 |
| [removeAdvancedMsgListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/removeAdvancedMsgListener.html) | 移除高级消息的事件监听器 |
| [getC2CHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getC2CHistoryMessageList.html) | 获取单聊（C2C）历史消息 |
| [getHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getHistoryMessageList.html) | 获取历史消息高级接口 |
| [getGroupHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getGroupHistoryMessageList.html) | 获取群组历史消息 |
| [markC2CMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markC2CMessageAsRead.html) | 设置单聊（C2C）消息已读 |
| [markGroupMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markGroupMessageAsRead.html) | 设置群组消息已读 |
| [markAllMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markAllMessageAsRead.html) | 标记所有消息为已读 |
| [deleteMessageFromLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/deleteMessageFromLocalStorage.html) | 删除本地消息 |
| [deleteMessages](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/deleteMessages.html) | 删除本地及漫游消息 |
| [insertGroupMessageToLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/insertGroupMessageToLocalStorage.html) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/insertC2CMessageToLocalStorage.html) | 向C2C消息列表中添加一条消息 |
| [clearC2CHistoryMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/clearC2CHistoryMessage.html) | 清空单聊本地及云端的消息（不删除会话） |
| [clearGroupHistoryMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/clearGroupHistoryMessage.html) | 清空群组及云端的消息（不删除会话） |
| [downloadMergerMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/downloadMergerMessage.html) | 获取合并消息的子消息 |
| [setC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setC2CReceiveMessageOpt.html) | 设置针对某个用户的 C2C 消息接收选项（支持批量设置） |
| [getC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getC2CReceiveMessageOpt.html) | 查询针对某个用户的 C2C 消息接收选项 |
| [setGroupReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setGroupReceiveMessageOpt.html) | 修改群消息接收选项 |
| [setLocalCustomData](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setLocalCustomData.html) | 设置消息自定义数据（本地保存，不会发送到对端，程序卸载重装后失效） |
| [setLocalCustomInt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setLocalCustomInt.html) | 设置消息自定义数据，可以用来标记语音、视频消息是否已经播放（本地保存，不会发送到对端，程序卸载重装后失效） |
| [revokeMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/revokeMessage.html) | 撤回消息的时间限制默认 2 minutes，超过 2 minutes 的消息不能撤回，您也可以在 控制台（功能配置 -> 登录与消息 -> 消息撤回设置）自定义撤回时间限制。 |
| [modifyMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/modifyMessage.html) | 消息变更 |
| [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) | 发送消息 |
| [searchLocalMessages](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/searchLocalMessages.html) | 搜索本地消息 |
| [sendMessageReadReceipts](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessageReadReceipts.html) | 发送群消息已读回执 |
| [getMessageReadReceipts](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getMessageReadReceipts.html) | 获取自己发送消息的已读回执 |
| [getGroupMessageReadMemberList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getGroupMessageReadMemberList.html) | 获取自己发送的群消息已读（未读）群成员列表 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，亦被称作消息列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [addConversationListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/addConversationListener.html) | 添加关系链监听器 |
| [removeConversationListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/removeConversationListener.html) | 移除关系链监听器 |
| [getConversationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversationList.html) | 获取会话列表 |
| [getConversationListByConversationIds](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversationListByConversationIds.html) | 通过会话ID获取指定会话列表 |
| [pinConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/pinConversation.html) | 会话置顶 |
| [getTotalUnreadMessageCount](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getTotalUnreadMessageCount.html) | 获取会话未读总数 |
| [getConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversation.html) | 获取指定会话 |
| [deleteConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/deleteConversation.html) | 删除会话 |
| [setConversationDraft](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/setConversationDraft.html) | 设置会话草稿 |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work） ：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public） ：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [addGroupListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/addGroupListener.html) | 添加群组监听器 |
| [removeGroupListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/removeGroupListener.html) | 移除群组监听器 |
| [createGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/createGroup.html) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/joinGroup.html) | 加入群组 |
| [quitGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/quitGroup.html) | 退出群组 |
| [dismissGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/dismissGroup.html) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getJoinedGroupList.html) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupsInfo.html) | 拉取群资料 |
| [setGroupInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupInfo.html) | 修改群资料 |
| [initGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/initGroupAttributes.html) | 初始化群属性，会清空原有的群属性列表 |
| [setGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupAttributes.html) | 设置群属性。已有该群属性则更新其 value 值，没有该群属性则添加该属性。 |
| [deleteGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/deleteGroupAttributes.html) | 删除指定群属性，keys 传 null 则清空所有群属性。 |
| [getGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupAttributes.html) | 获取指定群属性，keys 传 null 则获取所有群属性。 |
| [searchGroups](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/searchGroups.html) | 搜索群列表 |
| [getGroupOnlineMemberCount](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupOnlineMemberCount.html) | 获取指定群在线人数(目前只支持直播群) |
| [getGroupMemberList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupMemberList.html) | 获取群成员列表 |
| [getGroupMembersInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupMembersInfo.html) | 获取指定的群成员资料 |
| [setGroupMemberInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupMemberInfo.html) | 修改指定的群成员资料 |
| [searchGroupMembers](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/searchGroupMembers.html) | 搜索群成员 |
| [muteGroupMember](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/muteGroupMember.html) | 禁言 |
| [kickGroupMember](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/kickGroupMember.html) | 踢人 |
| [setGroupMemberRole](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupMemberRole.html) | 切换群成员的角色 |
| [transferGroupOwner](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/transferGroupOwner.html) | 转让群主 |
| [inviteUserToGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/inviteUserToGroup.html) | 邀请他人入群 |
| [getGroupApplicationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupApplicationList.html) | 获取加群的申请列表 |
| [acceptGroupApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/acceptGroupApplication.html) | 同意某一条加群申请 |
| [refuseGroupApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/refuseGroupApplication.html) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupApplicationRead.html) | 标记申请列表为已读 |
| [getJoinedCommunityList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getJoinedCommunityList.html) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/createTopicInCommunity.html) | 创建话题 |
| [deleteTopicFromCommunity](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/deleteTopicFromCommunity.html) | 删除话题 |
| [setTopicInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setTopicInfo.html) | 设置话题属性 |
| [getTopicInfoList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getTopicInfoList.html) | 获取话题属性的列表 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getUsersInfo.html) | 获取用户资料 |
| [getUserStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getUserStatus.html) | 获取用户在线状态 |
| [setSelfInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setSelfInfo.html) | 修改个人资料 |
| [setSelfStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setSelfStatus.html) | 设置当前登录用户在线状态 |
| [addToBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addToBlackList.html) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFromBlackList.html) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getBlackList.html) | 获取黑名单列表 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) > **功能配置 **> **登录与消息 **> **好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [addFriendListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriendListener.html) | 添加关系链监听器 |
| [removeFriendListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/removeFriendListener.html) | 移除关系链监听器 |
| [getFriendList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendList.html) | 获取好友列表 |
| [getFriendsInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendsInfo.html) | 获取指定好友资料 |
| [setFriendInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/setFriendInfo.html) | 设置指定好友资料 |
| [addFriend](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriend.html) | 添加好友 |
| [deleteFromFriendList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFromFriendList.html) | 删除好友 |
| [checkFriend](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/checkFriend.html) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendApplicationList.html) | 获取好友申请列表 |
| [acceptFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/acceptFriendApplication.html) | 同意好友申请 |
| [refuseFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/refuseFriendApplication.html) | 拒绝好友申请 |
| [deleteFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendApplication.html) | 删除好友申请 |
| [setFriendApplicationRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/setFriendApplicationRead.html) | 设置好友申请已读 |
| [createFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/createFriendGroup.html) | 新建好友分组 |
| [getFriendGroups](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendGroups.html) | 获取分组信息 |
| [deleteFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendGroup.html) | 删除好友分组 |
| [renameFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/renameFriendGroup.html) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriendsToFriendGroup.html) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendsFromFriendGroup.html) | 从好友分组中删除好友 |
| [searchFriends](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/searchFriends.html) | 搜索好友 |

## 离线推送相关接口

如果您希望应用切到后台时依然能够稳定接收 IM 消息，建议您使用 [推送服务（Push）](https://cloud.tencent.com/document/product/269/100621)，其全面覆盖主流厂商通道、最快3分钟即可实现一键式集成，支持丰富多样的推送方式和消息全链路统计分析能力，保障精准送达、服务安全稳定，助您轻松提升用户留存和互动活跃度。若您希望自行实现离线推送能力，也可参考 [自集成推送](https://cloud.tencent.com/document/product/269/75428) 指引逐一适配每个设备厂商的推送服务，将用到以下接口。
| API | 描述 |
| --- | --- |
| [setAPNSListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setAPNSListener.html) | 设置苹果系统离线推送专用监听器 |
| [doBackground](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/doBackground.html) | 设置离线推送配置信息 |
| [doForeground](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/doForeground.html) | 设置离线推送配置信息 |
| [setOfflinePushConfig](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/setOfflinePushConfig.html) | 设置离线推送配置信息 |

---

## Platform: Flutter SDK

---
product: 'Chat'
tech_stack: 'flutter_sdk'
framework: 'sdk'
title: 'flutter sdk 常见问题'
keywords: ['sdk 集成', '登录 API', '创建群组','发送消息','接收消息']
---

# Flutter SDK 常见问题

## Flutter SDK 常见问题解决方案 (Topic: SDK FAQ)

### Q: SDK 怎么实现消息回应？
**A：** SDK 支持消息回应能力，您需要购买旗舰版或企业版套餐，具体 API 有3个：
- 添加消息回应 [addMessageReaction](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/addMessageReaction.html) 
- 获取消息回应 [getMessageReactions](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getMessageReactions.html)
- 删除消息回应 [removeMessageReaction](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/removeMessageReaction.html)

### Q: 单聊消息怎么标记已读？
**A：** 如果您需要标记单聊消息已读同时清空会话未读数，可以使用 [markC2CMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markC2CMessageAsRead.html)。

### Q: 群聊消息怎么清空未读数？
**A：** 如果您需要清空群聊会话未读数，可以使用 [markGroupMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markGroupMessageAsRead.html)。

### Q: 单聊和群聊消息怎么实现单条消息已读？
**A：** 如果您需要实现单条消息已读功能，需要使用消息已读回执能力。 如何开启已读回执、如何发送已读回执等详细信息可以参考：https://cloud.tencent.com/document/product/269/75345。

### Q: SDK 怎么发送图片消息？
**A：** 可以使用 [createImageMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createImageMessage.html) 创建图片消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送图片消息。

### Q: SDK 怎么发送视频消息？
**A：** 可以使用 [createVideoMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createVideoMessage.html) 创建视频消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送视频消息。

### Q: SDK 怎么发送语音消息？
**A：** 可以使用 [createSoundMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createSoundMessage.html) 创建语音消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送语音消息。

### Q: SDK 怎么发送文件消息？
**A：** 可以使用 [createFileMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFileMessage.html) 创建文件消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送文件消息。

### Q: SDK 如何删除会话？
**A：** 删除会话仅支持单向删除，SDK 删除会话 API `deleteConversation`，参考文档：https://cloud.tencent.com/document/product/269/75380

### Q: SDK 如何删除会话历史消息？
**A：** SDK 清空消息分为两种：清空单聊消息、清空群聊消息。清空消息会同时清空当前会话内所有的消息，包含本地和云端消息，但不会删除会话本身，且清空的是单向的，不会把对方会话中的消息也情况。
- 清空单聊消息 [clearC2CHistoryMessage](https://cloud.tencent.com/document/product/269/75336)
- 清空群聊消息 [clearGroupHistoryMessage](https://cloud.tencent.com/document/product/269/75336)

### Q: SDK 怎么获取用户在线状态功能？
**A：** 
1. SDK 提供了**用户状态查询、订阅、取消订阅 API 和状态变更事件通知**，详情可参考：https://cloud.tencent.com/document/product/269/105469
2. 服务端**在线状态变更回调**可参考：https://cloud.tencent.com/document/product/269/2570

### Q: SDK 怎么获取好友列表？
**A：** SDK 支持好友的管理，用户可以主动添加、删除好友，也可以设置仅针对好友才能发送消息。详情可参考：https://cloud.tencent.com/document/product/269/75421

---

## Platform: Flutter SDK

## 初始化登录接口

初始化并成功登录，是正常使用腾讯云 IM 服务的前提。
| API | 描述 |
| --- | --- |
| [initSDK](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/initSDK.html) | 初始化 SDK |
| [unInitSDK](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/unInitSDK.html) | 反初始化 SDK |
| [login](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/login.html) | 登录 |
| [logout](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/logout.html) | 登出 |
| [getLoginUser](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getLoginUser.html) | 获取当前登录用户的 UserID |
| [getLoginStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getLoginStatus.html) | 获取登录状态 |
| [getServerTime](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getServerTime.html) | 获取服务器当前时间（Web不支持） |
| [getVersion](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getVersion.html) | 获取版本号 |
| [getConversationManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getConversationManager.html) | 会话功能模块 |
| [getFriendshipManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getFriendshipManager.html) | 关系链功能模块 |
| [getGroupManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getGroupManager.html) | 高级群组功能模块 |
| [getMessageManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getMessageManager.html) | 高级消息功能模块 |
| [getOfflinePushManager](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getOfflinePushManager.html) | 离线推送模块 |

## 创建消息接口

创建的消息会返回一个id字段，将id字段等传递给统一的发送接口（sendMessage）即可发送消息。
| API | 描述 |
| --- | --- |
| [createTextMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTextMessage.html) | 创建文本消息 |
| [createCustomMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createCustomMessage.html) | 创建定制化消息 |
| [createImageMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createImageMessage.html) | 创建图片消息 |
| [createSoundMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createSoundMessage.html) | 创建音频文件 |
| [createVideoMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createVideoMessage.html) | 创建视频文件 |
| [createTextAtMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTextAtMessage.html) | 创建AT消息 |
| [createFileMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFileMessage.html) | 创建文件消息 |
| [createLocationMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createLocationMessage.html) | 创建位置信息 |
| [createFaceMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFaceMessage.html) | 创建表情消息 |
| [createMergerMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createMergerMessage.html) | 创建合并消息 |
| [createForwardMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createForwardMessage.html) | 创建转发消息 |
| [createTargetedGroupMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createTargetedGroupMessage.html) | 创建一条定向群消息 |
| [appendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/appendMessage.html) | 添加多Element消息 |

## 消息收发接口

如果您需要收发图片、视频、文件等富媒体消息，并需要撤回消息、标记已读、查询历史消息等高级功能，推荐使用下面这套高级消息接口（原3.6.0前的高级消息部分接口已弃用，请使用新版创建消息接口后调用发送消息接口）。
| API | 描述 |
| --- | --- |
| [addAdvancedMsgListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/addAdvancedMsgListener.html) | 设置高级消息的事件监听器 |
| [removeAdvancedMsgListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/removeAdvancedMsgListener.html) | 移除高级消息的事件监听器 |
| [getC2CHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getC2CHistoryMessageList.html) | 获取单聊（C2C）历史消息 |
| [getHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getHistoryMessageList.html) | 获取历史消息高级接口 |
| [getGroupHistoryMessageList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getGroupHistoryMessageList.html) | 获取群组历史消息 |
| [markC2CMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markC2CMessageAsRead.html) | 设置单聊（C2C）消息已读 |
| [markGroupMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markGroupMessageAsRead.html) | 设置群组消息已读 |
| [markAllMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markAllMessageAsRead.html) | 标记所有消息为已读 |
| [deleteMessageFromLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/deleteMessageFromLocalStorage.html) | 删除本地消息 |
| [deleteMessages](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/deleteMessages.html) | 删除本地及漫游消息 |
| [insertGroupMessageToLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/insertGroupMessageToLocalStorage.html) | 向群组消息列表中添加一条消息 |
| [insertC2CMessageToLocalStorage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/insertC2CMessageToLocalStorage.html) | 向C2C消息列表中添加一条消息 |
| [clearC2CHistoryMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/clearC2CHistoryMessage.html) | 清空单聊本地及云端的消息（不删除会话） |
| [clearGroupHistoryMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/clearGroupHistoryMessage.html) | 清空群组及云端的消息（不删除会话） |
| [downloadMergerMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/downloadMergerMessage.html) | 获取合并消息的子消息 |
| [setC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setC2CReceiveMessageOpt.html) | 设置针对某个用户的 C2C 消息接收选项（支持批量设置） |
| [getC2CReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getC2CReceiveMessageOpt.html) | 查询针对某个用户的 C2C 消息接收选项 |
| [setGroupReceiveMessageOpt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setGroupReceiveMessageOpt.html) | 修改群消息接收选项 |
| [setLocalCustomData](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setLocalCustomData.html) | 设置消息自定义数据（本地保存，不会发送到对端，程序卸载重装后失效） |
| [setLocalCustomInt](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/setLocalCustomInt.html) | 设置消息自定义数据，可以用来标记语音、视频消息是否已经播放（本地保存，不会发送到对端，程序卸载重装后失效） |
| [revokeMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/revokeMessage.html) | 撤回消息的时间限制默认 2 minutes，超过 2 minutes 的消息不能撤回，您也可以在 控制台（功能配置 -> 登录与消息 -> 消息撤回设置）自定义撤回时间限制。 |
| [modifyMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/modifyMessage.html) | 消息变更 |
| [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) | 发送消息 |
| [searchLocalMessages](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/searchLocalMessages.html) | 搜索本地消息 |
| [sendMessageReadReceipts](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessageReadReceipts.html) | 发送群消息已读回执 |
| [getMessageReadReceipts](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getMessageReadReceipts.html) | 获取自己发送消息的已读回执 |
| [getGroupMessageReadMemberList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getGroupMessageReadMemberList.html) | 获取自己发送的群消息已读（未读）群成员列表 |

## 会话列表相关接口

会话列表，即登录微信或 QQ 后首屏看到的列表，亦被称作消息列表，包含会话节点、会话名称、群名称、最后一条消息以及未读消息数等元素。
| API | 描述 |
| --- | --- |
| [addConversationListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/addConversationListener.html) | 添加关系链监听器 |
| [removeConversationListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/removeConversationListener.html) | 移除关系链监听器 |
| [getConversationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversationList.html) | 获取会话列表 |
| [getConversationListByConversationIds](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversationListByConversationIds.html) | 通过会话ID获取指定会话列表 |
| [pinConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/pinConversation.html) | 会话置顶 |
| [getTotalUnreadMessageCount](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getTotalUnreadMessageCount.html) | 获取会话未读总数 |
| [getConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/getConversation.html) | 获取指定会话 |
| [deleteConversation](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/deleteConversation.html) | 删除会话 |
| [setConversationDraft](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_conversation_manager/V2TIMConversationManager/setConversationDraft.html) | 设置会话草稿 |

## 群组相关接口

腾讯云 IM SDK 支持五种预设的群组类型，每种类型都有其适用场景：
- 工作群（Work） ：类似普通微信群，创建后不能自由加入，必须由已经在群的用户邀请入群，同旧版本中的 Private。

- 公开群（Public） ：类似 QQ 群，用户申请加入，但需要群主或管理员审批。

- 会议群（Meeting）：适合跟 [TRTC](https://cloud.tencent.com/product/trtc) 结合实现视频会议和在线教育等场景，支持随意进出，支持查看进群前的历史消息，同旧版本中的 ChatRoom。

- 社群（Community）：创建后可以随意进出，适合用于知识分享和游戏交流等超大社区群聊场景。

- 直播群（AVChatRoom）：适合直播弹幕聊天室等场景，支持随意进出，人数无上限。

| API | 描述 |
| --- | --- |
| [addGroupListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/addGroupListener.html) | 添加群组监听器 |
| [removeGroupListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/removeGroupListener.html) | 移除群组监听器 |
| [createGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/createGroup.html) | 创建群组（高级版本），可在建群同时设置群信息和初始的群成员 |
| [joinGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/joinGroup.html) | 加入群组 |
| [quitGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/quitGroup.html) | 退出群组 |
| [dismissGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/dismissGroup.html) | 解散群组（仅群主和管理员可以解散） |
| [getJoinedGroupList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getJoinedGroupList.html) | 获取已经加入的群列表（不包括已加入的直播群） |
| [getGroupsInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupsInfo.html) | 拉取群资料 |
| [setGroupInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupInfo.html) | 修改群资料 |
| [initGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/initGroupAttributes.html) | 初始化群属性，会清空原有的群属性列表 |
| [setGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupAttributes.html) | 设置群属性。已有该群属性则更新其 value 值，没有该群属性则添加该属性。 |
| [deleteGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/deleteGroupAttributes.html) | 删除指定群属性，keys 传 null 则清空所有群属性。 |
| [getGroupAttributes](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupAttributes.html) | 获取指定群属性，keys 传 null 则获取所有群属性。 |
| [searchGroups](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/searchGroups.html) | 搜索群列表 |
| [getGroupOnlineMemberCount](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupOnlineMemberCount.html) | 获取指定群在线人数(目前只支持直播群) |
| [getGroupMemberList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupMemberList.html) | 获取群成员列表 |
| [getGroupMembersInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupMembersInfo.html) | 获取指定的群成员资料 |
| [setGroupMemberInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupMemberInfo.html) | 修改指定的群成员资料 |
| [searchGroupMembers](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/searchGroupMembers.html) | 搜索群成员 |
| [muteGroupMember](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/muteGroupMember.html) | 禁言 |
| [kickGroupMember](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/kickGroupMember.html) | 踢人 |
| [setGroupMemberRole](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupMemberRole.html) | 切换群成员的角色 |
| [transferGroupOwner](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/transferGroupOwner.html) | 转让群主 |
| [inviteUserToGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/inviteUserToGroup.html) | 邀请他人入群 |
| [getGroupApplicationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getGroupApplicationList.html) | 获取加群的申请列表 |
| [acceptGroupApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/acceptGroupApplication.html) | 同意某一条加群申请 |
| [refuseGroupApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/refuseGroupApplication.html) | 拒绝某一条加群申请 |
| [setGroupApplicationRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setGroupApplicationRead.html) | 标记申请列表为已读 |
| [getJoinedCommunityList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getJoinedCommunityList.html) | 获取当前用户已经加入的支持话题的社群列表 |
| [createTopicInCommunity](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/createTopicInCommunity.html) | 创建话题 |
| [deleteTopicFromCommunity](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/deleteTopicFromCommunity.html) | 删除话题 |
| [setTopicInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/setTopicInfo.html) | 设置话题属性 |
| [getTopicInfoList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_group_manager/V2TIMGroupManager/getTopicInfoList.html) | 获取话题属性的列表 |

## 用户资料相关接口

包含查询用户资料、修改个人资料以及屏蔽某人消息（即把某用户加入黑名单中）的相关接口。
| API | 描述 |
| --- | --- |
| [getUsersInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getUsersInfo.html) | 获取用户资料 |
| [getUserStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/getUserStatus.html) | 获取用户在线状态 |
| [setSelfInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setSelfInfo.html) | 修改个人资料 |
| [setSelfStatus](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setSelfStatus.html) | 设置当前登录用户在线状态 |
| [addToBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addToBlackList.html) | 屏蔽某人的消息（添加该用户到黑名单中） |
| [deleteFromBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFromBlackList.html) | 取消某人的消息屏蔽（把该用户从黑名单中移除） |
| [getBlackList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getBlackList.html) | 获取黑名单列表 |

## 好友管理相关接口

腾讯云 IM 在收发消息时默认不检查是不是好友关系，您可以在 [**控制台**](https://console.cloud.tencent.com/im) > **功能配置 **> **登录与消息 **> **好友关系检查**中开启"发送单聊消息检查关系链"开关，并使用如下接口增删好友和管理好友列表。
| API | 描述 |
| --- | --- |
| [addFriendListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriendListener.html) | 添加关系链监听器 |
| [removeFriendListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/removeFriendListener.html) | 移除关系链监听器 |
| [getFriendList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendList.html) | 获取好友列表 |
| [getFriendsInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendsInfo.html) | 获取指定好友资料 |
| [setFriendInfo](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/setFriendInfo.html) | 设置指定好友资料 |
| [addFriend](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriend.html) | 添加好友 |
| [deleteFromFriendList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFromFriendList.html) | 删除好友 |
| [checkFriend](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/checkFriend.html) | 检查指定用户的好友关系 |
| [getFriendApplicationList](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendApplicationList.html) | 获取好友申请列表 |
| [acceptFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/acceptFriendApplication.html) | 同意好友申请 |
| [refuseFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/refuseFriendApplication.html) | 拒绝好友申请 |
| [deleteFriendApplication](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendApplication.html) | 删除好友申请 |
| [setFriendApplicationRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/setFriendApplicationRead.html) | 设置好友申请已读 |
| [createFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/createFriendGroup.html) | 新建好友分组 |
| [getFriendGroups](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/getFriendGroups.html) | 获取分组信息 |
| [deleteFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendGroup.html) | 删除好友分组 |
| [renameFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/renameFriendGroup.html) | 修改好友分组的名称 |
| [addFriendsToFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/addFriendsToFriendGroup.html) | 添加好友到一个好友分组 |
| [deleteFriendsFromFriendGroup](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/deleteFriendsFromFriendGroup.html) | 从好友分组中删除好友 |
| [searchFriends](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_friendship_manager/V2TIMFriendshipManager/searchFriends.html) | 搜索好友 |

## 离线推送相关接口

如果您希望应用切到后台时依然能够稳定接收 IM 消息，建议您使用 [推送服务（Push）](https://cloud.tencent.com/document/product/269/100621)，其全面覆盖主流厂商通道、最快3分钟即可实现一键式集成，支持丰富多样的推送方式和消息全链路统计分析能力，保障精准送达、服务安全稳定，助您轻松提升用户留存和互动活跃度。若您希望自行实现离线推送能力，也可参考 [自集成推送](https://cloud.tencent.com/document/product/269/75428) 指引逐一适配每个设备厂商的推送服务，将用到以下接口。
| API | 描述 |
| --- | --- |
| [setAPNSListener](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_manager/V2TIMManager/setAPNSListener.html) | 设置苹果系统离线推送专用监听器 |
| [doBackground](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/doBackground.html) | 设置离线推送配置信息 |
| [doForeground](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/doForeground.html) | 设置离线推送配置信息 |
| [setOfflinePushConfig](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_offline_push_manager/V2TIMOfflinePushManager/setOfflinePushConfig.html) | 设置离线推送配置信息 |

---

## Platform: Flutter SDK

---
product: 'Chat'
tech_stack: 'flutter_sdk'
framework: 'sdk'
title: 'flutter sdk 常见问题'
keywords: ['sdk 集成', '登录 API', '创建群组','发送消息','接收消息']
---

# Flutter SDK 常见问题

## Flutter SDK 常见问题解决方案 (Topic: SDK FAQ)

### Q: SDK 怎么实现消息回应？
**A：** SDK 支持消息回应能力，您需要购买旗舰版或企业版套餐，具体 API 有3个：
- 添加消息回应 [addMessageReaction](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/addMessageReaction.html) 
- 获取消息回应 [getMessageReactions](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/getMessageReactions.html)
- 删除消息回应 [removeMessageReaction](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/removeMessageReaction.html)

### Q: 单聊消息怎么标记已读？
**A：** 如果您需要标记单聊消息已读同时清空会话未读数，可以使用 [markC2CMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markC2CMessageAsRead.html)。

### Q: 群聊消息怎么清空未读数？
**A：** 如果您需要清空群聊会话未读数，可以使用 [markGroupMessageAsRead](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/markGroupMessageAsRead.html)。

### Q: 单聊和群聊消息怎么实现单条消息已读？
**A：** 如果您需要实现单条消息已读功能，需要使用消息已读回执能力。 如何开启已读回执、如何发送已读回执等详细信息可以参考：https://cloud.tencent.com/document/product/269/75345。

### Q: SDK 怎么发送图片消息？
**A：** 可以使用 [createImageMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createImageMessage.html) 创建图片消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送图片消息。

### Q: SDK 怎么发送视频消息？
**A：** 可以使用 [createVideoMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createVideoMessage.html) 创建视频消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送视频消息。

### Q: SDK 怎么发送语音消息？
**A：** 可以使用 [createSoundMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createSoundMessage.html) 创建语音消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送语音消息。

### Q: SDK 怎么发送文件消息？
**A：** 可以使用 [createFileMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/createFileMessage.html) 创建文件消息实例，然后使用 [sendMessage](https://pub.dev/documentation/tencent_cloud_chat_sdk/latest/manager_v2_tim_message_manager/V2TIMMessageManager/sendMessage.html) 发送文件消息。

### Q: SDK 如何删除会话？
**A：** 删除会话仅支持单向删除，SDK 删除会话 API `deleteConversation`，参考文档：https://cloud.tencent.com/document/product/269/75380

### Q: SDK 如何删除会话历史消息？
**A：** SDK 清空消息分为两种：清空单聊消息、清空群聊消息。清空消息会同时清空当前会话内所有的消息，包含本地和云端消息，但不会删除会话本身，且清空的是单向的，不会把对方会话中的消息也情况。
- 清空单聊消息 [clearC2CHistoryMessage](https://cloud.tencent.com/document/product/269/75336)
- 清空群聊消息 [clearGroupHistoryMessage](https://cloud.tencent.com/document/product/269/75336)

### Q: SDK 怎么获取用户在线状态功能？
**A：** 
1. SDK 提供了**用户状态查询、订阅、取消订阅 API 和状态变更事件通知**，详情可参考：https://cloud.tencent.com/document/product/269/105469
2. 服务端**在线状态变更回调**可参考：https://cloud.tencent.com/document/product/269/2570

### Q: SDK 怎么获取好友列表？
**A：** SDK 支持好友的管理，用户可以主动添加、删除好友，也可以设置仅针对好友才能发送消息。详情可参考：https://cloud.tencent.com/document/product/269/75421
