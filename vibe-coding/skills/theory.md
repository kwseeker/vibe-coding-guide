# Skill 工作原理

Skill 相当于是一个用于**临时查阅的工具手册**。

例子：https://github.com/firecrawl/cli/tree/main/skills/firecrawl-cli， 
firecrawl 技能就是指导 Agent 使用 firecrawl-cli 工具干活的说明书。

## **Skill 目录结构**

+ **SKILL.md**（必须）
	Agent 需要读取这个文件来理解技能。本质就是一个使用说明书。
	必须内容 (frontmatter)：
	- **name**  
	- **description**  
		功能描述，用于Agent匹配场景是否可用此技能。
		是技能被触发的**唯一依据**。
	其他内容：
	- prerequisites
		前置条件。
		比如 `firecrawl` 的前置条件是需要安装 `firecrawl cli` 工具。
	- workflow
		技能的工作流程。
	- commands  
		比如 `firecrawl cli` 支持的命令。
	- examples
		比如 `firecrawl cli` 使用案例。
	- ...
- agents
	技能名片，不影响 AI 的行为，纯粹是给产品界面用的。
+ scripts/
	比如有些技能需要内部集成一些脚本实现更精准的内部手册查找，比如 `ui-ux-pro-max` 技能可以根据用户指定的风格，查找并加载对应的风格样式数据文件，而不是加载所有风格样式数据文件。
+ assets/
	比如上面说的风格样式数据文件。
+ references/
	更详细的参考说明，可以按类型、功能拆分指导说明内容，也便于按需加载。
+ ...

## **如何写出好的 skill**

下面的要点大多是围绕一个问题：**如何在有限的上下文中，尽可能多地加载当前任务需要的相关信息**（提升有效信息的密度）。

- **语言要简洁精炼**
	避免客气、抽象的用词，避免说AI已经掌握的信息。
- **skill的内容使用三级分层架构**
	skill的内容也是要按需加载，这点可以参考 `ui-ux-pro-max` 技能。
	- 第一层（`SKILL.md` 元数据，即 name 和 description）
		这部分内容长驻上下文，需要控制在 100 tokens 以内。
	- 第二层（`SKILL.md` body数据）
		技能触发时加载，需要控制在 5K tokens 以内。
	- 第三层（捆绑资源，即 SKILL.md 外其他目录和文件）
		执行到具体任务时按需要加载。
- **AI 自由度控制**
	在一些需要可靠结果的任务中可以通过一些确定性脚本（scripts）避免出现可靠性问题。
	自由度级别：文本指令 > 模板/伪代码 > 确定性脚本。
- **skill-creator的六步创建流程与质量保障链**
	**[六步创建流程](https://github.com/datawhalechina/hello-agents/blob/main/Extra-Chapter/Extra08-%E5%A6%82%E4%BD%95%E5%86%99%E5%87%BA%E5%A5%BD%E7%9A%84Skill.md#%E4%B8%83%E8%90%BD%E5%9C%B0%E5%85%AD%E6%AD%A5%E5%88%9B%E5%BB%BA%E6%B5%81%E7%A8%8B)**：
	- Step 1：理解技能——用具体例子建立共识
	- Step 2：规划可复用的技能内容
		反复使用的东西 → 封装成 scripts/references/assets。
	- Step 3：初始化技能
	- Step 4：编辑技能
		- 阶段一：先实现可复用资源
		- 阶段二：更新 SKILL.md
	- Step 5：校验技能
	- Step 6：迭代
	**质量保证链**：
	![](../../assets/imgs/file-interaction.png)

## 参考
- [如何写出好的 Skill](https://github.com/datawhalechina/hello-agents/blob/main/Extra-Chapter/Extra08-%E5%A6%82%E4%BD%95%E5%86%99%E5%87%BA%E5%A5%BD%E7%9A%84Skill.md)
