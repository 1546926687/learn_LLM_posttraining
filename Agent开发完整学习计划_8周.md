# LLM Agent 开发完整学习计划

> 目标：从"会用 Agent 框架"进阶到"理解原理 + 能训练 Agent 模型 + 能设计多 Agent 系统"
> 总周期：约 8 周（2 个月）| 每日投入：工作日 2-3 小时，周末 4-6 小时
> 起点：熟悉 Python + PyTorch，有 SFT/LoRA 经验，用过 LangChain 或类似框架搭过 RAG/Agent 应用
> 终点：能从底层手写 Agent runtime，能构造 tool-use 训练数据并微调模型，能设计多 Agent 协作系统和评测体系
>
> ⚠️ **时间估计说明**：每日标注的小时数是**最低投入**。论文精读、环境搭建、调参实验等任务经常会超时，预留 50% 缓冲是合理的。

---

# ═══════════════════════════════════════════
# 阶段一：Agent 核心原理与推理模式（第 1-2 周）
# ═══════════════════════════════════════════

> 目标：深入理解 Agent 的理论基础——推理、规划、工具调用的核心范式

## 📚 阶段一必读论文清单

| # | 论文 | 链接 | 安排周次 | 为什么要读 |
|---|------|------|----------|-----------|
| 1 | ReAct: Synergizing Reasoning and Acting in Language Models, Yao et al. 2022 | https://arxiv.org/abs/2210.03629 | 第 1 周 | Agent 最核心的范式——交替推理与行动，所有 Agent 框架的理论基础 |
| 2 | Chain-of-Thought Prompting Elicits Reasoning in LLMs, Wei et al. 2022 | https://arxiv.org/abs/2201.11903 | 第 1 周 | CoT 是 Agent 推理能力的基石 |
| 3 | Toolformer: Language Models Can Teach Themselves to Use Tools, Schick et al. 2023 | https://arxiv.org/abs/2302.04761 | 第 1 周 | 模型自主学会调用工具的奠基论文 |
| 4 | Tree of Thoughts: Deliberate Problem Solving with LLMs, Yao et al. 2023 | https://arxiv.org/abs/2305.10601 | 第 2 周 | 从线性推理到树状搜索，理解 Agent 的规划能力 |
| 5 | Reflexion: Language Agents with Verbal Reinforcement Learning, Shinn et al. 2023 | https://arxiv.org/abs/2303.11366 | 第 2 周 | Agent 的自我反思与纠错机制 |
| 6 | A Survey on Large Language Model based Autonomous Agents, Wang et al. 2023 | https://arxiv.org/abs/2308.11432 | 第 2 周 | 全景综述，建立 Agent 领域的知识地图 |

## 🎬 阶段一推荐课程/讲座

| 资源 | 链接 | 时长 | 何时看 |
|------|------|------|--------|
| Andrew Ng "AI Agentic Design Patterns with AutoGen" | https://www.deeplearning.ai/short-courses/ai-agentic-design-patterns-with-autogen/ | ~1h | 第 1 周 |
| Andrew Ng "AI Agents in LangGraph" | https://www.deeplearning.ai/short-courses/ai-agents-in-langgraph/ | ~1h | 第 2 周 |
| Harrison Chase "Building LLM Agents" (LangChain YouTube) | https://www.youtube.com/watch?v=DWUdGhRrv2c | ~1h | 第 1 周 |

## 🔧 阶段一核心代码资源

| 资源 | 链接 | 用途 |
|------|------|------|
| LangChain 源码 | https://github.com/langchain-ai/langchain | 阅读 Agent 模块核心逻辑 |
| OpenAI Function Calling 文档 | https://platform.openai.com/docs/guides/function-calling | 理解 tool-use 的工业标准协议 |
| Anthropic Tool Use 文档 | https://docs.anthropic.com/en/docs/build-with-claude/tool-use | Claude 的工具调用实现 |

## 🗂️ 阶段一核心数据集

| 数据集 | 链接 | 用途 |
|--------|------|------|
| HotpotQA | https://huggingface.co/datasets/hotpot_qa | 多跳推理基准，用于测试 ReAct 模式 |
| ALFWorld | https://github.com/alfworld/alfworld | 文本交互环境，经典 Agent 评测环境 |

---

## 第 1 周：ReAct + 工具调用原理

### 本周目标
深入理解 ReAct 范式和 Function Calling 协议，能手写一个最小的 ReAct 循环。

### 每日任务

**周一（2h）：精读 ReAct 论文**
- 论文：https://arxiv.org/abs/2210.03629
- 重点读 Section 1-3：Thought-Action-Observation 循环的设计动机
- 对比 CoT-only vs Act-only vs ReAct 三种模式的效果差异（论文 Table 1）
- 手动在 ChatGPT/Claude 中用 ReAct 格式做一个多步问答，体会循环过程

