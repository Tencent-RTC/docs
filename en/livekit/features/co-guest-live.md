> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

**AtomicXCore** provides the CoGuestStore module, purpose-built to manage the complete audience co-hosting workflow. You do not need to handle complex state synchronization or signaling logic—simply call a few intuitive methods to add robust audio and video interaction between viewers and hosts in your live stream.

## Core Features
`CoGuestStore` supports the two most common co-hosting scenarios:
- **Audience Request to Join**: An audience member initiates a co-hosting request, and the host can accept or reject it.

- **Host Invites to Join**: The host can invite any audience member in the live room to co-host.

## Core Concepts

| **概念** | **职责描述** | **Android** | **iOS** |
| --- | --- | --- | --- |
| `CoGuestStore` | - `state`: A StatePublisher containing `connected`, `applicants`, and `invitees` lists. - `applyForSeat()`: Viewer applies to become a co-guest. - `inviteToSeat()`: Host invites a viewer to take a seat. - `acceptApplication()`: Host accepts a link mic application. - `disConnect()`: Terminates the connection. | Manages the entire signaling workflow for viewer-host interaction (Apply, Invite, Accept, Reject, Disconnect) and maintains real-time state of related user lists. | Manages the entire signaling workflow for viewer-host interaction (Apply, Invite, Accept, Reject, Disconnect) and provides Combine publishers for event handling. |
| `HostListener` | - `onGuestApplicationReceived()`: Triggered when a viewer applies for a seat. - `onHostInvitationResponded()`: Triggered when a viewer responds to an invitation. | Used by the UI layer to respond to events such as "Received viewer application" or "Viewer accepted invitation". | — |
| `GuestListener` | - `onHostInvitationReceived()`: Triggered when the host sends an invitation. - `onGuestApplicationResponded()`: Triggered when the host processes an application (Accept/Reject). - `onKickedOffSeat()`: Triggered when kicked off the seat by the host. | Used by the UI layer to respond to events such as "Received host invitation", "Application approved", or "Kicked off seat". | — |
| `CoGuestState` | - `connected`: List of users currently co-hosting. - `applicants`: List of viewers currently applying. - `invitees`: List of viewers currently being invited. | — | Stores all user lists related to link mic, driving UI updates (e.g., notification badges, video window display). |
| `HostEvent / GuestEvent` | - `hostEventPublisher`: Publishes host-side events (e.g., onGuestApplicationReceived). - `guestEventPublisher`: Publishes audience-side events (e.g., onHostInvitationReceived). | — | Defines the signaling events received by the host and audience respectively, emitted via Publishers in the Store. |

## Platform: Android

## Implementation

### Step 1: Component Integration

Refer to Quick Start to integrate AtomicXCore and complete the setup of LiveCoreView.

### Step 2: Implement Audience Request to Join

#### Audience-Side Implementation

As an audience member, your primary tasks are to **initiate a request, handle the host's response, and leave the seat when desired.**
1. **Initiate a Co-hosting Request**

   When the user taps the "Request to Join" button, call the `applyForSeat` method.

   ``` kotlin
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   
   val liveId = "Room ID"
   val guestStore = CoGuestStore.create(liveId)
   
   // User clicks "Request to Join"
   fun requestToConnect() {
       // timeout: Request timeout in seconds, e.g., 30
       guestStore.applyForSeat(
           seatIndex = 0,
           timeout = 30,
           extraInfo = null,
           completion = object : CompletionHandler {
               override fun onSuccess() {
                   println("Co-hosting request sent, waiting for host response...")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   println("Failed to send request: $desc")
               }
           }
       )
   }
   ```
2. **Listen for Host Response**

   Add a `GuestListener` to receive the host's response.

   ``` kotlin
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.live.GuestListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   class YourActivity : AppCompatActivity() {
       val liveId = "Room ID"
       val guestStore = CoGuestStore.create(liveId)
       val deviceStore = DeviceStore.shared()
       private val guestListener = object : GuestListener() {
           override fun onGuestApplicationResponded(isAccept: Boolean, hostUser: LiveUserInfo) {
               if (isAccept) {
                   println("Host ${hostUser.userName} accepted your request. Preparing to join the seat.")
                   // Co-hosting request accepted; enable camera and microphone
                   DeviceStore.shared().openLocalCamera(true, completion = null)
                   DeviceStore.shared().openLocalMicrophone(completion = null)
                   // Update UI, e.g., disable request button and show co-hosting status
               } else {
                   println("Host ${hostUser.userName} rejected your request.")
                   // Show rejection notification
               }
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           guestStore.addGuestListener(guestListener)
       }
   
       override fun onDestroy() {
           super.onDestroy()
           guestStore.removeGuestListener(guestListener)
       }
   }
   ```
3. **Leave the Seat**

   When the co-hosting audience member wants to end the interaction, call `disconnect` to return to audience status.

   ``` kotlin
   // User clicks "Leave Seat"
   fun leaveSeat() {
       guestStore.disconnect(object : CompletionHandler {
           override fun onSuccess() {
               println("Successfully left the seat")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to leave seat: $desc")
           }
       })
   }
   ```
