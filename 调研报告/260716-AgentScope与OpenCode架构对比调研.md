# AgentScope 与 OpenCode 工作原理、架构设计及运行模式对比调研

> 调查时间:2026-07-16
> 调查对象:[agentscope-ai/agentscope](https://github.com/agentscope-ai/agentscope)、[anomalyco/opencode](https://github.com/anomalyco/opencode)
> 评估维度:项目定位、工作原理、架构设计、运行模式、多 Agent、状态与记忆、权限与安全、扩展能力、成熟度及适用场景
> 源码检查点:AgentScope `0745ea5`,OpenCode `1754480`;动态数据以调查当日 GitHub API 与仓库页面为准

---

## 一、结论先行

这两个项目都运行“模型推理 → 调工具 → 回灌结果 → 继续推理”的 Agent 循环,但**不在同一个产品层级**:

- **AgentScope**:面向开发者的通用 Agent 框架与服务化底座。核心交付物是 Python API、统一事件模型、中间件、权限、沙箱、长期记忆、RAG 和分布式 Agent Team。
- **OpenCode**:面向软件开发场景的完整 AI Coding Agent 产品。核心交付物是 CLI/TUI、桌面端、Web、IDE、HTTP Server、会话系统、代码工具、权限审批、文件快照和插件生态。
- 简单说:**AgentScope 用来“造 Agent 产品”;OpenCode 本身就是一个已经造好的“编码 Agent 产品”,同时开放配置、SDK、Server 和插件接口。**

因此它们不是严格替代关系:

| 目标 | 更合适 |
|---|---|
| 构建客服、研究、数据分析、企业流程等通用 Agent 应用 | AgentScope |
| 直接使用或二次开发 AI 编码助手 | OpenCode |
| 构建多租户、多会话、可分布式扩展的 Agent SaaS | AgentScope |
| 统一终端、IDE、桌面、CI 中的编码 Agent 体验 | OpenCode |
| 强语义记忆、RAG、沙箱后端、多 Agent 消息协作 | AgentScope |
| 强代码工作区感知、LSP、代码快照、撤销、开发者工具链 | OpenCode |

---

## 二、项目基线

| 维度 | AgentScope | OpenCode |
|---|---|---|
| 定位 | 通用 Agent 框架 + Agent Service | AI Coding Agent 产品 + Server/SDK |
| 主语言 | Python 3.11+ | TypeScript + Bun |
| 默认分支 | `main` | `dev` |
| 当前稳定版 | `v2.0.4`,2026-07-07 | `v1.18.2`,2026-07-15 |
| 许可证 | Apache-2.0 | MIT |
| Stars | 27,911 | 186,308 |
| Forks | 3,203 | 23,340 |
| Watchers | 156 | 718 |
| GitHub UI 待处理量 | 215 Issues + 65 PRs | 约 3.6k Issues + 1.1k PRs |
| Releases | 42 | 840 |
| 仓库规模 | 约 407 次当前仓提交 | 15,005 次当前仓提交 |
| 核心入口 | Python `Agent.reply_stream()` | `opencode` CLI/TUI + HTTP Server |
| 生态协议 | MCP、AG-UI、OTel | MCP、ACP、LSP、OpenAPI 3.1、AI SDK |

动态数据来源:[AgentScope API](https://api.github.com/repos/agentscope-ai/agentscope)、[OpenCode API](https://api.github.com/repos/anomalyco/opencode)。Stars 和发版数只反映采用规模与节奏,不等同于架构稳定性。

---

## 三、AgentScope

### 3.1 工作原理

AgentScope 2.0 把一次 Agent 回复建模为一个**可流式恢复的 ReAct 状态机**:

```text
UserMsg
  ↓
Agent.reply_stream()
  ↓
_reply_impl()
  ├─ reasoning:格式化上下文 → 调模型 → 流式生成事件
  ├─ acting:解析 tool call → 权限判断 → 执行工具
  ├─ 将 tool result 写回上下文
  └─ 未完成则继续 reasoning/acting
  ↓
ReplyEndEvent + 完整 AssistantMsg
```

核心路径位于 [`src/agentscope/agent/_agent.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/agent/_agent.py):

1. 用户调用 `Agent.reply_stream(UserMsg(...))`。
2. `_reply_impl` 初始化 `reply_id`、迭代次数和回复状态。
3. `_check_next_action` 判断下一步是继续模型推理、执行工具、返回最终消息,还是因中断或超过最大迭代数结束。
4. `_reasoning_impl` 用 Formatter 把系统提示、上下文和工具 Schema 转成目标模型格式,调用 `ChatModelBase`,再将模型流转换为 Text、Thinking、ToolCall、Data 等结构化事件。
5. `_acting` 收集可执行工具,先经过 `PermissionEngine`,安全工具可按 `is_concurrency_safe` 分组并发,非并发安全工具串行,工具结果作为 `ToolResultBlock` 写回上下文。
6. 如果模型仍请求工具或尚未形成最终答案,则进入下一轮。

#### 最关键的设计:Message 与 Event 是同一回复的两种视图

官方文档明确规定:

- `Msg` 是 Agent 间通信和持久化单元;
- `Event` 是前端流式渲染和 HITL 单元;
- 一次 `reply_stream` 产生的所有事件最终恰好组成一个完整 `AssistantMsg`;
- 通过 `Msg.append_event()` 重放事件,可以恢复完整消息。

这使后端、SSE 前端、审计日志和断线恢复共享同一套语义,而不是分别维护“流式输出格式”和“最终消息格式”。

证据:[Message & Event 文档](https://docs.agentscope.io/latest/en/building-blocks/message-and-event)、[`message/_base.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/message/_base.py)、[`event/_event.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/event/_event.py)。

### 3.2 架构设计

AgentScope 可以拆成四层。

#### A. 基础数据层

- `message/`:`Msg` 与 Text、Thinking、Data、ToolCall、ToolResult、Hint 等 Block。
- `event/`:模型流、工具流、生命周期、HITL、Custom Event。
- `state/`:上下文、摘要、权限上下文、工具缓存、任务上下文、中间件状态。
- `formatter/`:把统一消息转换成不同模型厂商的 API 格式。

#### B. Agent 运行时

- `agent/Agent`:项目刻意保留的单一核心 Agent 类。
- `model/ChatModelBase`:模型调用、流式响应、重试、结构化输出。
- `tool/Toolkit`:统一注册本地工具、MCP、Skill、Tool Group。
- `permission/PermissionEngine`:工具调用前的决策引擎。
- `middleware/MiddlewareBase`:对 reply、reasoning、acting、model call、context compression 等生命周期做洋葱式拦截。

这种设计的核心不是堆叠多个 Agent 子类,而是:

```text
一个 Agent 核心类
+ 不同 Model
+ 不同 Toolkit
+ 不同 Middleware
+ 不同 PermissionContext
+ 不同 Workspace
```

它比“每种 Agent 定义一个新类”更强调组合。

#### C. 能力扩展层

- `rag/`:知识库、文档解析、分块、向量库。
- `middleware`:RAG、Agentic Memory、Mem0、ReMe、Tracing、TTS。
- `workspace/`:Local、Docker、E2B、K8s、OpenSandbox、Daytona。
- `mcp/`、`skill/`、`embedding/`、`tts/`:外部能力适配。

#### D. 服务化层

`agentscope.app.create_app()` 提供 FastAPI Agent Service:

- 多租户、多会话;
- Session、Credential、Knowledge Base、Workspace 等 API;
- Redis/InMemory Storage 与 Message Bus;
- Index Worker、定时任务和后台工具;
- AG-UI/Web UI 接入;
- Agent Team。

核心入口见 [`app/_app.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/app/_app.py)。

### 3.3 权限与 HITL

AgentScope 的工具状态不是简单“成功/失败”,而是显式状态机:

```text
pending
  ├─ deny → finished
  ├─ ask → asking → 用户批准 → allowed
  └─ allow → allowed
allowed
  ├─ 本地执行 → finished
  └─ 外部执行 → submitted → 外部结果 → finished
```

权限模式包含:

- `DEFAULT`
- `EXPLORE`
- `ACCEPT_EDITS`
- `BYPASS`
- `DONT_ASK`

同时支持:

- `RequireUserConfirmEvent`
- `UserConfirmResultEvent`
- `RequireExternalExecutionEvent`
- `ExternalExecutionResultEvent`
- 用户中断与未完成工具状态清理

这使它不仅适合交互式审批,也适合“模型决定调用、外部系统真正执行”的企业集成模式。

证据:[`permission/_engine.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/permission/_engine.py)、[`message/_block.py`](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/message/_block.py)。

### 3.4 多 Agent 运行模式

AgentScope 的 Agent Team 不是在 Leader 内部嵌套几个协程,而是:

- Leader 是一个 Session;
- 每个 Worker 也是独立 Session;
- 每个 Session 有自己的状态、事件流和 Workspace 绑定;
- 通过 `TeamCreate`、`AgentCreate`、`AgentInvite`、`TeamSay`、`TeamDelete` 协作;
- 消息经过 Redis-backed Message Bus 写入目标 Session Inbox;
- 任意服务节点上的 Wakeup Dispatcher 都可唤醒目标 Worker;
- `InboxMiddleware` 在下一轮 reasoning 前把团队消息注入上下文。

因此它的多 Agent 本质是**分布式 Session 间消息协作**,适合多进程、多节点和长时间任务。

证据:[Agent Team 文档](https://docs.agentscope.io/latest/en/deploy/agent-team)。

### 3.5 运行模式

1. **嵌入式单 Agent**:Python 进程内直接调用。
2. **多 Agent 应用**:多个 Agent 实例或 Agent Team。
3. **FastAPI Agent Service**:HTTP/SSE、租户、会话、凭据和知识库。
4. **异步流模式**:`async for event in reply_stream()`。
5. **后台任务模式**:工具结果延迟返回,再唤醒会话。
6. **沙箱模式**:本地、容器、K8s、E2B、Daytona 等。
7. **分布式模式**:Redis Storage/Message Bus + 多 Worker。
8. **可观测模式**:Tracing Middleware + OpenTelemetry/OTLP。

### 3.6 优势与风险

#### 优势

- 通用性强,适合从零构建领域 Agent。
- Event/Message 一致性设计清晰,前后端与审计容易统一。
- HITL、外部执行和沙箱是核心能力,不是外围补丁。
- Agent Team 可跨进程、跨节点运行。
- RAG、长期记忆、Workspace、OTel 更贴近生产 Agent 平台。

#### 风险

- 2.0 于 2026-05 才发布,当前仍处于快速扩展期。
- 1.x 到 2.x API 变化较大,历史项目有迁移成本。
- 没有 LangGraph 式静态 DAG;任务编排更多依赖模型自行规划。
- 服务层虽开箱即用,但主要技术栈被限定在 Python/FastAPI/Redis 范式。
- 抽象面较宽,真正生产落地仍需自己定义业务状态、租户策略、工具权限和失败补偿。

---

## 四、OpenCode

### 4.1 工作原理

OpenCode 的核心不是单纯的 Agent 类,而是一个**会话持久化的客户端—服务端编码 Agent 系统**:

```text
TUI / Desktop / Web / IDE / CLI run / SDK
  ↓ HTTP
OpenCode Server
  ↓
SessionPrompt.runLoop()
  ↓
加载 Session、Agent、Model、AGENTS.md、Skills、Tools、MCP
  ↓
LLM 流式生成
  ├─ text/reasoning
  ├─ tool call
  └─ finish/error
  ↓
Permission ask/allow/deny
  ↓
执行 read/edit/bash/lsp/MCP/subagent 等工具
  ↓
工具结果写入 Session + SQLite
  ↓
SSE 推送客户端
  ↓
继续下一轮 / compact / stop / abort
```

官方文档确认,执行 `opencode` 时会同时启动 TUI 和本地 Server,TUI 本身是 Server 的客户端;Server 暴露 OpenAPI 3.1,并用于生成 SDK。

证据:[Server 文档](https://opencode.ai/docs/server/)。

#### 生产主循环

当前生产 CLI 仍走 [`packages/opencode/src/session/prompt.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/session/prompt.ts) 中的 `SessionPrompt.runLoop()`:

1. 读取当前 Session 的消息与未完成任务。
2. 判断上一条 Assistant 消息是否已经自然结束。
3. 选择 Agent 和 Model。
4. 处理 Subagent Task 或 Compaction Task。
5. 组装环境、项目规则、`AGENTS.md`、Skill、MCP 指令与历史消息。
6. `SessionProcessor.process()` 启动一次模型流。
7. 处理 text、reasoning、tool-call、tool-result、step-start、step-finish 等事件。
8. 工具执行完成后,将结果持久化并进入下一轮。
9. 达到 Agent `steps` 上限时注入强制文本收尾提示。
10. 上下文溢出时触发 Compaction;用户中止时取消运行 Fiber 并清理运行中的工具。

事件处理与工具闭环在 [`session/processor.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/session/processor.ts)。

### 4.2 架构设计

OpenCode 可拆成六层。

#### A. 多客户端层

- TUI
- Desktop Electron
- Web
- IDE 扩展
- `opencode run`
- ACP 客户端
- GitHub/GitLab CI
- JS/TS SDK

这些客户端共享 Server,而不是各自复制 Agent 运行时。

#### B. Server 与协议层

- 本地或远程 HTTP Server;
- OpenAPI 3.1;
- SSE 事件流;
- SDK 自动生成;
- HTTP Basic Auth;
- `attach` 可将 TUI 接到远端 Server;
- mDNS 可发现局域网 Server。

实际 Server 入口位于 [`packages/opencode/src/server/server.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/server/server.ts)。

#### C. Session/Agent 编排层

OpenCode 内置:

- Primary Agent:`build`、`plan`
- Subagent:`general`、`explore`、`scout`
- 隐藏系统 Agent:`compaction`、`title`、`summary`

Subagent 通过 Task 工具创建子 Session;父子 Session 可导航、并行运行。它更像**面向开发任务的层级委派**,不是 AgentScope 那种 Redis 消息驱动的通用 Agent Team。

证据:[Agents 文档](https://opencode.ai/docs/agents/)。

#### D. Tool 与开发环境层

内置能力包括:

- `read`、`edit`、`write`、`apply_patch`
- `glob`、`grep`
- `bash`
- `lsp`
- `skill`
- `task`
- `question`
- `webfetch`、`websearch`
- MCP Tool
- 自定义 TypeScript Tool

OpenCode 对代码工作区做了明显的垂直优化:文件编辑、命令执行、LSP、项目规则、Git 快照、Session Diff、Undo/Redo 都进入核心路径。

#### E. 持久化与可逆层

- SQLite + Drizzle 保存 Session、Message、Part、Todo、Session Input 等;
- 默认数据库位于 `~/.local/share/opencode/opencode.db`;
- 每个模型流增量写入消息与 Part;
- SSE 事件由持久化状态驱动客户端更新;
- 独立内部 Git 仓库保存工作区快照。

内部 Git 不使用项目自己的 `.git`,而是类似:

```text
~/.local/share/opencode/snapshot/<project>/<worktree-hash>/.git
```

通过 `--git-dir` 与 `--work-tree` 跟踪项目文件,从而支持 `/undo`、`/redo`、Session Revert。

证据:[`snapshot/index.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/snapshot/index.ts)、[`core/src/session/sql.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/core/src/session/sql.ts)。

#### F. 扩展与协议层

OpenCode 的扩展方式比一般 CLI 丰富:

- JSON/JSONC 配置;
- Markdown Agent;
- Markdown Command;
- `SKILL.md`;
- TypeScript Custom Tool;
- Plugin 生命周期钩子;
- MCP;
- LSP;
- ACP;
- OpenAPI/SDK;
- Claude Code 的 `CLAUDE.md`、Skill 目录兼容。

这使它既是产品,也逐渐成为 Coding Agent Runtime。

### 4.3 权限模式

OpenCode 权限使用三态:

- `allow`
- `ask`
- `deny`

规则特点:

- 可按工具、命令、路径、URL、Skill、Subagent 配置;
- 支持通配符;
- 最后匹配规则生效;
- 可限制工作区外路径;
- `.env` 默认禁止读取;
- 同一工具和相同输入连续重复三次触发 `doom_loop` 审批;
- `--auto` 只自动批准 `ask`,不会绕过显式 `deny`。

批准界面支持:

- `once`
- `always`
- `reject`

但 `always` 仅在当前 OpenCode 进程/会话中记忆,不是长期权限数据库。

证据:[Permissions 文档](https://opencode.ai/docs/permissions/)、[`permission/index.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/permission/index.ts)。

### 4.4 当前 v1/v2 双轨状态

这是 OpenCode 当前最值得注意的架构事实:

- 生产二进制仍是 `packages/opencode` 的 `opencode`;
- 生产 Prompt Loop 仍主要使用 `packages/opencode/src/session/*`;
- `packages/core/src/session/runner/*` 正在构建新的 Effect 化 Session Runner;
- 新 Runner 已加入 Session Input Queue/Steer、Context Epoch、per-session Run Coordinator、并发 Tool Settlement 和更明确的 Location/Execution 分层;
- 但源码仍留有重试边界、状态持久化、多节点所有权等 TODO;
- `packages/cli` 的二进制叫 `lildax`,当前不是正式 `opencode` 入口。

新 Runner 见 [`packages/core/src/session/runner/llm.ts`](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/core/src/session/runner/llm.ts)。

这说明 OpenCode 正从“功能快速增长的单体 Coding Agent”迁移到更清晰的 Core/Server/Client 架构,但迁移尚未完全结束。

### 4.5 运行模式

1. `opencode`:TUI + 本地 Server。
2. `opencode run`:非交互式执行,可输出 JSON 事件。
3. `opencode serve`:Headless Server。
4. `opencode web`:Server + Web UI。
5. `opencode attach`:TUI 连接已有 Server。
6. Desktop:Electron 桌面应用,当前仍标 Beta。
7. IDE:VS Code/Cursor/Windsurf 等。
8. ACP:Zed、JetBrains、Neovim 客户端接入。
9. GitHub/GitLab CI:Issue、PR、评论触发。
10. 多 Session:多个会话并行。
11. Subagent:父子 Session 分工。
12. Auto Mode:自动批准未被显式拒绝的权限请求。

### 4.6 优势与风险

#### 优势

- 开箱即用,不需要先设计 Agent 产品层。
- 客户端形态、模型 Provider 和开发工具链覆盖非常广。
- Client/Server 分离使 TUI、Web、Desktop、IDE、SDK 共用运行时。
- Session、SQLite、SSE、内部 Git 快照构成完整可恢复执行链。
- Agent、Skill、Command、Tool、Plugin、MCP 的扩展层次清晰。
- 社区规模与发布速度显著高于 AgentScope。

#### 风险

- 当前生产 v1 Loop 与新 Core v2 Runner 并存,维护复杂度较高。
- Monorepo 超过 30 个包,新人理解成本高。
- Effect 4 仍为 beta 版本,且仓库维护多项 patched dependencies。
- 高频发版带来快速修复,也意味着接口和内部结构变化快。
- Desktop 仍标 Beta。
- 权限与快照很强,但不是 AgentScope 那种多后端隔离沙箱;允许 Bash 后仍依赖宿主环境边界。
- 通用 RAG、长期语义记忆、多租户计费与分布式 Agent Team 不是核心强项。

---

## 五、综合对比

| 维度 | AgentScope | OpenCode |
|---|---|---|
| 本质 | Agent 开发框架 | Coding Agent 产品 |
| 抽象中心 | Agent、Event、Message、Middleware | Session、Workspace、Tool、Client/Server |
| 主循环 | 单一 `Agent` ReAct Loop | 持久化 `SessionPrompt.runLoop` |
| 用户入口 | Python API、FastAPI、Web UI 示例 | TUI、CLI、Desktop、Web、IDE、CI、SDK |
| 状态粒度 | `AgentState` + 上层 Storage | SQLite Session/Message/Part |
| 流式模型 | Event 可重建完整 Msg | SSE 推送持久化 Session 事件 |
| 多 Agent | 分布式 Session + Message Bus + Team Tools | Primary/Subagent + 父子 Session |
| 多租户 | 原生 Agent Service 能力 | 核心产品并非多租户 Agent SaaS |
| 工具扩展 | Python Tool、Toolkit、MCP、Skill | TS Tool、Plugin、MCP、Skill、Command |
| 权限 | 5 种 Mode + ASK/外部执行状态机 | allow/ask/deny + glob + workspace boundary |
| 沙箱 | Local/Docker/E2B/K8s/Daytona/OpenSandbox | 主要依赖宿主工作区与权限;无同等级后端矩阵 |
| 记忆/RAG | 短期上下文 + Mem0/ReMe/Agentic Memory + RAG | Session 历史 + Compaction;语义记忆通常靠插件/MCP |
| 代码能力 | 有 Bash/Edit/Grep 等,但非唯一场景 | LSP、快照、Undo、Diff、AGENTS.md 深度集成 |
| 可观测性 | OTel/OTLP + Tracing Middleware | SSE、日志、持久消息;标准化 Trace 不是主要卖点 |
| 编排风格 | 模型自主规划,非静态 DAG | 模型自主编码循环 + Task/Subagent |
| 部署 | Python 嵌入、FastAPI、Redis、分布式 Worker | 本地应用、远程 Server、Desktop/Web/IDE/CI |
| 许可证 | Apache-2.0,含明确专利授权 | MIT,更简洁宽松 |
| 当前架构风险 | 2.0 较新、1.x 迁移成本 | v1/v2 双轨、Effect beta、超大 Monorepo |

---

## 六、关键架构判断

### 6.1 两者都是 ReAct,但“状态所有者”不同

- AgentScope:**Agent 是状态所有者**,Service 再把 Agent 托管起来。
- OpenCode:**Session 是状态所有者**,Agent 是 Session 每轮执行时选择的一组 Prompt、Model、Tool 和 Permission 配置。

这决定了扩展方式:

- AgentScope 更适合在代码里组合 Agent 能力;
- OpenCode 更适合围绕持久会话、工作区和客户端扩展产品体验。

### 6.2 多 Agent 的语义不同

- AgentScope 是“多个长期存在、可跨节点通信的 Agent Session”。
- OpenCode 是“主 Agent 把具体编码任务委派给子 Session”。

前者更像团队协作系统,后者更像编码任务分解器。

### 6.3 安全重点不同

- AgentScope 强调:权限 + 外部执行 + 隔离 Workspace/Sandbox。
- OpenCode 强调:工具规则 + 用户审批 + 工作区边界 + 可撤销文件快照。

OpenCode 的 Undo 能恢复文件,但不能替代系统级沙箱;AgentScope 的沙箱能隔离执行,但不会天然提供 OpenCode 那种开发者级 Diff/Undo 体验。

### 6.4 OpenCode 的产品成熟度高于其内部架构稳定度

186k Stars、840 Releases 和 15k Commits 表明采用与迭代规模极大;但源码同时存在旧 Session Loop、新 Core Runner、实验 CLI 和 Effect beta。结论应是:

- **产品能力与生态成熟度高**;
- **内部架构仍处于快速重构期**。

### 6.5 AgentScope 的生产抽象完整度高于其采用规模

AgentScope 在多租户、消息总线、沙箱、HITL、长期记忆、RAG、OTel 上形成了更完整的通用 Agent 平台闭环;但 2.0 发布时间较近,生态和真实大规模案例仍弱于 OpenCode。

---

## 七、选型建议

### 7.1 选择 AgentScope,如果你要

- 构建非编码领域 Agent;
- 定义自己的业务消息、工具、记忆和中间件;
- 做多租户、多会话 Agent 服务;
- 让多个 Agent 跨进程、跨节点协作;
- 使用 Docker/K8s/E2B/Daytona 等隔离环境;
- 原生接入 RAG、长期记忆和 OpenTelemetry;
- 将 Agent 作为业务系统中的一个可嵌入运行时。

### 7.2 选择 OpenCode,如果你要

- 直接获得可用的 AI Coding Agent;
- 在终端、IDE、桌面、Web 和 CI 共用会话;
- 深度使用代码搜索、编辑、Bash、LSP、Diff、Undo;
- 通过配置快速定义 Build/Plan/Review/Debug 等 Agent;
- 使用 MCP、Skill、Command、Plugin 扩展编码流程;
- 通过 Server/OpenAPI/SDK 二次开发编码产品。

### 7.3 如果目标是“企业内部编码 Agent 平台”

建议优先级是:

1. **以 OpenCode 为编码执行内核**,复用其 Session、代码工具、LSP、权限、快照和多客户端。
2. 在外围补充企业身份、审计、模型网关、配额和隔离执行。
3. 只有当需求扩展到跨领域 Agent Team、长期记忆、RAG、多租户业务工作流时,再考虑引入 AgentScope 作为上层编排平台。
4. 两者可通过 OpenCode Server/SDK/MCP 与 AgentScope Tool 适配,但官方没有现成的一体化集成,需要自行维护边界。

---

## 八、参考来源

### 8.1 AgentScope

1. [AgentScope 仓库与 README](https://github.com/agentscope-ai/agentscope)
2. [AgentScope 官方文档](https://docs.agentscope.io/)
3. [Agent 主循环](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/agent/_agent.py)
4. [Message & Event](https://docs.agentscope.io/latest/en/building-blocks/message-and-event)
5. [Permission Engine](https://github.com/agentscope-ai/agentscope/blob/0745ea5a1ae06a523624ade4bd3daceb4a3ca070/src/agentscope/permission/_engine.py)
6. [Agent Team](https://docs.agentscope.io/latest/en/deploy/agent-team)
7. [v2.0.4 Release](https://github.com/agentscope-ai/agentscope/releases/tag/v2.0.4)
8. [GitHub Repository API](https://api.github.com/repos/agentscope-ai/agentscope)

### 8.2 OpenCode

1. [OpenCode 仓库与 README](https://github.com/anomalyco/opencode)
2. [OpenCode 官方文档](https://opencode.ai/docs/)
3. [Server 架构](https://opencode.ai/docs/server/)
4. [Agent 系统](https://opencode.ai/docs/agents/)
5. [权限系统](https://opencode.ai/docs/permissions/)
6. [生产 Session Loop](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/session/prompt.ts)
7. [Stream Processor](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/session/processor.ts)
8. [新 Core Runner](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/core/src/session/runner/llm.ts)
9. [内部 Git Snapshot](https://github.com/anomalyco/opencode/blob/17544802c38a4d35834275526ccf38be1cdcfbf4/packages/opencode/src/snapshot/index.ts)
10. [v1.18.2 Release](https://github.com/anomalyco/opencode/releases/tag/v1.18.2)
11. [GitHub Repository API](https://api.github.com/repos/anomalyco/opencode)

> 事实数据均以 2026-07-16 的官方仓库、GitHub API、官方文档和发布记录为准。Stars、Issues、PR、提交数等动态指标会持续变化;源码判断使用固定 Commit 链接,避免后续分支演进导致证据漂移。
