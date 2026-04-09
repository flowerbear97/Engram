# MCP Server

Neuragram provides an MCP (Model Context Protocol) server that exposes memory tools to any MCP-compatible client (Claude Desktop, Cursor, etc.).

## Setup

```bash
pip install neuragram[mcp]
neuragram-mcp --db-path ./memory.db
```

With embeddings:

```bash
pip install neuragram[mcp,openai]
neuragram-mcp --db-path ./memory.db --embedding openai
```

## Tools

The MCP server exposes 6 tools:

### `neuragram_remember`

Store a new memory.

**Parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `content` | string | yes | The memory content |
| `user_id` | string | no | User identifier |
| `type` | string | no | Memory type (fact, episode, preference, procedure, plan_state) |
| `importance` | float | no | Importance score [0, 1] |
| `tags` | list[string] | no | Tags for categorization |

### `neuragram_recall`

Search memories by natural language query.

**Parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `query` | string | yes | Search query |
| `user_id` | string | no | Scope to user |
| `top_k` | int | no | Max results (default: 10) |

### `neuragram_smart_remember`

Store with auto-classification of type, importance, and confidence.

**Parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `content` | string | yes | The memory content |
| `user_id` | string | no | User identifier |

### `neuragram_forget`

Remove memories for a user.

**Parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `user_id` | string | yes | User whose memories to remove |
| `hard` | bool | no | Permanently delete (default: false) |

### `neuragram_list`

List recent memories.

**Parameters:**

| Name | Type | Required | Description |
|---|---|---|---|
| `user_id` | string | no | Scope to user |
| `limit` | int | no | Max results (default: 20) |

### `neuragram_stats`

Show memory store statistics.

## Client Configuration

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "neuragram": {
      "command": "neuragram-mcp",
      "args": ["--db-path", "./memory.db"]
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "neuragram": {
      "command": "neuragram-mcp",
      "args": ["--db-path", "./memory.db"]
    }
  }
}
```
