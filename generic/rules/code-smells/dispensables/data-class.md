---
id: data-class
title: "Data Class"
description: "Data and behavior are separated."
labels: [code-smells, dispensables]
---

# Data Class

**Signs:**
- Struct with only fields and getters/setters, no behavior
- All logic that operates on the data lives elsewhere
- Other classes reach into its fields directly

**Why it's bad:** Data and behavior are separated. Logic that belongs with the data is scattered.

**Treatment:**
- Move Method — move behavior into the data class
- Encapsulate Field — add methods that use the data
- Encapsulate Collection — return copies, not references

**Detection (Go):**
- Struct with 0 methods but 5+ exported fields
- Other packages access fields directly in 3+ places

**SOLID violation:** SRP — behavior belongs with its data (Tell, Don't Ask)

**Reference:** https://refactoring.guru/smells/data-class
