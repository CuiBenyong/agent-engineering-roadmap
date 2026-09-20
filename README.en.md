# AI Agent Engineering Roadmap

> From calling an LLM API to shipping an agent and keeping it alive in production.
>
> [中文](./README.md) · [Online courses](https://course.front-end-js.top/en)

This roadmap answers one specific question: **if you already know how to code, in what order should you learn things to become an engineer who can ship agent systems independently?**

It is not a link dump. Each stage states three things: the problem it solves, how you know you're done, and where you'll get burned if you skip it.

---

## Why order matters

There is no shortage of agent material anymore. What's missing is sequence. Two common routes both stall:

- **Starting from a framework.** You learn LangChain or some agent SDK, get a demo running, and then have no idea where to look when production breaks. The framework wraps the agent loop, context assembly, and tool-call protocol — and those three layers are exactly where things break.
- **Starting from papers.** You read ReAct, Reflexion, Plan-and-Execute, and still can't ship. Papers don't cover how to reconnect a dropped stream, how to merge `tool_calls` deltas, or which part of the context to drop when you're over budget.

The order below follows **dependencies**, not hype. Each stage's output is the next stage's input.

---

## Stage 0 · Get fluent with the tools (1–2 weeks, no prerequisites)

Before writing any agent code, solve two things: **stating what you want clearly**, and **actually working with AI coding tools**. The time these save you exceeds any technique that comes later.

| Problem to solve | Done when |
|---|---|
| Turning vague requirements into instructions a model executes consistently | You validate prompts against a small test set, not against "that answer felt good" |
| Putting AI coding tools into a real workflow | You can drive one from exploration through planning, implementation, verification, to commit — not just autocomplete |

**The courses in this stage:**

- [Prompt Engineering From Scratch in 5 Days](https://course.front-end-js.top/en/learn/prompt-engineering-5days) — 5 days
- [Mastering Claude: From Conversation to Claude Code in 5 Days](https://course.front-end-js.top/en/learn/claude-mastery) — 5 days
- [Mastering Codex and the OpenAI Agents SDK in 5 Days](https://course.front-end-js.top/en/learn/codex-mastery) — 5 days (the last day runs the same task through both Codex and Claude Code for a measured comparison)

---

## Stage 1 · The agent core (4 weeks)

One goal: **hand-write a working agent loop with no framework at all.**

Why insist on writing it yourself? Because all of an agent's complexity lives in three places, and frameworks hide exactly those three:

1. **Streaming.** Time-to-first-token differs by an order of magnitude between models, so streaming is the only fix — and SSE reconnection has to be handled separately at the transport, protocol, and application layers.
2. **Tool calls.** The starting value of `index` in `tool_calls` cannot be assumed. Measured: the same model on the same day returned `0,1` on one run and `1,2` on another. Merging deltas requires a dictionary keyed by index, not an array you append to.
3. **Loop termination.** When to call another tool, when to stop, and when the model has simply gone in circles.

You're done when: **you can narrate every state a tool-using turn passes through, from the user pressing enter to text appearing on screen.**

- [From Frontend Engineer to Agent Engineer in 30 Days](https://course.front-end-js.top/en/learn/agent-30days) — 30 days

---

## Stage 2 · Give the agent capabilities (3–4 weeks)

Once the core runs, the question shifts from "how do I make it move" to "how does it reach things and remember things." Three independent directions — pick what you need:

### Connecting tools — MCP

MCP solves "write a tool once, use it from any agent." Note that the **spec to follow is 2026-07-28**; a lot of tutorials online are still on 2025-11-25, and the differences are substantial (stateless, per-request capability negotiation, servers no longer initiate JSON-RPC requests, roots deprecated, only stdio and Streamable HTTP transports remain).

- [MCP in 7 Days: Wire Tools Into Any Agent](https://course.front-end-js.top/en/learn/mcp-7days) — 7 days

### Reusing expertise — Agent Skills

Write down what you already know how to do as a `SKILL.md`, so the client opens it when it becomes relevant. The split against MCP: **MCP is the wiring, Skills are the expertise.**

- [Agent Skills in 7 Days: Turn Experience Into Reusable Capability](https://course.front-end-js.top/en/learn/agent-skills-7days) — 7 days

### Managing context — Context Engineering

Treat the context window as luggage with a fixed capacity. This is the most underrated layer: most "the model got dumber" incidents trace back to context assembly, not to the model.

- [Context Engineering in 5 Days](https://course.front-end-js.top/en/learn/context-engineering-5days) — 5 days

---

## Stage 3 · Let the agent use your data (2 weeks)

The hard part of RAG isn't getting a demo running — it's **proving the thing is correct**. The recommended order is counterintuitive: **make keyword retrieval solid first, then add vectors.** People who start at the vector store usually can't tell you whether retrieval recalled the right document at all.

You're done when you can report recall@k, MRR, and nDCG, and explain why a change moved one up and another down.

- [RAG in 14 Days: From Retrieval to Trustworthy Answers](https://course.front-end-js.top/en/learn/rag-14days) — 14 days

---

## Stage 4 · Engineering: from "it runs" to "I'd ship it" (3 weeks)

This stage separates "has built an agent" from "can deliver an agent." Three things, none optional:

### Evaluation — turning "seems fine" into numbers

Agents are non-deterministic, so one successful run is not evidence. You turn non-determinism into **pass^k**, turn production incidents into a benchmark set, and put the LLM judge itself through an exam (judges are wrong too, and wrong with systematic bias).

- [Agent Evals and Observability in 7 Days: Turning "Seems Fine" Into Numbers You Can Reproduce](https://course.front-end-js.top/en/learn/agent-evals-7days) — 7 days

### Security — detectors aren't a boundary, architecture is

To judge whether an agent is dangerous, check whether it has the **lethal trifecta**: access to private data, exposure to untrusted content, and the ability to communicate externally. With all three, prompt injection can exfiltrate.

Two numbers must always appear together: **ASR (attack success rate) and utility (normal task completion rate)**. Any defense that reports only ASR is not credible — an agent that does nothing at all has an ASR of 0.

- [Agent Security in 5 Days](https://course.front-end-js.top/en/learn/agent-security-5days) — 5 days

### Frontend — turning an event stream into an interface that doesn't jitter

If your agent has a UI, this layer is routinely underestimated: the rhythm of streaming deltas, human-in-the-loop approval, a reviewable run history, and a sane reading order for screen readers.

Align with **AG-UI** (MIT) rather than inventing a protocol. Note that the `THINKING_*` events are deprecated — use `REASONING_*`. Many tutorials still show the old names.

- [Frontend Agent UX in 7 Days: From Streaming to Generative UI](https://course.front-end-js.top/en/learn/frontend-agent-ux-7days) — 7 days

---

## Stage 5 · Capstone (3–4 weeks)

Everything above is parts; this is the assembled machine. Finishing one of these is worth more than skimming all the courses.

### Build a terminal coding agent

Start from an empty REPL, add one capability per day, and end up with a tool that reads code in a real repository, edits files, runs tests, works in parallel, and is installable and distributable. **Zero frameworks, zero runtime dependencies** — slash commands, SSE parsing, the tool loop, and the MCP protocol are all hand-written.

- [Build Your Own Coding Agent in 21 Days](https://course.front-end-js.top/en/learn/my-coding-agent-21days) — 21 days

### Build a multimodal production line

Turn one sentence into an episode of a vertical-format short drama, then scale it into a platform that produces in parallel, supports review, and controls cost. The focus here is **orchestration and budget**: DAGs, retries, spend guardrails, and human review gates.

- [Build an AI Short-Drama Production Pipeline With Agents in 14 Days](https://course.front-end-js.top/en/learn/ai-drama-pipeline) — 14 days

---

## Full course list

| # | Course | Days |
|---|---|---|
| 1 | [From Frontend Engineer to Agent Engineer in 30 Days](https://course.front-end-js.top/en/learn/agent-30days) | 30 |
| 2 | [Prompt Engineering From Scratch in 5 Days](https://course.front-end-js.top/en/learn/prompt-engineering-5days) | 5 |
| 3 | [Mastering Claude: From Conversation to Claude Code in 5 Days](https://course.front-end-js.top/en/learn/claude-mastery) | 5 |
| 4 | [Mastering Codex and the OpenAI Agents SDK in 5 Days](https://course.front-end-js.top/en/learn/codex-mastery) | 5 |
| 5 | [MCP in 7 Days: Wire Tools Into Any Agent](https://course.front-end-js.top/en/learn/mcp-7days) | 7 |
| 6 | [Agent Skills in 7 Days: Turn Experience Into Reusable Capability](https://course.front-end-js.top/en/learn/agent-skills-7days) | 7 |
| 7 | [Context Engineering in 5 Days](https://course.front-end-js.top/en/learn/context-engineering-5days) | 5 |
| 8 | [Frontend Agent UX in 7 Days: From Streaming to Generative UI](https://course.front-end-js.top/en/learn/frontend-agent-ux-7days) | 7 |
| 9 | [RAG in 14 Days: From Retrieval to Trustworthy Answers](https://course.front-end-js.top/en/learn/rag-14days) | 14 |
| 10 | [Build an AI Short-Drama Production Pipeline With Agents in 14 Days](https://course.front-end-js.top/en/learn/ai-drama-pipeline) | 14 |
| 11 | [Build Your Own Coding Agent in 21 Days](https://course.front-end-js.top/en/learn/my-coding-agent-21days) | 21 |
| 12 | [Agent Evals and Observability in 7 Days: Turning "Seems Fine" Into Numbers You Can Reproduce](https://course.front-end-js.top/en/learn/agent-evals-7days) | 7 |
| 13 | [Agent Security in 5 Days](https://course.front-end-js.top/en/learn/agent-security-5days) | 5 |

**13 courses, 132 days.** Every chapter exists in both English and Chinese.

There's also a [cross-course interview question bank](https://course.front-end-js.top/en/interviews). Each question ships with the **reasoning path** (what it's testing → how to decompose it → the conclusion → follow-ups to expect), not just bullet points to memorize.

---

## Start here

If you'd rather sample than follow the sequence, start here:

- [LLM API Basics: messages/roles, Tokens, Streaming, Temperature; What an Agent Actually Is](https://course.front-end-js.top/en/learn/agent-30days/day-01)
- [How Tool Calling Works: JSON Schema, the tool_use Loop; Hand-Writing an Agent Loop With No Framework](https://course.front-end-js.top/en/learn/agent-30days/day-02)
- [What a Prompt Is, and Isn't: How the Model Reads Instructions; the Four Elements of Role / Task / Format / Constraints](https://course.front-end-js.top/en/learn/prompt-engineering-5days/day-01)
- [Why a Protocol: the Host/Client/Server Triangle, JSON-RPC Messages, and Three Primitives](https://course.front-end-js.top/en/learn/mcp-7days/day-01)
- [Threat modelling: the lethal trifecta, trust boundaries, and how to use two OWASP lists as checklists](https://course.front-end-js.top/en/learn/agent-security-5days/day-01)
- [What Layers Make Up a Coding Agent: the REPL Skeleton, the Command System, and a Provider-Neutral Gateway Abstraction](https://course.front-end-js.top/en/learn/my-coding-agent-21days/day-01)

---

## About

The courses live at [Dayward AI](https://course.front-end-js.top/en). Every day consists of three parts: an explanation of the mechanism, a lab you can actually run, and a few interview questions with worked reasoning. All lab code runs offline (`MOCK=1`) — no API key required to complete them.

This roadmap is published under [CC BY 4.0](./LICENSE). Reuse and adapt it freely with attribution. If something is wrong or the ordering could be better, open an issue.
