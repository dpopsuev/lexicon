---
id: bug-template
title: Bug Template
kind: bug
description: Defect tracking — observed vs expected behavior
labels: [process, template]
---

# {Bug Title}

> **Status:** `open` | `active` | `complete`
> **Severity:** critical | high | medium | low

---

## Observed vs Expected

**Observed:** {What actually happens.}

**Expected:** {What should happen instead.}

---

## Reproduction

Steps to reproduce the defect.

1. {Step 1}
2. {Step 2}
3. {Observe: ...}

Environment: {OS, version, config, etc.}

---

## Root Cause

(Filled during investigation.)

> {Component, code path, why it happens.}

---

## Fix

(Filled during resolution.)

> {What was changed. Tests added. Residual risk.}

---

## Security Assessment

If a trust boundary is affected, document here.
If none, write: "No trust boundaries affected."

| OWASP | Finding | Mitigation |
|-------|---------|------------|
