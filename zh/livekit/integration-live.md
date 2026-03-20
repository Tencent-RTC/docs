> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter**

本文档将帮助您使用 **AtomicXCore SDK**的核心组件 LiveCoreView，快速构建一个包含主播开播和观众观看功能的基础直播 App。

### **核心功能**

**LiveCoreView** 是一个专为直播场景设计的轻量级 `View` 组件，是您构建直播场景的核心，它封装了所有复杂的底层直播技术（如推拉流、连麦、音视频渲染）。您可以将 LiveCoreView 作为直播画面的"画布"，专注于上层 UI 与交互的开发。

通过下方的视图层级示意图，您可以直观了解 **LiveCoreView** 在直播界面中的位置和作用：

## API 文档
| **Store/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **LiveCoreView** | 直播视频流展示与交互的核心视图组件：负责视频流渲染和视图挂件处理，支持主播直播、观众连麦、主播连线等场景。 |  |
| **LiveListStore** | 直播间全生命周期管理：创建 / 加入 / 离开 / 销毁房间，查询房间列表，修改直播信息（名称、公告等），监听直播状态（如被踢出、结束）。 |  |
| **DeviceStore** | 音视频设备控制：麦克风（开关 / 音量）、摄像头（开关 / 切换 / 画质）、屏幕共享，设备状态实时监听。 |  |
| **CoGuestStore** | 观众连麦管理：连麦申请 / 邀请 / 同意 / 拒绝，连麦成员权限控制（麦克风 / 摄像头），状态同步。 |  |
| **CoHostStore** | 主播跨房连线：支持多布局模板（动态网格等），发起 / 接受 / 拒绝连线，连麦主播互动管理。 |  |
| **BattleStore** | 主播 PK 对战：发起 PK（配置时长 / 对手），管理 PK 状态（开始 / 结束），同步分数，监听对战结果。 |  |
| **GiftStore** | 礼物互动：获取礼物列表，发送 / 接收礼物，监听礼物事件（含发送者、礼物详情）。 |  |
| **BarrageStore** | 弹幕功能：发送文本 / 自定义弹幕，维护弹幕列表，实时监听弹幕状态。 |  |
| **LikeStore** | 点赞互动：发送点赞，监听点赞事件，同步总点赞数。 |  |
| **LiveAudienceStore** | 观众管理：获取实时观众列表（ID / 名称 / 头像），统计观众数量，监听观众进出事件。 |  |
| **AudioEffectStore** | 音频特效：变声（童声 / 男声）、混响（KTV 等）、耳返调节，实时切换特效。 |  |
| **BaseBeautyStore** | 基础美颜：调节磨皮 / 美白 / 红润（0-100 级），重置美颜状态，同步效果参数。 |  |

## Platform: Android

## 准备工作

### 步骤1：开通服务

请参见 开通服务，获取体验版或付费版 SDK。

### 步骤2：在当前项目中导入 AtomicXCore

**安装组件**：请在您的 **build.gradle** 文件中添加 `implementation 'com.tencent.atomicx:atomicxcore:latest'` 依赖，然后执行 **Gradle Sync**。
``` gradle
dependencies {
    implementation 'io.trtc.uikit:atomicx-core:latest.release'
    api "io.trtc.uikit:rtc_room_engine:3.4.0.1306"
    api "io.trtc.uikit:atomicx-core:3.4.0.1307"
    api "com.tencent.liteav:LiteAVSDK_Professional:12.8.0.19279"
    api "com.tencent.imsdk:imsdk-plus:8.7.7201"
    // 其他依赖...
}
```

### 步骤3：实现登录逻辑

在您的项目中调用 `LoginStore.shared.login`完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 App 自身的用户账户登录成功后，再调用 LoginStore.shared.login，以确保登录业务逻辑的清晰和一致。
> 

