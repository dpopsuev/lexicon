---
id: operational-quality
title: Operational Quality
description: SRE principles, error budgets, golden signals, and operational methodologies for knowing whether the system is working.
labels: [architecture, process]
---

# Operational Quality

**You can't improve what you can't measure. Define what "working" means before you deploy.**

## SRE Principles (Google)

| Concept | Definition | Application |
|---------|-----------|-------------|
| **SLI** (Service Level Indicator) | A quantitative measure of some aspect of service (latency p99, error rate, throughput) | Define before deploy: "what metric proves this change is good?" |
| **SLO** (Service Level Objective) | Target value for an SLI (p99 latency < 200ms, error rate < 0.1%) | The acceptance criteria for the Observe phase |
| **SLA** (Service Level Agreement) | SLO with consequences (refund, escalation) | External commitment. SLO is the internal target. |
| **Error Budget** | 100% - SLO = budget for risk. If SLO is 99.9%, you have 0.1% budget for failures. | When budget is exhausted: freeze features, focus on reliability |

## The Four Golden Signals

The minimum viable set of metrics for any service:

| Signal | What It Measures | Warning Sign |
|--------|-----------------|--------------|
| **Latency** | Time to serve a request (distinguish success vs error latency) | p99 increasing, error latency diverging from success latency |
| **Traffic** | Demand on the system (requests/sec, transactions/sec) | Sudden spike or drop. Both are anomalies. |
| **Errors** | Rate of failed requests (explicit 5xx AND implicit timeouts) | Any increase. Distinguish between client errors (4xx) and server errors (5xx). |
| **Saturation** | How full the system is (CPU, memory, disk, queue depth) | Above 80% of any resource. Performance degrades non-linearly near capacity. |

If your Observe phase provides these four, you can operate. Everything else is refinement.

## RED Method (Tom Wilkie)

For request-driven services, simpler than Golden Signals:

- **R**ate -- requests per second
- **E**rrors -- failed requests per second
- **D**uration -- distribution of request durations (histogram, not average)

## USE Method (Brendan Gregg)

For resource-oriented analysis (infrastructure, containers):

- **U**tilization -- percentage of resource busy (CPU 75%)
- **S**aturation -- work queued beyond capacity (run queue length)
- **E**rrors -- error events (disk errors, network drops, OOM kills)

## Observability vs Monitoring

| Monitoring | Observability |
|-----------|--------------|
| Known unknowns -- dashboards for expected failure modes | Unknown unknowns -- explore data to understand novel failures |
| Alerts when a metric crosses a threshold | Investigate why a metric crossed a threshold |
| "Is it broken?" | "Why is it broken?" |

Monitoring tells you THAT something is wrong. Observability tells you WHY. The Observe phase needs both.

## Operational Readiness

Before deploying any change, verify:

1. **SLIs defined** -- what metrics prove this change works?
2. **SLOs set** -- what are the acceptable thresholds?
3. **Alerting configured** -- who gets notified when SLOs breach?
4. **Runbook exists** -- what to do when the alert fires (automated or manual)?
5. **Rollback tested** -- can you undo this change in under 5 minutes?
6. **Canary plan** -- how will you verify in production before full rollout?

## Error Budget Policy

When the error budget is exhausted:

1. **Freeze** -- no new features until budget recovers
2. **Focus** -- prioritize reliability work
3. **Postmortem** -- what consumed the budget? Blameless analysis.
4. **Prevent** -- systemic fix, not just the incident

When error budget is healthy:

1. **Ship** -- you have budget to take risks
2. **Experiment** -- try new approaches, the budget absorbs failures
3. **Optimize** -- invest in developer velocity

## Anti-Patterns

- **Vanity metrics.** Measuring what's easy to measure instead of what matters. Lines of code, number of deploys, test count -- none of these prove quality.
- **Average latency.** Averages hide outliers. Use percentiles (p50, p95, p99).
- **Alert on everything.** If everything alerts, nothing alerts. Tune thresholds to actionable signals only.
- **No SLO.** Without a target, you can't know if a change improved or degraded the system. Define SLOs before deploying.
- **Dashboard without action.** A dashboard nobody looks at is monitoring theater. Every metric must have a response plan.

## Complements

- `defense-in-depth` -- operational quality is the Observe+React layer of the membrane
- `testing-methodology` -- ROGYB covers pre-deploy verification; operational quality covers post-deploy
- `trust-boundaries` -- SLO breaches signal trust boundary failures
