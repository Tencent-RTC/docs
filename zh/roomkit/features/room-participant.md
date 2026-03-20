> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

`RoomParticipantStore` 是 `AtomicXCore` 中专用于房间参与者管理的模块。该模块为多人房间场景提供了核心能力，支持构建完整的成员管理体系。

> **Vue 差异：** 本篇文档将指导您如何管理房间内的参会者。Atomicx 提供了开箱即用的列表组件 `RoomParticipantList` 用于快速集成，并暴露了 [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState) Hook，方便您实现自定义的成员控制逻辑

## 房间模式说明

在开始使用成员管理功能之前，需要了解 `AtomicXCore` 中房间模块支持的两种房间模式，不同模式下的成员管理功能有所区别：
| **标准房间模式（Standard Room）** | **网络研讨会房间模式（Webinar Room）** |
| --- | --- |
| - **适用场景：**小型会议、团队协作、朋友聊天等。 - **成员特点：**所有成员都是参与者，可以自由开启/关闭音视频设备。 - **管理特点：**扁平化管理，房主可设置管理员进行协助管理。 | - **适用场景**：网络研讨会、在线培训等。 - **成员特点**：   - **嘉宾（Participants）**：可以开启音视频设备，参与互动。   - **观众（Audience）**：只能观看，无法开启音视频设备。 - **管理特点**：分层管理，支持观众与嘉宾身份转换。 |

## 核心功能

- **获取参与者列表**：获取并展示当前房间内所有参与者信息（网络研讨会房间模式下，获取到的是嘉宾列表，嘉宾可以开启麦克风与其他嘉宾互动）。
- **获取观众列表：**网络研讨会房间模式下，获取观众列表信息。
- **搜索房间内成员：**搜索所在房间内的指定用户。
- **将观众提升为嘉宾：**网络研讨会房间模式下，可以将观众提升为嘉宾，提升为嘉宾的用户可以开启麦克风与其他房间内的嘉宾互动。
- **将嘉宾降级为观众：**网络研讨会房间模式下，可以将嘉宾降级为观众， 降级为观众的用户无法与房间内的嘉宾进行语音互动。
- **转移房主身份**：房主可以将房主身份转移给房间内任意一名参与者。
- **设置/撤销管理员**：房主可以设置参与者为管理员，同时也可撤销房间内的管理员。
- **将参与者移出房间**：房主或者管理员可以将房间内任意一名参与者移出房间。
- **更新参与者信息**：房间内所有人都可以更新自己的参与者信息。
- **音视频设备管理**：支持申请/邀请打开摄像头、麦克风，关闭远端摄像头、麦克风，以及房主和管理员设置全体静音，全体禁用摄像头功能。

> **Vue 差异：** 成员管理模块涵盖了房间内用户信息展示以及权限处理的全套能力：。- **实时列表展示：**动态呈现房间内所有成员的昵称、角色、音视频状态及音量波动。- **权限控制：**支持房主或管理员执行踢人、全员禁言、转让房主等管理操作。- **分级管理：**支持对房主、管理员及普通成员实现差异化的权限策略

## 功能对比

| **功能** | **标准房间模式** | **网络研讨会房间模式** |
| --- | --- | --- |
| 获取参与者列表 | 获取所有房间成员 | 获取嘉宾列表 |
| 获取观众列表 | 不适用 | 获取观众列表 |
| 搜索房间内成员 | 支持 | 支持 |
| 将观众提升为嘉宾 | 不适用 | 支持 |
| 将嘉宾降级为观众 | 不适用 | 支持 |
| 转移房主身份 | 支持 | 支持 |
| 设置/撤销管理员 | 支持 | 支持 |
| 移出房间 | 支持 | 支持 |
| 更新参与者信息 | 支持 | 支持 |
| 音视频设备管理 | 支持 | 支持 |

## 核心概念

在开始集成之前，需要通过下表了解一下`RoomParticipantStore`相关的几个核心概念：
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| RoomParticipant | `data class` | 代表参与者的核心数据模型，封装了参与者的完整信息和状态管理能力。 **标准房间模式：**代表房间内的所有成员。 **网络研讨会房间模式：**代表嘉宾，可以开启/关闭麦克风。 核心功能包括：参与者基本信息管理（用户 ID 、用户名称、用户头像、房间内角色身份）、参与者设备状态管理（麦克风状态，摄像头状态，屏幕分享状态，消息状态）。 |
| RoomParticipantState | `data class` | 代表参与者状态管理的核心数据结构，负责维护房间内与参与者相关状态信息。 核心属性： - `participantList`**标准房间模式**存储所有房间成员，**网络研讨会房间模式**存储嘉宾列表。 - `participantListCursor`**标准房间模式**代表房间内所有成员列表的分页游标，**网络研讨会房间模式**代表房间内嘉宾列表的分页游标。 - `localParticipant` 代表自身所在房间内的参与者信息。 |
| RoomParticipantListener | `abstract class` | 代表房间内和参与者相关的实时事件。主要事件分为三大类：参与者进退房事件、 参与者身份改变事件，参与者设备控制事件。 |
| RoomParticipantStore | `abstract class` | 这是参与者控制相关的核心类。功能包含：控制房间内参与者音视频状态、执行参与者管理操作，并通过订阅其 `RoomParticipantListener` 来接收实时事件。 |

## 实现步骤

### 步骤1：组件集成

请参考 接入概览 集成 **AtomicXCore SDK**，并已完成 实现登录逻辑 部分的接入。

