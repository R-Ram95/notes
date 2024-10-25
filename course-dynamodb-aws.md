# 1. Introduction

DynamoDB is a fully managed NoSQL DB that provides fast, consistent performance at scale. It is used for high-scalable apps and serverless apps. It is very useful for OLTP (online transaction provessing)

  

**Features**

- NoSql serverless DB
- flexible scaling
- good for horizontal scaling
    - only way to vertically scale RDBMS is to add CPU and RAM and IO
    - horizontal scaling with RDS by adding EC2 and Read replicas ⇒ these only give read access though
- NoSQL so it does not perform AVG, SUM, etc.
- fully managed, available with replication
- millions of requests per second, low latency

# 2. Basics

Data is stored in tables and each “row” in the table is an item. Each item must have a Partition key which is unique for each item. You can optionally have a sort key. The Partition and Sort key make up the primary key and must be unique from item to item.

## 2.2 Primary Keys

### 2.2.1 Partition Primary Key

- unique for each item
- diverse so data is distributed
- partition key is used for sharding which enabled horizontal scaling
- can be the primary key if not sort key is specified

### 2.2.2 Partition and Sort Key (Sort + Range)

- combo must be unique for each item and makes up the primary key
- grouped by partition key

** always choose the partition key with the most cardinality

## 2.3 Partitions

- data stored in partititons
- partition key through hash to determine which partition to go to
- \#of WCU and RCU are spread evenly across partitions

# 3. Consistency

The ability read data with the understanding that prior writes will be reflected in the read. Reads and can strongly or eventually consistent

## 3.1 Write

### 3.1.1 Provisioned mode

- set number of read/writes per second (RCU/ WCU)
- plan capacity before hand
- pay for what you provision
- burst capacity ⇒ exceed throughput temporarily
    - if you exceed burst ⇒ `ProvisionedThroughPutExceededException`
- retry using exponential backoff

### 3.1.2 On-demand Mode

- read/writes scale up and down automatically
- pay for what you use
- more expensive
- switch between the two every 24h

## 3.2 Read

### 3.2.1 Strongly Consistent Read

- we will for sure get the correct data
- consumes twice the RCU
- slightly higher latency

### 3.2.2 Eventually Consistent Read (Default)

- if we read right after we write ⇒ possibility to get stale data b.c. of replication

## 3.3 Read and Write Capcity Units

You specify read and write throughput when you create a table

### 3.3.1 Read Capcity Unit (RCU)

The number of strongly consistent reads/second of items up to 4KB in size.

1 RCU = 1 strongly consistent read per second = 2 eventually consistent read per second of item up to 4KB in size

- round up to the nearest 4KB


### 3.3.2 Write Capcity Unit (WCU)

The number of 1 KB writes per second

1 WCU = 1 write/s ⇒ for item up to 1KB in size

- round up for item size


## 3.4 Throttling

- exceed RCU or WCU ⇒ ProvisionedThroughputExceededException
    - hot keys
    - hot partition
    - large item
- solutions
    - exponential backoff
    - distribute partition keys evenly
    - DAX

# 4. Secondary Indices

These are used to perform queries on attributes that are not part of the tables primary key.

**Global Indices:**

- index across all partition keys
- can have partition and sort keys that are different from the keys of the table
- Can be created when a table is created or can be added to an existing table and it can be deleted
- eventual consitency only
- Queries or scans on this index consume capacity units from the index, not from the base table.
- Queries on this index support eventual consistency only.

**Local Indices**

- index is local to a partition key
- allows you to query items with the same partition key
- created when table is created
- supports eventual and strong consistency
- **Queries or scans on this index consume read capacity units from the tables provisioned throughput**
- For each partition key value, the total size of all indexed items must be 10 GB or less.

# 5. Streams

- ordered list of item-level modifications (create/update/delete)
- can be:
    - sent to Kinesis
    - read by Lambda
    - read by KCL
- retention up to 24hours
- own custom logic = processing layer
- can choose info to be written to stream
    - KEYS_ONLY
    - NEW_IMAGE - entire item after mod
    - OLD_IMAGE - entire item before mod
    - NEW_AND_OLD_IMAGES - both new and old image
- streams are made of shards ⇒ like Kinesis
- records are not populated retroactively ⇒ only records created after stream enabled will be in the stream
    
    

## 5.1 With Lambda

- need Event Source Mapping to read from Dynamo DB Streams
- need appropriate permissions
- Lambda invoked synchonously

# 6. DynamoDB Accelerator (DAX)

**Features**

- in-mem cache for DynamDB
- cache frequently accessed data ⇒ decrease read times
- Solves Hot Key Problem (too many reads)
- 5 min TTL for cache (default)
- 10 nodes in cluster
- Multi-AZ

**Use Cases:**

- Reduce response times of eventually-consistent read workloads by an order of magnitude
- Reduce operational and application complexity through a managed service that is API-compatible with Amazon DynamoDB
- Increase throughput for read-heavy or bursty workloads

## 6.1 DAX vs. ElastiCache

- DAX better for individual object cache
- query and scan cache
- if running complex filters or queries ⇒ better to store aggregation result in Elasticache


# 7. Design Considerations

**Uniform Workloads**

- Choose a partition key to distribute data in keys to prevent hot spots
- distribute reads and writes across multiple paritions

**Hot and Cold Data**

- store cold (rarely accessed) data in tables with lower throughput
- store hot (frequently accessed) data in tables with high troughput

**Manage Large Attributes**

- store large attribute values in S3
- comrpess large values before storing in DynamoDB
- break large attributes across multiple items

**Secondary Indicies**

- Local Secondary Index
    - uses tables provisioned throughput
- Gloabl Secondary Index
    - use eventually consistent read replicas

Use **Optimistic Locking with version number**

- ensure data you are reading has not changed since you last read it

  

# Optimistic Locking

- Conditional writes ⇒ write based on some condition
- strategy to ensure data hasn’t changed before writing to it
- each item has attribute that acts as a version

# TTL

- automatically delete item after expiry
- TTL must be a number
- does not consume WCUs

# CLI

- --projection-expressions: one or more attributes to retrieve
- --filter-expressions: filter items before returned to you
- pagination options
    - --page-size: get all data but in chunks (make many smaller API calls than one big one)
    - --max-items: max items to show in CLI ⇒ uses NextToken
    - --starting-token: specify last NextToken

# Transactions

- all-or-nothing operations (add/update/delete) to multiple items
- provides ACID
- Read Modes: Eventual, Standard, Transactional
- Write Modes: Standard, Transactional
- consumes 2x WCU and RCU
- `TransactGetItems` - one or more `GetItem` calls
- `TransactWriteItems` - one or more `PutItem, UpdateItem, DeleteItem` operations


# Session State Cache

- DynamoDB can be used to store session state
- vs Elasticache:
    - fully in mem ⇒ dynamo is serverless
    - both are key/value stores
- vs EFS:
    - store state on disk and share with EC2 instances
- vs. EBS
    - used for local caching, not storing cache
- vs S3
    - S3 meant for small objects
