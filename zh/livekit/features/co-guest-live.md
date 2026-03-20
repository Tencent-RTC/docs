> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter**

**AtomicXCore** 提供了 CoGuestStore 模块，专门用于管理观众连麦的完整业务流程。您无需关心复杂的状态同步和信令交互，只需调用几个简单的方法，即可为您的直播添加强大的观众与主播音视频互动功能。

## API 文档
关于 CoGuestStore 及其相关类的所有公开接口、属性和方法的详细信息，请参阅随 AtomicXCore 框架的官方 API 文档。本指南使用到的相关 Store 如下：
| Store/Component | 功能描述 | - |
| --- | --- | --- |
| LiveCoreView | 直播视频流展示与交互的核心视图组件：负责视频流渲染和视图挂件处理，支持主播直播、观众连麦、主播连线等场景。 |  |
| DeviceStore | 音视频设备控制：麦克风（开关 / 音量）、摄像头（开关 / 切换 / 画质）、屏幕共享，设备状态实时监听。 |  |
| CoGuestStore | 观众连麦管理：连麦申请 / 邀请 / 同意 / 拒绝，连麦成员权限控制（麦克风 / 摄像头），状态同步。 |  |

## Platform: Android

## 核心场景

`CoGuestStore` 支持以下两种最主流的连麦场景：
- **观众申请上麦**：观众主动发起连麦请求，主播在收到请求后进行同意或拒绝。

- **主播邀请上麦**：主播可以主动向直播间内的任意一位观众发起连麦邀请。

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreView** 的接入。

### 步骤2：实现观众申请上麦

#### 观众端实现

作为观众，您的核心任务是**发起申请、接收结果**和**主动下麦**。
1. **发起连麦申请**

   当用户点击界面上的"申请连麦"按钮时，调用 `applyForSeat` 方法。

   ``` kotlin
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   
   val liveId = "房间ID"
   val guestStore = CoGuestStore.create(liveId)
   
   // 用户点击"申请连麦"
   fun requestToConnect() {
       // timeout: 请求超时时间，例如 30 秒
       guestStore.applyForSeat(
           seatIndex = 0, // 麦位索引
           timeout = 30,
           extraInfo = null,
           completion = object : CompletionHandler {
               override fun onSuccess() {
                   println("连麦申请已发送，等待主播处理...")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   println("申请发送失败: $desc")
               }
           }
       )
   }
   ```
2. **监听主播的响应**

   通过添加 `GuestListener`，您可以接收到主播的处理结果。

   ``` kotlin
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.live.GuestListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   // 在您的Activity或Fragment中实现监听
   class YourActivity : AppCompatActivity() {
       val liveId = "房间ID"
       val guestStore = CoGuestStore.create(liveId)
       val deviceStore = DeviceStore.shared()
       private val guestListener = object : GuestListener() {
           override fun onGuestApplicationResponded(isAccept: Boolean, hostUser: LiveUserInfo) {
               if (isAccept) {
                   println("主播 ${hostUser.userName} 同意了你的申请，准备上麦")
                   // 上麦申请被同意，进行上麦后的操作
                   // 1. 打开摄像头、麦克风
                   DeviceStore.shared().openLocalCamera(true, completion = null)
                   DeviceStore.shared().openLocalMicrophone(completion = null)
                   // 2. 在此更新 UI，例如关闭申请按钮，显示连麦中的状态
               } else {
                   println("主播 ${hostUser.userName} 拒绝了你的申请")
                   // 上麦申请被拒绝，弹窗提示
               }
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // 添加监听器
           guestStore.addGuestListener(guestListener)
       }
   
       override fun onDestroy() {
           super.onDestroy()
           // 移除监听器
           guestStore.removeGuestListener(guestListener)
       }
   }
   ```
3. **主动下麦**

   当连麦观众想结束互动时，调用 `disconnect` 方法即可返回普通观众状态。

   ``` kotlin
   // 用户点击"下麦"按钮
   fun leaveSeat() {
       guestStore.disconnect(object : CompletionHandler {
           override fun onSuccess() {
               println("已成功下麦")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("下麦失败: $desc")
           }
       })
   }
   ```
4. **(可选) 取消申请**

   如果观众在主播处理前想撤回申请，可以调用 `cancelApplication`。

   ``` kotlin
   // 用户在等待时，点击"取消申请"
   fun cancelRequest() {
       guestStore.cancelApplication(object : CompletionHandler {
           override fun onSuccess() {
               println("申请已取消")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("申请取消失败: $desc")
           }
       })
   }
   ```

#### 主播端实现