**周二（2h）：精读 CoT 论文 + Toolformer 论文前半部分**
- CoT 论文：https://arxiv.org/abs/2201.11903，重点 Section 2-3
- Toolformer 论文：https://arxiv.org/abs/2302.04761，重点 Section 1-3
- 理解 Toolformer 如何通过自监督让模型学会插入 API 调用标记
- 思考：Toolformer 的训练范式和 Function Calling 的推理范式有什么本质区别？

**周三（2h）：Function Calling 协议深入**
- 阅读 OpenAI Function Calling 文档：https://platform.openai.com/docs/guides/function-calling
- 阅读 Anthropic Tool Use 文档：https://docs.anthropic.com/en/docs/build-with-claude/tool-use
- 对比两者的 JSON Schema 格式、调用流程、parallel tool calling 差异
- 用 Python 调用 OpenAI/Claude API 实现一个带工具的对话（如天气查询 + 计算器）

**周四（2h）：手写一个最小 ReAct Agent**
- 不用任何框架，纯用 API 调用实现：
  - 定义 3 个工具（搜索、计算、日期查询）
  - 实现 ReAct 循环：构造 prompt → 解析 Thought/Action → 执行工具 → 拼回 Observation → 继续
  - 加入最大循环次数限制、错误处理
- 目标：理解 Agent 框架的核心不过是一个 while 循环

**周五（2h）：观看 Andrew Ng 课程 + Harrison Chase 讲座**
- Andrew Ng "AI Agentic Design Patterns with AutoGen"：https://www.deeplearning.ai/short-courses/ai-agentic-design-patterns-with-autogen/
- Harrison Chase "Building LLM Agents"：https://www.youtube.com/watch?v=DWUdGhRrv2c
- 记录四种 Agentic 设计模式：Reflection、Tool Use、Planning、Multi-Agent

**周末（4-6h）：LangChain Agent 源码阅读**
- 克隆 LangChain 仓库：https://github.com/langchain-ai/langchain
- 阅读 `langchain/agents/` 目录，重点：
  - `agent.py`：AgentExecutor 的核心循环逻辑
  - `output_parsers/`：如何从 LLM 输出中解析 Action
  - `tools/`：工具的注册与执行机制
- 画出 AgentExecutor 的完整执行流程图
- 写笔记：ReAct 论文 → Function Calling 协议 → LangChain 实现，三者的映射关系

### ✅ 验收
- [ ] 能不看代码手写一个 ReAct Agent（while 循环 + prompt 构造 + 工具调度）
- [ ] 能解释 ReAct 和 CoT 的区别、Toolformer 和 Function Calling 的区别
- [ ] 能说出 OpenAI 和 Anthropic 工具调用协议的 3 个关键差异

---

## 第 2 周：规划与推理进阶

### 本周目标
理解 Agent 的高级推理模式（ToT、Reflexion），掌握规划与自我纠错的核心机制。

### 每日任务

**周一（2h）：精读 Tree of Thoughts 论文**
- 论文：https://arxiv.org/abs/2305.10601
- 重点理解：为什么线性 CoT 不够？ToT 的 BFS/DFS 搜索策略
- 对比 CoT → CoT-SC (Self-Consistency) → ToT 的演进
- 思考：ToT 在实际 Agent 中的应用场景（什么任务值得用树搜索？）

**周二（2h）：精读 Reflexion 论文**
- 论文：https://arxiv.org/abs/2303.11366
- 重点：linguistic reinforcement 怎么工作——用自然语言做"奖励信号"
- 理解 Reflexion 的三个组件：Actor、Evaluator、Self-Reflection
- 和 RLHF 对比：Reflexion 是推理时的"强化学习"，RLHF 是训练时的

**周三（2h）：实现 Reflexion 机制**
- 在周一手写的 ReAct Agent 基础上加入 Reflexion：
  - Agent 执行完任务后，让 LLM 评估结果是否正确
  - 如果错误，生成反思文本，加入上下文重新尝试
  - 实现最多 3 次反思重试
- 在 HotpotQA（https://huggingface.co/datasets/hotpot_qa）上测试几个多跳问题

**周四（2h）：阅读 Agent 综述论文**
- 论文：https://arxiv.org/abs/2308.11432
- 这是一篇 60+ 页的综述，不需要全读，重点：
  - Section 3: Agent 架构（Profile、Memory、Planning、Action）
  - Section 4: Agent 的应用领域分类
  - 用这篇论文建立 Agent 领域的知识地图，标记后续要深入的方向

