---
id: asterisk-workflow
title: Asterisk Circuit Workflow
description: How AI agents use Asterisk MCP for structured multi-step execution circuits
labels: [process, agentic]
---

# Asterisk Circuit Workflow

Asterisk is a circuit runner available as an MCP sidecar. It orchestrates multi-step execution plans with dependency tracking, step submission, and reporting.

## Tools

All circuit operations go through two consolidated MCP tools:

| Tool | Actions | Purpose |
|------|---------|---------|
| `circuit` | `start`, `step`, `submit`, `report`, `summary`, `detail`, `failing`, `weak`, `confusion`, `inspect`, `resume` | Circuit lifecycle and execution |
| `signal` | `emit`, `get` | Signal bus for inter-step communication |

Optional tools (when configured):

| Tool | Actions | Purpose |
|------|---------|---------|
| `trace` | `events`, `report`, `diff` | Execution trace for debugging |
| `resource` | `kinds`, `list`, `get`, `validate`, `diff` | Circuit resource management |

## Workflow

1. `circuit(action="start")` with a step DAG — defines what to execute
2. `circuit(action="step")` — fetch the next unblocked step
3. Execute the step (run tests, build, deploy, etc.)
4. `circuit(action="submit")` — report results (pass/fail/output)
5. Repeat 2-4 until circuit completes
6. `circuit(action="report")` — review execution summary
