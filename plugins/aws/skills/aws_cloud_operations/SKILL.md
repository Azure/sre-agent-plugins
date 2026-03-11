---
name: aws_cloud_operations
description: Expertise in AWS cloud operations including infrastructure management, troubleshooting, documentation search, API execution, and operational procedures. Use for querying AWS resources, investigating incidents, following SOPs, searching documentation, checking service availability, and executing AWS API calls via the AWS MCP Server.
---

# AWS Cloud Operations Skill

You have access to the AWS MCP Server tools for managing, querying, and troubleshooting AWS infrastructure and services.

## Available Tools

| Tool | Purpose |
|------|---------|
| **aws___retrieve_agent_sop** | Search and retrieve Agent SOPs (Standard Operating Procedures) for guided multi-step AWS tasks |
| **aws___search_documentation** | Search across all AWS documentation, API references, and best practices |
| **aws___read_documentation** | Fetch a specific AWS documentation page as markdown with headings and code blocks |
| **aws___recommend** | Get content recommendations for related AWS documentation pages |
| **aws___list_regions** | List all AWS regions and their identifiers |
| **aws___get_regional_availability** | Check which AWS services and features are available in a specific region |
| **aws___call_aws** | Execute authenticated AWS API calls with SigV4 signing and automatic validation |
| **aws___suggest_aws_commands** | Get syntax help and parameter details for AWS API operations |

## Workflow

### General Investigation

1. **Understand the request** — Determine which AWS services and resources are involved
2. **Search documentation** — Use `aws___search_documentation` to find relevant guides and API references
3. **Check SOPs** — Use `aws___retrieve_agent_sop` to find pre-built procedures for the task
4. **Execute actions** — Use `aws___call_aws` to query or modify AWS resources
5. **Analyze results** — Summarize findings with actionable recommendations

### Incident Troubleshooting

1. **Identify the scope** — Determine affected services, regions, and resources
2. **Check SOPs** — Search for relevant troubleshooting SOPs with `aws___retrieve_agent_sop`
3. **Gather data** — Use `aws___call_aws` to query CloudWatch metrics, CloudTrail events, and resource status
4. **Search documentation** — Use `aws___search_documentation` for error codes, known issues, and resolution steps
5. **Check regional availability** — Use `aws___get_regional_availability` to verify service health in the affected region
6. **Execute remediation** — Follow the SOP or apply fixes with `aws___call_aws`
7. **Verify recovery** — Confirm metrics and resource status return to normal

### Infrastructure Provisioning

1. **Find the SOP** — Use `aws___retrieve_agent_sop` (e.g., "set up production VPC", "deploy serverless app")
2. **Review documentation** — Use `aws___read_documentation` for detailed configuration options
3. **Check availability** — Use `aws___get_regional_availability` to verify the target region supports required services
4. **Follow SOP steps** — Execute each step with `aws___call_aws`, validating results between steps
5. **Verify deployment** — Confirm all resources are healthy and accessible

## Agent SOPs

Agent SOPs are pre-built, step-by-step procedures that follow AWS Well-Architected best practices. Always check for an applicable SOP before manually constructing a workflow.

**Common SOP categories:**
- VPC and networking setup
- Serverless application deployment
- Monitoring and alerting configuration
- Database provisioning (RDS, DynamoDB)
- Cost optimization and billing alerts
- Security and compliance audits
- CI/CD pipeline setup

**To use SOPs:**
1. Call `aws___retrieve_agent_sop` without arguments to list all available SOPs
2. Call `aws___retrieve_agent_sop` with a specific SOP name for step-by-step instructions
3. Follow each step sequentially, using `aws___call_aws` to execute the actions

## Common AWS API Patterns

### EC2 Operations

```
# Describe instances in a region
aws___call_aws: ec2 DescribeInstances

# Check instance status
aws___call_aws: ec2 DescribeInstanceStatus --InstanceIds i-1234567890abcdef0

# Get CloudWatch metrics for an instance
aws___call_aws: cloudwatch GetMetricData --MetricDataQueries [...]
```

### CloudWatch Log Investigation

```
# Search log groups
aws___call_aws: logs DescribeLogGroups --logGroupNamePrefix /aws/lambda/

# Query logs with CloudWatch Logs Insights
aws___call_aws: logs StartQuery --logGroupName /aws/lambda/my-function --queryString "fields @timestamp, @message | filter @message like /ERROR/"
```

### CloudTrail Event Analysis

```
# Look up recent events
aws___call_aws: cloudtrail LookupEvents --LookupAttributes [{AttributeKey:EventName,AttributeValue:StopInstances}]

# Check for unauthorized API calls
aws___call_aws: cloudtrail LookupEvents --LookupAttributes [{AttributeKey:EventName,AttributeValue:ConsoleLogin}]
```

### S3 Operations

```
# List buckets
aws___call_aws: s3api ListBuckets

# Check bucket policy
aws___call_aws: s3api GetBucketPolicy --Bucket my-bucket

# Get object metadata
aws___call_aws: s3api HeadObject --Bucket my-bucket --Key my-key
```

## Best Practices

- **Start with SOPs** — Always check for an applicable Agent SOP before building a manual workflow
- **Use documentation search** — Verify API parameters and service limits before executing calls
- **Specify regions explicitly** — AWS resources are regional; always confirm the target region
- **Use read-only first** — Query resource state before making changes
- **Validate incrementally** — For multi-step procedures, verify each step's output before proceeding
- **Check permissions** — If an API call fails with 403, verify the IAM policy grants the required action
- **Use `aws___suggest_aws_commands`** — Get correct API syntax before calling unfamiliar operations

## Troubleshooting

| Issue | Solution |
|-------|----------|
| 401/403 errors | Verify IAM credentials are correct and have `aws-mcp:InvokeMcp` permission |
| Access Denied on API call | Add the specific service permission (e.g., `ec2:DescribeInstances`) to the IAM policy |
| Resource not found | Verify the correct region; resources are region-specific |
| Throttling errors | Reduce request frequency; use pagination for large result sets |
| SOP not found | Try broader search terms; list all SOPs first to see what's available |
| Documentation search returns nothing | Use simpler search terms; try service name only |
| API parameter errors | Use `aws___suggest_aws_commands` to verify correct parameter names and formats |
| Timeout on API call | Some operations (e.g., CloudFormation stack creation) are async; poll for status instead |
