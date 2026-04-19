# AIOps & LLMOps

The operational side of running AI systems in production. This page covers the lifecycle concerns — deployment, scaling, monitoring, incidents, cost, governance — that determine whether an AI product survives contact with real users.

A note on terminology: **MLOps** grew up around classical ML (training pipelines, model registries, feature stores). **LLMOps** is the variant for foundation-model-based systems. **AIOps** historically means "using AI for IT operations" but is increasingly used as an umbrella term. This page uses **LLMOps** for specificity, but the principles generalize.

## How LLMOps differs from MLOps

Most MLOps practices transfer, but the shape of the work is different.

| Concern | Classical MLOps | LLMOps |
|---|---|---|
| Primary artifact | Your trained model | Prompts, evals, retrieval indexes — the model is usually someone else's |
| Training | Central to the loop | Often skipped entirely, or reserved for fine-tuning |
| Versioning | Model weights, datasets | Prompts, system messages, tool definitions, indexes, model IDs |
| Performance metric | Accuracy, F1, AUC on a labeled test set | Multi-dimensional, often subjective, includes cost and latency |
| Inference cost | Roughly fixed per request | Varies 10–100x by prompt length, output length, model choice |
| Failure mode | Wrong prediction | Hallucination, refusal, injection, format break, tool misuse, drift |
| Regulatory surface | Model bias, data governance | All of that + content safety, copyright, provider compliance |

The net effect: your ops story shifts weight away from "training and deploying models" and toward "versioning prompts, running evals, monitoring outputs, and managing provider dependencies."

## The LLMOps lifecycle

```
  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
  │   Develop   │──▶│   Evaluate  │──▶│    Deploy   │──▶│   Monitor   │
  │             │   │             │   │             │   │             │
  │ prompts,    │   │ offline     │   │ staged      │   │ traces,     │
  │ tools, RAG, │   │ evals,      │   │ rollouts,   │   │ evals,      │
  │ code        │   │ red team    │   │ feature     │   │ costs,      │
  │             │   │             │   │ flags       │   │ feedback    │
  └─────────────┘   └─────────────┘   └─────────────┘   └──────┬──────┘
         ▲                                                     │
         └─────────────────────────────────────────────────────┘
                           production learnings
```

The feedback loop from production back to development is where good LLMOps earns its keep. Every incident becomes an eval case; every user complaint tightens the system prompt; every tool misuse sharpens the tool description.

## Versioning

What needs to be versioned, beyond your code:

- **Prompts** — Every system prompt, every example block. Use a prompt registry or, at minimum, keep prompts in version control with clear IDs.
- **Model identifiers** — `claude-opus-4-7`, `gpt-5-1106-preview`, etc. Never "latest." Pin versions and promote deliberately.
- **Tool definitions** — Tool schemas and descriptions are part of the contract with the model.
- **Retrieval indexes** — Which documents, chunked how, embedded with which model.
- **Evaluation sets** — Treated as test data, not as disposable scripts.
- **Scoring prompts** — If you use LLM-as-judge, the judge prompt is part of your test harness.

A practical pattern: bundle the prompt, model, tools, and retrieval config into a single versioned "configuration" and deploy those atomically. Prompt changes without corresponding eval runs are the most common source of silent regressions.

## Deployment patterns

### Staged rollouts

Prompt and model changes behave more like config changes than code changes — cheap to deploy, cheap to revert. Use that:

- **Shadow mode** — Run the new version in parallel, log its outputs, don't show them to users. Compare against the current version on real traffic.
- **Canary / percentage rollout** — Route 1%, then 10%, then 50% of traffic to the new version. Monitor eval metrics and user signals at each step.
- **Per-user or per-segment rollouts** — Internal users first, then power users, then everyone.

### Feature flags for prompts

Treat prompts like config behind flags. Changing a prompt shouldn't require a deploy; rolling back shouldn't require a deploy either. Tools like LaunchDarkly, Unleash, or purpose-built prompt management (LangSmith, Langfuse, PromptLayer) handle this.

### Blue-green at the model level

When swapping model providers or upgrading to a new model version, run both in parallel for a window. Collect paired outputs, evaluate, then cut over.

### Fallbacks

For every model call, have a plan for:
- Primary model is down or rate-limited → fall back to a secondary model.
- Tool call fails → handle gracefully in the agent loop.
- Response violates structured output schema → retry with correction, then fall back.

