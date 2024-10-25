
# Uses

- Troubleshoot performance
- understand dependencies in microservices architecture
- pinpoint service behaviour issues
- review requested behaviour
- find errors and exceptions

# How to Enable?

1) Requires changes to code using the AWS SDK

2) install AWS Daemon or enable X-ray integration

- each application must have IAM rights to write data to X-Ray

For Lambda:

- need to ensure it has IAM execution role with the proper policy

Install Daemon on Beanstalk:

- need to add `.ebextensions/x-ray-daemon.config` file `X-RayEnabled:true`

# API

Write:

`PutTraceSegements` - uploads segment documents to X-ray (required to write into X-Ray)

`PutTelemtryRecord` - helps with metrics ⇒ tells X-Ray how many segments are uploaded

`GetSamplingRule` - retrieve sampling rules

`GetSamplingTargets` - advanced

`GetSamplingStaistics` - advanced

X-Ray Daemon needs to have correct IAM rules

Read:

- GetServiceGraph: main graph
- BatchGetTraces: Retrieves a list of traces specified by ID. Each trace is a collection of segment documents that originates from a single request.
- GetTraceSummaries: Retrieves IDs and annotations for traces available for a specified time frame using an optional filter.To get the full traces, pass the trace IDs to BatchGetTraces.
- GetTraceGraph: Retrieves a service graph for one or more specific trace IDs.

# X-Ray with Beanstalk

Beanstalk platform includes the X-Ray Daemon. run by adding one setting in the console or setting `X-RayEnable:true` in the `ebextension/xray-daemon.config` file.

Make sure the instance profile has the correct IAM permissions for the X-Ray daemon

# X-Ray with ECS

ECS Cluster (X-Ray Container as Daemon)

- run the X-Ray daemon as a container on each EC2 instance


ECS Cluster (X-Ray container as Sidecar)

- run an X-ray container as a sidecar to the app container (via network)
- one app container/ X-Ray container
- The same applies to Fargat b/c we don’t have control over the underlying EC2 instances


## Example Task Definition


- map container port for X-Ray daemon to port 2000 UDP
- set env variable called `AWS_XRAY_DAEMON_ADDRESS`
- link the two containers
