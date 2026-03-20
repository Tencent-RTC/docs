> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

**适用平台：Android, iOS, Flutter, uni-app**

本文档将帮助开发者使用 `AtomicXCore SDK`的 `LiveListStore` 和 `LiveSeatStore`  快速构建一个包含主播开播和观众进房功能的语聊房 `App`。

## 核心概念
在开始集成之前，我们先通过下表了解一下 `LiveSeatStore` 相关的几个核心概念：

| **概念** | **职责描述** | **Android** | **iOS** | **Flutter** |
| --- | --- | --- | --- | --- |
| `LiveSeatStore` | 麦位管理核心，负责管理房间内的所有麦位信息和麦位相关操作。 通过 `liveSeatState.seatList` 暴露实时的麦位列表数据流。 | `abstract class` | `class` | `class` |
| `LiveSeatState` | 代表麦位的当前状态。 - `seatList` ：是一个 `StateFlow`，存储了麦位列表的实时状态； - `speakingUsers` ：则代表当前正在说话的人和对应的音量。 | `data class` | `struct` | `class` |
| `SeatInfo` | 单个麦位的数据模型。` LiveSeatStore` 所推送的麦位列表（`seatList`）就是由多个 `SeatInfo` 对象组成的。  关键字段：  - `index`：麦位的索引（位置）。 - `isLocked `：麦位是否被锁定。  - `userInfo`： 麦位上的用户信息。如果麦位为空，此字段也为空对象。 | `data class` | `struct` | `class` |
| `SeatUserInfo` | 麦上用户的详细数据模型。 当一个用户成功上麦后，`SeatInfo` 中的 `userInfo` 字段就会被填充为此用户。  关键字段：  - `userID`：  用户的唯一 ID。  - `userName`：  用户的昵称。  - `avatarURL`：  用户的头像 URL。  - `microphoneStatus`： 麦克风状态（开/关）。  - `cameraStatus`：  摄像头状态（开/关）。 | `data class` | `struct` | `class` |

## API 文档
| **Store/Component** | **功能描述** | **API 文档** |
| --- | --- | --- |
| **LiveListStore** | 直播间全生命周期管理：创建 / 加入 / 离开 / 销毁房间，查询房间列表，修改直播信息（名称、公告等），监听直播状态（如被踢出、结束）。 |  |
| **LiveSeatStore** | 麦位管理核心：管理麦位列表、麦上用户状态、麦位相关操作（上麦、下麦、踢人、锁麦、开关麦克风/摄像头等），监听麦位事件。 |  |
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

请参见 开通服务，获取体验版或付费版 `SDK`。

### 步骤2：在当前项目中导入 AtomicXCore

**安装组件**：请在您的 `build.gradle` 文件中添加 `implementation 'com.tencent.atomicx:atomicxcore:latest'`  依赖，然后执行 `Gradle Sync`。
``` gradle
dependencies {
    implementation 'io.trtc.uikit:atomicx-core:latest.release'
    api "io.trtc.uikit:rtc_room_engine:latest.release"
    api "com.tencent.liteav:LiteAVSDK_Professional:latest.release"
    api "com.tencent.imsdk:imsdk-plus:latest.release"
    // 其他依赖...
}
```

### 步骤3：实现登录逻辑

在项目中调用 `LoginStore.shared.login` 完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 `App` 自身的用户账户登录成功后，再调用 `LoginStore.shared.login`，以确保登录业务逻辑的清晰和一致。
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
            1400000001,        // 替换为 SDKAppID
            "test_001",        // 替换为 UserID
            "xxxxxxxxxxx",     // 替换为 UserSig
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
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| `sdkAppId` | `Int` | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的10位整数。 |
| `userID` | `String` | 当前用户的唯一 `ID`，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| `userSig` | `String` | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 `userSig` 或者 通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 `UserSig`。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 `UserSig` 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建基础语聊房

### 步骤1：实现房主创建语聊房

房主开播流程如下，您只需执行以下几步操作，即可快速搭建一个语聊房。

#### **1. 初始化麦位 **`Store`

在您的房主 `Activity` 中，创建一个 `LiveSeatStore` 实例。您需要监听 `liveSeatState.seatList` 的变化，以实时获取麦位数据来渲染您的 `UI`。
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
import io.trtc.tuikit.atomicxcore.api.live.LiveSeatStore
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// YourAnchorActivity 代表您的房主Activity
class YourAnchorActivity : AppCompatActivity() {

    private lateinit var liveListStore: LiveListStore
    private lateinit var liveSeatStore: LiveSeatStore
    private lateinit var deviceStore: DeviceStore
    private val liveID = "test_voice_room_001"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_anchor) // 假设您有自己的布局

        // 1. 初始化 Stores
        liveListStore = LiveListStore.shared()
        liveSeatStore = LiveSeatStore.create(liveID)
        deviceStore = DeviceStore.shared()

        // 2. 监听麦位列表变化
        observeSeatList()
    }

    private fun observeSeatList() {
         // 监听 seatList 变化，并更新您的麦位 UI
         CoroutineScope(Dispatchers.Main).launch {
             liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                 // 在这里根据 seatInfoList 渲染您的麦位 UI
                 // 例如：updateMicSeatView(seatInfoList)
                 Log.d("AnchorActivity", "Seat list updated: ${seatInfoList.size} seats")
             }
         }
    }
}
```

#### **2. 打开麦克风**

通过调用 `DeviceStore` 的 `openLocalMicrophone` 接口打开麦克风，示例代码如下：
``` java
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

class YourAnchorActivity : AppCompatActivity() {
    // ... 其他代码 ...

    private fun openDevices() {
        // 1. 打开麦克风
        DeviceStore.shared().openLocalMicrophone(completion = null)
    }
}
```

#### **3. 开始语聊**

通过调用 `LiveListStore` 的 `createLive` 接口开始语聊房直播，完整示例代码如下：
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.TakeSeatMode

class YourAnchorActivity : AppCompatActivity() {
    // ... 其他代码 ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ... 其他代码 ...

        // 开始语聊
        startLive()
    }

    private fun startLive() {
        val liveInfo = LiveInfo().apply {
            // 设置房间 id
            liveID = this@YourAnchorActivity.liveID
            // 设置房间名称
            liveName = "test 语聊房"
            // 配置为语聊房（启用麦位）
            isSeatEnabled = true
            // 房主默认上麦
            keepOwnerOnSeat = true
            // 5. 设置麦位布局模板（例如 70 为 10 麦位模板）
            seatLayoutTemplateID = 70
            // 6. 设置上麦模式，例如申请上麦
            seatMode = TakeSeatMode.APPLY
            // 7. 设置最大麦位数
            maxSeatCount = 10
        }

        // 8. 调用 createLive 开始直播
        liveListStore.createLive(liveInfo, object : LiveInfoCompletionHandler {
            override fun onFailure(code: Int, desc: String) {
                Log.e("Live", "Response startLive onError: $desc")
            }

            override fun onSuccess(liveInfo: LiveInfo) {
                Log.d("Live", "Response startLive onSuccess")
                // 房主创建成功后，默认会上麦，此时您可以调用 unmuteMicrophone
                liveSeatStore.unmuteMicrophone(null)
            }
        })
    }
}
```

