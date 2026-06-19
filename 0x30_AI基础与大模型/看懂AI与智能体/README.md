# 看懂 AI 与智能体

这个专栏面向希望系统理解 AI 与智能体基础概念的读者。

它不从数学公式或模型论文切入，而是先回答三个更基础的问题：AI 模型到底如何生成回答，Agent 为什么不是聊天机器人，以及普通人和工程师应该如何理解 AI 能力边界。

## 文章目录

1. [AI 模型到底是怎么"思考"的？](./AI模型到底是怎么思考的.md)
   - 用"预测下一个片段"的视角解释 AI 模型的工作方式、训练过程、理解边界和幻觉风险。
2. [Agent 到底是什么？](./Agent到底是什么.md)
   - 从目标、计划、工具、观察、修正和验证闭环解释 Agent 为什么不是聊天机器人，而是执行系统。
3. [AI 很强，但为什么还会"忘"？](./AI很强但为什么还会忘.md)
   - 用"每次只看一张有限的纸"的视角解释上下文、Token、会话与上下文的差异，以及为什么模型决定上限、上下文决定下限。
4. [AI 怎么从“会回答”变成“会干活”？看懂 Tool、Skill 和 MCP](./AI怎么从会回答变成会干活.md)
   - 解释 Tool、Skill、MCP 如何让 AI 从生成回答走向执行任务，以及普通用户如何识别能力来源、从业者如何供给可调用能力。
   - 发布状态：已发布到知乎专栏《看懂 AI 与智能体》，链接：<https://zhuanlan.zhihu.com/p/2050966572882306331>。
5. [别再找 Prompt 模板了：提示词的本质，是你和 AI 的任务契约](./别再找Prompt模板了.md)
   - 从任务契约的角度解释提示词的本质，拆解好 Prompt 的五个零件，给出普通人三步法和工程师接口设计视角。
   - 发布状态：草稿完成，配图已生成。

## 阅读建议

建议按顺序读：

- 先理解模型为什么能生成看似合理的回答（第 1 篇）。
- 再理解 Agent 如何把模型能力放进目标、工具、权限和验证闭环里（第 2 篇）。
- 然后理解 AI 每次回答到底"看到了什么"，以及为什么会忘、会跑偏（第 3 篇）。
- 再理解 Tool、Skill 和 MCP 如何让 AI 从"会回答"走向"会干活"（第 4 篇）。

## 配图说明

本专栏配图统一存放在 [`assets/`](./assets/) 下：

- [`assets/ai-model-thinking/`](./assets/ai-model-thinking/)：AI 模型原理文章配图。
- [`assets/agent-execution-system/`](./assets/agent-execution-system/)：Agent 执行系统文章配图。
- [`assets/context-engineering/`](./assets/context-engineering/)：上下文工程文章配图（待生成）。
- [`assets/tool-skill-mcp/`](./assets/tool-skill-mcp/)：Tool、Skill、MCP 文章配图。
- [`assets/prompt-contract/`](./assets/prompt-contract/)：提示词契约文章配图。