**周五（2h）：观看 Andrew Ng LangGraph 课程**
- 课程：https://www.deeplearning.ai/short-courses/ai-agents-in-langgraph/
- 重点关注：状态图（State Graph）的设计思想，和 ReAct 循环的关系
- 理解 LangGraph 相比 LangChain Agent 的核心改进：从链式调用到图状态机

**周末（4-6h）：记忆系统设计 + 阶段总结**
- 研究 Agent 记忆系统的三种模式：
  - 短期记忆：对话上下文窗口
  - 长期记忆：向量数据库存储 + 检索（RAG 模式）
  - 工作记忆：scratchpad / 中间推理状态
- 阅读 MemGPT 论文：https://arxiv.org/abs/2310.08560，理解如何让 Agent 管理自己的记忆
- 写阶段一总结笔记：Agent 核心范式地图（ReAct / CoT / ToT / Reflexion / Memory）

### ✅ 验收
- [ ] 能解释 CoT → CoT-SC → ToT → Reflexion 的演进逻辑
- [ ] 手写的 Agent 已支持 Reflexion 自我纠错
- [ ] 能画出 Agent 架构四要素（Profile/Memory/Planning/Action）的关系图

---

# ═══════════════════════════════════════════
# 阶段二：框架深入 + 手写 Agent Runtime（第 3-4 周）
# ═══════════════════════════════════════════

> 目标：拆解主流 Agent 框架源码，自己从零造一个 mini-Agent runtime

## 📚 阶段二必读论文清单

| # | 论文 | 链接 | 安排周次 | 为什么要读 |
|---|------|------|----------|-----------|
| 1 | Voyager: An Open-Ended Embodied Agent with LLMs, Wang et al. 2023 | https://arxiv.org/abs/2305.16291 | 第 3 周 | 代码生成 + 技能库 + 自动课程，Agent 系统设计的典范 |
| 2 | Generative Agents: Interactive Simulacra of Human Behavior, Park et al. 2023 | https://arxiv.org/abs/2304.03442 | 第 3 周 | 经典多 Agent 仿真架构，记忆/反思/规划的完整实现 |
| 3 | TaskWeaver: A Code-First Agent Framework, Qiao et al. 2023 | https://arxiv.org/abs/2311.17541 | 第 4 周 | 微软的代码优先 Agent 框架，理解代码执行 Agent 的设计 |

## 🎬 阶段二推荐课程/讲座

| 资源 | 链接 | 时长 | 何时看 |
|------|------|------|--------|
| Andrew Ng "Functions, Tools and Agents with LangChain" | https://www.deeplearning.ai/short-courses/functions-tools-agents-langchain/ | ~1h | 第 3 周 |
| LangGraph 官方文档 - Conceptual Guide | https://langchain-ai.github.io/langgraph/concepts/ | ~2h 阅读 | 第 3 周 |

## 🔧 阶段二核心代码资源

| 资源 | 链接 | 用途 |
|------|------|------|
| LangGraph 源码 | https://github.com/langchain-ai/langgraph | 阅读状态图引擎核心实现 |
| Claude Agent SDK | https://github.com/anthropics/claude-code/tree/main/packages/claude-agent-sdk | Anthropic 官方 Agent SDK |
| Voyager 源码 | https://github.com/MineDojo/Voyager | 阅读技能库 + 自动课程的实现 |
| AutoGen | https://github.com/microsoft/autogen | 多 Agent 对话框架参考 |

## 🗂️ 阶段二核心数据集

| 数据集 | 链接 | 用途 |
|--------|------|------|
| GAIA Benchmark | https://huggingface.co/datasets/gaia-benchmark/GAIA | Agent 综合能力评测基准 |
| SWE-bench | https://github.com/princeton-nlp/SWE-bench | 代码 Agent 评测（GitHub issue 自动修复） |

---

## 第 3 周：拆解框架源码

### 本周目标
深入阅读 LangGraph 源码，理解图状态机的设计；阅读 Voyager/Generative Agents 论文，学习工业级 Agent 的架构设计。

### 每日任务

**周一（2h）：LangGraph 源码阅读 — 状态图核心**
- 克隆 LangGraph：https://github.com/langchain-ai/langgraph
- 阅读核心文件：
  - `langgraph/graph/state.py`：StateGraph 的定义与编译
  - `langgraph/pregel/`：图执行引擎（基于 Pregel 模型）
- 理解：Node、Edge、ConditionalEdge 如何组成一个可执行的状态机

**周二（2h）：LangGraph 源码阅读 — checkpoint 与 memory**
- 阅读 `langgraph/checkpoint/`：状态持久化机制
- 理解 human-in-the-loop 的实现：interrupt → 等待用户输入 → 恢复执行
- 阅读 LangGraph 概念文档：https://langchain-ai.github.io/langgraph/concepts/
- 动手：用 LangGraph 搭一个带 checkpoint 的多步 Agent

