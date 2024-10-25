
# Overview

Lambda@Edge is triggered by:

- Viewer Request ⇒ CF receives request from viewer
- Origin Request ⇒ Before CF sends request to Origin
- Origin Response ⇒ CF receives response from Origin
- Viewer Response ⇒ After CF sends response to Viewer


  

When you create a trigger, Lambda replicated the function to AWS locations:


# IAM Permissions

Lambda@Edge creates service-linked roles to replicate Lambda functions to CloudFront Regions and to enable CloudWatch to use CloudFront log files.

- `lambda:GetFunction`
    - Allows user to get config info for lambda and a presigned URL to download a .zip file containing the lambda code
- `lambda:EnableReplication*`
    - Allows Lambda replication service permissions to get function code and config
- `iam:CreateServiceLinkedRole`
    - allows user to create a service lined role used by Lambda@Edge to replicate functions in CloudFront

# Function Execution Role

Must create IAM Role that can be assumed by the service principals: [`lambda.amazonaws.com`](http://lambda.amazonaws.com) `edgelambda.amazonaws.com`. The role is assumed by service principals when they execute your function.

Add the role in Trust Relationship tab in IAM

```JavaScript
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": {
            "Service": [
               "lambda.amazonaws.com",
               "edgelambda.amazonaws.com"
            ]
         },
         "Action": "sts:AssumeRole"
      }
   ]
}
```

# Lambda@Edge Event Structure

Structure of object passed on CloudFront to Lambda

Viewer Request

```JavaScript
{
  "Records": [
    {
      "cf": {
        "config": {
          "distributionDomainName": "d111111abcdef8.cloudfront.net",
          "distributionId": "EDFDVBD6EXAMPLE",
          "eventType": "viewer-request",
          "requestId": "4TyzHTaYWb1GX1qTfsHhEqV6HUDd_BzoBZnwfnvQc_1oF26ClkoUSEQ=="
        },
        "request": {
          "clientIp": "203.0.113.178",
          "headers": {
            "host": [
              {
                "key": "Host",
                "value": "d111111abcdef8.cloudfront.net"
              }
            ],
            "user-agent": [
              {
                "key": "User-Agent",
                "value": "curl/7.66.0"
              }
            ],
            "accept": [
              {
                "key": "accept",
                "value": "*/*"
              }
            ]
          },
          "method": "GET",
          "querystring": "",
          "uri": "/"
        }
      }
    }
  ]
}
```

Origin Request

```JavaScript
{
  "Records": [
    {
      "cf": {
        "config": {
          "distributionDomainName": "d111111abcdef8.cloudfront.net",
          "distributionId": "EDFDVBD6EXAMPLE",
          "eventType": "viewer-request",
          "requestId": "4TyzHTaYWb1GX1qTfsHhEqV6HUDd_BzoBZnwfnvQc_1oF26ClkoUSEQ=="
        },
        "request": {
          "clientIp": "203.0.113.178",
          "headers": {
            "host": [
              {
                "key": "Host",
                "value": "d111111abcdef8.cloudfront.net"
              }
            ],
            "user-agent": [
              {
                "key": "User-Agent",
                "value": "curl/7.66.0"
              }
            ],
            "accept": [
              {
                "key": "accept",
                "value": "*/*"
              }
            ]
          },
          "method": "GET",
          "querystring": "",
          "uri": "/"
        }
      }
    }
  ]
}
```

See below for what the fields mean.

> [!info] Lambda@Edge event structure  
> The following topics describe the request and response event objects that CloudFront passes to a Lambda@Edge function when it's triggered.  
> [https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-event-structure.html](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-event-structure.html)  

# Generating HTTP Response in Request Triggers

Response object

```JavaScript
const response = {
    body: 'content',
    bodyEncoding: 'text' | 'base64',
    headers: {
        'header name in lowercase': [{
            key: 'header name in standard case',
            value: 'header value'
         }],
         ...
    },
    status: 'HTTP status code (string)',
    statusDescription: 'status description'
};
```

Status field is required

Description of fields:

[[lambdag]]

# Restrictions on Lambda@Edge Functions

- Function and CloudFront distribution must be owned by the same account
- must use numbered versions ⇒ cannot use `$LATEST`
- region must be `us-east-1` ⇒ N.Virginia
- cannot use lambda to access resources inside VPC
- no DLQs
- no Env Variables
- no Layers
- no X-Ray
- Supported runtimes:
    - Node.js 16
    - Node.js 14
    - Node.js 12²
    - Node.js 10¹
    - Node.js 8¹
    - Node.js 6¹

## Query Strings

- To access the query string in an origin request or origin response function, your cache policy or origin request policy must be set to **All** for **Query strings**.

# Authorization Lambda

Using Cognito

# Resources

> [!info] Authorization@Edge using cookies: Protect your Amazon CloudFront content from being downloaded by unauthenticated users | Amazon Web Services  
> Enterprise customers who host private web apps on Amazon CloudFront may struggle with a challenge: how to prevent unauthenticated users from downloading the web app's source code (for example, React, Angular, or Vue).  
> [https://aws.amazon.com/blogs/networking-and-content-delivery/authorizationedge-using-cookies-protect-your-amazon-cloudfront-content-from-being-downloaded-by-unauthenticated-users/](https://aws.amazon.com/blogs/networking-and-content-delivery/authorizationedge-using-cookies-protect-your-amazon-cloudfront-content-from-being-downloaded-by-unauthenticated-users/)  

https://github.com/aws-samples/cloudfront-authorization-at-edge

> [!info] cloudfront-authorization-at-edge/index.ts at master · aws-samples/cloudfront-authorization-at-edge  
> Protect downloads of your content hosted on CloudFront with Cognito authentication using cookies and Lambda@Edge - cloudfront-authorization-at-edge/index.  
> [https://github.com/aws-samples/cloudfront-authorization-at-edge/blob/master/src/lambda-edge/check-auth/index.ts](https://github.com/aws-samples/cloudfront-authorization-at-edge/blob/master/src/lambda-edge/check-auth/index.ts)
