
# 1. Introduction

Amazon S3 provided object level storage. S3 stores objects in a flat, non-hierarchal structure, and objects are stored in resources called buckets. S3 supports parallel requests so it can scale by the factor of your compute. S3 supports 3500 add requests/s and 5500 retrieve requests.

![[/Untitled 59.png|Untitled 59.png]]

**Buckets**

- store files (objects) in buckets (directories)
- globally unique name across all regions and all accounts
- **defined at the REGION LEVEL**

**Objects**

- they have a key
- key is the full path
    
    **s3://my-bucket/**==**my_file.txt**==
    
    **s3://my-bucket/**==**my_folder1/another_folder/my_file.txt**==
    
- composed of ==prefix== + ==object name==
    
    **s3://my-bucket/**==**my_folder1/another_folder**==**/**==**my_file.txt**==
    
- no concept of directories
- values are the content of the body
- meta data
- tags
- version id

# 2. Storage Classes

**S3 Lifecycle policy**

Lifecycle policy can be used to transition data between storage classes based on access patterns.

- transition objects from storage classes using life-cycle rules
- Transition actions - transition objects from storage classes
    - move objects to Standard-IA after 60 days after creation
    - Move to glacier for archiving after 6 months
- Expiration actions - configure objects to expire (delete) after some time period
    - access logs deleted after 365 days
    - delete old versions of files
    - delete incomplete mulit-part uploads

## 2.1 Frequently and Infrequently accessed data

### 2.1.1 S3 Standard

- 99.99% availability
- frequently accessed data
- low latency, high throughput
- sustain 2 concurrent facility failures
- use cases: Big data, mobile & gaming, content distribution

### 2.1.2 S3 Infrequent Access (IA)

- lower cost than standard
- for data that is not accessed frequently
- 99.9% availability
- use cases: backup and disaster recovery
- 3 AZ

### 2.1.3 S3 One Zone-IA

- high availability for a single AZ but data is lost when AZ is destroyed
- 1 AZ
- 99.5% availability
- use cases: store backup for on-prem data or data that is easily recreated

### 2.1.4 S3 Intelligent-Tiering

- monthly and auto-tiering fee for ability to automatically move objects between access tiers based on usage
- no retrieval charges
- frequent access = default tier, automatic
- infrequent access = object not accessed for 30 days, automatic
- archive instant access = objects not accessed for 90 days, automatic
- archive access = object not accessed for 90-700+ days, optional
- deep archive = objects not accessed for 180-700+ days, optional

## 2.2 Archive Data

The glacier types offer low cost object storage for archiving / backup. Pricing is based on storage and object retrieval.

### 2.2.1 S3 Glacier Instant Retrieval

- ms retrieval good for data accessed once a quarter
- min storage = 90 days

### 2.2.2 S3 Glacier Flexible Retrieval

Retrieval time is minutes to hours:

- expedited = 1-5 mins
- standard = 3- 5 hrs
- bulk = 5-12 hrs

The minimum storage = 90 days

### 2.2.3 S3 Glacier Deep Archive - LONG TERM STORAGE

Retrieval time is up to 12 hours.

- standard = 12 hours
- bulk = 48 hours
- min storage = 180 days

  

# 3. Features

- Storage management
    - range of ways to categorize data: bucket names, prefixes, object tags, S3 inventory
- Version Control
- Replication
- Retention and Compliance
- Storage Monitoring

## 3.1 S3 Replication

- must enable versioning
- buckets can be in different AWS Accounts
- copying is ASYNCHRONOUS ⇒ happens in the background
- must give proper IAM permissions to S3

Two types:

1) Cross Region Replication (CRR)

2) Same Region Replication (SRR)

NOTES:

- only new objects are replicated after enabling replication
- use S3 Batch Replication to replicate existing objects
- DELETE operations
    - replicate delete markers
    - Deletions with version ids are not replicated to avoid malicious deletes
- Cannot chain replications

## 3.2 Moving Between Storage Classes

![[/Untitled 1 40.png|Untitled 1 40.png]]

- infrequently accessed - **Standard-IA**
- for archived objects that you DONT need fast access to - **Glacier or Glacier Deep Archive**

# 4. S3 Storage analytics and insights

## 4.1 Storage Lens

Provides organization wide visibility into object storage usage and activity trends. Metrics describe size, quantity and characteristics of storage.

## 4.2 Storage Class Analysis

Storage class analysis helps to analyze acces patterns to help you when to transition between storage classes.

  

# 5. S3 Access Control & Security

## 5.1 Access Management

### 5.1.1 User Based - Identity and Access Management

This is where you create users to manage their access permissions. Specifies which API calls should be allowed for a specific user from IAM. NOTE: an IAM principal can access S3 object if: the user IAM permissions allow it OR the resource policy allows it AND there are no DENY

![[/Untitled 2 31.png|Untitled 2 31.png]]

![[/Untitled 3 30.png|Untitled 3 30.png]]

### 5.1.2 Access Control List

This is where individual objects accesible to authorized users

### 5.1.3 Resource Based - S3 Bucket Policies

The are bucket wide rules from the S3 console.