`LiveInfo`** 参数说明**
| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符 |
| `liveName` | `String` | 选填 | 直播间的标题 |
| `notice` | `String` | 选填 | 直播间的公告信息 |
| `isMessageDisable` | `Boolean` | 选填 | 是否禁言（`true`：是，`false`：否） |
| `isPublicVisible` | `Boolean` | 选填 | 是否公开可见（`true`：是，`false`：否） |
| `isSeatEnabled` | `Boolean` | 选填 | 是否启用麦位功能（`true`：是，`false`：否） |
| `keepOwnerOnSeat` | `Boolean` | 选填 | 是否保持房主在麦位上 |
| `maxSeatCount` | `Int` | 必填 | 最大麦位数量 |
| `seatMode` | TakeSeatMode | 选填 | 上麦模式（`FREE`：自由上麦，`APPLY`：申请上麦） |
| `seatLayoutTemplateID` | `Int` | 必填 | 麦位布局模板 `ID` |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址 |
| `categoryList` | `List<Int>` | 选填 | 直播间的分类标签列表 |
| `activityStatus` | `Int` | 选填 | 直播活动状态 |
| `isGiftEnabled` | `Boolean` | 选填 | 是否启用礼物功能（`true`：是，`false`：否） |

#### **4. 构建麦位 UI 界面**

> **提示：**
> 

> 麦位 `UI` 效果的业务代码，您也可以参考 `TUILiveKit` 开源项目中的 [SeatGridView.kt](https://github.com/Tencent-RTC/TUILiveKit/blob/main/Android/tuilivekit/src/main/java/com/trtc/uikit/livekit/voiceroomcore/SeatGridView.kt) 文件来了解完整的实现逻辑。
> 

通过 LiveSeatStore 实例，监听 liveSeatState.seatList 的变化，以实时获取麦位数据来渲染您的 UI。您可以在 Activity 中（例如 YourAnchorActivity 或 YourAudienceActivity）通过以下方式监听数据：
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

class YourAnchorActivity : AppCompatActivity() {
    // ... 其他代码 ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ... 其他代码 ...

        // 监听 seatList 变化
        observeSeatList()
    }

    private fun observeSeatList() {
        // 监听 seatList 变化，并更新您的麦位 UI
        CoroutineScope(Dispatchers.Main).launch {
            liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                // seatInfoList 即为最新的麦位列表 (List<SeatInfo>),在这里根据 seatInfoList 渲染您的麦位 UI
                Log.d("AnchorActivity", "Seat list updated: ${seatInfoList.size} seats")
            }
        }
    }
}
```

#### **5. 结束语聊**

语聊结束后，房主可以调用 `LiveListStore` 的 `endLive` 接口结束语聊，`SDK` 会处理停止推流和销毁房间的逻辑。
``` java
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.tencent.cloud.tuikit.engine.extension.TUILiveListManager
import io.trtc.tuikit.atomicxcore.api.live.StopLiveCompletionHandler

class YourAnchorActivity : AppCompatActivity() {
    // ... 其他代码 ...

    // 结束语聊
    private fun stopLive() {
        liveListStore.endLive(object : StopLiveCompletionHandler {
            override fun onSuccess(statisticsData: TUILiveListManager.LiveStatisticsData) {
                Log.d("Live", "endLive success, duration: ${statisticsData.liveDuration}")
            }

            override fun onFailure(code: Int, desc: String) {
                Log.e("Live", "endLive error: $desc")
            }
        })
    }

    // 确保在 Activity 销毁时也调用
    override fun onDestroy() {
        super.onDestroy()
        stopLive()
        Log.d("Live", "YourAnchorActivity onDestroy")
    }
}
```

### 步骤2：实现观众进入语聊房

观众进房流程如下，通过简单几步操作，即可实现观众进入语聊房。

#### **1. 初始化麦位 **`Store`

在您的观众 `Activity` 中，创建 `LiveSeatStore` 实例，并监听 `liveSeatState.seatList` 的变化，以渲染麦位 `UI`。
``` java
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
import io.trtc.tuikit.atomicxcore.api.live.LiveSeatStore
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import android.util.Log

// YourAudienceActivity 代表您的观众Activity
class YourAudienceActivity : AppCompatActivity() {

    private lateinit var liveListStore: LiveListStore
    private lateinit var liveSeatStore: LiveSeatStore
    private val liveID = "test_voice_room_001" // 确保 liveID 与房主一致

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_audience) // 假设您有自己的布局

        // 1. 初始化 Stores
        liveListStore = LiveListStore.shared()
        liveSeatStore = LiveSeatStore.create(liveID)

        // 2. 监听麦位列表变化
        observeSeatList()
    }

    private fun observeSeatList() {
         // 3. 监听 seatList 变化，并更新您的麦位 UI
         CoroutineScope(Dispatchers.Main).launch {
             liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                 // 在这里根据 seatInfoList 渲染您的麦位 UI
                 // 例如：updateMicSeatView(seatInfoList)
                 Log.d("AudienceActivity", "Seat list updated: ${seatInfoList.size} seats")
             }
         }
    }
}
```

#### **2. 进入语聊房**

通过调用 `LiveListStore` 的 `joinLive` 接口加入语聊房，完整示例代码如下：
``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler

// YourAudienceActivity 代表您的观众Activity
class YourAudienceActivity : AppCompatActivity() {
    
    // ... 其他代码 ...
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_audience) // 假设您有自己的布局
        // ... 其他代码 ...
        
        // 进入语聊房
        joinLive()
    }

    private fun joinLive() {
        // 1. 调用 joinLive 进入语聊房
        liveListStore.joinLive(liveID, object : LiveInfoCompletionHandler {
            override fun onSuccess(liveInfo: LiveInfo) {
                Log.d("Live", "joinLive success")
            }
            override fun onFailure(code: Int, desc: String) {
                Log.e("Live", "joinLive error: $desc")
            }
        })
    }
}
```

#### **3. 构建麦位 UI 界面**

观众构建麦位 UI 界面的流程与主播完全一致，请参考主播 构建麦位 UI 界面。

#### **4. 退出语聊房**

