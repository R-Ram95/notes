---
tags:
  - AWS
  - DevOps
  - Networking
---
- provides HTTP endpoint and more for our application
- REST API
- Expose any AWS Service

# Endpoint Types

Edge-Optimized (default) - accessible from anywhere in the world

- requests routed through CloudFront Edge locations
- Gateway lives in one region

Regional: clients within the same region

- Create own CloudFront distrubution

Private: Only access through VPC using ENI

- use resource policy to define access

# Deployment Stages

- must make deployment for Gatway to be effective
- deployments are in stages
- stages can be rolled backed

## Stage Variables

- like env variables for API gateway
- use them to change often changing config variables

# Canary Deployment

- enable canary deployments for any stage
- choose % of traffic the canary channel receives

  

# Integration Types

MOCK

- Gateway returns response w.o. sending request to backend

HTTP / AWS Services

- configure request and response
- use mapping templates for request and response

AWS_PROXY

- incoming request is input to Lambda
- function responsible for logic of request / response
- No mapping template, headers, query string params passed as arguments

HTTP_PROXY

- no mapping template
- HTTP request passed to backend
- HTTP response forwarded from backend to Gateway

## Mapping Template

- used for HTTP or AWS services w.o. proxy
- allows us to modify requests
- filter the response
- e.g. can be used as “middle man” to convert a REST API to a SOAP API if the backend requires a SOAP API

![[/Untitled 9.png|Untitled 9.png]]

  

# Caching API responses

- on request, Gateway looks at Gateway cache, if cache miss ⇒ request to backend
- default TTL: 300 s, min: 0, max: 3600s
- cache capacity: 0.5 - 237GB

## Invalidating the Cache

- flush the cache entirely
- clients can invalidate with header: `Cache-Control:max-age=0`

  

# Usage Plans & API Keys

- make API available to customers for a cost
- Usage Plan:
    - who can access deployed API stages and method
    - how much and how fast they can use it
    - API keys to identify API clients
    - configure throttle limits, quota limits
- API keys
    - alphanumeric strings to distribute to customers
    - throttling limits applied to keys
    - quota limits is the overall number of max requests
- to configure:
    
    1) create one or more APIs, configure methods to require API key and deploy
    
    2) generate or import API keys to distribute to developers
    
    3) create the usage plan with desire throttle and quota limits
    
    4) associate API stages and keys with usage plan
    

# Logging and Tracing

CloudWatch Logs:

- logging at stage level
- override settings on a per API basis
- log contains info about request/response body

X-Ray

CloudWatch Metrics:

- `CacheHitCount` - efficiency of cache
- `CacheMissCount` - cache efficiency
- `Count` - requests in given period
- `IntegrationLatency` - how long backend takes to response
- `Latency` - time between how long API receives request to returning a response
- max time to response = 29seconds

## Errors

- `4XX` client side error
    - `400` - bad request
    - `403` - access denied
    - `429` - quota exceeded
- `5XX` server side error
    
    - `502` - bad gateway exception
    - `503` - service unavailable exception
    - `504` - integration failure
    
      
    

# CORS

- if you want to receive API calls from another domain
- Options pre-flight request must contain these headers:
    - Access-Control-Allow-Method
    - Access-Control-Allow-Headers
    - Access-Control-Allow-Origin
- CORS can be set through console

# Security

## IAM Permissions

- create IAM policy authorization and attach to User / Role
    - Authentication: IAM
    - Authorization: IAM Policy
- best way to protect API Gateway for services within API Gateway

![[/Untitled 1 2.png|Untitled 1 2.png]]

## Resource Policies

- allow you to set a JSON policy to define who and what can access API gateway
- Allow cross account access

## Cognito

- manage user lifecycle, token expire automatically
- Gateway verifies identity from Cognito
- Authentication: Cognito User Pools
- Authorization: API Gateway Methods
    
    ![[/Untitled 2 3.png|Untitled 2 3.png]]
    

  

## Lambda Authorizer

- Token based authorizer
- request based params to Lambda authorizer
- Lambda returns IAM policy
- Authentication: External
- Authorization: Lambda

![[/Untitled 3 3.png|Untitled 3 3.png]]

# Summary

IAM:

- for users / roles in AWS account ⇒ resource policy for cross account
- Authentication + authorization
- SigV4

Custom Authorizer:

- third party DB of users
- flexible in term of what IAM policy is returned
- Authentication verification + authorization in Lambda

Cognito User Pool:

- manage own user pool
- no need for custom code
- implement authorization in backend

  

# HTTP API

- low cost alternative
- no data mapping
- no usage plan and API key

# WebSocket API

- two way interactive communication between browser and server
- server can respond without request
- used in real-time applications
- works with AWS services or HTTP endpoints

## Connection to API

![[/Untitled 4 2.png|Untitled 4 2.png]]

- websocket URL: wss://[some-uniqueid].execute-api.[region].amazonaws.com/[stage-name]
- client connects
- client establishes persistent connection to API (connectionid)
- connectionid persisted in Dynam0DB

## Routing

![[/Untitled 5 2.png|Untitled 5 2.png]]

- routeselection expression tells you which backend to route to