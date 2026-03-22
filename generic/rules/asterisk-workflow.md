---
id: asterisk-workflow
title: Asterisk Circuit Workflow
description: How AI agents use Asterisk MCP for structured multi-step execution circuits
labels: [process, agentic]
---

# Asterisk Circuit Workflow

Asterisk is a circuit runner available as an MCP sidecar. It orchestrates multi-step execution plans with dependency tracking, step submission, and reporting.

## Tools

| Tool | Purpose |
|------|---------|
| `start_circuit` | Begin a new execution circuit with steps |
| `get_next_step` | Get the next unblocked step to execute |
| `submit_step` | Submit results for a completed step |
| `get_report` | Get circuit execution report |
| `get_run_report` | Get report for a specific run |
| `get_trace` | Get execution trace for debugging |
| `get_signals` | Read signals emitted during execution |
| `emit_signal` | Emit a signal for downstream steps |
| `get_worker_health` | Check worker health status |
| `diff_runs` | Compare two circuit runs |

## Workflow

1. `start_circuit` with a step DAG — defines what to execute
2. `get_next_step` — fetch the next unblocked step
3. Execute the step (run tests, build, deploy, etc.)
4. `submit_step` — report results (pass/fail/output)
5. Repeat 2-4 until circuit completes
6. `get_report` — review execution summary
