> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, uni-app**

本篇文档旨在指导 Android 开发者如何使用 `AtomicXCore` 框架中的 `BarrageStore` 模块，为您的直播/语聊房应用快速集成功能丰富、性能卓越的弹幕系统。

## 核心功能
`BarrageStore` 为您的直播应用提供了一套完整的弹幕解决方案，核心功能包括：
- 接收并展示直播间弹幕消息。

- 发送文本弹幕与观众互动。

- 发送自定义业务弹幕，以支持礼物、点赞等复杂场景。

- 在本地消息列表中插入系统提示（例如，"欢迎 XX 进入直播间"）。

## 核心概念解析
在开始集成之前，我们先通过下表了解一下 BarrageStore 相关的几个核心概念：

| **概念** | **职责描述** | **Android** | **iOS** | **Flutter** |
| --- | --- | --- | --- | --- |
| `Barrage` | 代表一条弹幕消息的数据模型。它包含了发送者信息 (`sender`)、消息内容 (`textContent` 或 `data`)、消息类型 (`messageType`) 等所有关键信息。 | `data class` | `struct` | `class` |
| `BarrageState` | 代表弹幕模块的当前状态。其核心属性 `messageList` 是一个`StateFlow`，按时间顺序存储了当前直播间的所有弹幕消息，是UI渲染的数据源。 | `data class` | `struct` | `class` |
| `BarrageStore` | 这是与弹幕功能交互的核心管理类。通过它，您可以发送消息 (`sendTextMessage`, `sendCustomMessage`)，并通过订阅其 `state` 属性来接收和监听所有弹幕消息的更新。 | `abstract class` | `class` | `class` |

## API 文档
关于 BarrageStore 及其相关类的所有公开接口、属性和方法的详细信息，请参阅随 AtomicXCore 框架的官方 API 文档。

## Platform: Android

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并监听弹幕

获取一个与当前直播间 `liveId` 绑定的 `BarrageStore` 实例，并设置一个订阅者来实时接收最新的**全量**弹幕消息列表。
``` kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*
import io.trtc.tuikit.atomicxcore.api.barrage.Barrage
import io.trtc.tuikit.atomicxcore.api.barrage.BarrageStore

class BarrageManager(
    private val liveId: String
) {
    private val barrageStore: BarrageStore = BarrageStore.create(liveId)
    private val scope = CoroutineScope(Dispatchers.Main)
    
    // 对外暴露【全量】消息列表的Flow，方便UI层订阅
    private val _messagesFlow = MutableStateFlow<List<Barrage>>(emptyList())
    val messagesFlow: StateFlow<List<Barrage>> = _messagesFlow.asStateFlow()
    
    init {
        // 初始化后立即开始监听弹幕消息
        subscribeToBarrageUpdates()
    }

    private fun subscribeToBarrageUpdates() {
        scope.launch {
            barrageStore.barrageState.messageList
                .collect { messageList ->
                    // 当 messageList 更新时，通过 Flow 将新列表传递给UI层
                    // 关键点：这里接收到的是包含所有历史消息的【完整列表】
                    _messagesFlow.value = messageList
                }
        }
    }
}
```

### 步骤3：发送文本弹幕

调用 `sendTextMessage` 方法向直播间内的所有用户广播一条纯文本消息。
``` kotlin
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.barrage.BarrageStore

class BarrageManager(
    private val liveId: String
) {
    private val barrageStore: BarrageStore = BarrageStore.create(liveId)

    // 发送一条文本弹幕
    fun sendTextMessage(text: String) {
        // 建议：增加非空校验，避免发送无效消息
        if (text.isEmpty()) {
            return
        }
        
        // 调用核心API发送消息
        barrageStore.sendTextMessage(
            text,
            mapOf(
                "custom key" to "custom value",
            ),
            object : CompletionHandler {
                override fun onSuccess() {
                    println("文本弹幕 '$text' 发送成功")
                }

                override fun onFailure(code: Int, desc: String) {
                    println("文本弹幕发送失败: $desc")
                }
            }
        )
    }
}

```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `text` | `String?` | 要发送的文本内容。 |
| `extensionInfo` | `Map<String, String>?` | 附加的扩展信息，可用于业务自定义。 |
| `completion` | `CompletionHandler?` | 发送完成后的回调，包含成功或失败的结果。 |

### 步骤4：发送自定义弹幕

