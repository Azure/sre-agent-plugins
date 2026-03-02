# Atlassian Rovo MCP Server Plugin

Connects Azure SRE Agent to Jira, Confluence, Compass, and Jira Service Management via the official Atlassian Rovo MCP server.

## Endpoint

The Atlassian Rovo MCP server has a single endpoint for all regions:

```
https://mcp.atlassian.com/v1/mcp
```

## Create an API token

API token authentication is recommended for Azure SRE Agent because it enables headless configuration without browser-based OAuth flows.

> **Prerequisite:** Your organization admin must [enable API token authentication](https://support.atlassian.com/security-and-access-policies/docs/control-atlassian-rovo-mcp-server-settings/) in the Rovo MCP server settings at **admin.atlassian.com** > **Security** > **Rovo MCP server**.

1. Go to the [API token creation page with all MCP scopes preselected](https://id.atlassian.com/manage-profile/security/api-tokens?autofillToken&expiryDays=max&appId=mcp&selectedScopes=all)
   - Or navigate manually: [id.atlassian.com](https://id.atlassian.com/) > **profile avatar** > **Manage account** > **Security** > **API tokens**
2. Optionally deselect scopes you don't need (see [scope categories](#scope-categories) below)
3. Copy the generated token immediately — it cannot be viewed again
4. Base64-encode your credentials:

   ```bash
   # Format: email:api_token
   echo -n "your.email@example.com:YOUR_API_TOKEN_HERE" | base64
   ```

   On Windows PowerShell:

   ```powershell
   [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("your.email@example.com:YOUR_API_TOKEN_HERE"))
   ```

   The output is the value you'll use as `Basic <value>` in the `Authorization` header.

## Scope categories

| Category | Scopes |
|----------|--------|
| **Jira** | `read:jira-work`, `write:jira-work`, `read:jira-user` |
| **Confluence** | `read:page:confluence`, `write:page:confluence`, `read:comment:confluence`, `write:comment:confluence`, `read:space:confluence`, `read:hierarchical-content:confluence`, `read:confluence-user`, `search:confluence` |
| **Compass** | `read:component:compass`, `write:component:compass` |
| **JSM** | `read:incident:jira-service-management`, `write:incident:jira-service-management`, `read:ops-alert:jira-service-management`, `write:ops-alert:jira-service-management`, `read:ops-config:jira-service-management`, `read:servicedesk-request` |
| **Platform** | `read:me`, `read:account`, `search:rovo:mcp` |

> **Note:** JSM tools only work with API token authentication, not OAuth 2.1. Some Compass tools may require OAuth 2.1 and are not available with API tokens.