``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.login.LoginStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import android.util.Log

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        LoginStore.shared.login(
            this,              // context
            1400000001,        // 替换为您的 SDKAppID
            "test_001",        // 替换为您的 UserID
            "xxxxxxxxxxx",     // 替换为您的 UserSig
            object : CompletionHandler {
                override fun onSuccess() {
                    // 登录成功处理
                    Log.d("Login", "login success");
                }

                override fun onFailure(code: Int, desc: String) {
                    // 登录失败处理
                    Log.e("Login", "login failed, code: $code, error: $desc");
                }
            }
        )
    }
}
```

**登录接口参数说明：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| sdkAppId | Int | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| userID | String | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| userSig | String | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者 通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 UserSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 UserSig 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建基础直播间

### 步骤1：实现主播视频开播

主播开播流程如下，您只需执行以下几步操作，即可快速搭建主播视频直播。

> **提示：**
> 

> 主播开播推流的业务代码，您也可以参考 TUILiveKit 开源项目中的 [VideoLiveAnchorActivity.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/livestream/VideoLiveAnchorActivity.kt) 文件来了解完整的实现逻辑。
> 

1. **初始化主播推流的视图**

   在您的主播 Activity 中，创建一个 `LiveCoreView` 实例，并将 `viewType` 指定为 `PUSH_VIEW`（推流视图）。

   ``` java
   import io.trtc.tuikit.atomicxcore.api.view.CoreViewType
   import io.trtc.tuikit.atomicxcore.api.view.LiveCoreView
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import androidx.constraintlayout.widget.ConstraintLayout
   
   // YourAnchorActivity 代表您的主播开播Activity
   class YourAnchorActivity : AppCompatActivity() {
   
       // 1. 将 LiveCoreView 作为您Activity的一个属性
       private lateinit var coreView: LiveCoreView
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           // 2. 初始化视图
           coreView = LiveCoreView(this, viewType = CoreViewType.PUSH_VIEW)
           coreView.setLiveID("test_live_001")
   
           // 布局 UI
           setupUI()
       }
   
       private fun setupUI() {
           setContentView(R.layout.activity_anchor)
   
           // 3. 将主播推流页面加载到您的视图上
           val container = findViewById<ConstraintLayout>(R.id.container)
           container.addView(coreView)
   
           // 设置布局参数
           val params = ConstraintLayout.LayoutParams(
               ConstraintLayout.LayoutParams.MATCH_PARENT,
               ConstraintLayout.LayoutParams.MATCH_PARENT
           )
           params.topMargin = 36
           params.bottomMargin = 96
           coreView.layoutParams = params
       }
   }
   
   ```
2. **打开摄像头和麦克风**

   通过调用 **DeviceStore** 的 `openLocalCamera、openLocalMicrophone` 接口打开摄像头和麦克风，**您无需做额外操作，LiveCoreView 会自动预览当前摄像头的视频流**，示例代码如下：

   ``` java
   import androidx.appcompat.app.AppCompatActivity
   import android.os.Bundle
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   
   class YourAnchorActivity : AppCompatActivity() {
       // ... 其他代码 ...
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // 布局 UI
           setupUI()
           // 打开设备
           openDevices()
       }
   
       private fun openDevices() {
           // 1. 打开前置摄像头
           DeviceStore.shared().openLocalCamera(true, completion = null)
           // 2. 打开麦克风
           DeviceStore.shared().openLocalMicrophone(completion = null)
       }
   }
   ```
3. **开始直播**

   通过调用 **LiveListStore** 的 `createLive` 接口开始视频直播，完整示例代码如下：

   ``` java
   import android.util.Log
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   
   class YourAnchorActivity : AppCompatActivity() {
   
       // ... 其他代码 ...
   
       // 调用开播接口开始直播
       private fun startLive() {
           val liveInfo = LiveInfo().apply {
               // 1. 设置直播的房间 id
               liveID = "test_live_001"
               // 2. 设置直播的房间名称
               liveName = "test 直播"
               // 3. 配置布局模板，默认：600 动态宫格布局
               seatLayoutTemplateID = 600 
               // 4. 配置主播始终在麦位上
               keepOwnerOnSeat = true
           }
           // 5. 调用 LiveListStore.shared().createLive 开始直播
           LiveListStore.shared().createLive(liveInfo, object: LiveInfoCompletionHandler {
               override fun onFailure(code: Int, desc: String) {
                   Log.e("Live", "Response startLive onError: $desc")
               }
   
               override fun onSuccess(liveInfo: LiveInfo) {
                   Log.d("Live", "Response startLive onSuccess")
               }
           })
       }
   }
   ```

   `LiveInfo`** 参数说明**

| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符。 |
| `liveName` | `String` | 选填 | 直播间的标题。 |
| `notice` | `String` | 选填 | 直播间的公告信息。 |
| `isMessageDisable` | `Boolean` | 选填 | 是否禁言（`true`：是，`false`：否）。 |
| `isPublicVisible` | `Boolean` | 选填 | 是否公开可见（`true`：是，`false`：否）。 |
| `isSeatEnabled` | `Boolean` | 选填 | 是否启用麦位功能（`true`：是，`false`：否）。 |
| `keepOwnerOnSeat` | `Boolean` | 选填 | 是否保持房主在麦位上。 |
| `maxSeatCount` | `Int` | 必填 | 最大麦位数量。 |
| `seatMode` | TakeSeatMode | 选填 | 上麦模式（`FREE`：自由上麦，`APPLY`：申请上麦）。 |
| `seatLayoutTemplateID` | `Int` | 必填 | 麦位布局模板 `ID`。 |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址。 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址。 |
| `categoryList` | `List<Int>` | 选填 | 直播间的分类标签列表。 |
| `activityStatus` | `Int` | 选填 | 直播活动状态。 |
| `isGiftEnabled` | `Boolean` | 选填 | 是否启用礼物功能（`true`：是，`false`：否）。 |

4. **结束直播**

   直播结束后，主播可以调用 **LiveListStore** 的 `endLive` 接口结束直播。SDK 会处理停止推流和销毁房间的逻辑。

   ``` java
   import android.util.Log
   import androidx.appcompat.app.AppCompatActivity
   import com.tencent.cloud.tuikit.engine.extension.TUILiveListManager
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import io.trtc.tuikit.atomicxcore.api.live.StopLiveCompletionHandler
   
   class YourAnchorActivity : AppCompatActivity() {
   
       // ... 其他代码 ...
   
       // 结束直播
       private fun stopLive() {
           LiveListStore.shared().endLive(object : StopLiveCompletionHandler {
               override fun onSuccess(statisticsData: TUILiveListManager.LiveStatisticsData) {
                   Log.d("Live", "endLive success, duration: ${statisticsData.liveDuration}, totalViewers: ${statisticsData.totalViewers}")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   Log.e("Live", "endLive error: $desc")
               }
           })
       }
   
       // 确保在 Activity 销毁时也调用结束直播
       override fun onDestroy() {
           super.onDestroy()
           stopLive()
           // 关闭本地设备
           DeviceStore.shared().closeLocalCamera()
           DeviceStore.shared().closeLocalMicrophone()
           Log.d("Live", "YourAnchorActivity onDestroy")
       }
   }
   ```

### 步骤2：实现观众进房观看

观众观看流程如下，通过简单几步操作，即可实现观众观看直播。

> **提示：**
> 

> 观众进房拉流的业务代码，您也可以参考 TUILiveKit 开源项目中的 [VideoLiveAudienceActivity.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/livestream/VideoLiveAudienceActivity.kt) 文件来了解完整的实现逻辑。
> 

1. **添加观众拉流页面**

   在您的观众 Activity 中，创建 `LiveCoreView` 实例，并将 `viewType` 指定为`PLAY_VIEW`（拉流视图）。

   ``` java
   import io.trtc.tuikit.atomicxcore.api.view.CoreViewType
   import io.trtc.tuikit.atomicxcore.api.view.LiveCoreView
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import androidx.constraintlayout.widget.ConstraintLayout
   
   // YourAudienceActivity 代表您的观众观看Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // 1. 初始化观众拉流页面
       private lateinit var coreView: LiveCoreView
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           // 2. 初始化视图
           coreView = LiveCoreView(this, viewType = CoreViewType.PLAY_VIEW)
           coreView.setLiveId("test_live_001")
   
           // UI 布局
           setupUI()
       }
   
       private fun setupUI() {
           setContentView(R.layout.activity_audience)
   
           // 3. 将观众拉流页面加载到您的视图上
           val container = findViewById<ConstraintLayout>(R.id.container)
           container.addView(coreView)
   
           // 设置布局参数
           val params = ConstraintLayout.LayoutParams(
               ConstraintLayout.LayoutParams.MATCH_PARENT,
               ConstraintLayout.LayoutParams.MATCH_PARENT
           )
           params.topMargin = 36
           params.bottomMargin = 96
           coreView.layoutParams = params
       }
   }
   ```
2. **进入直播间观看**

   通过调用 **LiveListStore** 的 `joinLive` 接口加入直播，**您无需做额外操作，LiveCoreView 会自动播放当前房间的视频流**，完整示例代码如下：

   ``` java
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import android.util.Log
   
   // YourAudienceActivity 代表您的观众观看Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // ... 其他代码 ...
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setupUI()
           // 1. 调用 LiveListStore.shared().joinLive 进入直播间
           //       - liveID: 与主播开播同样的 liveID
           LiveListStore.shared().joinLive(liveID, object : LiveInfoCompletionHandler {
               override fun onFailure(code: Int, desc: String) {
                   Log.e("Live", "joinLive error: $desc")
               }
   
               override fun onSuccess(liveInfo: LiveInfo) {
                   Log.d("Live", "joinLive success")
               }
           })
       }
   }
   ```
3. **退出直播**

   观众退出直播间时，需要调用 **LiveListStore** 的 `leaveLive` 接口退出直播。SDK 会自动停止拉流并退出房间。

   ``` java
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import androidx.appcompat.app.AppCompatActivity
   import android.util.Log
   
   // YourAudienceActivity 代表您的观众观看Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // ... 其他代码 ...
   
       // 退出直播
       private fun leaveLive() {
           LiveListStore.shared().leaveLive(object : CompletionHandler {
               override fun onSuccess() {
                   Log.d("Live", "leaveLive success")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   Log.e("Live", "leaveLive error: $desc")
               }
           })
       }
   
       // 确保在 Activity 销毁时也调用退出直播
       override fun onDestroy() {
           super.onDestroy()
           leaveLive()
           Log.d("Live", "YourAudienceActivity onDestroy")
       }
   }
   ```

### 步骤3：监听直播事件

在观众加入直播间后，您还需要处理一些房间内的“被动”事件。例如主播主动结束了直播，或者观众因为违规等原因被踢出房间。如果不监听这些事件，观众端 UI 可能会停留在黑屏页面，影响用户体验。

您可以通过实现 `LiveListListener` 监听器，并将其注册到 `LiveListStore` 来实现事件监听。
``` java
import io.trtc.tuikit.atomicxcore.api.live.LiveEndedReason
import io.trtc.tuikit.atomicxcore.api.live.LiveKickedOutReason
import io.trtc.tuikit.atomicxcore.api.live.LiveListListener
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import android.util.Log

