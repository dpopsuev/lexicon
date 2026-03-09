---
id: contract-workflow
title: Contract Workflow
kind: rule
description: How to create, size, iterate, and complete contracts using Lex templates and Scribe storage
labels: [process, contracts]
---

# Contract Workflow

Lex defines contract shape (this template). Scribe stores and manages contracts.

## Creating a contract

1. Use the Scribe `create_artifact` tool with `kind: contract`.
2. Set `scope` to the project name, `status` to `draft`.
3. Include: goal (binary pass/fail), acceptance criteria, depends_on.

## Sizing

Split a contract that exceeds any threshold:

- More than 15 tasks: split into must/should/nice sub-contracts.
- More than 3 phases: each phase becomes a separate contract.
- Mixed urgency tiers: split by tier.

### Urgency tiers

- **MUST** -- blocks the current goal gate. Without this, the goal cannot be met.
- **SHOULD** -- improves quality significantly. Works without it but weaker.
- **NICE** -- architecture evolution, post-goal vision. Not blocking.

## Iteration

Active contracts accumulate insights from calibration runs, discussions, and field observations.

- After calibration: check results for patterns mapping to contract tasks; log findings in contract notes.
- During discussions: if an insight touches a monitored contract, propose adding it to notes or promoting to a task.
- Cross-contract: if one contract's findings affect another, log in the affected contract.

## Lifecycle

- `draft` -- planned, not being executed.
- `active` -- currently being executed. Verify dependencies are met before activating.
- `complete` -- finished. All acceptance criteria met, tests green.

Use Scribe `set_field` to transition status.

## Persistence triggers

When a conversation produces reusable knowledge, persist to the appropriate Scribe artifact:

- Deep reference or analysis: `kind: spec`
- Execution plan with tasks: `kind: contract`
- Security finding: `kind: spec` with label `security-case`
- New constraint or convention: Lex rule
- Domain terms: `kind: spec` with label `glossary`
