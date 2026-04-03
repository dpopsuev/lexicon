---
id: pull-not-push
title: Pull Not Push (JIT for Agent Resources)
description: Agents spawn on demand, tools load when needed, context injected when relevant. No pre-allocation, no eager loading, no speculative work.
labels: [lean, jit, pull, agentic]
---

# Pull Not Push

**Produce only when downstream demands. Never pre-allocate, never speculate.**

Adapted from Toyota's Just-in-Time and Pull System principles.

## Pull vs Push in Agent Systems

| Aspect | Push (wasteful) | Pull (lean) |
|--------|----------------|-------------|
| **Agent spawning** | Pre-allocate 3 executors at boot | Spawn executor when task arrives |
| **Tool loading** | Load all MCP tools at startup | Connect MCP and load tools when first used |
| **Context injection** | Dump entire project context into every prompt | Inject only relevant context for current task |
| **Support roles** | Always spawn inspector + auditor | Spawn support only when executor signals need |
| **File reading** | Read all files in scope upfront | Read files on demand as agent needs them |
| **Memory** | Carry full conversation history forever | Compact when threshold reached, keep only relevant |

## The Supermarket Model

Taiichi Ohno observed supermarket shelves: items restocked only after customers buy them. Applied to agents:

- The **executor** is the customer — it pulls work from the plan
- The **scheduler** is the restocking system — it spawns support roles when the executor signals need
- The **external tool server** is the supplier — it connects when tools are needed, not at boot
- The **context** is the shelf — it holds only what's relevant, replenished on demand

## Rules

1. **No pre-allocation.** Don't spawn agents "just in case." Spawn when needed.
2. **No eager loading.** Don't connect all MCPs at boot. Connect when first tool call requires it.
3. **No speculative context.** Don't dump the entire codebase into the prompt. Inject what the current task touches.
4. **Signal-driven.** Downstream signals upstream. Executor signals need → supervisor spawns support. Agent signals tool need → runtime connects external service.

## Anti-Patterns

- Spawning 5 support agents when only 1 inspector is needed
- Loading all external tool servers when the task only needs file editing
- Injecting all 200 rules when only 5 apply to the current language
- Reading all files in a package when the agent only needs one function

## Complements

- `agent-waste` — push creates overproduction waste (muda #1)
- `day-0-1-2` — Day 1 built-ins are always available; Day 2 MCPs connect on demand (pull)
- `factory-principles` — JIT is a core Toyota principle
- `agentic-flywheel` — pull reduces waste, flywheel compounds the savings
