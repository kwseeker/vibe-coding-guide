# Claude

让 Claude Code 有完全的自主权的启动方式：
```sh
claude --dangerously-skip-permissions
```

 **常用命令**：
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
