# Hybrid Search & Retrieval

Neuragram combines three retrieval signals into a single ranked result set using Reciprocal Rank Fusion (RRF).

## Search Pipeline

```
Query → [Vector Search] → rank list ─┐
      → [Keyword Search] → rank list ─┤→ RRF Fusion → Recency Boost → Dedup → Results
      → [Recency Score]  → weights ───┘
```

### Vector Search

When an embedding provider is configured, queries are embedded and compared against stored memory vectors using cosine similarity via sqlite-vec.

### Keyword Search

FTS5 full-text search with BM25 ranking. Works even without embeddings — this is the default retrieval mode.

### Recency Boost

Exponential decay based on memory age:

```
boost = 0.5 ^ (age_days / half_life_days)
```

Default half-life is 7 days. A memory accessed yesterday gets ~0.91; one from a month ago gets ~0.06.

## Configuration

```python
mem = AgentMemory(
    db_path="./memory.db",
    embedding="openai",
    retrieval_vector_weight=0.5,    # weight for vector similarity
    retrieval_keyword_weight=0.3,   # weight for keyword match
    retrieval_recency_weight=0.2,   # weight for recency
    recency_half_life_days=7.0,     # half-life for recency decay
    dedup_threshold=0.95,           # similarity threshold for dedup
    retrieval_top_k=10,             # max results
)
```

## Explainable Retrieval

Use `explain()` to get a full score breakdown for each result:

```python
for exp in mem.explain("user preferences", user_id="u1"):
    print(exp["summary"])
    # "vector rank #1 (+0.0082) | keyword rank #3 (+0.0048) | recency 0.95 (age 0.5d)"
    print(exp["score_explanation"])
    # ScoreExplanation(vector_rank=1, keyword_rank=3, rrf_score=0.013, recency_factor=0.95, ...)
```

## Filtering

Scope searches with `MemoryFilter`:

```python
from neuragram.core.filters import MemoryFilter

results = mem.recall(
    "debugging",
    user_id="u1",
    filter=MemoryFilter(
        types=["episode"],
        tags=["redis"],
        min_importance=0.5,
    )
)
```

## Graceful Degradation

| Component | Available | Unavailable |
|---|---|---|
| sqlite-vec | Vector search enabled | Vector search skipped, keyword + recency only |
| FTS5 | Keyword search enabled | Keyword search skipped, vector + recency only |
| Both missing | — | Returns empty results (no search possible) |

Neuragram detects availability at startup and adjusts automatically.
