---
tags:
  - AWS
  - DevOps
  - Serverless
---
- Write YAML files to template how application behaves
- develop and deploy applications using YAML
- compatible with CloudFormation
- two commands:

# Recipe

- Transform Header: indicates its a SAM template
- code:
    - AWS::Serverless::Function, etc.
- Package and deploy
    - aws cloudformation package / sam package
    - aws cloudformation deploy / sam deploy
- Work flow:
    
    - Sam Build - transform SAM template to Cloudformation template
    - Sam Package OR cloudformation package - zip and upload package to S3
    - Same Deploy OR cloudformation deploy - create / execute ChangeSet
        
        ![[/Untitled 67.png|Untitled 67.png]]
        
    - running lambda locally: SAM CLI