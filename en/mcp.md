> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

MCP (Model Context Protocol) standardizes how applications provide context to LLMs. This guide describes how to use MCP to integrate TRTC (Real-Time Communication) and Chat (Instant Messaging) UIKit components. Only `stdio` transport mode is supported.

## MCP Features
- **Smart Integration**: Covers Chat/Call/Live/Room UIKit and underlying SDK (WebRTC and Native RTC) integration tools.

- **Feature Details Query:** Provides UI component and API usage details.

- **FAQ Query:** FAQ query tool.

- **Test UserSig Generation:** UserSig generation tool (for testing only).

- **Multi-Platform Support:** Complete documentation support for Web, iOS, Android, and Flutter.

## Prerequisites

MCP is published on [NPM](https://www.npmjs.com/package/@tencent-rtc/mcp). You can install it via `npx` in any AI editor that supports MCP.

To use MCP, you need to create an application in the Tencent Cloud console first and obtain the following credentials:
| Credential | Where to Get | Purpose |
| --- | --- | --- |
| `SDKAppID` | [Chat Console](https://console.trtc.io/) | MCP environment variable |
| `SecretKey` | Chat Console → App Management | MCP environment variable, generate UserSig (for testing only) |

## MCP Configuration

### Configure in AI Editor

Configure MCP in your AI editor (CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop, etc.).

[CodeBuddy]

Settings > Select MCP > Configure MCP > Open mcp.json.
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

[Trae]

Settings > Select MCP > Manually Add MCP > Raw Configuration (JSON) > Open mcp.json.
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

[Cursor]

Settings > Select Tools & MCP > Add Custom MCP > Open mcp.json.
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

[Codex]
1. Run the following command to configure MCP. Replace SDKAPPID and SECRETKEY with your actual application credentials.

``` bash
codex mcp add tencent-rtc \
  --env SDKAPPID=YOUR_SDK_APP_ID --env SECRETKEY=YOUR_SECRET_KEY \
  -- npx -y @tencent-rtc/mcp@latest
```

For project-level configuration, append `--scope project` to write to `.mcp.json` in the project root.
2. Run `codex mcp list` to verify that MCP is configured successfully. If `tencent-rtc` appears in the list, the configuration is successful.

[Claude Code]
3. Run the following command to configure MCP. Replace SDKAPPID and SECRETKEY with your actual application credentials.

``` bash
claude mcp add tencent-rtc \
  --env SDKAPPID=YOUR_SDK_APP_ID --env SECRETKEY=YOUR_SECRET_KEY \
  -- npx -y @tencent-rtc/mcp@latest
```

For project-level configuration, append `--scope project` to write to `.mcp.json` in the project root.
4. Run `claude mcp list` to verify that MCP is configured successfully. If `tencent-rtc` appears in the list, the configuration is successful.

[Claude Desktop]

Settings > Select Developer > Edit Config > claude_desktop_config.json.
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

### Environment Variables
- **SDKAPPID:** The application ID for TRTC (Real-Time Communication) and Chat (Instant Messaging).

- **SECRETKEY:** The secret key corresponding to the SDKAppID, used to generate test UserSig.

### Verify MCP

Copy the following Prompt directly into your IDE chat box to verify MCP availability:
``` plaintext
Use the tencent-rtc tool to get the userSig for test001
```

When the Agent outputs SDKAppID, userID, and userSig information, it indicates that MCP is configured successfully.

## Appendix: Global Manual Installation

If you need a global installation, run the following command in the terminal:
``` javascript
npx -y @tencent-rtc/mcp@latest
```
