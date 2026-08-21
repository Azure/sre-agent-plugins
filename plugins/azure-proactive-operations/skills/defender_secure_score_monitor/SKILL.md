---
name: defender_secure_score_monitor
description: Monitor and improve Microsoft Defender for Cloud Secure Score. Tracks score trends, identifies quick wins, checks Defender plan coverage, and highlights critical security recommendations. Use when asked about secure score, security posture, or Defender status.
---

# Defender Secure Score Monitor

## Purpose

Track and improve your Microsoft Defender for Cloud Secure Score through focused monitoring, trend analysis, and actionable remediation guidance.

## Procedure

### Step 1: Current Secure Score

```bash
az rest --method get --url "https://management.azure.com/subscriptions/<sub-id>/providers/Microsoft.Security/secureScores/ascScore?api-version=2020-01-01"
```

```bash
az rest --method get --url "https://management.azure.com/subscriptions/<sub-id>/providers/Microsoft.Security/secureScores/ascScore/secureScoreControls?api-version=2020-01-01"
```

Report current score (X/Y points, Z%), top 5 controls with most room for improvement.

### Step 2: Unhealthy assessments

```bash
az rest --method get --url "https://management.azure.com/subscriptions/<sub-id>/providers/Microsoft.Security/assessments?api-version=2021-06-01" \
  --query "value[?properties.status.code=='Unhealthy'].{displayName:properties.displayName, severity:properties.metadata.severity}"
```

Group by severity: 🔴 High, 🟡 Medium, 🟢 Low.

### Step 3: Quick wins

Identify recommendations that affect the most resources, have the highest point value, and can be fixed with a single command or policy (HTTPS-only, secure transfer, enable Defender plans, restrict public access).

### Step 4: Defender plan coverage

```bash
az security pricing list --query "[].{plan:name, tier:pricingTier, subPlan:subPlan}" -o table
```

Check: Servers (P2), App Service, SQL, Storage, Containers, Key Vault, Resource Manager, DNS. Flag disabled plans with estimated score impact.

### Step 5: Attack surface indicators

1. Public management ports (SSH/RDP open to internet)
2. Storage accounts with public blob access
3. SQL servers with public endpoint
4. Key Vaults accessible from public network

### Step 6: Credential hygiene

Check Key Vault secrets and certificates expiring within 30 days, and app registrations with expiring credentials.

## Scoring

| Level | Criteria |
|-------|----------|
| 🔴 Critical | Active exposure, immediate risk |
| 🟠 High | Significant gap, low-effort exploit |
| 🟡 Medium | Gap exists, specific conditions needed |
| 🟢 Low | Best practice deviation, minimal risk |

## Sample output

| Field | Value |
|-------|-------|
| Subscription | contoso-prod-001 |
| Secure Score | 38.50 / 56 (69%) |

| Priority | Action | Points | Effort |
|----------|--------|--------|--------|
| 1 | Enable HTTPS-only on 3 App Services | +4 | 5 min |
| 2 | Enable Defender for Storage | +3 | 2 min |
| 3 | Restrict SQL public access | +3 | 30 min |
| **Total achievable** | | **+10** | **~40 min** |

## References

- Defender for Cloud: https://learn.microsoft.com/en-us/azure/defender-for-cloud/secure-score-security-controls
- Security Recommendations: https://learn.microsoft.com/en-us/azure/defender-for-cloud/recommendations-reference
- Defender Plans: https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction
