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

## Most Important Patterns (applied directly in Alef)

---

### Pipes and Filters

**Problem:** Perform complex processing while keeping components independent and recomposable.

**Solution:** Divide processing into a sequence of independent **Filters** connected by **Pipes** (channels). Each filter has one input port and one output port. Same interface on every filter → compose into any sequence without changing the filters.

```
Input → [Filter A] → pipe → [Filter B] → pipe → [Filter C] → Output
```

**Key property:** Filters are unaware of each other. Adding, removing, or reordering filters requires no changes to the filters themselves — only the pipe wiring changes.

**In Alef:** The `Organ` interface IS the Filter. Event types ARE the Pipes. Composing `DialogOrgan → PlanningOrgan → LLMOrgan` by subscribing each to a different event type is Pipes and Filters.

```
sense/dialog.message → [PlanningOrgan] → motor/llm.phase → [LLMOrgan] → sense/llm.result
```

No filter (organ) knows about the others. You rewire by changing which event each subscribes to.

**Anti-pattern:** Filters that import each other directly. Bypass the pipes and you lose composability.

---

### Publish-Subscribe Channel

**Problem:** Broadcast an event to all interested receivers without knowing who they are.

**Solution:** A **Publish-Subscribe Channel** delivers a copy of each message to every subscriber. One publisher, many subscribers. Each subscriber gets its own copy.

**Key property:** The publisher does not know who the subscribers are. Subscribers can be added/removed without changing the publisher.

**In Alef:** The `InProcessBus` (`Motor` and `Sense` lanes) implements this. `nerve.motor.publish(event)` delivers to all Motor subscribers simultaneously. The wildcard `"*"` subscription (used by `EvaluatorOrgan`) is a Publish-Subscribe Channel across all event types.

**SeamRegistry implication:** For the `primary_cognition` seam, cardinality is `exactly-one` — this is a Point-to-Point Channel constraint overlaid on the pub-sub infrastructure. Exactly one organ handles the primary cognitive event.

---

### Correlation Identifier

**Problem:** When a reply arrives, how does the requestor know which request it belongs to?

**Solution:** The requestor generates a unique **Request ID** and includes it in the request. The replier copies it as the **Correlation ID** in the reply. The requestor matches replies to pending requests by correlation ID.

```
Requestor → [Request + requestId: "abc"] → Replier
Requestor ← [Reply + correlationId: "abc"] ← Replier
```

**In Alef:** `correlationId` on every `NerveEvent`. `dialog.send()` generates a UUID correlationId, stores a pending resolver keyed by that ID. When `Motor/dialog.message` arrives with the same correlationId, the resolver fires. `toolCallId` inside `MotorEvent.payload` is the inner correlation identifier for individual tool calls within a turn.

---

### Command Message

**Problem:** Invoke a procedure in another application via messaging.

**Solution:** A **Command Message** is a regular message whose content is interpreted as a command — "do this thing." The receiver is expected to execute the command and optionally reply.

**In Alef:** Every Motor event IS a Command Message. `motor/fs.read` = "read this file." `motor/shell.exec` = "execute this command." Motor events are commands flowing from the cerebrum to corpus organs. The organ executes and publishes a Sense event as the reply.

---

### Event Message

**Problem:** Notify receivers that something has happened.

**Solution:** An **Event Message** is a notification of a state change. Unlike a Command Message, the sender does not expect the receiver to do anything specific — just observe.

**In Alef:** Every Sense event IS an Event Message. `sense/fs.read` = "I read this file and here is what it contained." Sense events are observations flowing from corpus organs back to the cerebrum. The LLMOrgan observes and decides what to do next.

**The Motor/Sense split in EIP terms:** Motor lane = Command Messages. Sense lane = Event Messages. Two channels with different semantic contracts.

---

### Request-Reply

**Problem:** An application needs to send a request and receive a reply — synchronous semantics over async messaging.

**Solution:** The requestor sends a **Request** on one channel and waits for a **Reply** on another. Uses a **Correlation Identifier** to match the reply to the request.

**In Alef:** `dialog.send(text)` implements Request-Reply. The request = `Sense/dialog.message`. The reply = `Motor/dialog.message`. The `pending` Map in `DialogOrgan` is the return-address registry. Timeout is the `Message Expiration` safety valve.

---

### Process Manager

**Problem:** Route a message through multiple processing steps where the sequence is not known at design time and may not be sequential.

**Solution:** A central **Process Manager** maintains state across steps and determines the next step based on intermediate results. Hub-and-spoke pattern: all messages flow through the Process Manager.

```
Input → [Process Manager] → Step A → [Process Manager] → Step B → [Process Manager] → Output
```

**In Alef:** The **PlanningOrgan** IS a Process Manager. It receives `sense/dialog.message`, determines the phase sequence (Cynefin classification), sends to LLMOrgan for each phase via `motor/llm.phase`, collects `sense/llm.result`, advances to the next phase. The PlanningOrgan is the hub. LLMOrgan is a Processing Unit.

