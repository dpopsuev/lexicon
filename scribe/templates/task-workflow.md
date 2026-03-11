---
id: task-workflow
title: Task Workflow
kind: rule
description: How to create, size, iterate, and complete tasks using Lex templates and Scribe storage
labels: [process, tasks]
---

# Task Workflow

Lex defines task shape (see `_TASK_TEMPLATE.md`). Scribe stores and manages tasks.

## Creating a task

1. Use the Scribe `create_artifact` tool with `kind: task`.
2. Set `scope` to the project name, `status` to `draft`.
3. Include: goal (binary pass/fail), acceptance criteria, depends_on.
4. Populate expected sections: `context`, `checklist`, `acceptance`.

## Sizing

Split a task that exceeds any threshold:

- More than 15 checklist items: split into must/should/nice sub-tasks.
- More than 3 phases: each phase becomes a separate task.
- Mixed urgency tiers: split by tier.

### Urgency tiers

- **MUST** -- blocks the current goal gate. Without this, the goal cannot be met.
- **SHOULD** -- improves quality significantly. Works without it but weaker.
- **NICE** -- architecture evolution, post-goal vision. Not blocking.

## Iteration

Active tasks accumulate insights from calibration runs, discussions, and field observations.

- After calibration: check results for patterns mapping to task steps; log findings in task notes.
- During discussions: if an insight touches a monitored task, propose adding it to notes or promoting to a checklist item.
- Cross-task: if one task's findings affect another, log in the affected task.

## Lifecycle

- `draft` -- planned, not being executed.
- `active` -- currently being executed. Verify dependencies are met before activating.
- `complete` -- finished. All acceptance criteria met, tests green.

Use Scribe `set_field` to transition status.

## Persistence triggers

When a conversation produces reusable knowledge, persist to the appropriate Scribe artifact:

- Deep reference or analysis: `kind: spec`
- Execution plan with tasks: `kind: task`
- Security finding: `kind: spec` with label `security-case`
- New constraint or convention: Lex rule
- Domain terms: `kind: spec` with label `glossary`
