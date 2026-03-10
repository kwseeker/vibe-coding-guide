# Skill 安装

vercel labs 开发了一个名为 [skills](https://github.com/vercel-labs/skills) 的命令行工具。
支持 40 种 agent skills 生态系统，包括 OpenCode、Claude Code、Codex、Cursor、Antigravity 等。
可以很方便地安装技能。
比如： `npx skills add https://github.com/firecrawl/cli --skill firecrawl`，表示从 `https://github.com/firecrawl/cli` 安装技能，并将技能命名为 firecrawl 。
安装过程中会根据 Agent 生态调整目录。