发送一条包含自定义业务逻辑的消息，例如礼物、点赞或游戏化互动指令。这条消息对其他客户端来说是透明的，需要业务层自行解析。
``` kotlin
class BarrageManager(
    private val liveId: String
) {
    // 发送一条自定义弹幕，例如用于发送礼物
    fun sendGiftMessage(giftId: String, giftCount: Int) {
        // 1. 定义一个能识别业务的ID
        val businessId = "live_gift"

        // 2. 将业务数据编码为 JSON 字符串
        val giftData = mapOf(
            "gift_id" to giftId,
            "gift_count" to giftCount
        )

        val jsonString = try {
            // 使用 Gson 或其他 JSON 库进行序列化
            // 这里假设使用 Gson
            Gson().toJson(giftData)
        } catch (e: Exception) {
            return
        }

        // 3. 调用核心API发送自定义消息
        barrageStore.sendCustomMessage(
            businessId,
            jsonString,
            object : CompletionHandler {
                override fun onSuccess() {
                    println("礼物消息（自定义弹幕）发送成功")
                }
                
                override fun onFailure(code: Int, desc: String) {
                    println("礼物消息发送失败: $desc")
                }
            }
        )
    }
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `businessId` | `String` | 业务唯一标识符，例如 "live_gift"，用于接收端区分不同的自定义消息。 |
| `data` | `String` | 业务数据，通常为 JSON 格式的字符串。 |
| `completion` | `CompletionHandler?` | 发送完成后的回调。 |

### 步骤5：在本地插入提示消息

在当前用户的消息列表中插入一条本地消息，这条消息不会被发送到直播间的其他用户。这非常适合用来显示系统欢迎、警告或操作提示等信息。
``` kotlin
class BarrageManager(
    private val liveId: String
) {
    // 在本地消息列表中插入一条欢迎提示
    fun showWelcomeMessage(user: LiveUserInfo) {
        // 1. 创建一条 Barrage 消息
        val welcomeTip = Barrage(
            liveID = liveId,
            messageType = BarrageType.TEXT, // 可以复用 text 类型来显示
            textContent = "欢迎 ${user.userName} 进入直播间！",
            sender = LiveUserInfo() // sender 可以留空或设置为一个系统用户的标识
        )

        // 2. 调用API将其插入本地列表
        barrageStore.appendLocalTip(welcomeTip)
    }
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `message` | `Barrage` | 要在本地插入的消息对象。SDK 会将此消息对象追加到 BarrageState 的 messageList 中。 |

### 步骤6：管理用户发言（禁言与解禁）

作为主播或管理员，您可以对直播间内的用户发言权限进行管理，维护健康的社区氛围。

#### 禁止/解禁单个用户发言

通过 `LiveAudienceStore` 中的 `disableSendMessage` 接口来实现对指定用户的禁言或解禁。此状态会被持久化，即使用户重新进入直播间，禁言状态依然有效。
``` kotlin
import io.trtc.tuikit.atomicxcore.api.*
import io.trtc.tuikit.atomicxcore.api.live.LiveAudienceStore

// 1. 获取与当前直播间绑定的 LiveAudienceStore 实例
val audienceStore = LiveAudienceStore.create(liveId)

// 2. 定义要操作的用户ID和禁言状态
val userIdToMute = "user_id_to_be_muted"
val shouldDisable = true // true为禁言, false为解禁

// 3. 调用接口执行操作
audienceStore.disableSendMessage(
    userIdToMute,
    shouldDisable,
    object : CompletionHandler {
        override fun onSuccess() {
            println("${if (shouldDisable) "禁言" else "解禁"}用户 $userIdToMute 成功")
        }

        override fun onFailure(code: Int, desc: String) {
            println("操作失败: $desc")
        }
    }
)
```

#### 开启/关闭全体禁言

要对直播间内所有用户（通常不包括主播自己）进行禁言，您需要通过 LiveListStore 更新直播间信息来实现。
``` kotlin
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore

// 1. 获取 LiveListStore 单例
val liveListStore = LiveListStore.shared()

// 2. 获取当前直播间信息，并修改全体禁言状态
val currentLiveInfo = liveListStore.liveState.currentLive.value.copy(
    isMessageDisable = true // true为全体禁言, false为关闭
)

// 3. 调用更新接口，并指定修改的标志位
liveListStore.updateLiveInfo(
    currentLiveInfo,
    listOf(LiveInfo.ModifyFlag.IS_MESSAGE_DISABLE),
    object : CompletionHandler {
        override fun onSuccess() {
            println("全体禁言状态更新成功")
        }

        override fun onFailure(code: Int, desc: String) {
            println("操作失败: $desc")
        }
    }
)
```

## Platform: iOS

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并监听弹幕

获取一个与当前直播间 `liveId` 绑定的 `BarrageStore` 实例，并设置一个订阅者来实时接收最新的**全量**弹幕消息列表。
``` swift
import Foundation
import AtomicXCore // 确保导入核心库
import Combine     // 用于处理响应式编程

class BarrageManager {
    
    private let liveId: String
    private let barrageStore: BarrageStore
    private var cancellables = Set<AnyCancellable>()
    
    // 对外暴露【全量】消息列表的发布者，方便UI层订阅
    let messagesPublisher = PassthroughSubject<[Barrage], Never>()
    
    init(liveId: String) {
        self.liveId = liveId
        // 1. 通过 liveId 获取 BarrageStore 的单例
        self.barrageStore = BarrageStore.create(liveID: liveId)
        
        // 2. 初始化后立即开始监听弹幕消息
        subscribeToBarrageUpdates()
    }
    
    private func subscribeToBarrageUpdates() {
        barrageStore.state
            .subscribe() // 订阅 BarrageState 的所有变化
            .receive(on: DispatchQueue.main) // 确保在主线程处理数据，保证UI安全
            .sink { [weak self] barrageState in
                // 3. 当 messageList 更新时，通过 publisher 将新列表传递给UI层
                // 关键点：这里接收到的是包含所有历史消息的【完整列表】
                self?.messagesPublisher.send(barrageState.messageList)
            }
            .store(in: &cancellables) // 管理订阅的生命周期
    }
}
```

### 步骤3：发送文本弹幕

调用 `sendTextMessage` 方法向直播间内的所有用户广播一条纯文本消息。
``` swift
extension BarrageManager {
    
    /// 发送一条文本弹幕
    func sendTextMessage(text: String) {
        // 建议：增加非空校验，避免发送无效消息
        guard !text.isEmpty else {
            print("弹幕内容不能为空")
            return
        }
        
        // 调用核心API发送消息
        barrageStore.sendTextMessage(text: text, extensionInfo: nil) { result in
            // 在回调中处理发送结果，例如给用户提示
            switch result {
            case .success:
                print("文本弹幕 '\(text)' 发送成功")
            case .failure(let error):
                print("文本弹幕发送失败: \(error.localizedDescription)")
            }
        }
    }
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `text` | `String` | 要发送的文本内容。 |
| `extensionInfo` | `[String: String]?` | 附加的扩展信息，可用于业务自定义。 |
| `completion` | `CompletionClosure?` | 发送完成后的回调，包含成功或失败的结果。 |

### 步骤4：发送自定义弹幕

发送一条包含自定义业务逻辑的消息，例如礼物、点赞或游戏化互动指令。这条消息对其他客户端来说是透明的，需要业务层自行解析。
``` swift
extension BarrageManager {

    /// 发送一条自定义弹幕，例如用于发送礼物
    func sendGiftMessage(giftId: String, giftCount: Int) {
        // 1. 定义一个能识别业务的ID
        let businessId = "live_gift" 
        
        // 2. 将业务数据编码为 JSON 字符串
        let giftData: [String: Any] = ["gift_id": giftId, "gift_count": giftCount]
        guard let jsonData = try? JSONSerialization.data(withJSONObject: giftData),
              let jsonString = String(data: jsonData, encoding: .utf8) else {
            print("无法将礼物数据编码为JSON")
            return
        }
        
        // 3. 调用核心API发送自定义消息
        barrageStore.sendCustomMessage(businessID: businessId, data: jsonString) { result in
            switch result {
            case .success:
                print("礼物消息（自定义弹幕）发送成功")
            case .failure(let error):
                print("礼物消息发送失败: \(error.localizedDescription)")
            }
        }
    }
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `businessId` | `String` | 业务唯一标识符，例如 "live_gift"，用于接收端区分不同的自定义消息。 |
| `data` | `String` | 业务数据，通常为 JSON 格式的字符串。 |
| `completion` | `CompletionClosure?` | 发送完成后的回调。 |

### 步骤5：在本地插入提示消息

在当前用户的消息列表中插入一条本地消息，这条消息不会被发送到直播间的其他用户。这非常适合用来显示系统欢迎、警告或操作提示等信息。
``` swift
extension BarrageManager {
    
    /// 在本地消息列表中插入一条欢迎提示
    func showWelcomeMessage(for user: LiveUserInfo) {
        // 1. 创建一条 Barrage 消息
        var welcomeTip = Barrage()
        welcomeTip.messageType = .text // 可以复用 text 类型来显示
        welcomeTip.textContent = "欢迎 \(user.userName) 进入直播间！"
        // sender 可以留空或设置为一个系统用户的标识
        
        // 2. 调用API将其插入本地列表
        barrageStore.appendLocalTip(message: welcomeTip)
    }
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `message` | `Barrage` | 要在本地插入的消息对象。SDK 会将此消息对象追加到 BarrageState 的 messageList 中。 |

### 步骤6：管理用户发言（禁言与解禁）

作为主播或管理员，您可以对直播间内的用户发言权限进行管理，维护健康的社区氛围。

#### 禁止/解禁单个用户发言

通过 `LiveAudienceStore` 中的 `disableSendMessage` 接口来实现对指定用户的禁言或解禁。此状态会被持久化，即使用户重新进入直播间，禁言状态依然有效。
``` swift
import AtomicXCore

// 1. 获取与当前直播间绑定的 LiveAudienceStore 实例
let audienceStore = LiveAudienceStore.create(liveID: "your_live_id")

// 2. 定义要操作的用户ID和禁言状态
let userIdToMute = "user_id_to_be_muted"
let shouldDisable = true // true为禁言, false为解禁

// 3. 调用接口执行操作
audienceStore.disableSendMessage(userID: userIdToMute, isDisable: shouldDisable) { [weak self] result in
    guard let self = self else { return }
    switch result {
    case .success:
        print("\(shouldDisable ? "禁言" : "解禁")用户 \(userIdToMute) 成功")
    case .failure(let error):
        print("操作失败: \(error.localizedDescription)")
    }
}
```

#### 开启/关闭全体禁言

要对直播间内所有用户（通常不包括主播自己）进行禁言，您需要通过 LiveListStore 更新直播间信息来实现。
``` swift
import AtomicXCore

// 1. 获取 LiveListStore 单例
let liveListStore = LiveListStore.shared

// 2. 获取当前直播间信息，并修改全体禁言状态
var currentLiveInfo = liveListStore.state.value.currentLive 
currentLiveInfo.isMessageDisable = true // true为全体禁言, false为关闭

// 3. 调用更新接口，并指定修改的标志位
liveListStore.updateLiveInfo(currentLiveInfo, modifyFlag: .isMessageDisable) { result in
    switch result {
    case .success:
        print("全体禁言状态更新成功")
    case .failure(let error):
        print("操作失败: \(error.localizedDescription)")
    }
}
```

## Platform: Flutter

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

完成集成后回到当前工程。

### 步骤2：初始化并监听弹幕

获取一个与当前直播间 `liveId` 绑定的 `BarrageStore` 实例，并设置一个监听器来实时接收最新的**全量**弹幕消息列表。
``` java
import 'package:flutter/foundation.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class BarrageManager {
  final String liveId;
  late final BarrageStore barrageStore;
  late final VoidCallback _messageListChangedListener = _onMessageListChanged;

  // 对外暴露消息列表的变化通知
  final ValueNotifier<List<Barrage>> messagesNotifier = 
      ValueNotifier<List<Barrage>>([]);

  BarrageManager({required this.liveId}) {
    // 1. 通过 liveId 获取 BarrageStore 的单例（位置参数）
    barrageStore = BarrageStore.create(liveId);

    // 2. 初始化后立即开始监听弹幕消息
    _subscribeToBarrageUpdates();
  }

  void _subscribeToBarrageUpdates() {
    // 3. 使用 ValueListenable 的 addListener 监听消息列表变化
    barrageStore.barrageState.messageList.addListener(_messageListChangedListener);
  }

  void _onMessageListChanged() {
    // 4. 当 messageList 更新时，获取新列表并通知 UI 层
    // 关键点：这里获取到的是包含所有历史消息的【完整列表】
    messagesNotifier.value = barrageStore.barrageState.messageList.value;
  }

  void dispose() {
    barrageStore.barrageState.messageList.removeListener(_messageListChangedListener);
    messagesNotifier.dispose();
  }
}
```

### 步骤3：发送文本弹幕

调用 `sendTextMessage` 方法向直播间内的所有用户广播一条纯文本消息。
``` java
extension BarrageManagerSend on BarrageManager {
  /// 发送一条文本弹幕
  Future<void> sendTextMessage(String text) async {
    // 建议：增加非空校验，避免发送无效消息
    if (text.isEmpty) {
      debugPrint("弹幕内容不能为空");
      return;
    }

    // 调用核心 API 发送消息
    final result = await barrageStore.sendTextMessage(
      text: text,
      extensionInfo: null,
    );

    // 使用 isSuccess 检查结果
    if (result.isSuccess) {
      debugPrint("文本弹幕 '$text' 发送成功");
    } else {
      debugPrint("文本弹幕发送失败: ${result.errorMessage}");
    }
  }
}
```

**接口参数：**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `text` | `String` | 要发送的文本内容。 |
| `extensionInfo` | `Map<String, String>?` | 附加的扩展信息，可用于业务自定义。 |

### 步骤4：发送自定义弹幕

发送一条包含自定义业务逻辑的消息，例如礼物、点赞或游戏化互动指令。这条消息对其他客户端来说是透明的，需要业务层自行解析。
``` java
import 'dart:convert';

extension BarrageManagerCustom on BarrageManager {
  /// 发送一条自定义弹幕，例如用于发送礼物
  Future<void> sendGiftMessage({
    required String giftId,
    required int giftCount,
  }) async {
    // 1. 定义一个能识别业务的 ID
    const businessID = "live_gift";

    // 2. 将业务数据编码为 JSON 字符串
    final giftData = {"gift_id": giftId, "gift_count": giftCount};
    final jsonString = jsonEncode(giftData);

    // 3. 调用核心 API 发送自定义消息
    final result = await barrageStore.sendCustomMessage(
      businessID: businessID,
      data: jsonString,
    );

    if (result.isSuccess) {
      debugPrint("礼物消息（自定义弹幕）发送成功");
    } else {
      debugPrint("礼物消息发送失败: ${result.errorMessage}");
    }
  }
}
```

**接口参数：**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `businessID` | `String` | 业务唯一标识符，例如 "live_gift"，用于接收端区分不同的自定义消息。 |
| `data` | `String` | 业务数据，通常为 JSON 格式的字符串。 |

### 步骤5：在本地插入提示消息

在当前用户的消息列表中插入一条本地消息，这条消息不会被发送到直播间的其他用户。这非常适合用来显示系统欢迎、警告或操作提示等信息。
``` java
extension BarrageManagerLocal on BarrageManager {
  /// 在本地消息列表中插入一条欢迎提示
  void showWelcomeMessage(LiveUserInfo user) {
    // 1. 创建一条 Barrage 消息（使用命名参数构造函数）
    final welcomeTip = Barrage(
      messageType: BarrageType.text,
      textContent: "欢迎 ${user.userName} 进入直播间！",
    );

    // 2. 调用 API 将其插入本地列表
    barrageStore.appendLocalTip(welcomeTip);
  }
}
```

### 步骤6：管理用户发言（禁言与解禁）

作为主播或管理员，您可以对直播间内的用户发言权限进行管理，维护健康的社区氛围。

#### 禁止/解禁单个用户发言

通过 `LiveAudienceStore` 中的 `disableSendMessage` 接口来实现对指定用户的禁言或解禁。
``` java
// 1. 获取与当前直播间绑定的 LiveAudienceStore 实例（位置参数）
final liveId = "your_live_id";
final audienceStore = LiveAudienceStore.create(liveId);

// 2. 定义要操作的用户 ID 和禁言状态
final userIdToMute = "user_id_to_be_muted";
final shouldDisable = true; // true 为禁言, false 为解禁

// 3. 调用接口执行操作
final result = await audienceStore.disableSendMessage(
  userID: userIdToMute,
  isDisable: shouldDisable,
);

if (result.isSuccess) {
  debugPrint("${shouldDisable ? "禁言" : "解禁"}用户 $userIdToMute 成功");
} else {
  debugPrint("操作失败: ${result.errorMessage}");
}
```

#### 开启/关闭全体禁言

要对直播间内所有用户（通常不包括主播自己）进行禁言，您需要通过 `LiveListStore` 更新直播间信息来实现。
``` java
// 1. 获取 LiveListStore 单例
final liveListStore = LiveListStore.shared;

// 2. 获取当前直播间信息，并创建新的 LiveInfo 对象修改全体禁言状态
final currentLiveInfo = liveListStore.liveState.currentLive.value;
final updatedLiveInfo = LiveInfo(
  liveID: currentLiveInfo.liveID,
  liveName: currentLiveInfo.liveName,
  isMessageDisable: true, // true 为全体禁言, false 为关闭
);

// 3. 调用更新接口，并指定修改的标志位列表
final result = await liveListStore.updateLiveInfo(
  liveInfo: updatedLiveInfo,
  modifyFlagList: [ModifyFlag.isMessageDisable],
);

if (result.isSuccess) {
  debugPrint("全体禁言状态更新成功");
} else {
  debugPrint("操作失败: ${result.errorMessage}");
}
```

## 完整 UI 示例
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

class BarrageWidget extends StatefulWidget {
  final String liveId;

  const BarrageWidget({Key? key, required this.liveId}) : super(key: key);

  @override
  State<BarrageWidget> createState() => _BarrageWidgetState();
}

class _BarrageWidgetState extends State<BarrageWidget> {
  late BarrageManager _barrageManager;
  final TextEditingController _inputController = TextEditingController();
  final ScrollController _scrollController = ScrollController();

  @override
  void initState() {
    super.initState();
    _barrageManager = BarrageManager(liveId: widget.liveId);
  }

  void _scrollToBottom() {
    WidgetsBinding.instance.addPostFrameCallback((_) {
      if (_scrollController.hasClients) {
        _scrollController.animateTo(
          _scrollController.position.maxScrollExtent,
          duration: const Duration(milliseconds: 200),
          curve: Curves.easeOut,
        );
      }
    });
  }

  @override
  void dispose() {
    _barrageManager.dispose();
    _inputController.dispose();
    _scrollController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        // 弹幕列表
        Expanded(
          child: ValueListenableBuilder<List<Barrage>>(
            valueListenable: _barrageManager.messagesNotifier,
            builder: (context, messageList, child) {
              // 滚动到底部
              _scrollToBottom();
              return ListView.builder(
                controller: _scrollController,
                itemCount: messageList.length,
                itemBuilder: (context, index) {
                  final barrage = messageList[index];
                  return _buildBarrageItem(barrage);
                },
              );
            },
          ),
        ),
        // 输入框
        _buildInputBar(),
      ],
    );
  }

  Widget _buildBarrageItem(Barrage barrage) {
    return Container(
      margin: const EdgeInsets.symmetric(vertical: 4, horizontal: 8),
      padding: const EdgeInsets.symmetric(horizontal: 12, vertical: 6),
      decoration: BoxDecoration(
        color: Colors.black54,
        borderRadius: BorderRadius.circular(16),
      ),
      child: RichText(
        text: TextSpan(
          children: [
            TextSpan(
              text: '${barrage.sender.userName}: ',
              style: const TextStyle(
                color: Colors.yellow,
                fontSize: 14,
                fontWeight: FontWeight.bold,
              ),
            ),
            TextSpan(
              text: barrage.textContent,
              style: const TextStyle(color: Colors.white, fontSize: 14),
            ),
          ],
        ),
      ),
    );
  }

  Widget _buildInputBar() {
    return Container(
      padding: const EdgeInsets.all(8),
      color: Colors.white,
      child: Row(
        children: [
          Expanded(
            child: TextField(
              controller: _inputController,
              decoration: const InputDecoration(
                hintText: '说点什么...',
                border: OutlineInputBorder(),
                contentPadding: EdgeInsets.symmetric(horizontal: 12),
              ),
              onSubmitted: (_) => _sendMessage(),
            ),
          ),
          const SizedBox(width: 8),
          ElevatedButton(
            onPressed: _sendMessage,
            child: const Text('发送'),
          ),
        ],
      ),
    );
  }

  void _sendMessage() {
    final text = _inputController.text.trim();
    if (text.isNotEmpty) {
      _barrageManager.sendTextMessage(text);
      _inputController.clear();
    }
  }
}
```

## Platform: uni-app

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并监听弹幕

获取一个与当前直播间 `liveID` 绑定的 `BarrageState` 实例，并且监听 `messageList` 来接收最新的**全量**弹幕消息列表。
``` typescript
import { useBarrageState } from "@/uni_modules/tuikit-atomic-x/state/BarrageState";
import { watch } from 'vue';

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 BarrageState 的实例
const { messageList } = useBarrageState(liveID);

// 监听 messageList 的实时变更，用于驱动 UI 更新
watch(messageList, (newVal, oldVal) => {
    console.log(newVal)
})
```

### 步骤3：发送文本弹幕

调用 `sendTextMessage` 方法向直播间内的所有用户广播一条纯文本消息。
``` typescript
import { useBarrageState } from "@/uni_modules/tuikit-atomic-x/state/BarrageState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 BarrageState 的实例
const { sendTextMessage } = useBarrageState(liveID);

const sendMessage = () => {
    sendTextMessage({
      liveID: 'xxx', // 当前直播间的 liveID
      text: 'xxx', // 您想要发送的弹幕     
      success: () => {
        console.log("弹幕发送成功")
      },
      fail: (error) => {
        console.log("弹幕发送失败", error)
      } 
    })
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `liveID` | `string` | 当前直播间的`liveID`。 |
| `text` | `string` | 要发送的文本内容。 |
| `success` | `Function` | 发送成功的回调。 |
| `fail` | `Function` | 发送失败的回调。 |

### 步骤4：发送自定义弹幕

发送一条包含自定义业务逻辑的消息，例如礼物、点赞或游戏化互动指令。这条消息对其他客户端来说是透明的，需要业务层自行解析。
``` typescript
import { useBarrageState } from "@/uni_modules/tuikit-atomic-x/state/BarrageState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 BarrageState 的实例
const { sendCustomMessage } = useBarrageState(liveID);

/// 发送一条自定义弹幕，例如用于发送礼物
const sendGiftMessage = ( giftID: string, giftCount: number ) => {
    // 1. 定义一个能识别业务的ID 
    const businessID = "live_gift"
    // 2. 将业务数据编码为 JSON 字符串
    const giftData = { "gift_id": giftId, "gift_count": giftCount }
    // 3. 调用核心API发送自定义消息
    sendCustomMessage({
      liveID: 'xxx', // 当前直播间的 liveID
      businessID,
      data: JSON.stringify(giftData),     
      success: () => {
        console.log("礼物消息发送成功")
      },
      fail: (error) => {
        console.log("礼物消息发送失败", error)
      }
    })
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `liveID` | `string` | 当前直播间的`liveID`。 |
| `businessId` | `string` | 业务唯一标识符，例如 "live_gift"，用于接收端区分不同的自定义消息。 |
| `data` | `string` | 业务数据，通常为 JSON 格式的字符串。 |
| `success` | `Function` | 发送成功的回调。 |
| `fail` | `Function` | 发送失败的回调。 |

### 步骤5：在本地插入提示消息

在当前用户的消息列表中插入一条本地消息，这条消息不会被发送到直播间的其他用户。这非常适合用来显示系统欢迎、警告或操作提示等信息。
``` typescript
import { useBarrageState } from "@/uni_modules/tuikit-atomic-x/state/BarrageState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 BarrageState 的实例
const { appendLocalTip } = useBarrageState(liveID);
const showWelcomeMessage = (user: LiveUserInfo) => {
    // 创建一条本地提示消息
    let welcomeTip = {}
    welcomeTip.liveID = liveID;
    welcomeTip.sender = { userID: 'xxx' };
    welcomeTip.sequence = Date.now();
    welcomeTip.timestampInSecond = Date.now();
    welcomeTip.messageType = 'TEXT';
    welcomeTip.textContent = "欢迎 ${user.userName} 进入直播间！";
    // 调用 BarrageStore 的接口将其插入弹幕列表
    appendLocalTip(welcomeTip)
}
```

#### **接口参数**
| **参数** | **类型** | **描述** |
| --- | --- | --- |
| `message` | `string` | 要在本地插入的消息对象。SDK 会将此消息对象追加到 `BarrageState` 的 `messageList` 中。 |

### 步骤6：管理用户发言（禁言与解禁）

作为主播或管理员，您可以对直播间内的用户发言权限进行管理，维护健康的社区氛围。

#### 禁止/解禁单个用户发言

通过 `LiveAudienceState` 中的 `disableSendMessage` 接口来实现对指定用户的禁言或解禁。此状态会被持久化，即使用户重新进入直播间，禁言状态依然有效。
``` typescript
import { useLiveAudienceState } from '@/uni_modules/tuikit-atomic-x/state/LiveAudienceState';

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

// 1. 通过 liveID 获取 LiveAudienceState 的实例
const { disableSendMessage } = useLiveAudienceState(liveID)

// 2. 定义要操作的用户 ID 和禁言状态
const userIdToMute = "user_id_to_be_muted"
const shouldDisable = true // true为禁言, false为解禁

// 3. 调用接口执行操作
const handleDisableSendMessage = () => {
    disableSendMessage({
      liveID: 'xxx', // 当前直播间 liveID
      userID: userIdToMute,
      isDisable: shouldDisable,
      success: () => {
        console.log(`${shouldDisable ? "禁言" : "解禁"}用户 ${userIdToMute}成功`)
      },
      fail: (error) => {
        console.log("操作失败", error)
      }
    })
}
```

#### 开启/关闭全体禁言

要对直播间内所有用户（通常不包括主播自己）进行禁言，您需要通过 `LiveListState` 的 `updateLiveInfo` 更新直播间信息来实现。
``` typescript
// 1. 获取 LiveListState 的实例
const { updateLiveInfo, currentLive } = useLiveListState();

// 2. 获取当前直播间信息，并修改全体禁言状态
currentLive.value.isMessageDisable = true // true 为全体禁言, false 为关闭

// 3. 调用更新接口，并指定修改的标志位
const handleUpdateLiveInfo = () => {
    updateLiveInfo({
      liveInfo: currentLive.value,
      modifyFlagList: ['IS_MESSAGE_DISABLE'],
      success: () => {
        console.log("全体禁言成功")
      },
      fail: (error) => {
        console.log("操作失败", error)
      }
    })
}

```

## 常见问题

### 除了基础的文本弹幕，我们还希望实现"彩色弹幕"、"礼物弹幕"等更丰富的样式，该如何实现？

> **适用平台：Android**

这是通过自定义消息 `sendCustomMessage` 来实现的。`BarrageStore` 不会限制您的业务想象力。

**实现思路**
1. **定义数据结构:** 与您的客户端和服务器团队共同定义好自定义消息的 JSON 结构。例如，一条彩色弹幕可以这样定义：

   ``` json
   {  "type": "colored_text",  "text": "这是一条彩色弹幕!",  "color": "#FF5733" }
   ```
2. **发送端:** 在发送时，将这个 JSON 结构转换为字符串，并通过 `sendCustomMessage` 的 `data` 参数发送出去。`businessID` 可以设置为一个能代表您业务的唯一标识，例如 "`barrage_style_v1`"。

3. **接收端**: 在接收到弹幕消息后，检查其 `messageType` 是否为 `BarrageType.CUSTOM` 以及 `businessID` 是否匹配。如果匹配，则解析 `data` 字符串（通常是解析 JSON），根据解析出的数据（例如 `color、text`）来渲染您的自定义UI样式。

### 我在不同的类、不同的文件中都调用了 `BarrageStore.create(liveID = "some_id")`，这会创建出多个实例导致混乱吗？

> **适用平台：Android / iOS**

完全不会。`AtomicXCore` 内部机制会确保只要您传入的 `liveID` 相同，获取到的永远是同一个与该直播间绑定的 `BarrageStore` 实例。您可以在需要的地方随用随取，无需手动管理单例。

### 为什么我调用了 sendTextMessage，但是在消息列表中看不到我发送的消息？

> **适用平台：Android / iOS / uni-app**

**请按以下步骤排查：**
> **Android：** 1. **检查 completion 回调**：sendTextMessage 方法有一个完成回调。请检查回调返回的结果是成功还是失败。如果失败，错误信息会明确指出问题所在（例如"您已被禁言"、"网络错误"等）。
> **iOS：** 1. **检查 completion 回调**：sendTextMessage 方法有一个完成回调。请检查回调返回的结果是成功还是失败。如果失败，错误信息会明确指出问题所在（例如“您已被禁言”、“网络错误”等）。
> **uni-app：** 1. **检查 success，fail 回调**：`sendTextMessage` 方法有成功和失败回调。请检查回调返回的结果是成功还是失败。如果失败，错误信息会明确指出问题所在（例如“您已被禁言”、“网络错误”等）。

2. **确认订阅时机**：确保您对 barrageStore.barrageState.messageList 的订阅发生在该 liveID 对应的直播开始之后。如果在加入直播房间之前就开始监听，可能会错过部分消息。

3. **检查 liveID**：确认您在创建 BarrageStore 实例、加入直播房间、以及发送消息时使用的 liveID 完全一致，包括大小写。

4. **网络问题**：检查设备当前的网络连接是否正常。消息发送依赖于网络。

### 新观众进入直播间时，如何让他们看到加入前的历史弹幕消息？

> **适用平台：Android / iOS / Flutter / uni-app**

`AtomicXCore` 支持拉取历史弹幕消息，但这需要您在**服务端控制台**进行一项简单的配置。配置完成后，SDK会自动处理后续的一切，您无需编写额外的代码。

**步骤1：在 IM 控制台进行配置**
> **Android/iOS/Flutter：** 1. 登录您的 [即时通讯 IM 控制台](https://cloud.tencent.com/login?s_url=https%3A%2F%2Fconsole.cloud.tencent.com%2Fim)。
> **uni-app：** 1. 登录您的 [即时通信 IM 控制台](https://cloud.tencent.com/login?s_url=https%3A%2F%2Fconsole.cloud.tencent.com%2Fim)。

> **Android：** 2. 在左侧导航栏，按照路径**消息服务 Chat** >** 功能配置 **> **群组配置** >** 群功能配置** > **直播群新成员查看入群前消息量配置**进行导航。
> **iOS/Flutter/uni-app：** 2. 在左侧导航栏按照路径：**消息服务 Chat** >** 功能配置 **>** 群组配置 **> **群功能配置** >** 直播群新成员查看入群前消息量配置**进行导航。

   

3. 修改"新成员可查看最近消息数"，最大支持 **50** 条。

   **步骤2：客户端无感获取**

   完成上述配置后，您的客户端代码**无需做任何改动**。 

> **Android：** 当新用户加入直播间时，`AtomicXCore` 的底层会自动拉取您配置的历史消息数量。这些历史消息会和实时消息一样，通过您已实现的 `BarrageStore.barrageState.messageList` 订阅通道推送给您的 UI 层。您的应用会像接收实时弹幕一样，自然地接收并展示这些历史弹幕。
> **iOS：** 当新用户加入直播间时，`AtomicXCore` 的底层 会自动拉取您配置的历史消息数量。这些历史消息会和实时消息一样，通过您已实现的 `BarrageStore.state` 订阅通道推送给您的UI层。您的应用会像接收实时弹幕一样，自然地接收并展示这些历史弹幕。
> **Flutter：** 当新用户加入直播间时，`AtomicXCore` 的底层 会自动拉取您配置的历史消息数量。这些历史消息会和实时消息一样，通过您已实现的 `BarrageStore.barrageState` 订阅通道推送给您的 UI 层。您的应用会像接收实时弹幕一样，自然地接收并展示这些历史弹幕。
> **uni-app：** 当新用户加入直播间时，`AtomicXCore` 的底层 会自动拉取您配置的历史消息数量。这些历史消息会和实时消息一样，通过您已实现的 `BarrageState` 订阅通道推送给您的 UI 层。您的应用会像接收实时弹幕一样，自然地接收并展示这些历史弹幕。

### 除了基础的文本弹幕，我们还希望实现“彩色弹幕”、“礼物弹幕”等更丰富的样式，该如何实现？

> **适用平台：iOS / uni-app**

这是通过自定义消息 `sendCustomMessage` 来实现的。`BarrageStore` 不会限制您的业务想象力。

**实现思路**
1. **定义数据结构:** 与您的客户端和服务器团队共同定义好自定义消息的 JSON 结构。例如，一条彩色弹幕可以这样定义：

   ``` json
   {  "type": "colored_text",  "text": "这是一条彩色弹幕!",  "color": "#FF5733" }
   ```
2. **发送端:** 在发送时，将这个 JSON 结构转换为字符串，并通过 `sendCustomMessage` 的 `data` 参数发送出去。`businessId` 可以设置为一个能代表您业务的唯一标识，例如 "`barrage_style_v1`"。

3. **接收端**: 在接收到弹幕消息后，检查其 `messageType` 是否为 `.custom` 以及 `businessId` 是否匹配。如果匹配，则解析 `data` 字符串（通常是解析JSON），根据解析出的数据（例如 `color、text`）来渲染您的自定义UI样式。

### 如何实现彩色弹幕、礼物弹幕等更丰富的弹幕样式？

> **适用平台：Flutter**

这是通过自定义消息 `sendCustomMessage` 来实现的。`BarrageStore` 不会限制您的业务想象力。

**实现思路**
1. **定义数据结构:** 与您的客户端和服务器团队共同定义好自定义消息的 JSON 结构。例如，一条彩色弹幕可以这样定义：

   ``` json
   {  "type": "colored_text",  "text": "这是一条彩色弹幕!",  "color": "#FF5733" }
   ```
2. **发送端:** 在发送时，将这个 JSON 结构转换为字符串，并通过 `sendCustomMessage` 的 `data` 参数发送出去。`businessId` 可以设置为一个能代表您业务的唯一标识，例如 "`barrage_style_v1`"。

3. **接收端**: 在接收到弹幕消息后，检查其 `messageType` 是否为 `.custom` 以及 `businessId` 是否匹配。如果匹配，则解析 `data` 字符串（通常是解析 JSON），根据解析出的数据（例如 `color、text`）来渲染您的自定义 UI 样式。

### 不同的类、不同的文件中都调用了 `BarrageStore.create("some_id")`，这会创建出多个实例导致混乱吗？

> **适用平台：Flutter**

完全不会。`AtomicXCore` 内部机制会确保只要您传入的 `liveId` 相同，获取到的永远是同一个与该直播间绑定的 `BarrageStore` 实例。您可以在需要的地方随用随取，无需手动管理单例。

### 为什么调用了 sendTextMessage，但是在消息列表中看不到发送的消息？

> **适用平台：Flutter**

**请按以下步骤排查：**
1. **检查返回结果**：`sendTextMessage` 方法有一个完成回调。请检查回调返回的结果是成功还是失败。如果失败，错误信息会明确指出问题所在（例如“您已被禁言”、“网络错误”等）。

2. **确认监听时机**：确保您对 `barrageStore.state` 的订阅发生在该 `liveId` 对应的直播开始之后。如果在加入直播房间之前就开始监听，可能会错过部分消息。

3. **检查 liveId**：确认您在创建 `BarrageStore` 实例、加入直播房间、以及发送消息时使用的 `liveId` 完全一致，包括大小写。

4. **网络问题**：检查设备当前的网络连接是否正常。消息发送依赖于网络。

### 为什么我的弹幕区域没有消息时，遮盖住了其底层区域的点击事件。

> **适用平台：uni-app**

在 `nvue` 下，如果您给组件提供默认的宽高，即使该组件没有渲染，它还是会挂载在 `UI` 上。这里建议不要给组件提供默认的宽高，而是根据有没有内容渲染来进行宽高的动态计算。
