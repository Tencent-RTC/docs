> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

This document guides developers through building a voice chat room application with host broadcasting and audience participation features using the **AtomicXCore** SDK's `LiveListStore` and `LiveSeatStore`.

## Core Concepts

| **概念** | **职责描述** | **Android** | **iOS** | **Flutter** |
| --- | --- | --- | --- | --- |
| `LiveListStore` | - `createLive()`: Start live stream as host. - `endLive()`: End live stream as host. - `joinLive()`: Audience joins live room. - `leaveLive()`: Leave live room. | `abstract class` | `class` | — |
| `LiveInfo` | - `liveID`: Unique room identifier. - `seatLayoutTemplateID`: Layout template ID (e.g., 600 for dynamic grid). | `data class` | `struct` | — |
| `LiveSeatStore` | Core seat management class. Manages all seat information and seat-related operations in the room. Provides a real-time seat list data stream via liveSeatState.seatList. | `abstract class` | `class` | `class` |
| `LiveSeatState` | Represents the current state of all seats. - `seatList`: a StateFlow containing the real-time seat list. - `speakingUsers`: users currently speaking and their volume. | `data class` | `struct` | `class` |
| `SeatInfo` | Data model for a single seat. The seat list (seatList) emitted by LiveSeatStore is a list of SeatInfo objects. Key fields: - `index`: seat position. - `isLocked`: whether the seat is locked. - `userInfo`: user information for the seat. If the seat is empty, this field is empty. | `data class` | `class` | `class` |
| `SeatUserInfo` | Detailed data model for the user occupying a seat. When a user successfully takes a seat, the userInfo field in SeatInfo is populated. Key fields: - `userID`: unique user ID. - `userName`: user nickname. - `avatarURL`: user avatar URL. - `microphoneStatus`: microphone status (on/off). - `cameraStatus`: camera status (on/off). | `data class` | `class` | `class` |

## Advanced Features
### **Implementing Speaking Wave Animation for Mic Seat Users**

In voice chat rooms, it is common to show a wave animation on the avatar of users who are speaking, so everyone can see who is currently talking. `LiveSeatStore` provides a `speakingUsers` data stream for this purpose.

#### Example

#### 

#### Implementation

> **Note：**
> 

> For a complete implementation of the speaking wave animation, refer to [SeatGridView.kt](https://github.com/Tencent-RTC/TUILiveKit/blob/main/Android/tuilivekit/src/main/java/com/trtc/uikit/livekit/voiceroomcore/SeatGridView.kt) in the open-source TUILiveKit project.
> 

In `YourAnchorActivity`, observe changes in `speakingUsers` and update the UI to reflect the speaking status:
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

// In YourHostActivity or YourAudienceActivity
class YourHostActivity : AppCompatActivity() {
    // ... (omitting other code) ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ... (omitting other code) ...

        // Listen for speakingUsers changes
        observeSpeakingUsersState()
    }

    private fun observeSpeakingUsersState() {
        // Listen for speakingUsers changes and update the "currently speaking" status
        CoroutineScope(Dispatchers.Main).launch {
            liveSeatStore.liveSeatState.speakingUsers.collect { speakingUserSet ->
                // Pass the set of "currently speaking" user IDs to the UI, update UI state
                Log.d("HostActivity", "Speaking users updated: ${speakingUserSet.size} users")
            }
        }
    }
}
```

### Synchronizing Custom State in Live Streaming Room

In Live Streaming Room, hosts may need to synchronize custom information with all participants, such as the current room topic or background music. The `metaData` feature of `LiveListStore` supports this use case.

#### Implementation
1. On the host side, set custom information using the `updateLiveMetaData` API. `AtomicXCore` synchronizes these changes in real time to all participants. 

2. On the audience side, subscribe to `LiveListState.currentLive` and listen for changes in `metaData`. When a relevant key is updated, parse its value and update your business logic.

#### Code Example
``` kotlin
import io.trtc.tuikit.atomicxcore.api.LiveListStore
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import com.google.gson.Gson
import io.trtc.tuikit.atomicxcore.api.MetaDataCompletionHandler
import io.trtc.tuikit.atomicxcore.api.LiveListStore

// 1. Define a background music model (using data class)
data class MusicModel(
    val musicId: String,
    val musicName: String
)

// 2. Host side: Add a method to push background music in your Host business logic
fun updateBackgroundMusic(music: MusicModel) {
    val gson = Gson()
    val jsonString = gson.toJson(music) ?: ""

    // The metaData to be updated
    val metaData = hashMapOf("music_info" to jsonString)

    // Update metaData
    LiveListStore.shared()
        .updateLiveMetaData(
            metaData,
            object : CompletionHandler {
                override fun onSuccess() {
                    print("Background music ${music.musicName} pushed successfully")
                }

                override fun onFailure(code: Int, desc: String) {
                    print("Failed to push background music: $desc")
                }
            }
        )
}

// 3. Audience side: Add a method to listen for background music changes in your Audience business logic
private fun subscribeToDataUpdates() {
    CoroutineScope(Dispatchers.Main).launch {
        LiveListStore.shared()
            .liveState
            .currentLive
            .map { it.metaData }
            .collect {
                val musicInfo = it["music_info"]
                // Refresh business state, e.g., play new background music
            }
    }
}
```

## Prerequisites
### Step 1: Activate the Service

See Activate Service to obtain either the trial or paid version of the SDK.Then, go to [the Console](https://console.trtc.io/app) for application management, and get the following:
- `SDKAppID`: Application identifier (required). Tencent Cloud uses `SDKAppId` for billing and details.

- `SDKSecretKey`: Application secret key, used to initialize the configuration file with secret information.

   

### Step 2: Import AtomicXCore into Your Project

**Install the component**: Add the dependency `implementation 'com.tencent.atomicx:atomicxcore:latest`' to your `build.gradle` file, then perform a **Gradle****Sync**.
``` gradle
dependencies {
    implementation 'io.trtc.uikit:atomicx-core:latest.release'
    api "io.trtc.uikit:rtc_room_engine:3.4.0.1306"
    api "io.trtc.uikit:atomicx-core:3.4.0.1307"
    api "com.tencent.liteav:LiteAVSDK_Professional:12.8.0.19279"
    api "com.tencent.imsdk:imsdk-plus:8.7.7201"
    // Other dependencies...
}
```

