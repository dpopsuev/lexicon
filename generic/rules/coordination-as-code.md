---
id: coordination-as-code
title: Coordination as Code
description: Replace conversational coordination with codified artifacts. Decisions in specs, not meetings. Conventions in rules, not tribal knowledge. Contracts at boundaries, not Slack threads.
labels: [architecture, process]
---

# Coordination as Code

**If it's not codified, it doesn't exist. Decisions live in artifacts, not conversations.**

## The Problem

Software delivery's bottleneck is not writing code. It's coordination:

- 20% of time goes to code. 80% goes to alignment, waiting, context switching, and re-discovering decisions.
- Decisions made in meetings live in someone's head until that person leaves.
- Architecture rationale is oral tradition. New team members take months to absorb it.
- Cross-team dependencies are negotiated in Slack threads that nobody can find later.
- Pressure compresses quality -- shortcuts taken under deadline pressure become permanent debt.

## The Principle

Every coordination artifact that lives in conversation must be codified into a machine-readable, version-controlled, queryable form:

| Conversational | Codified |
|---------------|----------|
| "We decided in the meeting to..." | Spec with decision section, rationale, alternatives considered |
| "Ask Sarah, she knows how that works" | Architecture in Locus, decisions in Scribe, rules in Lex |
| "We always do it this way here" | Lex rule with `always_apply` |
| "Can you review my PR for X?" | Template with required checklist, mechanical verification via Locus/Limes |
| "Team B needs to expose an API for us" | Contract test at port boundary -- machine-verified compatibility |
| "Is this safe to deploy?" | Operational readiness checklist, blast radius analysis, gate validation |
| "What's the status of the project?" | Work graph in Scribe -- queryable, not narrated |

## The Constraint Funnel

Each layer narrows the agent's (and the human's) degrees of freedom:

```
Need   -- why does the business care?
Goal   -- what does success look like?
Spec   -- how will we achieve it?
Task   -- what specific work to do?
Template -- what must you think about?
Rule   -- how must you behave?
Code   -- the actual output
```

Without outer layers, the inner layers have infinite freedom and produce plausible but aimless output. The funnel IS the coordination mechanism.

## Conway's Law (and the Inverse)

Organizations produce systems that mirror their communication structures. If coordination is conversational, the architecture is chaotic. If coordination is codified, the architecture is structured.

The Inverse Conway Maneuver: deliberately codify your coordination to get the architecture you want. Team boundaries become API boundaries. Meetings become specs. Tribal knowledge becomes rules.

## Rules

1. **Decisions go in specs, not meetings.** If a decision was made, it has a spec with a decision section. If there's no spec, the decision doesn't exist.
2. **Conventions go in rules, not heads.** If "we always do X," write a Lex rule. If there's no rule, the convention doesn't exist.
3. **Architecture goes in code, not wikis.** Locus scans the actual code. Architecture diagrams that drift from reality are worse than no diagrams.
4. **Contracts go in tests, not Slack.** If two components must agree on an interface, there's a contract test. If there's no test, there's no contract.
5. **Status goes in the work graph, not standups.** Scribe artifacts have status fields. Query them, don't narrate them.
6. **Quality goes in gates, not reviews.** Templates force required analysis. Locus and Limes mechanically verify. The membrane doesn't negotiate.

## Anti-Patterns

- **Meeting-driven development.** The decision was made in the meeting but never written down. Three months later, nobody remembers why.
- **Tribal knowledge.** "Ask Sarah" is a bus factor, not a knowledge management strategy.
- **Review as coordination.** Using PR reviews to enforce architecture, style, and conventions. That's what rules, templates, and mechanical verification are for. Reviews are for judgment calls.
- **Dashboard as documentation.** A Grafana dashboard is not an SLO definition. The dashboard visualizes; the spec defines.
- **Oral onboarding.** New team members learn by sitting next to someone for months. The constraint funnel should make them productive in hours.

## Complements

- `integrate-early` -- codify interfaces before implementing internals
- `hexagonal-architecture` -- ports and adapters ARE codified boundaries
- `testing-methodology` -- tests ARE codified expectations
- `scribe-workflow` -- the work graph IS codified coordination
- `trust-boundaries` -- trust boundaries ARE codified security contracts
