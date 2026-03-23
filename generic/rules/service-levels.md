---
id: service-levels
title: Service Levels — SLI, SLO, SLA
description: Every operation must define three service levels. Measure, target, enforce. The agent doesn't decide timeouts — the system enforces them mechanically.
labels: [architecture, reliability, fail-fast]
---

# Service Levels — SLI, SLO, SLA

Every operation that crosses a boundary (network, process, disk, LLM) must define three service levels:

| Level | Name | What | Who Owns |
|---|---|---|---|
| **SLI** | Service Level Indicator | What we **measure** | Instrumentation (metrics, logs) |
| **SLO** | Service Level Objective | What we **target** | Engineering team (config) |
| **SLA** | Service Level Agreement | What we **enforce** | System (context.WithTimeout) |

## The Chain

```
SLI (observe) → SLO (compare) → SLA (enforce)
```

- **SLI** feeds dashboards. "TTFT was 2.3s."
- **SLO** triggers alerts. "p95 TTFT exceeded 5s target."
- **SLA** kills operations. "context.WithTimeout(ctx, 30s) → fail."

## Rules

1. **Every I/O operation gets a context.WithTimeout.** No blocking call without a deadline. This is the SLA.
2. **Every timeout has a metric.** Log duration on success AND failure. This is the SLI.
3. **Every metric has a target.** Define p95/p99 thresholds in config. This is the SLO.
4. **SLA > SLO always.** The kill timeout must be larger than the target. SLO=3s, SLA=10s. If SLO is breached, alert. If SLA is breached, kill.
5. **Fail fast, fail loud.** On timeout: return actionable error with duration + operation name. Never hang silently.
6. **Emit progress before timeout.** At 50% of SLA, emit a progress signal. At 80%, emit a warning. The human should never be surprised by a timeout.

## Human Patience Thresholds

| Duration | Perception | Required Action |
|---|---|---|
| < 100ms | Instant | No feedback needed |
| 100ms–1s | Noticeable | Show spinner |
| 1s–5s | Tolerable | Show what's happening |
| 5s–10s | Frustration | Show progress + ETA |
| 10s–30s | Anger | Show partial result or explain |
| > 30s | Rage/abandon | MUST return something or fail |

## Anti-Patterns

- Blocking I/O without context deadline.
- Using `http.DefaultClient` (no timeout configured).
- `exec.Command` instead of `exec.CommandContext`.
- `bufio.Scanner.Scan()` on network I/O without read deadline.
- Swallowing timeout errors (`_ = ctx.Err()`).
- Setting SLA = SLO (no buffer between target and kill).
- Retry without backoff after timeout (amplifies load).

## Budget Parameters (Beyond Time SLAs)

Time is not the only dimension. These also need mechanical enforcement:

| Budget | What It Limits | Default |
|---|---|---|
| Token budget | Max tokens per role/turn/session | 50k per role |
| Turn budget | Max agent loop iterations | 20 per prompt |
| Retry budget | Max retries before permanent fail | 3 attempts |
| Silence budget | Max time without output | 15s warn, 30s fail |
| Blast radius | Max files changed per task | 20 files |
| Cost ceiling | Max USD per role/session | Configurable |
| Loop budget | Max feedback loops (rework cycles) | 3 cycles |
| Queue depth | Max pending items in mailbox | 5 per role |