观众退出语聊房时，需要调用 `LiveListStore` 的 `leaveLive` 接口来退出。
``` java
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// YourAudienceActivity 代表您的观众Activity
class YourAudienceActivity : AppCompatActivity() {
    // ... 其他代码 ...

    private fun leaveLive() {
        liveListStore.leaveLive(object : CompletionHandler {
            override fun onSuccess() {
                Log.d("Live", "leaveLive success")
            }

            override fun onFailure(code: Int, desc: String) {
                Log.e("Live", "leaveLive error: $desc")
            }
        })
    }

    // 确保在 Activity 销毁时也调用
    override fun onDestroy() {
        super.onDestroy()
        leaveLive()
        Log.d("Live", "YourAudienceActivity onDestroy")
    }
}
```

### 运行效果

完成上述步骤后，即可完成一个最基础的语聊直播场景。您可以参考 丰富语聊场景 来完善语聊场景。

## 丰富语聊房场景

当您完成了基础的语聊房功能后，您可以参考以下功能指南来为语聊房添加丰富的互动玩法。
| **功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现听众上麦** | 听众申请上麦，与房主进行实时语音互动。 | CoGuestStore | 观众上麦 |
| **实现房主跨房连线 PK** | 两个不同房间的主播进行连线，实现互动或 PK。 | CoHostStore BattleStore | 主播连线 & PK |
| **添加弹幕聊天功能** | 房间内成员可以发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## Platform: iOS

## 准备工作

### 步骤1：开通服务

请参见 开通服务，获取体验版或付费版 `SDK`。

### 步骤2：在当前项目中导入 AtomicXCore
1. **安装组件**：请在您的 **Podfile** 文件中添加 `pod 'AtomicXCore'` 依赖，然后执行`pod install`。

   ``` ruby
   # Podfile
   target 'xxxx' do
     pod 'AtomicXCore'
   end
   ```
2. **配置工程权限:  **请在应用的 `Info.plist` 文件中添加相机和麦克风的使用权限说明。

   ``` ruby
   <key>NSMicrophoneUsageDescription</key>
   <string>TUILiveKit需要访问您的麦克风权限，开启后录制的视频才会有声音</string>
   ```
   

### 步骤3：实现登录逻辑

