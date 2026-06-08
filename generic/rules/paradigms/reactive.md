---
id: reactive-programming
title: "Reactive Programming"
description: "Data streams and propagation of change. Non-blocking backpressure governs the exchange between fast producers and slow consumers."
labels: [paradigm, reactive, dataflow, backpressure]
---

# Reactive Programming

**Non-blocking backpressure is the core contribution of reactive systems. Everything else is a consequence.**

Reactive programming treats values as streams over time and propagates change automatically. The engineering breakthrough — formalised in the Reactive Streams specification (2014) — is that a slow consumer can signal a fast producer to slow down or buffer, without blocking threads.

## Reactive Manifesto Properties (2014)

Reactive systems are:
1. **Responsive** — respond in a timely manner
2. **Resilient** — stay responsive in the face of failure
3. **Elastic** — stay responsive under varying workload
4. **Message Driven** — rely on asynchronous message passing with explicit back-pressure

Source: https://www.reactivemanifesto.org/

## Reactive Streams Specification

The Reactive Streams specification (https://www.reactive-streams.org/) defines four interfaces that govern the exchange of stream data across an asynchronous boundary:

```java
// Publisher — source of data
Publisher<T>.subscribe(Subscriber<T> s)

// Subscriber — consumer of data
Subscriber<T>.onSubscribe(Subscription s)   // called once, before any items
Subscriber<T>.onNext(T item)               // called for each item
Subscriber<T>.onError(Throwable t)         // terminal
Subscriber<T>.onComplete()                 // terminal

// Subscription — the demand channel between Publisher and Subscriber
Subscription.request(long n)   // consumer signals how many items it can accept
Subscription.cancel()          // consumer cancels the subscription

// Processor — both Publisher and Subscriber (pipeline stage)
```

**The backpressure protocol:** `Subscriber.onSubscribe(sub)` is called first. The subscriber then calls `sub.request(n)` to signal it is ready for `n` items. The publisher sends at most `n` items. When the subscriber has processed them, it calls `request(n)` again. The publisher never sends more than was requested. This bounds the queue between threads without blocking.

## Backpressure — the key mechanism

**Problem:** A fast producer (LLM generating tokens at 100/s) and a slow consumer (network sending at 10/s) cannot be directly connected — the intermediary queue grows without bound.

**Solution:** The consumer controls the rate by requesting items explicitly.

```python
# Conceptual — Python does not have Reactive Streams natively,
# but the pattern maps to asyncio generators with explicit pacing:

async def consume(producer):
    async for chunk in producer:          # pulls one at a time
        await process(chunk)             # consumer drives the pace
        # producer cannot send the next until this returns

# The asyncio generator protocol IS a form of demand-driven delivery:
# the consumer's iteration step is the implicit request(1) call.
```

**Non-blocking vs blocking backpressure:**
- **Blocking:** slow consumer causes the producer thread to block. Simple but wastes threads.
- **Non-blocking (Reactive Streams):** consumer signals demand; producer sends exactly that many items; no thread blocks. Queues are bounded by design.

## Practical terminology

| Term | Meaning |
|---|---|
| **Backpressure** | Flow control signal from consumer to producer — "slow down" or "I can accept N more" |
| **Demand** | The number of items the consumer has requested via `request(n)` |
| **Overflow strategy** | What the operator does when demand is zero and items arrive anyway: DROP, BUFFER, ERROR, LATEST |
| **Hot observable** | Produces items regardless of subscribers (live data: stock prices, LLM tokens). Backpressure must be applied externally. |
| **Cold observable** | Produces items on demand (file read, database query). Backpressure is natural. |

## Implementations

| Library | Language | Notes |
|---|---|---|
| Project Reactor (`Flux`, `Mono`) | Java / JVM | Spring WebFlux default |
| RxJava 2+ (`Flowable`) | Java | `Flowable` = backpressure-aware; `Observable` = not |
| Akka Streams | Scala / JVM | Built on Reactive Streams; graph DSL |
| Python `asyncio` generator | Python | Implicit demand-driven via `async for`; no explicit `request(n)` |
| Python `anyio` / `trio` | Python | Memory channels with bounded capacity — explicit backpressure |

## Relationship to Durable Streaming

When a consumer is slow AND the producer cannot slow down (e.g., an LLM generating tokens):
1. Backpressure cannot propagate to the LLM — it runs at its own pace.
2. A **server-side buffer** (deque, log) absorbs the mismatch.
3. The consumer reads from the buffer at its own pace — this is the **EIP Durable Subscriber** pattern applied to reactive streams.

The buffer replaces `request(n)` when upstream cannot honour it.

## Complements

- `enterprise-integration-patterns` — Durable Subscriber (EIP) is the messaging pattern for slow consumers
- `architecture/protocol-design-patterns` — Durable Streaming section for server-side buffering
- `event-driven-architecture` — pub-sub at architectural scale

## References

- Reactive Streams specification — https://www.reactive-streams.org/
- Reactive Manifesto — https://www.reactivemanifesto.org/
- Reactive Manifesto glossary (back-pressure) — https://www.reactivemanifesto.org/glossary#Back-Pressure
- Project Reactor documentation — https://projectreactor.io/docs/core/release/reference/
