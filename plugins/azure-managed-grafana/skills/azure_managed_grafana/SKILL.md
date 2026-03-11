---
name: azure_managed_grafana
description: Expertise in Azure Managed Grafana including dashboards, Azure Monitor metrics, Application Insights failures and traces, Azure Data Explorer (Kusto) queries, SQL Server queries, and Azure Resource Graph. Use for querying observability data, searching dashboards, investigating failures, analyzing GenAI agent telemetry, and running KQL or SQL queries via the AMG-MCP endpoint.
---

# Azure Managed Grafana Observability Skill

You have access to the Azure Managed Grafana MCP server (AMG-MCP) tools for querying observability data, dashboards, and connected data sources.

## Available Tools

| Tool | Purpose |
|------|---------|
| **amgmcp_insights_get_failures** | Get failure insights from Application Insights — failed requests, dependencies, and exceptions |
| **amgmcp_insights_get_agents** | Get GenAI agent insights from Application Insights — invocations, token usage, latency (OpenTelemetry GenAI Semantic Conventions) |
| **amgmcp_kusto_get_metadata** | List connected Azure Data Explorer clusters with databases and schema |
| **amgmcp_kusto_query** | Execute KQL queries against Azure Data Explorer clusters |
| **amgmcp_mssql_get_metadata** | List connected SQL Server data sources with databases, tables, and column schemas |
| **amgmcp_mssql_query** | Execute SQL queries against connected Microsoft SQL Server data sources |
| **amgmcp_query_application_insights_trace** | Query Application Insights trace data, aggregating across multiple instances |
| **amgmcp_query_azure_subscriptions** | List Azure subscriptions accessible through the Grafana Azure Monitor data source |
| **amgmcp_query_resource_graph** | Execute Azure Resource Graph (ARG) queries for resource inventory and configuration |
| **amgmcp_query_resource_log** | Query Azure resource logs through the Azure Monitor data source |
| **amgmcp_query_resource_metric** | Query Azure resource metric values through the Azure Monitor data source |
| **amgmcp_query_resource_metric_definition** | Get available metric definitions for Azure resources |
| **amgmcp_dashboard_search** | Search Grafana dashboards by query string — returns title, UID, folder, tags, and URL |
| **amgmcp_datasource_list** | List all configured Grafana data sources |

## Workflow

### General Investigation

1. **Understand the scope** — Identify which Azure resources, services, or data sources are involved
2. **Discover data sources** — Use `amgmcp_datasource_list` to see what's connected
3. **Check subscriptions** — Use `amgmcp_query_azure_subscriptions` to list accessible subscriptions
4. **Query relevant data** — Use the appropriate tool for the data type (metrics, logs, traces, KQL, SQL)
5. **Search dashboards** — Use `amgmcp_dashboard_search` to find existing dashboards with relevant visualizations
6. **Analyze and recommend** — Summarize findings with actionable next steps

### Failure Investigation

1. **Get failure overview** — Use `amgmcp_insights_get_failures` to retrieve failure summary (failed requests, dependencies, exceptions)
2. **Trace failures** — Use `amgmcp_query_application_insights_trace` to follow request traces across services
3. **Check metrics** — Use `amgmcp_query_resource_metric` to correlate with resource-level metrics (CPU, memory, latency)
4. **Query logs** — Use `amgmcp_query_resource_log` for detailed log entries around the failure time
5. **Check resource health** — Use `amgmcp_query_resource_graph` to verify resource configuration and status

### Azure Monitor Metrics Analysis

1. **Discover metrics** — Use `amgmcp_query_resource_metric_definition` to list available metrics for a resource
2. **Query metric values** — Use `amgmcp_query_resource_metric` with the desired metric name, aggregation, and time range
3. **Correlate** — Check related resources' metrics to identify cascading issues

### Azure Data Explorer (Kusto) Queries

1. **Get cluster metadata** — Use `amgmcp_kusto_get_metadata` to list clusters, databases, and table schemas
2. **Write and run KQL** — Use `amgmcp_kusto_query` to execute Kusto Query Language queries

**Common KQL patterns:**

```kql
// Recent errors from a table
TableName
| where Timestamp > ago(1h)
| where Level == "Error"
| project Timestamp, Message, Source
| order by Timestamp desc
| take 100

// Aggregated error counts
TableName
| where Timestamp > ago(24h)
| summarize ErrorCount = count() by bin(Timestamp, 1h), Source
| order by Timestamp desc

// Percentile latency analysis
RequestTable
| where Timestamp > ago(1h)
| summarize p50 = percentile(Duration, 50),
            p95 = percentile(Duration, 95),
            p99 = percentile(Duration, 99)
  by bin(Timestamp, 5m)
```

### SQL Server Queries

1. **Get schema metadata** — Use `amgmcp_mssql_get_metadata` to list databases, tables, and columns
2. **Run queries** — Use `amgmcp_mssql_query` to execute SQL queries

### GenAI Agent Telemetry

Use `amgmcp_insights_get_agents` to analyze GenAI agent performance. This tool queries data following [OpenTelemetry Generative AI Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) and returns:
- Agent invocation counts
- Token usage (input/output)
- Latency distributions
- Error rates

### Azure Resource Graph Queries

Use `amgmcp_query_resource_graph` to query resource inventory across subscriptions:

```kusto
// List all VMs and their status
Resources
| where type == "microsoft.compute/virtualmachines"
| project name, location, properties.hardwareProfile.vmSize, powerState = properties.extended.instanceView.powerState.code

// Find resources by tag
Resources
| where tags["environment"] == "production"
| summarize count() by type

// Check NSG rules
Resources
| where type == "microsoft.network/networksecuritygroups"
| mv-expand rules = properties.securityRules
| project name, ruleName = rules.name, access = rules.properties.access, direction = rules.properties.direction
```

## Best Practices

- **Start with data source discovery** — Always use `amgmcp_datasource_list` to understand what's connected before querying
- **Check metric definitions first** — Use `amgmcp_query_resource_metric_definition` before querying metrics to get correct names
- **Get schema before querying** — Use `amgmcp_kusto_get_metadata` or `amgmcp_mssql_get_metadata` to discover table/column names
- **Use dashboards for context** — Search for existing dashboards with `amgmcp_dashboard_search` — they often reveal which metrics and queries are most relevant
- **Scope time ranges** — Always specify reasonable time ranges to avoid excessive data retrieval
- **Aggregate across instances** — Use `amgmcp_query_application_insights_trace` when traces span multiple Application Insights instances

## Troubleshooting

| Issue | Solution |
|-------|----------|
| 401/403 errors | Verify the service account token or Entra ID token is valid and not expired |
| Token expired | Grafana service account tokens don't expire; if using Entra ID, refresh the token |
| No data returned | Verify the Grafana instance has the expected data sources configured |
| Data source not found | Use `amgmcp_datasource_list` to check available data sources |
| Metric not found | Use `amgmcp_query_resource_metric_definition` to discover valid metric names |
| KQL query error | Verify table and column names with `amgmcp_kusto_get_metadata` |
| SQL query error | Verify schema with `amgmcp_mssql_get_metadata` |
| Dashboard not found | Try broader search terms; search matches title, tags, and folder names |
| Sovereign cloud | AMG-MCP is only available in Azure Public Cloud — sovereign clouds are not yet supported |
