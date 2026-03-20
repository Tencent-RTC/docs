> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

# LiveKit API Reference (LLM 轻量聚合)

## 意图索引

- 分辨率/清晰度/画质 -> `DeviceStore.updateVideoQuality`
- 开关麦克风 -> `DeviceStore.openLocalMicrophone` / `DeviceStore.closeLocalMicrophone`
- 切换摄像头 -> `DeviceStore.switchCamera`
- 开始/停止屏幕分享 -> `DeviceStore.startScreenShare` / `DeviceStore.stopScreenShare`
- 上麦/下麦 -> `LiveSeatStore.takeSeat` / `LiveSeatStore.leaveSeat`
- 锁麦/解锁麦位 -> `LiveSeatStore.lockSeat` / `LiveSeatStore.unlockSeat`
- 踢用户下麦 -> `LiveSeatStore.kickUserOutOfSeat`
- 观众列表/人数 -> `LiveAudienceStore.fetchAudienceList`
- 设置/撤销管理员 -> `LiveAudienceStore.setAdministrator` / `LiveAudienceStore.revokeAdministrator`
- 美颜(磨皮/美白/红润) -> `BaseBeautyStore.setSmoothLevel` / `BaseBeautyStore.setWhitenessLevel` / `BaseBeautyStore.setRuddyLevel`
- 弹幕发送 -> `BarrageStore.sendTextMessage` / `BarrageStore.sendCustomMessage`
- 连麦申请/邀请 -> `CoGuestStore.applyForSeat` / `CoGuestStore.acceptInvitation` / `CoGuestStore.rejectInvitation`
- 主播连线 -> `CoHostStore.requestHostConnection` / `CoHostStore.acceptHostConnection` / `CoHostStore.rejectHostConnection`
- 主播PK -> `BattleStore.requestBattle` / `BattleStore.acceptBattle` / `BattleStore.rejectBattle` / `BattleStore.exitBattle`

## Native Store API（Android/iOS/Flutter）

### LoginStore

- 高频问法关键词: `登录` `登出` `用户信息` `鉴权`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `loginStatus` | Android:StateFlow / iOS:LoginStatus / Flutter:LoginStatus | 登录状态。 |
| `loginUserInfo` | Android:StateFlow / iOS:UserProfile? / Flutter:UserProfile? | 登录用户信息。 |

**三端签名(高频)**

- 方法总数: 7（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `login` | 登录。 | `abstract fun login( context: Context, sdkAppID: Int, userID: String,` | `public func login(sdkAppID: Int32, userID: String, userSig: String, completion: CompletionClosure?) {}` | `Future<CompletionHandler> login({ required int sdkAppID, required String userID, required String userSig,` |
| `setSelfInfo` | 设置个人信息。 | `abstract fun setSelfInfo( userProfile: UserProfile, completion: CompletionHandler? = null )` | `public func setSelfInfo(userProfile: UserProfile, completion: CompletionClosure?) {}` | `Future<CompletionHandler> setSelfInfo({required UserProfile userInfo});` |
| `logout` | 登出。 | `abstract fun logout(completion: CompletionHandler? = null)` | `public func logout(completion: CompletionClosure?) {}` | `Future<CompletionHandler> logout();` |
| `shared` | 单例对象。 | `-` | `public static let shared: LoginStore = LoginStoreImpl()` | `static LoginStore get shared => LoginStoreImpl.instance;` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `login` | context:Context(必填) 上下文。; sdkAppID:Int(必填) SDK 应用 ID。; userID:String(必填) 用户 ID。; userSig:String(必填) 用户签名。; ...+1 |
| `setSelfInfo` | userProfile:UserProfile(必填) 用户资料。; completion:CompletionHandler?(必填) 完成回调。 |
| `logout` | completion:CompletionHandler?(必填) 完成回调。 |
| `shared` | - |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `LoginStatus` | 登录状态。 | UNLOGIN:未登录。; LOGINED:已登录。 | unlogin:未登录。; logined:已登录。 | unlogin:未登录。; logined:已登录。 |
| `UserProfile` | 用户资料。 | userID:String 用户 ID。; nickname:String? 昵称。; avatarURL:String? 头像URL。; selfSignature:String? 个性签名。; gender:Gender? 性别。; role:Int? 角色。; ...+3 | userID:String 用户 ID。; nickname:String? 昵称。; avatarURL:String? 头像URL。; selfSignature:String? 个性签名。; gender:Gender? 性别。; role:UInt32? 角色。; ...+3 | userID:String 用户 ID。; nickname:String? 昵称。; avatarURL:String? 头像URL。; selfSignature:String? 个性签名。; gender:Gender? 性别。; role:int? 角色。; ...+3 |

**最小示例**

```text
store = LoginStore.shared
订阅 state.loginStatus 用于 UI 同步
await store.login(...)
结束/清理时: store.logout(...)
```

### DeviceStore

- 高频问法关键词: `麦克风` `摄像头` `分辨率` `视频质量` `音量` `屏幕分享` `音频路由`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `microphoneStatus` | Android:StateFlow / iOS:DeviceStatus / Flutter:ValueListenable | 麦克风状态。 |
| `microphoneLastError` | Android:StateFlow / iOS:DeviceError / Flutter:ValueListenable | 麦克风错误，用于出现报错时提取错误信息。 |
| `captureVolume` | Android:StateFlow / iOS:Int / Flutter:ValueListenable | 采集音量，取值范围 [0, 100]。 |
| `currentMicVolume` | Android:StateFlow / iOS:Int / Flutter:ValueListenable | 当前用户实际输出音量。 |
| `outputVolume` | Android:StateFlow / iOS:Int / Flutter:ValueListenable | 最大输出音量，取值范围 [0, 100]。 |
| `cameraStatus` | Android:StateFlow / iOS:DeviceStatus / Flutter:ValueListenable | 摄像头状态。 |
| `cameraLastError` | Android:StateFlow / iOS:DeviceError / Flutter:ValueListenable | 摄像头错误，用于出现报错时提取错误信息。 |
| `isFrontCamera` | Android:StateFlow / iOS:Bool / Flutter:ValueListenable | 是否为前置摄像头。 |
| `localMirrorType` | Android:StateFlow / iOS:MirrorType / Flutter:ValueListenable | 镜像状态。 |
| `localVideoQuality` | Android:StateFlow / iOS:VideoQuality / Flutter:ValueListenable | 本地视频质量。 |
| `currentAudioRoute` | Android:StateFlow / iOS:AudioRoute / Flutter:ValueListenable | 当前音频路由位置。 |
| `screenStatus` | Android:StateFlow / iOS:DeviceStatus / Flutter:ValueListenable | 屏幕分享状态。 |
| ... | - | 其余 1 个字段见原始 API 文档 |

**三端签名(高频)**

