---
tags:
  - AWS
  - Storage
---
1. Snowball - Data Migration & Edge Computing

2. Snowcone

3. Snowmobile

4. Available Job types - Snowball & Snowcone

5. Relationship between device and Job type

# 1. Snowball - Data Migration & Edge Computing

A small, rugged, portable, secure data migration and edge computing device with two options: **Compute Optimized and Storage Optimized.**

  

**Features:**

- rugged and portable
- object and block storage
- fast data transfer
    - 10 - 100 Gbps
- OpsHub GUI to setup and manage
- GPU support
- Clustering
    - cluster multiple Snowball devices
- Encryption
- Secure Erasure
    - AWS erases data once transferred in accordance to NIST

**Use Cases:**

- Tactical edge computing
- Content distribution
- ML
- Manufacturing
- Remote locations with simple data

**Cost**

- free to transfer data in to AWS
- cost per region for data out of AWS

# 2. Snowcone

A rugged petabyte scale data transport device with onboard sotrage and compute. It has different models of AWS snowball edge devices.

**Features:**

- Small and light
- ruggedized enclosure
- OpsHub setup
- onboard compute
- offline data transfer ⇒ S3
    - device is physically shipped to you and back to AWS
- online data transfer ⇒ DataSync
- file based storage
- networking capable
- Power
- Encryption
- Secure Erasure
    - AWS erases data once transferred in accordance to NIST

**Use Cases:**

- Tactical edge computing
- Content distribution
- Transportation, logistics and autonomous vehicles
- IoT: Healthcase and Industrial

# 3. Snowmobile

A large truck to migrate exabyte-scale datasets into and out of AWS cloud.

  

# 4. Available Job types - Snowball & Snowcone

**Local compute and storage only** - choose this to perform compute and storage without data transferred into AWS

**Import into S3** - AWS ships an empty Snowball device to you for data collection and processing. Once returned, data is transferred uploaded to S3.

**Export from S3** - Export data from S3 into your device, AWS then ships the device to you.

![[/Untitled 39.png|Untitled 39.png]]

# 5. Relationship between device and Job type

Features of Snowball and Snowcone vary based on the device and job type.

![[/Untitled 1 25.png|Untitled 1 25.png]]