4. **(Optional) Cancel Request**

   To withdraw a pending request before the host responds, call `cancelApplication`.

   ``` kotlin
   // User clicks "Cancel Request"
   fun cancelRequest() {
       guestStore.cancelApplication(object : CompletionHandler {
           override fun onSuccess() {
               println("Request cancelled")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to cancel request: $desc")
           }
       })
   }
   ```

#### Host-Side Implementation

As a host, your main responsibilities are to **receive requests, display the applicant list, and process requests**.
1. **Listen for New Co-hosting Requests**

   Add a `HostListener` to be notified when a new audience request arrives.

   ``` kotlin
   import android.os.Bundle
   import androidx.appcompat.app.AppCompatActivity
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.live.HostListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   class YourActivity : AppCompatActivity() {
       val liveId = "Room ID"
       val guestStore = CoGuestStore.create(liveId)
       val hostListener = object : HostListener() {
           override fun onGuestApplicationReceived(guestUser: LiveUserInfo) {
               println("Received co-hosting request from ${guestUser.userName}")
               // Update UI, e.g., show a badge on the "Request List" button
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // Add listener
           guestStore.addHostListener(hostListener)
       }
   
       override fun onDestroy() {
           super.onDestroy()
           // Remove listener
           guestStore.removeHostListener(hostListener)
       }
   }
   ```
2. **Display Request List**

   `CoGuestStore` maintains the current list of applicants in real time. Subscribe to this list to update your UI.

   ``` kotlin
   class YourActivity : AppCompatActivity() {
       val liveId = "Room ID"
       val guestStore = CoGuestStore.create(liveId)
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
   
           // Subscribe to applicant list updates
           lifecycleScope.launch {
               guestStore.coGuestState.applicants.collect { applicants ->
                   println("Current number of applicants: ${applicants.size}")
                   // Update your "Applicant List" UI here
                   // updateApplicantListView(applicants)
               }
           }
       }
   }
   ```
3. **Process Co-hosting Requests**

   When you select an audience member and choose "Accept" or "Reject", call the corresponding method.

   ``` kotlin
   // Host taps "Accept" for a given userId
   fun accept(userId: String) {
       guestStore.acceptApplication(userId, object : CompletionHandler {
           override fun onSuccess() {
               println("Accepted $userId's request. They are joining the stage.")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to accept request: $desc")
           }
       })
   }
   
   // Host taps "Reject"
   fun reject(userId: String) {
       guestStore.rejectApplication(userId, object : CompletionHandler {
           override fun onSuccess() {
               println("Rejected $userId's request")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to reject request: $desc")
           }
       })
   }
   ```

### Step 3: Implement Host Inviting Audience to Join the Stage

#### Host-Side Implementation
1. **Send Invitation to Audience**

   When the host selects an audience member and taps "Invite to Co-host", call the `inviteToSeat` method.

   ``` kotlin
   // Host selects an audience member and sends an invitation
   fun invite(userId: String) {
       // timeout: Invitation timeout duration in seconds
       guestStore.inviteToSeat(
           inviteeID = userId,
           seatIndex = 0,
           timeout = 30,
           extraInfo = null,
           completion = object : CompletionHandler {
               override fun onSuccess() {
                   println("Invitation sent to $userId. Waiting for response...")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   println("Failed to send invitation: $desc")
               }
           }
       )
   }
   ```
2. **Listen for Audience Response**

   Listen for the `onHostInvitationResponded` event via `HostListener`.

   ``` kotlin
   // In HostListener implementation
   override fun onHostInvitationResponded(isAccept: Boolean, guestUser: LiveUserInfo) {
       if (isAccept) {
           println("Audience member ${guestUser.userName} accepted your invitation")
       } else {
           println("Audience member ${guestUser.userName} rejected your invitation")
       }
   }
   ```

#### Audience-Side Implementation
1. **Receive Host Invitation**

   Listen for the `onHostInvitationReceived` event via `GuestListener`.

   ``` kotlin
   // In GuestListener implementation
   override fun onHostInvitationReceived(hostUser: LiveUserInfo) {
       println("Received co-hosting invitation from host ${hostUser.userName}")
       // Show a dialog to let the user choose "Accept" or "Reject"
       // showInvitationDialog(hostUser)
   }
   ```
2. **Respond to Invitation**

   After the user makes a selection, call the corresponding method.

   ``` kotlin
   val inviterId = "Inviting Host ID" // Obtained from onHostInvitationReceived
   
   // User taps "Accept"
   fun accept() {
       guestStore.acceptInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               // Enable camera and microphone
               DeviceStore.shared().openLocalCamera(true, completion = null)
               DeviceStore.shared().openLocalMicrophone(completion = null)
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to accept invitation: $desc")
           }
       })
   }
   
   // User taps "Reject"
   fun reject() {
       guestStore.rejectInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               println("Invitation rejected")
           }
   
           override fun onFailure(code: Int, desc: String) {
               println("Failed to reject invitation: $desc")
           }
       })
   }
   ```

### Run and Test

After integrating the above features, use two audience members and a host to test co-hosting. For example, Audience A enables both camera and microphone, while Audience B enables only the microphone. The result is shown below. For UI customization, see Refining UI Details.

