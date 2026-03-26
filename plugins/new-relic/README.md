# New Relic MCP Server Plugin

Bridges Azure SRE Agent with New Relic's full-stack observability platform through the official New Relic remote MCP server, giving your agent access to entities, NRQL analytics, alert management, deployment tracking, and AI-powered performance insights.

## Identify your regional endpoint

New Relic operates separate MCP endpoints per region. Select the one matching your New Relic account:

| Region | MCP Endpoint |
|--------|-------------|
| US | `https://mcp.newrelic.com/mcp/` |
| EU | `https://mcp.eu.newrelic.com/mcp/` |

> [!NOTE]
> Check your account's region by looking at the URL you use to sign in — `one.newrelic.com` = US, `one.eu.newrelic.com` = EU.

## Generate a User API key

The connector authenticates with a New Relic **User API key** passed via the `Api-Key` header.

1. Sign in to [New Relic](https://one.newrelic.com/) (use `one.eu.newrelic.com` for EU accounts)
2. Click your **user avatar** (bottom-left) and select **API keys** (direct URL: `https://one.newrelic.com/api-keys`)
3. Click **Create a key**, set **Key type** to **User**, give it a name (e.g., `sre-agent-mcp`), and click **Create a key**
4. Copy the key — it follows the format `NRAK-XXXXXXXXXXXXXXXXXXXXX`

### Permissions and access

The MCP server respects New Relic's Role-Based Access Control (RBAC). The tools available to your agent are limited to the data, entities, and accounts the API key owner can access.

To use the MCP server, the key owner must belong to a group assigned one of these roles:

- **Organization Read Only**, **Organization Manager**, or **Organization Product Admin** (default roles)
- A custom organization-scoped role with MCP server read permission

> [!TIP]
> For production environments, create a dedicated service user with least-privilege access. Only grant permissions for the accounts and data the agent actually needs.

## Enable the MCP server preview

New Relic's MCP server is currently in public preview. You must opt in before connecting:

1. In the New Relic UI, click your username (bottom-left)
2. Go to **Administration** > **Previews & Trials**
3. Enable **New Relic AI MCP Server**

## Add the connector in Azure portal

1. Navigate to your SRE Agent resource
2. Select **Builder** > **Connectors** > **Add connector**
3. Select **New Relic MCP server** and select **Next**
4. Configure the connector:

   | Field | Value |
   |-------|-------|
   | **Name** | `new-relic-mcp` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | Your regional endpoint from the table above |
   | **Api-Key** | Your New Relic User API key (`NRAK-…`) |

5. Select **Next** to review, then **Add connector**

Once the connector shows **Connected** status, the New Relic MCP tools are available to your agent.

## Filtering available tools

New Relic's MCP tools are organized by tags. You can restrict which tools your agent sees by adding an `include-tags` header to the connector configuration:

| Tag | Scope |
|-----|-------|
| `discovery` | Entities, dashboards, accounts, entity types |
| `data-access` | NRQL queries, natural language queries |
| `alerting` | Alert policies, conditions, incidents, synthetic monitors |
| `incident-response` | Deployment impact, alert insights, error groups, change events |
| `performance-analytics` | Logs analysis, golden metrics, transactions, threads, Kafka metrics |
| `advanced-analysis` | Combines incident-response and performance-analytics capabilities |

To use tag filtering, add a custom header when configuring the connector:

| Header | Example Value |
|--------|--------------|
| **include-tags** | `discovery,alerting,data-access` |

Omit this header to receive all available tools.
