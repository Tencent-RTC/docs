> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter**

**AtomicXCore** 提供了 CoHostStore 和 BattleStore 两个核心模块，分别用于处理**跨房连线和 PK 对战**。本文档将指导您如何组合使用这两个工具，来完成直播场景下连线到 PK 的完整流程。

## 功能进阶
### 通过 REST API 实现 PK 分数更新

通常在**直播主播 PK 场景**下，会将主播收到的礼物价值与 PK 数值挂钩（例如：观众送 "火箭" 礼物，主播 PK 分数增加 500 分），您可以通过我们的 REST API，轻松实现直播 PK 场景下的分数实时更新。

> **重要说明：**
> 

> LiveKit 后台的 PK 分数系统采用纯数值计算和累加机制，所以您需要根据自身的运营策略和业务需求，在调用更新接口前完成 PK 分数的计算，您可以参考如下的 PK 分数计算示例：
> 
| 礼物类型 | 分数计算规则 | 示例 |
| --- | --- | --- |
| ​基础礼物​ | 礼物价值 × 5 | 10元礼物 → 50分 |
| ​中级礼物​ | 礼物价值 × 8 | 50元礼物 → 400分 |
| ​高级礼物​ | 礼物价值 × 12 | 100元礼物 → 1200分 |
| ​特效礼物​ | 固定高分数 | 520元礼物 → 1314分 |

#### REST API 调用流程

#### 关键流程说明
1. ​**获取 PK 状态​**：

  - 回调配置​：您可以通过配置 PK 状态回调，由 LiveKit 后台在 PK 开始、结束时，主动通知您的系统 PK 状态。

  - 主动查询​：您的后台服务可主动调用 PK 状态查询 接口，随时查询当前 PK 状态。

2. **PK分数计算**​：您的后台服务根据业务规则（如上述示例），计算 PK 分数增量。

3. **PK分数更新​**：您的后台服务调用 修改 PK 分数 接口，向 LiveKit 后台更新 PK 分数。

4. **LiveKit后台 同步客户端**​：LiveKit 后台自动将更新后的 PK 分数同步到所有客户端。

#### 涉及的 REST API 接口
| 接口 | 功能描述 | 请求示例 |
| --- | --- | --- |
| 主动接口 - 查询 PK 状态 | 可根据此接口查询当前房间是否在 PK | 请求示例 |
| 主动接口 - 修改 PK 分数 | 将计算后的 PK 数值通过此接口更新 | 请求示例 |
| 回调配置 - PK 开始时回调 | 客户后台可以通过该回调及时知晓 PK 开启 | 回调示例 |
| 回调配置 - PK 结束时回调 | 客户后台可以通过该回调及时知晓 PK 结束 | 回调示例 |

## API 文档
关于 CoHostStore 及其相关类的所有公开接口、属性和方法的详细信息，请参阅随 AtomicXCore 框架的官方 API 文档。本指南使用到的相关 Store 如下：
| Store/Component | 功能描述 | - |
| --- | --- | --- |
| LiveCoreView | 直播视频流展示与交互的核心视图组件：负责视频流渲染和视图挂件处理，支持主播直播、观众连麦、主播连线等场景。 |  |
| DeviceStore | 音视频设备控制：麦克风（开关 / 音量）、摄像头（开关 / 切换 / 画质）、屏幕共享，设备状态实时监听。 |  |
| CoHostStore | 主播跨房连线：支持多布局模板（动态网格等），发起 / 接受 / 拒绝连线，连麦主播互动管理。 |  |
| BattleStore | 主播 PK 对战：发起 PK（配置时长 / 对手），管理 PK 状态（开始 / 结束），同步分数，监听对战结果。 |  |

## Platform: Android

## 核心场景

一次完整的“主播连线 PK”通常包含三个核心阶段，其整体流程如下：

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreView** 的接入。

### 步骤2：实现跨房连线

此步骤的目标是让两个主播的画面出现在同一个视图中，我们将使用 CoHostStore 来完成。

#### 邀请方（主播 A）实现
1. **发起连线邀请**

   当主播A在界面上选择目标主播B并发起连线时，调用 `requestHostConnection` 方法。

   ``` kotlin
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.CoHostLayoutTemplate
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   
   // 主播A的Activity
   class AnchorAActivity : AppCompatActivity() {
       private val liveId = "主播A的房间ID"
       private val coHostStore = CoHostStore.create(liveId)
   
       // 用户点击"连线"按钮，并选择了主播B
       fun inviteHostB(targetHostLiveId: String) {
           val layout = CoHostLayoutTemplate.HOST_DYNAMIC_GRID // 选择一个布局模板
           val timeout = 30 // 邀请超时时间（秒）
   
           coHostStore.requestHostConnection(
               targetHostLiveID = targetHostLiveId,
               layoutTemplate = layout,
               timeout = timeout,
               extraInfo = null,
               completion = object : CompletionHandler {
                   override fun onSuccess() { 
                       println("连线邀请已发送，等待对方处理...")
                   }
   
                   override fun onFailure(code: Int, desc: String) { 
                       println("邀请发送失败: $desc")
                   }
               }
           )
       }
   }
   ```
2. **监听邀请结果**

   通过 `CoHostListener` 的回调方法，您可以接收到主播 B 的处理结果。

   ``` java
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoHostListener
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   import io.trtc.tuikit.atomicxcore.api.live.SeatUserInfo
   
   class AnchorAActivity : AppCompatActivity() {
       private val liveId = "主播A的房间ID"
       private val coHostStore = CoHostStore.create(liveId)
       private val coHostListener = object : CoHostListener() {
           override fun onCoHostRequestAccepted(invitee: SeatUserInfo) {
               println("主播 ${invitee.userName} 同意了你的连线邀请")
           }
   
           override fun onCoHostRequestRejected(invitee: SeatUserInfo) {
               println("主播 ${invitee.userName} 拒绝了你的邀请")
           }
   
           override fun onCoHostRequestTimeout(inviter: SeatUserInfo, invitee: SeatUserInfo) { 
               println("邀请超时，对方未回应")
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_anchor_a)
           
           // 初始化CoHostStore
           coHostStore.addCoHostListener(coHostListener)
       }
   }
   ```

#### 受邀方（主播 B）实现
1. **接收连线邀请**

   通过 `CoHostListener`，主播B可以监听到来自主播A的邀请。

   ``` kotlin
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoHostListener
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   import io.trtc.tuikit.atomicxcore.api.live.SeatUserInfo
   
   // 主播B的Activity
   class AnchorBActivity : AppCompatActivity() {
       private val liveId = "主播B的房间ID"
       private val coHostStore = CoHostStore.create(liveId)
       
       private val coHostListener = object : CoHostListener() {
           override fun onCoHostRequestReceived(inviter: SeatUserInfo, extensionInfo: String) {
               println("收到主播 ${inviter.userName} 的连线邀请")
               // 显示邀请对话框,响应联线邀请
               // showInvitationDialog(inviter)
           }
       }
   }
   ```
