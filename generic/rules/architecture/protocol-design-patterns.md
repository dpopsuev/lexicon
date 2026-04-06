---
id: protocol-design-patterns
title: Protocol Design Patterns
description: Recurring patterns for agent communication, work distribution, health signaling, process supervision, and stigmergic coordination. Not GoF — infrastructure patterns for protocol-driven systems.
labels: [architecture, design, patterns, protocol]
---

# Protocol Design Patterns

**Patterns for how agents communicate, not how objects collaborate.**

GoF patterns (Strategy, Observer, Factory) solve object collaboration problems. Protocol patterns solve agent coordination problems: how work flows, how health is signaled, how processes are supervised, how routing emerges from observation.

## Work Distribution

Prior art: Enterprise Integration Patterns (Hohpe & Woolf), Erlang/OTP.

| Pattern | Problem | Solution | Prior Art |
|---------|---------|----------|-----------|
| **Competing Consumers (Pull/Push)** | Server holds work, workers are stateless | Workers pull items, push results. Server owns queue. No worker-to-worker communication. | EIP Competing Consumers |
| **Scatter-Gather** | Need all agents to process, collect all responses | Fan-out to N, wait for all, join responses. Two variants: recipient list (known targets) or pub-sub (any interested). | EIP Scatter-Gather |
| **Race (First Best)** | Need fastest answer from N agents | Fan-out to N, first response wins, cancel rest via context. Completeness strategy: WhenAny. | EIP Scatter-Gather completeness variant |
| **Round-Robin** | Distribute load across healthy agents | Atomic index, skip unhealthy (readiness probe), wrap around. | EIP Competing Consumers + health filtering |

## Health & Signaling

Prior art: IEC 60073 (stack lights), Kubernetes probes, animal communication (stigmergy).

| Pattern | Problem | Solution | Prior Art |
|---------|---------|----------|-----------|
| **Stack Light (Andon)** | Health is more than up/down | Priority-ordered severity levels (0-255) with vocabulary declaration on handshake. Levels: nominal, degraded, failure, blocked, dead. | IEC 60073 industrial stack lights |
| **Liveness + Readiness** | "Is it running?" differs from "can it work?" | Two independent probes, orthogonal. Agent can be alive but not ready (starting, overloaded, terminating). | K8s liveness/readiness probes |
| **Stigmergy (Wake)** | Routing should learn from history without explicit configuration | Agents deposit performance data on a shared medium as a byproduct of work (not reported). Scheduler reads accumulated signals. Trails decay (stale data expires) and amplify (repeated patterns strengthen). | Grassé 1959, ant pheromone trails, ACO algorithms |

## Process Supervision

Prior art: Erlang/OTP supervision trees, Linux process model.

| Pattern | Problem | Solution | Prior Art |
|---------|---------|----------|-----------|
| **Fork/Kill/Wait** | Agent lifecycle with parent tracking | Parent spawns child, moves to zombie on death, parent reaps via Wait(). Hierarchy tracked. | Linux fork/kill/wait, POSIX process model |
| **Orphan Adoption** | Parent dies, children must survive | Subreaper inherits orphaned children. Default subreaper is root (PID 0 / Agent 0). | Linux PR_SET_CHILD_SUBREAPER |
| **Restart Policy** | Agents should auto-recover from failure | Never (default), OnFailure, Always. Budget-exceeded agents never restart. | OTP child restart types: permanent, transient, temporary |
| **Restart Intensity** | Agent restart-loops must be bounded | MaxR restarts in MaxT seconds, then supervisor escalates to parent. Prevents thrashing. | OTP restart intensity limiting |
| **Graceful Termination** | Stop routing before killing | Mark not-ready (readiness probe false), wait grace period for current work to finish, then force kill. | K8s terminationGracePeriodSeconds, OTP shutdown protocol |
| **Agent 0 / Agent 1** | Who controls the system? Who manages lifecycle? | Agent 0 = operator (PID 0, kernel, always exists, never spawned). Agent 1 = first AI agent (PID 1, init, lifecycle manager). Consumer defines who Agent 1 is. | Linux PID 0 (kernel) / PID 1 (init/systemd) |

## Identity & Selection

Prior art: ECS game engines, OCP File-Based Catalogs.