### 步骤2：获取参与者列表

在加入房间后，调用RoomParticipantStore的getParticipantList接口获取房间内参与者列表。
- **标准房间模式：**获取房间内所有成员列表。
- **网络研讨会房间模式：**获取房间内嘉宾列表。
   ``` swift
   import Foundation
   import AtomicXCore
   func getParticipantList() {
       // 1. 业务逻辑说明
       // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
       let participantStore = RoomParticipantStore.create(roomID: "your_room_id")
       // 2. 首次获取参与者列表（从头开始）
       // 支持分页加载，可以通过 cursor 参数实现增量获取
       let initialCursor: String? = nil  // nil 表示从第一页开始获取
       participantStore.getParticipantList(cursor: initialCursor) { result in
           switch result {
           case .success(let participantInfo):
               print("成功获取参与者列表，数量: \(participantInfo.0.count)")
           case .failure(let error):
               print("获取参与者列表失败 [错误码: \(error.code)]: \(error.message)")
           }
       }
   }
   ```

> **Android 差异：** 在加入房间后，调用 `RoomParticipantStore` 的 `getParticipantList` 接口获取到房间内参与者列表
> **Flutter 差异：** 在加入房间后，调用`RoomParticipantStore`的`getParticipantList`接口获取到房间内参与者列表
> **Vue 差异：** 通过 [getParticipantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#getParticipantList) 获取响应式的成员数据 [participantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#participantList)。
> **注意**
> **说明：**
> `getParticipantList` 接口拉取成功的数据会自动维护在 `participantList` 中，UI 只需要渲染 `participantList` 的数据即可。

**代码示例：**

**Vue（主示例）：**
``` typescript
import { useRoomState, useRoomParticipantState } from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { participantList, participantListCursor, getParticipantList } = useRoomParticipantState();

// 在进房成功之后获取房间内用户列表
watch(() => currentRoom.value?.roomId, async () => {
 await getParticipantList();
})

// 当列表滚动到底部时，加载更多用户
const handleLoadMore = async () => {
  if (participantListCursor.value) {
    await getParticipantList({ cursor: participantListCursor.value });
  }
};
```

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipant
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler

fun getParticipantList() {
    // 1. 业务逻辑说明
    // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
    val roomID = "test_room_001"
    val participantStore = RoomParticipantStore.create(roomID)

    // 2. 首次获取参与者列表（从头开始）
    // 支持分页加载，可以通过 cursor 参数实现增量获取
    val initialCursor: String? = null  // null 表示从第一页开始获取

    participantStore.getParticipantList(initialCursor, object : ListResultCompletionHandler<RoomParticipant> {
        override fun onSuccess(result: List<RoomParticipant>, cursor: String) {
            Log.d("Participant", "成功获取参与者列表，数量: ${result.size}")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "获取参与者列表失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| Flutter | Future<void> getParticipantList() async { | - | Future<Result> |

### 步骤3：获取观众列表

在进入的房间类型为`WEBINAR`类型时，可以通过调用`RoomParticipantStore`的`getAudienceList`接口获取房间内的观众列表。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

fun getAudienceList() {
    // 前提：需要先完成进房操作, 并且进房时 roomType 设置为 WEBINAR 类型。
    // 1. 业务逻辑说明
    // 通过进房的roomID创建RoomParticipantStore实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 首次获取观众列表（从头开始）
    // 支持分页加载，可以通过 cursor 参数实现增量获取
    val initialCursor: String? = null  // null 表示从第一页开始获取

    participantStore.getAudienceList(cursor = initialCursor, completion = object : ListResultCompletionHandler<RoomUser> {
        override fun onSuccess(result: List<RoomUser>, cursor: String) {
            Log.d("Test", "获取观众列表成功，观众数量: ${result.size}")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Test", "获取观众列表失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func getAudienceList() { | - | Closure + Result |
| Flutter | Future<void> getAudienceList() async { | - | Future<Result> |

### 步骤4：搜索房间内成员

进入房间成功后，可以通过调用`RoomParticipantStore`的`searchUsers`接口搜索房间内的成员。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

fun searchUsers() {
    // 前提：需要先完成进房操作。
    // 1. 业务逻辑说明
    // 通过进房的roomID创建RoomParticipantStore实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 通过用户名称为关键字搜索房间内成员
    val keyword = "userName"

    participantStore.searchUsers(keyword = keyword, completion = object : ListResultCompletionHandler<RoomUser> {
        override fun onSuccess(result: List<RoomUser>, cursor: String) {
            Log.d("Test", "搜索房间成员成功，成员数量: ${result.size}")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Test", "搜索房间成员失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func searchUsers() { | - | Closure + Result |
| Flutter | Future<void> searchUsers() async { | - | Future<Result> |

### 步骤5：将**观众提升为嘉宾**

网络研讨会房间模式下，作为房主或管理员，调用`RoomParticipantStore`的`promoteAudienceToParticipant`接口可以将房间内的观众提升为嘉宾。
作为房间内成员订阅`RoomParticipantStore`的`participantEventPublisher`中的`onAudiencePromotedToParticipant`事件，被动接收观众提升为参与者的变化通知。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore

fun promoteAudienceToParticipant(userID: String) {
    // 前提：需要先完成进房操作。
    // 1. 业务逻辑说明
    // 通过进房的 roomID 创建 RoomParticipantStore 实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 调用 RoomParticipantStore 提升观众为嘉宾接口
    // 注意：只有房主或管理员才有权限执行此操作。
    participantStore.promoteAudienceToParticipant(userID = userID, completion = object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Test", "提升为嘉宾成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Test", "提升为嘉宾失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

fun subscribeParticipantEvent() {
    // 1. 业务逻辑说明
    // 前提：需要先完成进房操作,通过进房的 roomID 创建 RoomParticipantStore 实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 订阅参与者事件
    // 通过添加 RoomParticipantListener 监听所有参与者相关的事件
    val listener = object : RoomParticipantListener() {
        override fun onAudiencePromotedToParticipant(userInfo: RoomUser) {
            Log.d("Test", "观众被提升为嘉宾，userInfo: $userInfo")
        }
    }

    participantStore.addRoomParticipantListener(listener)

    // 注意：在不需要监听时，记得移除监听器
    // participantStore.removeRoomParticipantListener(listener)
}

```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func promoteAudienceToParticipant(userID: String) { | // participantEventPublisher 会推送所有参与者相关的事件 | Closure + Result |
| Flutter | Future<void> promoteAudienceToParticipant(String userID) async { | late RoomParticipantListener _listener; | Future<Result> |

### 步骤6：将**嘉宾降级为观众**

网络研讨会房间模式下，作为房主或管理员，调用`RoomParticipantStore`的`demoteParticipantToAudience`接口可以将房间内的嘉宾降级为观众。
作为房间内成员订阅`RoomParticipantStore`的`participantEventPublisher`中的`onParticipantDemotedToAudience`事件，被动接收参与者降级为观众的变化通知。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore

fun demoteParticipantToAudience(userID: String) {
    // 前提：需要先完成进房操作。
    // 1. 业务逻辑说明
    // 通过进房的 roomID 创建 RoomParticipantStore 实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 调用 RoomParticipantStore 降级嘉宾为观众接口
    // 注意：只有房主或管理员才有权限执行此操作。
    participantStore.demoteParticipantToAudience(userID = userID, completion = object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Test", "降级为观众成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Test", "降级为观众失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

fun subscribeParticipantEvent() {
    // 1. 业务逻辑说明
    // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
    val participantStore = RoomParticipantStore.create(roomID = "your_room_id")

    // 2. 订阅参与者事件
    // 通过添加 RoomParticipantListener 监听所有参与者相关的事件
    val listener = object : RoomParticipantListener() {
        override fun onParticipantDemotedToAudience(userInfo: RoomUser) {
            Log.d("Test", "嘉宾被降级为观众，userInfo: $userInfo")
        }
    }

    participantStore.addRoomParticipantListener(listener)

    // 注意：在不需要监听时，记得移除监听器
    // participantStore.removeRoomParticipantListener(listener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func demoteParticipantToAudience(userID: String) { | // participantEventPublisher 会推送所有参与者相关的事件 | Closure + Result |
| Flutter | Future<void> demoteParticipantToAudience(String userID) async { | late RoomParticipantListener _listener; | Future<Result> |

### 步骤7：转移房主身份

作为房主，调用 `RoomParticipantStore` 的 `transferOwner` 接口可以将房主身份直接转移给房间内任意一位参与者，转移后原房主身份将变为普通参与者，一个房间内有且仅有一名房主。
作为参与者，订阅 `RoomParticipantStore` 的 `RoomParticipantListener` 中的 `onOwnerChanged` 事件，被动接收房主变化通知。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

fun transferOwner(userID: String) {
    // 1. 业务逻辑说明
    // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
    val roomID = "test_room_001"
    val participantStore = RoomParticipantStore.create(roomID)

    // 2. 调用 RoomParticipantStore 转移房主接口
    // 注意：只有当前房主才有权限执行此操作，转移后原房主将变为普通用户
    participantStore.transferOwner(userID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "房主权限转移成功，新房主: $userID")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "转移房主权限失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 参与者事件监听器
private val participantListener = object : RoomParticipantListener() {
    override fun onOwnerChanged(newOwner: RoomUser, oldOwner: RoomUser) {
        Log.d("Participant", "房主变更通知，newOwner: $newOwner, oldOwner: $oldOwner")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvent() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func transferOwner(to userID: String) { | // participantEventPublisher 会推送所有参与者相关的事件 | Closure + Result |
| Flutter | Future<void> transferOwner(String userID) async { | late RoomParticipantListener _participantListener; | Future<Result> |

### 步骤8：设置/撤销管理员

作为房主，调用 `RoomParticipantStore` 的 `setAdmin` 接口，指定房间内任意一位用户为管理员，也可以将管理员身份撤销。
作为参与者，订阅 `RoomParticipantStore` 的 `RoomParticipantListener` 中的 `onAdminSet` 和 `onAdminRevoked` 事件，被动接收参与者身份变化通知。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 设置用户为管理员
fun setUserAsAdmin(userID: String) {
    participantStore.setAdmin(userID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "成功设置用户 $userID 为管理员")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "设置管理员失败 [错误码: $code]: $desc")
        }
    })
}

// 撤销用户的管理员权限
fun revokeAdmin(userID: String) {
    participantStore.revokeAdmin(userID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "成功撤销用户 $userID 的管理员权限")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "撤销管理员失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 参与者事件监听器
private val participantListener = object : RoomParticipantListener() {
    override fun onAdminSet(userInfo: RoomUser) {
        Log.d("Participant", "设置管理员事件通知，userInfo: $userInfo")
    }

    override fun onAdminRevoked(userInfo: RoomUser) {
        Log.d("Participant", "撤销管理员事件通知，userInfo: $userInfo")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvents() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func setUserAsAdmin(userID: String) { | participantStore.participantEventPublisher | Closure + Result |
| Flutter | Future<void> setUserAsAdmin(String userID) async { | late RoomParticipantListener _participantListener; | Future<Result> |

### 步骤9：**将参与者移出房间**

作为房主和管理员，调用 `RoomParticipantStore` 的 `kickUser` 可以将房间内参与者移出房间。
作为参与者，订阅 `RoomParticipantStore` 的 `RoomParticipantListener` 中的 `onKickedFromRoom` 事件，被动接收参与者被移出房间通知。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun kickUser(userID: String) {
    // 1. 业务逻辑说明
    // kickUser 接口用于将指定用户移出房间
    // 注意：只有房主或管理员才有权限执行此操作
    // 被移出的用户将立即离开房间，并收到相应的通知

    // 2. 调用 RoomParticipantStore 移出用户接口
    participantStore.kickUser(userID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "用户移出成功，被移出用户: $userID")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "移出用户失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.KickedOutOfRoomReason
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

private val participantListener = object : RoomParticipantListener() {
    override fun onKickedFromRoom(reason: KickedOutOfRoomReason, message: String) {
        Log.d("Participant", "已被移出房间，被移出原因：$reason, 额外信息：$message")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvents() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func kickUser(userID: String) { | participantStore.participantEventPublisher | Closure + Result |
| Flutter | Future<void> kickUser(String userID) async { | late RoomParticipantListener _participantListener; | Future<Result> |

### 步骤10：更新参与者信息

在加入房间后，调用 `RoomParticipantStore` 的 `updateParticipantNameCard`、 `updateParticipantMetaData` 更新您在房间里的参与者信息，包含：参与者名片信息，参与者自定义信息。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun updateParticipantNameCard(userID: String, nameCard: String) {
    // 1. 业务逻辑说明
    // updateParticipantNameCard 接口用于更新指定参与者的名片信息
    // 名片通常用于显示用户的自定义昵称或标识
    // 注意：通常只有管理员或用户本人才能修改名片

    // 2. 调用 RoomParticipantStore 更新名片接口
    participantStore.updateParticipantNameCard(userID, nameCard, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "名片更新成功，用户: $userID, 新名片: $nameCard")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "名片更新失败 [错误码: $code]: $desc")
        }
    })
}

fun updateParticipantMetaData(userID: String, metaData: HashMap<String, ByteArray>) {
    // 1. 业务逻辑说明
    // updateParticipantMetaData 接口用于更新指定参与者的自定义信息
    // 存储自定义的业务数据，如用户标签、扩展属性等
    // 注意：自定义信息的键值对数量和大小有限制，键长度不能超过 50 字节，值长度不超过 200 字节

    // 2. 调用 RoomParticipantStore 更新自定义信息接口
    participantStore.updateParticipantMetaData(userID, metaData, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "自定义信息数据更新成功，用户: $userID, 键数量: ${metaData.size}")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "自定义信息更新失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func updateParticipantNameCard(userID: String, nameCard: String) { | - | Closure + Result |
| Flutter | Future<void> updateParticipantNameCard(String userID, String nameCard) async { | - | Future<Result> |

### 步骤11：邀请参与者打开其设备

作为房主和管理员，调用 `RoomParticipantStore` 的 `inviteToOpenDevice` 接口，邀请房间内参与者打开摄像头，麦克风。
作为参与者，需要：
- 订阅 `RoomParticipantStore` 的 `RoomParticipantListener` 中的 `onDeviceInvitationReceived` 事件，被动接收邀请打开设备通知。
- 接收到邀请打开设备通知后调用 `RoomParticipantStore` 的 `acceptOpenDeviceInvitation` 或者 `declineOpenDeviceInvitation` 接口接受或拒绝邀请。
   ``` java
   import android.util.Log
   import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
   import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
   import io.trtc.tuikit.atomicxcore.api.room.DeviceRequestInfo
   import io.trtc.tuikit.atomicxcore.api.device.DeviceType
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   // 业务逻辑说明
   // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
   private val roomID = "test_room_001"
   private val participantStore = RoomParticipantStore.create(roomID)
   // 参与者事件监听器
   private val participantListener = object : RoomParticipantListener() {
       override fun onDeviceInvitationReceived(invitation: DeviceRequestInfo) {
           Log.d("Participant", "收到设备邀请 - 设备: ${invitation.device}, 发送者: ${invitation.senderUserName}")
           // 处理接受、拒绝设备邀请
       }
   }
   // 订阅参与者相关事件
   private fun subscribeParticipantEvents() {
       participantStore.addRoomParticipantListener(participantListener)
   }
   fun acceptOpenDeviceInvitation(userID: String, device: DeviceType) {
       // 处理接受邀请
       participantStore.acceptOpenDeviceInvitation(userID, device, object : CompletionHandler {
           override fun onSuccess() {
               Log.d("Participant", "接受设备邀请成功 - 设备: $device")
           }
           override fun onFailure(code: Int, desc: String) {
               Log.e("Participant", "接受设备邀请失败 [错误码: $code]: $desc")
           }
       })
   }
   fun declineOpenDeviceInvitation(userID: String, device: DeviceType) {
       // 处理拒绝邀请
       participantStore.declineOpenDeviceInvitation(userID, device, object : CompletionHandler {
           override fun onSuccess() {
               Log.d("Participant", "拒绝设备邀请成功 - 设备: $device")
           }
           override fun onFailure(code: Int, desc: String) {
               Log.e("Participant", "拒绝设备邀请失败 [错误码: $code]: $desc")
           }
       })
   }
   ```

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun inviteUserToOpenDevice(userID: String, device: DeviceType, timeout: Int = 30) {
    // 1. 业务逻辑说明
    // inviteToOpenDevice 接口用于邀请指定用户开启某个设备
    // 通常用于管理员邀请参与者开启麦克风、摄像头或屏幕共享
    // 被邀请的用户会收到邀请通知，可以选择接受或拒绝

    // 2. 调用 RoomParticipantStore 邀请开启设备接口
    participantStore.inviteToOpenDevice(userID, device, timeout, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "设备邀请发送成功，用户: $userID, 设备: $device")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "设备邀请发送失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func inviteUserToOpenDevice(userID: String, device: DeviceType, timeout: Int = 30) { | - | Closure + Result |
| Flutter | Future<void> inviteUserToOpenDevice(String userID, DeviceType device, {int timeout = 30}) async { | - | Future<Result> |

### 步骤12：关闭远端参与者设备

作为房主和管理员，调用`RoomParticipantStore`的`closeParticipantDevice`可以主动关闭远端参与者的摄像头，麦克风。
作为参与者，订阅`RoomParticipantStore`的`RoomParticipantListener`中的`onParticipantDeviceClosed`事件，被动接收设备被关闭通知，并在UI上做出提示。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun closeParticipantDevice(userID: String, device: DeviceType) {
    // 1. 业务逻辑说明
    // closeParticipantDevice 接口用于房主或管理员关闭指定参与者的某个设备
    // 被关闭的用户会收到设备被关闭通知

    // 2. 调用 RoomParticipantStore 关闭参与者设备接口
    participantStore.closeParticipantDevice(userID, device, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "关闭参与者设备成功 - 用户: $userID, 设备: $device")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "关闭参与者设备失败: [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 参与者事件监听器
private val participantListener = object : RoomParticipantListener() {
    override fun onParticipantDeviceClosed(device: DeviceType, operator: RoomUser) {
        Log.d("Participant", "设备被关闭 - 设备: $device, 操作者: ${operator.userName}")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvents() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func closeParticipantDevice(userID: String, device: DeviceType) { | participantStore.participantEventPublisher | Closure + Result |
| Flutter | Future<void> closeParticipantDevice(String userID, DeviceType device) async { | late RoomParticipantListener _participantListener; | Future<Result> |

### 步骤13：参与者申请打开自己的设备

作为参与者，调用`RoomParticipantStore`的`requestToOpenDevice`接口，可以在房间被房主或管理员设置全体静音，全体禁画的场景下，主动申请打开摄像头，麦克风。
作为房主和管理员，需要：
- 订阅`RoomParticipantStore`的`RoomParticipantListener`中的`onDeviceRequestReceived`事件，被动接收申请打开设备通知。
- 接收到申请打开设备通知后，调用`RoomParticipantStore`的`approveOpenDeviceRequest`或者`rejectOpenDeviceRequest`接口，批准或者拒绝申请。
   ``` java
   import android.util.Log
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import io.trtc.tuikit.atomicxcore.api.device.DeviceType
   import io.trtc.tuikit.atomicxcore.api.room.DeviceRequestInfo
   import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
   import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
   // 业务逻辑说明
   // 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
   private val roomID = "test_room_001"
   private val participantStore = RoomParticipantStore.create(roomID)
   // 参与者事件监听器
   private val participantListener = object : RoomParticipantListener() {
       override fun onDeviceRequestReceived(request: DeviceRequestInfo) {
           Log.d("Participant", "收到设备请求 - 用户: ${request.senderUserName}, 设备: ${request.device}")
           // 批准、拒绝开启设备请求操作
       }
   }
   // 订阅参与者相关事件
   private fun subscribeParticipantEvents() {
       participantStore.addRoomParticipantListener(participantListener)
   }
   fun approveOpenDeviceRequest(device: DeviceType, userID: String) {
       // 批准开启请求
       participantStore.approveOpenDeviceRequest(device, userID, object : CompletionHandler {
           override fun onSuccess() {
               Log.d("Participant", "批准设备请求成功 - 用户: $userID, 设备: $device")
           }
           override fun onFailure(code: Int, desc: String) {
               Log.e("Participant", "批准设备请求失败: $desc")
           }
       })
   }
   fun rejectOpenDeviceRequest(device: DeviceType, userID: String) {
       // 拒绝开启请求
       participantStore.rejectOpenDeviceRequest(device, userID, object : CompletionHandler {
           override fun onSuccess() {
               Log.d("Participant", "拒绝设备请求成功 - 用户: $userID, 设备: $device")
           }
           override fun onFailure(code: Int, desc: String) {
               Log.e("Participant", "拒绝设备请求失败: [错误码: $code]: $desc")
           }
       })
   }
   ```

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun requestToOpenDevice(device: DeviceType, timeout: Int = 30) {
    // 1. 业务逻辑说明
    // requestToOpenDevice 接口用于请求开启某个设备
    // 通常用于参与者申请开启麦克风、摄像头或屏幕共享
    // 房主和管理员会收到申请通知，可以选择同意或拒绝

    // 2. 调用 RoomParticipantStore 请求开启设备接口
    participantStore.requestToOpenDevice(device, timeout, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Participant", "设备开启请求发送成功 - 设备: $device")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Participant", "设备开启请求发送失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func requestToOpenDevice(device: DeviceType, timeout: Int = 30) { | - | Closure + Result |
| Flutter | Future<void> requestToOpenDevice(DeviceType device, {int timeout = 30}) async { | - | Future<Result> |

### 步骤14：全体静音、全体禁画

作为房主和管理员调用`RoomParticipantStore`的`disableAllDevices`接口设置全员静音与全员禁用摄像头。开启后，房间内参与者的音视频开启权限将被限制，无法自主打开麦克风或摄像头设备。
作为参与者订阅 `RoomParticipantStore`的`participantEventPublisher`中的`onAllDevicesDisabled`事件，可以监听全员静音或禁用摄像头指令，并在 UI 界面同步受控状态。受限状态下，摄像头与麦克风的主动开启功能将被锁定，参与者需发起开启申请，待房主或管理员核准授权后方可使用。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

fun disableAllDevices(device: DeviceType, disable: Boolean) {
    // 1. 业务逻辑说明
    // disableAllDevices 接口用于房主或管理员设置房间禁用、解禁房间内全体成员麦克风，摄像头
    // 设置后房间内参与者会收到禁用、解禁通知

    // 2. 调用 RoomParticipantStore 禁用、解禁全体设备接口
    participantStore.disableAllDevices(device, disable, object : CompletionHandler {
        override fun onSuccess() {
            val action = if (disable) "禁用" else "启用"
            Log.d("Participant", "${action}所有${device}成功")
        }

        override fun onFailure(code: Int, desc: String) {
            val action = if (disable) "禁用" else "启用"
            Log.e("Participant", "${action}所有${device}失败 [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.device.DeviceType
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 参与者事件监听器
private val participantListener = object : RoomParticipantListener() {
    override fun onAllDevicesDisabled(device: DeviceType, disable: Boolean, operator: RoomUser) {
        val action = if (disable) "禁用" else "启用"
        Log.d("Participant", "所有${device}被${action} - 操作者: ${operator.userName}")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvents() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func disableAllDevices(device: DeviceType, disable: Bool) { | participantStore.participantEventPublisher | Closure + Result |
| Flutter | Future<void> disableAllDevices(DeviceType device, bool disable) async { | late RoomParticipantListener _participantListener; | Future<Result> |

### 步骤15：监听参与者事件

订阅`RoomParticipantListener`参与者相关的被动事件。以订阅参与者加入房间、离开房间为例，示例代码如下：
订阅`RoomParticipantState`参与者相关的属性状态变化。以订阅房间内正在说话的用户为例，示例代码如下：

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantListener
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

// 参与者事件监听器
private val participantListener = object : RoomParticipantListener() {
    override fun onParticipantJoined(participant: RoomUser) {
        Log.d("Participant", "参与者加入房间 - 用户: ${participant.userName}, ID: ${participant.userID}")
    }

    override fun onParticipantLeft(participant: RoomUser) {
        Log.d("Participant", "参与者离开房间 - 用户: ${participant.userName}, ID: ${participant.userID}")
    }
}

// 订阅参与者相关事件
private fun subscribeParticipantEvents() {
    participantStore.addRoomParticipantListener(participantListener)
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomParticipantStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// 业务逻辑说明
// 前提：需要先完成进房操作,通过进房的roomID创建RoomParticipantStore实例
private val roomID = "test_room_001"
private val participantStore = RoomParticipantStore.create(roomID)

/// 设置参与者状态监听
private fun subscribeParticipantState() {
    CoroutineScope(Dispatchers.Main).launch {
        participantStore.state.speakingUsers.collect { speakingUsers ->
            Log.d("Participant", "说话用户状态变更 - 当前说话用户数: ${speakingUsers.size}")
        }
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | private func subscribeParticipantEvent() { | participantStore.participantEventPublisher | - |
| Flutter | void subscribeParticipantEvent() { | late RoomParticipantListener _participantListener; | - |

## API 文档

**能力索引**
| 能力 | 核心能力 | 适用平台 |
| --- | --- | --- |
| RoomParticipantStore | 房间内参与者管理：设置管理员 / 转移房主 / 获取参与者列表 / 移出房间 / 参与者设备控制（例如关闭、邀请打开摄像头、麦克风等）/ 申请打开设备（例如申请打开摄像头、麦克风等）。 | Android, iOS, Flutter |
| useRoomParticipantState | 包含房间内用户数据，用户管理接口。 | Vue |

**参数 / 返回 / 限制**
- 参数：以各平台 API 文档为准（命名不同，语义一致）。
- 返回：异步模型存在差异（Callback / Closure / Future / Promise）。
- 限制：需先完成登录并进入房间；管理类能力通常要求房主或管理员权限。

**主示例（Vue）：**
```typescript
import { useRoomParticipantState } from 'tuikit-atomicx-vue3/room';
const { getParticipantList } = useRoomParticipantState();
await getParticipantList();
```

**多平台差异（字段级）**
| 字段 | Android | iOS | Flutter | Vue |
| --- | --- | --- | --- | --- |
| API 入口 | RoomParticipantStore | RoomParticipantStore | RoomParticipantStore | useRoomParticipantState |
| 异步模型 | CompletionHandler | CompletionClosure | Future<Result> | Promise |
| 事件机制 | addRoomParticipantListener | participantEventPublisher | addRoomParticipantListener | subscribeEvent |
| 文档入口 | SDK API 文档 | SDK API 文档 | SDK API 文档 | Web API 文档 |

## 前提条件

- 用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

- 用户已经通过 [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 进入房间，请参考 房间管理。

## 集成成员管理组件

如果您需要快速构建成员列表界面，推荐直接使用 `RoomParticipantList` 组件。`RoomParticipantList` 是一个开箱即用的参会者列表组件，内置了完整的成员管理 UI 和交互。

集成 `RoomParticipantList` 组件示例代码：
``` typescript
<template>
  <div class="room-page">
    <!-- 参会者列表侧边栏 -->
    <aside class="sidebar">
      <div class="sidebar-header">
        <span>参会者 ({{ currentRoom.participantCount }})</span>
        <IconClose />
      </div>

      <!-- 参会者列表组件 -->
      <RoomParticipantList />
    </aside>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { IconClose } from '@tencentcloud/uikit-base-component-vue3';
import { 
  RoomView,
  RoomParticipantList,
  useRoomState,
  useRoomParticipantState 
} from 'tuikit-atomicx-vue3/room';

const { currentRoom } = useRoomState();
const { participantList } = useRoomParticipantState();
</script>

<style scope>
.room-page {
  width: 100vw;
  height: 100vh;
  min-width: 1150px;
  display: flex;
  flex-direction: row;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  position: relative;
  overflow: hidden;
}

.sidebar {
  position: absolute;
  top: 0;
  right: 0;
  width: 400px;
  height: 100%;
  box-sizing: border-box;
}

.sidebar-header {
  display: flex;
  height: 60px;
  justify-content: space-between;
  align-items: center;
  padding: 0px 20px;
}
</style>
```

> **说明：**
> - `RoomParticipantList` 会自动占满父容器的高度和宽度，请确保其父容器具备明确的尺寸。
> - 使用 `RoomParticipantList` 组件请确保在 `App.vue` 中配置全局 `UIKitProvider`，具体请参考 [配置 App.vue](https://cloud.tencent.com/document/product/647/81962#ce98ee15-b0cb-43af-81da-70f51231430e)。

## 实现成员管理功能

如果您需要自定义成员列表 UI 或在特定业务中触发管理行为，请使用 [useRoomParticipantState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomParticipantState)。

### 步骤2：渲染参会者信息

开发者可以使用  [participantList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#participantList) 数据自定义渲染用户信息列表。
``` typescript
<template>
  <div class="participant-list">
    <div class="list-header">
      <h3>参会者 ({{ participantList.length }})</h3>
      <button @click="handleRefresh">
        <IconRefresh />
      </button>
    </div>
    <div class="list-content">
      <div 
        v-for="participant in participantList" 
        :key="participant.userId"
        class="participant-item"
      >
        <!-- 头像 -->
        <Avatar 
          :user-id="participant.userId" 
          :avatar-url="participant.avatarUrl" 
        />

        <!-- 用户信息 -->
        <div class="user-info">
          <div class="user-name">
            <span>{{ participant.userName }}</span>
            <span v-if="participant.userId === localParticipant.userId" class="badge">我</span>
            <span 
              v-if="participant.role === RoomParticipantRole.Owner" 
              class="role-badge owner"
            >
              房主
            </span>
            <span 
              v-else-if="participant.role === RoomParticipantRole.Admin" 
              class="role-badge admin"
            >
              管理员
            </span>
          </div>
        </div>

        <!-- 设备状态 -->
        <div class="device-status">
          <IconMicOn 
            size="20"
            v-if="participant.microphoneStatus === DeviceStatus.On" 
            class="icon-on"
          />
          <IconMicOff size="20" v-else class="icon-off" />
          <IconCameraOn
            size="20"
            v-if="participant.cameraStatus === DeviceStatus.On" 
            class="icon-on"
          />
          <IconCameraOff size="20" v-else class="icon-off" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { IconRefresh, IconMicOn, IconMicOff, IconCameraOn, IconCameraOff } from '@tencentcloud/uikit-base-component-vue3';
import { 
  useRoomParticipantState,
  Avatar,
  RoomParticipantRole,
  DeviceStatus
} from 'tuikit-atomicx-vue3/room';

const { 
  localParticipant, 
  participantList,
  getParticipantList,
  transferOwner
} = useRoomParticipantState();

const handleRefresh = async () => {
  await getParticipantList({ cursor: '' });
};
</script>

<style scoped>
.participant-list {  display: flex;  flex-direction: column;  width: 400px;  height: 100%;  background-color: white;}.list-header {  display: flex;  align-items: center;  justify-content: space-between;  padding: 16px;  border-bottom: 1px solid #e0e0e0;}.list-header h3 {  margin: 0;  font-size: 16px;  font-weight: 600;}.list-content {  flex: 1;  overflow-y: auto;  padding: 8px;}.participant-item {  display: flex;  align-items: center;  gap: 12px;  padding: 12px;  border-radius: 8px;  transition: background-color 0.2s;}.participant-item:hover {  background-color: #f5f5f5;}.participant-item.is-local {  background-color: #e3f2fd;}.user-info {  flex: 1;  min-width: 0;}.user-name {  display: flex;  align-items: center;  gap: 6px;  font-size: 14px;  font-weight: 500;}.badge {  padding: 2px 6px;  background-color: #4caf50;  color: white;  border-radius: 3px;  font-size: 12px;}.role-badge {  padding: 2px 6px;  border-radius: 3px;  font-size: 12px;}.role-badge.owner {  background-color: #ff9800;  color: white;}.role-badge.admin {  background-color: #2196f3;  color: white;}.device-status {  display: flex;  gap: 8px;}.icon-on {  color: #4caf50;}.icon-off {  color: #999;}.action-button {  padding: 4px 8px;  background: transparent;  border: none;  cursor: pointer;  border-radius: 4px;  color: #666;}.action-button:hover {  background-color: #f0f0f0;}
</style>
```

### 步骤3：设置用户自定义信息

开发者可以使用 [updateParticipantMetaData](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateParticipantMetaData) 接口设置用户的自定义数据，用于**实现职务**、**购买状态**等能力。更新之后 `metaData` 信息将被维护在 `participantList` 对应用户的响应式数据中。
``` typescript
<template>
  <div v-for="participant in participantList" :key="participant.userId">
    <span>{{ participant.userName }}</span>
    <span v-if="participant.metaData">
      {{ JSON.parse(participant.metaData).level }}
    </span>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { IconRefresh, IconMicOn, IconMicOff, IconCameraOn, IconCameraOff } from '@tencentcloud/uikit-base-component-vue3';
import { 
  useRoomParticipantState,
} from 'tuikit-atomicx-vue3/room';

const { 
  updateParticipantMetaData,
} = useRoomParticipantState();

await updateParticipantMetaData({
 userId: '', // 指定用户 id
 metaData: JSON.stringify({ level: 1 }),   // 业务自定义信息
})
</script>
```

### 步骤4：管理成员身份

房主可以通过 [setAdmin](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#setAdmin) 接口变更用户角色为管理员, 通过 [revokeAdmin](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#revokeAdmin) 方法撤销用户管理员身份。
``` typescript
const { setAdmin, revokeAdmin } = useRoomParticipantState();

// 将普通用户设置为管理员
await setAdmin({ userId });

// 将管理员设置为普通用户
await revokeAdmin({ userId });
```

### 步骤5：管理成员媒体设备

房主及管理员可以通过 [closeParticipantDevice](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#closeParticipantDevice) 接口远程控制其他成员的媒体设备，以维持会议环境的安静及有序。
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { closeParticipantDevice } = useRoomParticipantState();

// 强制关闭指定成员的麦克风
async function handleCloseUserMicrophone(userId: string) {
  await closeParticipantDevice({
    userId,
    deviceType: DeviceType.Microphone,
  });
}

// 强制关闭指定成员的摄像头
async function handleCloseUserCamera(userId: string) {
  await closeParticipantDevice({
    userId,
    deviceType: DeviceType.Camera,
  });
}
```

### 步骤6：开启全局状态管理

房主及管理员可通过 [disableAllDevices](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#disableAllDevices) 及 [disableAllMessages](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#disableAllMessages) 接口开启房间全局状态管理。

#### **禁用房间内所有普通用户的媒体设备**
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { closeParticipantDevice } = useRoomParticipantState();

// 强制关闭所有普通成员的麦克风
async function handleDisableAllMicrophone() {
  await disableAllDevices({
    deviceType: DeviceType.Microphone,
    disable: true,
  });
}

// 解除全员麦克风禁用
async function handleEnableAllMicrophone() {
  await disableAllDevices({
    deviceType: DeviceType.Microphone,
    disable: false,
  });
}

// 强制关闭所有普通成员的摄像头
async function handleDisableAllCamera() {
  await disableAllDevices({
    deviceType: DeviceType.Camera,
    disable: true,
  });
}

// 解除全员摄像头禁用
async function handleEnableAllCamera() {
  await disableAllDevices({
    deviceType: DeviceType.Camera,
    disable: false,
  });
}
```

#### **禁用房间内所有普通用户的会中聊天**
``` typescript
import { useRoomParticipantState, DeviceType } from 'tuikit-atomicx-vue3/room';
const { disableAllMessages } = useRoomParticipantState();

// 强制禁用所有普通成员的聊天能力
async function handleDisableAllMessage() {
  await disableAllMessages({
    disable: true,
  });
}
```

### **步骤7：**监控成员发言状态

通过 [speakingUsers](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#speakingUsers) 可以实时获取房间内正在说话的用户及其音量大小。
``` typescript
const { speakingUsers } = useRoomParticipantState();

// 判断用户是否正在说话
const isSpeaking = (userId: string) => speakingUsers.value.has(userId);
// 获取用户实时音量 (0-100)
const getVolume = (userId: string) => speakingUsers.value.get(userId) || 0;
```

### 步骤8：踢出房间内成员

房主及管理员可以通过 [kickParticipant](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#kickParticipant) 接口将用户移出房间。
``` typescript
const { kickParticipant } = useRoomParticipantState();

// 将用户踢出房间
await kickParticipant({ userId });
```

### 步骤9：邀请其他人加入房间

实际业务中，您可能需要向未进房的用户发起呼叫邀请。Atomicx 提供了完整的呼叫系统支持，请查看 会中呼叫。

## 开发建议

**权限校验：**在 UI 层建议通过 `participant.role` 判断当前用户的操作权限，对不具备管理权限的用户隐藏**踢人**或**禁言**按钮。
``` typescript
<script setup>
import { RoomParticipantRole } from 'tuikit-atomicx-vue3/room';

const { localParticipant } = useRoomParticipantState();

// 判断当前用户是否有管理权限
const hasAdminPermission = computed(() => {
  return localParticipant.value.role === RoomParticipantRole.Owner ||
         localParticipant.value.role === RoomParticipantRole.Admin;
});
</script>

// 在模板中使用
<template>
<button v-if="hasAdminPermission" @click="handleKick">踢出</button>
</template>
```
