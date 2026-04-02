# OpenClaw 工作原理

## **代码目录结构**

```sh
.
├── AGENTS.md
├── apps             # 端侧节点，相当于为 OpenClaw 提供五官和肢体，让其可以接受指令与信息，触达和控制你的物理设备
│   ├── android          # 提供的能力：硬件感知与控制（相机拍照、视频录制、读取GPS位置、读取设备传感器数据）、系统数据访问（通讯录、日历、相册、系统状态）、实时交互与UI、通讯拦截与操作、语音与智能唤醒
│   ├── ios
│   ├── macos            # 除了类似移动端的能力，额外还提供了网关管理与监控、远程接入与网络等功能，相当于提供了一个对整个系统的控制台
├── extensions       # 插件集合，主要提供了 对接模型、消息通道（对接聊天/办公软件）、工具（网络搜索）、深度功能（长期记忆等） 等拓展
│   ├── deepseek
│   ├── firecrawl
│   ├── tavily
│   ├── telegram
│   ├── feishu
│   ├── memory-core
├── scripts
├── skills           # 内部集成了许多技能
├── src              # 核心系统实现
│   ├── acp               # Agent Client Protocol 协议桥接模块 
│   ├── agents            # 最核心的模块，包括最重要的Agent循环、模型调度、Prompt 组件、工具分发和回复拼接
│   ├── auto-reply        # 处理无需 Agent 思考的反馈，比如系统状态反馈等
│   ├── bindings          # 负责维护会话绑定记录，它将特定的发送者（Sender）或对话（Conversation）与特定的插件或工作空间持久化地关联起来，确保 Agent 能在正确的上下文中响应
│   ├── bootstrap         # 负责在 Agent 启动时构建其运行环境和上下文背景
│   ├── browser           # 一个强大的浏览器自动化与 CDP (Chrome DevTools Protocol) 桥接器，可以启动、管理 Chromium 进程，管理浏览器登录状态、提供基于 Playwright 的底层API，让 Agent 能够抓取网页、点击等操作
│   ├── bundled-web-search-registry.ts
│   ├── canvas-host       # “画布”功能的宿主模块。它会在 18789 端口上开辟一个路由，用于承载 Agent 生成的 HTML/A2UI 界面
│   ├── channel-web.ts
│   ├── channels          # 实现各类消息通道的后端逻辑（Telegram, WhatsApp, Discord, Slack 等）
│   ├── cli               # 命令行指令的具体实现
│   ├── commands          # 命令行指令的具体实现
│   ├── compat
│   ├── config            # 各类配置文件的解析和动态刷新逻辑
│   ├── context-engine    # 负责处理长文本上下文的提取、总结和优化  
│   ├── cron              # 周期性任务调度引擎
│   ├── daemon            # 网关守护进程，负责作为 WebSocket 服务器运行
│   ├── extensions
│   ├── gateway           # 定义了 OpenClaw 的网关通讯协议（WS 协议、请求/响应帧等）
│   ├── hooks             # 提供系统级钩子（Hooks），在特定事件（如 Agent 启动前后）自动运行脚本
│   ├── i18n
│   ├── image-generation  # 图像生成（如调用 DALL-E 或 MJ）
│   ├── index.test.ts
│   ├── index.ts          # openclaw 启动入口
│   ├── infra             # 系统底层依赖，如网络库、文件锁、SSH 隧道等 
│   ├── install-sh-version.test.ts
│   ├── interactive       # 处理各种交互式组件（如按钮、选择菜单、表单）的点击回调和状态更新，主要服务于 Slack 按钮或移动端 UI 的交互反馈
│   ├── library.ts
│   ├── line
│   ├── link-understanding # 自动抓取并总结对话中发送的链接内容
│   ├── markdown          # 处理对话中的 Markdown 格式化（流式渲染前后的处理）
│   ├── media             # 系统音视频和图片的“处理中心”
│   ├── media-understanding # 图片、视频和音频的理解抽象层
│   ├── memory            # 实现 Agent 的长期记忆（通过向量数据库或文本存储）
│   ├── node-host         # 负责与移动端 App 或 macOS 原生应用等“节点”进行命令通讯
│   ├── pairing           # 处理设备配对流程（如扫描二维码授权登录）
│   ├── param-key.ts
│   ├── plugin-sdk        # 为所有在 `extensions/` 下的插件提供统一的 API 接口，并确保插件的导入边界安全。
│   ├── plugins           # 处理插件的发现、动态加载、校验和禁用逻辑
│   ├── process           # 底层的子进程管理系统，用于安全地启动shell命令、管理命令队列、处理进程异常恢复
│   ├── routing           # 消息路由引擎，决定消息应该发给哪个 Agent，以及处理Agent之间的协作
│   ├── runtime.ts
│   ├── scripts
│   ├── secrets           # 密钥与凭证管理，负责安全地处理各种API密钥和敏感凭证（比如从环境变量和配置读取密钥信息、在密钥被传给Agent前做审计和脱敏处理）
│   ├── security          # 安全策略模块，包含准入控制 (ACL)、黑白名单和加密令牌校验
│   ├── sessions          # 负责对话历史的持久化存储和会话状态的隔离
│   ├── shared
│   ├── terminal          # 负责命令行终端下的各种炫酷 UI、表格渲染和交互提示符
│   ├── test-helpers
│   ├── test-utils
│   ├── tts               # 语音合成与识别模块
│   ├── tui               # 终端用户界面，实现 `openclaw` 命令行交互界面（REPL）。它提供了丰富的终端文本渲染、输入历史记录、流式输出动画以及在命令行中直接进行对话的体验
│   ├── types
│   ├── web-search        # 网页搜索模块
│   └── wizard            # `openclaw onboard` 的向导模式，帮助新用户快速上手
├── Swabble          # 实现 macOS 以及 iOS 语音唤醒的组件，使用苹果原生的 `Speech.framework` 在本地进行语音识别
├── ui               # OpenClaw 的 Web 控制台，提供 Web 聊天、可视化配置管理、网管状态监控、频道管理、Token用量与统计、设备配对、Agent过程可视化等控制功能
├── vendor
│   └── a2ui         # A2UI (Agent-to-User Interface)，作用是让 Agent 可以说 UI 语言，比如展示一个包含按钮的页面，相比于文本UI界面有时更容易表达Agent的意图，尤其是做界面设计时
```

