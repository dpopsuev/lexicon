---
id: effective-go
title: Effective Go
description: Go-specific conventions for type safety, error discipline, concurrency, and code hygiene. Derived from go.dev/doc/effective_go and pthethanh/effective-go.
labels: [go, code-hygiene, best-practices, type-safety]
---

# Effective Go

**Every Go file must follow these conventions.**

## 1. Named types over raw primitives

Define named types for domain identifiers. Never pass raw `string` where a domain type exists. The compiler catches swapped arguments, typos, and cross-domain mixing.

```go
// Good — compiler catches swapped args
type Role string
type ScopePath string
type AgentID string
func Spawn(role Role, scope ScopePath, id AgentID) {}

// Bad — any string accepted, bugs hide at runtime
func Spawn(role string, scope string, id string) {}
```

Rules:
- Every domain identifier gets a named type: IDs, roles, paths, event kinds, tool names.
- Typed maps over raw maps: `type Capabilities map[ToolName]bool` not `map[string]bool`.
- Typed slices when semantics matter: `type History []Entry` not `[]Entry`.
- Enums with `iota` and a `String()` method, not raw strings.
- Use constants of the named type, not untyped string constants.

```go
// Good — typed enum with exhaustive switch
type NodeState int
const (
    NodeIdle NodeState = iota
    NodeActive
    NodeDone
    NodeError
)

// Good — string enum when values are wire-format
type EventKind string
const (
    EventOutput   EventKind = "output"
    EventThinking EventKind = "thinking"
)

// Bad — raw string, no exhaustiveness check
const eventOutput = "output"
```

## 2. Constants over embedded literals

Named constants for any string, number, or duration that appears in logic. If the same value appears twice, it needs a constant.

```go
// Good
const (
    roleExecutor   = "executor"
    defaultTimeout = 30 * time.Second
)

// Bad
if role == "executor" { ... }
```

Group related constants by domain. Package-level for shared values, file-level for local values.

## 3. Sentinel errors

Define package-level sentinel errors for programmatic matching. Wrap with context using `%w`.

```go
// Good
var ErrNodeNotFound = errors.New("node not found")
return fmt.Errorf("lookup %s: %w", name, ErrNodeNotFound)

// Bad
return fmt.Errorf("node not found")
```

Rules:
- Never swallow errors silently. If `_` discards an error, add a comment why.
- `errors.Is` / `errors.As` for matching, never string comparison.
- Return early on error — no nested if/else chains.
- Wrap at each layer: `fmt.Errorf("operation: %w", err)`.
- Error strings: lowercase, no punctuation. Enables composition.
- Use multiple returns `(value, error)` — never in-band errors (-1, empty string).

## 4. Control flow

**Switch over if-else-if** when branching on the same variable:
```go
switch role {
case roleExecutor: ...
case roleReviewer: ...
}
```

**Guard clauses / early returns** over deep nesting:
```go
func process(x *Thing) error {
    if x == nil {
        return ErrNilThing
    }
    // main logic at top indent level
}
```

**Maximum nesting depth**: 3 levels. Extract a helper function if deeper.

**Labeled breaks** for nested loops:
```go
Loop:
    for _, item := range items {
        switch item.Kind {
        case kindDone:
            break Loop
        }
    }
```

## 5. Interface design

- Small, role-specific interfaces (1-3 methods ideal).
- Define interfaces where they're consumed, not where they're implemented.
- Use stdlib interfaces: `io.Reader`, `io.Writer`, `fs.FS`, `fmt.Stringer`.
- Verify implementations: `var _ Interface = (*Impl)(nil)`.
- Return concrete types from constructors; let consumers define interfaces.
- Embed interfaces to compose: `type ReadWriter interface { Reader; Writer }`.
- Don't create interfaces "for mocking" — design APIs for real usage.

## 6. Struct design

- **Zero value should be useful**, or require a constructor (`NewFoo()`).
- Group related fields together.
- JSON/YAML tags on all exported struct fields (snake_case for JSON).
- Unexported by default — only export what the API contract requires.
- Use embedding for composition, not inheritance.
- Receiver is the inner type, not the outer — know this when embedding.
- Don't copy structs containing mutexes, slices, or maps without understanding aliasing.

```go
// Good — zero value works
type SyncedBuffer struct {
    mu  sync.Mutex
    buf bytes.Buffer
}

// Good — embedding promotes methods
type Job struct {
    Command string
    *log.Logger
}
job.Println("starting")
```

