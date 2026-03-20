> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, uni-app**

`LiveAudienceStore` 是 `AtomicXCore` 中专门负责管理直播间/语聊房观众信息的模块。通过`LiveAudienceStore`，您可以为您的直播/语聊房应用构建一套完整的观众列表及管理系统。

## 核心功能
- **实时观众列表**：获取并展示当前在直播间内的所有观众信息。

- **观众人数统计**：实时获取当前直播间的准确观众总数。

- **观众动态监听**：通过事件订阅，实时感知观众的加入和离开。

- **管理员权限**：主播可以将普通观众设为管理员，或撤销其管理员身份。

- **房间管理**：主播或管理员可以将任意普通观众请出（踢出）直播间。

## 核心概念
在开始集成之前，我们先通过下表了解一下 LiveAudienceStore 相关的几个核心概念：

| **概念** | **职责描述** | **Android** | **iOS** | **Flutter** |
| --- | --- | --- | --- | --- |
| `LiveUserInfo` | 代表一个观众（用户）的基础信息模型。它包含了用户的唯一标识 (`userID`)、昵称 (`userName`) 和头像地址 (`avatarURL`)。 | data class | struct | `class` |
| `LiveAudienceState` | 代表观众模块的当前状态。其核心属性 `audienceList` 是一个 StateFlow，存储了观众列表的实时状态；`audienceCount` 则代表当前观众总数的实时状态。 | data class | struct | `class` |
| `LiveAudienceListener` | 代表观众动态的实时事件。分为 `onAudienceJoined` (观众加入) 和 `onAudienceLeft` (观众离开) 两种，用于实现对观众列表的增量更新。 | abstract class | — | `class` |
| `LiveAudienceStore` | 这是与观众列表功能交互的核心管理类。通过它，您可以获取观众列表快照、执行管理操作，并通过订阅其 `LiveAudienceListener` 来接收实时动态。 | abstract class | class | `class` |
| `LiveAudienceEvent` | 代表观众动态的实时事件。分为 `.onAudienceJoined`（观众加入） 和 `.onAudienceLeft`（观众离开）两种，用于实现对观众列表的增量更新。 | — | enum | — |

## 功能进阶
### 在弹幕区展示观众入场欢迎语

当有新观众进入直播间时，弹幕/聊天区域会在本地自动显示一条欢迎消息，例如："欢迎 [用户昵称] 进入直播间"。

#### 实现方式

我们通过添加 `LiveAudienceStore` 的观众加入事件监听器（`LiveAudienceListener.onAudienceJoined`）来获取新观众加入的实时通知。一旦事件触发，我们便提取出新观众的昵称信息，然后立即调用 `BarrageStore` 的本地插入接口 `appendLocalTip`。
``` kotlin
import io.trtc.tuikit.atomicxcore.api.live.LiveAudienceListener
import io.trtc.tuikit.atomicxcore.api.live.LiveAudienceStore
import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
import io.trtc.tuikit.atomicxcore.api.barrage.Barrage
import io.trtc.tuikit.atomicxcore.api.barrage.BarrageStore
import io.trtc.tuikit.atomicxcore.api.barrage.BarrageType

class LiveRoomManager(
    private val liveId: String
) {

    init {
        // 初始化两个核心管理器
        setupWelcomeMessageFlow()
    }

    private fun setupWelcomeMessageFlow() {
        // 1. 获取 LiveAudienceStore 的实例
        val audienceStore = LiveAudienceStore.create(liveId)

        // 2. 获取 BarrageStore 的实例 (得益于内部机制，这会是同一个实例)
        val barrageStore = BarrageStore.create(liveId)

        // 3. 订阅观众事件
        audienceStore.addLiveAudienceListener(object : LiveAudienceListener() {
            override fun onAudienceJoined(audience: LiveUserInfo) {
                // 4. 创建一条本地提示消息
                val welcomeTip = Barrage().apply {
                    liveID = liveId
                    messageType = BarrageType.TEXT
                    textContent = "欢迎 ${audience.userName} 进入直播间！"
                }

                // 5. 调用 BarrageStore 的接口将其插入弹幕列表
                barrageStore.appendLocalTip(welcomeTip)
            }
        })
    }
}
```

