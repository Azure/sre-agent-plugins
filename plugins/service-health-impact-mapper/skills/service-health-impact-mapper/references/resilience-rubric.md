# Availability-Zone Resilience Rubric

### Step 10: Assess availability zones and resilience

For each resource that is not `Explicitly excluded`, capture its configured logical zone from
the resource's `zones` property or service-specific zone-redundancy configuration.

Retrieve the logical-to-physical mapping independently for every impacted subscription:

```bash
az account list-locations \
  --query "[?availabilityZoneMappings].{name:name,displayName:displayName,availabilityZoneMappings:availabilityZoneMappings}" \
  -o json
```

The `--subscription` flag is not accepted by this command. The returned mapping reflects the
Azure CLI's current default subscription and cannot be selected per subscription within this
command. For a multi-subscription assessment, never reuse one result across subscriptions.
Either switch the default Azure CLI context before each call or use the subscription-scoped
Resource Manager `List Locations` API below. Record the subscription from which each mapping
was obtained in `Data gaps and limitations`.

Equivalent Resource Manager API:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/locations?api-version=2022-12-01
```

Use `availabilityZoneMappings[].logicalZone` and
`availabilityZoneMappings[].physicalZone`. The physical-zone identifier is an opaque Azure
identifier, not a datacenter name or address.

Assign exactly one resilience posture:

| Posture | Rule |
|---------|------|
| Zone-redundant | Service configuration explicitly reports zone redundancy across two or more zones |
| Multi-zonal | Workload instances are explicitly distributed across two or more logical zones |
| Single-zonal | Resource is pinned to exactly one logical zone |
| Availability-set only | VM uses an availability set but not availability zones |
| Regional/nonzonal | Resource is regional and has no configured zone |
| Not zone-capable | Resource type/SKU/region is documented or reported as not supporting zones |
| Not applicable | The resource is global or zone posture is not meaningful |
| Unknown | Configuration or capability evidence is insufficient |

Determine zone-failure resilience separately:

- `Yes`: explicit zone-redundant configuration or evidenced multi-zonal deployment with a
  viable traffic path, state replication or stateless behavior, redundant dependencies,
  failover behavior, and sufficient available capacity across zones.
- `Partial`: multiple zones exist, but a shared single-zone dependency or incomplete failover
  or capacity evidence remains.
- `No`: single-zonal, availability-set only, or regional/nonzonal configuration without
  evidenced zone redundancy.
- `Unknown`: capability, configuration, or dependency evidence is insufficient.

Do not label an availability set as availability-zone resilient. Do not infer resilience from
the fact that a region supports availability zones. Multi-zone placement without evidence of
the complete service path is `Partial` or `Unknown`, not automatically `Yes`.
