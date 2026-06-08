---
id: divergent-change
title: "Divergent Change"
description: "Every change risks breaking unrelated functionality."
labels: [code-smells, change-preventers]
---

# Divergent Change

**Signs:**
- One module changes for multiple unrelated reasons
- Different features require modifying the same file
- Module has multiple sections that change at different rates

**Why it's bad:** Every change risks breaking unrelated functionality. High blast radius per change.

**Treatment:**
- Extract Class — separate unrelated responsibilities
- Extract Module — split into focused packages
- Move Method — relocate methods to their proper home

**Detection:**
- One file modified in >50% of recent commits for different reasons
- Architecture analysis shows high churn and high fan-in on the same component
- `git log --oneline -- <file>` reveals commits with unrelated subject lines

**SOLID violation:** SRP — multiple reasons to change

**Reference:** https://refactoring.guru/smells/divergent-change
