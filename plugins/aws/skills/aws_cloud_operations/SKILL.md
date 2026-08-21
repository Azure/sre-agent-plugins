---
name: aws_cloud_operations
description: Expertise in AWS cloud operations including infrastructure management, troubleshooting, documentation search, API execution, operational procedures, and AI-powered incident investigation via the AWS DevOps Agent. Use for querying AWS resources, investigating incidents, following SOPs, searching documentation, checking service availability, executing AWS API calls via the AWS MCP Server, and running automated root cause analysis with the AWS DevOps Agent.
---

# AWS Cloud Operations Skill

You have access to the AWS MCP Server tools for managing, querying, and troubleshooting AWS infrastructure and services, plus the AWS DevOps Agent tools for AI-powered incident investigation, root cause analysis, and interactive operational chat.

## Available Tools

### AWS MCP Server Tools

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

### AWS DevOps Agent Tools

| Tool | Purpose |
|------|---------|
| **aws___list_agent_spaces** | Discover available AgentSpaces in the account — returns names, ARNs, and status |
| **aws___get_agent_space** | Get full details for a specific AgentSpace including ARN, configuration, and linked resources |
| **aws___create_agent_space** | Create a new AgentSpace to scope investigations, chats, and evaluations to a set of resources |
| **aws___create_investigation** | Start an asynchronous incident investigation (typically runs 5–8 minutes); returns a task ID for polling |
| **aws___get_task** | Poll the status of an investigation task — check progress, completion, or failure |
| **aws___list_tasks** | List investigation tasks with optional filters (status, AgentSpace, time range) |
| **aws___list_journal_records** | Read the agent's root cause analysis journal — structured findings, evidence, and timeline entries |
| **aws___list_executions** | List individual execution runs within an investigation task for detailed step-by-step audit |
| **aws___list_recommendations** | Get mitigation and remediation recommendations generated from an investigation |
| **aws___get_recommendation** | Get the full remediation specification for a specific recommendation including runbook steps |
| **aws___list_goals** | List evaluation goals that define quality criteria for investigations |
| **aws___start_evaluation** | Start a quality evaluation of an investigation against defined goals |
| **aws___create_chat** | Start a real-time interactive chat session with the DevOps Agent for ad-hoc troubleshooting |
| **aws___list_chats** | List recent chat sessions with status and metadata |
| **aws___send_message** | Send a message to an active chat session and receive a streamed response from the DevOps Agent |

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

### DevOps Agent — Automated Investigation

Use the AWS DevOps Agent tools when you need AI-powered root cause analysis for incidents or operational issues.

1. **Find the AgentSpace** — Use `aws___list_agent_spaces` to discover available spaces, or `aws___create_agent_space` to create one scoped to the affected resources
2. **Start an investigation** — Use `aws___create_investigation` with a description of the incident; this returns a task ID and runs asynchronously (5–8 minutes)
3. **Poll for completion** — Use `aws___get_task` with the task ID to check investigation status; re-poll until status is `COMPLETED`
4. **Read root cause analysis** — Use `aws___list_journal_records` to review the agent's structured findings, evidence chain, and incident timeline
5. **Review executions** — Use `aws___list_executions` to see the individual steps the agent performed during the investigation
6. **Get recommendations** — Use `aws___list_recommendations` to see mitigation options, then `aws___get_recommendation` for full remediation runbooks
7. **Evaluate quality** — Optionally use `aws___list_goals` and `aws___start_evaluation` to assess investigation quality against defined criteria

### DevOps Agent — Interactive Chat

Use chat for real-time, conversational troubleshooting with the AWS DevOps Agent.

1. **Start a chat** — Use `aws___create_chat` to open a new session scoped to an AgentSpace
2. **Ask questions** — Use `aws___send_message` to describe the issue or ask operational questions; the agent streams a response
3. **Follow up** — Continue the conversation with additional `aws___send_message` calls to drill deeper
4. **Review history** — Use `aws___list_chats` to find and revisit previous chat sessions

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

### AWS DevOps Agent — Investigation Lifecycle

```
# List available AgentSpaces
aws___list_agent_spaces

# Get details for a specific AgentSpace
aws___get_agent_space: --agentSpaceId my-space-id

# Create a new AgentSpace scoped to specific resources
aws___create_agent_space: --name "production-web-tier" --description "Web tier resources in us-east-1"

# Start an incident investigation
aws___create_investigation: --agentSpaceId my-space-id --description "High latency on API Gateway endpoints since 14:00 UTC"

# Poll investigation status (repeat until COMPLETED)
aws___get_task: --taskId task-abc123

# List all investigations filtered by status
aws___list_tasks: --agentSpaceId my-space-id --status COMPLETED

# Read the root cause analysis journal
aws___list_journal_records: --taskId task-abc123

# List execution steps within the investigation
aws___list_executions: --taskId task-abc123

# Get remediation recommendations
aws___list_recommendations: --taskId task-abc123

# Get full details for a specific recommendation
aws___get_recommendation: --recommendationId rec-xyz789
```

### AWS DevOps Agent — Evaluation

```
# List evaluation goals
aws___list_goals: --agentSpaceId my-space-id

# Start a quality evaluation for a completed investigation
aws___start_evaluation: --taskId task-abc123 --goalId goal-456
```

### AWS DevOps Agent — Interactive Chat

```
# Start a new chat session
aws___create_chat: --agentSpaceId my-space-id

# Send a message and get a streamed response
aws___send_message: --chatId chat-def456 --message "Why is my Lambda function timing out?"

# List recent chat sessions
aws___list_chats: --agentSpaceId my-space-id
```

## Best Practices

- **Start with SOPs** — Always check for an applicable Agent SOP before building a manual workflow
- **Use documentation search** — Verify API parameters and service limits before executing calls
- **Specify regions explicitly** — AWS resources are regional; always confirm the target region
- **Use read-only first** — Query resource state before making changes
- **Validate incrementally** — For multi-step procedures, verify each step's output before proceeding
- **Check permissions** — If an API call fails with 403, verify the IAM policy grants the required action
- **Use `aws___suggest_aws_commands`** — Get correct API syntax before calling unfamiliar operations
- **Prefer investigations for complex incidents** — Use `aws___create_investigation` for multi-service issues rather than manual troubleshooting; the DevOps Agent correlates signals across CloudWatch, X-Ray, CloudTrail, and more
- **Poll investigations patiently** — Investigations run 5–8 minutes; poll `aws___get_task` at 30-second intervals rather than flooding with requests
- **Review journal records thoroughly** — The journal contains structured evidence; read all entries before acting on recommendations
- **Scope AgentSpaces appropriately** — Create AgentSpaces aligned with service boundaries (e.g., per-team or per-environment) for focused investigations

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
| Investigation stuck in RUNNING | Investigations take 5–8 minutes; continue polling with `aws___get_task` at 30s intervals |
| Investigation FAILED | Check the task error message; common causes are missing IAM permissions or inaccessible resources in the AgentSpace |
| Empty journal records | The investigation may still be running; verify task status is `COMPLETED` before reading journal |
| No recommendations returned | Not all investigations produce recommendations; check journal records for root cause details instead |
| Chat session expired | Chat sessions have a TTL; create a new session with `aws___create_chat` |
| AgentSpace not found | Verify the AgentSpace ID with `aws___list_agent_spaces`; spaces are region-specific |
| `send_message` timeout | Streamed responses may take time for complex queries; increase timeout or retry |
