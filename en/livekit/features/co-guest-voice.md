> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

AtomicXCore provides the CoGuestStore and LiveSeatStore modules, which are designed to streamline the entire audience co-hosting workflow for live streaming scenarios. You do not need to manage complex state synchronization or signaling logic—simply invoke a few intuitive methods to enable seamless audio and video interactions between hosts and audience members. This guide explains how to quickly implement voice co-hosting features in your Android app using `CoGuestStore` and `LiveSeatStore`.

## Advanced Features
Once a user is on mic, the host may need to manage mic seats. The following features are primarily provided by `LiveSeatStore`, which functions with `CoGuestStore`.

### On-Mic Users Control Their Own Microphone

On-mic users (including the host) can control their own microphone mute status via the `LiveSeatStore` interface.

#### Implementation
- **Mute:** Call `muteMicrophone()`. This is a one-way request with no callback.

- **Unmute:** Call `unmuteMicrophone(completion)`.

#### Example Code
``` java
import io.trtc.tuikit.atomicxcore.api.live.LiveSeatStore

val seatStore = LiveSeatStore.create(liveId)

seatStore.muteMicrophone() // Mute

seatStore.unmuteMicrophone(null) // Unmute
```

#### unmuteMicrophone Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `completion` | `CompletionHandler?` | Callback after operation completes. |

### Host Remotely Controls Mic Seat User’s Microphone

Hosts can forcibly mute or invite mic seat users to unmute.

#### Implementation
1. **Force Mute (Lock):** Hosts call `closeRemoteMicrophone` to mute and lock a user’s microphone. The user receives the `onLocalMicrophoneClosedByAdmin` event in `LiveSeatListener`, and their "Unmute Microphone" button should be disabled.

2. **Invite to Unmute (Unlock):** Hosts call `openRemoteMicrophone` to unlock mic permissions. The user receives the `onLocalMicrophoneOpenedByAdmin` event and can unmute themselves.

3. **User Unmutes Themselves:** After receiving an unlock notification, the user must call `unmuteMicrophone()` to resume audio transmission.

#### Example Code

##### Host Side
``` java
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.DeviceControlPolicy

val targetUserId = "userD"

// 1. Force mute and lock
seatStore.closeRemoteMicrophone(targetUserId, object : CompletionHandler {
    override fun onSuccess() {
        print("$targetUserId has been muted and locked")
    }
    override fun onFailure(code: Int, desc: String) {
        print("Operation failed: $desc")
    }
})

// 2. Unlock mic permissions (userD remains muted)
seatStore.openRemoteMicrophone(targetUserId, DeviceControlPolicy.UNLOCK_ONLY, object : CompletionHandler {
    override fun onSuccess() {
        print("Invited $targetUserId to unmute (unlocked)")
    }
    override fun onFailure(code: Int, desc: String) {
        print("Operation failed: $desc")
    }
})
```

##### Audience Side
``` java
import io.trtc.tuikit.atomicxcore.api.live.DeviceControlPolicy
import io.trtc.tuikit.atomicxcore.api.live.LiveSeatListener

// userD listens for host actions
val seatListener = object : LiveSeatListener() {
    override fun onLocalMicrophoneClosedByAdmin() {
        print("Muted by host")
    }

    override fun onLocalMicrophoneOpenedByAdmin(policy: DeviceControlPolicy) {
        print("Host has unlocked mute")
    }
}
seatStore.addLiveSeatEventListener(seatListener)
```

#### closeRemoteMicrophone Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `userID` | `String` | The user ID of the target user. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

#### openRemoteMicrophone Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `userID` | `String` | The user ID of the target user. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

### Host Removes On-Mic User from the Seat

#### Implementation
1. **Remove User from Mic Seat:** Hosts call `kickUserOutOfSeat` to forcibly remove a user from a mic seat.

2. **Event Notification:** The removed user receives the `onKickedOffSeat` event in `GuestListener`.

#### Example Code
``` java
// Remove "userB" from mic seat
val targetUserId = "userB"
seatStore.kickUserOutOfSeat(targetUserId, object : CompletionHandler {
    override fun onSuccess() {
        print("$targetUserId has been removed from the mic seat")
    }
    override fun onFailure(code: Int, desc: String) {
        print("Failed to remove user: $desc")
    }
})

// "userB" receives the event in GuestListener
override fun onKickedOffSeat(seatIndex: Int, hostUser: LiveUserInfo) {
    // Show notification
}
```

#### kickUserOutOfSeat Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `userID` | `String` | The user ID of the user to remove. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

### Host Locks and Unlocks Mic Seats

Hosts can lock or unlock specific mic seats.

#### Implementation
1. **Lock Mic Seat:** Call `lockSeat` to lock a mic seat at the specified index. Locked seats cannot be occupied via `applyForSeat` or `takeSeat`.

