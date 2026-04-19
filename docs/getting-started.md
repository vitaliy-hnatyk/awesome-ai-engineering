# Getting Started

A path for someone new to AI Engineering who wants to get productive fast. This is opinionated — there are other valid paths.

## Week 1 — Make things work

Goal: Have a running end-to-end LLM application you built yourself.

1. **Pick one provider and read its docs end to end.** Anthropic, OpenAI, or Google. Don't shop around yet.
2. **Build a single-turn chat.** "User message in, model response out," in whatever language you're most fluent in.
3. **Add a system prompt.** Feel how the system prompt changes behavior.
4. **Add a tool.** One function the model can call. Something concrete — fetch a weather API, look up a user by ID.
5. **Build a tiny RAG demo.** Index a handful of documents, retrieve the top few by similarity, stuff them in context, answer questions about them.

You don't need a framework for any of this. The point is to feel the primitives.

## Week 2 — Make things good

Goal: Understand quality, cost, and latency trade-offs.

1. **Build an eval set of 20–30 examples** for your Week 1 demo. Include obvious cases, edge cases, and known-bad cases.
2. **Write an evaluator** — can be as simple as exact match, keyword check, or "does this output pass this assertion."
3. **Try three different prompts** on the same task and compare against your eval set. Numbers should inform your intuition, not replace it.
4. **Try two different models** (e.g., a flagship model vs. a smaller/cheaper one). Measure quality, cost, and latency.
5. **Add logging.** For every call, record the prompt, response, tokens, latency.

By the end of this week, you should feel the difference between "the prompt is good enough" and "I've measured that the prompt is good enough."

## Week 3 — Learn a framework

Goal: Have a point of view on when frameworks help and when they don't.

1. **Pick one framework** based on your language and use case. LangChain for breadth, LlamaIndex for RAG, Vercel AI SDK for TypeScript UIs, DSPy if you're curious about the programming-not-prompting paradigm.
2. **Rebuild one of your Week 1–2 demos in the framework.** Note what's easier and what's harder.
3. **Add one non-trivial feature** that would be annoying without a framework: streaming, multi-step chaining, memory, retrieval with a vector DB.
4. **Read the framework's docs on evaluation and observability.** Most have integrated support worth using.

## Week 4 — Build something real

Goal: Ship something someone else would use.

1. **Pick a concrete, narrow use case.** "A tool that drafts a reply to a customer email, given the email and the customer's order history." Narrow scope beats ambitious scope.
2. **Build it end to end.** Inputs, model calls, outputs, error handling, a simple UI or API.
3. **Deploy it somewhere real.** Doesn't need to be production — just not `localhost:3000`. Modal, Render, Fly.io, Vercel all work.
4. **Get at least one other person to try it.** Watch them use it. Note every point of confusion.
5. **Fix three things based on what you saw.**

## What to keep doing

Once you're past the initial learning curve:

- **Read papers selectively.** One or two a month that shape your thinking beats twenty you skim. The [papers doc](./papers.md) is a starting list.
- **Stay current on models.** New models land monthly. At minimum, know what's state of the art in your use case.
- **Maintain your eval set.** Every production bug becomes an eval case. This compounds.
- **Build range.** If you started with RAG, try fine-tuning something. If you started with agents, go deep on evaluation. The techniques interact.

## Common mistakes at the start

- **Starting with a framework before you understand the primitives.** You end up debugging framework abstractions instead of learning how LLMs actually behave.
- **Optimizing prompts without evals.** You'll convince yourself things are better when they're actually worse.
- **Skipping observability.** When something breaks in production, without logs you're guessing.
- **Over-reaching on scope.** "An AI assistant that does everything" is a trap. Pick something narrow and make it good.
- **Ignoring cost until it's painful.** A $200/day bill is a bug, not a feature. Track costs from day one.
- **Treating the model as magic.** It's a very impressive token predictor. Understanding that framing prevents a lot of category errors.

## Signals you're making progress

- You can look at a new LLM product and guess, reasonably well, how it works under the hood.
- Your first instinct for a problem is "how would I evaluate this?" before "what prompt should I write?"
- You've deleted more prompt text than you've added in your last few iterations.
- You know when *not* to use an LLM.

## Resources for each stage

**Starting out:**
- [Anthropic's prompt engineering interactive tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- [OpenAI Cookbook](https://github.com/openai/openai-cookbook)
- [Simon Willison's blog](https://simonwillison.net/) — daily practical notes

**Going deeper:**
- *AI Engineering* by Chip Huyen
- *Hands-On Large Language Models* by Jay Alammar & Maarten Grootendorst
- The [papers list](./papers.md) in this repo

**Staying current:**
- [Latent Space](https://www.latent.space/)
- [Interconnects](https://www.interconnects.ai/)
- [Hugging Face Daily Papers](https://huggingface.co/papers)
