# Evaluation & Observability

Evaluation is the single most undervalued skill in AI Engineering. Teams that build good evals ship faster and with fewer surprises; teams that skip evals spend their days arguing about which prompt "feels" better.

## Why LLM evals are different

Traditional ML evaluation relies on labeled datasets and fixed metrics (accuracy, F1, BLEU). LLM evaluation has to grapple with:

- **Open-ended outputs** — There isn't always one right answer.
- **Subjective quality** — Tone, helpfulness, and clarity are judgment calls.
- **Non-determinism** — The same input can produce different outputs across runs.
- **Moving targets** — Models and prompts change; evals need to keep up.

This doesn't mean evaluation is impossible. It means you need to build evaluation systems suited to the problem, not borrow ML ones wholesale.

## The evaluation stack

Think of evaluation as three concentric layers, each serving a different purpose:

### 1. Unit-style tests (fast, deterministic)

For things you can check programmatically:

- JSON schema validation.
- Regex / keyword presence or absence.
- Length constraints.
- Latency and cost budgets.
- Banned-content detection.

Run these on every change. They catch the dumb stuff.

### 2. Reference-based evals (medium effort)

When you have ground truth, or can construct it:

- Exact match for classification.
- Numeric comparison for extraction.
- Similarity scores (embedding-based, ROUGE, BLEU) for generation.
- For RAG: retrieved-chunk precision/recall against labeled relevance.

Build an eval set of 50–500 labeled examples. Curate it carefully — it's more valuable than a raw dataset 100x the size.

### 3. Reference-free evals (judgment required)

For open-ended quality questions:

- **LLM-as-judge** — Use a strong model to rate outputs on specified criteria.
- **Human review** — The gold standard; expensive but irreplaceable for calibration.
- **Pairwise comparison** — Often easier than absolute scoring. "Which is better, A or B?" produces more reliable judgments than "rate from 1–5."
- **User feedback** — Thumbs up/down, implicit signals (retries, rephrases, abandonment).

## LLM-as-judge

Using an LLM to grade LLM outputs is the workhorse of modern evaluation. Some practical guidelines:

### When it works well

- Criteria are specific and verifiable ("Does the answer cite a source?").
- Evaluating narrow dimensions one at a time, not overall quality.
- Tasks where a strong model clearly outperforms the judged model.

### When it fails

- Asking for vague "overall quality" ratings.
- Domain areas where the judge is also weak (specialized technical content, niche languages).
- Asking for fine-grained numeric scores (1–10) without clear anchors.

### Best practices

- **Use a stronger judge than the system under test.** A Haiku judging Haiku outputs is suspect.
- **Score one criterion at a time.** A rubric with 5 checks run independently is more reliable than one prompt asking for 5 scores.
- **Provide anchored criteria.** "Score 5: cites at least one source for every claim. Score 1: no sources cited at all." Not "Score 5: very good."
- **Prefer binary over graded.** Did it cite sources? Yes or no. Aggregate across examples for the real signal.
- **Validate against human labels.** Before trusting LLM judgments at scale, check agreement with humans on a sample. If agreement is below ~80%, the judge prompt needs work.
- **Watch for position bias.** In pairwise comparisons, LLMs often prefer the first option shown. Randomize order.

## Building an eval set

The eval set is the most important artifact in your AI project. Guidelines:

### Size

Start with 20–50 carefully chosen examples. Grow to a few hundred. Rarely useful to go beyond ~1,000 unless you're doing statistical hypothesis testing.

### Coverage

Cover the taxonomy of your use case:

- **Happy path** — Typical successful inputs.
- **Edge cases** — Boundary conditions, ambiguous inputs.
- **Adversarial** — Inputs designed to break the system (prompt injection, jailbreaks, off-topic).
- **Regressions** — Specific bugs you've already fixed, to catch them coming back.

### Sourcing

- **Production logs** — Best source once you're live. Sample recent real traffic.
- **User reports** — Every complaint or unexpected result belongs in the eval set.
- **Synthetic** — LLM-generated examples for coverage. Cheap, but can miss real-world weirdness.
- **Red team** — Deliberately adversarial examples.

### Maintenance

The eval set should evolve with the product. Add new cases as you encounter them. Review periodically — some examples become stale, some reveal yourself-overfitting ("I've seen this example so many times that my prompt is now overfit to it").

## Online vs. offline evaluation

**Offline evals** run against your static eval set during development. Fast feedback, controlled environment, no user risk.

**Online evals** run against live production traffic. Catches things offline evals miss — real user inputs, real distribution, real edge cases — but the feedback loop is slower and changes carry user risk.

Production systems need both. Offline evals gate deploys; online evals detect drift and surface new failure modes for your offline set.

## Observability

What to capture for every production request:

- **Inputs** — User message, system prompt version, retrieved context, any metadata.
- **Outputs** — Full response, tool calls, tool results.
- **Metadata** — Model version, latency per step, token counts, cost.
- **Trace structure** — If there's a pipeline (retrieve → rerank → generate → verify), record each step independently.
- **Feedback signals** — Explicit user feedback, implicit signals (regenerate, copy, abandon).

Privacy caveat: logs often contain sensitive data. Redact appropriately, set retention policies, and respect user data requests.

## Platforms

- **[LangSmith](https://www.langchain.com/langsmith)** — Tight integration with LangChain, strong eval features, hosted.
- **[Langfuse](https://github.com/langfuse/langfuse)** — Open-source, self-hostable, framework-agnostic.
- **[Braintrust](https://www.braintrust.dev/)** — Eval-first product, strong LLM-as-judge tooling.
- **[Helicone](https://github.com/Helicone/helicone)** — Open-source, simple proxy-based integration.
- **[Arize Phoenix](https://github.com/Arize-ai/phoenix)** — Open-source, trace-focused.
- **[Weights & Biases Weave](https://wandb.ai/site/weave)** — Good if you already use W&B.

## Common eval pitfalls

### Evaluating on the training examples

If you tuned a prompt on 20 examples, evaluating on those same 20 tells you nothing. Hold out a test set.

### Trusting vibes

"This feels better" is not evaluation. It's directionally useful for hypothesis generation, not for decisions.

### Over-indexing on benchmarks

Public benchmarks (MMLU, HumanEval) tell you about general model capability. They tell you almost nothing about whether a specific model is good for *your* specific task. Build your own.

### Measuring the wrong thing

BLEU score on a customer support bot. Semantic similarity on extraction tasks where exactness matters. Pick metrics that actually reflect product quality.

### Single-point estimates

One run of one eval set tells you little, especially with non-deterministic outputs. Report confidence intervals or run multiple times.

### Ignoring cost and latency

A system that's 2% better on accuracy but 10x slower and 5x more expensive is usually a worse product. Multi-dimensional evaluation is non-negotiable.

## Further reading

- [Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/) — Hamel Husain, essential.
- [A Survey on LLM-as-a-Judge](https://arxiv.org/abs/2411.15594)
- [Who Validates the Validators?](https://arxiv.org/abs/2404.12272) — On calibrating LLM judges.
- [Evaluating LLMs is a minefield](https://www.cs.princeton.edu/~arvindn/talks/evaluating_llms_minefield/) — Arvind Narayanan.
