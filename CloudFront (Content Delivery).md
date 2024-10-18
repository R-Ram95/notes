---
tags:
  - AWS
  - DevOps
  - Networking
---
- improves read performance because content is cached at edge locations closer to the where the user is trying to access content

# CloudFront Origins

- S3 buckets
    - distributing files and caching them at the edge
    - enhance security with CloudFront Origin Access Identity (OAI)
        - ensures that CloudFront can access S3 bucket
        - OAI is an IAM role for CloudFront
    - CloudFront can be used to upload files to S3
- Custom Origin
    - must be HTTP
    - Can be:
        - ALB
        - EC2 Instance
        - S3 website (must be static)
        - any HTTP backend

S3 as origin:

![[/Untitled 38.png|Untitled 38.png]]

- OAI is an IAM role for cloudfront

EC2 as Origin:

![[/Untitled 1 24.png|Untitled 1 24.png]]

- traffic traverses security group around EC2
- EC2 must be public
- EC2 security group must allow ip addresses of edge locations

![[/Untitled 2 20.png|Untitled 2 20.png]]

- security group around ALB
- ALB must be public
- ALB security group must allow ips of edge locations

# Geo Restrictions

Whitelist: approved list of countries that users are from that can access content

Black List: prevent users from accessing content if they are in the countries in this list

# CloudFront vs. Cross Region Replication

CloudFront:

- global edge network
- files cached for TTL
- great for static content that must be available everywhere

Cross Region Replication:

- set up for each region that replication will happen
- files updated near real time
- read only
- for dynamic content that needs to be available at low-latency

# Caching

Cache based on:

- headers
- session cookies
- query params

- Goal: maximize cache hit rate to minimize requests to origin
    - control the TTL
    - invalidate part of the cache using the `CreateInvalidation` API
- Maximize cache hits by separating static and dynamic distributions
    - use different distributions of CloudFront
        
        ![[/Untitled 3 20.png|Untitled 3 20.png]]
        

  

# Security

- Geo Restrictions
    - Whitelist vs. blacklist
- HTTPS
    - Viewer Protocol Policy
        - redirect HTTP to HTTPS
        - or use HTTPS only
    - Origin Protocol Policy (HTTP or S3)
        - HTTP only
        - or match Viewer
    - NOTE: S3 bucket websites don’t support HTTPS

# Signed URL / Signed Cookies

Use Case: distribute paid shared content to premium users over the world

- use CloudFront Signed URL / Cookie ⇒ attach policy with:
    - URL expiration
    - IP ranges to access data from
    - trusted signed (which AWS accounts can create urls)
- signed url = access to individual files
- signed cookies = access multiple files

CloudFront Signed URL vs S3 Pre-signed URL

CloudFront:

- access to a path no matter the origin (S3, EC2, ALB)
- account wide key-pair ⇒ only the root can manage it
- filter by IP, path, data, expiration
- leverage caching features

S3 Pre-Signed:

- issue request as person who presigned URL
- uses IAM key of signing IAM principal
- limited liftetime