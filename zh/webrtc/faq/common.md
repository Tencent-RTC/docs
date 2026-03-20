> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 概览

本文档主要介绍腾讯云实时音视频（TRTC）服务的两种鉴权方式，目前，腾讯云实时音视频（TRTC）、即时通信（IM）等服务都采用了 UserSig 的鉴权方式。UserSig 是腾讯云设计的一种安全保护签名，目的是为了阻止恶意攻击者盗用您的云服务使用权。如果您要使用这些基础云服务，就需要在 SDK 初始化或登录函数中提供 SDKAppID，UserID 和 UserSig 三个关键信息，具体含义如下：
- SDKAppID 用于标识您的应用。

- UserID 用于标识您的用户。

- UserSig 则是基于前两者计算出的安全签名，它由 **HMAC SHA256** 加密算法计算得出。只要攻击者不能伪造 UserSig，就无法盗用您的云服务流量。

## 在调试阶段如何计算 UserSig？

如果您希望快速跑通 Demo，了解 TRTC SDK 相关能力，您可以通过 客户端示例代码 和 控制台 两种方法计算获取 UserSig，具体请参见以下介绍。

> **不安全：**
> 

> 注意，如下两种 UserSig 获取计算方案仅适用于调试，如果产品要正式上线，**不推荐**采用这种方案，因为客户端代码（尤其是 Web 端）中的 SECRETKEY 很容易被反编译逆向破解。一旦您的密钥泄露，攻击者就可以盗用您的腾讯云流量。
> 

### 客户端计算 UserSig

