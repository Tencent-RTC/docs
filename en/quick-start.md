> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

This guide helps you quickly build a working Tencent Cloud Real-Time Communication (TRTC) and Instant Messaging (Chat) application using an AI editor. We provide rich documentation resources to enhance your development experience.

RTC MCP Server

RTC Skills

Quick Start Guide

OpenClaw Guide

## Prepare Account & Credentials (Required)

You need to create an application in the Tencent Cloud console first and obtain the following credentials:
| Credential | Where to Get | Purpose |
| --- | --- | --- |
| `SDKAppID` | [Chat Console](https://console.trtc.io/) | SDK initialization, MCP environment variable |
| `SecretKey` | Chat Console → App Management | Generate UserSig (for testing only), MCP environment variable |

## Configure MCP (Required)

Configure MCP in your AI editor (CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop, etc.):
``` json
{
  "mcpServers": {
    "tencent-rtc": {
      "command": "npx",
      "args": ["-y", "@tencent-rtc/mcp@latest"],
      "env": {
        "SDKAPPID": "YOUR_SDKAPPID",
        "SECRETKEY": "YOUR_SECRET_KEY"
      }
    }
  }
}
```

For detailed installation instructions for CodeBuddy, Trae, Cursor, Codex, Claude Code, Claude Desktop, etc., see MCP Configuration.

## Install Skills (Recommended)

Run the following command to install Skills. The CLI will automatically detect the tool you are using (CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop, etc.):
``` bash
npx skills add Tencent-RTC/tencent-rtc-skills
```

Installing Skills is recommended to improve the stability and accuracy of Agent MCP tool invocations. For details, see: Tencent RTC Skills.

## Quick Start Guide

Copy the following Prompt directly into your IDE chat box to integrate the full Chat UIKit:
``` markdown
Task: Integrate Chat UIKit to build a WeChat-like chat application (use full-featured mode and achieve minimum runnable state)

Requirements:
1. Auto-detect framework (React/Vue3/Flutter/Android/iOS).
2. If detection fails, call `present_framework_choice` to let the user choose.
3. Call MCP tools based on platform:
   - Web: `get_web_chat_uikit_integration`
   - Native: `get_native_chat_uikit_integration`
4. Parameters:
   - `framework`: fill in the actual framework
   - `integrationMode`: `full-featured`
5. Call `get_usersig` to obtain credentials for 2 test userIDs, complete SDK initialization and login.
6. Generate a complete chat entry (conversation list + chat window + contacts/profile) and login page. Users can select a test account to log in, and routes or page registrations are auto-completed.
7. Start the project and output: modified files, run commands, verification steps.

Acceptance Checklist:
- SDK initialization succeeds
- User login succeeds
- Conversation list is visible
- Messages can be sent and received

Troubleshooting:
- Invalid userSig: Re-call `get_usersig`
- Login failure: Check `SDKAPPID/SECRETKEY` environment variables
- Messages not showing: Check conversation ID format (e.g. `C2Cuserxxx`)

Constraints:
- Do not use deprecated APIs
- Production environment must switch to server-side UserSig generation
```

## Extended Scenarios (Optional)

After successfully running the Chat UIKit integration, you can use the following Prompts as needed.

[Build Audio/Video Call Application]
``` markdown
Task: Integrate CallKit into the current project (first get 1v1 voice call and video call working)

Requirements:
1. Auto-detect framework (React/Vue3/Flutter/Android/iOS).
2. If detection fails, call `present_framework_choice` to let the user choose.
3. Call MCP tools based on platform:
   - Web: `get_web_call_uikit_integration`
   - Native: `get_native_call_uikit_integration`
4. Parameters: `framework`.
5. Call `get_usersig` to complete initialization and login.
6. Generate minimum runnable call entry and incoming call handling pages.

Acceptance Checklist:
- Initialization and login succeed
- Can initiate a 1v1 video call
- Can receive and answer incoming calls

Troubleshooting:
- 60001: Regenerate userSig
- Call failure: Check network and device permissions
```

[Build Live Streaming Application]
``` markdown
Task: Integrate LiveKit into the current project (first get the basic video live streaming flow working)

Requirements:
1. Auto-detect framework (Web Vue3/Flutter/Android/iOS).
2. If detection fails, call `present_framework_choice` to let the user choose.
3. Call MCP tools based on platform:
   - Web: `get_web_live_uikit_integration`
   - Native: `get_native_live_uikit_integration`
4. Parameters: `framework`.
5. Call `get_usersig` to complete initialization and login.
6. Generate host streaming page, audience viewing page, and basic interaction entry.

Acceptance Checklist:
- Host can start streaming
- Audience can watch
- Basic interactions are available (e.g. co-guest entry)

Troubleshooting:
- Black screen: Check device permissions and render initialization
- No audio: Check audio permissions and device routing

```

[Build Video Conference Application]
``` markdown
Task: Integrate RoomKit into the current project (first get the quick-start mode working)

Requirements:
1. Auto-detect framework (Web Vue3).
2. If detection fails, call `present_framework_choice` to let the user choose.
3. Call MCP tools based on platform:
   - Web: `get_web_room_uikit_integration`
   - Native: `get_native_room_uikit_integration`
4. Parameters: `framework`.
5. Call `get_usersig` to complete initialization and login.
6. Generate conference entry page, conference room page, and route registration.

Acceptance Checklist:
- Device detection works properly
- Can join a meeting and see audio/video
- Screen sharing works (when supported by the platform)

Troubleshooting:
- Failed to join room: Check roomId and login status
- No video/audio: Check camera and microphone permissions
```

## References

[MCP NPM](https://www.npmjs.com/package/@tencent-rtc/mcp)

[Tencent RTC Skills](https://github.com/Tencent-RTC/tencent-rtc-skills)

