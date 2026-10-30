# Agent 工程师学习路线图

> 从会调 LLM API，到能把一个 Agent 送上线并守住它。
>
> [English](./README.en.md) · [在线课程](https://zhurilab.com/learn)

这份路线图回答一个具体问题：**一个已经会写代码的人，按什么顺序学，才能成为能独立交付 Agent 系统的工程师？**

它不是链接收藏夹。每个阶段都写清楚三件事：这一阶段要解决什么问题、学完的判据是什么、以及跳过它会在哪里翻车。

---

## 先选一条适合自己的路线

| 你现在想做什么 | 从哪里开始 |
|---|---|
| 刚开始用 AI，想练习提问、整理资料和日常办公 | [AI 入门学习路线与练习资源](./beginners/README.md)：14 次短练习，附虚构素材、提示词和核查表，无需编程 |
| 已经会写代码，想开发和交付 Agent | 继续阅读下面的六阶段工程路线 |
| 想按具体问题选课 | [逐日AI课程目录](https://zhurilab.com/learn) |

入门练习可以直接在仓库里完成。对应课程放在每个练习后面，便于需要时展开学习。

## 为什么需要一份顺序

Agent 相关的资料现在不缺，缺的是顺序。常见的两种走法都会卡住：

- **从框架入手**——先学 LangChain 或某个 Agent SDK，能跑通 demo，但线上一出问题就不知道该看哪里。因为框架把 Agent Loop、上下文组装、工具调用协议全包起来了，而出问题的恰恰是这三层。
- **从论文入手**——读了一堆 ReAct、Reflexion、Plan-and-Execute，但落不了地。因为论文不讲流式怎么断线重连、工具调用的 `index` 分片怎么归并、上下文超了该丢哪一段。

下面的顺序按**依赖关系**排，不按热度排。每一阶段的产出都是下一阶段的输入。

---

## 阶段 0 · 先把工具用顺手（零基础，1–2 周）

在写任何 Agent 代码之前，先解决两件事：**能把需求说清楚**，以及**能用 AI 编程工具干活**。这两项省下来的时间，比后面任何一个技巧都多。

| 要解决的问题 | 判据 |
|---|---|
| 把模糊需求写成模型能稳定执行的指令 | 你能用一个小样本测试集验证提示词，而不是「感觉这次答得不错」 |
| 把 AI 编程工具用进真实工作流 | 你能让它从探索、计划、实现、验证一路跑到提交，而不是只会让它补全函数 |

**这一阶段的课程**：

- [5 天提示词工程零基础](https://zhurilab.com/learn/prompt-engineering-5days) — 5 天
- [Claude 高效使用：从对话到 Claude Code](https://zhurilab.com/learn/claude-mastery) — 5 天
- [Codex 与 OpenAI Agents SDK 高效使用](https://zhurilab.com/learn/codex-mastery) — 5 天（最后一天用同一个任务实测对比 Codex 与 Claude Code）

---

## 阶段 1 · Agent 的内核（4 周）

这一阶段只有一个目标：**不用任何框架，手写出一个能跑的 Agent Loop**。

为什么坚持手写？因为 Agent 的全部复杂度都在三个地方，而框架恰好把这三个地方藏起来了：

1. **流式**——首字延迟在不同模型之间差一个数量级，流式是唯一解法；而 SSE 断线重连要在传输层、协议层、业务层各做一次。
2. **工具调用**——`tool_calls` 的 `index` 起点不可假设（实测同一个模型同一天两次运行分别给出 `0,1` 与 `1,2`），归并分片必须按 index 建字典。
3. **循环终止**——什么时候该继续调工具，什么时候该收尾，什么时候是模型已经绕进死循环。

学完的判据：**你能说清楚一次带工具调用的对话，从用户回车到屏幕出字，中间经过了哪些状态。**

- [30 天从前端工程师到 Agent 工程师](https://zhurilab.com/learn/agent-30days) — 30 天

---

## 阶段 2 · 给 Agent 装上能力（3–4 周）

内核跑通之后，问题从「怎么让它动起来」变成「怎么让它够得着东西、记得住事」。三个方向互相正交，可以按需挑：

### 接工具 —— MCP

MCP 解决的是「一个工具写一次，任何 Agent 都能用」。注意**规范以 2026-07-28 为准**，网上大量教程还停在 2025-11-25 那版，差异很大（无状态、按请求协商能力、服务端不再发起 JSON-RPC 请求、roots 已弃用、传输只剩 stdio 与 Streamable HTTP）。

- [7 天 MCP：把工具接进任何 Agent](https://zhurilab.com/learn/mcp-7days) — 7 天

### 复用经验 —— Agent Skills

把你已经做熟的事写成 `SKILL.md`，让客户端在该用的时候自己翻开它。和 MCP 的分工是：**MCP 管接线，Skills 管经验。**

- [7 天 Agent Skills：把经验做成可复用能力](https://zhurilab.com/learn/agent-skills-7days) — 7 天

### 管住上下文 —— 上下文工程

把上下文窗口当成一件容量固定的行李。这是最容易被低估的一层：多数「模型变笨了」的线上问题，根因在上下文组装，不在模型。

- [5 天上下文工程](https://zhurilab.com/learn/context-engineering-5days) — 5 天

---

## 阶段 3 · 让 Agent 知道你的数据（2 周）

RAG 的坑不在「跑通一个 demo」，在于**怎么证明它是对的**。建议的顺序是反直觉的：**先把关键词检索做扎实，再上向量**——直接从向量库入手的人，往往连「检索到底召回了没有」都说不清。

学完的判据：你能报出 recall@k、MRR、nDCG 三个数，并解释为什么这次改动让其中一个升了另一个降了。

- [14 天 RAG：从检索到可信回答](https://zhurilab.com/learn/rag-14days) — 14 天

---

## 阶段 4 · 工程化：从「能跑」到「敢上线」（3 周）

这一阶段区分「做过 Agent」和「能交付 Agent」。三件事缺一不可：

### 评估 —— 把「感觉还行」变成数字

Agent 是非确定性的，所以单次运行的成功不构成证据。要把非确定性变成 **pass 的 k 次方**，把线上翻的车变成基准集，还要把模型裁判本身考一遍（裁判也会错，而且错得有系统性偏向）。

- [7 天 Agent 评估与可观测](https://zhurilab.com/learn/agent-evals-7days) — 7 天

### 安全 —— 检测器不是边界，架构才是

判断一个 Agent 危不危险，看它有没有凑齐**致命三件套**：接触私有数据、能处理不可信内容、能对外通信。三者同时具备，提示注入就能把数据送出去。

这里有个必须成对出现的数字：**ASR（攻击成功率）与 utility（正常任务完成率）**。只报 ASR 的防御方案一律不可信——把 Agent 改成什么都不做，ASR 就是 0。

- [5 天 Agent 安全](https://zhurilab.com/learn/agent-security-5days) — 5 天

### 前端 —— 一条事件流怎么变成不抖动的界面

如果你的 Agent 有用户界面，这一层的工作量常被低估：流式增量的渲染节奏、人在回路的审批、可回溯的运行历史、屏幕阅读器的朗读顺序。

协议建议对齐 **AG-UI**（MIT）而不是自造。注意 `THINKING_*` 系列已弃用，一律用 `REASONING_*`——网上大量教程还在写旧名字。

- [7 天前端 Agent 体验：从流式到生成式界面](https://zhurilab.com/learn/frontend-agent-ux-7days) — 7 天

---

## 阶段 5 · 综合项目（3–4 周）

前面都是零件，这一阶段是整机。两个方向选一个做完，比把所有课都看一遍有用得多。

### 做一个终端 Coding Agent

从一个空壳 REPL 开始，一天加一个能力，最后得到能在真实仓库里读代码、改文件、跑测试、并行干活、可安装可分发的工具。**全程零框架、零运行时依赖**——斜杠命令、SSE 解析、工具循环、MCP 协议全部手写。

- [21 天手搓一个 Coding Agent](https://zhurilab.com/learn/my-coding-agent-21days) — 21 天

### 做一条多模态生产线

把一句话变成一集竖屏短剧，再把它扩成能并行产出、可审核、可控成本的平台。这个方向的重点是**编排与成本**：DAG、重试、预算护栏、人工审核卡点。

- [14 天用 Agent 搭一条 AI 短剧生产线](https://zhurilab.com/learn/ai-drama-pipeline) — 14 天

---

## 工程路线课程表

| # | 课程 | 天数 |
|---|---|---|
| 1 | [30 天从前端工程师到 Agent 工程师](https://zhurilab.com/learn/agent-30days) | 30 |
| 2 | [5 天提示词工程零基础](https://zhurilab.com/learn/prompt-engineering-5days) | 5 |
| 3 | [Claude 高效使用：从对话到 Claude Code](https://zhurilab.com/learn/claude-mastery) | 5 |
| 4 | [Codex 与 OpenAI Agents SDK 高效使用](https://zhurilab.com/learn/codex-mastery) | 5 |
| 5 | [7 天 MCP：把工具接进任何 Agent](https://zhurilab.com/learn/mcp-7days) | 7 |
| 6 | [7 天 Agent Skills：把经验做成可复用能力](https://zhurilab.com/learn/agent-skills-7days) | 7 |
| 7 | [5 天上下文工程](https://zhurilab.com/learn/context-engineering-5days) | 5 |
| 8 | [7 天前端 Agent 体验：从流式到生成式界面](https://zhurilab.com/learn/frontend-agent-ux-7days) | 7 |
| 9 | [14 天 RAG：从检索到可信回答](https://zhurilab.com/learn/rag-14days) | 14 |
| 10 | [14 天用 Agent 搭一条 AI 短剧生产线](https://zhurilab.com/learn/ai-drama-pipeline) | 14 |
| 11 | [21 天手搓一个 Coding Agent](https://zhurilab.com/learn/my-coding-agent-21days) | 21 |
| 12 | [7 天 Agent 评估与可观测](https://zhurilab.com/learn/agent-evals-7days) | 7 |
| 13 | [5 天 Agent 安全](https://zhurilab.com/learn/agent-security-5days) | 5 |

本工程路线选取 **13 门课 / 132 天**，全部中英双语。其他入门与日常应用课程见[网站课程目录](https://zhurilab.com/learn)。

配套还有一个[跨课程面试题库](https://zhurilab.com/interviews)，每道题除了答题要点还写了**分析过程**（这题在考什么 → 怎么拆 → 结论 → 可预期的追问），目的是教推导而不是给要点背。

---

## 先看看质量

不想按顺序走的，从这几天开始最快：

- [LLM API 基础：messages/roles、token、流式、temperature；Agent 到底是什么](https://zhurilab.com/learn/agent-30days/day-01)
- [工具调用原理：JSON Schema、tool_use 循环；不用框架手写 Agent Loop](https://zhurilab.com/learn/agent-30days/day-02)
- [提示词是什么、不是什么：模型如何读指令；角色 / 任务 / 格式 / 约束四要素](https://zhurilab.com/learn/prompt-engineering-5days/day-01)
- [为什么需要一个协议：host / client / server 三角、JSON-RPC 消息与三种原语](https://zhurilab.com/learn/mcp-7days/day-01)
- [威胁模型：致命三件套、信任边界，以及两张 OWASP 清单怎么当检查表用](https://zhurilab.com/learn/agent-security-5days/day-01)
- [一个 Coding Agent 由哪几层组成：REPL 骨架、命令系统与不绑厂商的网关抽象](https://zhurilab.com/learn/my-coding-agent-21days/day-01)

---

## 关于

课程站是[逐日AI](https://zhurilab.com)（Dayward AI）。每一天都由三部分组成：原理讲解、一个能跑起来的实验、几道带分析过程的面试题。实验代码全部可离线运行（`MOCK=1`），不需要 API key 也能跑通。

这份路线图以 [CC BY 4.0](./LICENSE) 发布，欢迎转载与改编，注明出处即可。发现内容有误或顺序可以改进，欢迎开 issue。
