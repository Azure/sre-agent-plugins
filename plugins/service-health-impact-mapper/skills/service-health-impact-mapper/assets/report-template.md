## Mandatory output

Use this exact section order, headings, table columns, classification labels, and field names
for every run. Do not add, remove, rename, or reorder sections. Populate unavailable values
with `Not available`; populate empty result sets with `None found`.

The report has two layers:

- **Customer report:** Sections 1-7 contain concise conclusions, material evidence, resilience
  concerns, limitations, and actions. Keep cells short and use plain language.
- **Evidence appendices:** Appendices A-E retain the complete resource inventory, telemetry
  coverage, dependencies, detailed timeline, and query provenance needed for auditability.

Do not repeat the same explanation in several sections. Put detailed query, metric, and
permission information in the appendices and reference it from the customer report.

### Preferred delivery format

When the runtime can create file artifacts, deliver the report as a self-contained HTML
dashboard and also retain the Markdown report as the machine-readable source. The HTML must
work offline and must not load external scripts, fonts, styles, or telemetry.

The HTML presentation must include:

- a report header with tracking ID, event, region, status, analyzed window, and assessment status;
- summary cards for assessed, priority, potential, corroborated, excluded, and zone-resilient counts;
- a five-question customer interpretation panel;
- a priority-resource panel showing only `Corroborated impact` and
  `High-confidence exposure`;
- a searchable and classification-filterable complete resource table that shows whether each
  resource has direct, indirect, no-data, unavailable, or unqueried monitoring evidence;
- a visible subscription-and-region-specific logical-to-physical zone mapping table, including
  an `Applies to assessed resources` field;
- short evidence and action tables in the main view;
- collapsible evidence appendices for monitoring coverage, dependency paths, detailed
  timeline, provenance, and accepted exceptions;
- print-friendly styling and accessible table headings.

Use restrained status indicators with text labels; never rely on color alone. Do not replace
missing evidence with visual confidence. The HTML and Markdown values must come from the same
final resource rows and reconciled totals.

If HTML artifact creation is unavailable, return the Markdown format below without changing
its section order or fields.

## Azure Service Health Resource Impact Report

| Report detail | Value |
|---------------|-------|
| Tracking ID | `<tracking ID>` |
| Azure event | `<title>` |
| Service / region | `<service> / <region>` |
| Event type / status | `<type> / <status>` |
| Declared impact window (UTC) | `<start> to <end>` |
| Telemetry analyzed (UTC) | `<effective analysis start> to <effective analysis end>` |
| Subscriptions assessed | `<count>` |
| Output mode | `Internal` or `Customer-safe` |
| Report generated (UTC) | `<YYYY-MM-DD HH:MM:SSZ>` |
| Assessment status | `Complete`, `Partial`, or `Unable to assess` |

### 1. Customer summary

Use exactly one row for each question. Answers must be one or two short sentences.

| Customer question | Answer |
|-------------------|--------|
| What did Azure report? | `<event, service, region, and status>` |
| Which resources need attention? | `<counts and names of corroborated/high-confidence resources, or None found>` |
| Was customer impact confirmed? | `<Yes/No/Partially, with a concise evidence statement>` |
| What is the resilience concern? | `<largest zone or dependency risk>` |
| How confident is this assessment? | `<High/Medium/Low and the principal reason>` |

Do not use causal wording unless the source explicitly establishes causality.

### 2. Scope and outcome

| Measure | Result | Customer meaning |
|---------|--------|------------------|
| Azure service and region | `<service> / <region>` | `<plain-language scope>` |
| Direct resources | `<count>` | `<resources directly in Azure event scope>` |
| Dependent resources | `<count>` | `<resources connected to direct resources>` |
| Azure per-resource outcomes | `<Finished/Skipped/Not returned counts>` | `<plain-language interpretation>` |
| Corroborated impact | `<count>` | `<strong symptom evidence>` |
| High-confidence exposure | `<count>` | `<strong exposure evidence>` |
| Potential exposure | `<count>` | `<matched scope but inconclusive evidence>` |
| Not corroborated | `<count>` | `<complete monitoring showed no anomaly>` |
| Unable to assess | `<count>` | `<insufficient access or evidence>` |
| Explicitly excluded | `<count>` | `<considered but proven outside scope>` |

### 3. Resources requiring attention

Include only `Corroborated impact` and `High-confidence exposure` rows. If none, write one
`None found` row. Keep evidence and next steps concise.

| Priority | Resource | Resource group | Assessment | Azure outcome | Customer evidence | Recommended check |
|----------|----------|----------------|------------|---------------|-------------------|-------------------|
| 1..N | ... | ... | Corroborated impact/High-confidence exposure | Finished/Skipped/Not returned/Not applicable | ... | ... |

### 4. Resilience overview

| Resilience result | Resource count | Customer meaning |
|-------------------|----------------|------------------|
| Yes | X | Can tolerate the assessed zone failure based on current configuration |
| Partial | X | Some redundancy exists, but a dependency or configuration gap remains |
| No | X | No evidenced zone-failure resilience |
| Unknown | X | Configuration evidence is insufficient |

Add at most three rows for the most important resilience risks:

| Risk | Affected resources | Why it matters |
|------|--------------------|----------------|
| ... | ... | ... |

Show every zone mapping retrieved for an impacted subscription and region:

| Subscription | Region | Logical zone | Physical zone | Applies to assessed resources |
|--------------|--------|--------------|---------------|-------------------------------|
| ... | ... | ... | ... | Yes/No - `<reason>` |

If no assessed resource has a logical zone, still show the mapping for reference and state
that it does not apply to those resources. Never imply that regional capability means the
resource is zonally deployed.

