---
id: defense-in-depth
title: Defense in Depth
description: Layer defenses so no single failure compromises the system. Swiss Cheese model, Safety-I/II, and the quality membrane.
labels: [security, architecture]
---

# Defense in Depth

**No single defense is sufficient. Stack layers so that each layer's holes are covered by another.**

## Swiss Cheese Model (James Reason)

Every defense layer has holes. Individual layers fail. But when layers are stacked, the probability of a failure passing through all layers drops exponentially.

Applied to the DevOps loop:

| Layer | What It Catches | What It Misses |
|-------|----------------|----------------|
| Plan (threat model) | Design flaws, missing requirements | Implementation bugs |
| Code (sandbox, lint) | Syntax errors, style violations, sandbox escapes | Logic errors, integration bugs |
| Verify (tests, scan) | Functional regressions, known vulnerabilities | Unknown vulnerabilities, emergent failures |
| Package (sign, attest) | Tampered artifacts, unsigned images | Correctly-signed but vulnerable images |
| Deploy (canary, gates) | Deployment failures, obvious regressions | Slow-burn performance degradation |
| Observe (metrics, trace) | Runtime anomalies, SLO breaches | Novel failure modes with no metric |
| React (auto-remediate) | Known failure patterns | Unknown unknowns |

No single layer is the "security layer" or the "quality layer." Security and quality are the membrane around all layers.

## Safety-I and Safety-II (Hollnagel)

| Approach | Focus | In Practice |
|----------|-------|-------------|
| **Safety-I** | Prevent bad things from happening | Vulnerability scanning, policy enforcement, sandbox isolation, watchdogs |
| **Safety-II** | Ensure good things happen reliably | Test-gated promotion, contract tests, observability, SLO verification |

Most systems only practice Safety-I (block the bad). Mature systems also practice Safety-II (prove the good). Both are necessary. Watchdogs catch anomalies (Safety-I). Test gates prove correctness (Safety-II).

## NIST Cybersecurity Framework

Five functions, mapped to the DevOps loop:

| Function | Loop Phase | Activity |
|----------|-----------|----------|
| **Identify** | Plan | Threat model, asset inventory, trust boundary map |
| **Protect** | Code, Verify, Package | Sandbox isolation, least privilege, signing, attestation |
| **Detect** | Observe | Anomaly detection, runtime monitoring, log analysis |
| **Respond** | React | Incident response, auto-remediate, operator escalation |
| **Recover** | Operate | Rollback, restore, postmortem, lessons learned |

## The Quality Membrane

Security and Quality are not phases -- they are the membrane that wraps every phase:

- At **Plan**: Is the threat model complete? Are acceptance criteria defined?
- At **Code**: Is the sandbox isolated? Are trust boundaries respected?
- At **Verify**: Do tests cover the happy AND unhappy paths? Are vulnerabilities scanned?
- At **Package**: Is the artifact signed? Is provenance attestable?
- At **Deploy**: Is rollback possible? Is the canary healthy?
- At **Observe**: Are SLOs met? Are anomalies detected?
- At **React**: Is the response proportional? Is the audit trail complete?

Every MCP port boundary is a verification point in the membrane.

## Applying Layers

When adding a new feature or boundary:

1. **Identify the layers** that touch this change (which phases?)
2. **For each layer**, ask: what does this layer catch? What does it miss?
3. **Stack verification**: the next layer must catch what the previous misses
4. **No single layer is sufficient** -- if removing one layer would leave a gap, add another

## Anti-Patterns

- **Single gate syndrome.** "We have tests, so we're safe." Tests miss integration failures, vulnerabilities, and runtime anomalies.
- **Security as a phase.** Putting security only at Verify. Security is a membrane, not a stage.
- **Quality theater.** Running tests that always pass. If you've never seen a test fail, the test is too weak.
- **Alert fatigue.** So many layers that the operator ignores warnings. Layers must be tuned, not just added.

## Complements

- `trust-boundaries` -- STRIDE analysis at each layer's boundary
- `security-analysis` -- OWASP checklist at each trust boundary
- `testing-methodology` -- ROGYB ensures both Safety-I and Safety-II
- `code-smells` -- code quality is a layer in the membrane
