---
id: large-class
title: "Large Class"
description: "Large classes are hard to navigate, test in isolation, and change safely."
labels: [code-smells, bloaters]
---

# Large Class

**Signs:**
- Class/struct has >15 exported fields or methods
- File exceeds 2000 LOC
- Multiple unrelated responsibilities

**Why it's bad:** Large classes are hard to navigate, test in isolation, and change safely.

**Treatment:**
- Extract Class — move related fields and methods together
- Extract Interface — define smaller role-specific interfaces
- Replace Data Value with Object — promote field groups to types

**Detection (Go):**
- >15 exported symbols in one package
- >2000 LOC in a single file

**SOLID violation:** SRP — class has multiple reasons to change

**Reference:** https://refactoring.guru/smells/large-class
