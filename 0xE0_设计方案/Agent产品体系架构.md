# Agent 产品体系架构

## 产品构成

Agent 产品由 4 个核心项目构成，按职责分层：

| 项目 | 角色 | 技术栈 | 仓库地址 |
|------|------|--------|---------|
| **agent-gateway** | 控制面 + 流量入口 | Java 17, Spring Cloud Gateway, K8s Fabric8, Redis Reactive | `https://github.com/ArchAIHarness/agent-gateway.git` |
| **build-on-vscode-opencode-image** | 运行时基础镜像 | Docker, code-server, OpenCode CLI, Node.js 20, Python3 | `https://github.com/ArchAIHarness/build-on-vscode-opencode-image.git` |
| **a2ui-opencode** | VS Code 插件（用户界面） | TypeScript, VS Code Extension, OpenCodeUI, Vite | `https://github.com/ArchAIHarness/a2ui-opencode.git` |
| **agent-plugin** | 场景化能力插件集合 | OpenCode Plugin 格式, Agent 定义, Skill, Tool | `https://github.com/ArchAIHarness/agent-plugin.git` |

---

## 架构总览

```mermaid
flowchart TB
    User(["用户"])

    Gateway("agent-gateway<br/>Spring Cloud Gateway")
    MasterAPI("Master API<br/>生命周期管理")
    SubdomainRoute("子域名路由<br/>WebUI 代理")
    AgentRoute("Agent 路由<br/>API + WebSocket 代理")
    Redis[("Redis<br/>Runtime 状态")]

    Image("build-on-vscode-opencode-image<br/>基础镜像")

    K8sAPI("K8s API<br/>Fabric8 Client")
    PVC[/"持久卷<br/>工作空间"/]

    WebUI("code-server<br/>:8080")
    A2UI("a2ui-opencode<br/>VS Code 插件")
    OpenCodeCLI("opencode CLI<br/>AI 后端 :4096")
    AgentPlugin("agent-plugin<br/>场景化插件")

    User -->|"子域名 *.{domain}"| SubdomainRoute
    User -->|"/agent/*"| AgentRoute
    User -->|"/runtime/*"| MasterAPI

    Gateway --> Redis
    Gateway -->|"Fabric8"| K8sAPI

    K8sAPI -->|"创建/删除"| Image
    Image -->|"构建"| WebUI
    Image -->|"预装"| A2UI
    Image -->|"预装"| AgentPlugin
    Image -->|"全局安装"| OpenCodeCLI

    WebUI -->|"SimpleBrowser"| A2UI
    A2UI -->|"打开"| OpenCodeCLI
    AgentPlugin -->|"扩展"| OpenCodeCLI
    WebUI --> PVC
```

---

## 设计流程

```mermaid
flowchart LR
    subgraph 构建
        A[agent-plugin<br/>场景化插件] --> D
        B[a2ui-opencode<br/>VS Code 插件] --> D
        C[code-server + opencode] --> D
        D[build-on-vscode-opencode-image<br/>Docker 构建]
    end

    subgraph 部署
        D --> E[K8s Deployment + Service]
    end

    subgraph 接入
        E --> F[agent-gateway<br/>动态路由代理]
        F --> G[用户交互<br/>浏览器 / API]
    end
```

---

## 组件关系

```mermaid
flowchart LR
    GW("agent-gateway")
    K8S("K8s Cluster")
    IMG("build-on-vscode-opencode-image")
    CS("code-server")
    OC("opencode CLI")
    A2U("a2ui-opencode<br/>VS Code 插件")
    PLG("agent-plugin<br/>场景化插件")

    GW -->|"创建/manage"| K8S
    K8S -->|"Deploy"| IMG
    IMG -->|"构建"| CS
    IMG -->|"构建"| OC
    IMG -->|"预装"| A2U
    IMG -->|"预装"| PLG
    GW -->|"路由代理"| CS
    GW -->|"API 代理"| OC
    A2U -->|"嵌入 UI"| CS
    PLG -->|"扩展能力"| OC
```

---

## 各项目详情

### 1. agent-gateway（智能网关）

**定位**：Agent 产品的中枢控制面 + 流量入口。

职责：
- **生命周期管理**：通过 K8s Fabric8 客户端在集群上创建/删除/重启 Agent 实例
- **子域名路由**：`{runtimeId}.{domain}` → Agent WebUI（`:8080`）
- **Agent API 代理**：`{domain}/agent/**` → OpenCode API（`:4096`）
- **WebSocket 透传**：二进制零修改，支持 Terminal / LSP / DAP
- **状态存储**：Redis 双索引（runtimeId / userId），TTL 自动过期回收

