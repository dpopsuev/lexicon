---
id: structured-logging
title: "Structured Logging"
description: "Machine-readable logs with unified field schema, scoped loggers, and severity levels. No free-text fmt.Println."
labels: [pragmatic, practice, observability, logging]
always_apply: true
---

# Structured Logging

**Every log entry is a machine-readable record with typed fields. No free-text strings. No fmt.Println. No log.Printf.**

## The Rules

1. **Structured, not strings.** Every log call uses key-value fields, not interpolated strings.
2. **Unified field schema.** Same key names across all packages, all services, all languages.
3. **Scoped loggers.** Every component gets a logger with its name baked in.
4. **Severity levels.** ROGYB: Warn for failures (Orange), Info/Debug for success (Yellow), Error for unrecoverable.
5. **Accept logger from caller.** Libraries take a logger parameter, default to the stdlib default.
6. **No sensitive data.** No passwords, tokens, PII in logs. Ever.

## Per-Language Idioms

### Go — slog (stdlib, Go 1.21+)

```go
// Scoped logger with component name
log := slog.With("component", "mirage")

// Structured fields, not string interpolation
log.Info("space created",
    slog.String("workspace", spec.Workspace),
    slog.String("backend", string(spec.Backend)),
    slog.Duration("elapsed", elapsed),
)

// NEVER this
fmt.Printf("created space for %s using %s in %v\n", spec.Workspace, spec.Backend, elapsed)

// Library accepts caller's logger
type Config struct {
    Logger *slog.Logger  // optional, defaults to slog.Default()
}
```

### Python — structlog

```python
import structlog
log = structlog.get_logger(component="mirage")

# Structured
log.info("space_created", workspace=spec.workspace, backend=spec.backend)

# NEVER this
print(f"created space for {spec.workspace}")
```

### Rust — tracing

```rust
use tracing::{info, warn};

info!(workspace = %spec.workspace, backend = %spec.backend, "space created");
```

### TypeScript — pino

```typescript
const log = pino({ component: 'mirage' });
log.info({ workspace: spec.workspace, backend: spec.backend }, 'space created');
```

## Key Constants (avoid raw strings)

Define constants for all field keys in one place:

```go
// Go: package logkeys
const (
    KeyComponent = "component"
    KeyWorkspace = "workspace"
    KeyBackend   = "backend"
    KeyError     = "error"
    KeyElapsed   = "elapsed"
    KeyAgentID   = "agent_id"
    KeyToolName  = "tool"
)
```

If the same key appears in two packages with different spellings, it's a bug.

## ROGYB Alignment

| Phase | Level | When | Example |
|---|---|---|---|
| **Orange** | Warn, Error | Before Green — problem signals | `log.Warn("space create failed", "error", err)` |
| **Yellow** | Info, Debug | After Green — success signals | `log.Info("space created", "elapsed", d)` |

## Anti-Patterns

- `fmt.Println("error: " + err.Error())` — unstructured, invisible to log aggregation
- `log.Info("failed to create space")` — no context (which workspace? which backend? what error?)
- `log.Debug("x=%v y=%v z=%v", x, y, z)` — positional, not key-value
- `log.Info("password=" + password)` — PII in logs
- Each package defining its own key strings — inconsistent schema

## Complements

- `pillars/observability` — ODD: structured logging is the foundation
- `pillars/testing` — ROGYB: Orange + Yellow are logging phases
- `languages/effective-go` — §17: slog with consistent field keys

**Reference:** https://openobserve.ai/blog/structured-logging-best-practices/
