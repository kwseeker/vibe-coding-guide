系统性研究如果更好地使用 AI 编程。

## Vibe Coding 简介

现在有两种模式：
- Vibe Coding (氛围编程)
- AI-Assisted Coding（AI辅助编程）
氛围编程适合用于MVP原型开发与快速验证阶段；
AI辅助编程适合用于MVP之后的生产阶段。

AI 的局限：
+ 高度复杂的系统
+ 底层优化与系统编程
+ 使用独特或小众框架
+ 创造性UI/UX设计
+ 意图与需求的解读
现在的AI归根结底只是模式匹配，一些富有创造性的方面表现不佳，比如根据新的研究论文编写全新的算法。
AI 编程同样存在“最后一公里”的问题，比如**边界情况、优化架构、确保可维护性**。
AI 生成的代码需要程序员进行审核、重构，避免变的不可控，程序员负责把握整体架构、进行任务拆分。

如何高效地与AI交流（怎么用提示词）：
写提示词时应该注重可**复用性**和**未来适用性**。
编写提示词应该像编写需求规格说明书一样思考，力求清晰明确地表达意图。
- 复用性
	TODO：参考一些开源的提示词模板怎么写的。

AI在项目开发工作流中持续有效的模式：
- AI作为初稿起草者
	AI模型生成初始代码，开发者随后进行优化、重构和测试。
- AI作为结对编程员
	开发者与AI保持持续对话，形成紧密的反馈循环、频繁的代码审查和极简的上下文提供。
- AI作为验证者
	开发者仍编写初始代码，而后使用AI进行验证、测试和改进

Vibe Coding 黄金法则：
1. 明确具体地表达需求  
2. 始终对照意图验证AI输出  
3. 将AI视为需指导的初级开发者  
4. 用AI扩展能力，而非取代思考  
5. 生成代码前团队预先协调  
6. 将AI使用视为开发对话的正常部分  
7. 在Git中隔离AI变更并单独提交  
8. 确保所有代码（无论人写或AI生成）都经过代码审查  
9. 不合并你不理解的代码  
10. 优先考虑文档、注释与架构决策记录  
11. 分享并复用有效提示词  
12. 定期反思与迭代

如何确保AI生成的代码的**正确、安全且可维护**：
- 通读AI生成的代码，推演是否符合自己的意图
- 是否有多余代码、重复代码、意义不明确的代码、功能类似的库
- 边界条件是否考虑完整
- 是否存在性能问题
- 是否是当前问题的最优解
- 代码是否遵循编程规范和项目风格
- 是否存在漏洞，比如未处理的异常
- 多重构AI生成的代码
- 注重测试：单元、集成与端到端测试
	TODO：怎么使用 AI 辅助测试

## AI Coding 从设计到部署

- 提示词工程
- UI/UX 设计
- 全栈开发
- 测试
- 部署

### 提示词工程

详细参考：[docs/prompts.md](prompts.md)
涉及：
- 精确表达意图
- 规范驱动开发（SDD）
- 提示词复用（指令、技能化）
- 提示词模板
- 提示词协作技巧

### UI/UX设计

UI/UX 设计时，感觉页面比后端业务流程更难以清晰地描述（长篇大论的提示词写完花费的时间可能借助 Figma 都画完了），推荐是自己找一些设计素材，然后贴到 AI 页面设计工具中，让其模仿实现，自己写些简单的提示词进行调整。
#### 工具

