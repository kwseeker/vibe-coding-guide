# BuiltRuntime

```rust
struct BuiltRuntime {
    runtime: Option<ConversationRuntime<AnthropicRuntimeClient, CliToolExecutor>>,
    plugin_registry: PluginRegistry,
    plugins_active: bool,
    mcp_state: Option<Arc<Mutex<RuntimeMcpState>>>,
    mcp_active: bool,
}

// LiveCli
let runtime = build_runtime(
	session_state.with_persistence_path(session.path.clone()),
	&session.id,
	model.clone(),
	system_prompt.clone(),
	enable_tools,
	true,
	allowed_tools.clone(),
	permission_mode,
	None,
)?;
```

**BuiltRuntime 初始化原理**：

1. **构建插件与工具基座 (build_runtime_plugin_state)**
	在进入你提到的代码块之前，它先调用了这个方法来准备“原材料”：
	- **加载配置**：利用 ConfigLoader  读取用户和项目级的 JSON 配置文件，前面步骤（LiveCli）仅仅是读取了配置文件的路径。
	- **初始化插件系统**：加载所有启用的插件，并提取插件提供的 Hooks（钩子） 和 Tools（工具）。
		插件（Plugin）是 Claude Code 中最高级别的扩展机制，用于**将命令、代理、Skills、钩子、MCP、LSP 等能力打包、版本化、共享和分发**。
		**插件本质是一个自带说明书的可执行脚本/程序**。
		插件定义：
		```rust
		pub struct RegisteredPlugin {
		    definition: PluginDefinition,
		    enabled: bool,
		}
		pub enum PluginDefinition {
		    Builtin(BuiltinPlugin),
		    Bundled(BundledPlugin),
		    External(ExternalPlugin),
		}
		#[derive(Debug, Clone, PartialEq)]
		pub struct BuiltinPlugin {
		    metadata: PluginMetadata,
		    hooks: PluginHooks,
		    lifecycle: PluginLifecycle,
		    tools: Vec<PluginTool>,
		}
		#[derive(Debug, Clone, PartialEq)]
		pub struct BundledPlugin {
		    metadata: PluginMetadata,
		    hooks: PluginHooks,
		    lifecycle: PluginLifecycle,
		    tools: Vec<PluginTool>,
		}
		#[derive(Debug, Clone, PartialEq)]
		pub struct ExternalPlugin {
		    metadata: PluginMetadata,
		    hooks: PluginHooks,
		    lifecycle: PluginLifecycle,
		    tools: Vec<PluginTool>,
		}
		// 插件的核心数据结构，这里可以看到插件是 command + args， 即上面说的（可执行脚本或程序）
		pub struct PluginTool {
		    plugin_id: String,
		    plugin_name: String,
		    definition: PluginToolDefinition,
		    command: String,
		    args: Vec<String>,
		    required_permission: PluginToolPermission,
		    root: Option<PathBuf>,
		}
		```
	- **MCP 服务器连接**：初始化 Model Context Protocol (MCP) 服务器，建立与外部工具服务器的通信。
	- **整合工具注册表**：通过 GlobalToolRegistry 将插件工具、MCP 工具和系统内置工具统一管理。

