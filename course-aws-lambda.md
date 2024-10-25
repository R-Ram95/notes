
# What is Serverless?

- there are still servers but they are now managed ⇒ developers don’t need to deal with servers

# What is Lambda?

- Virtual functions ⇒ no server to manage
- limited by time ⇒ short executions max 15 mins
- on-demand ⇒ pay for what you use
- scaling is automated

# Benefits

- get more resources per function ⇒ up to 10 GB
- increasing RAM will improve CPU and network

# Language Support

**Java, Go, PowerShell, Node.**  
   
**js, C#, Python, and Ruby**

Custom Runtime API

- has Lambda Container Image but image must run the Lambda RuntTime API
- ECS/Fargate preferred to run arbitrary Docker Images

# Synchronous Invocation

- Results are return right away
- Error handling on client side

![[/Untitled 15.png|Untitled 15.png]]

## Services

- anything user invoked: ELB, Amazon Gateway API, CloudFront, S3 Batch
- Service Invokes: Cognito, Step Functions,
- Other services: Lex, Alexa, Firehose

## Lambda + ELB

- Expose Lambda as HTTPS endpoint using ALB (or Gateway)
- lambda must be registered as target group

![[/Untitled 1 8.png|Untitled 1 8.png]]

### ALB to Lambda and Back

ALB TO LAMBDA: HTTP TO JSON

- Request payload for lambda is turned into a JSON by the ALB
- JSON is sent to Lambda

![[/Untitled 2 9.png|Untitled 2 9.png]]

LAMBDA TO ALB: JSON TO HTTP

![[/Untitled 3 9.png|Untitled 3 9.png]]

  

### ALB Multi-header values

- if header or query parameters contain the same name for keys but different values, the ALB sends the params as an array in the JSON.
- must enable the multi-header option

![[/Untitled 4 6.png|Untitled 4 6.png]]

## Lambda@Edge

- CDN deployed on CloudFront
- want to run Lambda globally alongside
- Deploy Lambda alongside your CloudFront CDN
- Lambda can be used to change CloudFront requests
    - Viewer Request → CloudFront receives request from user
    - Viewer Response → CloudFront send response to user
    - Origin Request → CloudFront sends request to Origin
    - Origin Response → CloudFront receives response from Origin
- ** Use when you need to integrate Lambda with CloudFront **

# Async Invocation

- services that invoke Lambda behind the scenes, e.g. S3, SNS, CloudWatch events
- events placed in Event Queue waiting for Lambda to process them
    - lambda attempts retry on errors 3 times:
        - 1 min wait after first, then 2 min
- if Lambda never suceeds ⇒ send to DLQ
- can batch file processing (start processing X number of files at a time)

# Event Source Mapping

- for: Kinesis Data Steam, SQS & SNS FIFO Queue, DynamoDB
- Lambda must poll for records (ask each service for records)
- Lambda is invoked synchonously

![[/Untitled 5 6.png|Untitled 5 6.png]]

## Streams (Kinesis & DynamoDB)

- event source mapping creates iterator for each shard ⇒ items are processed in order
- processed items are not removed from the stream ⇒ other consumers can read them
- Low Traffic ⇒ use batch window to accumulate records
- High Traffic ⇒ process multiple batches in parallel at the shard level
    - 10 batch processors per shard

On Error:

- batch is reprocessed till function succeeds or items in batch expire ⇒ can block processing
- to overcome configure source mapping to:
    - discard old events
    - restrict number of retries
    - split batch on error ⇒ if error is due to Lambda timeout
- discarded events got to a Destination

## Queues (SQS and SQS FIFO)

![[/Untitled 6 5.png|Untitled 6 5.png]]

- uses Event Source Mapping
- will poll SQS using Long Polling
- 1 - 10 messages per batch
- synchronous invocation of Lambda ⇒ cannot set up DLQ for Lamda, must set DLQ for SQS
- or use Destination for failures

  

# Destinations

- Async Invocations - destinations for failed and successful events:
    - SQS, SNS, Lambda, Event Bridge
    - recommended to use this instead of DLQ
- Event Source Mapping - discarded events
    - SQS, SNS

  

# Lambda Permissions

