---
tags:
  - AWS
  - Compute
  - DevOps
---
  

What is EC2?

EC2 sizing & config

EC2 Lifecycle

EC2 User Data

EC2 Instance Types

Naming Convention

General Purpose

Compute Optimized

Memory Optimized

Accelerated Computing

Storage Optimized

Security Groups

SSH Summary

EC2 Instance Role Demo

EC2 Instances Purchasing Options

EC2 Capacity Reservations

Summary

# What is EC2?

- Elastic Compute Cloude ⇒ Infrastructure as a Service
- consists in the capability of:
    
    - renting virtual machines (EC2)
    - storing data on virtual drives (EBS)
    - distributing load across machines (ELB)
    - scaling services using auto-scaling group (ASG)
    
    ## Benefits
    
    - elasticity ⇒ scale up and down in minutes
    - control ⇒ root access, admin access
    - flexible ⇒ multiple OS, instance types, etc
    - integrated ⇒ integrated with other aws seriices
    - reliable ⇒ instance can be replaced quicky
    - secure ⇒ data center and network architecture
    - cost-effective ⇒ pay as you go
    
      
    

# EC2 sizing & config

we can choose exactly how we want out virtual machine to be

- OS ⇒ Linux, Windows, MacOS
- how much compute (CPU)
- how much RAM
- how much storage space:
    - network attached (EBS & EFS)
    - hardware (EC2 instance store)
- Network card: speed of card, public IP address
- firewall rules: security group
- bootstrap script ⇒ configure at first launch: EC2 User Data

![[/Untitled 23.png|Untitled 23.png]]

# EC2 Lifecycle

**Pending**

billing has not started, machine is getting ready to run

**Running**

billing starts, machine is running and ready to use. Maintain public DNS name

**Rebooting**

different than stop, === rebooting an OS. Must have EBS. Retains private IPv4

**Stopped**

Similar to shutting down laptop, when you stop, machine state is preserved

**Terminate**

instance stores are erased

![[/Untitled 1 12.png|Untitled 1 12.png]]

# EC2 User Data

- use EC2 user data script to bootstrap instances
- bootstrap = launch commands when machine starts
- script is only run once at instance start
- automate tasks such as:
    - install updates
    - install software
    - download common files from internet
- Runs with the Root User

# EC2 Instance Types

## Naming Convention

e.g. m5.2xlarge

m - instance class

5- generation

2xlarge - size of the instance (bigger size ⇒ bigger memory)

## General Purpose

- for diverse workloads like web servers and code repos
- balance between compute, memory and networking

## Compute Optimized

- for compute-intensive tasks that require high performance processors
- e.g. machine learning, batch processing workloads

## Memory Optimized

- fast performance for workloads that process in-memory data
- use cases:
    - high performance relational/non-relational dbs
    - distributed web scale cache
    - in-memory dbs optimized for BI

## Accelerated Computing

- floating point number processing
- graphics

  

## Storage Optimized

- for storage intensive tasks that require high sequential read/write access to large data sets
- OLTP systems
- relation/no-sql dbs
- data warehousing
- distributed file systems

  

# Security Groups

- a firewall around EC2 instance to control how traffic is allowed in and out
- only contain **allow** rules
- rules can reference by IP or security group
- regulate:
    - access to ports
    - authorised ip ranges
    - communication protocol
- can be attached to multiple instance
- locked to region / VPC combination
- if traffic is blocked, EC2 won’t see it
- **all inbound traffic is blocked by default**
- **all outbound traffic is authorized by default**

![[/Untitled 2 12.png|Untitled 2 12.png]]

** Ports to Know **

- 22 - ssh ⇒ log into EC2 instance on linux
- 21 - FTP ⇒ upload files into file share
- 22 - SFTP (secure file transfer) ⇒ upload files using ssh
- 80 - HTTP ⇒ access unsecured websites
- 443 - HTTPS ⇒ secured websites
- 3389 - RDP (remote desktop ) ⇒ log into a windows instance

# SSH Summary

- Secure Shell
- SSH ⇒ Mac, Linux, Windows > =10
- Putty ⇒ all Windows
- EC2 Instance Connect ⇒ all OS but only for Amazon NX2

  

# EC2 Instance Role Demo

- Only add aws credentials to EC2 instances through IAM Roles ⇒ never use `aws configure`

# EC2 Instances Purchasing Options

- pay by the hour

  

|   |   |
|---|---|
|On Demand|**- for short, un-interrupted workloads**  <br>- pay for what you use (PH/PM)  <br>- highest cost, no upfront payment  <br>- no long-term commitment|
|Reserved|- **for steady-state usage applications (DBs)**  <br>- up to x% discount to On-demand  <br>- reserve specific instance attributes  <br>- 1 or 3year reservation period  <br>- Upfront, Partial, All upfront|
|Savings Plan|- discount based on long-term usage  <br>- usage beyond EC2 savings billed as On-demand price  <br>  <br>- locked to instance family and region (e.g. M5 us-east-I)  <br>- flexible on Instance Size, OS, Tenancy|
|Spot Instances|- best discount  <br>- define max price ⇒ can lose at any time  <br>- useful for: batch jobs, data analysis, image processing, distributed workloads  <br>- NOT SUITABLE FOR CRITICAL JOBS OR DBs|
|Dedicated Hosts|- physical EC2 instance dedicated to you  <br>- for compliance requirements and allow you to use existing server-bound software licences  <br>- most $  <br>- BYOL (bring your own licence)  <br>- companies with strong regulatory needs|
|EC2 Dedicated Instances|- instance runs on hardware dedicated to you  <br>- may share hardware with other instance in same account  <br>- no control over instance placement|

Difference between Dedicated Hosts and Instances:

![[/Untitled 3 12.png|Untitled 3 12.png]]

Hosts:

- access to physical server itself
- access to lower-level hardware

Instance:

- own instance on own hardware

# EC2 Capacity Reservations

![[/Untitled 4 8.png|Untitled 4 8.png]]

# Summary

![[/Untitled 5 8.png|Untitled 5 8.png]]