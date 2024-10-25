# 1. EFS

EFS is a set-and-forget file system that grows and shrinks automatically as you add and remove files. It can be attached to thousands of compute instances at the same time. You only pay for storage that you use. Choose from two performance and two throughput modes.

**Features:**

- fully managed ⇒ NFS for Linux
- Available, durable (11 9’s), and scalable
    - With standard storage, you can access EFS from any AZ in a region
- Storage classes and Lifecyle Management
    - Standard IA and One Zone-IA are cost optimized for infrequently accessed files
- Elastic & Scalable
    - Throughput and IOPS scale as file system grows (over 10 GB/sec and over 500,000 IOPS)
    - EFS can grow to a PB in scale

**Storage Classes:**

EFS Standard and Standard IA - multi-AZ, highest level of durability and availabiliy

EFS One Zone and One Zone IA - cheaper but single AZ

**Performance Modes:**

choose from two performance and two throughput modes

- general purpose:
    - lowest latency
    - latency sensitive use cases (e.g. CMS)
- max I/O:
    - scale to high levels of throughput and IOPS
    - for highly parallel applications
    - only for standard storage classes

**Throughput Modes:**

- Bursting (1TB = 50MiB/s + burst up to 100MiB/s)
- Provisioned: set throughput regardless of size

**Use cases:**

- Persistent Storage for containers and serverless
    - EFS allows data to be persisted separately from compute
- move to managed file system
    - migrate from enterprise applications from on-prem to AWS cloud
- Analytics and ML
    - EFS integrates with SageMaker
- Web serving and CMS
- not meant for Windows

# 2. FSx

Fully managed service that offers reliability, security, scalability and a lot of capabilities

## 2.1 FSx - Lustre

For fast-storage to keep up with fast compute, highest level of throughput and IOPS. Lustre can be natively configured with S3 for low-cost storage


**Features:**

- POSIX compliant ⇒ can be used with Linux
- High, Scalable Performance
    - adds storage and increase performance linealy as you scale
    - uses SSD for serving meta data
    - uses SSD or HDD for serving data
- seamless access to data repos
    - native drive for Linux based EC2 and EKS
- fully managed
- cost optimized
    - provides short term scratch and long-term persistent processing options

  

**Use Cases:**

- Horizontal use cases, i.e. high performance to server more compute
    - examples include high performance computing and machine learning
- Vertical use cases, i.e. compute-intensive
    - life sceience genoimcs
    - financial models
    - design simulation

## 2.2 FSx - Windows File Server

Windows File Server for customers building and running windows applications. Windows File Server can be accessed by 1000s of computes using the SMB protocol.

## 2.3 FSx - NetApp ONTAP

NetApp ONTAP - similar to on-prem NetApp

for Linux, WIndows and macOS compute running on AWS

## 2.4 FSx - OpenZFS

OpenZFS - can move on-prem ZFS or other linux-based file servers to AWS without change code

for latency-sensitive small file workloads
