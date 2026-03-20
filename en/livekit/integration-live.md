> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

This document guides you through building a basic live streaming app with host broadcasting and audience viewing capabilities using the core component LiveCoreView from the **AtomicXCore SDK**.

### Core Features

**LiveCoreView** is a lightweight View component purpose-built for live streaming scenarios. It serves as the foundation of your live streaming implementation, encapsulating all complex streaming technologies—including stream publishing and playback, co-hosting, and audio/video rendering. Use LiveCoreView as the "canvas" for your live video, enabling you to focus on developing your custom UI and interactions.

The following view hierarchy diagram illustrates the position and role of **LiveCoreView** within the live streaming interface:

## Core Concepts

| **Core Concept** | **Core Responsibility** | **Key API / Property** |
| --- | --- | --- |
| LiveCoreView | Handles publishing, playing, and rendering audio/video streams. Provides adapter interfaces for integrating custom UI components (e.g., user info, PK progress bar). | - `viewType`:   - `CoreViewType.PUSH_VIEW` (host publishing view)   - `CoreViewType.PLAY_VIEW` (audience playback view) - `setLiveId()`: Binds the live room ID for this view. - `setVideoViewAdapter():` Adapter for custom video display UI slots. |
| LiveListStore | Manages the complete live room lifecycle (create, join, leave), synchronizes state, and listens for passive events (e.g., live ended, kicked out). | - `createLive()`: Start live stream as host. - `endLive()`: End live stream as host. - `joinLive()`: Audience joins live room. - `leaveLive()`: Leave live room. |
| LiveInfo | Defines room parameters before going live, such as room ID, seat layout mode, max co-hosts, etc. | - `liveID`: Unique room identifier. - `seatLayoutTemplateID`: Layout template ID (e.g., 600 for dynamic grid). |

## Advanced Features
### Synchronizing Custom State in Live Streaming Room

In Live Streaming Room, hosts may need to synchronize custom information with all participants, such as the current room topic or background music. The `metaData` feature of `LiveListStore` supports this use case.

#### Implementation

On the host side, set custom information using the `updateLiveMetaData` API. `AtomicXCore` synchronizes these changes in real time to all participants. On the audience side, subscribe to `LiveListState.currentLive` and listen for changes in `metaData`. When a relevant key is updated, parse its value and update your business logic.

#### Example Code
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

## Platform: Android

## Preparation

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