在项目中调用 `LoginStore.shared.login` 完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

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
| **参数** | **类型** | **说明** |
| --- | --- | --- |
| sdkAppId | Int32 | 从 [控制台](https://console.cloud.tencent.com/trtc) 获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| userID | String | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| userSig | String | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 UserSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 UserSig 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建基础语聊房

### 步骤1：实现房主创建语聊房

房主开播流程如下，您只需执行以下几步操作，即可快速搭建一个语聊房。

#### **1. 初始化麦位 **`Store`

在您的房主 `ViewController` 中，创建一个 `LiveSeatStore` 实例。您需要使用 `Combine` 框架监听 `liveSeatStore.state` 的变化，以实时获取麦位数据来渲染您的 `UI`。
``` swift
import UIKit
import AtomicXCore
import Combine // 导入 Combine 框架

// YourAnchorViewController 代表您的房主 ViewController
class YourAnchorViewController: UIViewController {

    private let liveListStore = LiveListStore.shared
    private let deviceStore = DeviceStore.shared

    // 使用 liveID 初始化 LiveSeatStore
    private let liveID = "test_voice_room_001"
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: liveID)

    // 用于管理订阅生命周期
    private var cancellables = Set<AnyCancellable>()

    override func viewDidLoad() {
        super.viewDidLoad()
        // 假设您有自己的布局
        // setupUI() 

        // 2. 监听麦位列表变化
        observeSeatList()
    }

    private func observeSeatList() {
         // 监听 state.seatList 的变化，并更新您的麦位 UI
         liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList)) // 仅关心麦位列表
            .removeDuplicates()
            .receive(on: DispatchQueue.main) // 确保在主线程更新 UI
            .sink { [weak self] seatInfoList in
                // 在这里根据 seatInfoList 渲染您的麦位 UI
                // 例如：self?.updateMicSeatView(seatInfoList)
                print("Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables) // 管理订阅
    }
}
```

#### **2. 打开麦克风**

通过调用 `DeviceStore` 的 `openLocalMicrophone` 接口打开麦克风，示例代码如下：
``` swift
import UIKit
import AtomicXCore

class YourAnchorViewController: UIViewController {
    // ... 其他代码 ...

    private func openDevices() {
        // 1. 打开麦克风
        DeviceStore.shared.openLocalMicrophone(completion: nil)
    }
}
```

#### **3. 开始语聊**

通过调用 `LiveListStore` 的 `createLive` 接口开始语聊房直播，完整示例代码如下：
``` swift
import UIKit
import AtomicXCore

class YourAnchorViewController: UIViewController {
    // ... 其他代码 ...
    private let liveID = "test_voice_room_001"
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // ... 其他代码 ...

        // 开始语聊
        startLive()
    }

    private func startLive() {
        // 1. 准备 LiveInfo 对象
        var liveInfo = LiveInfo()

        // 2. 设置房间 id
        liveInfo.liveID = liveID
        // 3. 设置房间名称
        liveInfo.liveName = "test 语聊房"
        // 4. 配置为语聊房（启用麦位）
        liveInfo.isSeatEnabled = true
        // 5. 房主默认上麦
        liveInfo.keepOwnerOnSeat = true
        // 6. 设置麦位布局模板（例如 70 为 10 麦位模板）
        // 重要：请根据产品规范传入正确的 ID
        liveInfo.seatLayoutTemplateID = 70 
        // 7. 设置上麦模式，例如申请上麦
        liveInfo.seatMode = .apply
        // 8. 设置最大麦位数
        liveInfo.maxSeatCount = 10

        // 9. 调用 createLive 开始直播
        
        liveListStore.createLive(liveInfo) { [weak self] result in
            guard let self = self else { return }
            switch result {
            case .success(let liveInfo):
                print("Response startLive onSuccess")
                // 房主创建成功后，默认会上麦，此时您可以调用 unmuteMicrophone
                liveSeatStore.unmuteMicrophone(completion: nil)
            case .failure(let errorInfo):
                print("Response startLive onError: \(errorInfo.message)")
            }
        }
    }
}
```

`LiveInfo`**参数说明:**
| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符 |
| `liveName` | `String` | 选填 | 直播间的标题 |
| `notice` | `String` | 选填 | 直播间的公告信息 |
| `isMessageDisable` | `Bool` | 选填 | 是否禁言（`true`：是，`false`：否） |
| `isPublicVisible` | `Bool` | 选填 | 是否公开可见（`true`：是，`false`：否） |
| `isSeatEnabled` | `Bool` | 选填 | 是否启用麦位功能（`true`：是，`false`：否） |
| `keepOwnerOnSeat` | `Bool` | 选填 | 是否保持房主在麦位上 |
| `maxSeatCount` | `Int` | 必填 | 最大麦位数量 |
| `seatMode` | `TakeSeatMode` | 选填 | 上麦模式（`.free`：自由上麦，`.apply`：申请上麦） |
| `seatLayoutTemplateID` | `UInt` | 必填 | 麦位布局模板 `ID` |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址 |
| `categoryList` | `[NSNumber]` | 选填 | 直播间的分类标签列表 |
| `activityStatus` | `Int` | 选填 | 直播活动状态 |
| `isGiftEnabled` | `Bool` | 选填 | 是否启用礼物功能（`true`：是，`false`：否） |

#### **4. 构建麦位 UI 界面**

> **提示：**
> 

> 麦位 `UI` 效果的业务代码，您也可以参考 `TUILiveKit` 开源项目中的 [SeatGridView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/SeatGridview/SeatGridView.swift) 文件来了解完整的实现逻辑。
> 

通过 `LiveSeatStore` 实例，监听 `state.seatList` 的变化，以实时获取麦位数据来渲染您的 UI。您可以在 `ViewController` 中（例如 `YourAnchorViewController` 或 `YourAudienceViewController`）通过以下方式监听数据：
``` swift
import UIKit
import AtomicXCore
import Combine

class YourAnchorViewController: UIViewController {
    // ... 其他代码 ...
    private var cancellables = Set<AnyCancellable>()
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: "your_live_id")

    override func viewDidLoad() {
        super.viewDidLoad()
        // ... 其他代码 ...

        // 监听 seatList 变化
        observeSeatList()
    }

    private func observeSeatList() {
        // 监听 state.seatList 变化，并更新您的麦位 UI
         liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList)) // 仅关心麦位列表
            .removeDuplicates()
            .receive(on: DispatchQueue.main) // 确保在主线程更新 UI
            .sink { [weak self] seatInfoList in
                // seatInfoList 即为最新的麦位列表 (List<SeatInfo>)
                // 在这里根据 seatInfoList 渲染您的麦位 UI
                print("Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables)
    }
}
```

#### **5. 结束语聊**

语聊结束后，房主可以调用 `LiveListStore` 的 `endLive` 接口结束语聊，`SDK` 会处理停止推流和销毁房间的逻辑。
``` swift
import UIKit
import AtomicXCore
import RTCRoomEngine // 导入以获取 TUILiveStatisticsData

class YourAnchorViewController: UIViewController {
    // ... 其他代码 ...

    // 结束语聊
    private func stopLive() {
        liveListStore.endLive { result in
            switch result {
            case .success(let data):
                print("endLive success")
            case .failure(let errorInfo):
                print("endLive error: \(errorInfo.message)")
            }
        }
    }
}
```

### 步骤2：实现观众进入语聊房

观众进房流程如下，通过简单几步操作，即可实现观众进入语聊房。

#### **1. 初始化麦位 **`Store`

在您的观众 `ViewController` 中，创建 `LiveSeatStore` 实例，并监听 `state.seatList` 的变化，以渲染麦位 `UI`。
``` swift
import UIKit
import AtomicXCore
import Combine

// YourAudienceViewController 代表您的观众 ViewController
class YourAudienceViewController: UIViewController {

    private let liveListStore = LiveListStore.shared

    // 确保 liveID 与房主一致
    private let liveID = "test_voice_room_001" 
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: liveID)

    // 用于管理订阅生命周期
    private var cancellables = Set<AnyCancellable>()

    override func viewDidLoad() {
        super.viewDidLoad()
        // 假设您有自己的布局
        // setupUI()

        // 2. 监听麦位列表变化
        observeSeatList()
    }

    private func observeSeatList() {
         // 3. 监听 state.seatList 变化，并更新您的麦位 UI
         liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList)) // 仅关心麦位列表
            .removeDuplicates()
            .receive(on: DispatchQueue.main) // 确保在主线程更新 UI
            .sink { [weak self] seatInfoList in
                // 在这里根据 seatInfoList 渲染您的麦位 UI
                // 例如：self?.updateMicSeatView(seatInfoList)
                print("AudienceVC Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables)
    }
}
```

#### **2. 进入语聊房**

通过调用 `LiveListStore` 的 `joinLive` 接口加入语聊房，完整示例代码如下：
``` swift
import UIKit
import AtomicXCore

// YourAudienceViewController 代表您的观众 ViewController
class YourAudienceViewController: UIViewController {

    // ... 其他代码 ...
    override func viewDidLoad() {
        super.viewDidLoad()
        // ... 其他代码 ...

        // 进入语聊房
        joinLive()
    }

    private func joinLive() {
        // 1. 调用 joinLive 进入语聊房
        liveListStore.joinLive(liveID: liveID) { result in
            DispatchQueue.main.async {
                switch result {
                case .success(let liveInfo):
                    print("joinLive success")
                case .failure(let errorInfo):
                    print("joinLive error: \(errorInfo.message)")
                }
            }
        }
    }
}
```

#### **3. 构建麦位 UI 界面**

观众构建麦位 UI 界面的流程与主播完全一致，请参考主播 构建麦位 UI 界面。

#### **4. 退出语聊房**

观众退出语聊房时，需要调用 `LiveListStore` 的 `leaveLive` 接口来退出。
``` swift
import UIKit
import AtomicXCore

// YourAudienceViewController 代表您的观众 ViewController
class YourAudienceViewController: UIViewController {
    // ... 其他代码 ...
    
    private func leaveLive() {
        liveListStore.leaveLive { result in
              switch result {
              case .success:
                  print("leaveLive success")
              case .failure(let errorInfo):
                  print("leaveLive error: \(errorInfo.message)")
              }
        }
    }
}
```

### 运行效果

完成上述步骤后，即可完成一个最基础的语聊直播场景。您可以参考 丰富语聊场景 来完善语聊场景。

## 丰富语聊房场景

当您完成了基础的语聊房功能后，您可以参考以下功能指南来为语聊房添加丰富的互动玩法。
| **功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现听众上麦** | 听众申请上麦，与房主进行实时语音互动。 | CoGuestStore | 观众上麦 |
| **实现房主跨房连线 PK** | 两个不同房间的主播进行连线，实现互动或 PK。 | CoHostStore BattleStore | 主播连线和 PK |
| **添加弹幕聊天功能** | 房间内成员可以发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## Platform: Flutter

## 准备工作

### 步骤1：开通服务

请参见 开通服务，获取体验版或付费版 `SDK`。

### 步骤2：在当前项目中导入 AtomicXCore
1. **安装组件**：请在您的 `pubspec.yaml` 文件中添加 `atomic_x_core` 依赖，然后执行 `flutter pub get`。

   ``` yaml
   dependencies:
     atomic_x_core: ^3.6.0
   ```
2. **配置工程权限：**Android 和 iOS 工程都需要配置权限。

   

【Android】

在 `android/app/src/main/AndroidManifest.xml` 文件中添加麦克风的使用权限说明。
``` xml
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

【iOS】

在应用 `ios` 目录的 **Podfile** 和 `ios/Runner` 目录的 `Info.plist` 文件中添加麦克风的使用权限说明。

**Podfile**
``` ruby
post_install do |installer|
  installer.pods_project.targets.each do |target|
    flutter_additional_ios_build_settings(target)
      target.build_configurations.each do |config|
          config.build_settings['GCC_PREPROCESSOR_DEFINITIONS'] ||= [
        '$(inherited)',
        'PERMISSION_MICROPHONE=1',
        ]
      end
  end
end
```

**Info.plist**
``` xml
<key>NSMicrophoneUsageDescription</key>
<string>TUILiveKit需要访问您的麦克风权限，开启后录制的视频才会有声音</string>
```

### 步骤3：实现登录逻辑

在项目中调用 `LoginStore.shared.login` 完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 App 自身的用户账户登录成功后，再调用 LoginStore.shared.login，以确保登录业务逻辑的清晰和一致。
> 

``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// main.dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  // 登录
  final result = await LoginStore.shared.login(
    sdkAppID: 1400000001,           // 替换为您的 sdkAppID
    userID: "test_001",             // 替换为您的 userID
    userSig: "xxxxxxxxxxx",         // 替换为您的 userSig
  );

  if (result.isSuccess) {
    debugPrint("login success");
  } else {
    debugPrint("login failed code: ${result.code}, message: ${result.message}");
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

## 构建基础语聊房应用

### 步骤1：实现房主创建语聊房

房主开播流程如下，您只需执行以下几步操作，即可快速搭建一个语聊房。

#### 1. 初始化麦位 Store

在您的房主页面中，创建一个 `LiveSeatStore` 实例。您需要监听 `liveSeatStore.liveSeatState` 的变化，以实时获取麦位数据来渲染您的 `UI`。
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// YourAnchorPage 代表您的房主页面
class YourAnchorPage extends StatefulWidget {
  const YourAnchorPage({super.key});

  @override
  State<YourAnchorPage> createState() => _YourAnchorPageState();
}

class _YourAnchorPageState extends State<YourAnchorPage> {
  final _liveListStore = LiveListStore.shared;
  final _deviceStore = DeviceStore.shared;

  // 使用 liveID 初始化 LiveSeatStore
  final String _liveID = "test_voice_room_001";
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create(_liveID);

    // 监听麦位列表变化
    _observeSeatList();
  }

  void _observeSeatList() {
    // 监听 liveSeatState.seatList 的变化，并更新您的麦位 UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // 在这里根据 seatInfoList 渲染您的麦位 UI
    final seatInfoList = _liveSeatStore.liveSeatState.seatList.value;
    debugPrint("Seat list updated: ${seatInfoList.length} seats");
    setState(() {});
  }

  @override
  void dispose() {
    _liveSeatStore.liveSeatState.seatList.removeListener(_seatListListener);
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    // 假设您有自己的布局
    return Container();
  }
}
```

#### 2. 打开麦克风

通过调用 `DeviceStore` 的 `openLocalMicrophone` 接口打开麦克风，示例代码如下：
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... 其他代码 ...

  void _openDevices() {
    // 打开麦克风
    DeviceStore.shared.openLocalMicrophone();
  }
}
```

#### 3. 开始语聊

通过调用 `LiveListStore` 的 `createLive` 接口开始语聊房直播，完整示例代码如下：
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... 其他代码 ...
  final String _liveID = "test_voice_room_001";

  @override
  void initState() {
    super.initState();
    // ... 其他代码 ...

    // 开始语聊
    _startLive();
  }

  Future<void> _startLive() async {
    // 1. 准备 LiveInfo 对象
    final liveInfo = LiveInfo(
      // 2. 设置房间 id
      liveID: _liveID,
      // 3. 设置房间名称
      liveName: "test 语聊房",
      // 4. 配置为语聊房（启用麦位）
      isSeatEnabled: true,
      // 5. 房主默认上麦
      keepOwnerOnSeat: true,
      // 6. 设置麦位布局模板（例如 70 为 10 麦位模板）
      // 重要：请根据产品规范传入正确的 ID
      seatLayoutTemplateID: 70,
      // 7. 设置上麦模式，例如申请上麦
      seatMode: TakeSeatMode.apply,
      // 8. 设置最大麦位数
      maxSeatCount: 10,
    );

    // 9. 调用 createLive 开始直播
    final result = await _liveListStore.createLive(liveInfo);

    if (result.isSuccess) {
      debugPrint("Response startLive onSuccess");
      // 房主创建成功后，默认会上麦，此时您可以调用 unmuteMicrophone
      _liveSeatStore.unmuteMicrophone();
    } else {
      debugPrint("Response startLive onError: ${result.message}");
    }
  }
}
```

