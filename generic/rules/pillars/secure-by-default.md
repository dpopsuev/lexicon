---
id: secure-by-default
title: Secure by Default
description: Products ship in a hardened configuration. Insecurity requires explicit, deliberate opt-in.
labels: [security, architecture, owasp, cisa, nist]
---

# Secure by Default

**A product is Secure by Default when the out-of-the-box configuration is the most secure configuration. Reducing security requires a deliberate, explicit action.**

## The Core Claim (Saltzer & Schroeder: Fail-Safe Defaults)

From Saltzer & Schroeder (1975), Principle 2 — Fail-Safe Defaults:

> "Base access decisions on permission rather than exclusion. The default situation is lack of access, and the protection scheme identifies conditions under which access is permitted."

Translated to modern product design: the product ships locked. Every reduction in security is an opt-in that the operator makes consciously, not a default the operator must remember to turn off.

CISA's 2023 Secure by Design guidance extends this to the software supply chain: technology manufacturers, not end users, must bear the burden of security. When a product requires the customer to take additional steps to be secure, the manufacturer has externalized their security cost onto the customer — and most customers will not take those steps.

## Why Secure by Default Matters

**The security posture of a deployed fleet is the integral of all default decisions.** If a product ships with debug mode enabled, telemetry unencrypted, admin credentials set to `admin/admin`, and all ports open — most deployments will remain in that state indefinitely. The minority of operators who harden the product cannot compensate for the majority who do not.

This means the manufacturer's default choices determine the security posture of the ecosystem — not the operators' individual decisions.

| If the default is... | Result at scale |
|---------------------|----------------|
| **Insecure** (debug on, auth off, ports open) | Most deployments are insecure; breach affects many |
| **Secure** (auth required, ports closed, encryption on) | Most deployments are secure; breach requires active misconfiguration |