// YourAudienceActivity 代表您的观众观看Activity
class YourAudienceActivity : AppCompatActivity() {
    
    // ... (coreView, liveId, setupUI, joinLive, leaveLive 等代码) ...

    // 2. 定义一个 LiveListListener 实例
    private val liveListListener = object : LiveListListener() {
        override fun onLiveEnded(liveID: String, reason: LiveEndedReason, message: String) {
            // 监听到直播结束
            Log.d("Live", "Live ended. liveID: $liveID, reason: $reason, message: $message")
            // 在此处处理退出直播间的逻辑，例如关闭当前 Activity
            // finish()
        }

        override fun onKickedOutOfLive(liveID: String, reason: LiveKickedOutReason, message: String) {
            // 监听到被踢出直播
            Log.d("Live", "Kicked out of live. liveID: $liveID, reason: $reason, message: $message")
            // 在此处处理退出直播间的逻辑
            // finish()
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setupUI() 
        
        // 3. 注册监听器
        LiveListStore.shared().addLiveListListener(liveListListener)

        // 4. 进入直播间
        joinLive()
    }
    
    // ... joinLive() 和 leaveLive() 方法 ...

    override fun onDestroy() {
        super.onDestroy()
        // 5. 确保在 Activity 销毁时移除监听器，防止内存泄漏
        LiveListStore.shared().removeLiveListListener(liveListListener)
        leaveLive()
        Log.d("Live", "YourAudienceActivity onDestroy")
    }
}
```

### 运行效果

集成 **LiveCoreView** 后，您将得到一个纯净的视频渲染视图，它已具备完整的直播业务能力，但没有任何交互 UI。您可以参考下一章节 丰富直播场景 来完善直播场景。
|  | **动态宫格布局** | **浮动小窗布局** | **固定宫格布局** | **固定小窗布局** |
| --- | --- | --- | --- | --- |
| **模板 ID** | 600 | 601 | 800 | 801 |
| **描述** | 默认布局，可根据连麦人数动态调整宫格大小。 | 连麦嘉宾以浮动小窗形式显示。 | 连麦人数固定，每个嘉宾占据一个固定宫格。 | 连麦人数固定，嘉宾以固定小窗形式显示。 |
| **运行效果** |  |  |  |  |

## 丰富直播场景

当您完成了基础的直播功能后，您可以参考以下功能指南来为直播添加丰富的互动玩法。
| **直播功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现观众音视频连线** | 观众申请上麦，与主播进行实时视频互动。 | CoGuestStore | 观众连线 |
| **实现主播跨房连线 PK** | 两个不同房间的主播进行连线，实现互动或 PK。 | CoHostStore BattleStore | 主播连线 & PK |
| **添加弹幕聊天功能** | 观众可以在直播间发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## Platform: iOS

## 准备工作

### 步骤1：开通服务

请参见 开通服务，获取体验版或付费版 SDK。

### 步骤2：在当前项目中导入 AtomicXCore
1. **安装组件**：请在您的 **Podfile** 文件中添加 `pod 'AtomicXCore'` 依赖，然后执行`pod install`。

   ``` ruby
   target 'xxxx' do
     pod 'AtomicXCore'
   end
   ```
2. **配置工程权限:  **请在应用的 `Info.plist` 文件中添加相机和麦克风的使用权限说明。

   ``` ruby
   <key>NSCameraUsageDescription</key>
   <string>TUILiveKit需要访问你的相机权限，开启后录制的视频才会有画面</string>
   <key>NSMicrophoneUsageDescription</key>
   <string>TUILiveKit需要访问您的麦克风权限，开启后录制的视频才会有声音</string>
   ```
   

### 步骤3：实现登录逻辑

在您的项目中调用 `LoginStore.shared.login` 完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 App 自身的用户账户登录成功后，再调用 LoginStore.shared.login，以确保登录业务逻辑的清晰和一致。
> 

``` swift
import AtomicXCore

//  AppDelegate.swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    LoginStore.shared.login(sdkAppID: 1400000001,                  // 替换为您的 SDKAppID
                            userID: "test_001",                    // 替换为您的 UserID
                            userSig: "xxxxxxxxxxx") { result in    // 替换为您的 UserSig
      switch result {
        case .success(let info):
        debugPrint("login success")
        case .failure(let error):
        debugPrint("login failed code:\(error.code), message:\(error.message)")
      }
    }
    return true
}
```

**登录接口参数说明：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| sdkAppId | Int32 | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| userID | String | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| userSig | String | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 UserSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 UserSig 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建基础直播间

### 步骤1：实现主播视频开播

主播开播流程如下，您只需执行以下几步操作，即可快速搭建主播视频直播。

> **提示：**
> 

> 主播开播推流的业务代码，您也可以参考 TUILiveKit 开源项目中的 [TUILiveRoomAnchorViewController.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/LiveStream/TUILiveRoomAnchorViewController.swift) 文件来了解完整的实现逻辑。
> 

1. **初始化主播推流的视图**

   在您的主播视图控制器中，创建一个 `LiveCoreView` 实例，并将 `viewType` 指定为 `.pushView`（推流视图）。

   ``` swift
   import AtomicXCore
   
   // YourAnchorViewController 代表您的主播开播视图控制器
   class YourAnchorViewController: UIViewController {
   
       private let liveId: String
       // 1. 将 LiveCoreView 作为您视图控制器的一个属性
       private let coreView = LiveCoreView(viewType:.pushView, frame: UIScreen.main.bounds)
      
       // 2. 新增便利构造函数完成实例初始化
       //    - liveId: 您需要开播的直播房间 id
       public init(liveId: String) {
           self.liveId = liveId
           super.init(nibName: nil, bundle: nil)
           self.coreView.setLiveID(liveId)
       }
       
       required init?(coder: NSCoder) {
           fatalError("init(coder:) has not been implemented")
       }
       
       public override func viewDidLoad() {
           super.viewDidLoad()
           // 3. 将主播推流页面加载到您的视图上
           view.addSubview(coreView)
       }
   }  
   ```
2. **打开摄像头和麦克风**

   通过调用 **DeviceStore** 的 `openLocalCamera、openLocalMicrophone` 接口打开摄像头和麦克风，**您无需做额外操作，LiveCoreView 会自动预览当前摄像头的视频流**，示例代码如下：

   ``` swift
   import AtomicXCore
   
   // YourAnchorViewController 代表您的主播开播视图控制器
   class YourAnchorViewController: UIViewController {
       // ... 其他代码 ...
       public override func viewDidLoad() {
           super.viewDidLoad()
           // 打开设备
           openDevices()
       }
       
       private func openDevices() {
           // 1. 打开前置摄像头 
           DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
           // 2. 打开麦克风
           DeviceStore.shared.openLocalMicrophone(completion: nil)
       }
   }
   ```
3. **开始直播**

   通过调用 **LiveListStore** 的 `createLive` 接口开始视频直播，完整示例代码如下：

   ``` swift
   import AtomicXCore
   
   // YourAnchorViewController 代表您的主播开播视图控制器
   class YourAnchorViewController: UIViewController {
   
       // ... 其他代码 ...
   
       // 调用开播接口开始直播
       private func startLive() {
           var liveInfo = LiveInfo()
           // 1. 设置直播的房间 id
           liveInfo.liveID = self.liveId
           // 2. 设置直播的房间名称
           liveInfo.liveName = "test 直播"
           // 3. 配置布局模板，默认：600 动态宫格布局
           liveInfo.seatLayoutTemplateID = 600
           // 4. 配置主播始终在麦位上
           liveInfo.keepOwnerOnSeat = true
           // 5. 调用 LiveListStore.shared.createLive 开始直播
           LiveListStore.shared.createLive(liveInfo) { [weak self] result in
               guard let self = self else { return }
               switch result {
               case .success(let info):
                   debugPrint("startLive success")
               case .failure(let error):
                   debugPrint("startLive error:\(error.message)")
               }
           }
       }
   }  
   ```

   `LiveInfo`**参数说明:**

| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符。 |
| `liveName` | `String` | 选填 | 直播间的标题。 |
| `notice` | `String` | 选填 | 直播间的公告信息。 |
| `isMessageDisable` | `Bool` | 选填 | 是否禁言（`true`：是，`false`：否）。 |
| `isPublicVisible` | `Bool` | 选填 | 是否公开可见（`true`：是，`false`：否）。 |
| `isSeatEnabled` | `Bool` | 选填 | 是否启用麦位功能（`true`：是，`false`：否）。 |
| `keepOwnerOnSeat` | `Bool` | 选填 | 是否保持房主在麦位上。 |
| `maxSeatCount` | `Int` | 必填 | 最大麦位数量。 |
| `seatMode` | `TakeSeatMode` | 选填 | 上麦模式（`.free`：自由上麦，`.apply`：申请上麦）。 |
| `seatLayoutTemplateID` | `UInt` | 必填 | 麦位布局模板 `ID。` |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址。 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址。 |
| `categoryList` | `[NSNumber]` | 选填 | 直播间的分类标签列表。 |
| `activityStatus` | `Int` | 选填 | 直播活动状态。 |
| `isGiftEnabled` | `Bool` | 选填 | 是否启用礼物功能（`true`：是，`false`：否）。 |

4. **结束直播**

   直播结束后，主播可以调用 **LiveListStore** 的 `endLive` 接口结束直播。SDK 会处理停止推流和销毁房间的逻辑。

   ``` swift
   import AtomicXCore
   
   // YourAnchorViewController 代表您的主播开播视图控制器
   class YourAnchorViewController: UIViewController {
   
       // ... 其他代码 ...
   
       // 结束直播
       private func stopLive() {
           LiveListStore.shared.endLive { [weak self] result in
               guard let self = self else { return }
               switch result {
               case .success(let data):
                   debugPrint("endLive success")
               case .failure(let error):
                   debugPrint("endLive error: \(error.message)")
               }
           }
       }
   }
   ```

### 步骤2：实现观众进房观看

观众观看流程如下，通过简单几步操作，即可实现观众观看直播。

> **提示：**
> 

> 观众进房拉流的业务代码，您也可以参考 TUILiveKit 开源项目中的 [TUILiveRoomAudienceViewController.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/LiveStream/TUILiveRoomAudienceViewController.swift) 文件来了解完整的实现逻辑。
> 

1. **实现观众拉流页面**

   在您的观众视图控制器中，创建 `LiveCoreView` 实例，并将 `viewType` 指定为 `.playView`（拉流视图）。

   ``` swift
   import AtomicXCore
   
   // YourAudienceViewController 代表您的观众观看视图控制器
   class YourAudienceViewController: UIViewController {
      
       // 1. 初始化观众拉流页面
       private let coreView = LiveCoreView(viewType:.playView, frame: UIScreen.main.bounds)
       private let liveId: String
       
       public init(liveId: String) {
           self.liveId = liveId
           super.init(nibName: nil, bundle: nil)
           // 2. 绑定直播 id
           self.coreView.setLiveID(liveId)
       }
       
       required init?(coder: NSCoder) {
           fatalError("init(coder:) has not been implemented")
       }
       
       public override func viewDidLoad() {
           super.viewDidLoad()
           // 3. 将主播推流页面加载到您的视图上
           view.addSubview(coreView)
       }
   }  
   ```
2. **进入直播间观看**

   通过调用 **LiveListStore** 的 `joinLive` 接口加入直播，**您无需做额外操作，LiveCoreView 会自动播放当前房间的视频流**，完整示例代码如下：

   ``` swift
   import AtomicXCore
   
   // YourAudienceViewController 代表您的观众观看视图控制器
   class YourAudienceViewController: UIViewController {
       // ... 其他代码 ...
   
       public override func viewDidLoad() {
           super.viewDidLoad()
           // 3. 将主播推流页面加载到您的视图上
           view.addSubview(coreView)
   
           // 4. 进入直播间
           joinLive()
       }
   
       private func joinLive() {
            // 调用 LiveListStore.shared.joinLive 进入直播间
           // - liveId: 与主播开播同样的 liveId
           LiveListStore.shared.joinLive(liveID: liveId) { [weak self] result in
               guard let self = self else { return }
               switch result {
               case .success(let info):
                   debugPrint("joinLive success")
               case .failure(let error):
                   debugPrint("joinLive error \(error.message)")
                   // 进房失败，也需要退出页面
                   // self.dismiss(animated: true)
               }
           }
       }
   }
   ```
3. **退出直播**

   观众退出直播间时，需要调用 **LiveListStore** 的 `leaveLive` 接口退出直播。SDK 会自动停止拉流并退出房间。

   ``` swift
   import AtomicXCore
   
   // YourAudienceViewController 代表您的观众观看视图控制器
   class YourAudienceViewController: UIViewController {
   
       // ... 其他代码 ...
   
       // 退出直播
       private func leaveLive() {
           LiveListStore.shared.leaveLive { [weak self] result in
               guard let self = self else { return }
               switch result {
               case .success:
                   debugPrint("leaveLive success")
               case .failure(let error):
                   debugPrint("leaveLive error \(error.message)")
               }
           }
       }
   }
   ```

### 步骤3：监听直播事件

在观众加入直播间后，您还需要处理一些房间内的“被动”事件。例如，主播主动结束了直播，或者观众因为违规等原因被踢出房间。如果不监听这些事件，观众端 UI 可能会停留在黑屏页面，影响用户体验。

您可以通过订阅 `LiveListStore` 提供的 `liveListEventPublisher` 来实现事件监听。
``` swift
import AtomicXCore
import Combine // 1. 导入 Combine 框架

