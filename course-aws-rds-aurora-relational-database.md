# Relational Database Service (RDS)

RDS is a managed DB service that uses SQL. RDS Supports:

- Postgres
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- Aurora (AWS Proprietary database) - Cloud Native

**Database Instances:**

A database instance is the compute portion of RDS which consists of compute and storage. A DB instance can contain multiple databases with the same engine, and each DB can contain multiple tables. The DB instance is simply an EC2 instance.

**Storage:**

The storage portion using EBS (Elastic Block Store). Includes MySQL, MariaDB, PostgresSQL, Oracle, and SQL server. there are three storage types:

- General purpose (gp2 and gp3)
- Provisioned (io1)
- Magnetic (standard)

**RDS in a VPC**

DB instances live in a VPC and then you create subnets called DB subnet groups. Subnets in a DB group are private ⇒ no access to the internet. Access can be further restricted using NACLs and security groups.

**Backup data:**

Both Automated backups (point in time recovery) and manual snapshots (can retain data for longer than 35 days) should be used.

**Advantages:**

- Automated provisioning, OS patching
- Point in Time Restore
- Monitoring Dashboard
- Read replicas
- Multi AZ setup
- Maintenance windows for upgrades
- Scaling (horizontal and vertical)
- Storage backup by EBS
- Disadvantage:
    - cant SSH into b/c it is managed by AWS

## Storage Autoscaling


- increase storage dynamically
- automatic scaling when you are running out of free space
- Must set max
- modify storage if:
    - free storage < 10% allocated
    - low-storage lasts > 5 mins
    - 6 hours passed since last modification
- useful for unpredictable work loads

## RDS Read Replicas (Scaling)


- help scale read operations
- have up to 5 read replicas within AZ, Cross AZ or Cross Region
- Replication is ASYNC ⇒ eventually consistent
- can be promoted to their own DB
- Read Replicas are for SELECT (READ ONLY) statements

### Network Costs

- normally there are costs from one AZ to another
- same region different AZ = no cost
- cross-region = cost


  

### Multi AZ (Disaster Recovery)


- SYNC replication
- one DNS name ⇒ automatic app failover
- not used for scaling
- standby is like a backup
- Read Replicas can be setup as MultiAZ DR

### Single AZ to MultiAZ


- 0 downtime (no need to stop DB)
- just click “modify” for the DB

  

# Amazon Aurora

Aurora data is stored in cluster volumes, i.e. single virtual volumes that use SSDs. Cluster volumes contain copies of data across three AZ’s in a single AWS region. Aurora uses local storage for temp files.

- proprietary AWS
- Postgres and MySQL are supported
- AWS cloud optimized
- Automatically grows in increments of 10GB up to 128TB
- can have 15 replicas
- failover is instantaneous
- 20% more $$

## High Availability and Read Scaling


- writer Endpoint ⇒ provides connection to the correct master in case of failover
- reader end point ⇒ provides connection to the correct read replicas when using auto scaling
- load balancing happens at the connection level, not the statement level

# RDS & Aurora Security

- At-rest encryption:
    - encryption on the volumes
    - master and replicas encrypted using AWS KMS
    - master must be encrypted o/w replicas cannot
    - make SC and restore as encrypted to go from un-encrypted ⇒ encrypted
- in-flight
    - TLS-ready by default use the AWS TLS root certificates client-side
- IAM Authentication:
    - IAM roles to connect to DB instead of uname and pw
- Security Groups:
    - control network access
- no SSH except RDS custom
- Audit Logs can be enabled and sent to CloudWatch Logs for longer retention
