---
id: event-driven-architecture
title: Event-Driven Architecture
description: Systems communicate by producing and consuming events — discrete records of something that happened. No direct calls between components. Decoupled, asynchronous, scalable.
labels: [architecture, design, events, async, decoupling]
---

# Event-Driven Architecture

Systems communicate by producing and consuming events rather than calling each other directly. An event is a discrete record of something that happened. Producers don't know who consumes. Consumers don't know who produced. The event bus decouples them.

**Origin:** Distributed systems research, messaging middleware (1990s). Popularized by Apache Kafka, cloud event services, and microservices patterns.

## Core Patterns

### 1. Publish-Subscribe
Producers publish events to a topic. Consumers subscribe to topics they care about. One event, N consumers. Decouples producers from consumers entirely.

### 2. Event Sourcing
State is not stored as current snapshot — it's stored as an append-only log of events. Current state = replay of all events. Natural audit trail. Enables temporal queries ("what was the state at time T?").

### 3. CQRS (Command-Query Responsibility Segregation)
Separate the write model (commands that change state) from the read model (queries that read state). Commands go through one path, queries through another. Each optimized independently.

### 4. Choreography
No central orchestrator. Each component reacts to events independently. The system's behavior emerges from the interactions, not from a conductor. Contrast with Orchestration where a central controller directs the flow.

### 5. Event-Carried State Transfer
Events carry the full state needed by consumers, not just a notification. Consumers don't need to call back to the producer for details. Reduces coupling at the cost of larger events.

## When to Use

| Scenario | EDA fits | EDA doesn't fit |
|----------|----------|----------------|
| Components need decoupling | yes | — |
| Real-time reactions needed | yes | — |
| Async processing acceptable | yes | strict request-response needed |
| Audit trail required | yes (event sourcing) | — |
| Simple CRUD | — | overkill |
| Strict ordering required | use carefully (partitioning) | — |

## Relationship to Other Patterns

- **Sensory-Motor Bus** — the buses ARE event channels. Sensory = inbound events. Motor = outbound commands. EDA applied to agent internals.
- **Hexagonal Architecture** — event bus = port. Producers/consumers = adapters. Domain reacts to events through ports.
- **Observer Pattern** — pub-sub is the architectural scale of observer.
- **ACT-R Buffers** — cognitive architecture's version of event-driven: modules write to buffers, production system reads. Async, decoupled.
- **Ablation Testing** — event-driven systems are naturally ablation-ready. Unsubscribe a consumer = disable a component.

## Application to Agent Systems

For AI agent architectures, EDA maps naturally:

| Agent Concept | EDA Pattern |
|---------------|-------------|
| Sensory input | Events published by Organs to Sensory Bus |
| Motor output | Commands published by Cerebrum to Motor Bus |
| Molecule | Event-sourced state (atoms are append-only events) |
| Reactor | Event consumer that processes and produces events |
| Shelf.Watch | Event subscription (artifact arrival) |
| Instrument execution | Command event → result event |
| Session lifecycle | Event chain: Watch → work → drain → Push → Watch |
| Inter-agent communication | Events between agent buses via broker |

The Molecule IS event-sourced: atoms are append-only, never modified. Current state = the graph of all atoms. Replay the atoms to reconstruct any prior state.

## Anti-patterns

- **Event soup** — publishing everything as an event without clear domain boundaries. Events should be meaningful business occurrences, not internal state changes.
- **Synchronous disguised as async** — publishing an event then immediately waiting for a response event. That's request-response with extra steps.
- **Missing idempotency** — consumers must handle duplicate events gracefully. At-least-once delivery is the norm.
- **Unbounded event growth** — without retention policies or compaction, event logs grow forever. Define TTL and compaction strategies.
- **Choreography spaghetti** — too many components reacting to too many events creates emergent behavior that's impossible to debug. Use bounded contexts.

## Sources

- [Event-Driven Architecture Complete Guide (2026)](https://risingwave.com/blog/event-driven-architecture-complete-guide/)
- [Best Architectural Patterns for Event-Driven Systems](https://www.gravitee.io/blog/event-driven-architecture-patterns)
- [Microservices Pattern: Event-Driven Architecture](https://microservices.io/patterns/data/event-driven-architecture.html)
- [Event-Driven Architecture Patterns (Solace)](https://solace.com/event-driven-architecture-patterns/)