### Step 3: Implement Login Logic

Call `LoginStore.shared.login` in your project to complete authentication. **This is required before using any functionality of AtomicXCore**.

> **Note：**
> 

> We recommend calling `LoginStore.shared.login` after your app's own user authentication is successful to ensure clear and consistent login logic.
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
            1400000001,        // Replace with your SDKAppID
            "test_001",        // Replace with your UserID
            "xxxxxxxxxxx",     // Replace with your UserSig
            object : CompletionHandler {
                override fun onSuccess() {
                    // Handle login success
                    Log.d("Login", "login success")
                }

                override fun onFailure(code: Int, desc: String) {
                    // Handle login failure
                    Log.e("Login", "login failed, code: $code, error: $desc")
                }
            }
        )
    }
}
```

**Login API Parameter Description**
| Parameter | Type | Description |
| --- | --- | --- |
| `sdkAppID` | `Int` | Get this from [TRTC console > Application management](https://console.trtc.io/app). |
| `userID` | `String` | The unique ID for the current user. Must contain only English letters, numbers, hyphens, and underscores. |
| `userSig` | `String` | A ticket for Tencent Cloud authentication. Please note: - **Development Environment**: You can use the local `GenerateTestUserSig.genTestSig` function to generate a UserSig or generate a temporary UserSig via the [UserSig Generation Tool](https://console.trtc.io/usersig). - **Production Environment**: To prevent key leakage, you must use a server-side method to generate UserSig. For details, see Generating UserSig on the Server. - For more information, see How to Calculate and Use UserSig. |

## Platform: Android

## Building a Basic Voice Chat Room

### Step 1: Host Room Creation

Follow the steps below to quickly set up a voice chat room as the host.

#### **1. Initialize the Seat **`Store`

In your host `Activity`, create a `LiveSeatStore` instance. Observe changes in `liveSeatState.seatList` to get real-time mic seat data and update your UI.
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

// YourHostActivity represents your Host Activity
class YourHostActivity : AppCompatActivity() {

    private lateinit var liveListStore: LiveListStore
    private lateinit var liveSeatStore: LiveSeatStore
    private lateinit var deviceStore: DeviceStore
    private val liveID = "test_voice_room_001"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_host) // Assume you have your own layout

        // 1. Initialize Stores
        liveListStore = LiveListStore.shared()
        liveSeatStore = LiveSeatStore.create(liveID)
        deviceStore = DeviceStore.shared()

        // 2. Listen for mic seat list changes
        observeSeatList()
    }

    private fun observeSeatList() {
         // Listen for seatList changes and update your mic seat UI
         CoroutineScope(Dispatchers.Main).launch {
             liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                 // Render your mic seat UI here based on seatInfoList
                 // Example: updateMicSeatView(seatInfoList)
                 Log.d("HostActivity", "Seat list updated: ${seatInfoList.size} seats")
             }
         }
    }
}
```

#### **2. **Turn On Microphone

Turn On Microphone by calling the `openLocalMicrophone` method from `DeviceStore`:
``` java
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.device.DeviceStore

class YourHostActivity : AppCompatActivity() {
    // ... Other code ...

    private fun openDevices() {
        // 1. Turn on the microphone
        DeviceStore.shared().openLocalMicrophone(completion = null)
    }
}
```

#### **3. Start Voice Chat**

Start the live voice chat by calling the `createLive` method of `LiveListStore`:
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.TakeSeatMode

class YourHostActivity : AppCompatActivity() {
    // ... Other code ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ... Other code ...

