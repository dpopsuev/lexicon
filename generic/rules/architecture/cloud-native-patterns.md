---
id: cloud-native-patterns
title: "Cloud-Native Design Patterns"
description: "Structural, resilience, and data patterns for distributed systems — Sidecar, Ambassador, Circuit Breaker, Saga, and more"
labels: [architecture, cloud-native, distributed, patterns]
---

# Cloud-Native Design Patterns

Patterns for building, connecting, and operating services in distributed systems. Language-agnostic, infrastructure-agnostic. Organized by concern.

## Structural Patterns

### Sidecar

Deploy a helper alongside the main service to handle cross-cutting concerns (logging, monitoring, config, security) without modifying the main service's code. The sidecar shares the same lifecycle and network namespace.

**When to use:** Infrastructure concerns that every service needs but shouldn't embed.
**Trade-off:** Adds resource overhead per instance.

### Ambassador

A specialized sidecar that acts as a proxy for outbound connections. Handles retries, routing, protocol translation, and service discovery on behalf of the main service. The main service talks to localhost; the ambassador talks to the world.

**When to use:** Outbound communication complexity (retries, circuit breaking, TLS) that shouldn't live in application code.
**Trade-off:** Adds latency to every outbound call.

### Adapter

A sidecar that transforms the main service's interface to match what consumers expect. Translates protocols, formats, or data shapes. Aligns with DDD's Anti-Corruption Layer.

**When to use:** Integrating with systems that speak a different protocol or data format.
**Trade-off:** Adds translation overhead; adapter must be maintained when either side changes.

### Gateway (Aggregation / Offloading / Routing)

A single entry point that routes, aggregates, or offloads cross-cutting concerns for multiple backend services.

- **Aggregation:** Combines multiple backend calls into one client response.
- **Offloading:** Handles TLS, auth, rate limiting centrally.
- **Routing:** Directs requests to the correct backend by path, header, or intent.

**When to use:** Multiple services behind one API surface.
**Trade-off:** Single point of failure if not replicated.

## Resilience Patterns

### Circuit Breaker

Prevents calls to a service that is likely to fail. Three states: closed (normal), open (all calls rejected), half-open (probe to test recovery). Protects the caller from cascading failures and gives the failing service time to recover.

**When to use:** Any call to an external or unreliable dependency.
**Trade-off:** Must tune thresholds (failure count, timeout, recovery probe interval).

### Bulkhead

Isolates resources into separate pools so a failure in one pool doesn't exhaust resources for others. Named after ship compartments — one flooding doesn't sink the ship.

