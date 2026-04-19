# Papers Worth Reading

A reading list for AI engineers who want to understand *why* things work, not just which library to `pip install`. Organized by topic, roughly chronologically within each.

This isn't exhaustive — it's a starting path. If you read everything here, you'll have a solid foundation for reading almost anything new that appears.

## Architecture Foundations

- **Attention Is All You Need** (Vaswani et al., 2017) — The Transformer. The single most important paper in modern AI.
- **Language Models are Unsupervised Multitask Learners** (Radford et al., 2019) — GPT-2, introducing the autoregressive LM paradigm.
- **Language Models are Few-Shot Learners** (Brown et al., 2020) — GPT-3. In-context learning as a concept.
- **Scaling Laws for Neural Language Models** (Kaplan et al., 2020) — The empirical basis for the scale-it-up era.
- **Training Compute-Optimal Large Language Models** (Hoffmann et al., 2022) — Chinchilla. Scaling laws, revised.

## Post-training & Alignment

- **Training Language Models to Follow Instructions with Human Feedback** (Ouyang et al., 2022) — InstructGPT; RLHF as a recipe.
- **Constitutional AI: Harmlessness from AI Feedback** (Bai et al., 2022) — Anthropic's approach.
- **Direct Preference Optimization** (Rafailov et al., 2023) — DPO. Simpler alternative to RLHF.
- **LIMA: Less Is More for Alignment** (Zhou et al., 2023) — A small, high-quality dataset goes a long way.
- **Self-Instruct** (Wang et al., 2022) — Bootstrapping instruction data from the model itself.

## Reasoning

- **Chain-of-Thought Prompting Elicits Reasoning** (Wei et al., 2022) — CoT. The technique and the phenomenon.
- **Self-Consistency Improves Chain-of-Thought Reasoning** (Wang et al., 2022) — Sample many, take the majority.
- **Tree of Thoughts** (Yao et al., 2023) — Search over reasoning paths.
- **Let's Verify Step by Step** (Lightman et al., 2023) — Process supervision for math reasoning.

## Retrieval-Augmented Generation

- **Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks** (Lewis et al., 2020) — The paper that named the pattern.
- **REALM: Retrieval-Augmented Language Model Pre-training** (Guu et al., 2020) — Earlier, also foundational.
- **Lost in the Middle** (Liu et al., 2023) — On long-context attention failures.
- **Dense Passage Retrieval** (Karpukhin et al., 2020) — The basis of modern dense retrieval.
- **ColBERT** (Khattab & Zaharia, 2020) — Late-interaction retrieval, still competitive.

## Agents & Tool Use

- **ReAct: Synergizing Reasoning and Acting** (Yao et al., 2022) — The canonical agent framing.
- **Toolformer** (Schick et al., 2023) — LMs teaching themselves to use tools.
- **Reflexion** (Shinn et al., 2023) — Agents that learn from verbal self-feedback.
- **SWE-agent** (Yang et al., 2024) — Agents for real software engineering tasks.
- **Voyager** (Wang et al., 2023) — Open-ended embodied agents in Minecraft.

## Fine-Tuning & Efficient Training

- **LoRA: Low-Rank Adaptation** (Hu et al., 2021) — The basis of most modern fine-tuning.
- **QLoRA** (Dettmers et al., 2023) — 4-bit + LoRA, enables fine-tuning on consumer GPUs.
- **FlashAttention** (Dao et al., 2022) — The attention kernel that made long context practical.
- **Mixture of Experts: a Survey** (Fedus et al., 2022) — Background for modern MoE models.

## Inference & Serving

- **Efficient Memory Management for Large Language Model Serving with PagedAttention** (Kwon et al., 2023) — vLLM.
- **Speculative Decoding** (Leviathan et al., 2022; Chen et al., 2023) — Making autoregressive generation faster.
- **Mixture of Depths** (Raposo et al., 2024) — Dynamic compute allocation across tokens.

## Evaluation

- **Holistic Evaluation of Language Models (HELM)** (Liang et al., 2022) — Broad, structured benchmark methodology.
- **Judging LLM-as-a-Judge** (Zheng et al., 2023) — The MT-Bench paper; sets the methodology for LLM judging.
- **Who Validates the Validators?** (Shankar et al., 2024) — Calibrating LLM evaluators.

## Multimodal

- **Learning Transferable Visual Models From Natural Language Supervision** (Radford et al., 2021) — CLIP.
- **Flamingo** (Alayrac et al., 2022) — Few-shot visual language models.
- **LLaVA: Visual Instruction Tuning** (Liu et al., 2023) — Open-source visual instruction tuning.

## Safety & Red Teaming

- **Red Teaming Language Models with Language Models** (Perez et al., 2022) — Scaling red teaming.
- **Prompt Injection Attacks and Defenses** (Greshake et al., 2023) — The indirect prompt injection threat model.
- **Universal and Transferable Adversarial Attacks on Aligned Language Models** (Zou et al., 2023) — Gradient-based jailbreaks.

## Where to find more

- **[arxiv-sanity-lite](https://arxiv-sanity-lite.com/)** — Filters the arXiv firehose.
- **[Hugging Face Daily Papers](https://huggingface.co/papers)** — Community-curated recent papers.
- **[The Gradient](https://thegradient.pub/)** — Accessible deep dives.
- **[AK's Twitter](https://twitter.com/_akhaliq)** — Daily paper highlights.

## How to read a paper

If you're new to academic papers, a workable approach:

1. **Read the abstract and conclusion first.** Decide if it's worth more time.
2. **Look at the figures.** They often tell the story better than the text.
3. **Skip to the experiments.** What did they actually measure, and on what?
4. **If still worthwhile, read the method.** What's the actual technique?
5. **Skim related work.** See what this builds on.

Most papers don't need a full read. One or two that shape your thinking do.
