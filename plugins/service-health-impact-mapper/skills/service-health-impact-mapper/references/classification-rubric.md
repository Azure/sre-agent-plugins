# Resource Classification Rubric

### Step 9: Classify each resource

Assign exactly one classification using these rules:

#### Corroborated impact

Use only when:

- the resource is directly in event scope or has an explicit dependency path from a directly
  scoped resource; and
- at least one strong signal overlaps the event window:
  - Resource Health `Unavailable` or `Degraded`;
  - availability/probe failure;
  - material error, failure, or timeout increase;
  - explicit platform or application log evidence of failed operations.

#### High-confidence exposure

Use when either condition is true:

- the resource is directly in the event scope and has either two independent supporting
  signals or one material signal that is not strong enough to establish
  `Corroborated impact`.

Keep Azure maintenance outcome in a separate field. `Finished` confirms that Azure reports
maintenance execution on the resource, but it does not independently prove customer-visible
impact or satisfy this classification.

#### Potential exposure

Use when:

- the resource matches the event's subscription, service, location, and applicable qualifiers;
  but
- telemetry is absent, incomplete, ambiguous, or shows no material anomaly.

Also use for an explicitly evidenced dependency when there is no resource-level symptom.

Also use when a resource appears in `impactedResources` with `status` beginning `Skipped`.
Skipped lowers likelihood but does not prove absence of impact. It is not a region, zone, SKU,
or configuration qualifier, so it never justifies `Explicitly excluded` or
`Not corroborated` when Resource Health history is unavailable.

For future or nonincident `HealthAdvisory` and `SecurityAdvisory` events, matching resources
default to `Potential exposure` unless independent current evidence supports a stronger
classification. Do not use `Not corroborated` to imply that the advisory is irrelevant merely
because no operational anomaly occurred.

#### Not corroborated

Use only when:

- the resource matches event scope;
- Resource Health history is healthy or unsupported without adverse evidence;
- appropriate telemetry covers the full analysis window;
- no material anomaly or aligned alert is present; and
- monitoring coverage is sufficient to make that statement.

This means `no impact was observed in the available evidence`; it does not mean the resource
was definitely unaffected.

#### Explicitly excluded

Use when a resource was considered but a known event qualifier excludes it, such as another
region, zone, SKU, configuration, resource type, or control/data plane.

Absence from `impactedResources` can justify `Explicitly excluded` only when child retrieval
completed successfully and the event semantics establish that the returned set is exhaustive
for that resource family. Otherwise classify a matching candidate as `Potential exposure`,
record `Not enumerated by Azure`, and preserve the retrieval limitation.

#### Unable to assess

Use when the event, resource, health, configuration, or telemetry data required for a defensible
classification could not be accessed or interpreted.

## Accepted exceptions

If the user provides accepted exceptions, retain the resource or check in the report and mark
it `Accepted exception`. Include the user-provided reason verbatim. An exception suppresses a
recommendation but does not alter observed impact evidence or resilience facts.

## Scoring

This skill does not produce a numeric maturity score. A single score would hide material
differences between confirmed symptoms, platform exposure, monitoring coverage, and zone
resilience. Instead, it produces:

- exactly one impact classification per assessed resource;
- one zone-failure-resilience result per assessed resource;
- an evidence-quality status for each monitoring source;
- an overall assessment status of `Complete`, `Partial`, or `Unable to assess`.

These deterministic outcomes are the report's scoring model. Never average them into a
single percentage.
