---
name: dynatrace_observability
description: Query Dynatrace observability data including logs, metrics, problems, and vulnerabilities using DQL
---

# Dynatrace Observability Skill

You have access to the Dynatrace MCP server tools for querying observability data from Dynatrace environments.

## Available Tools

| Tool | Purpose |
|------|---------|
| **Create DQL query** | Generate DQL queries from natural language descriptions |
| **Explain DQL query** | Get plain English explanations of existing DQL queries |
| **Ask Dynatrace** | Ask general questions about Dynatrace workflows, alerts, and configuration |
| **Run DQL query** | Execute DQL queries and return results (up to 1000 records) |
| **Query problems** | List active or closed Davis Problems with filtering |
| **Get problem by ID** | Get detailed problem information including root cause |
| **Get vulnerabilities** | List open security vulnerabilities by risk level |
| **Get K8s cluster events** | Get Kubernetes events for cluster troubleshooting |
| **Timeseries forecast** | Predict future metric values using statistical models |

## Workflow

1. **Understand the request** — Determine what observability data the user needs
2. **Use the right tool** — Select the appropriate Dynatrace tool for the task
3. **Query with DQL** — Use "Create DQL query" for natural language to DQL conversion, then "Run DQL query" to execute
4. **Investigate problems** — Use "Query problems" to list issues, then "Get problem by ID" for details
5. **Analyze results** — Summarize findings with actionable recommendations

## Best Practices

- Start with smaller timeframes (last 1h or 12h) to optimize query performance
- Use "Create DQL query" to generate queries from natural language before running them
- When investigating problems, correlate with related logs and events using DQL
- For security reviews, check vulnerabilities by risk level (CRITICAL, HIGH, MEDIUM, LOW)
- Always explain the data returned and suggest next steps
