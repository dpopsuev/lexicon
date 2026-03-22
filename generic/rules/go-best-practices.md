---
id: go-best-practices
title: Go Best Practices
description: Go-specific code hygiene rules for consistency, debuggability, and maintainability.
labels: [go, code-hygiene, best-practices]
---

# Go Best Practices

**Every Go file must follow these conventions.**

## 1. Constants over embedded literals

Named constants for any string, number, or duration that appears in logic (switch cases, comparisons, map keys, function arguments). If the same value appears twice, it needs a constant.

```go
// Good
const (
    roleExecutor = "executor"
    roleReviewer = "reviewer"
    defaultTimeout = 30 * time.Second
)

// Bad
if role == "executor" { ... }
time.After(30 * time.Second)
```

Group related constants by domain. Package-level for shared values, file-level for local values.

## 2. Sentinel errors

Define package-level sentinel errors for programmatic matching. Wrap with context using `%w`.

```go
// Good
var ErrNodeNotFound = errors.New("node not found")
return fmt.Errorf("lookup %s: %w", name, ErrNodeNotFound)

// Bad
return fmt.Errorf("node not found")
return errors.New("node " + name + " not found")
```

Rules:
- Never swallow errors silently. If `_` discards an error, add a comment explaining why.
- Use `errors.Is` / `errors.As` for matching, never string comparison.
- Return early on error — no nested if/else chains.
- Wrap with context at each layer: `fmt.Errorf("operation: %w", err)`.

## 3. Control flow

**Switch over if-else-if** when branching on the same variable:

```go
// Good
switch role {
case roleExecutor: ...
case roleReviewer: ...
}

// Bad
if role == "executor" { ...
} else if role == "reviewer" { ...
}
```

**Guard clauses / early returns** over deep nesting:

```go
// Good
func process(x *Thing) error {
    if x == nil {
        return ErrNilThing
    }
    // main logic at top indent level
}

// Bad
func process(x *Thing) error {
    if x != nil {
        // 3 levels deep...
    }
    return nil
}
```

**Maximum nesting depth**: 3 levels. Extract a helper function if deeper.

## 4. Modern stdlib APIs

Use modern Go APIs over legacy equivalents:

| Use | Instead of |
|-----|-----------|
| `slices.Contains(s, v)` | Manual for loop |
| `slices.SortFunc(s, cmp)` | `sort.Slice(s, less)` |
| `maps.Copy(dst, src)` | Manual for loop |
| `filepath.WalkDir` | `filepath.Walk` |
| `strings.Cut` | `strings.SplitN(s, sep, 2)` |
| `errors.Is` / `errors.As` | Type assertion on errors |
| `context.WithTimeout` | Manual timer + select |
| `any` | `interface{}` |
| `for range n` | `for i := 0; i < n; i++` (Go 1.22+) |
| `for range s` | `for i := range s` when index unused |

## 5. Concurrency

Narrowest primitive wins: `atomic` > `sync.Mutex` > `channel`.

```go
// Good — atomic for simple counter
var count atomic.Int64
count.Add(1)

// Good — mutex for struct-level state
type Registry struct {
    mu    sync.RWMutex
    items map[string]*Item
}

// Good — channel for signaling between goroutines
done := make(chan struct{})
```

Rules:
- `context.Context` as first parameter for cancellation.
- `defer mu.Unlock()` immediately after `mu.Lock()`.
- Never hold locks across I/O, channel operations, or function calls that might block.
- `sync.Once` for one-time initialization.
- `t.Helper()` + `sync.Mutex` in test doubles that are accessed from multiple goroutines.

## 6. Resource cleanup

Use `defer` for resource cleanup — files, locks, connections, cancel functions.

```go
// Good
f, err := os.Open(path)
if err != nil { return err }
defer f.Close()

ctx, cancel := context.WithTimeout(ctx, timeout)
defer cancel()

mu.Lock()
defer mu.Unlock()
```

Never rely on manual cleanup in every code path — a panic will skip it, `defer` won't.

## 7. Collection patterns

Pre-allocate when length is known:

```go
// Good
out := make([]string, 0, len(items))

// Bad
var out []string // grows via append, multiple allocations
```

Filter as a named function when the pattern repeats:

```go
func (r *Registry) Active() []Item {
    r.mu.RLock()
    defer r.mu.RUnlock()
    var out []Item
    for _, item := range r.items {
        if item.Status == StatusActive {
            out = append(out, item)
        }
    }
    return out
}
```

## 8. Struct design

- Zero value should be useful, or require a constructor (`NewFoo()`).
- Group related fields together.
- JSON/YAML tags on all exported struct fields with consistent naming (snake_case for JSON).
- Unexported by default — only export what the API contract requires.

## 9. Interface design

- Small, role-specific interfaces (1-3 methods).
- Define interfaces where they're consumed, not where they're implemented.
- Use stdlib interfaces: `io.Reader`, `fs.FS`, `context.Context`.
- Verify implementations: `var _ Interface = (*Impl)(nil)`.

## 10. Structured logging (slog)

Use `slog` with consistent field keys defined as constants.

```go
slog.Debug(logNodeEnter,
    logKeyComponent, logComponentWalk,
    logKeyNode, node.Name())
```

Rules:
- `slog.Debug` for decision points (edge taken, loop incremented)
- `slog.Info` for lifecycle events (run start, run complete)
- `slog.Warn` for recoverable failures (case failed but others continue)
- `slog.Error` for unrecoverable failures (all cases failed)

## 11. Stdlib first

Before adding a dependency, check if stdlib solves it:
- `encoding/json` not a third-party serializer
- `net/http` not a framework
- `testing` not testify (custom assertions with `t.Helper()`)
- `testing/fstest.MapFS` not a mock filesystem library

Only add deps when stdlib is genuinely insufficient. Document the decision.

## 12. Testing conventions

- Table-driven tests for multiple cases.
- `testdata/` for YAML/JSON fixtures.
- `t.Helper()` in all test helpers and assertions.
- `t.Skipf()` for optional tests (requires external tools/daemon).
- `t.TempDir()` for test artifacts (auto-cleaned).
- Contract tests for interface compliance (`var _ I = (*T)(nil)`).

## Agent Behavior

- **Before completing a task**: scan changed code for violations of these rules. Fix before committing.
- **Constants**: grep for embedded string literals in function bodies. Extract to file-level or package-level constants.
- **Errors**: grep for `fmt.Errorf("...",` without `%w` or without a sentinel. Fix.
- **Control flow**: grep for `} else if` chains. Convert to switch if same variable.

## Complements

- `code-smells` — structural smells; this rule covers mechanical hygiene.
- `solid-principles` — dependency inversion, single responsibility.
- `hexagonal-architecture` — domain in libraries, thin binaries.
- `testing-methodology` — ROGYB test layers.
