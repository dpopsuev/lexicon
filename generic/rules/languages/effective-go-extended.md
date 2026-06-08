---
id: effective-go-extended
title: Effective Go — Extended Conventions (§11–19)
description: Go conventions for function design, options pattern, package naming, allocation, init, panic, logging, stdlib discipline, and testing. Companion to effective-go.
labels: [go, code-hygiene, best-practices]
---

# Effective Go — Extended Conventions

Companion to `effective-go` (§1–10). Load this rule when working on Go code that involves function signatures, package structure, concurrency teardown, or testing.

## 11. Function design

- Pass values, not pointers, for small fixed-size types. Do not pass `*string` or `*int`.
- Named result parameters when returning 2+ values of the same type or when meaning is unclear.
- No naked returns except in very short functions.
- Variadic functions: `func Min(values ...int) int`.

**Value vs pointer receivers:** Use value receivers for small immutable types; pointer receivers when the method modifies state or the type is large. Do not mix on the same type.

```go
func (c Color) Hex() string { return c.hex }        // value — immutable
func (r *Registry) Add(item Item) { ... }            // pointer — mutates
```

## 12. Functional options

Use the options pattern for constructors with 3+ optional parameters. The default config must work without any options.

```go
type Option func(*Config)

func WithTimeout(d time.Duration) Option {
    return func(c *Config) { c.timeout = d }
}

func New(opts ...Option) *Client {
    cfg := defaultConfig()
    for _, o := range opts { o(&cfg) }
    return &Client{cfg: cfg}
}
```

Rules: option functions are exported; the config struct is not. Each option has one responsibility.

## 13. Package design

- Lowercase, single-word names. No underscores, no MixedCaps.
- Package name equals directory base name.
- Do not stutter: `bufio.Reader` not `bufio.BufReader`.
- Avoid meaningless names: `util`, `common`, `misc`, `helpers`, `types`.
- Initialisms are all caps or all lowercase: `URL`, `HTTPServer`, not `Url`, `HttpServer`.

## 14. Allocation: new vs make

- `new(T)` allocates zeroed memory, returns `*T`. Prefer composite literals instead.
- `make(T, args)` initialises slices, maps, and channels only.

```go
buf := make([]byte, 0, 1024)
m   := make(map[string]*Entry)
ch  := make(chan int, 10)
f   := &File{fd: fd, name: name}   // idiomatic — not new(File)
```

## 15. init() functions

Use for package-level setup only: registering drivers, validating mandatory env vars, one-time constants. Do not use for logic that could fail gracefully — prefer explicit constructors. Never rely on cross-package `init()` ordering.

```go
// Good — validates mandatory env at startup
func init() {
    if os.Getenv("HOME") == "" { panic("$HOME not set") }
}

// Bad — swallows error, leaks global state
func init() { db, _ = sql.Open("postgres", os.Getenv("DB_URL")) }
```

## 16. Panic and recover

- Panic only for truly unrecoverable state: corrupted invariants, programming bugs.
- Never panic across package boundaries — convert to error at the API surface.
- Recover in deferred functions to prevent goroutine crash propagation.
- `Must*` prefix signals a function that panics on error (stdlib convention).

```go
func SafeProcess(input []byte) (result string, err error) {
    defer func() {
        if r := recover(); r != nil { err = fmt.Errorf("internal: %v", r) }
    }()
    return process(input), nil
}
```

## 17. Structured logging (slog)

Use `log/slog` (stdlib, Go 1.21+) with consistent field key constants.

```go
const (
    keyComponent = "component"
    keyError     = "error"
    keyElapsed   = "elapsed"
)

slog.Info("request completed", keyComponent, "api", keyElapsed, elapsed)
slog.Warn("retry attempted",   keyError, err, "attempt", n)
```

Levels: `Debug` for decision points, `Info` for lifecycle events, `Warn` for recoverable failures, `Error` for unrecoverable ones.

## 18. Stdlib first

Before adding a dependency, check stdlib:
- `encoding/json` not a third-party serialiser.
- `net/http` not a framework.
- `testing` not testify (use `t.Helper()` for custom assertions).
- `testing/fstest.MapFS` not a mock filesystem library.

Only add a dependency when stdlib is genuinely insufficient. Document the decision.

## 19. Testing conventions

- Table-driven tests for multiple cases.
- `testdata/` for YAML/JSON fixtures.
- `t.Helper()` in all test helpers and assertions.
- `t.Skipf()` for optional tests requiring external tools.
- `t.TempDir()` for test artifacts (auto-cleaned).
- Contract tests: `var _ Interface = (*Impl)(nil)`.
- Example functions as runnable documentation.

## Complements

- `effective-go` — §1–10: types, errors, control flow, interfaces, concurrency
- `testing-methodology` — ROGYB test layers and coverage matrix
- `code-smells` — structural smells
