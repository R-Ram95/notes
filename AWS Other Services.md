---
tags:
  - AWS
  - DevOps
---
# SES

- simple email service
- used to send emails to people
- integrated with IAM for allowing to send emails

  

# DB Summary

- RDS - relational DB, OLTP
    - SQL, Oracle
    - Aurora + Aurora Serverless
    - Provisioned DB
- Dynamo DB: NoSQL
    - managed, key value store
    - serverless
- Elasticache: in mem DB
    - Redis / Memcached
    - provisioned
- Redshit: OLAP - analytic processing
    - data warehousing / datalake
- Neptune: Graph DB
- DMS: Database migration service
- DocumentDB: managed MongoDB

# ACM - AWS Certificate Manage

- lets you provision, manage and deploy SSL/TLS certificates

# CloudMap

- fully managed resource discovery service
- creates a map of the backend services/resources your application depends on

# Fault Injection Simulator (FIS)

- fully managed service for running fault injection experiments on AWS workloads
- Chaos Engineering - stressing the system

  

# AWS DataSync

- move large amount of data to and from places
    - on-prem/other cloud to AWS - needs agent to run on prem or other cloud
    - AWS to AWS - no agent needed
    - File permission and meta data are preserved using the NFS POSIX, SMB protocol
    - one task can use 10Gbps