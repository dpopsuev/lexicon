---
id: composition-over-inheritance
title: "Composition Over Inheritance"
description: "Favor has-a over is-a. Compose behavior from small interfaces and embedded types instead of inheriting from deep class hierarchies."
labels: [pragmatic, architecture, design, oop, composition]
always_apply: true
---

# Composition Over Inheritance

**Favor has-a over is-a. Build behavior by composing small, focused components instead of inheriting from deep class hierarchies.**

## The Problem with Inheritance

- **Tight coupling** — child is bound to parent's implementation, not just its interface
- **Fragile base class** — changing the parent breaks all children
- **Rigid hierarchy** — can't mix behaviors from multiple parents (diamond problem)
- **Refused bequest** — child inherits methods it doesn't want (code smell)
- **Exploding taxonomy** — N dimensions × M variants = N×M classes

## The Composition Alternative

Instead of "X is-a Y", ask "X has-a Y" or "X behaves-like Y":

```
Inheritance:   Dog extends Animal extends LivingThing
Composition:   Dog has-a Walker, has-a Eater, behaves-like Pet
```

## Per-Language Idioms

### Go — Embedding + Interfaces (no inheritance exists)

```go
// Behavior defined by interface (consumer-side)
type Reader interface { Read(p []byte) (int, error) }
type Writer interface { Write(p []byte) (int, error) }
type ReadWriter interface { Reader; Writer }  // composed interface

// Struct composition via embedding
type BufferedWriter struct {
    Writer              // embedded — promotes Write() method
    buf    bytes.Buffer // own state
}

// NOT: type BufferedWriter extends Writer  ← doesn't exist in Go
```

**Go rule:** No classes, no inheritance, no constructors. Interfaces are implicit (duck typing). Embedding promotes methods without creating is-a relationships.

### Python — ABC + Protocols + Composition

```python
# Interface via ABC (Abstract Base Class)
from abc import ABC, abstractmethod

class Serializable(ABC):
    @abstractmethod
    def serialize(self) -> bytes: ...

# Or Protocol (structural typing, Python 3.8+)
from typing import Protocol

class Readable(Protocol):
    def read(self, n: int) -> bytes: ...

# Composition — NOT class Dog(Animal)
class Dog:
    def __init__(self):
        self.walker = Walker()      # has-a
        self.eater = Eater()        # has-a

    def walk(self):
        return self.walker.walk()   # delegation
```

**Python rule:** Use ABC or Protocol for contracts. Use composition + delegation for behavior reuse. Avoid deep `class Child(Parent)` hierarchies. Mixins are acceptable for small, orthogonal behaviors.

### Rust — Traits (no inheritance exists)

```rust
// Behavior defined by trait
trait Drawable {
    fn draw(&self);
}

trait Resizable {
    fn resize(&mut self, width: u32, height: u32);
}

// Composition: struct holds components, implements traits
struct Button {
    label: String,
    bounds: Rectangle,  // has-a
}

impl Drawable for Button {
    fn draw(&self) { /* use self.bounds */ }
}

impl Resizable for Button {
    fn resize(&mut self, w: u32, h: u32) { self.bounds.resize(w, h); }
}
```

**Rust rule:** No inheritance. Traits define behavior. Default trait methods provide shared logic. Composition via struct fields. `impl Trait for Type` — explicit, no implicit inheritance chain.

### Java — Interfaces + Delegation

```java
// Interface for contract
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

// Composition via delegation, NOT extends
class Duck implements Flyable, Swimmable {
    private final FlyBehavior flyBehavior;      // has-a
    private final SwimBehavior swimBehavior;    // has-a

    public void fly() { flyBehavior.fly(); }   // delegation
    public void swim() { swimBehavior.swim(); }
}
```

**Java rule:** "Favor composition over inheritance" — Effective Java, Item 18. Use interfaces for contracts. Use delegation for code reuse. Reserve `extends` for true is-a relationships with stable base classes.

### TypeScript — Interfaces + Mixins

```typescript
// Interface for contract (structural typing)
interface Serializable {
    serialize(): string;
}

interface Loggable {
    log(message: string): void;
}

// Composition via mixin pattern
class UserService implements Serializable, Loggable {
    private logger = new Logger();    // has-a
    private serializer = new JSONSerializer();  // has-a

    serialize(): string { return this.serializer.toJSON(this); }
    log(msg: string) { this.logger.write(msg); }
}
```

**TypeScript rule:** Structural typing means interfaces are implicit. Use intersection types (`A & B`) for composed contracts. Avoid `extends` chains deeper than 2 levels.

### C# — Interfaces + Composition

```csharp
// Interface for contract
interface INotifiable {
    void Notify(string message);
}

// Composition
class OrderService : INotifiable {
    private readonly IEmailSender _email;   // has-a
    private readonly ISmsGateway _sms;      // has-a

    public void Notify(string msg) {
        _email.Send(msg);
        _sms.Send(msg);
    }
}
```

**C# rule:** Prefer interfaces over abstract classes. Use dependency injection for composition. Default interface methods (C# 8+) provide shared behavior without inheritance.

## Decision Framework

| Signal | Use Inheritance | Use Composition |
|---|---|---|
| True is-a relationship | ✓ (rare) | |
| Behavior varies by type | | ✓ (Strategy pattern) |
| Multiple behavior dimensions | | ✓ (mix interfaces) |
| Base class is stable and unlikely to change | ✓ (cautiously) | |
| Need to swap behavior at runtime | | ✓ |
| Code reuse across unrelated types | | ✓ (embed/delegate) |
| Framework requires subclassing | ✓ (forced) | |

## The Test

> If you can't confidently say "X **is-a** Y in every case, forever", use composition.

A `Square` is-a `Rectangle`? No — changing width should change height for squares but not rectangles. Composition: `Square` has-a `Shape` with constrained dimensions.

## Related

- `architecture/solid-principles` — LSP (Liskov Substitution) catches bad inheritance
- `code-smells/oo-abusers/refused-bequest` — child doesn't want parent's methods
- `design-patterns/structural/decorator` — add behavior via composition, not subclassing
- `design-patterns/behavioral/strategy` — swap algorithms via composition
- `refactoring/dealing-with-generalization/replace-inheritance-with-delegation` — the refactoring technique

**References:**
- [Composition over inheritance — Wikipedia](https://en.wikipedia.org/wiki/Composition_over_inheritance)
- [Python Composition over Inheritance](https://python-patterns.guide/gang-of-four/composition-over-inheritance/)
- [Why Modern Languages Prefer Composition](https://leapcell.io/blog/why-modern-languages-prefer-composition-over-inheritance)
- [From Java to Go: Composition over Inheritance](https://dev.to/leapcell/from-java-to-go-why-composition-is-preferred-over-inheritance-17h4)
- Effective Java, Item 18: "Favor composition over inheritance" — Joshua Bloch