// YourAudienceViewController 代表您的观众观看视图控制器
class YourAudienceViewController: UIViewController {
   
    // ... 其他代码 (coreView, liveId, init, deinit, joinLive, leaveLive等) ...

    // 2. 定义 cancellableSet 来管理订阅生命周期
    private var cancellableSet: Set<AnyCancellable> = []
    
    public override func viewDidLoad() {
        super.viewDidLoad()
        view.addSubview(coreView)
        // 3. 监听直播事件
        setupLiveEventListener()
        // 4. 进入直播间
        joinLive()
    }

    // 5. 新增一个方法来设置事件监听
    private func setupLiveEventListener() {
        LiveListStore.shared.liveListEventPublisher
            .receive(on: RunLoop.main) // 确保在主线程处理 UI 更新
            .sink { [weak self] event in
                guard let self = self else { return }
                switch event {
                case .onLiveEnded(let liveID, let reason, let message):
                    // 监听到直播结束
                    debugPrint("Live ended. liveID: \(liveID), reason: \(reason.rawValue), message: \(message)")
                    // 在此处处理退出直播间的逻辑，例如关闭当前页面
                    // self.dismiss(animated: true)
                case .onKickedOutOfLive(let liveID, let reason, let message):
                    // 监听到被踢出直播
                    debugPrint("Kicked out of live. liveID: \(liveID), reason: \(reason.rawValue), message: \(message)")
                    // 在此处处理退出直播间的逻辑
                    // self.dismiss(animated: true)
                }
            }
            .store(in: &cancellableSet) // 管理订阅
    }
    
