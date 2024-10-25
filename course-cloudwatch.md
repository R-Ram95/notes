# Terms

Logs: Collect, monitor, analyze and store logs

Events: Send Notifications when certain events happen

Alarms: react in real time to metrics / events

Log Groups: name representing an application

Log Stream: within groups ⇒ instances within application / log files / containers

Dimension: an attribute of a metric (instanceId, environment, etc.)

  

# How CloudWatch works

Many AWS resources send metrics to CloudWatch at a rate of 1 data point per metrics per 5min interval. For EC2, you can post metrics every minute using detailed monitoring.

# Metrics

A metric represents a time-ordered set of data points that are publisehd to cloudwatch. Metrics are the variable and data points are the value. Every metric is associated with a timestamp. Resources attach dimensions, name/value pairs that are part of the metrics identity.

CloudWatch aggregates stats according to time periods. Live data is when data is published within the last minute that has not been aggregated.

- metrics belong to namespaces

- up to 10 dimensions per metric
- have timestamps

## EC2 Detailed Monitoring

- cost for data every minute
- use if you want to scale ASG faster
- Memory usage is not default

## Custom Metrics

- use API call `PutMetricData`
- `StorageResolution` api ⇒ two params Standard & High Resolution
- Important: when you push a metric in the future or the past, this works ⇒ configure EC2 instance time correctly
    - 2 weeks in the past 2 hours in the future

# Logs

Logs are a centralized place for logs to be stored and analyzed. You can query and filter data in logs.

Lambda is setup to post to CloudWatch logs by giving the Lambda permission to post. For EC2 you need to install the CloudWatch Logs agent

  

**Sources:**

- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
- Elastic Beanstalk
- ECS
- AWS Lambda
- VPC
- Api Gatway
- CloudTrail
- Route53

## Filter

Can filter expressions in Logs

Filters can be used to trigger CloudWatch Alarms

## S3 Export

- export logs to S3
- can take up to 12 hours
- `CreateExportTask` is the API call
- to stream logs ⇒ use Subscriptions

## Subscriptions

- filter you add ontop of cloudwatch logs

![[/Untitled 53.png|Untitled 53.png]]

# Alarms

Alarms are notifications sent by CloudWatch when metrics are above or below a set threshold for a specified amount of time. Alarms can initiate an actions.
