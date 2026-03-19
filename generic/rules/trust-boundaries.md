---
id: trust-boundaries
title: Trust Boundaries
description: Identify, classify, and verify every trust boundary using STRIDE, Zero Trust, Least Privilege, and Blast Radius analysis.
labels: [security, architecture]
---

# Trust Boundaries

**Every point where data or control crosses between components with different privilege levels is a trust boundary. Verify at every boundary.**

## Identifying Trust Boundaries

A trust boundary exists wherever:

- Two components run with different privileges (user vs admin, agent vs host)
- Data crosses a network (MCP call, HTTP request, Unix socket)
- A process talks to an external system (database, API, LLM)
- Configuration changes the behavior of a downstream component (Djinnfile, env vars)
- A less-trusted component's output becomes a more-trusted component's input

If you can't draw the boundary on a diagram, you don't understand the system.

## STRIDE Threat Model

Apply at each boundary. Every boundary gets six questions:

| Threat | Question | Example |
|--------|----------|---------|
| **S**poofing | Can an attacker pretend to be the other side? | Agent impersonates operator |
| **T**ampering | Can data be modified in transit or at rest? | MCP response altered |
| **R**epudiation | Can an actor deny performing an action? | Agent claims it never deleted the file |
| **I**nformation Disclosure | Can sensitive data leak across the boundary? | Credentials in logs, secrets in error messages |
| **D**enial of Service | Can the boundary be overwhelmed or blocked? | Agent floods MCP adapter with requests |
| **E**levation of Privilege | Can a low-privilege actor gain high-privilege access? | Agent escapes sandbox, Mod tier writes outside scope |

## DREAD Risk Scoring

When STRIDE finds a threat, score it with DREAD to prioritize:

| Factor | Score 1-3 | Question |
|--------|-----------|----------|
| **D**amage | How bad if exploited? | 1=low, 2=medium, 3=catastrophic |
| **R**eproducibility | How easy to reproduce? | 1=rare, 2=sometimes, 3=always |
| **E**xploitability | How easy to exploit? | 1=hard, 2=moderate, 3=trivial |
| **A**ffected users | How many impacted? | 1=few, 2=some, 3=all |
| **D**iscoverability | How easy to find? | 1=hidden, 2=findable, 3=obvious |

Sum the scores. 12-15 = critical. 8-11 = high. 5-7 = medium. Below 5 = low.

## Zero Trust Principles

1. **Never trust, always verify.** No implicit trust based on network location, container co-location, or prior authentication.
2. **Assume breach.** Design every boundary as if the other side is compromised.
3. **Least privilege.** Grant minimum access needed for the task. Revoke when the task ends.
4. **Verify explicitly.** Authenticate and authorize every request, every time.

## Blast Radius Analysis

For each boundary, answer: **what's the worst that happens if it's breached?**

Size the defense proportionally:
- Small blast radius (one test fails) -- log and continue
- Medium blast radius (one service affected) -- alert and contain
- Large blast radius (data breach, production outage) -- operator approval gate, defense in depth

## Confused Deputy Prevention

A privileged component must not act on behalf of an unprivileged component without verifying authorization. The question is not "can I do this?" but "should I do this on behalf of the requester?"

- Validate the requester's authorization, not just your own
- Pass credentials through, don't elevate implicitly
- Log who requested what, not just what happened

## Capability-Based Security

Access is granted by holding a capability token, not by identity alone:
- Capabilities are specific (read file X, not read all files)
- Capabilities are revocable (task ends, token expires)
- Capabilities are non-transferable (agent A's token doesn't work for agent B)

## Agent Behavior

- Before implementing any cross-boundary interaction, identify the trust boundary
- Apply STRIDE to the boundary -- document findings
- Score findings with DREAD if severity is unclear
- Verify Least Privilege: is the caller getting minimum access?
- Check for Confused Deputy: is authorization verified at the boundary, not assumed?

## Complements

- `security-analysis` -- OWASP Top 10 checklist applied at boundaries
- `defense-in-depth` -- no single boundary verification is sufficient
- `testing-methodology` -- contract tests verify boundary behavior
- `solid-principles` -- Dependency Inversion ensures clean boundary interfaces
