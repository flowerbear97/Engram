# LlamaIndex

Neuragram provides a LlamaIndex chat memory adapter for integration with LlamaIndex agents and chat engines.

## Setup

```bash
pip install neuragram[llamaindex]
```

## Usage

```python
from neuragram.integrations.llamaindex import NeuragramChatMemory

memory = NeuragramChatMemory(db_path="./memory.db", user_id="u1")

# Store memories
memory.put("User is a Python developer", memory_type="fact")
memory.put("User prefers type hints", memory_type="preference")

# Retrieve relevant memories
results = memory.get("programming language")
for r in results:
    print(r)

# Get all memories
all_memories = memory.get_all()

# Reset (clear all memories for this user)
memory.reset()
```

## Configuration

```python
memory = NeuragramChatMemory(
    db_path="./memory.db",
    user_id="u1",
    embedding="local",    # enable hybrid search with local embeddings
    top_k=5,              # max results per query
)
```
