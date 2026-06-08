---
id: effective-typescript
title: Effective TypeScript
description: TypeScript-specific conventions for type safety, async discipline, and code hygiene. Covers the smells the compiler cannot catch by default.
labels: [typescript, javascript, code-hygiene, type-safety, async]
---

# Effective TypeScript

## 1. No inline dynamic imports for types

`import("pkg").Type` in a type position is a dynamic import expression, not a type import. It forces module loading at runtime and defeats tree-shaking. Use top-level `import type` instead.

```typescript
// Bad — inline dynamic import in a type position
const fn = async (x: import("some-lib").Foo): Promise<import("some-lib").Bar> => { ... };

// Good — top-level type import
import type { Foo, Bar } from "some-lib";
const fn = async (x: Foo): Promise<Bar> => { ... };
```

Rules:
- All type imports are top-level `import type { ... }`.
- `import()` inside a type annotation is never acceptable. No exceptions.

---

## 2. `as unknown as T` is always a bug report

A double cast (`as unknown as T`) tells the compiler to forget what it knows. It is the type system's ejector seat. Every occurrence is evidence of a type mismatch somewhere upstream that has not been fixed.

```typescript
// Bad — hiding a type mismatch
const items = adaptItems(rawItems) as unknown as Item[];

// Good — fix the mismatch at the source
// RawItem should extend or alias Item, not be a separate incompatible type
```

Treatment:
- Find where the two types diverge. Fix the divergence.
- If the types are structurally compatible but named differently, make one an alias or subtype of the other.
- If the cast crosses an external API boundary (e.g., a library returns `unknown`), narrow with a type guard or Zod schema, not a cast.

---

## 3. `as T` over type guards is runtime unsafety

A type assertion (`value as Foo`) tells the compiler to trust you. It is not checked at runtime. Prefer a type guard that narrows based on actual shape.

```typescript
// Bad — assertion that could lie at runtime
const role = (msg as { role: string }).role;

// Good — guard that checks before narrowing
function hasRole(x: unknown): x is { role: string } {
    return typeof x === "object" && x !== null && "role" in x && typeof (x as { role: unknown }).role === "string";
}
```

Rules:
- Use type guards for values that arrive from external sources (event payloads, JSON, network responses).
- `as T` is acceptable only when you can prove correctness at the call site and a guard would be trivially equivalent. Document why.
- Repeated inline `(x as { field?: T }).field` in multiple places signals a missing interface or type guard function.

---

## 4. Payload accessor helpers over repeated inline narrowing

Accessing `Record<string, unknown>` payload fields with repeated `typeof x === "string" ? x : undefined` is a bloater. Extract typed accessor helpers.

```typescript
// Bad — repeated 30+ times across every action handler
const path = typeof ctx.payload.path === "string" ? ctx.payload.path : "";
const maxLines = typeof ctx.payload.maxLines === "number" ? ctx.payload.maxLines : undefined;

// Good — extract once, use everywhere
function getString(payload: Record<string, unknown>, key: string): string | undefined {
    const v = payload[key];
    return typeof v === "string" ? v : undefined;
}
function getNumber(payload: Record<string, unknown>, key: string): number | undefined {
    const v = payload[key];
    return typeof v === "number" ? v : undefined;
}

const path = getString(ctx.payload, "path") ?? "";
const maxLines = getNumber(ctx.payload, "maxLines");
```

Signal: if the same narrowing pattern appears more than three times, extract it.

---

## 5. `void promise` suppresses lint but not errors

`void asyncFn()` satisfies the no-floating-promises lint rule but does not handle the error. If the promise rejects, the rejection is swallowed silently.

```typescript
// Suppresses lint, swallows errors
void this.db.write(record);

// Better — explicit fire-and-forget with error logging
this.db.write(record).catch((err) => log.warn({ err }, "write failed"));
```

Rules:
- `void fn()` is acceptable only for genuinely intentional fire-and-forget where failure is non-fatal AND documented.
- Any fire-and-forget that writes to durable storage, a network socket, or a file should have a `.catch()` that at minimum logs.
- Never `void` a promise that a caller might reasonably expect to have completed.

---

## 6. `catch (e)` requires narrowing before use

TypeScript catches arrive typed as `unknown` in strict mode. Accessing properties on `e` without narrowing is an error waiting to happen.

```typescript
// Bad — e is unknown, this crashes if e is not an Error
} catch (e) {
    console.error(e.message);
}

// Good — narrow first
} catch (e) {
    const msg = e instanceof Error ? e.message : String(e);
    console.error(msg);
}

// Also good — always safe
} catch (e) {
    log.warn({ error: String(e) }, "operation failed");
}
```

Rule: every `catch (e)` that uses `e` must narrow with `instanceof Error` or use `String(e)`. Bare `catch {}` (no binding) is acceptable for expected-failure paths where the error is irrelevant.

---

## 7. Non-null assertions signal missing structure

`value!` tells the compiler a value is definitely not null. Each occurrence is a promise to the compiler that you may not be able to keep.

```typescript
// Bad — crashes if set is undefined
set!.delete(handler);

// Good — use optional chaining or restructure so set is guaranteed
set?.delete(handler);
```

Rules:
- If a value might genuinely be null/undefined, use optional chaining or an explicit guard.
- If a value is structurally guaranteed to be non-null at that call site, restructure the code to make the guarantee visible to the compiler (e.g., create the value in the same scope).
- Count of `!` in production code should be zero or close to zero.

---

## 8. Structural type alignment over parallel type hierarchies

When two types represent the same concept but are declared separately, casts accumulate. Align them at the declaration site.

```typescript
// Bad — InternalItem and ExternalItem describe the same shape,
// forcing casts everywhere they interact
interface InternalItem { id: string; name: string; }
type ExternalItem = { id: string; label: unknown; }; // from library

const projected = adaptItems(items) as unknown as ExternalItem[]; // required every time

// Good — make InternalItem satisfy ExternalItem structurally,
// or use ExternalItem directly, or use a shared base type
```

Rule: if you find yourself writing `as unknown as ExternalType` to bridge two internal types, one of the types is wrong. Fix the type, not the cast.

---

## 9. Async event handlers require explicit error boundaries

Attaching an `async` function as an event handler (bus subscriber, DOM event, EventEmitter) means the handler's Promise is not awaited by the emitter. An unhandled rejection in the handler is silently dropped.

```typescript
// Bad — rejection from asyncHandler is silently dropped
emitter.on("event", async (data) => {
    await riskyOperation(data); // if this throws, nobody knows
});

// Good — wrap with an explicit error boundary
emitter.on("event", async (data) => {
    try {
        await riskyOperation(data);
    } catch (e) {
        log.error({ error: String(e) }, "event handler failed");
    }
});
```

Rule: every `async` function passed to a subscriber/listener/callback must either handle its own errors internally or the caller must explicitly `await` the returned Promise.

---

## What belongs where

| Pattern | Treatment |
|---------|-----------|
| `as unknown as T` | Fix the type mismatch upstream |
| `as T` on external data | Replace with type guard or Zod parse |
| Repeated payload narrowing | Extract `getString`, `getNumber` helpers |
| `void asyncFn()` | Add `.catch()` if failure matters |
| `catch (e)` using `e` | Narrow with `instanceof Error` or `String(e)` |
| `value!` non-null | Restructure so compiler sees the guarantee |
| Parallel type hierarchies | Align declarations, eliminate casts |
| Inline `import()` types | Top-level `import type` |
| `async` event handler | Wrap body in try/catch |

**Reference:** https://effectivetypescript.com / https://typescript-eslint.io/rules/
