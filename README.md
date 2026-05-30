# Seed Runtime

**Capability Bootstrapping for AI Agents**

> 人是第一版 Seed；自动化人如何操作 AI，就是 Seed Runtime 的第一轮自举。

## What

Seed Runtime 是一套面向 AI agent 的能力自举框架。它定义了 agent 如何从最小规则核（Seed）出发，在 runtime 中逐步配置接口、获得反馈、验证路径、压缩经验，最终形成可复用的 Skill Library。

## Core Concepts

- **Seed** — 最小生成规则，不穷举实现，定义原则/框架/边界/验证
- **Skill** — 已验证的可复用路径，是 Seed 生长路径中的稳定节点
- **Seed of Seed** — 把 trace 和 skill 压缩成稳定 seed 的元规则
- **Net-Positive Bootstrapping** — 在有损环境中追求净正收益，而非假设无损递归

## Six Components

```
Seed Kernel + Seed Runtime + Verified Skill Library
+ Independent Verifier + Trace Store + Governance
= Stable Agent Growth System
```

## Read

- [WHITEPAPER.md](WHITEPAPER.md) — Full white paper (v0.3, 31 sections)

## License

MIT
