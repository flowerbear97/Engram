# Lifecycle Management

Neuragram provides built-in tools for managing memory lifecycle: expiration, archival, forgetting, and versioning.

## TTL Expiration

Set `expires_at` on memories that should auto-expire:

```python
from datetime import datetime, timedelta, timezone

mem.remember(
    "Meeting at 3pm today",
    user_id="u1",
    type="episode",
    expires_at=datetime.now(timezone.utc) + timedelta(hours=6),
)
```

Expired memories are marked `status=expired` during decay runs. They are excluded from search results but retained in the database for auditing.

## Inactivity Archival

Memories not accessed (via `recall()` or `get()`) for a configurable period are automatically archived:

```python
# Archive memories untouched for 30+ days
result = mem.decay(max_age_days=30)
print(f"Expired: {result.expired_count}, Archived: {result.archived_count}")
```

!!! note
    `recall()` automatically "touches" returned memories, updating their `last_accessed_at` timestamp. This creates a natural "use it or lose it" reinforcement.

## GDPR Forgetting

Remove all data for a specific user:

```python
# Soft delete (marks as deleted, retains record)
count = mem.forget(user_id="u1")

# Hard delete (permanently removes from database)
count = mem.forget(user_id="u1", hard=True)
```

## Version History

Every `update()` call creates a new version. View the full history:

```python
mid = mem.remember("User uses VS Code", user_id="u1")

# Update the memory
mem.update(mid, content="User switched to Cursor")

# View all versions
for version in mem.history(mid):
    print(f"v{version.version}: {version.content} (at {version.updated_at})")
```

## Background Worker

Automate lifecycle tasks with a background worker:

```python
worker = mem.create_worker(interval_seconds=3600)
await worker.start()

# Worker runs every hour:
# 1. Expire memories past their TTL
# 2. Archive inactive memories
# 3. (Future) Run merging and consolidation

await worker.stop()  # graceful shutdown
```

## Statistics

Get a snapshot of your memory store:

```python
stats = mem.stats()
print(f"Total: {stats.total}")
print(f"Active: {stats.active}")
print(f"Archived: {stats.archived}")
print(f"By type: {stats.by_type}")
```
