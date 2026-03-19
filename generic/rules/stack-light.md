---
id: stack-light
title: Stack Light
description: Signal tower pattern for system health. Four-tier visual status (Green/Yellow/Red/Black) applied to agents, workstreams, and the factory. Every component emits its status; the operator sees the tower.
labels: [architecture, process]
---

# Stack Light

**Every component has a stack light. The operator sees health at a glance.**

Reference: https://en.wikipedia.org/wiki/Stack_light

## The Pattern

A stack light (signal tower, Andon light) is a tiered column of colored lights on a machine in a factory. The foreman walks the floor and sees every machine's status without stopping to inspect. Green means running. Yellow means attention needed. Red means stopped. The information radiates — no one needs to ask "how's machine 7?"

## The Four Tiers

| Tier | Color | Meaning | Agent Behavior | System Response |
|------|-------|---------|---------------|-----------------|
| **1** | Green | High confidence, proceeding | Continue autonomously | No intervention |
| **2** | Yellow | Uncertainty, caution, degraded but functional | Signal concern, continue with monitoring | Watchdog attention, operator notified, logged |
| **3** | Red | Problem detected, needs guidance | Pause, request input | Gate blocks promotion, operator escalation |
| **4** | Black | Critical failure, unsafe state | Stop immediately | Halt workstream, cordon scope, operator alerted directly |

## Where Stack Lights Apply

Every component in Aeon emits a stack light signal:

### Agent Level
Each agent emits its current confidence as a stack light:
- Green — "I know what I'm doing, tests pass, within scope"
- Yellow — "Something is off — coverage dropped, uncertain about this approach"
- Red — "I'm stuck, tests fail, need human guidance"
- Black — "Security issue found, unsafe state, stop everything"

### Workstream Level
Aggregated from all agents in the workstream (worst-flag-wins):
- Green — all agents green
- Yellow — any agent yellow, none red/black
- Red — any agent red
- Black — any agent black, entire workstream halted

### Factory Level (Andon Board)
Aggregated from all workstreams (worst-flag-wins):
- The operator sees one board with all workstreams color-coded
- Drill into any workstream to see individual agent lights

### Gate Level
Each promotion gate has a stack light:
- Green — all checks pass, promotion allowed
- Yellow — checks pass with warnings (coverage decreased but still above threshold)
- Red — checks fail, promotion blocked
- Black — checks reveal critical issue (security vulnerability), cordon triggered

## The Signal

```
Signal {
    Level:      Green | Yellow | Red | Black
    Confidence: 0.0 - 1.0
    Source:      who emitted (agent ID, watchdog ID, gate ID)
    Scope:      what's affected (file paths, package paths)
    Category:   what kind (test, security, performance, drift, budget)
    Evidence:   why (human-readable explanation)
}
```

## Worst-Flag-Wins Aggregation

Health rolls up by taking the worst flag at each level:
- If 9 agents are Green and 1 is Yellow, the workstream is Yellow
- If 5 workstreams are Green and 1 is Red, the factory is Red
- One Black anywhere means the factory is Black

This is intentionally aggressive. A single critical signal must be visible at the factory level. The operator can always drill down to find the source.

## Cordon (Black Flag Special Behavior)

Black is not just "stop this workstream." It triggers a **cordon** — the affected scope is marked unsafe across ALL workstreams:

1. Agent A in Workstream 1 emits Black for `auth/middleware.go`
2. Cordon registry marks `auth/middleware.go` as unsafe
3. Agent B in Workstream 2 also touches `auth/middleware.go`
4. Agent B receives a Black signal from the cordon — it halts too
5. Operator reviews, resolves, clears the cordon
6. Both workstreams resume

Like a police cordon — nobody enters until cleared.

## Stack Light vs Logging

Stack lights are NOT logs. They are orthogonal:

| Stack Light | Logging |
|------------|---------|
| Current state (what IS the status right now) | History (what HAS happened) |
| Aggregatable (worst-flag-wins) | Appendable (every event recorded) |
| Radiates to operator without being asked | Must be queried or tailed |
| Four tiers, no more | Arbitrary detail |
| Actionable (each tier has a defined response) | Diagnostic (helps understand why) |

The stack light tells you something is wrong. The log tells you why.

## Anti-Patterns

- **All green, all the time.** If the light never goes yellow, the thresholds are too lax. A factory that never flags a problem isn't well-monitored — it's blind.
- **Crying wolf.** If the light goes red for trivial issues, the operator learns to ignore it. Reserve red for genuine problems. Yellow exists for "not great but not blocking."
- **No black.** If there's no mechanism for "everything stops," critical issues get treated as red (blocking one workstream) when they should cordon the entire affected scope.
- **Invisible lights.** A stack light nobody sees is useless. The Andon board must be the operator's default view, not a page they navigate to.

## Complements

- `factory-principles` — Andon (Toyota) is the manufacturing origin of stack lights
- `defense-in-depth` — stack lights are one layer of the quality membrane
- `operational-quality` — SLO breaches map to Yellow/Red flag transitions
- `trust-boundaries` — Black flags at trust boundaries trigger cordons
