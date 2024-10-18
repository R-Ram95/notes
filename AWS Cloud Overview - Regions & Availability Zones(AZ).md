---
tags:
  - AWS
  - DevOps
  - Fundamentals
---
## Region

- A **region** is a cluster of data centers
- AWS services are region-scoped
- **How to choose AWS Region?**
    - Compliance - regulations and legal requirements mean that data can never leave a region without explicit permission
    - Proximity - keep regions close to users to reduce latency
    - Available Services - new services are not available at all regions
    - Pricing - pricing varies region to region

## Availability Zone (AZ)

- regions are composed of AZ’s
- each region has 2-6 AZs
- each AZ is one or more data centers
- AZs are isolated from each other for redundancy
    
    ![[/Untitled 62.png|Untitled 62.png]]