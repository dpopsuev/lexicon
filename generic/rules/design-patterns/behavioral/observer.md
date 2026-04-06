---
id: observer
title: "Observer Pattern"
description: "Objects need to be notified when another object changes state."
labels: [design-patterns, behavioral]
---

# Observer

**Problem:** Objects need to be notified when another object changes state.

**Solution:** Subject maintains a list of observers and notifies them on state change.

**Structure:**
- Subject: holds observers, notifies on change
- Observer: interface with Update method
- ConcreteObserver: reacts to notifications

**When to use:**
- Event systems (signal bus, UI updates)
- One-to-many state propagation

**When NOT to use:**
- Only one observer exists — direct callback is simpler
- Notification order matters critically

**Go notes:**
- Use channels: `Subscribe() <-chan Event`
- Or callbacks: `OnEvent(func(Event))`
- Djinn's terminal.ViewEvent + Subscribe is Observer

**Related:** Chain of Responsibility, Command, Mediator

**Reference:** https://refactoring.guru/design-patterns/observer
