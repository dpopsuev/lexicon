---
id: feature-envy
title: "Feature Envy"
description: "Wrong location."
labels: [code-smells, couplers]
---

# Feature Envy

**Signs:**
- Method references another package's data more than its own
- Function mostly reads/writes fields of a foreign struct
- Logic would be simpler if moved to the other package

**Why it's bad:** Wrong location. Logic and data are separated, increasing coupling.

**Treatment:**
- Move Method — move to the class it envies
- Extract Method — isolate the envious section, then move it

**Detection (Go):**
- Function imports package X and calls X.field/method 5+ times but uses own package 0-1 times

**SOLID violation:** DIP — depends on concrete details of another module

**Reference:** https://refactoring.guru/smells/feature-envy