2. **Unlock Mic Seat:** Call `unlockSeat` to make the seat available again.

#### Example Code
``` java
// Lock mic seat 2
seatStore.lockSeat(2, object : CompletionHandler {
    override fun onSuccess() {
        print("Mic seat 2 locked")
    }
    override fun onFailure(code: Int, desc: String) {
        print("Failed to lock: $desc")
    }
})

// Unlock mic seat 2
seatStore.unlockSeat(2, object : CompletionHandler {
    override fun onSuccess() {
        print("Mic seat 2 unlocked")
    }
    override fun onFailure(code: Int, desc: String) {
        print("Failed to unlock: $desc")
    }
})
```

#### lockSeat Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatIndex` | `Int` | Index of the mic seat to lock. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

#### unlockSeat Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `seatIndex` | `Int` | Index of the mic seat to unlock. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

### Move Mic Seats

Hosts and mic seat users can call `moveUserToSeat` to move users between mic seats.

#### Implementation
1. **Host Moves User to Mic Seat:** The host can use this API to move any user to a specified mic seat. Provide the target user's `userID`, the target seat index as `targetIndex`, and use the `policy` parameter to specify the seat movement strategy if the target seat is occupied (see parameter details below).

2. **On-Mic User Moves Themselves:** On-mic users can also call this API to move themselves. In this case, `userID` must be the user's own ID, `targetIndex` is the desired new seat index, and the `policy` parameter is ignored. If the target seat is occupied, the move fails with an error.

#### Example Code
``` java
import io.trtc.tuikit.atomicxcore.api.CompletionHandler
import io.trtc.tuikit.atomicxcore.api.live.MoveSeatPolicy

seatStore.moveUserToSeat("userC",
    newSeatIndex,
    MoveSeatPolicy.ABORT_WHEN_OCCUPIED,
    object : CompletionHandler {
        override fun onSuccess() {
            print("Successfully moved to mic seat $newSeatIndex")
        }
        override fun onFailure(code: Int, desc: String) {
            print("Failed to switch seat, seat may be occupied: $desc")
        }
})
```

#### moveUserToSeat Parameters
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| `userID` | `String` | The user ID of the user to move. |
| `targetIndex` | `Int` | Target mic seat index. |
| `policy` | `MoveSeatPolicy?` | Seat movement policy if the target seat is occupied: - `abortWhenOccupied`: Abort move if seat is occupied (default) - `forceReplace`: Force replace the user on the target seat; replaced user will be removed - `swapPosition`: Swap positions with the user on the target seat. |
| `completion` | `CompletionHandler?` | Callback after request completes. |

## Platform: Android

## Core Scenarios

`CoGuestStore` and `LiveSeatStore` support the following primary live streaming scenarios:
- **Audience Request to Join Mic**: Audience members can request to join the mic; hosts can approve or reject these requests.

- **Host Invites Audience to Mic**: The host can proactively invite any viewer in the live room to join the mic.

- **Host Manages Mic Seats**: Hosts can manage mic seat users, including removing users, muting, and locking mic seats.

## Implementation

### Step 1: Component Integration

Refer to the Quick Start guide to integrate **AtomicXCore** into your project.

### Step 2: Implement Audience Mic-Link Request

#### Audience Side Implementation

As an audience member, your main tasks are **initiating a request, handling the response**, and **leaving the mic proactively**.
1. **Initiate Mic-Link Request**

   When the user taps the "Request to Co-host" button, call the `applyForSeat` method:

   ``` java
   import io.trtc.tuikit.atomicxcore.api.CompletionHandler
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   
   val liveId = "Room ID"
   val guestStore = CoGuestStore.create(liveId)
   
   // User taps "Request to Co-host"
   fun requestToConnect() {
       // timeout: request timeout, e.g., 30 seconds
       guestStore.applyForSeat(-1, 30, null, object : CompletionHandler {
           override fun onSuccess() {
               print("Co-hosting request sent, waiting for host to respond...")
           }
   
           override fun onFailure(code: Int, desc: String) {
               print("Failed to send request: $desc")
           }
       })
   }
   ```
2. **Listen for Host Response**

   Subscribe to `GuestListener` to handle the host’s approval or rejection:

   ``` java
   import io.trtc.tuikit.atomicxcore.api.device.DeviceStore
   import io.trtc.tuikit.atomicxcore.api.live.GuestListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   // Add listener during Activity/Fragment initialization
   fun subscribeGuestEvents() {
       val guestListener = object : GuestListener() {
           override fun onGuestApplicationResponded(isAccept: Boolean, hostUser: LiveUserInfo) {
               if (isAccept) {
                   print("Host ${hostUser.userName} approved your request. Preparing to join mic.")
                   // 1. Enable camera and microphone
                   DeviceStore.shared().openLocalCamera(true, null)
                   DeviceStore.shared().openLocalMicrophone(null)
                   // 2. Update UI, e.g., disable request button, show co-hosting status
               } else {
                   print("Host ${hostUser.userName} rejected your request.")
                   // Notify user of rejection
               }
           }
       }
       guestStore.addGuestListener(guestListener)
   }
   ```
