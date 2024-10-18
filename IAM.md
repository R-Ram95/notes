---
tags:
  - AWS
  - DevOps
  - Security
---
# Identity and Access Management (IAM)

- IAM allows you to group **users** in your organization so that they have different permissions.
- IAM is a Global Service ⇒ does not depend on region
- Groups can only contain users, not other groups
- users don’t have to belong to a group but can belong to multiple groups
    
    ![[/Untitled 65.png|Untitled 65.png]]
    
- IAM permissions:
    - policies (in the form of JSON documents) are assigned to Users or Groups
    - apply the Least Privilege Principle ⇒ don’t give users more permissions than they need

## IAM Policies Inheritance

- All users inherit the policy for the group
- users without policy get an inline policy
    
    ![[/Untitled 1 45.png|Untitled 1 45.png]]
    
    ![[/Untitled 2 36.png|Untitled 2 36.png]]
    

## IAM Policy Structure (JSON)

- Version: policy language verion
- id: identifier for policy (optional)
- Statement: one or more individual statements (required)

Policy Statement:

- ==Sid: identifier of statement (optional)==
- ==Effect: Allow | Deny==

- *Principal: Account/User/Role to which policy is applied
- *Action: list of actions policy allows or denies
- *Resource: list of resources to which the actions are applied to
- *Condition: conditions for when this policy is in effect

## MFA Devices Options

- Virtual MFA Device ⇒ e.g. Google Authenticator (phone only), Auth(multi-device)
- Universal 2nd Factor Security Key (U2F) ⇒ physical device(e.g. YubiKey) ⇒ support multiple root and IAM users with a single security key
- Hardware Key FOB MFA Device ⇒ e.g. Gemalto
- Hardware key FOB MFA device for AWS GovCloud(USA) ⇒ e.g. SurePassID

## IAM Roles for Services

- some aws services need to perform actions on your behalf
- we need to assign permissions to AWS services with IAM roles

## IAM Security Tools

- IAM Credentials Report (account-level)
    - lists all account users and status of credentials
- IAM Access Advisor (user-level)
    - shows service permissions granted to a user and when they were accessed last

## IAM Best Practices

- don’t use root account except for AWS account setup
- One physical user = One AWS user
- assign users to groups and assign permissions to groups
- use MFA
- create and use roles for permissions for AWS services
- use access keys for programmatic access (CLI / SDK)

# STS - Security Token Service

- allows you to get temp access to AWS resource (up to 1 hour)
- API
    - **AssumeRole** = assume roles within account or cross account
    - AssumeRoleWithSAML: return creds for users logged with SAML
    - AssumeRoleWithWebIdentity:
        - return creds from users logged with an IdP
        - use Cognito instead
    - **GetSessionToken:** for MFA
    - GetFederationToken: temp reds from federated user
    - **GetCalledIdentity**: details about IAM user or role within API call
    - **DecodeAuthorizationMessage**: decode error message when API is denied

# Advanced IAM

![[/Untitled 3 34.png|Untitled 3 34.png]]

- Explicit Deny wins ⇒ evaluated first

## S3 Bucket Policy with IAM Policies

- S3 bucket has Bucket Policy
- IAM policy attached to users, roles, groups
- during evaluation, the UNION of the S3 policy and IAM policy are evaluated

  

# User Permissions to PASS a role to AWS Service

Used when we give AWS service permissions to access another AWS Service

- the user who passes the role needs iam:PassRole, also comes with iam:GetRole

## Can a role be passed to any service?

No, roles can only be passed to what their trust allows

trust policy for the role allows the service to assume the role