**技术栈**：Java 17 + Spring Cloud Gateway + WebFlux（全反应式）+ Fabric8 + Redis Reactive

### 2. build-on-vscode-opencode-image（基础镜像）

**定位**：每个 Agent 实例运行时的容器底座。

组成：
- **code-server**：VS Code 网页版编辑器，监听 `:8080`
- **OpenCode CLI**：全局安装的 AI 后端
- **a2ui-opencode**：预装的 VS Code 插件
- **agent-plugin**：预装的 OpenCode 场景化插件
- **运行时**：Node.js 20 + Python3 + git + curl

关键设计：
- 扩展与用户数据固定在 `/opt`，不受工作目录挂载影响
- `seed-if-absent`：默认开箱即用，业务配置可完全接管
- 环境变量可覆盖：`BIND_ADDR`、`WORKSPACE_DIR`、`DISABLE_AUTH` 等

### 3. a2ui-opencode（VS Code 插件）

**定位**：在 VS Code 编辑器内嵌入 OpenCodeUI 前端界面。

工作方式：
- 插件激活 → 启动 `opencode serve --port 4096`
- 同时启动 OpenCodeUI Vite 开发服务器 `:5173`
- Vite 代理 `/api/*` → `localhost:4096`
- VS Code Simple Browser 或系统浏览器打开 OpenCodeUI

**通信链路**：

```mermaid
sequenceDiagram
    participant User as 用户
    participant A2UI as a2ui-opencode
    participant Vite as Vite Dev Server
    participant OC as opencode serve

    User->>A2UI: 打开 Chat
    A2UI->>A2UI: 启动 opencode serve (4096)
    A2UI->>A2UI: 启动 Vite 开发服务器 (5173)
    A2UI->>User: 打开 Simple Browser → localhost:5173
    User->>Vite: 访问 OpenCodeUI
    Vite->>OC: 代理 /api/* 到 localhost:4096
    OC->>Vite: SSE / JSON 响应
    Vite->>User: 渲染 AI 对话界面
```

### 4. agent-plugin（场景化插件集合）

**定位**：为 Agent 提供可插拔的场景化能力扩展。

格式：OpenCode 官方插件格式，通过 `opencode.json` 的 `plugin` 配置动态加载。

内容：
- `agents/*.md`：场景化 Agent 角色定义
- `skills/**/SKILL.md`：场景化 Skill 技能包
- `tools/**`：自定义工具脚本

不修改 `agent-gateway` 或 `build-on-vscode-opencode-image` 核心代码，完全解耦。

---

## 部署架构

```mermaid
flowchart TB
    subgraph "K8s Cluster / agent-runtime namespace"
        GW_INGRESS["Ingress *.{domain}"]
        GW_SVC["Gateway Service :8080"]
        GW_POD["gateway Pod<br/>Spring Cloud Gateway"]
        AGENT_SVC["Agent Service<br/>:8080 / :4096"]
        AGENT_DEP["Agent Deployment<br/>build-on-vscode-opencode-image"]
        REDIS_SVC["Redis Service :6379"]
        REDIS_POD["Redis Pod"]
        PVC["PersistentVolumeClaim<br/>agent-workspace"]
    end

    User(["用户"]) -->|"*.{domain}"| GW_INGRESS
    GW_INGRESS --> GW_SVC
    GW_SVC --> GW_POD
    GW_POD -->|"生命周期管理"| AGENT_DEP
    GW_POD -->|"路由代理"| AGENT_SVC
    GW_POD -->|"状态存储"| REDIS_SVC
    REDIS_SVC --> REDIS_POD
    AGENT_DEP --> PVC
```

---

## 运行效果

在 code-server 中通过 a2ui-opencode 插件打开 OpenCodeUI，与 AI Agent 对话：

![Agent 运行效果](../assets/agent-running-effect.jpg)

---

## 相关文档

- `05-工程研发/INDEX.md` — 研发仓库索引
- `05-工程研发/agent-gateway/README.md` — 网关详细文档
- `05-工程研发/build-on-vscode-opencode-image/README.md` — 镜像构建文档
- `05-工程研发/a2ui-opencode/README.md` — VS Code 插件文档
- `05-工程研发/agent-plugin/README.md` — 插件系统文档
