> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

`LiveAudienceStore` is a module in `AtomicXCore` designed for managing audience information in live streaming rooms. With `LiveAudienceStore`, you can implement a comprehensive audience list and management system for your live streaming application.

## Core Features
- **Real-Time Audience List**: Retrieve and display all audience information currently present in the live room.

- **Audience Count Statistics**: Access the accurate, real-time total number of viewers in the live room.

- **Audience Activity Monitoring**: Subscribe to events to instantly detect when viewers join or leave.

- **Administrator Privileges**: The host can promote regular viewers to administrators or revoke their admin status.

- **Room Management**: The host or administrator can remove (kick out) any regular viewer from the live room.

## Core Concepts

| **概念** | **职责描述** | **Android** | **iOS** | **Flutter** |
| --- | --- | --- | --- | --- |
| `LiveUserInfo` | - Data model representing a viewer. - Contains basic profile information such as userID, userName, and avatarURL. | data class | `class` | `class` |
| `LiveAudienceState` | - State container for the audience module. - Exposes `audienceList` (StateFlow) for the real-time user list and `audienceCount` for the total viewer count. | data class | `struct` | `class` |
| `LiveAudienceListener` | Represents real-time audience activity events. It includes `onAudienceJoined` (audience joins) and `onAudienceLeft` (audience leaves), used for incremental updates to the audience list. | abstract class | — | `class` |
| `LiveAudienceStore` | - Primary interface for audience management. - Used to fetch list snapshots, perform admin operations (e.g., kicking users), and subscribe to real-time events. | abstract class | `class` | `class` |
| `LiveAudienceEvent` | - Event listener for audience changes. - Provides callbacks like `.onAudienceJoined` and` .onAudienceLeft` to handle incremental list updates. | — | `enum` | — |

## Advanced Features
### Display a Welcome Message in the Barrage Area When a Viewer Joins

When a new viewer enters the live room, a welcome message such as `"Welcome [user nickname] to the live room"` is automatically displayed locally in the barrage/chat area.

#### Implementation

Add a listener for the audience join event `LiveAudienceListener.onAudienceJoined` in `LiveAudienceStore` to receive real-time notifications when a new viewer joins. When triggered, extract the new viewer's nickname and call the `appendLocalTip` method of BarrageStore to insert the message locally.
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
        // Initialize two core managers
        setupWelcomeMessageFlow()
    }

    private fun setupWelcomeMessageFlow() {
        // 1. Get an instance of LiveAudienceStore
        val audienceStore = LiveAudienceStore.create(liveId)

        // 2. Get an instance of BarrageStore (this will be the same instance due to internal mechanisms)
        val barrageStore = BarrageStore.create(liveId)

        // 3. Subscribe to audience events
        audienceStore.addLiveAudienceListener(object : LiveAudienceListener() {
            override fun onAudienceJoined(audience: LiveUserInfo) {
                // 4. Create a local tip message
                val welcomeTip = Barrage().apply {
                    liveID = liveId
                    messageType = BarrageType.TEXT
                    textContent = "Welcome ${audience.userName} to the live room!"
                }

                // 5. Use BarrageStore's method to insert it into the barrage list
                barrageStore.appendLocalTip(welcomeTip)
            }
        })
    }
}
```

## Platform: Android

## Implementation Guide

### Step 1: Component Integration
- **Video Live Streaming**: Please refer to Quick Start to integrate AtomicXCore

- **Voice Chat Room**: Please refer to Quick Start to integrate AtomicXCore.

### Step 2: Initialize and Retrieve the Audience List

Obtain a `LiveAudienceStore` instance bound to the current live room's `liveId`, and fetch the current audience list for the initial display.
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

    // Expose the [full] audience list state flow for UI layer subscription
    private val _audienceList = MutableStateFlow<List<LiveUserInfo>>(emptyList())
    val audienceList: StateFlow<List<LiveUserInfo>> = _audienceList.asStateFlow()
    
    // Expose the audience count state flow
    private val _audienceCount = MutableStateFlow(0)
    val audienceCount: StateFlow<Int> = _audienceCount.asStateFlow()
    
    init {
        // 1. Subscribe to state and events, see next section for implementation
        subscribeToAudienceState()
        subscribeToAudienceEvents()

        // 2. Actively fetch initial data for the first screen
        fetchInitialAudienceList()
    }

    /// Actively fetch a snapshot of the audience list once
    private fun fetchInitialAudienceList() {
        audienceStore.fetchAudienceList(object : CompletionHandler {
            override fun onSuccess() {
                println("Successfully fetched the initial audience list")
                // After success, the data will be automatically updated via the state subscription channel below
            }

            override fun onFailure(code: Int, desc: String) {
                println("Failed to fetch the initial audience list: $desc")
            }
        })
    }
    
    // ... subsequent code
}
```

