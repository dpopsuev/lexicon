---
id: iterator
title: "Iterator Pattern"
description: "Need to traverse a collection without exposing its internal structure."
labels: [design-patterns, behavioral]
---

# Iterator

**Problem:** Need to traverse a collection without exposing its internal structure.

**Solution:** Extract traversal logic into a separate iterator object.

**Structure:**
- Iterator: interface with Next(), HasNext()
- ConcreteIterator: traversal implementation
- Collection: creates iterators

**When to use:**
- Complex data structures (trees, graphs)
- Multiple traversal strategies

**When NOT to use:**
- Simple slice — use range loop
- Only one traversal order needed

**Go notes:**
- Use channels for lazy iteration: `func (t *Tree) Walk() <-chan Node`
- Or callback: `func (t *Tree) Walk(fn func(Node) bool)`

**Related:** Composite, Visitor, Memento

**Reference:** https://refactoring.guru/design-patterns/iterator