[LiteLLM](https://github.com/BerriAI/litellm) and similar proxies make multi-provider fallback straightforward.

## Monitoring & observability

### The three streams

Structure your observability around three distinct streams of data:

1. **Traces** — The full record of a request: inputs, every model call, tool call, retrieval, final output. This is your primary debugging artifact.
2. **Metrics** — Aggregate counters and histograms: QPS, latency percentiles, token counts, error rates, cost per request.
3. **Evals on production traffic** — Ongoing sampled evaluation of live outputs. Not the same as offline evals on a fixed set.

### What to log on every request

At minimum:
- Unique trace ID (propagated across services).
- User ID or session ID (hashed or anonymized).
- Model ID and version.
- Prompt version / config version.
- Full prompt and response (subject to privacy rules below).
- Token counts (input, output, cached).
- Latency per step (retrieval, model call, tools).
- Cost (computed from tokens × rates).
- Outcome (success, error type, moderation action).
- Feedback signals (thumbs, regenerate, abandon).

### Platforms

- **[LangSmith](https://www.langchain.com/langsmith)** — Framework-friendly, strong eval integration, hosted.
- **[Langfuse](https://langfuse.com/)** — Open-source, self-hostable, framework-agnostic.
- **[Helicone](https://helicone.ai/)** — Lightweight proxy-based logging.
- **[Arize Phoenix](https://github.com/Arize-ai/phoenix)** — Open-source, OpenTelemetry-native.
- **[Datadog LLM Observability](https://www.datadoghq.com/product/llm-observability/)** — If you're already on Datadog.
- **[W&B Weave](https://wandb.ai/site/weave)** — If you're already on W&B.
- **Roll your own** — OpenTelemetry + ClickHouse or Postgres works fine for moderate scale.

### Golden signals for LLM services

Borrowed from SRE, adapted:

- **Latency** — TTFT (time to first token), TPS (tokens per second), end-to-end. Track p50, p95, p99.
- **Traffic** — Requests per second, tokens per second, broken down by route and model.
- **Errors** — Provider errors (rate limits, timeouts, 5xx), schema violations, refusals, tool call failures.
- **Saturation** — For hosted models: provider rate limits approaching. For self-hosted: GPU utilization, queue depth, KV-cache pressure.

Additionally:

- **Cost** — $/request and $/DAU. Alert on anomalies.
- **Quality drift** — Sampled online evals against a rolling baseline.
- **Safety signals** — Moderation flags, prompt-injection heuristics, PII detections.

## Incident response

LLM incidents are often quieter than traditional outages — the service is up, it's just behaving badly. Plan for:

### Common failure modes

| Failure | Detection | Mitigation |
|---|---|---|
| Silent quality regression after prompt change | Online eval drop, user feedback shift | Roll back prompt; promote previous version |
| Model provider outage | Provider status, error spike | Fall back to secondary provider or cached responses |
| Cost runaway | Hourly cost anomaly | Rate-limit affected endpoint; find prompt causing long outputs |
| Infinite tool/agent loops | Iteration cap hit, latency outliers | Enforce iteration limits; review trace |
| Prompt injection in user traffic | Output filter hits, suspicious tool calls | Tighten input/output filters; patch system prompt |
| Retrieval index corruption | Citation failures, empty contexts | Rebuild from source; gate deploys on retrieval sanity check |
| Upstream model deprecation | Provider advisory | Migrate to current version with shadow-mode validation |

### Runbooks

Write them down. At minimum, for each LLM-powered feature:
- How to kill the feature (feature flag, circuit breaker).
- How to roll back to the previous prompt/model.
- Who to page.
- What logs and traces to capture before mitigations.

### Kill switches

Every LLM feature should have a way to turn it off without a deploy. This is non-negotiable for anything user-facing. The first few times you need it, you'll be grateful.

## Cost management

LLM costs can surprise teams used to predictable infrastructure bills. Disciplines:

### Tracking

- Attribute cost to features, users, and tenants — not just as a total.
- Track cost per request and per active user over time. These should generally trend down as you optimize.
- Alert on anomalies (daily cost > N × baseline).

### Optimization levers

Roughly in order of impact:

1. **Right-size the model.** Use the smallest model that meets quality requirements. A Haiku-class model for routing and classification, a flagship model for the hard step.
2. **Shorten prompts.** Prune examples, compress instructions, move stable content into cached prefixes.
3. **Prompt caching.** Most major providers support caching of long, stable prompt prefixes. Can cut costs 50–90% for RAG and agent use cases.
4. **Shorter outputs.** Ask for what you need, not more. For structured extraction, use tool/JSON mode to avoid verbose prose.
5. **Batch where possible.** Batch APIs (Anthropic, OpenAI) offer ~50% discounts for non-real-time workloads.
6. **Cache deterministic results.** Embeddings, classifications, retrieval results — cache them.
7. **Semantic caching for Q&A.** Cache answers for semantically similar questions (with care — can cause staleness issues).

### Rate limiting and quotas

Protect your costs from runaway usage:
- Per-user rate limits.
- Per-feature daily spend caps.
- Circuit breakers on unusual patterns (10x normal traffic, 100x normal output length).

## Data governance

### Privacy

- **Redact before logging.** Strip or hash PII before it hits long-term storage.
- **Retention policies.** Traces accumulate fast. Set TTLs appropriate to your use case and regulatory context.
- **Tenancy isolation.** In multi-tenant systems, ensure prompts, caches, retrieval indexes, and logs are scoped correctly. Cross-tenant leakage via shared caches is a real class of bug.
- **Data residency.** Where your model provider processes data matters for GDPR, HIPAA, and similar frameworks. Check contracts and available regional endpoints.

### Provider data policies

Know for each provider you use:
- Does the provider retain your data? For how long?
- Is your data used for training?
- What regional processing is available?
- Are there enterprise terms that change the defaults?

For regulated workloads, this matters as much as the model quality.

## Self-hosted models

For teams running open-weight models in their own infrastructure, an additional layer of ops:

### Serving stack

- **Inference engine** — vLLM, TGI, SGLang, TensorRT-LLM. Choice depends on model, hardware, and throughput requirements.
- **GPU orchestration** — Kubernetes with GPU operators, Ray, SkyPilot, or managed platforms (Modal, RunPod, Replicate).
- **Routing / load balancing** — Across replicas, across model sizes, across regions.
- **Autoscaling** — Hard problem for LLM serving because cold starts are slow (model weights are large). Common patterns: maintain a warm floor, use speculative preloading, keep models resident and scale replicas.

### Capacity planning

- **Tokens per second per GPU** — Your core throughput metric. Varies with model size, batch size, context length.
- **KV cache sizing** — Long contexts eat memory fast. Monitor and set limits.
- **Continuous batching** — vLLM and similar engines batch requests dynamically for much higher throughput than naive batching.

### When to self-host

Generally worth considering when:
- Per-token economics of hosted models don't work at your scale.
- Latency requirements demand regional or on-device deployment.
- Data must not leave your infrastructure.
- You need fine-grained control over the model (custom fine-tunes, specific architectures).

Generally *not* worth it when:
- You're still figuring out product-market fit.
- Your volume doesn't amortize the engineering cost.
- Hosted quality meaningfully exceeds what you can run on available hardware.

## CI/CD for LLM systems

What belongs in your pipelines:

### On every PR

- Lint and unit tests (same as any codebase).
- **Deterministic output checks** — Schema validation, format checks, regex assertions on a fixed test set.
- **Fast eval subset** — 20–50 cases that run in under a minute against the current model.
- **Cost delta check** — Flag if typical request cost changes materially.

### On main branch merge

- **Full eval suite** — Hundreds of cases, including adversarial and regression.
- **Shadow mode deploy** to staging against mirrored production traffic (if feasible).

### Pre-production

- **Red team suite** — Known-attack examples and automated scanners (Garak, PyRIT, Promptfoo red team).
- **Load test** — At expected peak traffic, with realistic prompt distributions.

### Promotion

- Eval metrics at or above baseline.
- No regression in safety signals.
- Cost and latency within budget.
- Feature-flagged rollout with online eval monitoring.

## Organizational patterns

A few patterns that tend to work:

- **Prompts live where the feature lives.** Not in a central "prompt library" owned by another team. Feature teams own their prompts and their evals.
- **A shared platform for eval infrastructure, tracing, and prompt versioning.** Individual teams shouldn't each build these.
- **Evals on-call.** Quality regressions should page someone, not sit in a dashboard.
- **Regular eval review.** The set gets stale; review and prune quarterly.
- **Prompt change review.** Treat prompt changes like code changes — someone else reads them before merge.

## Further reading

- [Emerging Architectures for LLM Applications](https://a16z.com/emerging-architectures-for-llm-applications/) — a16z's reference architecture.
- [Patterns for Building LLM-Based Systems & Products](https://eugeneyan.com/writing/llm-patterns/) — Eugene Yan, comprehensive.
- [What We Learned from a Year of Building with LLMs](https://applied-llms.org/) — Chip Huyen, Hamel Husain, Jason Liu, Shreya Shankar, Eugene Yan, Bryan Bischof.
- [The LLMOps Database](https://www.zenml.io/llmops-database) — ZenML's collection of production case studies.
- [Google's SRE Book](https://sre.google/books/) — Pre-LLM, but the discipline transfers.
- [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) — Emerging standard for LLM tracing.