    // ... joinLive() 和 leaveLive() 方法 ...
}
```

### 运行效果

集成 **LiveCoreView** 后，您将得到一个纯净的视频渲染视图，它已具备完整的直播业务能力，但没有任何交互 UI。您可以参考下一章节 丰富直播场景 来完善直播场景。
|  | **动态宫格布局** | **浮动小窗布局** | **固定宫格布局** | **固定小窗布局** |
| --- | --- | --- | --- | --- |
| **模板 ID** | 600 | 601 | 800 | 801 |
| **描述** | 默认布局，可根据连麦人数动态调整宫格大小。 | 连麦嘉宾以浮动小窗形式显示。 | 连麦人数固定，每个嘉宾占据一个固定宫格。 | 连麦人数固定，嘉宾以固定小窗形式显示。 |
| **运行效果** |  |  |  |  |

## 丰富直播场景

当您完成了基础的直播功能后，您可以参考以下功能指南来为直播添加丰富的互动玩法。
| **直播功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现观众音视频连线** | 观众申请上麦，与主播进行实时视频互动。 | CoGuestStore | 实现观众连线 |
| **实现主播跨房连线 PK** | 两个不同房间的主播进行连线，实现互动或 PK。 | CoHostStore BattleStore | 实现主播连线 PK |
| **添加弹幕聊天功能** | 观众可以在直播间发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## Platform: Flutter

## 准备工作

### 步骤1：开通服务

请参见 开通服务，获取体验版或付费版 SDK。

### 步骤2：在当前项目中导入 AtomicXCore
1. **安装组件**：请在您的 `pubspec.yaml` 文件中添加 `atomic_x_core` 依赖，然后执行 `flutter pub get`。

   ``` yaml
   dependencies:
     atomic_x_core: ^3.6.0
   ```
2. **配置工程权限：**Android 和 iOS 工程都需要配置权限。

   

【Android】

请在 `android/app/src/main/AndroidManifest.xml` 文件中添加相机和麦克风的使用权限说明。
``` xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

【iOS】

请在应用 `iOS` 目录的 **Podfile** 和 `ios/Runner` 目录的 `Info.plist` 文件中添加相机和麦克风的使用权限说明。

**Podfile**
``` ruby
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
      target.build_configurations.each do |config|
          config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
        '$(inherited)',
        'PERMISSION_MICROPHONE=1',
        'PERMISSION_CAMERA=1',
        ]
      end
  end
end
```

**Info.plist**
``` xml
<key>NSCameraUsageDescription</key>
<string>TUILiveKit需要访问你的相机权限，开启后录制的视频才会有画面</string>
<key>NSMicrophoneUsageDescription</key>
<string>TUILiveKit需要访问您的麦克风权限，开启后录制的视频才会有声音</string>
```

### 步骤3：实现登录逻辑

在您的项目中调用 `LoginStore.shared.login` 完成登录，**这是使用 AtomicXCore 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 App 自身的用户账户登录成功后，再调用 LoginStore.shared.login，以确保登录业务逻辑的清晰和一致。
> 

