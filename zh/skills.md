> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

RTC Skills 为腾讯云实时音视频场景提供结构化指引。它可以将自然语言需求转为可执行步骤，并帮助 Agent 按正确顺序调用对应的 MCP 工具。使用 RTC Skills，让 AI Agent 更高效地完成 Chat、Call、Live、Room 的 MCP 集成。

## 前置条件

使用 Skills 前，请先完成 MCP Server 配置，MCP 配置说明。

## 安装

安装 RTC Skills 全量包，安装过程中需要您选择使用的工具（CodeBuddy / Trae / Cursor / Codex / Claude Code / Claude Desktop 等）：
``` bash
npx skills add Tencent-RTC/tencent-rtc-skills
```

如需手动导入，可通过 GitHub 克隆：
``` bash
git clone https://github.com/Tencent-RTC/tencent-rtc-skills.git ~/.skills/tencent-rtc-skills
```

### 优势
- **结构化知识注入**：将领域知识直接注入 Agent 上下文，降低歧义，提升输出一致性。

- **与 Prompt 工程深度配合：**内置标准化执行流程，帮助 Agent 按可复用路径完成集成。

- **零运行时依赖**：Skills 为静态文本资源，无需额外启动服务。

- **场景覆盖完整：**单一技能包覆盖 Chat / Call / Live / Room 集成场景。

## 参考

[Tencent RTC Skills](https://github.com/Tencent-RTC/tencent-rtc-skills)

[MCP NPM](https://www.npmjs.com/package/@tencentcloud/sdk-mcp)