![[/Untitled 4 21.png|Untitled 4 21.png]]

Policy Structure:

![[/Untitled 5 15.png|Untitled 5 15.png]]

Resources: buckets and objects

Effect: “Allow” or “Deny”

Actions: set of API to allow or Deny

Principal: the account or user to apply policy to

### 5.1.4 Access Points

![[/Untitled 6 12.png|Untitled 6 12.png]]

- create layer on-top of S3 bucket and attach policies that grant r/w access to a specific prefix
- simplifies bucket policies ⇒ one policy per access point
- access points get their own DNS and policy

### 5.1.5 Pre-Signed URLs

- gives users temporary accesse to GET / PUT files in a private S3 Bucket
- URL Expiration
    - S3 console - 1 min to 12 hours
    - AWS CLI 3600s - 168 hrs
- users with pre-signed urls inherit permissions of the user that generated the URL
- use cases:
    - allow only logged in users to dl a premium video from S3
    - allow an ever-changing list of users to dl files by generating urls dynamically
    - allow temporarily a user to upload a file to a specific location in S3

## 5.2 On-prem Connectivity

Use VPC endpoints to connect to S3 resources from Amazon VPC and from on-prem environment. Amazon PrivateLink for S3 provides private connections from S3 to on-prem.

## 5.3 Encryption

Encrypt S3 buckets using:

Server-side Encryption (SSE)

1) Amazon S3 managed keys (SSE-S3)

- use encryption keys owned and managed by AWS

2) KMS (SSE-KMS)

3) Customer provided keys (SSE-C)

- customer provides key

Client-side Encryption (CSE)

- data is encrypted (on client side) before it is uploaded to S3

** Important to know when to use each **

### 5.3.1 SSE-S3

![[/Untitled 7 8.png|Untitled 7 8.png]]

- encrypted server-side
- type: AES-256
- set header: `"x-amz-server-side-encruption":"AES256"`

### 5.3.2 SSE-KMS

![[/Untitled 8 7.png|Untitled 8 7.png]]

- manage our own keys using KMS
- advantages: user control, audit key using CloudTrail
- encrypted server-sde
- header: **`"x-amz-server-side-encryption": "aws:kms"`**
- limitations: need to make API calls (GenerateDataKey) using KMS API for each data upload ⇒ might reach throttling limit based on # of requests
    
    ![[/Untitled 9 5.png|Untitled 9 5.png]]
    

### 5.3.3 SSE-C

- keys managed outside of AWS
- key must be sent to AWS
- must use HTTPS and key is provided in the HTTP header for every request
    
    ![[/Untitled 10 2.png|Untitled 10 2.png]]
    

## 5.4 Block Public Access

This ensures that S3 objects do not have public access.

## 5.5 Access Analyzer for S3

A feature that monitors your bucket access policies to ensure that policies provide the intended access to S3 resources.

## 5.6 Amazon Macie

Used to discover and protect senstive data stored in S3.

  

# 6. S3 Data processing and Query

## 6.1 Object Lambda

- use AWS Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed ⇒ create S3 AP ontop of S3 bucket and an S3 object AP to access the lambda

![[/Untitled 11 2.png|Untitled 11 2.png]]

## 6.2 Query in place

You can run analytics directly in S3, i.e. you do not need to copy and load it into a separate data warehouse. S3 is compatible with Amazon Athena and Amazon Redshift Spectrum:

- Athena: Queries data in S3 without needing to extract and load it to a different platform. It uses SQL.
- RedShift: runs SQl queries against data at rest in S3.

# 7. Use Cases

- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud storage
- Application hosting
- Media hosting
- Data lakes & big data analytics
- Software delivery
- Static website

  

# 8. S3 Event Notifications

- can send events from S3 based on, e.g. S3:ObjectCreated, S3:ObjectDelete, etc to different AWS services
- the 4 services are: **SNS**, **SQS**, **Lambda**, and **EventBridge**

  

# 9. S3 Performance

## Baseline Performance

- S3 automatically scales to high request rates and latency (100-200ms)
- 3500 PUT/COPY/POST/DELETE per second per prefix
- 5500 GET/HEAD per second per prefix

## Improving Performance

Multi-part upload:

- parallelize upload ⇒ decrease time to upload
    
    ![[/Untitled 12 2.png|Untitled 12 2.png]]
    

S3 Transfer Acceleration:

- transfer file to AWS Edge Location
- forward data to target region
    
    ![[/Untitled 13 2.png|Untitled 13 2.png]]
    

Parallelize GET:

- request specific byte ranges
    
    ![[/Untitled 14 2.png|Untitled 14 2.png]]
    

Retrieve only partial Data:

e.g. head of a file

![[/Untitled 15 2.png|Untitled 15 2.png]]

  

  

# 10. CORS

- Cross-Origin-Resource-Sharing
- origin = scheme (protocol) + host (domain) + port
    - same origin:
        
        **http://example.com/app1 & http://example.com/app2**
        
    - different origin:
        
        **http://www.example.com & http://other.example.com**
        
- request not fulfilled if other origin does not allow requests
- must enable cross origin requests

![[/Untitled 16 2.png|Untitled 16 2.png]]