**LiveInfo 参数说明：**
| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `String` | 必填 | 直播间的唯一标识符 |
| `liveName` | `String` | 选填 | 直播间的标题 |
| `notice` | `String` | 选填 | 直播间的公告信息 |
| `isMessageDisable` | `bool` | 选填 | 是否禁言（`true`：是，`false`：否） |
| `isPublicVisible` | `bool` | 选填 | 是否公开可见（`true`：是，`false`：否） |
| `isSeatEnabled` | `bool` | 选填 | 是否启用麦位功能（`true`：是，`false`：否） |
| `keepOwnerOnSeat` | `bool` | 选填 | 是否保持房主在麦位上 |
| `maxSeatCount` | `int` | 选填 | 最大麦位数量 |
| `seatMode` | `TakeSeatMode` | 选填 | 上麦模式（`free`：自由上麦，`apply`：申请上麦） |
| `seatLayoutTemplateID` | `int` | 必填 | 麦位布局模板 `ID` |
| `coverURL` | `String` | 选填 | 直播间的封面图片地址 |
| `backgroundURL` | `String` | 选填 | 直播间的背景图片地址 |
| `categoryList` | `List<int>` | 选填 | 直播间的分类标签列表 |
| `activityStatus` | `int` | 选填 | 直播活动状态 |
| `isGiftEnabled` | `bool` | 选填 | 是否启用礼物功能（`true`：是，`false`：否） |

#### 4. 构建麦位 UI 界面

> **提示：**
> 

> 麦位 `UI` 效果的业务代码，可参考 `TUILiveKit` 开源项目中的 [seat_grid_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/seat_grid_widget/seat_grid_widget.dart) 文件来了解完整的实现逻辑。
> 

