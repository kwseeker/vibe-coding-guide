# Codex

入门参考：
- [Codex Full Course 2026: The NEW Best AI Coding Tool](https://www.youtube.com/watch?v=KXIdYEdOPys)

## 常用内容

### 命令

- /init
	让 Codex 通读当前文件夹，会自动分析项目信息并将内容保存到 AGENTS.md。
	这个文件也需要随着项目迭代增减内容以保持和项目内容的一致性。
- /compact
	压缩上下文，虽然上下文占用达到阈值后会自动压缩，不过也可以通过这个命令进行手动压缩。
- /new
	新开一个对话框。
- /approvals
	批准的意思，用于设置权限级别，包括 Read Only、Auto、Full Access。
- /mcp
	列出所有安装的mcp工具。
- /review
	审查当前代码修改，参考[Build Code Review with the Codex SDK](https://developers.openai.com/cookbook/examples/codex/build_code_review_with_codex_sdk?utm_source=chatgpt.com)。
- 支持自定义命令
	创建 `~/.codex/prompts/<cmd-name>.md` 文件，填写提示词说明命令的功能和流程，内部可以使用参数占位符 `$<n>`。
