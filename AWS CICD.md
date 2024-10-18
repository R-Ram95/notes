---
tags:
  - AWS
  - DevOps
---
![[/Untitled 66.png|Untitled 66.png]]

# Continuous Integration (CI)

![[/Untitled 1 46.png|Untitled 1 46.png]]

- developers push code to repo (Github, CodeCommit, etc.)
- testing / build server checks the code
- developer gets feed back
- finds bugs early

# Continuous Delivery

![[/Untitled 2 37.png|Untitled 2 37.png]]

- software released reliably
- deployments happen often and quickly
- automated deployment (CodeDeploy, Jenkins CD, Spinnaker, etc.)

# CodeCommit

- Like Github
- supports SSH keys and HTTPS Git

# CodePipeline

Visual workflow to orchedstrate CICD. It consists of stages and each stage can have sequential and/or parallel actions. Each stage can create artifacts that are stored in S3 buckets. For example, CodeBuild does not need access to CodeCommit, it simply needs access to the S3 bucket that the code is stored in as an output from CodeCommit:

![[/Untitled 3 35.png|Untitled 3 35.png]]

* pipelines require an IAM role attached to them

* stages have multiple action groups

  

  

# CodeBuild

Allows you take take source code from code repo and build it using build instructions (`buildspec.yml`) which lives at the root of the code OR you could insert it manually using the console.

- by default it runs outside of the VPC ⇒ cannot access resources inside VPC
- can allow using VPC configurations

## How it works

![[/Untitled 4 24.png|Untitled 4 24.png]]

- CodeBuild pulls the `buildspec.yml` file from CodeCommit
- CodeBuild runs instructions from `buildspec.yml`
- pulls docker image (if required)
- caches if steps are lengthy
- store artifacts in S3 bucket
- stores logs in bucket or CloudWatch logs

## buildspec.yml

- must be at root of code
- env ⇒ define environment variables
    - variables ⇒ in plain text
    - parameter-store ⇒ variables in SSM param Store
    - secrets-manager ⇒ secrets stored in AWS Secrets Manager
- phases - commands to run
    - `install` ⇒ dependencies
    - `pre_build` ⇒ finals commands before build
    - `Build` ⇒ actual build commands
    - `post_build` ⇒ finishing touches
- `artifacts` ⇒ what to upload to S3 (encrypted with KMS)
- cache ⇒ what files to store in the cache

# CodeDeploy

Scenario: want to deploy application on EC2 instances not managed by Elastic Beanstalk

- can use open source (Terraform, Chef, etc.)
- can use AWS CodeDeploy

Rollback = redeploy a previously deployed versions

Automatically ⇒ rollback when deployment fails or CloudWatch alarm is triggered

Manually

The last known good version is redeployed (not a restored version)

## Steps to make it work

- EC2 instances or on-prem servers ⇒ MUST USE CodeDeploy Agent

![[/Untitled 5 18.png|Untitled 5 18.png]]

1) developer pushes code to code repo

2) trigger CodeDeploy

3) CodeDeploy agents continuously poll CodeDeploy for work ⇒ work to be done once CodeDeploy is triggered

4) App + `appspec.yml` (deployment instructions) are pulled from repo by the EC2 instance or on-prem servers

5) CodeDeploy Agent reports success/failure

## Components

- Application - functions are a container
- Compute Platform - EC2/ On-prem, Lambda, ECS
- Deployment Config - rules for deployment success/failure
    - EC2/ On-prem - specify min number of healthy instances
    - Lambda/ ECS - specify how traffic is routed to updated versions
- Deployment Group - group of tagged EC2 instances (dev, test, prod)
- Deployment Type - method used to deploy app to Deployment Group
    - In-place - supports EC2/On-prem
    - Blue/Green - EC2, Lambda, ECS only
- IAM Instance Profile - give EC2 permissions to access S3 / code repo
- Application Revision - app code + `appspec.yml`
- Service Role - IAM role for code deploy to perform operations on EC2 instances, ASGs, ELBs, etc.
- Target Revision - most recent revision to deploy to Deployment Group

  

## Deploy to EC2

Define how to deploy using `appspec.yml`. Uses in-place deployment

## Deploy to ASG

In-Place:

- updates existing EC2 instances (does not add new ones)
- Instances created by ASG will get the deployments

Blue/Green Deployment

- new ASG created
- choose how long to keep old ASG
- must use EL

  

# CodeStar

Integrated solution that groups GitHub, CodeCommit, CodeBuild, CodeDeploy, CloudFormation, CodePipeline, CloudWatch

# CodeArtifact

A place to store libraries but it does not actually store the library, it proxies the public repository. It also caches versions of packages so if the public repo goes down, you can still use the library. Managers and IT leaders can publish approved packages and CodeDeploy can pull from CodeArtifact.

![[/Untitled 6 15.png|Untitled 6 15.png]]

# CodeGuru

- ML powered service for code review and app performance recommendations
- CodeGuru Reviewer ⇒ code reviews during development
- CodeGuru Profiler ⇒ visibility and recommendations during runtime (production)