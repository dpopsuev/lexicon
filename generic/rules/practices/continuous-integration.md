---
id: continuous-integration
title: "Continuous Integration (CI)"
description: "Merge frequently, test automatically — catch integration errors early"
labels: [practice, xp, ci, devops]
---

# Continuous Integration (CI)

**Core idea:** Developers merge code into the main branch frequently (at least daily). Every merge triggers automated build and tests. Integration errors are caught within minutes, not weeks.

## Rules

1. Maintain a single source repository
2. Automate the build
3. Make the build self-testing
4. Everyone commits to mainline every day
5. Every commit triggers a build
6. Fix broken builds immediately
7. Keep the build fast
8. Test in a production-like environment

## Djinn Alignment

- Mirage Diff → Commit is a CI step (changes verified before promotion)
- HITL gate ensures human review before mainline merge
- Agentic Flywheel micro-circuit: Build → Lint → Test → Integration

**Reference:** https://en.wikipedia.org/wiki/Continuous_integration
