---
id: observability-first
title: "Observability First"
description: "First-class pillar alongside Testing and Security. Part of ROGYB (Orange + Yellow). If you cannot see it, you cannot fix it."
labels: [engineering, observability, flywheel, philosophy]
always_apply: true
---

# Observability First

**First-class pillar alongside Testing (ROGYB) and Security (STRIDE). Not a phase. Not optional. Part of every code increment — Orange instruments failures, Yellow instruments success.**

**Every decision must be data-driven. Every system must be observable by default. If you can't measure it, you can't improve it.**

## The Rule

Expose everything. During development, during debugging, during production. The cost of observability is paid once. The cost of flying blind is paid every incident.

## What to Expose

### During Development (every tool call)

| What | How | Why |
|---|---|---|
| Tool calls | Structured log: tool name, input, output, duration | See what the agent did |
| State changes | Event bus: before/after on every mutation | See what changed |
| Decisions | Log with reasoning: "chose X because Y" | See why |
| Errors | Structured error with context, not just message | See what went wrong |
| Performance | Duration, allocation count, cache hit/miss | See cost |

### During Debugging (trace everything)

| What | How | Why |
|---|---|---|
| Trace spans | Tracer.Begin/End with parent-child hierarchy | See the call tree |
| Ring buffer | Bounded circular buffer of recent events | Inspect without external tools |
| Health scoring | Aggregate signals → health per component | See system health at a glance |
| Audit trail | Who did what, when, with what result | Accountability |

### During Production (metrics + alerts)

| What | How | Why |
|---|---|---|
| Throughput | Items completed per time period | Capacity planning |
| Latency | p50/p95/p99 per operation | Performance budgets |
| Error rate | Errors / total per time window | Quality signal |
| Resource usage | Memory, CPU, token spend | Budget tracking |

## Anti-Patterns

- **Add tracing later** — by the time you need it, you can't add it. Instrument as you build.
- **Log strings, not structure** — `slog.Info("failed", "error", err)` not `fmt.Println("failed: " + err.Error())`
- **Observe only failures** — success signals matter too (ROGYB: Orange for failures, Yellow for success).
- **Dashboard without action** — every metric must have a threshold and a response. Otherwise it's decoration.
- **Sampling in development** — sample in production if needed. In development, record everything.

## Data-Driven Decision Framework

Before making a decision, ask:

1. **What data supports this?** — not intuition, not opinion
2. **Can I measure the outcome?** — if not, add a metric first
3. **What would change my mind?** — define the threshold before acting
4. **Is the measurement cheap enough to keep?** — if yes, keep it forever

## Flywheel Connection

Observability IS the flywheel lubricant:

```
Observe → Measure → Decide → Act → Observe (feedback loop)
```

Without observation, the flywheel has no feedback. Without feedback, no improvement. Without improvement, no compounding. The flywheel stops.

## Djinn Alignment

- **telemetry/** — trace, signal, watchdog, slog (the observation layer)
- **Reconciliation Circuit** — Observe phase reads from telemetry
- **WasteClassifier** — classifies tool calls by Lean waste type (data-driven waste detection)
- **Scoring** — burndown is observable convergence (data-driven progress)
- **HITL** — operator sees data, makes decisions (not blind approval)

## Complements

- `testing-methodology` — ROGYB: Orange (problem signals) + Yellow (success signals)
- `agentic-flywheel` — "Observable by default. Every system step has debug hooks."
- `defense-in-depth` — observation is a defense layer (Detect in NIST framework)
- `performance-engineering` — "Benchmark before optimizing. Intuition about bottlenecks is wrong."

**Reference:** Charity Majors, "Observability Engineering" (O'Reilly, 2022)