## Refining UI Details

Use the "slot" feature provided by the `VideoViewAdapter` interface to add custom views on top of audience co-hosting video streams. For example, display nicknames, avatars, or show a placeholder image when the camera is off to enhance the user experience.

### Displaying Nicknames on Video Streams

#### Implementation Example

#### Implementation

> **Note：**
> 

> You can also refer to the [widgets](https://github.com/Tencent-RTC/TUIKit_Android/tree/main/live/tuilivekit/src/main/java/com/trtc/uikit/livekit/features/anchorboardcast/view/coguest/widgets) directory in the open-source TUILiveKit project for a complete implementation.
> 

- **Step 1**: Create the foreground view `CustomSeatView` to display user info above the video stream.

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.view.ViewGroup
   import android.widget.LinearLayout
   import android.widget.TextView
   
   // Custom floating user info view (foreground)
   class CustomSeatView(context: Context) : LinearLayout(context) {
       private val nameLabel: TextView
   
       init {
           orientation = VERTICAL
           gravity = Gravity.BOTTOM or Gravity.START
           setBackgroundColor(Color.parseColor("#80000000")) // Semi-transparent black background
   
           nameLabel = TextView(context).apply {
               setTextColor(Color.WHITE)
               textSize = 14f
           }
   
           addView(nameLabel, LayoutParams(
               ViewGroup.LayoutParams.WRAP_CONTENT,
               ViewGroup.LayoutParams.WRAP_CONTENT
           ).apply {
               setMargins(20, 0, 0, 20) // Left margin 20, bottom margin 20
           })
       }
   
       fun setUserName(userName: String) {
           nameLabel.text = userName
       }
   }
   ```
- **Step 2**: Create the background view `CustomAvatarView` to serve as a placeholder when the user has no video stream.

   ``` kotlin
   import android.content.Context
   import android.graphics.Color
   import android.view.Gravity
   import android.view.ViewGroup
   import android.widget.ImageView
   import android.widget.LinearLayout
   
   // Custom avatar placeholder view (background)
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
           // Load user avatar here, e.g., using Glide
           // Glide.with(context).load(avatarUrl).into(avatarImageView)
       }
   }
   ```
- **Step 3**: Implement the `VideoViewAdapter.createCoGuestView` interface. Return the appropriate view based on the viewLayer value.

   ``` kotlin
   import android.os.Bundle
   import android.view.View
   import androidx.appcompat.app.AppCompatActivity
   import com.tencent.cloud.tuikit.engine.room.TUIRoomDefine
   import io.trtc.tuikit.atomicxcore.api.view.VideoViewAdapter
   import io.trtc.tuikit.atomicxcore.api.view.ViewLayer
   
   // Implement VideoViewAdapter in your Activity
   class YourActivity : AppCompatActivity(), VideoViewAdapter {
   
       // ... other code ...
   
       // Implement the interface method for both viewLayer types
       override fun createCoGuestView(userInfo: TUIRoomDefine.SeatFullInfo?, viewLayer: ViewLayer?): View? {
           userInfo ?: return null
           val userId = userInfo.userId
           if (userId.isNullOrEmpty()) return null
   
           return when (viewLayer) {
               ViewLayer.FOREGROUND -> {
                   // Show foreground view when camera is on
                   val seatView = CustomSeatView(this)
                   seatView.setUserName(userInfo.userName ?: "")
                   seatView
               }
               ViewLayer.BACKGROUND -> {
                   // Show background view when camera is off
                   val avatarView = CustomAvatarView(this)
                   userInfo.userAvatar?.let { avatarView.setUserAvatar(it) }
                   avatarView
               }
               else -> null
           }
       }
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           // Set the adapter
           liveCoreView.setVideoViewAdapter(this)
       }
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatInfo` | `SeatFullInfo?` | Seat information object, containing detailed information about the user on the seat |
| `seatInfo.userId` | `String` | ID of the user on the seat |
| `seatInfo.userName` | `String` | Nickname of the user on the seat |
| `seatInfo.userAvatar` | `String` | Avatar URL of the user on the seat |
| `seatInfo.userMicrophoneStatus` | `DeviceStatus` | Microphone status of the user on the seat |
| `seatInfo.userCameraStatus` | `DeviceStatus` | Camera status of the user on the seat |
| `viewLayer` | `ViewLayer` | View layer enum: - `FOREGROUND`: Foreground widget view, always displayed on top of the video - `BACKGROUND`: Background widget view, displayed below the foreground view, only shown when the user has no video stream (e.g., camera is off); typically used for the user's avatar or a placeholder image |

## API Documentation

For detailed information on all public interfaces, properties, and methods of CoGuestStore and related classes, refer to the official API documentation included with the AtomicXCore framework. The relevant stores used in this guide include:
| **Store/Component** | **Function Description** | **API Documentation** |
| --- | --- | --- |
| LiveCoreView | Core view component for live video stream display and interaction. Handles video rendering and widget management, supporting host streaming, audience co-hosting, host connections, and more. |  |
| DeviceStore | Audio and video device control: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, real-time device status monitoring. |  |
| CoGuestStore | Audience co-hosting management: request/invite/accept/reject co-hosting, permission control (microphone/camera), state synchronization. |  |

## Platform: iOS

## Implementation

### Step 1: Component Integration

Refer to Quick Start to integrate AtomicXCore and complete the setup of `LiveCoreView`.

### Step 2: Implement Audience Request to Join

#### Audience-Side Implementation

As an audience member, your primary tasks are to **initiate a request, handle the host's response, and leave the seat when desired.**
1. **Initiate a Co-hosting Request**

   When the user taps the "Request to Join" button, call the `applyForSeat` method.

   ``` swift
   import AtomicXCore
   
   let liveId = "Room ID"
   let guestStore = CoGuestStore.create(liveID: liveId)
   
   // User taps "Request to Co-host"
   func requestToConnect() {
       // timeout: Request timeout duration, e.g., 30 seconds
       guestStore.applyForSeat(timeout: 30.0, extraInfo: nil) { result in
         switch result {
         case .success():
             print("Co-hosting request sent, waiting for host response...")
         case .failure(let error):
             print("Failed to send request: \(error.message)")
         }
       }
   }
   ```
2. **Listen for Host Response**

   Subscribe to `guestEventPublisher` to receive the host's response.

   ``` swift
   // Subscribe to events during your view controller's initialization
   func subscribeGuestEvents() {
       guestStore.guestEventPublisher
         .sink { [weak self] event in
             if case let .onGuestApplicationResponded(isAccept, hostUser) = event {
                 if isAccept {
                     print("Host \(hostUser.userName) accepted your request, preparing to go live")
                     // 1. Enable camera and microphone
                     DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
                     DeviceStore.shared.openLocalMicrophone(completion: nil)
                     // 2. Update UI, e.g., disable the request button and show co-hosting status
                 } else {
                     print("Host \(hostUser.userName) rejected your request")
                     // Show a popup to notify the user that the request was rejected
                 }
             }
         }
         .store(in: &cancellables) // Manage subscription lifecycle
   }
   ```
3. **Leave the Seat**

   When a co-hosting audience member wants to end the interaction, call the `disConnect` method to return to audience status.

   ``` swift
   // User taps "Leave Mic" button
   func leaveSeat() {
       guestStore.disConnect { result in
         switch result {
         case .success():
             print("Successfully left the mic")
         case .failure(let error):
             print("Failed to leave the mic: \(error.message)")
         }
       }
   }
   ```
4. **(Optional) Cancel Request**

   If the audience member wants to withdraw the request before the host responds, call `cancelApplication`.

   ``` swift
   // User taps "Cancel Request" while waiting
   func cancelRequest() {
       guestStore.cancelApplication { result in
         switch result {
         case .success():
             print("Request cancelled")
         case .failure(let error):
             print("Failed to cancel request: \(error.message)")
         }
       }
   }
   ```

#### Host-Side Implementation

As a host, your main responsibilities are to **receive requests, display the applicant list, and process requests**.
1. **Listen for New Co-hosting Requests**

   Subscribe to `hostEventPublisher` to be notified when a new audience member requests to co-host.

   ``` swift
   import AtomicXCore
   
   let liveId = "Room ID"
   let guestStore = CoGuestStore.create(liveID: liveId)
   
   // Subscribe to host events
   guestStore.hostEventPublisher
       .sink { [weak self] event in
           if case let .onGuestApplicationReceived(guestUser) = event {
               print("Received co-hosting request from audience member \(guestUser.userName)")
               // Update UI, e.g., show a red dot on the "Request List" button
           }
       }
       .store(in: &cancellables)
   ```
2. **Display Request List**

   `CoGuestStore` maintains the current list of applicants in real time. Subscribe to this list to update your UI.

   ``` swift
   // Subscribe to state changes
   guestStore.state
       .subscribe(StatePublisherSelector(keyPath: \CoGuestState.applicants)) // Only observe changes to the applicant list
       .removeDuplicates()
       .sink { applicants in
           print("Current number of applicants: \(applicants.count)")
           // Refresh your "Applicant List" UI here
           // self.applicantListView.update(with: applicants)
       }
       .store(in: &cancellables)
   ```
3. **Process Co-hosting Requests**

   When you select an audience member and choose "Accept" or "Reject", call the corresponding method.

   ``` swift
   // Host taps "Accept" button, passing in the applicant's userID
   func accept(userId: String) {
       guestStore.acceptApplication(userID: userId) { result in
           if case .success = result {
               print("Accepted \(userId)'s request, they are joining as a co-host")
           }
       }
   }
   
   // Host taps "Reject" button
   func reject(userId: String) {
       guestStore.rejectApplication(userID: userId) { result in
           if case .success = result {
               print("Rejected \(userId)'s request")
           }
       }
   }
   ```

### Step 3: Implement Host Inviting Audience to Join the Stage

#### Host-Side Implementation
1. **Send Invitation to Audience**

   When the host selects an audience member and taps "Invite to Co-host", call the `inviteToSeat` method.

   ``` swift
   // Host selects an audience member and sends an invite
   func invite(userId: String) {
       // timeout: Invitation timeout duration
       guestStore.inviteToSeat(userID: userId, timeout: 30.0, extraInfo: nil) { result in
           if case .success = result {
               print("Invitation sent to \(userId), waiting for their response...")
           }
       }
   }
   ```
2. **Listen for Audience Response**

   Listen for the `onHostInvitationResponded` event via `hostEventPublisher`.

   ``` swift
   // Add this in the hostEventPublisher subscription
   if case let .onHostInvitationResponded(isAccept, guestUser) = event {
       if isAccept {
           print("Audience member \(guestUser.userName) accepted your invitation")
       } else {
           print("Audience member \(guestUser.userName) rejected your invitation")
       }
   }
   ```

#### Audience-Side Implementation
1. **Receive Host Invitation**

   Listen for the `onHostInvitationReceived` event via `guestEventPublisher`.

   ``` swift
   // Add this in the guestEventPublisher subscription
   if case let .onHostInvitationReceived(hostUser) = event {
       print("Received co-hosting invitation from host \(hostUser.userName)")
       // Show a dialog to let the user choose "Accept" or "Reject"
       // self.showInvitationDialog(from: hostUser)
   }
   ```
2. **Respond to Invitation**

   After the user makes a selection, call the corresponding method.

   ``` swift
   let inviterId = "Inviting host's ID" // Obtain from the onHostInvitationReceived event
   
   // User taps "Accept"
   func accept() {
       guestStore.acceptInvitation(inviterID: inviterId) { result in
           if case .success = result {
               // Enable camera and microphone
               DeviceStore.shared.openLocalCamera(isFront: true, completion: nil)
               DeviceStore.shared.openLocalMicrophone(completion: nil)
           }
       }
   }
   
   // User taps "Reject"
   func reject() {
       guestStore.rejectInvitation(inviterID: inviterId) { result in
           // ...
       }
   }
   ```

### Run and Test

After integrating the above features, use two audience members and a host to test co-hosting. For example, Audience A enables both camera and microphone, while Audience B enables only the microphone. The result is shown below. For UI customization, see Refining UI Details.

## Refining UI Details

Use the "slot" capability provided by the `LiveCoreView.VideoViewDelegate` protocol to add custom views on top of the co-host video stream. For example, display the user's nickname, avatar, or a placeholder image when the camera is off to enhance the visual experience.

### Displaying Nicknames on Video Streams

#### Implementation Example

#### Implementation Steps

> **Note：**
> 

> For complete implementation details, refer to the open-source TUILiveKit project files [AnchorCoGuestView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorCoGuestView.swift) and [AnchorEmptySeatView.swift](https://github.com/Tencent-RTC/TUIKit_iOS/blob/main/live/Sources/Features/AnchorBoardcast/View/LivingView/Video/AnchorEmptySeatView.swift).
> 

- **Step 1**: Create the foreground view `CustomSeatView` to display user info above the video stream.

   ``` swift
   import UIKit
   
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
- **Step 2**: Create the background view `CustomAvatarView` to serve as a placeholder when the user has no video stream.

   ``` swift
   import UIKit
   
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
- **Step 3**: Implement the `VideoViewDelegate.createCoGuestView` protocol method, returning the appropriate view based on the `viewLayer` value.

   ``` swift
   import AtomicXCore
   
   // 1. In your view controller, conform to the VideoViewDelegate protocol
   class YourViewController: UIViewController, VideoViewDelegate {
   
       // ... other code ...
   
       // 2. Implement the protocol method to handle both viewLayer types
       func createCoGuestView(seatInfo: TUISeatFullInfo, viewLayer: ViewLayer) -> UIView? {
           guard let userId = seatInfo.userID, !userId.isEmpty else {
               return nil
           }
           if viewLayer == .foreground {
               // When the user's camera is on, display the foreground view
               let seatView = CustomSeatView()
               seatView.nameLabel.text = seatInfo.userName
               return seatView
           } else { // viewLayer == .background
               // When the user's camera is off, display the background view
               let avatarView = CustomAvatarView()
               // Load the user's avatar here using seatInfo.userAvatar if available
               return avatarView
           }
       }
   }
   ```

#### Parameter Description:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatInfo` | `SeatFullInfo?` | Seat information object, containing detailed information about the user on the seat |
| `seatInfo.userId` | `String` | ID of the user on the seat |
| `seatInfo.userName` | `String` | Nickname of the user on the seat |
| `seatInfo.userAvatar` | `String` | Avatar URL of the user on the seat |
| `seatInfo.userMicrophoneStatus` | `DeviceStatus` | Microphone status of the user on the seat |
| `seatInfo.userCameraStatus` | `DeviceStatus` | Camera status of the user on the seat |
| `viewLayer` | `ViewLayer` | View layer enum: - `.foreground`: Foreground widget view, always displayed on top of the video - `.background`: Background widget view, displayed below the foreground view, only shown when the user has no video stream (e.g., camera is off); typically used for the user's avatar or a placeholder image |

