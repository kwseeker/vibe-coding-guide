# LM Studio 

## 部署本地大模型

```sh
curl -fsSL https://lmstudio.ai/install.sh | bash

# 无头部署
# 后台管理进程（控制中心）
lms daemon up          # Start the daemon
lms get <model>        # Download a model
# 推理引擎 + API 服务，HTTP 接口默认是 1234
lms server start       # Start the local server
# 如果要设置 API 密钥
export LMS_API_KEY="你自己的密钥"
# 如果要将本地模型接口开放给局域网其他设备使用，使用 --bind <addr>
lms server start --bind 0.0.0.0 -p 1234
lms chat               # Open an interactive session

# 局域网内其他设备测试连接局域网中的本地模型，比如请求查询模型列表接口
curl http://192.168.8.108:1234/v1/models

# 常用命令
# 查看已经加载的模型列表
lms ps
# 查看磁盘上可用的模型列表
lms ls
# 加载模型 -c/--context-length 指定最大上下文
lms load qwen/qwen3.5-9b -c 32768
```

**GUI 启动链路**：  
`Electron App → 内部 Controller → Server → 推理引擎 → 模型实例`

**CLI 启动链路**：  
`lms CLI → lms daemon → Server → 推理引擎 → 模型实例`

可能遇到的问题：
- GUI 启动本地模型后 CLI 查看服务状态是 “The Server is not running” 
	GUI启动状态 和 CLI启动状态不会自动同步。
- LM Studio 一段时间不使用后再访问会失败
	因为 LM Studio 过一段时间不使用后会自动卸载模型，配置位于 `Settings -> Developer -> On-Demand Loading and Model TTL -> 最大空闲时间`。
	默认空闲 60 分钟后会被卸载。

## 对接 Agent

- **对接到 `CherryStudio`**
	`配置 -> 模型服务 -> LM Studio -> 修改API地址 和 API密钥（有设置的话）-> 搜索模型列表并添加`。

- **对接到 `Claude Code`**
	修改 `claude` 配置：
	```json
	{
	  "env": {
	    "ANTHROPIC_AUTH_TOKEN": "",
	    "ANTHROPIC_BASE_URL": "http://192.168.8.108:1234",
	    "API_TIMEOUT_MS": "3000000",
	    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1"
	  },
	}
	```
	可能遇到的问题：
	- LM Studio 接入 Claude Code 模型崩溃
		默认加载的模型上下文设置是4K，而 Claude Code 启动时就往模型中塞了 18K 的提示词导致模型上下文被撑爆。

- **对接到 `OpenClaw`**
	`openclaw onboard` 中选择 `Custom Provider`，设置模型 API Base URL： http://192.168.8.108:1234/v1 ，设置 API KEY（如果 LMStudio 没有设置 API KEY，就随便填但是不能设置为空，不要被配置向导误导，否则后面会报错），
	设置 `Endpoint compatibility`: OpenAI-compatible；然后选择本地模型ID。
	测试联通性：`openclaw doctor`。

## 开放接口

支持三种API：
- LM Studio REST API: [Stateful Chats, MCPs via API](https://lmstudio.ai/docs/developer/rest)  
- OpenAI‑compatible: [Chat, Responses, Embeddings](https://lmstudio.ai/docs/developer/openai-compat)  
- Anthropic-compatible: [Messages](https://lmstudio.ai/docs/developer/anthropic-compat)
LM Studio 开发者窗口中会列举接口列表。
