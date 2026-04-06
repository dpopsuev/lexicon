---
id: strategy
title: "Strategy Pattern"
description: "Multiple algorithms for the same task."
labels: [design-patterns, behavioral]
---

# Strategy

**Problem:** Multiple algorithms for the same task. Chosen at runtime.

**Solution:** Define a family of algorithms behind an interface. Client picks which one.

**Structure:**
- Strategy: interface for the algorithm
- ConcreteStrategy: each algorithm
- Context: uses a strategy

**When to use:**
- Algorithm varies by configuration or input
- Avoiding complex conditionals for algorithm selection

**When NOT to use:**
- Only one algorithm exists
- Algorithm never changes

**Go notes:**
- Pass strategy as interface parameter or functional option
- Shell presets selecting tools is a Strategy pattern

**Related:** State, Template Method, Command, Bridge

**Reference:** https://refactoring.guru/design-patterns/strategy