### Step 3: Listen to Audience List State and Real-Time Activity

Subscribe to the `liveAudienceState` of `audienceStore` and add a `LiveAudienceListener` to receive full list snapshots and real-time audience join/leave events. Use these updates to drive UI changes.
``` kotlin
class AudienceManager {
    /// Subscribe to state for audience count and list snapshots
    private fun subscribeToAudienceState() {
        scope.launch {
            // audienceList is a full snapshot
            audienceStore.liveAudienceState.audienceList.collect { audienceList ->
                _audienceList.value = audienceList
            }
        }

        scope.launch {   
            // audienceCount is the real-time total
            audienceStore.liveAudienceState.audienceCount.collect { count ->
                _audienceCount.value = count
            }
        }
    }

    /// Subscribe to events to handle real-time audience join/leave
    private fun subscribeToAudienceEvents() {
        audienceStore.addLiveAudienceListener(object : LiveAudienceListener() {
            override fun onAudienceJoined(newAudience: LiveUserInfo) {
                println("Audience ${newAudience.userName} joined the live room")
                // Incremental update: add the new audience member to the end of the current list
                val currentList = _audienceList.value.toMutableList()
                if (!currentList.any { it.userID == newAudience.userID }) {
                    currentList.add(newAudience)
                    _audienceList.value = currentList
                }
            }

            override fun onAudienceLeft(departedAudience: LiveUserInfo) {
                println("Audience ${departedAudience.userName} left the live room")
                // Incremental update: remove the departed audience member from the current list
                val currentList = _audienceList.value.toMutableList()
                currentList.removeAll { it.userID == departedAudience.userID }
                _audienceList.value = currentList
            }
        })
    }
}
```

### Step 4: Manage Audience (Kick and Set Administrator)

As a host or administrator, you can manage viewers in the live room.

#### 4.1 Kick a Viewer Out of the Live Room

Call the `kickUserOutOfRoom` method to remove a specified user from the live room.
``` kotlin
class AudienceManager {
    fun kick(userId: String) {
        audienceStore.kickUserOutOfRoom(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("Successfully kicked user $userId out of the room")
                // After a successful kick, you will receive an onAudienceLeft event
            }

            override fun onFailure(code: Int, desc: String) {
                println("Failed to kick user $userId: $desc")
            }
        })
    }
}
```

#### 4.2 Set or Revoke Administrator Status

Use the `setAdministrator` and `revokeAdministrator` methods to manage a user's administrator status.
``` kotlin
class AudienceManager {
    /// Promote user to administrator
    fun promoteToAdmin(userId: String) {
        audienceStore.setAdministrator(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("Successfully promoted user $userId to administrator")
            }
            
            override fun onFailure(code: Int, desc: String) {
                println("Failed to set administrator: $desc")
            }
        })
    }

    /// Revoke user's administrator status
    fun revokeAdmin(userId: String) {
        audienceStore.revokeAdministrator(userId, object : CompletionHandler {
            override fun onSuccess() {
                println("Successfully revoked administrator status for user $userId")
            }

            override fun onFailure(code: Int, desc: String) {
                println("Failed to revoke administrator: $desc")
            }
        })
    }
}
```

## API Documentation

