---
name: local-materialized-view
description: Identity Map + Unit of Work + Optimistic Offline Lock for API-backed agent workflows
labels: [architecture, patterns, caching, agents]
priority: 30
---

# Local Materialized View

Three Fowler patterns (PoEAA, 2002) composed into a single agent-friendly data layer.

## The three patterns

| Pattern | Role | Agent equivalent |
|---------|------|-----------------|
| **Identity Map** | Each entity loaded once, looked up by key | Local store keyed by ref — no duplicate fetches |
| **Unit of Work** | Track dirty objects, flush as single transaction | Batch push — coalesce mutations into minimal API calls |
| **Optimistic Offline Lock** | Version check before write, detect stale data | Compare snapshot versions on push, surface conflicts |

## When to apply

- Agent holds API data locally while reasoning over it (minutes, not milliseconds)
- Multiple mutations to the same resource before writing back
- Cross-backend workflows where data from Jira, GitHub, RP lives in the same working set
- Offline resilience — agent continues working if upstream is temporarily unreachable

## Architecture (CQRS-lite)

```
Pull (read path)              Push (write path)
     │                              │
     ▼                              ▼
┌──────────┐  get/mutate   ┌──────────────┐
│ API      │──────────────>│ ViewStore    │
│ Adapter  │               │ (Identity   │
│          │<──────────────│  Map +      │
└──────────┘  batch push   │  Unit of    │
                           │  Work)      │
                           └──────────────┘
                                  │
                           DirtyTracker
                           marks changed
                           fields
```

**Pull**: Fetch from adapter, store structured (not blob). Each ref maps to exactly one ViewRecord.

**Work locally**: Agent reads and mutates the local copy. DirtyTracker marks which fields changed.

**Push**: Unit of Work flushes only dirty fields. One PUT per resource, not one per field. Optimistic lock checks version before overwriting.

## Implementation rules

1. **ViewRecord stores structured fields, not serialized blobs.** The agent must be able to read individual fields without deserializing the whole response.

2. **Mutations are field-level, not replace-all.** `mutate(ref, "status", "done")` marks only `status` as dirty. Other fields untouched.

3. **Push is idempotent.** If push fails mid-batch, re-pushing sends the same mutations. No partial state corruption.

4. **Conflict = dirty field also changed remotely.** Non-overlapping changes merge cleanly. Overlapping changes surface as conflict — agent decides (force, re-pull, merge).

5. **The view is disposable.** Like a materialized view in a database — can be rebuilt from source at any time. Never the source of truth.

## Anti-patterns

- **Caching responses as opaque blobs** — defeats the purpose; agent can't inspect fields without full deserialization
- **Write-through on every mutation** — eliminates the batching benefit; one API call per field change
- **Infinite TTL without version check** — stale data served indefinitely; optimistic lock exists to prevent this
- **Treating the view as source of truth** — it's a replica, not the master; always re-pullable

## Related patterns

- **Materialized View** (Azure/cloud) — server-side pre-computed read model
- **CQRS** — formal read/write separation at architecture level
- **Event Sourcing** — append-only event log as write model (heavier than needed for agent use)
- **Repository pattern** — the adapter layer that ViewStore delegates to for actual API calls

## References

- Martin Fowler, *Patterns of Enterprise Application Architecture* (2002)
  - [Identity Map](https://martinfowler.com/eaaCatalog/identityMap.html)
  - [Unit of Work](https://martinfowler.com/eaaCatalog/unitOfWork.html)
  - [Optimistic Offline Lock](https://martinfowler.com/eaaCatalog/optimisticOfflineLock.html)
- [CQRS — Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs)
- [Materialized View — Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/patterns/materialized-view)