通过 `LiveSeatStore` 实例，监听 `liveSeatState.seatList` 的变化，以实时获取麦位数据来渲染您的 UI。您可以在页面中（例如 `YourAnchorPage` 或 `YourAudiencePage`）通过以下方式监听数据：
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... 其他代码 ...
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create("your_live_id");
    // ... 其他代码 ...

    // 监听 seatList 变化
    _observeSeatList();
  }

  void _observeSeatList() {
    // 监听 liveSeatState.seatList 变化，并更新您的麦位 UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // seatInfoList 即为最新的麦位列表 (List<SeatInfo>)
    final seatInfoList = _liveSeatStore.liveSeatState.seatList.value;
    // 在这里根据 seatInfoList 渲染您的麦位 UI
    debugPrint("Seat list updated: ${seatInfoList.length} seats");
    setState(() {});
  }

  @override
  void dispose() {
    _liveSeatStore.liveSeatState.seatList.removeListener(_seatListListener);
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return ValueListenableBuilder<List<SeatInfo>>(
      valueListenable: _liveSeatStore.liveSeatState.seatList,
      builder: (context, seatList, child) {
        return GridView.builder(
          gridDelegate: const SliverGridDelegateWithFixedCrossAxisCount(
            crossAxisCount: 4,
          ),
          itemCount: seatList.length,
          itemBuilder: (context, index) {
            final seat = seatList[index];
            return _buildSeatItem(seat);
          },
        );
      },
    );
  }

  Widget _buildSeatItem(SeatInfo seat) {
    // 构建单个麦位 UI
    return Container();
  }
}
```

#### 5. 结束语聊

语聊结束后，房主可以调用 `LiveListStore` 的 `endLive` 接口结束语聊，`SDK` 会处理停止推流和销毁房间的逻辑。
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... 其他代码 ...

  // 结束语聊
  Future<void> _stopLive() async {
    final result = await _liveListStore.endLive();

    if (result.isSuccess) {
      debugPrint("endLive success");
    } else {
      debugPrint("endLive error: ${result.message}");
    }
  }
}
```

### 步骤2：实现观众进入语聊房

观众进房流程如下，通过简单几步操作，即可实现观众进入语聊房。

#### 1. 初始化麦位 Store

在您的观众页面中，创建 `LiveSeatStore` 实例，并监听 `liveSeatState.seatList` 的变化，以渲染麦位 `UI`。
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// YourAudiencePage 代表您的观众页面
class YourAudiencePage extends StatefulWidget {
  const YourAudiencePage({super.key});

  @override
  State<YourAudiencePage> createState() => _YourAudiencePageState();
}

class _YourAudiencePageState extends State<YourAudiencePage> {
  final _liveListStore = LiveListStore.shared;

  // 确保 liveID 与房主一致
  final String _liveID = "test_voice_room_001";
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create(_liveID);

    // 监听麦位列表变化
    _observeSeatList();
  }

  void _observeSeatList() {
    // 监听 liveSeatState.seatList 变化，并更新您的麦位 UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // 在这里根据 seatInfoList 渲染您的麦位 UI
    final seatInfoList = _liveSeatStore.liveSeatState.seatList.value;
    debugPrint("AudiencePage Seat list updated: ${seatInfoList.length} seats");
    setState(() {});
  }

  @override
  void dispose() {
    _liveSeatStore.liveSeatState.seatList.removeListener(_seatListListener);
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    // 假设您有自己的布局
    return Container();
  }
}
```

#### 2. 进入语聊房

通过调用 `LiveListStore` 的 `joinLive` 接口加入语聊房，完整示例代码如下：
``` java
class _YourAudiencePageState extends State<YourAudiencePage> {
  // ... 其他代码 ...

  @override
  void initState() {
    super.initState();
    // ... 其他代码 ...

    // 进入语聊房
    _joinLive();
  }

  Future<void> _joinLive() async {
    // 调用 joinLive 进入语聊房
    final result = await _liveListStore.joinLive(_liveID);

    if (result.isSuccess) {
      debugPrint("joinLive success");
    } else {
      debugPrint("joinLive error: ${result.message}");
    }
  }
}
```

#### 3. 构建麦位 UI 界面

观众构建麦位 UI 界面的流程与主播完全一致，请参考构建麦位 UI 界面。

#### 4. 退出语聊房

观众退出语聊房时，需要调用 `LiveListStore` 的 `leaveLive` 接口来退出。
``` java
class _YourAudiencePageState extends State<YourAudiencePage> {
  // ... 其他代码 ...

  Future<void> _leaveLive() async {
    final result = await _liveListStore.leaveLive();

    if (result.isSuccess) {
      debugPrint("leaveLive success");
    } else {
      debugPrint("leaveLive error: ${result.message}");
    }
  }
}
```

### 运行效果

完成上述步骤后，即可完成一个最基础的语聊直播场景。您可以参考 丰富语聊房场景 来完善语聊场景。

## 丰富语聊房场景

当您完成了基础的语聊房功能后，您可以参考以下功能指南来为语聊房添加丰富的互动玩法。
| **功能** | **功能介绍** | **功能 Stores** | **实现指南** |
| --- | --- | --- | --- |
| **实现听众上麦** | 听众申请上麦，与房主进行实时语音互动。 | CoGuestStore | 观众上麦 |
| **添加弹幕聊天功能** | 房间内成员可以发送和接收实时文字消息。 | BarrageStore | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftStore | 实现礼物功能 |

## Platform: uni-app

## 准备工作

### 步骤 1. 开通服务

请参见 开通服务，获取体验版或付费版 SDK。

### 步骤2. 导入 tuikit-atomic-x
1. **安装组件**：打开 [AtomicXCore SDK](https://ext.dcloud.net.cn/plugin?id=25488) ，单击**下载插件并导入HBuilderX**，选择需要集成的项目并单击**确定**。

   

2. **配置工程权限:  **请在应用的 manifest.json 文件的app-plus > distribute 节点下，确保添加了以下必要的权限：

  - Android 平台（`android`节点）：在 `permissions` 数组中，请确保包含以下权限：

      ``` json
      "<uses-permission android:name=\"android.permission.RECORD_AUDIO\" />"
      ```
  - iOS 平台（ios 节点）：在 `privacyDescription` 对象中，请确保包含以下描述：

      ``` json
      "NSMicrophoneUsageDescription" : "应用需要访问您的麦克风以进行直播"  
      ```

      在 `UIBackgroundModes` 数组中，添加 `audio` 以支持后台播放音频。

      ``` json
      "UIBackgroundModes" : [ "audio" ]
      ```

### 步骤 3. 完成登录

在您的项目中调用 LoginState 中的 login 方法完成登录，**这是使用 **`AtomicXCore`** 所有功能的关键前提**。

> **重要：**
> 

> 推荐在您 App 自身的用户账户登录成功后，再调用 LoginState 中的 login 方法，以确保登录业务逻辑的清晰和一致。
> 

``` typescript
import { useLoginState } from "@/uni_modules/tuikit-atomic-x/state/LoginState";
  
const { login } = useLoginState();
  
