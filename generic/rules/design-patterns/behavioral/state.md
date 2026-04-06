---
id: state
title: "State Pattern"
description: "Object behavior changes based on its internal state."
labels: [design-patterns, behavioral]
---

# State

**Problem:** Object behavior changes based on its internal state. Large switch/if-else chains.

**Solution:** Extract each state into its own type implementing a shared interface.

**Structure:**
- Context: holds current state, delegates behavior
- State: interface with state-specific methods
- ConcreteState: one per state

**When to use:**
- Behavior depends on state (FSM, workflow, connection lifecycle)
- State transitions are complex

**When NOT to use:**
- Few states with simple transitions
- Switch statement is clear enough

**Go notes:**
- State interface with methods for each action
- Context holds `state State` field, delegates calls

**Related:** Strategy, Bridge, Singleton

**Reference:** https://refactoring.guru/design-patterns/state
