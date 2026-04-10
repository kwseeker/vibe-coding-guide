# ConversationRuntime

**推理主循环（run_turn）**:

1. 输入捕获与记忆写入 (L301-L305)
	记录开始：调用 record_turn_started() 做回合记录（SessionTracer 将用户输入信息保存到 Telemetry）。
	输入预处理: **将用户通过 CLI 输入的原始文本存入 self.session.messages**。

2. **进入推理主循环** (L312-L470) — ReAct (Reasoning and Acting) 模式
	**这是一个 loop 循环，每一圈代表一次“思考 -> 动作 -> 观察”的过程**：
	
	1. 迭代限制 (L314-L320)
		检查**迭代次数**。如果 Agent 陷入死循环，在此处会被强行切断并抛错（RuntimeError）。
	
	2. 请求构造并通过 AnthropicRuntimeClient 调用 LLM 发起推理 (L322-L344，**思考**)
		**构造请求**: **将系统提示词 + 完整的历史对话记忆打包发给模型**。
		```rust
		let request = ApiRequest {
			system_prompt: self.system_prompt.clone(),
			messages: self.session.messages.clone(),
		};
		```
		**流式响应**: 通过 api_client ( AnthropicRuntimeClient ) 接收流式结果，并用 build_assistant_message 组装成一个完整的助手回复。
		```rust
		let events = match self.api_client.stream(request) {
				Ok(events) => events,
				Err(error) => {
					self.record_turn_failed(iterations, &error);
					return Err(error);
				}
			};
		// 回复信息包括：LLM 返回信息、Token消耗、缓存命中情况
		let (assistant_message, usage, turn_prompt_cache_events) =
			match build_assistant_message(events) {
				Ok(result) => result,
				Err(error) => {
					self.record_turn_failed(iterations, &error);
					return Err(error);
				}
			};
		```
		**记录统计**: 使用 SessionTracer 记录这一轮消耗的 Token、 迭代次数、响应消息、待调用的工具， 在内存中记录缓存命中情况。
	
	3. **提取工具调用** (L345-L368)
		扫描 LLM 的回复。**如果回复中不包含 ToolUse**（工具调用），说明 Agent 已经想好了最终答案，通过 break 退出循环及本次 Turn。
		**如果包含 ToolUse**，则进入下一步。
	
	4. **安全授权与工具执行** (L370-L469，**动作**)
		对于模型提出的每一个工具调用，系统会循环执行以下精密步骤：
		1. 运行前置钩子 (Pre-Tool Use Hook)：
			插件可以在这里介入，比如修改模型输入的参数，或者直接根据安全规则拦截调用。
		2. 权限检查 (Permission Check)：
		    根据 `permission_policy` 判定。如果是高危操作（如写文件），可能会调用 `prompter`（即你在终端看到的 `[y/N]` 提示）请求用户手动授权。
		3. 物理执行 (Execution)：
		    如果获准执行，调用 `tool_executor.execute`（这会触发我们之前讨论过的插件子进程或内置函数）。
		4. 运行后置钩子 (Post-Tool Use Hook)：
		    根据执行成功或失败，运行对应的后置钩子。钩子可以再次审视结果，甚至将成功的结果判定为失败（比如发现输出包含敏感信息）。
		5. 结果反馈 (Tool Result)：
			将工具的输出、钩子的反馈信息合并，构造一个 `tool_result` 消息。
		    存入会话：将结果存入历史。这意味着在下一次循环迭代中，模型将能看到这个工具的结果，并基于此进行下一步思考。

		三种钩子：
		- `PreToolUse` 钩子 (工具运行前)
			**代码位置**：`self.run_pre_tool_use_hook(&tool_name, &input)`。
			**它的作用**：
		    - **参数预处理**：它可以修改模型生成的参数。例如：如果模型想写一个绝对路径的文件，钩子可以强行将其改为相对路径以增加安全性。
		    - **权限静默升级**：它能返回一个“权限覆盖”，告诉系统：虽然规则说这个操作需要询问用户，但因为是在特定安全目录，我决定直接准许执行。
		    - **拦截与终止**：它具有“一票否决权”。如果钩子返回 `Denied` 或 `Cancelled`，该工具调用将直接中止，根本不会进入真正的执行阶段。
		
		 - `PostToolUse` 钩子 (工具运行成功后)
			**代码位置**：`self.run_post_tool_use_hook(...)`。
			**它的作用**：
		    - **结果审计**：工具虽然运行成功了，但钩子会检查输出内容。例如：如果执行 `cat` 命令发现输出了敏感的 API Key，钩子可以拦截这个结果，并反馈给模型“报错：输出包含敏感信息”，阻止模型看到内容。
		    - **输出修饰**：它可以对工具结果进行美化或摘要。
		    - **链式触发**：你可以配置钩子在某个工具运行成功后，自动清理临时文件。
		
		- `PostToolUseFailure` 钩子 (工具运行失败后)
			**代码位置**：`self.run_post_tool_use_failure_hook(...)`。
			**它的作用**：
		    - **自动修复建议**：这是它最强大的用途。当一个命令（如 `npm install`）报错时，钩子可以识别错误类型（如“权限不足”），并在返回给模型的错误消息中附带建议：“请尝试使用超级用户权限，或检查磁盘空间”。
		    - **故障诊断**：收集更详细的系统错误日志，帮助大模型更好地理解为什么失败。

3. 会话整理与结束 (L472-L485)
	1. **自动压缩 (Compaction)**：调用 `maybe_auto_compact`。如果对话历史太长（超过 Context Window 限制），系统会自动进行摘要压缩，以节省 Token 并保持核心记忆。
	2. **生成摘要**：构建 `TurnSummary`，包含本次回合的所有消息、工具结果、Token 消耗统计（Usage）等。
	3. **返回结果**：至此，一个回合执行完毕。用户将在终端看到最终的回复。