#### **1. 获取 SDKAPPID 和密钥**：
- 登录**实时音视频控制台** > [应用管理](https://console.cloud.tencent.com/trtc/app)。

- 单击您需查看的 SDKAppID 对应的**应用信息**，单击进入**应用概览**。

- **点击SDK密钥查看**密钥，即可获取用于计算 UserSig 的加密密钥。

- 单击**复制密钥**，可将密钥拷贝到剪贴板中。

   

#### **2. 计算 UserSig：**

为了方便客户端使用，我们提供各平台计算 UserSig 的源码文件，您可直接下载使用：
| Android | iOS | Web | 微信小程序 | Windows（C++） | Windows（C#） | Flutter | Mac |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [GitHub](https://github.com/LiteAVSDK/TRTC_Android/tree/main/TRTC-API-Example/Debug/src/main/java/com/tencent/trtc/debug/GenerateTestUserSig.java) | [GitHub](https://github.com/LiteAVSDK/TRTC_iOS/tree/main/TRTC-API-Example-OC/Debug/GenerateTestUserSig.h) | [GitHub](https://github.com/LiteAVSDK/TRTC_Web/blob/main/quick-demo-js/js/libs/generateTestUserSig.js) | [GitHub](https://github.com/LiteAVSDK/Live_WXMini/blob/main/TRTCScenesDemo/TUICallKit-WX-Demo/debug/GenerateTestUserSig.js) | [GitHub](https://github.com/LiteAVSDK/TRTC_Windows/blob/main/TRTC-API-Example-C%2B%2B/TRTC-API-Example-Qt/src/Util/defs.h) | [GitHub](https://github.com/LiteAVSDK/TRTC_Windows/blob/main/TRTC-API-Example-CSharp/TRTC-API-Example-CSharp/GenerateTestUserSig.cs) | [GitHub](https://github.com/Tencent-RTC/TRTC_Flutter/blob/master/TRTC-API-Example/lib/debug/generate_test_user_sig.dart) | [GitHub](https://github.com/LiteAVSDK/TRTC_Mac/tree/main/OCDemo/TRTCDemo/TRTC/GenerateTestUserSig.h) |

示例代码如下（当然您也可以参考我们各产品的 Demo 工程，详见各产品的开发文档）：

【Android】
``` java
// Step1: 导入源码文件
import com.xxx.xxx.GenerateTestUserSig;

// Step2：填写上一步骤中获取到的 SDKAppID，SDK 密钥
GenerateTestUserSig.SDKAPPID = xxxxxx;
GenerateTestUserSig.SECRETKEY = "xxxxxx";

// Step3：根据 userID，生成 userSig
String userSig = GenerateTestUserSig.genTestUserSig("userID");
```

【iOS】
``` objectivec
// Step1: 导入头文件
#import "GenerateTestUserSig.h"

// Step2: 填写上一步骤中获取到的 SDKAppID，SDK 密钥
[GenerateTestUserSig setSDKAPPID:xxxxxx];
[GenerateTestUserSig setSECRETKEY:@"xxxxxx"];

// Step3：根据 userID，生成 userSig
NSString *userSig = [GenerateTestUserSig genTestUserSig:@"userID"];

```

【Web】
``` javascript
// Step1: 导入模块
<script src='js/libs/lib-generate-test-usersig.min.js'></script>
<script src='js/libs/generateTestUserSig.js'></script>

// Step2：填写上一步骤中获取到的 SDKAppID，SDK 密钥、输入自定义的userID，生成 userSig
const {sdkAppId, userSig } = genTestUserSig({
	sdkAppId: xxxxxx, 
	userId: 'xxxxxx',
	sdkSecretKey: 'xxxxxx',
}
```

【微信小程序】
``` javascript
// Step1: 导入模块
import { genTestUserSig } from 'GenerateTestUserSig';

// Step2：填写上一步骤中获取到的 SDKAppID，SDK 密钥、输入自定义的userID，生成 userSig
const {userSig } = genTestUserSig({
	sdkAppId: xx, 
	userId: 'xxxxxx',
	sdkSecretKey: 'xxxxxx'
 })
```

【Window（C++）】
``` cpp
// Step1: 导入头文件
#include "GenerateTestUserSig.h"

// Step2：填写上一步骤中获取到的 SDKAppID，SDK 密钥
const int SDKAPPID = xxxxxx;
const char* SECRETKEY = "xxxxxx";

// Step3：根据 userID，生成 userSig
const char* userSig = GenerateTestUserSig::genTestUserSig("userID", SDKAPPID, SECRETKEY);
```

【Window（C#）】
``` csharp
// Step1: 导入头文件
using GenerateTestUserSig;

// Step2: 填写上一步骤中获取到的 SDKAppID 和 SDK 密钥
GenerateTestUserSig.SDKAPPID = xxxxxx; 
GenerateTestUserSig.SECRETKEY = "xxxxxx";

// Step3：根据 userID，生成 userSig
string userSig = GenerateTestUserSig.GetInstance().GenTestUserSig("userID");
```

【Flutter】
``` django
// Step1: 导入源码文件
import 'package:xxx/GenerateTestUserSig.dart';

// Step2: 填写上一步骤中获取到的 SDKAppID，SDK 密钥
GenerateTestUserSig.SDKAPPID = xxxxxx;
GenerateTestUserSig.SECRETKEY = "xxxxxx";

// Step3：根据 userID，生成 userSig
String userSig = GenerateTestUserSig.genTestUserSig("userID");
```

【Mac】
``` objectivec
// Step1: 导入头文件
#import "GenerateTestUserSig.h"

// Step2: 填写上一步骤中获取到的 SDKAppID，SDK 密钥
[GenerateTestUserSig setSDKAPPID:xxxxxx];
[GenerateTestUserSig setSECRETKEY:@"xxxxxx"];

// Step3：根据 userID，生成 userSig
NSString *userSig = [GenerateTestUserSig genTestUserSig:@"userID"];
```

### 控制台获取 UserSig
- 登录**实时音视频控制台**，进入**开发辅助** > [UserSig生成&校验](https://console.cloud.tencent.com/trtc/usersigtool)。

- 在签名（UserSig）生成工具下，选择对应的 SDKAppID 和 UserID。

- 单击**生成签名(UserSig)**，即可计算得到对应的 UserSig。

   

## 在正式运行阶段如何计算 UserSig？

业务正式运行阶段，TRTC 提供安全等级更高的服务端计算 UserSig 的方案，可以最大限度地保障计算 UserSig 用的密钥不被泄露，因为攻破一台服务器的难度要高于逆向一款 App。具体的实现流程如下：

1. 您的 App 在调用 SDK 的初始化函数之前，首先要向您的服务器请求 UserSig。

2. 您的服务器根据 SDKAppID 和 UserID 计算 UserSig，计算源码见文档前半部分。

3. 服务器将计算好的 UserSig 返回给您的 App。

4. 您的 App 将获得的 UserSig 通过特定 API 传递给 SDK。

5. SDK 将 `SDKAppID + UserID + UserSig` 提交给腾讯云服务器进行校验。

6. 腾讯云校验 UserSig，确认合法性。

7. 校验通过后，会向 TRTCSDK 提供实时音视频服务。

为了简化您的实现过程，我们提供了多个语言版本的 UserSig 计算源代码及其示例：
| 语言版本 | 签名算法 | 源代码 | 使用示例 |
| --- | --- | --- | --- |
| Java | HMAC-SHA256 | [genSig](https://github.com/tencentyun/tls-sig-api-v2-java/blob/master/src/main/java/com/tencentyun/TLSSigAPIv2.java) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-java) |
| GO | HMAC-SHA256 | [GenSig](https://github.com/tencentyun/tls-sig-api-v2-golang/blob/master/tencentyun/TLSSigAPI.go) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-golang) |
| PHP | HMAC-SHA256 | [genSig](https://github.com/tencentyun/tls-sig-api-v2-php/blob/master/src/TLSSigAPIv2.php) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-php) |
| Node.js | HMAC-SHA256 | [genSig](https://github.com/tencentyun/tls-sig-api-v2-node/blob/master/TLSSigAPIv2.js) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-node) |
| Python | HMAC-SHA256 | [genSig](https://github.com/tencentyun/tls-sig-api-v2-python/blob/master/TLSSigAPIv2.py) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-python) |
| C# | HMAC-SHA256 | [GenSig](https://github.com/tencentyun/tls-sig-api-v2-cs/blob/master/tls-sig-api-v2-cs/TLSSigAPIv2.cs) | [GitHub](https://github.com/tencentyun/tls-sig-api-v2-cs) |

## 常见问题

### **SDK 密钥如何切换为非对称密钥？**

若查看密钥时只能获取公钥和私钥信息，可点击下图所示位置切换为最新的非对称密钥，请注意：切换密钥前请确保线上业务已停用。

