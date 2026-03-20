> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本篇文档将详细指导您如何使用 Atomicx 的 [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 管理房间的完整生命周期，包括创建、加入、离开以及结束房间，以及处理关键的房间状态通知。以下时序图演示了房间从创建到销毁的主要流程。

> **Android 差异：** `RoomStore`  专职负责  `AtomicXCore` 中的房间事务管理，提供从房间创建到解散的完整流程控制接口。通过 `RoomStore` 提供的接口（例如获取及更新房间信息），可高效开发出一套成熟的房间管理体系
> **iOS 差异：** `RoomStore`  专职负责  `AtomicXCore`  中的房间事务管理，提供从房间创建到解散的完整流程控制接口。通过 `RoomStore` 提供的接口（如获取及更新房间信息），可高效开发出一套成熟的房间管理体系
> **Flutter 差异：** `RoomStore` 专职负责 `AtomicXCore` 中的房间事务管理，提供从房间创建到解散的完整流程控制接口。通过 `RoomStore` 提供的接口（例如获取及更新房间信息），可高效开发出一套成熟的房间管理体系

## 核心功能

[useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 是 Atomicx 方案中负责房间管理的核心 Hook。它封装了复杂的底层信令流程，开发者可以通过简单的响应式数据和方法调用来驱动房间状态。
- **创建与加入：**通过 [createAndJoinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#createAndJoinRoom) 方法创建并加入房间，或通过 [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) 方法加入已有房间。
- **状态实时同步：**`useRoomState`Hook 内部维护 [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) 响应式数据，自动同步房间名称、模式及成员数等信息。
- **权限分明的退出机制：**针对不同角色提供了清晰的操作边界。普通成员可以使用 [leaveRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#leaveRoom) 退出；而房主（主持人）则拥有 [endRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#endRoom) 权限，可以一键结束房间并销毁房间资源。

> **Android 差异：** - **创建并加入房间**：房主通过调用该接口可以直接创建并进入房间。- **加入房间**：参与者通过调用该接口可以直接进入已存在的房间。- **离开房间**：参与者通过调用该接口可以退出已进入的房间。- **结束房间**：房主通过调用该接口可以结束当前房间，如果房间内还有其他参与者，此时他们会收到 `onRoomEnded` 事件。- **更新房间信息**：房主或管理员通过调用该接口可以更新房间信息，目前支持更新房间名称，房间密码。- **获取房间信息**：通过调用该接口可以获取房间的详细信息
> **iOS 差异：** - **创建并加入房间**：房主通过调用该接口可以直接创建并进入房间。- **加入房间**：参与者通过调用该接口可以直接进入已存在的房间。- **离开房间**：参与者通过调用该接口可以退出已进入的房间。- **结束房间**：房主通过调用该接口可以结束当前房间，如果房间内还有其他参与者，此时他们会收到 `onRoomEnded` 事件。- **更新房间信息**：房主或管理员通过调用该接口可以更新房间信息，目前支持更新房间名称，房间密码。- **获取房间信息**：通过调用该接口可以获取房间的详细信息
> **Flutter 差异：** - **创建并加入房间**：房主通过调用该接口可以直接创建并进入房间。- **加入房间**：参与者通过调用该接口可以直接进入已存在的房间。- **离开房间**：参与者通过调用该接口可以退出已进入的房间。- **结束房间**：房主通过调用该接口可以结束当前房间，如果房间内还有其他参与者，此时参与者会收到`onRoomEnded`事件。- **更新房间信息**：房主或管理员通过调用该接口可以更新房间信息，目前支持更新房间名称，房间密码。- **获取房间信息**：通过调用该接口可以获取房间的详细信息

## 核心概念

在开始集成之前，需要通过下表了解一下 `RoomStore` 相关的几个核心概念：
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| RoomInfo | `data class` | 代表房间信息的核心数据模型，封装了房间的完整信息和状态管理能力。 核心功能包括： - 房间基本信息管理（房间 ID、名称、创建者）。 - 实时状态跟踪（参与者数量、房间状态、创建时间）。 - 房间调度功能（预约时间、参会者列表、提醒设置）。 - 权限控制（密码保护、设备管控、消息管理）。 |
| RoomState | `data class` | 代表房间状态管理的核心数据结构，负责维护用户的房间相关状态信息。 核心属性： - `scheduledRoomList`存储了当前账号下所有预约的房间列表信息。 - `scheduledRoomListCursor` 则代表预约房间列表的分页快照。 - `currentRoom`则代表当前所在房间的状态信息。 |
| RoomListener | `abstract class` | 代表房间动态的实时事件。主要事件分为三大类：预约房间事件，房间结束事件，房间呼叫事件。 |
| RoomStore | `abstract class` | 这是与房间全生命周期相关的核心类。功能包含：获取预约房间列表快照、执行房间管理操作，并通过订阅其 `RoomListener` 来接收实时动态事件。 |

## 实现步骤

### 步骤1：组件集成

请参考 接入概览 集成 **AtomicXCore SDK **，并已完成 实现登录逻辑 部分接入。

### 步骤2：**创建并加入房间**

##### 实现方式：
1. **配置房间初始化参数：**初始化`CreateRoomOptions`来设置房间名称，房间密码。
2. **房间权限预设：**设置房间内全场成员权限，例如全体静音，全体禁画等。
3. **创建并加入房间：**调用`RoomStore`的`createAndJoinRoom`接口执行核心操作。
##### 示例代码：
##### createAndJoinRoom 接口参数详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomID` | `String` | 是 | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| `roomType` | `RoomType` | 是 | 房间类型。 - standard：标准房间，所有成员可自由音视频互动。 - webinar：大型研讨会房间，区分嘉宾和观众角色，观众默认无上麦权限。 |
| `options` | `CreateRoomOptions` | 是 | - 创建房间配置对象。 - 详细用法请参考：CreateRoomOptions 结构体详细说明。 |
| `completion` | `CompletionClosure` | 否 | 操作完成回调，用于返回创建和加入房间的结果。若创建失败则会返回错误码和错误信息。 |
##### CreateRoomOptions 结构体详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomName` | `String` | 否 | - 房间名称，可以不设置，默认为空字符串。 - 限制长度为 0-60 字节。 - 支持中英文、数字、特殊字符。 |
| `password` | `String` | 否 | - 房间密码，空字符串 "" 通常表示该房间不设密码。 - 限制长度为 0-32 字节。 - 推荐使用 4-8 位纯数字，方便移动端输入。设置后，其他用户加入房间时需输入密码。建议不要存储明文敏感信息。 |
| `isAllMicrophoneDisabled` | `Bool` | 否 | 是否全员禁止打开麦克风。开启后，除房主/管理员外，普通参与者默认禁止打开麦克风。 - true：禁止。 - false：取消禁止 （默认值）。 |
| `isAllCameraDisabled` | `Bool` | 否 | 是否全员禁止打开摄像头。开启后，除房主/管理员外，普通参与者默认禁止打开摄像头。 - true：禁止。 - false：取消禁止（默认值）。 |
| `isAllScreenShareDisabled` | `Bool` | 否 | 是否全员禁止发起屏幕共享。开启后，仅房主/管理员可进行屏幕共享。 - true：禁止。 - false：取消禁止（默认值）。 |
| `isAllMessageDisabled` | `Bool` | 否 | 是否全员禁止发送聊天消息（禁言）。开启后，普通参与者无法在房间内发送文字消息。 - true：禁止。 - false：取消禁止（默认值）。 |

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.CreateRoomOptions
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomType

private val roomID = "test_room_001"
private val roomType = RoomType.STANDARD

fun createAndJoinRoom() {
    // 1. 配置房间创建参数
    // CreateRoomOptions 定义房间的基础属性和初始权限设置
    val options = CreateRoomOptions(
        roomName = "团队会议室",           // 房间显示名称
        password = "",                     // 房间密码（可选）
        // 2. 设置房间初始权限控制
        isAllCameraDisabled = false,       // 是否禁用所有成员摄像头
        isAllMessageDisabled = false,      // 是否禁言所有成员
        isAllMicrophoneDisabled = false,   // 是否静音所有成员
        isAllScreenShareDisabled = false   // 是否禁止所有成员屏幕分享
    )

    // 3. 调用 RoomStore 创建并加入房间接口
    // 该方法会自动处理房间创建（如果不存在）和加入逻辑
    RoomStore.shared().createAndJoinRoom(roomID, roomType, options, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "成功创建并加入房间")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "创建并加入房间失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func createAndJoinRoom() { | - | Closure + Result |
| Flutter | void createAndJoinRoom() async { | - | Future<Result> |

### 步骤3：**加入房间**

参与者需要加入房间时，调用 `RoomStore` 的 `joinRoom` 接口，即可加入房间，此时房间内其他参与者会收到参与者加入房间事件通知。
##### joinRoom 接口参数详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomID` | `String` | 是 | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| `roomType` | `RoomType` | 是 | 房间类型 - standard：标准房间，所有成员可自由音视频互动。 - webinar：大型研讨会房间，区分嘉宾和观众角色，观众默认无上麦权限。 |
| `password` | `String` | 是 | - 房间密码，空字符串 "" 通常表示该房间不设密码。 - 限制长度为 0-32 字节。 - 推荐使用 4-8 位纯数字，方便移动端输入。设置后，其他用户加入房间时需输入密码。建议不要存储明文敏感信息。 |
| `completion` | `CompletionClosure` | 否 | 操作完成回调，用于返回加入房间的结果。若创建失败则会返回错误码和错误信息。 |

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomType

private val roomID = "test_room_001"
private val roomType = RoomType.STANDARD

fun joinRoom() {
    // 1. 准备加入房间参数
    // joinRoom 适用于加入一个已知且正在进行的房间
    val roomPassword = "" // 房间密码，若房间未设置密码则传空字符串或 null

    // 2. 调用 RoomStore 加入房间接口
    // 该操作会验证房间是否存在、用户是否被禁入以及密码是否正确
    RoomStore.shared().joinRoom(roomID, roomType, roomPassword, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "加入房间成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "加入房间失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func joinRoom() { | - | Closure + Result |
| Flutter | Future<void> joinRoom() async { | - | Future<Result> |

### **步骤4：离开房间**

房主及普通用户均可调用 [leaveRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#leaveRoom) 接口离开房间，使用该接口的前提是用户已经通过 [createAndJoinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#createAndJoinRoom) 或者 [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) 接口加入房间。

> **Android 差异：** 当参与者要离开房间时，通过调用 `RoomStore` 的 `leaveRoom` 接口即可离开房间，此时房间内其他参与者都会收到参与者离开房间事件通知
> **iOS 差异：** 当参与者要离开房间时，通过调用 `RoomStore` 的 `leaveRoom` 接口即可离开房间，此时房间内其他参与者都会收到参与者离开房间事件通知
> **Flutter 差异：** 当参与者要离开房间时，通过调用`RoomStore`的`leaveRoom`接口即可离开房间，此时房间内其他参与者都会收到参与者离开房间事件通知（onParticipantLeft）

**代码示例：**

**Vue（主示例）：**
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';
const { leaveRoom } = useRoomState();

await leaveRoom();
```

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

fun leaveRoom() {
    // 1. 业务逻辑说明
    // leaveRoom 接口用于普通成员或房主主动退出房间
    // 与 endRoom 不同，房主调用 leaveRoom 只会让自己离开，房间依然存在

    // 2. 调用 RoomStore 离开房间接口
    // 该操作会停止音视频流传输，并通知服务器将当前用户移出房间
    RoomStore.shared().leaveRoom(object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "离开房间成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "离开房间失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func leaveRoom() { | - | Closure + Result |
| Flutter | Future<void> leaveRoom() async { | - | Future<Result> |

### 步骤5：**结束房间**

当房主期望结束房间时，调用`RoomStore`的`endRoom`接口即可结束当前房间。结束房间后，房间内的所有参与者都会收到房间结束事件（onRoomEnded）。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

fun endRoom() {
    // 1. 业务逻辑说明
    // endRoom 接口用于永久解散当前房间
    // 注意：该操作通常仅限房主（Owner）执行，解散后所有成员将被强制退出

    // 2. 调用 RoomStore 解散房间接口
    // 该方法会向服务器发送解散指令，并通知所有在线参与者房间已结束
    RoomStore.shared().endRoom(object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "解散房间成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "解散房间失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func endRoom() { | - | Closure + Result |
| Flutter | Future<void> endRoom() async { | - | Future<Result> |

### **步骤6：更新房间信息**

当房主期望更新房间名称或者更改房间密码时，调用`RoomStore`的`updateRoomInfo`接口即可更新房间信息。更新后房间内的参与者会收到房间状态变更通知。
##### updateRoomInfo 接口参数详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomID` | `String` | 是 | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| `options` | `UpdateRoomOptions` | 是 | - 更新房间属性配置对象。 - 详细用法请参考：UpdateRoomOptions 结构体详细说明 |
| `modifyFlagList` | `List<UpdateRoomOptions.ModifyFlag>` | 是 | - 更新房间属性修改标志位。目前支持更新**房间名称**、**房间密码**。 - 详细用法请参考：UpdateRoomOptions.ModifyFlag 详细说明。 |
| `completion` | `CompletionHandler` | 否 | 操作完成回调，用于返回更新房间信息的结果。若更新失败则会返回错误码和错误信息。 |
**UpdateRoomOptions 结构体详细说明**
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomName` | `String` | 否 | - 房间名称，可以不设置，默认为空字符串。 - 限制长度为 0-60 字节。 - 支持中英文、数字、特殊字符。 |
| `password` | `String` | 否 | - 房间密码，空字符串 "" 通常表示该房间不设密码。 - 限制长度为 0-32 字节。 - 推荐使用 4-8 位纯数字，方便移动端输入。设置后，其他用户加入房间时需输入密码。建议不要存储明文敏感信息。 |
**UpdateRoomOptions.ModifyFlag 详细说明**
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `ROOM_NAME` | `enum` | 否 | - 修改房间名称时的标识位。 - 当修改 `UpdateRoomOptions` 中的 `roomName` 后，需要同步设置 `ModifyFlag` 的 `ROOM_NAME`标识位。 |
| `PASSWORD` | `enum` | 否 | - 修改房间密码时的标识位。 - 当修改 `UpdateRoomOptions` 中的 `password` 后，需要同步设置 `ModifyFlag` 的 `PASSWORD` 标识位。 |

> **Vue 差异：** 房主可以在创建房间后使用 [updateRoomInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateRoomInfo) 更新房间名称和密码。
> **注意**
> **说明：**
> 房主必须在房间内才能调用 [updateRoomInfo](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateRoomInfo)。如果房主已离开房间，需要重新加入后才能更新房间信息。

**代码示例：**

**Vue（主示例）：**
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';
const { updateRoomInfo } = useRoomState();

await updateRoomInfo({
  roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
  options: {
     roomName: '新的房间名称',
     password: '234567',
  },
})
```

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.UpdateRoomOptions
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

private val roomID = "test_room_001"

fun updateRoomInfo() {
    // 1. 配置房间更新参数
    // UpdateRoomOptions 用于定义需要更新的房间属性
    val options = UpdateRoomOptions(
        roomName = "新的会议室名称"     // 更新房间显示名称
    )

    // 2. 设置修改标志位
    // modifyFlagList 用于指定具体要更新哪些字段，支持组合多个标志
    val modifyFlagList = listOf(UpdateRoomOptions.ModifyFlag.ROOM_NAME)

    // 3. 调用 RoomStore 更新房间信息接口
    // 该方法会向服务器提交更新请求，并同步给所有房间成员
    RoomStore.shared().updateRoomInfo(roomID, options, modifyFlagList, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "更新房间信息成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "更新房间信息失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func updateRoomInfo() { | - | Closure + Result |
| Flutter | Future<void> updateRoomInfo() async { | - | Future<Result> |

### 步骤7：获取房间信息

进入房间成功后调用 `RoomStore` 的 `getRoomInfo` 接口即可获取到房间详细信息。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.GetRoomInfoCompletionHandler

private val roomID = "test_room_001"

fun getRoomInfo() {
    // 1. 业务逻辑说明
    // getRoomInfo 接口用于获取指定房间的详细信息
    // 可以获取房间名称、创建者、参与者数量、权限设置等完整信息

    // 2. 调用 RoomStore 获取房间信息接口
    // 该操作会从服务器获取最新的房间状态和配置信息
    RoomStore.shared().getRoomInfo(roomID, object : GetRoomInfoCompletionHandler {
        override fun onSuccess(roomInfo: RoomInfo) {
            Log.d("Room", "获取房间信息成功，roomInfo: $roomInfo")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.e("Room", "获取房间信息失败 [错误码: $code]: $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func getRoomInfo() { | - | Closure + Result |
| Flutter | Future<void> getRoomInfo() async { | - | Future<Result> |

### 步骤8：监听房间实时事件及状态变化

订阅`RoomEvent`房间内的被动事件。以订阅房间结束事件为例，示例代码如下：
订阅`RoomState`房间属性状态变化。以订阅当前所在房间信息变化为例，示例代码如下：

**代码示例：**

**Android（主示例）：**
``` java
import android.content.Context
import android.util.AttributeSet
import android.util.Log
import android.widget.FrameLayout
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomListener
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

class RoomMainView @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : FrameLayout(context, attrs, defStyleAttr) {

    // 房间事件监听器
    private val roomListener = object : RoomListener() {
        override fun onRoomEnded(roomInfo: RoomInfo) {
            Log.d("Room", "当前所在房间已结束")
            // 建议操作：关闭当前页面，返回上一级
        }
    }

    override fun onAttachedToWindow() {
        super.onAttachedToWindow()
        // 添加房间事件监听器
        RoomStore.shared().addRoomListener(roomListener)
    }

    override fun onDetachedFromWindow() {
        super.onDetachedFromWindow()
        // 移除监听器，防止内存泄漏
        RoomStore.shared().removeRoomListener(roomListener)
    }
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// 订阅当前所在房间信息变化
private fun subscribeCurrentRoomState() {
    CoroutineScope(Dispatchers.Main).launch {
        RoomStore.shared().state.currentRoom.collect { roomInfo ->
            Log.d("Room", "房间信息更新 新的房间信息: $roomInfo")
        }
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | private func subscribeRoomEvent() { | case .onRoomEnded(let roomInfo): | - |
| Flutter | void initState() { | onRoomEnded: (roomInfo) { | - |

## API 文档

**能力索引**
| 能力 | 核心能力 | 适用平台 |
| --- | --- | --- |
| RoomStore | 房间全生命周期管理：创建并加入 / 加入 / 离开 / 结束房间 / 更新、获取房间信息 / 房间预约 / 呼叫房间外成员 / 监听房间内被动事件（例如房间解散，房间信息更新等）。 | Android, iOS, Flutter |

**参数 / 返回 / 限制**
- 参数：以各平台 API 文档为准（命名不同，语义一致）。
- 返回：异步模型存在差异（Callback / Closure / Future / Promise）。
- 限制：需先完成登录并进入房间；管理类能力通常要求房主或管理员权限。

**主示例（Android）：**
```kotlin
val store = RoomParticipantStore.create("your_room_id")
store.getParticipantList(cursor = null, completion = null)
```

**多平台差异（字段级）**
| 字段 | Android | iOS | Flutter | Vue |
| --- | --- | --- | --- | --- |
| API 入口 | RoomStore | RoomStore | RoomStore | - |
| 异步模型 | CompletionHandler | CompletionClosure | Future<Result> | Promise |
| 事件机制 | addRoomParticipantListener | participantEventPublisher | addRoomParticipantListener | subscribeEvent |
| 文档入口 | SDK API 文档 | SDK API 文档 | SDK API 文档 | Web API 文档 |

## 常见问题

### `RoomInfo` 中的参与人数（`participantCount`）是如何更新的？时机和频率是怎样的？

`participantCount` 的更新并非总是实时的，其机制如下：
- **主动进出房间**：当用户主动加入或正常退出房间时，参与人数的变更通知会即时触发。您会很快在 `RoomState` 中的`currentRoom`观察到 `participantCount` 的变化。
- **异常掉线**：当用户因网络问题、应用崩溃等原因异常掉线时，系统需要通过心跳机制来判断其真实状态。只有当该用户连续 90 秒没有心跳后，系统才会判定其为离线，并触发人数变更通知。
- **更新机制与频率控制：**
  - 无论是即时触发还是延迟触发，所有的人数变更通知都是以消息的形式在房间内广播的。
  - 房间内每秒的消息总量有上限，单房间消息频控是**每秒 40 条消息**。
  - **关键点：**在“弹幕风暴”消息流量极高的场景下，如果房间内的消息速率超过了 40条/秒 的阈值，为了保证核心消息（例如弹幕）的送达，**人数变更的消息可能会被频控策略丢弃**。
      **这对开发者意味着什么？**
      `participantCount` 是一个非常接近实时的**高精度估算值**，但在极端高并发场景下，它可能存在短暂的延迟或数据丢失。因此，我们建议您将其用于 **UI 展示**，而不应作为计费、统计等需要绝对精准场景的唯一依据。

## **前提条件**

用户已经通过 [useLoginState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-LoginState) 完成登录鉴权，请参考 接入概览。

### 步骤1：导入 useRoomState

``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { 
  createAndJoinRoom, 
  joinRoom, 
  leaveRoom, 
  endRoom,
  currentRoom 
} = useRoomState();
```

### 步骤2：创建房间

创建房间（房主）：使用 [createAndJoinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#createAndJoinRoom)，调用成功后该用户将自动获得房主权限。

> **注意：**
> 创建房间时指定的 roomId 建议由业务后台生成并下发，以确保房间 Id 在全局应用中的唯一性与稳定性。
- **示例一：用户创建房间**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { createAndJoinRoom } = useRoomState();
   
   await createAndJoinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
   })
   ```
- **示例二：用户创建房间并指定房间名称**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { createAndJoinRoom } = useRoomState();
   
   await createAndJoinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
     options: {
       roomName: '我的临时房间'
     }
   })
   ```
- **示例三：用户创建密码房间**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { createAndJoinRoom } = useRoomState();
   
   await createAndJoinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
     options: {
       roomName: '我的临时房间',
       password: '123456',
     }
   })
   ```   

   > **说明：**
   > 

   > 创建者在调用 [createAndJoinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#createAndJoinRoom) 时会自动加入房间，无需输入密码。
   > 

- **示例四：用户创建房间并配置成员管理信息**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { createAndJoinRoom } = useRoomState();
   
   await createAndJoinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
     options: {
       roomName: '我的临时房间',
       password: '123456',
       isAllMicrophoneDisabled: true,   // 开启全员静音
       isAllCameraDisabled: true,       // 开启全员静画
       isAllScreenShareDisabled: true,  // 开启全员禁止屏幕共享
       isAllMessageDisabled: true,      // 开启全员禁止发会中消息
     }
   })
   ```   

   > **说明：**
   > 

   > 成员管理配置信息仅对普通成员生效，房主和管理员不受限制。如需在房间创建后修改成员管理配置或单独管理成员权限，请参考 成员管理。
   >

### 步骤3：用户加入已有房间

登录用户可以调用 [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) 加入已有房间。Atomicx 仅支持用户同时加入一个房间。
- **示例一：用户加入房间**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { joinRoom } = useRoomState();
   
   await joinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
   })
   ```
- **示例二：用户加入密码房间**

   ``` typescript
   import { useRoomState } from 'tuikit-atomicx-vue3/room';
   const { joinRoom } = useRoomState();
   
   await joinRoom({
     roomId: 'YOUR_ROOM_ID',    // 指定房间 Id，由业务后台生成
     password: '123456',
   })
   ```

### 步骤6：销毁房间

仅房主可调用 [endRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#endRoom) 接口销毁房间，使用该接口的前提是用户已经通过 [createAndJoinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#createAndJoinRoom) 创建房间或者已经获取房主身份。
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';
const { endRoom } = useRoomState();

await endRoom();
```

> **说明：**
> - 如果房主离开房间但未调用 [endRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#endRoom)，房间将继续存在，直到满足自动回收条件（连续 6 小时无用户）。
> - 房主可以通过 [transferOwner](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#transferOwner) 接口将房主身份转移给其他用户。

### 步骤7：异常退出处理

#### 处理房间被销毁事件

普通用户可通过监听 `RoomEvent.onRoomEnded` 事件处理房间被销毁的业务交互。
``` typescript
import { useRoomState, RoomEvent } from 'tuikit-atomicx-vue3/room';
import { TUIMessageBox } from '@tencentcloud/uikit-base-component-vue3';
const { subscribeEvent, unsubscribeEvent } = useRoomState();

function onRoomEnded() {
  TUIMessageBox.alert({
    title: '通知',
    content: '房主已解散房间',
  });
}

// 在组件加载时监听 RoomEvent
subscribeEvent(RoomEvent.onRoomEnded, onRoomEnded);
// 在组件销毁时取消监听 RoomEvent
unsubscribeEvent(RoomEvent.onRoomEnded, onRoomEnded);
```

#### 处理用户被踢退房事件

普通用户可通过监听 `RoomParticipantEvent.onKickedFromRoom` 事件处理用户被踢出房间的业务交互。
``` typescript
import { useRoomParticipantState, RoomParticipantEvent, KickedOutOfRoomReason } from 'tuikit-atomicx-vue3/room';
import { TUIMessageBox } from '@tencentcloud/uikit-base-component-vue3';

const { subscribeEvent, unsubscribeEvent } = useRoomParticipantState();

function onKickedFromRoom({ reason }: { reason: KickedOutOfRoomReason; message: string }) {
    let notice = '';
    switch (reason) {
      case KickedOutOfRoomReason.KickedByAdmin:
        notice = '您已被房主移出房间';
        break;
      case KickedOutOfRoomReason.ReplacedByAnotherDevice:
        notice = '您的账号已在其他设备登录';
        break;
      case KickedOutOfRoomReason.KickedByServer:
        notice = '您已被服务器移出房间';
        break;
      case KickedOutOfRoomReason.ConnectionTimeout:
        notice = '网络连接超时，正在退出房间';
        break;
      case KickedOutOfRoomReason.InvalidStatusOnReconnect:
        notice = '房间已解散或您在离线期间已被移出';
        break;
      case KickedOutOfRoomReason.RoomLimitExceeded:
        notice = '房间人数已达上限，无法加入';
        break;
      default:
        notice = '您已被移出房间';
        break;
    }
    TUIMessageBox.alert({
      title: '通知',
      content: notice,
    });
  }

// 在组件加载时监听 RoomParticipantEvent
subscribeEvent(RoomParticipantEvent.onKickedFromRoom, onKickedFromRoom);
// 在组件销毁时取消监听 RoomParticipantEvent
unsubscribeEvent(RoomParticipantEvent.onKickedFromRoom, onKickedFromRoom);
```

## 进阶实现

当前文档介绍的是快速创建并立即加入一个房间，适用于即时多人音视频互通场景。

但在现实商业场景中，我们往往需要提前规划：设置房间的主题、预定特定的开始与结束时间、并邀请参与者。参与者可以在多人音视频房间开始前收到提醒，并准时接入。

Atomicx 提供了完整的预约房间能力，包括：
- **预定房间：**提前设定多人房间时间与参与人名单。

- **预约列表**：查看并管理自己名下的所有待开始的多人房间。

- **入会提醒：**支持开始前的自动提醒逻辑。

   如果您需要实现上述更复杂的商业多人音视频房间管理逻辑，请参考：预定房间。

### 房间内已经没有用户但房间未被解散，房间是否会一直存在？

房间内连续 6 小时无用户进入时，后台将尝试回收房间。

### 房间内已经没有用户但房间未被解散，是否会产生费用消耗？

没有用户但是未被解散的房间仅占用套餐群组数，不产生分钟数消耗和额外费用消耗。

### 一个用户可以同时加入多个房间吗？

Atomicx 提供的 `useRoomState` 为单例接口，仅支持用户同时加入一个房间。若您需要支持一个用户同时加入多个房间，欢迎 [联系我们](https://cloud.tencent.com/document/product/647/19906) 提交反馈。

### 最多支持多少人同时加入房间？

房间内用户数量上限和您的套餐包相关，具体请查看 [TUIRoomKit 套餐包说明](https://cloud.tencent.com/document/product/647/104842#function)。
