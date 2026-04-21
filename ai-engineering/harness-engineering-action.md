# 驾驭工程实战

理论参考(实战的文档很少)：
- [Harness Engineering 最佳实践：从概念到落地的完整操作手册](https://zhuanlan.zhihu.com/p/2023068557592863537)
- [一文讲透如何构建Harness——六大组件全解析](https://cloud.tencent.com/developer/article/2648873)

## 个人网站项目驾驭工程实战

基于驾驭工程理论开发个人网站项目，实践驾驭工程的方法论。

### 实战目标
（除了LLM，Agent中其他一切都归于驾驭工程范畴，但是有些能力是基础设施提供的这里不做展开，比如文件系统、记忆系统、上下文压缩、沙箱、工具调用）

- **多Agent架构**
	（设置三个Agent协作，驾驭工程最小架构最少是三个，像omx内置了十几个角色，多Agent协作相当于变相地拓展了上下文空间，可以做更复杂的项目）
	- **Planner** 做项目架构设计、UI规范设计
		- UI规范
			比如MD文档写清楚某个UI页面的布局、组件、风格（风格库、主题配置等）
		- 设计标准化的代码目录结构
	- **Generator** 做模块代码实现和测试（多个，每个独立的功能模块使用独立会话）
	- Executor（可选，代码执行和测试，涉及比较危险的操作时建议添加，可以在独立沙箱执行）
	- **Evaluator** 做代码质量审查和验收测试
	- **自动编排（编排器）**
- **上下文工程**
	- 实现结构化上下文管理
		- AGENTS.md 作为项目简介和文档导航地图
			AGENTS.md 控制在100行以内。
			包括项目概述、架构、团队规范等，但是架构、团队规范放知识外链，不直接放内容。
		- docs/ 下构建结构化的知识库
			即多目录层级的Markdown文档，每一层可以导航到更深层详细的文档。
			每个文档都包含元信息（包括作用、状态等）
	- 需要时常维护
	- 上下文压缩
		常用 Coding Agent 本身自带压缩功能，不过也有自己重新实现的。
- **架构约束**
	- 约束需要包含的三要素
		- 错误原因
		- 怎么修改
		- 去哪看文档
	- ESLint 约束
		- 依赖方向约束
			比如： Types → Config → Repo → Service → Runtime → UI，不能反向被依赖。
	- CI pipeline 约束
		- 类型检查
		- 自定义规则
		- 单元测试
		- 覆盖率阈值检查
		- 架构约束
		- 文件大小检查
			比如限制一个文件的行数不超过500行，超过则进行模块拆分
		- 文档新鲜度
			检查文档是否长期未更新，自省是否有好好维护文档
- **反馈循环（自我验证循环）**
	比如 Generator 生成代码、执行代码、运行测试，Evaluator 分析结果做验收评估，评估未通过交给 Generator 修复，直到评估通过。
- **垃圾回收（熵管理）**
	定期启动专门的Agent做偏差检测、质量等级评定，并根据结果发起针对性重构 PR，以及扫描文档和代码不一致问题自动修复。
- **可观测性**
	引入日志、监控系统，可以让 Agent 定期扫描检测问题，分析bug，自动修复。

### 细节实现

上述目标中的一些细节的具体实现。

+ **如何启动多个Agent？以及多Agent如何编排？**  
	+ Coding Agent 原生支持
		+ Codex 的 [Subagents](https://developers.openai.com/codex/subagents)
			子代理（Subagents）是 Codex 的高级功能，允许你将大型任务分解为更小的部分，并行或顺序处理。子Agent拥有独立上下文空间，主 Agent 作为编排者给子Agent分配任务以及提供必要的上下文，支持自动编排和手动指示某个子Agent执行任务。
			在 .codex/agents/  下面通过添加独立的 TOML 文件[自定义 Agent 角色](https://developers.openai.com/codex/subagents#managing-subagents)，还可以设置使用不同的模型。
		+ Antigravity 的 Agent Manager
			比如将主对话框作为 Planner，然后在 AgentManager 中开启多个新的独立窗口，这些会话窗口对当前工作空间的修改是可以跨对话可见的，不过需要用户自己充当编排者角色协调多个Agent 协作。
+ **项目初始化后的关键文档要怎么写？**
	+ AGENTS.md 是否会自动创建？有没有模版？
		可以使用脚手架初始化项目，然后执行 Codex `/init` 命令总结项目自动创建和填充 AGENTS.md，然后自己参考一些AI驱动开发的开源项目的 AGENTS.md 是怎么写的。
		提供几个可参考的开源项目的 AGENTS.md:
		- [everything-claude-code/AGENTS.md](https://github.com/affaan-m/everything-claude-code/blob/main/AGENTS.md)
		- [openclaw/AGENTS.md](https://github.com/openclaw/openclaw/blob/main/AGENTS.md)
		- [hermes-agent](https://github.com/NousResearch/hermes-agent/blob/main/AGENTS.md)
	+ 如何设计结构化的知识库的层级？
		可以参考 `openclaw` 的 `docs` 文档。
		```
		docs/
		  index.md             # 文档结构说明
		  design/              # 原始需求设计文档，人类工程师编写
		  architecture/        # 经过Agent评估、细化后的架构文档
		    index.md
		    layering.md
		    content-model.md
		  ui/                  # 经过Agent评估、细化后的UI设计文档
		    index.md
		    design-system.md
		    page-home.md
		    page-notes.md
		  workflows/           # 定义项目中一些常用工作流
		    index.md
		    task-loop.md
		    review-loop.md
		  tasks/               # 开发任务规范文档
		    index.md
		    feature-personal-homepage.md
		```
	+ 上下文空间不足时Codex和Antigravity会自动压缩么？怎么压缩的？
		会自动压缩，Codex 可以通过 `/compact` 指令手动触发压缩，Antigravity 暂时没看。
+ **架构约束怎么写？**
	+ ESLint、CI 约束怎么配置？怎么触发？
		这些“硬约束”通常要拆成几类检查器脚本，然后挂到 `package.json` 脚本里，再在不同阶段触发。
		比如最常见的触发层是这三层：
		- 本地编译、测试期：编译、测试时就报错
		- 提交期：pre-commit / pre-push 跑一遍（Git 钩子）
		- CI/PR 期：GitHub Actions 作为最终门禁
			问题触发后可以手动将问题信息发给本地 Codex 让其修复，也可以让 Github Actions 调起云端 Codex 自动修复。
+ **如何通过 Codex Subagents 或 Antigravity Agent Manager 实现反馈循环？**
	```
	Planner -> Generator -> Executor(optional) -> Evaluator
                               ^               |
                               |_______________|
	```
	具体步骤：
	1. Planner 写 `plan.md`
	2. Generator 实现一个 milestone
	3. Executor 跑 `lint/test/build`
	4. Evaluator 读结果，以及审查代码，给出：
	    - 是否通过
	    - 原因
	    - 修复建议
	    - 对应文档
	5. 未通过则回到 Generator
	6. 通过再进入下一 milestone
	
	具体实现（以 Codex 为例）：
	先使用 TOML 自定义Agent角色（Planner、Generator、Executor(optional)、Evaluator）；
	然后主Agent通过提示词编排（可以将流程写成一个编排 Skill），比如：
	```md
	需求：为个人网站新增博客系统，支持文章列表、详情页、标签过滤、Markdown 渲染。  
  
	请按下面流程执行，并严格等待上一步完成后再进入下一步：  
	  
	1. Spawn `planner`  
		- 产出实现计划  
		- 产出技术规范和目录变更建议  
		- 拆出可顺序执行的任务列表  
		- 输出到 docs/plan/blog-feature-plan.md  
	  
	2. 在主线程汇总 planner 结果，并确认任务顺序  
	  
	3. 对任务列表逐项执行：  
		- 每次只 spawn 一个 `generator`  
		- 让 `generator` 只实现当前一个任务  
		- 每完成一个任务就提交当前 diff 摘要，不要一次改完整个需求  
	  
	4. 每个任务完成后，spawn `evaluator`  
		- 检查当前 diff 是否符合 planner 的规范  
		- 检查 correctness / regression / missing tests  
		- 如果有问题，列出修改项  
		  
	5. 若 `evaluator` 判定不通过：  
		- 将 evaluator 的意见交回 `generator`  
		- 只修复指出的问题  
		- 修完后再次交给 `evaluator`  
		  
	6. 全部任务完成后：  
		- 给出最终总结  
		- 列出改动文件  
		- 列出未解决风险
	```
	Codex已经支持[代码审查](https://developers.openai.com/codex/app/review)，可以通过命令 `/review` 在本地审查当前修改，也可以配置在远程进行PR审查，代码审查实现原理也是通过模型，编写一段代码审查提示词，约定角色和规则，然后将 `git diff`读取的差异代码一并发给大模型进行审查。
	Antigravity 并没有提供代码审查功能，不过可以根据上述原理，以 Skill 的方式实现。
+ **垃圾回收具体怎么实现？**
	比如使用 Codex 开启定期巡检任务。
	- 代码熵巡检
		每周一次：
		- 大文件扫描
		- 重复组件扫描
		- 死代码 / 未使用导出
		- import 方向漂移
		- TODO/FIXME 过期项
	- 文档熵巡检
		每周一次：
		- docs/ 中不存在的文件链接
		- API / page / component 与文档不一致
		- updated_at 太旧但代码最近改过的文档
	- 任务熵巡检
		- 每周一次：
		- 未关闭的 task 文档
		- plan.md 与代码状态不一致
		- review 里长期未处理的问题
- **怎么设置让 Agent 自动扫描日志、监控系统，自动发现问题修复问题？**
	Cron / GitHub Action  
		-> 生成 observability-report.md  
		-> Evaluator 读取并分类  
		-> 低风险问题生成 fix PR  
		-> 高风险问题只产出 report
