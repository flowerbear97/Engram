# API Reference

## AgentMemory

The primary interface to Neuragram. All user-facing operations go through this class.

```python
from neuragram import AgentMemory
```

### Constructor

```python
AgentMemory(
    store="sqlite",                # backend name or BaseMemoryStore instance
    db_path="./neuragram.db",      # database path (SQLite)
    embedding="none",              # "none", "local", "openai", "ollama", or BaseEmbeddingProvider
    embedding_model="",            # model identifier for embedding provider
    embedding_dimension=384,       # vector dimensionality
    llm=None,                      # LLM provider for smart features (optional)
    llm_model="",                  # model identifier for LLM
    access_policy=None,            # AccessPolicy instance (optional)
    actor_id="",                   # identity for access control checks
    config=None,                   # NeuragramConfig (overrides individual params)
)
```

### Core Methods

#### `remember(content, **kwargs) → str`

Store a new memory. Returns the memory ID.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `content` | str | required | Memory content |
| `user_id` | str | `""` | User identifier |
| `agent_id` | str | `""` | Agent identifier |
| `namespace` | str | `""` | Namespace for isolation |
| `type` | str | `"fact"` | Memory type |
| `importance` | float | `0.5` | Importance [0, 1] |
| `tags` | list[str] | `[]` | Tags for categorization |
| `metadata` | dict | `{}` | Arbitrary key-value pairs |
| `source` | str | `""` | Provenance |
| `expires_at` | datetime | `None` | TTL expiration |

#### `recall(query, **kwargs) → list[ScoredMemory]`

Search memories by query. Returns ranked results.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `query` | str | required | Search query |
| `user_id` | str | `""` | Scope to user |
| `namespace` | str | `""` | Scope to namespace |
| `top_k` | int | config | Max results |
| `filter` | MemoryFilter | `None` | Additional filters |

#### `smart_remember(content, **kwargs) → list[str]`

Store with auto-classification. Returns list of memory IDs.

#### `get(memory_id) → Memory | None`

Get a single memory by ID. Returns `None` if not found.

#### `update(memory_id, **kwargs) → bool`

Update a memory. Creates a new version. Returns success.

#### `explain(query, **kwargs) → list[dict]`

Like `recall()` but includes full score breakdown.

#### `forget(user_id, hard=False) → int`

Remove all memories for a user. Returns count of affected memories.

#### `decay(**kwargs) → DecayResult`

Run decay: expire TTL memories and archive inactive ones.

#### `history(memory_id) → list[MemoryVersion]`

Get version history for a memory.

#### `stats(**kwargs) → StoreStats`

Get memory store statistics.

#### `create_worker(interval_seconds=3600) → LifecycleWorker`

Create a background worker for periodic maintenance.

#### `close()`

Close the database connection and release resources.

### Async Methods

Every method above has an async counterpart prefixed with `a`:
`aremember()`, `arecall()`, `asmart_remember()`, `aget()`, `aupdate()`, `aforget()`, `adecay()`, `ahistory()`, `astats()`, `aclose()`.

---

## Data Models

### Memory

```python
@dataclass
class Memory:
    content: str
    memory_type: MemoryType       # fact, episode, preference, procedure, plan_state
    status: MemoryStatus          # active, archived, expired, deleted
    user_id: str
    agent_id: str
    namespace: str
    tags: list[str]
    metadata: dict[str, Any]
    confidence: float             # [0, 1]
    importance: float             # [0, 1]
    version: int
    source: str
    expires_at: datetime | None
    created_at: datetime
    updated_at: datetime
    last_accessed_at: datetime
    id: str                       # UUID hex
```

### ScoredMemory

```python
@dataclass
class ScoredMemory:
    memory: Memory
    score: float                  # combined RRF + recency score
```

### MemoryType

```python
class MemoryType(str, Enum):
    FACT = "fact"
    EPISODE = "episode"
    PREFERENCE = "preference"
    PROCEDURE = "procedure"
    PLAN_STATE = "plan_state"
```

### MemoryStatus

```python
class MemoryStatus(str, Enum):
    ACTIVE = "active"
    ARCHIVED = "archived"
    EXPIRED = "expired"
    DELETED = "deleted"
```

---

## Configuration

### NeuragramConfig

```python
@dataclass
class NeuragramConfig:
    store: str = "sqlite"
    db_path: str = "./neuragram.db"
    embedding: str = "none"
    embedding_model: str = ""
    embedding_dimension: int = 384
    embedding_options: dict = field(default_factory=dict)
    retrieval_top_k: int = 10
    retrieval_vector_weight: float = 0.5
    retrieval_keyword_weight: float = 0.3
    retrieval_recency_weight: float = 0.2
    recency_half_life_days: float = 7.0
    dedup_threshold: float = 0.95
    decay_max_age_days: int = 30
    decay_ttl_enabled: bool = True
```

---

## Access Control

### AccessLevel

```python
class AccessLevel(Enum):
    NONE = 0
    READ = 1
    WRITE = 2
    ADMIN = 3
```

### AccessPolicy

```python
class AccessPolicy:
    enabled: bool = False
    default_level: AccessLevel = AccessLevel.ADMIN

    def grant(actor_id: str, level: AccessLevel, namespace: str = "") → None
    def revoke(actor_id: str) → None
    def get_level(actor_id: str, namespace: str = "") → AccessLevel
```

---

## Exceptions

All exceptions inherit from `NeuragramError`:

| Exception | Raised when |
|---|---|
| `NeuragramError` | Base exception |
| `ConfigError` | Invalid configuration |
| `StoreError` | Database operation failure |
| `AccessError` | Insufficient permissions |
| `EmbeddingError` | Embedding provider failure |
