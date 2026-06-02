# OpenClaw 安装与本地网关配置指南

> **阅读对象**:AI 工具使用者、平台工程师、开发者
> **适用环境**:macOS、Node.js、OpenClaw CLI
> **说明**:本文中的“网关”指 OpenClaw 本地网关服务,不是 ArchAIHarness 的 Spring Cloud Gateway 仓库。

本文记录在 Mac mini M4 上安装 OpenClaw、处理 npm 安装异常、完成初始化配置并启动本地网关的完整过程。

## 1. 环境准备

| 项 | 说明 |
| --- | --- |
| 设备 | Mac mini M4 16 GB |
| 网络 | Hiddify |
| Node.js | 安装脚本检测到 Node.js v24.14.0 |
| npm | 安装脚本检测到 npm 11.9.0 |

## 2. 安装 OpenClaw

### 2.1 官方推荐安装脚本

官方推荐通过安装脚本完成 npm 全局安装并进入新手引导:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### 2.2 手动全局安装

如果已经完成 Node.js 安装,也可以手动安装:

```bash
npm install -g openclaw@latest
```

### 2.3 验证安装

```bash
openclaw --version
```

示例输出:

```text
2026.3.2
```

## 3. 安装失败排查

### 3.1 官方安装脚本 npm install 失败

执行:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

安装过程中可能出现:

```text
npm install failed for openclaw@latest
Command: env SHARP_IGNORE_GLOBAL_LIBVIPS=1 npm --loglevel error --silent --no-fund --no-audit install -g openclaw@latest
Installer log: /var/folders/.../tmp.xxxxx
npm install failed; retrying
```

如果安装脚本日志为空,可以改用手动安装以获得更明确的 npm 错误。

### 3.2 GitHub SSH 依赖拉取失败

手动安装时可能出现:

```text
npm error code 128
npm error command git --no-replace-objects ls-remote ssh://git@github.com/whiskeysockets/libsignal-node.git
npm error git@github.com: Permission denied (publickey).
npm error fatal: Could not read from remote repository.
```

原因通常是某些依赖通过 GitHub SSH 地址解析,但本机没有可用 SSH Key 或网络环境不允许 SSH 拉取。

处理方式:将 GitHub SSH 地址改写为 HTTPS。

```bash
git config --global url."https://github.com/".insteadOf ssh://git@github.com/
git config --global url."https://github.com/".insteadOf git@github.com:
```

然后重新安装:

```bash
npm install -g openclaw@latest
```

### 3.3 npm cache 权限异常

重新安装时可能出现:

```text
npm error code EACCES
npm error syscall mkdir
npm error path /Users/<user>/.npm/_cacache/index-v5/...
npm error Your cache folder contains root-owned files
```

原因是历史 npm 操作留下了 root-owned cache 文件。

按 npm 提示修复当前用户的 npm cache 权限:

```bash
sudo chown -R $(id -u):$(id -g) "$HOME/.npm"
```

然后重新安装:

```bash
npm install -g openclaw@latest
```

## 4. 首次启动本地网关

直接启动 OpenClaw 本地网关:

```bash
openclaw gateway --port 18789
```

可能出现:

```text
Missing config. Run `openclaw setup` or set gateway.mode=local (or pass --allow-unconfigured).
```

先执行初始化:

```bash
openclaw setup
```

示例输出:

```text
Wrote ~/.openclaw/openclaw.json
Workspace OK: ~/.openclaw/workspace
Sessions OK: ~/.openclaw/agents/main/sessions
```

再次启动时,如果仍然出现:

```text
Gateway start blocked: set gateway.mode=local (current: unset) or pass --allow-unconfigured.
```

说明配置中仍未设置 `gateway.mode`。

## 5. 配置 gateway.mode

### 5.1 推荐方式:永久设置 local 模式

```bash
openclaw config set gateway.mode local
```

然后正常启动:

```bash
openclaw gateway --port 18789
```

### 5.2 临时方式:允许未配置启动

如果只是临时验证,可以加 `--allow-unconfigured`:

```bash
openclaw gateway --port 18789 --allow-unconfigured
```

成功启动示例:

```text
[canvas] host mounted at http://127.0.0.1:18789/__openclaw__/canvas/
[heartbeat] started
[health-monitor] started
[gateway] agent model: zai/glm-5
[gateway] listening on ws://127.0.0.1:18789, ws://[::1]:18789
[browser/server] Browser control listening on http://127.0.0.1:18791/ (auth=token)
```

## 6. 使用 doctor 检查环境

如果网关无法访问或启动被阻止,执行:

```bash
openclaw doctor
```

重点关注 Gateway 检查项:

```text
gateway.mode is unset; gateway start will be blocked.
Fix: run openclaw configure and set Gateway mode (local/remote).
Or set directly: openclaw config set gateway.mode local
```

如果 doctor 提示生成 gateway token,按交互选择 `Yes` 完成 token 配置。

## 7. 访问与验证

网关成功启动后,终端会显示监听地址。常见地址包括:

```text
ws://127.0.0.1:18789
http://127.0.0.1:18789/__openclaw__/canvas/
http://127.0.0.1:18791/
```

注意:端口 `18789` 主要是 OpenClaw 网关 WebSocket 与内部服务入口,浏览器直接访问根路径 `http://127.0.0.1:18789` 不一定会显示页面。应以启动日志中打印的具体 HTTP 地址为准。

## 8. 推荐命令序列

首次安装与启动建议按以下顺序执行:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
openclaw --version
openclaw setup
openclaw config set gateway.mode local
openclaw doctor
openclaw gateway --port 18789
```

如果安装脚本失败:

```bash
git config --global url."https://github.com/".insteadOf ssh://git@github.com/
git config --global url."https://github.com/".insteadOf git@github.com:
sudo chown -R $(id -u):$(id -g) "$HOME/.npm"
npm install -g openclaw@latest
```

## 9. 排查清单

| 问题 | 优先检查 |
| --- | --- |
| 安装脚本失败且日志为空 | 改用 `npm install -g openclaw@latest` 查看真实错误 |
| GitHub SSH permission denied | 配置 GitHub SSH 到 HTTPS 的 `insteadOf` 规则 |
| npm cache EACCES | 修复 `$HOME/.npm` 权限 |
| `Missing config` | 执行 `openclaw setup` |
| `gateway.mode is unset` | 执行 `openclaw config set gateway.mode local` |
| 浏览器访问根路径失败 | 查看启动日志中的具体 HTTP 地址 |
