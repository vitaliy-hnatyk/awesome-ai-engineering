# Awesome AI Engineering [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated list of resources, tools, frameworks, and best practices for AI Engineering — the discipline of building production-grade systems powered by large language models and foundation models.

AI Engineering sits at the intersection of software engineering, machine learning, and applied research. This list focuses on the practical craft of **shipping** AI products: prompting, evaluation, RAG, agents, fine-tuning, infrastructure, safety, and the operational concerns that come with running models in production.

> **New to the field?** Start with [`docs/getting-started.md`](docs/getting-started.md) — an opinionated 4-week path from zero to shipping.

## Contents

- [Foundations](#foundations)
- [Learning Resources](#learning-resources)
- [Foundation Models & APIs](#foundation-models--apis)
- [Frameworks & Orchestration](#frameworks--orchestration)
- [Prompt Engineering](#prompt-engineering)
- [Retrieval-Augmented Generation (RAG)](#retrieval-augmented-generation-rag)
- [Vector Databases](#vector-databases)
- [Agents & Tool Use](#agents--tool-use)
- [Fine-Tuning & Post-Training](#fine-tuning--post-training)
- [Evaluation & Observability](#evaluation--observability)
- [Inference & Serving](#inference--serving)
- [AIOps & LLMOps](#aiops--llmops)
- [Safety, Security & Alignment](#safety-security--alignment)
- [Multimodal](#multimodal)
- [Voice & Speech](#voice--speech)
- [Developer Tooling](#developer-tooling)
- [Datasets](#datasets)
- [Benchmarks](#benchmarks)
- [Papers Worth Reading](#papers-worth-reading)
- [Newsletters & Blogs](#newsletters--blogs)
- [Communities](#communities)
- [Books](#books)
- [Contributing](#contributing)

---

## Foundations

Essential reading to understand what AI Engineering *is* as a discipline — distinct from traditional ML engineering.

- [What Is an AI Engineer?](https://www.latent.space/p/ai-engineer) — Swyx's original essay defining the role.
- [AI Engineering Roadmap](https://github.com/chiphuyen/aie-book) — Chip Huyen's companion repo to the *AI Engineering* book.
- [Building LLM-Powered Applications](https://huyenchip.com/2023/04/11/llm-engineering.html) — A comprehensive overview of the LLM application stack.
- [Emerging Architectures for LLM Applications](https://a16z.com/emerging-architectures-for-llm-applications/) — a16z's reference architecture.
- [The Rise of the AI Engineer](https://www.latent.space/p/ai-engineer) — Why this role emerged and what it entails.

## Learning Resources

### Courses

- [DeepLearning.AI Short Courses](https://www.deeplearning.ai/short-courses/) — Free courses on prompting, RAG, agents, fine-tuning.
- [Full Stack Deep Learning](https://fullstackdeeplearning.com/) — Production ML from data to deployment.
- [Hugging Face NLP Course](https://huggingface.co/learn/nlp-course) — Free, comprehensive, hands-on.
- [LangChain Academy](https://academy.langchain.com/) — Official LangChain courses.
- [Anthropic Courses](https://github.com/anthropics/courses) — Prompt engineering, real-world prompting, API fundamentals.

### Interactive Tutorials

- [Anthropic Prompt Engineering Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- [OpenAI Cookbook](https://github.com/openai/openai-cookbook) — Recipes and patterns.
- [Google Cloud Generative AI](https://github.com/GoogleCloudPlatform/generative-ai) — Notebooks and examples.

## Foundation Models & APIs

### Closed-Source / Hosted

- [Anthropic Claude](https://docs.claude.com/) — Claude Opus, Sonnet, and Haiku families.
- [OpenAI](https://platform.openai.com/docs) — GPT, o-series reasoning models.
- [Google Gemini](https://ai.google.dev/) — Gemini Pro, Flash, and Ultra.
- [Mistral](https://docs.mistral.ai/) — European frontier lab, open and hosted models.
- [Cohere](https://docs.cohere.com/) — Command models, focused on enterprise RAG.

### Open-Weight Models

- [Llama (Meta)](https://llama.meta.com/) — Llama 3 family.
- [Mistral / Mixtral](https://mistral.ai/news/) — Strong open MoE models.
- [Qwen (Alibaba)](https://qwenlm.github.io/) — Competitive open-weight series.
- [DeepSeek](https://www.deepseek.com/) — Strong reasoning-focused open models.
- [Gemma (Google)](https://ai.google.dev/gemma) — Open models from the Gemini family.

### Model Catalogs

- [Hugging Face Hub](https://huggingface.co/models) — The central hub for open-weight models.
- [OpenRouter](https://openrouter.ai/) — Unified API across hundreds of models.
- [Together AI](https://www.together.ai/) — Hosted open-model inference.

## Frameworks & Orchestration

- [LangChain](https://github.com/langchain-ai/langchain) — The original LLM orchestration framework.
- [LangGraph](https://github.com/langchain-ai/langgraph) — Graph-based agent orchestration.
- [LlamaIndex](https://github.com/run-llama/llama_index) — Data framework focused on RAG.
- [Haystack](https://github.com/deepset-ai/haystack) — Production-focused LLM framework.
- [DSPy](https://github.com/stanfordnlp/dspy) — Programming (not prompting) foundation models.
- [Instructor](https://github.com/jxnl/instructor) — Structured outputs via Pydantic.
- [Outlines](https://github.com/dottxt-ai/outlines) — Structured generation for LLMs.
- [Semantic Kernel](https://github.com/microsoft/semantic-kernel) — Microsoft's orchestration SDK.
- [Mastra](https://github.com/mastra-ai/mastra) — TypeScript AI framework.
- [Vercel AI SDK](https://github.com/vercel/ai) — TypeScript SDK for streaming UIs.
- [Pydantic AI](https://github.com/pydantic/pydantic-ai) — Type-safe agent framework.

See [`docs/frameworks.md`](docs/frameworks.md) for detailed comparisons.

## Prompt Engineering

- [Prompt Engineering Guide](https://www.promptingguide.ai/) — Comprehensive reference.
- [Anthropic Prompt Library](https://docs.claude.com/en/resources/prompt-library/library) — Production-tested prompts.
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [Learn Prompting](https://learnprompting.org/) — Free open-source course.
- [Awesome ChatGPT Prompts](https://github.com/f/awesome-chatgpt-prompts) — Community prompt collection.

See [`docs/prompt-engineering.md`](docs/prompt-engineering.md) for techniques and patterns.

## Retrieval-Augmented Generation (RAG)

- [RAG Techniques](https://github.com/NirDiamant/RAG_Techniques) — Advanced RAG patterns.
- [RAGatouille](https://github.com/AnswerDotAI/RAGatouille) — ColBERT-based retrieval.
- [Verba](https://github.com/weaviate/Verba) — Open-source RAG application.
- [R2R](https://github.com/SciPhi-AI/R2R) — Production RAG system.
- [LangChain RAG Tutorials](https://python.langchain.com/docs/tutorials/rag/)
- [Anthropic Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval) — Improved chunking approach.

See [`docs/rag.md`](docs/rag.md) for architecture patterns.

## Vector Databases

- [Pinecone](https://www.pinecone.io/) — Managed vector DB.
- [Weaviate](https://weaviate.io/) — Open-source, hybrid search.
- [Qdrant](https://qdrant.tech/) — Open-source, Rust-based.
- [Chroma](https://www.trychroma.com/) — Developer-friendly, embedded.
- [Milvus](https://milvus.io/) — Scalable open-source.
- [LanceDB](https://lancedb.com/) — Serverless, embedded.
- [pgvector](https://github.com/pgvector/pgvector) — PostgreSQL extension.
- [Turbopuffer](https://turbopuffer.com/) — Object-storage-backed.

## Agents & Tool Use

- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) — Open standard for tool/data connections.
- [OpenAI Agents SDK](https://github.com/openai/openai-agents-python)
- [Anthropic's Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) — Essential reading.
- [AutoGen](https://github.com/microsoft/autogen) — Multi-agent framework from Microsoft.
- [CrewAI](https://github.com/crewAIInc/crewAI) — Role-based agent teams.
- [smolagents](https://github.com/huggingface/smolagents) — Minimal agent library from HF.
- [Browser Use](https://github.com/browser-use/browser-use) — Browser automation agents.

See [`docs/agents.md`](docs/agents.md) for design patterns.

## Fine-Tuning & Post-Training

- [Unsloth](https://github.com/unslothai/unsloth) — 2x faster fine-tuning.
- [Axolotl](https://github.com/axolotl-ai-cloud/axolotl) — Fine-tuning config framework.
- [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) — Unified fine-tuning UI.
- [TRL](https://github.com/huggingface/trl) — Transformer Reinforcement Learning (DPO, PPO, GRPO).
- [PEFT](https://github.com/huggingface/peft) — Parameter-Efficient Fine-Tuning (LoRA, QLoRA).
- [torchtune](https://github.com/pytorch/torchtune) — PyTorch-native fine-tuning.

## Evaluation & Observability

- [LangSmith](https://www.langchain.com/langsmith) — Tracing and evals.
- [Langfuse](https://github.com/langfuse/langfuse) — Open-source LLM observability.
- [Helicone](https://github.com/Helicone/helicone) — Open-source logging and analytics.
- [Braintrust](https://www.braintrust.dev/) — Eval platform.
- [Promptfoo](https://github.com/promptfoo/promptfoo) — Open-source prompt testing.
- [DeepEval](https://github.com/confident-ai/deepeval) — Unit testing framework for LLMs.
- [Ragas](https://github.com/explodinggradients/ragas) — RAG-specific eval framework.
- [Inspect AI](https://github.com/UKGovernmentBEIS/inspect_ai) — UK AISI's eval framework.
- [OpenAI Evals](https://github.com/openai/evals)

See [`docs/evaluation.md`](docs/evaluation.md) for eval methodology.

## Inference & Serving

- [vLLM](https://github.com/vllm-project/vllm) — High-throughput inference engine.
- [TensorRT-LLM](https://github.com/NVIDIA/TensorRT-LLM) — NVIDIA's optimized inference.
- [llama.cpp](https://github.com/ggerganov/llama.cpp) — CPU/GPU inference in C++.
- [Ollama](https://github.com/ollama/ollama) — Local model runner.
- [LM Studio](https://lmstudio.ai/) — Desktop app for local models.
- [Text Generation Inference (TGI)](https://github.com/huggingface/text-generation-inference) — HF production server.
- [SGLang](https://github.com/sgl-project/sglang) — Fast serving with structured outputs.
- [MLX](https://github.com/ml-explore/mlx) — Apple Silicon inference.

## AIOps & LLMOps

### Orchestration & Compute

- [BentoML](https://github.com/bentoml/BentoML) — Model serving framework.
- [Modal](https://modal.com/) — Serverless GPU compute.
- [RunPod](https://www.runpod.io/) — GPU cloud.
- [Replicate](https://replicate.com/) — Model hosting and inference API.
- [SkyPilot](https://github.com/skypilot-org/skypilot) — Multi-cloud GPU orchestration.
- [Ray](https://github.com/ray-project/ray) — Distributed compute for ML.
- [Kubeflow](https://www.kubeflow.org/) — ML on Kubernetes.
- [KServe](https://github.com/kserve/kserve) — Model inference on Kubernetes.
- [ZenML](https://github.com/zenml-io/zenml) — MLOps pipelines, LLM-aware.

### Prompt & Config Management

- [PromptLayer](https://www.promptlayer.com/) — Prompt registry and versioning.
- [Humanloop](https://humanloop.com/) — Prompt management with evals.
- [Agenta](https://github.com/Agenta-AI/agenta) — Open-source LLMOps platform.
- [Pezzo](https://github.com/pezzolabs/pezzo) — Open-source prompt ops.

### Gateways & Proxies

- [LiteLLM](https://github.com/BerriAI/litellm) — Unified API across 100+ providers with logging and fallbacks.
- [Portkey](https://github.com/Portkey-AI/gateway) — AI gateway with routing, caching, guardrails.
- [Kong AI Gateway](https://konghq.com/products/kong-ai-gateway) — AI routing on Kong.
- [Cloudflare AI Gateway](https://developers.cloudflare.com/ai-gateway/) — Hosted gateway with caching and analytics.

### Experiment Tracking

- [Weights & Biases](https://wandb.ai/) — The default for most ML teams.
- [MLflow](https://mlflow.org/) — Open-source, now with LLM features.
- [Comet](https://www.comet.com/) — Experiment tracking with LLM support.

See [`docs/aiops.md`](docs/aiops.md) for deployment patterns, monitoring, cost management, and incident response.

## Safety, Security & Alignment

- [OWASP Top 10 for LLMs](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [Anthropic's Responsible Scaling Policy](https://www.anthropic.com/rsp-updates)
- [Garak](https://github.com/NVIDIA/garak) — LLM vulnerability scanner.
- [PyRIT](https://github.com/Azure/PyRIT) — Microsoft's red-teaming toolkit.
- [Guardrails](https://github.com/guardrails-ai/guardrails) — Output validation framework.
- [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) — NVIDIA's safety framework.
- [Llama Guard](https://github.com/meta-llama/PurpleLlama) — Input/output classifier.

See [`docs/safety.md`](docs/safety.md) for security practices.

## Multimodal

- [CLIP](https://github.com/openai/CLIP) — Image-text embedding model.
- [LLaVA](https://github.com/haotian-liu/LLaVA) — Visual instruction tuning.
- [Florence-2](https://huggingface.co/microsoft/Florence-2-large) — Unified vision model.
- [Qwen-VL](https://github.com/QwenLM/Qwen-VL) — Strong open vision-language model.
- [ColPali](https://github.com/illuin-tech/colpali) — Visual document retrieval.

## Voice & Speech

- [Whisper](https://github.com/openai/whisper) — Speech-to-text.
- [faster-whisper](https://github.com/SYSTRAN/faster-whisper) — 4x faster Whisper.
- [Coqui TTS](https://github.com/coqui-ai/TTS) — Text-to-speech toolkit.
- [Piper](https://github.com/rhasspy/piper) — Fast local neural TTS.
- [LiveKit Agents](https://github.com/livekit/agents) — Real-time voice agents.
- [Pipecat](https://github.com/pipecat-ai/pipecat) — Voice/multimodal agent framework.

## Developer Tooling

### IDEs & Assistants

- [Claude Code](https://claude.com/claude-code) — Anthropic's terminal-based coding agent.
- [Cursor](https://cursor.sh/) — AI-first code editor.
- [Windsurf](https://codeium.com/windsurf) — Agentic IDE.
- [Continue](https://github.com/continuedev/continue) — Open-source IDE assistant.
- [Aider](https://github.com/Aider-AI/aider) — AI pair programming in terminal.

### Libraries

- [LiteLLM](https://github.com/BerriAI/litellm) — Unified API across 100+ providers.
- [Jan](https://github.com/janhq/jan) — Offline ChatGPT alternative.
- [Open WebUI](https://github.com/open-webui/open-webui) — Self-hosted chat UI.

## Datasets

- [Hugging Face Datasets](https://huggingface.co/datasets) — Central hub.
- [The Pile](https://pile.eleuther.ai/) — 800GB diverse text.
- [RedPajama](https://github.com/togethercomputer/RedPajama-Data) — Open reproduction of LLaMA data.
- [FineWeb](https://huggingface.co/datasets/HuggingFaceFW/fineweb) — 15T token web dataset.
- [Dolma](https://github.com/allenai/dolma) — AI2's open pretraining corpus.

## Benchmarks

- [MMLU](https://github.com/hendrycks/test) — General knowledge.
- [HumanEval](https://github.com/openai/human-eval) — Code generation.
- [SWE-bench](https://www.swebench.com/) — Real GitHub issues.
- [GPQA](https://github.com/idavidrein/gpqa) — Graduate-level science.
- [LMSYS Chatbot Arena](https://chat.lmsys.org/) — Crowdsourced human preferences.
- [HELM](https://crfm.stanford.edu/helm/) — Holistic Evaluation of Language Models.
- [BIG-bench](https://github.com/google/BIG-bench) — 200+ diverse tasks.

## Papers Worth Reading

A starter list — see [`docs/papers.md`](docs/papers.md) for a fuller reading list.

- *Attention Is All You Need* (Vaswani et al., 2017)
- *Language Models are Few-Shot Learners* (Brown et al., 2020) — GPT-3
- *Chain-of-Thought Prompting* (Wei et al., 2022)
- *Training Language Models to Follow Instructions* (Ouyang et al., 2022) — InstructGPT
- *Constitutional AI* (Bai et al., 2022)
- *Direct Preference Optimization* (Rafailov et al., 2023)
- *Retrieval-Augmented Generation* (Lewis et al., 2020)
- *Toolformer* (Schick et al., 2023)
- *ReAct: Synergizing Reasoning and Acting* (Yao et al., 2022)

## Newsletters & Blogs

- [Latent Space](https://www.latent.space/) — Swyx and Alessio on AI engineering.
- [Import AI](https://importai.substack.com/) — Jack Clark's weekly roundup.
- [The Batch](https://www.deeplearning.ai/the-batch/) — Andrew Ng's newsletter.
- [Ahead of AI](https://magazine.sebastianraschka.com/) — Sebastian Raschka on research.
- [Interconnects](https://www.interconnects.ai/) — Nathan Lambert on post-training.
- [Chip Huyen's Blog](https://huyenchip.com/blog/) — Production ML deep dives.
- [Simon Willison's Blog](https://simonwillison.net/) — Daily notes on practical LLM use.

## Communities

- [r/LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/) — Open-weight model community.
- [Hugging Face Forums](https://discuss.huggingface.co/)
- [LangChain Discord](https://discord.gg/langchain)
- [EleutherAI Discord](https://discord.gg/eleutherai) — Research-focused.
- [AI Engineer Summit](https://www.ai.engineer/) — Annual conference.

## Books

- *AI Engineering* — Chip Huyen (O'Reilly, 2024)
- *Designing Machine Learning Systems* — Chip Huyen (O'Reilly, 2022)
- *Build a Large Language Model (From Scratch)* — Sebastian Raschka (Manning, 2024)
- *Hands-On Large Language Models* — Jay Alammar & Maarten Grootendorst (O'Reilly, 2024)
- *Prompt Engineering for LLMs* — John Berryman & Albert Ziegler (O'Reilly, 2024)

## Contributing

Contributions welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting a PR. In short: the entry should be genuinely useful, actively maintained, and relevant to engineers shipping AI products.

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0/)

To the extent possible under law, the contributors have waived all copyright and related rights to this work.