- 方法总数: 16（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `openLocalMicrophone` | 打开本地麦克风。 | `abstract fun openLocalMicrophone(completion: CompletionHandler?)` | `public func openLocalMicrophone(completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> openLocalMicrophone();` |
| `closeLocalMicrophone` | 关闭本地麦克风。 | `abstract fun closeLocalMicrophone()` | `public func closeLocalMicrophone() { fatalError("\(#function) must be overridden by subclass") }` | `void closeLocalMicrophone();` |
| `setCaptureVolume` | 设置采集音量。 | `abstract fun setCaptureVolume(volume: Int)` | `public func setCaptureVolume(volume: Int) { fatalError("\(#function) must be overridden by subclass") }` | `void setCaptureVolume(int volume);` |
| `openLocalCamera` | 打开本地摄像头。 | `abstract fun openLocalCamera(isFront: Boolean, completion: CompletionHandler?)` | `public func openLocalCamera( isFront: Bool, completion: CompletionClosure? ) {` | `Future<CompletionHandler> openLocalCamera(bool isFront);` |
| `switchCamera` | 切换摄像头。 | `abstract fun switchCamera(isFront: Boolean)` | `public func switchCamera(isFront: Bool) { fatalError("\(#function) must be overridden by subclass") }` | `void switchCamera(bool isFront);` |
| `updateVideoQuality` | 更新视频质量。 | `abstract fun updateVideoQuality(quality: VideoQuality)` | `public func updateVideoQuality(_ quality: VideoQuality) { fatalError("\(#function) must be overridden by subclass") }` | `void updateVideoQuality(VideoQuality quality);` |
| `startScreenShare` | 开启屏幕分享。 | `abstract fun startScreenShare()` | `public func startScreenShare(appGroup: String) { fatalError("\(#function) must be overridden by subclass") }` | `void startScreenShare();` |
| `stopScreenShare` | 关闭屏幕分享。 | `abstract fun stopScreenShare()` | `public func stopScreenShare() { fatalError("\(#function) must be overridden by subclass") }` | `void stopScreenShare();` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `openLocalMicrophone` | completion:CompletionHandler?(必填) 操作是否成功。 |
| `closeLocalMicrophone` | - |
| `setCaptureVolume` | volume:Int(必填) 采集音量，取值范围 [0, 100]。 |
| `openLocalCamera` | isFront:Boolean(必填) 是否前置摄像头。; completion:CompletionHandler?(必填) 操作是否成功。 |
| `switchCamera` | isFront:Boolean(必填) 是否前置摄像头。 |
| `updateVideoQuality` | quality:VideoQuality(必填) 视频质量。 |
| `startScreenShare` | appGroup:String(必填) App Group ID。 |
| `stopScreenShare` | - |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `DeviceError` | 设备相关错误码。 | NO_ERROR=0(操作成功。); NO_DEVICE_DETECTED=1(未检测到设备。); NO_SYSTEM_PERMISSION=2(没有系统权限。); NOT_SUPPORT_CAPTURE=3(不支持采集。); OCCUPIED_ERROR=4(设备已占用。); UNKNOWN_ERROR=5(未知错误。) | noError=0(操作成功。); noDeviceDetected=1(未检测到设备。); noSystemPermission=2(没有系统权限。); notSupportCapture=3(不支持采集。); occupiedError=4(设备已占用。); unknownError=5(未知错误。) | noError=0(操作成功。); noDeviceDetected=1(未检测到设备。); noSystemPermission=2(没有系统权限。); notSupportCapture=3(不支持采集。); occupiedError=4(设备已占用。); unknownError=5(未知错误。) |
| `DeviceStatus` | 设备开启状态。 | OFF=0(关闭。); ON=1(开启。) | off=0(关闭。); on=1(开启。) | off=0(关闭。); on=1(开启。) |
| `AudioRoute` | 音频路由。 | SPEAKERPHONE=0(扬声器，使用扬声器播放（即"免提"），扬声器位于手机底部，声音偏大，适合外放音乐。); EARPIECE=1(听筒，使用听筒播放，听筒位于手机顶部，声音偏小，适合需要保护隐私的通话场景。) | speakerphone=0(扬声器，使用扬声器播放（即"免提"），扬声器位于手机底部，声音偏大，适合外放音乐。); earpiece=1(听筒，使用听筒播放，听筒位于手机顶部，声音偏小，适合需要保护隐私的通话场景。) | speakerphone=0(扬声器，使用扬声器播放（即"免提"），扬声器位于手机底部，声音偏大，适合外放音乐。); earpiece=1(听筒，使用听筒播放，听筒位于手机顶部，声音偏小，适合需要保护隐私的通话场景。) |
| `VideoQuality` | 视频质量。 | QUALITY_360P=1(360P。); QUALITY_540P=2(540P。); QUALITY_720P=3(720P。); QUALITY_1080P=4(1080P。) | quality360P=1(360P。); quality540P=2(540P。); quality720P=3(720P。); quality1080P=4(1080P。) | quality360P=1(360P。); quality540P=2(540P。); quality720P=3(720P。); quality1080P=4(1080P。) |
| `MirrorType` | 摄像头镜像状态。 | AUTO=0(自动，前置摄像头镜像，后置摄像头不镜像。); ENABLE=1(前后摄像头均镜像。); DISABLE=2(前后摄像头均不镜像。) | auto=0(自动，前置摄像头镜像，后置摄像头不镜像。); enable=1(前后摄像头均镜像。); disable=2(前后摄像头均不镜像。) | auto=0(自动，前置摄像头镜像，后置摄像头不镜像。); enable=1(前后摄像头均镜像。); disable=2(前后摄像头均不镜像。) |
| `NetworkInfo` | 网络信息 | userID:String 用户唯一ID。; quality:NetworkQuality 网络质量。; upLoss:Int 上行丢包率，取值范围 [0, 100]。; downLoss:Int 下行丢包率，取值范围 [0, 100]。; delay:Int 延迟（单位：毫秒）。 | userID:String 用户唯一ID。; quality:NetworkQuality 网络质量。; upLoss:UInt32 上行丢包率，取值范围 [0, 100]。; downLoss:UInt32 下行丢包率，取值范围 [0, 100]。; delay:UInt32 延迟（单位：毫秒）。 | userID:String 用户唯一ID。; quality:NetworkQuality 网络质量。; upLoss:int 上行丢包率，取值范围 [0, 100]。; downLoss:int 下行丢包率，取值范围 [0, 100]。; delay:int 延迟（单位：毫秒）。 |

**最小示例**

```text
store = DeviceStore.shared
订阅 state.microphoneStatus 用于 UI 同步
await store.openLocalMicrophone(...)
结束/清理时: store.closeLocalMicrophone(...)
失败时读取: state.microphoneLastError
```

### LiveAudienceStore

- 高频问法关键词: `观众列表` `观众人数` `管理员` `禁言` `踢人`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `audienceList` | Android:StateFlow > / iOS:[LiveUserInfo] / Flutter:ValueListenable > | 观众列表。 |
| `audienceCount` | Android:StateFlow / iOS:UInt / Flutter:ValueListenable | 观众数量。 |
| `messageBannedUserList` | Android:StateFlow > / iOS:[LiveUserInfo] / Flutter:ValueListenable > | 消息被禁言的用户列表。 |

**三端签名(高频)**