For detailed information on all public interfaces, properties, and methods of LiveAudienceStore and its related classes, refer to the official API documentation included with the AtomicX Core framework. The relevant Stores used in this document are as follows:
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| LiveCoreView | Core view component for live video stream display and interaction: responsible for video stream rendering and view widget handling, supporting scenarios such as host streaming, audience co-hosting, and host-to-host connection. |  |
| LiveAudienceStore | Audience management: obtain real-time audience list (ID / name / avatar), count audience numbers, and listen for audience join/leave events. |  |
| BarrageStore | Barrage feature: send text/custom barrage, maintain barrage list, and listen to barrage status in real time. |  |

## Platform: iOS

## Implementation

### Step 1: Component Integration
- **Video Live Streaming**: Please refer to Quick Start to integrate AtomicXCore.

- **Voice Chat Room**: Please refer to Quick Start to integrate AtomicXCore.

### Step 2: Initialize and Retrieve the Audience List

Get a `LiveAudienceStore` instance linked to the `liveId` and fetch the initial audience snapshot. 
``` swift
import Foundation
import AtomicXCore 
import Combine     

class AudienceManager {
    private let liveId: String
    private let audienceStore: LiveAudienceStore
    private var cancellables = Set<AnyCancellable>()
    
    // Expose the [full] audience list publisher for UI layer subscription
    let audienceListPublisher = CurrentValueSubject<[LiveUserInfo], Never>([])
    // Expose the audience count publisher
    let audienceCountPublisher = PassthroughSubject<UInt, Never>()

    init(liveId: String) {
        self.liveId = liveId
        // 1. Get the LiveAudienceStore instance by liveId
        self.audienceStore = LiveAudienceStore.create(liveID: liveId)
        
        // 2. Subscribe to state and events
        subscribeToAudienceState()
        subscribeToAudienceEvents()
        
        // 3. Proactively fetch initial data for the first screen
        fetchInitialAudienceList()
    }

    /// Proactively fetch a snapshot of the audience list
    func fetchInitialAudienceList() {
        audienceStore.fetchAudienceList { [weak self] result in
            switch result {
            case .success:
                print("Successfully fetched the audience list for the first time")
                // On success, data will be automatically updated via the state subscription channel below
            case .failure(let error):
                print("Failed to fetch the audience list for the first time: \(error.localizedDescription)")
            }
        }
    }
    
    // ... subsequent code
}
```

### Step 3: Subscribe to State and Events

Observe the `audienceStore` state and listen for events to receive list snapshots and real-time join/leave updates
``` swift
extension AudienceManager {
    /// Subscribe to state to get audience count and list snapshot
    private func subscribeToAudienceState() {
        audienceStore.state
            .subscribe()
            .receive(on: DispatchQueue.main)
            .sink { [weak self] state in
                // state.audienceList is a full snapshot
                self?.audienceListPublisher.send(state.audienceList)
                // state.audienceCount is the real-time total
                self?.audienceCountPublisher.send(state.audienceCount)
            }
            .store(in: &cancellables)
    }

    /// Subscribe to events to handle real-time audience join/leave
    private func subscribeToAudienceEvents() {
        audienceStore.liveAudienceEventPublisher
            .receive(on: DispatchQueue.main)
            .sink { [weak self] event in
                guard let self = self else { return }
                
                var currentList = self.audienceListPublisher.value
                
                switch event {
                case .onAudienceJoined(let newAudience):
                    print("Audience \(newAudience.userName) joined the live room")
                    // Incremental update: add the new audience member to the end of the current list
                    if !currentList.contains(where: { $0.userID == newAudience.userID }) {
                        currentList.append(newAudience)
                        self.audienceListPublisher.send(currentList)
                    }
                    
                case .onAudienceLeft(let departedAudience):
                    print("Audience \(departedAudience.userName) left the live room")
                    // Incremental update: remove the departed audience member from the current list
                    currentList.removeAll { $0.userID == departedAudience.userID }
                    self.audienceListPublisher.send(currentList)
                }
            }
            .store(in: &cancellables)
    }
}
```

### Step 4: Manage Audience

As a host or administrator, you can manage viewers in the live room.

#### 4.1 Kick a Viewer Out of the Live Room

