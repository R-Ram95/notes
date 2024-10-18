---
tags:
  - AWS
  - Storage
---
1. Introduction

2. Features

3. Use Cases

# 1. Introduction

Outputs extends AWS infrastructure, services, APIs, and tools to your on-prem servers. It is suitable to use local compute and storage to aheive lower latency and data processing. AWS manages the capcity as part of an AWS region.

  

# 2. Features

- Compute
    - EC2 instances: General purpose, compute optimized, memory optimized, graphics optimized, I/O optimized,
- EBS, EBS snapshots
- S3
- Cloud Endure migration and disaster recovery
- Networking
    - VPC extension - extend vpc in cloud to on-prem Outpost
    - Local gateway - gateway to connect Outpost resources to on-prem networks
    - load balancer - you can provision an ALB
    - private connectivity - connect to AWS region from Outpost via AWS Direct Connect
    - Security and Compliance

# 3. Use Cases

- low-latency compute
- local data processing
- data residency
- migration & modernization