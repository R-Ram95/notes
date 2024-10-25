---
tags:
  - AWS
  - Storage
---

Storage gateway is a hybrid solution that connects an on-prem appliance with cloud-based storage.

Overview of the process


# 2. Types of Storage Gateways


## 2.1 **S3 File Gateway**

Data is stored as objects in S3 which is highley durable and cost efficient.

- Supports NFS or SMB

You can store files as objects on S3 using the NFS and SMB file protocols.

## 2.2 **FSx File Gateway**

Optimizes on-prem access to Windows file shares on Amazon FSx. Data is stored as files natively rather than objects. It is a solution for replacing NAS (Network Attaches Storage).

- SMB Only

## 2.3 **Tape Gateway**

- iSCSI
- replaces physical tape

## 2.4 **Volume Gateway**

You can create block storage volumes and mount them as iSCSI devices from on-prem or EC2 app servers. Runs in cached mode (data written to S3 while retaining frequently accessed data locally in a cache) or stored mode (data is stored locally and entire dataset is available for low-latency access).

- iSCSI
    - this cannot be used to store files
- block storage volumes with snapshots
- used in conjunction with Windows and Linux file servers
- backup and disaster recovery
- data migration

## 2.5 Choosing right file Gateway

- S3 File Gateway → data stored as objects
    - supports: data lakes, backups, ML workflows
- FSx File Gateway → data stored natively as file data
    - used to replace on-prem Network Attached Storage

# 3. Features

- standard storage protocols
- fully managed cache
- optmized data transfer
- native aws integration
- high availability on VMware

# 4. Use Cases

- low-latency access for on-prem apps to the cloud
- use on-prem file shares backed by cloud storage
- move backups to the cloud
- data protection and disaster recovery

# 5. Pricing

You pay for what you use. Charged based on amount of data transferred, type and amount of storage used, and requests made. If the deployed solution uses a hardware appliance, you pay for that as well.

**Storage**

- fee based on type (e.g. S3 vs. EBS)

**Requests**

- fees based on data ops including data ingest into AWS

**Data transfer**

- fee based on data transferred out of Storage Gateway and into storage gateway appliance

# 6. S3 File Gateway Deep Dive

## 6.1 Deploying the Solution - S3 File Gateway

Two components to deploy

1) gateway appliance (on-prem component)

- depends on storage soluton and where deployment happens

2) cloud component

**Deploying the Gateway Appliance:**

**On-prem**

- download VM appliance on deploy it on:
    - VMware ESXi Hypervisor
    - Microsoft Hyper-V
    - Linx Kernal-based Vitual Machine (KVM)
- purchase the hardware appliance

**On-AWS**

- deploy it as an AMI on EC2

**Connecting Appliance to AWS Service**

**Public Endpoint**

- appliance connects to public endpoint over internet

**VPC Endpoint**

- appliance connects to VPC endpoints over a private connection to AWS (Direct connect or AWS Virtual Private Network)

**Federal Information Processing Standards (FIPS)**

- appliance connects to public endpoint over the internet which complies with FIPS standards

**Configuring Local Disks (Cache)**

You need to allocate and configure a local disk if you are using the VM for storage gateway so that Storage Gateway knows which device to use for caching. Larger cache = lower retrieval cost because you can cache more data locally rather than getting it from AWS.

- NOTE: a high-performance disk (SSD NVMe or virtual disk) results in better throughout and more input/output

**You can choose S3 storage type:**


**Adding file shares to the gateway Appliance**

You can create file shares that can be mounted on Linux or Windows servers. SMB for Windows, NFS for Linux. A file gateway can host one or more file shares of different types:


# 6.2 How S3 File Gateway Reads, Writes, Updates

### 6.2.1 Reads

Check if data is in cache → if no it is fetched from S3 → data no stored in cache and provided to client.

  

File Gateway performas optimizations, i.e. it tries to predict patterns and do pre-fetching and read-aheads

### 6.2.2 Writes

Client writes data to gateway appliance → appliance stores data locally → data compressed async → changed data is uploaded securiley

  

Data transfer is optimzed using multi-part parallel uploads

### 6.2.3 Write Updates

To reduce data transfer overhead, the gateway uses multipart uploads and _copy put_, so **only changed data in your files is uploaded to Amazon S3**.

## 6.3 Security for S3 Gateway

**Granting permissions:**

- use IAM roles and policies to control permissions to S3
- Storage gateway needs permission to upload and retrieve files from an S3 bucket
- Actions - storage gateway defines these and they can be used to grant permission for specific API ops

**Protecting data**

- data in cache is not encrypted
- data in transit and in the cloud is encrypted
    - in transit - SSL/TLS
    - in S3 - AWS-256
        - SSE-S3 - server-side encryption
        - AWS KMS

**Access to the file share**

NFS:

By default, any client on your network can mount to your file share, configure NFS file share with admin controls:

- limit by IP specific to NFS clients or networks
- permit RO or RW
- activate permission squasing

SMB:

- limiting access for Active Directory users only
- File share visibility: RO or RW
- control access using POSIX

**Access to S3**

Controll access using:

- IAM policies that specify users that can access buckets and objects
- use bucket policies
- use S3 Block Public Access to limit public acces
    - this setting overrides bucket policies and object permissions
- restrict specific actions by activating object lock

