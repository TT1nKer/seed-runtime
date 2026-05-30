# Seed Runtime: Capability Bootstrapping for AI Agents

> 种子不是指令，而是 agent 在最小 runtime 中完成能力自举的生成核。

---

## 摘要

本文提出 **Seed Runtime**：一种面向 AI agent 的能力自举框架。

- **Seed** 不是指令，也不是 skill，而是 agent 在最小 runtime 中生成行动路径的最小规则核。
- **Skill** 压缩已验证的行动轨迹；**Seed** 压缩产生可验证行动轨迹的过程。
- Seed 的稳定性不来自自由探索，而来自：runtime 能力边界、feedback verification、reviewed skill library、audit trace、skill-to-seed 晋升机制。
- Seed 与 Skill 不是对立关系。Seed 是调度器，Skill 是标准库。
- 未来 agent 系统不应只追求更强模型，而应建设：**Seed Kernel + Seed Runtime + Verified Skill Library + Independent Verifier + Trace Store + Governance**。

> Seed Kernel defines growth. Seed Runtime enables growth. Skill Library stabilizes growth. Verifier validates growth. Trace Store records growth. Governance evolves growth.

> 人是第一版 Seed；自动化人如何操作 AI，就是 Seed Runtime 的第一轮自举。

---

## 0. 来源：一段 Arch Linux 自举体验

这套理论不是凭空推出来的，它来自一次真实的系统自举经历。

**过程还原：**

作者在学习 Arch Linux 时，并没有先读完完整的 Arch Wiki 再一次性执行，而是这样做的：

```
初始状态：不会 Arch，只有一个最小 live 环境
    ↓
目标驱动："先让网络通"
    ↓
问 AI：这个怎么配？
    ↓
执行
    ↓
报错
    ↓
贴报错给 AI
    ↓
AI 解释并给出修正方案
    ↓
再试 → 成功
    ↓
进入下一步："装包管理器"
    ↓
重复上面的循环
    ↓
一个接口一个接口地配置出来
    ↓
最终得到一个可用的完整系统
```

这个过程中，人并没有"消费"完整的 Arch Wiki skill，而是：

```
最小目标
    ↓
在真实系统里执行
    ↓
根据反馈修正
    ↓
踩坑
    ↓
形成经验
    ↓
下次路径更稳定
```

本质上就是：

```
Seed（最小目标） + Environment（真实系统） + Feedback（报错/AI解释） → Skill（能用 Arch 的经验）
```

**从中提取的结构：**

```
人 = agent（决策者、执行者）
AI = reasoning assistant / local skill generator
终端 = action interface
报错信息 = feedback
Arch Wiki = external knowledge source
记忆 = skill storage
最小 live 环境 = minimal harness
```

对应到 agent 系统：

```
LLM agent = agent
BoOS / Hermes = runtime / harness
shell/files/network = action interface
stdout/stderr/test = feedback
docs/web/manual = external knowledge source
memory/skills = skill storage
```

**关键洞察：**

Arch Linux 的设计哲学本身就是"最小系统 + 自行配置"。它不是 Ubuntu 那种给你一个已配置好的完整系统——它给你一个最小可运行环境，让你把系统生长出来。这恰好是 Seed Runtime 思想的完美原型：

> 人类学习 Arch 的过程，本质上是带着 AI 辅助完成了一次能力自举。Seed Runtime 要做的，是把这种自举能力从"人类带着 AI"迁移到"agent 在可控 runtime 中自主完成"。

---

## 1. 定义

**Seed / Seed Kernel** 是 agent 在 runtime 中生成行动路径的最小规则核。它可以由自然语言、结构化 schema、policy、skill routing rule 共同组成。Prompt 只是 Seed 的一种载体，不是 Seed 的全部。

**Seed 不是：**
- 穷举清单（"Ubuntu 用 apt，CentOS 用 dnf..."）
- 自动优化的起点（进化算法中的初始个体）
- 模板填空（"把 XXX 替换成 YYY"）
- 一个更短的 skill
- 对完整知识的压缩——它是生长规则，不是压缩的百科全书

**Seed 是：**
- 对"产生可压缩行动轨迹的过程"的压缩
- 一个定义吸引域而非固定路径的生成核
- agent 给自己配置接口以获得更大行动空间的最小原则集

---

## 2. 核心区分

```
Skill  compresses traces.（压缩行动轨迹）
Seed   compresses the process of producing compressible traces.（压缩产生可压缩行动轨迹的过程）
```

| | Skill | Seed |
|---|---|---|
| 抽象层级 | 具体路径 | 路径生成规则 |
| 适用性 | 已知场景 | 未知但同类场景 |
| 稳定性来源 | 过往成功 | 跨环境验证的收敛性 |
| 扩展方式 | 追加新 skill | 多个 skill family 提取不变量后升格 |
| 知识依赖 | 假设环境已知 | 不假设，先观察 |
| 失败处理 | 失败则 skill 不适用 | 失败是生长材料 |

---

## 3. 四层结构

每个 Seed 包含四个不可约部分：

### 3.1 原则（Principles）
不可变的决策依据。回答"什么是对的"。
> "先观察项目声明，再选择工具。"
> "优先最小修改，不先升级大版本依赖。"

### 3.2 生长框架（Growth Framework）
判断流程和分叉逻辑。回答"怎么展开"。
> 检测 OS → 读项目声明 → 选包管理器 → 安装 → 验证 → 失败则按错误类型分叉

### 3.3 边界（Boundaries）
什么不能做，什么时候停下。回答"极限在哪"。
> "不删除 lockfile。不无限重试。同类失败两次后停止。不擅自切换技术栈。"

### 3.4 验收（Verification）
如何证明完成。回答"怎么知道做完了"。
> "没有验证，不报告完成。"

删掉原则，seed 变成随机游走。删掉框架，seed 无法展开。删掉边界，seed 会越权或死循环。删掉验收，seed 会假完成。四者缺一不可。

