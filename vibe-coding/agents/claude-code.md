# Claude

让 Claude Code 有完全的自主权的启动方式：
```sh
claude --dangerously-skip-permissions
```

Claude Code 项目结构：
```
your-project/
├── CLAUDE.md                    ← 团队共享指令，提交到 git
├── CLAUDE.local.md              ← 个人覆盖，被 git 忽略
└── .claude/
    ├── settings.json            ← 权限 + 配置，提交到 git
    ├── settings.local.json      ← 个人权限，被 git 忽略
    ├── commands/                ← 自定义斜杠命令
    │   ├── review.md            →  /project:review
    │   ├── fix-issue.md         →  /project:fix-issue
    │   └── deploy.md            →  /project:deploy
    ├── rules/                   ← 模块化指令文件（全局生效）
    │   ├── code-style.md
    │   ├── testing.md
    │   └── api-conventions.md
    ├── skills/                  ← 自动调用的工作流
    │   ├── security-review/
    │   │   └── SKILL.md
    │   └── deploy/
    │       └── SKILL.md
    └── agents/                  ← 子代理角色定义
        ├── code-reviewer.md
        └── security-auditor.md
```

Claude Code 的记忆系统：
1. 企业级配置（Enterprise policy）    ← 最高优先级，只读
2. 用户级 CLAUDE.md                         ← ~/.claude/CLAUDE.md，对所有项目生效
3. 项目级 CLAUDE.md                         ← 项目根目录，随 Git 提交共享给团队
4. 子目录级 CLAUDE.md                     ← src/、api/、tests/ 等子目录，按上下文加载

Claude Code 钩子：

| 事件名称                | 触发时机                | 核心作用                                |
| ------------------- | ------------------- | ----------------------------------- |
| `PreToolUse`        | 工具调用**之前**          | 可拦截工具执行（如阻止修改敏感文件），并向 Claude 反馈调整建议 |
| `PermissionRequest` | 弹出权限请求对话框时          | 自动批准或拒绝权限申请                         |
| `PostToolUse`       | 工具调用**完成后**         | 执行后置操作（如格式化代码、记录日志）                 |
| `UserPromptSubmit`  | 用户提交提示词后、Claude 处理前 | 预处理用户输入（如补充上下文信息）                   |
| `Notification`      | Claude 发送通知时        | 自定义通知方式（如桌面弹窗、短信提醒）                 |
| `Stop`              | Claude 完成响应时        | 执行收尾工作（如清理临时文件）                     |
| `SubagentStop`      | 子代理任务完成时            | 处理子代理的执行结果                          |
| `PreCompact`        | 即将执行上下文压缩操作时        | 自定义压缩规则                             |
| `SessionStart`      | 启动新会话或恢复旧会话时        | 初始化会话环境（如加载项目配置）                    |
| `SessionEnd`        | 会话结束时               | 保存会话数据、清理环境                         |
Claude Code 检查点 ：
**代码安全回退工具**，能自动跟踪 Claude 对文件的编辑操作，帮你快速撤销不需要的更改，避免代码改坏后难以恢复。

Claude Code Chrome ：
Claude Code 的 Chrome 集成功能，让你可以直接从命令行（CLI）或 VS Code 扩展中控制浏览器。
- 打开网页、点击按钮、填写表单
- 读取浏览器控制台日志（console errors、network requests、DOM 状态）
- 与任何你已登录的网站交互（Gmail、Notion、Google Docs 等）
- 录制浏览器操作为 GIF 文件
- 在多个标签页之间协同工作
## 常用命令
- 内置操作命令
	- `/help`     查看所有可用命令 
	- `/init`     解析当前项目文件，生成 Claude Code 项目配置层文件。
	- `/help`	查看全部能力
	- `/clear`	清空对话
	- `/plan`	进入规划模式
	- `/model` 	切换模型
	- `/context` 查看上下文使用情况
	- `/export`	  导出对话
	- `/status`   环境状态
	- `/tasks`     管理后台任务
	- `/theme.  主题切换
	- `/memory`   编辑 `CLAUDE.md`
- 基础命令
	- `claude`  
		启动 Claude Code 交互式终端
	- `claude .`  
		在当前项目目录启动 Claude，并加载项目上下文
	- `claude run "<task>"`  
		执行一次 AI 任务，例如：`claude run "explain this project"`
- 代码开发
	- `claude edit <file>`  
		修改指定文件，例如：`claude edit src/app.ts`
	- `claude explain <file>`  
		解释代码文件，例如：`claude explain src/server.ts`
	- `claude generate "<description>"`  
		根据描述生成代码
- 项目分析
	- `claude analyze`  
	    分析项目结构并给出改进建议
	- `claude debug <file>`  
	    分析并定位代码问题
	- `claude review`  
	    代码审查（安全、性能、bug）
- Skill（技能）管理
	- `skills list`  
		查看已安装的 skills
	- `skills refresh`
		强制刷新 Skill 缓存
	- `skills search <keyword>`  
		搜索技能，例如：`skills search crawler`
	- `skills add <repo>`  
		安装技能，例如：  
		`skills add https://github.com/firecrawl/cli`
	- `skills inspect <skill>`  
		查看 skill 详情，例如：`skills inspect firecrawl`
	- `skills remove <skill>`  
		删除 skill
- 工具（Tools）
	- `claude tools`  
		查看 Claude 可用工具（filesystem / git / shell / browser）
- Git 相关
	- `claude diff`  
		查看当前代码修改
	- `claude commit`  
		自动生成 Git commit message
	- `claude explain commit`  
		解释最近一次 commit
- 配置管理
	- `claude config`  
		查看或修改配置
	- `claude login`  
		登录 Claude 账号
	- `claude --version`  
		查看版本号
- 文件操作
	- `claude cat <file>`  
		查看文件内容
	- `claude search "<keyword>"`  
		搜索代码内容
	- `claude find <function>`  
		查找函数或符号
- Agent / Skill 调试
	- `claude agents`  
		查看可用 Agent
	- `claude agent run`  
		运行 Agent
	- `claude logs`  
		查看运行日志
