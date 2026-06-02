<div align="center">

# ArchAIHarness · Docs

### 架构哲学 · 人机协同方法论 · 工程白皮书

**让 AI 在架构师定义的秩序中生长**

[![MIT License](https://img.shields.io/badge/License-MIT-success.svg?style=flat-square)](LICENSE)
[![Organization](https://img.shields.io/badge/Org-ArchAIHarness-181717?style=flat-square&logo=github)](https://github.com/ArchAIHarness)
[![DDD](https://img.shields.io/badge/Paradigm-DDD-blue?style=flat-square)](https://github.com/ArchAIHarness)
[![Human + AI](https://img.shields.io/badge/Mode-Human%20%2B%20AI-orange?style=flat-square)](https://github.com/ArchAIHarness)

</div>

---

## 这是什么

ArchAIHarness 的核心思想文档库,沉淀「**人负责架构、AI 负责编码**」这套人机协同研发范式的完整方法论。

如果说 [`framework`](https://github.com/ArchAIHarness/framework) 是我们的「**骨**」,那么 `docs` 就是我们的「**魂**」。

---

## 核心命题

> 当 AI 编码能力突飞猛进、却频繁失控时,什么才是真正的护栏?
>
> 我们的回答是:**架构师的秩序**。

- 架构师定义**边界、规范、价值底线**
- AI 在边界内完成**样板代码与重复实现**
- 一套**可被 AI 理解、可被 AI 执行、可被人类审计**的工程治理体系

---

## 文档地图

### 🧭 [01 · 架构哲学(Philosophy)](./01-philosophy)

> **道层** —— 体系的本源认知,回答「为什么」

- [知行合一 · 上下相济 · 循环演进 —— 软件架构与人机协同的哲学体系](./01-philosophy/01-architecture-philosophy.md)

### 📐 [02 · 方法论(Methodology)](./02-methodology)

> **法层** —— 可落地的工程方法论,回答「怎么做」

- [架构设计入门指南](./02-methodology/01-architecture-primer.md) —— 从业务到部署的设计逻辑链
- [人机协同开发流程(Vibe Coding)](./02-methodology/02-human-ai-collaboration.md) —— AI 时代的开发心法
- [团队协作落地手册](./02-methodology/03-team-playbook.md) —— DDD + TDD + SDD 三位一体
- [代码质量评估体系](./02-methodology/04-code-quality.md) —— 量化、可对比、可治理

### 🧱 [03 · 实践模式(Patterns)](./03-patterns)

> **术层** —— 经过验证的可复用工程模式

- [DDD + 多租户架构模式](./03-patterns/ddd-multi-tenant.md) —— 框架核心模式提炼

---

## 适用读者

| 角色 | 你能在这里得到什么 |
| --- | --- |
| 🏗 **架构师** | 一套可对外输出的人机协同治理范式 |
| 💼 **技术负责人** | 团队级 AI 提效落地路径与质量门禁 |
| 🤖 **AI 工程师** | 让 Agent 输出可控、可审计的约束机制 |
| 📚 **学习者** | 从哲学到落地的完整架构思想链路 |

---

## 与组织内其他仓库的关系

```text
                ┌──────────────────────────┐
                │   ArchAIHarness/docs     │  ← 你在这里 · 思想与方法
                │   (philosophy + methods) │
                └──────────────┬───────────┘
                               │ 指导
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
   framework               mcp-sdk              skill-market
   (DDD 脚手架)            (Agent 通信)         (AI 技能包)
```

---

## 演进原则

- 🎯 **可读性优先**:每篇文档独立成体,30 分钟读懂一个主题
- 🔄 **持续演进**:实践反哺文档,每个版本沉淀新认知
- 🤝 **欢迎共建**:Issue / PR / 案例分享 全部开放

---

## License

[MIT License](./LICENSE) · 自由使用、传播与二次创作

<div align="center">

—— **Engineered by Architects · Empowered by AI** ——

</div>