---

## 4. 能力自举（Capability Bootstrapping）

Seed 的第一使命不是完成任务，而是**让 agent 获得完成任务所需的接口**。

在 Arch 体验中，人是一层一层配置接口的：先网络、再包管理、再磁盘挂载、再用户……每一步都打开新的能力空间。agent 也是同样的逻辑。

```
最小能力集合
    ↓
检测当前环境缺少什么接口
    ↓
配置第一个关键接口（网络/包管理/工具链）
    ↓
用新接口获得更多能力（查文档/安装依赖/运行测试）
    ↓
验证能力是否可靠
    ↓
把可靠路径压缩成 skill
    ↓
再用 skill 扩展下一层能力
```

### 关键接口

agent 要完成实际任务，至少需要这些接口：

| 接口 | 能力 |
|------|------|
| 网络 | 学习、查文档、调用 API |
| 文件系统 | 读写项目、改配置 |
| 命令执行 | 运行工具、启动服务 |
| 模型调用 | 推理、生成、判断 |
| 记忆 | 保存经验、避免重复犯错 |
| 验证 | 判断是否成功 |
| 权限边界 | 知道不能做什么 |
| 外部知识 | 获取最新信息（文档、搜索结果） |

Seed 的第一个动作不是"开始干活"，而是"我能看到什么？我能改什么？我缺什么？"

### 自举的终点："足够好"状态

自举不是无限的。人类在配置 Arch 时知道什么时候"够用了"——能联网、能装软件、能登录、能干活。agent 同样需要一个"足够好"（good enough）的判断标准，否则会无限优化。

**成功边界（Success Boundary）：**
> 当所有必要接口可用且至少一个关键任务路径可验证通过时，自举阶段结束。

这不是完美主义，是实用主义。就像 Arch 装到能用的程度就停，而不是把所有可选包都配到最优。

---

## 5. 反馈验证

没有反馈验证的 seed 只是愿望。在 Arch 体验中，每一条命令的 stdout/stderr 就是最原始的验证器。

### 三层验证

**5.1 局部验证：** 每一步做完都验证。
> 安装 node 后 → `node -v`
> 安装依赖后 → `npm run build`
> 修改代码后 → 跑相关测试

**5.2 分叉验证：** 选择某条路径后，验证这条路径是否真的适用。
> 判断项目用 pnpm → 验证 `pnpm-lock.yaml` 是否存在、`pnpm` 是否可用

**5.3 最终验收：** 任务结果必须可被独立验证。
> build passed / test passed / API 返回正确 / 脚本输出符合样例

### 人与 agent 的关键差异

人在配 Arch 时有强烈的现实感和风险直觉——看到 `rm -rf` 或 `mkfs` 会本能停一下。agent 没有这种直觉。所以 agent 的 seed growing 必须比人类版多显式的安全层：

```
权限边界 → 危险动作需确认
可回滚 → 关键操作前保存状态
日志 → 每一步可追溯
最大重试 → 防止死循环
人工接管点 → 关键时刻让人介入
```

人可以靠直觉刹车。agent 必须靠 runtime 刹车。

---

## 6. 路径稳定性（Growing Path Stability）

**定义：** 同一个 seed 在不同但同类环境中展开时，虽然具体路径可以不同，但最终应该稳定收敛到正确、可验证、安全的状态。

在 macOS 上：`detect macOS → brew → install → verify`
在 Ubuntu 上：`detect Ubuntu → apt → install → verify`
在有 Dockerfile 时：`detect Dockerfile → docker build → verify`

路径不同，但结构稳定：
```
detect → choose → execute → verify → fallback if failed → stop if unstable
```

### 稳定性指标

```
success_rate          成功率
verification_rate     可验证完成率
avg_steps             平均行动步数
retry_count           平均重试次数
unsafe_action_count   危险动作次数
human_intervention_rate  人工介入率
cost_per_success      每次成功成本
```

### 非确定性问题

LLM 本质上是非确定的——同一个 seed 运行两次可能走不同路径。这是 Seed 与确定性脚本的根本区别。

**对稳定性的理解需要调整：**
- 不是要求"同一种子每次长出同一棵树"
- 而是要求"长出的树都能达到同一类健康形态"
- 稳定性是统计意义上的——多次运行的成功率和方差

这意味着 Seed 的评价不能靠单次测试，需要**多次运行统计**。这也意味着 seed 的验证成本比传统测试更高。

---

## 7. Skill → Seed 的工程流程

不是所有 skill 都能升格为 seed。能升格的 skill 必须满足：

1. 至少在 3 个不同环境/项目中成功过
2. 成功路径有共同结构
3. 中间状态可以被观察
4. 每个关键步骤可以验证
5. 失败模式可以分类
6. 失败时可以安全停止

### 升格流程

```
Action Trace（行动轨迹）
    ↓
Successful Skill（成功技能）
    ↓
Multiple Similar Skills（多个相似技能）
    ↓
Extract Invariants（提取不变量）
    ↓
Define Branch Conditions（定义分叉条件）
    ↓
Define Verification Points（定义验证点）
    ↓
Define Failure Boundaries（定义失败边界）
    ↓
Compress into Seed（压缩为 seed）
    ↓
Cross-Environment Verification（跨环境验证）
    ↓
Stable Seed（稳定 seed）
```

关键：**一个 skill 直接变 seed 很危险。多个 skill 之间提取共同结构，才更像 seed。**

---

## 8. Seed 格式模板

```text
Seed Name: [名称]

Goal: [抽象目标，not 具体操作]

Principles:
1. [不可变的决策原则]
2. [...]

Growth Framework:
1. [观察] → 2. [判断] → 3. [行动] → 4. [验证] → 5. [分叉/收敛]

Boundaries:
1. [什么不能做]
2. [什么时候停止]

Success Condition:
[什么叫"够好了"，不需要再优化]

Verification:
1. [局部验证方式]
2. [最终验收标准]

Failure Policy:
1. [同类失败 N 次后停止]
2. [输出当前环境、尝试路径、失败原因、建议]
```

