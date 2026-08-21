# Azure Proactive Operations Plugin

Proactive operations skills for Azure SRE Agent that run structured assessments using built-in Azure tools. No external MCP server or connector required.

## Skills included

| Skill | Description |
|-------|-------------|
| `well_architected_review` | Well-Architected Framework assessment across all 5 pillars with scored report |
| `compliance_governance_audit` | Policy, RBAC, tagging, resource locks, and regulatory compliance audit |
| `capacity_planning` | Quota utilization, growth projections, and scaling readiness assessment |
| `finops_intelligence` | Cost optimization, waste identification, and chargeback/showback reporting |
| `incident_postmortem_generator` | Blameless postmortem generation from resolved incident context |
| `defender_secure_score_monitor` | Defender for Cloud Secure Score tracking with quick-win improvement plan |
| `digital_native_governance_check` | Governance maturity assessment for startups and Digital Natives |
| `ai_foundry_posture_check` | Security, reliability, and cost posture for Azure OpenAI and AI Foundry |

## Prerequisites

- Azure SRE Agent configured on your subscription
- The identity running SRE Agent needs **Reader** role on the target subscription
- For FinOps skills: **Cost Management Reader** role is recommended
- For Defender skills: **Security Reader** role is recommended

## How it works

These skills are **instruction-based**, not MCP integrations. They guide the SRE Agent to run `az` CLI commands and Kusto queries against your Azure environment, then produce structured reports with scores and remediation guidance.

No external connector, API key, or MCP server is needed. The skills use the agent's built-in `RunAzCliReadCommands` and `execute_kusto_query` tools.

## Installation

Install the plugin from the Azure SRE Agent portal:

1. Navigate to **Azure portal > SRE Agent > Skills**
2. Browse the marketplace and search for "Azure Proactive Operations"
3. Click **Install** and select which skills to enable

## Usage

Once installed, trigger skills by asking the SRE Agent:

- "Run a Well-Architected review on this subscription"
- "Audit our compliance posture"
- "Are we going to hit any quota limits?"
- "How much did each team spend this month?"
- "Write a postmortem for the incident we just resolved"
- "What's our Defender Secure Score?"
- "Are we production-ready?" (Digital Native governance)
- "Check our Azure OpenAI deployment posture"

## Full documentation

For detailed documentation, customization guides, and scheduled task configuration, visit the full repository: [azure-sre-agent-skills](https://github.com/ricmmartins/azure-sre-agent-skills)

## License

MIT