### 5. Evidence highlights

Include only material evidence that changes a classification or customer action. Limit the
main table to ten rows; put all other evidence in Appendix D.

| Time (UTC) | Resource/scope | Evidence | What it means |
|------------|----------------|----------|---------------|
| ... | ... | ... | ... |

### 6. Limitations

Include only limitations that materially change confidence, classification, or scope. Limit
the main table to seven rows; put operational query detail in Appendices B and E.

| Limitation | Effect on this assessment | How to close the gap |
|------------|---------------------------|----------------------|
| ... | ... | ... |

Always state whether application-to-application and external dependencies were available.

### 7. Recommended actions

Recommendations are advisory only. Never execute them. Limit this table to seven actions.

| Priority | Recommended action | Applies to | Expected result |
|----------|--------------------|------------|-----------------|
| ... | ... | ... | ... |

Prioritize validating customer symptoms, closing evidence gaps that changed classifications,
addressing resilience risks, validating recovery, and submitting Report an Impact separately
when appropriate.

## Appendix A. Complete resource inventory

Include exactly one row for every assessed and explicitly excluded resource. Use full
subscription IDs only in `Internal` mode. Mask subscription IDs and customer-selected
resource identifiers in `Customer-safe` mode. Sort by assessment, subscription, resource
group, type, and resource.

| Assessment | Resource | ARM type / SKU | Subscription | Resource group | Region | Exposure | Azure outcome | Monitor evidence | Customer symptom | Resilience |
|------------|----------|----------------|--------------|----------------|--------|----------|---------------|------------------|------------------|------------|
| ... | ... | ... | ... | ... | ... | Direct/Dependent/Excluded | Finished/Skipped/Not returned/Not applicable | Direct/Indirect/No data/Unavailable/Not queried/Not applicable | ... | Yes/Partial/No/Unknown |

For dependent resources whose resource type was not represented in `impactedResources`, use
Azure outcome `Not applicable`, not `Not enumerated`. Use `Explicitly excluded` only when an
event qualifier or the authoritative child set excludes a matching candidate.

Every `Corroborated impact`, `High-confidence exposure`, and `Potential exposure` row must
have an explicit `Monitor evidence` value. `Direct` means resource-level metric, log,
Application Insights, alert, or Resource Health evidence was successfully queried. `Indirect`
means evidence came only from a dependency. `No data` means a query succeeded but returned no
usable values. `Unavailable` means unsupported, not configured, access denied, or query
failure. `Not queried` is never acceptable in a complete report; if execution limits prevent
the query, set the overall assessment to `Partial`, identify the gap, and continue the
deterministic batches before final delivery whenever the runtime permits.

## Appendix B. Monitoring coverage

### B1. Coverage summary

| Resource | Resource Health | Metrics | Logs | Application Insights | Alerts | Dependency coverage | Evidence quality |
|----------|-----------------|---------|------|----------------------|--------|---------------------|------------------|
| ... | Available/Missing/Unsupported | Available/Missing/Unsupported | Available/Missing/Unsupported | Available/Missing/Unsupported | Available/Missing/Unsupported | Complete/Partial/Unknown | Sufficient/Limited/Insufficient |

### B2. Complete telemetry manifest

Include one row for every discovered platform metric and every configured log category,
workspace table, alert rule, Application Insights signal, and Resource Health source. Never
combine several signals into one row.

| Resource | Source | Signal | Scope/dimension | Aggregation/grain | Status | Data in window | Note |
|----------|--------|--------|-----------------|-------------------|--------|----------------|------|
| ... | Metric/Log/Alert/Application Insights/Resource Health | ... | ... | ... | Queried/No data/Unsupported/Not configured/Access denied/Query failed | Yes/No/Unknown | ... |

## Appendix C. Dependency paths

| Direct resource | Dependent resource | Relationship | Hop | Evidence source | Impact interpretation |
|-----------------|--------------------|--------------|-----|-----------------|-----------------------|
| ... | ... | ... | 1/2 | ... | ... |

## Appendix D. Detailed evidence timeline

Sort ascending by timestamp. Every `Corroborated impact` and `High-confidence exposure`
resource must have at least one row.

| Time (UTC) | Resource | Source | Signal | Observed value | Baseline/threshold | Interpretation |
|------------|----------|--------|--------|----------------|--------------------|----------------|
| ... | ... | ... | ... | ... | ... | ... |

## Appendix E. Evidence provenance and exceptions

### E1. Evidence provenance

| Source | Scope queried | Query window (UTC) | Result | Note |
|--------|---------------|--------------------|--------|------|
| Service Health | ... | ... | Success/Partial/Failed | ... |
| Azure Resource Graph | ... | ... | Success/Partial/Failed | ... |
| Resource Health | ... | ... | Success/Partial/Failed | ... |
| Azure Monitor metrics | ... | ... | Success/Partial/Failed | ... |
| Log Analytics | ... | ... | Success/Partial/Failed | ... |
| Application Insights | ... | ... | Success/Partial/Failed | ... |
| Azure Monitor alerts | ... | ... | Success/Partial/Failed | ... |
| Zone mapping API | ... | ... | Success/Partial/Failed | ... |
| Customer topology | ... | ... | Success/Partial/Not provided | ... |

### E2. Accepted exceptions

| Resource/check | Customer-provided reason | Effect on recommendations |
|----------------|--------------------------|---------------------------|
| ... | ... | ... |

---

*This report is an evidence-based correlation of Azure event scope, resource configuration,
Resource Health, and available telemetry. Correlation does not by itself establish root cause.
Review high-impact findings with the application and platform owners before external use.*
