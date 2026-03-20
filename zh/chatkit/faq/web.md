> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## Platform: React

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'react'
title: 'react uikit 集成和常见问题解决方案'
keywords: ['react', '初始化', '组件', '集成', 'TUIKit']
---

# React TUIKit 集成与常见问题解决方案

## 模块一：环境准备与快速初始化 (Topic: Setup)

### Q: 如何在 React 项目中安装 TUIKit？

**A:** TUIKit 是基于 Web SDK 开发的，您需要安装 `@tencentcloud/chat-uikit-react`

```bash
# 安装 TUIKit React 版本
npm install @tencentcloud/chat-uikit-react@latest
```

## 模块二：常见问题解决方案 (Topic: Question)

### Q: 是否支持 react 17/19 版本？

**A:** 目前不支持 17.x/19.x 版本，仅支持 React v18。

---

## Platform: Vue

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'vue'
title: 'vue uikit 集成和常见问题解决方案'
keywords: ['vue', '初始化', '组件', '集成', 'TUIKit']
---

# Vue TUIKit 集成与常见问题解决方案

## 模块一：环境准备与快速初始化 (Topic: Setup)

### Q: 如何在 Vue 项目中安装 TUIKit？

**A:** TUIKit 是基于 Web SDK 开发的，您需要安装 `@tencentcloud/chat-uikit-vue3`。

```bash
# 安装 TUIKit Vue 版本
npm install @tencentcloud/chat-uikit-vue3@latest
```

## 模块二：常见问题解决方案 (Topic: Question)

### Q: 我能不能在 TUIKit 中使用第三方组件库，例如 Element-Plus？

**A:** 核心组件之间的粘连代码可以使用其他组件库，这一点在示例代码中也可以看到，例如您可以将 <ChatSetting /> 封装成全屏抽屉组件。但是核心组件内部已经存在的组件暂时不支持修改。
```typescript
const isChatSettingShow = ref(false);
<el-drawer
  v-model="isChatSettingShow"
  title="设置"
  <ChatSetting />
</el-drawer>
```

---

## Platform: Web SDK

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'sdk'
title: 'web js sdk 错误码大全'
keywords: ['错误码', '报错', '登录失败']
---

# Web JS SDK 错误码大全

## Web JS SDK 错误码 (Topic: SDK Error Code)

## 错误码 2024
**原因：** 用户未登录。
**解决方案：** 检查是否正确调用了登录接口。

## 错误码 2025
**原因：** 重复登录，15s 内连续触发了多次 login 请求，SDK 会进行拦截。
**解决方案：** 检查调用登录接口的时机和触发机制。

## 错误码 2301
**原因：** 语音大小超出了限制。
**解决方案：** 发送语音,最大限制是 20MB。

## 错误码 2252
**原因：** 图片消息发送失败,无效的图片格式。
**解决方案：** 图片格式支持：'jpg', 'jpeg', 'gif', 'png', 'bmp', 'image', 'webp'。

## 错误码 2253
**原因：** 图片大小超出了限制。
**解决方案：** 发送图片消息,最大限制是 20MB。

## 错误码 2351
**原因：** 视频大小超出了限制。
**解决方案：** 发送视频消息，最大限制是 100MB。

## 错误码 2352
**原因：** 视频消息发送失败,无效的视频格式。
**解决方案：** 图片格式支持：'mp4', 'quicktime', 'mov'。

## 错误码 2401
**原因：** 文件不存在。
**解决方案：** 请检查文件路径是否正确。

## 错误码 2402
**原因：** 文件大小超出了限制。
**解决方案：** 发送文件消息，最大限制是 100MB。

---

## Platform: Web SDK

---
product: 'Chat'
tech_stack: 'web_js_sdk'
framework: 'sdk'
title: 'web js sdk 常见问题'
keywords: ['sdk 集成', '登录 API', '创建群组']
---

# Web js SDK 常见问题

