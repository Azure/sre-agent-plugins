# Service-to-ARM Resource Mapping

Use this mapping only after the Service Health event scope has been resolved. Treat unmapped or ambiguous services conservatively and never broaden scope by guessing.

### Step 3: Map impacted services to ARM resource types

When Step 1b returns `impactedResources` children, use that list as the highest-precedence
per-resource scope only when retrieval is successful and complete. Use the service-to-type
mapping below to validate child rows and discover qualified candidates. Absence from a child
list is not automatically an exclusion.

Use a deterministic service-to-resource-type mapping. Start with the following approved
mapping and extend it only when the event or ARM provider metadata gives an unambiguous mapping.

Use `kind`, SKU, child-resource type, cloud, and global-service behavior when needed to
disambiguate resources that share a provider type. Resource types and service names evolve;
if the event or current provider metadata conflicts with this table, preserve the conflict,
use the current authoritative metadata when unambiguous, and record the mapping version and
decision in evidence provenance.

| Service Health service | Candidate ARM resource types |
|------------------------|------------------------------|
| Virtual Machines | `microsoft.compute/virtualmachines`, `microsoft.compute/virtualmachinescalesets` |
| Virtual Machine Scale Sets | `microsoft.compute/virtualmachinescalesets` |
| Managed Disks | `microsoft.compute/disks`, `microsoft.compute/snapshots` |
| App Service | `microsoft.web/sites`, `microsoft.web/serverfarms` |
| Azure Functions | `microsoft.web/sites`, `microsoft.web/serverfarms` |
| Azure Kubernetes Service | `microsoft.containerservice/managedclusters` |
| Container Apps | `microsoft.app/containerapps`, `microsoft.app/managedenvironments` |
| Azure Load Balancer | `microsoft.network/loadbalancers` |
| Application Gateway | `microsoft.network/applicationgateways` |
| Azure Front Door | `microsoft.cdn/profiles`, `microsoft.network/frontdoors` |
| Virtual Network | `microsoft.network/virtualnetworks`, `microsoft.network/networkinterfaces` |
| VPN Gateway | `microsoft.network/virtualnetworkgateways`, `microsoft.network/connections` |
| ExpressRoute | `microsoft.network/expressroutecircuits`, `microsoft.network/connections` |
| NAT Gateway | `microsoft.network/natgateways` |
| Azure DNS | `microsoft.network/dnszones`, `microsoft.network/privatednszones` |
| Azure Storage | `microsoft.storage/storageaccounts` |
| Azure SQL Database | `microsoft.sql/servers`, `microsoft.sql/servers/databases` |
| Azure Database for PostgreSQL | `microsoft.dbforpostgresql/flexibleservers`, `microsoft.dbforpostgresql/servers` |
| Azure Database for MySQL | `microsoft.dbformysql/flexibleservers`, `microsoft.dbformysql/servers` |
| Azure Cosmos DB | `microsoft.documentdb/databaseaccounts` |
| Azure Cache for Redis | `microsoft.cache/redis`, `microsoft.cache/redisenterprise` |
| Service Bus | `microsoft.servicebus/namespaces` |
| Event Hubs | `microsoft.eventhub/namespaces` |
| Event Grid | `microsoft.eventgrid/topics`, `microsoft.eventgrid/domains`, `microsoft.eventgrid/systemtopics` |
| Key Vault | `microsoft.keyvault/vaults`, `microsoft.keyvault/managedhsms` |
| API Management | `microsoft.apimanagement/service` |
| Azure Monitor | `microsoft.insights/components`, `microsoft.operationalinsights/workspaces` |

For an unmapped or ambiguous service:

1. Inspect the event details for an explicit provider or resource type.
2. Inspect ARM provider metadata with read-only commands.
3. If still ambiguous, do not invent a mapping. Add the service to `Unmapped services` and set
   affected-resource discovery for that service to `Unable to assess`.
