---
name: pager_duty
description: Load this skill for expertise in PagerDuty's incident management platform including incidents, on-call schedules, services, teams, escalation policies, event orchestration, incident workflows, and status pages. Use for managing incidents, checking on-call status, investigating alerts, escalating issues, and navigating PagerDuty data via the PagerDuty MCP server.
---

# PagerDuty Incident Management for Azure Resources

## Core Capabilities

- List incidents for a mapped Azure resource/service (focus on active first)
- Retrieve an incident by ID (status, responders, notes, timeline)
- Acknowledge (establish ownership) and document current hypothesis + next update ETA
- Add structured diagnostic/update notes (context, evidence, action, next steps)
- Resolve (after verified recovery) with cause, fix, and follow‑up tasks
- Query PagerDuty AI assistant for runbook steps, differential diagnosis, known issues, validation & rollback guidance

## Required Context Inputs

- Azure resource identifier (full resource ID or name + subscription/resource group)
- PagerDuty incident ID (if direct action) OR resource → mapped PagerDuty service
- Incident attributes: status, urgency, responders, escalation policy, recent notes
- Telemetry snapshot (alerts, metrics, logs, recent deployments/changes) when diagnosing or resolving

## Lightweight Workflow

1. Target: Incident ID given? → act on it. Else map Azure resource → service → active incidents.
2. Validate: Confirm status, service mapping, recent notes/timeline, ownership.
3. Decide Action: situational awareness, acknowledge, add note, resolve, or AI guidance.
4. Execute: Perform action using appropriate tool(s); keep communication concise (system prompt style).
5. Document: Add/update note (especially for acknowledge/resolve) with clear evidence + next step.
6. Follow-Up: Ensure stakeholders informed; link telemetry or change records if relevant.

## Action Guidance

### List / Filter by Azure Resource

- Map resource → PagerDuty service/integration.
- Return active (triggered/acknowledged) first; include resolved only if user requests history.
- Present essentials: ID, title, status, urgency, service, primary assignee/responders, brief timeline summary.

### Retrieve by Incident ID

- Show: status, responders, escalation policy, last 2–3 notes, triggers (alerts/metrics), recent changes.
- Provide concise status + any blocking investigation context.

### Acknowledge

- **CRITICAL**: Before acknowledging, ALWAYS fetch the latest incident details using GetPagerDutyIncidentById and check the incident status.
- **Only acknowledge incidents in "triggered" status**. Do NOT acknowledge incidents that are already "acknowledged", "resolved", or "closed".
- Acknowledging a resolved incident will reactivate it and trigger unnecessary alerts to on-call engineers.
- Do when taking ownership or starting active investigation of a triggered incident.
- Note template:
  "Acknowledged at [UTC timestamp]. Observations: [key signal]. Hypothesis: [initial]. Next update: [ETA]."

### Add Note

- Use structured mini-format:
  Context: what was looked at / changed
  Evidence: metrics/logs/error signatures/trace IDs
  Action: commands, config adjust, rollback, none
  Next: monitoring step, verification window, escalation trigger
- Keep each section to a sentence or compact list; link Azure artifacts when clarifying.

### Resolve

- Preconditions: symptom cleared, metrics/alerts normal, fix or rollback validated, no residual impact.
- Resolution note:
  "Resolved at [UTC]. Cause: [root/trigger]. Fix: [action]. Verification: [metrics/tests]. Follow-up: [RCA task / runbook update]."

### Query PagerDuty AI Assistant

- Provide: incident ID/title, service, resource type, key error patterns, recent note summary.
- Ask for specific output (e.g., "runbook steps for storage latency", "differential diagnosis for CPU spike", "rollback procedure for failed deployment").
- Cross-check AI suggestions against live telemetry before acting.

## Best Practices

- Ownership: Ensure exactly one active owner post-ack.
- Brevity: Notes should accelerate future decisions; avoid narrative fluff.
- Auditability: Reference change IDs, deployment timestamps, or query links when relevant.
- Escalation: If no progress within SLA window or repeated blockers → escalate per policy.
- Consistency: Use UTC timestamps; avoid subjective language ("seems fine").

## Error Handling (Condensed)

| Scenario | Response |
|----------|----------|
| Incident or resource not found | Revalidate IDs; confirm subscription/resource group; clarify mapping. |
| Permission denied | Surface need for authorized responder; pause action. |
| Ambiguous AI output | Request clarification or narrower query; verify with telemetry. |
| Action tool failure | Parse error → 1 retry if transient; after 2 failures escalate (aligns with system prompt). |

## Examples

### List incidents for Azure VM

Input: VM resource ID → map service → list active incidents (ID, title, status, urgency, assignee).

### Acknowledge + Diagnostic Note

"Acknowledged at 10:04 UTC. Observations: sustained 95% CPU on scale set. Hypothesis: autoscale lag. Next update: 10:19 UTC."

### Resolve After Fix

"Resolved at 11:22 UTC. Cause: misconfigured autoscale profile. Fix: adjusted min/max instances. Verification: CPU stable <60% for 20m, alerts cleared. Follow-up: update autoscale runbook & add guardrail validation."

## Quick Reference Checklists

Acknowledgment: fetch latest incident → verify status is "triggered" → acknowledge → ownership established → diagnostic outline → ETA set.
Resolution: impact ended → metrics normal → cause identified → fix validated → follow-up logged.
AI Query: clear question → incident context packaged → narrow objective → verify output.

---
Use this skill only for PagerDuty incident lifecycle and diagnostics enrichment; defer all general Azure discovery & configuration behaviors to the core system instructions.