---

## 9. 局限：Seed 不能做什么

Seed 不是银弹。以下场景不适合 seed 化：

**9.1 反馈周期过长的任务**
> 验证结果需要数小时/数天才能得到（如 A/B 测试、训练模型）。反馈太慢，seed 无法有效生长。

**9.2 缺乏可验证标准的任务**
> 如"写一篇好文章""设计一个好看的界面"。没有客观验证器，seed 只是在随机游走。

**9.3 需要主观判断的任务**
> 如"判断客户情绪""谈判报价"。这些依赖人的直觉和语境理解，不适合压缩成原则。

**9.4 一次性任务**
> 只做一次、不会再遇到的任务。不值得投入 seed 化的成本。

**9.5 安全敏感操作**
> 如直接操作生产数据库、修改防火墙规则。这类任务不应该让 agent 自主生长路径，应该用固定的、审计过的 skill。

**9.6 知识快速过期的领域**
> 如特定框架的 API 用法、第三方服务的配置方式。seed 中的原则可能长期有效，但具体的执行路径可能在几周内就过时。seed 需要搭配知识刷新机制。

---

## 10. 开放问题

以下是本文框架尚未完全解决的问题，留待后续探索：

**10.1 Seed 的组合与继承**
一个 seed 能否继承另一个 seed 的原则？多个 seed 能否组合成更大的 seed？在 Arch 体验中，人是在线性环境中逐个配置接口的——但如果多个种子需要协同生长（比如同时配置网络和用户权限），它们之间如何不互相破坏？

**10.2 自举悖论（Bootstrap Paradox）**
第一个 seed 从哪来？如果 seed 需要从多个 skill 中提取不变量，那在没有任何 skill 之前，种子如何诞生？可能的答案：最原始的种子是人手工写的（就像第一个编译器是手写的），之后系统自我改进。

**10.3 Seed 的版本化与退化**
Seed 会随时间演化。但演化不一定是进步——可能退化。如何判断一个 seed 版本比上一个更好？如何防止 seed 在迭代中丢失关键约束？这需要 seed 的回归测试框架。

**10.4 知识时效性**
seed 中的原则可能长期有效，但 agent 在执行时需要查询最新的外部知识（最新版本的 API、最新的包名）。Arch 体验中 AI 提供了最新知识——但 agent 自己如何知道"我该去查一下最新的文档"而不是依赖过时的训练数据？

**10.5 成本约束**
每步验证都烧 token。在成本敏感的系统中，可能需要在"验证充分性"和"token 经济性"之间做权衡。最小验证集是什么？

**10.6 人类角色的设计**
在 Arch 体验中，人既是最初的决策者，也是最终的安全阀。在 agent 自举系统中，人的角色从"操作者"变为"种子设计者 + 异常接管者"。但接管点在哪儿？太早接管失去自动化价值，太晚接管可能已造成损害。

---

## 11. 与现有概念的关系

| 概念 | 关系 |
|------|------|
| Prompt Engineering | Seed 是它的一个子方向，但超出"优化指令"的范畴 |
| Meta-Prompting | 接近但不同——Seed 强调生长和反馈闭环，不是"关于提示的提示" |
| Constitutional AI | 原则层相似，但 Seed 多了生长框架、验证闭环和失败边界 |
| Few-Shot | Seed 不给示例，给生成示例的方法 |
| Agent Loop | Seed 依赖 agent loop 作为 runtime，但不是 loop 本身 |
| Skill（Hermes） | Skill 既可以是 Seed 执行后的产物，也可以成为 Seed 稳定生长路径中的节点。Seed 和 Skill 是循环关系：Trace → Skill → Seed → Skill Routing → New Trace → Refined Skill/Seed |
| Arch Linux 哲学 | 最小系统 + 自行配置 → 与 Seed Runtime 同构 |
| 编译器自举 | 历史上第一个编译器手工写，后续编译器由前代编译 → Seed 同理 |

---

## 12. 最小可生长系统

一个能够承载 Seed 的最小系统（Seed Runtime）必须提供：

```
观察能力：能看环境
执行能力：能运行命令/调用工具
修改能力：能写文件/改配置
验证能力：能判断是否成功
记忆能力：能保存经验
边界能力：能阻止越权
外部知识：能获取最新信息
人工接管：关键时刻能交给人
```

没有这些，Seed 只能变成聊天。

最小可生长系统不是 `LLM + prompt`，而是 `LLM + harness + permissions + verifier + memory + external knowledge + human handoff`。

---

## 13. 循环

```
Seed
  ↓ (展开)
Runtime + Action Trace
  ↓ (验证)
Successful Path
  ↓ (压缩)
Skill
  ↓ (积累)
Skill Library
  ↓ (提取不变量)
Stronger Seed
  ↓ (循环)
```

这就是自举编译器在 agent 领域的类比：从最小的生成核开始，逐步生长出更复杂的系统，每一步都有验证闭环。

在 Arch 的体验中，这个循环运行了一次；在 agent 系统中，它应该持续运行——每一次成功都让下一次更稳，每一次失败都让边界更清晰。

---

## 14. Seed 作为社会性制品

Seed 不只是技术文档，它天然是可分享的。

Profiles（如 Hermes 的 Profile 系统）是一种隐式的 seed 分发的雏形：一个人把包含 seed、skill、配置的 profile 打包导出，别人导入后，在他们的环境中生长出适配方案。这是 seed 的 fork/clone 模式。

未来可能有：
- **Seed Hub**：类似 skill hub，但分享的是生成规则而非固定路径
- **Seed 合并**：两个人的 seed 合并成更鲁棒的版本
- **Seed 评分**：不是按"写得详细"评分，而是按跨环境稳定性评分