**周三（2h）：精读 Voyager 论文**
- 论文：https://arxiv.org/abs/2305.16291
- 重点关注三个核心模块：
  - Automatic Curriculum：Agent 如何自动设定下一个目标
  - Skill Library：代码形式的可复用技能如何存储和检索
  - Iterative Prompting：执行失败后如何修正代码
- 浏览 Voyager 源码（https://github.com/MineDojo/Voyager），找到这三个模块的实现

**周四（2h）：精读 Generative Agents 论文**
- 论文：https://arxiv.org/abs/2304.03442
- 重点：记忆流（Memory Stream）+ 反思（Reflection）+ 规划（Planning）
- 理解 recency × importance × relevance 的记忆检索公式
- 思考：这个架构如何推广到非仿真场景？

**周五（2h）：观看课程 + Claude Agent SDK**
- Andrew Ng "Functions, Tools and Agents with LangChain"：https://www.deeplearning.ai/short-courses/functions-tools-agents-langchain/
- 阅读 Claude Agent SDK 文档与示例：https://github.com/anthropics/claude-code/tree/main/packages/claude-agent-sdk
- 对比 LangGraph vs AutoGen vs Claude Agent SDK 的设计理念

**周末（4-6h）：对比分析 + 设计自己的 Agent Runtime**
- 做一张框架对比表：

| 维度 | LangGraph | AutoGen | Claude Agent SDK |
|------|-----------|---------|-----------------|
| 核心抽象 | 状态图 | 多 Agent 对话 | ? |
| 工具注册 | ? | ? | ? |
| 记忆机制 | ? | ? | ? |
| 适用场景 | ? | ? | ? |

- 设计你自己的 mini-Agent runtime 架构（下周实现）：
  - 画架构图：Tool Registry、LLM Interface、Memory、Planner、Executor
  - 定义核心接口和数据结构

### ✅ 验收
- [ ] 能画出 LangGraph 的执行引擎流程图（StateGraph → Compile → Pregel 执行）
- [ ] 能解释 Voyager 的三个核心模块及其交互方式
- [ ] 完成 mini-Agent runtime 的架构设计文档

---

## 第 4 周：手写 Mini-Agent Runtime

### 本周目标
从零实现一个 mini-Agent runtime，包含工具注册/调度、记忆系统、规划引擎、代码执行沙箱。

### 每日任务

**周一（2h）：实现核心框架骨架**
- 创建项目结构：
  ```
  mini_agent/
  ├── core.py          # Agent 主循环
  ├── tools.py         # 工具注册与调度
  ├── llm.py           # LLM 接口（支持 OpenAI/Claude）
  ├── memory.py        # 记忆系统
  ├── planner.py       # 规划引擎
  └── sandbox.py       # 代码执行沙箱
  ```
- 实现 `tools.py`：用装饰器注册工具，自动生成 JSON Schema
- 实现 `llm.py`：统一的 LLM 调用接口，支持 tool_call 解析

**周二（2h）：实现 Agent 主循环 + 工具调度**
- 实现 `core.py`：
  - ReAct 循环：思考 → 调用工具 → 观察 → 继续
  - 支持 parallel tool calling
  - 最大轮数限制、超时控制、错误恢复
- 用 3-5 个内置工具测试（搜索、计算器、文件读写、HTTP 请求）

**周三（2h）：实现记忆系统**
- 实现 `memory.py`：
  - 短期记忆：对话历史管理（滑动窗口 + 摘要压缩）
  - 长期记忆：用 chromadb 或 FAISS 实现向量存储
  - 记忆检索：相关性 + 时间衰减的混合评分
- 测试：让 Agent 在多轮对话中记住和检索之前的信息

**周四（2h）：实现代码执行沙箱**
- 实现 `sandbox.py`：
  - 用 subprocess 或 Docker 隔离执行 Python 代码
  - 超时控制、输出捕获、错误处理
  - 安全限制：禁止危险操作（文件删除、网络写入等）
- 阅读 TaskWeaver 论文：https://arxiv.org/abs/2311.17541，对比你的实现
- 让 Agent 具备"写代码并执行"的能力

**周五（2h）：实现规划引擎**
- 实现 `planner.py`：
  - Plan-and-Execute 模式：先生成计划，再逐步执行
  - 支持计划的动态调整（某步失败后重新规划）
  - 参考 LangGraph 的 Plan-and-Execute 模板
- 整合所有模块，跑通一个端到端的任务（如"搜索某个 topic，整理成报告"）

