> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

**AtomicXCore** provides two primary modules: CoHostStore and BattleStore, which handle cross-room co-hosting and PK battles respectively. This guide walks you through using both modules together to implement the complete workflow from co-hosting to PK in a live streaming scenario.

## Advanced Features
### Updating PK Score via REST API

In typical** live host PK scenarios,** the value of gifts received by the host is linked to the PK score (for example, when a viewer sends a "Rocket" gift, the host's PK score increases by 500 points). You can implement real-time PK score updates using our REST API.

> **Note：**
> 

> The PK score system in the `LiveKit` backend uses pure numeric calculation and accumulation. You must calculate the PK score according to your own business logic before calling the update API. See the following PK score calculation examples:
> 
| Gift Type | Score Calculation Rule | Example |
| --- | --- | --- |
| Basic Gift | Gift value × 5 | 10 RMB gift → 50 points |
| Intermediate Gift | Gift value × 8 | 50 RMB gift → 400 points |
| Advanced Gift | Gift value × 12 | 100 RMB gift → 1200 points |
| Special Effect Gift | Fixed high score | 520 RMB gift → 1314 points |

#### REST API Call Flow

#### Key Process Description
1. **Obtain PK Status:**

  - **Callback Configuration**: Configure PK Status Callback to have the `LiveKit` backend actively notify your system when PK starts or ends.

  - **Active Query**: Your backend can call the PK Status Query API at any time to check the current PK status.

2. **PK Score Calculation**: Your backend calculates the PK score increment based on your business rules.

3. **PK Score Update**: Your backend calls the Update PK Score API to update the PK score in the LiveKit backend.

4. **LiveKit Backend Syncs to Client**: The backend automatically synchronizes the updated PK score to all clients.

#### Involved REST API Endpoints
| **API** | **Function Description** | **Request Example** |
| --- | --- | --- |
| Active API - Query PK Status | Check whether the current room is in PK | Example |
| Active API - Update PK Score | Update the calculated PK score | Example |
| Callback Configuration - PK Start Callback | Receive real-time notification when PK starts | Example |
| Callback Configuration - PK End Callback | Receive real-time notification when PK ends | Example |

## Platform: Android

## Core Scenario

A typical "Host Co-hosting PK" session consists of three main stages, as shown below:
1. **Cross-room Co-hosting**: Two hosts connect, and both video streams are displayed in a shared view.

2. **Initiate PK**: After the connection is established, either host can start a PK challenge.

3. **PK Battle**: Both hosts compete in a PK battle, with scores updated in real time.

   

## Implementation

### Step 1: Component Integration

Refer to Quick Start to integrate AtomicXCore and complete the setup of LiveCoreView.

### Step 2: Implement Cross-Room Co-hosting

The goal of this step is to display the video streams of two hosts in the same view. Use CoHostStore to achieve this.

#### Inviter (Host A) Implementation
1. **Initiate Co-hosting Invitation**

   When Host A selects Host B in the UI and initiates a co-hosting request, call the `requestHostConnection` method.

   ``` kotlin
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.CoHostLayoutTemplate
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   
   // Host A's Activity
   class HostAActivity : AppCompatActivity() {
       private val liveId = "hostA_roomID" // Host A's Room ID
       private val coHostStore = CoHostStore.create(liveId)
   
       // User clicks the "Co-host" button and selects Host B
       fun inviteHostB(targetHostLiveId: String) {
           val layout = CoHostLayoutTemplate.HOST_DYNAMIC_GRID // Select a layout template
           val timeout = 30 // Invitation timeout (seconds)
   
           coHostStore.requestHostConnection(
               targetHostLiveID = targetHostLiveId,
               layoutTemplate = layout,
               timeout = timeout,
               extraInfo = null,
               completion = object : CompletionHandler {
                   override fun onSuccess() { 
                       println("Co-hosting invitation sent, waiting for response...")
                   }
   
                   override fun onFailure(code: Int, desc: String) { 
                       println("Invitation failed to send: $desc")
                   }
               }
           )
       }
   }
   ```
2. **Listen for Invitation Result**

   Receive Host B's response via the CoHostListener callback methods.

   ``` java
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoHostListener
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   import io.trtc.tuikit.atomicxcore.api.live.SeatUserInfo
   
   class HostAActivity : AppCompatActivity() {
       private val liveId = "hostA_roomID" // Host A's Room ID
       private val coHostStore = CoHostStore.create(liveId)
       private val coHostListener = object : CoHostListener() {
           override fun onCoHostRequestAccepted(invitee: SeatUserInfo) {
               println("Host ${invitee.userName} accepted your co-hosting invitation")
           }
   
           override fun onCoHostRequestRejected(invitee: SeatUserInfo) {
               println("Host ${invitee.userName} rejected your invitation")
           }
   
           override fun onCoHostRequestTimeout(inviter: SeatUserInfo, invitee: SeatUserInfo) { 
               println("Invitation timed out, no response received")
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // ... initialization code ...
           
           // Initialize CoHostStore
           coHostStore.addCoHostListener(coHostListener)
       }
   }
   ```

#### Invitee (Host B) Implementation
1. **Receive Co-hosting Invitation**

   Host B listens for invitations from Host A via `CoHostListener`.

   ``` kotlin
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoHostListener
   import io.trtc.tuikit.atomicxcore.api.live.CoHostStore
   import io.trtc.tuikit.atomicxcore.api.live.SeatUserInfo
   
   // Host B's Activity
   class HostBActivity : AppCompatActivity() {
       private val liveId = "hostB_roomID" // Host B's Room ID
       private val coHostStore = CoHostStore.create(liveId)
       
       private val coHostListener = object : CoHostListener() {
           override fun onCoHostRequestReceived(inviter: SeatUserInfo, extensionInfo: String) {
               println("Received co-hosting invitation from Host ${inviter.userName}")
               // Display invitation dialog to respond
               // showInvitationDialog(inviter)
           }
       }
   }
   ```
2. **Respond to Co-hosting Invitation**

   After Host B receives the invitation, prompt for “Accept” or “Reject” and call the appropriate method:

   ``` kotlin
   // Part of HostBActivity
   fun acceptInvitation(fromHostLiveId: String) {
       coHostStore.acceptHostConnection(fromHostLiveID = fromHostLiveId, completion = null)
   }
   
   fun rejectInvitation(fromHostLiveId: String) {
       coHostStore.rejectHostConnection(fromHostLiveID = fromHostLiveId, completion = null)
   }
   ```

### Step 3: Implement Host PK

After a successful co-hosting connection, either host can initiate a PK. Use BattleStore for PK functionality.

#### Challenger (e.g., Host A) Implementation
1. **Initiate PK Challenge**

   When Host A clicks the "PK" button, call the `requestBattle` method.

   ``` kotlin
   // Part of HostAActivity
   class HostAActivity : AppCompatActivity() {
       private val liveId = "hostA_roomID" // Host A's Room ID
       private val battleStore = BattleStore.create(liveId)
   
       fun startPK(opponentUserId: String) {
           val config = BattleConfig(duration = 300) // PK lasts 5 minutes
           battleStore.requestBattle(
               config = config,
               userIDList = listOf(opponentUserId),
               timeout = 30,
               completion = null
           )
       }
   }
   ```
2. **Listen for PK Status**

   Use `BattleListener` to monitor key events such as the start and end of PK.

   ``` java
   // Part of HostAActivity
   class HostAActivity : AppCompatActivity() {
       private val liveId = "hostA_roomID" // Host A's Room ID
       private val battleStore = BattleStore.create(liveId)
       
       private val battleListener = object : BattleListener() {
           override fun onBattleStarted(
               battleInfo: BattleInfo,
               inviter: SeatUserInfo,
               invitees: List<SeatUserInfo>
           ) {
               println("PK started")
           }
   
           override fun onBattleEnded(battleInfo: BattleInfo, reason: BattleEndedReason?) {
               println("PK ended")
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // ... other initialization code ...
           battleStore.addBattleListener(battleListener)
       }
   }
   ```

#### Opponent (Host B) Implementation
1. **Receive PK Challenge**

   Listen for PK invitations via `BattleListener`.

   ``` kotlin
   // Add to HostBActivity
   class HostBActivity : AppCompatActivity() {
       private val liveId = "hostB_roomID" // Host B's Room ID
       private val battleStore = BattleStore.create(liveId)
   
       private val battleListener = object : BattleListener() {
           override fun onBattleRequestReceived(battleID: String, inviter: SeatUserInfo, invitee: SeatUserInfo) {
               println("Received PK challenge from Host ${inviter.userName}")
               // Pop up a dialog for Host B to choose "Accept" or "Reject"
               // showPKChallengeDialog(battleID, inviter)
               }
           }
       
           override fun onCreate(savedInstanceState: Bundle?) {
               super.onCreate(savedInstanceState)
               // ... other initialization code ...
               battleStore.addBattleListener(battleListener)
           }
   }
   ```
2. **Respond to PK Challenge**

   Prompt Host B for “Accept” or “Reject” and call the corresponding method:

   ``` kotlin
   // Part of HostBActivity
   // User clicks "Accept Challenge"
   fun acceptPK(battleId: String) {
       battleStore.acceptBattle(battleID = battleId, completion = null)
   }
   
   // User clicks "Reject Challenge"
   fun rejectPK(battleId: String) {
       battleStore.rejectBattle(battleID = battleId, completion = null)
   }
   ```

### Step 4: End PK and Disconnect Co-hosting

After the PK session, end the PK and disconnect co-hosting in sequence.
1. **End PK Battle**

   PK typically ends automatically when the timer expires, but hosts can also end PK early. Use `exitBattle`:
   

   > **Note****：**After PK ends, both hosts remain connected (side-by-side video streams). Only the PK progress bar and score panel are removed.
   > 

   ``` java
   fun stopPK(battleId: String) {
       battleStore.exitBattle(battleID = battleId, completion = object : CompletionHandler {
           override fun onSuccess() {
               println("PK ended")
               // Handle UI refresh in onBattleEnded event
           }
           override fun onFailure(code: Int, desc: String) {
               println("Failed to end PK: $desc")
           }
       })
   }
   ```
2. **Disconnect Cross-room Co-hosting**

   To return to solo live streaming, call `exitHostConnection`:

   ``` java
     fun stopConnection() {
       coHostStore.exitHostConnection(completion = object : CompletionHandler {
           override fun onSuccess() {
               println("Co-hosting disconnected, back to solo live")
               // UI will receive onCoHostUserLeft event
           }
           override fun onFailure(code: Int, desc: String) {
               println("Failed to disconnect co-hosting: $desc")
           }
       })
   }
   ```
3. **Listen for End Events**

   Handle UI cleanup in event listeners for consistency:

   ``` java
     private val battleListener = object : BattleListener() {
       override fun onBattleEnded(battleInfo: BattleInfo, reason: BattleEndedReason?) {
           println("Received PK end event, reason: $reason")
           // Remove PK score view, progress bar, etc.
           // runOnUiThread { removeBattleUI() }
       }
   }
   
   private val coHostListener = object : CoHostListener() {
       override fun onCoHostUserLeft(userInfo: SeatUserInfo) {
           println("Host ${userInfo.userName} has left the co-hosting session")
           // Remove the other host's video view and restore solo live layout
           // runOnUiThread { resetToSingleStreamLayout() }
       }
   }
   ```

### Run and Test

After integrating the above features, use Host A and Host B to test the corresponding operations. The runtime effect is shown below. For UI customization, see Refine UI Details.

## Refine UI Details

Use the slot capability in the `VideoViewAdapter` interface to overlay custom views on video streams, such as nicknames, avatars, PK progress bars, or placeholder images when the host’s camera is off.

### Display Nicknames on Video Streams

#### Implementation Example

#### Implementation
- **Step 1**: Create a foreground view `CustomSeatView` to display user information above the video stream.
   

   > **Note：**
   > 

   > For a complete implementation, refer to the [widgets](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/cohost/widgets) directory in the open-source TUILiveKit project.
   > 

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.widget.LinearLayout
   import android.widget.TextView
   
   // Custom user information overlay view (Foreground)
   class CustomSeatView(context: Context) : LinearLayout(context) {
       private val nameLabel: TextView
   
       init {
           orientation = VERTICAL
           setBackgroundColor(Color.parseColor("#80000000")) // Semi-transparent black background
   
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
- **Step 2**: Create a background view `CustomAvatarView `to serve as a placeholder when the user has no video stream.

   ``` kotlin
   import com.tencent.cloud.tuikit.engine.room.TUIRoomDefine
   import android.view.View
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.ViewLayer
   import io.trtc.tuikit.atomicxcore.api.VideoViewAdapter
   
   // 1. In your Activity, implement the VideoViewAdapter interface
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       // ... Other code ...
   
       // 2. Fully implement the interface method to handle both view layers
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
                   // You can load the user's real avatar here using seatInfo.userAvatar
                   avatarView
               }
               null -> null
           }
       }
   }
   ```
- **Step 3**: Implement the `VideoViewAdapter.createCoHostView` method, returning the appropriate view based on `viewLayer`.

   ``` kotlin
   import com.tencent.cloud.tuikit.engine.room.TUIRoomDefine
   import android.view.View
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.ViewLayer
   import io.trtc.tuikit.atomicxcore.api.VideoViewAdapter
   
   // 1. In your Activity, implement the VideoViewAdapter interface
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       // ... Other code ...
   
       // 2. Fully implement the interface method to handle both view layers
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
                   // You can load the user's real avatar here using seatInfo.userAvatar
                   avatarView
               }
               null -> null
           }
       }
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatInfo` | `SeatFullInfo?` | Seat information object containing user details |
| `seatInfo.userId` | `String?` | User ID on the seat |
| `seatInfo.userName` | `String?` | User nickname on the seat |
| `seatInfo.userAvatar` | `String?` | User avatar URL |
| `seatInfo.userMicrophoneStatus` | `DeviceStatus` | User microphone status |
| `seatInfo.userCameraStatus` | `DeviceStatus` | User camera status |
| `viewLayer` | `ViewLayer` | View layer enum - `FOREGROUND`: Foreground widget view, always displayed on top of the video - `BACKGROUND`: Background widget view, under the foreground view, displayed only when the user has no video stream (e.g., camera off), typically used for the user's default avatar or a placeholder image |

### Displaying PK User Score on Video View

When the host starts a PK, you can attach a custom view to the opponent's video to display gift values or other PK-related information.

#### Implementation Example

#### Implementation
- **Step 1**: Create a custom PK user view. For a complete implementation, see [BattleMemberInfoView.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/battle/widgets/BattleMemberInfoView.kt)  in the open-source TUILiveKit project.

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.widget.LinearLayout
   import android.widget.TextView
   import com.tencent.cloud.tuikit.engine.extension.TUILiveBattleManager.BattleUser
   import io.trtc.tuikit.atomicxcore.api.BattleStore
   import kotlinx.coroutines.CoroutineScope
   import kotlinx.coroutines.Dispatchers
   import kotlinx.coroutines.launch
   
   // Custom PK User View
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
   
           // UI Layout
           setupUI()
           // Subscribe to score changes
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
   
       // Subscribe to PK score changes
       private fun subscribeBattleState() {
           CoroutineScope(Dispatchers.Main).launch {
               battleStore.battleState.battleScore.collect { battleScore ->
                   val score = battleScore[battleUser.userId] ?: 0
                   // Update UI
                   scoreLabel.text = score.toString()
               }
           }
       }
   }
   ```
- **Step 2**: Implement the `VideoViewAdapter.createBattleView `method.

   ``` kotlin
   // 1. Have your Activity implement the VideoViewAdapter interface
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       override fun createBattleView(battleUser: BattleUser?): View? {
           battleUser ?: return null
           // CustomBattleUserView is your custom PK user information view
           return CustomBattleUserView(this, liveId, battleUser)
       }
   
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `battleUser` | `BattleUser?` | PK user info object |
| `battleUser.roomId` | `String` | PK room ID |
| `battleUser.userId` | `String` | PK user ID |
| `battleUser.userName` | `String` | PK user nickname |
| `battleUser.avatarUrl` | `String` | PK user avatar URL |
| `battleUser.score` | `UInt` | PK score |

### Displaying PK Status on Video Stream

#### Implementation Example

#### Implementation
- **Step 1**: Create a custom PK global view (CustomBattleContainerView). For a complete implementation, see [BattleInfoView.kt](https://github.com/Tencent-RTC/TUIKit_Android/blob/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/battle/widgets/BattleInfoView.kt)  in the open-source TUILiveKit project.

- **Step 2**: Implement the `VideoViewAdapter.createBattleContainerView `method.

   ``` kotlin
   // Have your Activity implement the VideoViewAdapter interface and set the adapter
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
       override fun createBattleContainerView(): View? {
           return CustomBattleContainerView(this)
       }
   }
   ```

## API Documentation

For detailed information on all public interfaces, properties, and methods of CoHostStore  and related classes, refer to the official API documentation included with the AtomicXCore framework. The relevant stores used in this guide are as follows:
| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| `LiveCoreView` | Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. |  |
| `DeviceStore` | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. |  |
| `CoHostStore` | Handles host cross-room connections: supports multiple layout templates (dynamic grid, etc.), initiates/accepts/rejects connections, and manages co-host interactions. |  |
| `BattleStore` | Manages host PK battles: initiate PK (set duration/opponent), manage PK status (start/end), synchronize scores, and listen for battle results. |  |

## Platform: iOS

## Core Scenario

A typical "Host Co-hosting PK" session consists of three main stages, as shown below:
1. **Cross-room Co-hosting**: Two hosts connect, and both video streams are displayed in a shared view.

2. **Initiate PK**: After the connection is established, either host can start a PK challenge.

3. **PK Battle**: Both hosts compete in a PK battle, with scores updated in real time.

   

## Implementation

### Step 1: Component Integration

Refer to Quick Start to integrate AtomicXCore and complete the setup of LiveCoreView.

### Step 2: Implement Cross-Room Co-hosting

The goal of this step is to display the video streams of two hosts in the same view. Use CoHostStore to achieve this.

#### Inviter (Host A) Implementation
1. **Initiate Co-hosting Invitation**

   When Host A selects Host B in the UI and initiates a co-hosting request, call the `requestHostConnection` method.

   ``` swift
   import AtomicXCore
   import Combine
   
   // Host A's view controller
   class AnchorAViewController {
       private let liveId = "Host A's Room ID"
       private var cancellables: Set<AnyCancellable> = []
       private lazy var coHostStore: CoHostStore = {
           return CoHostStore.create(liveID: self.liveId)
       }()
   
       // User clicks the "Co-host" button and selects Host B
       func inviteHostB(targetHostLiveId: String) {
           let layout: CoHostLayoutTemplate = .hostDynamicGrid // Choose a layout template
           let timeout: TimeInterval = 30.0 // Invitation timeout
   
           coHostStore.requestHostConnection(targetHost: targetHostLiveId,
                                             layoutTemplate: layout,
                                             timeout: timeout) { result in
               switch result {
               case .success():
                   print("Co-hosting invitation sent, waiting for response...")
               case .failure(let error):
                   print("Failed to send invitation: \(error.message)")
               }
           }
       }
   }
   ```
2. **Listen for Invitation Result**

   Subscribe to `coHostEventPublisher` to receive Host B's response.

   ``` swift
   // Set up listener during AnchorAViewController initialization
   func setupListeners() {
       coHostStore.coHostEventPublisher
           .sink { [weak self] event in
               switch event {
               case .onCoHostRequestAccepted(let invitee):
                   print("Host \(invitee.userName) accepted your co-hosting invitation")
               case .onCoHostRequestRejected(let invitee):
                   print("Host \(invitee.userName) rejected your invitation")
               case .onCoHostRequestTimeout:
                   print("Invitation timed out, no response from the other party")
               default:
                   break
               }
           }
           .store(in: &cancellables)
   }
   ```

#### Invitee (Host B) Implementation
1. **Receive Co-hosting Invitation**

   Host B listens for invitations from Host A via `coHostEventPublisher`.

   ``` swift
   import AtomicXCore
   import Combine
   
   // Host B's view controller
   class AnchorBViewController {
       // ... coHostStore and cancellables initialization ...
   
       // Set up listener during initialization
       func setupListeners() {
           coHostStore.coHostEventPublisher
               .sink { [weak self] event in
                   if case let .onCoHostRequestReceived(inviter, _) = event {
                       print("Received co-hosting invitation from host \(inviter.userName)")
                       // self?.showInvitationDialog(from: inviter)
                   }
               }
               .store(in: &cancellables)
       }
   }
   ```
2. **Respond to Co-hosting Invitation**

   After Host B receives the invitation, prompt for “Accept” or “Reject” and call the appropriate method:

   ``` swift
   // Part of AnchorBViewController
   func acceptInvitation(fromHostLiveId: String) {
       coHostStore.acceptHostConnection(fromHostLiveID: fromHostLiveId, completion: nil)
   }
   
   func rejectInvitation(fromHostLiveId: String) {
       coHostStore.rejectHostConnection(fromHostLiveID: fromHostLiveId, completion: nil)
   }
   ```

### Step 3: Implement Host PK

After a successful co-hosting connection, either host can initiate a PK. Use BattleStore for PK functionality.

#### Challenger (e.g., Host A) Implementation
1. **Initiate PK Challenge**

   When Host A clicks the "PK" button, call the `requestBattle` method.

   ``` swift
   // Part of AnchorAViewController
   private lazy var battleStore: BattleStore = BattleStore.create(liveID: self.liveId)
   
   func startPK(with opponentUserId: String) {
       var config = BattleConfig(duration: 300) // PK lasts 5 minutes
       battleStore.requestBattle(config: config, userIDList: [opponentUserId], timeout: 30.0, completion: nil)
   }
   ```
2. **Listen for PK Status**

   Subscribe to `battleEventPublisher` to monitor key PK events such as start and end.

   ``` swift
   // Add to AnchorAViewController's setupListeners method
   battleStore.battleEventPublisher
       .sink { [weak self] event in
           switch event {
           case .onBattleStarted:
               print("PK started")
           case .onBattleEnded:
               print("PK ended")
           default:
               break
           }
       }
       .store(in: &cancellables)
   ```

#### Opponent (Host B) Implementation
1. **Receive PK Challenge**

   Listen for PK invitations via `battleEventPublisher`.

   ``` swift
   // Add to AnchorBViewController's setupListeners method
   battleStore.battleEventPublisher
       .sink { [weak self] event in
           if case let .onBattleRequestReceived(battleId, inviter, _) = event {
               print("Received PK challenge from host \(inviter.userName)")
               // Show dialog for Host B to choose "Accept" or "Reject"
               // self?.showPKChallengeDialog(battleId: battleId)
           }
       }
       .store(in: &cancellables)
   ```
2. **Respond to PK Challenge**

   Prompt Host B for “Accept” or “Reject” and call the corresponding method:

   ``` swift
   // Part of AnchorBViewController
   // User clicks "Accept Challenge"
   func acceptPK(battleId: String) {
       battleStore.acceptBattle(battleID: battleId) { result in
           // ...
       }
   }
   
   // User clicks "Reject Challenge"
   func rejectPK(battleId: String) {
       battleStore.rejectBattle(battleID: battleId) { result in
           // ...
       }
   }
   ```

### Step 4: End PK and Disconnect Co-hosting

After the PK session, end the PK and disconnect co-hosting in sequence.
1. **End PK Battle**

   PK typically ends automatically when the timer expires, but hosts can also end PK early. Use `exitBattle`:
   

   > **Note****：**After PK ends, both hosts remain connected (side-by-side video streams). Only the PK progress bar and score panel are removed.
   > 

   ``` swift
   func stopPK(battleId: String) {
       battleStore.exitBattle(battleID: battleId) { result in
           switch result {
           case .success:
               print("PK ended")
               // UI receives onBattleEnded event; refresh UI in callback
           case .failure(let error):
               print("Failed to end PK: \(error.message)")
           }
       }
   }
   ```
2. **Disconnect Cross-room Co-hosting**

   To return to solo live streaming, call `exitHostConnection`:

   ``` swift
   func stopConnection() {
       coHostStore.exitHostConnection { result in
           switch result {
           case .success:
               print("Co-hosting disconnected, back to solo live")
               // UI receives onCoHostUserLeft event
           case .failure(let error):
               print("Failed to disconnect co-hosting: \(error.message)")
           }
       }
   }
   ```
3. **Listen for End Events**

   Handle UI cleanup in event listeners for consistency:

   ``` swift
   func setupAdditionalListeners() {
       // Listen for PK end event
       battleStore.battleEventPublisher
           .sink { [weak self] event in
               if case .onBattleEnded(let battleInfo, let reason) = event {
                   print("Received PK end event, reason: \(reason)")
                   // self?.removeBattleUI()
               }
           }
           .store(in: &cancellables)
   
       // Listen for co-host disconnect event
       coHostStore.coHostEventPublisher
           .sink { [weak self] event in
               if case .onCoHostUserLeft(let userInfo) = event {
                   print("Anchor \(userInfo.userName) has left co-hosting")
                   // self?.resetToSingleStreamLayout()
               }
           }
           .store(in: &cancellables)
   }
   ```

### Run and Test

After integrating the above features, use Host A and Host B to test the corresponding operations. The runtime effect is shown below. For UI customization, see Refine UI Details.

## Refine UI Details

Use the slot capability in the `LiveCoreView.VideoViewDelegate` interface to overlay custom views on video streams, such as nicknames, avatars, PK progress bars, or placeholder images when the host’s camera is off.

### Display Nicknames on Video Streams

#### Implementation Example

#### Implementation
- **Step 1**: Create a foreground view `CustomSeatView` to display user information above the video stream.
   

   > **Note：**
   > 

   > You can also refer to the open-source TUILiveKit files [AnchorCoHostView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorCoHostView.swift) and [AnchorEmptySeatView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorEmptySeatView.swift) for complete implementation logic.
   > 

   ``` swift
   import UIKit
   import SnapKit
   
   // Custom floating user info view (foreground)
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
- **Step 2**: Create a background view `CustomAvatarView` to use as a placeholder when the user has no video stream.

   ``` swift
   import UIKit
   import SnapKit
   
   // Custom avatar placeholder view (background)
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
- **Step 3**: Implement the `VideoViewDelegate.createCoHostView` protocol method and return the appropriate view based on `viewLayer`.

   ``` swift
   import AtomicXCore
   import RTCRoomEngine
   
   // 1. In your view controller, conform to the VideoViewDelegate protocol
   class YourViewController: UIViewController, VideoViewDelegate {
   
       // ... other code ...
   
       // 2. Fully implement the protocol method, handling both viewLayer types
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
               // Optionally load the user's avatar using seatInfo.userAvatar
               return avatarView
           }
       }
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatInfo` | `TUISeatFullInfo` | Seat information object containing user details |
| `seatInfo.userId` | `String?` | User ID on the seat |
| `seatInfo.userName` | `String?` | User nickname on the seat |
| `seatInfo.userAvatar` | `String?` | User avatar URL |
| `seatInfo.userMicrophoneStatus` | `TUIDeviceStatus` | User microphone status |
| `seatInfo.userCameraStatus` | `TUIDeviceStatus` | User camera status |
| `viewLayer` | `ViewLayer` | View layer enum - `.foreground`: Foreground widget view, always displayed on top of the video - `.background`: Background widget view, under the foreground view, displayed only when the user has no video stream (e.g., camera off), typically used for the user's default avatar or a placeholder image |

### Displaying PK User Score on Video View

When the host starts a PK, you can attach a custom view to the opponent's video to display gift values or other PK-related information.

#### Implementation Example

#### Implementation
- **Step 1**: Create a custom PK user view. For a complete implementation, refer to [AnchorBattleMemberInfoView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/HostBattle/View/AnchorBattleMemberInfoView.swift).

   ``` swift
   import AtomicXCore
   import RTCRoomEngine
   import SnapKit
   
   // Custom PK user view
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
           setupUI()
           subscribeBattleState()
       }
   
       private func setupUI() {
           addSubview(scoreView)
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
   
       // Subscribe to PK score changes
       private func subscribeBattleState() {
           battleStore.state
               .subscribe(StatePublisherSelector(keyPath: \BattleState.battleScore))
               .removeDuplicates()
               .receive(on: RunLoop.main)
               .sink { battleScore in
                   guard let score = battleScore[self.userId] else { return }
                   self.scoreLabel.text = "\(score)"
               }
               .store(in: &cancellableSet)
       }
   }
   ```
- **Step 2**: Implement the `VideoViewDelegate.createBattleView` protocol.

   ``` swift
   // 1. Let your view controller conform to the VideoViewDelegate protocol
   extension YourViewController: VideoViewDelegate {
   
       public func createBattleView(battleUser: TUIBattleUser) -> UIView? {
           // CustomBattleUserView is your custom PK user info view
           let customView = CustomBattleUserView(liveId: liveId, battleUser: battleUser)
           return customView
       }
   
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `battleUser` | `TUIBattleUser` | PK user info object |
| `battleUser.roomId` | `String` | PK room ID |
| `battleUser.userId` | `String` | PK user ID |
| `battleUser.userName` | `String` | PK user nickname |
| `battleUser.avatarUrl` | `String` | PK user avatar URL |
| `battleUser.score` | `UInt` | PK score |

### Displaying PK Status on Video Stream

#### Implementation Example

#### Implementation
- **Step 1**: Create a custom PK global view (CustomBattleContainerView). For a complete implementation, refer to [AnchorBattleInfoView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/HostBattle/View/AnchorBattleInfoView.swift).

- **Step 2**: Implement the `VideoViewDelegate.createBattleContainerView` protocol.

   ``` swift
   // Let your view controller conform to the VideoViewDelegate protocol and set the delegate
   extension YourViewController: VideoViewDelegate {
       func createBattleContainerView() -> UIView? {
           return CustomBattleContainerView()
       }
   }
   ```

## API Documentation

For detailed information on all public interfaces, properties, and methods of CoHostStore and related classes, refer to the official API documentation included with the AtomicXCore framework. The relevant stores used in this guide are as follows:
| **Store/Component** | **Description** | **API Reference** |
| --- | --- | --- |
| `LiveCoreView` | Core view component for displaying and interacting with live video streams. Handles video rendering and view widgets, supports host streaming, audience co-hosting, host connections, and more. |  |
| `DeviceStore` | Controls audio/video devices: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, and real-time device status monitoring. |  |
| `CoHostStore` | Handles host cross-room connections: supports multiple layout templates (dynamic grid, etc.), initiates/accepts/rejects connections, and manages co-host interactions. |  |
| `BattleStore` | Manages host PK battles: initiate PK (set duration/opponent), manage PK status (start/end), synchronize scores, and listen for battle results. |  |

## Platform: Flutter

## Core Scenario

A complete "anchor connection PK" usually consists of three core stages, and the overall process is as follows:

## Implementation Steps

### Step 1: Integrating the Component

Refer to Quick Connection for seamless integration with **AtomicXCore** and complete **LiveCoreWidget** access.

### Step 2: Implementing Cross-Room Connection

The goal of this step is to have two Anchors appear in the same view, and we will use CoHostStore to complete it.

#### Inviter (Anchor A) Implementation
1. **Initiate connection invitation**

   When Anchor A selects target Anchor B on the interface and initiates connecting line, call the `requestHostConnection` method.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   // Anchor A's webpage
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
   
     // User clicks the "connecting line" button and selected Anchor B
     Future<void> inviteHostB(String targetHostLiveId) async {
       final layout = CoHostLayoutTemplate.hostDynamicGrid; // Select a layout template
       const timeout = 30; // Invitation timeout duration (seconds)
   
       final result = await _coHostStore.requestHostConnection(
         targetHostLiveID: targetHostLiveId,
         layoutTemplate: layout,
         timeout: timeout,
       );
   
       if (result.isSuccess) {
         print('Connection invitation sent, waiting for peer to handle...');
       } else {
         print('Invitation sending failure: ${result.errorMessage}');
       }
     }
   
     @override
     void dispose() {
       _coHostStore.removeCoHostListener(_coHostListener);
       super.dispose();
     }
   }
   ```
2. **Listen for invitation result**

   Through `CoHostListener`, you can receive the Anchor B's processing result.

   ``` java
   // Set up monitoring when initializing _AnchorAPageState
   void _setupListeners() {
     _coHostListener = CoHostListener(
       onCoHostRequestAccepted: (invitee) {
         print('Anchor ${invitee.userName} granted your connection invitation');
       },
       onCoHostRequestRejected: (invitee) {
         print('Anchor ${invitee.userName} refused your invitation');
       },
       onCoHostRequestTimeout: (inviter, invitee) {
         print('Invitation timeout, no response from the other party');
       },
       onCoHostUserJoined: (userInfo) {
         print('Anchor ${userInfo.userName} joined the connecting line');
       },
       onCoHostUserLeft: (userInfo) {
         print('Anchor ${userInfo.userName} left the connecting line');
       },
     );
     _coHostStore.addCoHostListener(_coHostListener);
   }
   ```

#### Invitee (Anchor B) Implementation
1. **Receive connection invitation**

   Through `CoHostListener`, Anchor B can listen to the invitation from Anchor A.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   // Anchor B's webpage
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
   
     // Set up monitoring during initialization
     void _setupListeners() {
       _coHostListener = CoHostListener(
         onCoHostRequestReceived: (inviter, extensionInfo) {
           print('Received connection invitation from Anchor ${inviter.userName}');
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
2. **Respond to connection invitation**

   When Anchor B makes a selection in the pop-up dialog box, call the method accordingly.

   ``` java
   // Part of _AnchorBPageState
   Future<void> acceptInvitation(String fromHostLiveId) async {
     final result = await _coHostStore.acceptHostConnection(fromHostLiveId);
     if (result.isSuccess) {
       print('Connection invitation accepted');
     } else {
       print('Connection acceptance failure: ${result.errorMessage}');
     }
   }
   
   Future<void> rejectInvitation(String fromHostLiveId) async {
     final result = await _coHostStore.rejectHostConnection(fromHostLiveId);
     if (result.isSuccess) {
       print('Connection invitation denied');
     } else {
       print('Connection rejection failure: ${result.errorMessage}');
     }
   }
   ```

### Step 3: Implement Anchor PK

Upon success of the connecting line, either party can initiate PK. This step will be achieved by using BattleStore to implement Anchor PK.

#### Challenger (Such As Anchor A) Implementation
1. **Initiate PK challenge**

   When Anchor A clicks the "PK" button, call the `requestBattle` method.

   ``` java
   // Part of _AnchorAPageState
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
     final config = BattleConfig(duration: 300); // PK lasts 5 minutes
     final result = await _battleStore.requestBattle(
       config: config,
       userIDList: [opponentUserId],
       timeout: 30,
     );
   
     if (result.isSuccess) {
       print('Battle request sent, battleID: ${result.battleID}');
     } else {
       print('PK request failure: ${result.errorMessage}');
     }
   }
   ```
2. **Listen for PK status**

   Listen for key events such as PK start and end via `BattleListener`.

   ``` java
   // Add to the _setupBattleListeners method in _AnchorAPageState
   void _setupBattleListeners() {
     _battleListener = BattleListener(
       onBattleStarted: (battleInfo, inviter, invitees) {
         print('PK started');
       },
       onBattleEnded: (battleInfo, reason) {
         print('PK ended, reason: $reason');
       },
       onUserJoinBattle: (battleID, battleUser) {
         print('User ${battleUser.userName} joined PK');
       },
       onUserExitBattle: (battleID, battleUser) {
         print('User ${battleUser.userName} left the PK');
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

#### Challenger (Anchor B) Implementation
1. **Accept PK challenge**

   Listen for PK invitation via `BattleListener`.

   ``` java
   // Add to the _setupBattleListeners method in _AnchorBPageState
   void _setupBattleListeners() {
     _battleListener = BattleListener(
       onBattleRequestReceived: (battleId, inviter, invitee) {
         print('Received PK challenge from Anchor ${inviter.userName}');
         // Display a pop-up dialog box for Anchor B to select "Agree" or "Reject"
         // _showPKChallengeDialog(battleId);
       },
       onBattleStarted: (battleInfo, inviter, invitees) {
         print('PK started');
       },
       onBattleEnded: (battleInfo, reason) {
         print('PK ended');
       },
     );
     _battleStore.addBattleListener(_battleListener);
   }
   ```
2. **Respond to PK challenge**

   When Anchor B makes a selection, call the appropriate method.

   ``` java
   // Part of _AnchorBPageState
   // User click "Accept Challenge"
   Future<void> acceptPK(String battleId) async {
     final result = await _battleStore.acceptBattle(battleId);
     if (result.isSuccess) {
       print('PK challenge accepted');
     } else {
       print('PK acceptance failure: ${result.errorMessage}');
     }
   }
   
   // User click "Reject Challenge"
   Future<void> rejectPK(String battleId) async {
     final result = await _battleStore.rejectBattle(battleId);
     if (result.isSuccess) {
       print('PK challenge denied');
     } else {
       print('PK rejection failure: ${result.errorMessage}');
     }
   }
   ```

### Execution Effect

After integrating the above feature implementation, perform the corresponding operation using Anchor A and Anchor B separately. The execution effect is as follows. You can refer to the next chapter Refine UI Detail to customize UI logic.

## Refine UI Detail

You can use the "slot" capacity provided by the `VideoWidgetBuilder` parameter of `LiveCoreWidget` to add custom view on top of the video stream image for displaying nickname, avatar, PK progress bar, etc., or provide placeholder image when they turn camera off to optimize visual experience.

### Implementing Nickname Display on Video Stream Image

#### Effect

#### Implementation Method
- **Step 1**: Create a foreground view (**CustomCoHostForegroundView**) to display user information above the video stream.
   

   > **Note:**
   > 

   > You can also refer to the [co_host_foreground_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/co_host/co_host_foreground_widget.dart) and [co_host_background_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/co_host/co_host_background_widget.dart) files in the TUILiveKit open-source project to understand the complete implementation logic.
   > 

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Custom connected host message floating view (foreground)
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
- **Step 2**: Create a background view (**CustomCoHostBackgroundView**) to display a placeholder image when there is no video stream from the user.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Custom connected anchor avatar placeholder view (background)
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
- **Step 3**: Build a custom view through the `VideoWidgetBuilder`'s `coHostWidgetBuilder` callback, and return the corresponding view based on the `viewLayer` value.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Live streaming page with custom connection view
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
   
     /// Build a custom view for the connected host
     Widget _buildCoHostWidget(
       BuildContext context, 
       SeatFullInfo seatFullInfo, 
       ViewLayer viewLayer,
     ) {
       if (viewLayer == ViewLayer.foreground) {
         // Foreground layer: Always display on top of the video image for Nickname, information, etc.
         return CustomCoHostForegroundView(seatInfo: seatFullInfo);
       } else {
         // Background layer: Display only when the corresponding user has no video stream, used for avatar placeholder image
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

#### **Parameter description:**
| **Parameter** | **Type** | **Note** |
| --- | --- | --- |
| `seatFullInfo` | `SeatFullInfo` | Seat information object, containing the Anchor's detailed information |
| `seatFullInfo.userInfo.userId` | `String` | Anchor's user ID |
| `seatFullInfo.userInfo.userName` | `String` | Anchor's Nickname |
| `seatFullInfo.userInfo.avatarUrl` | `String` | Anchor's profile photo URL |
| `viewLayer` | `ViewLayer` | View hierarchy enumeration `ViewLayer.foreground` refers to the pendant view in the foreground, always displayed on top of the video image. `ViewLayer.background` refers to the pendant view in the background, located in the lower layer of the foreground view. It is only displayed when the corresponding user has no video stream (such as when the camera is off), usually used for displaying the default avatar or placeholder image. |

### Implementing Score Display for PK User View

When the Anchor starts PK, you can mount a custom view on the other party's video footage, usually used for showing the value of gifts received or other PK-related information.

#### **Effect**

#### Implementation Method
- **Step 1**: Create a custom PK user view. You can refer to the [battle_member_info_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/battle/battle_member_info_widget.dart) file in the TUILiveKit open-source project to learn about the complete implementation logic.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Custom PK User View
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
   
     /// Subscribe to PK score changes
     void _subscribeBattleState() {
       _battleStore.battleState.battleScore.addListener(_scoreChangedListener);
       // Initialize the score
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
- **Step 2**: Build a custom PK view through the `battleWidgetBuilder` callback of `VideoWidgetBuilder`.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   Live streaming page with custom PK view
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
   
     /// Build Custom PK User View
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

#### **Parameter description:**
| **Parameter** | **Type** | **Note** |
| --- | --- | --- |
| `battleUser` | `TUIBattleUser` | PK user information object. |
| `battleUser.roomId` | `String` | PK room ID. |
| `battleUser.userId` | `String` | PK user ID. |
| `battleUser.userName` | `String` | PK user nickname. |
| `battleUser.avatarUrl` | `String` | PK user avatar address. |
| `battleUser.score` | `int` | PK score. |

### Displaying PK Status on Video Stream Image

#### **Effect**

#### **Implementation Method**
- **Step 1:** Create a custom PK global view **CustomBattleContainerView**. You can refer to the [battle_info_widget.dart](https://github.com/Tencent-RTC/TUIKit_Flutter/blob/main/live/livekit/lib/live_stream/features/decorations/battle/battle_info_widget.dart) file in the TUILiveKit open-source project to achieve the same effect.

- **Step 2**: Build a custom PK container view through the `battleContainerWidgetBuilder` callback of `VideoWidgetBuilder`.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   
   Live streaming page with custom PK container view
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
   
     /// Build PK Container View
     Widget _buildBattleContainerWidget(BuildContext context) {
       // CustomBattleContainerView is your custom PK global view
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

### Combine Multiple Custom Views

In actual scenarios, you may need to customize the connected host view, PK user view, and PK container view at the same time. The following example shows how to use them in combination.
``` java
import 'package:flutter/material.dart';
import 'package:atomic_x_core/atomicxcore.dart';
import 'package:rtc_room_engine/rtc_room_engine.dart';

/// Complete custom view live streaming page
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

  /// Build a custom view for the connected host
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

  /// Build Custom PK User View
  Widget _buildBattleWidget(BuildContext context, TUIBattleUser userInfo) {
    return CustomBattleUserView(
      liveId: widget.liveId,
      battleUser: userInfo,
    );
  }

  /// Build PK Container View
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

## API documentation

For detailed information about all public interfaces, attributes, and methods of [CoHostStore](https://pub.dev/documentation/atomic_x_core/latest/api_live_co_host_store/CoHostStore-class.html) and its related classes, see the official API documentation of the AtomicXCore framework. The related Store used in this guide is as follows:
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **LiveCoreWidget** | The core view component for live video stream display and interaction: responsible for stream rendering and view widget processing, supporting scenarios such as anchor live streaming, audience mic connection, and cross-room connection with other anchors. |  |
| **LiveCoreController** | The controller of LiveCoreWidget: are used to set the live stream ID, control the preview, and perform other operations. |  |
| **VideoWidgetBuilder** | Video view adapter: used for custom pendant view of video stream in scenarios such as connected host, PK user, and PK containers. |  |
| **DeviceStore** | Audio/Video device control: microphone (on/off, volume), camera (on/off, switchover, video quality), screen sharing, and real-time monitoring of device status. |  |
| **CoHostStore** | Anchor cross-room connection: supports multiple layout templates (dynamic grid, etc.), initiate/accept/reject connection, and manage interactive Anchor connections. |  |
| **BattleStore** | Anchor PK battle: trigger PK (duration configuration/competitor), manage PK status (started/ended), synchronize score, listen to battle results. |  |

## FAQs

### Why didn't the other party receive my co-hosting invitation?

> **Platforms: Android / iOS**

- Ensure that `targetHostLiveId` is correct and that the other host's live room is broadcasting normally.

- Check network stability. The invitation signaling has a default timeout of 30 seconds.

### What happens if one host disconnects from the network or the app crashes during co-hosting or PK?

> **Platforms: Android / iOS**

Both `CoHostStore` and `BattleStore` include built-in heartbeat and timeout detection. If one party exits abnormally, the other party is notified via events such as `onCoHostUserLeft` or `onUserExitBattle`. You can update the UI accordingly, for example, by displaying "The other party has disconnected" and ending the interaction.

### Why can PK scores only be updated via REST API?

> **Platforms: Android / iOS**

The REST API meets the security, real-time, and scalability requirements for PK scores:
- **Tamper-proof and fair**: Requires authentication and data validation. Each update is traceable (e.g., tied to a gift action), preventing manual score changes or cheating and ensuring fair competition.

- **Real-time synchronization across multiple clients**: Uses standardized formats (such as JSON) to quickly connect gift, PK, and display systems, ensuring real-time consistency of scores among hosts, viewers, and backend.

- **Flexible rule adaptation**: Backend configuration changes (such as adjusting gift-to-score mapping or bonus points) can be made without modifying the frontend, reducing iteration costs.

### How do I manage the lifecycle and events of custom views added via `VideoViewAdapter`?

> **Platforms: Android**

`LiveCoreView` automatically manages views returned by delegate methods, including adding and removing them. No manual lifecycle management is required.

### How do I manage the lifecycle and events of custom views added via `VideoViewDelegate`?

> **Platforms: iOS**

LiveCoreView automatically manages views returned by delegate methods, including adding and removing them. No manual lifecycle management is required.

### **Why Was the Connection Invitation Sent but Not Received by the Other Party?**

> **Platforms: Flutter**

- Please check whether `targetHostLiveId` is correct and the other party's live streaming room is in normal live streaming status.

- Check the network connection is connected. The invitation signaling has a default timeout duration of 30 seconds.

### What to Do When Network Disconnection or App Crash Occurs during Connecting Line or PK for an Anchor?

> **Platforms: Flutter**

**CoHostStore** and **BattleStore** have internal heartbeat and timeout detection mechanisms. If one party exits abnormally, the other will receive notifications through events such as **onCoHostUserLeft** or **onUserExitBattle**. Based on these events, you can handle the UI, for example, prompt "The other party has gone offline" and end the interaction.

### Why Can PK Score Only Be Updated Via REST API?

> **Platforms: Flutter**

Because REST API can satisfy both the **security, real-timeness, and scalability** requirements of PK scores at the same time:
- **Tamper-proof and fair**: Authentication + data validation is required. Each update can trace the source (such as gift actions) to prevent manual score changes or cheating, guaranteeing fair competitive gaming.

- **Multi-end real-time synchronization**: Use a standardized format (such as JSON) to quickly connect the gift, PK, and show systems, ensuring real-time consistency of scores for anchors, audience, and backend with no latency.

- **Flexible rule adaptation**: Just modify the backend configuration (such as adjusting the score corresponding to gifts or bonus points) to adapt to business changes, with no need to modify the frontend, reducing iteration cost.

### How to Manage the Lifecycle and Event of Custom Views Added Via `VideoWidgetBuilder`?

> **Platforms: Flutter**

**LiveCoreWidget** automatically manages the addition and removal of views returned by `coHostWidgetBuilder`, `battleWidgetBuilder`, and `battleContainerWidgetBuilder` callbacks, with no need for manual processing. If necessary to handle user interaction (such as click event) in custom views, just add the corresponding event when creating the view.