**内置关键技能**：

- **编程与开发 (Development):**
    - `coding-agent`: 核心技能，指导 Agent 如何调用 `codex` 或 `claude` CLI 进行自动编程。
    - `github` / `gh-issues`: 操作 GitHub 仓库、处理 Issue。
    - `tmux`: 学习如何管理多个终端会话。
- **个人生产力与笔记 (Productivity):**
    - `apple-notes` / `apple-reminders`: 操作 Mac 原生备忘录和提醒事项。
    - `notion` / `obsidian` / `trello`: 对接流行的生产力工具。
    - `1password`: 学习如何安全地检索密码。
- **通讯与社交 (Communication):**
    - `slack` / `discord` / `imsg` / `bluebubbles`: 学习如何通过 CLI 方式在这些平台上发送富媒体消息。
- **硬件与多媒体 (Hardware & Media):**
    - `camsnap`: 调用手机或 Mac 摄像头拍照。
    - `spotify-player` / `sonoscli`: 控制音响和音乐播放。
    - `openhue`: 控制飞利浦 Hue 智能灯光。
- **系统辅助 (System Utilities):**
    - `summarize`: 专门用于长文本摘要的策略。
    - `session-logs`: 学习如何分析和提取过往的对话日志。
    - `model-usage`: 查询当前模型的配额和成本。

## 工作流程

（本质是启动了一个支持多协议的Web服务器）

1. 启动入口 src/index.ts，提供了两种启动方式：CLI应用启动，作为库整合到其他项目中启动；
2. 构建命令树；
	 openclaw 中包含一组命令：
	 - **gateway**：最核心的命令，启动 openclaw 核心后台
	 - **agent**：用于构建客户端与核心后台交互，比如发送消息和任务
	 - acp：用于连接其他形式的客户端工具
	 - channels：用于管理与其他通信平台的连接
	 - dashboard： 在浏览器中打开Web端的控制中心
	 - 其他命令：包括配置向导、安装初始化、诊断、状态纵览、健康检查、模型管理、会话管理、程序更新、卸载等