**Warning from EIP:** "A Process Manager can execute any sequence of steps. Therefore, almost any integration problem can be solved with a Process Manager. Likewise, most patterns could be implemented using a Process Manager. Many EAI vendors make you believe every integration problem is a process problem. Using a Process Manager for every situation may be overkill."

Apply the PlanningOrgan only when implicit LLM reasoning is insufficient. For most coding tasks, LLMOrgan alone (without a Process Manager) is the right choice.

---

### Wire Tap

**Problem:** Observe all messages on a channel without disturbing the existing message flow.

**Solution:** Insert a **Wire Tap** — a Recipient List with two outputs: the main channel and a secondary monitoring channel. The Wire Tap publishes every message to both, unmodified.

**In Alef:** The `EvaluatorOrgan` IS a Wire Tap. It subscribes `motor/*` and `sense/*` (wildcard = all channels), counts events, detects loops — all without affecting the primary message flow. The `BusEventRecorder` in `testkit` is also a Wire Tap for tests.

The `onAnyMotor()` and `onAnySense()` methods on `InProcessNerve` provide the Wire Tap subscription point.

---

### Dead Letter Channel

**Problem:** What happens to a message that cannot be delivered or processed?

**Solution:** Move undeliverable messages to a **Dead Letter Channel** for inspection, retry, or discard.

**In Alef:** The `isError: true` flag on `SenseEvent` is the Dead Letter signal. When a corpus organ's `handle()` throws, the framework publishes a Sense event with `isError: true` and `errorMessage`. The LLMOrgan receives this as a tool error result and decides whether to retry or abandon.

There is no explicit Dead Letter Channel as a separate bus destination — errors are inline in the reply. This is a deliberate simplification: the LLM handles error recovery, so a separate dead-letter queue adds no value.

---

### Message Store

**Problem:** Report on message flow and audit which messages have been processed.

**Solution:** A **Message Store** captures messages as they pass through the system, providing an audit trail and replay capability.

**In Alef:** The `SessionStore` (TSK-131) IS a Message Store. It appends every turn (user message, assistant message, tool call, tool result) to a JSONL file. The `EvalHarness` uses `InMemorySpanExporter` (OTel) as a Message Store for spans rather than messages.

---

### Content-Based Router

**Problem:** Route a message to the appropriate processing step based on message content.

**Solution:** Inspect the message content and route to one of several output channels based on what's inside.

**In Alef:** The `SeamRegistry` + blueprint validation is a Content-Based Router at boot time — it routes organs to the correct seams based on their action map key prefixes (`motor/` vs `sense/`). At runtime, the `InProcessBus` routes by event type — which IS content-based routing (the `type` field of every event is the routing key).

---

## EIP ↔ Alef Mapping Summary

| EIP Pattern | Alef Implementation |
|---|---|
| Pipes and Filters | `Organ` interface + event type wiring |
| Publish-Subscribe Channel | `InProcessBus` (Motor + Sense lanes) |
| Correlation Identifier | `correlationId` on every `NerveEvent` |
| Command Message | Motor events (`motor/fs.read`, `motor/shell.exec`) |
| Event Message | Sense events (`sense/fs.read`, `sense/dialog.message`) |
| Request-Reply | `dialog.send()` + `DialogOrgan` pending map |
| Process Manager | `PlanningOrgan` (phase-based orchestration) |
| Wire Tap | `EvaluatorOrgan` + `BusEventRecorder` |
| Dead Letter Channel | `SenseEvent.isError = true` |
| Message Store | `SessionStore` (JSONL append) + OTel spans |
| Content-Based Router | `InProcessBus` routing by `event.type` |
| Message Router | `PlanningOrgan` routing to `motor/llm.phase` per phase |

---

## What EIP Does NOT Cover (relevant gaps for AI agents)

EIP was designed for enterprise data integration — moving orders, invoices, and customer records between systems. It predates LLMs by 20 years. Patterns not in EIP that matter for AI agents:

- **Context Window Management** — no EIP pattern for managing a bounded in-memory context (ContextualOrgan, golden-mean eviction)
- **Seam Cardinality** — EIP assumes you can have N subscribers; AI agents need exactly-one reasoner (SeamRegistry)
- **Token Budget** — no EIP concept of message cost; agents need token-aware routing
- **Structured Reasoning Phases** — Process Manager gets close but doesn't capture dialectical phase progression (Think/Compose/Implement triads)

These are the gaps Alef's kernel fills on top of the EIP foundation.

---

## References

- Gregor Hohpe & Bobby Woolf, *Enterprise Integration Patterns* (Addison-Wesley, 2003)
- Online catalog: [enterpriseintegrationpatterns.com/patterns/messaging/](https://www.enterpriseintegrationpatterns.com/patterns/messaging/)
- Gregor Hohpe, "Conversation Patterns": [ramblings/81_restconversation](https://www.enterpriseintegrationpatterns.com/ramblings/81_restconversation.html)
