# LangChain

Neuragram provides a LangChain `BaseMemory` adapter for drop-in integration with LangChain chains and agents.

## Setup

```bash
pip install neuragram[langchain]
```

## Usage

```python
from neuragram.integrations.langchain import NeuragramMemory

memory = NeuragramMemory(db_path="./memory.db", user_id="u1")

# Save context from a conversation
memory.save_context(
    {"input": "I prefer concise answers"},
    {"output": "Got it! I'll keep responses brief."}
)

# Load relevant memories
result = memory.load_memory_variables({"input": "answer style"})
print(result["history"])
```

## With LangChain Chains

```python
from langchain.chains import ConversationChain
from langchain_openai import ChatOpenAI

llm = ChatOpenAI()
memory = NeuragramMemory(db_path="./memory.db", user_id="u1")

chain = ConversationChain(llm=llm, memory=memory)
chain.run("What do you know about my preferences?")
```

## Configuration

```python
memory = NeuragramMemory(
    db_path="./memory.db",
    user_id="u1",
    embedding="openai",           # enable hybrid search
    memory_key="history",         # key in memory_variables dict
    return_messages=False,        # return as string (True for message objects)
)
```