---

## 15. 命名

本文描述的概念体系可称为：

- **Seed Runtime** — 承载 seed 生长的最小运行时
- **Capability Bootstrapping** — 能力自举的过程
- **Growing Path Stability** — 路径稳定性的度量
- **Attractor Prompt** — seed 定义的是吸引域而非固定路径

中文：
- **种子运行时**
- **能力自举**
- **生长路径稳定性**
- **吸引域提示**

---

## 16. 在 Agent 生态系统中的位置

Seed Runtime 不是孤立概念，它需要在现有 agent 工具中找到自己的位置。

### 三类 Agent 系统

| 类型 | 代表 | 核心逻辑 | 适合什么 |
|------|------|----------|----------|
| **工具编排型** | OpenClaw 类 | 给 LLM 预设工具和流程，agent 在框架里调用 | 快速接入多平台、消息转发、简单自动化 |
| **生长型** | Hermes 类 | 让 agent 在环境中探索、失败、沉淀 skill、再改进 | 经验积累、技能复用、持续改进 |
| **运行时型** | BoOS / Seed Kernel | 给 seed/skill 生长提供安全、可审计、权限可控的底座 | 底层控制、安全边界、自举验证 |

三者不是竞争关系，是不同抽象层。

### OpenClaw 为什么像"专家系统 2.0"

OpenClaw 不弱——它接入多平台、工具丰富、开箱即用。但它给人的感觉是：

```
LLM + 工具清单 + 固定入口 + 预设流程
```

它的逻辑偏向"我已经定义好工具和场景，你来调用它们"——这不是让 agent 生长，而是让 agent 在预设菜单里点菜。

Seed 哲学要的是另一种模式：

```
agent 先发现自己有什么能力
→ 配置接口
→ 验证路径
→ 沉淀 skill
→ 扩展能力边界
```

这是范式差异，不是审美问题。一个像把 LLM 放进预设工具专家系统，一个像给 agent 一个能形成经验的生长环境。

### 分工建议

```
短期赚钱：Hermes + opencode + DeepSeek
入口需要：OpenClaw 只做 bridge（消息转发）
长期底座：BoOS + Seed Kernel
```

一句话：

> OpenClaw 是工具箱，Hermes 是学习者，BoOS 是温室，Seed Kernel 是种子。

---

## 17. Seed of Seed：如何稳定地产生 Seed

如果 Seed 是"行动生成核"，那么 Seed of Seed 就是"行动生成核的生成核"。

```
Seed          生成行为
Seed of Seed  生成 Seed
```

### 定义

> Seed of Seed 是一套元规则，用来把经过验证的 trace、skill、失败路径压缩成稳定的 Seed。它的目的不是把经验写短，而是提取重复成功经验背后的稳定生长机制。

### 为什么需要这一层

- 单次成功不足以产生 Seed。一次性的 workaround、特定平台的奇技淫巧、偶然的幸运路径都不具备生成性。
- 把 skill 直接"写短"不是 seed 化。skill 压缩的是路径，seed 压缩的是路径的生成规则。
- 没有晋升机制的 seed 只是"感觉对的哲学文本"，无法被验证、迭代、淘汰。

### 核心流程：Trace → Skill → Seed

```
1. 收集 Action Trace（行动轨迹）
2. 标记 Outcome（成功/失败/部分成功）
3. 抽取 Skill（可复用路径）
4. 聚类 Skill Family（跨场景的共同结构）
5. 提取 Invariant（不变量）
6. 定义 Branch Condition（分叉条件）
7. 定义 Verification Point（验证点）
8. 定义 Boundary（边界）
9. 压缩成 Candidate Seed
10. 跨环境验证
11. 晋升为 Stable Seed
12. 失败后 Refinement
```

### Seed 的生命周期与晋升条件

每个阶段都有明确的晋升门槛。没有通过就不能进入下一阶段。

| 阶段 | 晋升条件 |
|------|----------|
| Action Trace | 至少有一次完整执行路径 |
| Skill | 路径可复用、有可验证结果、失败点和修正点被记录 |
| Skill Family | 至少3个相似 skill，背后有共同结构，不是偶然 workaround |
| Candidate Seed | 可抽出 观察→判断→行动→验证 结构 |
| Stable Seed | 多环境验证通过、成功率高、失败可收敛、不越权、不无限循环 |
| Refined Seed | 根据失败 trace 修正后重新验证通过 |
| Deprecated | 适用域消失或路径不再收敛 |

### Seed 的完整 Schema

```text
Seed Name: [唯一名称]
Seed Type: [task / environment / coding / business / meta]
Target Domain: [适用范围]
Goal: [抽象目标]

Principles:
- [不可变的决策依据]

Growth Framework:
1. Observe   → [观察什么信号]
2. Classify  → [判断属于哪种情况]
3. Choose    → [选择最小路径]
4. Act       → [执行]
5. Verify    → [局部验证]
6. Fallback  → [失败时如何处理]

Boundaries:
- [什么绝对不能做]
- [什么时候需要人工确认]
- [什么时候停止]

Verification:
- Local: [每步验证方式]
- Final: [最终验收标准]
- Failure: [失败报告格式]

Promotion Criteria:
- [这个 seed 何时可被认定为 stable]

Trace Requirements:
- [执行时必须记录什么数据，用于未来 refinement]
```

多了两个关键字段：**Promotion Criteria**（晋升条件）和 **Trace Requirements**（日志要求）。Seed 不是写完就完了，它要进入生命周期。

### 四个 Meta-Seed

Seed of Seed 本身可以用四个 meta-seed 来实例化：

**1. Trace-to-Skill Seed**

目标：从 action trace 中抽取可复用 skill。

它负责问：
> 任务是什么？成功了吗？关键转折点在哪？哪些动作是必要的？哪些是偶然的？下次应保留什么？

