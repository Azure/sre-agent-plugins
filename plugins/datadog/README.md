# Datadog MCP Server Plugin

Connects Azure SRE Agent to Datadog's observability platform via the official Datadog MCP server.

## Find your MCP endpoint

Select the endpoint URL that matches your Datadog organization's region from the [Datadog MCP server setup docs](https://docs.datadoghq.com/bits_ai/mcp_server/setup/?tab=other):

| Region | Endpoint URL |
|--------|-------------|
| US1 (default) | `https://mcp.datadoghq.com/api/unstable/mcp-server/mcp` |
| US3 | `https://mcp.us3.datadoghq.com/api/unstable/mcp-server/mcp` |
| US5 | `https://mcp.us5.datadoghq.com/api/unstable/mcp-server/mcp` |
| EU1 | `https://mcp.datadoghq.eu/api/unstable/mcp-server/mcp` |
| AP1 | `https://mcp.ap1.datadoghq.com/api/unstable/mcp-server/mcp` |
| AP2 | `https://mcp.ap2.datadoghq.com/api/unstable/mcp-server/mcp` |

## Create API and Application keys

The connector requires two credentials passed as custom headers: `DD_API_KEY` and `DD_APPLICATION_KEY`.

### API key

1. Log in to your [Datadog organization](https://app.datadoghq.com/) (use your region-specific URL if applicable)
2. Select your **account avatar** > **Organization Settings**
3. In the left sidebar under *Access*, select **API Keys** (direct URL: `https://app.datadoghq.com/organization-settings/api-keys`)
4. Select **+ New Key**, enter a name (e.g., `sre-agent-mcp`), and select **Create Key**
5. Copy the key immediately—it is shown only once

### Application key

1. From the same **Organization Settings** page, select **Application Keys** in the left sidebar (direct URL: `https://app.datadoghq.com/organization-settings/application-keys`)
2. Select **+ New Key**, enter a name (e.g., `sre-agent-mcp-app`), and select **Create Key**
3. Copy the key immediately—it is shown only once

### Add MCP permissions

After creating the Application key, grant it MCP-specific scopes:

1. In the **Application Keys** list, select the key name to open its detail panel
2. In the **Scopes** section, select **Edit**
3. Search for `MCP` and check **MCP Read** (required) and optionally **MCP Write**
4. Select **Save**

> [!TIP]
> For production use, create keys from a **service account** (**Organization Settings** > **Service Accounts**) rather than a personal account. Apply least privilege—grant only `MCP Read` unless write operations are needed.

## Add the connector in Azure portal

1. Navigate to your SRE Agent resource
2. Select **Builder** > **Connectors** > **Add connector**
3. Select **Datadog MCP server** and select **Next**
4. Configure the connector:

   | Field | Value |
   |-------|-------|
   | **Name** | `datadog-mcp` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | Your regional endpoint from the table above |
   | **DD_API_KEY** | Your Datadog API key |
   | **DD_APPLICATION_KEY** | Your Datadog Application key |

5. Select **Next** to review, then **Add connector**

Once the connector shows **Connected** status, the Datadog MCP tools are available to your agent.
