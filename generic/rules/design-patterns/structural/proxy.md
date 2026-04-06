---
id: proxy
title: "Proxy Pattern"
description: "Need to control access to an object — lazy init, access control, logging, caching."
labels: [design-patterns, structural]
---

# Proxy

**Problem:** Need to control access to an object — lazy init, access control, logging, caching.

**Solution:** Wrapper with the same interface that adds control logic before/after delegation.

**Structure:**
- Subject: shared interface
- RealSubject: actual implementation
- Proxy: controls access to RealSubject

**When to use:**
- Lazy initialization (virtual proxy)
- Access control (protection proxy)
- Logging/caching (smart proxy)

**When NOT to use:**
- No access control or lifecycle management needed
- Direct access is fine

**Go notes:**
- Implement same interface as the real subject
- Proxy wraps real subject, delegates after checks

**Related:** Adapter, Decorator, Facade

**Reference:** https://refactoring.guru/design-patterns/proxy
