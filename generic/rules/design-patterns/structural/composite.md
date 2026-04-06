---
id: composite
title: "Composite Pattern"
description: "Need to treat individual objects and compositions uniformly."
labels: [design-patterns, structural]
---

# Composite

**Problem:** Need to treat individual objects and compositions uniformly.

**Solution:** Tree structure where branches and leaves share a common interface.

**Structure:**
- Component: shared interface
- Leaf: terminal node
- Composite: contains children Components

**When to use:**
- Tree/hierarchy structures (file system, UI panels, org chart)
- Client should treat single and group objects the same

**When NOT to use:**
- Structure is flat, not hierarchical
- Leaf and composite behaviors differ significantly

**Go notes:**
- Define interface with shared methods
- Composite has `Children() []Component`
- Djinn's `core.Panel` is a Composite

**Related:** Builder, Iterator, Visitor, Decorator

**Reference:** https://refactoring.guru/design-patterns/composite
