---
name: digital_native_governance_check
description: Assess Azure governance maturity for Digital Natives and startups. Detects common anti-patterns like incomplete Service Health alerts, flat subscription topology, excessive RBAC, missing budget alerts, no Managed Identity usage, and unprotected web apps. Use when asked about startup readiness, governance maturity, or production readiness.
---

# Digital Native Governance Check

## Purpose

Evaluate whether a startup's Azure environment has the foundational governance that enterprise customers, investors, and auditors expect. Produces a maturity score (0-100) with clear next steps.

## Maturity levels

| Score | Level |
|-------|-------|
| 0-39 | 🔴 Foundation |
| 40-69 | 🟡 Developing |
| 70-89 | 🟢 Established |
| 90-100 | 🏆 Optimized |

## Procedure

### Category 1: Alerting & Observability (20 pts)

1. **Service Health alerts** (8 pts): Check for alerts covering all 4 event types
   ```bash
   az monitor activity-log alert list --query "[?contains(to_string(condition.allOf), 'ServiceHealth')]" -o json
   ```
2. **Resource Health alerts** (6 pts): Check for VM/resource availability alerts
3. **Action Groups** (6 pts): Verify email + at least one other channel (webhook, SMS)
   ```bash
   az monitor action-group list --query "[].{name:name, emails:length(emailReceivers)}" -o table
   ```

### Category 2: Subscription Topology (20 pts)

1. **Management Groups** (8 pts): Check for custom hierarchy beyond Tenant Root
   ```bash
   az account management-group list -o table
   ```
2. **Environment separation** (8 pts): Verify prod/nonprod in distinct subscriptions
3. **Resource Locks** (4 pts): Check critical infrastructure has CanNotDelete locks

### Category 3: Cost Controls (15 pts)

1. **Budget alerts** (8 pts): Check budgets with 80%/100% notifications
   ```bash
   az rest --method get --url "https://management.azure.com/subscriptions/<sub-id>/providers/Microsoft.Consumption/budgets?api-version=2023-11-01"
   ```
2. **Advisor cost recommendations** (4 pts): Check unaddressed high-impact items
3. **Cost-leaking VMs** (3 pts): Find VMs in stopped-but-allocated state

### Category 4: Identity & Access (20 pts)

1. **Owner count** (6 pts): Flag > 3 Owners per subscription
2. **Managed Identities** (7 pts): Check for MI usage on resources
3. **Defender for Cloud** (7 pts): Verify CSPM + workload protection enabled

### Category 5: Production Web Protection (15 pts)

1. **Custom domains** (5 pts): Check if production apps use custom domains
2. **WAF/Front Door** (5 pts): Check for L7 protection on web endpoints
3. **DDoS protection** (5 pts): Verify DDoS plan on VNets with public IPs

### Category 6: Operational Foundations (10 pts)

1. **Activity Log forwarding** (5 pts): Check diagnostic settings to Log Analytics
2. **Backup** (5 pts): Verify Recovery Services vault with active items

## Sample output

| Field | Value |
|-------|-------|
| Score | 52 / 100 |
| Level | 🟡 Developing |
| Subscription | contoso-prod-001 |

| Category | Score | Max | Status |
|----------|-------|-----|--------|
| Alerting & Observability | 10 | 20 | 🟡 |
| Subscription Topology | 4 | 20 | 🔴 |
| Cost Controls | 12 | 15 | 🟢 |
| Identity & Access | 13 | 20 | 🟡 |
| Web Protection | 7 | 15 | 🟡 |
| Operational Foundations | 7 | 10 | 🟡 |
| **Total** | **52** | **100** | **🟡** |

## References

- Service Health: https://learn.microsoft.com/en-us/azure/service-health/alerts-activity-log-service-notifications-portal
- Management Groups: https://learn.microsoft.com/en-us/azure/governance/management-groups/overview
- RBAC Best Practices: https://learn.microsoft.com/en-us/azure/role-based-access-control/best-practices
- Managed Identities: https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview
- WAF: https://learn.microsoft.com/en-us/azure/web-application-firewall/overview
