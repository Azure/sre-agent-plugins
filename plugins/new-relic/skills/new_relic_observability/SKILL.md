---
name: new_relic_observability
description: Expertise in New Relic's full-stack observability platform covering entities, NRQL queries, alert policies, incidents, deployment analysis, log inspection, golden metrics, and performance diagnostics. Use for discovering entities, running NRQL analytics, investigating alerts, analyzing deployments, reviewing error groups, and navigating New Relic telemetry data via the New Relic MCP server.
---

# New Relic Observability Skill

You have access to the New Relic MCP server tools for querying and analyzing observability data from New Relic accounts.

## Available Tools by Category

### Entity & Account Discovery

| Tool | Purpose |
|------|---------|
| **get_entity** | Look up entities by GUID or search by name pattern |
| **list_related_entities** | Find entities one hop away from a given entity GUID |
| **list_available_new_relic_accounts** | Enumerate all accessible account IDs |
| **list_dashboards** | List all dashboards in an account |
| **get_dashboard** | Retrieve details for a specific dashboard |
| **list_entity_types** | Show the full catalog of entity types with domain/type definitions |
| **search_entity_with_tag** | Find entities by a specific tag key-value pair |
| **convert_time_period_to_epoch_ms** | Translate natural time ranges (e.g., "last 30 minutes") into epoch milliseconds |

### Data Querying

| Tool | Purpose |
|------|---------|
| **execute_nrql_query** | Run an NRQL query directly against NRDB |
| **natural_language_to_nrql_query** | Describe what you need in plain language — the server converts it to NRQL, executes it, and returns results |

### Alert Management

| Tool | Purpose |
|------|---------|
| **list_alert_policies** | List alert policies, optionally filtered by name |
| **list_alert_conditions** | Show conditions within a specific alert policy |
| **search_incident** | Search alert events (open and closed) with flexible filters |
| **list_recent_issues** | List all currently open issues for an account |
| **list_synthetic_monitors** | List synthetic monitors checking service availability |

### Incident Response

| Tool | Purpose |
|------|---------|
| **analyze_deployment_impact** | Assess how a deployment affected a specific entity's performance |
| **generate_alert_insights_report** | Produce an intelligence report for a specific issue |
| **generate_user_impact_report** | Analyze end-user impact of a specific issue |
| **list_entity_error_groups** | Retrieve error groups from Errors Inbox for an entity within a time window |
| **list_change_events** | Show deployment and change history for an entity |

### Performance Analytics

| Tool | Purpose |
|------|---------|
| **analyze_entity_logs** | Scan application logs for error patterns and anomalies |
| **analyze_golden_metrics** | Review key health signals — throughput, response time, error rate, saturation |
| **analyze_kafka_metrics** | Inspect consumer lag, producer throughput, message latency, partition balance |
| **analyze_threads** | Examine thread states, CPU usage, and memory consumption |
| **analyze_transactions** | Identify slow and error-prone transactions within a time window |
| **list_garbage_collection_metrics** | Retrieve GC and memory metrics for an entity |
| **list_recent_logs** | Fetch recent log entries for a given account and entity |

## Investigation Workflow

1. **Discover scope** — Use `list_available_new_relic_accounts` to identify which accounts to target, then `get_entity` or `search_entity_with_tag` to locate the relevant entities
2. **Assess health** — Run `analyze_golden_metrics` on the entity to get a quick health snapshot (throughput, response time, error rate)
3. **Check alerts** — Use `list_recent_issues` and `search_incident` to see if there are active or recent alert events
4. **Dive into data** — Execute NRQL queries with `execute_nrql_query` or use `natural_language_to_nrql_query` if you need the system to write the query for you
5. **Analyze logs** — Call `analyze_entity_logs` or `list_recent_logs` to look for error patterns around the incident timeframe
6. **Review deployments** — Use `list_change_events` to check for recent deployments, then `analyze_deployment_impact` to quantify their effect
7. **Examine errors** — Use `list_entity_error_groups` to surface error clusters from Errors Inbox
8. **Map dependencies** — Call `list_related_entities` to identify upstream/downstream services that may be affected

## Writing NRQL Queries

When using `execute_nrql_query`, follow these patterns:

```sql
-- Error rate by application over the last hour
SELECT count(*) FROM TransactionError
WHERE appName = 'checkout-service' SINCE 1 hour ago FACET error.class

-- Average response time by transaction
SELECT average(duration) FROM Transaction
WHERE appName = 'api-gateway' FACET name SINCE 30 minutes ago

-- Infrastructure CPU usage
SELECT average(cpuPercent) FROM SystemSample
WHERE hostname LIKE 'web-prod%' SINCE 1 hour ago TIMESERIES

-- Log error frequency
SELECT count(*) FROM Log
WHERE level = 'ERROR' FACET service.name SINCE 6 hours ago

-- Deployment markers correlated with error spike
SELECT count(*) FROM TransactionError
WHERE appName = 'payment-service' SINCE 1 day ago TIMESERIES
```

Always scope queries with `SINCE` / `UNTIL` clauses to keep result sets manageable. Start narrow (last 1 hour) and expand only if needed.

## Using Natural Language Queries

The `natural_language_to_nrql_query` tool lets you skip NRQL syntax entirely. Provide a clear question and the tool handles the translation:

- "What are the slowest transactions for the checkout service in the last 2 hours?"
- "How many 500 errors occurred across all services today?"
- "Show CPU usage trend for production hosts over the past 24 hours"
- "Which applications have the highest error rate right now?"

## Troubleshooting

| Symptom | Resolution |
|---------|-----------|
| 401/403 errors | Verify the User API key is valid and the owner has MCP server permissions (Organization Read Only or higher) |
| No data returned | Confirm the API key owner has access to the target account and entities via RBAC |
| Incorrect region | Ensure the connector URL matches your account region — `mcp.newrelic.com` for US, `mcp.eu.newrelic.com` for EU |
| Missing tools | Check whether `include-tags` filtering is restricting available tools; remove the header to get all tools |
| Preview not enabled | Navigate to Administration > Previews & Trials in the New Relic UI and enable the MCP Server preview |
| Permission error on specific tool | The API key owner's role lacks the required permission; ask an admin to adjust RBAC |