作为主播，您的核心任务是**接收申请、展示申请列表**和**处理申请**。
1. **监听新的连麦申请**

   通过添加 `HostListener`，您可以在有新观众申请时立即收到通知，并给出提示。

   ``` kotlin
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.live.HostListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   class YourActivity : AppCompatActivity() {
       val liveId = "房间ID"
       val guestStore = CoGuestStore.create(liveId)
       val hostListener = object : HostListener() {
           override fun onGuestApplicationReceived(guestUser: LiveUserInfo) {
               println("收到观众 ${guestUser.userName} 的连麦申请")
               // 在此更新 UI，例如在"申请列表"按钮上显示红点
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // 添加监听器
           guestStore.addHostListener(hostListener)
       }
   
       override fun onDestroy() {
           super.onDestroy()
           // 移除监听器
           guestStore.removeHostListener(hostListener)
       }
   }
   ```
2. **展示申请列表**

   `CoGuestStore` 的 state 会实时维护当前的申请者列表，您可以订阅它来刷新您的 UI。

   ``` kotlin
   class YourActivity : AppCompatActivity() {
       val liveId = "房间ID"
       val guestStore = CoGuestStore.create(liveId)
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           // 订阅申请者列表变化
           lifecycleScope.launch {
               guestStore.coGuestState.applicants.collect { applicants ->
                   println("当前申请人数: ${applicants.size}")
                   // 在此刷新您的"申请者列表"UI
                   // updateApplicantListView(applicants)
               }
           }
       }
   }
   ```
3. **处理连麦申请**

   当您在列表中选择一位观众并点击"同意"或"拒绝"时，调用相应的方法。

   ``` kotlin
   // 主播点击"同意"按钮，传入申请者的 userID
   fun accept(userId: String) {
       guestStore.acceptApplication(userId, object : CompletionHandler {
           override fun onSuccess() {
               println("已同意 $userId 的申请，对方正在上麦")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("同意申请失败: $desc")
           }
       })
   }
   
   // 主播点击"拒绝"按钮
   fun reject(userId: String) {
       guestStore.rejectApplication(userId, object : CompletionHandler {
           override fun onSuccess() {
               println("已拒绝 $userId 的申请")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("拒绝申请失败: $desc")
           }
       })
   }
   ```

### 步骤3：实现主播邀请上麦

#### 主播端实现
1. **向观众发起邀请**

   当主播在观众列表中选择某人并点击"邀请连麦"时，调用 `inviteToSeat` 方法。

   ``` kotlin
    // 主播选择观众并发起邀请
   fun invite(userId: String) {
       // timeout: 邀请超时时间
       guestStore.inviteToSeat(
           inviteeID = userId,
           seatIndex = 0,
           timeout = 30,
           extraInfo = null,
           completion = object : CompletionHandler {
               override fun onSuccess() {
                   println("已向 $userId 发出邀请，等待对方回应...")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   println("发送邀请失败: $desc")
               }
           }
       )
   }
   ```
2. **监听观众的回应**

   通过 `HostListener` 监听 `onHostInvitationResponded` 事件。

   ``` kotlin
   // 在 HostListener 的实现中添加
   override fun onHostInvitationResponded(isAccept: Boolean, guestUser: LiveUserInfo) {
       if (isAccept) {
           println("观众 ${guestUser.userName} 接受了你的邀请")
       } else {
           println("观众 ${guestUser.userName} 拒绝了你的邀请")
       }
   }
   ```

#### 观众端实现
1. **接收主播的邀请**

   通过 `GuestListener` 监听 `onHostInvitationReceived` 事件。

   ``` kotlin
   // 在 GuestListener 的实现中添加
   override fun onHostInvitationReceived(hostUser: LiveUserInfo) {
       println("收到主播 ${hostUser.userName} 的连麦邀请")
       // 在此弹出一个对话框，让用户选择"接受"或"拒绝"
       // showInvitationDialog(hostUser)
   }
   ```
2. **响应邀请**

   当用户在弹出的对话框中做出选择后，调用相应的方法。

   ``` kotlin
   val inviterId = "发起邀请的主播ID" // 从 onHostInvitationReceived 事件中获取
   
   // 用户点击"接受"
   fun accept() {
       guestStore.acceptInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               // 2. 打开摄像头、麦克风
               DeviceStore.shared().openLocalCamera(true, completion = null)
               DeviceStore.shared().openLocalMicrophone(completion = null)
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("接受邀请失败: $desc")
           }
       })
   }
   
   // 用户点击"拒绝"
   fun reject() {
       guestStore.rejectInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               println("已拒绝邀请")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("拒绝邀请失败: $desc")
           }
       })
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用两个观众与主播进行连麦操作，观众 A 同时打开摄像头和麦克风，观众 B 只打开麦克风，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制您想要的 UI 逻辑。

## 完善 UI 细节

您可以通过 `VideoViewAdapter` 接口提供的"插槽"能力，在观众连麦的视频流画面上添加自定义视图，用于显示昵称、头像等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### **实现效果**

#### **实现方式**

> **提示：**
> 

> 您也可以参考 TUILiveKit 开源项目中的 [widgets](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/coguest/widgets)目录下文件来了解完整的实现逻辑。
> 

- **步骤 1**：创建前景视图 (**CustomSeatView**)，该视图用于在视频流上方显示用户信息。

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.view.ViewGroup
   import android.widget.LinearLayout
   import android.widget.TextView
   
   // 自定义的用户信息悬浮视图（前景）
   class CustomSeatView(context: Context) : LinearLayout(context) {
       private val nameLabel: TextView
   
       init {
           orientation = VERTICAL
           gravity = Gravity.BOTTOM or Gravity.START
           setBackgroundColor(Color.parseColor("#80000000")) // 半透明黑色背景
   
           nameLabel = TextView(context).apply {
               setTextColor(Color.WHITE)
               textSize = 14f
           }
   
           addView(nameLabel, LayoutParams(
               ViewGroup.LayoutParams.WRAP_CONTENT,
               ViewGroup.LayoutParams.WRAP_CONTENT
           ).apply {
               setMargins(20, 0, 0, 20) // 左边距20，下边距20
           })
       }
   
       fun setUserName(userName: String) {
           nameLabel.text = userName
       }
   }
   ```