## 7. Modern stdlib APIs

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
| `crypto/rand` | `math/rand` for security-sensitive values |

## 8. Concurrency

Narrowest primitive wins: `atomic` > `sync.Mutex` > `channel`.

**Share by communicating, not by shared memory.**

```go
// Good — channel for ownership transfer
c := make(chan *Work)
go func() { c <- process(item) }()
result := <-c

// Good — mutex for struct-level state
type Registry struct {
    mu    sync.RWMutex
    items map[string]*Item
}

// Good — atomic for simple counter
var count atomic.Int64
```

Rules:
- `context.Context` as first parameter for cancellation.
- `defer mu.Unlock()` immediately after `mu.Lock()`.
- Never hold locks across I/O, channels, or blocking calls.
- `sync.Once` for one-time initialization.
- Make goroutine exit conditions explicit — document when and why they terminate.
- Buffered channels as semaphores for limiting concurrency.

## 9. Resource cleanup

Use `defer` for cleanup — files, locks, connections, cancel functions.

```go
f, err := os.Open(path)
if err != nil { return err }
defer f.Close()

ctx, cancel := context.WithTimeout(ctx, timeout)
defer cancel()
```

Defers run LIFO. Arguments evaluated at defer time, not at execution time.

## 10. Collection patterns

**Pre-allocate** when length is known:
```go
out := make([]string, 0, len(items))
```

**Nil slices preferred** over empty literals:
```go
var t []string     // Good: nil slice, len=0, cap=0
t := []string{}    // Avoid: non-nil, zero-length (matters for JSON: null vs [])
```

**Comma-ok idiom** for maps:
```go
if val, ok := cache[key]; ok {
    return val
}
```

**Delete is safe** on missing keys: `delete(m, key)`.

## 11. Function design

- Pass values, not pointers, for small fixed-size types. Don't pass `*string` or `*int`.
- Named result parameters when returning 2+ values of the same type or when meaning is unclear.
- No naked returns except in very short functions.
- Variadic functions: `func Min(values ...int) int`.

## 12. Package design

- Lowercase, single-word names. No underscores, no MixedCaps.
- Package name = directory base name.
- Don't stutter: `bufio.Reader` not `bufio.BufReader`.
- Avoid meaningless names: `util`, `common`, `misc`, `helpers`, `types`.
- Initialisms: all caps or all lowercase — `URL`, `HTTPServer`, not `Url`, `HttpServer`.

## 13. Structured logging (slog)

Use `slog` with consistent field keys as constants.

```go
slog.Debug(logNodeEnter,
    logKeyComponent, logComponentWalk,
    logKeyNode, node.Name())
```

- `slog.Debug` for decision points.
- `slog.Info` for lifecycle events.
- `slog.Warn` for recoverable failures.
- `slog.Error` for unrecoverable failures.

## 14. Stdlib first

Before adding a dependency, check if stdlib solves it:
- `encoding/json` not a third-party serializer.
- `net/http` not a framework.
- `testing` not testify (use `t.Helper()` for custom assertions).
- `testing/fstest.MapFS` not a mock filesystem library.

Only add deps when stdlib is genuinely insufficient. Document the decision.

## 15. Testing conventions

- Table-driven tests for multiple cases.
- `testdata/` for YAML/JSON fixtures.
- `t.Helper()` in all test helpers and assertions.
- `t.Skipf()` for optional tests (requires external tools/daemon).
- `t.TempDir()` for test artifacts (auto-cleaned).
- Contract tests: `var _ Interface = (*Impl)(nil)`.
- `t.Helper()` + `sync.Mutex` in test doubles accessed from multiple goroutines.
- Example functions as runnable documentation.

## Agent Behavior

- **Before completing a task**: scan changed code for violations. Fix before committing.
- **Types**: grep for raw `string` parameters that should be named types. Flag.
- **Constants**: grep for embedded string literals in function bodies. Extract.
- **Errors**: grep for `fmt.Errorf` without `%w` or without a sentinel. Fix.
- **Control flow**: grep for `} else if` chains. Convert to switch.
- **Interfaces**: verify consumer-side definition. Flag producer-side interfaces.

## Complements

- `code-smells` — structural smells; this rule covers mechanical hygiene.
- `solid-principles` — dependency inversion, single responsibility.
- `hexagonal-architecture` — domain in libraries, thin binaries.
- `testing-methodology` — ROGYB test layers.
- `trust-boundaries` — security at every boundary crossing.
- `naming` — Kenneth Reitz naming principles.