        // Start voice chat
        startLive()
    }

    private fun startLive() {
        val liveInfo = LiveInfo().apply {
            // 1. Set room id
            liveID = this@YourHostActivity.liveID
            // 2. Set room name
            liveName = "test voice room"
            // 3. Configure as a voice chat room (enable mic seats)
            isSeatEnabled = true
            // 4. Host takes mic by default
            keepOwnerOnSeat = true
            // 5. Set mic seat layout template (e.g., 70 for a 10-seat template)
            seatLayoutTemplateID = 70
            // 6. Set mic-taking mode, e.g., apply to take mic
            seatMode = TakeSeatMode.APPLY
            // 7. Set maximum number of mic seats
            maxSeatCount = 10
        }

        // 8. Call createLive to start the stream
        liveListStore.createLive(liveInfo, object : LiveInfoCompletionHandler {
            override fun onFailure(code: Int, desc: String) {
                Log.e("Live", "Response startLive onError: $desc")
            }

            override fun onSuccess(liveInfo: LiveInfo) {
                Log.d("Live", "Response startLive onSuccess")
                // After successful creation, the Host is on the mic by default, now you can call unmuteMicrophone
                liveSeatStore.unmuteMicrophone(null)
            }
        })
    }
}
```

**LiveInfo Parameter Description**
| **Parameter Name** | **Type** | **Required** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Required | Unique identifier for the live room |
| `liveName` | `String` | Optional | Title of the live room |
| `notice` | `String` | Optional | Announcement information for the live room |
| `isMessageDisable` | `Boolean` | Optional | Mute status (`true`: muted, `false`: not muted) |
| `isPublicVisible` | `Boolean` | Optional | Public visibility (`true`: visible, `false`: hidden) |
| `isSeatEnabled` | `Boolean` | Optional | Enable mic seat feature (`true`: enabled, `false`: disabled) |
| `keepOwnerOnSeat` | `Boolean` | Optional | Keep host on mic seat |
| `maxSeatCount` | `Int` | Required | Maximum number of mic seats |
| `seatMode` | TakeSeatMode | Optional | Mic seat mode (`FREE`: free to take seat, `APPLY`: apply to take seat) |
| `seatLayoutTemplateID` | `Int` | Required | Mic seat layout template ID |
| `coverURL` | `String` | Optional | Cover image URL for the live room |
| `backgroundURL` | `String` | Optional | Background image URL for the live room |
| `categoryList` | `List<Int>` | Optional | Category tag list for the live room |
| `activityStatus` | `Int` | Optional | Live activity status |

#### **4. Build the Mic Seat UI**

> **Note：**
> 

> For the complete business logic of mic seat UI effects, refer to the open-source [SeatGridView.kt](https://github.com/Tencent-RTC/TUILiveKit/blob/main/Android/tuilivekit/src/main/java/com/trtc/uikit/livekit/voiceroomcore/SeatGridView.kt) in the TUILiveKit project.
> 

Use the `LiveSeatStore` instance to observe changes in `liveSeatState.seatList` and update your UI in real time. In your Activity (such as `YourAnchorActivity` or `YourAudienceActivity`), observe the data as follows:
``` java
import android.os.Bundle
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch

class YourHostActivity : AppCompatActivity() {
    // ... Other code ...

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ... Other code ...

        // Listen for seatList changes
        observeSeatList()
    }

    private fun observeSeatList() {
        // Listen for seatList changes and update your mic seat UI
        CoroutineScope(Dispatchers.Main).launch {
            liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                // seatInfoList is the latest mic seat list (List<SeatInfo>), render your mic seat UI here based on seatInfoList
                Log.d("HostActivity", "Seat list updated: ${seatInfoList.size} seats")
            }
        }
    }
}
```

#### **5. End Voice Chat**

To end the voice chat, call the `endLive` method of `LiveListStore`. The SDK will handle stopping the stream and destroying the room.
``` java
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import com.tencent.cloud.tuikit.engine.extension.TUILiveListManager
import io.trtc.tuikit.atomicxcore.api.live.StopLiveCompletionHandler

class YourHostActivity : AppCompatActivity() {
    // ... Other code ...

    // End voice chat
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

    // Ensure this is also called when the Activity is destroyed
    override fun onDestroy() {
        super.onDestroy()
        stopLive()
        Log.d("Live", "YourHostActivity onDestroy")
    }
}
```

### Step 2: Audience Joins the Voice Chat Room

Follow these steps to allow audience members to join the voice chat room.

#### **1. Initialize the Mic Seat Store**

In your audience `Activity`, create a `LiveSeatStore` instance and observe changes in `liveSeatState.seatList` to update the mic seat UI.
``` java
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
import io.trtc.tuikit.atomicxcore.api.live.LiveSeatStore
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import android.util.Log

// YourAudienceActivity represents your Audience Activity
class YourAudienceActivity : AppCompatActivity() {

    private lateinit var liveListStore: LiveListStore
    private lateinit var liveSeatStore: LiveSeatStore
    private val liveID = "test_voice_room_001" // Ensure liveID matches the Host's

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_audience) // Assume you have your own layout

        // 1. Initialize Stores
        liveListStore = LiveListStore.shared()
        liveSeatStore = LiveSeatStore.create(liveID)

        // 2. Listen for mic seat list changes
        observeSeatList()
    }

    private fun observeSeatList() {
         // 3. Listen for seatList changes and update your mic seat UI
         CoroutineScope(Dispatchers.Main).launch {
             liveSeatStore.liveSeatState.seatList.collect { seatInfoList ->
                 // Render your mic seat UI here based on seatInfoList
                 // Example: updateMicSeatView(seatInfoList)
                 Log.d("AudienceActivity", "Seat list updated: ${seatInfoList.size} seats")
             }
         }
    }
}
```

#### **2. Join Voice Chat Room**

Join the voice chat room by calling the `joinLive` method of `LiveListStore`:
``` java
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import android.util.Log
import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler

// YourAudienceActivity represents your Audience Activity
class YourAudienceActivity : AppCompatActivity() {
    