- **步骤 2**：创建背景视图 (**CustomAvatarView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.view.ViewGroup
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
   
           addView(avatarImageView, LayoutParams(120, 120)) // 60dp * 2 = 120px
       }
   
       fun setUserAvatar(avatarUrl: String) {
           // 在这里加载用户头像，可以使用Glide等图片加载库
           // Glide.with(context).load(avatarUrl).into(avatarImageView)
       }
   }
   ```
- **步骤 3**：实现 `VideoViewAdapter.createCoGuestView` 接口，根据 `viewLayer` 的值返回对应的视图。

   ``` kotlin
   import android.os.Bundle
   import android.view.View
   import androidx.appcompat.app.AppCompatActivity
   import com.tencent.cloud.tuikit.engine.room.TUIRoomDefine
   import io.trtc.tuikit.atomicxcore.api.view.VideoViewAdapter
   import io.trtc.tuikit.atomicxcore.api.view.ViewLayer
   
   // 1. 在您的Activity中，实现 VideoViewAdapter 接口
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       // ... 其他代码 ...
   
       // 2. 完整实现接口方法，处理两种 viewLayer
       override fun createCoGuestView(userInfo: TUIRoomDefine.SeatFullInfo?, viewLayer: ViewLayer?): View? {
           userInfo ?: return null
           val userId = userInfo.userId
           if (userId.isNullOrEmpty()) return null
   
           return when (viewLayer) {
               ViewLayer.FOREGROUND -> {
                   // 用户摄像头开启时，显示前景视图
                   val seatView = CustomSeatView(this)
                   seatView.setUserName(userInfo.userName ?: "")
                   seatView
               }
               ViewLayer.BACKGROUND -> {
                   // 用户摄像头关闭时，显示背景视图
                   val avatarView = CustomAvatarView(this)
                   // 您可以在这里通过 userInfo.userAvatar 加载用户真实头像
                   userInfo.userAvatar?.let { avatarView.setUserAvatar(it) }
                   avatarView
               }
               else -> null
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // 设置适配器
           liveCoreView.setVideoViewAdapter(this)
       }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `seatInfo` | `SeatFullInfo?` | 麦位信息对象，包含麦上用户的详细信息 |
| `seatInfo.userId` | `String` | 麦上用户的 ID |
| `seatInfo.userName` | `String ` | 麦上用户的昵称 |
| `seatInfo.userAvatar` | `String` | 麦上用户的头像 URL |
| `seatInfo.userMicrophoneStatus` | `DeviceStatus` | 麦上用户的麦克风状态 |
| `seatInfo.userCameraStatus ` | `DeviceStatus` | 麦上用户的摄像头状态 |
| `viewLayer` | `ViewLayer` | 视图层级枚举 - `FOREGROUND` 表示前景挂件视图，始终显示在视频画面的最上层 - `BACKGROUND` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图 |

## Platform: iOS

## 核心场景

`CoGuestStore` 支持以下两种最主流的连麦场景：
- **观众申请上麦**：观众主动发起连麦请求，主播在收到请求后进行同意或拒绝。

- **主播邀请上麦**：主播可以主动向直播间内的任意一位观众发起连麦邀请。

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreView** 的接入。

### 步骤2：实现观众申请上麦

#### 观众端实现

作为观众，您的核心任务是**发起申请、接收结果**和**主动下麦**。
1. **发起连麦申请**

   当用户点击界面上的“申请连麦”按钮时，调用 `applyForSeat` 方法。

   ``` swift
   import AtomicXCore
   
   let liveId = "房间ID"
   let guestStore = CoGuestStore.create(liveID: liveId)
   
   // 用户点击“申请连麦”
   func requestToConnect() {
       // timeout: 请求超时时间，例如 30 秒
       guestStore.applyForSeat(timeout: 30.0, extraInfo: nil) { result in
         switch result {
         case .success():
             print("连麦申请已发送，等待主播处理...")
         case .failure(let error):
             print("申请发送失败: \(error.message)")
         }
       }
   }
   ```
2. **监听主播的响应**

   通过订阅 `guestEventPublisher`，您可以接收到主播的处理结果。

   ``` swift
   // 在您的视图控制器初始化时订阅事件
   func subscribeGuestEvents() {
       guestStore.guestEventPublisher
         .sink { [weak self] event in
             if case let .onGuestApplicationResponded(isAccept, hostUser) = event {
                 if isAccept {
                     print("主播 \(hostUser.userName) 同意了你的申请，准备上麦")
                     // 1. 打开摄像头、麦克风 
                     DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
                     DeviceStore.shared.openLocalMicrophone(completion: nil)
                     // 2. 在此更新 UI，例如关闭申请按钮，显示连麦中的状态
                 } else {
                     print("主播 \(hostUser.userName) 拒绝了你的申请")
                     // 弹窗提示用户申请被拒绝
                 }
             }
         }
         .store(in: &cancellables) // 管理订阅生命周期
   }
   ```
3. **主动下麦**

   当连麦观众想结束互动时，调用 `disConnect` 方法即可返回普通观众状态。

   ``` swift
   // 用户点击“下麦”按钮
   func leaveSeat() {
       guestStore.disConnect { result in
         switch result {
         case .success():
             print("已成功下麦")
         case .failure(let error):
             print("下麦失败: \(error.message)")
         }
       }
   }
   ```
4. **(可选) 取消申请**

   如果观众在主播处理前想撤回申请，可以调用 `cancelApplication`。

   ``` swift
   // 用户在等待时，点击“取消申请”
   func cancelRequest() {
       guestStore.cancelApplication { result in
         switch result {
         case .success():
             print("申请已取消")
         case .failure(let error):
             print("申请取消失败: \(error.message)")
         }
       }
   }
   ```

#### 主播端实现

作为主播，您的核心任务是**接收申请、展示申请列表**和**处理申请**。
1. **监听新的连麦申请**

   通过订阅 `hostEventPublisher`，您可以在有新观众申请时立即收到通知，并给出提示。

   ``` swift
   import AtomicXCore
   
   let liveId = "房间ID"
   let guestStore = CoGuestStore.create(liveID: liveId)
   
   // 订阅主播事件
   guestStore.hostEventPublisher
       .sink { [weak self] event in
           if case let .onGuestApplicationReceived(guestUser) = event {
               print("收到观众 \(guestUser.userName) 的连麦申请")
               // 在此更新 UI，例如在“申请列表”按钮上显示红点
           }
       }
       .store(in: &cancellables)
   ```
2. **展示申请列表**

   `CoGuestStore` 的 state 会实时维护当前的申请者列表，您可以订阅它来刷新您的 UI。

   ``` swift
   // 订阅状态变更
   guestStore.state
       .subscribe(StatePublisherSelector(keyPath: \CoGuestState.applicants)) // 只关心申请者列表的变化
       .removeDuplicates()
       .sink { applicants in
           print("当前申请人数: \(applicants.count)")
           // 在此刷新您的“申请者列表”UI
           // self.applicantListView.update(with: applicants)
       }
       .store(in: &cancellables)
   ```
3. **处理连麦申请**

   当您在列表中选择一位观众并点击“同意”或“拒绝”时，调用相应的方法。

   ``` perl
   // 主播点击“同意”按钮，传入申请者的 userID
   func accept(userId: String) {
       guestStore.acceptApplication(userID: userId) { result in
           if case .success = result {
               print("已同意 \(userId) 的申请，对方正在上麦")
           }
       }
   }
   
   // 主播点击“拒绝”按钮
   func reject(userId: String) {
       guestStore.rejectApplication(userID: userId) { result in
           if case .success = result {
               print("已拒绝 \(userId) 的申请")
           }
       }
   }
   ```

### 步骤3：实现主播邀请上麦

#### 主播端实现
1. **向观众发起邀请**

   当主播在观众列表中选择某人并点击“邀请连麦”时，调用 `inviteToSeat` 方法。

   ``` swift
    // 主播选择观众并发起邀请
   func invite(userId: String) {
       // timeout: 邀请超时时间
       guestStore.inviteToSeat(userID: userId, timeout: 30.0, extraInfo: nil) { result in
           if case .success = result {
               print("已向 \(userId) 发出邀请，等待对方回应...")
           }
       }
   }
   ```
2. **监听观众的回应**

   通过 `hostEventPublisher` 监听 `onHostInvitationResponded` 事件。

   ``` swift
   // 在 hostEventPublisher 的订阅中添加
   if case let .onHostInvitationResponded(isAccept, guestUser) = event {
       if isAccept {
           print("观众 \(guestUser.userName) 接受了你的邀请")
       } else {
           print("观众 \(guestUser.userName) 拒绝了你的邀请")
       }
   }
   ```

#### 观众端实现
1. **接收主播的邀请**

   通过 `guestEventPublisher` 监听 `onHostInvitationReceived` 事件。

   ``` swift
   // 在 guestEventPublisher 的订阅中添加
   if case let .onHostInvitationReceived(hostUser) = event {
       print("收到主播 \(hostUser.userName) 的连麦邀请")
       // 在此弹出一个对话框，让用户选择“接受”或“拒绝”
       // self.showInvitationDialog(from: hostUser)
   }
   ```
2. **响应邀请**

   当用户在弹出的对话框中做出选择后，调用相应的方法。

   ``` swift
   let inviterId = "发起邀请的主播ID" // 从 onHostInvitationReceived 事件中获取
   
   // 用户点击“接受”
   func accept() {
       guestStore.acceptInvitation(inviterID: inviterId) { result in
           if case .success = result {
               // 2. 打开摄像头、麦克风  
               DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
               DeviceStore.shared.openLocalMicrophone(completion: nil)
           }
       }
   }
   
   // 用户点击“拒绝”
   func reject() {
       guestStore.rejectInvitation(inviterID: inviterId) { result in
           // ...
       }
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用两个观众与主播进行连麦操作，观众 A 同时打开摄像头和麦克风，观众 B 只打开麦克风，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制您想要的 UI 逻辑。

## 完善 UI 细节

您可以通过 `LiveCoreView.VideoViewDelegate` 协议提供的“插槽”能力，在观众连麦的视频流画面上添加自定义视图，用于显示昵称、头像等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### **实现效果**

#### **实现方式**

> **提示：**
> 

> 您也可以参考 TUILiveKit 开源项目中的 [AnchorCoGuestView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorCoGuestView.swift) 和 [AnchorEmptySeatView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorEmptySeatView.swift) 文件来了解完整的实现逻辑。
> 

- **步骤 1**：创建前景视图 (**CustomSeatView**)，该视图用于在视频流上方显示用户信息。

   ``` swift
   import UIKit
   
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
- **步骤 2**：创建背景视图 (**CustomAvatarView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` swift
   import UIKit
   
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
- **步骤 3**：实现 `VideoViewDelegate.createCoGuestView` 协议，根据 `viewLayer` 的值返回对应的视图。

   ``` swift
   import AtomicXCore
   
   // 1. 在您的视图控制器中，遵守 VideoViewDelegate 协议
   class YourViewController: UIViewController, VideoViewDelegate {
   
       // ... 其他代码 ...
       
       // 2. 完整实现协议方法，处理两种 viewLayer
       func createCoGuestView(seatInfo: TUISeatFullInfo, viewLayer: ViewLayer) -> UIView? {
           guard let userId = seatInfo.userID,!userId.isEmpty else {
               return nil
           }
           if viewLayer == .foreground {
               // 用户摄像头开启时，显示前景视图
               let seatView = CustomSeatView()
               seatView.nameLabel.text = seatInfo.userName
               return seatView
           } else { // viewLayer == .background
               // 用户摄像头关闭时，显示背景视图
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
| `seatInfo` | `TUISeatFullInfo` | 麦位信息对象，包含麦上用户的详细信息。 |
| `seatInfo.userID` | `String?` | 麦上用户的 ID。 |
| `seatInfo.userName` | `String? ` | 麦上用户的昵称。 |
| `seatInfo.userAvatar` | `String?` | 麦上用户的头像 URL。 |
| `seatInfo.userMicrophoneStatus` | `TUIDeviceStatus` | 麦上用户的麦克风状态。 |
| `seatInfo.userCameraStatus ` | `TUIDeviceStatus` | 麦上用户的摄像头状态。 |
| `viewLayer` | `ViewLayer` | 视图层级枚举。 `.foreground` 表示前景挂件视图，始终显示在视频画面的最上层。 `.background` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图。 |

## Platform: Flutter

## 核心场景

`CoGuestStore` 支持以下两种最主流的连麦场景：
- **观众申请上麦**：观众主动发起连麦请求，主播在收到请求后进行同意或拒绝。

- **主播邀请上麦**：主播可以主动向直播间内的任意一位观众发起连麦邀请。

## 实现步骤

### 步骤1：组件集成

请参考 开始直播 集成 **AtomicXCore**，并完成 **LiveCoreWidget** 的接入。

完成集成后，开始实现连麦功能。

### 步骤2：实现观众申请上麦

#### 观众端实现

作为观众，您的核心任务是**发起申请、接收结果**和**主动下麦**。
1. **发起连麦申请**

   当用户点击界面上的"申请连麦"按钮时，调用 `applyForSeat` 方法。

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "房间ID";
   final CoGuestStore guestStore = CoGuestStore.create(liveId);
   
   // 用户点击"申请连麦"
   void requestToConnect() async {
       // seatIndex: 麦位索引，取值范围为 -1(自动选择空麦位) 和 [0,当前房间支持最大麦位数-1] ，timeout: 请求超时时间（秒）
       final result = await guestStore.applyForSeat(
           seatIndex: 0, 
           timeout: 30, 
           extraInfo: null,
       );
       if (result.isSuccess) {
           print("连麦申请已发送，等待主播处理...");
       } else {
           print("申请发送失败: ${result.errorMessage}");
       }
   }
   ```
2. **监听主播的响应**

   通过 `addGuestListener` 添加监听器，您可以接收到主播的处理结果。

   ``` java
   late GuestListener _guestListener;
   
   // 在您的 Widget 初始化时订阅事件
   void subscribeGuestEvents() {
       _guestListener = GuestListener(
           onApplicationAccepted: (hostUser) {
               print("主播 ${hostUser.userName} 同意了你的申请，准备上麦");
               // 1. 打开摄像头、麦克风 
               DeviceStore.shared.openLocalCamera(isFront: true);
               DeviceStore.shared.openLocalMicrophone();
               // 2. 在此更新 UI，例如关闭申请按钮，显示连麦中的状态
           },
           onApplicationRejected: (hostUser) {
               print("主播 ${hostUser.userName} 拒绝了你的申请");
               // 弹窗提示用户申请被拒绝
           },
           onInvitationReceived: (hostUser) {
               print("收到主播 ${hostUser.userName} 的连麦邀请");
               // 在此弹出一个对话框，让用户选择"接受"或"拒绝"
               _showInvitationDialog(hostUser);
           },
           onKickedOffSeat: () {
               print("您已被主播踢下麦位");
               // 更新 UI，关闭摄像头和麦克风
           },
       );
       guestStore.addGuestListener(_guestListener);
   }
   
   @override
   void dispose() {
       guestStore.removeGuestListener(_guestListener);
       super.dispose();
   }
   ```
3. **主动下麦**

   当连麦观众想结束互动时，调用 `disconnect` 方法即可返回普通观众状态。

   ``` java
   // 用户点击"下麦"按钮
   void leaveSeat() async {
       final result = await guestStore.disconnect();
       if (result.isSuccess) {
           print("已成功下麦");
       } else {
           print("下麦失败: ${result.errorMessage}");
       }
   }
   ```
4. **(可选) 取消申请**

   如果观众在主播处理前想撤回申请，可以调用 `cancelApplication`。

   ``` java
   // 用户在等待时，点击"取消申请"
   void cancelRequest() async {
       final result = await guestStore.cancelApplication();
       if (result.isSuccess) {
           print("申请已取消");
       } else {
           print("申请取消失败: ${result.errorMessage}");
       }
   }
   ```

#### 主播端实现

作为主播，您的核心任务是**接收申请、展示申请列表**和**处理申请**。
1. **监听新的连麦申请**

   通过 `addHostListener` 添加监听器，您可以在有新观众申请时立即收到通知，并给出提示。

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "房间ID";
   final CoGuestStore guestStore = CoGuestStore.create(liveId);
   late HostListener _hostListener;
   late final VoidCallback _applicantsChangedListener = _onApplicantsChanged;
   
   // 订阅主播事件
   void subscribeHostEvents() {
       _hostListener = HostListener(
           onApplicationReceived: (guestUser) {
               print("收到观众 ${guestUser.userName} 的连麦申请");
               // 在此更新 UI，例如在"申请列表"按钮上显示红点
           },
           onInvitationAccepted: (guestUser) {
               print("观众 ${guestUser.userName} 接受了你的邀请");
           },
           onInvitationRejected: (guestUser) {
               print("观众 ${guestUser.userName} 拒绝了你的邀请");
           },
       );
       guestStore.addHostListener(_hostListener);
   }
   
   @override
   void dispose() {
       guestStore.removeHostListener(_hostListener);
       super.dispose();
   }
   ```
2. **展示申请列表**

   `CoGuestStore` 的 `coGuestState` 会实时维护当前的申请者列表，您可以监听它来刷新您的 UI。

   ``` java
   // 订阅状态变更
   void subscribeApplicants() {
       guestStore.coGuestState.applicants.addListener(_applicantsChangedListener);
   }
   
   void _onApplicantsChanged() {
       final applicants = guestStore.coGuestState.applicants.value;
       print("当前申请人数: ${applicants.length}");
       // 在此刷新您的"申请者列表"UI
       setState(() {
           _applicantList = applicants;
       });
   }
   
   @override
   void dispose() {
       guestStore.coGuestState.applicants.removeListener(_applicantsChangedListener);
       super.dispose();
   }
   ```
3. **处理连麦申请**

   当您在列表中选择一位观众并点击"同意"或"拒绝"时，调用相应的方法。

   ``` java
   // 主播点击"同意"按钮，传入申请者的 userID
   void accept(String userId) async {
       final result = await guestStore.acceptApplication(userId);
       if (result.isSuccess) {
           print("已同意 $userId 的申请，对方正在上麦");
       }
   }
   
   // 主播点击"拒绝"按钮
   void reject(String userId) async {
       final result = await guestStore.rejectApplication(userId);
       if (result.isSuccess) {
           print("已拒绝 $userId 的申请");
       }
   }
   ```

### 步骤3：实现主播邀请上麦

#### 主播端实现
1. **向观众发起邀请**

   当主播在观众列表中选择某人并点击"邀请连麦"时，调用 `inviteToSeat` 方法。

   ``` java
   // 主播选择观众并发起邀请
   void invite(String userId) async {
       // inviteeID: 被邀请者ID，seatIndex: 麦位索引，取值范围为 -1(自动选择空麦位) 和 [0,当前房间支持最大麦位数-1] 
       // timeout: 邀请超时时间
       final result = await guestStore.inviteToSeat(
           inviteeID: userId,
           seatIndex: 0,
           timeout: 30, 
           extraInfo: null,
       );
       if (result.isSuccess) {
           print("已向 $userId 发出邀请，等待对方回应...");
       }
   }
   ```
2. **监听观众的回应**

   通过 `HostListener` 监听邀请响应事件。

   ``` java
   // 在 HostListener 中已配置回调
   _hostListener = HostListener(
       onInvitationAccepted: (guestUser) {
           print("观众 ${guestUser.userName} 接受了你的邀请");
       },
       onInvitationRejected: (guestUser) {
           print("观众 ${guestUser.userName} 拒绝了你的邀请");
       },
   );
   ```

#### 观众端实现
1. **接收主播的邀请**

   通过 `GuestListener` 监听邀请事件。

   ``` java
   // 在 GuestListener 中已配置回调
   _guestListener = GuestListener(
       onInvitationReceived: (hostUser) {
           print("收到主播 ${hostUser.userName} 的连麦邀请");
           // 在此弹出一个对话框，让用户选择"接受"或"拒绝"
           _showInvitationDialog(hostUser);
       },
   );
   ```
2. **响应邀请**

   当用户在弹出的对话框中做出选择后，调用相应的方法。

   ``` java
   String inviterId = "发起邀请的主播ID"; // 从 onInvitationReceived 回调中获取
   
   // 用户点击"接受"
   void acceptInvitation() async {
       final result = await guestStore.acceptInvitation(inviterId);
       if (result.isSuccess) {
           // 打开摄像头、麦克风  
           DeviceStore.shared.openLocalCamera(isFront: true);
           DeviceStore.shared.openLocalMicrophone();
       }
   }
   
   // 用户点击"拒绝"
   void rejectInvitation() async {
       await guestStore.rejectInvitation(inviterId);
   }
   ```

### 运行效果

当您集成以上功能实现后，请分别使用两个观众与主播进行连麦操作，观众 A 同时打开摄像头和麦克风，观众 B 只打开麦克风，运行效果如下，您可以参考下一章节 完善 UI 细节 来定制您想要的 UI 逻辑。

## 完善 UI 细节

您可以通过 `LiveCoreWidget` 的 `VideoWidgetBuilder` 参数提供的"插槽"能力，在观众连麦的视频流画面上添加自定义视图，用于显示昵称、头像等信息，或在他们关闭摄像头时提供占位图，以优化视觉体验。

### 实现视频流画面的昵称显示

#### **实现效果**

#### **实现方式**
- **步骤 1**：创建前景视图 (**CustomSeatForegroundView**)，该视图用于在视频流上方显示用户信息。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 自定义的用户信息悬浮视图（前景）
   class CustomSeatForegroundView extends StatelessWidget {
       final SeatFullInfo seatInfo;
   
       const CustomSeatForegroundView({
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
                       margin: const EdgeInsets.all(5),
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
- **步骤 2**：创建背景视图 (**CustomSeatBackgroundView**)，该视图用于在用户无视频流时作为占位图显示。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 自定义的头像占位视图（背景）
   class CustomSeatBackgroundView extends StatelessWidget {
       final SeatFullInfo seatInfo;
   
       const CustomSeatBackgroundView({
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
- **步骤 3**：通过 `VideoWidgetBuilder` 的 `coGuestWidgetBuilder` 回调构建自定义视图，根据 `viewLayer` 的值返回对应的视图。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// 带有自定义连麦视图的直播页面
   class CustomCoGuestLiveWidget extends StatefulWidget {
       final String liveId;
   
       const CustomCoGuestLiveWidget({
           Key? key, 
           required this.liveId,
       }) : super(key: key);
   
       @override
       State<CustomCoGuestLiveWidget> createState() => _CustomCoGuestLiveWidgetState();
   }
   
   class _CustomCoGuestLiveWidgetState extends State<CustomCoGuestLiveWidget> {
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
   
       /// 构建连麦观众的自定义视图
       Widget _buildCoGuestWidget(
           BuildContext context, 
           SeatFullInfo seatFullInfo, 
           ViewLayer viewLayer,
       ) {
           if (viewLayer == ViewLayer.foreground) {
               // 前景层：始终显示在视频画面的最上层，用于显示昵称等信息
               return CustomSeatForegroundView(seatInfo: seatFullInfo);
           } else {
               // 背景层：仅在对应用户没有视频流时显示，用于显示头像占位图
               return CustomSeatBackgroundView(seatInfo: seatFullInfo);
           }
       }
   
       @override
       Widget build(BuildContext context) {
           return Scaffold(
               body: LiveCoreWidget(
                   controller: _controller,
                   videoWidgetBuilder: VideoWidgetBuilder(
                       coGuestWidgetBuilder: _buildCoGuestWidget,
                   ),
               ),
           );
       }
   }
   ```

#### **参数说明**：
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `seatFullInfo` | `SeatFullInfo` | 麦位信息对象，包含麦上用户的详细信息。 |
| `seatFullInfo.userInfo.userId` | `String` | 麦上用户的 ID。 |
| `seatFullInfo.userInfo.userName` | `String` | 麦上用户的昵称。 |
| `seatFullInfo.userInfo.avatarUrl` | `String` | 麦上用户的头像 URL。 |
| `viewLayer` | `ViewLayer` | 视图层级枚举。 - `ViewLayer.foreground` 表示前景挂件视图，始终显示在视频画面的最上层。 - `ViewLayer.background` 表示背景挂件视图，位于前景视图下层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图。 |

## 常见问题

### 如何管理通过 `VideoViewAdapter` 添加的自定义视图的生命周期和事件？

> **适用平台：Android**

**LiveCoreView** 会自动管理您通过适配器方法返回的视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件即可。

### `VideoViewAdapter` 中的 viewLayer 参数有什么作用？

> **适用平台：Android**

`viewLayer` 用于区分前景和背景挂件：
- `FOREGROUND`：前景层，始终显示在视频画面的最上层。

- `BACKGROUND`：背景层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图。

### 我的自定义视图为什么没有显示？

> **适用平台：Android / iOS / Flutter**

> **Android：** - **检查适配器设置**：请确认已调用 `liveCoreView.setVideoViewAdapter(this)` 并成功设置了适配器。
> **iOS：** - **检查代理设置**：请确认已调用 `coreView.videoViewDelegate = self` 并成功设置了代理。
> **Flutter：** - **检查 VideoWidgetBuilder 设置**：请确认已正确设置 `LiveCoreWidget` 的 `videoWidgetBuilder` 参数。

> **Android：** - **检查实现方法**：请检查是否正确实现了对应的适配器方法（例如 `createCoGuestView`）。
> **iOS：** - **检查实现方法**：请检查是否正确实现了对应的代理方法（例如 `createCoGuestView`）。
> **Flutter：** - **检查回调实现**：请检查是否正确实现了 `coGuestWidgetBuilder` 回调函数。

> **Android：** - **检查返回值**：确保您的适配器方法在正确的时机返回了一个有效的 `View` 实例，而不是 `null`。您可以在适配器方法中添加日志进行调试。
> **iOS：** - **检查返回值**：确保您的代理方法在正确的时机返回了一个有效的 `UIView` 实例，而不是 `nil`。您可以在代理方法中添加断点或日志进行调试。
> **Flutter：** - **检查返回值**：确保您的回调函数返回了一个有效的 `Widget` 实例。

### 如何管理通过 `VideoViewDelegate` 添加的自定义视图的生命周期和事件？

> **适用平台：iOS**

**LiveCoreView** 会自动管理您通过代理方法返回的视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件即可。

### `VideoViewDelegate` 中的 viewLayer 参数有什么作用？

> **适用平台：iOS**

`viewLayer` 用于区分前景和背景挂件：
- `.foreground`：前景层，始终显示在视频画面的最上层。

- `.background`：背景层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图。

### 如何管理通过 `VideoWidgetBuilder` 添加的自定义视图的生命周期和事件？

> **适用平台：Flutter**

**LiveCoreWidget** 会自动管理您通过 `coGuestWidgetBuilder` 回调返回的视图的添加和移除，您无需手动处理。如果需要在自定义视图中处理用户交互（例如点击事件），请在创建视图时为其添加相应的事件处理即可。

### `ViewLayer` 参数有什么作用？

> **适用平台：Flutter**

`ViewLayer` 用于区分前景和背景挂件：
- `ViewLayer.foreground`：前景层，始终显示在视频画面的最上层。

- `ViewLayer.background`：背景层，仅在对应用户没有视频流（例如未开摄像头）的情况下显示，通常用于展示用户的默认头像或占位图。