## 重要提醒
**SDK 版本说明：**  
 - v2：`tim-js-sdk` 和 `tim-wx-sdk`
 - v3: `@tencentcloud/chat`
 - v4: `@tencentcloud/lite-chat`
 如果需要输出示例代码，必须使用最新版本 `@tencentcloud/lite-chat` 进行展示

## Web JS SDK 常见问题解决方案 (Topic: SDK FAQ)

### Q: chat SDK v3 怎么升级 v4 版本？
**A：** v4 [集成指引](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/tutorial-03-integration-v4.html)，具体操作如下：
安装 v4 SDK
```bash
npm install @tencentcloud/lite-chat --save
```
更新代码中引用 v3 SDK 的逻辑，把 `import TencentCloudChat from '@tencentcloud/chat';` 更新为 `import TencentCloudChat from '@tencentcloud/lite-chat';`。

### Q: chat restapi 发送流式消息(https://cloud.tencent.com/document/product/269/104537)， 怎么通过 SDK 接收流式消息？
**A：** SDK 通过消息接收事件[TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.MESSAGE_RECEIVED)和消息修改事件[TencentCloudChat.EVENT.MESSAGE_MODIFIED](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.MESSAGE_MODIFIED)来处理，restapi 发送第一个片段时 SDK 通过消息接收事件通知接入侧，后续通过消息修改事件通知接入侧。

### Q: SDK 登录失败是什么问题？
**A：** 请提供报错信息或错误码。

