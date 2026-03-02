# Dynatrace MCP Server Plugin

Connects Azure SRE Agent to Dynatrace's observability platform via the official Dynatrace-hosted MCP server for querying logs, investigating problems, analyzing vulnerabilities, and executing DQL.

## Endpoint

The MCP endpoint is served by your Dynatrace environment:

```
https://{TENANT_NAME}.apps.dynatrace.com/platform-reserved/mcp-gateway/v0.1/servers/dynatrace-mcp/mcp
```

Replace `{TENANT_NAME}` with your Dynatrace tenant name from your environment URL (e.g., `abc12345`).

## Create a Platform Token

1. Go to [My Platform Tokens](https://myaccount.dynatrace.com/platformTokens)
2. Click **+ Create token**
3. Add a token name, expiration date, and the required scopes listed below. For more details, see [Dynatrace platform tokens documentation](https://docs.dynatrace.com/docs/manage/identity-access-management/access-tokens-and-oauth-clients/platform-tokens#my-platform-tokens).
4. Copy the generated token

The `Authorization` header value must use the format `Bearer {your-platform-token}`.

## Required scopes

| Tool | Required Scopes |
|------|----------------|
| **MCP Gateway access** | `mcp-gateway:servers:invoke`, `mcp-gateway:servers:read` |
| **Create DQL query** | `davis-copilot:nl2dql:execute` |
| **Explain DQL query** | `davis-copilot:dql2nl:execute` |
| **Ask Dynatrace** | `davis-copilot:conversations:execute` |
| **Run DQL query** | `storage:buckets:read` |
| **Query problems** | `storage:buckets:read`, `storage:events:read` |
| **Get vulnerabilities** | `storage:security.events:read` |
| **Timeseries forecast** | `davis:analyzers:read`, `davis:analyzers:execute` |

> **Important:** The `mcp-gateway:servers:invoke` and `mcp-gateway:servers:read` scopes are **required** for connectivity to the Dynatrace-hosted MCP server.
