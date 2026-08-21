---
name: compliance_governance_audit
description: Audit Azure environment for compliance and governance posture. Checks Azure Policy, RBAC, tagging, resource locks, naming conventions, and regulatory alignment. Use when asked about compliance, governance, audit readiness, or policy violations.
---

# Compliance & Governance Audit

## Purpose

Assess the governance posture of Azure subscriptions by auditing policies, RBAC assignments, tagging standards, resource locks, and regulatory alignment. Produce a compliance scorecard with remediation steps.

## Procedure

### Step 1: Azure Policy compliance

```bash
az policy state summarize --query "value[].{policy:policyDefinitionName, nonCompliant:nonCompliantResources}" -o table
```

Report total policies assigned, % compliant, and top 5 violated policies.

### Step 2: RBAC review

```bash
az role assignment list --all --query "[].{principal:principalName, role:roleDefinitionName, scope:scope}" -o table
```

Flag: > 3 Owners at subscription level, Contributor sprawl, classic admins, guest users with privileged roles, stale assignments (90+ days inactive), overly broad custom roles.

### Step 3: Tagging compliance

Check mandatory tags (environment, owner, cost-center, application):

```bash
az resource list --query "[?tags.environment==null || tags.owner==null].{name:name, type:type, rg:resourceGroup}" -o table
```

Report % of resources with all mandatory tags and top offending resource groups.

### Step 4: Resource locks

```bash
az lock list --query "[].{name:name, level:level, resource:resourceId}" -o table
```

Verify production databases, storage accounts, and networking resources have delete locks.

### Step 5: Naming conventions

Analyze resource naming patterns against Azure naming conventions (rg-, vnet-, vm-, st, kv-). Report % compliance.

### Step 6: Network governance

Check for public endpoints that should be private, NSG flow logs, Network Watcher in all active regions, DDoS protection.

### Step 7: Diagnostic settings

```bash
az monitor diagnostic-settings subscription list --subscription <id> -o table
```

Verify logs flow to a central Log Analytics workspace.

## Scoring

| Rating | Meaning |
|--------|---------|
| 🟢 **Compliant** | Meets standard, no action needed |
| 🟡 **Partial** | Partially implemented, needs improvement |
| 🔴 **Non-compliant** | Missing or misconfigured, action required |

## Sample output

| Field | Value |
|-------|-------|
| Subscription | contoso-prod-001 |
| Assessment Date | 2026-07-15 |
| Overall Score | 61% |

| Area | Status | Score |
|------|--------|-------|
| Azure Policy | 🟡 | 69% |
| RBAC | 🔴 | 33% |
| Tagging | 🟡 | 69% |
| Resource Locks | 🔴 | 25% |
| Naming | 🟢 | 87% |
| Network Gov. | 🟡 | 60% |
| Diagnostics | 🟡 | 57% |

## References

- Azure Policy: https://learn.microsoft.com/en-us/azure/governance/policy/overview
- RBAC Best Practices: https://learn.microsoft.com/en-us/azure/role-based-access-control/best-practices
- Resource Tagging: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources
- Resource Locks: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/lock-resources
- Naming Conventions: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
