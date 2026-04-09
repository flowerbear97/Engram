# Neuragram

**Lightweight, framework-agnostic memory layer for AI agents.**

Built on SQLite + sqlite-vec + FTS5 — no external services required.

---

## Why Neuragram?

Every other agent memory solution requires you to set up external services — vector databases, graph databases, Docker containers, or mandatory LLM API keys. **Neuragram is different.**

- **Zero external deps** — everything runs inside your process, backed by SQLite
- **LLM optional** — rule-based fallback for all intelligent features; add an LLM when you want, not because you must
- **`pip install` and go** — from zero to first memory in under 30 seconds
- **Framework agnostic** — works with LangChain, LlamaIndex, Claude Code, or plain Python

## Quick Start

```bash
pip install neuragram
```

```python
from neuragram import AgentMemory

mem = AgentMemory(db_path="./memory.db")
mem.remember("User prefers concise code explanations", user_id="u1", type="preference")
results = mem.recall("What style does the user prefer?", user_id="u1")
print(results[0].memory.content)
mem.close()
```

## How It Compares

| | Neuragram | Mem0 | Letta | Graphiti |
|---|---|---|---|---|
| Install | `pip install neuragram` | `pip install` | Docker + Server | pip + Neo4j |
| External Deps | **None** | Vector DB + LLM | PG + Server + LLM | Graph DB + LLM |
| Framework Lock-in | **None** | None | Letta runtime | None |
| Memory Lifecycle | **Built-in** | None | Agent self-managed | Partial |
| LLM Required | **No** | Yes | Yes | Yes |

## Feature Highlights

- **Hybrid search** — vector + keyword + recency, fused via Reciprocal Rank Fusion
- **Smart memory** — auto-classification, conflict detection, memory merging
- **Lifecycle** — TTL, archival, GDPR forgetting, background worker
- **Multi-tenancy** — namespace + user + agent scoping with RBAC
- **Integrations** — Claude Code, MCP, REST API, LangChain, LlamaIndex
- **Observability** — OpenTelemetry traces & metrics (zero overhead when not installed)

## Architecture

```
AgentMemory (client.py)
├── Store Layer          SQLite + sqlite-vec + FTS5
├── Retrieval Engine     RRF fusion, recency boost, deduplication
├── Processing           extraction → classification → conflict → merge
├── Lifecycle            decay, forgetting, background worker
├── Access Control       role-based, namespace-scoped
├── Telemetry            OpenTelemetry traces + metrics
└── Integrations         MCP Server, REST API, LangChain, LlamaIndex
```
