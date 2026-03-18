---
id: go-best-practices
title: Go Best Practices
description: Go-specific code hygiene rules for consistency, debuggability, and maintainability.
labels: [go, code-hygiene, best-practices]
---

# Go Best Practices

**Every Go file must follow these conventions.**

## Constants over embedded strings

Named constants for any string that appears in a switch case, comparison, log message, or error. If the same string appears twice, it needs a constant.

```go
// Good
const LogNodeEnter = "node enter"
slog.Debug(LogNodeEnter, LogKeyComponent, LogComponentWalk)

// Bad
slog.Debug("node enter", "component", "walk")
```

Group related constants by domain:

```go
const (
    LogComponentWalk      = "walk"
    LogComponentDSL       = "dsl"
    LogComponentCalibrate = "calibrate"
)
```

## Structured logging (slog)

Use `slog` with consistent field keys defined as constants. Every function that crosses a boundary (I/O, external call, state mutation) gets a log line.

```go
slog.Debug(LogNodeEnter,
    LogKeyComponent, LogComponentWalk,
    LogKeyNode, node.Name(),
    LogKeyWalker, walkerName)
```

Rules:
- `slog.Debug` for decision points (edge taken, loop incremented)
- `slog.Info` for lifecycle events (run start, run complete)
- `slog.Warn` for recoverable failures (case failed but others continue)
- `slog.Error` for unrecoverable failures (all cases failed)

## Modern stdlib APIs

Use modern Go APIs over legacy equivalents:

| Use | Instead of |
|-----|-----------|
| `slices.Contains(s, v)` | Manual for loop |
| `maps.Copy(dst, src)` | Manual for loop |
| `filepath.WalkDir` | `filepath.Walk` |
| `strings.Cut` | `strings.SplitN(s, sep, 2)` |
| `errors.Is` / `errors.As` | Type assertion on errors |
| `context.WithTimeout` | Manual timer + select |

## Error handling

- Wrap with context: `fmt.Errorf("node %s: %w", name, err)`
- Define package-level sentinels: `var ErrNodeNotFound = errors.New("node not found")`
- Never swallow errors silently. If `_` discards an error, add a comment explaining why.
- Return early on error — no nested if/else chains.

## Stdlib first

Before adding a dependency, check if stdlib solves it:
- `encoding/json` not a third-party serializer
- `net/http` not a framework
- `container/list` not an LRU library
- `testing/fstest.MapFS` not a mock filesystem library

Only add deps when stdlib is genuinely insufficient. Document the decision.

## Interface design

- Small, role-specific interfaces (1-3 methods)
- Define interfaces where they're consumed, not where they're implemented
- Use `io.Reader`, `fs.FS`, `context.Context` from stdlib
- Verify implementations: `var _ Interface = (*Impl)(nil)`

## Testing conventions

- Table-driven tests for multiple cases
- `testdata/` for YAML/JSON fixtures
- `t.Helper()` in all test helpers
- `t.Skipf()` for optional tests (requires external tools)
- `t.TempDir()` for test artifacts (auto-cleaned)

## Complements

- `solid-principles` — dependency inversion, single responsibility
- `hexagonal-architecture` — domain in libraries, thin binaries
- `testing-methodology` — ROGYB test layers
