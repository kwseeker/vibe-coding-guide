# Antigravity

[Docs](https://antigravity.google/docs/agent-modes-settings)

历史会话消息存储位置：`~/.gemini/antigravity/brain/[会话的 UUID 目录]/`。

**功能**：
- Rules
	每次执行任务都必须遵循的规则，分为全局规则（~/.gemini/GEMINI.md）和项目本地规则（.agent/rules）。
- Workflows
	使用提示词定义的一系列步骤，Markdown文件格式，用于执行重复性任务。
	其他 Agent 称作命令（Commands）。
- Skills
	可复用的知识包，用于拓展 Agent 的能力，包括：
	- 关于如何处理特定任务的指导
	- 最佳实践与应遵循的惯例
	- Agent 可使用的可选脚本和资源
	Skills 同样分为全局 Skills（`~/.gemini/antigravity/skills/<someone-skill>`）和 项目本地 Skills（`.agent/skills/<someone-skill>`）。
	固定命名为 `SKILL.md`。
	```
	.agent/skills/my-skill/ 
	├─── SKILL.md   # Main instructions (required)
	├─── scripts/   # Helper scripts (optional) 
	├─── examples/  # Reference implementations (optional) 
	└─── resources/ # Templates and other assets (optional)
	```
	Agent 执行任务时默认会根据技能描述判断是否与任务相关，如果相关，则会读取技能完整提示词内容并遵循内部定义的规则，当然也可以手动指定使用某个技能。
	安装Skill：需要手动将技能目录复制到Antigravity指定的技能目录。
- TaskGroups

**新开会话窗口后，Antigravity Agent 默认行为**：
会自动读取以下几类上下文内容：
1. **系统预设与用户规则（User Rules）**：比如你定义的 `<MEMORY[agent-chat.md]>` 和 `<MEMORY[style-guide.md]>`，这些规则被硬编码在当前对话初始化配置中，因此我会每次都自动读取并严格遵守。
2. **特定的上下文状态**：比如你当前打开了哪些文件（例如你刚才停留在 
    packages/types/src/index.ts）、光标位置、终端正在运行什么进程等状态信息。
3. **知识库（Knowledge Items, KIs）历史总结**：在后台运行的知识子代理帮咱们提取出的历史摘要，这有助于我了解前因后果并检索跨会话的关联资料。
