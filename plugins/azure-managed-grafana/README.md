# Azure Managed Grafana MCP Server Plugin

Connects Azure SRE Agent to [Azure Managed Grafana](https://azure.microsoft.com/products/managed-grafana/) via the built-in AMG-MCP remote endpoint for querying dashboards, Azure Monitor metrics, Application Insights, Azure Data Explorer, SQL Server, and Azure Resource Graph.

## Endpoint

Every Azure Managed Grafana instance includes a built-in remote MCP endpoint. The URL follows this pattern:

```
https://<grafana-endpoint>/api/azure-mcp
```

For example: `https://my-grafana-d5ggtqegcr2safcp.wcus.grafana.azure.com/api/azure-mcp`

To find your Grafana endpoint:

1. Open the [Azure portal](https://portal.azure.com)
2. Navigate to your **Azure Managed Grafana** resource
3. Copy the **Endpoint** URL from the **Overview** page
4. Append `/api/azure-mcp` to form the MCP endpoint

> [!NOTE]
> AMG-MCP is available in Azure Public Cloud. Sovereign clouds are not currently supported.

## Authentication

AMG-MCP supports two authentication methods. Both use the `Authorization: Bearer <token>` header format.

### Option 1 — Grafana service account token (recommended for Azure SRE Agent)

A Grafana service account token (format: `glsa_xxx`) provides persistent, non-expiring access scoped to the Grafana instance.

1. Open your Azure Managed Grafana instance (click the **Endpoint** URL in Azure portal)
2. Navigate to **Administration** > **Service accounts**
3. Select **Add service account**
4. Enter a name (e.g., `sre-agent-mcp`) and select a role:
   - **Viewer** — read-only access to dashboards and data sources (recommended for monitoring)
   - **Editor** — read/write access to dashboards
   - **Admin** — full administrative access
5. Select **Create**
6. On the service account page, select **Add service account token**
7. Enter a token name (e.g., `sre-agent-connector`) and select **Generate token**
8. **Copy the token immediately** — it is shown only once

The token format is: `glsa_xxxxxxxxxxxxxxxxxxxxxxxx_xxxxxxx`

> [!TIP]
> For production use, create a service account with the **Viewer** role unless the agent needs to modify dashboards. This follows the principle of least privilege.

### Option 2 — Entra ID token

Use an Azure Entra ID (Azure AD) token for authentication via managed identities or service principals.

**Required token audience:** `ce34e7e5-485f-4d76-964f-b3d2b16d1e4f`

To obtain a token using Azure CLI:

```bash
az account get-access-token --resource ce34e7e5-485f-4d76-964f-b3d2b16d1e4f --query accessToken -o tsv
```

To obtain a token using PowerShell:

```powershell
(Get-AzAccessToken -ResourceUrl "ce34e7e5-485f-4d76-964f-b3d2b16d1e4f").Token
```

> [!IMPORTANT]
> Entra ID tokens are short-lived (typically 1 hour). For persistent connectivity, use a Grafana service account token instead, or configure automatic token refresh in your connector setup.

The Entra ID principal must have an appropriate Grafana role assignment on the Azure Managed Grafana resource:

| Azure Role | Grafana Permission |
|-----------|-------------------|
| **Grafana Viewer** | Read-only access to dashboards and data |
| **Grafana Editor** | Read/write access to dashboards |
| **Grafana Admin** | Full administrative access |

## Add the connector in Azure portal

1. Navigate to your SRE Agent resource
2. Select **Builder** > **Connectors** > **Add connector**
3. Select **Azure Managed Grafana MCP server** and select **Next**
4. Configure the connector:

   | Field | Value |
   |-------|-------|
   | **Name** | `azure-managed-grafana-mcp` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | `https://<grafana-endpoint>/api/azure-mcp` |
   | **Authorization** | `Bearer <your-token>` (service account token or Entra ID token) |

5. Select **Next** to review, then **Add connector**

Once the connector shows **Connected** status, the AMG-MCP tools are available to your agent.

## Available capabilities

Once connected, your SRE Agent can:

- **Query Azure Monitor metrics** — Access resource metrics and metric definitions through Grafana's Azure Monitor data source
- **Search Application Insights** — Analyze failures, traces, and GenAI agent telemetry
- **Query Azure Data Explorer** — Run KQL queries against connected Kusto clusters
- **Query SQL Server** — Execute queries against connected Microsoft SQL Server data sources
- **Search Azure Resource Graph** — Query resource inventory and configuration across subscriptions
- **Browse dashboards** — Search and discover Grafana dashboards by name, tag, or folder
- **List data sources** — View all configured Grafana data sources and their connection details

## Links

- AMG-MCP Documentation: https://aka.ms/amg-mcp
- Report issues: https://aka.ms/managed-grafana/issues
