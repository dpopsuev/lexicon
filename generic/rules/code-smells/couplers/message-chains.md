---
id: message-chains
title: "Message Chains"
description: "Fragile."
labels: [code-smells, couplers]
---

# Message Chains

**Signs:**
- `a.B().C().D()` — navigating deep object graphs
- Client depends on the structure of intermediate objects
- Changing any link in the chain breaks the caller

**Why it's bad:** Fragile. Couples caller to the entire chain structure. Law of Demeter violation.

**Treatment:**
- Hide Delegate — add a method on the first object that returns what's needed
- Extract Method — encapsulate the chain
- Move Method — relocate to where the data lives

**Detection (Go):**
- Method chains >3 deep: `x.GetA().GetB().GetC()`
- Especially with error-prone nil checks between each step

**SOLID violation:** Law of Demeter — only talk to immediate friends

**Reference:** https://refactoring.guru/smells/message-chains