**周末（4-6h）：完善 + 评测 + 写文档**
- 在 GAIA benchmark（https://huggingface.co/datasets/gaia-benchmark/GAIA）上测试几道题
- 添加 streaming 输出、日志记录
- 写 README 文档：架构设计、使用示例、与主流框架的对比
- 写阶段二总结笔记：框架本质就是 while 循环 + 状态管理 + 工具调度

### ✅ 验收
- [ ] mini-Agent runtime 能完成一个多步骤任务（搜索 → 分析 → 生成报告）
- [ ] 支持工具注册（装饰器）、记忆（短期+长期）、代码执行（沙箱）
- [ ] 在 GAIA 上至少跑通 3 道 Level 1 题目

---

# ═══════════════════════════════════════════
# 阶段三：Agent 模型训练（第 5-6 周）
# ═══════════════════════════════════════════

> 目标：训练模型具备工具调用和 Agent 能力——从数据构造到 SFT 到强化学习

## 📚 阶段三必读论文清单

| # | 论文 | 链接 | 安排周次 | 为什么要读 |
|---|------|------|----------|-----------|
| 1 | Gorilla: Large Language Model Connected with Massive APIs, Patil et al. 2023 | https://arxiv.org/abs/2305.15334 | 第 5 周 | API 调用微调的代表性工作，理解如何训练模型调用工具 |
| 2 | ToolLLM: Facilitating LLMs to Master 16000+ Real-world APIs, Qin et al. 2023 | https://arxiv.org/abs/2307.16789 | 第 5 周 | 大规模 tool-use 数据构造（DFSDT）+ 训练流程 |
| 3 | Agent-FLAN: Designing Data and Methods of Effective Agent Tuning, Chen et al. 2024 | https://arxiv.org/abs/2403.12881 | 第 5 周 | Agent 能力微调的系统性研究，数据配比与负采样策略 |
| 4 | FireAct: Toward Language Agent Fine-tuning, Chen et al. 2023 | https://arxiv.org/abs/2310.05915 | 第 6 周 | 用多种 Agent 轨迹微调，CoT/ReAct/Reflexion 轨迹数据的对比 |
| 5 | Qwen-Agent Technical Report | https://arxiv.org/abs/2309.16609 | 第 6 周 | Qwen 的 Agent 能力训练方案，和你使用的模型直接相关 |

## 🎬 阶段三推荐课程/讲座

| 资源 | 链接 | 时长 | 何时看 |
|------|------|------|--------|
| Andrew Ng "Building and Evaluating Advanced RAG" | https://www.deeplearning.ai/short-courses/building-evaluating-advanced-rag/ | ~1h | 第 5 周 |
| HuggingFace "Training LLMs to use tools" blog | https://huggingface.co/blog/unified-tool-use | ~30min | 第 5 周 |

## 🔧 阶段三核心代码资源

| 资源 | 链接 | 用途 |
|------|------|------|
| LLaMA-Factory | https://github.com/hiyouga/LLaMA-Factory | tool-use SFT 训练 |
| trl (HuggingFace) | https://github.com/huggingface/trl | GRPO/DPO 强化训练 |
| ToolBench 代码 | https://github.com/OpenBMB/ToolBench | 参考 tool-use 数据构造流程 |
| Qwen-Agent | https://github.com/QwenLM/Qwen-Agent | Qwen 官方 Agent 框架与训练数据格式 |

## 🗂️ 阶段三核心数据集

| 数据集 | 链接 | 用途 |
|--------|------|------|
| ToolBench | https://huggingface.co/datasets/sambanovasystems/ToolBench | 16000+ API 的 tool-use 训练数据 |
| ToolAlpaca | https://github.com/tangqiaoyu/ToolAlpaca | 多工具交互的指令数据 |
| API-Bank | https://github.com/AlibabaResearch/DAMO-ConvAI/tree/main/api-bank | API 调用评测基准 |
| Agent-FLAN 数据 | https://huggingface.co/datasets/internlm/Agent-FLAN | Agent 微调数据集（含负采样） |
| glaive-function-calling-v2 | https://huggingface.co/datasets/glaiveai/glaive-function-calling-v2 | Function Calling 格式训练数据 |

---

## 第 5 周：Tool-Use 训练数据构造与 SFT

### 本周目标
掌握 tool-use 训练数据的构造方法，完成一个 tool-use 模型的 SFT 微调。

### 每日任务

**周一（2h）：精读 Gorilla + ToolLLM 论文**
- Gorilla：https://arxiv.org/abs/2305.15334
  - 重点：如何把 API 文档转化为训练数据，retrieval-augmented training
- ToolLLM：https://arxiv.org/abs/2307.16789
  - 重点 Section 3：DFSDT（Depth-First Search-based Decision Tree）数据构造方法
  - 理解：为什么用树搜索生成的轨迹比直接生成的质量更高

