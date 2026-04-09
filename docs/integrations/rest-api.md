# REST API

Neuragram includes a FastAPI-based HTTP server for language-agnostic access.

## Setup

```bash
pip install neuragram[api]
neuragram-api --db-path ./memory.db --port 8080
```

## Endpoints

### Memory Operations

| Method | Path | Description |
|---|---|---|
| `POST` | `/memories` | Create a new memory |
| `GET` | `/memories/{id}` | Get a memory by ID |
| `PUT` | `/memories/{id}` | Update a memory |
| `DELETE` | `/memories/{id}` | Delete a memory |
| `POST` | `/memories/batch` | Batch insert memories |

### Search

| Method | Path | Description |
|---|---|---|
| `POST` | `/recall` | Search memories by query |
| `POST` | `/explain` | Search with score explanations |

### Lifecycle

| Method | Path | Description |
|---|---|---|
| `POST` | `/forget` | GDPR-compliant user data removal |
| `POST` | `/decay` | Run decay (TTL + archival) |
| `GET` | `/memories/{id}/history` | Get version history |

### Stats

| Method | Path | Description |
|---|---|---|
| `GET` | `/stats` | Memory store statistics |

## Examples

### Create a Memory

```bash
curl -X POST http://localhost:8080/memories \
  -H "Content-Type: application/json" \
  -d '{
    "content": "User prefers dark mode",
    "user_id": "u1",
    "type": "preference"
  }'
```

### Search

```bash
curl -X POST http://localhost:8080/recall \
  -H "Content-Type: application/json" \
  -d '{
    "query": "user preferences",
    "user_id": "u1",
    "top_k": 5
  }'
```

### Forget User Data

```bash
curl -X POST http://localhost:8080/forget \
  -H "Content-Type: application/json" \
  -d '{"user_id": "u1", "hard": true}'
```
