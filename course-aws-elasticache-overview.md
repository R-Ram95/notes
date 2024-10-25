
# 1. ElastiCache

Elasticache is a fully managed Redis and Memached distributed memorcy cache in the cloud. It offers low latency, in memory data stores. It supports two engines: Redi and Memached. Elasticache normally sits in between a DB and an application running on EC2.

**Features**

- helps reduce load from DBs for read-intensive workloads
- stateless
- involves heavy application code changes
- application can be stateless by writing session into ElastiCache

  

![[/Untitled 45.png|Untitled 45.png]]

![[/Untitled 1 30.png|Untitled 1 30.png]]

  

# 2. Redis vs Memcached

## 2.1 Redis

- **High availability, backup, read replicas**
- multi AZ with auto-failover
- has Read Replicas ⇒ scale reads with high availability
- Data durability
- backup and restore features
- supports complex data type
    
    ![[/Untitled 2 25.png|Untitled 2 25.png]]
    

  

## 2.2 Memcached

- **pure distributed cache where you can afford to lose data**
- for small and static data
- sharding
- no replications
- non persistent
- no backup and restore
- multi-threaded architecture
    
    ![[/Untitled 3 25.png|Untitled 3 25.png]]
    

## 3. Pricing

Pay for what you use.