- 完全免费
	- **Stitch**（**推荐**）
		完全免费，使用基本没有限制，每月给的额度用于设计生产类项目也是可以的。
		效果也还行。
	- **Pencil.dev**（本身免费但是需要外接模型）
		可以借助它直接生成UI/UX设计及代码，也可以导入Figma设计图生成代码。
		基本对所有IDE都有提供插件（本质是在本地安装MCP服务，设计任务还是需要Agent执行）；虽然也有提供桌面应用但是Pencil.dev的桌面应用貌似只能用ClaudeCode；所以推荐用插件，安装针对自己使用的IDE的 Pencil MCP Server。
		特点：
		- 可以像使用 Canva 或 Figma 一样，拖拽设计，样式布局
		- 提示词生成
		- 组件库联动，可以将项目引入的组件库组件列在侧边栏
		使用方法：
		1. 比如这里用的是 Antigravity，安装 Pencil 插件，此插件会自动为所有 Agent 安装 Pencil MCP Server；(注意有可能因为 Antigravity 版本太老而导致自动配置失败，出现此问题后先更新版本)
		参考：
		- [Pencil：设计和写代码，以后就全让AI干了](https://www.53ai.com/news/LargeLanguageModel/2026012420847.html)
		- [Pencil高级使用技巧保姆级教程](https://www.bilibili.com/video/BV1uKzvBCEzR/?spm_id_from=333.788.player.player_end_recommend_autoplay&vd_source=e085f6b3e74d1e9c35fe18734cac42f7&trackid=web_related_0.router-related-2481894-96shx.1770031955602.554)
		实际体验：感觉效果并不是很好，页面中存在比较复杂一点的布局，Pencil 就实现不了。
- 免费增值（有一定的免费额度）
	- Aura
		- [如何用AI做出高颜值的原型设计？](https://www.bilibili.com/video/BV1phndzwEs7/?spm_id_from=333.1387.collection.video_card.click&vd_source=e085f6b3e74d1e9c35fe18734cac42f7)
	- Lovable （AI全栈应用生成）
		用它实现页面设计也是可以的。
		每天刷新免费额度但是额度也很少。
		生成效果还可以。
	- 妙多
		一个月的免费额度，只支持生成很少的几个页面。用的话基本只能使用付费版（90元/月）。
		效果还可以。
		可以选中页面某个部分单独进行优化。
- 付费
	- Pixso
		类似 Lovabel 是直接生成应用。
		效果不是很好，有很多低级瑕疵，容易理解错用户意图。
- **模型聊天生成UI代码**（**强烈推荐**）
	其实不一定非要用专门的UI工具设计UI/UX，测试了下**这种方式生成的UI效果并不比UI工具差，尤其是搭配上 ui-ux-pro-max Skill 后作出的效果很惊艳**。
	可以让模型（大模型或编程Agent）直接生成页面源码，现在的很多AI+UI工具推测也是先生成源码，然后再渲染到UI工具的窗口里面。
	使用模型或编程Agent生成UI/UX可以使用Skills，Rules等（现在的UI工具基本都不支持），灵活度也更高。
#### 优化设计
使用AI设计UI时碰到的一些问题：
+ **AI 难以理解你的意图**
	做UI设计自己也需要掌握一些UI设计的专业术语，比如风格或样式的专业词汇；
	可以去收集一些UI设计专业的提示词模板，在模板基础上进行修改；学习提示词模板的写法。
	也可以借助一些专业的UI设计Skill进行设计。
	UI设计提示词模板参考:
	- [ai-prototype-prompts](https://github.com/swiftdo/ai-prototype-prompts)
	UI设计Skills：
	+ [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
		这个Skill内部提供了50种风格，21中调色板，50种字体搭配，20种图表，8种技术栈（还在不断迭代增加），包括一个完整的构建工作流。
		[benchmark-skill-ui-ux-pro-max](https://github.com/hylarucoder/benchmark-skill-ui-ux-pro-max) TS脚本让 ClaudeCode + ui-ux-pro-max 生成100个落地页。
		详细使用参考：[ui-ux-pro-max.md](ui-ux-pro-max.md)。
	+ frontend-design
		Claude 官方UI设计Skill。
+ **生成的页面风格难以保持统一**
	- 使用 ui-ux-pro-max Skill 保证风格一致性
		它会在生成UI页面前先生成一个 `MASTER.md` 的基准提示词文档来保证所有页面风格的一致性。
	+ 使用项目规则（Rule）保证风格一致性
		需要自己先定义一个类似 ui-ux-pro-max 那样的 `MASTER.md`，并作为所有UI界面生成必须遵循的规则；
		关于如何从参考页面提取风格，可以使用 DevTools 检查工具查看网页的CSS样式，复制到规则文档中，也可以使用 Chorme 拓展 `superdesign` 提取Web网页的风格。
+ **部分工具不会复用组件化的设计**
	即在不同的页面本应复用的组件，但是AI却没有将它们联系起来，分别重新设计了一次。
+ **页面风格死板缺乏设计感**

### 全栈开发

#### 工具

#####  Agents
（这些Agent都集成有CLI）
- Claude Code
	- 帐号与模型申请
		- 官方帐号申请
			限制很严格、很容易被封号。
		- 中转站
			使用中转站提供的帐号 API Key。
			- https://codeyy.top/
		- 使用第三方模型（比如 DeepSeek、GLM，不需要帐号，参考第三方模型官方接入指南）
	- 工具配置
		- [everything-claude-code](https://github.com/affaan-m/everything-claude-code)
- Codex
- OpenCode
##### 模型
- [编程能力排行](https://www.swebench.com/)（Github 开源项目 Issue 解决率）
##### AI IDEs
（AI IDE 本身是编程 Agent + IDE的结合体，AI IDE中也可以通过 MCP 或插件连接上面的Agent）
- 免费增值
	- **Antigravity**
	- **QCoder**
	- **Trae**
- 付费
	- **Cursor**
##### Coding Agent 能力
- 命令（Commands）
- 规则（Rules）
- Agent Skills
	之前手动编程复用代码通过封装库、框架，现在AI编程复用 Prompt 通过封装 Skills。
	- Skill 平台
		- [skillsmp](https://skillsmp.com/)
		- [anthropics/skills](https://github.com/anthropics/skills)
		- [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills)
	- 常用推荐Skills
		- ui-ux-pro-max（UI设计）
- 多 Agent 协作
- 多任务管理
	- [claude-task-master](https://github.com/eyaltoledano/claude-task-master)
- MCP 工具
- [SuperPowers](https://github.com/obra/superpowers)（Claude Code 插件）
	目前（260127）仅Claude Code、Codex、OpenCode支持；
	是使用15个 Skills 实现的一个开发工作流；
	推荐搭配 OpenSpec 一起使用。
	```
	brainstorming（理清需求，只有复杂的任务才需要）
	→ openspec proposal（正式化规则，生成 tasks.md）
	→ writing-plans（把 tasks.md 的内容展开更细的步骤，不一定需要）
	→ openspec apply（开始实现）
	→ openspec archive（归档）
	```
	特点：
	- 强制TDD
	- 提前规划
		- 头脑风暴
		- 将大任务原子拆分为小任务
		- 按计划执行并验证
	- 子代理驱动
		- 使用子代理（Sub-agent，相当于一个独立的会话）执行原子任务
		- 主代理（Parent）负责统筹，子代理负责干活。干完活后，主代理会进行代码审查。这样做的好处是防止长对话导致 AI 变笨或遗忘上下文。
	- 自动化 Git 工作流（Worktrees）

#### 开发技巧
##### 接口设计
借助AI编写遵循 [OpenAPI 规范](https://swagger.org.cn/docs/specification/v3_0/about/) 的接口文档，使用 [orval](https://orval.dev/) 等工具生成TS接口定义。
##### 代码生成
推荐使用 SDD 开发模式。

### 测试

### 部署

### 最佳实现

#### UI/UX 设计
**工具**：
+ Claude Code + Claude Sonnet4.5 + ui-ux-pro-max（首选）
+ Antigravity + Gemini 3 Pro + ui-ux-pro-max
**复制风格样式**：
（ui-ux-pro-max 中默认提供了几十种网站设计风格，也可以通过下面的方法从网站提取风格样式）
+ superdesign
	可以拉取Web网站所有网页并导出，然后让 Claude Sonnet4.5 或 Gemini 3 Pro 按照 ui-ux-pro-max 的 Master.md 格式分析并生成风格指南，替换或者优先级覆盖 Master.md。
	superdesign 本身虽然提供直接生成设计风格提示词但是收费（20美元/月）。

#### 全栈开发
**关于提示词**：
	开发过程中输入对话框以及AI返回的消息推荐存储到提示词日志文件中，用于后期**从中梳理详细的开发规范**用于后续项目。

## Coding Agent 内部工作流程

**开源 Agent**：
- [**OpenCode**](https://github.com/anomalyco/opencode)

## 参考

- [用 Skill 拯救你的 AI 网页设计审美！告别 AI 味！](https://www.bilibili.com/video/BV1tDrPBFEmA/?spm_id_from=333.337.search-card.all.click&vd_source=e085f6b3e74d1e9c35fe18734cac42f7)
- [如何用Claude Code生成顶级 UI](https://www.bilibili.com/video/BV1Fx1kBvEFz/?spm_id_from=333.1391.0.0&vd_source=e085f6b3e74d1e9c35fe18734cac42f7)
- https://github.com/affaan-m/everything-claude-code （使用Claude Code氛围编程配置）
	其他编程 Agent 也可以参考，基本都一样。
- https://github.com/github/spec-kit
- https://github.com/liyupi/ai-guide
- https://www.aivi.fyi/llms/introduce-Superpowers
- [給 AI 超能力？Superpowers 的設計與取捨](https://kaochenlong.com/ai-superpowers-skills)
