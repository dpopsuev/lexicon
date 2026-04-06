---
id: andon
title: "Andon (Visual Alert System)"
description: "Signal problems immediately — anyone can stop the line"
labels: [manufacturing, tps, lean, observability]
---

# Andon

**Core idea:** A visual signal system that alerts supervisors when a problem occurs. Any worker can pull the cord to stop the line. Problems are surfaced immediately, not hidden.

## In Manufacturing

- Colored lights on the production line (green/yellow/red)
- Worker pulls cord when defect detected
- Supervisor responds immediately
- Line stops until problem is resolved

## In Software (Djinn)

- signal.Bus emits signals with levels (Green/Yellow/Red/Black)
- Watchdogs detect anomalies (budget exceeded, deadlock, stuck agent)
- TUI dashboard shows signal status
- Operator can cordon (stop work in a scope)

## Key Principle

**Stop and fix, don't work around.** A hidden defect compounds. A surfaced defect gets fixed.

**Reference:** https://en.wikipedia.org/wiki/Andon_(manufacturing)
