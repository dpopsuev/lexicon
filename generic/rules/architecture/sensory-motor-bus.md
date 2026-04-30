---
id: sensory-motor-bus
title: Sensory-Motor Bus
description: Decouple the brain from the body via two DIP-compliant buses — inbound sensory signals and outbound motor commands. Based on ACT-R cognitive architecture.
labels: [architecture, design, dip, agent, cognitive]
---

# Sensory-Motor Bus

Decouple an agent's reasoning core from its interaction layer using two unidirectional buses. The brain reads sensory input and writes motor output. The body writes sensory signals and reads motor commands. Neither side imports the other.

**Origin:** ACT-R cognitive architecture (Anderson & Lebiere, Carnegie Mellon, 1998). Also present in BDI agent architecture (event-driven plan triggering) and Three-Layer architectures (sensory/sequencing/deliberative).

## The Pattern

```
Body (Organs/Modules) → Sensory Bus → Brain (Cerebrum/Production System) → Motor Bus → Body
```

- **Sensory Bus (inbound):** environment signals flowing toward the reasoning core. New work items, alerts, state changes, external knowledge.
- **Motor Bus (outbound):** decisions flowing from the reasoning core toward the environment. Actions to execute, state to persist, UI to render, alerts to raise.

Both buses are interfaces (ports in hexagonal terms). The composition root wires concrete implementations.

## ACT-R Foundation

ACT-R organizes cognition around **modules** and **buffers**:

- Each module (visual, auditory, manual, vocal) communicates ONLY through its buffer
- Buffers hold a single chunk of information (not a queue — a register)
- The central production system reads all buffers each cycle, matches patterns, fires one production
- Adding a new module means registering a new buffer, not changing the production system

This is DIP applied to cognitive architecture: the production system depends on buffer abstractions, never on concrete modules.

## Why Two Buses

| Alternative | Problem |
|-------------|---------|
| Brain imports organs directly | Every new organ requires changing the brain. DIP violation. |
| Single shared bus | Direction ambiguity. Organs reading their own motor commands. Fan-out complexity. |
| Event system (pub/sub) | Too generic. Loses the directional constraint. Brain might subscribe to irrelevant events. |
| Direct method calls | Tight coupling. Brain must know organ interfaces. Testing requires mocking every organ. |

Two buses preserve the biological metaphor (afferent/efferent nerves) AND the architectural constraint (inbound port/outbound port).

## Implementation

The bus interface is minimal:

```go
type SensoryBus interface {
    Receive() Signal    // brain reads
}

type MotorBus interface {
    Send(command Command) // brain writes
}
```

Organs implement the write side of sensory and the read side of motor. The composition root connects them.

## Relationship to Other Patterns

- **Hexagonal Architecture** — sensory bus = inbound port, motor bus = outbound port, organs = adapters, brain = domain
- **DIP** — brain depends on bus interfaces, not organ concretions
- **Observer** — organs publish signals to the sensory bus
- **Mediator** — the composition root mediates between brain and organs via buses
- **Strategy** — bus implementations are swappable (stub for testing, real for production)
- **ACT-R Buffers** — single-chunk registers, not queues. Current state, not history.

## Anti-patterns

- Putting business logic in the bus (the bus routes, it doesn't compute)
- Allowing organs to read the sensory bus (that's the brain's job)
- Allowing the brain to write to the sensory bus (that's the organs' job)
- Using the bus for inter-organ communication (organs don't know each other; use the brain as mediator)
- Making the bus a queue when a register suffices (ACT-R insight: current state beats event history for most cognitive tasks)
