# PagerDuty MCP Server Plugin

Connects Azure SRE Agent to PagerDuty's incident management platform via the official PagerDuty MCP server.

## Endpoint

Select the endpoint URL that matches your PagerDuty account's service region:

| Region | Endpoint URL |
|--------|-------------|
| US (default) | `https://mcp.pagerduty.com/mcp` |
| EU | `https://mcp.eu.pagerduty.com/mcp` |

## Create a PagerDuty API token

PagerDuty uses a single **User API Token** for both authentication and authorization — the token inherits all permissions of the user account that creates it.

### Navigate to API Access

1. Log in to your [PagerDuty account](https://app.pagerduty.com/) (for EU accounts, use `https://app.eu.pagerduty.com/`)
2. Select your **user avatar** in the top-right corner
3. Select **My Profile**
4. Select the **User Settings** tab
5. Scroll down to the **API Access** section

### Create the token

1. In the **API Access** section, select **Create API User Token**
2. Enter a descriptive name (e.g., `sre-agent-mcp`)
3. Select **Create Token**
4. **Copy the token value immediately** — it is displayed only once and cannot be retrieved later

The token format looks like: `u+xxxxxxxxxxxxxxxx`

> **Important:** Store the API token securely. If you lose it, you must delete the old token and create a new one from **My Profile** > **User Settings** > **API Access**.

The `Authorization` header value must use the format `Token <your-api-token>` (not `Bearer`).

### Choose the right account

| Account type | When to use | Permissions |
|-------------|-------------|-------------|
| **Personal account** | Quick testing and development | Full permissions of your user role |
| **Service account** (recommended for production) | Production deployments | Create a dedicated PagerDuty user with a restricted role |
| **Read-only account** | Monitoring-only use cases | Create a user with the **Observer** or **Restricted Access** role |

> **Tip:** For production, create a dedicated PagerDuty user with a **Responder** or **Observer** role, then generate the token from that account. This ensures the integration survives team member changes and limits blast radius if the token is compromised.
>
> PagerDuty also supports **Account-level API keys** (under **Integrations** > **Developer Tools** > **API Access Keys**), but the MCP server requires a **User API Token**, not an account-level key.