## API 文档
关于 LiveAudienceStore 及其相关类的所有公开接口、属性和方法的详细信息，请参阅随 AtomicX Core 框架的官方 API 文档。本文档使用到的相关 Store 如下：
| Store/Component | 功能描述 | - |
| --- | --- | --- |
| LiveCoreView | 直播视频流展示与交互的核心视图组件：负责视频流渲染和视图挂件处理，支持主播直播、观众连麦、主播连线等场景。 |  |
| LiveAudienceStore | 观众管理：获取实时观众列表（ID / 名称 / 头像），统计观众数量，监听观众进出事件。 |  |
| BarrageStore | 弹幕功能：发送文本 / 自定义弹幕，维护弹幕列表，实时监听弹幕状态。 |  |

## Platform: Android

## 实现指南

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并获取观众列表

获取一个与当前直播间 liveID 绑定的 `LiveAudienceStore` 实例，并主动拉取一次当前的观众列表，用于首次展示。
``` kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.LiveAudienceStore
import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo

class AudienceManager(
    private val liveId: String
) {
    private val audienceStore: LiveAudienceStore = LiveAudienceStore.create(liveId)
    private val scope = CoroutineScope(Dispatchers.Main)

    // 对外暴露【全量】观众列表的状态流，方便UI层订阅
    private val _audienceList = MutableStateFlow<List<LiveUserInfo>>(emptyList())
    val audienceList: StateFlow<List<LiveUserInfo>> = _audienceList.asStateFlow()
    
    // 对外暴露观众总数的状态流
    private val _audienceCount = MutableStateFlow(0)
    val audienceCount: StateFlow<Int> = _audienceCount.asStateFlow()
    
    init {
        // 1. 订阅状态和事件，实现见下一节
        subscribeToAudienceState()
        subscribeToAudienceEvents()

        // 2. 主动拉取首屏数据
        fetchInitialAudienceList()
    }

    /// 主动获取一次观众列表快照
    private fun fetchInitialAudienceList() {
        audienceStore.fetchAudienceList(object : CompletionHandler {
            override fun onSuccess() {
                println("首次拉取观众列表成功")
                // 成功后，数据会通过下面的 state 订阅通道自动更新
            }

            override fun onFailure(code: Int, desc: String) {
                println("首次拉取观众列表失败: $desc")
            }
        })
    }
    
    // ... 后续代码
}
```

### 步骤3：监听观众列表状态与实时动态

订阅 `audienceStore` 的 `liveAudienceState` 和添加 `LiveAudienceListener`，以接收全量列表快照和实时的观众进出事件，从而驱动UI更新。
``` kotlin
class AudienceManager {
    /// 订阅 state，用于获取观众总数和列表快照
    private fun subscribeToAudienceState() {
        scope.launch {
            // audienceList 是一个全量快照
            audienceStore.liveAudienceState.audienceList.collect { audienceList ->
                _audienceList.value = audienceList
            }
        }

        scope.launch {   
            // audienceCount 是实时总数
            audienceStore.liveAudienceState.audienceCount.collect { count ->
                _audienceCount.value = count
            }
        }
    }

    /// 订阅 event，用于处理观众的实时进出事件
    private fun subscribeToAudienceEvents() {
        audienceStore.addLiveAudienceListener(object : LiveAudienceListener() {
            override fun onAudienceJoined(newAudience: LiveUserInfo) {
                println("观众 ${newAudience.userName} 加入了直播间")
                // 增量更新：在当前列表末尾添加新观众
                val currentList = _audienceList.value.toMutableList()
                if (!currentList.any { it.userID == newAudience.userID }) {
                    currentList.add(newAudience)
                    _audienceList.value = currentList
                }
            }

            override fun onAudienceLeft(departedAudience: LiveUserInfo) {
                println("观众 ${departedAudience.userName} 离开了直播间")
                // 增量更新：从当前列表中移除离开的观众
                val currentList = _audienceList.value.toMutableList()
                currentList.removeAll { it.userID == departedAudience.userID }
                _audienceList.value = currentList
            }
        })
    }
}
```

### 步骤4：管理观众（踢人与设置管理员）

作为主播或管理员，对直播间内的观众进行管理操作。

#### 4.1 将观众踢出直播间