2. **响应连线邀请**

   当主播B在弹出的对话框中做出选择后，调用相应的方法。

   ``` kotlin
   // AnchorBActivity 的一部分
   fun acceptInvitation(fromHostLiveId: String) {
       coHostStore.acceptHostConnection(fromHostLiveID = fromHostLiveId, completion = null)
   }
   
   fun rejectInvitation(fromHostLiveId: String) {
       coHostStore.rejectHostConnection(fromHostLiveID = fromHostLiveId, completion = null)
   }
   ```

### 步骤3：实现主播 PK

连线成功后，任意一方都可以发起 PK，此步骤我们将使用 BattleStore 来实现主播 PK。

#### 挑战方（例如主播 A）实现
1. **发起 PK 挑战**

   当主播 A 点击** PK** 按钮时，调用 `requestBattle` 方法。

   ``` kotlin
   // AnchorAActivity 的一部分
   class AnchorAActivity : AppCompatActivity() {
       private val liveId = "主播A的房间ID"
       private val battleStore = BattleStore.create(liveId)
   
       fun startPK(opponentUserId: String) {
           val config = BattleConfig(duration = 300) // PK持续5分钟
           battleStore.requestBattle(
               config = config,
               userIDList = listOf(opponentUserId),
               timeout = 30,
               completion = null
           )
       }
   }
   ```
2. **监听 PK 状态**

   通过 `BattleListener` 监听 PK 的开始、结束等关键事件。

   ``` java
    // AnchorAActivity 的一部分
   class AnchorAActivity : AppCompatActivity() {
       private val liveId = "主播A的房间ID"
       private val battleStore = BattleStore.create(liveId)
       
       private val battleListener = object : BattleListener() {
           override fun onBattleStarted(
               battleInfo: BattleInfo,
               inviter: SeatUserInfo,
               invitees: List<SeatUserInfo>
           ) {
               println("PK 开始")
           }
   
           override fun onBattleEnded(battleInfo: BattleInfo, reason: BattleEndedReason?) {
               println("PK 结束")
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // ... 其他初始化代码 ...
           battleStore.addBattleListener(battleListener)
       }
   }
   ```

#### 应战方（主播 B）实现
1. **接收 PK 挑战**

   通过 `BattleListener` 监听到 PK 邀请。

   ``` kotlin
   // 在 AnchorBActivity 中添加
   class AnchorBActivity : AppCompatActivity() {
       private val liveId = "主播B的房间ID"
       private val battleStore = BattleStore.create(liveId)
   
       private val battleListener = object : BattleListener() {
           override fun onBattleRequestReceived(battleID: String, inviter: SeatUserInfo, invitee: SeatUserInfo) {
               println("收到主播 ${inviter.userName} 的PK挑战")
               // 弹出对话框，让主播B选择"接受"或"拒绝"
               // showPKChallengeDialog(battleID, inviter)
               }
           }
       
           override fun onCreate(savedInstanceState: Bundle?) {
               super.onCreate(savedInstanceState)
               // ... 其他初始化代码 ...
               battleStore.addBattleListener(battleListener)
           }
   }
   ```
2. **响应 PK 挑战**

   当主播 B 做出选择后，调用相应的方法。

   ``` kotlin
   // AnchorBActivity 的一部分
   // 用户点击"接受挑战"
   fun acceptPK(battleId: String) {
       battleStore.acceptBattle(battleID = battleId, completion = null)
   }
   
   // 用户点击"拒绝挑战"
   fun rejectPK(battleId: String) {
       battleStore.rejectBattle(battleID = battleId, completion = null)
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用主播 A 和主播 B 进行对应操作，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制您想要的 UI 逻辑。

## 完善 UI 细节

您可以通过 `VideoViewAdapter` 接口提供的"插槽"能力，在视频流画面上添加自定义视图，用于显示昵称、头像、PK 进度条等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### 实现效果

#### 实现方式
- **步骤1**：创建前景视图 (**CustomSeatView**)，该视图用于在视频流上方显示用户信息。
   

   > **提示：**
   > 

   > 您也可以参考 TUILiveKit 开源项目中的 [widgets](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/cohost/widgets) 目录下文件来了解完整的实现逻辑。
   > 

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.widget.LinearLayout
   import android.widget.TextView
   
   // 自定义的用户信息悬浮视图（前景）
   class CustomSeatView(context: Context) : LinearLayout(context) {
       private val nameLabel: TextView
   
       init {
           orientation = VERTICAL
           setBackgroundColor(Color.parseColor("#80000000")) // 半透明黑色背景
   
           nameLabel = TextView(context).apply {
               setTextColor(Color.WHITE)
               textSize = 14f
               gravity = Gravity.CENTER
           }
   
           addView(nameLabel)
           val layoutParams = nameLabel.layoutParams as LayoutParams
           layoutParams.setMargins(5, 0, 5, 5)
       }
   
       fun setUserName(userName: String) {
           nameLabel.text = userName
       }
   }
   ```
