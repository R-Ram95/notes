What is Streaming Data?

- data generated continuously by thousands of data sources
- Examples:
    - log files
    - ecommerce purchases
    - in-game player activity
    - information from social networks
    - financial trading floors
    - geospatial services
    - telemetry from connected devices

Kinesis is a family of analytics services for streaming data.

![[/Untitled 54.png|Untitled 54.png]]

# Kinesis Data Streams

- data ingestion and processing service
- data collected and available withint 70ms
- real time analytics
- syncronously replicates data across 3 AZ’s and data can be stored for 7 days
- can handle MB to TB/hour and thousands to millions of PutRecords/second

![[/Untitled 1 35.png|Untitled 1 35.png]]

  

**Architecture**

![[/Untitled 2 29.png|Untitled 2 29.png]]

Data Stream - logical grouping of shards (retained for min 24hours but extended to 7 days)

Shard - temporary holidng space to accumlate data until it is processed

- base throughput unit of kinesis data streams
- contains sequence of records ordered by arrival time
- each shard can ingest up to 1000/second and emit up to 2MB/second
- more throughput === more shards

Data Record - unit of data stored in stream

- sequence #
- partition key
- data blob

Data Blob - data of interest a producer adds to the stream (max size = 1MB before encoding)

Partition Key - meaningful identifier for click stream or time stamp

# FireHose

- loads streaming data into data lakes, data stores, and analytics tools
- enables near real-time analytics

![[/Untitled 3 29.png|Untitled 3 29.png]]

  

Comapred to Data Streams:

|   |   |   |
|---|---|---|
|Characteristic|Data Streams|FireHose|
|Processing Time|70ms|600-900s|
|Storage and Duration|in shards, 24h - 7d|Buffer = 128Mb and 900s|
|Transformation and Conversion|none|Lambda and Glue|
|Data Producer|||

Use **Amazon Kinesis Data Streams** for a data streaming application that ingests **massive amounts of data**.

- Requires sending data to consumer for analytics services for millisecond response time
- Massively scalable
- Data retention from hours to days

**Example:** Real-time gaming

Use **Amazon Kinesis Data Firehose** for a data streaming application that requires **near-real-time response in seconds**

- Must perform data augmentation, data transformation, or data compression
- Must save data to Amazon S3, Amazon Redshift, Amazon ES, Splunk, or send data to Amazon Kinesis Data Analytics for analytics

**Example:** Log analytics, where logs are captured in small batch modes and delivered to Kinesis Data Firehose

# Data Analytics

- Service to analyze streaming data
- scales automatically to match volume and throughput of incoming data
- this is where customers write logic to process streaming data
    - written in ANSI SQL ⇒ Apache Flink and AWS SDK

**How it works**

Data Analytics app has three components

1) Input - Streaming source fo applications, customer must map input to in-app data stream

2) Application Code - Java Operatrors or SQL statement that process data

3) Output - where app code writes to after completing processing

![[/Untitled 4 20.png|Untitled 4 20.png]]
