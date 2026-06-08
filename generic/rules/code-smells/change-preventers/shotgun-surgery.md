---
id: shotgun-surgery
title: "Shotgun Surgery"
description: "Easy to miss a location."
labels: [code-smells, change-preventers]
---

# Shotgun Surgery

**Signs:**
- One logical change requires modifying 3+ files
- Adding a feature means touching many unrelated packages
- Small change, big diff

**Why it's bad:** Easy to miss a location. High risk of incomplete changes.

**Treatment:**
- Move Method — consolidate scattered logic
- Move Field — co-locate related data
- Inline Class — merge overly-split classes

**Detection:**
- `git diff` shows 5+ files changed for a single logical change
- Architecture coupling analysis shows high fan-out from the changed component

**SOLID violation:** SRP — responsibility is scattered instead of cohesive

**Reference:** https://refactoring.guru/smells/shotgun-surgery