## API Documentation

For detailed information on all public interfaces, properties, and methods of CoGuestStore and related classes, refer to the official API documentation included with the AtomicXCore framework. The relevant stores used in this guide include:
| **Store/Component** | **Function Description** | **API Documentation** |
| --- | --- | --- |
| LiveCoreView | Core view component for live video stream display and interaction. Handles video rendering and widget management, supporting host streaming, audience co-hosting, host connections, and more. |  |
| DeviceStore | Audio and video device control: microphone (on/off, volume), camera (on/off, switch, quality), screen sharing, real-time device status monitoring. |  |
| CoGuestStore | Audience co-hosting management: request/invite/accept/reject co-hosting, permission control (microphone/camera), state synchronization. |  |

## Platform: Flutter

## Core Scenario

`CoGuestStore` supports the following two most mainstream microphone-connected scenarios:
- **Audience request to speak**: The audience proactively initiate mic connection request, and the Anchor can grant or reject after receiving the request.

- **Anchor invitation to speak**: The Anchor can proactively initiate mic connection to any audience in the live streaming room.

## Implementation Steps

### Step 1: Integrating the Component

Refer to Quick Access for seamless integration with **AtomicXCore** and complete **LiveCoreWidget** access.

After integration, start implementing the co-anchoring function.

### Step 2: Implementing Audience Request to Speak

