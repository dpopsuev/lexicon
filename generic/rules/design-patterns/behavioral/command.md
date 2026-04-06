---
id: command
title: "Command Pattern"
description: "Need to parameterize, queue, or undo operations."
labels: [design-patterns, behavioral]
---

# Command

**Problem:** Need to parameterize, queue, or undo operations.

**Solution:** Encapsulate a request as an object with Execute/Undo methods.

**Structure:**
- Command: interface with Execute()
- ConcreteCommand: binds receiver + action
- Invoker: triggers command
- Receiver: does the work

**When to use:**
- Undo/redo support
- Queue or schedule operations
- Parameterize objects with operations

**When NOT to use:**
- Simple direct method call suffices
- No undo or queuing needed

**Go notes:**
- Define `Command` interface with `Execute(ctx) error`
- Commands are values — store, queue, replay

**Related:** Chain of Responsibility, Mediator, Memento, Observer

**Reference:** https://refactoring.guru/design-patterns/command