3. 启动 gateway 命令 `gateway run`（src/cli/gateway-cli/run.ts runGatewayCommand()）
	1. 加载配置、监听端口
	2. 鉴权初始化
	3. 环境清理，清除旧的网关进程
	4. 启动循环，runGatewayLoop 确保网关崩溃后自动重启
	5. 最终启动**网关服务器 startGatewayServer()**
		网关服务器是一个 WebSocket 服务器，启动时会**初始化插件注册表**，并**挂载路由**；
		就是一个”Web后端服务器“。
4. **网关路由**（`src/gateway`）
	提供了下面几类路由：
	- **核心通信路由（WebSocket）**
		- **OpenClaw Protocol 路由**（任务处理主要看这个路由，支持Request-Response、Event Streaming 通信模式，通过`attachGatewayWsHandlers()`注册）
			- **Methods**（消息入口）
				1. 会话与对话处理 (Chat & Sessions)
					**agent / chat.send**（**最核心的入口命令**）: 向 Agent 发送消息并开始对话流。
					sessions.list / sessions.history: 获取当前的活动会话或历史消息。
					sessions.subscribe: 订阅特定会话的实时消息更新（如打字机效果输出）。
					chat.abort / sessions.abort: 强制停止正在运行的 Agent 推理。
					sessions.compact: 压缩会话历史，节省上下文窗口。
				2. 系统状态与诊断 (System & Health)
					health / status: 查询网关整体健康度和子系统运行状态。
					logs.tail: 实时流式传输网关的后台日志。
					system-presence: 查询当前所有连接节点（手机、Web、ACP）的在线情况。
					doctor.memory.status: 诊断内存使用情况。
				3. 配置与密钥 (Config & Secrets)
					config.get / config.set: 动态获取或修改 openclaw.json 配置。
					secrets.reload: 运行时重新加载环境变量中的 API 密钥等敏感信息。
				4. 节点与设备配对 (Nodes & Devices)
					node.list / node.invoke: 管理连接的辅助节点（如另一台电脑上的能力提供者）并远程调用其指令。
					device.pair.approve / node.pair.request: 处理手机端 App 发起的配对申请。
				5. 流程执行审批 (Execution & Approvals)
					exec.approvals.get: 列出正在等待用户审批的高风险指令（如删除文件、修改系统设置）。
					exec.approval.resolve: 用户点击“同意”或“拒绝”时调用的接口。
				6. 多媒体与增强能力 (Multimedia)
					tts.convert / tts.providers: 将文字转换为语音或检索可用的配音提供商。
					voicewake.get / voicewake.set: 获取或配置唤醒词感知状态。
					cron.list / cron.run: 管理和手动触发网关定制定时任务。
			- **Events**（响应出口）
				**chat**: 实时推送 Agent 的逐字回复内容（Streaming）。
				session.message: 告知会话中出现了新消息（由其他端发送）。
				presence: 当有新设备上线（如手机 App 联网）或离线时发送。
				tick: 网关的这种脉冲信号（通常包含基础性能数据）。
				heartbeat: 各个节点与网关保持活跃的信号。
				exec.approval.requested: 实时通知客户端有高风险操作需要用户介入。
				update.available: 提示有可用的 OpenClaw 版本更新。
		- ACP (Agent Client Protocol): 用于 IDE（如 Zed）驱动 Agent。
	- HTTP路由
		- **Webhook入口**
			可以接收来自Github、Gmail等外部系统通知，并触发Agent任务
		- 会话管理
		- 工具执行
		- 通道回调
	- AI 协议路由
		- 与模型交互（提供兼容 OpenAI的对话接口）
		- 查看支持的所有子模型
		- 生成式响应
	- 前端与交互中心
		- Web控制面板
		- 提供声明式UI组件的静态资源
	- 系统与诊断
		- 健康检查
		- 插件路由
			网关会自动挂载 `extensions/` 目录下各插件定义的自定义 HTTP 端点