**周二（2h）：精读 Agent-FLAN 论文 + 数据格式分析**
- Agent-FLAN：https://arxiv.org/abs/2403.12881
  - 重点：数据配比实验（tool-use : general = ?），负采样的作用
- 下载并分析现有数据集的格式：
  - glaive-function-calling-v2：https://huggingface.co/datasets/glaiveai/glaive-function-calling-v2
  - Agent-FLAN 数据：https://huggingface.co/datasets/internlm/Agent-FLAN
- 搞清楚：function calling 数据的标准格式（system prompt 定义工具 → user 提问 → assistant 调用工具 → tool 返回结果 → assistant 回答）

**周三（2h）：构造自己的 tool-use 训练数据**
- 设计 5-10 个工具（天气、搜索、计算、翻译、日历等）
- 用 GPT-4/Claude 生成训练数据：
  - 构造 system prompt：定义工具的 name、description、parameters
  - 生成多轮 tool-use 对话（含单工具、多工具、并行调用场景）
  - 生成拒绝调用的负例（用户问题不需要工具时，模型不应调用）
- 格式转换为 LLaMA-Factory 的 ShareGPT 格式
- 目标：生成 500-1000 条高质量训练数据

**周四（2h）：用 LLaMA-Factory 进行 tool-use SFT**
- 使用 Qwen2.5-1.5B 或 Qwen2.5-7B 作为基座模型
- 配置 LLaMA-Factory（https://github.com/hiyouga/LLaMA-Factory）：
  - 数据集配置：注册自己的 tool-use 数据集
  - 训练参数：LoRA rank=16, lr=2e-4, epochs=3
  - 参考你在后训练计划中的 SFT 经验调整参数
- 开始训练，观察 loss 曲线

**周五（2h）：训练完成 + 初步评测**
- 训练完成后做基础评测：
  - 给模型不同的工具定义和用户问题，看是否正确调用工具
  - 测试边界情况：不需要工具时是否会误调用？参数是否正确？
  - 和基座模型对比 tool-use 能力的提升
- 阅读 HuggingFace blog：https://huggingface.co/blog/unified-tool-use

**周末（4-6h）：扩大数据规模 + 迭代训练**
- 基于评测中发现的问题，针对性补充训练数据：
  - 模型容易犯的错误类型 → 构造更多对应样本
  - 增加多工具协作、嵌套调用的复杂场景
- 用扩充后的数据重新训练
- 对比两次训练的效果差异，写训练日志

### ✅ 验收
- [ ] 构造了 500+ 条 tool-use 训练数据，格式正确
- [ ] 完成 LoRA SFT 训练，loss 正常收敛
- [ ] 微调后的模型在简单 tool-use 场景下准确率 > 80%

---

## 第 6 周：Agent 轨迹训练 + GRPO 强化

### 本周目标
用 ReAct/Reflexion 格式的 Agent 轨迹做 SFT，再用 GRPO 进一步强化 Agent 行为。

### 每日任务

**周一（2h）：精读 FireAct 论文**
- 论文：https://arxiv.org/abs/2310.05915
- 重点：不同类型轨迹（CoT / ReAct / Reflexion）训练出的 Agent 有什么差异
- 理解：为什么 Agent 轨迹数据的质量比数量更重要
- 阅读 Qwen-Agent 技术报告：https://arxiv.org/abs/2309.16609

**周二（2h）：构造 Agent 轨迹训练数据**
- 用你的 mini-Agent runtime（第 4 周成果）生成 ReAct 轨迹数据：
  - 设计 20-30 个任务（信息检索、数据分析、代码生成等）
  - 运行 Agent，记录完整的 Thought-Action-Observation 轨迹
  - 人工筛选：只保留成功的、步骤高效的轨迹
- 将轨迹转换为训练格式：每个 Thought/Action/Observation 作为对话轮次

**周三（2h）：Agent 轨迹 SFT 训练**
- 将 tool-use 数据（第 5 周）和 Agent 轨迹数据混合：
  - 建议比例：tool-use 60% + Agent 轨迹 30% + 通用指令 10%
  - 通用指令数据防止"灾难性遗忘"
- 用 LLaMA-Factory 训练，参数与上周类似
- 训练完成后测试：模型是否能自主进行多步推理和工具调用？

**周四（2h）：设计 GRPO 奖励函数**
- 为 Agent 行为设计奖励信号：
  - 任务完成度：最终答案是否正确（+1 / 0）
  - 效率奖励：步骤越少越好（-0.1 per step）
  - 格式奖励：是否遵循 ReAct 格式（+0.2）
  - 工具使用正确性：调用参数是否正确（+0.3）
- 参考后训练计划中的 GRPO 实现，调整为 Agent 场景