Call the `kickUserOutOfRoom` API to remove a specified user from the live room.
``` swift
extension AudienceManager {
    func kick(userId: String) {
        audienceStore.kickUserOutOfRoom(userID: userId) { result in
            switch result {
            case .success:
                print("Successfully kicked user \(userId) out of the room")
                // After a successful kick, you will receive an onAudienceLeft event
            case .failure(let error):
                print("Failed to kick user \(userId) out: \(error.localizedDescription)")
            }
        }
    }
}
```

#### 4.2 Manage Admin Roles

Use the `setAdministrator` and `revokeAdministrator` APIs to grant or revoke administrator privileges for a user.
``` swift
extension AudienceManager {
    /// Promote user to administrator
    func promoteToAdmin(userId: String) {
        audienceStore.setAdministrator(userID: userId) { result in
            if case .success = result {
                print("Successfully promoted user \(userId) to administrator")
            }
        }
    }

    /// Revoke user's administrator status
    func revokeAdmin(userId: String) {
        audienceStore.revokeAdministrator(userID: userId) { result in
            if case .success = result {
                print("Successfully revoked administrator status for user \(userId)")
            }
        }
    }
}
```

## API Documentation

For detailed information on all public interfaces, properties, and methods of LiveAudienceStore and its related classes, refer to the official API documentation included with the AtomicX Core framework. The relevant Stores used in this document are as follows:
| **Store/Component** | **Feature Description** | **API Documentation** |
| --- | --- | --- |
| LiveCoreView | Core view component for live video stream display and interaction: responsible for video stream rendering and view widget handling, supporting scenarios such as host streaming, audience co-hosting, and host-to-host connection. |  |
| LiveAudienceStore | Audience management: obtain real-time audience list (ID / name / avatar), count audience numbers, and listen for audience join/leave events. |  |
| BarrageStore | Barrage feature: send text/custom barrage, maintain barrage list, and listen to barrage status in real time. |  |

## Platform: Flutter

## Implementation Steps

### Step 1: Component Integration
- **Live streaming**: Refer to Quick Access for seamless integration with **AtomicXCore** and service access.

- **Voice chat room**: Refer to Quick Access for integration with **AtomicXCore** and access completed.

### Step 2: Initialize and Obtain Audience List

Retrieve a `LiveAudienceStore` instance bound to the current live streaming room liveId, actively pull the current audience list once for first-time show.

When exiting the live streaming room, you can reclaim resources by calling the `AudienceManager`'s `dispose` method.
``` java
import 'package:atomic_x_core/atomicxcore.dart';

class AudienceManager {
    final String liveId;
    late final LiveAudienceStore audienceStore;
    late LiveAudienceListener _audienceListener;
    late final VoidCallback _audienceListChangedListener = _onAudienceListChanged;
    late final VoidCallback _audienceCountChangedListener = _onAudienceCountChanged;

    // Externally exposed [full] audience list
    List<LiveUserInfo> audienceList = [];
    // Externally exposed total audience count
    int audienceCount = 0;

    // Status change callback
    Function()? onStateChanged;

    AudienceManager({required this.liveId}) {
        // 1. Retrieve an instance of LiveAudienceStore by liveId
        audienceStore = LiveAudienceStore.create(liveId);

        // 2. Subscribe to status and events
        _subscribeToAudienceState();
        _subscribeToAudienceEvents();

        // 3. Actively pull first screen data
        fetchInitialAudienceList();
    }

    /// Proactively retrieve an audience list snapshot once
    Future<void> fetchInitialAudienceList() async {
        final result = await audienceStore.fetchAudienceList();
        if (result.isSuccess) {
            print("First pull audience list successfully");
            // Upon success, data will be auto-updated via the state subscription channel below
        } else {
            print("First pull audience list failed: ${result.errorMessage}");
        }
    }

    void dispose() {
        audienceStore.liveAudienceState.audienceList.removeListener(_audienceListChangedListener);
        audienceStore.liveAudienceState.audienceCount.removeListener(_audienceCountChangedListener);
        audienceStore.removeLiveAudienceListener(_audienceListener);
    }
}
```

The audience list and other `state` subscriptions will be explained in detail in the next step.

### Step 3: Monitoring the Audience List Status and Real-Time Updates

