---
id: workspace-hygiene
title: Workspace Hygiene (5S for Agent Workspaces)
description: 5S methodology applied to agent workspaces — Sort, Set in Order, Shine, Standardize, Sustain.
labels: [5s, hygiene, workspace, agentic]
---

# Workspace Hygiene

**A clean workspace is a productive workspace. Apply 5S to every agent scope.**

Adapted from Toyota's 5S methodology for AI agent workspaces.

## The 5S for Agent Workspaces

### 1. Sort (Seiri) — Remove What Doesn't Belong
- Dead code: unused functions, unreachable branches, commented-out blocks
- Unused dependencies: imports that nothing calls
- Stale files: generated artifacts from previous runs
- Detection: built-in arch tool identifies unused symbols and imports

### 2. Set in Order (Seiton) — Organize by Convention
- Files in correct packages per hexagonal architecture (domain/port/adapter/app)
- Test files next to source (`*_test.go` co-located)
- Config in standard locations (project config, user config)
- Scope types: System (persistent), Operations (ephemeral), Prototype (experimental)

### 3. Shine (Seiso) — Make Problems Visible in 5 Seconds

Run a build, lint, and test pass when entering an unfamiliar codebase. Any new contributor should be able to identify failing checks within 5 seconds of reading the CI output. A codebase where "build is red but that's normal" has failed this step.

### 4. Standardize (Seiketsu) — Make Hygiene Routine

Hygiene rules are enforced by tooling, not by convention. Every commit passes lint. Every task has acceptance criteria before work starts. Every trust boundary has at least one negative test. "Should" is not enforcement.

### 5. Sustain (Shitsuke) — Embed Through Discipline

Hygiene degrades without active maintenance. Check drift — desired state vs actual state — at the start of each sprint. Architecture scores, test coverage, and dead code metrics should trend in one direction: better.

### 6. Safety (6th S) — Trust Boundaries as Hazards

Every trust boundary is a hazard zone. Mark it, gate it, test it. External input, inter-service calls, and file I/O are trust boundaries. Internal function calls within a single process are not.

## Agent Behavior

- Enter a codebase: run build, lint, test before making changes
- Exit a task: verify no new lint violations or failing tests
- Between tasks: delete temporary files, close resources, clean up generated artifacts

## Complements

- `effective-go` — language-specific hygiene standards
- `hexagonal-architecture` — the organizational structure 5S maintains
- `trust-boundaries` — the hazard identification for the 6th S
