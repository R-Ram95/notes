---
tags:
  - AWS
  - DevOps
  - Serverless
---
- executes many small tasks in a workflow
- e.g. talk to API, execute Lambda
- Choice State: test for condition to send to a branch
    - parallel state ⇒ begins execution in parallel

# Error Handling

- Retry ⇒ retry failed state
- Catch ⇒ transition to failure path
- do this in State Machine and not App Code ⇒ makes app simpler
- Error Codes:
    - State.ALL ⇒ match any error name
    - States.Timeout ⇒ task ran longer than TimeoutSeconds
    - States.TaskFailes ⇒ execution failure
    - State.Permissions ⇒ not enough privallege

  

# Standard vs. Express

- standard ⇒ longer slower throughput
- express ⇒ shorter, faster throughput

# AppSync

- manage service that users GraphQL
- Real time WebSocket or MQTT
- for mobile apps: local data access & data sync

![[/Untitled 35.png|Untitled 35.png]]

## Security

- four ways to authorize apps to interact with AWS AppSync
    - API_Key
    - AWS_IAM
    - OPENID_CONNECT
    - AMAZON_COGNITO_USER_POOLS
- for HTTPS user Cloudfront in front of AppSync

# Amplify

- create mobile and web applications
- Elastic Beanstalk for mobile and web apps
- Important Features
    - Authentication
        - leverages Cognito
    - DataStore
        - Uses AppSync and DynamoDB