Call `LoginStore.shared.login` in your project to complete login. **This is required before you can use any functionality in AtomicXCore**.

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
| SDKAppID | Int | Get this from [TRTC console > Application management](https://console.trtc.io/app). |
| UserID | String | The unique ID for the current user. Must contain only English letters, numbers, hyphens, and underscores. |
| userSig | String | A ticket for Tencent Cloud authentication. Please note: - **Development Environment**: You can use the local `GenerateTestUserSig.genTestSig` function to generate a UserSig or generate a temporary UserSig via the [UserSig Generation Tool](https://console.trtc.io/usersig). - **Production Environment**: To prevent key leakage, you must use a server-side method to generate UserSig. For details, see Generating UserSig on the Server. - For more information, see How to Calculate and Use UserSig. |

## Building a Basic Live Room

### Step 1: Implement Host Video Streaming

Follow these steps to quickly set up host video streaming:

> **Note：**
> 

> For a complete implementation, refer to [VideoLiveAnchorActivity.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/livestream/VideoLiveAnchorActivity.kt) in the open-source TUILiveKit project.
> 

1. **Initialize the Broadcaster Streaming View**

   In your host `Activity`, create a LiveCoreView instance and set viewType to `PUSH_VIEW` (publishing view).

   ``` java
   import io.trtc.tuikit.atomicxcore.api.view.CoreViewType
   import io.trtc.tuikit.atomicxcore.api.view.LiveCoreView
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import androidx.constraintlayout.widget.ConstraintLayout
   
   class YourAnchorActivity : AppCompatActivity() {
       private lateinit var coreView: LiveCoreView
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           coreView = LiveCoreView(this, viewType = CoreViewType.PUSH_VIEW)
           coreView.setLiveID("test_live_001")
   
           setupUI()
       }
   
       private fun setupUI() {
           setContentView(R.layout.activity_anchor)
   
           val container = findViewById<ConstraintLayout>(R.id.container)
           container.addView(coreView)
   
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
2. **Open Camera and Microphone**

   Call the `openLocalCamera` and `openLocalMicrophone` methods of `DeviceStore` to enable the camera and microphone. **No additional action is required—LiveCoreView will automatically preview the current camera video stream**.

   ``` java
   import androidx.appcompat.app.AppCompatActivity
   import android.os.Bundle
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   
   class YourAnchorActivity : AppCompatActivity() {
       // ... other code ...
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setupUI()
           openDevices()
       }
   
       private fun openDevices() {
           DeviceStore.shared().openLocalCamera(true, completion = null)
           DeviceStore.shared().openLocalMicrophone(completion = null)
       }
   }
   ```
3. **Start Live Streaming**

   Start the live stream by calling `createLive` on `LiveListStore`.

   ``` java
   import android.util.Log
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   
   class YourAnchorActivity : AppCompatActivity() {
       // ... other code ...
   
       private fun startLive() {
           val liveInfo = LiveInfo().apply {
               liveID = "test_live_001"
               liveName = "test live"
               seatLayoutTemplateID = 600 // Default: dynamic grid layout
               keepOwnerOnSeat = true
           }
           LiveListStore.shared().createLive(liveInfo, object: LiveInfoCompletionHandler {
               override fun onFailure(code: Int, desc: String) {
                   Log.e("Live", "startLive error: $desc")
               }
   
               override fun onSuccess(liveInfo: LiveInfo) {
                   Log.d("Live", "startLive success")
               }
           })
       }
   }
   ```

   `LiveInfo`** Parameter Description:**

| **Parameter Name** | **Type** | **Required** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Yes | Unique identifier for the live room. |
| `liveName` | `String` | No | Title of the live room. |
| `notice` | `String` | No | Announcement message for the live room. |
| `isMessageDisable` | `Boolean` | No | Disable chat (true = yes, false = no). |
| `isPublicVisible` | `Boolean` | No | Room is publicly visible (true = yes, false = no). |
| `isSeatEnabled` | `Boolean` | No | Enable seat (co-host) functionality (true = yes, false = no). |
| `keepOwnerOnSeat` | `Boolean` | No | Keep the owner on a seat. |
| `maxSeatCount` | `Int` | Yes | Maximum number of seats. |
| `seatMode` | TakeSeatMode | No | Seat mode (FREE: free to take seat, APPLY: apply to take seat). |
| `seatLayoutTemplateID` | `Int` | Yes | Seat layout template ID. |
| `coverURL` | `String` | No | Cover image URL for the live room. |
| `backgroundURL` | `String` | No | Background image URL for the live room. |
| `categoryList` | `List` | No | List of category tags for the live room. |
| `activityStatus` | `Int` | No | Live activity status. |
| `isGiftEnabled` | `Boolean` | No | Whether to enable gift functionality (true: yes, false: no). |

4. **End Live Streaming**

   When the live stream ends, call the `endLive` method of `LiveListStore`. The SDK will handle stopping the stream and destroying the room.

   ``` java
   import android.util.Log
   import androidx.appcompat.app.AppCompatActivity
   import com.tencent.cloud.tuikit.engine.extension.TUILiveListManager
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import io.trtc.tuikit.atomicxcore.api.live.StopLiveCompletionHandler
   
   class YourAnchorActivity : AppCompatActivity() {
       // ... other code ...
   
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
   
       override fun onDestroy() {
           super.onDestroy()
           stopLive()
           DeviceStore.shared().closeLocalCamera()
           DeviceStore.shared().closeLocalMicrophone()
           Log.d("Live", "YourAnchorActivity onDestroy")
       }
   }
   ```

### Step 2: Implement Audience Join and Watch

Follow these steps to enable audience viewing:

> **Note：**
> 

> For a complete reference implementation of audience viewing logic, see [VideoLiveAudienceActivity.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/livestream/VideoLiveAudienceActivity.kt) in the open-source TUILiveKit project.
> 

1. **Add Audience Playback View**

   In your audience `Activity`, create a `LiveCoreView` instance and set `viewType` to `PLAY_VIEW` (playback view).

   ``` java
   import io.trtc.tuikit.atomicxcore.api.view.CoreViewType
   import io.trtc.tuikit.atomicxcore.api.view.LiveCoreView
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import androidx.constraintlayout.widget.ConstraintLayout
   
   // YourAudienceActivity represents your audience viewing Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // 1. Initialize the audience playback view
       private lateinit var coreView: LiveCoreView
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           // 2. Initialize the view
           coreView = LiveCoreView(this, viewType = CoreViewType.PLAY_VIEW)
           coreView.setLiveId("test_live_001")
   
           // UI layout
           setupUI()
       }
   
       private fun setupUI() {
           setContentView(R.layout.activity_audience)
   
           // 3. Add the playback view to your layout
           val container = findViewById<ConstraintLayout>(R.id.container)
           container.addView(coreView)
   
           // Set layout parameters
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
2. **Join the Live Room**

   Call the `joinLive` method of `LiveListStore` to join the live stream. **No additional setup is required—LiveCoreView will automatically play the video stream.**

   ``` java
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfo
   import io.trtc.tuikit.atomicxcore.api.live.LiveInfoCompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import android.util.Log
   
   // YourAudienceActivity represents your audience viewing Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // ... other code ...
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setupUI()
           // 1. Join the live room (liveID must match the host's)
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
3. **Exit the Live Room**

   When the audience leaves, call the `leaveLive` method of `LiveListStore`. The SDK will automatically stop playback and exit the room.

   ``` java
   import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import androidx.appcompat.app.AppCompatActivity
   import android.util.Log
   
   // YourAudienceActivity represents your audience viewing Activity
   class YourAudienceActivity : AppCompatActivity() {
   
       // ... other code ...
   
       // Exit the live room
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
   
       // Always call leaveLive when Activity is destroyed
       override fun onDestroy() {
           super.onDestroy()
           leaveLive()
           Log.d("Live", "YourAudienceActivity onDestroy")
       }
   }
   ```

### Step 3: Listen for Live Events

After joining a live room, you should handle passive events such as the host ending the stream or the audience being removed for violations. If you do not listen for these events, the audience UI may remain in an invalid state, impacting user experience.

Implement the `LiveListListener` interface and register it with `LiveListStore`:
``` java
import io.trtc.tuikit.atomicxcore.api.live.LiveEndedReason
import io.trtc.tuikit.atomicxcore.api.live.LiveKickedOutReason
import io.trtc.tuikit.atomicxcore.api.live.LiveListListener
import io.trtc.tuikit.atomicxcore.api.live.LiveListStore
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import android.util.Log

// YourAudienceActivity represents your audience viewing Activity
class YourAudienceActivity : AppCompatActivity() {
    
    // ... (coreView, liveId, setupUI, joinLive, leaveLive, etc.) ...

    // 2. Define a LiveListListener instance
    private val liveListListener = object : LiveListListener() {
        override fun onLiveEnded(liveID: String, reason: LiveEndedReason, message: String) {
            // Listen for live stream end
            Log.d("Live", "Live ended. liveID: $liveID, reason: $reason, message: $message")
            // Handle exit logic, e.g., finish()
            // finish()
        }

        override fun onKickedOutOfLive(liveID: String, reason: LiveKickedOutReason, message: String) {
            // Listen for being kicked out of the live room
            Log.d("Live", "Kicked out of live. liveID: $liveID, reason: $reason, message: $message")
            // Handle exit logic
            // finish()
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setupUI() 
        
        // 3. Register the listener
        LiveListStore.shared().addLiveListListener(liveListListener)

        // 4. Join the live room
        joinLive()
    }
    
    // ... joinLive() and leaveLive() methods ...

    override fun onDestroy() {
        super.onDestroy()
        // 5. Remove the listener to prevent memory leaks
        LiveListStore.shared().removeLiveListListener(liveListListener)
        leaveLive()
        Log.d("Live", "YourAudienceActivity onDestroy")
    }
}
```

### Run and Test

After integrating `LiveCoreView`, you will have a pure video rendering view with full live streaming capabilities, but without any interactive UI. To add interactive features, see Enriching the Live Streaming Scene.
|  | Dynamic Grid Layout | Floating Window Layout | Fixed Grid Layout | Fixed Window Layout |
| --- | --- | --- | --- | --- |
| **Template ID** | 600 | 601 | 800 | 801 |
| **Description** | Default layout; grid size adjusts dynamically based on number of co-hosts. | Co-hosts are displayed as floating windows. | Fixed number of co-hosts; each occupies a fixed grid. | Fixed number of co-hosts; guests are displayed as fixed windows. |
| **Example** |  |  |  |  |

## Enriching the Live Streaming Scene

Once the basic live streaming functionality is in place, refer to the following guides to add interactive features to your live stream:
| **Feature** | **Description** | **Store** | **Implementation Guide** |
| --- | --- | --- | --- |
| **Enable Audience Audio/Video Co-hosting** | Audience can apply to join the host on stage for real-time video interaction. | CoGuestStore | Implementation Guide |
| **Enable Host Cross-room PK** | Hosts from different rooms can connect for interaction or PK. | CoHostStore BattleStore | Implementation Guide |
| **Add Bullet Chat Feature** | Audience can send and receive real-time text messages in the live room. | BarrageStore | Implementation Guide |
| **Build a Gift Giving System** | Audience can send virtual gifts to the host, increasing engagement and fun. | GiftStore | Implementation Guide |

## API Documentation
| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| LiveCoreView | Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. |  |
| LiveListStore | Manages the full lifecycle of live rooms: create/join/leave/destroy rooms, query room list, modify live info (name, announcement, etc.), and listen to live status events (e.g., kicked out, ended). |  |
| DeviceStore | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. |  |
| CoGuestStore | Manages audience co-hosting: apply/invite/accept/reject co-host requests, member permission control (microphone/camera), and status synchronization. |  |
| CoHostStore | Handles host cross-room connections: supports multiple layout templates (dynamic grid, etc.), initiates/accepts/rejects connections, and manages co-host interactions. |  |
| BattleStore | Manages host PK battles: initiate PK (set duration/opponent), manage PK status (start/end), synchronize scores, and listen for battle results. |  |
| GiftStore | Handles gift interactions: get gift list, send/receive gifts, and listen for gift events (including sender and gift details). |  |
| BarrageStore | Supports live chat: send text/custom danmaku, maintain danmaku list, and monitor danmaku status in real time. |  |
| LikeStore | Handles like interactions: send likes, listen for like events, and synchronize total like count. |  |
| LiveAudienceStore | Manages audience: get real-time audience list (ID/name/avatar), count audience, and listen for join/leave events. |  |
| AudioEffectStore | Audio effects: voice change (child/male), reverb (KTV, etc.), ear return adjustment, and real-time effect switching. |  |
| BaseBeautyStore | Basic beauty filters: adjust smoothing/whitening/rosiness (0-9), reset beauty status, and synchronize effect parameters. |  |

## Platform: iOS

## Preparation

### Step 1: Activate the Service

See Activate Service to obtain either the trial or paid version of the SDK.Then, go to [the Console](https://console.trtc.io/app) for application management, and get the following:
- `SDKAppID`: Application identifier (required). Tencent Cloud uses `SDKAppID` for billing and details.

- `SDKSecretKey`: Application secret key, used to initialize the configuration file with secret information.

   

### Step 2: Import AtomicXCore into Your Project
1. **Install the Component**: Add `pod 'AtomicXCore'` to your `Podfile`, then run `pod install`.

   ``` ruby
   target 'xxxx' do
     pod 'AtomicXCore'
   end
   ```
2. **Configure App Permissions**: Add camera and microphone usage descriptions to your app's `Info.plist` file.

   ``` xml
   <key>NSCameraUsageDescription</key>
   <string>TUILiveKit needs camera access to enable video recording with picture</string>
   <key>NSMicrophoneUsageDescription</key>
   <string>TUILiveKit needs microphone permission to enable sound in recorded videos</string>
   ```
   

### Step 3: Implement Login Logic

Call `LoginStore.shared.login` in your project to complete authentication. **This is required before using any functionality of AtomicXCore**.

> **Note：**
> 

> We recommend calling LoginStore.shared.login only after your app's own user authentication is successful, to ensure clear and consistent login logic.
> 

``` swift
import AtomicXCore

//  AppDelegate.swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    LoginStore.shared.login(sdkAppID: 1400000001,                  // Replace with your SDKAppID
                            userID: "test_001",                    // Replace with your UserID
                            userSig: "xxxxxxxxxxx") { result in    // Replace with your UserSig
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

**Login API Parameter Description**
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `SDKAppID` | `Int` | Get this from [TRTC console](https://console.trtc.io/app). |
| `UserID` | `String` | The unique ID for the current user. Must contain only English letters, numbers, hyphens, and underscores. |
| `userSig` | `String` | A ticket for Tencent Cloud authentication. Please note: - Development Environment: You can use the local `GenerateTestUserSig.genTestSig` function to generate a `UserSig` or generate a temporary UserSig via the [UserSig Generation Tool](https://console.trtc.io/usersig). - Production Environment: To prevent key leakage, you must use a server-side method to generate `UserSig`. For details, see Generating UserSig on the Server. |

## Building a Basic Live Room

### Step 1: Implement Broadcaster Video Streaming

Follow these steps to quickly set up broadcaster video streaming:

> **Note：**
> 

> For a complete broadcaster streaming implementation, refer to [TUILiveRoomAnchorViewController.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/LiveStream/TUILiveRoomAnchorViewController.swift) in the TUILiveKit open source project.
> 

1. **Initialize the Broadcaster Streaming View**

   In your broadcaster view controller, create a `LiveCoreView` instance and set `viewType` to `.pushView`.

   ``` swift
   import AtomicXCore
   
   // YourAnchorViewController represents your broadcaster streaming view controller
   class YourAnchorViewController: UIViewController {
   
       private let liveId: String
       // 1. Add LiveCoreView as a property of your view controller
       private let coreView = LiveCoreView(viewType: .pushView, frame: UIScreen.main.bounds)
   
       // 2. Add a convenience initializer for instance initialization
       //    - liveId: The ID of the live room to start streaming
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
           // 3. Add the broadcaster streaming view to your view
           view.addSubview(coreView)
       }
   }
   ```
2. **Open Camera and Microphone**

   Call `DeviceStore.shared.openLocalCamera` and `DeviceStore.shared.openLocalMicrophone` to open the camera and microphone. **No further action is required—LiveCoreView automatically previews the current camera video stream**.

   ``` swift
   import AtomicXCore
   
   class YourAnchorViewController: UIViewController {
       // ... other code ...
       public override func viewDidLoad() {
           super.viewDidLoad()
           // Enable devices
           openDevices()
       }
   
       private func openDevices() {
           // 1. Enable front camera
           DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
           // 2. Enable microphone
           DeviceStore.shared.openLocalMicrophone(completion: nil)
       }
   }
   ```
3. **Start Live Streaming**

   Call `LiveListStore.shared.createLive` to start streaming.

   ``` swift
   import AtomicXCore
   
   class YourAnchorViewController: UIViewController {
   
       // ... other code ...
   
       // Call the start streaming interface to begin live streaming
       private func startLive() {
           var liveInfo = LiveInfo()
           // 1. Set the live room ID
           liveInfo.liveID = self.liveId
           // 2. Set the live room name
           liveInfo.liveName = "test live"
           // 3. Configure layout template, default: 600 dynamic grid layout
           liveInfo.seatLayoutTemplateID = 600
           // 4. Keep the broadcaster always on the seat
           liveInfo.keepOwnerOnSeat = true
           // 5. Call LiveListStore.shared.createLive to start streaming
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

   `LiveInfo` Parameter Descriptions:

| **Parameter Name** | **Type** | **Attribute** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Required | Unique identifier for the live room. |
| `liveName` | `String` | Optional | Title of the live room. |
| `notice` | `String` | Optional | Announcement for the live room. |
| `isMessageDisable` | `Bool` | Optional | Whether to mute chat (true: yes, false: no). |
| `isPublicVisible` | `Bool` | Optional | Whether the room is publicly visible (true: yes, false: no). |
| `isSeatEnabled` | `Bool` | Optional | Whether to enable seat functionality (true: yes, false: no). |
| `keepOwnerOnSeat` | `Bool` | Optional | Whether to keep the owner on the seat. |
| `maxSeatCount` | `Int` | Required | Maximum number of seats. |
| `seatMode` | `TakeSeatMode` | Optional | Seat mode (.free: free to take seat, .apply: apply to take seat). |
| `seatLayoutTemplateID` | `UInt` | Required | Seat layout template ID. |
| `coverURL` | `String` | Optional | Cover image URL for the live room. |
| `backgroundURL` | `String` | Optional | Background image URL for the live room. |
| `categoryList` | `NSNumber` | Optional | Category tag list for the live room. |
| `activityStatus` | `Int` | Optional | Live activity status. |
| `isGiftEnabled` | `Bool` | Optional | Whether to enable gift functionality (true: yes, false: no). |

4. **End Live Streaming**

   After the live stream ends, call `LiveListStore.shared.endLive `to stop streaming and destroy the room. The SDK handles cleanup automatically.

   ``` swift
   import AtomicXCore
   
   class YourAnchorViewController: UIViewController {
   
       // ... other code ...
   
       // End live streaming
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

### Step 2: Implement Audience Join and Watch

Enable audience viewing with the following steps:

> **Note：**
> 

> For a complete audience playback implementation, refer to [TUILiveRoomAudienceViewController.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/LiveStream/TUILiveRoomAudienceViewController.swift) in the TUILiveKit open source project.
> 

1. **Implement the Audience Playback Page**

   In your audience view controller, create a `LiveCoreView` instance and set `viewType` to `.playView`.

   ``` swift
   import AtomicXCore
   
   class YourAudienceViewController: UIViewController {
   
       // 1. Initialize the audience playback page
       private let coreView = LiveCoreView(viewType: .playView, frame: UIScreen.main.bounds)
       private let liveId: String
   
       public init(liveId: String) {
           self.liveId = liveId
           super.init(nibName: nil, bundle: nil)
           // 2. Bind the live ID
           self.coreView.setLiveID(liveId)
       }
   
       required init?(coder: NSCoder) {
           fatalError("init(coder:) has not been implemented")
       }
   
       public override func viewDidLoad() {
           super.viewDidLoad()
           // 3. Add the playback view to your view
           view.addSubview(coreView)
       }
   }
   ```
2. **Join the Live Room to Watch**

   Call `LiveListStore.shared.joinLive` to join the live stream. **No further action is required—LiveCoreView will automatically play the current room's video stream**.

   ``` swift
   import AtomicXCore
   
   class YourAudienceViewController: UIViewController {
       // ... other code ...
   
       public override func viewDidLoad() {
           super.viewDidLoad()
           view.addSubview(coreView)
           // 4. Join the live room
           joinLive()
       }
   
       private func joinLive() {
           // Call LiveListStore.shared.joinLive to enter the live room
           // - liveId: Same liveId as the broadcaster
           LiveListStore.shared.joinLive(liveID: liveId) { [weak self] result in
               guard let self = self else { return }
               switch result {
               case .success(let info):
                   debugPrint("joinLive success")
               case .failure(let error):
                   debugPrint("joinLive error \(error.message)")
                   // If joining fails, exit the page
                   // self.dismiss(animated: true)
               }
           }
       }
   }
   ```
3. **Leave the Live Room**

   When the audience leaves, call `LiveListStore.shared.leaveLive` to exit. The SDK will automatically stop playback and leave the room.

   ``` swift
   import AtomicXCore
   
   class YourAudienceViewController: UIViewController {
   
       // ... other code ...
   
       // Leave the live room
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

### Step 3: Listen for Live Events

After joining a live room, handle "passive" events such as the broadcaster ending the stream or the audience being removed. If you do not listen for these events, the audience UI may remain on a black screen, impacting user experience.

Subscribe to `liveListEventPublisher` from `LiveListStore` to listen for events:
``` swift
import AtomicXCore
import Combine // 1. Import the Combine framework

class YourAudienceViewController: UIViewController {

    // ... other code (coreView, liveId, init, deinit, joinLive, leaveLive, etc.) ...

    // 2. Define cancellableSet to manage subscription lifecycle
    private var cancellableSet: Set<AnyCancellable> = []

    public override func viewDidLoad() {
        super.viewDidLoad()
        view.addSubview(coreView)
        // 3. Listen for live events
        setupLiveEventListener()
        // 4. Join the live room
        joinLive()
    }

    // 5. Add a method to set up event listeners
    private func setupLiveEventListener() {
        LiveListStore.shared.liveListEventPublisher
            .receive(on: RunLoop.main) // Ensure UI updates are handled on the main thread
            .sink { [weak self] event in
                guard let self = self else { return }
                switch event {
                case .onLiveEnded(let liveID, let reason, let message):
                    // Live stream ended
                    debugPrint("Live ended. liveID: \(liveID), reason: \(reason.rawValue), message: \(message)")
                    // Handle logic to exit the live room, e.g., close the current page
                    // self.dismiss(animated: true)
                case .onKickedOutOfLive(let liveID, let reason, let message):
                    // Kicked out of live stream
                    debugPrint("Kicked out of live. liveID: \(liveID), reason: \(reason.rawValue), message: \(message)")
                    // Handle logic to exit the live room
                    // self.dismiss(animated: true)
                }
            }
            .store(in: &cancellableSet) // Manage subscription
    }

    // ... joinLive() and leaveLive() methods ...
}
```

### Run and Test

After integrating `LiveCoreView`, you will have a pure video rendering view with full live streaming capabilities, but without any interactive UI. To add interactive features, see Enriching the Live Streaming Scene.
|  | **Dynamic Grid Layout** | **Floating Window Layout** | **Fixed Grid Layout** | **Fixed Window Layout** |
| --- | --- | --- | --- | --- |
| Template ID | 600 | 601 | 800 | 801 |
| Description | Default layout; grid size adjusts dynamically based on number of co-hosts. | Co-hosts are displayed as floating windows. | Fixed number of co-hosts; each occupies a fixed grid. | Fixed number of co-hosts; guests are displayed as fixed windows. |
| Example |  |  |  |  |

## Enriching the Live Streaming Scene

Once the basic live streaming functionality is in place, refer to the following guides to add interactive features to your live stream:
| **Feature** | **Description** | **Store** | **Implementation Guide** |
| --- | --- | --- | --- |
| Enable Audience Audio/Video Co-hosting | Audience can apply to join the host and interact via real-time video. | CoGuestStore | Implementation Guide |
| Enable Host Cross-room PK | Hosts from different rooms can connect for interaction or PK. | CoHostStore | Implementation Guide |
| Add Bullet Chat Feature | Audience can send and receive real-time text messages in the live room. | BarrageStore | Implementation Guide |
| Build a Gift Giving System | Audience can send virtual gifts to the host, increasing engagement and fun. | GiftStore | Implementation Guide |

## API Documentation
| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| LiveCoreView | Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. | LiveCoreView |
| LiveListStore | Manages the full lifecycle of live rooms: create/join/leave/destroy rooms, query room list, modify live info (name, announcement, etc.), and listen to live status events (e.g., kicked out, ended). | LiveListStore |
| DeviceStore | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. | DeviceStore |
| CoGuestStore | Manages audience co-hosting: apply/invite/accept/reject co-host requests, member permission control (microphone/camera), and status synchronization. | CoGuestStore |
| CoHostStore | Handles host cross-room connections: supports multiple layout templates (dynamic grid, etc.), initiates/accepts/rejects connections, and manages co-host interactions. | CoHostStore |
| BattleStore | Manages host PK battles: initiate PK (set duration/opponent), manage PK status (start/end), synchronize scores, and listen for battle results. | BattleStore |
| GiftStore | Handles gift interactions: get gift list, send/receive gifts, and listen for gift events (including sender and gift details). | GiftStore |
| BarrageStore | Supports live chat: send text/custom danmaku, maintain danmaku list, and monitor danmaku status in real time. | BarrageStore |
| LikeStore | Handles like interactions: send likes, listen for like events, and synchronize total like count. | LikeStore |
| LiveAudienceStore | Manages audience: get real-time audience list (ID/name/avatar), count audience, and listen for join/leave events. | LiveAudienceStore |
| AudioEffectStore | Audio effects: voice change (child/male), reverb (KTV, etc.), ear return adjustment, and real-time effect switching. | AudioEffectStore |
| BaseBeautyStore | Basic beauty filters: adjust smoothing/whitening/rosiness (0-9), reset beauty status, and synchronize effect parameters. | BaseBeautyStore |

## Platform: Flutter

## Core Features

**LiveCoreWidget** is a lightweight widget purpose-built for live streaming scenarios and serves as the foundation of your live streaming interface. It abstracts complex live streaming technologies—including streaming, co-hosting, and audio/video rendering—so you can use LiveCoreWidget as the canvas for your live video. This allows you to focus on developing your custom UI and interactive features.

The following diagram illustrates where **LiveCoreWidget** fits within your live streaming interface and its role in the view hierarchy:

## Preparations

### Step 1: Activate the Service

See Activate Service to obtain either the trial or paid version of the SDK.Then, go to [the Console](https://console.trtc.io/app) for application management, and get the following:
- `SDKAppID`: Application identifier (required). Tencent Cloud uses `SDKAppID` for billing and details.

- `SDKSecretKey`: Application secret key, used to initialize the configuration file with secret information.

   

### Step 2: Import AtomicXCore into Your Project
1. **Component installation**: Please add the `atomic_x_core` dependency in your `pubspec.yaml` file and execute `flutter pub get`.

   ``` yaml
   dependencies:
     atomic_x_core: ^3.6.0
   ```
2. **Configure project permissions:** Both Android and iOS projects require configuration.

   

【Android】

Please add permission to use camera and microphone in the `android/app/src/main/AndroidManifest.xml` file.
``` xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

【iOS】

Please add permission to use camera and microphone in the `iOS` directory **Podfile** and `ios/Runner` directory `Info.plist` file.

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
<string>TUILiveKit needs to access your camera permission. Video recording with picture only after enabling.</string>
<key>NSMicrophoneUsageDescription</key>
<string>TUILiveKit needs to access your mic permission. Recorded video will have sound when enabled.</string>
```

### Step 3: Implement Login Logic

Call `LoginStore.shared.login` in your project to complete login. **This is the key premise for using all functions of AtomicXCore.**

> **Important:**
> 

> It is recommended to call LoginStore.shared.login after successful log-in to your App's user account to ensure clear and consistent business logic.
> 

``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();

  final result = await LoginStore.shared.login(
    sdkAppID: 1400000001,           // replace with your sdkAppID
    userID: "test_001",             // replace with your userID
    userSig: "xxxxxxxxxxx",         // replace with your userSig
  );

  if (result.isSuccess) {
    debugPrint("login success");
  } else {
    debugPrint("login failed code: ${result.errorCode}, message: ${result.errorMessage}");
  }

  runApp(const MyApp());
}
```

**Logon API parameter description:**
| **Parameter** | **Type** | **Note** |
| --- | --- | --- |
| `sdkAppID` | `int` | Get this from [TRTC console](https://console.trtc.io/app). |
| `userID` | `String` | The unique ID for the current user. Must contain only English letters, numbers, hyphens, and underscores.. To avoid multi-end login conflict, **do not use**`1`, `123`**or other simple IDs**. |
| `userSig` | `String` | A ticket for Tencent Cloud authentication. Please note: - Development Environment: You can use the local `GenerateTestUserSig.genTestSig` function to generate a `UserSig` or generate a temporary UserSig via the [UserSig Generation Tool](https://console.trtc.io/usersig). - Production Environment: To prevent key leakage, you must use a server-side method to generate `UserSig`. For details, see Generating UserSig on the Server. |

## Building a Basic Live Room

### Step 1: Implement Broadcaster Video Streaming

The anchor starts live streaming process is as follows. You just need to perform the following steps to quickly build a live stream.

> **Note:**
> 

> For the business code of live stream publishing, you can also refer to the [live_room_anchor_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/live_room_anchor_widget.dart) file in the TUILiveKit open-source project to understand the complete implementation logic.
> 

1. **Initialize the Anchor live streaming view**

   On your Anchor page, create a `LiveCoreWidget` instance and control the live stream behavior through `LiveCoreController`.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAnchorPage represents your anchor starts live streaming page
   class YourAnchorPage extends StatefulWidget {
     final String liveId;
   
     const YourAnchorPage({super.key, required this.liveId});
   
     @override
     State<YourAnchorPage> createState() => _YourAnchorPageState();
   }
   
   class _YourAnchorPageState extends State<YourAnchorPage> {
     // 1. Create a LiveCoreController instance
     late final LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       // 2. Initialize the controller
       _controller = LiveCoreController.create();
       // 3. Set up live streaming ID
       _controller.setLiveID(widget.liveId);
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             // 4. Load the broadcaster stream page to your view
             LiveCoreWidget(controller: _controller),
             // Your other UI components
           ],
         ),
       );
     }
   }
   ```
2. **Turn on the camera and microphone**

   By calling the **DeviceStore**`openLocalCamera` and `openLocalMicrophone` APIs to turn on the camera and microphone, **no need to do additional configuration as LiveCoreWidget will auto preview the current camera's video stream**. Sample code:

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAnchorPage represents your anchor starts live streaming page
   class YourAnchorPage extends StatefulWidget {
     final String liveId;
   
     const YourAnchorPage({super.key, required this.liveId});
   
     @override
     State<YourAnchorPage> createState() => _YourAnchorPageState();
   }
   
   class _YourAnchorPageState extends State<YourAnchorPage> {
     late final LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
       // Turn on the device
       _openDevices();
     }
   
     void _openDevices() {
       // 1. Open front camera
       DeviceStore.shared.openLocalCamera(true);
       // 2. Turn on the microphone
       DeviceStore.shared.openLocalMicrophone();
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
3. **Start live streaming**

   By calling the **LiveListStore**`createLive` API to start video live streaming, sample code:

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAnchorPage represents your anchor starts live streaming page
   class YourAnchorPage extends StatefulWidget {
     final String liveId;
   
     const YourAnchorPage({super.key, required this.liveId});
   
     @override
     State<YourAnchorPage> createState() => _YourAnchorPageState();
   }
   
   class _YourAnchorPageState extends State<YourAnchorPage> {
     late final LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       _controller = LiveCoreController.create();
       _controller.setLiveID(widget.liveId);
       _openDevices();
     }
   
     void _openDevices() {
       DeviceStore.shared.openLocalCamera(true);
       DeviceStore.shared.openLocalMicrophone();
     }
   
     // Call the go live API to start live streaming
     Future<void> _startLive() async {
       // 1. Prepare the LiveInfo object
       final liveInfo = LiveInfo(
         // Set up live streaming room id
         liveID: widget.liveId,
         // Set up live streaming room name
         liveName: "test live stream",
         // Configure the layout template, default: 600 dynamic grid layout
         seatLayoutTemplateID: 600,
         // Configure the anchor to always stay on the seat
         keepOwnerOnSeat: true,
       );
   
       // 2. Call LiveListStore.shared.createLive to start live streaming
       final result = await LiveListStore.shared.createLive(liveInfo);
   
       if (result.isSuccess) {
         debugPrint("startLive success");
       } else {
         debugPrint("startLive error: ${result.errorMessage}");
       }
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             LiveCoreWidget(controller: _controller),
             // Go live button
             Positioned(
               bottom: 50,
               left: 0,
               right: 0,
               child: Center(
                 child: ElevatedButton(
                   onPressed: _startLive,
                   child: const Text('Start live broadcast'),
                 ),
               ),
             ),
           ],
         ),
       );
     }
   }
   ```

   **LiveInfo parameter description:**

| **Parameter Name** | **Type** | **Attribute** | **Description** |
| --- | --- | --- | --- |
| `liveID` | `String` | Required | Unique identifier for the live streaming room. |
| `liveName` | `String` | Optional. | Title of the live streaming room. |
| `notice` | `String` | Optional. | Announcement information of the live streaming room. |
| `isMessageDisable` | `bool` | Optional. | Whether to mute (`true`: yes, `false`: no). |
| `isPublicVisible` | `bool` | Optional. | Whether publicly visible (`true`: yes, `false`: no). |
| `isSeatEnabled` | `bool` | Optional. | Whether to enable the seat feature (`true`: yes, `false`: no). |
| `keepOwnerOnSeat` | `bool` | Optional. | Whether to keep the room owner on the seat. |
| `maxSeatCount` | `int` | Optional. | Maximum number of microphones. |
| `seatMode` | `TakeSeatMode` | Optional. | Microphone mode (`TakeSeatMode.free`: Free to Join the Podium, `TakeSeatMode.apply`: Request to speak). |
| `seatLayoutTemplateID` | `int` | Required | layout template ID. |
| `coverURL` | `String` | Optional. | Thumbnail URL of the live streaming room. |
| `backgroundURL` | `String` | Optional. | Background image URL of the live streaming room. |
| `categoryList` | `List<int>` | Optional. | Tag list of categories in the live streaming room. |
| `activityStatus` | `int` | Optional. | Live stream status. |
| `isGiftEnabled` | `bool` | Optional. | Whether to enable the gift function (`true`: yes, `false`: no). |

4. **End live streaming**

   After the live streaming ends, the Anchor can call the **LiveListStore**'s `endLive` API to end the live stream. The SDK will handle the logic of stopping streaming and room termination.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAnchorPage represents your anchor starts live streaming page
   class YourAnchorPage extends StatefulWidget {
     final String liveId;
   
     const YourAnchorPage({super.key, required this.liveId});
   
     @override
     State<YourAnchorPage> createState() => _YourAnchorPageState();
   }
   
   class _YourAnchorPageState extends State<YourAnchorPage> {
     // ... Other code ...
   
     End live streaming
     Future<void> _stopLive() async {
       final result = await LiveListStore.shared.endLive();
   
       if (result.isSuccess) {
         debugPrint("endLive success");
       } else {
         debugPrint("endLive error: ${result.errorMessage}");
       }
     }
   
     // ... Other code ...
   }
   ```

### Step 2: Implement Audience Join and Watch

The audience viewing process is as follows. In just a few simple steps, you can achieve audience watching live.

> **Note:**
> 

> For the business code of audience enter room to pull stream, you can also refer to the [audience_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/audience/audience_widget.dart) file in the TUILiveKit open-source project to understand the complete implementation logic.
> 

1. **Implement the audience pull stream page**

   On your audience page, create a `LiveCoreWidget` instance and control the live stream behavior through `LiveCoreController`.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage represents your audience viewing webpage
   class YourAudiencePage extends StatefulWidget {
     final String liveId;
   
     const YourAudiencePage({super.key, required this.liveId});
   
     @override
     State<YourAudiencePage> createState() => _YourAudiencePageState();
   }
   
   class _YourAudiencePageState extends State<YourAudiencePage> {
     // 1. Create a LiveCoreController instance
     late final LiveCoreController _controller;
   
     @override
     void initState() {
       super.initState();
       // 2. Initialize the controller
       _controller = LiveCoreController.create();
       // 3. Bind live streaming id
       _controller.setLiveID(widget.liveId);
     }
   
     @override
     Widget build(BuildContext context) {
       return Scaffold(
         body: Stack(
           children: [
             // 4. Load the audience pull stream page to your view
             LiveCoreWidget(controller: _controller),
           ],
         ),
       );
     }
   }
   ```
2. **Enter live room to watch**

   By calling the **LiveListStore**`joinLive` API to join the live stream, **no need to do additional configuration, LiveCoreWidget will autoplay the current room video stream**, sample code:

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage represents your audience viewing webpage
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
       // Enter live room
       _joinLive();
     }
   
     Future<void> _joinLive() async {
       // Call LiveListStore.shared.joinLive to enter live room
       // - liveId: same liveId as anchor starts live streaming
       final result = await LiveListStore.shared.joinLive(widget.liveId);
   
       if (result.isSuccess) {
         debugPrint("joinLive success");
       } else {
         debugPrint("joinLive error: ${result.errorMessage}");
         // Failed to enter the room, must exit the page
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
3. **Log out of live stream**

   When the audience exits the live streaming room, they need to call the **LiveListStore**'s `leaveLive` API to exit the live stream. The SDK will automatically stop pulling streams and leave the room.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   /// YourAudiencePage represents your audience viewing webpage
   class YourAudiencePage extends StatefulWidget {
     final String liveId;
   
     const YourAudiencePage({super.key, required this.liveId});
   
     @override
     State<YourAudiencePage> createState() => _YourAudiencePageState();
   }
   
   class _YourAudiencePageState extends State<YourAudiencePage> {
     // ... Other code ...
   
     End live stream
     Future<void> _leaveLive() async {
       final result = await LiveListStore.shared.leaveLive();
   
       if (result.isSuccess) {
         debugPrint("leaveLive success");
       } else {
         debugPrint("leaveLive error: ${result.errorMessage}");
       }
     }
   
     // ... Other code ...
   }
   ```

### Step 3: Listen for Live Events

After the audience joins the live room, you also need to process some passive events inside the room. For example, the Anchor proactively ends the live stream, or the audience is kicked out of the room due to rule violation. If these events are not listened to, the client UI may stay on a black screen webpage, affecting user experience.

You can achieve event monitoring through `LiveListListener`.
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';

/// YourAudiencePage represents your audience viewing webpage
class YourAudiencePage extends StatefulWidget {
  final String liveId;

  const YourAudiencePage({super.key, required this.liveId});

  @override
  State<YourAudiencePage> createState() => _YourAudiencePageState();
}

class _YourAudiencePageState extends State<YourAudiencePage> {
  late final LiveCoreController _controller;
  // 1. Define LiveListListener to manage event monitoring
  late final LiveListListener _liveListListener;

  @override
  void initState() {
    super.initState();
    _controller = LiveCoreController.create();
    _controller.setLiveID(widget.liveId);
    // 2. Listen to Live Event
    _setupLiveEventListener();
    // 3. Enter live room
    _joinLive();
  }

  // 4. Add a method to set up event listening
  void _setupLiveEventListener() {
    _liveListListener = LiveListListener(
      onLiveEnded: (liveID, reason, message) {
        // Listen to live streaming end
        debugPrint("Live ended. liveID: $liveID, reason: ${reason.value}, message: $message");
        // Handle the logic for exiting a live streaming room herein, such as closing current page
        // if (mounted) Navigator.of(context).pop();
      },
      onKickedOutOfLive: (liveID, reason, message) {
        // Listen to being kicked out of live stream
        debugPrint("Kicked out of live. liveID: $liveID, reason: ${reason.value}, message: $message");
        // Handle the logic for exiting a live streaming room herein
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
    // 5. Remove event listening
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

### Run and Test

After integrating `LiveCoreWidget`, you will obtain a pure video rendering view with complete live streaming business capability but no interactive UI. You can refer to the next chapter Enrich Live-Streaming Scenarios to refine live broadcasting scenarios.
|  | **Dynamic Grid Layout** | **Floating Window Layout** | **Fixed Grid Layout** |
| --- | --- | --- | --- |
| Template ID | 600 | 601 | 800 |
| Description | Default layout; grid size adjusts dynamically based on number of co-hosts. | Co-hosts are displayed as floating windows. | Fixed number of co-hosts; each occupies a fixed grid. |
| Example |  |  |  |

## Enrich Live Streaming Scenarios

Once the basic live streaming functionality is in place, refer to the following guides to add interactive features to your live stream:
| **Feature** | **Description** | **Store** | **Implementation Guide** |
| --- | --- | --- | --- |
| **Enable Audience Audio/Video Co-hosting** | Audience can apply to join the host and interact via real-time video. | CoGuestStore | Implementation Guide |
| **Enable Host Cross-room PK** | Hosts from different rooms can connect for interaction or PK. | CoHostStore / BattleStore | Implementation Guide |
| **Add Bullet Chat Feature** | Audience can send and receive real-time text messages in the live room. | BarrageStore | Implementation Guide |
| **Build a Gift Giving System** | Audience can send virtual gifts to the host, increasing engagement and fun. | GiftStore | Implementation Guide |

## API documentation
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **LiveCoreWidget** | The core view component for live video stream display and interaction: responsible for video stream rendering and view widget processing, supporting scenarios like live streaming, audience co-broadcasting, and anchor connection. |  |
| **LiveCoreController** | The controller of LiveCoreWidget: used to set up the live streaming ID, control the preview, and perform other operations. |  |
| **LiveListStore** | Full lifecycle management of live streaming room: create/join/leave/terminate room, query room list, modify live information (name, notice), listen to live status (for example, being kicked out, ended). |  |
| **DeviceStore** | Audio/Video device control: microphone (on/off, volume), camera (on/off, switchover, video quality), screen sharing, monitor device status in real time. |  |
| **CoGuestStore** | Audience co-broadcasting management: join microphone application/invite/grant/deny, attendee permission control (microphone/camera), state synchronization. |  |
| **CoHostStore** | Anchor cross-room connection: supports multiple layout templates (dynamic grid, etc.), initiate/accept/reject connection, and manage interactive co-broadcasting with Anchors. |  |
| **BattleStore** | Anchor PK battle: trigger PK (duration configuration/competitor), manage PK status (start/End), synchronize score, listen to battle results. |  |
| **GiftStore** | Gift interaction: get gift list, send/receive gifts, listen to gift events (including sender, gift details). |  |
| **BarrageStore** | Danmaku function: send text/custom barrage item, maintain bullet screen list, monitor bullet screen status in real time. |  |
| **LikeStore** | Like interaction: send likes, listen to like events, sync total likes. |  |
| **LiveAudienceStore** | Audience management: get real-time audience list (ID/name/avatar), check audience size, listen to Enter/Exit Event. |  |
| **AudioEffectStore** | Audio effects: voice-changing (child voice/male voice), reverberation (KTV), ear monitoring adjustment, switch effects in real time. |  |
| **BaseBeautyStore** | Basic beauty filter: adjust skin smoothing/whitening/rosy (0-100 levels), reset beauty effect status, synchronize effect parameters. |  |

## FAQs

### Why is the screen black with no video after the host calls createLive or the audience calls joinLive?

> **Platforms: Android / iOS / Flutter**

**Android：**

- **Check setLiveID**: Ensure you have set the correct liveID for the LiveCoreView instance before calling start or join methods.

- **Check device permissions**: Ensure the app has system permissions for camera and microphone usage.

- **Check the host side**: Has the host called `DeviceStore.shared().openLocalCamera(true)`to enable the camera?

- **Check the network**: Ensure the device has a stable network connection.

**iOS：**

- **Check **`setLiveID`: Ensure you have set the correct liveID for the LiveCoreView instance before calling start or join methods.

- **Check device permissions**: Ensure the app has system permissions for camera and microphone usage.

- **Check the host side**: Has the host called `DeviceStore.shared().openLocalCamera(true)`to enable the camera?

- **Check the network**: Ensure the device has a stable network connection.

**Flutter：**

- **Check setLiveID**: Please ensure the correct `liveID` has been set for the `LiveCoreController` instance before calling the live streaming or viewing API.

- **Check device permission**: Please ensure the app has obtained system usage permission for the camera and microphone.

- **Check anchor side**: Whether the Anchor side normally called `DeviceStore.shared.openLocalCamera(true)` to open the camera.

- **Check the network**: Please check whether the device network connection is normal.

### After the host enables the camera, why is there a local video preview after starting the stream, but a black screen before starting?

> **Platforms: Android / iOS**

- **Check the host side**: Make sure the host streaming view (LiveCoreView) has its `viewType` set to `PUSH_VIEW`.

- **Check the audience side**: Make sure the audience playback view (LiveCoreView) has its `viewType` set to `PLAY_VIEW`.

### What restrictions and rules apply to `updateLiveMetaData`?

> **Platforms: Android / iOS**

To maintain system stability and efficiency, metaData usage follows these rules:
- **Permissions**: Only hosts and administrators can call `updateLiveMetaData`. Regular audience members cannot.

- **Quantity and Size Limits:**

  - Each room supports up to **10** keys.

  - Each key can be up to **50 bytes**; each value up to **2KB**.

  - The total size of all values in a room cannot exceed **16KB**.

- **Conflict Resolution**: The update mechanism is "last write wins". If multiple administrators modify the same key in quick succession, the last change takes effect. Avoid concurrent modifications to the same key in your business logic.

### sync Fails to Copy Third-party Library Resources Due to Insufficient Permissions

> **Platforms: iOS**

For example, when integrating the SnapKit framework with Xcode, you may encounter the following build error:
``` plaintext
rsync(xxxx):1:1: SnapKit.framework/SnapKit_Privacy.bundle/: mkpathat: Operation not permitted
```

**Cause**

Xcode's user script sandboxing mechanism restricts the rsync script's write access to `SnapKit.framework` resource files during the build process, causing the `SnapKit_Privacy.bundle` in the framework to fail to copy properly.

Solution Steps
1. Disable user script sandboxing. Open your Xcode project, go to **Build Settings**, search for User Script Sandboxing (or `ENABLE_USER_SCRIPT_SANDBOXING`), and change its value from YES to NO.

2. Clean and rebuild the project. Run **Product → Clean** Build Folder to clear the project cache, then rebuild and run the project.

### How to Request Permission in a Flutter Project?

> **Platforms: Flutter**

You can use the `permission_handler` plug-in to request camera and mic permission:
``` java
import 'package:permission_handler/permission_handler.dart';

Future<void> requestPermissions() async {
  await [
    Permission.camera,
    Permission.microphone,
  ].request();
}
```

### How to Handle Page Lifecycle in Flutter?

> **Platforms: Flutter**

It is recommended to clean up resources in the `dispose` method, such as removing event listeners:
``` java
@override
void dispose() {
  LiveListStore.shared.removeLiveListListener(_liveListListener);
  super.dispose();
}
```
