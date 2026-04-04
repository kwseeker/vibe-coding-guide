# OpenSpec

- [OpenSpec](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec-cn](https://github.com/studyzy/OpenSpec-cn) (中文汉化)
- [Docs](https://github.com/Fission-AI/OpenSpec/?tab=readme-ov-file#docs)

**安装&项目初始化**：
```sh
npm install -g @studyzy/openspec-cn@latest
openspec-cn --version
cd my-project
openspec-cn init
```
初始化后生成的文件（Antigravity）：
```
.
├── AGENTS.md                       # AI协作说明文件，告诉AI这个仓库使用了 OpenSpec、项目结构和指南、创建变更提案以及制定提案计划任务时需要遵循的格式和约定（主要是引导到 `@/openspec/AGENTS.md`）
├── openspec                        # 规范目录
│   ├── AGENTS.md                   # 让 AI编程助手使用 OpenSpec 进行规范驱动开发的操作指南，包含了如何创建变更提案、编写需求规范、验证和归档变更的完整工作流程
│   ├── changes                     # 存放变更提案，记录每次修改的计划，描述要改什么、为什么、怎么改
│   │   └── archive                 # 历史变更归档，已合并进 specs，类似 Git 中的已合并 PR
│   ├── project.md                  # 当前项目上下文说明，记录项目的信息（项目目标、架构、技术栈、不会轻易变化的共识）
│   └── specs                       # 当前有效规范（Source of Truth）
└──	.agent/
	└── workflows                   # 生成的三个Antigravity命令
	    ├── openspec-apply.md       # 实现一个已批准的 OpenSpec 变更并保持任务同步
	    ├── openspec-archive.md     # 归档一个已部署的 OpenSpec 变更并更新规范
	    └── openspec-proposal.md    # 快速搭建一个新的 OpenSpec 变更并严格验证 
```

**OpenSpec 开发流程**：
1. 首先使用 `openspec init` 生成初始规范文档；
2. 手动填写 `openspec/project.md` 完善项目上下文说明；
	如果项目不是从0开始的（即已有代码结构配置），也可以让 Agent 帮忙完善，比如：
	“请阅读 openspec/project.md 并帮我填写有关我的项目、技术堆栈和约定的详细信息”。
	这个文件内容不太可能一开始就规划地很详细，可以随着迭代不断完善。
3. 每次添加新需求（每个需求一个规范目录），先执行 `/openspec:proposal` 创建提案、规范和计划任务列表；
4. 评审 OpenSpec 生成的提案和规范并修改；
5. 评审完毕后执行 `/openspec:apply` 修改代码实现计划任务并更新任务列表状态；
6. AI 完整任务后执行 `/openspec:archieve` 将变更归档为真实源项目规范。
![](https://i.imgur.com/alVPKPM.png)
**参考**：
- [OpenSpec vs Spec Kit：为您的团队选择正确的 AI 驱动开发工作流程](https://hashrocket.com/blog/posts/openspec-vs-spec-kit-choosing-the-right-ai-driven-development-workflow-for-your-team#installation-a-developer-friendly-start)
- [From Spec to Shipping: How Developers Implement Features with AI-Driven Workflows](https://hashrocket.com/blog/posts/from-spec-to-shipping-how-developers-implement-features-with-ai-driven-workflows)
- [从1到N的开发神器OpenSpec规范驱动开发完整实战指南](https://www.aivi.fyi//llms/introduce-OpenSpec)
+ [openspec的project.md要求模板](https://www.cnblogs.com/llcdbk/p/19446752 "发布于 2026-01-06 11:58")