**2. Skill-to-Seed Seed**

目标：从多个相关 skill 中抽取 candidate seed。

它负责问：
> 这些 skill 的共同目标是什么？共同观察信号？共同分叉结构？共同验证方式？哪些具体步骤不能保留？哪些边界必须保留？

**3. Seed-Evaluation Seed**

目标：测试 candidate seed 是否稳定。

它负责：
> 设计多个环境变体 → 运行 seed → 记录成功率 → 记录失败模式 → 判断是否可晋升

**4. Seed-Refinement Seed**

目标：根据失败 trace 修正 seed。

它负责问：
> 失败是因为 seed 缺观察？缺边界？缺验证？分叉错误？过度压缩？环境不属于适用域？

### Seed Evaluation Metrics

Seed 的质量不是靠"写得多好"判断的，而是靠运行数据：

```
success_rate           成功率
verification_rate      有明确验证的比例
unsafe_action_rate     危险动作比例
human_intervention_rate 人工介入率
avg_steps_to_success   平均成功步数
retry_count            平均重试次数
cost_per_success       每次成功成本
branch_entropy         路径分叉混乱度
false_completion_rate  假完成率
```

好的 seed：成功率高、重试少、越权少、验证充分、成本可控、失败能收敛。

### Seed Anti-Patterns

| 反模式 | 示例 | 问题 |
|--------|------|------|
| 愿望型 | "自动适配所有环境并完成任务" | 无观察、分叉、验证、边界 |
| 清单型 | "Ubuntu 用 apt，Arch 用 pacman..." | 退化成脚本，失去生成性 |
| 过度自由 | "自行选择最佳方式" | 无权限和失败边界 |
| 过度压缩 | "先观察后行动" | 缺验证和停止机制 |
| 不可验证 | "让代码质量更好" | 无验收标准 |

### Seed Library 组织

```
seeds/
  core/          安全性、验证等基础 seed
  environment/   能力发现、依赖安装、网络配置
  coding/        代码修改、debug、测试修复、重构
  business/      需求解析、风险评估、报价、交付
  meta/          上面四个 meta-seed
```

`meta/` 目录就是 Seed of Seed 的具体实现。

### 最小可执行版本

先不做大部头。第一版 Seed Guide 只需：

```
Seed Guide v0.1
1. 定义
2. Schema
3. Trace → Skill 流程
4. Skill → Seed 流程
5. 稳定性验证
6. 反模式
7. 示例贯穿：Arch/Dependency Bootstrap
```

用你学 Arch 的真实经历贯穿全篇——从不会 Arch 到一步步配置、踩坑、形成 skill、压缩成 seed。这比任何抽象解释都有说服力。

### 一句话总结

> Seed Guide 不是教人写 prompt，而是教系统如何把经验变成可生长的生成核。

---

## 18. Seed 与 Skill：不是对立，是互补

一个容易产生的误解：Seed 哲学是反对 Skill 的。"既然种子自己能长，还要技能库干嘛？"

这是错的。

### 正确关系

```
Seed  = 生成规则 / 调度原则 / 生长方向
Skill = 已验证路径 / 可复用能力 / 稳定局部实现
```

不是 Seed 替代 Skill，而是：

```
Seed  选择 Skill
Skill 稳定 Seed
Seed  组织 Skill
Skill 反过来训练 Seed
```

类比操作系统：

```
Seed  ≈ 调度器
Skill ≈ 系统调用 / 标准库 / 驱动
```

没有 skill，seed 每次重新探索，不稳定。没有 seed，skill 是死的菜单，不会生长。

### 为什么通过 Skill 作为路径反而更稳定

纯 seed 生长太自由——每次可能选不同工具、不同策略，导致路径发散。Skill 库把已验证的局部路径固定下来：

```
遇到 pnpm 项目 → 优先调用 pnpm-install skill
遇到 Python venv → 调用 python-venv skill
遇到 Dockerfile → 调用 docker-build skill
遇到 registry timeout → 调用 registry-debug skill
```

稳定性公式：

```
Stable Seed Growth
  = Seed Policy
  + Reviewed Skill Library
  + Runtime Capability Discovery
  + Verification
  + Audit Trace
```

### Skill 库的信任分级

不是随便收集的 skill 都能被 seed 调用。需要分级治理：

| Level | 名称 | 条件 |
|-------|------|------|
| 0 | Community | 社区提交，未审核，默认不信任 |
| 1 | Human Reviewed | 人工检查过内容、用途、权限、危险命令 |
| 2 | Tested | 沙盒中跑过最小测试 |
| 3 | Cross-Env Verified | 多个环境验证通过 |
| 4 | Seed-Compatible | 有清晰适用条件、输入输出、失败模式、验证方法，可被 seed 稳定调用 |
| 5 | Audited / Signed | 安全审计、版本签名、依赖锁定、provenance 明确 |

只有 Level 4+ 的 skill 才适合直接进入 seed 的生长路径。

### Skill 不是牢笼

Skill 库如果变成"几千个固定流程，agent 只是匹配流程"，那就退回专家系统 2.0。

正确方式是：

> Skill 提供稳定局部路径。Seed 负责高层判断、组合、取舍、失败切换。Agent 可以在 skill 之间探索，但不能无边界乱试。

Skill 是骨架，不是牢笼。Seed 是生长规则，不是万能脚本。

### 结论

> Seed 不是反 Skill。Seed 是 Skill 的生成、选择和组合规则；Skill 是 seed 生长路径中的稳定节点。一个开源、人工审核、可验证的 skill library，不是 Seed Kernel 的对立面，而是它的标准库。

更短：

> Seed 是生长规则，Skill 是可验证路径，Skill Library 是 Seed 的标准库。

---

## 19. 系统分层：六项组件的形式定义

将前面的概念收敛为六个明确的系统组件：