const handleLogin = () => {
    login({
      sdkAppID: 1400000001,  // 替换为您的 SDKAppID
      userID: "test_001",    // 替换为您的 UserID
      userSig: "xxxxxxxxxx"  // 替换为您的 UserSig
    })
 }
```

**登录接口参数说明：**
| 参数 | 类型 | 说明 |
| --- | --- | --- |
| sdkAppID | number | 从 [控制台](https://console.cloud.tencent.com/trtc)获取，通常是以 `140` 或 `160` 开头的 10 位整数。 |
| userID | string | 当前用户的唯一 ID，仅包含英文字母、数字、连字符和下划线。为避免多端登录冲突，**请勿使用**`1`、`123`**等简单 ID**。 |
| userSig | string | 用于腾讯云鉴权的票据。请注意： - 开发环境：您可以采用本地 `GenerateTestUserSig.genTestSig` 函数生成 userSig 或者 通过 [UserSig 辅助工具](https://console.cloud.tencent.com/im/tool-usersig) 生成临时的 UserSig。 - 生产环境：为了防止密钥泄露，请务必采用服务端生成 UserSig 的方式。详细信息请参考 [服务端生成 UserSig](https://cloud.tencent.com/document/product/647/17275)。 更多信息请参见 [如何计算及使用 UserSig](https://cloud.tencent.com/document/product/647/17275)。 |

## 搭建语音聊天室

### 步骤1：实现房主创建语聊房

房主开播流程如下，您只需执行以下几步操作，即可快速搭建一个语聊房。

#### **1. 初始化麦位 **`State`

在您的房主页面中，创建一个 `LiveSeatState` 实例。您需要监听响应式数据的变化，以实时获取麦位数据来渲染您的 `UI`。
``` typescript
import { watch } from 'vue';
import { useLiveSeatState } from '@/uni_modules/tuikit-atomic-x/state/LiveSeatState';

// 定义直播间 ID（需要与后续 createLive 中使用的 liveID 保持一致）
const liveID = 'your_live_room_id'; // 替换为您的直播间 ID

// 使用 liveID 获取 LiveSeatState 实例
const { seatList } = useLiveSeatState(liveID);
```

#### **2. 打开麦克风**

通过调用 `DeviceState` 的 `openLocalMicrophone` 接口打开麦克风，示例代码如下：
``` typescript
import { useDeviceState } from "@/uni_modules/tuikit-atomic-x/state/DeviceState";
  
const { openLocalMicrophone } = useDeviceState();

const openMic = () => {
    openLocalMicrophone()
}
```

#### **3. 开始语聊**

通过调用 `LiveListState` 的 `createLive` 接口开始语聊房直播，完整示例代码如下：
``` typescript
import { useLiveListState } from '@/uni_modules/tuikit-atomic-x/state/LiveListState';

// 获取 LiveListState 实例
const { createLive } = useLiveListState();

// 开始直播
const startLive = () => {
    createLive({
      liveInfo: {
        liveID: 'xxx', // 设置直播的房间 id
        liveName: 'text', // 设置直播的房间名称
        keepOwnerOnSeat: true, // 配置主播始终在麦位上
        isSeatEnabled: true,
        seatLayoutTemplateID: 70, // 麦位布局模板（例如 70 为 10 麦位模板）
        seatMode: 'APPLY' // 上麦模式，例如申请上麦
        maxSeatCount: 10 // 最大麦位数
      },
      success: () => {        
        console.log('创建直播间成功')
        //您可以在这里进行页面的跳转
      },
      fail: (error) => {
        console.log('创建直播间失败', error)
      }
    });
  };
```

`LiveInfo`**参数说明:**
| **参数名** | **类型** | **属性** | **描述** |
| --- | --- | --- | --- |
| `liveID` | `string` | 必填 | 直播间的唯一标识符 |
| `liveName` | `string` | 选填 | 直播间的标题 |
| `notice` | `string` | 选填 | 直播间的公告信息 |
| `isMessageDisable` | `boolean` | 选填 | 是否禁言（`true`：是，`false`：否） |
| `isPublicVisible` | `boolean` | 选填 | 是否公开可见（`true`：是，`false`：否） |
| `isSeatEnabled` | `boolean` | 选填 | 是否启用麦位功能（`true`：是，`false`：否） |
| `keepOwnerOnSeat` | `boolean` | 选填 | 是否保持房主在麦位上 |
| `maxSeatCount` | `number` | 必填 | 最大麦位数量 |
| `seatMode` | `string` | 选填 | 上麦模式（`'FREE'`：自由上麦，`'APPLY'`：申请上麦） |
| `seatLayoutTemplateID` | `number` | 必填 | 麦位布局模板 `ID` |
| `coverURL` | `string` | 选填 | 直播间的封面图片地址 |
| `backgroundURL` | `string` | 选填 | 直播间的背景图片地址 |
| `activityStatus` | `number` | 选填 | 直播活动状态 |
| `isGiftEnabled` | `boolean` | 选填 | 是否启用礼物功能（`true`：是，`false`：否） |

#### **4. 构建麦位 UI 界面**

通过 `LiveSeatState` 实例，监听 `seatList` 的变化，以实时获取麦位数据来渲染您的界面 UI 。您可以在主播页通过以下方式监听数据：
``` typescript
import { watch } from 'vue';
import { useLiveSeatState } from '@/uni_modules/tuikit-atomic-x/state/LiveSeatState';

// 使用 liveID 获取 LiveSeatState 实例
const { seatList } = useLiveSeatState(liveID);

watch( seatList ,(newSeatList) => {
    console.log('新的麦位列表', newSeatList)
    // 用这个数据来维护您的 UI
})
```

#### **5. 结束语聊**

语聊结束后，房主可以调用 `LiveListState` 的 `endLive` 接口结束语聊，`SDK` 会处理停止推流和销毁房间的逻辑。
``` typescript
import { useLiveListState } from '@/uni_modules/tuikit-atomic-x/state/LiveListState';

// 获取 LiveListState 实例
const { endLive } = useLiveListState();

// 结束直播
const handleEndLive = () => {
    endLive({
      success: () => {        
        console.log('结束直播成功')
        //您可以在这里进行页面的跳转
      },
      fail: (error) => {
        console.log('结束直播失败', error)
      }
    });
 }
```

### 步骤2：实现观众进入语聊房

观众进房流程如下，通过简单几步操作，即可实现观众进入语聊房

#### **1. 初始化麦位 **`State`

在您的观众页面 中，创建 `LiveSeatState` 实例，并监听 `seatList` 的变化，以渲染麦位 `UI` 。
``` typescript
import { watch } from 'vue';
import { useLiveSeatState } from '@/uni_modules/tuikit-atomic-x/state/LiveSeatState';

// 观众进房前，需要先获取要加入的直播间 ID（liveID）。通常有以下几种方式：
// 通过房间列表选择（可从 LiveListState 的 liveList 数据中获取）
// 使用 liveID 获取 LiveSeatState 实例
const { seatList } = useLiveSeatState(liveID);

