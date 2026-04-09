# Getting Started

## Installation

=== "Core only"

    ```bash
    pip install neuragram
    ```

=== "With OpenAI embeddings"

    ```bash
    pip install neuragram[openai]
    ```

=== "With local embeddings"

    ```bash
    pip install neuragram[local]
    ```

=== "Everything"

    ```bash
    pip install neuragram[all]
    ```

All available extras:

| Extra | What it adds |
|---|---|
| `openai` | OpenAI embedding provider |
| `local` | sentence-transformers (runs locally) |
| `ollama` | Ollama embedding/LLM provider |
| `mcp` | MCP tool server (`neuragram-mcp` CLI) |
| `api` | REST API server (`neuragram-api` CLI) |
| `langchain` | LangChain `BaseMemory` adapter |
| `llamaindex` | LlamaIndex chat memory adapter |
| `telemetry` | OpenTelemetry instrumentation |
| `all` | All of the above |

## Basic Usage

### Remember & Recall

```python
from neuragram import AgentMemory

mem = AgentMemory(db_path="./memory.db")

# Store memories
mem.remember("User is a Python backend developer", user_id="u1", type="fact")
mem.remember("User prefers concise code comments", user_id="u1", type="preference", importance=0.8)
mem.remember("Debugged a Redis connection timeout issue", user_id="u1", type="episode", tags=["redis", "debugging"])

# Recall relevant memories
results = mem.recall("What does the user do?", user_id="u1")
for r in results:
    print(f"[{r.memory.memory_type.value}] {r.memory.content} (score: {r.score:.4f})")

mem.close()
```

### Embedding Providers

By default, Neuragram uses keyword-only retrieval (no embeddings). Add an embedding provider for hybrid vector + keyword search:

```python
# OpenAI embeddings
mem = AgentMemory(
    db_path="./memory.db",
    embedding="openai",
    embedding_model="text-embedding-3-small"
)

# Local embeddings (sentence-transformers, no API key needed)
mem = AgentMemory(db_path="./memory.db", embedding="local")
```

### LLM-Enhanced Features

Add an LLM provider to unlock smart classification, conversation extraction, and conflict resolution:

```python
from neuragram import AgentMemory, CallableLLMProvider

async def my_llm(prompt: str) -> str:
    # Your LLM call here
    return await call_my_llm(prompt)

mem = AgentMemory(
    db_path="./memory.db",
    llm=CallableLLMProvider(my_llm)
)

# Smart remember: auto-classifies type, importance, confidence
ids = mem.smart_remember("User prefers Python over JavaScript")
```

!!! note
    All LLM-powered features gracefully fall back to rule-based logic when no LLM is configured.

### Async API

Every method has an async counterpart prefixed with `a`:

```python
import asyncio
from neuragram import AgentMemory

async def main():
    mem = AgentMemory(db_path="./memory.db")
    await mem.aremember("async memory", user_id="u1")
    results = await mem.arecall("query", user_id="u1")
    await mem.aclose()

asyncio.run(main())
```

## Next Steps

- [Hybrid Search & Retrieval](guide/retrieval.md) — understand the search pipeline
- [Memory Intelligence](guide/intelligence.md) — smart classification, conflict detection, merging
- [Lifecycle Management](guide/lifecycle.md) — TTL, archival, GDPR forgetting
- [Integrations](integrations/claude-code.md) — Claude Code, MCP, REST API, LangChain, LlamaIndex
