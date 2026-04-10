# LiveCli 

LiveCli 是命令行界面。

**`LiveCli::new` 流程**：

1. **初始化系统提示词** (System Prompt)
	```rust
	let system_prompt = build_system_prompt()?;
	```
	**加载系统提示词**：
	1. 发现项目上下文 (Project Context)
		```rust
		let project_context = ProjectContext::discover_with_git(&cwd, current_date.into())?;
		
		pub struct ProjectContext {
		    pub cwd: PathBuf,
		    pub current_date: String,
		    // 当前打开项目的Git信息
		    pub git_status: Option<String>,
		    pub git_diff: Option<String>,
		    pub git_context: Option<GitContext>,
		    // 指令文件
		    pub instruction_files: Vec<ContextFile>,
		}
		```
		这一步最为繁忙，它会执行以下操作：
		- **搜索指令文件**：**从当前目录向上追溯到根目录，寻找 `CLAUDE.md`、`CLAUDE.local.md`、`.claw/CLAUDE.md`、`.claw/instructions.md` 文件。这些是用户自定义的项目规范。**
		- **读取 Git 状态**：调用 `git status -s` 获取当前有哪些文件被修改了。
		- **抓取代码差异 (Diff)**：获取暂存区 (Staged) 和未暂存区 (Unstaged) 的具体代码改动快照。
		- **获取历史记录**：提取最近的几条 Git 提交记录，让 AI 了解项目的近期动态。
	2. 加载配置 (Runtime Config)
		查找 .claudecode 或 .claw/settings.json 等配置文件；
		**配置文件路径**：
		- 最高优先级：**CLAW_CONFIG_HOME** 环境变量 首先检查是否通过环境变量显式指定了配置路径。这通常用于测试环境或用户想要把配置放在非标准位置。
		- 次高优先级：**$HOME/.claw** 如果没设置上述变量，它会尝试寻找当前用户的家目录（HOME 变量），并在其下创建一个名为 .claw 的隐藏文件夹。这是在 macOS/Linux 上的标准行为。
		- 最终兜底：**当前目录下的 .claw** 如果连 HOME 变量都拿不到（这在某些受限或极端的容器环境中可能发生），它会退而求其次，在当前执行命令的目录下寻找 .claw 文件夹。
		配置文件名：
		1. 用户级配置 (Global/User Scope)
			.claw.json (旧版兼容路径)：位于用户家目录（或 $CLAW_CONFIG_HOME 的父目录）。例如：/Users/arvin/.claw.json。
			settings.json (标准全局路径)：位于用户配置目录下。例如：/Users/arvin/.claw/settings.json。
		2. 项目级配置 (Project Scope)
			.claw.json (旧版兼容路径)：位于当前工作目录。例如：./.claw.json。
			.claw/settings.json (标准项目路径)：位于项目根目录的隐藏文件夹内。例如：./.claw/settings.json。
		3. 本地覆盖配置 (Local Override Scope)
			.claw/settings.local.json：专门用于存放不希望提交到 Git 的个人本地设置。例如：./.claw/settings.local.json。
		加载用户的偏好设置，例如默认的权限模式（permissionMode）等。
	3. 综合上面信息，和系统名、系统版本构造最终的系统提示词。
		```rust
		pub struct SystemPromptBuilder {
		    output_style_name: Option<String>,
		    output_style_prompt: Option<String>,
		    os_name: Option<String>,
		    os_version: Option<String>,
		    append_sections: Vec<String>,
		    project_context: Option<ProjectContext>,
		    config: Option<RuntimeConfig>,
		}
		```

2. **创建会话管理 (Session Management)**
	1. Session::new(): 在内存中生成一个新的会话对象，分配一个唯一的 session_id（通常是基于时间的随机 ID）。
		**Session 本身是当前会话的消息和提示词的存储集合**，短期记忆存内存、长期记忆通过 SessionPersistence 存储。
		```rust
		pub struct Session {
		    pub version: u32,
		    pub session_id: String,
		    pub created_at_ms: u64,
		    pub updated_at_ms: u64,
		    pub messages: Vec<ConversationMessage>,
		    pub compaction: Option<SessionCompaction>,
		    pub fork: Option<SessionFork>,
		    pub workspace_root: Option<PathBuf>,
		    pub prompt_history: Vec<SessionPromptEntry>,
		    persistence: Option<SessionPersistence>,
		}
		```
	2. create_managed_session_handle: **在本地文件系统中为这个会话创建一个持久化文件（通常位于 .claude/sessions/ 目录下）**。这确保了你的聊天记录可以在下次聊天窗口打开后“恢复” (--resume)，这个仅用于聊天窗口的恢复，不用做记忆。

3. **构建运行内核 (Runtime)**
	这是最核心的一步。[build_runtime](./claw-code-02-builtruntime) 会初始化 ConversationRuntime，它负责：
	- 连接 API：配置如何调用 Anthropic 的接口。
	- 加载工具：根据 enable_tools 和 allowed_tools 加载内置工具（如文件操作、搜索）以及 MCP (Model Context Protocol) 服务器。
	- 设置权限：确认 AI 在执行命令前是否需要经过你的同意（permission_mode）。

4. 组装 LiveCli 对象
	将上述所有组件（模型名称、权限模式、提示词历史、运行内核等）封装进 `LiveCli` 结构体中。
	```
	let cli = Self {
	    model,
	    allowed_tools,
	    permission_mode,
	    system_prompt,
	    runtime,
	    session,
	    prompt_history: Vec::new(),
	};
	```

5. 初始持久化
	在返回对象之前，先执行一次 persist_session。这会将目前的初始状态（包含系统提示词和空消息列表）立刻写入磁盘。
	默认情况下，会话状态保存在当前项目根目录下的隐藏目录中： `[项目根目录]/.claw/sessions/[工作区哈希值]/[会话ID].jsonl`。