调用 `kickUserOutOfRoom` 接口可以将指定用户请出直播间。
``` kotlin
class AudienceManager {
    fun kick(userId: String) {
        audienceStore.kickUserOutOfRoom(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("成功将用户 $userId 踢出房间")
                // 踢出成功后，您会收到 onAudienceLeft 事件
            }

            override fun onFailure(code: Int, desc: String) {
                println("踢出用户 $userId 失败: $desc")
            }
        })
    }
}
```

#### 4.2 设置/撤销管理员

通过 `setAdministrator` 和 `revokeAdministrator` 接口可以管理用户的管理员身份。
``` kotlin
class AudienceManager {
    /// 将用户设为管理员
    fun promoteToAdmin(userId: String) {
        audienceStore.setAdministrator(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("成功将用户 $userId 设为管理员")
            }
            
            override fun onFailure(code: Int, desc: String) {
                println("设置管理员失败: $desc")
            }
        })
    }

    /// 撤销用户的管理员身份
    fun revokeAdmin(userId: String) {
        audienceStore.revokeAdministrator(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("成功撤销用户 $userId 的管理员身份")
            }

            override fun onFailure(code: Int, desc: String) {
                println("撤销管理员失败: $desc")
            }
        })
    }
}
```

## Platform: iOS

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并获取观众列表

获取一个与当前直播间 liveId 绑定的 `LiveAudienceStore` 实例，并主动拉取一次当前的观众列表，用于首次展示。
``` swift
import Foundation
import AtomicXCore 
import Combine     

class AudienceManager {
    private let liveId: String
    private let audienceStore: LiveAudienceStore
    private var cancellables = Set<AnyCancellable>()
    
    // 对外暴露【全量】观众列表的发布者，方便UI层订阅
    let audienceListPublisher = CurrentValueSubject<[LiveUserInfo], Never>([])
    // 对外暴露观众总数的发布者
    let audienceCountPublisher = PassthroughSubject<UInt, Never>()

    init(liveId: String) {
        self.liveId = liveId
        // 1. 通过 liveId 获取 LiveAudienceStore 的实例
        self.audienceStore = LiveAudienceStore.create(liveID: liveId)
        
        // 2. 订阅状态和事件
        subscribeToAudienceState()
        subscribeToAudienceEvents()
        
        // 3. 主动拉取首屏数据
        fetchInitialAudienceList()
    }

    /// 主动获取一次观众列表快照
    func fetchInitialAudienceList() {
        audienceStore.fetchAudienceList { [weak self] result in
            switch result {
            case .success:
                print("首次拉取观众列表成功")
                // 成功后，数据会通过下面的 state 订阅通道自动更新
            case .failure(let error):
                print("首次拉取观众列表失败: \(error.localizedDescription)")
            }
        }
    }
    
    // ... 后续代码
}
```

### 步骤3：监听观众列表状态与实时动态

订阅 `audienceStore` 的 `state` 和 `event`，以接收全量列表快照和实时的观众进出事件，从而驱动 UI 更新。
``` swift
extension AudienceManager {
    /// 订阅 state，用于获取观众总数和列表快照
    private func subscribeToAudienceState() {
        audienceStore.state
            .subscribe()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] state in
                // state.audienceList 是一个全量快照
                self?.audienceListPublisher.send(state.audienceList)
                // state.audienceCount 是实时总数
                self?.audienceCountPublisher.send(state.audienceCount)
            }
            .store(in: &cancellables)
    }

    /// 订阅 event，用于处理观众的实时进出
    private func subscribeToAudienceEvents() {
        audienceStore.liveAudienceEventPublisher
            .receive(on: DispatchQueue.main)
            .sink { [weak self] event in
                guard let self = self else { return }
                
                var currentList = self.audienceListPublisher.value
                
                switch event {
                case .onAudienceJoined(let newAudience):
                    print("观众 \(newAudience.userName) 加入了直播间")
                    // 增量更新：在当前列表末尾添加新观众
                    if !currentList.contains(where: { $0.userID == newAudience.userID }) {
                        currentList.append(newAudience)
                        self.audienceListPublisher.send(currentList)
                    }
                    
                case .onAudienceLeft(let departedAudience):
                    print("观众 \(departedAudience.userName) 离开了直播间")
                    // 增量更新：从当前列表中移除离开的观众
                    currentList.removeAll { $0.userID == departedAudience.userID }
                    self.audienceListPublisher.send(currentList)
                }
            }
            .store(in: &cancellables)
    }
}
```

