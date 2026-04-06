---
id: temporary-field
title: "Temporary Field"
description: "Confusing — reader expects fields to always be valid."
labels: [code-smells, oo-abusers]
---

# Temporary Field

**Signs:**
- Struct field that's only set in some code paths
- Field is nil/zero in most usage contexts
- Field exists to pass data between methods instead of parameters

**Why it's bad:** Confusing — reader expects fields to always be valid. Leads to nil pointer panics.

**Treatment:**
- Extract Class — move field and its methods into their own type
- Introduce Null Object — provide a default instead of nil
- Replace Method with Method Object — encapsulate the computation

**Detection (Go):**
- Struct fields that are nil in most tests
- Fields set in one method, read in another, unused everywhere else

**SOLID violation:** SRP — the temporary state is a separate concern

**Reference:** https://refactoring.guru/smells/temporary-field
