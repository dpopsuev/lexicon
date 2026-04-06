---
id: testing-methodology
title: Testing Methodology
description: Coverage matrix, ROGYB cycle, BDD/Gherkin, security-as-test
labels: [testing, tdd, bdd, rogyb]
---

# Testing Methodology

**BDD and Gherkin:** Start stories with acceptance criteria in Gherkin (Given/When/Then).

## Coverage matrix

Before writing or modifying production code, verify that each applicable layer has adequate coverage. If a layer is not applicable, state why.

| Layer | Scope | Examples |
|-------|-------|----------|
| **Unit** | Function-level branches and edge cases | Parsers, builders, scoring, error paths, nil/empty inputs |
| **Integration** | Cross-boundary interactions | Transport layers, config loading, external tool calls |
| **Contract** | API schemas and interface compliance | Interface contracts, adapter compliance |
| **E2E** | Full pipeline validation | End-to-end flow from input to output; stub and real scenarios |
| **Concurrency** | Shared state and parallel paths | Race detectors, deadlock-free synchronization |
| **Security** | Trust boundaries and external data | OWASP checklist, input validation |

## Development cycle: Red-Orange-Green-Yellow-Blue

Every code increment follows five phases. Orange and Yellow are the observability pair -- do not skip either.

### 1. Red -- write a failing test

Capture the intended behavior or reproduce the bug. Run it. It **must fail**. If it passes, the test does not cover the change.

### 2. Orange -- instrument problem signals

Before implementing the fix, add logging and instrumentation that surfaces **failures, errors, and anomalies**. Orange output answers: *"What went wrong? Where? Why?"*

Use structured logging fields for machine-readability (e.g., case IDs, step names, error codes as distinct fields, not interpolated into message strings).

### 3. Green -- make the test pass

Implement the minimal production code to pass the Red test. Run the full affected test suite. Everything must be green.

If the change touches a circuit or pipeline, run stub calibration before declaring green.

### 4. Yellow -- instrument success signals

With the code working, add logging that surfaces **healthy operation and key decisions**. Yellow output answers: *"What happened? What did we choose? How long did it take?"*

Use structured fields for latency, throughput, and decision metadata (same principle as Orange).

### 5. Blue -- refactor

With tests green and full observability (Orange + Yellow) in place: remove duplication, improve naming, extract helpers. Review log levels. If the change touched AI inference, review and update prompts.

### Orange vs Yellow summary

| Phase | Signal type | Log level | Timing | Answers |
|-------|-------------|-----------|--------|---------|
| **Orange** | Problem / error / anomaly | `Warn`, `Error` | Before Green | *"What went wrong?"* |
| **Yellow** | Success / decision / metric | `Info`, `Debug` | After Green | *"What happened? Are we healthy?"* |

## Advanced testing techniques

Beyond the coverage matrix, consider these techniques for higher-confidence verification:

| Technique | What It Does | When to Apply |
|-----------|-------------|---------------|
| **Contract testing** | Consumer defines expected interface. Producer validates against it. Catches integration drift. | Any port/adapter boundary. MCP adapters must pass contract tests. |
| **Property-based testing** | Define invariants. Framework generates random inputs to falsify them. Finds edge cases humans miss. | Parsers, serializers, state machines, any "for all inputs X, property Y holds." |
| **Mutation testing** | Inject bugs into code. If tests still pass, the tests are too weak. Measures test quality itself. | After Green phase. Verify tests actually catch regressions. |
| **Chaos engineering** | Intentionally break things. Verify the system recovers gracefully. | Pre-production. "What happens when adapter X goes down mid-operation?" |
| **Fuzzing** | Feed malformed/random input to find crashes and security issues. | Trust boundaries. Parsers, deserializers, protocol handlers. |

These are not required on every change. Apply when the coverage matrix indicates a gap or when the blast radius of failure is high (see `trust-boundaries` rule).

## Security as test

Security tests prove the system **does not** exhibit harmful behavior. For every trust boundary touched:

1. Consult the OWASP checklist (see `security-analysis` rule).
2. Write at least one negative test.
3. Document the finding if a risk is identified.

## Quick reference

```
  1. ASSESS  -- verify coverage matrix (this rule)
  2. RED     -- write failing test
  3. ORANGE  -- instrument problem signals (errors, anomalies)
  4. GREEN   -- implement; make test pass
  5. YELLOW  -- instrument success signals (decisions, metrics)
  6. BLUE    -- refactor; tune log levels
```
