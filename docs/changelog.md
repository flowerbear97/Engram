# Changelog

All notable changes to this project will be documented in this file.

## [1.0.0] - 2025-04-09

### Added
- **Core**: `AgentMemory` facade with `remember()`, `recall()`, `smart_remember()`, `explain()`, `forget()`, `stats()`
- **Storage**: SQLite store with 3-index sync (relational + FTS5 + sqlite-vec), WAL mode, LRU cache
- **Retrieval**: Hybrid search with RRF fusion, recency boost (exponential decay), deduplication
- **Intelligence**: Memory classification, conflict detection (4 resolution strategies), memory merging
- **Lifecycle**: TTL expiration, inactivity archival, GDPR-compliant forgetting, background worker
- **Access Control**: Role-based access with namespace and user scoping
- **Integrations**: MCP server, REST API (FastAPI), LangChain adapter, LlamaIndex adapter, Claude Code plugin
- **Observability**: OpenTelemetry traces and metrics with no-op fallback
- **Multi-tenancy**: Namespace + user_id + agent_id scoping on all operations

### Performance
- Write serialization via `asyncio.Lock` to prevent database locking
- Atomic batch insert with single-transaction rollback
- Schema migration system with version tracking
- Graceful FTS5/sqlite-vec degradation
