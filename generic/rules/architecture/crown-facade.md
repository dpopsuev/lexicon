---
id: crown-facade
title: Crown Facade
description: Single entry point classifies and dispatches input; single exit point collects and emits output. The facade routes, it does not process. Prior art: Front Controller (J2EE Core Patterns), Message Dispatcher (EIP).
labels: [architecture, design, facade, agent, pipeline]
---

# Crown Facade

A processing pipeline's single entry and exit point. The Crown receives input signals, classifies them (new work, continuation, or contradiction), and dispatches to the first processing stage. When processing completes, the Crown emits the result.

**Prior art:** Front Controller (J2EE Core Patterns, Alur et al. 2001), Message Dispatcher (EIP, Hohpe & Woolf 2003). The Crown extends Front Controller with explicit triage classification and a symmetric exit facade.

## The Pattern

```
Input → Crown → Processing Stages → Crown → Output
          ↑                                    ↓
     Inbound Port                        Outbound Port
```

The Crown is a pure facade. It does not compute, reason, or transform. It:
1. **Receives** input from the inbound port
2. **Classifies** the input (new, continuation, or contradiction of existing work)
3. **Dispatches** to the first processing stage
4. **Collects** the final output from the last stage
5. **Emits** the result through the outbound port

## Classification Categories

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
| No classification | Processing stages must handle raw, unclassified input. SRP violation. |
| Classification inside processing | First stage is overloaded with classification + processing. |
| No exit facade | Processing stages must know how to deliver results. Coupling. |

The Crown separates classification from processing, and delivery from computation.

## Relationship to Other Patterns

- **Front Controller** — Crown IS a Front Controller. Adds explicit triage and a symmetric exit facade.
- **Message Dispatcher** (EIP) — Crown is a stateless dispatcher; the processing stages are the handlers.
- **Strategy** — classification logic can be a strategy (pluggable classifier).
- **Chain of Responsibility** — the processing stages form a chain. The Crown initiates and collects.
- **Hexagonal Architecture** — Crown = the driving port adapter. Processing = domain. Infrastructure = driven adapters.

## Anti-patterns

- Putting processing logic in the Crown (it classifies and routes, nothing more)
- Bypassing the Crown to inject signals directly into processing stages
- Multiple Crowns per pipeline (one Crown per reasoning context)
- Crown holding state between cycles (it is stateless — the processing context holds state)
