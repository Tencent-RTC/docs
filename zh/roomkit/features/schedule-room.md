> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

`RoomStore` 是 `AtomicXCore` 的房间管理核心模块，涵盖了预定房间体系。本章节详细介绍了如何利用 `RoomStore` 高效完成预定房间功能的开发与接入。

> **Vue 差异：** 预定房间功能允许用户提前规划会议时间、主题和入会密码。通过该功能，用户可以创建未来的会议计划，生成专属的会议号和入会链接，方便提前通知参会人员

## 核心功能

- **预定、取消预定房间**：任何用户均可完成预定房间，取消预定房间功能。
- **更新预定房间信息：**对已预定的房间进行房间信息更新，包含：房间名称，开始时间，结束时间。
- **获取预定房间列表**：获取当前账户下所有预定的房间列表信息。
- **获取预定房间的参与成员列表**：获取指定预定房间的参与成员列表信息。
- **添加、移除预定房间参与成员**：对已预定的房间可以添加、移除参与成员。

## 核心概念

在开始集成之前，可通过下表了解 `RoomStore` 的核心概念：
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| RoomStatus | `enum class` | 代表当前房间的状态，包含： - `SCHEDULED`（预定状态）。 - `RUNNING`（进行中）。 |
| ScheduleRoomOptions | `data class` | 代表预定房间的核心数据结构，包含：开始时间，结束时间，预定成员列表等。 |
| RoomState | `data class` | 代表房间状态管理的核心数据结构，负责维护用户的房间相关状态信息。 核心属性： - `scheduledRoomList`存储了当前账号下所有预定的房间列表信息。 - `scheduledRoomListCursor` 则代表预定房间列表的分页游标。 |
| RoomListener | `abstract class` | 代表房间动态的实时事件。其中包含预定房间相关的事件。 |
| RoomStore | `abstract class` | 这是与房间全生命周期相关的核心类。通过它，您可以主动发起预定房间，获取预定房间列表等功能，并通过订阅其 `RoomListener` 来接收与预定房间相关的实时动态事件。 |

## 实现步骤

### 步骤1：组件集成

请参考 接入概览 集成 **AtomicXCore SDK**，并已完成 实现登录逻辑 部分接入。

### 步骤2：预定房间功能

