---
name: finops_intelligence
description: Comprehensive FinOps analysis combining cost optimization, waste identification, and chargeback reporting. Use when asked about reducing Azure spend, finding unused resources, cost per team, chargeback, showback, or cost anomalies.
---

# FinOps Intelligence

## Purpose

Unified cost intelligence skill that identifies savings opportunities, tracks cost trends, and generates chargeback/showback reports by team or project.

## Procedure

### Step 1: Cost trend overview

```bash
az costmanagement query --type ActualCost --timeframe MonthToDate \
  --scope "subscriptions/<sub-id>" \
  --dataset-aggregation '{"totalCost":{"name":"Cost","function":"Sum"}}' \
  --dataset-grouping name="ServiceName" type="Dimension" -o table
```

Summarize: total spend this month vs. last month (% change), top 5 services by spend, spending anomalies (day-over-day spikes > 20%).

### Step 2: Orphaned resources (waste)

| Check | Command |
|-------|---------|
| Unattached disks | `az disk list --query "[?managedBy==null]"` |
| Unused public IPs | `az network public-ip list --query "[?ipConfiguration==null]"` |
| Stopped-but-allocated VMs | `az vm list -d --query "[?powerState=='VM deallocated']"` |
| Unused App Service plans | `az appservice plan list --query "[?numberOfSites==0]"` |

### Step 3: Rightsizing

Identify VMs with < 5% avg CPU (14 days), App Service plans with < 20% utilization, databases with < 20% DTU/vCore. Calculate current vs. recommended SKU cost.

### Step 4: Reservation opportunities

List VMs and databases running 24/7 for 30+ days as candidates for Reserved Instances (up to 72% savings).

### Step 5: Storage optimization

```bash
az storage account list --query "[].{name:name, accessTier:accessTier, kind:kind}" -o table
```

Identify blobs not accessed in 90+ days (candidates for Cool/Archive). Check lifecycle policies.

### Step 6: Cost allocation (chargeback)

Group costs by the user's chosen tag (cost-center, owner, team, application). Track untagged resources as "Unallocated." For each team: this period vs. previous, top cost driver, flag anomalies > 30% increase.

## Sample output

| Field | Value |
|-------|-------|
| Subscription | contoso-prod-001 |
| Total Spend (MTD) | $12,340 |
| MoM Change | +$1,700 (+11.5%) |
| Waste Identified | ~$1,179/month |

| Category | Savings/mo | Priority |
|----------|------------|----------|
| Orphaned resources | $52 | High |
| Rightsizing | $315 | High |
| Reservations | $864 | Medium |
| **Total recoverable** | **~$1,179** | |

## References

- Cost Management: https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/overview-cost-management
- Azure Advisor Cost: https://learn.microsoft.com/en-us/azure/advisor/advisor-cost-recommendations
- Reserved Instances: https://learn.microsoft.com/en-us/azure/cost-management-billing/reservations/save-compute-costs-reservations
