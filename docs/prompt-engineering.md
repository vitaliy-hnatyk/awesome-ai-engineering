# Prompt Engineering

A practical guide to the techniques that matter when building LLM-powered systems. The focus here is on patterns that survive contact with production — not clever tricks that work once in a demo.

## Core Principles

### 1. Be specific and concrete

Models respond to specificity. "Write a summary" is worse than "Write a 3-sentence summary focused on financial implications, for an executive reader." Every constraint you can add narrows the search space.

### 2. Show, don't just tell

Few-shot examples usually outperform long instructions. If you can demonstrate the format, tone, or reasoning style you want with 2–5 examples, do that instead of describing it in prose.

### 3. Structure your prompts

Use clear delimiters (XML tags, markdown headers, JSON) to separate instructions, context, examples, and the actual task. Models parse structured prompts more reliably than wall-of-text prompts.

```
<instructions>
You are a customer support triage agent...
</instructions>

<examples>
<example>
<input>My order hasn't arrived</input>
<output>category: shipping, priority: high</output>
</example>
</examples>

<task>
Classify this message: {user_message}
</task>
```

### 4. Give the model room to think

For any task requiring reasoning, let the model reason *before* answering. Either ask for step-by-step thinking explicitly, or use the model's built-in reasoning/thinking mode if available. Don't force it to answer immediately on hard problems.

## Techniques

### Zero-shot prompting

Just ask. Works well for simple tasks that the model has clearly seen during training (summarization, basic classification, translation).

### Few-shot prompting

Provide examples before the actual task. Rule of thumb: 3–5 examples captures most of the benefit. More than ~10 examples rarely helps and can confuse the model.

**Key considerations:**
- Examples should be diverse and cover edge cases.
- Example order matters — put your most representative example last.
- Keep format consistent across examples and the actual query.

### Chain-of-Thought (CoT)

Asking the model to "think step by step" or to "show your reasoning" improves performance on math, logic, and multi-step problems. Modern reasoning models (Claude with extended thinking, OpenAI o-series, DeepSeek R1) do this internally — you typically don't need to prompt for it explicitly.

### Role / Persona prompting

"You are an expert Python developer..." Works, but often less than people assume. It's more useful for **tone and vocabulary** than for capability — telling a model it's an expert doesn't make it smarter, but it does shift the register of the output.

### Decomposition

For complex tasks, break the work into smaller prompts chained together rather than trying to do everything in one prompt. A classification step, followed by a specialized prompt for each class, almost always beats a monolithic mega-prompt.

### Self-consistency

Run the same prompt multiple times with a nonzero temperature, then take the majority answer. Expensive, but noticeably improves accuracy on reasoning tasks where the model is "almost right" but inconsistent.

### Prompt caching

Most major providers now support caching of large, stable prompt prefixes (system prompts, examples, retrieved documents). Cached tokens cost a fraction of fresh tokens. Structure your prompts so the stable parts come first.

## Structured Outputs

Three main approaches, in rough order of reliability:

1. **Native structured output / JSON mode** — Most major providers now support constrained generation against a JSON schema. Use this when available.
2. **Tool use** — Define a "tool" whose parameters are your desired output schema, and force the model to call it. Well-supported across providers.
3. **Prompted JSON** — Ask for JSON in the prompt and parse. Fragile but works for quick prototypes. Libraries like [Instructor](https://github.com/jxnl/instructor) and [Outlines](https://github.com/dottxt-ai/outlines) add validation and retry logic.

## Anti-patterns

### Over-engineering the prompt

Long, complex prompts with dozens of rules are often worse than clean, focused ones. If you find yourself writing a 3,000-word system prompt, consider whether decomposition or fine-tuning would serve better.

### Confusing "nice to have" with "required"

Prompts accumulate cruft — a new edge case, a new request — and each addition pulls the model's attention away from the core task. Regularly prune.

### Negative instructions

"Don't mention X" often backfires. Models attend to whatever you bring up. Prefer positive framings: describe what to do, not what to avoid.

### Testing on your favorite example

Always evaluate prompts on a held-out set of realistic inputs, including adversarial ones. A prompt that works on three cherry-picked examples tells you almost nothing.

## Provider-specific notes

**Anthropic (Claude):** XML tags are a first-class structuring tool. Claude is trained to attend to them. Also supports prompt caching and extended thinking modes.

**OpenAI:** System messages carry strong weight. Tool use and structured output mode are reliable. Reasoning models (o-series) handle their own CoT — don't prompt for it.

**Google (Gemini):** Very long context windows; good for stuffing documentation or examples. System instructions are passed separately from user content.

**Open-weight models:** Behavior varies dramatically. Always check the model card for the expected chat template. Smaller models benefit more from few-shot examples and structured output constraints.

## Further Reading

- [Anthropic's Prompt Engineering Guide](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [The Prompt Report](https://arxiv.org/abs/2406.06608) — Systematic survey of 58 techniques.
- [DSPy](https://github.com/stanfordnlp/dspy) — Framework for programming, not prompting.
