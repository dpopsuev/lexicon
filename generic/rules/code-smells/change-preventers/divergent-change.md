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

**Detection (Go):**
- One file modified in >50% of recent commits for different reasons
- Locus churn + fan-in both high on same component

**SOLID violation:** SRP — multiple reasons to change

**Reference:** https://refactoring.guru/smells/divergent-change
