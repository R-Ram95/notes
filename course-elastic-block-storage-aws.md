# 1. Introduction

EBS consists of two types of block storage services: 1) EC2 Instance storage, 2) Elastic Block Storage (EBS)

# 2. Instance Store

Provides temporary (Ephemeral) block-level storage for an instance ⇒ storage is located on disks that are physically attached to the host computer. The lifecycle of Instance stores is tied to the lifecycle of EC2 instances, you lose storage if EC2 is **stopped**. Data is lost if:

- underlying disk drive fails
- instance stops, hibernates, or terminates

  

Use Cases:

- data replication to other EC2 instances
- good I/O performance
- buffer, cache, scratch data, temp content
- provides sub ms latency

Cons:

- risk of data loss if hardware fails
- backups are required
- not replicated across multiple devices

# 3. Elastic Block Store (EBS)

EBS is like a physical hard drive for EC2. It can be detached from instance and then re-attached to another instance, and data is persisted even after EC2 termination.

  

Features:

- data is persisted even after EC2 termination
- can only be mounted to one instance at a type.
- must provision capacity (GBS and IOPS) in advance
- capacity ranges from 1GB to 16TB.
- scale EBS volumes in two ways: 1) increase volume size, 2) attach multiple volumes to an instance.
- deployed in AZ of your EC2 → create snapshots for replications to new AZ
- divided into two categories: SSD and HDD
- elastics → you increase capcity dynamically
- data is encrypted

  

Use Cases:

- for quick data retrieval and long-term data persistence.
- primary storage for file systems, DB’s
- apps that require fine grain updates and access to raw data
- high durability =? suitable for lift and shift

  

Pricing:

- pay as you go
- cost is based on volume type, provisioned size, provisioned IOPS, and throughput performance

  

## 3.1 SSD

**General Purpose (gp2 / gp3)**

- Cost effective storage
- gp3:
    - independently set throughput and IOPS (not linked)
- gp2:
    - throughput and iops are linked

**Provisioned IOPS SSD (io1 / io2)**

- highest performance
- more than 16,000 IOPS
- good for DB work loads
- supports EBS multi-attach

## 3.2 HDD

**Throughput optimized (st1/sc1)**

- Throughput optimized st1
    - for frequently accessed, throughput-intensive workload
- Cold HDD sc1
    - lowest cost HDD volume for less frequently accessed data

## EBS Snapshots

- backup of EBS volume at a point in time
- do not need to detach volume to do snapshot, but is recommended
- can copy snapshots across AZs or regions

![[/Untitled 46.png|Untitled 46.png]]

## Features

EBS Snapshot Archive

- move snapshot to archive tier (75% cheaper)
- 24 to 72h for restoring archive

Recycle Bin

- retain deleted snapshots incase of accidental deletion

Fast Snapshot Restore

- add data from snapshot to volume instantly ($$$)

  

## EBS Multi-Attach

- attach same EBS to multiple EC2 instances in the SAME AZ
- must be used with io family EBS
- use case:
    - high application availability in clustered applications
- up to **16** EC2 instances at a time
- must use file system that is cluster aware (not XFS. EX4, etc.)

# EBS vs EFS vs Instance Store

EBS:

- attached to one EC2 instance at a time
- network file system
- one AZ
- migrate EBS from one AZ to another using Snapshots
- Root EBS get terminated if EC2 instance is terminated

EFS:

- attached to 100s of EC2 instances at a time
- network file system
- multiple AZs
- only for Linux Instances
- more $$ than EBS

Instance Store:

- physical drive attached to Instance
- provides max I/O
- ephemeral storage ⇒ data is lost