3. **Leave Mic Proactively**

   To leave the mic seat and revert to audience status, call `disconnect`:

   ``` java
   // User taps "Leave Mic"
   fun leaveSeat() {
       guestStore.disconnect(object : CompletionHandler {
           override fun onSuccess() {
               print("Successfully left the mic")
           }
   
           override fun onFailure(code: Int, desc: String) {
               print("Failed to leave mic: $desc")
           }
       })
   }
   ```
4. **(Optional) Cancel Request**

   If the audience member wishes to withdraw their request before the host responds, call `cancelApplication`:

   ``` java
   // User taps "Cancel Request" while waiting
   fun cancelRequest() {
       guestStore.cancelApplication(object : CompletionHandler {
           override fun onSuccess() {
               print("Request cancelled")
           }
   
           override fun onFailure(code: Int, desc: String) {
               print("Failed to cancel request: $desc")
           }
       })
   }
   ```

#### Host Side Implementation

As the host, your main tasks are **receiving requests, displaying the request list**, and **handling requests**.
1. **Listen for New Mic-Link Requests**

   Subscribe to `HostListener` to receive notifications when an audience member requests to join the mic:

   ``` java
   import io.trtc.tuikit.atomicxcore.api.live.CoGuestStore
   import io.trtc.tuikit.atomicxcore.api.live.HostListener
   import io.trtc.tuikit.atomicxcore.api.live.LiveUserInfo
   
   val liveId = "Room ID"
   val guestStore = CoGuestStore.create(liveId)
   
   // Subscribe to host events
   val hostListener = object : HostListener() {
       override fun onGuestApplicationReceived(guestUser: LiveUserInfo) {
           print("Received co-hosting request from ${guestUser.userName}")
           // Update UI, e.g., show notification on "Request List" button
       }
       // ... Override other needed callback methods
   }
   guestStore.addHostListener(hostListener)
   ```
2. **Display Request List**

   Subscribe to the `applicants` property in `CoGuestStore`’s state to update the UI in real time:

   ``` java
   import kotlinx.coroutines.CoroutineScope
   import kotlinx.coroutines.Dispatchers
   import kotlinx.coroutines.launch
   
   // Subscribe to state changes
   fun observeApplicants() {
       CoroutineScope(Dispatchers.Main).launch {
           guestStore.coGuestState.applicants.collect { applicants ->
               print("Current number of applicants: ${applicants.size}")
               // Update "Applicant List" UI
           }
       }
   }
   ```
3. **Handle Mic-Link Requests**

   Approve or reject requests using the following methods:

   ``` java
   // Host taps "Approve"
   fun accept(userId: String) {
       guestStore.acceptApplication(
           userId,
           object : CompletionHandler {
               override fun onSuccess() {
                   print("Approved $userId's request. They are joining the mic.")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   print("Failed to approve request: $desc")
               }
           }
       )
   }
   
   // Host taps "Reject"
   fun reject(userId: String) {
       guestStore.rejectApplication(
           userId,
           object : CompletionHandler {
               override fun onSuccess() {
                   print("Rejected $userId's request")
               }
   
               override fun onFailure(code: Int, desc: String) {
                   print("Failed to reject request: $desc")
               }
           }
       )
   }
   ```

### Step 3: Implement Host-Initiated Mic-Link Invitation

#### Host Side Implementation
1. **Invite Audience Mic**

   To invite an audience member, call `inviteToSeat`:

   ``` java
   // Host selects audience and sends invitation
   fun invite(userId: String) {
       // timeout: invitation timeout
       guestStore.inviteToSeat(userId, -1, 30, null, object : CompletionHandler {
           override fun onSuccess() {
               print("Invitation sent to $userId, waiting for response...")
           }
   
           override fun onFailure(code: Int, desc: String) {
               print("Failed to send invitation: $desc")
           }
       })
   }
   ```
2. **Listen for Audience Response**

   Handle the audience member’s response via `HostListener`:

   ``` java
   // Add to hostListener implementation
   override fun onHostInvitationResponded(isAccept: Boolean, guestUser: LiveUserInfo) {
       if (isAccept) {
           print("Audience ${guestUser.userName} accepted your invitation")
       } else {
           print("Audience ${guestUser.userName} rejected your invitation")
       }
   }
   ```