### 步骤4：管理观众（踢人与设置管理员）

作为主播或管理员，对直播间内的观众进行管理操作。

#### 4.1 将观众踢出直播间

调用 `kickUserOutOfRoom` 接口可以将指定用户请出直播间。
``` swift
extension AudienceManager {
    func kick(userId: String) {
        audienceStore.kickUserOutOfRoom(userID: userId) { result in
            switch result {
            case .success:
                print("成功将用户 \(userId) 踢出房间")
                // 踢出成功后，您会收到 onAudienceLeft 事件
            case .failure(let error):
                print("踢出用户 \(userId) 失败: \(error.localizedDescription)")
            }
        }
    }
}
```

#### 4.2 设置/撤销管理员

通过 `setAdministrator` 和 `revokeAdministrator` 接口可以管理用户的管理员身份。
``` swift
extension AudienceManager {
    /// 将用户设为管理员
    func promoteToAdmin(userId: String) {
        audienceStore.setAdministrator(userID: userId) { result in
            if case .success = result {
                print("成功将用户 \(userId) 设为管理员")
            }
        }
    }

    /// 撤销用户的管理员身份
    func revokeAdmin(userId: String) {
        audienceStore.revokeAdministrator(userID: userId) { result in
            if case .success = result {
                print("成功撤销用户 \(userId) 的管理员身份")
            }
        }
    }
}
```

## Platform: Flutter

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并获取观众列表

获取一个与当前直播间 liveId 绑定的 `LiveAudienceStore` 实例，并主动拉取一次当前的观众列表，用于首次展示。

在退出直播间时可通过调用 `AudienceManager` 的 `dispose` 方法回收资源。
``` java
import 'package:atomic_x_core/atomicxcore.dart';

class AudienceManager {
    final String liveId;
    late final LiveAudienceStore audienceStore;
    late LiveAudienceListener _audienceListener;
    late final VoidCallback _audienceListChangedListener = _onAudienceListChanged;
    late final VoidCallback _audienceCountChangedListener = _onAudienceCountChanged;

    // 对外暴露【全量】观众列表
    List<LiveUserInfo> audienceList = [];
    // 对外暴露观众总数
    int audienceCount = 0;

    // 状态变更回调
    Function()? onStateChanged;

    AudienceManager({required this.liveId}) {
        // 1. 通过 liveId 获取 LiveAudienceStore 的实例
        audienceStore = LiveAudienceStore.create(liveId);

        // 2. 订阅状态和事件
        _subscribeToAudienceState();
        _subscribeToAudienceEvents();

        // 3. 主动拉取首屏数据
        fetchInitialAudienceList();
    }

    /// 主动获取一次观众列表快照
    Future<void> fetchInitialAudienceList() async {
        final result = await audienceStore.fetchAudienceList();
        if (result.isSuccess) {
            print("首次拉取观众列表成功");
            // 成功后，数据会通过下面的 state 订阅通道自动更新
        } else {
            print("首次拉取观众列表失败: ${result.errorMessage}");
        }
    }

    void dispose() {
        audienceStore.liveAudienceState.audienceList.removeListener(_audienceListChangedListener);
        audienceStore.liveAudienceState.audienceCount.removeListener(_audienceCountChangedListener);
        audienceStore.removeLiveAudienceListener(_audienceListener);
    }
}
```

观众列表等 `state` 订阅将在下一步骤详细讲解。

### 步骤3：监听观众列表状态与实时动态

