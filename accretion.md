# Accretion

**Origin:** Rich Hickey (Clojure), "Spec-ulation" keynote (2016)

## Definition

A design principle where systems grow by addition, never by modification or removal. New capabilities are added alongside existing ones. Old capabilities are never broken, removed, or changed in incompatible ways.

## The Three Modes of API Change

Hickey defines three ways an API (function, struct, protocol) can change. Two are safe. One is not.

### Accretion (safe): provide more

Add a new function. Add a new field to a struct. Add a new optional parameter. Accept a new trait. Existing consumers are unaffected because they don't use the new thing. They continue calling the same functions, reading the same fields, passing the same arguments.

Examples:
- Adding `Services []*ServiceInstance` to Clock. Code that only reads `Traits` and `NICs` is unaffected.
- Adding `TraitHA = "HA"` to the trait vocabulary. Existing queries that don't use `"HA"` keep working.
- Adding `HardwareConfigParser` alongside `PtpConfigV1Parser`. Existing parsers still run.

### Relaxation (safe): require less

A function that used to need three arguments now works with two. A field that was required is now optional. A validation that used to reject certain input now accepts it. Existing consumers already provide more than needed, so they still work.

Examples:
- A parser that used to require `plugins.e810.settings` now also works without it (falls back to HardwareConfig).
- A query that used to require exact profile type matching now accepts trait-based filtering (broader).

### Fixation (breaking): require more or provide less

The dangerous one. Two forms, both break existing consumers.

**Require more (demand what the caller doesn't have):**

A function that used to accept two arguments now requires three. Every caller breaks at compile time (or worse, at runtime with missing defaults). A field that was optional is now required. Every producer that omitted it breaks.

Examples:
- `GetUbloxProtocolVersion(profile *PtpProfile)` changing to `GetUbloxProtocolVersion(profile *PtpProfile, family NICFamily)`. Every call site must be updated. This is fixation even if the new parameter provides better behaviour.
- A PtpConfig CR that used to work without `ptpSettings.controllingProfile` now requires it for T-BC detection. Older configs without the field break the detection.

**Provide less (stop giving what the caller expects):**

A function that used to return three values now returns two. Every consumer that read the third value breaks. A struct field is removed. Every reader breaks. A metric that used to be emitted is dropped. Every dashboard that reads it goes blank.

Examples:
- Removing `BaseType ClockBaseType` from Clock. Any code that read `clock.BaseType` breaks at compile time.
- `plugins.e825.settings` disappearing on GNRD 4.22+ configs. `GetPtpConfigProfilePluginSettings` returns an error because the `settings` key doesn't exist. Seven call sites break.
- T-TSC clock class changing from transitioning (6 → 165 → 248) to static (255). Tests that assert clock class transitions get a value they don't expect.

**Why fixation is subtle:** the change often improves the system. T-TSC clock class 255 is *correct* per the *Telecom Profile*. Removing `BaseType` in favour of traits is *better* architecture. Requiring `NICFamily` instead of raw profile is *cleaner*. But each is fixation: it demands something the caller didn't have, or stops providing something the caller expected. The improvement doesn't make the breakage disappear.

## Hickey's Practical Advice

- Never remove. Never rename. Never change the meaning of an existing name.
- Add new names alongside old ones. Let old names keep working.
- If you must change semantics, add a new name with the new semantics. Deprecate the old one. Let consumers migrate at their own pace.
- Version your APIs if you must break, but prefer accretion to versioning. Versioning is an admission that you couldn't figure out how to accrete.

## Accretion Applied to PTP Test Suite

### Strangler Fig Migration

The migration from ProfileInfo to Clock is accretion in action:

| Phase | Old API (ProfileInfo) | New API (Clock) | Fixation? |
|-------|----------------------|-----------------|-----------|
| Phase 1 | Works, unchanged | Added alongside. New tests use Clock. | No: accretion. |
| Phase 2 | Old tests migrate one by one | Clock facade grows. | No: each migration is voluntary. |
| Phase 3 | ProfileInfo becomes internal | Clock is the only public API. | No: ProfileInfo still exists, just not exported. Nothing is removed. |

At no phase does existing code break. ProfileInfo is never removed from the system. It becomes an internal implementation detail, but any code that compiled before continues to compile.

### The OCP API's Fixation Problem

The PTP Operator's API evolves through fixation:

| Version | Change | Fixation Type |
|---------|--------|--------------|
| 4.20 | T-BC requires `controllingProfile` | Require more (new field needed for detection) |
| 4.21 | T-TSC clock class changes to 255 | Provide less (expected clock class transitions stop happening) |
| 4.22 | `plugins.e825.settings` disappears | Provide less (holdover params key absent) |
| 4.22 | HardwareConfig CR required for holdover | Require more (new CR must be read) |

Each version is a fixation that breaks the test suite's assumptions. The domain model (parser strategy, trait derivation, clock class contract) absorbs these fixations so individual tests don't have to.

## Sources

- Hickey, R. "Spec-ulation" keynote, Clojure/conj (2016)
- Hickey, R. "Maybe Not" keynote, Clojure/conj (2018)
- Martin, R. C. *Clean Architecture* (2017) -- Open-Closed Principle
