---
tags:
  - AWS
  - Monitoring
---
Events

Retention

- Internal monitoring of API calls
- Audit changes to AWS Resources by your users
- Provides governance, audit and compliance for your AWS account
- Enabled by default
- allows you to get a history of events and API calls within your AWS account

  

# Events

Management Events - operations performed by AWS account

Data Events - S3 object level operations (get, put, delete)

CloudTrail Insight Events - tries to identify unusual activities

  

# Retention

- events stored in CloudTrail for 90 days
- store in S3 and use Athena to analyze past 90 days

![[Untitled.png]]