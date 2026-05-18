---
name: enterprise-integration-patterns
description: EIP — 65 canonical messaging patterns for building message-based distributed systems. Hohpe & Woolf, 2003. The vocabulary for event-driven architecture.
labels: [architecture, messaging, eda, patterns, integration]
priority: 30
always_apply: true
---

# Enterprise Integration Patterns (EIP)

**Gregor Hohpe & Bobby Woolf, 2003. The canonical reference for messaging-based system design. 65 patterns across 5 categories.**

Every major messaging system (Kafka, RabbitMQ, Azure Service Bus, AWS EventBridge, Spring Integration, Apache Camel) is built on these patterns. When you build an event-driven system, you are assembling EIP patterns whether you know it or not. Know them.

Reference: [enterpriseintegrationpatterns.com](https://www.enterpriseintegrationpatterns.com)

---

## The 5 Categories

1. **Message Construction** — what a message looks like
2. **Message Routing** — how messages get to the right place
3. **Message Transformation** — how messages change shape
4. **Messaging Endpoints** — how applications connect to the bus
5. **System Management** — how you observe and control the system

---

## Core Patterns

---

### Pipes and Filters

**Problem:** Perform complex processing while keeping components independent and recomposable.

**Solution:** Divide processing into a sequence of independent **Filters** connected by **Pipes** (channels). Each filter has one input port and one output port. Same interface on every filter — compose into any sequence without changing the filters themselves.

```
Input → [Filter A] → pipe → [Filter B] → pipe → [Filter C] → Output
```

**Key property:** Filters are unaware of each other. Adding, removing, or reordering filters requires no changes to the filters — only the pipe wiring changes.

**In event-driven systems:** Each processing component (handler, module, processor) is a filter. The event/message type IS the pipe. Composing different processing pipelines means changing which message type each component subscribes/publishes to — not changing the components themselves.

```
input-channel → [Classifier] → task-channel → [Executor] → result-channel
```

**Anti-pattern:** Filters that import each other directly. Bypass the pipes and you lose composability.

---

### Publish-Subscribe Channel

**Problem:** Broadcast an event to all interested receivers without knowing who they are.

**Solution:** A **Publish-Subscribe Channel** delivers a copy of each message to every subscriber. One publisher, many subscribers. Each subscriber gets its own copy.

**Key property:** The publisher does not know the subscribers. Subscribers can be added/removed without changing the publisher.

**In practice:** An event bus where any number of listeners can subscribe to any event type. Useful for: observability taps (receive every event for logging without disturbing the flow), fan-out processing (multiple handlers for the same event), decoupled notification (emit without knowing who cares).

**Point-to-Point Channel** is the alternative — only ONE consumer receives each message. Use this when exactly one handler must process each message (work queues, task dispatch).

---

### Correlation Identifier

**Problem:** When a reply arrives asynchronously, how does the requestor know which request it belongs to?

**Solution:** The requestor generates a unique **Request ID** and includes it in the request. The replier copies it as the **Correlation ID** in the reply. The requestor matches replies to pending requests by correlation ID.

```
Requestor → [Request + requestId: "abc"] → Replier
Requestor ← [Reply + correlationId: "abc"] ← Replier
```

**In practice:** Add `correlationId` to every message envelope. Generate a UUID at the start of each request. Thread it through all derived messages. Use a `Map<correlationId, PendingPromise>` to match async replies to waiting callers. Nested operations use a second identifier (e.g., `toolCallId`) as the inner correlation token.

---

### Command Message

**Problem:** Invoke a procedure in another application via messaging.

**Solution:** A **Command Message** is a message whose content is interpreted as a command — "do this thing." The receiver is expected to execute and optionally reply.

**In practice:** Commands flow from an orchestrator to worker components. Command = imperative ("read this file", "execute this command", "fetch this URL"). Workers execute and publish Event Messages as replies.

Separate command channels from event channels. Commands are directed. Events are observed.

---

### Event Message

**Problem:** Notify receivers that something has happened.

**Solution:** An **Event Message** is a notification of a state change. Unlike a Command Message, the sender does not dictate what receivers should do — just reports what happened.

**In practice:** Events flow back from workers to orchestrators. Event = past tense ("file was read", "command exited", "error occurred"). The receiver decides what action to take based on the event content.

**The Command/Event split in two-bus architectures:** One bus carries commands (Motor/inbound), the other carries events (Sense/outbound). This makes the direction semantically explicit and prevents feedback loops.

---

### Request-Reply

**Problem:** An application needs synchronous-style request/response semantics over an async messaging system.

**Solution:** The requestor sends a **Request** and waits for a **Reply** using a **Correlation Identifier** to match them. Uses a **Return Address** to tell the replier where to send the reply.

```
Requestor: send(request + correlationId) → wait for reply with same correlationId
Replier:   receive request → process → send(reply + correlationId from request)
```

**In practice:** A `send(text): Promise<string>` function that publishes a message and suspends until a matching reply arrives. The pending map (`Map<correlationId, resolver>`) is the in-memory return address registry. Always add a timeout — a missing reply must not hang the caller forever.

---

### Process Manager

**Problem:** Route a message through multiple processing steps where the sequence is not known at design time and may not be sequential.

**Solution:** A central **Process Manager** maintains state across steps and determines the next step based on intermediate results. Hub-and-spoke: all messages flow through the Process Manager.

```
Trigger → [Process Manager] → Step A → [PM] → Step B → [PM] → Step C → Output
```

**In practice:** Use when the processing sequence is dynamic (depends on intermediate results), non-linear (parallel steps, conditional branching), or too complex for a simple Routing Slip. The Process Manager holds the conversation state between steps.

**Warning from Hohpe & Woolf:** "A Process Manager can solve almost any integration problem. Using it for every situation may be overkill and cause significant performance overhead." Use only when simpler patterns (Routing Slip, Pipes and Filters) are insufficient.

**In AI agents:** A planning component that orchestrates multiple LLM calls — one per reasoning phase — is a Process Manager. Each phase is a step. The planner maintains state between calls and routes to the next phase based on the result.

---

### Wire Tap

**Problem:** Observe all messages on a channel without disturbing the existing message flow.

**Solution:** Insert a **Wire Tap** — a Recipient List with two outputs: the main channel and a secondary monitoring channel. Every message is published to both, unmodified.

**In practice:** An observer component that subscribes to all channels via wildcard (`"*"`), counts events, logs them, or checks invariants — without affecting the primary processing flow. This is the correct way to add observability: do not modify existing components, insert a Wire Tap instead.

**Used for:** Debugging, audit trails, loop detection, OTel span emission, test recorders.

---

### Dead Letter Channel

**Problem:** What happens to a message that cannot be delivered or processed?

**Solution:** Move undeliverable messages to a **Dead Letter Channel** for inspection, retry, or discard.

**In practice:** When a handler throws an error, do not silently drop the message. Publish an error event (with `isError: true` and `errorMessage`) to notify upstream components. The upstream component (e.g., an LLM) can inspect the error and decide to retry, fallback, or escalate.

Inline error signalling (error flag on the reply message) is simpler than a separate dead-letter queue when the sender is an active component that can handle errors dynamically.

---

### Message Store

**Problem:** Report on message flow, audit which messages have been processed, or replay the message history.

**Solution:** A **Message Store** captures messages as they pass through the system. Provides an audit trail, debugging capability, and replay.

**In practice:** An append-only log (JSONL file, database table, OTel span exporter) that records every message. For agent systems: each turn (user message, assistant reply, tool call, tool result) is appended. The store is the source of truth. In-memory processing state is derived from it and can be rebuilt on restart.

---

### Content-Based Router

**Problem:** Route a message to the appropriate processor based on message content.

**Solution:** Inspect the message content and route to one of several output channels based on what's inside.

**In practice:** An event bus that routes by message type (`event.type` field) is a Content-Based Router. Each subscriber registers interest in specific event types. The bus delivers only to matching subscribers. This is routing by content — the content being the event type discriminant.

---

## Pattern Relationships (quick reference)

```
Publish-Subscribe Channel    ← the bus itself
  └─ Pipes and Filters       ← how components connect to the bus
       └─ Process Manager    ← when the pipeline is stateful and dynamic
  └─ Wire Tap                ← how to observe without disturbing
  └─ Content-Based Router    ← how the bus routes by event type

Command Message + Event Message   ← the two semantic roles of messages
  └─ Request-Reply               ← synchronous semantics over async
       └─ Correlation Identifier ← how replies are matched to requests

Dead Letter Channel    ← what happens to unprocessable messages
Message Store          ← the audit trail
```

---

## What EIP Does NOT Cover

EIP was designed for enterprise data integration (2003). Three gaps that matter for AI agent systems:

1. **Context Window Management** — no pattern for managing a bounded in-memory context with cost-based eviction (token budget, hotness scoring).
2. **Seam Cardinality** — EIP assumes N subscribers on a pub-sub channel. AI agents need exactly-one semantic on certain channels (only one component may respond to a user request).
3. **Token Budget** — no concept of message cost. Agent systems must track token consumption per message and across the full context.

These gaps are addressed by patterns specific to agent architectures built on top of the EIP foundation.

---

## References

- Gregor Hohpe & Bobby Woolf, *Enterprise Integration Patterns* (Addison-Wesley, 2003)
- Online catalog: [enterpriseintegrationpatterns.com/patterns/messaging/](https://www.enterpriseintegrationpatterns.com/patterns/messaging/)
- Gregor Hohpe, "Ramblings on REST and Conversations": [ramblings](https://www.enterpriseintegrationpatterns.com/ramblings.html)