### Q: 为什么调用 off 接口取消监听事件后，仍然能监听到 SDK 派发的事件？
**A：** 对于同一个事件，如 [TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.MESSAGE_RECEIVED)，调用 [on](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#on) 接口监听事件和调用 [off](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#off) 接口取消监听事件，handler 参数应当指向同一个 function 。按照推荐的写法绑定事件写法如下：

``` typescript
// 推荐写法
tim.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived, this);
tim.off(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived);

// 注意！以下代码有 bug，无法取消监听 TencentCloudChat.EVENT.MESSAGE_RECEIVED，因为 bind() 方法每次会返回一个新的函数
tim.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, this.onMessageReceived.bind(this));
tim.off(TencentCloudChat.EVENT.MESSAGE_RECEIVED, this.onMessageReceived.bind(this));
```

### Q: 调用 logout 接口，SDK 会自动取消监听接入侧通过调用 on 接口已监听的事件吗？
**A：** 不会。取消监听事件，需接入侧主动调用 [off](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#off) 接口。

### Q: 登录成功后不能发送消息，提示接口调用时机不合理？
**A：** SDK 的 API 需要在监听到 SDK ready 后才能调用，监听事件 [TencentCloudChat.EVENT.SDK_READY](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.SDK_READY)，待 SDK ready 后再调用发送消息等需要鉴权的接口。

### Q: 我想在音视频聊天室实现点赞，送鲜花的功能，请问该怎么办？
**A：** 通过 [createCustomMessage](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#createCustomMessage) 和 [sendMessage](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#sendMessage) 接口实现。

### Q: Web IM SDK v2.x/v3.x/v4.x 如何兼容 REST API 或 旧版 IM 发送的组合消息？
**A：** 组合消息的内容存储在 Message 实例的 _elements 属性，接入侧需自主解析，解析请参见 [消息格式](https://cloud.tencent.com/document/product/269/2720)。
非组合消息，请使用推荐做法：使用 [Message](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html) 实例的 type 和 payload 属性。

### Q: 为什么 Web IM SDK 调用 sendMessage 发消息成功后，未收到 TencentCloudChat.EVENT.MESSAGE_RECEIVED 事件？
**A：** 自己发送的消息不会通过 `TencentCloudChat.EVENT.MESSAGE_RECEIVED` 通知给业务侧。[sendMessage](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#sendMessage) 接口返回 `Promise`，接入侧通过 `Promise.then` 或 `Promise.catch` 处理发送消息成功或失败后的业务逻辑。

### Q: 通过 getMessageList 拉取到的消息列表和通过监听 TencentCloudChat.EVENT.MESSAGE_RECEIVED 事件收到的消息合并后，发现有时会消息乱序，为什么？
**A：** 接入侧维护会话的消息列表时，请注意保证消息入列的顺序正确，若消息列表从旧到新排列，则通过 [getMessageList](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#getMessageList) 拉取到的历史消息应该从头部入列，通过监听 [TencentCloudChat.EVENT.MESSAGE_RECEIVED](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.MESSAGE_RECEIVED) 事件收到的实时消息应该从尾部入列。

### Q: 调用 createGroup 接口创建直播群后，收不到消息？
**A：** 直播群需要业务侧主动加群，启动长轮询消息通道。调用 [createGroup](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#createGroup) 接口创建直播群后，需调用 [joinGroup](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#joinGroup) 接口加入群组，才能进行消息收发流程。
``` typescript
let promise = tim.joinGroup({ groupID: 'group1' });
promise.then(function(imResponse) {
  switch (imResponse.data.status) {
    case TencentCloudChat.TYPES.JOIN_STATUS_WAIT_APPROVAL:
      break;
    case TencentCloudChat.TYPES.JOIN_STATUS_SUCCESS:
      console.log(imResponse.data.group); // 加入的群组资料
      break;
    case TencentCloudChat.TYPES.JOIN_STATUS_ALREADY_IN_GROUP:
      break;
    default:
      break;
    }
  }).catch(function(imError){
    console.warn('joinGroup error:', imError);
});
```

### Q: 直播群怎么没有未读消息计数？为什么拉取不到历史消息？
**A：** 已定的产品策略：直播群不支持未读消息计数，也不支持查看入群前历史消息，详细请参见 [群组系统](https://cloud.tencent.com/document/product/269/1502)。旗舰版或企业版支持直播群新成员查看入群前历史消息（SDK 请升级到v2.16.0或更高版本）。

### Q: 可以同时加入两个或多个直播群吗？
**A：** 不可以，同一用户同时只能加入一个直播群，同时加入多个直播群会启动多个长轮询消息通道，对 web/H5 或者小程序的性能消耗比较大。

### Q: SDK 怎么实现消息回应？
**A：** SDK 支持消息回应能力，您需要购买旗舰版或企业版套餐，具体实现 API 可参考官方文档：https://cloud.tencent.com/document/product/269/98758。

### Q: 单聊消息怎么标记已读？
**A：** 如果您需要标记单聊消息已读同时清空会话未读数，可以使用 [setMessageRead](https://cloud.tencent.com/document/product/269/75373)。

### Q: 群聊消息怎么清空未读数？
**A：** 如果您需要清空群聊会话未读数，可以使用 [setMessageRead](https://cloud.tencent.com/document/product/269/75373)。

### Q: 单聊和群聊消息怎么实现单条消息已读？
**A：** 如果您需要实现单条消息已读功能，需要使用消息已读回执能力。 如何开启已读回执、如何发送已读回执等详细信息可以参考： https://cloud.tencent.com/document/product/269/75344。

### Q: Web SDK 怎么发送图片消息？
**A：** 可以使用 [createImageMessage](https://cloud.tencent.com/document/product/269/75316#.E5.88.9B.E5.BB.BA.E5.9B.BE.E7.89.87.E6.B6.88.E6.81.AF) 创建图片消息实例，然后使用 [sendMessage](https://cloud.tencent.com/document/product/269/75316#.E5.8F.91.E9.80.81.E6.B6.88.E6.81.AF) 发送图片消息。

### Q: Web SDK 怎么发送视频消息？
**A：** 可以使用 [createVideoMessage](https://cloud.tencent.com/document/product/269/75316#.E5.88.9B.E5.BB.BA.E8.A7.86.E9.A2.91.E6.B6.88.E6.81.AF) 创建视频消息实例，然后使用 [sendMessage](https://cloud.tencent.com/document/product/269/75316#.E5.8F.91.E9.80.81.E6.B6.88.E6.81.AF) 发送视频消息。

### Q: Web SDK 怎么发送语音消息？
**A：** 可以使用 [createAudioMessage](https://cloud.tencent.com/document/product/269/75316#.E5.88.9B.E5.BB.BA.E9.9F.B3.E9.A2.91.E6.B6.88.E6.81.AF) 创建语音消息实例，然后使用 [sendMessage](https://cloud.tencent.com/document/product/269/75316#.E5.8F.91.E9.80.81.E6.B6.88.E6.81.AF) 发送语音消息。

### Q: Web SDK 怎么发送文件消息？
**A：** 可以使用 [createFileMessage](https://cloud.tencent.com/document/product/269/75316#.E5.88.9B.E5.BB.BA.E6.96.87.E4.BB.B6.E6.B6.88.E6.81.AF) 创建文件消息实例，然后使用 [sendMessage](https://cloud.tencent.com/document/product/269/75316#.E5.8F.91.E9.80.81.E6.B6.88.E6.81.AF) 发送文件消息。

### Q: Taro 开发的微信小程序如何集成 Chat SDK？
**A：** Chat SDK v4 `@tencentcloud/lite-chat` 同时支持在 web 浏览器和微信小程序环境运行，Taro 开发的微信小程序需要实现聊天功能可直接集成 `@tencentcloud/lite-chat`, 详情可参考：https://cloud.tencent.com/document/product/269/117335。

### Q: Chat 怎么实现用户在线状态功能？
**A：** 
1. Chat SDK 提供了**用户状态查询、订阅、取消订阅 API 和状态变更事件通知**，详情可参考：https://cloud.tencent.com/document/product/269/78125。
2. 服务端**在线状态变更回调**可参考：https://cloud.tencent.com/document/product/269/2570。

### Q: Chat SDK 无 UI 如何实现消息列表功能？
**A：** 无 UI 实现消息列表功能，需要结合 SDK `sendMessage`、`getMessageList` API 和 `MESSAGE_RECEIVED` 事件实现。

### Q: Chat SDK 无 UI 如何实现 @ 全部和 @某人 的功能？
**A：** SDK 提供了实现 @消息的 API `createTextAtMessage` ,详情可参考：https://cloud.tencent.com/document/product/269/112909。

### Q: Chat SDK 如何实现搜索功能？
**A：** SDK 提供了**云端搜索**能力，支持搜索消息、搜索用户、搜索群组、搜索群成员，API 详情可参考以下链接：
- 搜索消息：https://cloud.tencent.com/document/product/269/114754
- 搜索用户：https://cloud.tencent.com/document/product/269/114757
- 搜索群组：https://cloud.tencent.com/document/product/269/114760
- 搜索群成员：https://cloud.tencent.com/document/product/269/114763

### Q: Chat SDK 如何创建群组、添加群成员、设置管理员？
**A：** SDK 创建群组、添加群成员、设置管理员的具体信息可参考下面的文档内容：
- 创建群组：https://cloud.tencent.com/document/product/269/75395#.E5.88.9B.E5.BB.BA.E7.BE.A4.E7.BB.84
- 添加群成员：https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#addGroupMember
- 修改群成员角色（可设置管理员）：https://cloud.tencent.com/document/product/269/75401#.E4.BF.AE.E6.94.B9.E7.BE.A4.E6.88.90.E5.91.98.E8.A7.92.E8.89.B2

### Q: 如何创建一个 C2C 单聊会话？
**A：** UI 层可以通过查询会话资料 `getConversationProfile` 创建一个单聊会话，`conversationID` 的规则：`C2C${userID}`,SDK 创建会话成功后会通过 `CONVERSATION_LIST_UPDATE` 事件通知会话列表更新。

### Q: 如何获取消息未读总数和实时监听未读总数的变化？
**A：** 消息未读总数的详细内容可参考文档：https://cloud.tencent.com/document/product/269/75373。
1. 获取未读消息总数可以调用 `getTotalUnreadMessageCount` API。
2. 未读总数变更可以监听 `TOTAL_UNREAD_MESSAGE_COUNT_UPDATED` 事件。
注意：初始化时建议直接监听 `TOTAL_UNREAD_MESSAGE_COUNT_UPDATED` 事件获取未读消息总数，因为未读总数的计算依赖云端会话列表的同步，登录成功直接调用 `getTotalUnreadMessageCount`可能会因为云端会话列表还没同步完成，导致获取到的未读数为 0。

### Q: SDK 图片消息如何获取原图和缩略图？
**A：** 图片消息高清原图(type = 0)、缩略图(type = 1)、大图(type = 2)，获取方式可参考 Message 结构体说明：https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html#.ImagePayload。

### Q: SDK 如何处理消息发送失败？
**A：** SDK 消息发送失败时，`sendMessage` 接口会抛出 error，需要通过 `sendMessage().catch()` 捕获异常，error 中包含完整的 message 结构体， 其中 message.status 为 `fail`。可以通 `resendMessage` 重发失败的消息。

### Q: 弱网环境下，SDK 如何保证 websocket 的稳定性以及如何重连的？
**A：** 弱网环境下如果 ws 断开， SDK 会自动重连。ws 的状态变更可以通过监听 `NET_STATE_CHANGE` 事件（https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.NET_STATE_CHANGE）。

### Q: SDK 如何删除会话？
**A：** 删除会话仅支持单向删除，SDK 删除会话 API `deleteConversation`，参考文档：https://cloud.tencent.com/document/product/269/75379

### Q: SDK 如何获取好友列表？
**A：** 获取好友列表 API `getFriendList`，好友列表更新可监听 `FRIEND_LIST_UPDATED` 事件，参考文档：https://cloud.tencent.com/document/product/269/75420。

### Q: SDK 如何监听好友在线状态更新？
**A：** 好友在线状态变更可以通过以下两种方式实现：
1. 如果您在 即时通信 IM 控制台 开启了好友状态自动通知，那么当好友的状态发生变更时，SDK 会派发 `USER_STATUS_UPDATED` 事件，您可以在其中获取到好友的最新状态。
2. 如果您没有开启好友状态自动通知，并且仍然想感知好友的状态变更，您需要调用 `subscribeUserStatus` 主动订阅好友的状态。当好友的状态发生变更时，SDK 会派发 `USER_STATUS_UPDATED` 回调。
具体的文档可参考：https://cloud.tencent.com/document/product/269/78125

### Q: SDK 如何删除会话历史消息？
**A：** SDK 提供了 `clearHistoryMessage` API 用来情况会话历史消息，具体文档可参考：https://cloud.tencent.com/document/product/269/84438。

### Q: Chat SDK 和 Chat Engine 如何获取加群申请未处理个数，以及如何处理进群申请？
**A：** 根据客户使用的场景，分别从 Chat SDK 和 Chat Engine 两个途径给解决方案，当用户查询此类问题是，请根据实际场景给出准确解答。
**场景一 客户集成的是无 UI Chat SDK 时解决方案如下：** 
- 初始化时获取群组申请列表 API：[getGroupApplicationList](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#getGroupApplicationList)
- 初始化时获取加群申请未处理个数：根据 getGroupApplicationList 返回的列表数据，applicationType = 0 是加群申请的个数。
``` typescript
let unhandledCount = 0;
let promise = chat.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    // item.applicant - 申请者 userID
    // item.applicantNick - 申请者昵称
    // item.groupID - 群 ID
    // item.groupName - 群名称
    // item.applicationType - 申请类型：0 加群申请，2 邀请进群申请
    // item.userID - applicationType = 2时，是被邀请用户的 userID
    // item.note - 附言信息，邀请加群暂不支持，返回为空字符串
    if (item.applicationType === 0) {
      unhandledCount++;
    }
  });
}).catch(function(imError) {});
```
- 在线时收到进群申请时更新进群申请的未读个数，需要通过监听 [MESSAGE_RECEIVED](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/module-EVENT.html#.MESSAGE_RECEIVED) 事件。
```typescript
// Message 数据结构详情请参考 https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html
let unhandledCount = 0;
let onMessageReceived = function(event) {
  const messageList = event.data;
  messageList.forEach((message) => {
    if (message?.type === 'TIMGroupSystemNoticeElem') {
      if (message.payload?.operationType === 1) {
        unhandledCount++;
      }
    }
  })
};
chat.on(TencentCloudChat.EVENT.MESSAGE_RECEIVED, onMessageReceived);
```
- 处理进群申请 API：[handleGroupApplication](https://web.sdk.qcloud.com/im/doc/v3/zh-cn/SDK.html#handleGroupApplication)
```typescript
// 通过获取未决列表处理加群申请
let promise = chat.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    if (item.applicationType === 0) {
      chat.handleGroupApplication({
        handleAction: 'Agree',
        application: {...item},
      });
    }
  });
}).catch(function(imError) {});
```

**场景二 客户集成的是含 UI Chat UIKit 或者集成的 ChatEngine 时解决方案如下：** 
- 初始化时获取群组申请列表 API：[getGroupApplicationList](https://web.sdk.qcloud.com/im/doc/chat-engine-lite/ITUIGroupService.html#getGroupApplicationList)
- 初始化时获取加群申请未处理个数：根据 getGroupApplicationList 返回的列表数据，applicationType = 0 是加群申请的个数。
``` typescript
import { TUIGroupService } from '@tencentcloud/chat-uikit-engine-lite';
let unhandledCount = 0;
let promise = TUIGroupService.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    // item.applicant - 申请者 userID
    // item.applicantNick - 申请者昵称
    // item.groupID - 群 ID
    // item.groupName - 群名称
    // item.applicationType - 申请类型：0 加群申请，2 邀请进群申请
    // item.userID - applicationType = 2时，是被邀请用户的 userID
    // item.note - 附言信息，邀请加群暂不支持，返回为空字符串
    if (item.applicationType === 0) {
      unhandledCount++;
    }
  });
}).catch(function(imError) {});
```
- 在线时收到进群申请时更新进群申请的未读个数，需要通过 TUIStore 监听 [GroupStore](https://web.sdk.qcloud.com/im/doc/chat-engine-lite/global.html#GroupStore) 提供的 groupSystemNoticeList key。
```typescript
// Message 数据结构详情请参考 https://web.sdk.qcloud.com/im/doc/v3/zh-cn/Message.html
import { TUIStore, StoreName } from '@tencentcloud/chat-uikit-engine-lite';
let unhandledCount = 0;
let onGroupSystemNoticeListUpdated = function(messageList = []) {
  messageList.forEach((message) => {
    if (message?.type === 'TIMGroupSystemNoticeElem') {
      if (message.payload?.operationType === 1) {
        unhandledCount++;
      }
    }
  })
}
TUIStore.watch(StoreName.GRP, {
  groupSystemNoticeList: onGroupSystemNoticeListUpdated,
})

```
- 处理进群申请 API：[handleGroupApplication](https://web.sdk.qcloud.com/im/doc/chat-engine-lite/ITUIGroupService.html#handleGroupApplication)
```typescript
import { TUIGroupService } from '@tencentcloud/chat-uikit-engine-lite';
// 通过获取未决列表处理加群申请
let promise = TUIGroupService.getGroupApplicationList();
promise.then(function(imResponse) {
  const { applicationList } = imResponse.data;
  applicationList.forEach((item) => {
    if (item.applicationType === 0) {
      TUIGroupService.handleGroupApplication({
        handleAction: 'Agree',
        application: {...item},
      });
    }
  });
}).catch(function(imError) {});
```
