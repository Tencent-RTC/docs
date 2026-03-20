> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

RTC Skills provides structured guidance for Tencent Cloud real-time audio/video scenarios. It translates natural language requirements into executable steps and helps the Agent call the corresponding MCP tools in the correct order. Use RTC Skills to enable AI Agents to complete Chat, Call, Live, and Room MCP integrations more efficiently.

## Prerequisites

Before using Skills, please complete the MCP Server configuration first. See MCP Configuration Guide.

## Installation

Install the full RTC Skills package. During installation, you will be prompted to select the tool you are using (CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop, etc.):
``` bash
npx skills add Tencent-RTC/tencent-rtc-skills
```

For manual import, you can clone from GitHub:
``` bash
git clone https://github.com/Tencent-RTC/tencent-rtc-skills.git ~/.skills/tencent-rtc-skills
```

### Advantages
- **Structured Knowledge Injection**: Injects domain knowledge directly into the Agent context, reducing ambiguity and improving output consistency.

- **Deep Integration with Prompt Engineering:** Built-in standardized execution flows help the Agent complete integrations along reusable paths.

- **Zero Runtime Dependencies**: Skills are static text resources that require no additional services to run.

- **Complete Scenario Coverage:** A single skill package covers Chat / Call / Live / Room integration scenarios.

## References

[Tencent RTC Skills](https://github.com/Tencent-RTC/tencent-rtc-skills)

[MCP NPM](https://www.npmjs.com/package/@tencent-rtc/mcp)

