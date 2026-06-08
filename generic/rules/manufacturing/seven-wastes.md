---
id: seven-wastes
title: "Seven Wastes (Muda)"
description: "Toyota's 7 categories of waste: overproduction, waiting, transport, over-processing, inventory, motion, defects"
labels: [manufacturing, lean, waste, tps]
---

# Seven Wastes (Muda)

**Core idea:** Any activity that consumes resources without creating value for the customer is waste. Toyota identified 7 categories.

## Manufacturing → Software

| # | Manufacturing | Software equivalent |
|---|---|---|
| 1 | **Overproduction** | Extra features nobody asked for; code written speculatively |
| 2 | **Waiting** | Waiting for decisions, reviews, CI builds, deploy approvals |
| 3 | **Transportation** | Unnecessary data handoffs, serialization/deserialization across unnecessary boundaries |
| 4 | **Over-processing** | Gold plating, unnecessary abstraction, re-implementing what stdlib provides |
| 5 | **Inventory** | Partially done work, stale branches not merged, untriaged bug backlogs |
| 6 | **Motion** | Context switching, touching files unrelated to the current task |
| 7 | **Defects** | Bugs shipped, rework, wrong assumptions baked in early |

## The 8th Waste

Some add an 8th: **unused talent** — not leveraging team expertise. In agentic systems: not using the right agent for the job (executor doing auditor's work).

**Reference:** https://en.wikipedia.org/wiki/Muda_(Japanese_term)
