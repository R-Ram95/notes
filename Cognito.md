---
tags:
  - AWS
  - DevOps
  - Security
---
- Anytime we see hundreds of users, mobile users, SAML, we think Cognito instead of IAM

# Cognito User Pools (CUP)

- serverless DB of users for web & mobile applications
- simple login: Uname Pword
- pword reset
- email/phone verification
- MFA
- federated identities: Google, FB, SAML
- login sends back JWT

## Integrations

API Gateway and ALB

![[/Untitled 64.png|Untitled 64.png]]

# Cognito Identity Pools (Federated Identities)

- users outside of app env but they need access to our AWS env
- identities for users so they obtain temp AWS credentials
- cannot create normal IAM users b.c. they don’t scale
- Identity pool includes:
    - public providers
    - users in amazon (cognito user pool)
    - OpenID and SAML
    - developer authenticated identities
    - allows for guest policies

![[/Untitled 1 44.png|Untitled 1 44.png]]

![[/Untitled 2 35.png|Untitled 2 35.png]]

# User Pools vs. Identity Pools

User Pools:

- DB of users for web and mobile apps
- allow federated logins through Public Social, OIDC, SAML
- customize ui for login
- triggers for AWS lambda during auth flow

Identity Pools:

- obtain AWS creds for users
- login through Public Social, OIDC, SAML
- users authenticated as guests
- users are mapped to IAM roles and policies and you can use policy variables
- CUP + CIP = manage user / password + access AWS services

# Sync

- depracted ⇒ use AppSync
- store preferences, config, state of app
- Push Sync ⇒ silently notify accross all devices when identity data changes
- Cognito stream ⇒ stream data from Cognito into Kinesis
- Cognito Events ⇒ execute Lambda functions in response to events