> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

会中呼叫功能允许会议内的用户（邀请者）向房间外的其他用户（被邀请者）发送实时入会邀请。该功能基于 `Atomicx State Hooks` 实现，能够帮助开发者快速构建具有主动邀约能力的互动场景。

> **Android 差异：** `RoomStore` 是 `AtomicXCore` 中专职房间事务管理的模块，深度集成了会中呼叫能力。本章节旨在指导如何利用 `RoomStore` 高效完成会中呼叫相关功能的开发与接入
> **iOS 差异：** `RoomStore` 是 `AtomicXCore` 中专职房间事务管理的模块，深度集成了会中呼叫能力。本章节旨在指导如何利用 `RoomStore` 高效完成会中呼叫相关功能的开发与接入
> **Flutter 差异：** `RoomStore` 是 `AtomicXCore` 中专职房间事务管理的模块，深度集成了会中呼叫能力。本章节旨在指导如何利用 `RoomStore` 高效完成会中呼叫相关功能的开发与接入

## 核心功能

- **会中呼叫**：房间内任意参与者均可呼叫房间外任意成员加入房间。
- **获取会中呼叫用户列表**：可以获取指定房间的会中呼叫的用户列表。

## 核心概念

在开始集成之前，需要通过下表了解一下`RoomStore`中与会中呼叫相关的几个核心概念：
| **核心概念** | **类型** | **核心职责与描述** |
| --- | --- | --- |
| RoomCallStatus | `enum class` | 代表会中呼叫的状态，包含： - `NONE`（默认态）。 - `CALLING`（呼叫中）。 - `TIMEOUT`（呼叫超时）。 - `REJECTED`（呼叫被拒绝）。 |
| RoomCallResult | `enum class` | 代表会中呼叫的结果，包含： - `SUCCESS`（呼叫成功）。 - `ALREADY_IN_CALLING`（已在呼叫中）。 - `ALREADY_IN_ROOM`（已在房间中）。 |
| CallRejectionReason | `enum class` | 代表呼叫被拒绝的原因，目前仅支持： - `REJECTED`当有其他用户向被呼叫方发起入会邀请时触发（主动拒绝）。 - `IN_OTHER_ROOM`（在其他房间中）。 |
| RoomCall | `data class` | 代表会中呼叫业务的核心数据结构。负责存储呼叫者信息、被呼叫者信息、以及当前呼叫状态。 |
| RoomListener | `abstract class` | 代表房间动态的实时事件。其中包含会中呼叫相关的事件。 |
| RoomStore | `abstract class` | 这是与房间全生命周期相关的核心类。功能包含：发起会中呼叫，响应会中呼叫，获取房间的会中呼叫列表，并通过订阅其 `RoomListener` 来接收与会中呼叫相关的实时动态事件。 |

## 实现步骤

### 步骤1：组件集成

请参考 接入概览 集成 **AtomicXCore SDK**，并已完成 实现登录逻辑 部分接入。

### 步骤2：呼叫方功能

#### **1. 发起呼叫**
作为呼叫方，调用`RoomStore`的`callUserToRoom`接口，可以批量呼叫用户进房。
#### **2. 取消呼叫**
作为呼叫方，调用`RoomStore`的`cancelCall`接口，也可以批量取消正在进行的呼叫申请。
#### **3. 获取呼叫列表信息**
进入房间后，调用`RoomStore`的`getPendingCalls`接口，可以分页查询当前房间正在呼叫的用户列表信息。
#### **4. 订阅会中呼叫事件**
作为呼叫方，订阅 `RoomStore` 的 `RoomListener` 能够实时获取呼叫的生命周期事件。通过监听以下回调，可以精准捕捉呼叫的最终结果并据此更新 UI 状态或执行后续业务逻辑。
**呼叫事件函数详细说明**
| 事件函数 | 触发时机与说明 |
| --- | --- |
| onCallTimeout | 呼叫超时。当呼叫请求在预设的时间内（由调用 callUserToRoom 时指定）未得到被呼叫方的响应时触发。此时，呼叫自动结束，呼叫方通常需要提示“对方无人接听”。 |
| onCallAccepted | 呼叫被接受。被呼叫方主动点击了“接听”或“接受”按钮。收到此回调后，表示双方已达成呼叫意图，呼叫方通常应立即执行进入房间或开始推流的操作。 |
| onCallRejected | 呼叫被拒绝。被呼叫方主动拒绝了通话请求，或者因为被呼叫方当前正处于其他房间中而自动拒绝。回调中通常包含拒绝原因（例如主动拒绝或在其他房间中）。 |
| onCallHandledByOtherDevice | 呼叫已在其他设备处理。当用户在多个设备（如手机和 iPad）同时登录时，如果呼叫已在其中一台设备上被处理（接受或拒绝），当前设备会收到此通知，用于同步 UI 状态并自动消除呼叫界面。 |

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.CallUserToRoomCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomCallResult
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