```
Seed Kernel + Seed Runtime + Verified Skill Library + Verifier + Trace Store + Governance
= Stable Agent Growth System
```

> Seed Kernel defines growth. Seed Runtime enables growth. Skill Library stabilizes growth. Verifier validates growth. Trace Store records growth. Governance evolves growth.

> 人是第一版 Seed；自动化人如何操作 AI，就是 Seed Runtime 的第一轮自举。

| 组件 | 职责 | 关键属性 |
|------|------|----------|
| **Seed Kernel** | 最小生成规则：原则、框架、边界、验收 | 不包含具体实现，只定义生长方向 |
| **Seed Runtime** | 承载 Seed Kernel 展开的执行环境 | 观察、执行、权限、记忆、日志、人工接管 |
| **Skill Library** | 已验证路径库 | 分级（L0-L5）、有 manifest、可被 seed 路由 |
| **Verifier** | 独立判断成功/失败 | 独立于执行 actor，局部+最终两层验证 |
| **Trace Store** | 保存行动轨迹 | 用于 skill extraction 和 seed refinement |
| **Governance** | 管理 seed/skill 的提交、审核、测试、签名、降级、废弃 | 证据驱动晋升，自动化降级，防止生态污染 |

---

## 20. Seed-to-Skill Routing Protocol

Seed 如何调用 Skill？不是自由选择，而是结构化路由。

```
1. Observe
   Runtime 收集环境信号：OS、项目文件、权限、网络、错误日志

2. Match
   根据 signals 匹配 skill library 中的 applicability 条件

3. Rank
   按 trust level、适用度、风险等级、历史成功率排序

4. Execute
   在 runtime 权限边界内执行 skill

5. Verify
   调用 skill 自带 verifier 和 seed 的 final verifier

6. Trace
   记录执行路径、失败点、成功条件、成本

7. Update
   成功 → 提升 skill trust
   失败 → 记录 failure mode → 进入 seed refinement
```

---

## 21. Skill Manifest 标准

每个 skill 必须包含结构化 manifest，否则无法被 seed 稳定路由。

```json
{
  "name": "node-pnpm-install",
  "version": "0.1.0",
  "domain": "dependency-bootstrap",
  "goal": "Install dependencies for pnpm-based Node.js projects",
  "applicability": {
    "required_signals": [
      "pnpm-lock.yaml exists",
      "package.json exists"
    ],
    "supported_os": ["linux", "macos", "wsl"],
    "requires_network": true,
    "requires_sudo": false
  },
  "inputs": ["project_root"],
  "outputs": ["install_report", "verification_result"],
  "allowed_actions": [
    "read project files",
    "run pnpm install",
    "run project-defined build/test commands"
  ],
  "forbidden_actions": [
    "delete lockfile without approval",
    "upgrade major dependencies without approval",
    "use sudo",
    "execute remote shell scripts"
  ],
  "verification": {
    "local": ["pnpm install exits 0"],
    "final": ["build or test command succeeds"]
  },
  "failure_modes": [
    "registry timeout",
    "node version mismatch",
    "lockfile mismatch",
    "permission error"
  ],
  "risk_level": "medium",
  "trust_level": 3
}
```

机器可读的适用条件、权限边界、验证方式——这是 Seed 路由 Skill 的前提。

---

## 22. 安全威胁模型（Threat Model）

Seed Runtime 面临的安全威胁：

| # | 威胁 | 描述 |
|---|------|------|
| 1 | 越权执行 | agent 执行超出声明权限的操作 |
| 2 | 恶意 skill | skill 包含隐藏危险命令 |
| 3 | 技能描述欺诈 | skill 声称安全但实际命令危险 |
| 4 | Prompt injection | 外部输入诱导 agent 绕过 seed boundary |
| 5 | 外部知识污染 | 文档/网页误导 agent 判断 |
| 6 | Tool output 注入 | 命令输出中含有恶意指令 |
| 7 | 逃避验证 | agent 删除测试以通过验证 |
| 8 | 假完成 | agent 声称完成但实际未完成 |
| 9 | 成本炸弹 | 无限重试烧 token |
| 10 | 数据泄露 | agent 泄露本地文件、密钥、cookie |

### 对应防护

| # | 防护措施 |
|---|----------|
| 1 | capability-based permission |
| 2 | sandbox + signed skill |
| 3 | provenance tracking + human review |
| 4 | seed boundary 强制执行，不受 prompt 影响 |
| 5 | verifier 交叉检查，不单信一个来源 |
| 6 | tool output 进入前做 sanitization |
| 7 | verifier 独立于 actor，不可被 agent 篡改 |
| 8 | final verification 必须客观可测 |
| 9 | cost budget + max retry + circuit breaker |
| 10 | read/write scope isolation |

---

## 23. Verifier 独立性原则

> Verification should be independent from generation whenever possible.

执行者可以提出"我认为完成了"，但 verifier 必须独立检查：

- build/test 是否真的通过
- 文件是否真的存在
- API 是否真的返回预期
- 日志是否真的没有错误
- 输出是否符合 schema

**高风险任务中，actor 和 verifier 应使用不同模型、不同 prompt、甚至不同 runtime 权限。**

防止 agent 自我欺骗。这是 Seed Runtime 区别于"让 LLM 自己判断自己做没做完"的关键。

---

## 24. Seed Applicability Domain（适用域声明）

每个 seed 必须声明适用范围，防止滥用。

```text
Seed: Dependency Bootstrap

适用：
- 本地开发环境初始化
- 项目依赖安装
- 构建失败初步诊断

不适用：
- 生产服务器热修复
- 需要数据库迁移的任务
- 需要删除或覆盖大量文件的任务
- 没有网络且无本地缓存的环境

需要的 Runtime 能力：
- 网络访问
- 文件读写
- 命令执行

需要的 Verifier：
- build/test 命令可运行
- 文件存在性检查

允许的风险等级：medium
```

