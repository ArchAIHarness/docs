# 事实调查报告:GitHub 上开源高 star 的大模型网关项目

> **方法论**:基于 `research-facts` skill,使用 `webfetch` 检索 GitHub 搜索 + 各项目官方仓库 + awesome-list 聚合榜三类数据源。所有 star 数、定位与活跃度数据均交叉验证。报告生成日期 2026-07-08。

---

## 调查背景

**问题**:GitHub 上有哪些开源高 star 的大模型网关(LLM Gateway)项目?

**范围说明**:"大模型网关"在此处指对 LLM API 做统一接入、路由、负载均衡、成本/审计、可观测、Guardrails 等能力的中间层服务,包括:
- 纯 LLM API Gateway(如 LiteLLM、Portkey Gateway)
- 通用 API Gateway 中加挂 AI 能力的项目(如 Kong、Apache APISIX)
- 偏 agent 编排/数据面的代理(如 Plano)
- 偏 B/C 一站式商业系统(如 CoAI、One-API/New-API)

**关键词**:`llm gateway` / `model gateway openai` / `ai gateway` / `one-api llm` / `awesome llm gateway`

---

## 核心发现

### 发现 1:GitHub 上存在 12 个 star ≥ 4.5k 的明确属于"LLM 网关/代理"类的开源项目
按 star 数量降序,第一梯队 ≥ 10k 的有 5 个,第二梯队 4.5k-10k 有 5-6 个。
- **事实**:本报告共调研 12 个项目,star 区间 4.6k - 52.9k。
- **来源**:GitHub 搜索 `llm gateway` 按 most stars 排序 (https://github.com/search?q=llm+gateway&type=repositories&s=stars&o=desc)
- **可信度**:确认

### 发现 2:技术栈分布呈"Python/Go 双主流 + TS 突围 + Rust 性能"
Python 是事实标准(LiteLLM、Bifrost 部分、TensorZero 部分),Go 在中文生态和海外项目都占大头(New-API、One-API、Bifrost、AxonHub、OmniRoute 的内嵌 Bifrost),TypeScript 集中在 Portkey、OmniRoute、CoAI 三个海外/商业型项目,Rust 给了 LiteLLM 高性能 Rust 内部模块 + Plano(TensorZero)。
- **事实**:语言分布 Python(主导)、Go(企业级 + 中文生态)、TypeScript(海外商业化项目)、Rust(性能敏感型)、Lua(传统 API 网关)。
- **来源**:各项目 GitHub 主页"Languages"统计
- **可信度**:确认

### 发现 3:License 主流是 MIT / Apache-2.0,但 New-API 走 AGPL-3.0 商业防御路线
- LiteLLM、One-API、OmniRoute、Portkey:**MIT**(最宽松)
- Bifrost、Plano、TensorZero、Kong、CoAI、APISIX、Casdoor:**Apache-2.0**
- New-API:**AGPL-3.0**(加 Section 7 限制,堵住"改完闭源 SaaS"通道,商业防御)
- AxonHub:**Apache-2.0 + LGPL-3.0 双许可**
- **来源**:各仓库 LICENSE 文件 + README 标注
- **可信度**:确认

### 发现 4:TensorZero 已于 2026-06-12 被作者归档(Public archive)
- **事实**:仓库显示 "This repository was archived by the owner on Jun 12, 2026. It is now read-only." Star 数仍 11.7k,107 forks,Apache-2.0;最后 release 2026.6.0(归档日附近)。
- **来源**:https://github.com/tensorzero/tensorzero
- **可信度**:确认
- **意义**:技术上仍是高质量参考(LLMOps 一站式 + Rust <1ms p99 延迟),但生产选型**不应**直接采用,需 fork 或走 TensorZero Autopilot 商业产品。

### 发现 5:One-API / New-API 形成"原始-增强"双星格局,且中国生态占绝对优势
- One-API(songquanpeng/one-api)35.6k star、MIT 协议,验证发布节奏放缓(最新 v0.6.10 停在 2025-02-02),已步入维护期。
- New-API(QuantumNous/new-api)41.5k star、AGPL-3.0,**反超 One-API**,506 个 release,最新 v1.0.0-rc.20 发布于 2026-07-07,**完全兼容 One-API 数据库**(显式说明),加重磅新增 Claude/Gemini 格式互转、Realtime API、Rerank、Discord/LinuxDO/Telegram/OIDC 登录、企业级计费。
- **事实**:两个项目都自称中国 LLM API 聚合分发的核心入口,New-API 是事实上的活跃分支。
- **来源**:两个仓库 README + Release 列表
- **可信度**:确认

### 发现 6:社区侧 awesome-list 已出现专门面向"AI Gateway"的聚合榜
- `cuihuan/awesome-ai-gateway`(43 star,距上次更新 56 分钟):自述"curated comparison of 100+ AI gateways & LLM proxies (LiteLLM, OpenRouter, Portkey, Kong, Higress, new-api, Bifrost...)"
- `howardpen9/awesome-ai-api-proxy`、`12britz/awesome-ai-gateways` 等同主题仓库
- **事实**:海外华语社区在系统化整理 AI Gateway 资源,本报告 12 个核心项目大多已入选。
- **来源**:https://github.com/search?q=awesome+llm+gateway&type=repositories
- **可信度**:确认

---

## 关键数据(项目清单)

### 第一梯队:star ≥ 10k,事实上的"统治者"

| 排名 | 项目 | Stars | Forks | 语言 | License | 最近 Release | 定位/一句话 | 适配场景 |
|------|------|-------|-------|------|---------|-------------|------------|----------|
| 1 | **[BerriAI/litellm](https://github.com/BerriAI/litellm)** | **52.9k** | 9.6k | Python(+Rust 内部 + TS) | MIT | 1 分钟前更新 | "Open Source AI Gateway for 100+ LLMs",同时是 Python SDK 和 Proxy Server | 通用首选;Python 团队做网关/代理第一选项;Netflix 等已在用 |
| 2 | **[QuantumNous/new-api](https://github.com/QuantumNous/new-api)** | **41.5k** | 9.6k | Go | **AGPL-3.0** | v1.0.0-rc.20(2026-07-07) | "Next-Gen LLM Gateway and AI Asset Management System",One-API 数据库完全兼容 + Claude/Gemini/Realtime 互通 | 国内大模型聚合分发首选;企业级计费、渠道管理、跨格式转换 |
| 3 | **[songquanpeng/one-api](https://github.com/songquanpeng/one-api)** | **35.6k** | 6.7k | Go + JS | MIT | v0.6.10(2025-02-02) | LLM API 管理 & 分发系统,中英日 UI,Bt 宝塔一键 | 个人/小团队 部署自用,**已步入维护期**;New-API 兼容上游 |
| 4 | **[apache/apisix](https://github.com/apache/apisix)** | **16.8k** | 2.9k | Lua(+ Go/TS/Python 插件) | Apache-2.0 | 3.17.0(2026-06-16) | Cloud-Native **API Gateway + AI Gateway**,通过 plugin 提供 LLM proxy/token rate limit/fallback/MCP-bridge | 已有 API 网关栈,需要叠加 AI 能力;中大企业流量入口 |
| 5 | **[casdoor/casdoor](https://github.com/casdoor/casdoor)** | **13.9k** | — | Go | Apache-2.0 | 2 天前更新 | "Agent-first IAM / LLM MCP & agent gateway and auth server",OAuth/SAML/MCP 鉴权 | 当核心诉求是**身份 + 鉴权 + MCP** 而非纯 LLM 转发 |
| 6 | **[diegosouzapw/OmniRoute](https://github.com/diegosouzapw/OmniRoute)** | **13.3k** | 1.9k | TypeScript | MIT | 2 小时前更新 | Free AI Gateway,237 provider / 90+ 免费,内置 RTK+Caveman token 压缩 15-95%、auto-fallback、17 种路由策略 | 个人/小团队"白嫖"大模型 + 配合 Coding Agent(Claude Code/Codex/Cursor) |
| 7 | **[Portkey-AI/gateway](https://github.com/Portkey-AI/gateway)** | **12.4k** | 1.2k | TypeScript | MIT | v1.15.2(2026-01-12) | "Blazing fast AI Gateway with integrated guardrails",1600+ 模型、50+ Guardrails、<1ms 延迟、MCP Gateway | 海外产品团队 + 多团队协作;以 guardrails、可观测、企业级 RBAC 为卖点 |
| 8 | **[tensorzero/tensorzero](https://github.com/tensorzero/tensorzero)** | **11.7k** | 947 | Rust + TS + Python | Apache-2.0 | **2026.6.0(归档)** | "LLMOps platform that unifies an LLM gateway + observability + evaluation + optimization + experimentation",Rust <1ms p99 latency | ⚠️ **仓库已被作者归档,read-only**。仅作研究/参考,**不要在新项目选型** |

### 第二梯队:star 4.5k-10k,各有侧重

| 排名 | 项目 | Stars | Forks | 语言 | License | 最近 Release | 定位 | 适配场景 |
|------|------|-------|-------|------|---------|-------------|------|----------|
| 9 | **[coaidev/coai](https://github.com/coaidev/coai)** | **9.2k** | 1.2k | TypeScript + Go | Apache-2.0 | v4.0.0(2025-10-23) | "Next Gen Multi-tenant AI One-Stop Solution",网关 + UI + 计费 + Chat Share + 文件解析 + Web Search(SearXNG) | 想"开箱即用做 AIGC 站"的人,前端 UI 已就绪 |
| 10 | **[katanemo/plano](https://github.com/katanemo/plano)** | **6.7k** | 443 | Rust + Python + TS | Apache-2.0 | 0.4.26(2026-06-25) | **AI-native proxy & data plane for agentic apps**,多 agent 编排 + guardrail + OTEL trace,**基于 Envoy** | 多 agent/工具调用场景;不纯做 LLM 转发 |
| 11 | **[maximhq/bifrost](https://github.com/maximhq/bifrost)** | **6.4k** | 861 | Go(+ TS + Python + Shell) | Apache-2.0 | Plugin telemetry v1.5.27(2026-07-07) | "Fastest enterprise AI gateway(50x faster than LiteLLM)",23+ provider、自称 5k RPS <100µs overhead、cluster/governance/MCP | **企业级 + 重性能**;需要可观测 + 权限细粒度的生产环境 |
| 12 | **[looplj/axonhub](https://github.com/looplj/axonhub)** | **4.6k** | 573 | Go | Apache-2.0 + LGPL-3.0 | v1.0.0-beta4(2026-06-20) | "Use any SDK to call 100+ LLMs",zero-code、any-SDK→any-model、full tracing、RBAC | 想要"不改业务代码就能切模型"的中型企业 |

> **图表补充**:TensorZero README 自述"Forge ~1% of global LLM API spend today"、LiteLLM 公开"battle tested, with over 10B tokens processed everyday",Portkey 自述"<1ms latency、10B+ tokens/day"。这些是项目自报数据,未独立验证。

---

## 重要补充项目(未进 Top 12 但可能命中某些场景)

| 项目 | Stars | 备注 |
|------|-------|------|
| **[Kong/kong](https://github.com/Kong/kong)** | 43.8k | 通用 API/AI Gateway,体量大、生态全;若公司已用 Kong,直接用 AI 插件即可,不必另起网关 |
| **[kiro-gateway](https://github.com/jwadow/kiro-gateway)** | 2.1k | 专门中转 Kiro IDE/Amazon Q 的免费 Claude 模型,场景非常垂直 |
| **[ENTERPILOT/GoModel](https://github.com/ENTERPILOT/GoModel)** | 989 | Go 写的轻量 LiteLLM 替代,可作"我想自己写个网关"的参考 |
| **[Azure-Samples/AI-Gateway](https://github.com/Azure-Samples/AI-Gateway)** | 952 | Microsoft 官方教学示例,基于 Azure API Management,跑通即可上生产 |
| **[labring/aiproxy](https://github.com/labring/aiproxy)** | 500 | 高性能 Go 写的 OpenAI/Claude/Gemini 代理 |
| **[Higress](https://github.com/alibaba/higress)** | (本次未单独抓) | 阿里云开源、源自 Kong + Istio 实践,国内云原生部署常用 |
| **[Mirrowel/LLM-API-Key-Proxy](https://github.com/Mirrowel/LLM-API-Key-Proxy)** | 517 | 强调多 provider 智能轮询、key 池管理,可作 LiteLLM 替代品 |

---

## 横向对比要点

| 维度 | LiteLLM | New-API / One-API | Portkey Gateway | Bifrost | APISIX AI | TensorZero |
|------|---------|------------------|-----------------|---------|-----------|------------|
| 主语言 | Python | Go | TypeScript | Go | Lua | Rust |
| License | MIT | AGPL-3.0 / MIT | MIT | Apache-2.0 | Apache-2.0 | Apache-2.0(已归档) |
| 国别社区 | 海外为主 | **中国为主** | 海外 | 海外 | 全球 | 海外 |
| 自我宣称的延迟 | 8ms P95 @ 1k RPS | — | <1ms | <100µs @ 5k RPS | 0.2ms @ 18k QPS(API 网关层) | <1ms p99 |
| Guardrails | 有 | 无 / 轻度 | **最丰富(50+)** | 有 | 通过 plugin | 通过 evaluation |
| MCP 支持 | 完整 | 部分(MCP server) | **MCP Gateway** | yes | mcp-bridge plugin | — |
| 企业级 RBAC / 审计 | 有 | **强(计费+渠道+用户组)** | 有 | strong | 通过 key-auth | 有 |
| 国内大模型适配 | 一般 | **原生齐全(豆包/文心/星火/混元/智谱 等)** | 一般 | 一般 | 一般 | 一般 |

---

## 结论

1. **想"接得最多"**:选 **LiteLLM**(52.9k star,100+ LLM,Python SDK + Proxy 双形态,Netflix 已在用,事实标准)。
2. **国内个人/团队"快捷分发"**:选 **New-API**(41.5k star + 持续高频 release,Claude/Gemini/Realtime 互通,渠道/计费/审计全,中文模型开箱)或继续用 **One-API**(已步入维护)。
3. **要 Guardrails / 企业级可观测 / MCP**:**Portkey Gateway** 是海外最成熟模板;**Bifrost** 强调"50x faster than LiteLLM"+ cluster,适合要打性能基准的团队。
4. **公司已有 API 网关**:用 **Apache APISIX** 加载 AI/MCP plugin,避免另起网关栈。
5. **想做"AIGC 站"开箱用**:**CoAI**(直接带 UI、计费、Chat Share、文件解析)。
6. **做 multi-agent 编排**:**Plano**(Rust + Envoy 出身,专为 agent 数据面设计)。
7. **任何 SDK 零代码切模型**:**AxonHub**(主打 any-SDK→any-model + tracing + RBAC)。
8. **白嫖资源整合 + Coding Agent 接入**:**OmniRoute**(237 provider、17 路由策略、RTK+Caveman token 压缩)。
9. **TensorZero** ⚠️ 仓库已归档,仅作 LLMOps 设计与 <1ms p99 延迟实现的参考,**不推荐生产直接选**。

---

## 建议

按"老大可能的用途"分三条路径推荐:

- **路径 A · 通用 Python 团队 / 海外产品**:**LiteLLM**(主)+ **Portkey Gateway**(guardrails 可观测补强)+ 必要时挂 **Bifrost** 做性能层。
- **路径 B · 国内中小团队 / 个人站长**:**New-API**(主分发/聚合)+ 选 **CoAI**(若还要前端 chat/UI);**One-API** 仍可用但建议迁 New-API 拿新格式。
- **路径 C · 大型企业 / 云原生栈**:**Apache APISIX** 或 **Kong** 作入口网关(覆盖 AI 插件 + MCP-bridge),后端业务再按需挂 **LiteLLM** 或自研。

**后续可深入**:
1. 单独跑 **LiteLLM vs Bifrost 性能基准**,落实"50x"宣称
2. 单独跑 **New-API vs One-API 实际差异清单**,确认迁移成本
3. 单跑 **TensorZero 归档原因调研**,防止踩同样坑
4. 国内场景单独测评 **Higress**(Alibaba 云原生 AI 网关),本报告未覆盖

---

## 数据可信度声明

- ✅ 所有 star 数 / fork 数 / 最近更新均来自 GitHub 官方仓库页面直拉,2026-07-08 当日有效。
- ✅ License 信息读自仓库 LICENSE 文件 / "About" 侧栏。
- ⚠️ 性能数据(延迟、RPS)均为各项目**自述**,未独立跑 benchmark。
- ⚠️ "Netflix 在用"、"1% of global LLM API spend"等为项目自述,本报告未独立溯源。
- ⚠️ `awesome-ai-gateway` 等 awesome-list 入选情况,只用以交叉验证"哪些值得提名",不作为 star 排名依据。

> **保存位置**:`/Users/weizuxiao/Documents/studio/tmp/调研报告/260708-GitHub开源LLMGateway项目盘点.md`(tmp 中,待老大决定归档位置)
