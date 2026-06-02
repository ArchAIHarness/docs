# 0xB0 · 实践模式 · Patterns

> 经过生产验证的可复用工程模式。

## 文档清单

- [**DDD + 多租户架构模式**](./0xB1_DDD多租户架构模式.md)
  ArchAIHarness 框架核心模式 —— 六边形 + DDD 分层 + 三层租户隔离 + CQRS

## 模式的演进策略

本目录会随着框架与社区实践,持续沉淀更多模式:

```text
🔮 规划中
├── Outbox + Saga 跨服务一致性模式
├── Event Sourcing 事件溯源模式
├── Read Model 物化视图模式
├── BFF Backend for Frontend 模式
└── Anti-Corruption Layer 防腐层模式
```

> 💡 **想看哪个模式优先?** 欢迎在 [Issues](https://github.com/ArchAIHarness/docs/issues) 投票或提需求。

## 模式不是教条

每个模式都有**适用场景**与**反模式警告**:

- ✅ 用对场景:事半功倍,长期受益
- ❌ 用错场景:过度设计,徒增复杂

阅读时请重点关注每篇文档的**「适用场景速查」**章节。
