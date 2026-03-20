> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

MCP (Model Context Protocol) 标准化了应用向 LLM 提供上下文的方式。本文介绍如何使用 MCP 集成实时音视频 TRTC 和即时通信 IM 的 UIKit 组件，仅支持 `stdio`模式传输。

## MCP 功能
- **智能集成**：覆盖 Chat/Call/Live/Room UIKit 和 底层 SDK（WebRTC 和 Native RTC） 集成工具。

- **功能详情查询：** 提供 UI 组件与 API 使用详情。

- **常见问题查询：**常见问题查询工具。

- **测试 UserSig 生成：** UserSig 生成工具（仅用于测试）。

- **多平台支持：**完整的 Web、iOS、Android、Flutter 文档支持。

## 前置条件

MCP 已经发布在 [NPM](https://www.npmjs.com/package/@tencentcloud/sdk-mcp) 上，您可以通过 `npx` 安装在任何支持 MCP 的 AI 编辑器中。

使用 MCP 您需要先在腾讯云控制台创建应用，并获取以下凭证：
| 凭证 | 获取位置 | 用途 |
| --- | --- | --- |
| `SDKAppID` | [IM 控制台](https://console.cloud.tencent.com/im) | MCP 环境变量 |
| `SecretKey` | IM 控制台 → 应用管理 | MCP 环境变量，生成 UserSig （仅用于测试） |

## MCP 配置

### 在 AI 编辑器中配置

在您使用的 AI 编辑器（CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop 等）配置 MCP。

【CodeBuddy】

设置 > 选择 MCP > 配置 MCP > 打开 mcp.json。
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

【Trae】

设置 > 选择 MCP > 手动添加 MCP > 原始配置(JSON) > 打开 mcp.json。
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

【Cursor】

设置 > 选择 Tools & MCP > Add Custom MCP  > 打开 mcp.json。
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

【Codex】
1. 运行以下命令配置 MCP，将 SDKAPPID 和 SECRETKEY 替换为真实的应用信息。

``` bash
codex mcp add tencentcloud-sdk-mcp \
  --env SDKAPPID=YOUR_SDK_APP_ID --env SECRETKEY=YOUR_SECRET_KEY \
  -- npx -y @tencentcloud/sdk-mcp@latest
```

如需项目级配置，可追加 `--scope project`，写入项目根目录的 `.mcp.json`。
2. 运行 `codex mcp list` 验证 MCP 是否配置成功，列表中出现 `tencentcloud-sdk-mcp` 表示配置成功。

【Claude Code】
3. 运行以下命令配置 MCP，将 SDKAPPID 和 SECRETKEY 替换为真实的应用信息。

``` bash
claude mcp add tencentcloud-sdk-mcp \
  --env SDKAPPID=YOUR_SDK_APP_ID --env SECRETKEY=YOUR_SECRET_KEY \
  -- npx -y @tencentcloud/sdk-mcp@latest
```

如需项目级配置，可追加 `--scope project`，写入项目根目录的 `.mcp.json`。
4. 运行 `claude mcp list` 验证 MCP 是否配置成功，列表中出现 `tencentcloud-sdk-mcp`表示配置成功。

【Claude Desktop】

设置 > 选择 Developer > Edit Config > claude_desktop_config.json 。
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

### 环境变量
- **SDKAPPID:** 实时音视频 TRTC 和即时通信 IM 应用 ID。

- **SECRETKEY:** SDKAPPID 对应的密钥，用于生成测试 UserSig。

### 验证 MCP

直接复制下面 Prompt 到 IDE 对话框，验证 MCP 的可用性：
``` plaintext
使用 tencentcloud-sdk-mcp 工具获取 test001 的 userSig
```

当 Agent 输出 SDKAppID、userID、userSig 信息说明 MCP 配置成功。

## 附录：全局手动安装

如果需要全局安装，可以在命令行执行以下命令：
``` javascript
npx -y @tencentcloud/sdk-mcp@latest
```
