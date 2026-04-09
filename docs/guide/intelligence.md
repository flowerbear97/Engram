# Memory Intelligence

Neuragram provides intelligent memory processing with a dual-track approach: every feature works with rule-based logic out of the box, and can be enhanced with an LLM.

## Smart Remember

`smart_remember()` auto-classifies memory type, importance, and confidence:

```python
from neuragram import AgentMemory, CallableLLMProvider

# With LLM (higher quality classification)
mem = AgentMemory(db_path="./memory.db", llm=CallableLLMProvider(my_llm))
ids = mem.smart_remember("User prefers Python over JavaScript")

# Without LLM (rule-based classification still works)
mem = AgentMemory(db_path="./memory.db")
ids = mem.smart_remember("User prefers Python over JavaScript")
# → classified as "preference" via keyword detection
```

### Classification Rules (without LLM)

| Memory Type | Triggered by keywords |
|---|---|
| `preference` | "prefer", "like", "want", "favorite" |
| `episode` | "debug", "fixed", "resolved", "yesterday" |
| `procedure` | "step", "process", "how to", "workflow" |
| `plan_state` | "todo", "plan", "goal", "deadline" |
| `fact` | Default when no other pattern matches |

## Conversation Extraction

Extract structured memories from chat messages (requires LLM):

```python
from neuragram.processing.extraction import MemoryExtractor

extractor = MemoryExtractor(llm=my_llm_provider)
memories = await extractor.extract([
    {"role": "user", "content": "I'm a data scientist working with pandas and sklearn"},
    {"role": "assistant", "content": "Got it! I'll tailor my answers for data science workflows."},
])
# → extracts: fact("user is a data scientist"), fact("user works with pandas and sklearn")
```

## Conflict Detection

Detects contradicting memories and resolves them automatically:

```python
from neuragram.processing.conflict import ConflictDetector

detector = ConflictDetector(store=mem._store, llm=my_llm_provider)
conflicts = await detector.detect(user_id="u1")
# e.g., "User prefers Python" vs "User prefers JavaScript"
```

### Resolution Strategies

| Strategy | Behavior |
|---|---|
| `keep_newest` | Keep the most recently updated memory |
| `keep_highest_confidence` | Keep the memory with higher confidence |
| `merge` | Merge both into a combined memory |
| `manual` | Flag for human review |

## Memory Merging

Consolidate similar memories to reduce noise:

```python
from neuragram.processing.merger import MemoryMerger

merger = MemoryMerger(store=mem._store, llm=my_llm_provider)
result = await merger.merge(user_id="u1")
# Clusters similar memories, summarizes each cluster
```

!!! tip
    Run merging periodically via the [background worker](lifecycle.md#background-worker) to keep your memory store clean.