watch(seatList,(newSeatList) => {
    console.log('新的麦位列表', newSeatList)
    // 用这个数据来维护您的 UI
})   
```

#### **2. 进入语聊房**

通过调用 `LiveListState` 的 `joinLive` 接口加入语聊房，完整示例代码如下：
``` typescript
import { useLiveListState } from '@/uni_modules/tuikit-atomic-x/state/LiveListState';

// 获取 LiveListState 实例
const { joinLive } = useLiveListState();

// 加入直播
const handleJoinLive = () => {
    joinLive({
      liveID: 'xxx', // 想要加入语聊房的 liveID
      success: () => {        
        console.log('成功进入语聊房')
        //您可以在这里进行页面的跳转
      },
      fail: (error) => {
        console.log('进入语聊房失败', error)
      }
    });
 }
```

#### **3. 构建麦位 UI 界面**

观众构建麦位 UI的流程与主播完全一致，请参考主播 构建麦位 UI 界面

#### **4. 退出语聊房**

观众退出语聊房时，需要调用 `LiveListState` 的 `leaveLive` 接口来退出。
``` typescript

import { useLiveListState } from '@/uni_modules/tuikit-atomic-x/state/LiveListState';

// 获取 LiveListState 实例
const { leaveLive } = useLiveListState();

// 退出语聊房
const handleLeaveLive = () => {
    leaveLive({
      liveID: 'xxx', // 您当前加入语聊房的 liveID
      success: () => {        
        console.log('退出语聊房成功')
        //您可以在这里进行页面的跳转
      },
      fail: (error) => {
        console.log('退出语聊房失败', error)
      }
    });
 }
```

### 运行效果

完成上述步骤后，即可完成一个最基础的语聊直播场景。您可以参考 丰富语聊场景 来完善语聊场景。

## 丰富语音聊天室场景

当您完成了基础的语音聊天室功能后，您可以参考以下功能指南来为语音聊天室添加丰富的互动玩法。
| **语音聊天室功能** | **功能介绍** | **功能 State** | **实现指南** |
| --- | --- | --- | --- |
| **实现观众音频连麦** | 观众申请上麦，与主播进行实时音频互动。 | CoGuestState | 实现观众连麦 |
| **添加弹幕聊天功能** | 观众可以在语音聊天室发送和接收实时文字消息。 | BarrageState | 实现弹幕功能 |
| **构建礼物赠送系统** | 观众可以向主播赠送虚拟礼物，增加互动和趣味性。 | GiftState | 实现礼物功能 |
| **添加设置音效功能** | 变声（童声 / 男声）、混响（KTV 等）、耳返调节，实时切换特效。 | AudioEffectState | 实现音效功能 |

## 常见问题

### 听众调用 joinLive 后为什么没有声音？

> **适用平台：Android / iOS**

> **Android：** - **检查设备权限**：请确保 `App` 已获得麦克风的系统使用权限。
> **iOS：** - **检查设备权限**：请确保 `App` 已在 `Info.plist` 中声明并获得了麦克风的系统使用权限 (`NSMicrophoneUsageDescription`)。

> **Android：** - **检查房主端**：请确认房主端已正常调用 `DeviceStore.shared().openLocalMicrophone(null)` 以打开麦克风。
> **iOS：** - **检查房主端**：房主端是否正常调用 `DeviceStore.shared.openLocalMicrophone(completion: nil)` 打开了麦克风。

- **检查网络**：请检查设备网络连接是否正常。

### 为什么麦位列表没有显示或没有更新？

> **适用平台：Android / iOS**

> **Android：** - **检查 Store 初始化**：请确保您在 `createLive` 或 `joinLive` 之前，已经使用相同的 `liveID` 正确创建了 `LiveSeatStore` 实例 `(LiveSeatStore.create(liveID))`。
> **iOS：** - **检查 Store 初始化**：请确保您在 `createLive` 或 `joinLive` 之前，已经使用相同的 `liveID` 正确创建了 `LiveSeatStore` 实例 (`LiveSeatStore.create(liveID: liveID)`)。

> **Android：** - **检查数据监听**：请检查您是否正确监听了 `liveSeatStore.liveSeatState.seatList` 的数据流。
> **iOS：** - **检查数据监听**：请检查您是否正确使用了 `Combine` 框架来订阅 `liveSeatStore.state.seatList`，并确保 `cancellables` (订阅管理器) 的生命周期与您的 `ViewController` 保持一致。

> **Android：** - **检查接口调用**：请确保 `createLive` (房主) 或 `joinLive` (听众) 接口已成功调用（在 `onSuccess` 回调中确认）。
> **iOS：** - **检查接口调用**：请确保 `createLive` (房主) 或 `joinLive` (听众) 接口已成功调用（在 `switch result` 的 `.success` 分支中确认）。

### 听众进房后无声音

> **适用平台：Flutter**

- **检查设备权限**：请确保 `App` 已在 `Info.plist`（iOS）或 `AndroidManifest.xml`（Android）中声明并获得了麦克风的系统使用权限。

- **检查房主端**：房主端是否正常调用 `DeviceStore.shared.openLocalMicrophone()` 打开了麦克风。

- **检查网络**：请检查设备网络连接是否正常。

### 麦位列表未显示或更新

> **适用平台：Flutter**

- **检查 Store 初始化**：请确保您在 `createLive` 或 `joinLive` 之前，已经使用相同的 `liveID` 正确创建了 `LiveSeatStore` 实例（`LiveSeatStore.create(liveID)`）。

- **检查数据监听**：请检查您是否正确使用了 `addListener` 来监听 `liveSeatStore.liveSeatState.seatList`，并确保在 `dispose` 中调用 `removeListener` 移除监听。

- **检查接口调用**：请确保 `createLive`（房主）或 `joinLive`（听众）接口已成功调用（在回调结果的 `isSuccess` 为 `true` 时确认）。

### 代码变更后，没有效果或者编译报错？

> **适用平台：uni-app**

引入 uni_modules 后，需要重新制作自定义基座，详情参见 制作自定义基座流程。

### 原生能力集成问题

> **适用平台：uni-app**

- 问题：插件调用失败或无响应。

- 解决方案：

  - 必须打包自定义基座：在开发调试原生插件时，不能使用标准基座，必须通过 `HBuilderX` > 运行 > 运行到手机或模拟器 > 制作自定义基座，然后选择这个自定义基座来运行。

  - 检查权限：如本文档“准备工作”中所述，`manifest.json` manifest.json必须声明相机、麦克风等权限。

  - 查看原生日志：这是终极调试手段。通过 `Android Studio (Logcat)` 或 `Xcode (Console)` 查看设备的原生日志，通常能发现最直接的错误信息。
