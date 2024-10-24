---
id: Data Analytics Pipelines
aliases: []
tags:
  - AWS
  - Data-Analytics
---
This is a high-level example of a Data Analytics Pipeline:

Step 1: Data Ingestion

- Data collection
- data has 1 or more of these: Volume, Variety, Velocity and Veracity)

Step 2: Store

- data is stored in a scalable and persistent way

Step 3: Process and Analyze

- data taken from storage, operations are performed on it, processed data is stored back in storage
- Optionally, process data can be used by other processing or analysis tools

Step 4: Visualize

- answers from data are visualized using Business Intelligence (BI) tools

  

Data Collection:

- Kinesis Firehose
- Snowball
- Kinesis Data Streams
- Direct Connect

Store:

- S3
- S3 Galcier
- DynamoDB
- RDS
- ES
- Cloudsearch
- Aurora

Process and Analyze:

- EMR
- Athena
- Kinesis Data Analytics
- Sagemaker
- Managed Streaming for Kafka
- Redshift

Visualize:

- Quicksight

Automate:

- Database Migration Service
- Glue

  

# Moving Data to the Cloud - Options

Direct Connect:

- dedicated private connection
- 1 Gbps - 10 Gbps

Storage Gateway:

- hybrid cloud storage
- connects on-prem to the cloud

S3 Transfer Accelerator:

- accelerates transfer of data to S3 by 50-500%
- Customers use AWS’ global network infrastructure (high bandwidth, and internal)
- uses closest CDN to Customer

Snowball:

- physical devices used for large (TB

Kinesis Data Firehose:

- real-time streaming data
- data can be streamed to S3 and Redshift

Data Migration Service:

- migrate legacy databases to the cloud

  

# Data Warehouses

Datawarehouses are a central repository of data from different sources. The data is extracted, cleaned, transformed and loaded using ETL tools into a data warehouse

- AKA Online Analytical Processing (OLAP)
- stores data that has strict types that confom to a schema with rows and columns in a denormalized table
- data is stored in a way to optimize speed and analysis
- Examples include: AWS Redshift, Oracle, and Greenplum

  

## Challenges of Traditional and On-prem architectures

- difficult to scale
- long lead times for hardware procurement
- complex upgrades are normal
