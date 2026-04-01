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
- Config in standard locations (`djinn.yaml`, `~/.djinn/`)
- Scope types: System (persistent), Operations (ephemeral), Prototype (experimental)

### 3. Shine (Seiso) — Make Problems Visible in 5 Seconds
- Architecture health scan on scope entry — cycles, violations, hot spots
- Any newcomer to the workspace should spot problems within 5 seconds of looking at the health report
- Dashboard shows: build status, test status, lint status, architecture score

### 4. Standardize (Seiketsu) — Make Hygiene Routine
- Built-in checklists enforced via GateResolver — not optional, not "should"
- Language-specific rules applied automatically (Go → effective-go checklist)
- Every commit passes lint. Every task has acceptance criteria. Every trust boundary has a security assessment.

### 5. Sustain (Shitsuke) — Embed Through Discipline
- Flywheel: each iteration improves the workspace
- Drift detection: desired state vs actual state, checked every sprint
- Retrospective: architecture scores before/after each sprint
- No regression: new code cannot introduce violations that didn't exist before

### 6. Safety (6th S) — Trust Boundaries as Hazards
- Every trust boundary is a hazard zone — mark it, gate it, test it
- Agent cannot operate on files outside its scope (VFS containment)
- Sandbox isolation: physical (Misbah jail) + logical (policy enforcer)

## Agent Behavior

- On scope entry: run health scan (built-in arch tool)
- On scope exit: verify no new violations introduced
- Between tasks: clean up temporary files, close unused resources
- On idle: report workspace health to dashboard

## Complements

- `effective-go` — language-specific hygiene standards
- `hexagonal-architecture` — the organizational structure 5S maintains
- `trust-boundaries` — the hazard identification for the 6th S
