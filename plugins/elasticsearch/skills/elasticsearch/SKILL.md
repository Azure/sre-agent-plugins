---
name: elasticsearch
description: Query and analyze Elasticsearch data including logs, indices, mappings, and cluster health using ES|QL
---

# Elasticsearch Observability Skill

You have access to the Elasticsearch MCP server tools for querying and analyzing data from Elasticsearch clusters.

## Available Tools

| Tool | Purpose |
|------|---------|
| **list_indices** | List all available Elasticsearch indices |
| **get_mappings** | Get field mappings for a specific index |
| **search** | Perform searches using Elasticsearch Query DSL |
| **esql** | Execute ES|QL queries for data exploration |
| **get_shards** | Get shard information and cluster health status |

## Workflow

1. **Understand the request** — Determine what Elasticsearch data the user needs
2. **Discover indices** — Use `list_indices` to find relevant data sources
3. **Check mappings** — Use `get_mappings` to understand available fields
4. **Query data** — Use `search` (Query DSL) or `esql` (ES|QL) to retrieve results
5. **Check health** — Use `get_shards` for cluster and index health information
6. **Analyze results** — Summarize findings with actionable recommendations

## Best Practices

- Always start by listing indices to discover available data sources
- Check field mappings before writing queries to ensure correct field names
- Use ES|QL for complex aggregations and data exploration
- Use Query DSL search for precise filtering and full-text search
- Start with smaller timeframes and add LIMIT to optimize query performance
- Prefer safe, bounded queries — include explicit time filters
- Do not fabricate index names or field names; discover them first