    // ... Other code ...
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_audience) // Assume you have your own layout
        // ... Other code ...
        
        // Enter voice chat room
        joinLive()
    }

    private fun joinLive() {
        // 1. Call joinLive to enter the voice chat room
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

#### **3. Build the Mic Seat UI**

The process for building the mic seat UI as an audience member is the same as for the host. Refer to the host Build the Mic Seat UI.

#### **4. Leave the Voice Chat Room**

When an audience member leaves the voice chat room, call the `leaveLive` method of `LiveListStore`:
``` java
import android.util.Log
import androidx.appcompat.app.AppCompatActivity
import io.trtc.tuikit.atomicxcore.api.CompletionHandler

// YourAudienceActivity represents your Audience Activity
class YourAudienceActivity : AppCompatActivity() {
    // ... Other code ...

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

    // Ensure this is also called when the Activity is destroyed
    override fun onDestroy() {
        super.onDestroy()
        leaveLive()
        Log.d("Live", "YourAudienceActivity onDestroy")
    }
}
```

### Run and Test

After completing the steps above, you will have a basic voice chat live streaming setup. For more advanced features, see the "Enrich Voice Chat Room Scenarios" section.

## Enrich Voice Chat Room Scenarios

After implementing the basic voice chat room, you can add more interactive features by referring to the following guides:
| **Feature** | **Feature Description** | **Feature Stores** | **Implementation Guide** |
| --- | --- | --- | --- |
| **Enable Audience to Take Mic Seat** | Audience can apply to take a mic seat and interact with the host in real time. | CoGuestStore | Implementation |
| **Host Cross-Room Connection & PK** | Hosts from different rooms can connect for interaction or PK. | CoHostStore BattleStore | Implementation |
| **Add Barrage Chat** | Members in the room can send and receive real-time text messages. | BarrageStore | Implementation |
| **Build Gift System** | Audience can send virtual gifts to hosts to increase engagement and fun. | GiftStore | Implementation |

## API Documentation
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| `LiveListStore` | Manages the full lifecycle of live rooms: create, join, leave, destroy rooms; query room list; modify live info (name, announcement, etc.); listen to live status (such as being kicked out, ended). |  |
| `LiveSeatStore` | Core mic seat management: manage mic seat list, user status, seat operations (take seat, leave seat, kick, lock, toggle microphone/camera, etc.), listen to mic seat events. |  |
| `DeviceStore` | Audio/video device control: microphone (toggle/volume), camera (toggle/switch/quality), screen sharing, real-time device status monitoring. |  |
| `CoGuestStore` | Audience co-host management: co-host application/invitation/approval/rejection, member permission control (microphone/camera), status synchronization. |  |
| `CoHostStore` | Host cross-room connection: supports multiple layout templates (dynamic grid, etc.), initiate/accept/reject connection, manage co-host interaction. |  |
| `BattleStore` | Host PK battle: initiate PK (set duration/opponent), manage PK status (start/end), synchronize scores, listen to battle results. |  |
| `GiftStore` | Gift interaction: get gift list, send/receive gifts, listen to gift events (including sender and gift details). |  |
| `BarrageStore` | Bullet chat feature: send text/custom barrage, maintain barrage list, real-time barrage status monitoring. |  |
| `LikeStore` | Like interaction: send likes, listen to like events, synchronize total like count. |  |
| `LiveAudienceStore` | Audience management: get real-time audience list (ID/name/avatar), count audience number, listen to audience join/leave events. |  |
| `AudioEffectStore` | Audio effects: voice changer (child/male), reverb (KTV, etc.), ear monitor adjustment, real-time effect switching. |  |

## Platform: iOS

## Building a Basic Voice Chat Room

### Step 1: Host Room Creation

Follow these steps to quickly set up a voice chat room as the host.

#### **1. Initialize the Seat Store**

In your host `ViewController`, instantiate `LiveSeatStore`. Use the Combine framework to observe changes in `liveSeatStore.state` for real-time seat updates and UI rendering.
``` swift
import UIKit
import AtomicXCore
import Combine

class YourAnchorViewController: UIViewController {

    private let liveListStore = LiveListStore.shared
    private let deviceStore = DeviceStore.shared

    // Initialize LiveSeatStore with liveID
    private let liveID = "test_voice_room_001"
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: liveID)

    // Manage subscription lifecycle
    private var cancellables = Set<AnyCancellable>()

    override func viewDidLoad() {
        super.viewDidLoad()
        // Initialize your layout here
        // setupUI() 

        // Listen for seat list changes
        observeSeatList()
    }

    private func observeSeatList() {
        // Subscribe to seat list updates and refresh seat UI
        liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList))
            .removeDuplicates()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] seatInfoList in
                // Update seat UI with seatInfoList
                // Example: self?.updateMicSeatView(seatInfoList)
                print("Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables)
    }
}
```

#### **2. **Turn On Microphone

Turn On Microphone by calling `openLocalMicrophone` from `DeviceStore`:
``` swift
import UIKit
import AtomicXCore

class YourAnchorViewController: UIViewController {
    // ... other code ...

    private func openDevices() {
        DeviceStore.shared.openLocalMicrophone(completion: nil)
    }
}
```

#### **3. Start Voice Chat**

Start the voice chat room by invoking `createLive` on `LiveListStore`:
``` swift
import UIKit
import AtomicXCore

class YourAnchorViewController: UIViewController {
    // ... other code ...
    private let liveID = "test_voice_room_001"
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // ... other code ...

        // Start voice chat
        startLive()
    }

    private func startLive() {
        var liveInfo = LiveInfo()

        liveInfo.liveID = liveID
        liveInfo.liveName = "test voice chat room"
        liveInfo.isSeatEnabled = true
        liveInfo.keepOwnerOnSeat = true
        liveInfo.seatLayoutTemplateID = 70 // Use correct ID per product spec
        liveInfo.seatMode = .apply
        liveInfo.maxSeatCount = 10

        liveListStore.createLive(liveInfo) { [weak self] result in
            guard let self = self else { return }
            switch result {
            case .success(let liveInfo):
                print("Response startLive onSuccess")
                // Host is on seat by default; unmute microphone if needed
                liveSeatStore.unmuteMicrophone(completion: nil)
            case .failure(let errorInfo):
                print("Response startLive onError: \(errorInfo.message)")
            }
        }
    }
}
```

**LiveInfo Parameter Reference:**
| **Parameter Name** | **Type** | **Required** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Required | Unique identifier for the live room |
| `liveName` | `String` | Optional | Room title |
| `notice` | `String` | Optional | Room announcement |
| `isMessageDisable` | `Bool` | Optional | Mute chat (`true`: muted, `false`: not muted) |
| `isPublicVisible` | `Bool` | Optional | Public visibility (`true`: visible, `false`: not visible) |
| `isSeatEnabled` | `Bool` | Optional | Enable seat feature (`true`: enabled, `false`: disabled) |
| `keepOwnerOnSeat` | `Bool` | Optional | Keep host on seat |
| `maxSeatCount` | `Int` | Required | Maximum number of seats |
| `seatMode` | `TakeSeatMode` | Optional | Seat mode (`.free`: free seat, `.apply`: apply for seat) |
| `seatLayoutTemplateID` | `UInt` | Required | Seat layout template ID |
| `coverURL` | `String` | Optional | Room cover image URL |
| `backgroundURL` | `String` | Optional | Room background image URL |
| `categoryList` | `[NSNumber]` | Optional | Room category tags |
| `activityStatus` | `Int` | Optional | Live activity status |
| `isGiftEnabled` | `Bool` | Optional | Enable gift feature (`true`: enabled, `false`: disabled) |

#### **4. Build the Mic Seat UI**

> **Note：**
> 

> For seat UI logic and effects, refer to the open-source [SeatGridView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/SeatGridview/SeatGridView.swift) in TUILiveKit.
> 

Use your `LiveSeatStore` instance to subscribe to `state.seatList` and update the UI in real time:
``` swift
import UIKit
import AtomicXCore
import Combine

class YourAnchorViewController: UIViewController {
    // ... other code ...
    private var cancellables = Set<AnyCancellable>()
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: "your_live_id")

    override func viewDidLoad() {
        super.viewDidLoad()
        // ... other code ...

        observeSeatList()
    }

    private func observeSeatList() {
        liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList))
            .removeDuplicates()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] seatInfoList in
                // seatInfoList contains the latest seat data
                print("Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables)
    }
}
```

#### **5. End Voice Chat**

To end the session, call `endLive` on `LiveListStore`. The SDK will handle stream termination and room cleanup.
``` swift
import UIKit
import AtomicXCore
import RTCRoomEngine

class YourAnchorViewController: UIViewController {
    // ... other code ...

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

### Step 2:  Audience Joins the Voice Chat Room

Enable audience members to join the voice chat room with the following steps.

#### **1. Initialize the Seat Store**

In your audience `ViewController`, instantiate `LiveSeatStore` and subscribe to `state.seatList` for seat UI updates.
``` swift
import UIKit
import AtomicXCore
import Combine

class YourAudienceViewController: UIViewController {

    private let liveListStore = LiveListStore.shared

    // Use the same liveID as the host
    private let liveID = "test_voice_room_001" 
    private lazy var liveSeatStore = LiveSeatStore.create(liveID: liveID)

    private var cancellables = Set<AnyCancellable>()

    override func viewDidLoad() {
        super.viewDidLoad()
        // Initialize your layout here
        // setupUI()

        observeSeatList()
    }

    private func observeSeatList() {
        liveSeatStore.state
            .subscribe(StatePublisherSelector(keyPath: \LiveSeatState.seatList))
            .removeDuplicates()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] seatInfoList in
                // Update seat UI with seatInfoList
                // Example: self?.updateMicSeatView(seatInfoList)
                print("AudienceVC Seat list updated: \(seatInfoList.count) seats")
            }
            .store(in: &cancellables)
    }
}
```

#### **2. Join the Voice Chat Room**

Join the room by calling `joinLive` on `LiveListStore`:
``` swift
import UIKit
import AtomicXCore