Subscribe to `audienceStore`'s `liveAudienceState` and `LiveAudienceListener` to receive full list snapshots and real-time audience Enter/Exit Events, thereby driving UI updates.
``` java
extension AudienceManagerSubscription on AudienceManager {
    /// Subscribe to state to obtain the total count of audience and list snapshots
    void _subscribeToAudienceState() {
        // Listen to audience list adjustments
        audienceStore.liveAudienceState.audienceList.addListener(_audienceListChangedListener);
        // Listen to audience change rate
        audienceStore.liveAudienceState.audienceCount.addListener(_audienceCountChangedListener);
    }

    void _onAudienceListChanged() {
        // audienceList is a full snapshot
        audienceList = audienceStore.liveAudienceState.audienceList.value;
        onStateChanged?.call();
    }

    void _onAudienceCountChanged() {
        // audienceCount is the real-time total count
        audienceCount = audienceStore.liveAudienceState.audienceCount.value;
        onStateChanged?.call();
    }

    /// Subscribe to events for processing real-time audience entry and exit
    void _subscribeToAudienceEvents() {
        _audienceListener = LiveAudienceListener(
            onAudienceJoined: (audience) {
                print("Audience ${audience.userName} joined the live streaming room");
                // Incremental update: Add new audience at the end of the current list
                if (!audienceList.any((a) => a.userID == audience.userID)) {
                    audienceList.add(audience);
                    onStateChanged?.call();
                }
            },
            onAudienceLeft: (audience) {
                print("Audience ${audience.userName} left the live streaming room");
                // Incremental update: Remove leaving audience from the current list
                audienceList.removeWhere((a) => a.userID == audience.userID);
                onStateChanged?.call();
            },
        );
        audienceStore.addLiveAudienceListener(_audienceListener);
    }
}
```

### Step 4: Manage Audience (Remove User and Set Administrator)

As an Anchor or admin, perform management operations on the audience in the live streaming room.

#### 4.1 Kick Out Audience From Live Streaming Room

Call the `kickUserOutOfRoom` API to remove designated users from the live streaming room.
``` java
extension AudienceManagerActions on AudienceManager {
    Future<void> kick(String userId) async {
        final result = await audienceStore.kickUserOutOfRoom(userId);
        if (result.isSuccess) {
            print("Successfully kicked user $userId out of room");
            // Upon success, you will receive the onAudienceLeft event
        } else {
            print("Failed to kick out user $userId: ${result.errorMessage}");
        }
    }
}
```

#### 4.2 Set/Revoke Admin

Use the `setAdministrator` and `revokeAdministrator` APIs to manage user administrator privileges.
``` java
extension AudienceManagerAdmin on AudienceManager {
    /// Set the user as administrator
    Future<void> promoteToAdmin(String userId) async {
        final result = await audienceStore.setAdministrator(userId);
        if (result.isSuccess) {
            print("Successfully set user $userId as administrator");
        }
    }

    /// Revoke the user's administrator privileges
    Future<void> revokeAdmin(String userId) async {
        final result = await audienceStore.revokeAdministrator(userId);
        if (result.isSuccess) {
            print("Successfully revoked user $userId's administrator privileges");
        }
    }
}
```

## API documentation

For details about ALL public interfaces, properties, and methods of LiveAudienceStore and its related classes, refer to the official API documentation of the AtomicXCore framework. The related Store used in this document is as follows:
| **Store/Component** | **Feature Description** | **API Reference** |
| --- | --- | --- |
| **LiveCoreWidget** | The core view component for live video stream display and interaction: responsible for stream rendering and view widget processing, supporting live streaming, audience co-broadcasting, cross-room connection with other anchors, and other scenarios. |  |
| **LiveCoreController** | LiveCoreWidget controller: used to set up live streaming ID, control preview, and perform other operations. |  |
| **LiveAudienceStore** | Audience management: get real-time audience list (ID/name/avatar), check audience size, listen to Enter/Exit Event. |  |
| **BarrageStore** | Danmaku function: send text/custom barrage item, maintain bullet screen list, monitor in real time. |  |

## FAQs