**周五（2h）：GRPO 训练**
- 使用 trl（https://github.com/huggingface/trl）进行 GRPO 训练：
  - 基座：上面 SFT 后的模型
  - 采样：对同一个任务生成多条轨迹
  - 奖励：用设计好的奖励函数打分
  - 优化：GRPO 更新策略
- 观察训练过程中奖励的变化趋势

**周末（4-6h）：评测 + 对比实验 + 总结**
- 系统评测四个版本的模型：
  1. 基座模型（无微调）
  2. Tool-use SFT（第 5 周）
  3. + Agent 轨迹 SFT（本周三）
  4. + GRPO 强化（本周五）
- 在以下维度对比：tool-use 准确率、多步任务完成率、平均步骤数
- 写阶段三总结：Agent 训练 pipeline（数据构造 → SFT → GRPO）

### ✅ 验收
- [ ] 构造了 Agent 轨迹训练数据，格式为 ReAct 多轮对话
- [ ] 完成 GRPO 训练，奖励曲线有明显上升趋势
- [ ] 四个版本模型的对比实验结果清晰，能解释每步训练带来的增益

---

# ═══════════════════════════════════════════
# 阶段四：多 Agent 系统 + 评测 + 端到端项目（第 7-8 周）
# ═══════════════════════════════════════════

> 目标：设计多 Agent 协作系统，构建评测体系，完成一个端到端 Agent 项目

## 📚 阶段四必读论文清单

| # | 论文 | 链接 | 安排周次 | 为什么要读 |
|---|------|------|----------|-----------|
| 1 | AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation, Wu et al. 2023 | https://arxiv.org/abs/2308.08155 | 第 7 周 | 多 Agent 对话框架的代表作 |
| 2 | CAMEL: Communicative Agents for "Mind" Exploration of Large Language Model Society, Li et al. 2023 | https://arxiv.org/abs/2303.17760 | 第 7 周 | 角色扮演式多 Agent 协作 |
| 3 | AgentBench: Evaluating LLMs as Agents, Liu et al. 2023 | https://arxiv.org/abs/2308.03688 | 第 7 周 | Agent 评测基准设计 |
| 4 | The Landscape of Emerging AI Agent Architectures, Masterman et al. 2024 | https://arxiv.org/abs/2404.11584 | 第 8 周 | Agent 架构最新综述 |

## 🎬 阶段四推荐课程/讲座

| 资源 | 链接 | 时长 | 何时看 |
|------|------|------|--------|
| Andrew Ng "Multi AI Agent Systems with crewAI" | https://www.deeplearning.ai/short-courses/multi-ai-agent-systems-with-crewai/ | ~1h | 第 7 周 |
| Andrew Ng "Building Agentic RAG with LlamaIndex" | https://www.deeplearning.ai/short-courses/building-agentic-rag-with-llamaindex/ | ~1h | 第 8 周 |

## 🔧 阶段四核心代码资源

| 资源 | 链接 | 用途 |
|------|------|------|
| CrewAI | https://github.com/crewAIInc/crewAI | 多 Agent 协作框架 |
| AutoGen | https://github.com/microsoft/autogen | 多 Agent 对话框架 |
| AgentBench | https://github.com/THUDM/AgentBench | Agent 评测工具 |
| SWE-bench | https://github.com/princeton-nlp/SWE-bench | 代码 Agent 评测 |

## 🗂️ 阶段四核心数据集

| 数据集 | 链接 | 用途 |
|--------|------|------|
| AgentBench 数据 | https://github.com/THUDM/AgentBench | 8 种环境的 Agent 评测数据 |
| GAIA | https://huggingface.co/datasets/gaia-benchmark/GAIA | 通用 Agent 能力评测 |
| WebArena | https://github.com/web-arena-x/webarena | Web Agent 评测环境 |

---

## 第 7 周：多 Agent 协作 + 评测体系

### 本周目标
理解多 Agent 协作的核心模式，设计一套 Agent 评测体系。

### 每日任务

**周一（2h）：精读 AutoGen + CAMEL 论文**
- AutoGen：https://arxiv.org/abs/2308.08155
  - 重点：conversable agent 的设计，Agent 间如何通过对话协作
  - 理解：GroupChat 模式中的 speaker selection 策略
- CAMEL：https://arxiv.org/abs/2303.17760
  - 重点：inception prompting，用角色扮演驱动 Agent 协作
- 对比两种多 Agent 架构的优劣

**周二（2h）：多 Agent 协作模式实战**
- 观看 Andrew Ng "Multi AI Agent Systems with crewAI"：https://www.deeplearning.ai/short-courses/multi-ai-agent-systems-with-crewai/
- 用 CrewAI（https://github.com/crewAIInc/crewAI）实现一个多 Agent 系统：
  - 研究员 Agent：负责搜索和收集信息
  - 分析师 Agent：负责整理和分析数据
  - 写作者 Agent：负责生成最终报告
