# Antigravity

[Docs](https://antigravity.google/docs/agent-modes-settings)

功能：
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
