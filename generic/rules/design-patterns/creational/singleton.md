---
id: singleton
title: "Singleton Pattern"
description: "Exactly one instance of a class should exist and be globally accessible."
labels: [design-patterns, creational]
---

# Singleton

**Problem:** Exactly one instance of a class should exist and be globally accessible.

**Solution:** Private constructor + static access method. In Go, use sync.Once or package-level variable.

**Structure:**
- Singleton: the single instance
- Access: global function or method

**When to use:**
- Shared resource (connection pool, config, logger)
- Coordination point (registry, event bus)

**When NOT to use:**
- Testing requires different instances
- Prefer dependency injection over global state

**Go notes:**
- Use `sync.Once` for lazy init: `once.Do(func() { instance = &T{} })`
- Prefer passing dependencies explicitly over package-level singletons

**Related:** Factory Method, Facade

**Reference:** https://refactoring.guru/design-patterns/singleton
