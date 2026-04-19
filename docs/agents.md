# Agents & Tool Use

An "agent" is a system where an LLM decides what actions to take, in what order, and when to stop. The defining characteristic is the *loop*: the model observes results of its actions and chooses what to do next, rather than following a fixed script.

Agents are powerful but expensive and hard to debug. Before reaching for one, ask whether a simpler workflow (prompt chain, decision tree, retrieval) would do the job. Anthropic's [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) is essential reading on this distinction.

## Workflow vs. Agent

| Property | Workflow | Agent |
|---|---|---|
| Control flow | Predetermined | Model-determined |
| Debuggability | High | Low |
| Cost predictability | High | Low |
| Flexibility | Low | High |
| Right for | Well-understood tasks | Open-ended tasks |

**Rule of thumb:** use a workflow whenever you can describe the steps in advance. Use an agent only when you genuinely can't.

## Core components

### Tools

Tools are functions the model can call. Good tool design is the biggest lever in agent quality. Principles:

- **Name tools for what they do**, not how they work internally. `search_customer_orders` beats `db_query_v2`.
- **Keep arguments simple and typed**. JSON schema with clear descriptions.
- **Return structured, informative results**. Errors should explain what went wrong and how to fix it.
- **Make tools idempotent where possible** — agents retry.
- **Fewer, more powerful tools beat many narrow tools**. Each tool crowds the context window and the decision space.

### System prompt

The system prompt for an agent is a specification. It should describe:

- The agent's role and scope.
- Available tools and when to use each.
- Rules and constraints (what it must always/never do).
- Output format expectations.
- How to handle uncertainty and failures.

### Memory

- **Working memory** — The conversation history in context. Cheap and automatic but bounded.
- **Episodic memory** — Summaries of past sessions, retrievable later.
- **Semantic memory** — Structured facts learned over time (user preferences, entity relationships).

Don't build memory infrastructure before you need it. Many "agents" work fine with just the conversation history.

## Common agent patterns

### Tool-using chatbot

The simplest agent: a chat loop where the model can call tools when needed. Most production agents fit this pattern. Keep the loop simple and the tools well-designed.

### ReAct (Reason + Act)

Model alternates between reasoning steps ("I should look up X") and actions (tool calls). The reasoning is visible in the trajectory, which helps debugging. Most modern agents effectively do ReAct whether they call it that or not.

### Plan-and-execute

Model first generates a plan (a sequence of steps), then executes each step. Good when the task decomposes cleanly. Weak when early steps change what later steps should do — in those cases, re-planning is essential.

### Router

An LLM acts as a dispatcher, routing queries to specialized sub-agents or workflows. Cheap, effective, and easy to reason about. Often the right first design.

### Multi-agent

Multiple specialized agents collaborate, often with a coordinator. Powerful for complex tasks but introduces a lot of ways to go wrong. Reserve for cases where single-agent approaches clearly fail.

### Orchestrator–worker

A lead agent decomposes the task and dispatches subtasks to worker agents in parallel. The lead integrates results. Anthropic's research use-case work uses this pattern extensively.

## The Model Context Protocol (MCP)

[MCP](https://modelcontextprotocol.io/) is an open standard (introduced by Anthropic, now widely supported) for exposing tools and data sources to LLM applications. Instead of every app writing custom integrations, MCP servers provide a standard interface that any MCP-aware client can use.

Why it matters for engineers:
- You build an MCP server once; any compliant client (Claude Desktop, Claude Code, Cursor, etc.) can use it.
- You can compose third-party MCP servers (GitHub, Slack, filesystems) without writing integration code.
- It separates tool implementation from agent logic cleanly.

## Evaluation

Agent evaluation is genuinely hard because:
- Success is often subjective.
- There are many valid trajectories to the same answer.
- Failures cascade — one bad tool call ruins everything after.

Practical approaches:

- **Task completion rate** — Did the agent accomplish the goal? Usually requires human or LLM-as-judge labeling.
- **Trajectory analysis** — Did the agent take reasonable steps? Useful for diagnosing *why* failures happen.
- **Tool-call precision/recall** — Did it call the right tools with the right arguments?
- **Cost and latency** — Agents that succeed expensively still need optimization.

Build trajectory traces from day one. When something goes wrong in production, you'll need to see exactly what the model saw and decided.

## Production concerns

### Guardrails

- **Input filtering** — Block obviously malicious inputs before they reach the agent.
- **Tool-level permissions** — Every tool should enforce its own auth; don't trust the model to respect scope.
- **Spend limits** — Cap tokens, tool calls, and loop iterations per session.
- **Human-in-the-loop** — Require confirmation for destructive or irreversible actions.

### Observability

Log at minimum:
- Full trajectory (messages, tool calls, tool results).
- Token counts and costs per step.
- Latency per step.
- Final outcome and any errors.

Tools: [LangSmith](https://www.langchain.com/langsmith), [Langfuse](https://github.com/langfuse/langfuse), [Braintrust](https://www.braintrust.dev/), [Helicone](https://github.com/Helicone/helicone).

### Failure handling

Things that *will* happen in production:

- Tool calls with malformed arguments → validate and return clear errors.
- Tool APIs timing out or rate-limiting → retry with backoff, surface to the model.
- Infinite loops → enforce hard iteration limits.
- The model refusing to call tools → examine your system prompt and tool descriptions.
- The model hallucinating tool names → stricter output parsing, return clear "no such tool" errors.

### Cost control

- Use smaller/cheaper models for routing, classification, and structured extraction.
- Reserve the expensive model for the steps that actually need it.
- Cache expensive tool results when inputs haven't changed.
- Aggressively summarize long conversation histories before they balloon the context.

## Anti-patterns

### Building an agent when a workflow would do

If you can describe the steps, describe them. A hardcoded pipeline is faster, cheaper, and more debuggable.

### Too many tools

Every tool in the context is a distraction. If you have 30 tools, you probably need to consolidate or split into multiple agents.

### Assuming the model reads tool descriptions carefully

It does, but only as well as you wrote them. Test your tool descriptions by asking the model, outside of any task, to explain when it would use each one.

### No iteration cap

Without a limit, a confused agent will loop until it exhausts your budget. Always set a maximum.

### No human escape hatch

For any agent touching real systems, a clear handoff path to a human is a feature, not a failure.

## Further reading

- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) — Anthropic's essential primer.
- [A Practical Guide to Building Agents](https://cdn.openai.com/business-guides/a-practical-guide-to-building-agents.pdf) — OpenAI's take.
- [ReAct: Synergizing Reasoning and Acting](https://arxiv.org/abs/2210.03629) — The foundational paper.
- [Model Context Protocol](https://modelcontextprotocol.io/) — The standard for tool/data connections.
- [Agents](https://huyenchip.com/2025/01/07/agents.html) — Chip Huyen's overview.
