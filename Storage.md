---
tags:
  - AWS
  - Storage
---
AWS Storage Portfolio

1. Core Storage Services

1.1 Block Storage (AWS EBS)

1.2 File Storage (AWS EFS)

1.3 Object Storage (AWS S3 and AWS S3 Glacier)

2. Edge and Hybrid Services

2.1 Edge - Local Compute and Storage (AWS Snow Family)

2.2 Hybrid - On-prem cloud storage (AWS Outposts)

3.2 Hybrid - On-prem gateways (AWS Storage Gateway)

3. Data Transfer and Migration Services

3.1 File Transfer Services (AWS Transfer Family)

3.2 DataSync (AWS DataSync) - Online Data transfer

3.3 Offline data transfer (AWS Snow)

3.4 Migration Services (AWS MGN)

4. Data Protection

4.1 Backup and Archive (AWS Backup)

4.2 Snapshots

4.3 Replication

4.4 Disaster Recovery (CloudEndure Disaster Recovery)

5. Choosing the Right Storage Solution

5.1 Questions to ask

  

# AWS Storage Portfolio

![[/Untitled 14.png|Untitled 14.png]]

# 1. Core Storage Services

Regardless of On-prem or in the cloud, there are three categories for storage:

1) **File Storage** - data stored in file hierarchy

**Ideal for centralized access to files that must be shared across multiple host computers**. File storage is built on-top of block storage. The OS formats and manages the reading and writing of data to the block storage devices. The two most common protocols are Server Message Block (SMB) and Network File System (NFS).

  

2) **Block Storage** - data stored in fixed block size on and HDD, SSD, NVMe or SAN

Data is chunked up into fixed sizes and each chunk has an address. Block storage is fast and uses less bandwidth and is therefore **suitable for low-latency**

  

3) **Object Storage** - stored as objects in buckets

Object storage can be used to store almost any data type with no limit to the number of objects stored. **Useful for storing large or unstructured data sets.** Object storage is also built on-top of block storage.

![[/Untitled 1 7.png|Untitled 1 7.png]]

![[/Untitled 2 8.png|Untitled 2 8.png]]

### 1.1 Block Storage (AWS EBS)

Elastic Block Store (EBS) is the block storage option of AWS. It is attached to EC2 instances.

### 1.2 File Storage (AWS EFS)

Network Attached Storage (NAS) is used to support access to shared files. File Storage is **useful for content repos, dev environments, media stores, or user home directories.** AWS offers 5 file storage optioons: (FSx === File Storage x)

1) Elastic File Storage - multi-AZ file storage that uses the NFS protocol

  

2) FSx for Lustre - **designed for high performance computing (HPC) and Machine Learning workload.** It uses the POSIX-compliant protocol

  

3) FSx for Windows File Server - **designed for Windows workloads** and it uses the Server Message Block (SMB) protocol

  

4) FSx for NetApp ONTAP - **designed to provide NetApp block and file storage**. Uses the iSCSI block storage protocol and NFS/SMB for file storage

  

5) FSx for OpenZFS - used for migrating on-prem OpenZFS storage to AWS.

  

### 1.3 Object Storage (AWS S3 and AWS S3 Glacier)

Uses S3 and S3 Glacier (glacier is the lowest cost S3 solution)

  

# 2. Edge and Hybrid Services

AWS provides edge compute and storage solutions for remote or disonnected locations to connect with on-prem or storage services in the cloud.

  

![[/Untitled 3 8.png|Untitled 3 8.png]]

### 2.1 Edge - Local Compute and Storage (AWS Snow Family)

Allows you to use compute and storage even when disonnected from AWS cloud. Edge location devices includes the **AWS Snow Family:**

Snowball is an edge computing, data migration, edge storage device. It can be used for data collection, ML and processing, storage in remote and disonnected locations. There are two options: Storage Optimized and Compute Optimized

  

Snowcone is for used outside a traditional data center

  

Snowmobile is a large scale storage device used to physically migrate data into AWS. You can transger up to 100 PB per snowmobile.

### 2.2 Hybrid - On-prem cloud storage (AWS Outposts)

On prem cloud sotrage is part of AWS Outposts and includes EBS and S3 options. Outposts provides the same infrastrcutures, services, APIs, and tools to any data center, colocation space, or on-prem facility.

  

### 3.2 Hybrid - On-prem gateways (AWS Storage Gateway)

AWS Storage gateway connects on-prem users and apps with cloud-based storage. **Ideal for:**

- Moving backups to the cloud
- using on-prem file shares back by cloud storage
- low-latency access to data in AWS for on-prem apps because local caching reduces network latency for read and write

AWS Storage Gateway has four offerings:

1) S3 File Gateway: stores data in as objects in S3, offers SMB and NFS access with local caching

2) Fsx File Gateway: optimizes on-prem access to file shares in Amazon FSx or Windows File Server

3) Volume Gateway: cloud-backed iSCI block storage volumes. It stores an manages data in S# on your behalf

4) Tape Gateway: replaces physical tapes on prem with virtual tapes in AWS.

  

# 3. Data Transfer and Migration Services

Services designed to copy or transfer on-prem data to and from AWS storage services in the cloud.

![[/Untitled 4 5.png|Untitled 4 5.png]]

### 3.1 File Transfer Services (AWS Transfer Family)

AWS Transfer fmaily provides fully managed support for file transfers in and out of S3 or EFS. It supports Secure File Transfer Protocol(SFTP), File Transfer Protocol over SSL (FTSP), and File Transfer Protocol (FTP)

  

### 3.2 DataSync (AWS DataSync) - Online Data transfer

An online data transfer services that is used for:

- migrating active datasets to AWS
- archiving data
- replicating data
- transfering data for analysis and processing

![[/Untitled 5 5.png|Untitled 5 5.png]]

### 3.3 Offline data transfer (AWS Snow)

Performed using AWS Snow family for physical data transfer.

  

### 3.4 Migration Services (AWS MGN)

AWS Appication Migration Service (AWS MGN) is an automated lift-and-shift (rehost) solution.

  

# 4. Data Protection

Services to meet data redundancy and disaster requirement needs.

![[/Untitled 6 4.png|Untitled 6 4.png]]

  

### 4.1 Backup and Archive (AWS Backup)

Allows you to centralize and automate data protections across multiple AWS services. AWS Backup helps you achieve compliance or business policies for data protections.

  

### 4.2 Snapshots

Snapshots create backup copies of your data and are built into most core services. Snapshots are stored on a protected part of S3 (provides 11 9’s of durability)

### 4.3 Replication

Storage replication is built in to some of the core storage services. **Replciation increases Availability**

  

### 4.4 Disaster Recovery (CloudEndure Disaster Recovery)

Recovery option for on-prem servers and apps. It continuous replicates your machines in your target AWS account and preferred Region.

# 5. Choosing the Right Storage Solution

Picking the right solution depends on characterstics such as:

- shareable
- file size
- cache size
- access patterns
- latency
- throughput
- persistence of data

To optimize storage, the first step is to understand the performance profile of the workload, input/output operations per second (IOPS), throughput, etc.

  

### 5.1 Questions to ask

- _How often and how quickly do you need to access your data?_
- _Does your data store require high IOPS or throughput?_
- _What storage access protocols are required?_
- _How critical (durable) is your data?_
- _How sensitive is your data?_
- _How large is your dataset?_
- _How transient is your data?_
- _How much are you prepared to pay to store the data?_