- **步骤2**：创建背景视图 (**CustomAvatarView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.widget.ImageView
   import android.widget.LinearLayout
   
   // 自定义的头像占位视图（背景）
   class CustomAvatarView(context: Context) : LinearLayout(context) {
       private val avatarImageView: ImageView
   
       init {
           orientation = VERTICAL
           gravity = Gravity.CENTER
           setBackgroundColor(Color.TRANSPARENT)
   
           avatarImageView = ImageView(context).apply {
               setColorFilter(Color.GRAY)
               scaleType = ImageView.ScaleType.CENTER_CROP
           }
   
           addView(avatarImageView)
           val layoutParams = avatarImageView.layoutParams as LayoutParams
           layoutParams.width = 120 // 60dp
           layoutParams.height = 120 // 60dp
       }
   }
   ```
- **步骤3**：实现 `VideoViewAdapter.createCoHostView` 协议，根据 `viewLayer` 的值返回对应的视图。

   ``` kotlin
   import com.tencent.cloud.tuikit.engine.room.TUIRoomDefine
   import android.view.View
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.view.VideoViewAdapter
   import io.trtc.tuikit.atomicxcore.api.view.ViewLayer
   
   // 1. 在您的Activity中，实现 VideoViewAdapter 接口
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       // ... 其他代码 ...
   
       // 2. 完整实现接口方法，处理两种 viewLayer
       override fun createCoHostView(coHostUser: TUIRoomDefine.SeatFullInfo?, viewLayer: ViewLayer?): View? {
           val seatInfo = coHostUser ?: return null
           val userId = seatInfo.userId
           if (userId.isNullOrEmpty()) {
               return null
           }
   
           return when (viewLayer) {
               ViewLayer.FOREGROUND -> {
                   val seatView = CustomSeatView(this)
                   seatView.setUserName(seatInfo.userName ?: "")
                   seatView
               }
               ViewLayer.BACKGROUND -> {
                   val avatarView = CustomAvatarView(this)
                   // 您可以在这里通过 seatInfo.userAvatar 加载用户真实头像
                   avatarView
               }
               null -> null
           }
       }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `seatInfo` | `SeatFullInfo?` | 麦位信息对象，包含麦上用户的详细信息 |
| `seatInfo.userId` | `String` | 麦上用户的 ID |
| `seatInfo.userName` | `String` | 麦上用户的昵称 |
| `seatInfo.userAvatar` | `String` | 麦上用户的头像 URL |
| `seatInfo.userMicrophoneStatus` | `DeviceStatus` | 麦上用户的麦克风状态 |
| `seatInfo.userCameraStatus ` | `DeviceStatus` | 麦上用户的摄像头状态 |
| `viewLayer` | `ViewLayer` | 视图层级枚举 - `FOREGROUND` 表示前景挂件视图，始终显示在视频画面的最上层 - `BACKGROUND` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图 |

### 实现 PK 用户视图的分数展示

当主播开始 PK 后，可以在对方主播的视频画面上挂载自定义视图，通常用于展示该主播收到的礼物价值或其它 PK 相关信息。

#### **实现效果**

#### 实现方式
- **步骤1**：创建自定义 PK 用户视图，您可以参考 TUILiveKit 开源项目中的 [BattleMemberInfoView.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/battle/widgets/BattleMemberInfoView.kt) 文件来了解完整的实现逻辑。

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.widget.LinearLayout
   import android.widget.TextView
   import com.tencent.cloud.tuikit.engine.extension.TUILiveBattleManager.BattleUser
   import io.trtc.tuikit.atomicxcore.api.live.BattleStore
   import kotlinx.coroutines.CoroutineScope
   import kotlinx.coroutines.Dispatchers
   import kotlinx.coroutines.launch
   
   // 自定义PK 用户视图
   class CustomBattleUserView(
       context: Context,
       private val liveId: String,
       private val battleUser: BattleUser
   ) : LinearLayout(context) {
   
       private lateinit var scoreView: LinearLayout
       private lateinit var scoreLabel: TextView
       private val battleStore: BattleStore
   
       init {
           orientation = VERTICAL
           gravity = Gravity.BOTTOM or Gravity.END
           setBackgroundColor(Color.TRANSPARENT)
           isClickable = false
   
           battleStore = BattleStore.create(liveId)
   
           // UI 布局
           setupUI()
           // 订阅分数变化
           subscribeBattleState()
       }
   
       private fun setupUI() {
           scoreView = LinearLayout(context).apply {
               setBackgroundColor(Color.parseColor("#66000000"))
               orientation = VERTICAL
               gravity = Gravity.CENTER
           }
   
           scoreLabel = TextView(context).apply {
               setTextColor(Color.WHITE)
               textSize = 14f
               gravity = Gravity.CENTER
           }
   
           scoreView.addView(scoreLabel)
           addView(scoreView)
   
           val layoutParams = scoreView.layoutParams as LayoutParams
           layoutParams.width = LayoutParams.WRAP_CONTENT
           layoutParams.height = 48 // 24dp
           layoutParams.setMargins(0, 0, 10, 10)
       }
   
       // 订阅 PK 分数变化
       private fun subscribeBattleState() {
           CoroutineScope(Dispatchers.Main).launch {
               battleStore.battleState.battleScore.collect { battleScore ->
                   val score = battleScore[battleUser.userId] ?: 0
                   // 更新 UI
                   scoreLabel.text = score.toString()
               }
           }
       }
   }
   ```
- **步骤2**：实现 `VideoViewAdapter.createBattleView` 协议

   ``` kotlin
     // 1. 让您的Activity实现 VideoViewAdapter 接口
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       override fun createBattleView(battleUser: BattleUser?): View? {
           battleUser ?: return null
           // CustomBattleUserView 是您自定义的PK用户信息视图
           return CustomBattleUserView(this, liveId, battleUser)
       }
   
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `battleUser` | `BattleUser?` | PK 用户信息对象 |
| `battleUser.roomId` | `String` | PK 的房间 ID |
| `battleUser.userId` | `String` | PK 用户 ID |
| `battleUser.userName` | `String` | PK 用户昵称 |
| `battleUser.avatarUrl` | `String` | PK 用户头像地址 |
| `battleUser.score` | `int` | PK 分数 |

### 实现视频流画面上的 PK 状态显示

#### **实现效果**

#### **实现方式**
- **步骤1：**创建自定义 PK 全局视图 **CustomBattleContainerView**，您可以参考 TUILiveKit 开源项目中的 [BattleInfoView.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/battle/widgets/BattleInfoView.kt) 文件来实现，即可实现同样的效果。

- **步骤2**：实现 `VideoViewAdapter.createBattleContainerView` 协议。

   ``` kotlin
   // 让您的Activity实现 VideoViewAdapter 接口并设置适配器
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
       override fun createBattleContainerView(): View? {
           return CustomBattleContainerView(this)
       }
   }
   ```

## Platform: iOS

## 核心场景

一次完整的“主播连线 PK”通常包含三个核心阶段，其整体流程如下：

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreView** 的接入。

### 步骤2：实现跨房连线

此步骤的目标是让两个主播的画面出现在同一个视图中，我们将使用 CoHostStore 来完成。

#### 邀请方（主播 A）实现
1. **发起连线邀请**

   当主播A在界面上选择目标主播 B 并发起连线时，调用 `requestHostConnection` 方法。

   ``` swift
   import AtomicXCore
   import Combine
   
   // 主播A的视图控制器
   class AnchorAViewController {
       private let liveId = "主播A的房间ID"
       private var cancellables: Set<AnyCancellable> = []
       private lazy var coHostStore: CoHostStore = {
           return CoHostStore.create(liveID: self.liveId)
       }()
   
       // 用户点击“连线”按钮，并选择了主播B
       func inviteHostB(targetHostLiveId: String) {
           let layout: CoHostLayoutTemplate = .hostDynamicGrid // 选择一个布局模板
           let timeout: TimeInterval = 30.0 // 邀请超时时间
   
           coHostStore.requestHostConnection(targetHost: targetHostLiveId,
                                             layoutTemplate: layout,
                                             timeout: timeout) { result in
               switch result {
               case .success():
                   print("连线邀请已发送，等待对方处理...")
               case .failure(let error):
                   print("邀请发送失败: \(error.message)")
               }
           }
       }
   }
   ```
2. **监听邀请结果**

   通过订阅 `coHostEventPublisher`，您可以接收到主播 B 的处理结果。

   ``` swift
   // 在 AnchorAViewController 初始化时设置监听
   func setupListeners() {
       coHostStore.coHostEventPublisher
           .sink { [weak self] event in
               switch event {
               case .onCoHostRequestAccepted(let invitee): //
                   print("主播 \(invitee.userName) 同意了你的连线邀请")
               case .onCoHostRequestRejected(let invitee): //
                   print("主播 \(invitee.userName) 拒绝了你的邀请")
               case .onCoHostRequestTimeout: //
                   print("邀请超时，对方未回应")
               default:
                   break
               }
           }
           .store(in: &cancellables)
   }
   ```

#### 受邀方（主播 B）实现
1. **接收连线邀请**

   通过 `coHostEventPublisher`，主播B可以监听到来自主播 A 的邀请。

   ``` swift
   import AtomicXCore
   import Combine
   
   // 主播B的视图控制器
   class AnchorBViewController {
       // ... coHostStore 和 cancellables 初始化 ...
   
       // 在初始化时设置监听
       func setupListeners() {
           coHostStore.coHostEventPublisher
               .sink { [weak self] event in
                   if case let .onCoHostRequestReceived(inviter, _) = event { //
                       print("收到主播 \(inviter.userName) 的连线邀请")
                       // self?.showInvitationDialog(from: inviter)
                   }
               }
               .store(in: &cancellables)
       }
   }
   ```
2. **响应连线邀请**

   当主播 B 在弹出的对话框中做出选择后，调用相应的方法。

   ``` swift
   // AnchorBViewController 的一部分
   func acceptInvitation(fromHostLiveId: String) {
       coHostStore.acceptHostConnection(fromHostLiveID: fromHostLiveId, completion: nil) //
   }
   
   func rejectInvitation(fromHostLiveId: String) {
       coHostStore.rejectHostConnection(fromHostLiveID: fromHostLiveId, completion: nil) //
   }
   ```

### 步骤3：实现主播 PK

连线成功后，任意一方都可以发起 PK，此步骤我们将使用 BattleStore 来实现主播 PK。

#### 挑战方（例如主播 A）实现
1. **发起 PK 挑战**

   当主播 A 点击“PK”按钮时，调用 `requestBattle` 方法。

   ``` swift
     
   // AnchorAViewController 的一部分
   private lazy var battleStore: BattleStore = BattleStore.create(liveID: self.liveId)
   
   func startPK(with opponentUserId: String) {
       var config = BattleConfig(duration: 300) // PK持续5分钟
       battleStore.requestBattle(config: config, userIDList: [opponentUserId], timeout: 30.0, completion: nil)
   }
   ```
2. **监听 PK 状态**

   通过 `battleEventPublisher` 监听 PK 的开始、结束等关键事件。

   ``` swift
   // 在 AnchorAViewController 的 setupListeners 方法中添加
   battleStore.battleEventPublisher
       .sink { [weak self] event in
           switch event {
           case .onBattleStarted: //
               print("PK 开始")
           case .onBattleEnded: //
               print("PK 结束")
           default:
               break
           }
       }
       .store(in: &cancellables)
   ```

#### 应战方（主播 B）实现
1. **接收 PK 挑战**

   通过 `battleEventPublisher` 监听到 PK 邀请。

   ``` swift
   // 在 AnchorBViewController 的 setupListeners 方法中添加
   battleStore.battleEventPublisher
       .sink { [weak self] event in
           if case let .onBattleRequestReceived(battleId, inviter, _) = event {
               print("收到主播 \(inviter.userName) 的PK挑战")
               // 弹出对话框，让主播B选择“接受”或“拒绝”
               // self?.showPKChallengeDialog(battleId: battleId)
           }
       }
       .store(in: &cancellables)
   ```
2. **响应 PK 挑战**

   当主播 B 做出选择后，调用相应的方法。

   ``` swift
   // AnchorBViewController 的一部分
   // 用户点击“接受挑战”
   func acceptPK(battleId: String) {
       battleStore.acceptBattle(battleID: battleId) { result in
           // ...
       }
   }
   
   // 用户点击“拒绝挑战”
   func rejectPK(battleId: String) {
       battleStore.rejectBattle(battleID: battleId) { result in
           // ...
       }
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用主播 A 和主播 B 进行对应操作，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制您想要的 UI 逻辑。

## 完善 UI 细节

您可以通过 `LiveCoreView.VideoViewDelegate` 协议提供的“插槽”能力，在视频流画面上添加自定义视图，用于显示昵称、头像、PK 进度条等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### 实现效果

#### 实现方式
- **步骤1**：创建前景视图 (**CustomSeatView**)，该视图用于在视频流上方显示用户信息。
   

   > **提示：**
   > 

   > 您也可以参考 TUILiveKit 开源项目中的 [AnchorCoHostView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorCoHostView.swift) 和 [AnchorEmptySeatView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorEmptySeatView.swift) 文件来了解完整的实现逻辑。
   > 

   ``` swift
   import UIKit
   import SnapKit
   
   // 自定义的用户信息悬浮视图（前景）
   class CustomSeatView: UIView {
      lazy var nameLabel: UILabel = {
           let label = UILabel()
           label.textColor = .white
           label.font = .systemFont(ofSize: 14)
           return label
       }()
   
       override init(frame: CGRect) {
           super.init(frame: frame)
           backgroundColor = UIColor.black.withAlphaComponent(0.5)
           addSubview(nameLabel)
           nameLabel.snp.makeConstraints { make in
               make.bottom.equalToSuperview().offset(-5)
               make.leading.equalToSuperview().offset(5)
           }
       }
   }
   ```
- **步骤2**：创建背景视图 (**CustomAvatarView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` swift
   import UIKit
   import SnapKit
   
   // 自定义的头像占位视图（背景）
   class CustomAvatarView: UIView {
       lazy var avatarImageView: UIImageView = {
           let imageView = UIImageView()
           imageView.tintColor = .gray
           return imageView
       }()
   
       override init(frame: CGRect) {
           super.init(frame: frame)
           backgroundColor = .clear
           layer.cornerRadius = 30
           addSubview(avatarImageView)
           avatarImageView.snp.makeConstraints { make in
               make.center.equalToSuperview()
               make.width.height.equalTo(60)
           }
       }
   }
   ```
- **步骤3**：实现 `VideoViewDelegate.createCoHostView` 协议，根据 `viewLayer` 的值返回对应的视图。

   ``` swift
   import AtomicXCore
   import RTCRoomEngine
   
   // 1. 在您的视图控制器中，遵守 VideoViewDelegate 协议
   class YourViewController: UIViewController, VideoViewDelegate {
   
       // ... 其他代码 ...
       
       // 2. 完整实现协议方法，处理两种 viewLayer
       func createCoHostView(seatInfo: TUISeatFullInfo, viewLayer: ViewLayer) -> UIView? {
           guard let userId = seatInfo.userId, !userId.isEmpty else {
               return nil
           }
           if viewLayer == .foreground {
               let seatView = CustomSeatView()
               seatView.nameLabel.text = seatInfo.userName
               return seatView
           } else { // viewLayer == .background
               let avatarView = CustomAvatarView()
               // 您可以在这里通过 seatInfo.userAvatar 加载用户真实头像
               return avatarView
           }
       }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `seatInfo` | `TUISeatFullInfo` | 麦位信息对象，包含麦上用户的详细信息 |
| `seatInfo.userId` | `String?` | 麦上用户的 ID |
| `seatInfo.userName` | `String? ` | 麦上用户的昵称 |
| `seatInfo.userAvatar` | `String?` | 麦上用户的头像 URL |
| `seatInfo.userMicrophoneStatus` | `TUIDeviceStatus` | 麦上用户的麦克风状态 |
| `seatInfo.userCameraStatus ` | `TUIDeviceStatus` | 麦上用户的摄像头状态 |
| `viewLayer` | `ViewLayer` | 视图层级枚举 `.foreground` 表示前景挂件视图，始终显示在视频画面的最上层 `.background` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图 |

### 实现 PK 用户视图的分数展示

当主播开始 PK 后，可以在对方主播的视频画面上挂载自定义视图，通常用于展示该主播收到的礼物价值或其它 PK 相关信息。

#### **实现效果**

#### 实现方式
- **步骤1**：创建自定义 PK 用户视图，您可以参考 TUILiveKit 开源项目中的 [AnchorBattleMemberInfoView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/HostBattle/View/AnchorBattleMemberInfoView.swift) 文件来了解完整的实现逻辑。

   ``` swift
   import AtomicXCore
   import RTCRoomEngine
   import SnapKit
   
   // 自定义PK 用户视图
   class CustomBattleUserView: UIView {
       private let scoreView: UIView = {
           let view = UIView()
           view.backgroundColor = .black.withAlphaComponent(0.4)
           view.layer.cornerRadius = 12
           return view
       }()
   
       private lazy var scoreLabel: UILabel = {
           let label = UILabel()
           label.textColor = .white
           label.font = .systemFont(ofSize: 14, weight: .bold)
           return label
       }()
       
       private var userId: String
       private let battleStore: BattleStore
       private var cancellableSet: Set<AnyCancellable> = []
       
       init(liveId: String, battleUser: TUIBattleUser) {
           self.userId = battleUser.userId
           self.battleStore = BattleStore.create(liveID: liveId)
           super.init(frame: .zero)
           backgroundColor = .clear
           isUserInteractionEnabled = false
           // UI 布局
           setupUI()
           // 订阅分数变化
           subscribeBattleState()
       }
       
       private func setupUI() {
           addSubView(scoreView)       
           scoreView.addSubview(scoreLabel)
           scoreLabel.snp.makeConstraints { make in
               make.leading.trailing.equalToSuperview().inset(5)
           }
           scoreView.snp.makeConstraints { make in
               make.height.equalTo(24)
               make.bottom.equalToSuperview().offset(-5)
               make.trailing.equalToSuperview().offset(-5)
           }
       }
       
       // 订阅 PK 分数变化
       private func subscribeBattleState() {
           battleStore.state
               .subscribe(StatePublisherSelector(keyPath: \BattleState.battleScore))
               .removeDuplicates()
               .receive(on: RunLoop.main)
               .sink { battleUsers in
                   guard let self = self else { return }
                   guard let score = battleScore[self.userId] else { return }
                   // 更新 UI 
                   self.scoreLabel.text = "\(battleUser.score)"
               }
               .store(in: &cancellableSet)
       }
   }
   ```
- **步骤2**：实现 `VideoViewDelegate.createBattleView` 协议

   ``` swift
     // 1. 让您的视图控制器遵守 VideoViewDelegate 协议
   extension YourViewController: VideoViewDelegate {
   
       public func createBattleView(battleUser: TUIBattleUser) -> UIView? {
           // CustomBattleUserView 是您自定义的PK用户信息视图
           let customView = CustomBattleUserView(liveId:liveId, battleUser:battleUser)
           return customView
       }
   
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `battleUser` | `TUIBattleUser` | PK 用户信息对象 |
| `battleUser.roomId` | `String` | PK 的房间 ID |
| `battleUser.userId` | `String` | PK 用户 ID |
| `battleUser.userName` | `String` | PK 用户昵称 |
| `battleUser.avatarUrl` | `String` | PK 用户头像地址 |
| `battleUser.score` | `UInt` | PK 分数 |

### 实现视频流画面上的 PK 状态显示

#### **实现效果**

#### **实现方式**
- **步骤1：**创建自定义 PK 全局视图 **CustomBattleContainerView**，您可以参考 TUILiveKit 开源项目中的 [AnchorBattleInfoView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/HostBattle/View/AnchorBattleInfoView.swift) 文件来实现，即可实现同样的效果。

- **步骤2**：实现 `VideoViewDelegate.createBattleContainerView` 协议。

   ``` swift
   // 让您的视图控制器遵守 VideoViewDelegate 协议并设置代理
   extension YourViewController: VideoViewDelegate {
       func createBattleContainerView() -> UIView? {
           return CustomBattleContainerView()
       }
   }
   ```

## Platform: Flutter

## 核心场景

一次完整的"主播连线 PK"通常包含三个核心阶段，其整体流程如下：

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreWidget** 的接入。

### 步骤2：实现跨房连线

此步骤的目标是让两个主播的画面出现在同一个视图中，我们将使用 CoHostStore 来完成。

#### 邀请方（主播 A）实现
1. **发起连线邀请**

   当主播A在界面上选择目标主播 B 并发起连线时，调用 `requestHostConnection` 方法。

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   // 主播A的页面
   class AnchorAPage extends StatefulWidget {
     final String liveId;
   
     const AnchorAPage({Key? key, required this.liveId}) : super(key: key);
   
     @override
     State<AnchorAPage> createState() => _AnchorAPageState();
   }
   
   class _AnchorAPageState extends State<AnchorAPage> {
     late final CoHostStore _coHostStore;
     late final CoHostListener _coHostListener;
   
     @override
     void initState() {
       super.initState();
       _coHostStore = CoHostStore.create(widget.liveId);
       _setupListeners();
     }
   
     // 用户点击"连线"按钮，并选择了主播B
     Future<void> inviteHostB(String targetHostLiveId) async {
       final layout = CoHostLayoutTemplate.hostDynamicGrid; // 选择一个布局模板
       const timeout = 30; // 邀请超时时间（秒）
   
       final result = await _coHostStore.requestHostConnection(
         targetHostLiveID: targetHostLiveId,
         layoutTemplate: layout,
         timeout: timeout,
       );
   
       if (result.isSuccess) {
         print('连线邀请已发送，等待对方处理...');
       } else {
         print('邀请发送失败: ${result.errorMessage}');
       }
     }
   
     @override
     void dispose() {
       _coHostStore.removeCoHostListener(_coHostListener);
       super.dispose();
     }
   }
   ```
2. **监听邀请结果**

   通过 `CoHostListener`，您可以接收到主播 B 的处理结果。

   ``` java
   // 在 _AnchorAPageState 初始化时设置监听
   void _setupListeners() {
     _coHostListener = CoHostListener(
       onCoHostRequestAccepted: (invitee) {
         print('主播 ${invitee.userName} 同意了你的连线邀请');
       },
       onCoHostRequestRejected: (invitee) {
         print('主播 ${invitee.userName} 拒绝了你的邀请');
       },
       onCoHostRequestTimeout: (inviter, invitee) {
         print('邀请超时，对方未回应');
       },
       onCoHostUserJoined: (userInfo) {
         print('主播 ${userInfo.userName} 已加入连线');
       },
       onCoHostUserLeft: (userInfo) {
         print('主播 ${userInfo.userName} 已离开连线');
       },
     );
     _coHostStore.addCoHostListener(_coHostListener);
   }
   ```

#### 受邀方（主播 B）实现
1. **接收连线邀请**

   通过 `CoHostListener`，主播B可以监听到来自主播 A 的邀请。

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   // 主播B的页面
   class AnchorBPage extends StatefulWidget {
     final String liveId;
   
     const AnchorBPage({Key? key, required this.liveId}) : super(key: key);
   
     @override
     State<AnchorBPage> createState() => _AnchorBPageState();
   }
   
   class _AnchorBPageState extends State<AnchorBPage> {
     late final CoHostStore _coHostStore;
     late final CoHostListener _coHostListener;
   
     @override
     void initState() {
       super.initState();
       _coHostStore = CoHostStore.create(widget.liveId);
       _setupListeners();
     }
   
     // 在初始化时设置监听
     void _setupListeners() {
       _coHostListener = CoHostListener(
         onCoHostRequestReceived: (inviter, extensionInfo) {
           print('收到主播 ${inviter.userName} 的连线邀请');
           // _showInvitationDialog(inviter);
         },
       );
       _coHostStore.addCoHostListener(_coHostListener);
     }
   
     @override
     void dispose() {
       _coHostStore.removeCoHostListener(_coHostListener);
       super.dispose();
     }
   }
   ```
2. **响应连线邀请**

   当主播 B 在弹出的对话框中做出选择后，调用相应的方法。

   ``` java
   // _AnchorBPageState 的一部分
   Future<void> acceptInvitation(String fromHostLiveId) async {
     final result = await _coHostStore.acceptHostConnection(fromHostLiveId);
     if (result.isSuccess) {
       print('已接受连线邀请');
     } else {
       print('接受连线失败: ${result.errorMessage}');
     }
   }
   
   Future<void> rejectInvitation(String fromHostLiveId) async {
     final result = await _coHostStore.rejectHostConnection(fromHostLiveId);
     if (result.isSuccess) {
       print('已拒绝连线邀请');
     } else {
       print('拒绝连线失败: ${result.errorMessage}');
     }
   }
   ```

### 步骤3：实现主播 PK

连线成功后，任意一方都可以发起 PK，此步骤我们将使用 BattleStore 来实现主播 PK。

#### 挑战方（例如主播 A）实现
1. **发起 PK 挑战**

   当主播 A 点击"PK"按钮时，调用 `requestBattle` 方法。

   ``` java
   // _AnchorAPageState 的一部分
   late final BattleStore _battleStore;
   late final BattleListener _battleListener;
   
   @override
   void initState() {
     super.initState();
     _coHostStore = CoHostStore.create(widget.liveId);
     _battleStore = BattleStore.create(widget.liveId);
     _setupListeners();
     _setupBattleListeners();
   }
   
   Future<void> startPK(String opponentUserId) async {
     final config = BattleConfig(duration: 300); // PK 持续 5 分钟
     final result = await _battleStore.requestBattle(
       config: config,
       userIDList: [opponentUserId],
       timeout: 30,
     );
   
     if (result.isSuccess) {
       print('PK 请求已发送，battleID: ${result.battleID}');
     } else {
       print('PK 请求失败: ${result.errorMessage}');
     }
   }
   ```
2. **监听 PK 状态**

   通过 `BattleListener` 监听 PK 的开始、结束等关键事件。

   ``` java
   // 在 _AnchorAPageState 的 _setupBattleListeners 方法中添加
   void _setupBattleListeners() {
     _battleListener = BattleListener(
       onBattleStarted: (battleInfo, inviter, invitees) {
         print('PK 开始');
       },
       onBattleEnded: (battleInfo, reason) {
         print('PK 结束，原因: $reason');
       },
       onUserJoinBattle: (battleID, battleUser) {
         print('用户 ${battleUser.userName} 加入了 PK');
       },
       onUserExitBattle: (battleID, battleUser) {
         print('用户 ${battleUser.userName} 退出了 PK');
       },
     );
     _battleStore.addBattleListener(_battleListener);
   }
   
   @override
   void dispose() {
     _coHostStore.removeCoHostListener(_coHostListener);
     _battleStore.removeBattleListener(_battleListener);
     super.dispose();
   }
   ```

#### 应战方（主播 B）实现
1. **接收 PK 挑战**

   通过 `BattleListener` 监听到 PK 邀请。

   ``` java
   // 在 _AnchorBPageState 的 _setupBattleListeners 方法中添加
   void _setupBattleListeners() {
     _battleListener = BattleListener(
       onBattleRequestReceived: (battleId, inviter, invitee) {
         print('收到主播 ${inviter.userName} 的PK挑战');
         // 弹出对话框，让主播B选择"接受"或"拒绝"
         // _showPKChallengeDialog(battleId);
       },
       onBattleStarted: (battleInfo, inviter, invitees) {
         print('PK 开始');
       },
       onBattleEnded: (battleInfo, reason) {
         print('PK 结束');
       },
     );
     _battleStore.addBattleListener(_battleListener);
   }
   ```
2. **响应 PK 挑战**

   当主播 B 做出选择后，调用相应的方法。

   ``` java
   // _AnchorBPageState 的一部分
   // 用户点击"接受挑战"
   Future<void> acceptPK(String battleId) async {
     final result = await _battleStore.acceptBattle(battleId);
     if (result.isSuccess) {
       print('已接受 PK 挑战');
     } else {
       print('接受 PK 失败: ${result.errorMessage}');
     }
   }
   
   // 用户点击"拒绝挑战"
   Future<void> rejectPK(String battleId) async {
     final result = await _battleStore.rejectBattle(battleId);
     if (result.isSuccess) {
       print('已拒绝 PK 挑战');
     } else {
       print('拒绝 PK 失败: ${result.errorMessage}');
     }
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用主播 A 和主播 B 进行对应操作，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制 UI 逻辑。

## 完善 UI 细节

您可以通过 `LiveCoreWidget` 的 `VideoWidgetBuilder` 参数提供的"插槽"能力，在视频流画面上添加自定义视图，用于显示昵称、头像、PK 进度条等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### 实现效果

#### 实现方式
- **步骤1**：创建前景视图 (**CustomCoHostForegroundView**)，该视图用于在视频流上方显示用户信息。
   

   > **提示：**
   > 

   > 您也可以参考 TUILiveKit 开源项目中的 [co_host_foreground_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/co_host/co_host_foreground_widget.dart) 和 [co_host_background_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/co_host/co_host_background_widget.dart) 文件来了解完整的实现逻辑。
   > 

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 自定义的连线主播信息悬浮视图（前景）
   class CustomCoHostForegroundView extends StatelessWidget {
     final SeatFullInfo seatInfo;
   
     const CustomCoHostForegroundView({
       Key? key, 
       required this.seatInfo,
     }) : super(key: key);
   
     @override
     Widget build(BuildContext context) {
       return Container(
         color: Colors.transparent,
         child: Align(
           alignment: Alignment.bottomLeft,
           child: Container(
             margin: const EdgeInsets.all(5.0),
             padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 4),
             decoration: BoxDecoration(
               color: Colors.black.withOpacity(0.5),
               borderRadius: BorderRadius.circular(12),
             ),
             child: Text(
               seatInfo.userInfo.userName,
               style: const TextStyle(
                 color: Colors.white,
                 fontSize: 14,
               ),
             ),
           ),
         ),
       );
     }
   }
   ```
- **步骤2**：创建背景视图 (**CustomCoHostBackgroundView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 自定义的连线主播头像占位视图（背景）
   class CustomCoHostBackgroundView extends StatelessWidget {
     final SeatFullInfo seatInfo;
   
     const CustomCoHostBackgroundView({
       Key? key, 
       required this.seatInfo,
     }) : super(key: key);
   
     @override
     Widget build(BuildContext context) {
       final avatarUrl = seatInfo.userInfo.avatarUrl;
       return Container(
         decoration: BoxDecoration(
           color: Colors.grey[800],
         ),
         child: Center(
           child: Column(
             mainAxisSize: MainAxisSize.min,
             children: [
               ClipOval(
                 child: avatarUrl.isNotEmpty
                     ? Image.network(
                         avatarUrl,
                         width: 60,
                         height: 60,
                         fit: BoxFit.cover,
                         errorBuilder: (context, error, stackTrace) {
                           return _buildDefaultAvatar();
                         },
                       )
                     : _buildDefaultAvatar(),
               ),
               const SizedBox(height: 8),
               Text(
                 seatInfo.userInfo.userName,
                 style: const TextStyle(
                   color: Colors.white,
                   fontSize: 12,
                 ),
               ),
             ],
           ),
         ),
       );
     }
   
     Widget _buildDefaultAvatar() {
       return Container(
         width: 60,
         height: 60,
         color: Colors.grey,
         child: const Icon(Icons.person, size: 40, color: Colors.white),
       );
     }
   }
   ```
- **步骤3**：通过 `VideoWidgetBuilder` 的 `coHostWidgetBuilder` 回调构建自定义视图，根据 `viewLayer` 的值返回对应的视图。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 带有自定义连线视图的直播页面
   class CustomCoHostLiveWidget extends StatefulWidget {
     final String liveId;
   
     const CustomCoHostLiveWidget({
       Key? key, 
       required this.liveId,
     }) : super(key: key);
   
     @override
     State<CustomCoHostLiveWidget> createState() => _CustomCoHostLiveWidgetState();
   }
   
   class _CustomCoHostLiveWidgetState extends State<CustomCoHostLiveWidget> {
     late LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
     }
   
     @override
     void dispose() {
       _controller.dispose();
       super.dispose();
     }
   
     /// 构建连线主播的自定义视图
     Widget _buildCoHostWidget(
       BuildContext context, 
       SeatFullInfo seatFullInfo, 
       ViewLayer viewLayer,
     ) {
       if (viewLayer == ViewLayer.foreground) {
         // 前景层：始终显示在视频画面的最上层，用于显示昵称等信息
         return CustomCoHostForegroundView(seatInfo: seatFullInfo);
       } else {
         // 背景层：仅在对应用户没有视频流时显示，用于显示头像占位图
         return CustomCoHostBackgroundView(seatInfo: seatFullInfo);
       }
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: LiveCoreWidget(
           controller: _controller,
           videoWidgetBuilder: VideoWidgetBuilder(
             coHostWidgetBuilder: _buildCoHostWidget,
           ),
         ),
       );
     }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `seatFullInfo` | `SeatFullInfo` | 麦位信息对象，包含麦上用户的详细信息 |
| `seatFullInfo.userInfo.userId` | `String` | 麦上用户的 ID |
| `seatFullInfo.userInfo.userName` | `String` | 麦上用户的昵称 |
| `seatFullInfo.userInfo.avatarUrl` | `String` | 麦上用户的头像 URL |
| `viewLayer` | `ViewLayer` | 视图层级枚举 `ViewLayer.foreground` 表示前景挂件视图，始终显示在视频画面的最上层 `ViewLayer.background` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图 |

### 实现 PK 用户视图的分数展示

当主播开始 PK 后，可以在对方主播的视频画面上挂载自定义视图，通常用于展示该主播收到的礼物价值或其它 PK 相关信息。

#### **实现效果**

#### 实现方式
- **步骤1**：创建自定义 PK 用户视图，您可以参考 TUILiveKit 开源项目中的 [battle_member_info_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/battle/battle_member_info_widget.dart) 文件来了解完整的实现逻辑。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 自定义 PK 用户视图
   class CustomBattleUserView extends StatefulWidget {
     final String liveId;
     final TUIBattleUser battleUser;
   
     const CustomBattleUserView({
       Key? key,
       required this.liveId,
       required this.battleUser,
     }) : super(key: key);
   
     @override
     State<CustomBattleUserView> createState() => _CustomBattleUserViewState();
   }
   
   class _CustomBattleUserViewState extends State<CustomBattleUserView> {
     late final BattleStore _battleStore;
     late final VoidCallback _scoreChangedListener = _onScoreChanged;
     int _score = 0;
   
     @override
     void initState() {
       super.initState();
       _battleStore = BattleStore.create(widget.liveId);
       _subscribeBattleState();
     }
   
     /// 订阅 PK 分数变化
     void _subscribeBattleState() {
       _battleStore.battleState.battleScore.addListener(_scoreChangedListener);
       // 初始化分数
       _updateScore(_battleStore.battleState.battleScore.value);
     }
   
     void _onScoreChanged() {
       _updateScore(_battleStore.battleState.battleScore.value);
     }
   
     void _updateScore(Map<String, int> battleScore) {
       final score = battleScore[widget.battleUser.userId] ?? 0;
       if (mounted && score != _score) {
         setState(() {
           _score = score;
         });
       }
     }
   
     @override
     void dispose() {
       _battleStore.battleState.battleScore.removeListener(_scoreChangedListener);
       super.dispose();
     }
   
     @override
     Widget build(BuildContext context) {
       return IgnorePointer(
         child: Align(
           alignment: Alignment.bottomRight,
           child: Container(
             margin: const EdgeInsets.all(5),
             padding: const EdgeInsets.symmetric(horizontal: 8, vertical: 4),
             decoration: BoxDecoration(
               color: Colors.black.withOpacity(0.4),
               borderRadius: BorderRadius.circular(12),
             ),
             child: Text(
               '$_score',
               style: const TextStyle(
                 color: Colors.white,
                 fontSize: 14,
                 fontWeight: FontWeight.bold,
               ),
             ),
           ),
         ),
       );
     }
   }
   ```
- **步骤2**：通过 `VideoWidgetBuilder` 的 `battleWidgetBuilder` 回调构建自定义 PK 视图。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 带有自定义 PK 视图的直播页面
   class CustomBattleLiveWidget extends StatefulWidget {
     final String liveId;
   
     const CustomBattleLiveWidget({
       Key? key, 
       required this.liveId,
     }) : super(key: key);
   
     @override
     State<CustomBattleLiveWidget> createState() => _CustomBattleLiveWidgetState();
   }
   
   class _CustomBattleLiveWidgetState extends State<CustomBattleLiveWidget> {
     late LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
     }
   
     @override
     void dispose() {
       _controller.dispose();
       super.dispose();
     }
   
     /// 构建 PK 用户的自定义视图
     Widget _buildBattleWidget(BuildContext context, TUIBattleUser battleUser) {
       return CustomBattleUserView(
         liveId: widget.liveId,
         battleUser: battleUser,
       );
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: LiveCoreWidget(
           controller: _controller,
           videoWidgetBuilder: VideoWidgetBuilder(
             battleWidgetBuilder: _buildBattleWidget,
           ),
         ),
       );
     }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `battleUser` | `TUIBattleUser` | PK 用户信息对象 |
| `battleUser.roomId` | `String` | PK 的房间 ID |
| `battleUser.userId` | `String` | PK 用户 ID |
| `battleUser.userName` | `String` | PK 用户昵称 |
| `battleUser.avatarUrl` | `String` | PK 用户头像地址 |
| `battleUser.score` | `int` | PK 分数 |

### 实现视频流画面上的 PK 状态显示

#### **实现效果**

#### **实现方式**
- **步骤1：**创建自定义 PK 全局视图 **CustomBattleContainerView**，您可以参考 TUILiveKit 开源项目中的 [battle_info_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/battle/battle_info_widget.dart) 文件来实现，即可实现同样的效果。

- **步骤2**：通过 `VideoWidgetBuilder` 的 `battleContainerWidgetBuilder` 回调构建自定义 PK 容器视图。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// 带有自定义 PK 容器视图的直播页面
   class CustomBattleContainerLiveWidget extends StatefulWidget {
     final String liveId;
   
     const CustomBattleContainerLiveWidget({
       Key? key, 
       required this.liveId,
     }) : super(key: key);
   
     @override
     State<CustomBattleContainerLiveWidget> createState() => _CustomBattleContainerLiveWidgetState();
   }
   
   class _CustomBattleContainerLiveWidgetState extends State<CustomBattleContainerLiveWidget> {
     late LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
     }
   
     @override
     void dispose() {
       _controller.dispose();
       super.dispose();
     }
   
     /// 构建 PK 容器视图
     Widget _buildBattleContainerWidget(BuildContext context) {
       // CustomBattleContainerView 是您自定义的 PK 全局视图
       return CustomBattleContainerView(liveId: widget.liveId);
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: LiveCoreWidget(
           controller: _controller,
           videoWidgetBuilder: VideoWidgetBuilder(
             battleContainerWidgetBuilder: _buildBattleContainerWidget,
           ),
         ),
       );
     }
   }
   ```

### 组合使用多个自定义视图

在实际场景中，您可能需要同时自定义连线主播视图、PK 用户视图和 PK 容器视图。以下示例展示了如何组合使用：
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';
import 'package:rtc_room_engine/rtc_room_engine.dart';

/// 完整的自定义视图直播页面
class FullCustomLiveWidget extends StatefulWidget {
  final String liveId;

  const FullCustomLiveWidget({
    Key? key, 
    required this.liveId,
  }) : super(key: key);

  @override
  State<FullCustomLiveWidget> createState() => _FullCustomLiveWidgetState();
}

class _FullCustomLiveWidgetState extends State<FullCustomLiveWidget> {
  late LiveCoreController _controller;

  @override
  void initState() {
    super.initState();
    _controller = LiveCoreController.create();
    _controller.setLiveID(widget.liveId);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  /// 构建连线主播的自定义视图
  Widget _buildCoHostWidget(
    BuildContext context, 
    SeatFullInfo seatFullInfo, 
    ViewLayer viewLayer,
  ) {
    if (viewLayer == ViewLayer.foreground) {
      return CustomCoHostForegroundView(seatInfo: seatFullInfo);
    } else {
      return CustomCoHostBackgroundView(seatInfo: seatFullInfo);
    }
  }

  /// 构建 PK 用户的自定义视图
  Widget _buildBattleWidget(BuildContext context, TUIBattleUser userInfo) {
    return CustomBattleUserView(
      liveId: widget.liveId,
      battleUser: userInfo,
    );
  }

  /// 构建 PK 容器视图
  Widget _buildBattleContainerWidget(BuildContext context) {
    return CustomBattleContainerView(liveId: widget.liveId);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: LiveCoreWidget(
        controller: _controller,
        videoWidgetBuilder: VideoWidgetBuilder(
          coHostWidgetBuilder: _buildCoHostWidget,
          battleWidgetBuilder: _buildBattleWidget,
          battleContainerWidgetBuilder: _buildBattleContainerWidget,
        ),
      ),
    );
  }
}
```

## 常见问题

### **为什么发起了连线邀请，对方却没收到？**

> **适用平台：Android / iOS / Flutter**

- 请检查 **targetHostLiveId** 是否正确，并且对方直播间处于正常开播状态。

- 检查网络连接是否通畅，邀请信令有30秒的默认超时时间。

### 连线或PK过程中，一方主播网络断开或App崩溃了怎么办？

> **适用平台：Android**

**CoHostStore** 和 **BattleStore** 内部都有心跳和超时检测机制。如果一方异常退出，另一方会通过 **onCoHostUserLeft** 或 **onUserExitBattle** 等事件收到通知，您可以根据这些事件来处理UI，例如提示"对方已掉线"并结束互动。

### 为什么 PK 分数只能通过 REST API 更新？

> **适用平台：Android / iOS / Flutter**

因为 REST API  能同时满足 PK 分数的**安全性、实时性、扩展性**需求：
- **防篡改保公平**：需鉴权 + 数据校验，每笔更新可追溯来源（例如礼物行为），杜绝手动改分、刷分，保障竞技公平；

- **多端实时同步**：用标准化格式（例如 JSON）快速对接礼物、PK、展示系统，确保主播 / 观众 / 后台分数实时一致，无延迟；

- **灵活适配规则**：后端改配置（例如调整礼物对应分数、加成分数）即可适配业务变化，无需改前端，降低迭代成本。

### 如何管理通过 `VideoViewAdapter` 添加的自定义视图的生命周期和事件？

> **适用平台：Android**

**LiveCoreView** 会自动管理您通过适配器方法返回视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件即可。

### 连线或 PK 过程中，一方主播网络断开或 App 崩溃了怎么办？

> **适用平台：iOS / Flutter**

**CoHostStore** 和 **BattleStore** 内部都有心跳和超时检测机制。如果一方异常退出，另一方会通过 **onCoHostUserLeft** 或 **onUserExitBattle** 等事件收到通知，您可以根据这些事件来处理UI，例如提示“对方已掉线”并结束互动。

### 如何管理通过 `VideoViewDelegate` 添加的自定义视图的生命周期和事件？

> **适用平台：iOS**

**LiveCoreView** 会自动管理您通过代理方法返回视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件即可。

### 如何管理通过 `VideoWidgetBuilder` 添加的自定义视图的生命周期和事件？

> **适用平台：Flutter**

**LiveCoreWidget** 会自动管理您通过 `coHostWidgetBuilder`、`battleWidgetBuilder`、`battleContainerWidgetBuilder` 回调返回视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件即可。
