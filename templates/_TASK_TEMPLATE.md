---
id: task-template
title: Task Template
kind: task
description: Execution artifact — the unit of deliverable work
labels: [process, template]
---

# {Task Title}

> **Status:** `draft` | `active` | `complete` | `cancelled`
> **Implements:** {spec or bug ID}

---

## Goal

One sentence. The task is complete when this is true, and not before.

> {What does "done" look like? Measurable, binary — either it's true or it isn't.}

---

## Context

Background, motivation, relevant design decisions. Link to specs, docs, prior art.

> {Why does this task exist? What prior decisions led here?}

### Current Architecture

(Optional. Mermaid diagram of affected components before this task.
Required when changing component boundaries, data flow, or interfaces.
Exempt for behavior-only changes. If exempt, write "No architectural changes.")

### Desired Architecture

(Optional. Mermaid diagram of target state after this task.)

---

## Security Assessment

OWASP spot-check for trust boundaries touched by this task.
If none, write: "No trust boundaries affected."

| OWASP | Finding | Mitigation |
|-------|---------|------------|

---

## Execution Strategy

How the work will be approached. Reference the integrate-early rule, TDD approach, etc.

> {e.g., "Top-to-bottom. Interfaces first, contract tests second, stub implementations third, real internals last."}

---

## Checklist

Ordered steps. Each step is a discrete, completable unit of work. Order matters — earlier steps unblock later ones.

Mark status: `[ ]` pending, `[~]` in progress, `[x]` done, `[-]` cancelled.

- [ ] **Step 1** — Description.
- [ ] **Step 2** — Description.
- [ ] ...
- [ ] **Validate (green)** — All tests pass. All acceptance criteria met.
- [ ] **Tune (blue)** — Refactor: naming, structure, duplication, documentation. No behavior changes.
- [ ] **Validate (green)** — All tests still pass after tuning. Final confirmation.

---

## Acceptance Criteria

Given/When/Then or structural invariants that prove the goal is met.

> ```
> Given {precondition}
> When {action}
> Then {expected outcome}
> ```

---

## Notes

Running log of decisions, discoveries, and course corrections made during execution.
Newest at the top.

> {YYYY-MM-DD HH:MM — What happened, what was decided, why.}