#### Audience Side Implementation
1. **Receive Host Invitation**

   Listen for invitations via `GuestListener`:

   ``` java
   // Add to guestListener implementation
   override fun onHostInvitationReceived(hostUser: LiveUserInfo) {
       print("Received co-hosting invitation from host ${hostUser.userName}")
       // Show dialog for user to accept or reject
   }
   ```
2. **Respond to Invitation**

   Invoke the appropriate method based on the user’s choice:

   ``` java
   val inviterId = "Host ID who sent the invitation" // From onHostInvitationReceived
   
   // User taps "Accept"
   fun accept() {
       guestStore.acceptInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               // Enable microphone
               DeviceStore.shared().openLocalMicrophone(null)
           }
   
           override fun onFailure(code: Int, desc: String) {
               print("Failed to accept invitation: $desc")
           }
       })
   }
   
   // User taps "Reject"
   fun reject() {
       guestStore.rejectInvitation(inviterId, object : CompletionHandler {
           override fun onSuccess() {
               // ...
           }
           override fun onFailure(code: Int, desc: String) {
               // ...
           }
       })
   }
   ```

## API Documentation

For detailed information about all public interfaces, properties, and methods of CoGuestStore, LiveSeatStore, and related classes, refer to the official AtomicXCore framework API documentation. The relevant stores used in this guide are as follows:
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| CoGuestStore | Audience co-hosting management: co-hosting requests/invitations, approval/rejection, co-host member permission control (microphone/camera), state synchronization. |  |
| LiveSeatStore | Mic seat management: mute/unmute, lock/unlock mic seat, remove user from mic, remote microphone control, mic seat list state monitoring. |  |

## Platform: iOS

## Core Scenarios

`CoGuestStore` and `LiveSeatStore` support the following core live interaction scenarios:
- **Audience Request to Join Mic**: Viewers can actively request to join the mic; the host can approve or reject these requests.

- **Host Invites Audience to Mic**: The host can proactively invite any viewer in the live room to join the mic.

- **Host Manages Mic Seats**: The host can manage users on mic seats, including removing users, muting, and locking seats.

## Implementation

### Step 1: Component Integration

Refer to the Quick Start to integrate **AtomicXCore**.

### Step 2: Implement Audience Mic-Link Request

#### Audience Side Implementation

As an audience member, your main tasks are **initiating a request, handling the response**, and **leaving the mic proactively**.
1. **Initiate Mic-Link Request**

   When the user taps the "Request Mic-Link" button in the UI, call the `applyForSeat` method.

   ``` swift
   import AtomicXCore
   
   let liveId = "Room ID"
   var guestStore: CoGuestStore {
       CoGuestStore.create(liveID: liveID)
   }
   
   // User taps "Request Mic-Link"
   func requestToConnect() {
       // timeout: Request timeout, e.g., 30 seconds
       guestStore.applyForSeat(timeout: 30.0, extraInfo: nil) { result in
         switch result {
         case .success():
             print("Mic-link request sent, waiting for host response...")
         case .failure(let error):
             print("Failed to send request: \(error.message)")
         }
       }
   }
   ```
2. **Listen for Host Response**

   Subscribe to `guestEventPublisher` to receive the host’s response.

   ``` swift
   import AtomicXCore
   import Combine
   
   // Subscribe to events during view controller initialization
   func subscribeGuestEvents() {
       guestStore.guestEventPublisher
         .sink { [weak self] event in
             if case let .onGuestApplicationResponded(isAccept, hostUser) = event {
                 if isAccept {
                     print("Host \(hostUser.userName) accepted your request, preparing to join mic")
                     // 1. Open microphone
                     DeviceStore.shared.openLocalMicrophone(completion: nil)
                     // 2. Update UI, e.g., disable request button, show "on mic" status
                 } else {
                     print("Host \(hostUser.userName) rejected your request")
                     // Show popup to inform user of rejection
                 }
             }
         }
         .store(in: &cancellables) // Manage subscription lifecycle
   }
   ```