private val roomID = "test_room_001"

// 呼叫用户加入房间
fun callUserToRoom(userIDList: List<String>, timeout: Int = 30, extensionInfo: String? = null) {
    RoomStore.shared()
        .callUserToRoom(roomID, userIDList, timeout, extensionInfo, object : CallUserToRoomCompletionHandler {
            override fun onSuccess(result: Map<String, RoomCallResult>) {
                Log.d("Room", "呼叫用户请求发送成功")
            }

            override fun onFailure(code: Int, desc: String) {
                Log.d("Room", "呼叫用户失败: [错误码: $code]: $desc")
            }
        })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

private val roomID = "test_room_001"

// 取消呼叫
fun cancelCall(userIDList: List<String>) {
    RoomStore.shared().cancelCall(roomID, userIDList, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "取消呼叫成功")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "取消呼叫失败: [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.ListResultCompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.RoomCall
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

private val roomID = "test_room_001"

// 获取待处理的呼叫列表
fun getPendingCalls(roomID: String, cursor: String?) {
    RoomStore.shared().getPendingCalls(roomID, cursor, object : ListResultCompletionHandler<RoomCall> {
        override fun onSuccess(result: List<RoomCall>, cursor: String) {
            Log.d("Room", "获取呼叫列表成功，列表信息: $result, 分页游标: $cursor")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "获取呼叫列表失败: [错误码: $code]: $desc")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.CallRejectionReason
import io.trtc.tuikit.atomicxcore.api.room.RoomCall
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomListener
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

// 订阅会中呼叫相关事件
fun subscribeRoomCallEvents() {
    RoomStore.shared().addRoomListener(object : RoomListener() {
        override fun onCallTimeout(roomInfo: RoomInfo, call: RoomCall) {
            Log.d("Room", "呼叫超时 房间信息: $roomInfo, 呼叫者: ${call.caller}")
        }

        override fun onCallAccepted(roomInfo: RoomInfo, call: RoomCall) {
            Log.d("Room", "呼叫已被接受 房间信息: $roomInfo, 被呼叫者: ${call.callee}")
        }

        override fun onCallRejected(roomInfo: RoomInfo, call: RoomCall, reason: CallRejectionReason) {
            Log.d("Room", "呼叫被拒绝 房间信息: $roomInfo, 被呼叫者: ${call.callee}, 拒绝原因: $reason")
        }

        override fun onCallHandledByOtherDevice(roomInfo: RoomInfo, isAccepted: Boolean) {
            Log.d("Room", "呼叫已被其他设备处理 房间信息: $roomInfo 处理结果: $isAccepted")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | func callUserToRoom(userIDList: [String], timeout: Int = 30, extensionInfo: String? = nil) { | func callUserToRoom(userIDList: [String], timeout: Int = 30, extensionInfo: String? = nil) { | Closure + Result |
| Flutter | Future<void> callUserToRoom({ | String? extensionInfo, | Future<Result> |

### 步骤3：被呼叫方功能

#### **1. 订阅会中呼叫事件**
作为被呼叫方，订阅 `RoomStore` 的 `RoomListener` 可以实时监听并响应来自呼叫方的呼叫行为。以下是构建呼叫提醒界面以及维护呼叫链路状态的关键事件。
**事件函数详细说明**
| 事件函数 | 触发时机与说明 |
| --- | --- |
| onCallReceived | 收到呼叫邀请。当有其他用户向您发起入会邀请时触发。收到此回调后，被呼叫方通常应立即弹出“呼叫提醒”界面，展示呼叫者信息、房间信息，并提供“接听”和“拒绝”按钮。 |
| onCallCancelled | 呼叫被发起方取消。呼叫方在被呼叫方callUserToRoom未做出响应前，主动撤回了呼叫请求。收到此回调后，被呼叫方应立即关闭当前的来电界面，并提示“对方已取消呼叫”，以恢复应用正常操作流程。 |
| onCallRevokedByAdmin | 呼叫被管理员撤销。房间管理员或后台系统因权限变更、房间解散等原因强行终止了该笔呼叫邀请。收到此通知后，被呼叫方需立即停止呼叫流程并隐藏相关 UI，处理逻辑通常类似于呼叫被取消。 |
#### **2. 接受呼叫、拒绝呼叫**
作为被呼叫方，调用`RoomStore`的`acceptCall`或者`rejectCall`接口，处理接受或者拒绝呼叫邀请。

**代码示例：**

**Android（主示例）：**
``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.room.RoomCall
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomListener
import io.trtc.tuikit.atomicxcore.api.room.RoomStore
import io.trtc.tuikit.atomicxcore.api.room.RoomUser

// 订阅会中呼叫相关事件
fun subscribeRoomCallEvents() {
    RoomStore.shared().addRoomListener(object : RoomListener() {
        override fun onCallReceived(roomInfo: RoomInfo, call: RoomCall, extensionInfo: String) {
            Log.d("Room", "收到房间呼叫 房间信息: $roomInfo, 呼叫者: ${call.caller}, 扩展信息: $extensionInfo")
        }

        override fun onCallCancelled(roomInfo: RoomInfo, call: RoomCall) {
            Log.d("Room", "呼叫被取消 房间信息: $roomInfo, 呼叫者: ${call.caller}")
        }

        override fun onCallRevokedByAdmin(roomInfo: RoomInfo, call: RoomCall, operator: RoomUser) {
            Log.d("Room", "呼叫被管理员移除 房间信息: $roomInfo, 呼叫者: ${call.caller}, 移除操作者: $operator")
        }
    })
}
```

``` java
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.room.CallRejectionReason
import io.trtc.tuikit.atomicxcore.api.room.RoomCall
import io.trtc.tuikit.atomicxcore.api.room.RoomInfo
import io.trtc.tuikit.atomicxcore.api.room.RoomListener
import io.trtc.tuikit.atomicxcore.api.room.RoomStore

// 订阅会中呼叫相关事件
fun subscribeRoomCallEvents() {
    RoomStore.shared().addRoomListener(object : RoomListener() {
        override fun onCallReceived(roomInfo: RoomInfo, call: RoomCall, extensionInfo: String) {
            Log.d("Room", "收到房间呼叫 房间信息: $roomInfo, 呼叫者: ${call.caller}, 扩展信息: $extensionInfo")
            // 接受、拒绝呼叫邀请操作
        }
    })
}

// 接受呼叫邀请
fun acceptCall(roomID: String) {
    RoomStore.shared().acceptCall(roomID, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "接受呼叫成功，房间ID: $roomID")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "接受呼叫失败: [错误码: $code] $desc")
        }
    })
}

// 拒绝呼叫邀请
fun rejectCall(roomID: String, reason: CallRejectionReason = CallRejectionReason.REJECTED) {
    RoomStore.shared().rejectCall(roomID, reason, object : CompletionHandler {
        override fun onSuccess() {
            Log.d("Room", "拒绝呼叫成功，房间ID: $roomID")
        }

        override fun onFailure(code: Int, desc: String) {
            Log.d("Room", "拒绝呼叫失败: [错误码: $code] $desc")
        }
    })
}
```

**其余平台差异片段（字段级）：**
| 平台 | 函数签名 | 事件名 | 返回模型 |
| --- | --- | --- | --- |
| iOS | private func subscribeRoomCallEvents() { | case .onCallReceived(let roomInfo, let call, let extensionInfo): | Closure + Result |
| Flutter | void addRoomCallListener() { | onCallReceived: (roomInfo, call, extensionInfo) { | Future<Result> |

## API 文档

**能力索引**
| 能力 | 核心能力 | 适用平台 |
| --- | --- | --- |
| RoomStore | 房间全生命周期管理：创建并加入 / 加入 / 离开 / 结束房间 / 更新、获取房间信息 / 房间预约 / 呼叫房间外成员 / 监听房间内被动事件（例如房间解散，房间信息更新等）。 | Android, iOS, Flutter |
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

## 适用场景

- **协同办公：**在进行远程会议时，若涉及跨部门决策，可一键呼叫外部专家快速入会，缩短沟通链路。

- **直播教学：**老师在授课过程中，若发现部分学生缺席，可直接邀请其加入课堂。

- **互联网医疗会诊：**首诊医生在病历分析过程中，通过呼叫功能邀请上级医生进入虚拟诊室进行多方会诊，或者医生呼叫在等候室的患者进行问诊。

## 前提条件

在接入功能前，请确保满足以下条件：
- **邀请者 (User A)：**用户已经通过 `useLoginState` 完成登录鉴权，参考 接入概览，并已作为一个房间的房主或成员在房间内，参考 房间周期。

- **被邀请者 (User B)：**用户已经通过 `useLoginState` 完成登录鉴权，参考 接入概览，以便接收信令事件。

- **环境依赖：**项目已正确安装并引入 `tuikit-atomicx-vue3`。

## 实现会中呼叫功能

实现会中呼叫的核心流程如下：

会中呼叫时序图

### 步骤1：发起呼叫（邀请者）

邀请者调用 [callUserToRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#callUserToRoom) 接口发起邀请。请确保邀请者当前已在房间内（即 [currentRoom](https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html#currentRoom) 不为空），否则无法发起呼叫。
``` typescript
import { useRoomState } from 'tuikit-atomicx-vue3/room';

const { currentRoom, callUserToRoom } = useRoomState();

const inviteUser = async () => {
  // 会中呼叫必须关联一个已存在的 roomId，因此需先校验当前用户的进房状态
  if (!currentRoom.value) {
    console.warn('请先进入房间再发起邀请');
    return;
  }

  try {
    const resultMap = await callUserToRoom({
      roomId: currentRoom.value.roomId, // 被邀请者将要加入的房间 ID
      userIdList: ['user1', 'user2'],    // 目标用户 ID 数组，支持单人或多人
      timeout: 60,                        // 邀请有效时长（秒），超时后双方会触发 onCallTimeout
      // extensionInfo 可用于传递业务自定义数据，例如邀请类型、附加消息等
      extensionInfo: JSON.stringify({ type: 'emergency', priority: 'high' }) 
    });
    
    // resultMap 返回每个 userId 对应的状态，例如 Success、AlreadyInRoom (已在房) 或 AlreadyInCalling (通话中)
    console.log('邀请发送完成，详情状态映射:', resultMap);
  } catch (error) {
    console.error('呼叫请求发送失败，请检查网络或登录状态:', error);
  }
};
```

### 步骤2：监听呼叫事件（被邀请者）

被邀请者需要监听 `onCallReceived` 事件。建议在例如 `App.vue`等长驻组件中实现，以确保无论用户在哪个页面都能响应。
``` typescript
import { ref, onMounted, onUnmounted } from 'vue';
import { useRoomState, RoomEvent } from 'tuikit-atomicx-vue3/room';

const { 
  subscribeEvent, 
  unsubscribeEvent, 
  acceptCall, 
  rejectCall, 
  joinRoom, 
  currentRoom 
} = useRoomState();

const showInviteDialog = ref(false); // 控制 UI 弹窗显隐
const currentCallInfo = ref(null);   // 缓存收到的邀请上下文

const handleCallReceived = async ({ roomInfo, call, extensionInfo }) => {
  // 冲突处理：如果用户当前已经在会议中，根据业务策略通常应直接拒绝新邀请，防止干扰
  if (currentRoom.value?.roomId) {
    await rejectCall({ roomId: roomInfo.roomId });
    return;
  }

  // 记录邀请详情，用于在 UI 上展示邀请者名称或业务透传数据
  currentCallInfo.value = { roomInfo, call };
  showInviteDialog.value = true;
};

// 接受邀请逻辑
const onUserAccept = async () => {
  if (!currentCallInfo.value) return;
  const { roomId } = currentCallInfo.value.roomInfo;

  try {
    // 关键流程 A：响应信令。告知邀请者已接受，使其能更新 UI（例如显示“对方已接听”）
    await acceptCall({ roomId });
    
    // 关键流程 B：执行进房。只有调用 joinRoom 才会真正开启音视频推拉流链路
    await joinRoom({ roomId }); 
  } catch (error) {
    console.error('接受呼叫后的进房操作失败:', error);
  } finally {
    closeDialog();
  }
};

// 拒绝邀请逻辑
const onUserReject = async () => {
  if (currentCallInfo.value) {
    // 告知邀请者已拒绝，邀请者会收到 onCallRejected 事件
    await rejectCall({ roomId: currentCallInfo.value.roomInfo.roomId });
  }
  closeDialog();
};

const closeDialog = () => {
  showInviteDialog.value = false;
  currentCallInfo.value = null;
};

// 事件全生命周期管理：确保在组件挂载时监听，销毁时移除，防止事件溢出
onMounted(() => {
  subscribeEvent(RoomEvent.onCallReceived, handleCallReceived);
  
  // 监听导致邀请结束的各种异常/同步事件，确保 UI 弹窗能及时闭合
  subscribeEvent(RoomEvent.onCallCancelled, closeDialog); // 邀请者取消了呼叫
  subscribeEvent(RoomEvent.onCallTimeout, closeDialog);   // 呼叫超时未响应
  subscribeEvent(RoomEvent.onCallHandledByOtherDevice, closeDialog); // 用户在其他设备已处理该呼叫
});

onUnmounted(() => {
  // 移除所有监听器，避免在组件销毁后继续执行 handleCallReceived 导致内存泄漏或报错
  unsubscribeEvent(RoomEvent.onCallReceived, handleCallReceived);
  unsubscribeEvent(RoomEvent.onCallCancelled, closeDialog);
  unsubscribeEvent(RoomEvent.onCallTimeout, closeDialog);
  unsubscribeEvent(RoomEvent.onCallHandledByOtherDevice, closeDialog);
});
```

## 实践教程：提升呼叫接通率

为了在业务中获得更高的呼叫接通率，除了基础功能的实现外，建议参考以下策略优化用户体验：

### 1. 增强呼叫感知（避免用户遗漏）

由于 Web 端特性，浏览器静音或页面处于后台运行状态可能导致用户遗漏呼叫。
- **视觉提醒**：利用 [Page Visibility API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) 检测页面可见性状态。若页面处于后台，可通过循环修改 `document.title` 实现标题栏闪烁提示（例如：“【新邀请】UserA 邀请您入会...”），以吸引用户注意。

- **听觉提醒**：在 `onCallReceived` 事件触发时播放高优先级的提示铃声。注意：需遵循浏览器的 [自动播放策略](https://developer.mozilla.org/zh-CN/docs/Web/Media/Guides/Autoplay)，大部分浏览器在缺乏用户交互（例如点击、触摸或按键）的情况下会限制音频自动播放。

### 2. 丰富邀请上下文（提升接听意愿）

缺乏明确意图的邀请容易被用户拒绝。建议充分利用 `extensionInfo` 字段传递关键业务信息，以提升用户的接听意愿。
- **传递业务上下文**：利用 `extensionInfo` 透传如 `reason`（例如：“项目紧急评审”）、`level`（例如：“P0故障排查”）等业务字段，告知用户呼叫的紧迫性和重要性。

### 3. 多通道触达兜底（确保消息可达）

受限于 Web 端机制，浏览器进程结束将无法接收实时信令。建议采取以下替代方案以确保触达：
- **浏览器系统通知**：引导用户授权 [Web Notification API](https://developer.mozilla.org/en-US/docs/Web/API/Notification)。只要浏览器进程未关闭（包括最小化或在后台标签页），即可触发操作系统级别的弹窗通知，有效提醒用户。

- **多通道触达**：建议结合业务后端逻辑，在检测到用户不在线或超时未响应时，通过**短信**、**邮件**或**企业微信**等外部渠道发送入会链接，实现全方位触达。

### 4. 智能重试机制（解决“接受邀请后进房失败”）

在弱网或其他不稳定环境下，用户点击接受邀请后调用 `joinRoom` 可能会出现偶发性失败。
- **自动重试**：建议在 `onUserAccept` 的异常处理逻辑（`catch`）中引入 2-3 次自动重试机制（推荐结合指数退避策略），以最大程度确保用户点击“接受”后能成功加入会议。

## 开发注意事项

1. **接受邀请不等于进房**

   `acceptCall` 仅仅是信令层面的回复（告知对方已接听）。用户必须在调用 `acceptCall` 后紧接着手动调用 `await joinRoom({ roomId })` 才能真正进入音视频房间。

2. **生命周期管理**

   请务必在组件销毁周期（`onUnmounted`）调用 `unsubscribeEvent`。如果不清理监听器，当用户切换页面时，旧页面的监听器会残留在内存中，导致一次邀请弹出多个窗口或逻辑重复执行。

3. **超时设置**

   如果不设置 `timeout` 参数，在某些网络异常或程序崩溃情况下，邀请可能长期处于挂起状态，导致无法再次对该用户发起呼叫。建议设置为 30-60 秒。

## 常见问题

1. **如何在呼叫时带上自定义参数（如“来自XX的紧急会议”）？**

   使用 `callUserToRoom` 的 `extensionInfo` 参数传递 JSON 字符串。被邀请者可以在 `onCallReceived` 事件的 `extensionInfo` 字段中解析该字符串获取业务信息。

2. **被邀请者如果不在线，上线后能收到邀请吗？**

   不可以。邀请事件是一个瞬时信令动作，SDK 目前不会补发离线期间收到的呼叫通知。

3. **可以取消已发出的邀请吗？**

   可以。调用 `cancelCall({ roomId, userIdList })` 即可撤销。此时被邀请者会收到 `onCallCancelled` 事件，您需要监听此事件来关闭被邀请端的 UI 弹窗。

4. **接受后进房失败怎么办？**

   检查是否已登录 `TUIRoomEngine`，重试 `joinRoom({ roomId })` 并捕获错误提示用户；必要时在 UI 上提供“重试”按钮。

5. **对方忙线或已在其他房间如何提示？**

   `callUserToRoom` 的返回值 `resultMap` 中若出现 `AlreadyInRoom` / `AlreadyInCalling`，应在邀请者 UI 提示“对方忙线/已在会中”，并决定是否稍后重试。

6. **超时/取消后弹窗不关闭？**

   确保监听 `onCallTimeout`、`onCallCancelled`、`onCallHandledByOtherDevice`，统一在回调里关闭弹窗、清理当前邀请数据。

7. **多设备同时响铃如何处理？**

   在任意设备接受/拒绝后，其余设备会收到 `onCallHandledByOtherDevice`，需关闭其他端弹窗避免重复处理。

8. **extensionInfo 该怎么约定？**

   建议使用 JSON 字符串并约定字段（例如 `type`、`priority`、`from`），在 `handleCallReceived` 时解析后展示友好文案（例如“来自XX的紧急会议”）。

## **示例项目**

腾讯云在 GitHub 上提供了示例项目 [atomicx-vite-vue3-ts](https://github.com/Tencent-RTC/TUIRoomKit/tree/main/Web/example/atomicx-vite-vue3-ts) ，您可以参考以实现完整的 RoomKit 功能。
