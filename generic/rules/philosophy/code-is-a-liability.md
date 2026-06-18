---
id: code-is-a-liability
title: Code is a Liability
description: Every line of code is a maintenance burden. The goal is maximum functionality at minimum code. Prefer deletion over addition.
labels: [pragmatic, philosophy, process, constraints]
---

# Code is a Liability

**Code is not an asset. The functionality it delivers is. Every line written is a bet that its value exceeds its maintenance cost — forever.**

The software is the asset. The code that implements it is the liability. These are not the same thing.

## The Accounting

Each line of code you add to a codebase creates an obligation:

- It must be read by the next engineer
- It must be updated when requirements change
- It must be tested when dependencies upgrade
- It must be debugged when it breaks in production
- It must be understood before anything around it can change

None of these costs are paid once. They compound. A function written today is still paying interest in five years.

## The Carrying Capacity Trap

Existing code consumes team capacity. As a codebase grows, more of every sprint goes to maintenance, bugs, and understanding — not new functionality. This is **carrying capacity**: the overhead required just to keep the system running.

The trap: adding features increases carrying capacity, which reduces the bandwidth to add features, which creates pressure to cut corners, which increases carrying capacity further. The vicious cycle ends in crisis mode or a v2 rewrite.

The only exit is keeping the codebase small.

## Rules

1. **Prefer deletion over addition.** The best solution to a problem is often removing the code that caused it. Celebrate PRs that reduce line count.

2. **Write only what the task requires.** Code written for hypothetical future needs is pure liability with no offsetting asset. It will be maintained, misunderstood, and worked around — but never used.

3. **Every line is a bet.** Before writing, ask: will the value this delivers exceed its lifetime maintenance cost? If you cannot answer confidently, don't write it.

4. **Dead code is the worst kind.** It pays the full maintenance tax with zero functional return. Delete it immediately. Git has history.

5. **Small diffs are a virtue.** A PR that achieves the same result in fewer lines is strictly better. Fewer lines means less to review, less to break, less to understand.

6. **Abstractions multiply liability.** A premature abstraction is not one unit of debt — it is debt for every future engineer who must understand it before touching anything near it.

## The Inversion

Senior engineers are not distinguished by how much code they write. They are distinguished by how much code they *don't* write — and how much they delete.

The measure of engineering quality is not lines of code shipped. It is value delivered per line maintained.

## Complements

- `senior-engineer-mindset` — minimum viable diff; YAGNI as discipline
- `kiss-directives` — mechanical constraints that enforce this principle for agents
- `code-smells` — smells are liability signals: dead code, speculative generality, middle man
- `effective-go` — §18: stdlib first; avoid dependencies that add liability without adding value
