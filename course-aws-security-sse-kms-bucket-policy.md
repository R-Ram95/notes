# Overview


# S3 Encryption for Objects

Reminder, four methods of encrypting objects:

SSE-S3, SSE-KMS, SSE-C, Client Side

## SSE-KMS

- encryption using keys handled and managed by AWS
- encrypted server side
- To perform SSE-KMS:
    - KMS key policy that authorizes user / role
    - IAM policy that authorizes access to KMS
    - o.w. access denied error

## S3 Bucket Policies - Force SSL

- add Condition to bucket policy ⇒ Deny on this condition
    - `aws:SecureTransport:false`
    - DENY anything that is not using HTTPS

## S3 Bucket Policy - Force Encryption of SSE-KMS

- Deny incorrect encryption header
- deny when there is not encryption header

  

  

# SSM vs Secrets Manager

- Secrets Manager ($$$)
    - automatic rotation of secrets with AWS Lambda
    - Lambda fcn provided for RDS, Redshift, DocumentDB
    - KMS Encryption is mandatory
- SSM Parameter Store($)
    - Simple API
    - no secrets rotation
    - Encryption is optional
