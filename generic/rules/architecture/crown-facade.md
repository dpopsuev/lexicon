---
id: crown-facade
title: Crown Facade
description: Single entry point receives input signals, single exit point emits output. The facade triages but does not process. Based on Keter (Crown) from the Sefirot.
labels: [architecture, design, facade, agent, cognitive]
---

# Crown Facade

A processing pipeline's single entry and exit point. The Crown receives input signals, triages them (new, continuation, or contradiction of existing work), and dispatches to the first processing stage. When processing completes, the Crown emits the result.

**Origin:** Keter (Crown) from Kabbalistic Sefirot — the first emanation through which all divine light enters and through which all creation flows. Applied as an architectural pattern for agent reasoning pipelines.

## The Pattern

```
Input → Crown → Processing Stages → Crown → Output
          ↑                           ↓
     Inbound Port               Outbound Port
```

The Crown is a pure facade. It does not compute, reason, or transform. It:
1. **Receives** input from the inbound port (Sensory Bus)
2. **Triages** — classifies the input (new work, continuation, conflict)
3. **Dispatches** to the first processing stage
4. **Collects** the final output from the last stage
5. **Emits** the result through the outbound port (Motor Bus)

## Triage Categories

Every input signal falls into one of three categories:

| Category | Meaning | Action |
|----------|---------|--------|
| Genesis | New work, nothing in progress | Create new processing context |
| Continuation | Existing work, resume | Load existing context, continue |
| Contradiction | Conflicts with existing work | Challenge existing context |

## Why One Door

| Alternative | Problem |
|-------------|---------|
| Multiple entry points | Consumers must know which door to use. Coupling. |
| No triage | Processing stages must handle raw, unclassified input. SRP violation. |
| Triage inside processing | First stage is overloaded with classification + processing. |
| No exit facade | Processing stages must know how to deliver results. Coupling. |

The Crown separates classification from processing and delivery from computation.

## Relationship to Other Patterns

- **Facade** — the Crown IS a facade. Hides the processing pipeline behind a single interface.
- **Sensory-Motor Bus** — the Crown connects the inbound (sensory) and outbound (motor) buses to the processing pipeline.
- **Strategy** — triage classification can be a strategy (pluggable classifier).
- **Chain of Responsibility** — the processing stages form a chain. The Crown initiates and collects.
- **Hexagonal Architecture** — Crown = the port. Processing = domain. Buses = adapters.

## Anti-patterns

- Putting processing logic in the Crown (it classifies and routes, nothing more)
- Bypassing the Crown to inject signals directly into processing stages
- Multiple Crowns per pipeline (one Crown per reasoning context)
- Crown holding state between cycles (it's stateless — the processing context holds state)
