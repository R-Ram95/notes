---
tags:
  - AWS
  - DevOps
  - Security
---
** If you don’t specify a region, the default region is us-east-1

# AWS CLI

AWS CLI STS

- use to decode error messages

AWS Meta Data

- allows EC2 instances to “learn about themselves” without using IAM Role for that purpose
- can get IAM role name from meta data, but not IAM policies
    - Meta Data = info about EC2 instance
    - User Data = launch script of EC2
    

# MFA with CLI

- create a temporary session
- run the `STSGetSessionToken` API calls

  

# Software Development Kit (SDK)

- run AWS commands without using the CLI through supported programming lanugages
- the CLI uses the Boto3 (python) SDK
- know when to use the SDK for the exam

# AWS Limits (Quotas)

API Rate Limits

- there is a limit to how many times we can make AWS API calls
    - `DescribeInstances` for EC2 = 100 calls/s
    - `GetObject` on S3 = 5500 GET /s/prefix
- if we go over the limit we get **Intermittent Errors** b.c. we are throttled:
    - use Exponential Backoff
- for **Consistent Errors** (if our application is using the API extensively): request an API throttling limit increase

Service Quotas (Limits)

- running on-demand Standard Instances: 1152 vCPUs
- request service limit increase by opening a ticket
- request service quota increase using Service Quotas API programmatically

Exponential Backoff

- use when faced with `ThrottlingException`
- retry mechanism is included in the AWS SDK
- must implement yourself if using AWS API as-is
    - implement retries when faced with `5xx` server code errors
    - do not implement on `4xx` client errors
- the method: exponentially increase the time before calls (retry1 = 2s, retry2 = 4s, retry3 = 8s, etc.)

  

# AWS Credentials Chain

CLI looks for credentials in the following order:

1) CLI Options: `--region`, `--output`, and `--profile`

2) Environment Variables: `AWS_ACCESS_KEY`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSIONS_TOKEN`

3) CLI Credentials file - aws configure

`~/.aws/credentials`

4) CLI configuration file - aws configure

`~/.aws/config`

5) Container credentials - for ECS tasks

6) Instance Profile credentials - EC2 instance profiles

SDK is similar but slightly different

# SigV4

- any AWS API request must be signed
- SDK and CLI do this automatically
- if done manually, you must sign in HTTP headers or Query Strings