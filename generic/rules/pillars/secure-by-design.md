---
id: secure-by-design
title: Secure by Design
description: Security properties are architectural decisions made at product inception, not patches applied after the fact.
labels: [security, architecture, owasp, cisa]
---

# Secure by Design

**Security is not a feature added at the end. It is a property of the architecture, present from the first design decision.**

## The Core Claim

A system is **Secure by Design** when its threat model, trust boundaries, access controls, and failure semantics are defined before the first line of code is written — and revisited every time the architecture changes.

The alternative — **security-as-retrofit** — produces systems where defenses are bolted on after the structure is fixed, creating permanent mismatch between the shape of the problem and the shape of the solution.

OWASP Secure Product Design Cheat Sheet distinguishes two stages:

| Stage | What it means |
|-------|--------------|
| **Product inception** | Core security architecture: authentication model, authorization model, data classification, encryption posture, trust boundaries, failure semantics |
| **Continuous product design** | Every sprint that changes a trust boundary, adds an external input, or modifies access control reruns the threat model for that change |

Both stages are mandatory. A secure inception followed by insecure incremental changes degrades to security-as-retrofit over time.

## Principles (Saltzer & Schroeder 1975)

The eight principles from Saltzer & Schroeder's canonical 1975 paper remain the foundation:

| Principle | Meaning |
|-----------|---------|
| **Economy of Mechanism** | Keep the design as simple as possible. Complexity is the enemy of security. |
| **Fail-Safe Defaults** | Base decisions on permission, not exclusion. Default to no access; grant explicitly. |
| **Complete Mediation** | Every access to every object must be checked against the access control policy. No caching of authorization results without invalidation. |
| **Open Design** | The security of a mechanism must not depend on the secrecy of its design. Kerckhoffs's principle applied to systems. |
| **Separation of Privilege** | A protection mechanism requiring two keys is more robust than one requiring a single key. Multi-factor; dual approval. |
| **Least Privilege** | Every program and user operates with the minimum access necessary. Revoke when the reason lapses. |
| **Least Common Mechanism** | Minimize shared mechanisms between users — shared state creates covert channels and side channels. |
| **Psychological Acceptability** | Security mechanisms must be usable; if they are too difficult, users route around them. |

## What It Looks Like in Practice

**Before writing code:**
- Produce a threat model (STRIDE) for every trust boundary in the design
- Classify all data the system will handle; apply encryption requirements per classification
- Define the authentication and authorization model explicitly (who can do what to which resources under what conditions)
- Decide what the system does on failure: fail open, fail closed, fail safe

**During development:**
- Every new external input is an attack surface; add it to the threat model
- Every new dependency is a supply chain risk; evaluate before adding
- Every new trust boundary needs: authentication, authorization, input validation, output encoding, audit logging
- Code review includes a security angle on every PR that touches a boundary

**System properties that emerge from Secure by Design:**
- Authentication and authorization are centralized, not scattered across modules
- All input validation happens at trust boundaries, not deep in business logic
- Secrets are never in source code, environment variables passed in plaintext, or logs
- Failures produce safe, minimal output — no stack traces or internal state leaking to external callers
- Audit logs are tamper-evident and cover all security-relevant events

## Threat Modeling (STRIDE)

For each trust boundary, evaluate:

| Threat | Question | Control |
|--------|----------|---------|
| **Spoofing** | Can an attacker impersonate a principal? | Strong authentication |
| **Tampering** | Can an attacker modify data in transit or at rest? | Integrity checks, signing |
| **Repudiation** | Can an actor deny an action? | Audit logging |
| **Information Disclosure** | Can an attacker read data they shouldn't? | Least privilege, encryption |
| **Denial of Service** | Can an attacker exhaust resources? | Rate limiting, resource quotas |
| **Elevation of Privilege** | Can an attacker gain more access than granted? | Authorization checks at every layer |

## Anti-Patterns

- **Security theater.** Scanning for known CVEs while ignoring design flaws. Vulnerability scanning is necessary but not sufficient.
- **Perimeter-only security.** Treating the external firewall as the security boundary. When the perimeter is breached, there is no interior defense.
- **Auth as middleware.** Adding authentication as a decorator on top of a system that was never designed with trust boundaries. The decorator can be bypassed.
- **Security by obscurity.** Relying on attackers not knowing the design. Knowledge leaks; designs must be secure even when fully known.
- **One-time threat modeling.** Running STRIDE at inception, then never revisiting as the system evolves.

## Agent Behavior

When designing or reviewing a system:

1. Ask: **where are the trust boundaries?** Every point where data crosses from one trust domain to another is a boundary.
2. For each boundary: **STRIDE analysis** — what can an attacker do at this boundary?
3. Verify the eight Saltzer & Schroeder principles are satisfied in the design.
4. On every PR that touches a boundary: re-run the relevant STRIDE categories.
5. Flag Secure by Design violations as **high** severity; design flaws are harder to fix than implementation bugs.

## Complements

- `secure-by-default` — the product-facing expression: ship secure configurations out of the box
- `defense-in-depth` — layer defenses so no single failure is catastrophic
- `trust-boundaries` — STRIDE analysis at each trust boundary
- `security-analysis` — OWASP Top 10 checklist at each boundary

## References

- Saltzer, J. H. & Schroeder, M. D. (1975). [The Protection of Information in Computer Systems](https://web.mit.edu/Saltzer/www/publications/protection/). *Proc. IEEE* 63(9).
- CISA. (2023). [Shifting the Balance of Cybersecurity Risk: Principles and Approaches for Secure by Design Software](https://www.cisa.gov/resources-tools/resources/secure-by-design). Joint advisory with NSA, FBI, and 17 international partners.
- OWASP. [Secure Product Design Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Product_Design_Cheat_Sheet.html).
- NIST SP 800-53 Rev 5. SA-8: Security and Privacy Engineering Principles (including SA-8(23) Secure Defaults).