订阅 `audienceStore` 的 `liveAudienceState` 和 `LiveAudienceListener`，以接收全量列表快照和实时的观众进出事件，从而驱动 UI 更新。
``` java
extension AudienceManagerSubscription on AudienceManager {
    /// 订阅 state，用于获取观众总数和列表快照
    void _subscribeToAudienceState() {
        // 监听观众列表变化
        audienceStore.liveAudienceState.audienceList.addListener(_audienceListChangedListener);
        // 监听观众人数变化
        audienceStore.liveAudienceState.audienceCount.addListener(_audienceCountChangedListener);
    }

    void _onAudienceListChanged() {
        // audienceList 是一个全量快照
        audienceList = audienceStore.liveAudienceState.audienceList.value;
        onStateChanged?.call();
    }

    void _onAudienceCountChanged() {
        // audienceCount 是实时总数
        audienceCount = audienceStore.liveAudienceState.audienceCount.value;
        onStateChanged?.call();
    }

    /// 订阅 event，用于处理观众的实时进出
    void _subscribeToAudienceEvents() {
        _audienceListener = LiveAudienceListener(
            onAudienceJoined: (audience) {
                print("观众 ${audience.userName} 加入了直播间");
                // 增量更新：在当前列表末尾添加新观众
                if (!audienceList.any((a) => a.userID == audience.userID)) {
                    audienceList.add(audience);
                    onStateChanged?.call();
                }
            },
            onAudienceLeft: (audience) {
                print("观众 ${audience.userName} 离开了直播间");
                // 增量更新：从当前列表中移除离开的观众
                audienceList.removeWhere((a) => a.userID == audience.userID);
                onStateChanged?.call();
            },
        );
        audienceStore.addLiveAudienceListener(_audienceListener);
    }
}
```

### 步骤4：管理观众（踢人与设置管理员）

作为主播或管理员，对直播间内的观众进行管理操作。

#### 4.1 将观众踢出直播间

调用 `kickUserOutOfRoom` 接口可以将指定用户请出直播间。
``` java
extension AudienceManagerActions on AudienceManager {
    Future<void> kick(String userId) async {
        final result = await audienceStore.kickUserOutOfRoom(userId);
        if (result.isSuccess) {
            print("成功将用户 $userId 踢出房间");
            // 踢出成功后，您会收到 onAudienceLeft 事件
        } else {
            print("踢出用户 $userId 失败: ${result.errorMessage}");
        }
    }
}
```

#### 4.2 设置/撤销管理员

通过 `setAdministrator` 和 `revokeAdministrator` 接口可以管理用户的管理员身份。
``` java
extension AudienceManagerAdmin on AudienceManager {
    /// 将用户设为管理员
    Future<void> promoteToAdmin(String userId) async {
        final result = await audienceStore.setAdministrator(userId);
        if (result.isSuccess) {
            print("成功将用户 $userId 设为管理员");
        }
    }

    /// 撤销用户的管理员身份
    Future<void> revokeAdmin(String userId) async {
        final result = await audienceStore.revokeAdministrator(userId);
        if (result.isSuccess) {
            print("成功撤销用户 $userId 的管理员身份");
        }
    }
}
```

## Platform: uni-app

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，完成接入。

### 步骤2：初始化并获取观众列表

获取一个与当前语聊房 liveID 绑定的 `LiveAudienceState` 实例，并主动拉取一次当前的观众列表，用于首次展示。
``` typescript
import { useLiveAudienceState } from "@/uni_modules/tuikit-atomic-x/state/LiveAudienceState";
import { onMounted, watch } from 'vue';

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

// 通过 liveID 获取 LiveAudienceState 的实例
const { fetchAudienceList, audienceList } = useLiveAudienceState(liveID)

onMounted(() => {
    fetchAudienceList({
      liveID: 'xxx', // 当前语聊房的 liveID
      success: () => {        
        // 成功后，响应式数据会自动更新
        console.log('首次拉取观众列表成功')
      },
      fail: (error) => {
        console.log('首次拉取观众列表失败', error)
      }
    })
})

// 监听 audienceList 的实时变更，用于驱动 UI 变更
watch(audienceList, (newVal, oldVal) => {
    console.log(newVal)
})

// ... 后续代码 
```

### 步骤3：监听观众列表状态与实时动态

订阅和监听 `audienceState` 的 `event` 和 `响应式数据`，用来接收全量列表快照和实时的观众进出事件，从而驱动 UI 更新。
``` typescript
import { useLiveAudienceState } from "@/uni_modules/tuikit-atomic-x/state/LiveAudienceState";
import { onMounted, watch } from 'vue';

const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID
//通过 liveID 获取 LiveAudienceState 的实例
const { audienceList, audienceCount, addAudienceListener, removeAudienceListener } = useLiveAudienceState(liveID)

// 监听 audienceList 的实时变更，用于驱动 UI 更新
watch(audienceList, (newVal, oldVal) => {
    console.log(newVal)
})

// 监听 audienceCount 的实时变更，用于驱动 UI 更新
watch(audienceCount, (newVal, oldVal) => {
    console.log(newVal)
})

onMounted(() => {
    addAudienceListener( liveID, 'onAudienceJoined', 
      { callback: (event) => {
        const newAudience = JSON.parse(event)
        console.log("观众 ${newAudience.userName} 加入了语聊房")
      }
    })
    
    addAudienceListener( liveID, 'onAudienceLeft',
      { callback: (event) => {
        const departedAudience = JSON.parse(event)
        console.log("观众 ${departedAudience.userName} 离开了语聊房")
      }
    })
})
```

