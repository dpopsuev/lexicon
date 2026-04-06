---
id: primitive-obsession
title: "Primitive Obsession"
description: "Type errors hide at runtime."
labels: [code-smells, bloaters]
---

# Primitive Obsession

**Signs:**
- Raw strings used for domain identifiers (IDs, roles, paths)
- Constants scattered instead of typed enums
- Data clumps passed as individual primitives

**Why it's bad:** Type errors hide at runtime. Swapped arguments go undetected. No compiler help.

**Treatment:**
- Replace Data Value with Object — `type Role string` not `string`
- Introduce Parameter Object — group related primitives
- Replace Type Code with Class — typed enums with methods

**Detection (Go):**
- grep for `func.*string, string, string` — 3+ string params
- Raw string comparisons: `if role == "executor"`

**SOLID violation:** N/A — but enables SRP violations by losing type safety

**Reference:** https://refactoring.guru/smells/primitive-obsession