- Grants Lambda permissions to AWS resources
- Required when you use event source mapping to read event data or if Lambda needs to invoke other sevi
- Lambda Resource Based Policies
    - give other accounts and AWS services permission to use Lambda
    - IAM Principal can access Lambda:
        - if IAM policy attached to the principal authorizes user access
        - OR resource-based policy authorizes service access
- if lambda is invoked by a service ⇒ use resource based policy
- if Lambda is invoking a service ⇒ modify Lambda IAM permissions

# Env Variables

- key val pair in string form
- can be encrypted by KMS

  

# Logging & Monitoring

- Cloudwatch logs:
    - execution logs stored in CloudWatch logs
    - Lambda needs execution role with IAM policy that authorized write to CloudWatch Logs
- CloudWatch Metrics
    - Lambda metrics in CloudWatch Metrics UI
    - Invocations, Durations, Concurrent Executions, Error Count, Success Rate, Throttles, Async Delivery Failures, Iterator Age
- X-Ray:
    - Active Tracing
    - Runs X-Ray Daemon
    - use X-ray SDK in code
    - Lambda needs IAM Execution Role ⇒ AWSXRayDaemonWriteAccess
    - Env Variables
        - _X_AMZN_TRACE_ID: contains the tracing header
        - AWS_XRAY_CONTEXT_MISSING: by default, LOG_ERROR
        - AWS_XRAY_DAEMON_ADDRESS: the X-Ray Daemon IP_ADDRESS:PORT

# Lambda in VPC

By default, Lambda is launched outside your VPC on an AWS-owned VPC ⇒ it cannot access resources in your VPC (RDS, Elasticache, internal ELB, etc.)

![[/Untitled 7 3.png|Untitled 7 3.png]]

Solution:

- You can deploy your Lambda in a VPC
- Define VPC ID, Subnets and Security Groups
- Lambda creates ENI(Elastic Network Interface) in your subnets
- Requires AWSLambdaVPCAccessExecutionRole

![[/Untitled 8 3.png|Untitled 8 3.png]]

- RDS Security group requires network access to Lambda Security Group

Caveat:

- by default, Lambda inside your VPC does not have internet access
- deploying Lambda in a public subnet does not give it internet access or public IP
- Solution ⇒ deploy lambda in private subnet and give it Internet access using NAT gateway

![[/Untitled 9 3.png|Untitled 9 3.png]]

# Performance

RAM ⇒ 128 MB to 10GB in 1MB increments

more Ram ⇒ more vCPU

@1792 MB RAM ⇒ 1 full vCPU

after, you ned multi-threading to benefit

* Computation heavy ⇒ increase RAM

Time out: default 3s, max 900s

## Execution Context

- temp runtime enviroment that initializes DB Connections, HTTP Clients, SDK Clients…
- maintained for some time in case of other Lambda Invocations
- context is re-used to save time initialize
- includes the /tmp directory

  

# Concurrency

- can set concurrency → X number of Lambda functions in execution at a time up to 1000 functions running
- this limit applies to all functions in your Account
- Synch ⇒ direct invocation ⇒ return ThrottleError (429)
- Asynch ⇒ retry then go to DLQ

## Cold Starts

- on new instances, code load might take a long time
- Add provisioned Concurrency ⇒ allocated before function starts

  

# Lambda Layers

- Customer Runtimes
- Externalize Dependencies and re-use them:
    - Application (code you write) in one layer
    - Packages in another layer
    - Package layer can be re-used by other Lambda Functions

  

# Lambda Container Images

- Deploy Lambda Image from ECR up to 10GB
- allows us to package complex dependencies
- Base Image must run the Lambda Runtime API

  

# Lambda Versions and Aliases

## Versions

- `$LATEST` mutable
- Published functions are immutable ⇒ can’t change code, env variables
- each version gets it own ARN

## Aliases

- pointers to Lambda function versions
- mutable
- e.g. “dev” “test” “prod” aliases can point to different versions
- enable blue/green deployment b.c. we can add weights to Lambda functions
- aliases cannot reference alias
- aliases get ARN

# Lambda Limits

## Execution

- RAM - 128MB - 10GB
- Execution time: 15 mins
- Env Variables: 4KB
- Disk capacity in /tmp folder: 512MB to 10GB
- 1000 concurrent executions

## Deployment

- compressed zip file: 50MB
- uncompressed: 250MB
    - can use /tmp to load other files at startup
- env variables: 4KB

  

[[Lambda@Edge]]
