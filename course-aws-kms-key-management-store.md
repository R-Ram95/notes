  
- AWS manages encryption keys for us
- full integrated with IAM for authorization
- Can audit API calls to KMS key usage using CloudTrail

## Types of Keys

- Symmetric ⇒ one key to encrypt and decrypt
    - AWS services uses Symmetric
    - never get access to KMS key unencrypted ⇒ use API calls
- Asymmetric ⇒ public key to encrypt, Private Key to decrypt
    - dl public key but not private key
    - use case: encryption outside of AWS by users who can’t call the KMS API
- Three Types of KMS Keys
    - Managed key - free
    - Customer Managed Keys - created in KMS, not free
    - Customer Manage Keys Imported - must be 256-bit symetric key - not free
- pay for API calls to KMS - 3cents/10000 calls
- Automatic Key Rotation
    - managed KMS key - automatic every year
    - customer managed - automatic every year
    - imported KMS key - manual

## Copying snapshots across regions

- KMS keys are scoped to regions
- same key cannot live in the different regions

## KMS Encryption Patterns

- secrets file must be < 4KB
    - overcome with Envelope Encryption


## Envelope Encryption

- encrypt data >4KB
- `GenerateDataKey` api

  

## KMS Symmetric - API Summary

- Encrypt - encrypt up to 4KB of data through KMS
- GenerateDataKey - generated unique symmetric data key (DEK)
    - return plain text of data key
    - return an encrypted copy of data key using the CMK
- GenerateDataKeyWithoutPlainText
    - generate DEK to use at some point (not immediate)
    - DEK that encrypted under the CMK that you must specify
- Decrypt - decrypt up to 4KB of data
- GenerateRandomNumber

## KMS Limits