5. **agent / chat.send** & **chat**
	这三个命令对应的处理器分别是：
	- src/gateway/server-methods/agent.ts L198 (一个500多行的函数)
		- 识别并执行 `/new` 或 `/reset` 等会话重置指令。
		- 注入时间戳。
		- 处理多媒体附件（图片、文件）。
		- 最终调用 src/commands/agent.ts 里的 `agentCommandFromIngress` 来进入核心执行引擎。
	- src/gateway/server-methods/chat.ts L1225 (一个400多行的函数)
		- 是 agent 命令的简化版本，主要用于普通的聊天场景，它共享了大部分会话管理和指令解析逻辑，最后同样收敛到 agentCommandFromIngress。
6. **agentCommandFromIngress 核心处理流程**（内部流程依然比较复杂）
	1. **预处理与环境准备**
		- 消息增强: 向用户消息中注入时间戳、内部事件上下文（Internal Events）等，帮助模型理解当前的时间和状态。
		- 配置与密钥加载: 通过网关安全地解析 `openclaw.json` 中的 API 密钥（Secrets），确保运行时不泄露敏感信息。
		- Agent 身份校验: 验证指定的 `agentId` 是否存在。
		- 会话解析 (`resolveSession`):
		    - 如果是新请求，创建新的 `sessionId`。
		    - 如果是已有会话，根据 `sessionKey` 加载历史上下文（Token 使用量、状态等）。
		- 工作空间准备: 确保对应的 Agent 工作目录存在，并初始化必要的 `bootstrap` 文件（如模型需要的环境配置文件）。
	2. **ACP/标准模式分支**
		检查当前会话是否连接了 **ACP (Agent Client Protocol)**（例如正在使用 Zed 编辑器）：
		- ACP 模式: 如果是 ACP 会话，它不直接运行模型，而是开启一个 **“转播模式”**：将请求发送给外部编辑器（如 Zed），并监听编辑器返回的文本增量、工具调用等事件，再将这些事件重新包装成 OpenClaw 的 agent 事件广播出去。
		- **标准模式**: 进入 OpenClaw 自己的执行引擎（即后面的执行循环）。
	3. **高鲁棒性的执行循环** (runWithModelFallback)
		循环实现位于 `src/agents/model-fallback.ts` L516。
		不是简单地调一次 API，而是包含了一个 **故障切换（Failover）** 循环：
		- **尝试运行**: 首选用户配置的主模型（如 Claude 3.5 Sonnet）。
		- **自动重试/切换**: 如果主模型因为欠费、限流、网络超时或模型拒绝回答等原因失败，它会自动根据路由规则切换到备用模型（如 GPT-4o），确保任务不中断。
	4. **具体的Agent运行器**
		根据 Provider 的不同，它会选择不同的底层实现：
		- CLI Provider: 如果配置使用的是 `claude-cli` 这种外部命令行工具，它会通过 `runCliAgent` 派生子进程执行。
		- **Embedded Provider**: 这是最常用的模式，调用内置的 **PI Agent 引擎** (入口`runEmbeddedPiAgent`)
		    引擎实现位于：`src/agents/pi-embedded-runner`，代码量 2.7W行
			这个引擎负责：
		    - 管理 LLM 流式输出。
		    - **工具调用 (Tool Use)**: 决定是否需要调用本地工具（如文件读写、运行脚本）。
		    - **思考过程 (Thinking)**: 处理模型的思维链输出。
	5. 后置处理与交付
		- **结果外发 (`deliverAgentCommandResult`)**: 如果请求中开启了 `deliver: true`，执行结果会自动发送到对应的外部 Channel（如推送给你的 Telegram 或 Discord）。
		- **状态同步**: 运行结束后，更新本轮对话产生的 Token 消耗、费用统计，并持久化到本地 Session 数据库。
		- **生命周期通知**: 发送 `lifecycle: end` 事件，通知前端（手机端、Web 端）任务已完成。
7. **PI Agent 引擎工作原理**


## 任务处理流程

（以个人所得税报税为例）

