---
id: lazy-class
title: "Lazy Class"
description: "Extra indirection with no value."
labels: [code-smells, dispensables]
---

# Lazy Class

**Signs:**
- Class/package has <50 LOC
- Only wraps or delegates to another class
- Created for future use that never came

**Why it's bad:** Extra indirection with no value. Increases navigation cost.

**Treatment:**
- Inline Class — merge into its only user
- Collapse Hierarchy — remove unnecessary level
- Remove Middle Man — let clients call the real implementation

**Detection (Go):**
- Package with 1 exported function and <50 LOC
- Type that only delegates to an embedded type

**SOLID violation:** YAGNI — You Aren't Gonna Need It

**Reference:** https://refactoring.guru/smells/lazy-class
