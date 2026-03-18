---
name: hawkeye_incident_investigation
description: Expertise in autonomous incident investigation, root cause analysis, alert management, and incident reporting via the NeuBird Hawkeye MCP server. Use for investigating alerts, retrieving RCA results, managing projects and connections, creating manual investigations, and generating incident analytics reports.
---

# Hawkeye Incident Investigation Skill

You have access to the NeuBird Hawkeye MCP server tools for autonomous incident investigation, root cause analysis, and alert management. Hawkeye connects to cloud providers (AWS, Azure, GCP) and monitoring tools (Datadog, PagerDuty, New Relic, Grafana, etc.) to automatically investigate alerts by correlating data across connected sources.

## Available Tools

### Investigation Tools

| Tool | Purpose |
|------|---------|
| **hawkeye_list_sessions** | List investigation sessions and uninvestigated alerts. Supports `only_uninvestigated` filter to show only alerts that haven't been investigated yet |
| **hawkeye_investigate_alert** | Start an autonomous investigation for a specific alert by alert ID. The investigation correlates data across all connected sources |
| **hawkeye_get_investigation_status** | Check the real-time progress of a running investigation. Use this to monitor ongoing investigations |
| **hawkeye_continue_investigation** | Ask follow-up questions on an existing investigation for deeper analysis or to explore specific aspects |
| **hawkeye_get_rca** | Retrieve the Root Cause Analysis including timeline of events, corrective actions (with ready-to-execute scripts), and business impact assessment |
| **hawkeye_create_manual_investigation** | Create a manual investigation from a text description when no alert ID is available |

### Reporting Tools

| Tool | Purpose |
|------|---------|
| **hawkeye_get_incident_report** | Get organization-wide analytics including MTTR, time saved, investigation quality metrics, and trends |

### Project and Connection Management Tools

| Tool | Purpose |
|------|---------|
| **hawkeye_list_projects** | List all Hawkeye projects in the organization |
| **hawkeye_create_project** | Create a new project to organize connections and investigations |
| **hawkeye_set_default_project** | Set a project as the default for all subsequent operations |
| **hawkeye_get_project_details** | Get detailed info about a project including its connections and investigation instructions |
| **hawkeye_add_connection_to_project** | Link a cloud or monitoring connection to a project |
| **hawkeye_list_connections** | List all configured data source connections and their sync status |

### Help Tools

| Tool | Purpose |
|------|---------|
| **hawkeye_get_guidance** | Get interactive help and guidance on Hawkeye features, capabilities, and best practices |

## Workflow

### Investigating an Alert

1. **List uninvestigated alerts** — Use `hawkeye_list_sessions` with `only_uninvestigated=true` to find alerts that need attention
2. **Start investigation** — Use `hawkeye_investigate_alert` with the alert ID to kick off an autonomous investigation
3. **Monitor progress** — Use `hawkeye_get_investigation_status` to track the investigation in real time. Investigations typically complete in 30–60 seconds (the first investigation may take 2–5 minutes)
4. **Retrieve RCA** — Once the investigation completes, use `hawkeye_get_rca` to get the full Root Cause Analysis including timeline, corrective actions, and business impact
5. **Follow up** — Use `hawkeye_continue_investigation` to ask follow-up questions or drill deeper into specific aspects of the incident
6. **Report** — Use `hawkeye_get_incident_report` for organization-wide analytics and trends

### Manual Investigation (No Alert ID)

1. **Create investigation** — Use `hawkeye_create_manual_investigation` with a text description of the issue (e.g., "High CPU on production web servers since 2pm")
2. **Monitor and retrieve** — Follow steps 3–6 from the alert investigation workflow above

### Project Setup

1. **List projects** — Use `hawkeye_list_projects` to see existing projects
2. **Create project** — Use `hawkeye_create_project` if a new project is needed
3. **Add connections** — Use `hawkeye_add_connection_to_project` to link data sources
4. **Set default** — Use `hawkeye_set_default_project` to set the active project for investigations

### Understanding the Environment

1. **List connections** — Use `hawkeye_list_connections` to see all configured data sources and their sync status
2. **Check project details** — Use `hawkeye_get_project_details` to understand what sources are available for investigation
3. **Get guidance** — Use `hawkeye_get_guidance` for help on features and best practices

## Key Concepts

- **Projects** organize connections and investigations. Always ensure the correct project is set as default before investigating
- **Connections** are data sources (AWS, Azure, GCP, Datadog, PagerDuty, etc.) linked to a project. Investigations correlate data across all connections in the active project
- **Instructions** customize investigation behavior. There are three types:
  - **SYSTEM** — Global investigation instructions
  - **FILTER** — Rules for filtering alerts
  - **RCA** — Templates for root cause analysis output
- **RCA output** includes a timeline of events, corrective actions (often with ready-to-execute scripts), and business impact assessments

## Best Practices

- **Check uninvestigated alerts first** — Start with `hawkeye_list_sessions` using `only_uninvestigated=true` to prioritize alerts that haven't been analyzed
- **Wait for investigations to complete** — Use `hawkeye_get_investigation_status` to verify an investigation has finished before calling `hawkeye_get_rca`
- **Use follow-up questions** — After getting an initial RCA, use `hawkeye_continue_investigation` to drill into specific areas rather than starting a new investigation
- **Verify connections are synced** — Use `hawkeye_list_connections` to confirm data sources are connected and synced before investigating
- **Set the right project** — Use `hawkeye_set_default_project` to ensure investigations use the correct set of connections and instructions
- **Use manual investigations for ad-hoc issues** — When there's no alert but something seems wrong, use `hawkeye_create_manual_investigation` with a detailed description

## Troubleshooting

| Issue | Solution |
|-------|----------|
| 401/403 errors | Verify that the `X-Hawkeye-Email` and `X-Hawkeye-Password` headers (or Bearer token) are correct and the account is active |
| No alerts returned | Confirm that the active project has connected and synced data sources with `hawkeye_list_connections` |
| Investigation takes longer than expected | The first investigation in a session may take 2–5 minutes as Hawkeye indexes connected data sources. Subsequent investigations typically complete in 30–60 seconds |
| RCA not available | Ensure the investigation has completed by checking `hawkeye_get_investigation_status` before calling `hawkeye_get_rca` |
| Missing data in RCA | Check that the relevant data sources are connected to the active project using `hawkeye_get_project_details` |
| Connection sync failed | Use `hawkeye_list_connections` to check sync status. Re-add the connection or verify credentials in the Hawkeye dashboard |
| Wrong project context | Use `hawkeye_list_projects` and `hawkeye_set_default_project` to switch to the correct project |
| Unknown feature or capability | Use `hawkeye_get_guidance` for interactive help on Hawkeye features and best practices |
