---
id: tui-testing
title: TUI Testing
description: Best practices for testing terminal user interfaces. Decomposed testing, state assertions over render assertions, snapshot testing.
labels: [testing, tui, bubbletea, ui]
---

# TUI Testing

**Test behavior, not pixels. Assert on model state, not rendered strings.**

## The Three Layers

| Layer | What to test | How | Breaks when |
|-------|-------------|-----|-------------|
| **Model state** | State transitions, field mutations | Assert on model fields after Update() | Logic changes |
| **Component contract** | Each component honors its interface | Unit test component in isolation | API changes |
| **Visual regression** | Rendered output matches expectation | Golden file / snapshot comparison | Visual changes (intentionally) |

Most tests should be **model state**. Few should be **visual regression**. Component contracts bridge the gap.

## Anti-Patterns

### Brittle: Assert on rendered strings

```go
// BAD — breaks when status line format changes
view := model.View()
if !strings.Contains(view, "agent") {
    t.Fatal("should show mode")
}

// GOOD — asserts on model state
if model.mode != agent.ModeAgent {
    t.Fatal("mode should be agent")
}
```

### Brittle: Assert on exact output text

```go
// BAD — breaks when help text is reworded
if !strings.Contains(result.Output, "commands:") {
    t.Fatal("help should contain commands")
}

// GOOD — asserts structural property
if result.Output == "" {
    t.Fatal("help should produce output")
}
```

### Brittle: Assert on visual formatting

```go
// BAD — breaks when logo changes
if !strings.Contains(view, "___") {
    t.Fatal("should show logo")
}

// GOOD — asserts logo constant is used
if !strings.Contains(view, tui.DjinnLogo[:10]) {
    t.Fatal("should render the logo")
}
```

## Best Practices

### 1. Decompose into testable components

Each TUI component (input, conversation, status line, approval) should be testable in isolation without constructing the full model.

```go
// Component test — no full model needed
func TestStatusLine_HealthCollapsed(t *testing.T) {
    reports := []HealthReport{{Status: StatusGreen}, {Status: StatusGreen}}
    line := RenderHealth(reports)
    // Assert on the rendering function directly
}
```

### 2. Test state transitions, not views

The Bubbletea model is a pure function: `(Model, Msg) → (Model, Cmd)`. Test the state machine:

```go
// State transition test
model, _ := model.Update(ToolCallMsg{...})
assert(model.state == stateToolApproval)
assert(model.pendingTool != nil)
```

### 3. Use golden files for visual regression

When you DO need to test rendered output, use snapshot/golden files:

```go
func TestView_Welcome(t *testing.T) {
    got := model.View()
    golden := filepath.Join("testdata", "welcome.golden")
    if *update {
        os.WriteFile(golden, []byte(got), 0644)
    }
    want, _ := os.ReadFile(golden)
    if got != string(want) {
        t.Fatalf("view changed:\n%s", diff(got, string(want)))
    }
}
```

Run with `-update` flag to regenerate golden files after intentional visual changes.

### 4. Test commands separately from rendering

Slash commands are pure functions: `(Command, Session) → CommandResult`. Test them without any TUI:

```go
func TestMode_Switch(t *testing.T) {
    result := ExecuteCommand(Command{Name: "/mode", Args: []string{"auto"}}, sess)
    assert(sess.Mode == "auto")
    assert(result.ModeChange == "auto")
}
```

### 5. Test message flow, not handler internals

Test that the right messages arrive, not how they're sent:

```go
// Test the flow: agent event → tea.Msg → model state
model, _ := model.Update(tui.TextMsg("hello"))
assert(model.streamBuf.String() == "hello")
```

### 6. Spinner and animation tests

Test the flag, not the rendered frame:

```go
// GOOD — test the state
model.Update(startStreamingMsg)
assert(model.spinnerActive == true)

model.Update(tui.TextMsg("first token"))
assert(model.spinnerActive == false)

// BAD — test rendered spinner character (changes every frame)
if !strings.Contains(view, "⠋") { ... }
```

## Coverage Priority

| Priority | What | Example |
|----------|------|---------|
| **Must** | State transitions (input → streaming → approval → done) | All state changes tested |
| **Must** | Command dispatch and results | All slash commands tested |
| **Should** | Component rendering in isolation | StatusLine, ConversationView |
| **Should** | Edge cases (empty history, overflow, resize) | Boundary conditions |
| **Could** | Visual regression via golden files | Welcome screen, full conversation |
| **Won't** | Exact pixel/character positions | Too brittle, no value |

## Complements

- `testing-methodology` — ROGYB cycle, coverage matrix, BDD
- `effective-go` — table-driven tests, t.Helper()