3. **Leave Mic Proactively**

   When an audience member on mic wants to end the interaction, call `disConnect` to return to regular audience status.

   ``` swift
   // User taps "Leave Mic" button
   func leaveSeat() {
       guestStore.disConnect { result in
         switch result {
         case .success():
             print("Successfully left mic")
         case .failure(let error):
             print("Failed to leave mic: \(error.message)")
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

#### Host Side Implementation

As the host, your main tasks are **receiving requests, displaying the request list**, and **handling requests**.
1. **Listen for New Mic-Link Requests**

   Subscribe to `hostEventPublisher` to be notified immediately when a new audience request arrives.

   ``` swift
   import AtomicXCore
   import Combine
   
   let liveId = "Room ID"
   var guestStore: CoGuestStore {
       CoGuestStore.create(liveID: liveID)
   }
   
   // Subscribe to host events
   guestStore.hostEventPublisher
       .sink { [weak self] event in
           if case let .onGuestApplicationReceived(guestUser) = event {
               print("Received mic-link request from audience \(guestUser.userName)")
               // Update UI, e.g., show a red dot on the "Request List" button
           }
       }
       .store(in: &cancellables)
   ```
2. **Display Request List**

   The `CoGuestStore` state maintains the current list of applicants in real time. Subscribe to it to refresh your UI.

   ``` swift
   import AtomicXCore
   import Combine
   
   // Subscribe to state changes
   guestStore.state
       .subscribe(StatePublisherSelector(keyPath: \CoGuestState.applicants)) // Only care about applicant list changes
       .removeDuplicates()
       .sink { applicants in
           print("Current number of applicants: \(applicants.count)")
           // Refresh your "Applicant List" UI here
           // self.applicantListView.update(with: applicants)
       }
       .store(in: &cancellables)
   ```
3. **Handle Mic-Link Requests**

   When you select an audience member from the list and tap "Accept" or "Reject", call the corresponding method.

   ``` swift
   // Host taps "Accept" button, passing applicant's userID
   func accept(userId: String) {
       guestStore.acceptApplication(userID: userId) { result in
           if case .success = result {
               print("Accepted \(userId)'s request, they are joining the mic")
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

### Step 3: Implement Host-Initiated Mic-Link Invitation

#### Host Side Implementation
1. **Invite Audience to Mic**

   When the host selects a viewer from the audience list and taps "Invite to Mic", call the `inviteToSeat` method.

   ``` swift
   // Host selects audience and initiates invitation
   func invite(userId: String) {
       // timeout: Invitation timeout
       guestStore.inviteToSeat(userID: userId, timeout: 30.0, extraInfo: nil) { result in
           if case .success = result {
               print("Invitation sent to \(userId), waiting for response...")
           }
       }
   }
   ```
2. **Listen for Audience Response**

   Listen for the `onHostInvitationResponded` event via `hostEventPublisher`.

   ``` swift
   // Add to hostEventPublisher subscription
   if case let .onHostInvitationResponded(isAccept, guestUser) = event {
       if isAccept {
           print("Audience \(guestUser.userName) accepted your invitation")
       } else {
           print("Audience \(guestUser.userName) rejected your invitation")
       }
   }
   ```

#### Audience Side Implementation
1. **Receive Host Invitation**

   Listen for the `onHostInvitationReceived` event via `guestEventPublisher`.

   ``` swift
   // Add to guestEventPublisher subscription
   if case let .onHostInvitationReceived(hostUser) = event {
       print("Received mic-link invitation from host \(hostUser.userName)")
       // Show a dialog for the user to choose "Accept" or "Reject"
       // self.showInvitationDialog(from: hostUser)
   }
   ```
2. **Respond to Invitation**

   When the user makes a choice in the dialog, call the corresponding method.

   ``` swift
   let inviterId = "Host ID who sent the invitation" // Get from onHostInvitationReceived event
   
   // User taps "Accept"
   func accept() {
       guestStore.acceptInvitation(inviterID: inviterId) { result in
           if case .success = result {
               // 2. Open microphone
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

## API Documentation

For detailed information about all public interfaces, properties, and methods of CoGuestStore, LiveSeatStore, and related classes, refer to the official AtomicXCore framework API documentation. The relevant stores used in this guide are as follows:
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| CoGuestStore | Audience mic-link management: mic-link requests/invitations/accept/reject, member permission control (microphone/camera), state synchronization. |  |
| LiveSeatStore | Mic seat management: mute/unmute, lock/unlock seats, remove users from mic, remote mic control, mic seat list state monitoring |  |

## Platform: Flutter

## Core Scenario

`CoGuestStore` and `LiveSeatStore` support the following mainstream scenarios:
- **Audience request to speak**: The audience proactively initiates a mic connection request, and the anchor can grant or reject it upon receiving the request.

- **Anchor invitation to speak**: The anchor can proactively initiate a mic connection invitation to any audience in the live streaming room.

- **Anchor seat management**: The anchor can perform operations such as forced microphone removal, muting, and locking on the user on the seat.

## Implementation Steps

### Step 1: Component Integration

Refer to quick integration to integrate **AtomicXCore**.

> **Note:**
> 

> Before implementing the co-anchoring function, you need to get the `liveId` (the unique identifier of the live streaming room) to distinguish different live streaming rooms. The method for obtaining it is as follows.
> - **Anchor**: Specify when creating a live stream via `LiveListStore.shared.createLive(liveInfo)`.
> - **Client**: Get it from `liveInfo` in `LiveListState.liveList.value`, or obtain it by the Anchor sharing the live streaming room ID.

### Step 2: Implement Audience Mic-Link Request

#### Audience Side Implementation

As an audience member, your main tasks are **initiating a request, handling the response**, and **leaving the mic proactively**.
1. **Initiate Mic-Link Request**

   When the user taps the "Request Mic-Link" button in the UI, call the `applyForSeat` method.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "room ID";
   late CoGuestStore guestStore;
   
   @override
   void initState() {
     super.initState();
     guestStore = CoGuestStore.create(liveId);
   }
   
   // User click "Apply for Mic Connection"
   Future<void> requestToConnect() async {
     // seatIndex: The seat index, starting from 0, represents the applied microphone position. 0 means the first seat. View seat status via LiveSeatState.seatList.value and select an idle seat.
     // timeout: Request timeout (seconds). The audience can call cancelApplication to cancel the request to speak within the timeout period.
     // The request will expire if it hasn't been processed after reaching the timeout period
     final result = await guestStore.applyForSeat(
       seatIndex: 0,
       timeout: 30,
       extraInfo: null,
     );
     if (result.isSuccess) {
       debugPrint('Mic connection request sent, waiting for anchor processing...');
     } else {
       debugPrint('Failed to send request: ${result.message}');
     }
   }
   ```
2. **Listen for Host Response**

   By adding `GuestListener`, you can receive the host's response

   ``` java
   late GuestListener _guestListener;
   
   // Subscribe to events when your Widget initializes
   void subscribeGuestEvents() {
     _guestListener = GuestListener(
       onGuestApplicationResponded: (isAccept, hostUser) {
         if (isAccept) {
           debugPrint('Anchor ${hostUser.userName} granted your request, preparing to join the mic');
           // 1. Turn on microphone
           // DeviceStore is the device management module provided by AtomicXCore, used for managing microphones, cameras and other physical devices.
           // When the host agrees to connect mic, turn on microphone to start collecting audio.
           DeviceStore.shared.openLocalMicrophone();
           // 2. Update UI herein, such as disabling the apply button and displaying the mic connection state
         } else {
           debugPrint('Anchor ${hostUser.userName} refused your request');
           // Pop-up prompts user that the application was rejected
         }
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
3. **Leave Mic Proactively**

   When an audience member on mic wants to end the interaction, call `disConnect` to return to regular audience status.

   ``` java
   // User click "Disconnect" button
   Future<void> leaveSeat() async {
     final result = await guestStore.disconnect();
     if (result.isSuccess) {
       debugPrint('Disconnected successfully');
     } else {
       debugPrint('Failed to leave the seat: ${result.message}');
     }
   }
   ```
4. **(Optional) Cancel Request**

   If the audience member wants to withdraw the request before the host responds, call `cancelApplication`.

   ``` java
   // While waiting, the user clicks "Cancel Application"
   Future<void> cancelApplication() async {
     final result = await guestStore.cancelApplication();
     if (result.isSuccess) {
       debugPrint('Request canceled');
     } else {
       debugPrint('Failed to cancel request: ${result.message}');
     }
   }
   ```

#### Host Side Implementation

As the host, your main tasks are **receiving requests, displaying the request list**, and **handling requests**.
1. **Listen for New Mic-Link Requests**

   By adding `HostListener`, you can receive immediate notification when a new audience applies and prompt.

   ``` java
   import 'package:atomic_x_core/atomicxcore.dart';
   
   final String liveId = "room ID";
   late CoGuestStore guestStore;
   late HostListener _hostListener;
   
   @override
   void initState() {
     super.initState();
     guestStore = CoGuestStore.create(liveId);
   
     // Subscribe to anchor event
     _hostListener = HostListener(
       onGuestApplicationReceived: (guestUser) {
         debugPrint('Received mic connection request from audience ${guestUser.userName}');
         // Update the UI here, for example display a red dot on the "application list" button
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
2. **Display Request List**

   `CoGuestStore`'s `coGuestState` maintains the current applicant list in real time. You can subscribe to it to update your UI.

   ``` java
   late final VoidCallback _applicantsListener = _onApplicantsChanged;
   
   // Subscribe to status change
   void subscribeApplicants() {
     guestStore.coGuestState.applicants.addListener(_applicantsListener);
   }
   
   void _onApplicantsChanged() {
     final applicants = guestStore.coGuestState.applicants.value;
     debugPrint('Current number of applicants: ${applicants.length}');
     // Update your "applicant list" UI here
   }
   
   @override
   void dispose() {
     guestStore.coGuestState.applicants.removeListener(_applicantsListener);
     super.dispose();
   }
   ```
3. **Handle Mic-Link Requests**

   When you select an audience member from the list and tap "Accept" or "Reject", call the corresponding method.

   ``` java
   // Anchor clicks the "Grant" button and imports the applicant's userID
   Future<void> accept(String userId) async {
     final result = await guestStore.acceptApplication(userId);
     if (result.isSuccess) {
       debugPrint('Approved $userId\'s request, the other party is joining the mic');
     }
   }
   
   // Anchor clicks the "Reject" button
   Future<void> reject(String userId) async {
     final result = await guestStore.rejectApplication(userId);
     if (result.isSuccess) {
       debugPrint('$userId\'s application denied');
     }
   }
   ```

### Step 3: Implement Host-Initiated Mic-Link Invitation

#### Host Side Implementation
1. **Invite Audience to Mic**

   When the host selects a viewer from the audience list and taps "Invite to Mic", call the `inviteToSeat` method.

   ``` java
   // Anchor selects audience and initiates invitation
   Future<void> invite(String userId) async {
     // inviteeID: The ID of the invitee, seatIndex: The seat index, timeout: The invitation timeout duration
     final result = await guestStore.inviteToSeat(
       inviteeID: userId,
       seatIndex: 0,
       timeout: 30,
       extraInfo: null,
     );
     if (result.isSuccess) {
       debugPrint('Sent invitation to $userId, waiting for peer response...');
     }
   }
   ```
2. **Listen for Audience Response**

   Listen to the `onHostInvitationResponded` event via `HostListener`.

   ``` java
   // Add to the HostListener configuration
   _hostListener = HostListener(
     onHostInvitationResponded: (isAccept, guestUser) {
       if (isAccept) {
         debugPrint('Audience ${guestUser.userName} accepted your invitation');
       } else {
         debugPrint('Audience ${guestUser.userName} refused your invitation');
       }
     },
   );
   ```

#### Audience Side Implementation
1. **Receive Host Invitation**

   Listen to the `onHostInvitationReceived` event via `GuestListener`.

   ``` java
   // Add to the GuestListener configuration
   _guestListener = GuestListener(
     onHostInvitationReceived: (hostUser) {
       debugPrint('Received mic connection invitation from Anchor ${hostUser.userName}');
       // Pop up a dialog box for user selection between "Accept" or "Reject"
       // showInvitationDialog(hostUser);
     },
   );
   ```
2. **Respond to invitation**

   After the user makes a selection in the pop-up dialog box, call the method accordingly.

   ``` java
   final String inviterId = "anchor ID who initiates invitation"; // obtain from onHostInvitationReceived event
   
   // User clicks "Accept"
   Future<void> accept() async {
     final result = await guestStore.acceptInvitation(inviterId);
     if (result.isSuccess) {
       // Turn the mic on
       // DeviceStore is the device management module provided by AtomicXCore, used for managing microphones, cameras and other physical devices.
       // After accepting the invitation, turn on microphone to start collecting audio
       DeviceStore.shared.openLocalMicrophone();
     }
   }
   
   // User click "Reject"
   Future<void> reject() async {
     await guestStore.rejectInvitation(inviterId);
   }
   ```

## API documentation

For detailed information about ALL public interfaces, attributes, and methods of CoGuestStore, LiveSeatStore, and its related classes, see the official API documentation of the AtomicXCore framework. The related Store used in this guide is as follows:
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **CoGuestStore** | Audience co-broadcasting management: join microphone application/invite/consent/deny, member permission control (microphone/camera), state synchronization. |  |
| **LiveSeatStore** | Seat management: mute/unmute, lock/unlock seats, remove speaker, remotely control Anchor microphone, listen to list status. |  |

## FAQs

### What are the differences between voice room co-hosting and video live co-hosting?

> **Platforms: Android**

The main differences are in business logic and UI design:
- `Video Live Streaming`**:** The video feed is central. Use `LiveCoreView` to render host and co-host video streams. The UI focuses on video layout, sizing, and overlays (such as nickname or placeholder images) via `VideoViewAdapter`. Both camera and microphone can be enabled.

- `Voice Room (Audio Chat Room)`**:** The mic seat grid is central. Do not use `LiveCoreView`; instead, build a grid UI (e.g., `RecyclerView`) based on `LiveSeatStore.state` (especially `seatList`). The UI displays each mic seat’s `SeatInfo`—occupied, muted, locked, or speaking—in real time. Only the microphone needs to be enabled.

### How do I update mic seat information (such as occupancy and mute status) in the UI in real time?

> **Platforms: Android**

Subscribe to the `seatList` property in `LiveSeatState`, which is a reactive `List<SeatInfo>`. When the array changes, update your mic seat UI. For each seat:
- Use `seatInfo.userInfo` for user details.

- Use `seatInfo.isLocked` to check if the seat is locked.

- Use `seatInfo.userInfo.microphoneStatus` for the user’s microphone status.

### What is the difference between microphone interfaces in LiveSeatStore and DeviceStore?

> **Platforms: Android**

`DeviceStore` manages the physical microphone device, while `LiveSeatStore` manages mic seat business logic (audio stream).

`DeviceStore:`
- `openLocalMicrophone`: Requests system permission and starts the microphone device for audio capture. This is a resource-intensive operation.

- `closeLocalMicrophone`: Stops audio capture and releases the microphone device.

   `LiveSeatStore:`

- `muteMicrophone`: Mutes the audio stream sent to remote users; the microphone device remains active.

- `unmuteMicrophone`: Unmutes the audio stream.

   **Recommended workflow:** "Open the device once, control mute/unmute while on mic"

1. **When joining mic:** When an audience member successfully joins the mic, call `openLocalMicrophone` once to start the device.

2. **While on mic:** All "mute" and "unmute" actions while on mic should use `muteMicrophone` and `unmuteMicrophone` to control the audio stream.

3. **When leaving mic:** When leaving mic (e.g., calling `disconnect`), call `closeLocalMicrophone` to release the device.

### What’s the difference between voice room mic-link and video live mic-link implementation?

> **Platforms: iOS**

The main differences are in business logic and UI presentation:
- `Video Live: `The core is the video feed. Use `LiveCoreView` as the main component to render video streams for the host and mic-linked viewers. The UI focuses on video layout and sizing, and you can add overlays (nickname, placeholder image) via `VideoViewDelegate`. Both camera and microphone can be enabled.

- `Voice Room:` The core is the mic seat grid. Do not use `LiveCoreView`; instead, build a grid UI (e.g., `UICollectionView`) based on the `LiveSeatStore`'s `state` (especially `seatList`). The UI focuses on real-time display of each seat’s `SeatInfo` status: occupied, muted, locked, and speaking status. Only the microphone needs to be enabled.

### How do I refresh mic seat info (e.g., occupied, muted) in the UI in real time?

> **Platforms: iOS**

Subscribe to the `seatList` property in `LiveSeatState`, which is a reactive `[SeatInfo]` array. Any changes will notify you to re-render the mic seat list. Iterate through this array to:
- Get user info on each seat via `seatInfo.userInfo`.

- Check if a seat is locked via `seatInfo.isLocked`.

- Check mic status of the user via `seatInfo.userInfo.microphoneStatus`.

### What’s the difference between microphone interfaces in LiveSeatStore and DeviceStore?

> **Platforms: iOS**

This is an important distinction. `DeviceStore` manages the physical device, while `LiveSeatStore` manages mic seat business logic (audio stream).

`DeviceStore`:
- `openLocalMicrophone`: Requests system permission and starts the microphone hardware for audio capture. This is a time-consuming operation.

- `closeLocalMicrophone`: Stops audio capture and releases the microphone device.

   `LiveSeatStore`:

- `muteMicrophone`: Mute. Stops sending local audio stream to remote, but the microphone hardware remains running.

- `unmuteMicrophone`: Unmute. Resumes sending audio stream to remote.

   **Recommended workflow: **"Open the device once, control mute/unmute while on mic"

1. **When joining mic:** When an audience member successfully joins the mic, call `openLocalMicrophone` once to start the device.

2. **While on mic:** All "mute" and "unmute" actions while on mic should use `muteMicrophone` and `unmuteMicrophone` to control the audio stream.

3. **When leaving mic:** When leaving mic (e.g., calling `disconnect`), call `closeLocalMicrophone` to release the device.

### What Is the Difference between Voice Room Co-Streaming and Live Streaming Co-Streaming

> **Platforms: Flutter**

The main difference between both lies in business form and UI display:
- Live streaming: The core is video footage. You will use `LiveCoreWidget` as the core component to render the video stream of the Anchor and mic-connecting audience. The UI focuses on processing the layout and size of video footage, as well as adding 3D stickers (such as Nickname and placeholder image) on the video stream through `VideoViewDelegate`. You can turn on the camera and microphone at the same time.

- Voice room (voice chat room): The core is the seat grid. You will not use `LiveCoreWidget`, but build a grid UI (such as `GridView`) based on `LiveSeatStore`'s `liveSeatState` (especially `seatList`). The UI focuses on displaying each seat `SeatInfo` state in real time: whether there is a person, whether it is muted, whether it is locked, and whether speaking is ongoing. You only need to turn on microphone.

### How to Real-Time Refresh Seat Information on UI (Such As Whether There Is a Person, Whether Muted)

> **Platforms: Flutter**

You should subscribe to the `seatList` property in `LiveSeatState`, which is a responsive data of `ValueListenable<List<SeatInfo>>` data type. It will notify you to re-render the microphone position list whenever the array changes. By traversing this array, you can:
- Get user information on the seat through `seatInfo.userInfo`.

- Check whether the seat is locked through `seatInfo.isLocked`.

- Check the mic status of the on-mic user through `seatInfo.userInfo.microphoneStatus`.
