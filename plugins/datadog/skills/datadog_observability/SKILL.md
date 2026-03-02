---
name: datadog_observability
description: Expertise in Datadog's observability platform including logs, metrics, APM, monitors, incidents, dashboards, hosts, and services. Use for searching logs, querying metrics, investigating incidents, analyzing traces, inspecting monitors, and navigating Datadog data via the Datadog MCP server.
---

## Searching Logs

Use `search_datadog_logs` for individual log retrieval and `analyze_datadog_logs`
for SQL-based aggregation queries.

**Common log search patterns:**

```
# Errors from a specific service
service:payment-api status:error

# Logs from a host in the last hour
host:web-prod-01

# Logs containing a specific trace ID
trace_id:abc123def456

# Errors with a specific HTTP status
@http.status_code:500 service:api-gateway

# Logs from a Kubernetes pod
kube_namespace:production kube_deployment:checkout-service
```

**SQL-based log analysis with `analyze_datadog_logs`:**

```sql
-- Count errors by service in the last hour
SELECT service, count(*) as error_count
FROM logs
WHERE status = 'error'
GROUP BY service
ORDER BY error_count DESC

-- Average response time by endpoint
SELECT @http.url_details.path, avg(@duration) as avg_duration
FROM logs
WHERE service = 'api-gateway'
GROUP BY @http.url_details.path
```

## Querying Metrics

Use `search_datadog_metrics` to discover metrics, `get_datadog_metric_context`
for metadata, and `get_datadog_metric` for time series data.

**Common metric patterns:**

```
# System metrics
system.cpu.user, system.mem.used, system.disk.used

# Container metrics
docker.cpu.usage, kubernetes.cpu.requests

# Application metrics
trace.servlet.request.hits, trace.servlet.request.duration

# Custom metrics
app.payment.processed, app.queue.depth
```

Always specify a time range when querying metrics to avoid retrieving
excessive data.

## Investigating Traces

Use `get_datadog_trace` for complete trace details and `search_datadog_spans`
for span-level queries.

**Trace investigation workflow:**
1. Search for slow or errored spans with `search_datadog_spans`
2. Get the full trace with `get_datadog_trace` using the trace ID
3. Identify the bottleneck service or operation
4. Correlate with `search_datadog_logs` using the trace ID
5. Check related metrics with `get_datadog_metric`

## Working with Monitors

Use `search_datadog_monitors` to find monitors by name, tag, or status.

**Common monitor queries:**

```
# Find all triggered monitors
Search for monitors with status "Alert"

# Find monitors for a specific service
Search for monitors tagged with service:payment-api

# Find monitors by name
Search for monitors matching "CPU" or "memory"
```

## Incident Investigation Workflow

For structured incident investigation:
1. `search_datadog_incidents` — find recent or active incidents
2. `get_datadog_incident` — get full incident details and timeline
3. `search_datadog_monitors` — check which monitors triggered
4. `search_datadog_logs` — search for errors around the incident time
5. `get_datadog_metric` — check key metrics for anomalies
6. `get_datadog_trace` — inspect request traces for latency or errors
7. `search_datadog_hosts` — verify infrastructure health
8. `search_datadog_service_dependencies` — map affected services

## Working with Dashboards and Notebooks

- Use `search_datadog_dashboards` to find dashboards by title or tag
- Use `search_datadog_notebooks` and `get_datadog_notebook` for investigation
  notebooks that document past analyses

## Troubleshooting

| Issue | Solution |
|-------|----------|
| 401/403 errors | Verify API key and Application key are correct and active |
| No data returned | Check that Application key has `MCP Read` permission |
| Wrong region | Ensure the connector URL matches your Datadog organization's region |
| Truncated traces | Large traces may be truncated; this is a known limitation |
| Tool not found | The tool may require a non-default toolset; update the connector URL |
| Write operations fail | Verify Application key has `MCP Write` permission |
