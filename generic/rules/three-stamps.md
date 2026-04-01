---
id: three-stamps
title: Three Stamps (Artifact Transition Authority)
description: Three stamp types for artifact lifecycle transitions — Deterministic, Stochastic, Sovereign. Trust ordering and override rules.
labels: [lifecycle, stamps, quality, jidoka]
---

# Three Stamps

**Every artifact transition requires a stamp. Three types exist, with different trust levels.**

Adapted from Toyota's Jidoka principle — automation with human touch.

## Stamp Types

| Stamp | Authority | Trust | Example |
|-------|-----------|-------|---------|
| **Deterministic** | Djinn programmatic logic | Highest | Tests pass, lint clean, build compiles, sections filled |
| **Sovereign** | Human operator | Medium | "Approved to start", "Good enough", "Skip gate" |
| **Stochastic** | AI agent | Lowest | "I believe this is correct", "Evidence filled" |

Trust ordering: **Deterministic > Sovereign > Stochastic**

You trust math over humans over LLMs.

## Transition Rules

Each artifact transition requires specific stamp types:

| Transition | Required Stamps | Why |
|-----------|----------------|-----|
| Draft → Approved | Sovereign | Operator says "yes, do this work" |
| Approved → Ready | Deterministic | Gates resolved, sections computed from ComponentMap |
| Ready → In-Progress | Stochastic | Agent claims the work |
| In-Progress → In-Review | Stochastic | Agent fills evidence sections |
| In-Review → Done | Deterministic + Sovereign | Tests pass AND operator approves |

## Sovereign Override

The operator can skip any gate. Override is:
- **Allowed** — sovereign is sovereign
- **Logged** — non-repudiation audit trail
- **Annotated** — reason recorded on artifact
- **Visible** — appears in drift detection

Override does NOT change the stamp type — it records "Sovereign overrode Deterministic requirement" with timestamp and reason.

## Agent Self-Review

The stochastic stamp (agent says "done") is the LEAST trusted. It requires co-verification:
- Low risk (blast radius < 5): Deterministic stamp sufficient (tests pass = accepted)
- Medium risk (blast radius 5-15): Deterministic + Sovereign required
- High risk (blast radius > 15): All three stamps required

## Complements

- `factory-principles` — Jidoka: stop the line on defect
- `defense-in-depth` — stamps ARE the Swiss Cheese layers
- `trust-boundaries` — stamp requirements scale with trust boundary severity
