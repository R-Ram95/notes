---
tags:
  - AWS
  - Data-Analytics
  - Data-Warehouse
---
A Fully managed cloud data warehouse

- optimized for high performance
- supports open file formats
- petabyte scale capability
- supports complex queries and analytics with visualization tools
- end to end encryption
- SLA of 99.9%
- cost efficient
- based on open Postgre DB

  

## Performance Features

**Massively Parallel Processing (MPP)**

- breaks large job into smaller tasks and distributes tasks amongst multiple compute nodes ⇒ faster processing times

**Columnar Storage**

- data from columns is stored together so data can be accessed faster ⇒ improves performance

**Shared-nothing Archtecture**

- independent and resilient nodes without dependencies ⇒ improves scalability

  

## Architecture

- consists of leader node and one or more compute nodes
- leader node is in customers VPC ⇒ access point to redshift
- customer can only interact with leader node and only using JDBC or ODBC

  

## Resizing Redshift

  

**Elastic Resize - nodes are added or removed**

- in two stages
- Stage 1:
    - cluster is unavailable while elastic resize migrates cluster metadata
    - completes in minuts
    - holds session connections while queries remain queued
- Stage 2:
    - session connections are reinstated and queries resume
    - redistributes data to node slices in the background
    - cluster is available for read and write operations

**Classic Resize - configured to different node count and instance type**

- might take hours to complete
- all data is streamed from old cluster to the new cluster
- original cluster is in read-only mode during resize
- customer is only charged for one cluster

  

  

## Node Types

**Amazon Redshift Analytics - RA3**

- flexible, customers can grow and pay for compute and storage independently, and scale to meet their workload requirements
- uses combination of SSD for hot data and S3 for Cold Data

  

**Dense Compute - DC2**

- high-performance local SSD

  

**Dense Storage - DS2**

- local hard-disk drive storage
- generally not recommended