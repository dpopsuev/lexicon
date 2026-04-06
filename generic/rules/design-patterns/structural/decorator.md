---
id: decorator
title: "Decorator Pattern"
description: "Need to add behavior to objects dynamically without altering their class."
labels: [design-patterns, structural]
---

# Decorator

**Problem:** Need to add behavior to objects dynamically without altering their class.

**Solution:** Wrap an object with another that adds behavior before/after delegating.

**Structure:**
- Component: interface
- ConcreteComponent: base implementation
- Decorator: wraps Component, implements Component
- ConcreteDecorator: adds behavior

**When to use:**
- Adding cross-cutting concerns (logging, caching, auth)
- Behavior composition at runtime

**When NOT to use:**
- Only one fixed behavior needed
- Wrapper chain becomes hard to debug

**Go notes:**
- Middleware pattern: `func(next Handler) Handler`
- Tool Envelope (Gate/Enrich/Execute/Record) is a decorator chain

**Related:** Adapter, Proxy, Chain of Responsibility

**Reference:** https://refactoring.guru/design-patterns/decorator
