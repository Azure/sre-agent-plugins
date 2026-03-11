# AWS MCP Server Plugin

Connects Azure SRE Agent to AWS services via the managed [AWS MCP Server](https://docs.aws.amazon.com/aws-mcp/), providing access to AWS documentation, API execution, and Agent SOPs (Standard Operating Procedures) for infrastructure management and troubleshooting.

## Endpoint

The AWS MCP Server endpoint is region-specific. Choose the endpoint that matches your primary AWS Region:

| Region | Endpoint URL |
|--------|-------------|
| US East 1 (default) | `https://aws-mcp.us-east-1.api.aws/mcp` |
| US West 2 | `https://aws-mcp.us-west-2.api.aws/mcp` |
| EU West 1 | `https://aws-mcp.eu-west-1.api.aws/mcp` |
| AP Southeast 1 | `https://aws-mcp.ap-southeast-1.api.aws/mcp` |

Replace the region in the URL pattern `https://aws-mcp.<region>.api.aws/mcp` for other regions.

## Authentication

The AWS MCP Server uses [AWS SigV4](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-signing.html) request signing for authentication. This requires AWS IAM credentials (access key, secret key, and optionally a session token).

### Option 1 — IAM user credentials (simplest for Azure SRE Agent)

1. Log in to the [AWS Management Console](https://console.aws.amazon.com/)
2. Navigate to **IAM** > **Users** > select or create a user
3. Select **Security credentials** tab
4. Under **Access keys**, select **Create access key**
5. Choose **Third-party service** as the use case
6. Copy the **Access Key ID** and **Secret Access Key** immediately — the secret key is shown only once

### Option 2 — IAM Identity Center (SSO) with temporary credentials

1. Configure [AWS IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/)
2. Run `aws configure sso` and complete the SSO setup
3. Use `aws sso login` to authenticate
4. Retrieve temporary credentials from the SSO session

> [!TIP]
> For production use, create a dedicated **IAM user** or **IAM role** with least-privilege permissions rather than using personal credentials. See the IAM permissions section below.

## Configure IAM permissions

The IAM user or role used for the connector must have the following permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "aws-mcp:InvokeMcp",
        "aws-mcp:CallReadOnlyTool",
        "aws-mcp:CallReadWriteTool"
      ],
      "Resource": "*"
    }
  ]
}
```

| Permission | Purpose |
|-----------|---------|
| `aws-mcp:InvokeMcp` | Connect to the AWS MCP Server |
| `aws-mcp:CallReadOnlyTool` | Use read-only tools (search docs, list regions, query metrics) |
| `aws-mcp:CallReadWriteTool` | Use read-write tools (execute AWS API calls, modify resources) |

> [!IMPORTANT]
> For read-only monitoring use cases, omit `aws-mcp:CallReadWriteTool` to prevent the agent from making changes to your AWS resources.

Additionally, the IAM principal needs permissions for the specific AWS services you want the agent to interact with. For example, to troubleshoot EC2 instances, add `ec2:Describe*` permissions.

## Add the connector in Azure portal

1. Navigate to your SRE Agent resource
2. Select **Builder** > **Connectors** > **Add connector**
3. Select **AWS MCP Server** and select **Next**
4. Configure the connector:

   | Field | Value |
   |-------|-------|
   | **Name** | `aws-mcp` |
   | **Connection type** | Streamable-HTTP (pre-selected) |
   | **URL** | Regional endpoint from the table above (e.g., `https://aws-mcp.us-east-1.api.aws/mcp`) |
   | **Authorization** | `Bearer <your-aws-session-token>` |

5. Select **Next** to review, then **Add connector**

> [!NOTE]
> The AWS MCP Server uses SigV4 authentication. Depending on your Azure SRE Agent version, you may need to configure AWS credentials (Access Key ID, Secret Access Key, and Region) as separate connector fields rather than a single Authorization header. Check the Azure SRE Agent documentation for the latest AWS connector configuration options.

Once the connector shows **Connected** status, the AWS MCP tools are available to your agent.

## Available capabilities

Once connected, your SRE Agent can:

- **Search AWS documentation** — Get up-to-date service guides, API references, and best practices
- **Execute AWS API calls** — Run any of 15,000+ AWS APIs with SigV4 authentication
- **Follow Agent SOPs** — Use pre-built step-by-step procedures for common AWS tasks (VPC setup, serverless deployment, monitoring configuration)
- **Check regional availability** — Verify which services and features are available in specific AWS regions
- **Troubleshoot issues** — Analyze CloudWatch logs, CloudTrail events, and diagnose infrastructure problems