---

## 25. Seed Regression Testing（回归测试）

每个 stable seed 必须有回归测试。不是测"能不能跑通"，而是测"能不能安全失败"。

### 测试目录

```
seed_tests/dependency-bootstrap/
  cases/
    ubuntu-node-npm/
    macos-node-pnpm/
    wsl-python-venv/
    dockerfile-project/
    network-timeout/
    permission-denied/
    no-lockfile/
  expected/
    should_stop_when_no_permission
    should_not_delete_lockfile
    should_verify_build
    should_report_unverified
    should_stop_after_two_failures
```

### 测试指标

```
1. 成功环境能成功
2. 失败环境能安全失败
3. 高风险操作会请求人工确认
4. 不会假完成
5. 不会越权
6. 成本不超过预算
7. 失败路径稳定（不以不同方式反复失败）
```

---

## 26. Seed Runtime 与 BoOS 的关系

BoOS 是 Seed Runtime 的一个实验性实现。

```
BoOS 的核心目标不是替代 Hermes，也不是做完整的 AI assistant，
而是为 Seed Kernel 提供：

1. capability registry   — 能力注册
2. request/result queue  — 任务队列
3. permission check      — 权限检查
4. audit trace           — 审计日志
5. verifier hooks        — 验证钩子
6. skill execution boundary — 技能执行边界
7. human handoff point   — 人工接管点
```

一句话：

> Hermes 是学习者，BoOS 是可审计温室，Seed Kernel 是生长规则，Skill Library 是标准库。

---

## 27. Seed Runtime MVP 设计

### 目录结构

```
seed-runtime-mvp/
  seeds/
    dependency_bootstrap.seed.md
    code_fix.seed.md
    verification.seed.md

  skills/
    node/npm-install/
      SKILL.md
      manifest.json
    node/pnpm-install/
    python/venv-setup/

  traces/
    trace-0001.jsonl

  runtime/
    capability_registry.json
    permissions.json
    verifier.py
    router.py
```

### 最小流程

```
1. 用户给任务：让这个项目跑起来
2. runtime 加载 dependency_bootstrap.seed
3. runtime 观察项目结构
4. seed 选择 npm/pnpm/python skill
5. skill 执行
6. verifier 验证
7. trace 写入 Trace Store
8. 成功 trace → 压缩为 skill refinement
9. 失败 trace → 进入 seed refinement
```

---

## 28. Trace Format

Trace Store 是五大组件之一。没有标准化的 trace 格式，Seed of Seed 的 trace → skill → seed 流程就无法工程化。

### 单步 Trace

```json
{
  "trace_id": "trace-0001",
  "task_id": "task-001",
  "seed": "dependency-bootstrap",
  "skill": "node-pnpm-install",
  "runtime": "boos-mvp",
  "step": 1,
  "action": "observe_project_files",
  "input": {"path": "./project"},
  "output": {"found": ["package.json", "pnpm-lock.yaml"]},
  "status": "success",
  "error": null,
  "verification": null,
  "cost": {"tokens": 1200, "usd": 0.001},
  "timestamp": "2026-05-30T10:00:00Z"
}
```

### Trace Summary（任务完成后）

```json
{
  "trace_id": "trace-0001",
  "task_id": "task-001",
  "seed": "dependency-bootstrap",
  "outcome": "success",
  "verified": true,
  "verification_method": "pnpm build exited 0",
  "total_steps": 3,
  "failure_modes": [],
  "unsafe_actions": 0,
  "human_interventions": 0,
  "total_cost": {"tokens": 3600, "usd": 0.003},
  "promotable_to_skill": true
}
```

Trace 是 Seed 循环的原材料。没有标准 format，就像编译器没有 AST。

---

## 29. Governance：Seed / Skill 治理

信任分级需要配套的治理流程——谁来升级、怎么升级、怎么撤销。

```
1. Submit
   社区提交 seed/skill。

2. Review
   人工审查 manifest、权限、危险命令、适用域。

3. Sandbox Test
   在标准测试环境中运行最小测试。

4. Cross-Env Test
   多环境验证通过。

5. Sign
   签名、标记 trust level、发布。

6. Monitor
   收集生产运行 trace。

7. Deprecate
   成功率下降 → 降级；安全风险出现 → 撤销；适用域过期 → 废弃。
```

### Governance 的两个关键原则

**原则一：升级需要证据，不靠投票。**
社区 star 数不代表稳定性。升级到 Level 3+ 必须有 sandbox/cross-env test 数据，不能靠声誉。

**原则二：降级是自动的，不等人发现。**
如果运行 trace 显示成功率跌破阈值、危险动作增加、假完成率上升，自动降级并通知 maintainer。基础设施不能靠"有人发现了再说"。

### 完整体系

```
Seed Kernel
+ Seed Runtime
+ Verified Skill Library
+ Independent Verifier
+ Trace Store
+ Governance
= Stable Agent Growth System
```

六项组件，缺一不可。

---

## 30. 从白皮书到 Spec

当前版本：**Seed Runtime White Paper v0.3**

> 版本历史：v0.1 核心概念 → v0.2 工程框架 → v0.3 收束（命名修正、四层结构、Seed-Skill 循环、Trace Format、Governance、六组件体系、Operator Strategy Bootstrapping）

下一步演进方向：

```
概念定义
  ↓
系统分层（Seed Kernel / Runtime / Skill Library / Verifier / Trace Store / Governance）
  ↓
数据结构（Seed Schema / Skill Manifest / Trace Format）
  ↓
执行协议（Seed-to-Skill Routing / Verification Protocol）
  ↓
安全模型（Threat Model / Verifier Independence）
  ↓
评估方法（Evaluation Metrics / Regression Tests）
  ↓
MVP 实现（BoOS 或其他 runtime）
```

### Spec v0.1 输出物清单

