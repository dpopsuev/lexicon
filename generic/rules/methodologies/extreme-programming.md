---
id: extreme-programming
title: "Extreme Programming (XP)"
description: "Technical practices for high-quality software: TDD, pair programming, CI, simple design"
labels: [methodology, agile, xp]
---

# Extreme Programming (XP)

**Core idea:** Take good practices to their extreme. If testing is good, test first (TDD). If integration is good, integrate continuously (CI). If simplicity is good, do the simplest thing that works (YAGNI).

## The 12 Practices

| Practice | What |
|---|---|
| **Pair Programming** | Two developers, one workstation, continuous code review |
| **TDD** | Write the failing test before the code |
| **Continuous Integration** | Integrate and test every few hours |
| **Collective Code Ownership** | Anyone can change any code |
| **Simple Design** | Simplest solution for current needs (YAGNI) |
| **Refactoring** | Improve structure without changing behavior |
| **Small Releases** | Deploy frequently (every 1-3 weeks) |
| **Coding Standards** | Consistent style across the team |
| **Sustainable Pace** | No overtime — maintain velocity indefinitely |
| **Planning Game** | Customers estimate value, developers estimate effort |
| **On-Site Customer** | Customer representative works with the team daily |
| **System Metaphor** | Shared terminology for the system's design |

## Djinn Alignment

- TDD → ROGYB cycle (Red first, always)
- Simple Design → "don't add features beyond what was asked"
- Refactoring → continuous, not a phase
- Small Releases → each step is dogfood-testable

**Reference:** https://en.wikipedia.org/wiki/Extreme_programming
