---
id: middle-man
title: "Middle Man"
description: "Extra indirection."
labels: [code-smells, couplers]
---

# Middle Man

**Signs:**
- Class that only delegates to another with no added behavior
- >50% of methods are one-line delegations
- Removing the class wouldn't change any logic

**Why it's bad:** Extra indirection. Navigation cost with zero value.

**Treatment:**
- Remove Middle Man — let clients call the delegate directly
- Inline Class — merge into the delegate
- Replace Delegation with Inheritance — if a true is-a relationship exists

**Detection (Go):**
- Type where >50% of methods are `func (m *Mid) X() { m.inner.X() }`
- `jerichoport/` was a middle man before deletion

**SOLID violation:** N/A — but often created by over-application of Hide Delegate

**Reference:** https://refactoring.guru/smells/middle-man
