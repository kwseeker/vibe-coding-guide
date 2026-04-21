# oh-my-codex

`oh-my-codex` (简称 **OMX**) 并不是要取代 OpenAI Codex CLI，而是作为一个 **“协调层”（Orchestration Layer）** 运行在其之上。它的核心理念是：**你是一位“指挥家”（Conductor），而不是“演奏者”（Performer）。**

结合官方文档和源码分析，以下是使用 OMX 实现项目的核心逻辑和具体示例。

默认内置了 33 个提示词，36 个技能， 5 个 MCP服务器，以团队协作的方式工作。
团队角色：


## 使用入门

```sh
# 1 安装相关
# codex API keys 获取：https://platform.openai.com/home
npm install -g oh-my-codex

# 2 设置和验证
omx setup && omx doctor

# 3 启动
# 进入项目目录启动 OMX，以一个功能需求为周期（一次工作流完成一个功能）
omx --high
omx --madmax --high
# 让 Agent 澄清需求，确认边界和验收标准，类似于头脑风暴
$deep-interview
# 需求已经差不多清楚但方案还没定，将任务拆分为可执行计划（比如前后端怎么实现/修改）、梳理风险和测试（需要添加哪些测试）
$ralplan 
# 方案确认后执行，开发完成后自行运行测试和校验
$ralph
# 并行 workflow 
team 3：executor
omx team status <team-name>

# 4 状态查看 & 结束工作流
# 查看状态：工作模式、...
omx status
# 结束当前工作流
omx cancel
```


### 1. 核心工作流：OMX 的“三部曲”

OMX 推荐的标准化执行路径是：**澄清 → 规划 → 执行/验证**。

|阶段|关键词/指令|目的|
|---|---|---|
|**1. 深度访谈**|`$deep-interview`|解决模糊性。通过苏格拉底式提问确认需求边界（非目标、核心功能）。|
|**2. 共识规划**|`$ralplan`|生成架构设计方案。由 `Planner`、`Architect`、`Critic` 三方审核并达成一致。|
|**3. 并行执行**|`$team`|启动多 Agent 协作。使用 tmux 开启多个并行窗口（Worktrees）快速编写代码。|
|**或：持久执行**|`$ralph`|“滚石模式”。Agent 会不断尝试直到 `Architect` 验证任务目标完全达成。|

---

### 2. 如何使用 OMX 实现一个个人网站？

假设你想从零开始构建一个个人网站，你可以按照以下步骤操作：

#### 第一步：启动 OMX 增强环境

在终端进入你的项目目录，运行初始化命令：

bash

omx setup  # 初始化 .omx 目录和 AGENTS.md 合约

omx --madmax --high  # 以高并发模式启动领导者 (Leader) 会话

#### 第二步：需求澄清 (Deep Interview)

直接在 Codex 界面输入：

> `$deep-interview "我想用 React + CSS 实现一个个人作品集网站，包含博客和代码展示功能"`

- **OMX 行为**：它会开始反问你：需要什么配色方案？是否需要 SEO 优化？博客文章放在本地还是 CMS？
- **结果**：生成一份详尽的需求文档（通常在 `.omx/specs/` 下）。

#### 第三步：方案设计 (Ralplan)

输入：

> `$ralplan "基于刚才的需求，设计网站的技术架构和组件拆分"`

- **OMX 行为**：`Architect` 角色会提出方案，`Critic` 会指出潜在的性能问题或维护难点，最终生成 `.omx/plans/autopilot-impl.md`。

#### 第四步：全自动构建 (Autopilot)

如果你希望完全放手让 OMX 自己搞定（从代码编写到测试）：

> `$autopilot "根据核准的计划，实现整个 React 代码库并确保通过 lint 检查"`

- **OMX 行为**：
    1. **Expansion**: 细化任务列表。
    2. **Execution**: 启动 `$team` 调用多个 `executor` 角色并行写组件。
    3. **QA**: 自动运行 `npm run build` 和测试，遇到报错自动修复。
    4. **Validation**: 由 `Architect` 最终检查网页功能是否完整。

---

### 3. 开发者进阶：源码中的秘密

#### `AGENTS.md`：你的项目法律

在 OMX 目录中，`AGENTS.md` 是所有 Agent 遵循的 **“操作合约”**。

- 如果你希望网站使用特定的 CSS 框架（如 Vanilla CSS），你应该修改 `AGENTS.md` 中的 `<operating_principles>`。
- 任何时候 Agent 偏离了你的预期，检查这个文件是否定义了明确的规则。

#### `.omx/` 目录：项目的记忆

- `.omx/state/`: 存储当前循环的进度（如果崩溃了，运行 `omx resume` 可以从这里恢复）。
- `.omx/notepad.md`: 这是 Agent 间共享的“剪贴板”，存储临时状态和需要注意的变量。

#### 团队模式 (Team Mode)

如果你运行 `omx team 3:executor "实现 Hero 组件"`：

- OMX 会在后台开启 3 个 tmux 窗格。
- 每个窗格拥有独立的 **git worktree**（隔离的工作树），这意味着它们可以同时修改不同的文件而不会发生冲突。
- 任务完成后，OMX 会自动将这些分支 `merge` 到主干。

---

### 4. 总结：你应该怎么用？

1. **对于新功能**：永远先用 `$deep-interview` 或 `$plan`。
2. **对于大规模重构**：使用 `$ralph`，因为它有强大的验证闭环（Validation Loop）。
3. **对于追求速度**：使用 `$team` 或 `$ultrawork` 开启多核并行。
4. **遇到报错**：使用 `$build-fix` 自动扫描终端输出并修复。

**一句话建议**：把 OMX 看作是一个拥有 **30+ 种专家头衔**（Architect, Executor, Security Reviewer 等）的虚拟团队，你只需要下达明确的指令并审核他们的产出即可。