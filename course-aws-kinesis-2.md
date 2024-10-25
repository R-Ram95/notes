- makes it easy to collect, process and analyze data in REAL-TIME

# Kinesis Data Steams

- a way to stream big data into your systems
- data stream is made up of shards
    - must be provisioned ahead of time
- data is split across shards
- Produces create records:
    - partition key
    - Data blob = data up to 1 MB
    - data can be sent at 1MB/s /shard or 1000 msgs/sec/shard
- consumers get records:
    - partition key
    - Data blob
    - sequence no. ⇒ to tell where the data belongs
    - Shared ⇒ 2MB/s/shard for all consumers
    - Enhanced ⇒ 2MB/s/shared/consumer
- Retention: 1 to 365 days
- ability to reprocess data
- Data is immutable
- data sharing the same partition goes to the same shard (key based ordering)

## Capacity Modes

- provisioned mode: (if you know how much you need)
    - choose number of shards → scale manually or using API
    - each shard gets 1MB/s (or 1000 records/s) in
    - each shard gets 2MB/s out (classic or enhanced)
    - pay per shard provisioned per hour
- On-demand mode: (you don’t know how much you need)
    - do not need to provision or manage capacity
    - default capacity provisioned (4MB/s in or 4000 records/s)
    - scales automatically based on observed throughout peak during the last 30 days
    - pay per hour & data in/out per GB

## Security

- IAM policies: control access and authorization
- Encryption in flight (HTTPS)
- Encryption at rest (KMS)
- can implement client-side encryption
- VPC endpoint available for Kinesis ⇒ no need to access kinesis through internet

# Kinesis Producers

How to get data into Kinesis.

- Kinesis producers Put data records into data streams.
- Producers:
    - AWS SDK
    - Kinesis Producer Library
    - Kinesis Agent
- `PutRecord` API
- batch with `PutRecords` to reduce costs
- Hot Partition - when partition keys are not distributed well ⇒ results in one shard getting more data
- `ProvisionedThroughputExceeded` - results when we exceed the throughput threshold ⇒ solution is to:
    
    - use highly distributed partition keys
    - Implement retries with exponential backoff
    - increase shards (scale)
    
      
    

# Kinesis Consumers

Shared (Classic) Fan-out Consumer - Pull Model


- consumers share the 2 MB/s data transfer (e.g. each gets 666KB/s)
- low # of consuming apps
- Max 5 `GetRecords` API calls per sec
- latency 200ms
- minimize cost
- returns up to 10MB (then throttle for 5 seconds) or up to 10000 records

  

Enhanced Fan-out - Push Model


- multiple consuming applications per stream
- 2MB/s per consumer per shard
- latency = 70ms
- higher cost
- pushes data over HTTP/2
- soft limit of 5 consumer applications

# Kinesis Client Library (KCL)

- Java library to help read record from Kinesis Data Stream
- each shard can be read by 1 KCL instance
    - e.g ⇒ 4 shards = max 4 KCL instances

# Kinesis Firehose

- gets data from producers and batch writes to:
    - S3
    - Redshift
    - ElasticSearch
- full managed and serverless
- Near Real Time service
    - 60s latency min
    - need to wait till atleast 1MB to send

# Kinesis Data Analytics

Two flavours:

## Kinesis Data Analytics for SQL

Input:

- data stream
- fire hose

Processing:

- apply sql statements to apply real time analytics
- join data from s3 buckets ⇒ enrich the data

Output:

- data stream - create streams out of real time data analytics
- fire hose - send analytics results to destinations

  

- full managed service, auto scaling
- pay for consumption rate

## Apache Flink

- Java, Scala, SQL to process and analyze data
- Input:
    - Data Streams
    - Amazon SMK
- run Flink on managed AWS cluster ⇒ more powerful than standard SQL