- 方法总数: 9（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `fetchAudienceList` | 获取观众列表。 | `abstract fun fetchAudienceList(completion: CompletionHandler?)` | `public func fetchAudienceList( completion: CompletionClosure? ) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> fetchAudienceList();` |
| `setAdministrator` | 设置管理员。 | `abstract fun setAdministrator(userID: String?, completion: CompletionHandler?)` | `public func setAdministrator(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> setAdministrator(String userID);` |
| `revokeAdministrator` | 撤销管理员。 | `abstract fun revokeAdministrator(userID: String?, completion: CompletionHandler?)` | `public func revokeAdministrator(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> revokeAdministrator(String userID);` |
| `kickUserOutOfRoom` | 踢出用户。 | `abstract fun kickUserOutOfRoom(userID: String?, completion: CompletionHandler?)` | `public func kickUserOutOfRoom(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> kickUserOutOfRoom(String userID);` |
| `disableSendMessage` | 禁言/解禁用户。 | `abstract fun disableSendMessage( userID: String?, isDisable: Boolean, completion: CompletionHandler?` | `public func disableSendMessage(userID: String, isDisable: Bool, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> disableSendMessage({ required String userID, required bool isDisable, });` |
| `create` | 创建观众管理实例。 | `-` | `public static func create(liveID: String) -> LiveAudienceStore { let store: LiveAudienceStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `-` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `fetchAudienceList` | completion:CompletionHandler?(必填) 完成回调。 |
| `setAdministrator` | userID:String?(必填) 要设置为管理员的用户ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `revokeAdministrator` | userID:String?(必填) 要撤销管理员权限的用户ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `kickUserOutOfRoom` | userID:String?(必填) 要踢出的用户ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `disableSendMessage` | userID:String?(必填) 目标用户ID。; isDisable:Boolean(必填) true 表示禁用发送消息，false 表示解禁。; completion:CompletionHandler?(必填) 完成回调。 |
| `create` | liveID:String(必填) 直播间ID。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `LiveUserInfo` | 直播用户信息。 | userID:String 用户唯一标识ID。; userName:String 用户名称。; avatarURL:String 用户头像URL。 | userID:String 用户唯一标识ID。; userName:String 用户名称。; avatarURL:String 用户头像URL。 | userID:String 用户唯一标识ID。; userName:String 用户名称。; avatarURL:String 用户头像URL。 |

**最小示例**

```text
store = LiveAudienceStore.create(liveId)
订阅 state.audienceList 用于 UI 同步
await store.fetchAudienceList(...)
await store.setAdministrator(...)
```

### LiveSeatStore

- 高频问法关键词: `上麦` `下麦` `锁麦` `解锁麦位` `踢下麦` `远程开关麦克风` `远程开关摄像头`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `seatList` | Android:StateFlow > / iOS:[SeatInfo] / Flutter:ValueListenable > | 麦位列表。 |
| `canvas` | Android:StateFlow / iOS:LiveCanvas / Flutter:ValueListenable | 画布信息。 |
| `speakingUsers` | Android:StateFlow > / iOS:[String:Int] / Flutter:ValueListenable > | 正在说话的用户。 |
| `avStatistics` | Android:StateFlow > / iOS:[AVStatistics] / Flutter:ValueListenable > | 音视频相关统计信息。 |

**三端签名(高频)**

- 方法总数: 14（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `takeSeat` | 上麦。 | `abstract fun takeSeat( seatIndex: Int, completion: CompletionHandler? )` | `public func takeSeat(seatIndex: Int, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> takeSeat(int seatIndex);` |
| `leaveSeat` | 下麦。 | `abstract fun leaveSeat(completion: CompletionHandler?)` | `public func leaveSeat(completion: CompletionClosure? = nil) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> leaveSeat();` |
| `lockSeat` | 锁麦。 | `abstract fun lockSeat( seatIndex: Int, completion: CompletionHandler? )` | `public func lockSeat( seatIndex: Int, completion: CompletionClosure? ) {` | `Future<CompletionHandler> lockSeat(int seatIndex);` |
| `unlockSeat` | 解锁麦位。 | `abstract fun unlockSeat( seatIndex: Int, completion: CompletionHandler? )` | `public func unlockSeat( seatIndex: Int, completion: CompletionClosure? ) {` | `Future<CompletionHandler> unlockSeat(int seatIndex);` |
| `kickUserOutOfSeat` | 踢用户下麦。 | `abstract fun kickUserOutOfSeat( userID: String?, completion: CompletionHandler? )` | `public func kickUserOutOfSeat(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> kickUserOutOfSeat(String userID);` |
| `moveUserToSeat` | 移动用户。 | `abstract fun moveUserToSeat( userID: String?, targetIndex: Int, policy: MoveSeatPolicy? = MoveSeatPolicy.ABORT_WHEN_OCCUPIED,` | `public func moveUserToSeat( userID: String, targetIndex: Int, policy: MoveSeatPolicy?,` | `Future<CompletionHandler> moveUserToSeat({ required String userID, required int targetIndex, MoveSeatPolicy policy = MoveSeatPolicy.abortWhenOccupied,` |
| `openRemoteMicrophone` | 开启远程麦克风。 | `abstract fun openRemoteMicrophone( userID: String?, policy: DeviceControlPolicy, completion: CompletionHandler?` | `public func openRemoteMicrophone( userID: String, policy: DeviceControlPolicy, completion: CompletionClosure?` | `Future<CompletionHandler> openRemoteMicrophone({ required String userID, required DeviceControlPolicy policy, });` |
| `closeRemoteMicrophone` | 关闭远程麦克风。 | `abstract fun closeRemoteMicrophone( userID: String?, completion: CompletionHandler? )` | `public func closeRemoteMicrophone( userID: String, completion: CompletionClosure? ) {` | `Future<CompletionHandler> closeRemoteMicrophone(String userID);` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `takeSeat` | seatIndex:Int(必填) 麦位索引。; completion:CompletionHandler?(必填) 完成回调。 |
| `leaveSeat` | completion:CompletionHandler?(必填) 完成回调。 |
| `lockSeat` | seatIndex:Int(必填) 麦位索引。; completion:CompletionHandler?(必填) 完成回调。 |
| `unlockSeat` | seatIndex:Int(必填) 麦位索引。; completion:CompletionHandler?(必填) 完成回调。 |
| `kickUserOutOfSeat` | userID:String?(必填) 用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `moveUserToSeat` | userID:String?(必填) 用户 ID。; targetIndex:Int(必填) 目标麦位索引。; policy:MoveSeatPolicy?(必填) 移动策略。; completion:CompletionHandler?(必填) 完成回调。 |
| `openRemoteMicrophone` | userID:String?(必填) 用户 ID。; policy:DeviceControlPolicy(必填) 设备控制策略。; completion:CompletionHandler?(必填) 完成回调。 |
| `closeRemoteMicrophone` | userID:String?(必填) 用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `MoveSeatPolicy` | 移动麦位策略。 | ABORT_WHEN_OCCUPIED:被占用时中止。; FORCE_REPLACE:强制替换。; SWAP_POSITION:交换位置。 | abortWhenOccupied:被占用时中止。; forceReplace:强制替换。; swapPosition:交换位置。 | abortWhenOccupied:被占用时中止。; forceReplace:强制替换。; swapPosition:交换位置。 |
| `DeviceControlPolicy` | 设备控制策略。 | UNLOCK_ONLY:仅解锁。 | unlockOnly:仅解锁。 | unlockOnly:仅解锁。 |
| `AVStatistics` | 音视频相关统计信息。 | userID:String 用户 ID。; videoBitrate:Int 本地视频的码率。; videoWidth:Int 本地视频的宽度。; videoHeight:Int 本地视频的高度。; frameRate:Int 本地视频的帧率。; audioSampleRate:Int 音频的采样率。; ...+1 | userID:String 用户 ID。; videoBitrate:UInt 本地视频的码率。; videoWidth:CGFloat 本地视频的宽度。; videoHeight:CGFloat 本地视频的高度。; frameRate:UInt 本地视频的帧率。; audioSampleRate:UInt 音频的采样率。; ...+1 | userID:String 用户 ID。; videoBitrate:int 本地视频的码率。; videoWidth:int 本地视频的宽度。; videoHeight:int 本地视频的高度。; frameRate:int 本地视频的帧率。; audioSampleRate:int 音频的采样率。; ...+1 |
| `SeatInfo` | 麦位信息。 | index:Int 麦位索引。; isLocked:Boolean 是否锁定。; userInfo:SeatUserInfo 用户信息。; region:RegionInfo 区域信息。 | index:Int 麦位索引。; isLocked:Bool 是否锁定。; userInfo:SeatUserInfo 用户信息。; region:RegionInfo 区域信息。 | index:int 麦位索引。; isLocked:bool 是否锁定。; userInfo:SeatUserInfo 用户信息。; region:RegionInfo 区域信息。 |
| `LiveCanvas` | 直播画布。 | w:Int 宽度。; h:Int 高度。; templateID:Int 模板ID。 | w:CGFloat 宽度。; h:CGFloat 高度。; templateID:UInt 模板ID。 | w:int 宽度。; h:int 高度。; templateID:int 模板ID。 |

**最小示例**

```text
store = LiveSeatStore.create(liveId)
订阅 state.seatList 用于 UI 同步
await store.takeSeat(...)
结束/清理时: store.leaveSeat(...)
```

### CoGuestStore

- 高频问法关键词: `连麦申请` `连麦邀请` `接受连麦` `拒绝连麦` `断开连麦`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `connected` | Android:StateFlow > / iOS:[SeatUserInfo] / Flutter:ValueListenable > | 已经在麦位上的用户列表。 |
| `invitees` | Android:StateFlow > / iOS:[LiveUserInfo] / Flutter:ValueListenable > | 主播发出邀请的用户列表。 |
| `applicants` | Android:StateFlow > / iOS:[LiveUserInfo] / Flutter:ValueListenable > | 主播收到申请连麦的用户列表。 |
| `candidates` | Android:StateFlow > / iOS:[LiveUserInfo] / Flutter:ValueListenable > | 候选连麦的用户列表。 |

**三端签名(高频)**

- 方法总数: 17（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `applyForSeat` | 观众申请连麦。 | `abstract fun applyForSeat( seatIndex: Int, timeout: Int, extraInfo: String?,` | `public func applyForSeat(seatIndex: Int = -1, timeout: TimeInterval, extraInfo: String?, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> applyForSeat({ required int seatIndex, required int timeout, String? extraInfo,` |
| `cancelApplication` | 观众取消申请。 | `abstract fun cancelApplication( completion: CompletionHandler? )` | `public func cancelApplication(completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> cancelApplication();` |
| `acceptInvitation` | 观众接受邀请。 | `abstract fun acceptInvitation( inviterID: String?, completion: CompletionHandler? )` | `public func acceptInvitation(inviterID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> acceptInvitation(String inviterID);` |
| `rejectInvitation` | 观众拒绝邀请。 | `abstract fun rejectInvitation( inviterID: String?, completion: CompletionHandler? )` | `public func rejectInvitation(inviterID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> rejectInvitation(String inviterID);` |
| `disConnect` | 结束连麦会话。 | `-` | `public func disConnect(completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `-` |
| `create` | 创建对象实例。 | `-` | `public static func create(liveID: String) -> CoGuestStore { let store: CoGuestStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `-` |
| `acceptApplication` | 主播接受申请。 | `abstract fun acceptApplication( userID: String?, completion: CompletionHandler? )` | `public func acceptApplication(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> acceptApplication(String userID);` |
| `rejectApplication` | 主播拒绝申请。 | `abstract fun rejectApplication( userID: String?, completion: CompletionHandler? )` | `public func rejectApplication(userID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> rejectApplication(String userID);` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `applyForSeat` | seatIndex:Int(必填) 麦位索引，-1 表示自动分配麦位。; timeout:Int(必填) 超时时间（单位：秒）。; extraInfo:String?(必填) 额外信息。; completion:CompletionHandler?(必填) 完成回调。 |
| `cancelApplication` | completion:CompletionHandler?(必填) 完成回调。 |
| `acceptInvitation` | inviterID:String?(必填) 邀请者用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `rejectInvitation` | inviterID:String?(必填) 邀请者用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `disConnect` | completion:CompletionClosure?(必填) 完成回调。 |
| `create` | liveID:String(必填) 直播间ID。 |
| `acceptApplication` | userID:String?(必填) 用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |
| `rejectApplication` | userID:String?(必填) 用户 ID。; completion:CompletionHandler?(必填) 完成回调。 |

**最小示例**

```text
store = CoGuestStore.create(liveId)
订阅 state.connected 用于 UI 同步
await store.applyForSeat(...)
await store.cancelApplication(...)
```

### CoHostStore

- 高频问法关键词: `主播连线` `连线邀请` `接受连线` `拒绝连线` `退出连线`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `coHostStatus` | Android:StateFlow / iOS:CoHostStatus / Flutter:ValueListenable | 跨房连线的实时状态。 |
| `connected` | Android:StateFlow > / iOS:[SeatUserInfo] / Flutter:ValueListenable > | 正在和当前直播间连线的主播列表。 |
| `invitees` | Android:StateFlow > / iOS:[SeatUserInfo] / Flutter:ValueListenable > | 向其他直播间发出请求的主播列表。 |
| `applicant` | Android:StateFlow / iOS:SeatUserInfo? / Flutter:ValueListenable | 向当前直播间发起连线请求的主播。 |
| `candidatesCursor` | Android:StateFlow / iOS:String / Flutter:ValueListenable | 推荐用户列表游标。 |
| `candidates` | Android:StateFlow > / iOS:[SeatUserInfo] / Flutter:ValueListenable > | 推荐用户列表。 |

**三端签名(高频)**

- 方法总数: 10（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `requestHostConnection` | 发起连线请求。 | `abstract fun requestHostConnection( targetHostLiveID: String?, layoutTemplate: CoHostLayoutTemplate, timeout: Int,` | `public func requestHostConnection(targetHost liveId: String, layoutTemplate: CoHostLayoutTemplate, timeout: TimeInterval, extraInfo: String = "",` | `Future<CompletionHandler> requestHostConnection({ required String targetHostLiveID, required CoHostLayoutTemplate layoutTemplate, required int timeout,` |
| `cancelHostConnection` | 取消连线请求。 | `abstract fun cancelHostConnection( toHostLiveID: String?, completion: CompletionHandler? )` | `public func cancelHostConnection(toHostLiveID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> cancelHostConnection(String toHostLiveID);` |
| `acceptHostConnection` | 接受连线请求。 | `abstract fun acceptHostConnection( fromHostLiveID: String?, completion: CompletionHandler? )` | `public func acceptHostConnection(fromHostLiveID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> acceptHostConnection(String fromHostLiveID);` |
| `rejectHostConnection` | 拒绝连线请求。 | `abstract fun rejectHostConnection( fromHostLiveID: String?, completion: CompletionHandler? )` | `public func rejectHostConnection(fromHostLiveID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> rejectHostConnection(String fromHostLiveID);` |
| `exitHostConnection` | 退出连线。 | `abstract fun exitHostConnection( completion: CompletionHandler? )` | `public func exitHostConnection(completion: CompletionClosure? = nil) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> exitHostConnection();` |
| `create` | 创建对象实例。 | `-` | `public static func create(liveID: String) -> CoHostStore { let store: CoHostStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `-` |
| `getCoHostCandidates` | 获取推荐主播列表。 | `abstract fun getCoHostCandidates( cursor: String, completion: CompletionHandler? )` | `public func getCoHostCandidates(cursor: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> getCoHostCandidates(String cursor);` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `requestHostConnection` | targetHostLiveID:String?(必填) 目标主播的直播间ID。; layoutTemplate:CoHostLayoutTemplate(必填) 连线布局模板。; timeout:Int(必填) 请求超时时间（单位：秒）。; extraInfo:String?(必填) 扩展信息。; ...+2 |
| `cancelHostConnection` | toHostLiveID:String?(必填) 目标主播的直播间ID。; completion:CompletionHandler?(必填) 取消请求成功的回调。 |
| `acceptHostConnection` | fromHostLiveID:String?(必填) 发起连线请求的主播直播间ID。; completion:CompletionHandler?(必填) 接受成功的回调。 |
| `rejectHostConnection` | fromHostLiveID:String?(必填) 发起连线请求的主播直播间ID。; completion:CompletionHandler?(必填) 拒绝成功的回调。 |
| `exitHostConnection` | completion:CompletionHandler?(必填) 退出连线成功的回调。 |
| `create` | liveID:String(必填) 直播间ID。 |
| `getCoHostCandidates` | cursor:String(必填) 游标。; completion:CompletionHandler?(必填) 完成回调。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `CoHostStatus` | 当前用户的跨房连线状态。 | CONNECTED=0(和其他主播正在连线中。); DISCONNECTED=1(没有和其他主播连线。) | connected=0(和其他主播正在连线中。); disconnected=1(没有和其他主播连线。) | connected=0(和其他主播正在连线中。); disconnected=1(没有和其他主播连线。) |
| `CoHostLayoutTemplate` | 连线布局模板。 | HOST_VOICE_CONNECTION=2(语聊房连线布局。); HOST_DYNAMIC_GRID=600(主播动态网格布局。); HOST_DYNAMIC_1V6=601(主播动态1对6布局。) | hostVoiceConnection=2(语聊房连线布局。); hostDynamicGrid=600(主播动态网格布局。); hostDynamic1v6=601(主播动态1对6布局。) | hostVoiceConnection=2(语聊房连线布局。); hostDynamicGrid=600(主播动态网格布局。); hostDynamic1v6=601(主播动态1对6布局。) |

**最小示例**

```text
store = CoHostStore.create(liveId)
订阅 state.coHostStatus 用于 UI 同步
await store.requestHostConnection(...)
结束/清理时: store.exitHostConnection(...)
```

### BattleStore

- 高频问法关键词: `PK` `发起PK` `接受PK` `拒绝PK` `结束PK`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `currentBattleInfo` | Android:StateFlow / iOS:BattleInfo? / Flutter:ValueListenable | 当前 PK 信息。 |
| `battleUsers` | Android:StateFlow > / iOS:[SeatUserInfo] / Flutter:ValueListenable > | PK 用户列表。 |
| `battleScore` | Android:StateFlow > / iOS:[String:UInt] / Flutter:ValueListenable > | PK 分数的映射。 |

**三端签名(高频)**

- 方法总数: 9（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `requestBattle` | 发起 PK 请求。 | `abstract fun requestBattle( config: BattleConfig, userIDList: List<String>, timeout: Int,` | `public func requestBattle(config: BattleConfig, userIDList: [String], timeout: TimeInterval, completion: BattleRequestClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<BattleRequestCompletionHandler> requestBattle({ required BattleConfig config, required List<String> userIDList, required int timeout,` |
| `cancelBattleRequest` | 取消 PK 请求。 | `abstract fun cancelBattleRequest( battleID: String?, userIDList: List<String>, completion: CompletionHandler?` | `public func cancelBattleRequest(battleId: String, userIdList: [String], completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> cancelBattleRequest({ required String battleID, required List<String> userIDList, });` |
| `acceptBattle` | 接受 PK 请求。 | `abstract fun acceptBattle( battleID: String?, completion: CompletionHandler? )` | `public func acceptBattle(battleID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> acceptBattle(String battleID);` |
| `rejectBattle` | 拒绝 PK 请求。 | `abstract fun rejectBattle( battleID: String?, completion: CompletionHandler? )` | `public func rejectBattle(battleID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> rejectBattle(String battleID);` |
| `exitBattle` | 退出 PK。 | `abstract fun exitBattle( battleID: String?, completion: CompletionHandler? )` | `public func exitBattle(battleID: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> exitBattle(String battleID);` |
| `create` | 创建 BattleStore 实例。 | `-` | `public static func create(liveID: String) -> BattleStore { let store: BattleStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `-` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `requestBattle` | config:BattleConfig(必填) PK 配置。; userIDList:List(必填) 需要进入PK的用户 ID 列表。; timeout:Int(必填) 请求超时时间。; completion:BattleRequestCallback?(必填) 发起请求成功的回调。 |
| `cancelBattleRequest` | battleID:String?(必填) PK ID。; userIDList:List(必填) 用户ID列表。; completion:CompletionHandler?(必填) 取消请求成功的回调。; battleId:String(必填) PK ID。; ...+1 |
| `acceptBattle` | battleID:String?(必填) PK ID。; completion:CompletionHandler?(必填) 接受成功的回调。 |
| `rejectBattle` | battleID:String?(必填) PK ID。; completion:CompletionHandler?(必填) 拒绝成功的回调。 |
| `exitBattle` | battleID:String?(必填) PK ID。; completion:CompletionHandler?(必填) 退出 PK 成功的回调。 |
| `create` | liveID:String(必填) 直播间ID。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `BattleConfig` | 发送 PK 请求时，设置的 PK 配置信息。 | duration:Int PK 持续时间（单位：秒）。; needResponse:Boolean 被邀请用户是否需要回复同意/拒绝。; extensionInfo:String 扩展信息。 | duration:TimeInterval PK 持续时间（单位：秒）。; needResponse:Bool 被邀请用户是否需要回复同意/拒绝。; extensionInfo:String 扩展信息。 | duration:int PK 持续时间（单位：秒）。; needResponse:bool 被邀请用户是否需要回复同意/拒绝。; extensionInfo:String 扩展信息。 |
| `BattleInfo` | PK 信息。 | battleID:String PK ID。; config:BattleConfig 发送 PK 请求时，设置的 PK 配置信息。; startTime:Long PK 开始标记时间戳（单位：秒）。; endTime:Long PK 结束标记时间戳（单位：秒）。 | battleID:String PK ID。; config:BattleConfig 发送 PK 请求时，设置的 PK 配置信息。; startTime:UInt PK 开始标记时间戳（单位：秒）。; endTime:UInt PK 结束标记时间戳（单位：秒）。 | battleID:String PK ID。; config:BattleConfig 发送 PK 请求时，设置的 PK 配置信息。; startTime:int PK 开始标记时间戳（单位：秒）。; endTime:int PK 结束标记时间戳（单位：秒）。 |
| `BattleRequestCallback` | PK 请求回调。 | **参数名**:**类型** **说明**; battleInfo:BattleInfo PK 信息，包含 PK 的详细配置和状态。; resultMap:Map PK 请求的响应结果回调。 | **参数名**:**类型** **说明**; battleInfo:BattleInfo PK 信息，包含 PK 的详细配置和状态。; resultMap:Map PK 请求的响应结果回调。 | - |

**最小示例**

```text
store = BattleStore.create(liveId)
订阅 state.currentBattleInfo 用于 UI 同步
await store.requestBattle(...)
结束/清理时: store.exitBattle(...)
```

### BarrageStore

- 高频问法关键词: `弹幕` `文本消息` `自定义消息` `本地提示`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `messageList` | Android:StateFlow > / iOS:[Barrage] / Flutter:ValueListenable > | 当前房间的弹幕消息列表，支持实时更新并可被订阅监听。 |

**三端签名(高频)**

- 方法总数: 4（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `sendTextMessage` | 发送文本弹幕。 | `abstract fun sendTextMessage( text: String?, extensionInfo: Map<String, String>?, completion: CompletionHandler?` | `public func sendTextMessage(text: String, extensionInfo: [String: String]?, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> sendTextMessage({ required String text, Map<String, String>? extensionInfo, });` |
| `sendCustomMessage` | 发送自定义弹幕。 | `abstract fun sendCustomMessage( businessID: String?, data: String?, completion: CompletionHandler?` | `public func sendCustomMessage(businessID: String, data: String, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> sendCustomMessage({ required String businessID, required String data, });` |
| `appendLocalTip` | 添加本地提示消息。 | `abstract fun appendLocalTip(message: Barrage)` | `public func appendLocalTip(message: Barrage) { fatalError("\(#function) must be overridden by subclass") }` | `void appendLocalTip(Barrage message);` |
| `create` | 创建弹幕管理实例。 | `-` | `public static func create(liveID: String) -> BarrageStore { let store: BarrageStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `static BarrageStore create(String liveID) { return StoreFactory.shared.getStore<BarrageStore>(liveID: liveID); }` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `sendTextMessage` | text:String?(必填) 文本弹幕内容。; extensionInfo:Map ?(必填) 扩展信息，可包含自定义字段（如指定弹幕颜色、字体大小等）。; completion:CompletionHandler?(必填) 完成回调（成功/失败状态）。 |
| `sendCustomMessage` | businessID:String?(必填) 业务标识ID，用于区分不同业务场景的自定义弹幕。; data:String?(必填) 自定义数据内容，通常为JSON格式字符串，用于传递业务自定义的数据。; completion:CompletionHandler?(必填) 完成回调（成功/失败状态）。 |
| `appendLocalTip` | message:Barrage(必填) 本地弹幕消息（如系统提示、操作反馈等，仅当前用户可见）。 |
| `create` | liveID:String(必填) 直播间ID。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `Barrage` | 弹幕数据模型，包含单条弹幕的完整属性信息。 | liveID:String 弹幕所属直播间/语音聊天房的唯一标识ID。; sender:LiveUserInfo 弹幕发送者的用户信息（如用户ID、昵称、头像等）。; sequence:Long 弹幕消息的唯一序列ID，用于消息排序和去重。; timestampInSecond:Long 弹幕发送时间戳（单位：秒），用于展示发送时间顺序。; textContent:String 文本类型弹幕的消息内容，即弹幕的文本内容。; extensionInfo:Map 弹幕扩展信息，可自定义字段（如显示样式、优先级等）。当 messageType 为 TEXT 时有效。; ...+2 | liveID:String 弹幕所属直播间/语音聊天房的唯一标识ID。; sender:LiveUserInfo 弹幕发送者的用户信息（如用户ID、昵称、头像等）。; sequence:Int 弹幕消息的唯一序列ID，用于消息排序和去重。; timestampInSecond:TimeInterval 弹幕发送时间戳（单位：秒），用于展示发送时间顺序。; textContent:String 文本类型弹幕的消息内容，即弹幕的文本内容。; extensionInfo:[String:String]? 弹幕扩展信息，可自定义字段（如显示样式、优先级等）。当 messageType 为 TEXT 时有效。; ...+2 | liveID:String 弹幕所属直播间/语音聊天房的唯一标识ID。; sender:LiveUserInfo 弹幕发送者的用户信息（如用户ID、昵称、头像等）。; sequence:int 弹幕消息的唯一序列ID，用于消息排序和去重。; timestampInSecond:int 弹幕发送时间戳（单位：秒），用于展示发送时间顺序。; textContent:String 文本类型弹幕的消息内容，即弹幕的文本内容。; extensionInfo:Map 弹幕扩展信息，可自定义字段（如显示样式、优先级等）。当 messageType 为 TEXT 时有效。; ...+2 |

**最小示例**

```text
store = BarrageStore.create(liveId)
订阅 state.messageList 用于 UI 同步
await store.sendTextMessage(...)
await store.sendCustomMessage(...)
```

### GiftStore

- 高频问法关键词: `礼物` `送礼` `礼物消息` `礼物动画`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `usableGifts` | Android:StateFlow > / iOS:[GiftCategory] / Flutter:ValueListenable > | 当前房间可用的所有礼物分类及礼物列表。 |

**三端签名(高频)**

- 方法总数: 7（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `sendGift` | 发送礼物。 | `abstract fun sendGift( giftID: String, count: Int, completion: CompletionHandler?` | `public func sendGift(giftID: String, count: UInt, completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass")` | `Future<CompletionHandler> sendGift({ required String giftID, required int count, });` |
| `create` | 创建礼物管理实例。 | `-` | `public static func create(liveID: String) -> GiftStore { let store: GiftStoreImpl = StoreFactory.shared.getStore(liveId: liveID) return store }` | `-` |
| `refreshUsableGifts` | 刷新可用礼物列表。 | `abstract fun refreshUsableGifts(completion: CompletionHandler?)` | `public func refreshUsableGifts(completion: CompletionClosure?) { fatalError("\(#function) must be overridden by subclass") }` | `Future<CompletionHandler> refreshUsableGifts();` |
| `setLanguage` | 设置展示语言。 | `abstract fun setLanguage(language: String)` | `public func setLanguage(_ language: String) { fatalError("\(#function) must be overridden by subclass") }` | `void setLanguage(String language);` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `sendGift` | giftID:String(必填) 要发送的礼物唯一标识 ID。; count:Int(必填) 单次发送的礼物数量。; completion:CompletionHandler?(必填) 完成回调（成功/失败状态）。 |
| `create` | liveID:String(必填) 直播间ID。 |
| `refreshUsableGifts` | completion:CompletionHandler?(必填) 完成回调（成功时可通过 state 获取最新礼物列表，失败时返回错误信息）。 |
| `setLanguage` | language:String(必填) 语言代码（"zh-CN" 表示中文，"en" 表示英文），设置完成展示界面刷新后礼物名称、描述等会同步更新为对应语言。 |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `GiftCategory` | 礼物分类。 | categoryID:String 分类唯一标识 ID，用于区分不同礼物分类。; name:String 分类展示名称，用于 UI 分类显示（如 "热门礼物"，"高级礼物"）。; desc:String 分类描述信息，用于说明该分类的特点。; extensionInfo:Map 分类扩展信息，包含自定义字段（如排序权重、显示样式等）。; giftList:List 当前分类下的所有礼物列表。 | categoryID:String 分类唯一标识 ID，用于区分不同礼物分类。; name:String 分类展示名称，用于 UI 分类显示（如 "热门礼物"，"高级礼物"）。; desc:String 分类描述信息，用于说明该分类的特点。; extensionInfo:[String:String] 分类扩展信息，包含自定义字段（如排序权重、显示样式等）。; giftList:[Gift] 当前分类下的所有礼物列表。 | categoryID:String 分类唯一标识 ID，用于区分不同礼物分类。; name:String 分类展示名称，用于 UI 分类显示（如 "热门礼物"，"高级礼物"）。; desc:String 分类描述信息，用于说明该分类的特点。; extensionInfo:Map 分类扩展信息，包含自定义字段（如排序权重、显示样式等）。; giftList:List 当前分类下的所有礼物列表。 |

**最小示例**

```text
store = GiftStore.create(liveId)
订阅 state.usableGifts 用于 UI 同步
await store.sendGift(...)
await store.refreshUsableGifts(...)
```

### AudioEffectStore

- 高频问法关键词: `音效` `背景音乐` `变声` `混响` `耳返`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `audioChangerType` | Android:StateFlow / iOS:AudioChangerType / Flutter:ValueListenable | 变声状态。 |
| `audioReverbType` | Android:StateFlow / iOS:AudioReverbType / Flutter:ValueListenable | 混响状态。 |
| `isEarMonitorOpened` | Android:StateFlow / iOS:Bool / Flutter:ValueListenable | 耳返开启。 |
| `earMonitorVolume` | Android:StateFlow / iOS:Int / Flutter:ValueListenable | 耳返音量，取值范围 0 - 100。 如果将 volume 设置成 100 之后感觉音量还是太小，可以将 volume 最大设置成 150，但超过 100 的 volume 会有爆音的风险，请谨慎操作。 |

**三端签名(高频)**

- 方法总数: 6（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `shared` | 获取单例实例。 | `-` | `-` | `-` |
| `setAudioChangerType` | 设置变声效果。 | `abstract fun setAudioChangerType(type: AudioChangerType)` | `public func setAudioChangerType(type: AudioChangerType) { fatalError("\(#function) must be overridden by subclass") }` | `void setAudioChangerType(AudioChangerType type);` |
| `setAudioReverbType` | 设置混响效果。 | `abstract fun setAudioReverbType(type: AudioReverbType)` | `public func setAudioReverbType(type: AudioReverbType) { fatalError("\(#function) must be overridden by subclass") }` | `void setAudioReverbType(AudioReverbType type);` |
| `setVoiceEarMonitorEnable` | 开启/关闭耳返。 | `abstract fun setVoiceEarMonitorEnable(enable: Boolean)` | `public func setVoiceEarMonitorEnable(enable: Bool) { fatalError("\(#function) must be overridden by subclass") }` | `void setVoiceEarMonitorEnable(bool enable);` |
| `setVoiceEarMonitorVolume` | 设置耳返音量。 | `abstract fun setVoiceEarMonitorVolume(volume: Int)` | `public func setVoiceEarMonitorVolume(volume: Int) { fatalError("\(#function) must be overridden by subclass") }` | `void setVoiceEarMonitorVolume(int volume);` |
| `reset` | 重置为默认状态。 | `abstract fun reset()` | `public func reset() { fatalError("\(#function) must be overridden by subclass") }` | `void reset();` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `shared` | - |
| `setAudioChangerType` | type:AudioChangerType(必填) 变声效果类型。 |
| `setAudioReverbType` | type:AudioReverbType(必填) 混响效果类型。 |
| `setVoiceEarMonitorEnable` | enable:Boolean(必填) 是否开启耳返。 |
| `setVoiceEarMonitorVolume` | volume:Int(必填) 耳返音量。（取值范围：0 - 100（超过 100 可能导致爆音））（默认值：100）。 |
| `reset` | - |

**类型定义/数据结构(高频关联)**

| Type | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `AudioChangerType` | 变声效果类型。 | NONE=0(关闭特效。); CHILD=1(熊孩子。); LITTLE_GIRL=2(小女孩。); MAN=3(大叔。); HEAVY_METAL=4(重金属。); COLD=5(感冒。); ...+6 | none=0(关闭特效。); child=1(熊孩子。); littleGirl=2(小女孩。); man=3(大叔。); heavyMetal=4(重金属。); cold=5(感冒。); ...+6 | none=0(关闭特效。); child=1(熊孩子。); littleGirl=2(小女孩。); man=3(大叔。); heavyMetal=4(重金属。); cold=5(感冒。); ...+6 |
| `AudioReverbType` | 混响效果类型。 | NONE=0(关闭特效。); KTV=1(KTV。); SMALL_ROOM=2(小房间。); AUDITORIUM=3(大会堂。); DEEP=4(低沉。); LOUD=5(洪亮。); ...+2 | none=0(关闭特效。); ktv=1(KTV。); smallRoom=2(小房间。); auditorium=3(大会堂。); deep=4(低沉。); loud=5(洪亮。); ...+2 | none=0(关闭特效。); ktv=1(KTV。); smallRoom=2(小房间。); auditorium=3(大会堂。); deep=4(低沉。); loud=5(洪亮。); ...+2 |

**最小示例**

```text
store = AudioEffectStore.shared
订阅 state.audioChangerType 用于 UI 同步
await store.setAudioChangerType(...)
await store.setAudioReverbType(...)
```

### BaseBeautyStore

- 高频问法关键词: `美颜` `磨皮` `美白` `红润` `重置美颜`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |
| `smoothLevel` | Android:- / iOS:Float / Flutter:ValueListenable | 磨皮级别，取值范围 [0-9]；0 表示关闭，9 表示效果最明显。 |
| `whitenessLevel` | Android:- / iOS:Float / Flutter:ValueListenable | 美白级别，取值范围 [0-9]；0 表示关闭，9 表示效果最明显。 |
| `ruddyLevel` | Android:- / iOS:Float / Flutter:ValueListenable | 红润级别，取值范围 [0-9]；0 表示关闭，9 表示效果最明显。 |

**三端签名(高频)**

- 方法总数: 5（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `setSmoothLevel` | 设置磨皮级别。 | `-` | `public func setSmoothLevel(smoothLevel: Float) { fatalError("\(#function) must be overridden by subclass") }` | `void setSmoothLevel(double smoothLevel);` |
| `setWhitenessLevel` | 设置美白级别。 | `-` | `public func setWhitenessLevel(whitenessLevel: Float) { fatalError("\(#function) must be overridden by subclass") }` | `void setWhitenessLevel(double whitenessLevel);` |
| `setRuddyLevel` | 设置红润级别。 | `-` | `public func setRuddyLevel(ruddyLevel: Float) { fatalError("\(#function) must be overridden by subclass") }` | `void setRuddyLevel(double ruddyLevel);` |
| `reset` | 重置为默认状态。 | `-` | `public func reset() { fatalError("\(#function) must be overridden by subclass") }` | `void reset();` |
| `shared` | 获取单例实例。 | `-` | `-` | `-` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `setSmoothLevel` | smoothLevel:Float(必填) 磨皮级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |
| `setWhitenessLevel` | whitenessLevel:Float(必填) 美白级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |
| `setRuddyLevel` | ruddyLevel:Float(必填) 红润级别，取值范围 [0, 9]；0 表示关闭，9 表示效果最明显。 |
| `reset` | - |
| `shared` | - |

**最小示例**

```text
store = BaseBeautyStore.shared
订阅 state.smoothLevel 用于 UI 同步
await store.setSmoothLevel(...)
await store.setWhitenessLevel(...)
```

### LikeStore

- 高频问法关键词: `点赞` `连点` `点赞动画`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |

**三端签名(高频)**

- 方法总数: 0（仅展示高频方法签名）

- 当前 Store 未抽取到可用方法签名。

### LiveCoreView

- 高频问法关键词: `直播主画面` `流渲染` `画面组件`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |

**三端签名(高频)**

- 方法总数: 0（仅展示高频方法签名）

- 当前 Store 未抽取到可用方法签名。

### CameraView

- 高频问法关键词: `预览` `摄像头视图` `相机画面`

**状态字段(精简)**

| 字段 | 类型(Android/iOS/Flutter) | 说明 |
| --- | --- | --- |

**三端签名(高频)**

- 方法总数: 1（仅展示高频方法签名）

| Method | 说明 | Android | iOS | Flutter |
| --- | --- | --- | --- | --- |
| `CameraView` | 构造函数。 | `// 方式一：通过 Context 创建 val cameraView = CameraView(context) // 方式二：通过 SurfaceView 创建 val surfaceView = SurfaceView(context)` | `-` | `-` |

**关键参数(高频方法)**

| Method | 参数摘要 |
| --- | --- |
| `CameraView` | - |

## Web Vue State API 索引

### LLM 检索规则
- source: `web-vue`
- base: `https://web.sdk.qcloud.com/trtc/live/web/doc/zh/index.html`
- url_template: `{base}{anchor}`
- when_to_fetch: 命中 Web Vue 的 State/Method 问题时，先 fetch 再回答。
- required_extract: `signature, params, return, enum, constraints, errors`
- answer_rule: 若未 fetch 到详情，明确说明“仅基于索引，信息不完整”。

### LoginState
- 响应式数据(1): `loginUserInfo(#loginUserInfo)`
- 接口函数(3)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `login` | 用户登录。 | `#login` |
| `setSelfInfo` | 设置用户个人信息。 | `#setSelfInfo` |
| `logout` | 用户登出函数。 | `#logout` |

### DeviceState
- 响应式数据(24): `microphoneStatus(#microphoneStatus)`, `microphoneList(#microphoneList)`, `currentMicrophone(#currentMicrophone)`, `microphoneLastError(#microphoneLastError)`, `captureVolume(#captureVolume)`, `currentMicVolume(#currentMicVolume)`, `isMicrophoneTesting(#isMicrophoneTesting)`, `testingMicVolume(#testingMicVolume)`, `cameraStatus(#cameraStatus)`, `cameraList(#cameraList)`, `currentCamera(#currentCamera)`, `cameraLastError(#cameraLastError)`, `isCameraTesting(#isCameraTesting)`, `isCameraTestLoading(#isCameraTestLoading)`, `isFrontCamera(#isFrontCamera)`, `localMirrorType(#localMirrorType)`, `localVideoQuality(#localVideoQuality)`, `speakerList(#speakerList)`, `currentSpeaker(#currentSpeaker)`, `outputVolume(#outputVolume)`, `currentAudioRoute(#currentAudioRoute)`, `isSpeakerTesting(#isSpeakerTesting)`, `screenStatus(#screenStatus)`, `networkInfo(#networkInfo)`
- 接口函数(27)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `openLocalMicrophone` | 开启本地麦克风。 | `#openLocalMicrophone` |
| `closeLocalMicrophone` | 关闭本地麦克风。 | `#closeLocalMicrophone` |
| `muteLocalAudio` | 静音本地音频。 | `#muteLocalAudio` |
| `unmuteLocalAudio` | 取消静音本地音频。 | `#unmuteLocalAudio` |
| `getMicrophoneList` | 获取麦克风设备列表。 | `#getMicrophoneList` |
| `setCurrentMicrophone` | 设置当前麦克风设备。 | `#setCurrentMicrophone` |
| `startMicrophoneTest` | 开始麦克风测试。 | `#startMicrophoneTest` |
| `setCaptureVolume` | 设置音频采集音量。 | `#setCaptureVolume` |
| `setOutputVolume` | 设置音频播放音量。 | `#setOutputVolume` |
| `stopMicrophoneTest` | 停止麦克风测试。 | `#stopMicrophoneTest` |
| `getSpeakerList` | 获取扬声器设备列表。 | `#getSpeakerList` |
| `setCurrentSpeaker` | 设置当前扬声器设备。 | `#setCurrentSpeaker` |
| `setAudioRoute` | 设置音频路由。 | `#setAudioRoute` |
| `startSpeakerTest` | 开始扬声器测试。 | `#startSpeakerTest` |
| `stopSpeakerTest` | 停止扬声器测试。 | `#stopSpeakerTest` |
| `openLocalCamera` | 开启本地摄像头。 | `#openLocalCamera` |
| `closeLocalCamera` | 关闭本地摄像头。 | `#closeLocalCamera` |
| `getCameraList` | 获取摄像头设备列表。 | `#getCameraList` |
| `setCurrentCamera` | 设置当前摄像头设备。 | `#setCurrentCamera` |
| `switchCamera` | 切换前后摄像头。 | `#switchCamera` |
| `switchMirror` | 切换视频镜像模式。 | `#switchMirror` |
| `updateVideoQuality` | 更新视频质量。 | `#updateVideoQuality` |
| `startCameraDeviceTest` | 开始摄像头设备测试。 | `#startCameraDeviceTest` |
| `startScreenShare` | 开始屏幕分享。 | `#startScreenShare` |
| `stopScreenShare` | 停止屏幕分享。 | `#stopScreenShare` |
| `stopCameraDeviceTest` | 停止摄像头设备测试。 | `#stopCameraDeviceTest` |
| `screenCaptureStopped` | 屏幕分享停止回调。 | `#screenCaptureStopped` |

### LiveListState
- 响应式数据(3): `currentLive(#currentLive)`, `liveList(#liveList)`, `liveListCursor(#liveListCursor)`
- 接口函数(8)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `createLive` | 创建直播间。 | `#createLive` |
| `joinLive` | 加入直播间。 | `#joinLive` |
| `leaveLive` | 离开直播间。 | `#leaveLive` |
| `endLive` | 结束直播。 | `#endLive` |
| `updateLiveInfo` | 更新直播间信息。 | `#updateLiveInfo` |
| `queryMetaData` | 查询元数据。 | `#queryMetaData` |
| `updateLiveMetaData` | 更新直播间元数据。 | `#updateLiveMetaData` |
| `fetchLiveList` | 获取直播列表。 | `#fetchLiveList` |

### LiveSeatState
- 响应式数据(4): `seatList(#seatList)`, `canvas(#canvas)`, `speakingUsers(#speakingUsers)`, `networkQualities(#networkQualities)`
- 接口函数(14)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `takeSeat` | 用户上麦。 | `#takeSeat` |
| `leaveSeat` | 用户下麦。 | `#leaveSeat` |
| `lockSeat` | 锁定麦位。 | `#lockSeat` |
| `unLockSeat` | 解锁麦位。 | `#unLockSeat` |
| `kickUserOutOfSeat` | 踢用户下麦。 | `#kickUserOutOfSeat` |
| `moveUserToSeat` | 移动用户到指定麦位。 | `#moveUserToSeat` |
| `openRemoteCamera` | 开启远端摄像头。 | `#openRemoteCamera` |
| `closeRemoteCamera` | 关闭远端摄像头。 | `#closeRemoteCamera` |
| `openRemoteMicrophone` | 开启远端麦克风。 | `#openRemoteMicrophone` |
| `closeRemoteMicrophone` | 关闭远端麦克风。 | `#closeRemoteMicrophone` |
| `muteMicrophone` | 静音麦克风。 | `#muteMicrophone` |
| `unmuteMicrophone` | 取消静音麦克风。 | `#unmuteMicrophone` |
| `startPlayStream` | 开始播放流。 | `#startPlayStream` |
| `stopPlayStream` | 停止播放流。 | `#stopPlayStream` |

### LiveAudienceState
- 响应式数据(2): `audienceList(#audienceList)`, `audienceCount(#audienceCount)`
- 接口函数(5)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `fetchAudienceList` | 获取观众列表。 | `#fetchAudienceList` |
| `setAdministrator` | 设置管理员。 | `#setAdministrator` |
| `revokeAdministrator` | 撤销管理员。 | `#revokeAdministrator` |
| `kickUserOutOfRoom` | 踢出房间。 | `#kickUserOutOfRoom` |
| `disableSendMessage` | 禁言用户。 | `#disableSendMessage` |

### LiveMonitorState
- 响应式数据(1): `monitorLiveInfoList(#monitorLiveInfoList)`
- 接口函数(7)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `init` | 初始化监控。 | `#init` |
| `getLiveList` | 获取直播列表。 | `#getLiveList` |
| `closeRoom` | 关闭房间。 | `#closeRoom` |
| `sendMessage` | 发送消息。 | `#sendMessage` |
| `startPlay` | 开始播放。 | `#startPlay` |
| `stopPlay` | 停止播放。 | `#stopPlay` |
| `muteLiveAudio` | 静音直播音频。 | `#muteLiveAudio` |

### CoGuestState
- 响应式数据(4): `candidates(#candidates)`, `connected(#connected)`, `invitees(#invitees)`, `applicants(#applicants)`
- 接口函数(8)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `cancelApplication` | 取消申请。 | `#cancelApplication` |
| `acceptApplication` | 接受申请。 | `#acceptApplication` |
| `rejectApplication` | 拒绝申请。 | `#rejectApplication` |
| `cancelInvitation` | 取消邀请。 | `#cancelInvitation` |
| `acceptInvitation` | 接受邀请。 | `#acceptInvitation` |
| `rejectInvitation` | 拒绝邀请。 | `#rejectInvitation` |
| `disConnect` | 断开连接。 | `#disConnect` |
| `applyForSeat` | 申请上麦。 | `#applyForSeat` |

### CoHostState
- 响应式数据(5): `coHostStatus(#coHostStatus)`, `connected(#connected)`, `applicant(#applicant)`, `invitees(#invitees)`, `candidates(#candidates)`
- 接口函数(5)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `requestHostConnection` | 请求主播连麦。 | `#requestHostConnection` |
| `cancelHostConnection` | 取消主播连麦。 | `#cancelHostConnection` |
| `acceptHostConnection` | 接受主播连麦。 | `#acceptHostConnection` |
| `rejectHostConnection` | 拒绝主播连麦。 | `#rejectHostConnection` |
| `exitHostConnection` | 退出主播连麦。 | `#exitHostConnection` |

### BattleState
- 响应式数据(3): `currentBattleInfo(#currentBattleInfo)`, `battleUsers(#battleUsers)`, `battleScore(#battleScore)`
- 接口函数(5)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `requestBattle` | 请求 PK。 | `#requestBattle` |
| `cancelBattleRequest` | 取消 PK 请求。 | `#cancelBattleRequest` |
| `acceptBattle` | 接受 PK。 | `#acceptBattle` |
| `rejectBattle` | 拒绝 PK。 | `#rejectBattle` |
| `exitBattle` | 退出 PK。 | `#exitBattle` |

### BarrageState
- 响应式数据(1): `messageList(#messageList)`
- 接口函数(3)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `sendTextMessage` | 发送文本消息。 | `#sendTextMessage` |
| `sendCustomMessage` | 发送自定义消息。 | `#sendCustomMessage` |
| `appendLocalTip` | 添加本地提示。 | `#appendLocalTip` |

### VideoMixerState
- 响应式数据(4): `publishVideoQuality(#publishVideoQuality)`, `isVideoMixerEnabled(#isVideoMixerEnabled)`, `mediaSourceList(#mediaSourceList)`, `activeMediaSource(#activeMediaSource)`
- 接口函数(16)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `getVideoDataByQuality` | 根据质量获取视频数据。 | `#getVideoDataByQuality` |
| `getSizeByQuality` | 根据质量获取尺寸。 | `#getSizeByQuality` |
| `getSizeByResolution` | 根据分辨率获取尺寸。 | `#getSizeByResolution` |
| `transformTUIVideoQualityToTRTCVideoResolution` | 转换视频质量到 TRTC 分辨率。 | `#transformTUIVideoQualityToTRTCVideoResolution` |
| `transformTRTCVideoResolutionToTUIVideoQuality` | 转换 TRTC 分辨率到视频质量。 | `#transformTRTCVideoResolutionToTUIVideoQuality` |
| `transformTRTCVideoResModeToTUIVideoResMode` | 转换 TRTC 分辨率模式。 | `#transformTRTCVideoResModeToTUIVideoResMode` |
| `changeActiveMediaSource` | 切换活跃媒体源。 | `#changeActiveMediaSource` |
| `updateVideoQuality` | 更新视频质量。 | `#updateVideoQuality` |
| `getDefaultLayoutByMediaSource` | 根据媒体源获取默认布局。 | `#getDefaultLayoutByMediaSource` |
| `addMediaSource` | 添加媒体源。 | `#addMediaSource` |
| `updateMediaSource` | 更新媒体源。 | `#updateMediaSource` |
| `removeMediaSource` | 移除媒体源。 | `#removeMediaSource` |
| `enableLocalVideoMixer` | 启用本地视频混流。 | `#enableLocalVideoMixer` |
| `clearMediaSource` | 清空所有媒体源。 | `#clearMediaSource` |
| `initMediaSourceManager` | 初始化媒体源管理器。 | `#initMediaSourceManager` |
| `initVideoMixerState` | 初始化视频混流状态。 | `#initVideoMixerState` |

### VirtualBackgroundState
- 响应式数据(1): `virtualBackgroundConfig(#virtualBackgroundConfig)`
- 接口函数(4)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `initVirtualBackground` | 初始化虚拟背景。 | `#initVirtualBackground` |
| `saveVirtualBackground` | 保存虚拟背景。 | `#saveVirtualBackground` |
| `isSupported` | 检查是否支持。 | `#isSupported` |
| `setVirtualBackground` | 设置虚拟背景。 | `#setVirtualBackground` |

### ASRState
- 响应式数据(2): `recentTranscripts(#recentTranscripts)`, `transcriptHistory(#transcriptHistory)`
- 接口函数(3)

| Method | 说明 | Anchor |
| --- | --- | --- |
| `setRecentTranscriptsDuration` | 设置最近转录时长。 | `#setRecentTranscriptsDuration` |
| `clearHistory` | 清空转录历史记录。 | `#clearHistory` |
| `exportTranscripts` | 导出转录内容。 | `#exportTranscripts` |