# 7. Volume Gateway - Deep Dive

- block storage data is stored in AWS S3 service bucket (not customer bucket)
- manage data stored through EBS snapshots


**How it works:**

- You create gateway storage volumes
- the volumes are mapped to on-prem DAS or SAN disk
- mount the disks to on-prem app servers as iSCSI devices
- clients (initiators) send SCSI commands to storage devices (targets) on remote servers

  

**Cached Volume:** deployed into on-prem or AWS cloud env as VM (running on VMware ESXi, KVM, Hyper-V hypervisor, EC2, physical gateway appliance)

- S3 = primary data store
- all data stored in S3, frequently accessed data in local cache
- Cache volume size = 1 GiB - 32 TiB
- Max Storage = 1 PiB (32 volumes)
- Use Case: Custom File Share or Transitioning to EC2

  

Stored Volume7 is only for on-prem host platform. Data is stored on-prem and snapshots are stored on S3

- all of the data is stored locally but backed up to S3
- volume data range = 1 GiB - 16TiB
- each gateway can have 32 volumes ⇒ 0.5 PiB total storage
- use Cases: block storage, migrations or phased migrations, cloud-based recovery

**Cache storage** - volume gateway saves data to cache storage (low-latency access). Requires Cached Mode

**Upload Buffer** - staging area before gateway uploads data to S3. Required by Cache and Storage Volume modes

**Volumes as EBS Snapshots** - Incremental backups that can be used to restore on-prem gateway volume

  


  

**Features and Limitations:**

- Can use AWS Backup to manage and automate snapshots for Volume Gateway
- Does not support volume resizing
- Stored mode is better if you require entire data set (e.g. virus scans)
- Snapshot data is stored in S3 Service Bucket and is therefore only visible from the EBS and Storage Gateway management console

  

## 7.1 Pricing

- pay for what you use
- charged based on amount: Storage, Requests, Data Transfer

  

## 7.2 Deployment

On-Prem:

- download VM and deloy to:
    - VMWare EsXi Hypervisor
    - Microsoft Hyper-V
    - KVM
- purchase hardware appliance from AWS

On AWS:

- Deploy AMI to EC2
- Cache Volume is supported (Stored volume only for on-prem)

## 7.3 Reads, Writes, Updates

Data transferred between AWS and Storage Gateway is compressed and encrypted using SSL.

### 7.3.1 Volume Reads

Cached Volume: (read-through caching)

- if data is in cache, it is served locally
- if data not in cache, Storae Gateway retrived compressed data from S3, decompresses it, and stores in the local cache

Stored Volume:

- all data is stored locally so no need for caching → reads come direction from virtual appliance or SAN from the local disk

### 7.3.2 Volume Writes

Cached Volume:

- data gets stored in local volume cache in its native format
- Volume Gateway compressed and encrypts the data as it moves it from cache to upload buffer
- write-back → write ops are directed to cache and completion is acknowledged in the app

Stored Volume:

- writes happen to the disk and are immediately acknowleged locally

## 7.4 Optimizing Costs and Performance

**Volume and Snapshot management:**

Deleting Older Snapshots

- Billed for only amount of data stored on volume (not the size of the volume)
- delete older volumes and snapshots that are no longer needed
- deleting volumes do not automatically delete snapshots
- only data referenced by the exclusively by the snapshot is deleted

**Performance Recommendations:**

Optimize iSCSI Settings

Achieve higher I/O by choosing 256 KiB for `MaxReceiveDataSegementLength` and `FirstBurtLength` and 1 MiB for `MaxBurstLength`

Back gateway virtual disks with separate physical disks

- do not provision local disks for upload buffer and cache storage using the same underlying physical disk

Change Volume Config

- if adding more volumes to a gateway reduced throughput, consider adding volumes to a separate gateway

## 7.5 Secure and Monitor Gateway

### 7.5.1 Granting Permissions

- The Volume Gateway needs to authenticate to AWS to write volume data to S3
- AWS Creds must have access to Gateway File Share, Volume or Tape
- Needs access to AWS S3 and EBS

### 7.5.2 Monitor and Alerting

**CloudWatch**

- monitor performance of Gateway
- Metrics

|   |   |
|---|---|
|**Metric**|**Description**|
|AvailabilityNotifications|Number of availability-related notifications sent by the volume.|
|CacheHitPercent|Percent of application read operations from the volume that are served from cache.|
|CachePercentDirty|The volume's contribution to the overall percentage of the gateway's cache that isn't persisted to AWS.|
|CachePercentUsed|The volume's contribution to the overall percent use of the gateway's cache storage.|
|CloudBytesUploaded|The total number of bytes that the gateway uploaded to AWS during the reporting period.|
|CloudBytesDownloaded|The total number of bytes that the gateway downloaded from AWS during the reporting period.|
|HealthNotifications|The number of health notifications sent by the volume.|
|IoWaitPercent|Percent of time that the gateway is waiting on a response from the local disk.|
|ReadBytes|The total number of bytes read from your on-premises applications in the reporting period.|
|WriteBytes|The total number of bytes written to your on-premises applications in the reporting period.|

- Log Groups
    - monitor when Gateway encounters and error
- Alarms
    - notifications for changes in workload

**CloudTrail**

- captures API calls for Storage Gateway events