2. **初始化核心组件 (build_runtime_with_plugin_state)**
	拿到上述原材料后，它开始实例化核心运行对象：

	- **插件初始化**：调用 **plugin_registry.initialize()** 正式初始化所有加载的插件。
		这个过程不是真的去启动插件的脚本/程序，而是提取插件描述信息，主要是生成一个插件列表，需要用插件时从这个列表再临时加载（比如某个插件时Python脚本，就启动一个Python进程），插件进程在执行完任务并交出结果后，会**直接退出**。
	- **设定权限边界**：根据 PermissionMode（如 auto-approve 或 manual）生成权限策略。这决定了 AI 调用危险工具时是否需要问过你。
	- **创建 API 客户端 (AnthropicRuntimeClient)**：
		它负责与 Anthropic 后端（外接的 LLM 服务）通信。
		它被配置了具体的模型名称（如 claude-3-5-sonnet）以及是否允许输出结果。
		```rust
		AnthropicRuntimeClient::new(
			session_id,
			model,
			enable_tools,
			emit_output,
			allowed_tools.clone(),
			tool_registry.clone(),
			progress_reporter,
		)?，
		
		struct AnthropicRuntimeClient {
			// tokio 异步执行引擎，实现异步调用
		    runtime: tokio::runtime::Runtime,
		    // API 客户端核心实现
		    client: AnthropicClient,
		    // 当前客户端所属会话ID
		    session_id: String,
		    model: String,
		    // 是否实时回显模型生成内容，默认为true，有时在处理自动化脚本或后台任务时会设置为false
		    emit_output: bool,
		    // 可用工具信息
		    enable_tools: bool,
		    allowed_tools: Option<AllowedToolSet>,
		    tool_registry: GlobalToolRegistry,
		    // 进度报告器，用于在终端显示带图标的状态行，表示当前系统在做什么？做到了哪一步
		    // 比如
		    // 模型思考时：调用 mark_model_phase()，屏幕显示“正在分析请求”。
			// 生成正文时：调用 mark_text_phase()，屏幕显示“正在撰写最终计划”。
			// 调用工具时：调用 mark_tool_phase()，屏幕显示“正在运行工具 XX”（比如运行 ls 或 read_file）。
		    progress_reporter: Option<InternalPromptProgressReporter>,
		}
		// creates/api/src/providers/anthropic.rs
		// 一个使用 reqwest 实现的 HTTP 客户端
		pub struct AnthropicClient {
		    http: reqwest::Client,
		    auth: AuthSource,
		    // 默认是https://api.anthropic.com，可以通过配置 ANTHROPIC_BASE_URL 修改
		    base_url: String,
		    max_retries: u32,
		    initial_backoff: Duration,
		    max_backoff: Duration,
		    request_profile: AnthropicRequestProfile,
		    session_tracer: Option<SessionTracer>,
		    prompt_cache: Option<PromptCache>,
		    last_prompt_cache_record: Arc<Mutex<Option<PromptCacheRecord>>>,
		}
		```
	- **创建工具执行器 (CliToolExecutor)**：
		它是 AI 的“手”，负责在你的机器上执行具体操作（如运行 Shell 命令、读写文件）。
		本质是通过 tokio 异步执行引擎启动工具任务执行。
		```rust
		struct CliToolExecutor {
		    // 终端美化渲染器，负责把枯燥的工具输出（通常是 JSON 或纯文本）转化成漂亮的 Markdown 格式并展示在终端。它处理加粗、代码块、颜色等视觉样式。
		    renderer: TerminalRenderer,
		    emit_output: bool,
		    // 白名单工具列表（允许运行）
		    allowed_tools: Option<AllowedToolSet>,
		    // 工具注册总表（包含所有已经注册的工具）
		    tool_registry: GlobalToolRegistry,
		    // MCP 状态管理器，管理所有连接的 MCP 服务器（如 GitHub, Google Search 等）的状态
		    mcp_state: Option<Arc<Mutex<RuntimeMcpState>>>,
		}
		struct RuntimeMcpState {
		    runtime: tokio::runtime::Runtime,
		    // 服务器连接管理器
		    // 负责：
			// 连接维护：启动和关闭各个 MCP 服务器进程。
			// 协议转译：将 LLM 的搜索/调用指令转化为 MCP 协议格式。
			// 资源汇总：统一管理来自不同服务器（如 GitHub 服务器、Slack 服务器等）的所有可用工具（Tools）、资源（Resources）和提示模版（Prompts）。
		    manager: McpServerManager,
		    // 正在启动中的服务器列表，记录了那些已经发起了连接请求但尚未完全就绪的服务器名称。
		    // 这有助于系统决定是否需要等待它们，或者在工具搜索时提示用户“某些工具还在加载中”。
		    pending_servers: Vec<String>,
		    // 故障/降级报告，如果某些 MCP 服务器启动失败、配置错误或者运行中途崩溃，相关的错误信息会记录在这里。
		    degraded_report: Option<runtime::McpDegradedReport>,
		}
		```

3. **封装为状态机 (ConversationRuntime)**
	```rust
	let mut runtime = ConversationRuntime::new_with_features(
        session,
        AnthropicRuntimeClient::new(
            session_id,
            model,
            enable_tools,
            emit_output,
            allowed_tools.clone(),
            tool_registry.clone(),
            progress_reporter,
        )?,
        CliToolExecutor::new(
            allowed_tools.clone(),
            emit_output,
            tool_registry.clone(),
            mcp_state.clone(),
        ),
        policy,
        system_prompt,
        &feature_config,
    );
	```
	将上述所有组件注入到 **ConversationRuntime** 中。这是一个复杂的状态机，负责：
	- 管理会话历史 (Session)。
	- 处理 AI 的响应并决定何时调用工具。
	- 在调用工具前执行权限检查。
	- 进度报告 (Progress Reporting)：如果开启了输出，它会挂载一个 CliHookProgressReporter。你在终端看到的 `[hook start] ...` 这种实时反馈就是由它打印的。
