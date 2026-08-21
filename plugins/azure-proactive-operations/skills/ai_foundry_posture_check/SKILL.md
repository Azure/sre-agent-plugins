---
name: ai_foundry_posture_check
description: Assess security, reliability, and cost posture of Azure OpenAI and AI Foundry deployments. Detects anti-patterns like API keys instead of Managed Identity, public endpoints, disabled content filtering, deprecated models, over-provisioned PTU, missing AI Gateway, and single-region deployment.
---

# AI Foundry & OpenAI Posture Check

## Purpose

Assess the security, reliability, and cost efficiency of Azure OpenAI and AI Foundry deployments. Detects the most common anti-patterns that teams make when building AI-powered products.

## Procedure

### Step 0: Discover AI resources

```bash
az cognitiveservices account list \
  --query "[?kind=='OpenAI' || kind=='AIServices'].{name:name, kind:kind, rg:resourceGroup, location:location}" -o table
```

### Category 1: Security

1. **Managed Identity** (12 pts): Check identity type and `disableLocalAuth`
   ```bash
   az cognitiveservices account show --name <account> --resource-group <rg> \
     --query "{identity:identity.type, disableLocalAuth:properties.disableLocalAuth}" -o json
   ```
2. **Network isolation** (13 pts): Check public access, firewall rules, private endpoints
3. **Content filtering** (10 pts): Verify RAI policies assigned to all deployments
   ```bash
   az cognitiveservices account deployment list --name <account> --resource-group <rg> \
     --query "[].{name:name, model:properties.model.name, raiPolicy:properties.raiPolicyName}" -o table
   ```

### Category 2: Reliability & Operations

1. **Model versions** (7 pts): Check all deployments are on GA versions, not deprecated
   ```bash
   az cognitiveservices account deployment list --name <account> --resource-group <rg> \
     --query "[].{name:name, model:properties.model.name, version:properties.model.version}" -o table
   ```
2. **Diagnostic settings** (7 pts): Verify RequestResponse + Audit logs to Log Analytics
3. **Resource locks** (5 pts): Check CanNotDelete lock on production accounts
4. **Multi-region** (5 pts): Verify accounts in 2+ regions for resilience
5. **429 throttling** (6 pts): Query Log Analytics for throttle rate in last 24h
   ```kusto
   AzureDiagnostics
   | where TimeGenerated > ago(24h) and ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
   | summarize TotalRequests=count(), Throttled=countif(resultSignature_d == 429) by Resource
   ```

### Category 3: Cost & Efficiency

1. **Rate limits** (5 pts): Check deployments have explicit TPM capacity set
2. **Model diversity** (3 pts): Verify mix of models (not premium for everything)
3. **PTU utilization** (5 pts): If PTU exists, check utilization > 60%
4. **Token consumption trend** (5 pts): Query token usage over 7 days for anomalies

### Category 4: Architecture

1. **AI Gateway (APIM)** (7 pts): Check for API Management with OpenAI backend
   ```bash
   az apim list --query "[].{name:name, sku:sku.name}" -o table
   ```
2. **Environment separation** (5 pts): Verify dev/prod use separate OpenAI accounts

## Scoring

| Score | Level |
|-------|-------|
| 0-39 | 🔴 Critical |
| 40-69 | 🟡 Needs work |
| 70-89 | 🟢 Good |
| 90-100 | 🏆 Excellent |

## Sample output

| Field | Value |
|-------|-------|
| Score | 58 / 100 |
| Level | 🟡 Needs work |
| Accounts assessed | 2 (oai-prod-eastus, oai-dev-eastus) |

| Category | Score | Max |
|----------|-------|-----|
| Security | 19 | 35 |
| Reliability | 18 | 30 |
| Cost & Efficiency | 13 | 18 |
| Architecture | 8 | 12 |

| # | Critical finding | Impact |
|---|-----------------|--------|
| 1 | API keys enabled on oai-prod-eastus | 🔴 Key leak = full access |
| 2 | Public endpoint, no firewall | 🔴 Internet-exposed |
| 3 | Single region deployment | 🟡 No failover |

## References

- Azure OpenAI Security: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/managed-identity
- Content Filtering: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/content-filters
- Model Retirements: https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/model-retirements
- BCDR: https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/business-continuity-disaster-recovery
