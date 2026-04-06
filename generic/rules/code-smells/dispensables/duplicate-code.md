---
id: duplicate-code
title: "Duplicate Code"
description: "Changes must be made in multiple places."
labels: [code-smells, dispensables]
---

# Duplicate Code

**Signs:**
- Same logic in two or more places with cosmetic differences
- Copy-paste code with minor variations
- `dupl` linter flags threshold exceeded

**Why it's bad:** Changes must be made in multiple places. Bugs fixed in one copy persist in others.

**Treatment:**
- Extract Method — pull common code into shared function
- Extract Class — merge duplicate classes
- Form Template Method — share algorithm skeleton

**Detection (Go):**
- `dupl` linter: threshold >100 tokens
- Manual: same 5+ lines appearing in 2+ locations

**SOLID violation:** DRY — Don't Repeat Yourself

**Reference:** https://refactoring.guru/smells/duplicate-code