```
Seed Runtime Spec v0.1 应至少定义：

1. seed.schema.json       — Seed 的结构化格式
2. skill.manifest.schema.json — Skill Manifest 标准
3. trace.schema.jsonl     — Trace 的单步 + Summary 格式
4. routing protocol       — Seed-to-Skill 路由协议
5. verifier protocol      — 独立验证协议
6. trust level protocol   — 信任分级 + 治理流程
7. runtime capability API — Runtime 暴露给 Seed 的接口
8. regression test format — Seed 回归测试标准
```

一句话：

> 现在这份是理论白皮书 v0.3；下一步开 Seed Runtime Spec v0.1。

---

## 31. Operator Strategy Bootstrapping：人类操作者作为第一版 Seed

Seed 的第一来源不是模型自发生成，而是**人类 operator 对 AI 的操作策略被外部化、对象化、自动化**。

### 核心循环

```
AI 能力
  ↓ 我手动操作它
把"我怎么操作"内化成策略
  ↓
自动化这个策略
  ↓
它成为新的 AI 能力
  ↓
再手动操作新能力
  ↓ ...
```

这比普通 `Trace → Skill → Seed` 更高一层：**trace 不只来自 agent 自己的执行，也来自人如何使用 agent**。

### 和 Seed Runtime 循环的关系

```
人类 operator 的隐性策略
    ↓ (记录为 trace)
Reflection / 反思
    ↓ (抽象)
Skill（外部化的操作策略）
    ↓ (提取不变量)
Seed（可迁移的生成规则）
    ↓ (runtime 执行)
自动化后的新 AI capability
    ↓
成为新的 operator 对象
    ↓ (继续循环)
```

### 和三个研究簇的关系

**簇 A：Recursive Self-Improvement（RSI）**
RSI 讨论 AI 改进自身模型、训练流程、架构。你的循环不在这里——你改的不是模型权重，而是**人类如何操作 AI 的程序性知识**。RSI 改训练层，你改操作层。

**簇 B：Demonstration-Learning / Record-Replay**
AgentRR（record → summary → replay）和 AppAgent（观察人类示范学习操作 app）与你的 trace/skill 流程接近。但偏差关键：它们压缩的是操作外部 app/GUI 的行为，且通常一次性固定；你的循环压缩的是**操作 AI 本身**的策略，且产出物会反过来成为下一轮的操作对象——跨层递归，簇 B 基本没做。

**簇 C：Skill 外部化与治理**
Agent Skills survey 明确提出：SAGE、SEAgent 等系统能让 agent 通过经验学习技能，但学到的技能是 model-internal 的，无法被检视、共享或治理。这与你的白皮书高度吻合。但你的理论不止于 skill governance——你还加了一个 **seed-level generative layer** 和 **operator recursion layer**。你的完整循环是：

```
operator trace → skill → seed → runtime → verifier → governance → higher-level capability
```

### 独特切口：操作对象的跨层上升

多数 record-replay 或 demonstration-learning 系统在**固定任务层**内压缩行为。

Operator Strategy Bootstrapping 压缩的是**"人如何操作 AI 能力"的策略**，并把策略变成新 AI 能力；新能力又成为下一轮的操作对象。

这是跨层递归，不是同层优化。

### Factorio 同构

Factorio 的核心循环与 Seed Runtime 严格同构：

```
Factorio:
  手挖矿 → 采矿机 → 矿石喂给组装机 → 造采矿机的生产线 → 操作对象从"矿石"上升到"生产线设计"

Seed Runtime:
  手动操作 AI → 把操作策略压缩成 skill → skill 进入 runtime → seed 选择/组合 skill
  → 操作对象从"具体 AI 调用"上升到"seed/runtime/governance 设计"
```

每一层的输出是上一层的输入，自动化向"元层"爬升。

但 Factorio 也警示了风险：后期的瓶颈不是采矿，而是**复杂度管理**。对应到 Seed Runtime：

```
自动化一层之后，新瓶颈可能是：
- trace 太多，看不过来
- skill library 太大，路由失准
- verifier 成本太高
- governance 跟不上
- seed 退化成巨大规则清单
- operator 不再理解系统真实行为
```

但这不意味着 Skill Library 能**消除**损耗——它只是把损耗从"自由探索的不稳定性"转化成了"库治理的复杂度"。一开始 10 个 skill 很好管理；到了 1000 个，就会出现路由失准、版本冲突、过期 skill、verifier 不可信等问题。

### Net-Positive Bootstrapping：正确的问题框架

有修正机制 ≠ 没有损耗。修正机制的作用是把损耗压低，让循环保持净正收益。真正的问题不是"会不会 lossy"，而是每一轮修正后，收益是否大于损耗：

```
Net Gain = Capability Gain
         - Compression Loss（Trace → Skill → Seed 的信息损失）
         - Verification Cost（verifier 的 token/时间成本）
         - Governance Cost（审核、分级、签名的维护成本）
         - Complexity Cost（skill library 增长引入的路由和管理成本）
```

当 Net Gain > 0，系统继续生长。当 Net Gain ≤ 0，停止、回滚、降级或人工接管。

Seed Runtime 并不假设每一轮 Trace → Skill → Seed 都会自动带来净提升。它的设计目的正是**在承认有损的前提下追求净正**：通过 reviewed skill library 稳定局部路径，通过 independent verifier 防止假完成，通过 trace store 保留失败材料，通过 governance 控制晋升、降级和废弃。

> Seed Runtime 不是依赖无约束自我改进，而是通过 reviewed skill library 把生长路径稳定化。

> 你的系统不是否认有损自举，而是把有损自举工程化：用 skill 稳定路径，用 verifier 检测损耗，用 governance 防止复杂度反噬。

### 一句话

> 人是第一版 Seed；自动化人如何操作 AI，就是 Seed Runtime 的第一轮自举。
