---
id: accretion
title: Accretion
description: "Grow systems by addition only — never modify or remove. New capabilities alongside old ones; existing consumers always unaffected."
labels: [philosophy, architecture, process]
---

# Accretion

**Origin:** Rich Hickey (Clojure), "Spec-ulation" keynote (2016)

## Definition

A design principle where systems grow by addition, never by modification or removal. New capabilities are added alongside existing ones. Old capabilities are never broken, removed, or changed in incompatible ways.

## The Three Modes of API Change

Hickey defines three ways an API (function, struct, protocol) can change. Two are safe. One is not.

### Accretion (safe): provide more

Add a new function. Add a new field to a struct. Add a new optional parameter. Accept a new trait. Existing consumers are unaffected because they don't use the new thing. They continue calling the same functions, reading the same fields, passing the same arguments.

### Relaxation (safe): require less

A function that used to need three arguments now works with two. A field that was required is now optional. A validation that used to reject certain input now accepts it. Existing consumers already provide more than needed, so they still work.

### Fixation (breaking): require more or provide less

The dangerous one. Two forms, both break existing consumers.

**Require more:** a function that used to accept two arguments now requires three. Every caller breaks.

**Provide less:** a function that used to return three values now returns two. Every consumer that read the third value breaks. A struct field is removed. Every reader breaks.

**Why fixation is subtle:** the change often improves the system. Removing a field in favour of computed properties is better architecture. Requiring a richer parameter is cleaner. But each is fixation — it demands something the caller didn't have, or stops providing something the caller expected. The improvement doesn't make the breakage disappear.

## Hickey's Practical Advice

- Never remove. Never rename. Never change the meaning of an existing name.
- Add new names alongside old ones. Let old names keep working.
- If you must change semantics, add a new name with the new semantics. Deprecate the old one. Let consumers migrate at their own pace.
- Version your APIs if you must break, but prefer accretion to versioning. Versioning is an admission that you couldn't figure out how to accrete.

## Accretion in Practice

### Strangler Fig Migration

The strangler fig is accretion applied to legacy systems:

| Phase | Old API | New API | Fixation? |
|-------|---------|---------|-----------|
| Phase 1 | Works, unchanged | Added alongside. New code uses new API. | No: accretion. |
| Phase 2 | Old code migrates one by one | New API grows. | No: each migration is voluntary. |
| Phase 3 | Old API becomes internal | New API is the only public surface. | No: old API still exists, just not exported. |

At no phase does existing code break.

### The Fixation Trap

APIs evolve through fixation when:

| Change | Fixation Type |
|--------|--------------|
| New required field on existing type | Require more |
| Removing a previously emitted metric | Provide less |
| Changing a return type | Both |
| Dropping an enum variant | Provide less |

Each version that fixates breaks the consumer's assumptions. The domain model (parser strategy, trait derivation, contract abstraction) absorbs fixations so individual consumers don't have to.

## Sources

- Hickey, R. "Spec-ulation" keynote, Clojure/conj (2016)
- Hickey, R. "Maybe Not" keynote, Clojure/conj (2018)
- Martin, R. C. *Clean Architecture* (2017) — Open-Closed Principle
