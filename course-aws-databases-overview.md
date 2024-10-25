
# 1. History

**Relational DBS**

Use cases: Applications with fixed Schema or applications that need persistent storage

**Unmanaged:**

You are responsible for everything (power, host machines, db management, query optimization, etc.)

If you want to host on AWS, on an EC2 instance for example, you manage the EC2 instance, the DB on the host, optimizing queries, managing customer data. AWS is responsible for hardware and underlying infrastructure.

**Managed:**

AWS is responsible to setup the EC2 and DB, provide high availability, scalability, patching, and backup

You are responsible to ensure customer data is secure, query optimization

  


# 2. Choosing the right DB

**Use case:** Read-heacy OLTP ⇒ **RDS**

- read replica is asynchronously updated
- read replica make it easy to scale out for read-heacy workloads
- more read replicas = more capicity to server client
    
    

  

**Use case:** Media Streaming ⇒ **Elasticache**

- fast in-memory data store
- can store meta-data for userprofiles and viewing history
    
    

**Use case:** Gaming Application ⇒ **DynamoDB**

- automatically scales to millions of concurrent users and requests while maintaing low latency
    
    

  


# 3. Terms

**Relational** - has a schema, think SQL

**Key-value** - store and retrieve key-value pairs in large volumes

**Document** - data is represented as a readable document

**Graph** - for querying and navigating relationships between highly connected DBs

**In-memory** - for read-heacy compute-intensive applications that need low-latency access to data

# 4. Server-based Architecture

The server hosts, delivers, and manages the services. You are responsible for the servers. Scaling the application is costly. Two services that can be used in server-based are: RDS and EC2

# 5. Serverless Architecture

Applications are hosted by a third party service, the developer does not need to manage servers. Examples include DynamDB and Aurora Serverless