| Pattern | Problem | Solution | Prior Art |
|---------|---------|----------|-----------|
| **ECS Composition** | Identity is multi-axis, not a monolith | Independent components attached to entities: symbol (visual), trait (behavioral), intent (purpose). Change one without affecting others. | Entity-Component-System (game engines) |
| **Declarative Selection** | "Find the best agent for this intent" | Intent → traits → weights → score → Pick(). Declarative resolves DOWN to imperative. One exit point. | Attribute-Based Access Control (ABAC) scoring |
| **File-Based Catalog** | Embedded config must be versioned, composable, validatable | Per-entity subdirectories, snapshot-per-release, go:embed. Each snapshot self-contained (no diffs). | OCP File-Based Catalogs (FBC) |
| **Reservation** | Prefer a specific resource but accept alternatives | Request preferred assignment, fallback to any available if taken. Consumer expresses preference, system guarantees no collisions. | K8s node affinity with preferred/required |

## Security

Prior art: NIST SP 800-207 (Zero Trust), capability-based security, OWASP.

| Pattern | Problem | Solution | Prior Art |
|---------|---------|----------|-----------|
| **Capability Token** | Identity alone is insufficient for access control | Scoped, revocable, non-transferable tokens per action. Time-bounded. | Capability-based security (Dennis & Van Horn 1966) |
| **Confused Deputy Prevention** | Privileged service acts on behalf of unprivileged caller | Validate requester's authorization at the boundary, not just your own. Log who requested what. | Norm Hardy 1988 |
| **Graceful Degradation** | Trust decreases, but system must continue | Reduce agent capabilities rather than full shutdown. Multiple trust levels with corresponding capability restrictions. | Zero Trust for AI Agents (2025) |

## Collective Strategies

Prior art: Hegelian dialectic, XP pair programming, debate systems.

| Pattern | Problem | Solution |
|---------|---------|----------|
| **Dialectic** | Need stress-tested decisions | Thesis proposes, antithesis challenges, repeat until convergence or max rounds. Thesis has last word (synthesis). |
| **Arbiter** | Two agents disagree, need a tiebreaker | Third agent judges arguments from both sides, issues verdict (affirm, amend, remand). |
| **DialecticPair** | Need both debate AND paired execution | Phase 1: dialectic convergence on plan. Phase 2: driver implements, navigator reviews per tool call. Same two agents, different hats. |

## Anti-Patterns

- **Hub without health.** Central coordinator routes work to dead agents because it doesn't check readiness.
- **Monolith identity.** One struct mixing visual, behavioral, and purpose concerns. Use ECS composition.
- **Implicit restart.** Agent silently restarts without signaling. Operator can't tell if agents are thrashing.
- **Optional telemetry.** If reporting is optional, no one reports. Make performance data a byproduct (stigmergy), not a choice.
- **Restart without backoff.** Agent crash-loops at full speed. Use restart intensity limiting (MaxR in MaxT).
- **Static roles for dynamic agents.** RBAC can't handle agents that switch tasks hundreds of times per hour. Use capability tokens with dynamic scope.

## References

- Hohpe & Woolf, [Enterprise Integration Patterns](https://www.enterpriseintegrationpatterns.com/patterns/messaging/) (2003)
- [Erlang/OTP Supervisor Behaviour](https://www.erlang.org/doc/system/sup_princ.html)
- Grassé, "La reconstruction du nid et les coordinations interindividuelles" (1959) — [Stigmergy](https://en.wikipedia.org/wiki/Stigmergy)
- [NIST SP 800-207 Zero Trust Architecture](https://nvlpubs.nist.gov/nistpubs/specialpublications/NIST.SP.800-207.pdf)
- [OCP File-Based Catalogs](https://olm.operatorframework.io/docs/reference/file-based-catalogs/)
- [A Survey of Agent Interoperability Protocols](https://arxiv.org/html/2505.02279v1) (2025)
- [A Taxonomy of Hierarchical Multi-Agent Systems](https://arxiv.org/html/2508.12683) (2025)
- [Zero Trust for AI Agents](https://securityboulevard.com/2025/10/zero-trust-for-ai-agents-implementing-dynamic-authorization-in-an-autonomous-world/) (2025)

## Complements

- `design-patterns` — GoF patterns (object collaboration). This rule covers protocol patterns (agent coordination).
- `effective-go` — type safety for protocol types (named types over raw strings).
- `trust-boundaries` — STRIDE analysis at protocol boundaries.
- `defense-in-depth` — layered defenses across protocol layers.
- `code-smells` — smells that signal missing protocol patterns.