**When to use:** Shared resources (thread pools, connection pools, memory) serving independent workloads.
**Trade-off:** Reduces overall resource utilization (pools can't share capacity).

### Retry

Automatically retries failed operations with configurable policies: immediate, fixed delay, exponential backoff, jitter. Only for transient failures — permanent failures should fail fast.

**When to use:** Transient network errors, temporary unavailability.
**Trade-off:** Can amplify load on a struggling service (use with circuit breaker).

### Rate Limiting / Throttling

Limits the number of requests a service accepts per time window. Protects against overload from legitimate traffic or abuse.

**When to use:** Any public-facing or shared service.
**Trade-off:** Legitimate requests may be rejected during peaks.

### Health Endpoint Monitoring

Services expose health checks (liveness, readiness) that infrastructure uses to route traffic, restart instances, or alert operators.

- **Liveness:** Is the process alive? (restart if not)
- **Readiness:** Can it accept work? (stop routing if not)

**When to use:** Every service in a distributed system.

## Data Patterns

### Cache-Aside

Application checks cache before querying the data source. On miss, fetches from source, stores in cache. Application owns the cache lifecycle — not the data source.

**When to use:** Read-heavy workloads with tolerable staleness.
**Trade-off:** Cache coherency — stale data risk, invalidation complexity.

### CQRS (Command Query Responsibility Segregation)

Separate the write model (commands) from the read model (queries). Each can evolve, scale, and optimize independently. Write model enforces business rules; read model optimizes for access patterns.

**When to use:** Complex domains where reads and writes have fundamentally different shapes or scale requirements.
**Trade-off:** Added complexity — two models to maintain, eventual consistency between them.

### Event Sourcing

Store state as a sequence of events, not as current state. Derive current state by replaying events. Enables full audit trail, temporal queries, and event-driven integration.

**When to use:** Audit-critical systems, undo/redo, event-driven architectures.
**Trade-off:** Replay can be slow for long event streams; requires snapshots for performance.

### Saga

Coordinates multi-step operations across independent services without distributed transactions. Each step has a compensating action. On failure, compensating actions undo earlier steps.

- **Choreography:** Each service listens for events and acts independently.
- **Orchestration:** A central coordinator directs the sequence.

**When to use:** Workflows spanning multiple services that each own their data.
**Trade-off:** Compensating actions are hard to get right; eventual consistency.

## Composition Patterns

### Pipes and Filters

Chain processing steps where each step (filter) transforms data and passes it to the next via a uniform interface (pipe). Filters are independent and composable.

**When to use:** Data transformation pipelines, middleware chains.
**Trade-off:** Debugging multi-step pipelines; error propagation across steps.

### Strangler Fig

Incrementally replace a legacy system by routing traffic to new implementations one feature at a time. The old system shrinks until it can be removed. Never big-bang migrate.

**When to use:** Any legacy-to-modern migration.
**Trade-off:** Dual maintenance during transition; routing complexity.

### Competing Consumers

Multiple consumers pull from the same work queue. Load distributes automatically. If one consumer fails, others continue. Scale by adding consumers.

**When to use:** High-throughput work distribution.
**Trade-off:** Message ordering not guaranteed; need idempotent consumers.

## Observability Patterns

### Distributed Tracing

Propagate trace context (trace ID, span ID) across service boundaries. Each service records its span. Assemble the full request timeline from spans.

**When to use:** Any multi-service request path.

### Log Aggregation

Centralize logs from all services into one searchable store. Structured logging (JSON) enables filtering by service, request ID, severity.

**When to use:** Every distributed system.

### Health Dashboard (RED / USE)

- **RED** (request-driven): Rate, Errors, Duration per service.
- **USE** (resource-driven): Utilization, Saturation, Errors per resource.

**When to use:** RED for services, USE for infrastructure.

## Decision Guide

| Need | Pattern |
|------|---------|
| Cross-cutting concerns without code changes | Sidecar |
| Outbound proxy (retries, routing) | Ambassador |
| Interface translation | Adapter |
| Prevent cascading failures | Circuit Breaker |
| Isolate failure domains | Bulkhead |
| Retry transient errors | Retry (+ Circuit Breaker) |
| Protect from overload | Rate Limiting |
| Read-heavy performance | Cache-Aside |
| Separate read/write scaling | CQRS |
| Full audit trail | Event Sourcing |
| Multi-service transactions | Saga |
| Data transformation chains | Pipes and Filters |
| Incremental migration | Strangler Fig |
| Work distribution | Competing Consumers |

## Anti-Patterns

- **Distributed monolith.** Microservices that must deploy together. You got the complexity of distribution without the independence.
- **Shared database.** Two services reading/writing the same tables. Tight coupling disguised as simplicity.
- **Retry storm.** Retrying without backoff or circuit breaking amplifies failures instead of recovering from them.
- **Sidecar bloat.** Sidecar does more than the main service. The helper became the application.
- **Saga without compensation.** Multi-step workflow with no rollback plan. Partial execution leaves inconsistent state.

## References

- [Microsoft Azure Cloud Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/patterns/)
- [AWS Cloud Design Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/)
- Kubernetes Pod Design Patterns (Sidecar, Ambassador, Adapter)
- Martin Fowler, "Patterns of Enterprise Application Architecture"
- Chris Richardson, "Microservices Patterns" (2018)

## Complements

- `ports-and-adapters` — the hexagonal architecture these patterns operate within
- `protocol-design-patterns` — agent-level patterns (scatter-gather, competing consumers, stigmergy)
- `event-driven` — paradigm that many of these patterns assume
- `service-oriented` — SOA predecessor to cloud-native microservices
- `defense-in-depth` — resilience patterns are layers in the security membrane
- `operational-quality` — RED/USE methods for monitoring pattern effectiveness
