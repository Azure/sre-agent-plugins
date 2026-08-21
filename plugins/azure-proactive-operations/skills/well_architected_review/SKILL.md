---
name: well_architected_review
description: Run a Well-Architected Framework (WAF) review against Azure resources in scope. Use when asked about best practices, architecture review, WAF assessment, or pillar compliance (Reliability, Security, Cost, Operational Excellence, Performance Efficiency).
---

# Well-Architected Review

## Purpose

Perform a structured assessment of Azure resources against the five pillars of the Microsoft Azure Well-Architected Framework. Produce a scored report with prioritized recommendations.

## Procedure

### 1. Reliability

1. **Availability design**: Check if critical workloads use availability zones or availability sets
   ```bash
   az vm list --query "[].{name:name, zones:zones, availabilitySet:availabilitySet.id}" -o table
   az appservice plan list --query "[].{name:name, zoneRedundant:zoneRedundant, sku:sku.name}" -o table
   ```
2. **Backup coverage**: Verify Recovery Services vaults and backup policies exist
   ```bash
   az backup vault list -o table
   ```
3. **Disaster recovery**: Check for paired regions, ASR replication, or geo-redundant storage
4. **Health probes**: Verify App Service health checks, load balancer probes, and Container Apps health endpoints
5. **Auto-healing**: Check if App Service auto-heal rules or AKS pod disruption budgets are configured

### 2. Security

1. **Identity**: Check for managed identities vs. stored credentials
   ```bash
   az webapp identity show --name <app> --resource-group <rg>
   ```
2. **Network isolation**: Check for private endpoints, NSGs, and service endpoints
   ```bash
   az network private-endpoint list -o table
   az network nsg list -o table
   ```
3. **Encryption**: Verify encryption at rest and in transit
4. **Key management**: Check Key Vault usage and secret expiration
   ```bash
   az keyvault list -o table
   ```
5. **Defender for Cloud**: Check Secure Score and outstanding recommendations

### 3. Cost Optimization

1. **Rightsizing**: Identify underutilized VMs (CPU < 5% average over 14 days)
2. **Orphaned resources**: Find unattached disks, unused public IPs, empty resource groups
   ```bash
   az disk list --query "[?managedBy==null].{name:name, size:diskSizeGb, rg:resourceGroup}" -o table
   az network public-ip list --query "[?ipConfiguration==null].{name:name, rg:resourceGroup}" -o table
   ```
3. **Reservations**: Check if high-usage resources could benefit from reserved instances
4. **Dev/Test pricing**: Verify non-production workloads use Dev/Test subscriptions or B-series VMs
5. **Storage tiers**: Check if cool/archive tiers are used for infrequently accessed data

### 4. Operational Excellence

1. **Tagging**: Verify mandatory tags (environment, owner, cost-center) exist
   ```bash
   az resource list --query "[?tags.environment==null].{name:name, type:type, rg:resourceGroup}" -o table
   ```
2. **Monitoring**: Check for alert rules, action groups, and diagnostic settings
   ```bash
   az monitor metrics alert list -o table
   ```
3. **Deployment practices**: Check for deployment slots, blue-green, or canary configurations
4. **Automation**: Check for runbooks, Logic Apps, or scheduled tasks

### 5. Performance Efficiency

1. **Autoscaling**: Verify autoscale rules exist for App Service plans, VMSS, and Container Apps
   ```bash
   az monitor autoscale list --resource-group <rg> -o table
   ```
2. **Caching**: Check for Redis Cache or CDN usage on high-traffic workloads
3. **Database performance**: Check DTU/vCore utilization, index recommendations
4. **Content delivery**: Verify static assets use CDN or Front Door

## Scoring

For each check, assign one of:
- ✅ **Pass**: follows best practice
- ⚠️ **Needs attention**: partially implemented or at risk
- ❌ **Fail**: not implemented, risk exposure

Calculate per-pillar score as `pass / (pass + attention + fail)` percentage. Overall score is the average across all five pillars.

## Sample output

| Field | Value |
|-------|-------|
| Subscription | contoso-prod-001 |
| Assessment Date | 2026-07-15 |
| Overall Score | 68% |

| Pillar | Pass | Needs Attention | Fail | Score |
|--------|------|----------------|------|-------|
| Reliability | 3 | 1 | 1 | 60% |
| Security | 2 | 2 | 1 | 40% |
| Cost Optimization | 4 | 1 | 0 | 80% |
| Operational Excellence | 3 | 1 | 1 | 60% |
| Performance Efficiency | 4 | 0 | 1 | 80% |
| **Overall** | **16** | **5** | **4** | **68%** |

## References

- WAF Overview: https://learn.microsoft.com/en-us/azure/well-architected/
- Reliability: https://learn.microsoft.com/en-us/azure/well-architected/reliability/
- Security: https://learn.microsoft.com/en-us/azure/well-architected/security/
- Cost Optimization: https://learn.microsoft.com/en-us/azure/well-architected/cost-optimization/
- Operational Excellence: https://learn.microsoft.com/en-us/azure/well-architected/operational-excellence/
- Performance Efficiency: https://learn.microsoft.com/en-us/azure/well-architected/performance-efficiency/
