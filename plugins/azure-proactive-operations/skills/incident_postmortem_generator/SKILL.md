---
name: incident_postmortem_generator
description: Generate a blameless incident postmortem from the current or recent SRE Agent investigation. Use after an incident is resolved, when asked for a postmortem, RCA report, or lessons learned document.
---

# Incident Postmortem Generator

## Purpose

Automatically generate a structured, blameless postmortem document from the context of a resolved incident investigation.

## Principles

- **Blameless**: Focus on systems and processes, never individuals
- **Evidence-based**: Every claim backed by data (logs, metrics, timeline)
- **Action-oriented**: Every finding leads to a concrete action item

## Procedure

### Step 1: Gather incident context

From the current conversation and agent memory, extract:
- Incident ID, severity, duration, affected services, impacted users
- Timeline: issue start, detection, acknowledgment, mitigation, resolution
- Root cause and contributing factors

### Step 2: Assess detection and response

| Metric | Description |
|--------|-------------|
| Time to detect (TTD) | Issue start to alert firing |
| Time to acknowledge (TTA) | Alert to human engagement |
| Time to mitigate (TTM) | Engagement to mitigation |
| Time to resolve (TTR) | Total duration |

### Step 3: Five Whys analysis

Apply the 5 Whys technique to the root cause to identify systemic issues.

### Step 4: Generate action items

For each finding, create a SMART action item with owner, priority, and due date. Categories: prevent recurrence, improve detection, improve response, improve resilience.

### Step 5: Compile postmortem

Generate the full document with: executive summary, impact, timeline, root cause, Five Whys, detection assessment, what went well, what could be improved, action items, and lessons learned.

## Sample output

| Field | Value |
|-------|-------|
| Incident Date | 2026-07-10 |
| Severity | Sev-2 |
| Duration | 1h 23m |

**Summary**: payment-service returned HTTP 500 errors for 1h 23m due to OOM-killed pod. Root cause: memory leak in connection pool from release v2.14.0. Impact: ~340 failed transactions, 120 customers.

| Metric | Value | Target | Status |
|--------|-------|--------|--------|
| TTD | 6m | <5m | ⚠️ |
| TTA | 4m | <15m | ✅ |
| TTM | 28m | <30m | ✅ |
| TTR | 1h 23m | <2h | ✅ |

## References

- Postmortem Culture: https://learn.microsoft.com/en-us/azure/well-architected/operational-excellence/mitigation-strategy
- Azure Resource Health: https://learn.microsoft.com/en-us/azure/service-health/resource-health-overview
