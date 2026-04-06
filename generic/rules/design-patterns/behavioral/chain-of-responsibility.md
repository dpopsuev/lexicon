---
id: chain-of-responsibility
title: "Chain of Responsibility Pattern"
description: "Multiple handlers might process a request."
labels: [design-patterns, behavioral]
---

# Chain of Responsibility

**Problem:** Multiple handlers might process a request. Don't hardcode which one.

**Solution:** Pass request along a chain of handlers. Each decides to process or pass along.

**Structure:**
- Handler: interface with Handle method
- ConcreteHandler: processes or passes
- Client: sends to first handler

**When to use:**
- Multiple processing steps (middleware, validation pipeline)
- Handler set changes dynamically

**When NOT to use:**
- Only one handler ever processes the request
- Order doesn't matter

**Go notes:**
- Middleware chains: `func(next http.Handler) http.Handler`
- Tool Envelope gates are a chain of responsibility

**Related:** Command, Mediator, Observer, Decorator

**Reference:** https://refactoring.guru/design-patterns/chain-of-responsibility