### How is the online audience count (audienceCount) in LiveAudienceState updated? What are the timing and frequency?

> **Platforms: Android / iOS**

**Android：**

The update of `audienceCount` is not always strictly real-time. The mechanism is as follows:
- **Active Join/Leave**: When a user actively joins or leaves the live room, the audience count change notification is triggered instantly. You will quickly observe the change in `audienceCount` in `LiveAudienceState`.

- **Unexpected Disconnection**: If a user disconnects unexpectedly due to network issues, app crashes, and so on, the system determines their actual status using a heartbeat mechanism. Only when the user has no heartbeat for 90 consecutive seconds does the system consider them offline and trigger a count change notification.

- **Update Mechanism and Frequency Control:**

  - Whether triggered instantly or with a delay, all audience count change notifications are broadcast as messages within the room.

  - There is an upper limit to the total number of messages per second in a room, with a per-room message rate limit of 40 messages per second.

  - Key Point: In scenarios with extremely high message traffic, such as "barrage storms" or rapid-fire gifting, if the message rate in the room exceeds the 40 messages/second threshold, to ensure the delivery of core messages (such as barrage), audience count change messages may be dropped by the rate-limiting policy.

- **What does this mean for developers?**

   `audienceCount` is a near real-time, high-precision estimate, but in extreme high-concurrency scenarios, there may be brief delays or data loss. We recommend using it for UI display only, and not as the sole basis for billing, statistics, or other scenarios requiring absolute accuracy.

**iOS：**

The `audienceCount` is a high-precision estimate. Updates occur based on specific triggers and are subject to system frequency controls:
- **Active Entry/Exit (Immediate)**:When a user explicitly joins or leaves the room, the count updates immediately in `LiveAudienceState`.

- **Unexpected Disconnection (Delayed)**:If a user disconnects abnormally (e.g., app crash, network loss), the system uses a heartbeat mechanism to verify status. The user is marked offline only after 90 seconds of inactivity, triggering a delayed count update.

- **Frequency Control & Rate Limiting**: Audience count updates are broadcast as room messages. The system enforces a limit of 40 messages per second per room.
   

   > **Note：**
   > 

   > During high-traffic spikes (e.g., chat floods or rapid gifting), if the message rate exceeds this threshold, the system prioritizes core messages (like chat/barrage). Consequently, audience count update messages may be dropped to maintain stability.
   > 

- **What does this mean for developers?**

   Treat `audienceCount` as a metric for UI display purposes only. Do not rely on it for billing, critical statistics, or scenarios requiring 100% accuracy, as brief delays or data loss may occur during high-concurrency events.

### `LiveAudienceState` How Is the Number of Online Users (`AudienceCount`) Updated? What Is the Timing and How Often?

> **Platforms: Flutter**

`audienceCount` update is not always real-time. The mechanism is as follows:
- **Proactive enter and exit rooms**: When users proactively join or normally exit live streaming room, the change notification of number of online users will immediate trigger. You will soon observe the changes of `audienceCount` in `LiveAudienceState`.

- **Unexpected disconnection**: When a user unexpectedly disconnects due to network issue, application crash, etc., the system needs to determine their actual status through heartbeat mechanism. Only when the user has no heartbeat for 90 consecutive seconds will the system deem them offline and trigger the change notification of number of users.

- **Update mechanism and frequency control:**

  - Whether it is immediate trigger or delay trigger, all change notifications of number of users are broadcast in the room in the form of messages.

  - The message total inside the room has a capacity limit per second, with a single room message frequency control of **40 messages per second**.

  - **Key points:** In scenarios with extremely high message traffic such as "bullet screen storm" or gift spam, if the message rate inside the room exceeds the threshold of 40 messages per second, to ensure the delivery of core messages (for example, bullet screen), **messages of number of users change may be dropped by the frequency control policy**.

      **What does this mean for developers?**

      `audienceCount` is a **high-precision estimate** very close to real time, but under extreme high concurrency scenarios, it may have brief delay or data loss. Therefore, we recommend that you use it for **UI display** and should not take it as the only basis for scenarios requiring absolute accuracy such as billing or statistics.