- 测试不同的协作模式：sequential vs hierarchical

**周三（2h）：精读 AgentBench + 设计评测框架**
- AgentBench：https://arxiv.org/abs/2308.03688
  - 理解 8 种评测环境的设计（OS、DB、Web、Code 等）
  - 重点：评测指标的选择——成功率、步骤效率、错误恢复能力
- 设计你自己的 Agent 评测体系：
  - 维度：工具调用准确率、任务完成率、平均步骤数、鲁棒性
  - 数据：从 GAIA、AgentBench 中选取子集
  - 自动评测脚本：输入任务 → Agent 执行 → 评分

**周四（2h）：实现评测框架**
- 编写 Agent 评测脚本：
  - 支持批量评测：读取任务列表 → 逐个执行 → 记录结果
  - 自动评分：字符串匹配 / LLM-as-Judge
  - 输出评测报告：准确率、耗时、token 消耗
- 在 GAIA Level 1（https://huggingface.co/datasets/gaia-benchmark/GAIA）上评测你的模型

**周五（2h）：在自己训练的模型上跑评测**
- 用评测框架对比：
  - 基座模型
  - 你的 SFT + GRPO 模型（第 5-6 周成果）
  - GPT-4 / Claude（作为 baseline）
- 分析差距在哪里：是工具调用能力？还是规划能力？还是格式遵循？

**周末（4-6h）：手写多 Agent 协作框架**
- 在你的 mini-Agent runtime 基础上扩展多 Agent 支持：
  - Agent 注册与角色分配
  - 消息路由：Agent 之间如何传递信息
  - 协调策略：round-robin / manager-worker / debate
- 用多 Agent 系统完成一个实际任务（如技术调研报告生成）

### ✅ 验收
- [ ] 用 CrewAI 搭建了一个 3-Agent 协作系统
- [ ] 实现了 Agent 评测框架，能自动评分和生成报告
- [ ] 评测结果清晰展示了自训练模型与基座/GPT-4 的差距

---

## 第 8 周：端到端项目 + 总结

### 本周目标
完成一个可展示的端到端 Agent 项目，整理 8 周学习成果。

### 每日任务

**周一（2h）：确定项目方向 + 架构设计**
- 从以下方向选一个（或自定义）：
  - **代码 Agent**：给定 GitHub issue，自动分析代码 → 生成修复方案 → 写代码 → 跑测试
  - **数据分析 Agent**：接收数据集和分析需求 → 写 SQL/Python → 执行 → 可视化 → 生成报告
  - **知识助手 Agent**：接入知识库 → RAG 检索 → 多步推理 → 结构化回答
- 画系统架构图，定义 Agent 角色、工具集、交互流程

**周二（2h）：实现核心功能**
- 搭建项目骨架
- 实现核心 Agent 逻辑和工具集
- 接入你训练的 tool-use 模型（第 5-6 周成果）作为 Agent 的"大脑"

**周三（2h）：实现辅助功能**
- 添加记忆系统（对话历史 + 向量检索）
- 添加错误恢复和重试机制
- 添加执行日志和可观测性（每步推理过程可追踪）

**周四（2h）：测试 + 修 bug**
- 用 5-10 个真实场景测试 Agent 系统
- 修复发现的问题：格式解析错误、工具调用失败、死循环等
- 用评测框架（第 7 周）跑一轮系统评测

**周五（2h）：优化 + 对比实验**
- 对比自训练模型 vs API 模型（GPT-4/Claude）驱动的效果差异
- 尝试优化：
  - prompt 工程优化
  - 工具描述优化
  - 增加 few-shot examples
- 记录最终评测结果

**周末（4-6h）：整理成果 + 写总结**
- 整理项目代码，写 README
- 写 8 周学习总结笔记：
  - Agent 核心原理：ReAct / CoT / ToT / Reflexion
  - 框架本质：while 循环 + 状态管理 + 工具调度
  - Agent 训练 pipeline：数据构造 → SFT → GRPO
  - 多 Agent 协作：对话式 / 分层式 / 辩论式
  - 评测体系：GAIA / AgentBench / 自建评测
- 整理一份 Agent 技术栈速查表，方便面试复习

### ✅ 验收
- [ ] 端到端 Agent 项目可运行，能完成 3+ 个真实场景的任务
- [ ] 有完整的评测数据（自训练模型 vs API 模型）
- [ ] 8 周学习笔记和技术总结完成
- [ ] 能在 30 分钟内向面试官讲清楚：Agent 原理 → 框架设计 → 模型训练 → 评测体系