### 1. 定时任务触发流程

- **涉及模块**: **`src/cron/`**
- **作用说明**: 
	- **`src/cron/` (定时任务)**：如果你希望每月定时自动开始这个流程，可以使用 `cron` 模块进行调度。
- **相关文档**：
	- [定时任务](https://docs.openclaw.ai/zh-CN/automation/cron-jobs)
### 2. 数据采集 (Data Collection)

- **涉及模块:** **`src/config/`**, **`src/secrets/`**, **`src/agents/`**
- **作用说明:**
    - **`src/config/`**: 用于读取和解析存储员工信息的本地 JSON/YAML 配置文件。
    - **`src/secrets/`**: 申报密码等敏感信息不应直接写在配置中，而应通过此模块安全地从环境变量或加密存储中读取。
    - **`src/agents/`**: Agent 会读取这些数据作为“初始上下文”，为后续的填表逻辑做准备。
### 3. 登录自然人电子税务局 Web 端

- **涉及模块:** **`src/browser/`**, **`src/secrets/`**
- **作用说明:**
    - **`src/browser/`**: 调用其中的 `clickViaPlaywright` 和 `typeViaPlaywright` 模拟点击登录方式并输入申报密码。
    - **`src/browser/chrome.ts`**: 负责启动带有特定 Profile（可能包含登录缓存）的 Chrome 浏览器。
    - **`src/secrets/`**: 提供安全的登录凭证。
### 4. 员工信息录入

- **涉及模块:** **`src/browser/`**, **`src/agents/`**
- **作用说明:**
    - **`src/browser/` (工具端)**: 调用 `take_snapshot` 获取当前页面的员工列表。
    - **`src/agents/` (逻辑端)**: Agent 比较本地配置文件与网页快照中的员工信息。如果发现缺失，Agent 会指挥浏览器模块进入“人员信息采集”页面进行逐项填报。
### 5. 进入申报入口并填写申报表

- **涉及模块:** **`src/browser/`**, **`src/browser/pw-tools-core.interactions.ts`**
- **作用说明:**
    - **`src/browser/`**: 处理复杂的页面跳转逻辑（如点击“综合所得申报”等）。
    - **`interactions.ts`**: 核心填表逻辑。针对“复制上月数据”或“录入新数据”，它会精确操作网页上的每一个 Input 框。
### 6. 提交申报并缴纳税款 (关键的人机交互环节)

- **涉及模块:** **`src/interactive/`**, **`src/ui/`**, **`src/canvas-host/`**, **`src/channels/`**
- **作用说明:**
    - **`src/browser/`**: 采集已经填好的报表截图（`take_screenshot`）或表格数据。
    - **`src/interactive/`**: 生成一个带有“确认申报”按钮的交互式卡片。
    - **`src/canvas-host/`**: 如果需要更精细的报表预览，Agent 会在 Canvas 窗口（网页、安卓或 Mac 端）渲染一个实时报表预览界面。
    - **`src/channels/`**: 将预览信息和确认按钮发送到你的手机（如 Telegram/WhatsApp），等待你点击“发送申报”的指令。
    - **`src/browser/`**: 收到确认后，执行最终的申报和支付流程跳转。
### 7. 支付完成后确认信息

- **涉及模块:** **`src/media/`**, **`src/channels/`**, **`src/sessions/`**
- **作用说明:**
    - **`src/media/`**: 浏览器模块截图后，以此模块负责处理截图（如压缩、格式转换）并将电子税票保存为图像或 PDF。
    - **`src/channels/`**: 将最终的税表、支付金额和成功提示推送回给用户。
    - **`src/sessions/`**: 完整记录本次申报的每一个步骤、截图和系统日志，形成申报审计记录。
### 其他辅助模块

- **`skills/` (专家技能包)**: 建议将整个流程封装在 `skills/tax-agent/SKILL.md` 中。该文档会教导 Agent 特定的报税术语和容错策略（例如：如果遇到验证码该如何提示用户手动干预）。
- **`src/logging/`**: 在涉及税务这种严肃金融操作时，详细的日志记录对于后期追溯至关重要。