#### **一、预定房间**
##### 实现方式：
1. **配置预定房间初始化参数：**初始化 `ScheduleRoomOptions` 预定房间配置项设置房间名称，房间密码、开始时间、结束时间、参与者、房间信息等。
2. **房间权限预设：**设置房间内全场成员权限，例如全体静音，全体禁画等。
3. **预定房间：**调用 `RoomStore` 的 `scheduleRoom` 接口执行核心操作。
##### 示例代码：
##### scheduleRoom 接口参数详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `roomID` | `String` | 是 | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| `options` | `ScheduleRoomOptions` | 是 | - 创建房间配置对象。 - 详细用法请参考：ScheduleRoomOptions 结构体详细说明 |
##### ScheduleRoomOptions 结构体详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| roomName | String | 否 | - 房间名称，可以不设置，默认为空字符串。 - 限制长度为 0-60 字节。 - 支持中英文、数字、特殊字符。 |
| password | String | 否 | - 房间密码，空字符串 `""` 通常表示该房间不设密码。 - 限制长度为 0-32 字节。 - 推荐使用 4-8 位纯数字，方便移动端输入。设置后，其他用户加入房间时需输入密码。建议不要存储明文敏感信息。 |
| scheduleStartTime | int | 是 | - 预定的房间开始时间（Unix 时间戳，单位：秒）。 - 取值必须大于当前时间。 - 推荐设置为未来的整分时间。例如：1736164800（代表 2025-01-06 20:00:00）。 |
| scheduleEndTime | int | 是 | - 预定的房间结束时间（Unix 时间戳，单位：秒）。 - 取值必须大于 scheduleStartTime。 - 建议根据预期时长设置。例如：scheduleStartTime + 3600（预定 1 小时时长的房间）。 |
| reminderSecondsBeforeStart | int | 否 | - 房间开始前的提醒时间（单位：秒）。用于系统触发开始提醒。 - 推荐设置为 300（5分钟）或 900（15分钟），用于在 App 端触发本地通知。 |
| scheduleAttendees | List<String> | 否 | - 预定参与者的用户 ID 列表，在预定房间时设置，系统将向这些用户发送预定邀请。 - 获取方式请参考 获取指定预定房间的参与成员列表。 |
| isAllMicrophoneDisabled | bool | 否 | - 是否全员禁止打开麦克风。开启后，除房主/管理员外，普通参与者默认禁止打开麦克风。 - 参数含义：   - true : 禁止。   - false ：取消禁止 （默认值）。 |
| isAllCameraDisabled | bool | 否 | - 是否全员禁止打开摄像头。开启后，除房主/管理员外，普通参与者默认禁止打开摄像头。 - 参数含义：   - true : 禁止。   - false ：取消禁止（默认值）。 |
| isAllScreenShareDisabled | bool | 否 | - 是否全员禁止发起屏幕共享。开启后，仅房主/管理员可进行屏幕共享。 - 参数含义：   - true : 禁止。   - false ：取消禁止（默认值）。 |
| isAllMessageDisabled | bool | 否 | - 是否全员禁止发送聊天消息（禁言）。开启后，普通参与者无法在房间内发送文字消息。 - 参数含义：   - true : 禁止。   - false ：取消禁止（默认值）。 |
#### **二、取消房间预定**
- **预定房间者：**调用`RoomStore`的`cancelScheduledRoom`接口，实现取消预定房间功能。
   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   Future<void> cancelScheduledRoom(String roomID) async {
     final result = await RoomStore.shared.cancelScheduledRoom(roomID);
     if (result.isSuccess) {
       print("取消预定房间成功");
     } else {
       print("取消预定房间失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   ```
#### **三、更新预定房间**
- **房间预定者：**调用`RoomStore`的`updateScheduledRoom`接口，实现更新预定**房间名称**、**开始时间**、**结束时间**。
   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   Future<void> updateScheduledRoom() async {
     final newStartTime = DateTime.now().millisecondsSinceEpoch ~/ 1000 + 60 * 30; // 计算预定时间：当前时间 + 30分钟后开始
     final newEndTime = newStartTime + 60 * 45; // 计算结束时间：开始时间 + 45分钟（房间持续45分钟）
     final options = ScheduleRoomOptions(
       roomName: "房间名称",
       scheduleStartTime: newStartTime,
       scheduleEndTime: newEndTime,
     );
     final modifyFlagList = [
       ScheduleRoomOptionsModifyFlag.roomName,
       ScheduleRoomOptionsModifyFlag.scheduleStartTime,
       ScheduleRoomOptionsModifyFlag.scheduleEndTime,
     ];
     final result = await RoomStore.shared.updateScheduledRoom(
       roomID: "roomID",
       options: options,
       modifyFlagList: modifyFlagList,
     );
     if (result.isSuccess) {
       print("更新预定房间成功");
     } else {
       print("更新预定房间失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   ```
##### updateScheduledRoom 接口参数详细说明
| 参数名 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| roomID | String | 是 | - 字符串类型的房间唯一标识符。 - 限制长度为 0-48 字节。 - 建议仅包含数字、英文字母（区分大小写）、下划线（_）和连字符（-）。避免使用空格和中文字符。 |
| options | ScheduleRoomOptions | 是 | - 预定房间配置对象。 - 详细用法请参考：ScheduleRoomOptions 结构体详细说明 |
| modifyFlagList | List<ScheduleRoomOptionsModifyFlag> | 是 | - 更新房间属性修改标志位。目前支持更新**房间名称**、**开始时间**、**结束时间。** - 详细用法请参考：ScheduleRoomOptionsModifyFlag 详细说明。 |
**ScheduleRoomOptionsModifyFlag 详细说明**
| 参数名 | 说明 |
| --- | --- |
| `roomName` | - 修改预定房间名称时的标识位。 - 当修改`ScheduleRoomOptions`中的`roomName`后，需要同步向`modifyFlagList`中添加`roomName`标识位。 |
| `scheduleStartTime` | - 修改预定房间开始时间时的标识位。 - 当修改`ScheduleRoomOptions`中的`scheduleStartTime`后，需要同步向`modifyFlagList`中添加`scheduleStartTime`标识位。 |
| `scheduleEndTime` | - 修改预定房间结束时间时的标识位。 - 当修改`ScheduleRoomOptions`中的`scheduleEndTime`后，需要同步向`modifyFlagList`中添加`scheduleEndTime`标识位。 |
#### **四、添加、删除参与成员**
- **预定房间者：**调用 `RoomStore` 的 `addScheduledAttendees` 或者 `removeScheduledAttendees` 接口，可以为已预定的房间内批量添加或移除预定参与者。
   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   Future<void> addScheduledAttendees(String roomID, List<String> userIDList) async {
     final result = await RoomStore.shared.addScheduledAttendees(
       roomID: roomID,
       userIDList: userIDList,
     );
     if (result.isSuccess) {
       print("添加预定参与者成功");
     } else {
       print("添加预定参与者失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   Future<void> removeScheduledAttendees(String roomID, List<String> userIDList) async {
     final result = await RoomStore.shared.removeScheduledAttendees(
       roomID: roomID,
       userIDList: userIDList,
     );
     if (result.isSuccess) {
       print("移除预定参与者成功");
     } else {
       print("移除预定参与者失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   ```

**代码示例：**

**Android（主示例）：**
``` java
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.ScheduleRoomOptions

// 预定房间
fun scheduleRoom() {
    val startTime = (System.currentTimeMillis() / 1000) + 60 * 15 // 计算预定时间：当前时间 + 15分钟后开始
    val endTime = startTime + 60 * 30 // 计算结束时间：开始时间 + 30分钟（房间持续30分钟）
    val reminderSeconds = 60 * 5 // 设置提醒时间：房间开始前5分钟提醒
    
    // 创建预定房间选项配置
    val options = ScheduleRoomOptions(
        roomName = "房间名称",
        scheduleStartTime = startTime,
        scheduleEndTime = endTime,
        reminderSecondsBeforeStart = reminderSeconds,
        scheduleAttendees = listOf("user01", "user02", "user03"), // 设置预定参与者列表（用户ID数组）
        password = "房间密码", // 设置房间密码（可选，空字符串表示无密码）
        isAllMicrophoneDisabled = false, // 设置是否默认禁用所有参与者的麦克风（false = 允许开启麦克风）
        isAllCameraDisabled = false, // 设置是否默认禁用所有参与者的摄像头（false = 允许开启摄像头）
        isAllScreenShareDisabled = false, // 设置是否默认禁用所有参与者的屏幕共享（false = 允许屏幕共享）
        isAllMessageDisabled = false // 设置是否默认禁用所有参与者的消息功能（false = 允许发送消息）
    )
    
    RoomStore.shared().scheduleRoom("roomID", options, object : CompletionHandler {
        override fun onSuccess() {
            println("预定房间成功")
        }
        override fun onFailure(code: Int, desc: String) {
            println("预定房间失败: [错误码: $code] $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

// 取消预定房间
fun cancelScheduledRoom(roomID: String) {
    RoomStore.shared().cancelScheduledRoom(roomID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "取消预定房间成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "取消预定房间失败: [错误码: $code] $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.ScheduleRoomOptions

// 更新预定房间
fun updateScheduledRoom() {
    val newStartTime = (System.currentTimeMillis() / 1000) + 60 * 30 // 计算预定时间：当前时间 + 30分钟后开始
    val newEndTime = newStartTime + 60 * 45 // 计算结束时间：开始时间 + 45分钟（房间持续45分钟）

    val options = ScheduleRoomOptions(
        roomName = "房间名称",
        scheduleStartTime = newStartTime,
        scheduleEndTime = newEndTime
    )

    val modifyFlagList = listOf(
        ScheduleRoomOptions.ModifyFlag.ROOM_NAME,
        ScheduleRoomOptions.ModifyFlag.SCHEDULE_START_TIME,
        ScheduleRoomOptions.ModifyFlag.SCHEDULE_END_TIME
    )

    RoomStore.shared().updateScheduledRoom("roomID", options, modifyFlagList, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "更新预定房间成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "更新预定房间失败: [错误码: $code] $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

// 添加预定参与者
fun addScheduledAttendees(roomID: String, userIDList: List<String>) {
    RoomStore.shared().addScheduledAttendees(roomID, userIDList, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room","添加预定参与者成功")
        }
        override fun onFailure(code: Int, desc: String) {
            Log.d("Room","添加预定参与者失败: [错误码: $code] $desc")
        }
    })
}

// 移除预定参与者
fun removeScheduledAttendees(roomID: String, userIDList: List<String>) {
    RoomStore.shared().removeScheduledAttendees(roomID, userIDList, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room","移除预定参与者成功")
        }
        override fun onFailure(code: Int, desc: String) {
            Log.d("Room","移除预定参与者失败: [错误码: $code] $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func scheduleRoom() { | - | Closure + Result |
| Flutter | Future<void> scheduleRoom() async { | - | Future<Result> |

### 步骤3：获取预定房间列表

- **任意用户：**调用`RoomStore`的`getScheduledRoomList`接口，均可获取到自己账号下所有预定的房间列表信息。
   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   /// 获取预定房间列表
   Future<void> getScheduledRoomList({String? cursor}) async {
     /// [cursor] 为分页游标，首次调用时传入 null，获取第一页数据， 后续调用时传入上次返回的 nextCursor 值
     final result = await RoomStore.shared.getScheduledRoomList(cursor);
     if (result.isSuccess) {
       final roomList = result.data;
       final nextCursor = result.cursor;
       print("获取预定房间列表成功");
     } else {
       print("获取预定房间列表失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   ```

> **Android 差异：** 调用 `RoomStore` 的 `getScheduledRoomList` 接口，可获取到自己账号下所有预定的房间列表信息
> **iOS 差异：** 任意用户调用`RoomStore`的`getScheduledRoomList`接口，均可获取到自己账号下所有预约的房间列表信息

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

// 获取预定房间列表
fun getScheduledRoomList(cursor: String? = null) {
    // cursor 为分页游标，首次调用时传入 null，获取第一页数据， 后续调用时传入上次返回的 nextCursor 值
    RoomStore.shared().getScheduledRoomList(cursor, object : ListResultCompletionHandler<RoomInfo> {
        override fun onSuccess(result: List<RoomInfo>, cursor: String) {
            Log.d("Room", "获取预定房间列表成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "获取预定房间列表失败: [错误码: $code] $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func getScheduledRoomList(cursor: String? = nil) { | - | Closure + Result |

### 步骤4：获取指定预定房间的参与成员列表

- **任意用户：**调用`RoomStore`的`getScheduledAttendees`接口，可获取到指定预定房间的参与成员列表信息。
   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   /// 获取预定房间的参与者列表
   /// - [roomID]: 房间ID，用于标识要查询参与者列表的预定房间
   /// - [cursor] 为分页游标，首次调用时传入 null，获取第一页数据， 后续调用时传入上次返回的 nextCursor 值
   Future<void> getScheduledAttendees(String roomID, {String? cursor}) async {
     final result = await RoomStore.shared.getScheduledAttendees(
       roomID: roomID,
       cursor: cursor,
     );
     if (result.isSuccess) {
       final attendees = result.data;
       final nextCursor = result.cursor;
       print("获取预定房间参与者列表成功");
     } else {
       print("获取预定房间参与者列表失败: [错误码: ${result.errorCode}] ${result.errorMessage}");
     }
   }
   ```

> **Android 差异：** 调用 `RoomStore` 的 `getScheduledAttendees` 接口，可获取到指定预定房间的参与成员列表信息
> **iOS 差异：** 任意用户调用`RoomStore`的`getScheduledAttendees`接口，可获取到指定预约房间的参与成员列表信息

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 获取预定房间的参与者列表
fun getScheduledAttendees(roomID: String, cursor: String? = null) {
    // cursor 为分页游标，首次调用时传入 null，获取第一页数据， 后续调用时传入上次返回的 nextCursor 值
    RoomStore.shared().getScheduledAttendees(roomID, cursor, object : ListResultCompletionHandler<RoomUser> {
        override fun onSuccess(result: List<RoomUser>, cursor: String) {
            Log.d("Room","获取预定房间参与者列表成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room","获取预定房间参与者列表失败: [错误码: $code] $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func getScheduledAttendees(roomID: String, cursor: String? = nil) { | - | Closure + Result |

### 步骤5：监听预定房间实时事件及状态变化

订阅 `RoomListener` 预定房间相关的被动事件，示例代码如下：
订阅 `RoomState` 预定房间列表状态变化，示例代码如下：

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomListener
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

/**
 * 订阅预定房间相关事件
 */
fun subscribeScheduledRoomEvents() {
    val listener = object : RoomListener() {
        override fun onAddedToScheduledRoom(roomInfo: RoomInfo) {
            Log.d("Room","已被添加到预定房间 房间信息: $roomInfo")
        }

        override fun onRemovedFromScheduledRoom(roomInfo: RoomInfo, operator: RoomUser) {
            Log.d("Room","已被移除预定房间 房间信息: $roomInfo, 移除者: $operator")
        }

        override fun onScheduledRoomCancelled(roomInfo: RoomInfo, operator: RoomUser) {
            Log.d("Room", "预定房间被取消 房间信息: $roomInfo, 取消者: $operator")
        }

        override fun onScheduledRoomStartingSoon(roomInfo: RoomInfo) {
            Log.d("Room","预定房间即将开始 房间信息: $roomInfo")
        }

        // 可以根据需要重写其他事件回调方法
    }

    RoomStore.shared().addRoomListener(listener)
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// 订阅预定房间列表状态变化
private fun subscribeParticipantState() {
    CoroutineScope(Dispatchers.Main).launch {
        RoomStore.shared().state.scheduledRoomList.collect { scheduledRoomList ->
            Log.d("Room", "预定房间列表变化 房间列表信息: $scheduledRoomList")
        }
    }
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | private func subscribeScheduledRoomEvents() { | case .onAddedToScheduledRoom(let roomInfo): | - |

## API 文档

**能力索引**
| 能力 | 核心能力 | 适用平台 |
| --- | --- | --- |
| RoomStore | 房间全生命周期管理：创建并加入 / 加入 / 离开 / 结束房间 / 更新、获取房间信息 / 房间预定 / 呼叫房间外成员 / 监听房间内被动事件（例如房间解散，房间信息更新等）。 | Android, iOS, Flutter |
| useRoomState | 房间状态管理（创建、加入、预约等） | Vue |

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
| API 入口 | RoomStore | RoomStore | RoomStore | useRoomState |
| 异步模型 | CompletionHandler | CompletionClosure | Future<Result> | Promise |
| 事件机制 | addRoomParticipantListener | participantEventPublisher | addRoomParticipantListener | subscribeEvent |
| 文档入口 | SDK API 文档 | SDK API 文档 | SDK API 文档 | Web API 文档 |

## 常见问题

1. **预定成功但列表不更新？**

   请确认已登录且首屏调用过 `getScheduledRoomList`（例如 `cursor: ''`）。返回的 `cursor` 不为空需继续拉取；列表是响应式的，更新后 UI 会同步。

2. `joinRoom`** 参数怎么传？**

   推荐统一使用对象入参：`joinRoom({ roomId, password })`，避免直接传字符串导致类型不匹配或后续扩展受限。

3. **时间戳用毫秒导致预约失败？**

   所有时间相关参数必须是秒，`Date.now()` 需 `/ 1000`，否则会返回时间冲突错误。

4. **房间设置了密码如何处理？**

   捕获 `TUIErrorCode.ERR_NEED_PASSWORD` / `ERR_WRONG_PASSWORD`，弹出密码输入框，重新 `joinRoom({ roomId, password })`；错误多次后可提示联系房主重置。

5. **roomId 冲突或重复？**

   建议由后端生成全局唯一 roomId；如果前端生成，请在调度前检查冲突并做好重试策略。

6. **分页怎么继续拉取？**

   `getScheduledRoomList` 返回的 `cursor` 不为空时继续传入；为空表示已拉完。

### 当预定房间后，无法收到`RoomListener`的`onScheduledRoomStartingSoon`事件。

如果您在预定房间后未收到即将开始的事件通知，请按照以下两个核心要点进行检查：
1. **检查提醒参数配置** (`reminderSecondsBeforeStart`)

  - 触发机制：`onScheduledRoomStartingSoon` 事件的触发高度依赖于预定配置类 `ScheduleRoomOptions` 中的 `reminderSecondsBeforeStart` 属性。

  - 默认行为：该属性的默认值为 0，代表“不开启提醒”。

  - 解决建议：请确保在预定房间时明确设置该参数。该参数单位为**秒**，表示在房间开始前多少秒推送事件通知。

2. **验证预定时间逻辑**

  - 计算公式：系统触发提醒的前提是：预定开始时间 (`scheduleStartTime`) - 当前时间 > 提醒偏移量 (`reminderSecondsBeforeStart`)。

  - 场景说明：如果您的房间开始时间设置得过近（例如 5 分钟后开始），而提醒时间设置得过大（例如设置 10 分钟前提醒），系统将无法捕捉到触发时机，从而导致事件丢失。

  - 解决建议：请确保预定的开始时间距离当前时刻，大于您所设置的提醒秒数。

### 当预约房间后，无法收到`RoomEvent`的`onScheduledRoomStartingSoon`事件。

如果您在预约房间后未收到即将开始的事件通知，请按照以下两个核心要点进行检查：
1. **检查提醒参数配置** (`reminderSecondsBeforeStart`)
- 触发机制：`onScheduledRoomStartingSoon` 事件的触发高度依赖于预约配置类 `ScheduleRoomOptions` 中的 `reminderSecondsBeforeStart` 属性。
- 默认行为：该属性的默认值为 0，代表“不开启提醒”。
- 解决建议：请确保在预约房间时明确设置该参数。该参数单位为**秒**，表示在房间开始前多少秒推送事件通知。
2. **验证预约时间逻辑**
- 计算公式：系统触发提醒的前提是：预约开始时间 (`scheduleStartTime`) - 当前时间 > 提醒偏移量 (`reminderSecondsBeforeStart`)。
- 场景说明：如果您的房间开始时间设置得过近（例如 5 分钟后开始），而提醒时间设置得过大（例如设置 10 分钟前提醒），系统将无法捕捉到触发时机，从而导致事件丢失。
- 解决建议：请确保预约的开始时间距离当前时刻，大于您所设置的提醒秒数。

### 步骤5：监听预定房间事件及状态

- 订阅`RoomEvent`预定房间相关的被动事件，示例代码如下：

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   late RoomListener _roomListener;
   
   /// 订阅预定房间相关事件
   void subscribeScheduledRoomEvents() {
     _roomListener = RoomListener(
       onAddedToScheduledRoom: (roomInfo) {
         print("已被添加到预定房间 房间信息: $roomInfo");
       },
       onRemovedFromScheduledRoom: (roomInfo, operatorUser) {
         print("已被移除预定房间 房间信息: $roomInfo, 移除者: $operatorUser");
       },
       onScheduledRoomCancelled: (roomInfo, operatorUser) {
         print("预定房间被取消 房间信息: $roomInfo, 取消者: $operatorUser");
       },
       onScheduledRoomStartingSoon: (roomInfo) {
         print("预定房间即将开始 房间信息: $roomInfo");
       },
       onRoomEnded: (roomInfo) {
         print("房间已结束 房间信息: $roomInfo");
       },
     );
   
     RoomStore.shared.addRoomListener(_roomListener);
   }
   
   /// 取消订阅预定房间相关事件
   void unsubscribeScheduledRoomEvents() {
     RoomStore.shared.removeRoomListener(_roomListener);
   }
   ```
- 订阅`RoomState`预定房间列表状态变化，示例代码如下：

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// 订阅预定房间列表状态变化
   Widget buildScheduledRoomListWidget() {
     return ValueListenableBuilder(
       valueListenable: RoomStore.shared.state.scheduledRoomList,
       builder: (context, scheduledRoomList, child) {
         print("预定房间列表变化 房间列表信息: $scheduledRoomList");
         
         // 根据 scheduledRoomList 构建 UI
         return Container();
       },
     );
   }
   ```

## 适用场景

- **企业定期会议：** 提前安排周会、月会，确保参会者提前收到通知。

- **在线课堂排课：** 老师可以提前设置好一周的课程表。

- **远程面试预约：** HR 为面试者和面试官预约特定的时间段。

## 前提条件

- **用户状态：** 用户已经通过 useLoginState 完成登录鉴权，参考 接入概览，处于**已登录**状态才能发起预定或查看预定列表。

- **环境依赖：** 项目已引入 `tuikit-atomicx-vue3`。

## 实现预定房间功能

本功能提供两种集成方案，您可以根据业务需求选择最适合的一种：
- **方案一（推荐）：** 使用 UI 组件快速集成。直接引入 `tuikit-atomicx-vue3` 提供的预定房间面板（[ScheduleRoomPanel](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduleRoomPanel.vue)）和预定房间列表组件（[ScheduledRoomList](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduledRoomList.vue)），开发成本最低。

- **方案二（高级）：** 使用底层 API 自定义集成。基于 atomicx-core SDK API `useRoomState` 状态钩子自行实现 UI 和交互逻辑，灵活度最高。

   

## 方案一：使用 UI 组件快速集成

`tuikit-atomicx-vue3` 提供了两个核心组件：
- [**ScheduleRoomPanel**](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduleRoomPanel.vue)**：**预定会议的配置面板，包含时间选择、成员邀请等功能。

- [**ScheduledRoomList**](https://github.com/Tencent-RTC/TUIKit_Vue3/blob/main/packages/tuikit-atomicx-vue3/src/components/ScheduleRoomPanel/ScheduledRoomList.vue)**：**展示当前用户的预定会议列表，支持点击入会、修改和取消预定。

### 步骤1：引入组件

``` typescript
import { ScheduleRoomPanel, ScheduledRoomList } from 'tuikit-atomicx-vue3/room';
```

### 步骤2：使用组件

在您的页面中，可以直接组合使用这两个组件。
``` typescript
<template>
  <UIKitProvider theme="light" language="zh-CN">
    <div class="schedule-container">
      <!-- 预定按钮，点击后显示预定弹窗 -->
      <button @click="showSchedulePanel = true">
        预定会议
      </button>

      <!-- 预定列表组件 -->
      <div class="list-container">
        <!-- 监听 join-room 事件处理入会逻辑 -->
        <ScheduledRoomList @join-room="handleJoinRoom" />
      </div>

      <!-- 预定面板弹窗 -->
      <!-- 建议将其包裹在 Dialog 或 Modal 组件中 -->
      <div v-if="showSchedulePanel" class="modal-mask">
        <div class="modal-content">
          <ScheduleRoomPanel
            @confirm="handleScheduleConfirm"
            @cancel="showSchedulePanel = false"
          />
        </div>
      </div>
    </div>
  </UIKitProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { UIKitProvider } from '@tencentcloud/uikit-base-component-vue3';
import { ScheduleRoomPanel, ScheduledRoomList, useRoomState } from 'tuikit-atomicx-vue3/room';

const { joinRoom } = useRoomState();

const showSchedulePanel = ref(false);

const handleScheduleConfirm = (roomId: string, options: Record<string, any>) => {
  console.log('预定成功', roomId, options);
  showSchedulePanel.value = false;
  // 可选：显示邀请弹窗或复制链接
};

const handleJoinRoom = async (roomInfo: { roomId: string }) => {
  console.log('点击入会', roomInfo.roomId);
  await joinRoom(roomInfo.roomId); 
};
</script>

<style scoped>
.schedule-container{padding:24px;max-width:1000px;margin:0 auto;font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,'Helvetica Neue',Arial,sans-serif}button{background-color:#006eff;color:#fff;border:none;padding:10px 20px;border-radius:4px;cursor:pointer;font-size:14px;font-weight:500;transition:background-color .2s;margin-bottom:24px}button:hover{background-color:#0056cc}.list-container{background:#fff;border-radius:8px;box-shadow:0 2px 8px rgba(0,0,0,.05);padding:16px}.modal-mask{position:fixed;top:0;left:0;right:0;bottom:0;background-color:rgba(0,0,0,.4);backdrop-filter:blur(4px);display:flex;align-items:center;justify-content:center;z-index:1000}.modal-content{background-color:#fff;border-radius:8px;box-shadow:0 4px 16px rgba(0,0,0,.1);padding:24px;width:100%;max-width:480px}
</style>
```

## 方案二：使用底层 API 自定义集成

本节主要介绍如何通过 atomicx-core SDK API [useRoomState](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#module-RoomState) 接口来实现预定房间的核心逻辑。

预定房间时序图

### 步骤1：发起预定

使用 [scheduleRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#scheduleRoom) 方法创建一个新的预定会议。您需要指定会议的开始时间、结束时间以及房间名称等信息。
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { scheduleRoom } = useRoomState();

const createSchedule = async () => {
  try {
    // roomId 限制：字符串类型，必传参数，建议随机生成
    const roomId = '123456'; 
    
    // 注意：时间戳单位必须为 **秒** (Date.getTime() 获取的是毫秒，需除以 1000)
    const startTime = Math.floor(new Date().getTime() / 1000) + 3600; // 1小时后开始
    const duration = 1800; // 30分钟

    const options = {
      roomName: '产品需求评审会',
      scheduleStartTime: startTime, // 单位：秒
      scheduleEndTime: startTime + duration, // 单位：秒
      scheduleAttendees: ['userA', 'userB'], // 邀请参会成员ID列表
      password: '123', // 可选：设置入会密码
      isAllMicrophoneDisabled: false, // 可选：是否全体禁麦
      isAllCameraDisabled: false,     // 可选：是否全体禁画
    };

    await scheduleRoom({ roomId, options });
    console.log('预定成功', roomId);
  } catch (error) {
    console.error('预定失败', error);
  }
};
```

### 步骤2：获取预定列表

使用 [getScheduledRoomList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#getScheduledRoomList) 方法拉取当前用户的预定会议列表。列表数据会响应式更新到 [scheduledRoomList](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#scheduledRoomList) 状态中。
``` typescript
import { onMounted, watch } from 'vue';
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { 
  scheduledRoomList,      // 响应式数据：预定会议列表
  getScheduledRoomList    // 方法：拉取列表
} = useRoomState();

// 初始化加载列表
onMounted(async () => {
  // cursor 表示从哪里开始拉取数据，空字符串表示从头开始
  // getScheduledRoomList 返回值包含新的 cursor
  const { cursor } = await getScheduledRoomList({ cursor: '' }); 
  // 如果 cursor 不为空，表示还有更多数据，可继续调用拉取
});

// 监听列表变化，实时更新 UI
watch(scheduledRoomList, (list) => {
  console.log('当前预定列表：', list);
});
```

### 步骤3：修改预定

如果需要调整已预定会议的时间或主题，可以使用 [updateScheduledRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#updateScheduledRoom) 方法。
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { updateScheduledRoom } = useRoomState();

const updateRoom = async (roomId: string) => {
  try {
    const newStartTime = Math.floor(new Date().getTime() / 1000) + 7200; // 延后2小时
    const options = {
      roomName: '产品需求评审会（改期）',
      scheduleStartTime: newStartTime,
      scheduleEndTime: newStartTime + 1800,
    };

    await updateScheduledRoom({ roomId, options });
    console.log('修改成功');
  } catch (error) {
    console.error('修改失败', error);
  }
};
```

### 步骤4：取消预定

如果需要取消某个已预定的会议，可以调用 [cancelScheduledRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#cancelScheduledRoom) 方法。
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { cancelScheduledRoom } = useRoomState();

const cancelRoom = async (roomId: string) => {
  try {
    await cancelScheduledRoom({ roomId });
    console.log('取消成功');
    // 取消成功后，scheduledRoomList 会自动更新，无需手动重新拉取
  } catch (error) {
    console.error('取消失败', error);
  }
};
```

### 步骤5：处理入会密码

对于设置了密码的预定会议，在调用 [joinRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#joinRoom) 接口入会时，需要捕获鉴权失败的错误，并引导用户输入密码。
``` typescript
import { useRoomState, TUIErrorCode } from 'tuikit-atomicx-vue3/room';

const { joinRoom } = useRoomState();

const enterRoom = async (roomId: string, password?: string) => {
  try {
    await joinRoom({ roomId, password });
  } catch (error: any) {
    // 捕获特定错误码，TUIErrorCode.ERR_NEED_PASSWORD (-2010)
    if (error.code === TUIErrorCode.ERR_NEED_PASSWORD) {
      console.log('该房间需要密码，请引导用户输入');
      // 1. 弹出密码输入框
      // 2. 获取用户输入的密码 inputPassword
      // 3. 重新调用 enterRoom(roomId, inputPassword)
    } else if (error.code === TUIErrorCode.ERR_WRONG_PASSWORD) {
      console.error('密码错误，请重新输入');
    } else {
      console.error('入会失败', error);
    }
  }
};
```

### 步骤6：监听预定事件

除了被动响应数据变化，您还可以监听特定的预定事件来处理业务逻辑，例如在会议即将开始时弹出提醒。
``` typescript
import { onMounted, onUnmounted } from 'vue';
import { useRoomState, RoomEvent } from 'tuikit-atomicx-vue3/room';

const { subscribeEvent, unsubscribeEvent } = useRoomState();

// 会议即将开始通知（默认为开始前5分钟）
const handleRoomStartingSoon = (info: { roomInfo: any }) => {
  console.log('会议即将开始：', info.roomInfo.roomName);
  // 可在此处实现 Toast 提醒或系统通知
};

// 会议被取消通知
const handleRoomCancelled = (info: { roomInfo: any; operateUser: any }) => {
  console.log(`会议 ${info.roomInfo.roomName} 已被 ${info.operateUser.userName} 取消`);
};

// 收到新的会议邀请通知
const handleRoomAdded = (info: { roomInfo: any }) => {
  console.log('收到新的会议邀请：', info.roomInfo.roomName);
};

// 被移出会议通知
const handleRemovedFromRoom = (info: { roomInfo: any; operateUser: any }) => {
  console.log(`您已被 ${info.operateUser.userName} 移出会议 ${info.roomInfo.roomName}`);
};

onMounted(() => {
  // 注册事件监听
  subscribeEvent(RoomEvent.onScheduledRoomStartingSoon, handleRoomStartingSoon);
  subscribeEvent(RoomEvent.onScheduledRoomCancelled, handleRoomCancelled);
  subscribeEvent(RoomEvent.onAddedToScheduledRoom, handleRoomAdded);
  subscribeEvent(RoomEvent.onRemovedFromScheduledRoom, handleRemovedFromRoom);
});

onUnmounted(() => {
  // 移除事件监听
  unsubscribeEvent(RoomEvent.onScheduledRoomStartingSoon, handleRoomStartingSoon);
  unsubscribeEvent(RoomEvent.onScheduledRoomCancelled, handleRoomCancelled);
  unsubscribeEvent(RoomEvent.onAddedToScheduledRoom, handleRoomAdded);
  unsubscribeEvent(RoomEvent.onRemovedFromScheduledRoom, handleRemovedFromRoom);
});
```

## 开发注意事项

1. **时间戳单位**：SDK 中涉及的所有时间参数（例如 `scheduleStartTime`、`scheduleEndTime`）单位均为 **秒**，而非 JavaScript 中 `Date` 对象默认的毫秒。在传参前请务必执行 `/ 1000` 操作。

2. **样式引入**：使用 UI 组件时，务必引入 `UIKitProvider`，否则组件样式将无法正常渲染。

3. **RoomID 建议**：虽然 `scheduleRoom` 允许前端传入 `roomId`，但为了避免 ID 冲突，建议该 ID 由业务后端生成并保证全局唯一。

4. **分页处理**：预定列表可能会很长，`getScheduledRoomList` 接口支持分页拉取。请关注返回值中的 `cursor` 字段，若不为空，则说明还有更多数据。

## **示例项目**

腾讯云在 GitHub 上提供了示例项目 [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) ，您可以参考以实现完整的 RoomKit 功能。
