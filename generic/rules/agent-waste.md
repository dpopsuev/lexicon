---
id: agent-waste
title: Agent Waste Detection (Lean 7 Muda)
description: Classify agent tool calls as value-add or waste. Detect overproduction, waiting, transportation, over-processing, inventory, motion, defects.
labels: [lean, waste, agentic, quality]
---

# Agent Waste Detection

**Every agent action is either value-add or waste. Eliminate waste.**

Adapted from Toyota's 7 Wastes (Muda) for AI agent workflows.

## The 7 Agent Wastes

| # | Waste | Manufacturing | Agent Equivalent | Detection |
|---|-------|--------------|------------------|-----------|
| 1 | **Overproduction** | Making more than needed | Unnecessary tool calls — reading files that won't be used, creating files that won't be kept | Count tool calls that produce unused output |
| 2 | **Waiting** | Idle time in queues | Agent idle between tool calls — polling, unnecessary thinking blocks, waiting for approval on non-critical actions | Measure idle time between actions |
| 3 | **Transportation** | Moving goods unnecessarily | Redundant file reads — reading the same file twice, reading a file to check if it exists then reading it again | Detect duplicate Read calls for same path |
| 4 | **Over-processing** | Working beyond requirements | Solution exceeds what was asked — adding docstrings to unchanged code, refactoring beyond scope, adding error handling for impossible cases | Compare output scope to input scope |
| 5 | **Inventory** | Excess stored goods | Stale context — carrying conversation history that's no longer relevant, loading entire files when a function is needed | Context size vs active usage |
| 6 | **Motion** | Unnecessary movement | Context switching — jumping between unrelated files, switching between planning and executing without completing either | Track file access patterns for locality |
| 7 | **Defects** | Reworking errors | Hallucinated edits — writing code that doesn't compile, tests that don't pass, edits to wrong files | Count compile failures, test failures, reverts |

## Agent Behavior

- Before each tool call, ask: "Does this directly advance the task?"
- After a tool result, ask: "Did I use this output?"
- Track: tool calls per task, unused outputs, duplicate reads, compile failures
- Minimize: files touched, context carried, round-trips to completion

## The Eighth Waste: Unused Agent Capability

An agent with 4 tools that only uses 2 is carrying unused capability. An agent spawned to "help" that produces no output consumed by the executor is waste. Match agent capability to task need (JIT, not pre-allocated).

## Complements

- `factory-principles` — the Toyota production system this derives from
- `pull-not-push` — agents spawned on demand, not pre-allocated
- `agentic-flywheel` — waste elimination compounds over iterations