Secure by Default moves risk from the many (operators who don't harden) to the few (operators who explicitly disable protections for a known reason).

## What Secure by Default Requires

### At product inception (shipping defaults)

- **Authentication on by default.** No anonymous access without explicit configuration.
- **Minimum-open ports.** Bind to localhost by default; explicit configuration to expose externally.
- **Encryption on by default.** TLS for all inter-service and client-server communication; no plaintext fallback without explicit opt-in.
- **Logging on by default.** Security-relevant events are logged at launch; no `--verbose` needed to produce an audit trail.
- **Least-privilege credentials.** Default service accounts have the minimum permissions for the minimum-scope use case; broader permissions require explicit grant.
- **No hard-coded credentials.** Default passwords / API keys / tokens do not exist in the product; first-run forces credential establishment.
- **Safe error messages.** Error responses to external callers contain no stack traces, internal state, or path information.
- **Strong cipher suites.** Default TLS configuration excludes weak ciphers; downgrade requires explicit configuration.

### At feature design (capability opt-in)

Each new feature that **reduces** security must be:

1. **Not the default.** The feature is off, and turning it on requires explicit configuration.
2. **Documented as a security trade-off.** Operators understand what they are sacrificing.
3. **Audited when enabled.** The security log records that the operator enabled this feature and when.
4. **Scoped.** The insecure feature applies to the minimum scope required, not globally.

```yaml
# WRONG: opt-out security
debug_mode: true        # operator must remember to set false
auth_required: false    # operator must remember to set true
tls_verify: false       # operator must remember to set true

# RIGHT: opt-in insecurity
debug_mode: false       # operator enables explicitly when needed
auth_required: true     # operator disables explicitly with documented reason
tls_verify: true        # operator disables explicitly (e.g., self-signed CA setup)
```

### In agent / AI systems (sandbox defaults)

The same principle applies to AI agent sandboxes:

- **No capabilities by default.** An agent has access to no tools, no filesystem paths, no network targets, no code execution — until explicitly granted in the capability manifest.
- **Deny by default.** An import not in the `python_modules` allowlist is blocked, not allowed.
- **No shell by default.** Subprocess / exec / eval are not available unless the manifest explicitly grants shell capability.
- **No sensitive globals by default.** Secrets, credentials, and internal service handles are not injected into the sandbox unless the manifest lists them.

This is the application of Fail-Safe Defaults to the agent security model: the agent runs with zero trust until the operator extends trust explicitly.

## The Operator Burden Inversion

**Wrong framing:** "We give operators the flexibility to configure security as they need."

This framing puts the security burden on operators. Most will not configure security correctly — either from time pressure, lack of expertise, or because they never read the relevant documentation.

**Right framing:** "We make the secure path the only path that requires no configuration."

The operator who wants the common, secure use case does nothing. The operator who wants a deviation (less security, more flexibility) does explicit work and leaves an audit trail.

This inverts the burden: hardening is not an additional task, it is the absence of action.

## Fail-Safe vs. Fail-Open

**Fail-Safe (correct):** On unexpected error — authorization service unreachable, malformed token, ambiguous policy — **deny access**. The system defaults to the safe state.

**Fail-Open (incorrect):** On unexpected error — deny is failing, so **allow access**. The system defaults to the permissive state.

Fail-Open is often implemented accidentally: the developer checks for an affirmative `allow` signal and proceeds if the check errors, rather than checking for an affirmative `deny` signal and blocking if the check errors.

```python
# WRONG: fail-open
try:
    result = authz.check(user, resource, action)
    if result.allowed:
        proceed()
except AuthzError:
    proceed()  # silently allows on error — fail-open

# RIGHT: fail-safe
try:
    result = authz.check(user, resource, action)
except AuthzError:
    raise PermissionDenied("authorization unavailable")  # fail-safe
if not result.allowed:
    raise PermissionDenied(result.reason)
proceed()
```

## Anti-Patterns

- **Convenience defaults.** Shipping with auth disabled "because it's easier for evaluation." Most evaluations become production deployments.
- **Documentation-only hardening.** "See the hardening guide to secure this product." Most operators will not read it, or will read it too late.
- **Security as a profile.** Shipping a "security profile" that operators opt into. The default profile is then, implicitly, the insecure profile.
- **Backward compatibility as an excuse.** "We can't enable TLS by default because it would break existing deployments." Migration paths exist; permanent insecurity does not need to be one of them.
- **Capability sprawl.** Granting broad capabilities (shell, filesystem, network) as a default because "it's more useful." Usefulness and security are not opposites; usefulness should be composed from narrow, explicit grants.

## Agent Behavior

When designing a product, configuration schema, or agent sandbox:

1. **Enumerate all capabilities** the system can grant: network access, filesystem paths, module imports, shell execution, credential injection.
2. **Default every capability to denied.**
3. **For every opt-in insecurity**, require: explicit configuration key, documentation of the security trade-off, audit log entry when enabled.
4. **Review all error paths** for fail-open behavior; convert to fail-safe.
5. **On any PR that changes a default**: flag for security review. Default changes have ecosystem-wide impact.

## Complements

- `secure-by-design` — security properties are architectural; defaults are one expression of that architecture
- `defense-in-depth` — layered defenses catch what default-hardening misses
- `trust-boundaries` — Fail-Safe Defaults applies at every trust boundary in the STRIDE model

## References

- Saltzer, J. H. & Schroeder, M. D. (1975). [The Protection of Information in Computer Systems](https://web.mit.edu/Saltzer/www/publications/protection/). *Proc. IEEE* 63(9). (Principle 2: Fail-Safe Defaults)
- CISA. (2023). [Shifting the Balance of Cybersecurity Risk: Principles and Approaches for Secure by Design Software](https://www.cisa.gov/resources-tools/resources/secure-by-design). §3: "Secure by Default."
- OWASP. [Secure Product Design Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Product_Design_Cheat_Sheet.html). Principle 4: Establish secure defaults.
- NIST SP 800-53 Rev 5. SA-8(23): Security and Privacy Engineering Principles — Secure Defaults.
- NIST SP 800-160 Vol 1. System Security Engineering — trustworthy secure design.
