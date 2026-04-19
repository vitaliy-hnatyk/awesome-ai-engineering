# Retrieval-Augmented Generation (RAG)

RAG is the architecture of connecting an LLM to an external knowledge source at query time. It's the most common pattern in production LLM systems because it addresses two core weaknesses of foundation models: stale knowledge and lack of access to private data.

## When RAG is the right tool

RAG makes sense when:

- You need the model to work with content that wasn't in training data (internal docs, recent information, user-specific data).
- The corpus is too large to fit in context, or it's not economical to pass in full every query.
- You need citations or provenance for outputs.
- The corpus updates frequently — re-indexing is cheap; re-training isn't.

RAG is *not* the right tool when:

- The task requires deep reasoning over the entire corpus (use long-context or agentic search).
- Accuracy requirements exceed what retrieval can reliably deliver (consider fine-tuning or hybrid approaches).
- The "knowledge" is actually a skill or style (fine-tuning fits better).

## Anatomy of a RAG system

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   Documents  │─────▶│  Chunking &  │─────▶│    Vector    │
│              │      │  Embedding   │      │   Database   │
└──────────────┘      └──────────────┘      └──────────────┘
                                                    │
                                                    ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│     User     │─────▶│   Retrieval  │◀─────│  Query Embed │
│    Query     │      │              │      │              │
└──────────────┘      └──────┬───────┘      └──────────────┘
                             │
                             ▼
                      ┌──────────────┐      ┌──────────────┐
                      │   Re-rank    │─────▶│     LLM      │─────▶ Answer
                      │ (optional)   │      │   + Context  │
                      └──────────────┘      └──────────────┘
```

Each stage has its own failure modes. Debugging production RAG is mostly about figuring out which stage is breaking.

## Ingestion

### Chunking

Chunking strategy has outsized impact on retrieval quality. Common approaches:

- **Fixed-size chunks** (e.g., 512 tokens with 50-token overlap) — Simple, works as a baseline.
- **Recursive / structural** — Split on paragraphs, then sentences, then characters. Better respects document structure.
- **Semantic chunking** — Use embeddings to find natural topic boundaries. Slower but often higher-quality.
- **Document-aware** — Split by markdown headers, code blocks, or document sections. Best when you know the structure.

Rule of thumb: start simple (recursive, 500–1000 tokens, modest overlap), measure, then optimize.

### Embeddings

The embedding model determines the ceiling of your retrieval quality. Current strong choices:

- **OpenAI `text-embedding-3-large`** — Strong general-purpose, good multilingual.
- **Cohere `embed-v3`** — Strong on retrieval benchmarks, good multilingual.
- **Voyage AI** — Specialized models (code, finance, law).
- **BGE / E5 / GTE (open)** — Competitive open-weight options. See the [MTEB leaderboard](https://huggingface.co/spaces/mteb/leaderboard).

### Metadata

Store structured metadata alongside each chunk: source document, section, timestamps, author, access control. You'll use it for filtering during retrieval and for citation at generation time. Add it early — retrofitting metadata into an existing index is painful.

## Retrieval

### Dense retrieval

Embedding-based semantic search. Good at capturing meaning, bad at exact matches (SKU numbers, proper nouns, rare terminology).

### Sparse retrieval

Classical BM25 / TF-IDF. Good at exact matches, bad at paraphrases. Still competitive — don't dismiss it.

### Hybrid retrieval

Run both dense and sparse, combine the scores (Reciprocal Rank Fusion is the standard technique). Almost always beats either alone. Most production systems use hybrid.

### Re-ranking

After initial retrieval of 20–100 candidates, use a more expensive cross-encoder model to re-rank the top results. Cohere's Rerank, BGE-reranker, and ColBERT are common choices. A re-ranker can dramatically improve precision at top-k.

### Query transformation

The user's query often isn't optimized for retrieval. Techniques:

- **HyDE** — Generate a hypothetical answer, embed *that*, retrieve against it.
- **Query expansion** — Generate multiple paraphrases and retrieve with all of them.
- **Decomposition** — Break complex questions into sub-queries.
- **Step-back prompting** — Ask a more abstract question first to improve retrieval context.

## Generation

### Context formatting

How you format retrieved chunks in the prompt matters:

- Include clear delimiters between chunks.
- Include metadata (source, section) so the model can cite.
- Put the most relevant chunk last — models pay more attention to recent context ("lost in the middle" effect).
- Consider labeling chunks with retrieval scores so the model can weight them.

### Preventing hallucination

- Instruct the model to answer *only* from the provided context, and to say "I don't know" when the context is insufficient.
- Require citations — make the model quote or reference specific chunks.
- Use a separate verification pass to check claims against sources.

### Streaming citations

For UIs, emit sources as soon as retrieval completes — before generation finishes — so users see where information is coming from.

## Evaluation

RAG has two distinct quality dimensions that should be measured separately:

### Retrieval quality

- **Context precision** — Of the retrieved chunks, what fraction are relevant?
- **Context recall** — Of the chunks needed to answer, what fraction did we retrieve?
- **MRR / NDCG** — Standard IR metrics if you have labeled relevance.

### Generation quality

- **Faithfulness** — Does the answer stay grounded in retrieved context?
- **Answer relevance** — Does the answer address the question asked?
- **Citation accuracy** — Do cited sources actually support the claims?

Tools: [Ragas](https://github.com/explodinggradients/ragas), [DeepEval](https://github.com/confident-ai/deepeval), [TruLens](https://github.com/truera/trulens).

Build an eval set of 50–200 realistic queries with expected answers *before* iterating on your pipeline. Without this, you're flying blind.

## Advanced patterns

### Contextual retrieval

Before embedding each chunk, prepend a short LLM-generated description of how that chunk fits into the broader document. Anthropic reported ~35% reduction in retrieval failures with this approach. Expensive at index time, cheap at query time.

### Agentic RAG

Instead of a single retrieve-then-generate pass, let the model decide *when* and *how* to search. It can issue multiple queries, reformulate, and explore. More powerful but slower and harder to evaluate.

### Graph RAG

Represent the corpus as a knowledge graph and traverse it at query time. Useful for questions that require connecting information across documents. Microsoft's GraphRAG popularized the pattern.

### Cache-augmented generation

With million-token context windows and prompt caching, sometimes you can skip retrieval entirely and just stuff the whole corpus in context. Only works for small-to-medium corpora but eliminates a lot of complexity.

## Common failure modes

| Symptom | Likely cause |
|---|---|
| Model answers from general knowledge, ignores context | Retrieval returned irrelevant chunks, or prompt doesn't emphasize grounding |
| Correct info exists in corpus but never retrieved | Embedding model weakness, query/document vocabulary mismatch, chunks too large/small |
| Retrieves right chunk but model still hallucinates | Context too long ("lost in middle"), or conflicting chunks |
| Works in English, fails in other languages | Embedding model is English-heavy; use multilingual embeddings |
| Exact match queries (IDs, names) fail | Pure dense retrieval; add sparse/BM25 |
| Retrieval is fast, generation is slow | Consider smaller model, streaming, or caching |

## Further reading

- [Anthropic Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval)
- [RAG Techniques](https://github.com/NirDiamant/RAG_Techniques) — Comprehensive patterns repo.
- [Building RAG from Scratch](https://github.com/langchain-ai/rag-from-scratch)
- [Lost in the Middle](https://arxiv.org/abs/2307.03172) — On long-context attention.
- [Seven Failure Points When Engineering a RAG System](https://arxiv.org/abs/2401.05856)