#### Client Implementation

As an audience member, your core task is to **initiate application, receive results** and **become a listener**.
1. **Initiate mic connection**

   When the user clicks the "apply for mic connection" button on the interface, call the `applyForSeat` method.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "room ID";
   final CoGuestStore guestStore = CoGuestStore.create(liveId);
   
   // User clicks "apply for mic connection"
   void requestToConnect() async {
       // seatIndex: The microphone slot index, value range is -1 (auto select an empty slot) and [0, maximum number of microphone slots in the current room -1], timeout: request timeout period (seconds)
       final result = await guestStore.applyForSeat(
           seatIndex: 0, 
           timeout: 30, 
           extraInfo: null,
       );
       if (result.isSuccess) {
           print("Mic connection request sent, wait for anchor processing...");
       } else {
           print("Failed to send request: ${result.errorMessage}");
       }
   }
   ```
2. **Listen to the anchor's response**

   By adding a listener through `addGuestListener`, you can receive the anchor's processing result.

   ``` java
   late GuestListener _guestListener;
   
   // Subscribe to events when your Widget is initialized
   void subscribeGuestEvents() {
       _guestListener = GuestListener(
           onApplicationAccepted: (hostUser) {
               print("Anchor ${hostUser.userName} agreed to your request, preparing to connect mic");
               // 1. Turn on the camera, microphone 
               DeviceStore.shared.openLocalCamera(isFront: true);
               DeviceStore.shared.openLocalMicrophone();
               // 2. Update the UI here, such as disabling the apply button and displaying the mic connection state
           },
           onApplicationRejected: (hostUser) {
               print("Anchor ${hostUser.userName} refused your request");
               // Pop-up prompts user that the request was rejected
           },
           onInvitationReceived: (hostUser) {
               print("Received mic connection invitation from Anchor ${hostUser.userName}");
               // Pop up a dialog box herein for users to choose "Accept" or "Reject"
               _showInvitationDialog(hostUser);
           },
           onKickedOffSeat: () {
               print("You have been kicked off the mic by Anchor");
               // Update UI, turn off the camera and microphone
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
3. **Leave the mic**

   When a mic-connecting audience wants to end the interaction, call the `disconnect` method to return to general audience status.

   ``` java
   // User clicks "leave mic" button
   void leaveSeat() async {
       final result = await guestStore.disconnect();
       if (result.isSuccess) {
           print("Left mic successfully");
       } else {
           print("Failed to leave mic: ${result.errorMessage}");
       }
   }
   ```
4. **(Optional) Cancel application**

   If the audience wants to withdraw the application before anchor processing, they can call the API `cancelApplication`.

   ``` java
   // While waiting, the user clicks "Cancel Application"
   void cancelRequest() async {
       final result = await guestStore.cancelApplication();
       if (result.isSuccess) {
           print("Application canceled");
       } else {
           print("Failed to cancel request: ${result.errorMessage}");
       }
   }
   ```

#### Anchor Implementation

As an Anchor, your core task is to **receive applications, show the Application List** and **process applications**.
1. **Listen for new mic connection requests**

   By adding a listener through `addHostListener`, you can receive immediate notification when a new audience applies and get prompted.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "room ID";
   final CoGuestStore guestStore = CoGuestStore.create(liveId);
   late HostListener _hostListener;
   late final VoidCallback _applicantsChangedListener = _onApplicantsChanged;
   
   // Subscribe to anchor event
   void subscribeHostEvents() {
       _hostListener = HostListener(
           onApplicationReceived: (guestUser) {
               print("Received join microphone application from audience ${guestUser.userName}");
               // Update UI herein, for example display a red dot on the "application list" button
           },
           onInvitationAccepted: (guestUser) {
               print("Audience ${guestUser.userName} accepted your invitation");
           },
           onInvitationRejected: (guestUser) {
               print("Audience ${guestUser.userName} refused your invite");
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
2. **Show application list**

   `CoGuestStore`'s `coGuestState` maintains the current applicant list in real time. You can listen to it to update your UI.

   ``` java
   // Subscription status update
   void subscribeApplicants() {
       guestStore.coGuestState.applicants.addListener(_applicantsChangedListener);
   }
   
   void _onApplicantsChanged() {
       final applicants = guestStore.coGuestState.applicants.value;
       print("Current number of applicants: ${applicants.length}");
       // Refresh your "applicant list" UI herein
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
3. **Handle mic connection request**

   When you select an audience from the list and click "Grant" or "Reject", call the appropriate method.

   ``` java
   // Anchor clicks the "Grant" button and imports the applicant's userID
   void accept(String userId) async {
       final result = await guestStore.acceptApplication(userId);
       if (result.isSuccess) {
           print("Approved $userId's request, the other party is connecting mic");
       }
   }
   
   // Anchor clicked the "Reject" button
   void reject(String userId) async {
       final result = await guestStore.rejectApplication(userId);
       if (result.isSuccess) {
           print("Denied $userId's request");
       }
   }
   ```

### Step 3: Implementing Anchor Invitation to Speak

#### Anchor Implementation
1. **Initiate invitation to audience**

   When the Anchor selects someone from the audience list and clicks "invite to mic connection", call the `inviteToSeat` method.

   ``` java
   // Anchor selects audience and initiates invitation
   void invite(String userId) async {
       // inviteeID: The invitee ID, seatIndex: The microphone slot index, value range is -1 (auto select an empty slot) and [0, maximum number of microphone slots in the current room -1] 
       // timeout: invitation timeout duration
       final result = await guestStore.inviteToSeat(
           inviteeID: userId,
           seatIndex: 0,
           timeout: 30, 
           extraInfo: null,
       );
       if (result.isSuccess) {
           print("Invitation sent to $userId, waiting for peer response...");
       }
   }
   ```
2. **Listen to the audience response**

   Listen to the invitation event response via `HostListener`.

   ``` java
   // Callback configured in HostListener
   _hostListener = HostListener(
       onInvitationAccepted: (guestUser) {
           print("Audience ${guestUser.userName} accepted your invitation");
       },
       onInvitationRejected: (guestUser) {
           print("Audience ${guestUser.userName} refused your invite");
       },
   );
   ```

#### Client Implementation
1. **Receive the anchor's invitation**

   Listen to the invitation event via `GuestListener`.

   ``` java
   // Callback configured in GuestListener
   _guestListener = GuestListener(
       onInvitationReceived: (hostUser) {
           print("Received mic connection invitation from Anchor ${hostUser.userName}");
           // Pop up a dialog box herein for users to choose "Accept" or "Reject"
           _showInvitationDialog(hostUser);
       },
   );
   ```
2. **Respond to invitation**

   When the user makes a selection in the pop-up dialog box, call the appropriate method.

   ``` java
   String inviterId = "Anchor ID initiating invitation"; // Obtain from onInvitationReceived callback
   
   // User click "Accept"
   void acceptInvitation() async {
       final result = await guestStore.acceptInvitation(inviterId);
       if (result.isSuccess) {
           // Turn on the camera, microphone  
           DeviceStore.shared.openLocalCamera(isFront: true);
           DeviceStore.shared.openLocalMicrophone();
       }
   }
   
   // User click "Reject"
   void rejectInvitation() async {
       await guestStore.rejectInvitation(inviterId);
   }
   ```

### Execution Effect

After integrating the above feature implementation, perform a co-streaming operation using two audiences and an Anchor separately. Audience A turns on the camera and microphone simultaneously, while Audience B turns on microphone only. The execution effect is as follows. You can refer to the next chapter Refine UI Details to customize your desired UI logic.

## Refine UI Details

You can use the "slot" capacity provided by the `VideoWidgetBuilder` parameter of `LiveCoreWidget` to add custom views on the video stream image of audience co-broadcasting, for displaying Nickname, avatar information, etc., or offer placeholder images when they turn camera off, to optimize visual experience.

### Implementing Nickname Display on Video Stream Image

#### **Effect**

#### **Implementation Method**
- **Step 1**: Create a foreground view (**CustomSeatForegroundView**) used for displaying user information above the video stream.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Custom user information floating view (foreground)
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
- **Step 2**: Create a background view (**CustomSeatBackgroundView**) used as a placeholder image when the user has no video stream.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Custom avatar placeholder view (background)
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
- **Step 3**: Build a custom view through the `VideoWidgetBuilder`'s `coGuestWidgetBuilder` callback, and return the corresponding view based on the `viewLayer` value.

   ``` java
   import 'package:flutter/material.dart';
   import 'package:atomic_x_core/atomicxcore.dart';
   import 'package:rtc_room_engine/rtc_room_engine.dart';
   
   /// Live streaming page with custom connection view
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
   
       /// Build a custom view for the mic-connecting audience
       Widget _buildCoGuestWidget(
           BuildContext context, 
           SeatFullInfo seatFullInfo, 
           ViewLayer viewLayer,
       ) {
           if (viewLayer == ViewLayer.foreground) {
               // Foreground layer: Always display on top of the video image, used for Nickname, information, etc.
               return CustomSeatForegroundView(seatInfo: seatFullInfo);
           } else {
               // Background layer: Displayed only when the corresponding user has no video stream, used for displaying avatar placeholder images
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

#### **Parameter description:**
| **Parameter** | **Type** | **Note** |
| --- | --- | --- |
| `seatFullInfo` | `SeatFullInfo` | Seat information object, containing the Anchor's detailed information. |
| `seatFullInfo.userInfo.userId` | `String` | Anchor's user ID. |
| `seatFullInfo.userInfo.userName` | `String` | On-mic user's nickname. |
| `seatFullInfo.userInfo.avatarUrl` | `String` | On-mic user's profile photo URL. |
| `viewLayer` | `ViewLayer` | View hierarchy enumeration. - `ViewLayer.foreground` represents the pendant view, always displayed on top of the video image. - `ViewLayer.background` represents the background pendant view, located in the lower layer of the foreground view. It is displayed only when the corresponding user has no video stream (for example, when the camera is not turned on), usually used for displaying the default avatar or placeholder image of the user. |

## API documentation

For details about ALL public interfaces, properties, and methods of CoGuestStore and its related classes, see the official API documentation of the AtomicXCore framework. The related Stores used in this guide are as follows:
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| LiveCoreWidget | Core view component for live video stream display and interaction: Responsible for stream rendering and view widget processing, supporting scenarios such as Anchor live streaming, audience co-broadcasting, and cross-room connection with other anchors. |  |
| LiveCoreController | Controller for LiveCoreWidget: Used to set the live stream ID, control the preview, and perform other operations. |  |
| VideoWidgetBuilder | Video view adapter: Used for custom pendant views in scenarios such as mic-connecting audience, cross-room connection with other anchors, and PK. |  |
| DeviceStore | Audio/Video device control: Microphone (on/off, volume), camera (on/off, switchover, video quality), screen sharing, and real-time monitoring of device status. |  |
| CoGuestStore | Audience co-broadcasting management: Join microphone application/invite/grant/reject, attendee permission control (microphone/camera), state synchronization. |  |

## FAQs

### How do I manage the lifecycle and events of custom views added via `VideoViewAdapter`?

> **Platforms: Android**

LiveCoreView automatically manages the addition and removal of views returned by your adapter methods. You do not need to handle this manually. To support user interactions (such as click events) in your custom view, add the appropriate event listeners when creating the view.

### What is the purpose of the viewLayer parameter in VideoViewAdapter?

> **Platforms: Android / iOS**

The viewLayer parameter distinguishes between foreground and background widgets:
- `FOREGROUND`: Foreground layer, always displayed on top of the video.

- `BACKGROUND`: Background layer, displayed only when the user has no video stream (e.g., camera is off); typically used to show the user's avatar or a placeholder image.

### Why is my custom view not showing up?

> **Platforms: Android / iOS**

> **Android：** - **Check adapter settings**: Ensure you have called liveCoreView.setVideoViewAdapter(this) and set the adapter successfully.
> **iOS：** - **Check adapter settings**: Ensure you have called `coreView.videoViewDelegate = self` and set the adapter successfully.

- **Check implementation method**: Confirm that you have correctly implemented the relevant adapter method (such as `createCoGuestView`).

> **Android：** - **Check return value**: Make sure your adapter method returns a valid View instance at the appropriate time, not null. Add logs in the adapter method for debugging if needed.
> **iOS：** - **Check return value**: Make sure your adapter method returns a valid `UIView` instance at the appropriate time, not null. Add logs in the adapter method for debugging if needed.

### How do I manage the lifecycle and events of custom views added via `VideoViewDelegate`?

> **Platforms: iOS**

`LiveCoreView` automatically manages the addition and removal of views returned by your adapter methods. You do not need to handle this manually. To support user interactions (such as click events) in your custom view, add the appropriate event listeners when creating the view.

### How to Manage the Lifecycle and Event of a Custom View Added Via `VideoWidgetBuilder`?

> **Platforms: Flutter**

**LiveCoreWidget** will automatically manage the addition and removal of views returned by the `coGuestWidgetBuilder` callback, so you don't need to handle them manually. If necessary to process user interaction (such as click events) in a custom view, just add corresponding event handling when creating the view.

### `ViewLayer` What Are the Functions of the Parameter?

> **Platforms: Flutter**

`ViewLayer` is used to distinguish between foreground and background widgets.
- `ViewLayer.foreground`: The foreground layer, always displayed on top of the video image.

- `ViewLayer.background`: The background layer, displayed only when the corresponding user has no video stream (for example, when the camera is not turned on), usually used for displaying the default avatar or placeholder image of the user.

### Why Is My Custom View Not Displayed?

> **Platforms: Flutter**

- **Check VideoWidgetBuilder settings**: Please confirm the `LiveCoreWidget`'s `videoWidgetBuilder` parameter is correctly set.

- **Check callback implementation**: Please check if the `coGuestWidgetBuilder` callback function is correctly implemented.

- **Check the return value**: Ensure your callback function returns an effective `Widget` instance.
