---
name: capacity_planning
description: Assess Azure resource capacity, quota utilization, and growth trends to prevent outages from resource exhaustion. Use when asked about capacity, quotas, scaling readiness, load testing prep, or growth projections.
---

# Capacity Planning

## Purpose

Analyze current resource utilization, quota consumption, and growth trends to predict capacity risks and recommend scaling actions before limits are hit.

## Procedure

### Step 1: Quota utilization

```bash
az vm list-usage --location <region> -o table
az network list-usages --location <region> -o table
```

Check vCPU quotas (total and per-family), public IPs, load balancers, network interfaces, storage accounts per subscription, App Service plans per region, and AKS clusters. Flag resources > 70% utilized.

### Step 2: Compute capacity

1. **VM utilization** (last 14 days): average and P95 CPU/memory. Flag VMs > 80%.
2. **App Service plans**:
   ```bash
   az appservice plan list --query "[].{name:name, sku:sku.name, workers:numberOfWorkers}" -o table
   ```
3. **AKS clusters**:
   ```bash
   az aks list --query "[].{name:name, nodeCount:agentPoolProfiles[0].count, maxCount:agentPoolProfiles[0].maxCount}" -o table
   ```

### Step 3: Data layer capacity

Check SQL Database DTU/vCore utilization, Cosmos DB RU consumption and 429 rates, Redis Cache memory and server load, storage account transaction rates.

### Step 4: Networking capacity

Check ExpressRoute/VPN Gateway bandwidth, Application Gateway/Front Door connections, NAT Gateway SNAT utilization.

### Step 5: Growth projection

Apply linear projection to estimate when each resource hits 80% and 100%. If the user specified a growth factor, multiply current usage and check against limits.

### Step 6: Quota increase requests

```bash
az rest --method patch \
  --url "https://management.azure.com/subscriptions/<sub-id>/providers/Microsoft.Capacity/resourceProviders/Microsoft.Compute/locations/<region>/serviceLimits/<family>?api-version=2020-10-25" \
  --body '{"properties":{"limit":{"limitObjectType":"LimitValue","value":<new-limit>}}}'
```

## Scoring

| Risk | Utilization |
|------|-------------|
| 🟢 Low | < 70% |
| 🟡 Medium | 70-90% |
| 🔴 Critical | > 90% |

## Sample output

| Field | Value |
|-------|-------|
| Subscription | contoso-prod-001 |
| Region(s) | East US, West Europe |
| Resources scanned | 47 |
| At risk (>70%) | 5 |
| Critical (>90%) | 2 |

| Resource | Current | At 2x Load | Action |
|----------|---------|------------|--------|
| vCPUs East US | 85/100 (85%) | 170 ❌ | Quota increase |
| SQL DTU | 78% avg | 156% ❌ | Scale up tier |
| AKS nodes | 7/10 | 14 ❌ | Increase max |

## References

- Quotas Overview: https://learn.microsoft.com/en-us/azure/quotas/quotas-overview
- VM Sizes: https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/overview
- Autoscale: https://learn.microsoft.com/en-us/azure/azure-monitor/autoscale/autoscale-overview
