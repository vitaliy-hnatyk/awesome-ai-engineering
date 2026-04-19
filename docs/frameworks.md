# Frameworks & Orchestration

Choosing the right framework — or choosing to use none — is one of the earliest architectural decisions in an AI project. This page is a practical comparison, not a ranking; the right choice depends on your team, stack, and use case.

## The case for no framework

For simple applications, the provider SDK (Anthropic, OpenAI, etc.) plus a few hundred lines of Python or TypeScript is often enough. You get:

- Fewer abstractions to learn.
- Direct control over prompts and context.
- No framework churn.
- Smaller dependency footprint.

If your application is one LLM call plus some business logic, you probably don't need a framework.

The case *for* a framework grows with: multi-step pipelines, multiple providers, retrieval, agents, observability integration, team size, and the need to move fast without reinventing wheels.

## Category map

Frameworks cluster into a few rough categories:

| Category | What they optimize for | Examples |
|---|---|---|
| **General orchestration** | Chaining, routing, composing LLM calls | LangChain, LlamaIndex, Haystack, Semantic Kernel |
| **Agent-first** | Stateful multi-step agent loops | LangGraph, AutoGen, CrewAI, smolagents |
| **Structured outputs** | Reliable typed results | Instructor, Outlines, Pydantic AI |
| **Programming model** | Prompts as programs, optimized automatically | DSPy |
| **UI-first** | Streaming chat experiences in web apps | Vercel AI SDK, Mastra |
| **Provider abstraction** | One API across many model providers | LiteLLM, OpenRouter |

Most real applications use more than one — e.g., LiteLLM for provider abstraction, Instructor for structured outputs, your own orchestration logic on top.

## Major frameworks

### LangChain

The most widely known LLM framework. Huge ecosystem of integrations (every vector DB, every provider, every loader). Python and JavaScript versions.

**Strengths:** Breadth of integrations, strong community, batteries-included. Good when you want to try many components and iterate.

**Watch-outs:** The abstractions have changed significantly over time. Can feel over-engineered for small projects. The `LangChain Expression Language` (LCEL) has a learning curve.

**Best for:** Teams exploring the space, projects that need to integrate with many external services.

### LangGraph

LangChain's answer to stateful agents. Build agents as graphs of nodes (functions) and edges (conditions). Explicit, debuggable, supports persistence and human-in-the-loop.

**Strengths:** Explicit control flow, good checkpointing, solid for complex agents.

**Watch-outs:** Still evolving; familiarity with the graph model takes time.

**Best for:** Non-trivial agent applications where workflow-style control matters.

### LlamaIndex

Originally a RAG-focused framework; now broader. Strong data connectors, sophisticated indexing strategies, agent support.

**Strengths:** Best-in-class abstractions for document-heavy applications, wide array of retrieval patterns.

**Watch-outs:** Overlap with LangChain can be confusing; the API has also evolved a lot.

**Best for:** RAG-centric applications, document Q&A, knowledge base products.

### Haystack

Production-oriented framework from deepset. Pipeline-based architecture, strong typing, good observability.

**Strengths:** Mature, production-focused, pipeline abstraction is clean.

**Watch-outs:** Smaller community than LangChain/LlamaIndex.

**Best for:** Teams with strong production requirements who prefer explicit pipelines.

### DSPy

A different philosophy: describe what you want, and let the framework optimize prompts and few-shot examples automatically. Modules compose like neural net layers.

**Strengths:** Makes prompts maintainable at scale. Optimization can meaningfully improve quality without manual prompt tuning. Good for pipelines that will be revisited repeatedly.

**Watch-outs:** Higher initial learning curve. Less accessible if you want direct control over prompt text.

**Best for:** Teams building pipelines they'll iterate on for a long time, especially where they'd otherwise be in a loop of manual prompt tuning.

### Instructor / Outlines / Pydantic AI

These are less "orchestration" and more "reliable structured outputs." Define a Pydantic (or equivalent) schema, get typed objects out.

**Strengths:** Reduces a huge category of bugs. Works well with any other framework or with none at all.

**Watch-outs:** Not a full application framework — you still need orchestration.

**Best for:** Any application where you want typed, validated outputs. Most applications, in practice.

### Vercel AI SDK

TypeScript/JavaScript SDK focused on building chat UIs. First-class streaming, provider-agnostic, React/Svelte/Vue hooks.

**Strengths:** Excellent developer experience for web apps, well-designed streaming primitives.

**Watch-outs:** Narrower scope than Python frameworks; focused on the presentation layer.

**Best for:** TypeScript teams building chat interfaces.

### AutoGen

Microsoft Research framework for multi-agent systems. Agents talk to each other to solve tasks.

**Strengths:** Good for research and complex multi-agent scenarios.

**Watch-outs:** Multi-agent is expensive and hard to debug; make sure you actually need it.

**Best for:** Research, prototyping complex agent teams.

### CrewAI

Role-based multi-agent framework. Define "crew members" with roles, goals, and tools; orchestrate them on a shared task.

**Strengths:** Simple mental model for multi-agent tasks. Easy to prototype.

**Watch-outs:** Same multi-agent caveats as AutoGen; production deployments need additional engineering.

**Best for:** Multi-role workflows (research, writing, review) where roles map naturally to agents.

### smolagents

Hugging Face's minimal agent library. Philosophy: agents should be code-generating, not tool-calling-JSON-generating.

**Strengths:** Small, readable, a refreshing take when you want something minimal.

**Watch-outs:** Smaller ecosystem than bigger frameworks.

**Best for:** Teams who want a lightweight, code-first agent approach.

### LiteLLM

Not an orchestration framework — a provider abstraction layer. One API across 100+ providers, with unified logging, fallbacks, and cost tracking.

**Strengths:** Trivial to swap providers, great for cost optimization and resilience, good observability hooks.

**Watch-outs:** Adds a dependency between you and the provider SDK; occasional mismatches with bleeding-edge provider features.

**Best for:** Almost any multi-provider production system. Often worth using alongside another framework.

## How to choose

A rough decision tree:

1. **Single LLM call, simple use case** → Provider SDK. No framework.
2. **Need structured outputs** → Add Instructor (Python) or Zod + provider SDK (TypeScript).
3. **RAG-heavy application** → LlamaIndex or LangChain, whichever your team finds more ergonomic.
4. **Multi-step agent** → LangGraph, or provider agent SDKs (OpenAI Agents, Anthropic's patterns) for simpler cases.
5. **Many providers, want unified API** → LiteLLM underneath whatever else you use.
6. **TypeScript + streaming UI** → Vercel AI SDK.
7. **Large pipeline you'll optimize repeatedly** → Consider DSPy.

## Migration and lock-in

Framework migrations are painful. Mitigations:

- Keep your business logic (data models, prompts, tools) separate from framework-specific code.
- Use provider-agnostic abstractions (LiteLLM, or your own thin wrapper) for model calls.
- Don't store framework-specific objects in your database.
- Your eval infrastructure should work regardless of what framework you use.

The frameworks that look dominant today may not be dominant in two years. The things that will still matter: your prompts, your evals, your tools, your domain models.