``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  final result = await LoginStore.shared.login(
    sdkAppID: 1400000001,           // 替换为您的 sdkAppID
    userID: "test_001",             // 替换为您的 userID
    userSig: "xxxxxxxxxxx",         // 替换为您的 userSig
  );

  if (result.isSuccess) {
    debugPrint("login success");
  } else {
    debugPrint("login failed code: ${result.errorCode}, message: ${result.errorMessage}");
  }

  runApp(const MyApp());
}
```

**登录接口参数说明：**
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `sdkAppID` | `int` | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| `userID` | `String` | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| `userSig` | `String` | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 UserSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 UserSig 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建基础直播间

### 步骤1：实现主播开播

主播开播涉及“预览”和“直播”两个阶段。由于底层视频渲染机制不同，我们需要构建一个状态机来管理视图切换，从而避免预览时出现黑屏。
1. **核心逻辑与状态定义**

   首先，在您的 State 类中定义核心控制器，并增加一个 _isLiveStarted 标记，用于区分当前是“预览态”还是“直播态”。

   ``` java
   class _YourAnchorPageState extends State<YourAnchorPage> {
     // 核心控制器
     late final LiveCoreController _controller;
     // 状态标记：false = 预览中; true = 直播中
     bool _isLiveStarted = false; 
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create(CoreViewType.pushView);
       _controller.setLiveID(widget.liveId);
       
       // 初始化时直接打开设备（详见下一步）
       _openDevices();
     }
   }
   ```
2. **打开摄像头和麦克风**

   通过调用 **DeviceStore** 的 `openLocalCamera`、`openLocalMicrophone` 接口打开摄像头和麦克风，**您无需做额外操作，LiveCoreWidget 会自动预览当前摄像头的视频流**，示例代码如下：
   

   > **注意：**
   > 

   > 此步骤对于预览至关重要。只有打开设备，后续的 VideoView 才有画面可渲染。
   > 

   ``` java
   void _openDevices() {
       // 1. 打开前置摄像头 (true 表示前置)
       DeviceStore.shared.openLocalCamera(true);
       // 2. 打开麦克风
       DeviceStore.shared.openLocalMicrophone();
     }
   ```
3. **构建预览视图**

   这是最关键的一步。我们需要根据 `_isLiveStarted` 的状态选择不同的组件：

- **预览态：**使用 `VideoView` + `setLocalVideoView`（直接渲染本地采集数据）。

- **直播态：**使用 `LiveCoreWidget`（渲染直播间流数据）。

   ``` java
   // 3.1 封装预览视图组件
     Widget _buildLocalPreview() {
       return Container(
         color: Colors.black,
         child: VideoView(
           onViewCreated: (viewId) {
             // 关键：将本地摄像头画面绑定到预览 View
             TUIRoomEngine.sharedInstance().setLocalVideoView(viewId);
           },
           onViewDisposed: (viewId) {
             // 销毁时解绑
             TUIRoomEngine.sharedInstance().setLocalVideoView(0);
           },
         ),
       );
     }
   
     // 3.2 组合主视图
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             // 视图层：根据状态自动切换
             Positioned.fill(
               child: _isLiveStarted 
                   ? LiveCoreWidget(controller: _controller) // 直播中：用核心组件
                   : _buildLocalPreview(),                   // 预览中：用 VideoView
             ),
             
             // UI 层：开播按钮（仅在预览时显示）
             if (!_isLiveStarted)
               Positioned(
                 bottom: 50, left: 0, right: 0,
                 child: Center(
                   child: ElevatedButton(
                     onPressed: _startLive, // 下一步实现此方法
                     child: const Text('开始直播'),
                   ),
                 ),
               ),
           ],
         ),
       );
     }
   ```
4. **实现开播与关播逻辑**

   通过调用 **LiveListStore** 的 `createLive` 接口开始视频直播，示例代码如下：

   ``` java
   Future<void> _startLive() async {
       // 1. 配置直播间参数
       final liveInfo = LiveInfo(
         liveID: widget.liveId,         // 房间唯一标识
         liveName: "Atomic Live Demo",  // 房间标题
         seatLayoutTemplateID: 600,     // 布局模式：600 代表默认动态宫格
         keepOwnerOnSeat: true,         // 房主是否始终占有一个麦位
       );
   
       // 2. 发起开播请求
       final result = await LiveListStore.shared.createLive(liveInfo);
   
       if (result.isSuccess) {
         debugPrint("开播成功");
         // 3. 关键步骤：更新状态
         // 将 _isLiveStarted 置为 true，触发 build 方法重新构建，
         // 从而将 UI 从 VideoView（预览）切换为 LiveCoreWidget（直播）。
         setState(() {
           _isLiveStarted = true;
         });
       } else {
         debugPrint("开播失败: ${result.errorMessage}");
         // 可以在此处添加 Toast 提示用户
       }
     }
   ```

   **LiveInfo 参数说明：**

| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符。 |
| `liveName` | `String` | 选填 | 直播间的标题。 |
| `notice` | `String` | 选填 | 直播间的公告信息。 |
| `isMessageDisable` | `bool` | 选填 | 是否禁言（`true`：是，`false`：否）。 |
| `isPublicVisible` | `bool` | 选填 | 是否公开可见（`true`：是，`false`：否）。 |
| `isSeatEnabled` | `bool` | 选填 | 是否启用麦位功能（`true`：是，`false`：否）。 |
| `keepOwnerOnSeat` | `bool` | 选填 | 是否保持房主在麦位上。 |
| `maxSeatCount` | `int` | 选填 | 最大麦位数量。 |
| `seatMode` | `TakeSeatMode` | 选填 | 上麦模式（`TakeSeatMode.free`：自由上麦，`TakeSeatMode.apply`：申请上麦）。 |
| `seatLayoutTemplateID` | `int` | 必填 | 麦位布局模板 ID。 |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址。 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址。 |
| `categoryList` | `List<int>` | 选填 | 直播间的分类标签列表。 |
| `activityStatus` | `int` | 选填 | 直播活动状态。 |
| `isGiftEnabled` | `bool` | 选填 | 是否启用礼物功能（`true`：是，`false`：否）。 |

5. **结束直播**

   直播结束后，主播可以调用 **LiveListStore** 的 `endLive` 接口结束直播。SDK 会处理停止推流和销毁房间的逻辑。

   ``` java
   Future<void> _stopLive() async {
       // 1. 调用接口结束直播
       // SDK 内部会自动停止推流、销毁房间并清理媒体资源
       final result = await LiveListStore.shared.endLive();
   
       if (result.isSuccess) {
         debugPrint("关播成功");
         // 2. 退出当前页面
         if (mounted) {
           Navigator.of(context).pop();
         }
       } else {
         debugPrint("关播失败: ${result.errorMessage}");
       }
     }
   ```
6. **完整代码参考**

   为了方便您快速集成，我们将上述步骤整合为完整的 `YourAnchorPage` 文件。您可以直接复制以下代码：

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart'; // 引入 RTC 引擎
   
   class YourAnchorPage extends StatefulWidget {
     final String liveId;
     const YourAnchorPage({super.key, required this.liveId});
   
     @override
     State<YourAnchorPage> createState() => _YourAnchorPageState();
   }
   
   class _YourAnchorPageState extends State<YourAnchorPage> {
     late final LiveCoreController _controller;
     bool _isLiveStarted = false; // 直播状态标记
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create(CoreViewType.pushView);
       _controller.setLiveID(widget.liveId);
       _openDevices(); // 步骤2：打开设备
     }
   
     void _openDevices() {
       DeviceStore.shared.openLocalCamera(true);
       DeviceStore.shared.openLocalMicrophone();
     }
   
     // 步骤3：构建预览
     Widget _buildLocalPreview() {
       return Container(
         color: Colors.black,
         child: VideoView(
           onViewCreated: (viewId) => TUIRoomEngine.sharedInstance().setLocalVideoView(viewId),
           onViewDisposed: (viewId) => TUIRoomEngine.sharedInstance().setLocalVideoView(0),
         ),
       );
     }
   
     // 步骤4：开播逻辑
     Future<void> _startLive() async {
       final liveInfo = LiveInfo(
         liveID: widget.liveId,
         liveName: "Test Live",
         seatLayoutTemplateID: 600,
         keepOwnerOnSeat: true,
       );
   
       final result = await LiveListStore.shared.createLive(liveInfo);
       if (result.isSuccess) {
         setState(() {
           _isLiveStarted = true; // 切换 UI 状态
         });
       }
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             // 视图自动切换
             Positioned.fill(
               child: _isLiveStarted
                   ? LiveCoreWidget(controller: _controller)
                   : _buildLocalPreview(),
             ),
             // 开播按钮
             if (!_isLiveStarted)
               Positioned(
                 bottom: 50, left: 0, right: 0,
                 child: Center(
                   child: ElevatedButton(onPressed: _startLive, child: const Text('开始直播')),
                 ),
               ),
           ],
         ),
       );
     }
   }
   ```

