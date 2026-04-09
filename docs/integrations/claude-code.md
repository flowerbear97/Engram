# Claude Code

Neuragram integrates with Claude Code as a plugin, giving Claude persistent memory across sessions.

## Installation

=== "Plugin Marketplace"

    ```bash
    claude plugin marketplace add flowerbear97/neuragram
    claude plugin install neuragram
    ```

=== "Manual MCP"

    ```bash
    pip install neuragram[mcp]
    claude mcp add neuragram -- neuragram-mcp --db-path ./memory.db
    ```

=== "With OpenAI embeddings"

    ```bash
    pip install neuragram[mcp,openai]
    claude mcp add neuragram -- neuragram-mcp --db-path ./memory.db --embedding openai
    ```

## Available Tools

Once installed, Claude Code gains 6 memory tools:

| Tool | Description |
|---|---|
| `neuragram_remember` | Store a new memory |
| `neuragram_recall` | Search memories by query |
| `neuragram_smart_remember` | Store with auto-classification |
| `neuragram_forget` | Remove memories (by user or filter) |
| `neuragram_list` | List recent memories |
| `neuragram_stats` | Show memory statistics |

## How It Works

Claude Code connects to Neuragram via MCP (Model Context Protocol). The MCP server runs as a subprocess alongside Claude Code, communicating over stdio. Memories are stored in a local SQLite database — no data leaves your machine.

## Configuration

The MCP server supports these flags:

```bash
neuragram-mcp \
    --db-path ./memory.db \       # database location
    --embedding openai \           # embedding provider (optional)
    --embedding-model text-embedding-3-small  # model name
```
