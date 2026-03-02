# Elasticsearch MCP Server Plugin

Connects Azure SRE Agent to Elasticsearch clusters via Elastic's Agent Builder MCP endpoint for querying logs, analyzing metrics, and checking cluster health.

## Endpoint

The MCP endpoint is served by your Kibana instance:

```
https://{KIBANA_URL}/api/agent_builder/mcp
```

Replace `{KIBANA_URL}` with your Kibana host (e.g., `my-deployment.kb.us-east-1.aws.elastic.cloud`). Requires Elastic 9.2.0+ or Elasticsearch Serverless with Agent Builder enabled.

## Create an API key

1. Log in to Elastic Cloud or your self-hosted Kibana at `https://{your-kibana-url}`
2. Navigate to **Management** > **API Keys**
3. Click **Create API key**
4. Provide a name (e.g., `azure-sre-agent-mcp`)
5. Set appropriate permissions (at minimum, read access to indices you want to query)
6. Click **Create API key** and copy the encoded API key
7. Note your Kibana URL (e.g., `https://my-deployment.kb.us-east-1.aws.elastic.cloud`)

The `Authorization` header value must use the format `ApiKey {your-api-key}`.