class YourAudienceViewController: UIViewController {

    // ... other code ...
    override func viewDidLoad() {
        super.viewDidLoad()
        // ... other code ...

        joinLive()
    }

    private func joinLive() {
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

#### **3. Build the Mic Seat UI**

Building the seat UI for audience members is identical to the host process. Refer to the host Build the Mic Seat UI.

#### **4. Leave the Voice Chat Room**

To leave the room, call `leaveLive` on `LiveListStore`:
``` swift
import UIKit
import AtomicXCore

class YourAudienceViewController: UIViewController {
    // ... other code ...
    
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

### Run and Test

After completing these steps, you will have a functional voice chat live streaming room. For advanced features, see "Enriching Voice Chat Room Scenarios" below.

## Enriching Voice Chat Room Scenarios

After implementing the basic voice chat room, enhance your application with the following features:
| **Feature** | **Description** | **Store** | **Implementation Guide** |
| --- | --- | --- | --- |
| **Audience Take Seat** | Audience members can apply to take a seat and interact with the host in real time. | CoGuestStore | Implementation |
| **Host Cross-Room PK** | Hosts from different rooms can connect for interaction or PK. | CoHostStore BattleStore | Implementation |
| **Add Barrage Chat** | Room members can send and receive real-time text messages. | BarrageStore | Implementation |
| **Gift System** | Audience can send virtual gifts to hosts to increase interaction and engagement. | GiftStore | Implementation |

## API Documentation
| **Store/Component** | **Description** | **API Docs** |
| --- | --- | --- |
| `LiveListStore` | Manages the full live room lifecycle: create, join, leave, destroy room; query room list; modify room info; listen for room status changes. |  |
| `LiveSeatStore` | Seat management: handle seat list, user status, seat operations (take seat, leave seat, kick, lock, toggle mic/camera), and seat events. |  |
| `DeviceStore` | Audio/video device control: microphone, camera, screen sharing, device status monitoring. |  |
| `CoGuestStore` | Audience co-host management: application, invitation, acceptance, rejection, member permissions, status sync. |  |
| `CoHostStore` | Host cross-room connection: supports multiple layouts, initiate/accept/reject connection, manage co-host interaction. |  |
| `BattleStore` | Host PK battle: initiate PK, manage PK status, synchronize scores, listen for battle results. |  |
| `GiftStore` | Gift interaction: fetch gift list, send/receive gifts, listen for gift events. |  |
| `BarrageStore` | Barrage (chat overlay): send text/custom barrage, maintain barrage list, monitor barrage status. |  |
| `LikeStore` | Like interaction: send likes, listen for like events, synchronize like count. |  |
| `LiveAudienceStore` | Audience management: get real-time audience list, count, entry/exit events. |  |
| `AudioEffectStore` | Audio effects: voice change, reverb, in-ear monitoring, effect switching. |  |
| `BaseBeautyStore` | Basic beauty: adjust smoothing/whitening/redness (0-100), reset beauty status, synchronize effect parameters. |  |

## Platform: Flutter

## Preparations

### Step 1: Activate the Services

See Activate Service to obtain either the trial or paid version of the SDK.Then, go to [the Console](https://console.trtc.io/app) for application management, and get the following:
- `SDKAppID`: Application identifier (required). Tencent Cloud uses `SDKAppID` for billing and details.

- `SDKSecretKey`: Application secret key, used to initialize the configuration file with secret information.

   

### Step 2: Import AtomicXCore into Your Project
1. **Install component**: Please add `atomic_x_core` dependency in your `pubspec.yaml` file, then execute `flutter pub get`.

   ``` yaml
   dependencies:
     atomic_x_core: ^3.6.0
   ```
2. **Configure project permissions:** Android and iOS projects all are needed to configure permissions.

   

【Android】

Add permission to use microphone in file `android/app/src/main/AndroidManifest.xml`.
``` xml
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

【iOS】

Add permission to use microphone in file `ios` directory **Podfile** and `ios/Runner` directory `Info.plist`.

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
<string>TUILiveKit needs to access your microphone permission. Recorded video will have sound when enabled</string>
```

### Step 3: Implement Login Logic

Call `LoginStore.shared.login` in the project to complete login. **This is the key premise for using **`AtomicXCore`** all functions**.

> **Important:**
> 

> We recommend calling LoginStore.shared.login after successful log-in to your App's user account to ensure clear and consistent login service logic.
> 

``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// main.dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  // Log in.
  final result = await LoginStore.shared.login(
    sdkAppID: 1400000001,           // Replace with your sdkAppID
    userID: "test_001",             // Replace with your user ID
    userSig: "xxxxxxxxxxx",         // Replace with your userSig
  );

  if (result.isSuccess) {
    debugPrint("login success");
  } else {
    debugPrint("login failed code: ${result.code}, message: ${result.message}");
  }

  runApp(const MyApp());
}
```

**Login API parameter description:**
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `sdkAppID` | `Int` | Get this from [TRTC console > Application management](https://console.trtc.io/app). |
| `userID` | `String` | The unique ID for the current user. Must contain only English letters, numbers, hyphens, and underscores. |
| `userSig` | `String` | A ticket for Tencent Cloud authentication. Please note: - **Development Environment**: You can use the local `GenerateTestUserSig.genTestSig` function to generate a UserSig or generate a temporary UserSig via the [UserSig Generation Tool](https://console.trtc.io/usersig). - **Production Environment**: To prevent key leakage, you must use a server-side method to generate UserSig. For details, see Generating UserSig on the Server. - For more information, see How to Calculate and Use UserSig. |

## Building a Basic Voice Chat Room

### Step 1: Host Room Creation

Follow these steps to quickly set up a voice chat room as the host.

#### 1. **Initialize the Seat Store**

On your anchor page, create a `LiveSeatStore` instance. You need to listen to changes in `liveSeatStore.liveSeatState` for real-time seat updates and UI rendering.
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// YourAnchorPage represents your host homepage
class YourAnchorPage extends StatefulWidget {
  const YourAnchorPage({super.key});

  @override
  State<YourAnchorPage> createState() => _YourAnchorPageState();
}

class _YourAnchorPageState extends State<YourAnchorPage> {
  final _liveListStore = LiveListStore.shared;
  final _deviceStore = DeviceStore.shared;

  // Initialize LiveSeatStore with liveID
  final String _liveID = "test_voice_room_001";
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create(_liveID);

    // Listen to microphone position list adjustment
    _observeSeatList();
  }

  void _observeSeatList() {
    // Listen to changes in liveSeatState.seatList and refresh your mic seat UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // Render your mic seat UI here based on seatInfoList
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
    // Assume that you have your own layout
    return Container();
  }
}
```

#### Turn on Microphone

Turn on the microphone by calling the `DeviceStore``openLocalMicrophone` API. Sample code:
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... Other code ...

  void _openDevices() {
    // Turn the mic on
    DeviceStore.shared.openLocalMicrophone();
  }
}
```

#### 3. Start Voice Chat

By calling the `LiveListStore``createLive` API to start a voice chat room live stream, sample code:
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... Other code ...
  final String _liveID = "test_voice_room_001";

  @override
  void initState() {
    super.initState();
    // ... Other code ...

    // Start Voice Chat
    _startLive();
  }

  Future<void> _startLive() async {
    // 1. Prepare the LiveInfo object
    final liveInfo = LiveInfo(
      // 2. Set the room id
      liveID: _liveID,
      // 3. Set the room name
      liveName: "test voice chat room",
      // 4. Configured as a voice chat room (enable seat)
      isSeatEnabled: true,
      // 5. Room owner on seat by default
      keepOwnerOnSeat: true,
      // 6. Set the seat layout template (for example, 70 is the 10-seat template)
      // Important: According to the product specification, input the correct ID
      seatLayoutTemplateID: 70,
      // 7. Set the microphone mode, such as apply for microphone mode
      seatMode: TakeSeatMode.apply,
      // 8. Set the maximum number of microphone slots
      maxSeatCount: 10,
    );

    // 9. Call createLive to start live streaming
    final result = await _liveListStore.createLive(liveInfo);

    if (result.isSuccess) {
      debugPrint("Response startLive onSuccess");
      // Once created successfully, the room owner will join the stage by default. At this point, you can call unmuteMicrophone
      _liveSeatStore.unmuteMicrophone();
    } else {
      debugPrint("Response startLive onError: ${result.message}");
    }
  }
}
```

**LiveInfo parameter description:**
| **Parameter Name** | **Type** | **Attribute** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Required | Unique identifier of the live streaming room. |
| `liveName` | `String` | Optional. | Title of the live streaming room. |
| `notice` | `String` | Optional. | Announcement information of the live streaming room. |
| `isMessageDisable` | `bool` | Optional. | Whether to mute (`true`: yes, `false`: no). |
| `isPublicVisible` | `bool` | Optional. | Whether publicly visible (`true`: yes, `false`: no). |
| `isSeatEnabled` | `bool` | Optional. | Whether to enable the seat feature (`true`: yes, `false`: no). |
| `keepOwnerOnSeat` | `bool` | Optional. | Whether to keep the room owner on the seat. |
| `maxSeatCount` | `int` | Optional. | Maximum number of microphones. |
| `seatMode` | `TakeSeatMode` | Optional. | Microphone mode (`free`: Free to Join the Podium, `apply`: request to speak). |
| `seatLayoutTemplateID` | `int` | Required | Mic position layout template `ID.` |
| `coverURL` | `String` | Optional. | thumbnail URL of the live streaming room. |
| `backgroundURL` | `String` | Optional. | background image URL of the live streaming room. |
| `categoryList` | `List<int>` | Optional. | Category tag list of the live streaming room. |
| `activityStatus` | `int` | Optional. | Live stream status. |
| `isGiftEnabled` | `bool` | Optional. | Whether to enable the gift function (`true`: yes, `false`: no). |

#### 4. Build a Mic Seat UI

> **Note:**
> 

> For the business code of the Mic Seat `UI` effect, see the [seat_grid_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/seat_grid_widget/seat_grid_widget.dart) file in the `TUILiveKit` open-source project to learn about the complete implementation logic.
> 

Through the `LiveSeatStore` instance, listen to changes in `liveSeatState.seatList` to get seat data in real time for rendering your UI. You can listen to the data on the page (such as `YourAnchorPage` or `YourAudiencePage`) in the following ways:
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... Other code ...
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create("your_live_id");
    // ... Other code ...

    // Listen to seatList transition
    _observeSeatList();
  }

  void _observeSeatList() {
    // Listen to changes in liveSeatState.seatList and refresh your mic seat UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // seatInfoList is the latest microphone position list (List<SeatInfo>)
    final seatInfoList = _liveSeatStore.liveSeatState.seatList.value;
    // Render your mic seat UI here based on seatInfoList
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
    // Build one mic seat UI
    return Container();
  }
}
```

#### 5. End Voice Chat

After the voice chat, the room owner can call the `LiveListStore`'s `endLive` API to end the chat. The `SDK` will handle the logic of stopping streaming and terminating the room.
``` java
class _YourAnchorPageState extends State<YourAnchorPage> {
  // ... Other code ...

  // End Voice Chat
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

### Step 2: Audience Joins the Voice Chat Room

Enable audience members to join the voice chat room with the following steps.

#### 1. **Initialize the Seat Store**

On your audience page, create a `LiveSeatStore` instance and listen to changes in `liveSeatState.seatList` to render the mic seat UI.
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

// YourAudiencePage represents your audience page
class YourAudiencePage extends StatefulWidget {
  const YourAudiencePage({super.key});

  @override
  State<YourAudiencePage> createState() => _YourAudiencePageState();
}

class _YourAudiencePageState extends State<YourAudiencePage> {
  final _liveListStore = LiveListStore.shared;

  // Ensure the liveID matches the room owner
  final String _liveID = "test_voice_room_001";
  late final LiveSeatStore _liveSeatStore;
  late final VoidCallback _seatListListener = _onSeatListChanged;

  @override
  void initState() {
    super.initState();
    _liveSeatStore = LiveSeatStore.create(_liveID);

    // Listen to microphone position list adjustment
    _observeSeatList();
  }

  void _observeSeatList() {
    // Listen to changes in liveSeatState.seatList and refresh your mic seat UI
    _liveSeatStore.liveSeatState.seatList.addListener(_seatListListener);
  }

  void _onSeatListChanged() {
    // Render your mic seat UI here based on seatInfoList
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
    // Assume that you have your own layout
    return Container();
  }
}
```

#### 2. **Join the Voice Chat Room**

By calling the `LiveListStore``joinLive` API to join a voice chat room, sample code:
``` java
class _YourAudiencePageState extends State<YourAudiencePage> {
  // ... Other code ...

  @override
  void initState() {
    super.initState();
    // ... Other code ...

    // Enter a voice chat room
    _joinLive();
  }

  Future<void> _joinLive() async {
    // Call joinLive to enter a voice chat room
    final result = await _liveListStore.joinLive(_liveID);

    if (result.isSuccess) {
      debugPrint("joinLive success");
    } else {
      debugPrint("joinLive error: ${result.message}");
    }
  }
}
```

#### 3. Build a Mic Seat UI

The process for the audience to build a Mic Seat UI is the same as that for the Anchor. Refer to Build a Mic Seat UI.

#### 4. **Leave the Voice Chat Room**

When the audience logs out of the voice chat room, they need to call the `LiveListStore`'s `leaveLive` API to exit.
``` java
class _YourAudiencePageState extends State<YourAudiencePage> {
  // ... Other code ...

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

### Run and Test

After completing the above steps, you can complete a most basic voice chat live streaming scenario. You can refer to various voice chat room scenarios to refine the voice chat scenario.

## Enriching Voice Chat Room Scenario

After completing the basic voice chat room features, you can refer to the following feature guide to add various interactive gameplay features to the voice chat room.
| **Feature** | **Feature Description** | **Feature Stores** | **Implementation Guide** |
| --- | --- | --- | --- |
| **Audience Take Seat** | Audience members can apply to take a seat and interact with the host in real time. | CoGuestStore | Implementation |
| **Add Barrage Chat** | Room members can send and receive real-time text messages. | BarrageStore | Implementation |
| **Gift System** | Audience can send virtual gifts to hosts to increase interaction and engagement. | GiftStore | Implementation |

## API documentation
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **LiveListStore** | Full lifecycle management of live streaming room: create/join/leave/terminate room, query room list, modify live information (name, notice), listen to live streaming status (for example, being kicked out, ended). |  |
| **LiveSeatStore** | Core of seat management: manage seat list, Anchor status, and related operations (taking a seat, leaving a seat, removing user, locking seat, switching microphone on or off/camera), listen to seat events. |  |
| **DeviceStore** | Audio/Video device control: microphone (on/off, volume), camera (on/off, switchover, video quality), screen sharing, monitor device status in real time. |  |
| **CoGuestStore** | Audience co-broadcasting management: join microphone application/invite/grant/deny, member permission control (microphone/camera), state synchronization. |  |
| **CoHostStore** | Anchor cross-room connection: support for multiple layout templates (dynamic grid, etc.), initiate/accept/reject connection, and interactive management of co-broadcasting Anchors. |  |
| **BattleStore** | Anchor PK battle: initiate PK (duration configuration/competitor), manage PK status (start/end), synchronize score, listen to battle results. |  |
| **GiftStore** | Gift interaction: get gift list, send/receive gifts, listen to gift events (including sender, gift details). |  |
| **BarrageStore** | Danmaku function: send text/custom barrage item, maintain bullet screen list, monitor danmaku status in real time. |  |
| **LikeStore** | Like interaction: send likes, listen to like events, sync total likes. |  |
| **LiveAudienceStore** | Audience management: get real-time audience list (ID/name/avatar), check audience size, listen to Enter/Exit Event. |  |
| **AudioEffectStore** | Audio effects: voice-changing (child voice/male voice), reverberation (KTV), ear monitoring adjustment, switch special effects in real time. |  |
| **BaseBeautyStore** | Basic beauty filter: adjust skin smoothing/whitening/rosy (0-100 levels), reset beauty status, sync effect parameters. |  |

## FAQs

### Why is there no sound after the audience calls joinLive?

> **Platforms: Android / iOS**

> **Android：** - **Check device permissions:** Ensure the app has system permission to use the microphone.
> **iOS：** - **Check device permissions**: Ensure your app declares and requests microphone access (`NSMicrophoneUsageDescription`) in `Info.plist`.

> **Android：** - **Check the host:** Confirm the host has called `DeviceStore.shared().openLocalMicrophone(null)` to enable the microphone.
> **iOS：** - **Check host configuration**: Verify the host has enabled the microphone via `DeviceStore.shared.openLocalMicrophone(completion: nil)`.

> **Android：** - **Check the network:** Ensure the device's network connection is stable.
> **iOS：** - **Check network**: Confirm the device has a stable network connection.

### Why is the mic seat list not displayed or not updating?

> **Platforms: Android**

- **Check store initialization:** Make sure you have created the `LiveSeatStore` instance with the same `liveID` before calling `createLive` or `joinLive` (`LiveSeatStore.create(liveID)`).

- **Check data observation:** Ensure you are observing the `liveSeatStore.liveSeatState.seatList` data stream.

- **Check API calls:** Confirm that `createLive` (host) or `joinLive` (audience) was successfully called (check the `onSuccess` callback).åå

### Why is the seat list not displayed or not updating?

> **Platforms: iOS**

- **Check store initialization**: Confirm you create the `LiveSeatStore` instance (`LiveSeatStore.create(liveID: liveID)`) with the same `liveID` before calling `createLive` or `joinLive`.

- **Check data subscription**: Make sure you use Combine to subscribe to `liveSeatStore.state.seatList`, and the `cancellables` lifecycle matches your `ViewController`.

- **Check API calls**: Ensure `createLive` (host) or `joinLive` (audience) was called successfully (check the `.success` branch in `switch result`).

### Audience Enters the Room with No Sound

> **Platforms: Flutter**

- **Check device permissions**: Please ensure `App` has declared and obtained system authorization for microphone in `Info.plist` (iOS) or `AndroidManifest.xml` (Android).

- **Check the homeowner side**: Whether the room owner has normally called `DeviceStore.shared.openLocalMicrophone()` to open the microphone.

- **Check the network**: Please check whether the device network connection is normal.

### Microphone Position List Not Displayed or Refreshed

> **Platforms: Flutter**

- **Check Store initialization**: Please ensure you have already used the same `liveID` to correctly create the `LiveSeatStore` instance (`LiveSeatStore.create(liveID)`) before `createLive` or `joinLive`.

- **Check data listening**: Please check whether you have used `addListener` correctly to listen to `liveSeatStore.liveSeatState.seatList`, and ensure to call `removeListener` in `dispose` to remove listener.

- **Check API call**: Please ensure the `createLive` (room owner) or `joinLive` (listener) API has been successfully called (confirm when `isSuccess` in the callback result is `true`).