### 步骤4：管理观众（踢人与设置管理员）

作为主播或管理员，对语聊房内的观众进行管理操作。

#### 4.1 将观众踢出语聊房

调用 `kickUserOutOfRoom` 接口可以将指定用户请出语聊房。
``` typescript
import { useLiveAudienceState } from "@/uni_modules/tuikit-atomic-x/state/LiveAudienceState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 LiveAudienceState 的实例
const { kickUserOutOfRoom } = useLiveAudienceState(liveID)

const kickOut = () => {
    kickUserOutOfRoom({
      liveID: 'xxx', // 当前语聊房的 liveID
      userID: 'xxx', // 想要踢出成员的 userID
      success: () => {
        console.log("成功将用户 ${userID} 踢出房间")
        // 踢出成功后，您会收到 onAudienceLeft 事件
      },
      fail: (error) => {
        console.log("将用户 ${userID} 踢出房间失败", error)
      } 
    })
}
```

#### 4.2 设置/撤销管理员

通过 `setAdministrator` 和 `revokeAdministrator` 接口可以管理用户的管理员身份。
``` typescript
import { useLiveAudienceState } from "@/uni_modules/tuikit-atomic-x/state/LiveAudienceState";

// liveID 是语聊房的唯一标识
const liveID = 'your_live_id'; // 替换为实际的语聊房 liveID

//通过 liveID 获取 LiveAudienceState 的实例
const { setAdministrator, revokeAdministrator } = useLiveAudienceState(liveID)

// 将用户设为管理员
const promoteToAdmin = () => {
    setAdministrator({
      liveID: 'xxx', // 当前语聊房的 liveID
      userID: 'xxx', // 想要设为管理员的 userID 
      success: () => {
        console.log("成功将用户 ${userID} 设为管理员")
      },
      fail: (error) => {
        console.log("将用户 ${userID} 设为管理员失败", error)
      }
    })
}

// 撤销用户的管理员身份
const revokeAdmin = () => {
    revokeAdministrator({
      liveID: 'xxx', // 当前语聊房的 liveID
      userID: 'xxx', // 想要撤销管理员的 userID 
      success: () => {
        console.log("成功将用户 ${userID} 的管理员身份撤销")
      },
      fail: (error) => {
        console.log("将用户 ${userID} 设为管理员身份撤销失败", error)
      }
    })

}
```

## 常见问题

### `LiveAudienceState` 中的在线人数（`audienceCount`）是如何更新的？时机和频率是怎样的？

> **适用平台：Android / iOS / Flutter / uni-app**

`audienceCount` 的更新并非总是实时的，其机制如下：
- **主动进出房间**：当用户主动加入或正常退出直播间时，在线人数的变更通知会即时触发。您会很快在 `LiveAudienceState` 中观察到 `audienceCount` 的变化。

- **异常掉线**：当用户因网络问题、应用崩溃等原因异常掉线时，系统需要通过心跳机制来判断其真实状态。只有当该用户连续 90 秒没有心跳后，系统才会判定其为离线，并触发人数变更通知。

- **更新机制与频率控制：**

  - 无论是即时触发还是延迟触发，所有的人数变更通知都是以消息的形式在房间内广播的。

  - 房间内每秒的消息总量有上限，单房间消息频控是**每秒 40 条消息**。

  - **关键点：**在“弹幕风暴”或礼物刷屏等消息流量极高的场景下，如果房间内的消息速率超过了 40条/秒 的阈值，为了保证核心消息（例如弹幕）的送达，**人数变更的消息可能会被频控策略丢弃**。

      **这对开发者意味着什么？**

      `audienceCount` 是一个非常接近实时的**高精度估算值**，但在极端高并发场景下，它可能存在短暂的延迟或数据丢失。因此，我们建议您将其用于 **UI 展示**，而不应作为计费、统计等需要绝对精准场景的唯一依据。