### 步骤2：实现观众进房观看

观众观看流程如下，通过简单几步操作，即可实现观众观看直播。

> **提示：**
> 

> 观众进房拉流的业务代码，您也可以参考 TUILiveKit 开源项目中的 [audience_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/audience/audience_widget.dart) 文件来了解完整的实现逻辑。
> 

1. **实现观众拉流页面**

   在您的观众页面中，创建 `LiveCoreWidget` 实例，并通过 `LiveCoreController` 控制直播行为。
   

   > **注意：**
   > 

   > **必须确保 LiveCoreWidget 在调用 joinLive() 之前已经完成初始化并处于渲染树中。**
   > 

   > **原理分析：**`LiveCoreWidget` 在 `initState` 中会执行 `controller.init() `以注册对 `currentLive` 信息的监听。如果先调用` joinLive()` 导致房间信息更新，而此时 `Widget` 尚未渲染（监听器未注册），则无法触发内部的自动拉流逻辑，导致画面黑屏。
   > 

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage 代表您的观众观看页面
   class _YourAudiencePageState extends State<YourAudiencePage> {
     late final LiveCoreController _controller;
     bool _isJoining = true;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create(CoreViewType.playView);
       _controller.setLiveID(widget.liveId);
       
       // 修正：在第一帧渲染完成后再进房，确保 LiveCoreWidget 已完成 init() 
       WidgetsBinding.instance.addPostFrameCallback((_) {
         _joinLive();
       });
     }
   
     Future<void> _joinLive() async {
       final result = await LiveListStore.shared.joinLive(widget.liveId);
       if (result.isSuccess) {
         debugPrint("joinLive success");
         if (mounted) setState(() => _isJoining = false);
       } else {
         debugPrint("joinLive error: ${result.errorMessage}");
       }
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             // 必须始终渲染，不可根据 _isJoining 状态移除，否则会导致监听失效
             LiveCoreWidget(controller: _controller),
             
             if (_isJoining)
               const Center(child: CircularProgressIndicator(color: Colors.white)),
           ],
         ),
       );
     }
   }
   ```
2. **进入直播间观看**

   通过调用 **LiveListStore** 的 `joinLive` 接口加入直播，**您无需做额外操作，LiveCoreWidget 会自动播放当前房间的视频流**，完整示例代码如下：

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage 代表您的观众观看页面
   class YourAudiencePage extends StatefulWidget {
     final String liveId;
   
     const YourAudiencePage({super.key, required this.liveId});
   
     @override
     State<YourAudiencePage> createState() => _YourAudiencePageState();
   }
   
   class _YourAudiencePageState extends State<YourAudiencePage> {
     late final LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
       // 进入直播间
       _joinLive();
     }
   
     Future<void> _joinLive() async {
       // 调用 LiveListStore.shared.joinLive 进入直播间
       // - liveId: 与主播开播同样的 liveId
       final result = await LiveListStore.shared.joinLive(widget.liveId);
   
       if (result.isSuccess) {
         debugPrint("joinLive success");
       } else {
         debugPrint("joinLive error: ${result.errorMessage}");
         // 进房失败，也需要退出页面
         // if (mounted) Navigator.of(context).pop();
       }
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             LiveCoreWidget(controller: _controller),
           ],
         ),
       );
     }
   }
   ```
3. **退出直播**

   观众退出直播间时，需要调用 **LiveListStore** 的 `leaveLive` 接口退出直播。SDK 会自动停止拉流并退出房间。

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage 代表您的观众观看页面
   class YourAudiencePage extends StatefulWidget {
     final String liveId;
   
     const YourAudiencePage({super.key, required this.liveId});
   
     @override
     State<YourAudiencePage> createState() => _YourAudiencePageState();
   }
   
   class _YourAudiencePageState extends State<YourAudiencePage> {
     // ... 其他代码 ...
   
     // 退出直播
     Future<void> _leaveLive() async {
       final result = await LiveListStore.shared.leaveLive();
   
       if (result.isSuccess) {
         debugPrint("leaveLive success");
       } else {
         debugPrint("leaveLive error: ${result.errorMessage}");
       }
     }
   
     // ... 其他代码 ...
   }
   ```

### 步骤3：监听直播事件

在观众加入直播间后，您还需要处理一些房间内的"被动"事件。例如，主播主动结束了直播，或者观众因为违规等原因被踢出房间。如果不监听这些事件，观众端 UI 可能会停留在黑屏页面，影响用户体验。

您可以通过 `LiveListListener` 来实现事件监听。
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// YourAudiencePage 代表您的观众观看页面
class YourAudiencePage extends StatefulWidget {
  final String liveId;

  const YourAudiencePage({super.key, required this.liveId});

  @override
  State<YourAudiencePage> createState() => _YourAudiencePageState();
}

class _YourAudiencePageState extends State<YourAudiencePage> {
  late final LiveCoreController _controller;
  // 1. 定义 LiveListListener 来管理事件监听
  late final LiveListListener _liveListListener;

  @override
  void initState() {
    super.initState();
    _controller = LiveCoreController.create();
    _controller.setLiveID(widget.liveId);
    // 2. 监听直播事件
    _setupLiveEventListener();
    // 3. 进入直播间
    _joinLive();
  }

  // 4. 新增一个方法来设置事件监听
  void _setupLiveEventListener() {
    _liveListListener = LiveListListener(
      onLiveEnded: (liveID, reason, message) {
        // 监听到直播结束
        debugPrint("Live ended. liveID: $liveID, reason: ${reason.value}, message: $message");
        // 在此处处理退出直播间的逻辑，例如关闭当前页面
        // if (mounted) Navigator.of(context).pop();
      },
      onKickedOutOfLive: (liveID, reason, message) {
        // 监听到被踢出直播
        debugPrint("Kicked out of live. liveID: $liveID, reason: ${reason.value}, message: $message");
        // 在此处处理退出直播间的逻辑
        // if (mounted) Navigator.of(context).pop();
      },
    );
    LiveListStore.shared.addLiveListListener(_liveListListener);
  }

  Future<void> _joinLive() async {
    final result = await LiveListStore.shared.joinLive(widget.liveId);
    if (result.isSuccess) {
      debugPrint("joinLive success");
    } else {
      debugPrint("joinLive error: ${result.errorMessage}");
    }
  }

  Future<void> _leaveLive() async {
    final result = await LiveListStore.shared.leaveLive();
    if (result.isSuccess) {
      debugPrint("leaveLive success");
    } else {
      debugPrint("leaveLive error: ${result.errorMessage}");
    }
  }

  @override
  void dispose() {
    // 5. 移除事件监听
    LiveListStore.shared.removeLiveListListener(_liveListListener);
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(
        children: [
          LiveCoreWidget(controller: _controller),
        ],
      ),
    );
  }
}
```

