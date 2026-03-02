---
name: atlassian_rovo
description: Expertise in Atlassian Cloud products including Jira, Confluence, Compass, and Jira Service Management. Use for searching issues with JQL, creating and updating pages, managing service components, investigating ops alerts, and navigating Atlassian workspaces via the Rovo MCP server.
---

## Overview

Atlassian Cloud provides integrated tools for project tracking (Jira),
documentation (Confluence), service catalog management (Compass), and
incident management (Jira Service Management). The Atlassian Rovo MCP
server enables natural language interaction with all four products.

## Searching Jira with JQL

JQL (Jira Query Language) enables precise issue searches. Always use
`searchJiraIssuesUsingJql` for structured queries.

**Common JQL patterns:**

```jql
# Open issues assigned to current user
assignee = currentUser() AND status != Done

# Bugs created in the last 7 days
project = "MYPROJ" AND type = Bug AND created >= -7d

# High-priority issues in active sprints
project = "MYPROJ" AND priority in (High, Highest) AND sprint in openSprints()

# Full-text search
project = "MYPROJ" AND text ~ "payment error"

# Issues updated recently
updated >= -24h ORDER BY updated DESC
```

**JQL operators:** `=`, `!=`, `~` (contains), `in`, `>=`, `<=`, `NOT`, `AND`, `OR`

**JQL functions:** `currentUser()`, `openSprints()`, `startOfDay()`,
`endOfDay()`, `membersOf("group")`

## Searching Confluence with CQL

CQL (Confluence Query Language) searches pages, blog posts, and attachments.
Use `searchConfluenceUsingCql` for structured queries.

**Common CQL patterns:**

```cql
# Search by title
title ~ "Architecture"

# Search in specific space
space = "ENG" AND type = page

# Full-text content search
text ~ "deployment pipeline"

# Recently modified pages
lastModified >= now("-7d") AND type = page

# Pages by label
label = "runbook" AND space = "SRE"
```

**CQL fields:** `title`, `text`, `space`, `type`, `label`, `creator`,
`lastModified`

## Creating Jira Issues

Follow this workflow:
1. `getVisibleJiraProjects` — list available projects
2. `getJiraProjectIssueTypesMetadata` — list issue types for the project
3. `getJiraIssueTypeMetaWithFields` — get required/optional fields
4. `createJiraIssue` — create the issue

Common issue types: Story, Bug, Task, Epic, Sub-task.

## Creating Confluence Pages

Pages support Markdown content:
1. `getConfluenceSpaces` — list available spaces
2. `getPagesInConfluenceSpace` — optionally find a parent page
3. `createConfluencePage` — create the page with space, title, and body

## Working with Compass Components

Component types: SERVICE, LIBRARY, APPLICATION, CAPABILITY,
CLOUD_RESOURCE, DATA_PIPELINE, MACHINE_LEARNING_MODEL, UI_ELEMENT,
WEBSITE, OTHER.

Relationship types: DEPENDS_ON, OTHER.

## Jira Service Management Operations

For incident and alert management:
- `getJsmOpsAlerts` — query alerts by ID, alias, or search
- `updateJsmOpsAlert` — acknowledge, close, or escalate alerts
- `getJsmOpsScheduleInfo` — view on-call schedules and responders
- `getJsmOpsTeamInfo` — list teams with escalation policies

## Cross-Product Workflows

- Use `search` (Rovo Search) for natural language queries across products
- Use `fetch` with ARIs (Atlassian Resource Identifiers) for direct content retrieval
- Use `getAccessibleAtlassianResources` to list cloud sites and get cloudIds

## Troubleshooting

| Issue | Solution |
|-------|----------|
| JQL syntax error | Check field names; quote values with spaces |
| CQL returns no results | Verify space key; try broader terms |
| Cannot create issue | Verify "Create" permission in the project |
| Cannot edit page | Verify "Edit" permission in the space |
| OAuth expired | Re-invoke any tool to trigger fresh OAuth flow |
| "Site admin must authorize" | Admin must complete initial 3LO consent |
| cloudId errors | Use `getAccessibleAtlassianResources` to find correct cloudId |
