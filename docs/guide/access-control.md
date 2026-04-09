# Access Control

Neuragram supports role-based access control (RBAC) for multi-agent environments.

## Access Levels

| Level | Permissions |
|---|---|
| `NONE` | No access |
| `READ` | `recall()`, `get()`, `search()`, `stats()` |
| `WRITE` | READ + `remember()`, `update()`, `smart_remember()` |
| `ADMIN` | WRITE + `forget()`, `decay()`, `delete()` |

## Setup

```python
from neuragram import AgentMemory, AccessPolicy, AccessLevel

# Define an access policy
policy = AccessPolicy(enabled=True, default_level=AccessLevel.NONE)

# Grant access to agents
policy.grant("reader_agent", AccessLevel.READ)
policy.grant("writer_agent", AccessLevel.WRITE, namespace="project_a")
policy.grant("admin_bot", AccessLevel.ADMIN)

# Create memory with policy
mem = AgentMemory(
    db_path="./memory.db",
    access_policy=policy,
    actor_id="writer_agent",  # who is making the calls
)
```

## Namespace Scoping

Grants can be scoped to specific namespaces:

```python
# writer_agent can write to project_a only
policy.grant("writer_agent", AccessLevel.WRITE, namespace="project_a")

# admin can access everything (no namespace restriction)
policy.grant("admin_bot", AccessLevel.ADMIN)
```

## Enforcement

Access is checked before every operation via `_enforce()`. Unauthorized operations raise `AccessError`:

```python
from neuragram.core.exceptions import AccessError

mem = AgentMemory(db_path="./memory.db", access_policy=policy, actor_id="reader_agent")

mem.recall("query", user_id="u1")  # OK — READ allowed
mem.remember("data", user_id="u1")  # raises AccessError — WRITE not allowed
```

## Disabling

Access control is disabled by default (`AccessPolicy.enabled=False`). All operations are permitted when disabled.
