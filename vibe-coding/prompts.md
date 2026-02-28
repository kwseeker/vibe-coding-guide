# 提示词

一些概念：
- 零样本提示 （Zero-Shot Prompting）
	提示词中只有任务指令没有示例或额外指导。
- 单样本提示/少量样本提示（One-Shot and Few-Shot Prompting）
- 思维链提示
	思维链提示是指要求模型在给出最终答案前，逐步思考或展示其推理过程。换句话说，你鼓励模型将问题分解。涉及推理和多步计算的复杂问题很有用。
- 角色扮演提示
	让AI扮演某种角色，从这个角色的角度回答问题。
- 上下文提示
	向AI提供额外的上下文或信息。
- 元提示
	针对AI回答的方式或风格做出的指示。比如“首先，用两句话解释方法，然后提供代码。”
- 自一致性
	针对同一提示获取多个输出，然后从中选出最佳或最常见的答案。
- 推理行动提示
	结合了推理与行动。它让模型不仅像思维链那样思考，还能执行诸如计算、调用API或使用工具等行动。会推理拆分子任务，并一步步执行子任务。
	比如“先为此函数编写测试，运行测试，然后相应调整代码。”

使用提示词时应避免超负荷的提示词，每个任务都尽量小。

## 规范驱动开发

规范驱动开发框架：
 - [Spec-kit](https://speckit.org/)
	Spec-kit 需要完整的 7 步流程：制定准则 → 写需求 → 澄清疑问 → 定方案 → 拆任务 → 检查 → 写代码），适合从 0 开始做大型新项目。
 - [OpenSpec](https://openspec.dev/)
	OpenSpec 的流程更简化：起草提案 → 审查 → 实现 → 归档 → 验证，上手更快，适合在现有项目上迭代功能。

## 提示词复用

现在 AI 编程 IDE 基本都支持**命令**（Commands）功能，命令其实就是一种模块化的可复用的提示词。
另外 **Agent Skills** 也可以理解为一种提示词复用方式。

## 提示词协作技巧

+ [告别瞎写！AI Agent 提示词撰写指南](https://page.om.qq.com/page/OME9vJQu5mmizqgRgADtZ-Og0)

## 提示词模板

现在有一些提示词模板网站，比如：
- [prompts.chat](https://prompts.chat/prompts)
	[`Vibe Coding`](https://prompts.chat/prompts?category=cmj1zft8z00rovl0r6rdten09) 分类中基本都是一些项目的提示词，不过也可以参考。
- [Claude prompt-library](https://platform.claude.com/docs/zh-CN/resources/prompt-library/library)
- [PromptBase](https://promptbase.com/)
但是并没有找到适合用于AI编程实现某些业务模块的提示词模板。
理想来看，使用AI编程实现一个企业级项目，提示词也是可以分成很多可复用到类似项目模块提示词的（也许是为了方便使用直接做成了 Agent Skills 那种形式）。

额外一些AI编程提示词示例（仅供参考）：
- [vibe-coding-prompt-template](https://github.com/KhazP/vibe-coding-prompt-template)
- [ai-coding-prompt-java](https://github.com/jwangkun/ai-coding-prompt-java)

