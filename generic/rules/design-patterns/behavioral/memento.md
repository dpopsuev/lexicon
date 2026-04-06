---
id: memento
title: "Memento Pattern"
description: "Need to save and restore an object's state without exposing internals."
labels: [design-patterns, behavioral]
---

# Memento

**Problem:** Need to save and restore an object's state without exposing internals.

**Solution:** Object creates a snapshot (memento) that can be stored and restored later.

**Structure:**
- Originator: creates memento from its state
- Memento: opaque snapshot
- Caretaker: stores mementos

**When to use:**
- Undo/redo functionality
- Checkpointing (save/restore sessions)
- Transaction rollback

**When NOT to use:**
- State is trivial (just re-create)
- State is immutable (no need to save)

**Go notes:**
- Memento as a private struct with exported Save/Restore methods
- Mirage's Diff/Commit/Reset is a memento pattern

**Related:** Command, Iterator, Prototype

**Reference:** https://refactoring.guru/design-patterns/memento
