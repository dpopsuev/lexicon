---
id: off-the-shelf-modules
title: Off-the-Shelf Module Policy
description: Prefer mature, community-backed modules over bespoke implementations when justified
labels: [dependencies, governance]
---

# Off-the-Shelf Module Policy

**Prefer mature, community-backed modules over bespoke implementations -- but only when the trade-off is justified.**

## Maturity gate (all must pass to adopt)

| Criterion | Threshold |
|-----------|-----------|
| **Stars** | >= 1,000 GitHub stars (community validation) |
| **Maintenance** | Commit in last 12 months (not abandoned) |
| **API stability** | Semantic versioning; no breaking changes in last 2 major releases |
| **License** | MIT, Apache-2.0, or BSD (project-compatible) |
| **Dependency weight** | Transitive dep count proportional to LOC replaced (don't pull 50 deps to save 30 LOC) |

## Decision matrix

| Decision | Criteria |
|----------|----------|
| **Adopt** | Module passes maturity gate AND replaces >= 100 LOC of bespoke code (or eliminates a known bug class) |
| **Keep bespoke** | Module fails maturity gate, OR replaces < 100 LOC, OR domain logic is too specific for any generic library |
| **Watch** | Module is promising but immature; document in architecture notes for future re-evaluation |

## Agent behavior

- Before writing > 100 LOC of utility code, check if a module passes the maturity gate.
- When adding a dependency, document the decision (adopt/bespoke/watch) in architecture docs.
