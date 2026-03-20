> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

本文介绍用 AI 编辑器快速跑通一个可用的腾讯云实时音视频 TRTC 和即时通信 IM 应用。我们提供了丰富的文档资源来提升您的开发体验。

RTC MCP Server

RTC Skills

快速开始指南

OpenClaw 指南

## 准备账号与凭证（必做）

你需要先在腾讯云控制台创建应用，并获取以下凭证：
| 凭证 | 获取位置 | 用途 |
| --- | --- | --- |
| `SDKAppID` | [IM 控制台](https://console.cloud.tencent.com/im) | SDK 初始化、MCP 环境变量 |
| `SecretKey` | IM 控制台 → 应用管理 | 生成 UserSig（仅用于测试）、MCP 环境变量 |

## 配置 MCP（必做）

在你的 AI 编辑器（CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop 等）里配置 MCP：
``` json
{
  "mcpServers": {
    "tencentcloud-sdk-mcp": {
      "command": "npx",
      "args": ["-y", "@tencentcloud/sdk-mcp@latest"],
      "env": {
        "SDKAPPID": "YOUR_SDKAPPID",
        "SECRETKEY": "YOUR_SECRET_KEY"
      }
    }
  }
}
```

查看 CodeBuddy、Trae 、Cursor、Codex、Claude Code、Claude Desktop 等的详细安装说明，请点击 MCP 配置。

## 安装 Skills（推荐）

运行以下命令安装 Skills，CLI 会自动识别你正在使用的工具（CodeBuddy / Trae / Cursor / Codex/ Claude Code / Claude Desktop 等）：
``` bash
npx skills add Tencent-RTC/tencent-rtc-skills
```

建议安装 Skills，以提高 Agent 调用 MCP 工具的稳定性与准确率。详细说明见：Tencent RTC Skills。

## 快速开始指南

直接复制下面 Prompt 到 IDE 对话框，集成完整的 Chat UIKit ：
``` markdown
任务：集成 Chat UIKit 实现类微信的聊天应用（使用 full-featured 模式并完成最小可运行）

执行要求：
1. 自动识别框架（React/Vue3/Flutter/Android/iOS）.
2. 无法识别时，需要调用 `present_framework_choice` 给用户选择。
3. 按平台调用 MCP 工具：
   - Web: `get_web_chat_uikit_integration`
   - Native: `get_native_chat_uikit_integration`
4. 参数：
   - `framework`: 按实际框架填写
   - `integrationMode`: `full-featured`
5. 调用 `get_usersig` 获取 2 个测试 userID 的凭证，完成 SDK 初始化与登录。
6. 生成完整聊天入口（会话列表 + 聊天窗口 + 联系人/个人中心）和登录页，用户可选择测试账号登录，并自动补齐路由或页面注册。
7. 启动项目并输出：修改文件、运行命令、验证步骤。

验收清单：
- SDK 初始化成功
- 用户登录成功
- 会话列表可见
- 可以发送和接收消息

常见问题排查：
- userSig 无效：重新调用 `get_usersig`
- 登录失败：检查 `SDKAPPID/SECRETKEY` 环境变量
- 消息不显示：检查会话 ID 格式（例如 `C2Cuserxxx`）

约束：
- 不使用过时 API
- 生产环境必须改为服务端签发 UserSig
```

## OpenClaw 指南

如果您想在即时通信 IM 应用中接入 OpenClaw，您可以参考OpenClaw 快速接入指南。

## 扩展场景（可选）

在跑通 Chat UIKit 集成后，您可以根据需要使用下面的 Prompt。

【构建音视频通话应用】
``` markdown
任务：在当前项目中集成 CallKit（先跑通 1v1 语音通话和视频通话）

执行要求：
1. 自动识别框架（React/Vue3/Flutter/Android/iOS）.
2. 无法识别时，需要调用 `present_framework_choice` 给用户选择。
3. 按平台调用 MCP 工具：
   - Web: `get_web_call_uikit_integration`
   - Native: `get_native_call_uikit_integration`
4. 参数：`framework`。
5. 调用 `get_usersig` 完成初始化和登录。
6. 生成最小可运行的呼叫入口和来电处理页面。

验收清单：
- 初始化与登录成功
- 能发起 1v1 视频通话
- 能收到来电并接听

常见问题排查：
- 60001：重新生成 userSig
- 通话失败：检查网络与设备权限
```

【构建直播互动应用】
``` markdown
任务：在当前项目中集成 LiveKit（先跑通视频直播基础流程）

执行要求：
1. 自动识别框架（Web Vue3/Flutter/Android/iOS）。
2. 无法识别时，需要调用 `present_framework_choice` 给用户选择。
3. 按平台调用 MCP 工具：
   - Web: `get_web_live_uikit_integration`
   - Native: `get_native_live_uikit_integration`
4. 参数：`framework`。
5. 调用 `get_usersig` 完成初始化和登录。
6. 生成主播开播页、观众观看页、基础互动入口。

验收清单：
- 主播可开播
- 观众可观看
- 基础互动可用（如连麦入口）

常见问题排查：
- 黑屏：检查设备权限与渲染初始化
- 无声：检查音频权限与设备路由

```

【构建视频会议应用】
``` markdown
任务：在当前项目中集成 RoomKit（先跑通 quick-start 模式）

执行要求：
1. 自动识别框架（Web Vue3）。
2. 无法识别时，需要调用 `present_framework_choice` 给用户选择。
3. 按平台调用 MCP 工具：
   - Web: `get_web_room_uikit_integration`
   - Native: `get_native_room_uikit_integration`
4. 参数：`framework`。
5. 调用 `get_usersig` 完成初始化和登录。
6. 生成会议入口页、会议房间页、路由注册。

验收清单：
- 设备检测正常
- 可入会并看到音视频
- 屏幕共享可用（平台支持时）

常见问题排查：
- 进房失败：检查 roomId 与登录状态
- 无画面/无声音：检查摄像头麦克风权限
```

## 参考

[MCP NPM](https://www.npmjs.com/package/@tencentcloud/sdk-mcp)

[Tencent RTC Skills](https://github.com/Tencent-RTC/tencent-rtc-skills)

