# OMX 实战大纲

研究 Codex CLI 如何借助 OMX 实现**长时的自主化编程**。
即如何借助OMX实现下面流程：

- 项目工具初始化
- 架构设计与技术栈约定
	- 模块及组件架构
	- 技术栈
	- CICD 流程
-  UIUX 设计
- 业务功能和流程设计开发
- 自动化测试和验收
- 自动代码提交
- 自动执行 CICD 流程

## 实战流程
(以个人网站为例)

### 项目工具初始化

创建项目目录，然后 `omx --high`，会在项目目录下创建一些文件。

```sh
.
├── .codex
│   ├── agents                         # 20个Agent角色，指定Agent角色、职责、使用模型、推理强度、身份信息、工作流程、使用工具、规则等等
│   │   ├── analyst.toml                   # 分析师
│   │   ├── architect.toml                 # 架构师：负责系统设计、边界、接口、长期权衡，定义职责范围，执行循环，提问门槛，成功标准等等
│   │   ├── build-fixer.toml               # 构建和编译报错解决专家
│   │   ├── code-reviewer.toml             # 代码质量评审员
│   │   ├── code-simplifier.toml           # 代码重构师
│   │   ├── critic.toml                    # 工作计划评审专家
│   │   ├── debugger.toml                  # 调试专家：错误调试追踪根本原因，并推荐最小化修复方案
│   │   ├── dependency-expert.toml         # 依赖专家：评估外部 SDK、API 和包，帮助团队做出明智的采用决策
│   │   ├── designer.toml                  # UI/UX 设计师兼开发者
│   │   ├── executor.toml                  # 执行者（应该就是开发者）
│   │   ├── explore.toml                   # 代码库搜索专家
│   │   ├── git-master.toml                # Git 大师
│   │   ├── planner.toml                   # 战略规划顾问，带访谈工作流
│   │   ├── researcher.toml                # 外部文档与参考资料研究员
│   │   ├── security-reviewer.toml         # 安全漏洞检测专家
│   │   ├── team-executor.toml             # 团队执行专家
│   │   ├── test-engineer.toml             # 测试工程师，设计测试策略、编写测试、加固不稳定测试以及指导 TDD 工作流
│   │   ├── verifier.toml                  # 验证专家 用具体证据证明或证伪完成状态
│   │   ├── vision.toml                    # 视觉专家 负责解释图像、PDF、图表、图形和视觉内容，仅返回所请求的信息
│   │   └── writer.toml                    # 技术文档撰写者 负责 README 文件、API 文档、架构文档、用户指南和代码注释
│   ├── auth.json                      # codex 认证信息
│   ├── config.toml                    # OMX 配置（使用模型、模型上下文窗口大小、token压缩阈值）
│   ├── hooks.json                     # SessionStart PreToolUse PostToolUse UserPromptSubmit Stop 钩子列表
│   ├── prompts                        # 同 agents 内容，只是文件格式不一样
│   │   ├── analyst.md
│   │   ├── architect.md
│   │   ├── build-fixer.md
│   │   ├── ...
│   │   └── writer.md
│   └── skills                         # 内置技能, 可以通过 "$" 引用，比如 $de
│       ├── ai-slop-cleaner                # AI 冗余清理：专门清理 AI 生成的代码中的冗余 (Slop)、死代码、重复逻辑及不必要的抽象
│       ├── ask-claude                     # 通过本地 CLI 调用 Claude 或 Gemini 提取专项建议，并将其作为 Artifacts 存入项目
│       ├── ask-gemini
│       ├── autopilot                      # 自动驾驶，实现从想法到代码的全自动执行。涵盖需求分析、设计、并发实现、QA 测试及多维度验证，适合构建完整功能
│       ├── cancel                         # 一键终止并清理所有 active 的 OMX 模式（Autopilot, Ralph, Swarm 等）及其关联状态
│       ├── code-review                    # 代码评审：针对质量、安全、性能和维护性进行全面审查，并提供严重程度分级
│       ├── configure-notifications
│       ├── deep-interview                 # 深度访谈: 采用苏格拉底式提问，在执行前消除模糊性。它会将模糊的需求转化为具备验收标准的 Spec 规范
│       ├── doctor                         # 诊断并修复安装问题，检查插件版本、Hook 配置及 `AGENTS.md` 完整性
│       ├── help                           # 
│       ├── hud                            # 状态显示: 管理 tmux 下方的两层状态栏，实时显示当前的 Agent 运行状态、Token 消耗等。
│       ├── note                           # 记事本: 将重要上下文保存到 `.omx/notepad.md`，帮助信息在长对话压缩后依然得以保留。
│       ├── omx-setup                      # 初始化或刷新项目的 OMX 环境，配置 Agent 模型、Prompts 和 Hooks。
│       ├── plan
│       ├── ralph                          # 持久化循环: 一个自引向的执行循环，直到任务完成并通过架构师验证。它是任务成功的保证，支持断点续传
│       ├── ralplan                        # 战略规划：通过 Planner、Architect 和youyoutubeyouyoutyouyouyou Critic 三方 Agent 进行共识评审（Consensus Planning），并生成决策记录 (ADR)11123
│       ├── security-review                # 安全评审: 针对 OWASP Top 10、硬编码密钥及不安全模式进行专项审计
│       ├── skill
│       ├── team                           # 团队模式: 基于 tmux 的并发执行模式。启动多个 Worker 分别处理不同的任务，并通过 Mailbox 和 State 系统进行同步。
│       ├── trace                          # 追踪: 显示 Agent 流的执行时间线，分析各个环节的性能瓶颈。
│       ├── ultrawork                      # 最大并行性与并行代理编排
│       ├── visual-verdict                 # 视觉裁决: 通过截图与参考图对比，返回结构化的 JSON 评分，用于驱动 UI 的迭代修正。
│       ├── web-clone                      # 网页克隆: 驱动浏览器（Playwright）提取目标 URL 的样式和功能，并自动生成对应的代码实现
│       └── worker                         # 工作者协议: 定义了 Team 模式下 Worker 节点的行为准则，包括 ACK 确认、任务认领及结果汇报流程
├── .gitignore
├── .omx                              # 工作日志、状态文件、监控、计划等
│   ├── hud-config.json
│   ├── logs
│   │   ├── notify-fallback-2026-04-11.jsonl
│   │   ├── omx-2026-04-11.jsonl
│   │   └── session-history.jsonl
│   ├── metrics.json
│   ├── plans
│   ├── setup-scope.json
│   └── state
│       ├── hud-state.json
│       ├── notify-fallback-state.json
│       ├── sessions
│       ├── team-leader-nudge.json
│       └── update-check.json
├── AGENTS.md                         # 
```