### 运行效果

集成 **LiveCoreWidget** 后，您将得到一个纯净的视频渲染视图，它已具备完整的直播业务能力，但没有任何交互 UI。您可以参考下一章节 丰富直播场景 来完善直播场景。
|  | **动态宫格布局** | **浮动小窗布局** | **固定宫格布局** | **固定小窗布局** |
| --- | --- | --- | --- | --- |
| **模板 ID** | 600 | 601 | 800 | 801 |
| **描述** | 默认布局，可根据连麦人数动态调整宫格大小。 | 连麦嘉宾以浮动小窗形式显示。 | 连麦人数固定，每个嘉宾占据一个固定宫格。 | 连麦人数固定，嘉宾以固定小窗形式显示。 |

## 丰富直播场景

当您完成了基础的直播功能后，您可以参考以下功能指南来为直播添加丰富的互动玩法。
| **直播功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现观众音视频连线** | 观众申请上麦，与主播进行实时视频互动。 | CoGuestStore | 实现观众连线 |
| **实现主播跨房连线 PK** | 两个不同房间的主播进行连线，实现互动或 PK。 | CoHostStore / BattleStore | 实现主播连线 PK |
| **添加弹幕聊天功能** | 观众可以在直播间发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## 常见问题

### 主播调用 createLive 或 观众调用 joinLive 后为什么画面是黑的，没有视频画面？

> **适用平台：Android / iOS / Flutter**

> **Android/iOS：** - **检查 setLiveID**：请确保在调用开播或观看接口前，已经为 LiveCoreView 实例设置了正确的 liveID。
> **Flutter：** - **检查 setLiveID**：请确保在调用开播或观看接口前，已经为 `LiveCoreController` 实例设置了正确的 `liveID`。

- **检查设备权限**：请确保 App 已获得摄像头和麦克风的系统使用权限。

> **Android：** - **检查主播端**：主播端是否正常调用 `DeviceStore.shared().openLocalCamera(true)` 打开了摄像头。
> **iOS：** - **检查主播端**：主播端是否正常调用 `DeviceStore.shared.openLocalCamera(isFront: true)` 打开了摄像头。
> **Flutter：** - **检查主播端**：主播端是否正常调用 `DeviceStore.shared.openLocalCamera(true)` 打开了摄像头。

- **检查网络**：请检查设备网络连接是否正常。

### 主播端打开摄像头后，开播后可以看到本地视频预览画面，开播前视频预览是黑屏？

> **适用平台：Android / iOS**

> **Android：** - **检查主播端**：请检查主播推流视图`LiveCoreView` 的 `viewType`是否配置为`PUSH_VIEW`。
> **iOS：** - **检查主播端**：请检查主播推流视图`LiveCoreView` 的 `viewType`是否配置为`.pushView`。

> **Android：** - **检查观众端**：请检查观众拉流视图`LiveCoreView` 的 `viewType`是否配置为 `PLAY_VIEW`。
> **iOS：** - **检查观众端**：请检查观众拉流视图`LiveCoreView` 的 `viewType`是否配置为 `.playView`。

### rsync 因权限不足导致依赖三方库资源拷贝失败

> **适用平台：iOS**

例如在使用 Xcode 集成 SnapKit 框架进行开发时，可能会遇到如下编译报错：
``` plaintext
rsync(xxxx):1:1: SnapKit.framework/SnapKit_Privacy.bundle/: mkpathat: Operation not permitted
```

#### 问题原因

> **适用平台：iOS**

Xcode 的用户**脚本沙盒机制**限制了构建过程中 `rsync` 脚本对 `SnapKit.framework` 资源文件的写入权限，导致框架内的 `SnapKit_Privacy.bundle` 无法正常拷贝。

#### 解决步骤

> **适用平台：iOS**

1. 关闭用户脚本沙盒后，打开 Xcode 项目，进入项目的 **Build Settings**，搜索 `User Script Sandboxing`（或标识 `ENABLE_USER_SCRIPT_SANDBOXING`），将其值从 `YES` 修改为 `NO`。

2. 清理并重新构建项目执行 **Product → Clean Build Folder** 清理项目缓存，然后重新编译运行即可。

### 观众端调用了 joinLive 且返回 Success，但依然没有视频画面？

> **适用平台：Flutter**

- **时序问题：**是否在 LiveCoreWidget 挂载前就执行了 joinLive？请参考上文使用 addPostFrameCallback。

- **Controller 类型：**创建时是否正确传入了 CoreViewType.playView？

- **Widget 持久性：**在进房过程中，LiveCoreWidget 是否因为 setState 被意外销毁或重新创建？请确保其在 Widget 树中的稳定性。

### Flutter 项目如何请求权限？

> **适用平台：Flutter**

您可以使用 `permission_handler` 插件来请求摄像头和麦克风权限：
``` java
import 'package:permission_handler/permission_handler.dart';

Future<void> requestPermissions() async {
  await [
    Permission.camera,
    Permission.microphone,
  ].request();
}
```

### 如何在 Flutter 中处理页面生命周期？

> **适用平台：Flutter**

建议在 `dispose` 方法中清理资源，例如移除事件监听器：
``` java
@override
void dispose() {
  LiveListStore.shared.removeLiveListListener(_liveListListener);
  super.dispose();
}
```
