---
tags:
  - AWS
  - Compute
  - DevOps
  - Docker
---
ECS

EKS

ECR

Fargate Launch Type

EC2 Launch Type

Research

- container management on AWS
- Elastic Container Server (ECS)- Amazon’s container platform
- Elastic Kubernetes Servicer (EKS) - managed Kubernetes
- Fargate - serverless container platform, works with ECS and EKS
- Elastic Container Registry (ECR) - stores container images

# ECS

## Key Terms:

**Task** = run time instance ⇒ representation of image running in ECS

**Task Definition** = templates for the **Task** (Docker Image, Memory, CPU requirements, Networking, etc.)

**Container** = Virtualized instances that run **Tasks** (only for EC2), has no meaning in Fargate because we don’t care about virtualized instances

**Cluster** = Abstract pool of compute resource

EC2 a group of containers that run Tasks

Fargate a group of Tasks

**Service** - task management system that ensures X amount of tasks are up and running

Service ⇒ points to Cluster ⇒ Cluster ⇒ points to Task Definition

## EC2 Launch Type

![[/Untitled 18.png|Untitled 18.png]]

- Launch ECS Tasks on ECS Clusters
- Must provision and maintain the infrastructure (EC2 Instances)
- Each EC2 instance must run the ECS agent to register in the ECS cluster

## Fargate Launch Type

![[/Untitled 1 10.png|Untitled 1 10.png]]

- launch Docker container on AWS
- no need to provision infrastructure, Fargate does this for you
- all serverless
- just create task definitions
- AWS runs tasks for you based on CPU and RAM needs
- scale ⇒ increase number of tasks

## ECS IAM Roles

![[/Untitled 2 10.png|Untitled 2 10.png]]

### EC2 Instance Profile (EC2 Launch type only)

- used by **ECS Agent**
- makes API calls to ECS
- send container logs to CloudWatch
- Pull Docker images from ECR
- reference sensitive data

### ECS Task Roles

- allows each task to have a specific role
- different roles for different ECS services
- Task Role defined in Task Definition

  

  

## Auto Scaling

- Uses application Auto Scaling
    - ECS service average CPU usage
    - ECS service memory usaage (scale of RAM)
    - ALB request count per Target
- Target Tracking - scale based on based on target value of a specific CloudWatch metric
- Step Scaling - based on CloudWatch Alaram
- Scheduled Scaling - predictable usage
- ECS service auto scaling (task level)≠ EC2 auto scaling (instance level)
    
    ### EC2 Launch Type
    
    - add underlying EC2 instances
    - ASG Scaling
        - scale based on CPU usage
        - Add EC2 instances over time
    - ECS Cluster Capacity Provider
        - This is a better way of doing things
        - automatically provision and scale the infrastructure for your ECS tasks
        - Capacity Provider is paired with ASG
        - add EC2 instances when missing capacity

## Task Definitions

- meta data in JSON to tell ECS how to run a docker container
- contains:
    
    - Image Name
    - Port binding for container and host
    - Mem and CPU
    - Environment Variables
    - Networking Info
    - IAM Roles
    - Logging configuration
    
    ![[/Untitled 3 10.png|Untitled 3 10.png]]
    
    - connect container port (80) to EC2 (host) port (8080) to give container access to the internet

### Load Balancing (EC2 Launch Type)

- get dynamic host port mapping when only the container port is defined in the task definition
- the ALB finds the right port on EC2 instance
- EC2 instance security groups must allow any traffic from the ALB

![[/Untitled 4 7.png|Untitled 4 7.png]]

  

### Load Balancing (Fargate)

- only need to define the container port (serverless ⇒ no EC2)
- each ECS task gets private IP
- ECS ENI Security Group ⇒ allow port 80 from ALB
- ALB Security Group ⇒ allow port 80/443 from web
    
    ![[/Untitled 5 7.png|Untitled 5 7.png]]
    

### IAM Roles in ECS

- one IAM role per task definition
- Each task in an ECS service created from a task definition inherit the Role assigned to the Task Definition ⇒ NOTE the role is defined at the TASK DEFINITION level

  

### Environment Variables

- hardcoded ⇒ e.g. URLS
- Sensitive variables:
    - SSM Parameter Store ⇒ e.g. API keys, Shared configs
    - Secrets manager ⇒ DB passwords
- bulk ⇒ loaded from S3

### Data Volumes (Bind Mounts)

how to share data between ECS tasks?

- we mount data volumes on the containers that need to share data ⇒ works for both EC2 and Fargate tasks

![[/Untitled 6 6.png|Untitled 6 6.png]]

- application container writes to bind mount
- metrics and logs container reads from the bind mount
- EC2 tasks ⇒ use instance storage
    - data is tied to the lifecycle of the EC2 instance
- Fargate tasks ⇒ use ephermeral storage
    - data is tied to the container using them
    - 20 - 200 GiB

## Task Placement

ECS services needs to determine where to place/remove a task based on CPU, memory and available ports when task are added or removed

- need to define a task placement strategy and task placement constraints
- only works for EC2 Launch Type b.c. Fargate is managed and takes care of this

Steps ⇒ identify instances that satisfy:

1) CPU, memory, port requirements from the task definition

2) task placement constraints

3) task placement strategy

4) Select instances for task placement

Stategies:

Binpack:

- place tasks based on least available amount of CPU or memory
- minimize # of instances ⇒ cost savings
- place as many containers as it can on existing instances before adding new ones

Random:

- tasks placed randomly

Spread:

- for instances on multiple AZs
- tasks spread evenly across AZs

  

  

# EKS

Elastic Kubernetes Service is a way to launch managed Kubernetes clusters on AWS.

Kubernetes is an open-source system for automatic deployment, scaling and management of containerized applications ⇒ it is an alternative to ECS but using a different API

EKS supports two launch modes:

EC2 ⇒ to deploy worker nodes

Fargate ⇒ to deploy serverless containers

Use EKS if you’re already using Kubernetes on-prem or in another cloud and want to migrate the AWS.

Kubernetes is cloud-agnostic, i.e. it can be used on any cloud providers (GCP, Azure, AWS, etc.)

## Node Types

Managed Node Groups:

- creates and manages Nodes (EC2 instances) for you
- nodes are part of AGS managed by EKS
- support on-demand or spot instances

Self-managed Nodes:

- created by you and registered to EKS cluster and managed by ASG
- can use pre-built AMI which are optimized for EKS
- on-demand, spot-instances

AWS Fargate

- no maintenance and no nodes are managed

## Data Volumes

You need to specify StorageClass manifest on EKS cluster, and it leverages Container Storage Interface (CSI) compliant driver

  

  

# ECR

The Elastic Container Registry stores and manages Docker Images on AWS.

Must assign IAM role to EC2 instances to allow it to pull Docker Images from the ECR repository:

![[/Untitled 7 4.png|Untitled 7 4.png]]

  

## Fargate Launch Type

- pay for compute
- Persistent Storage:
    - Bind Mounts host volumes (tied to underlying hardware)
    - EFS

[https://aws.amazon.com/fargate/pricing/](https://aws.amazon.com/fargate/pricing/)

- no upfront costs
- pay for vCPU, memory, and storage consumed by applications
    
    |   |   |
    |---|---|
    ||Price|
    |per vCPU per hour|$0.04048|
    |per GB per hour|$0.004445|
    
    Fargte Spot ⇒ uses spare aws capacity
    
    |   |   |
    |---|---|
    |per vCPU per hour|$0.0140264|
    |per GB per hour|$0.0015402|
    

## EC2 Launch Type

- pay for underlying instance
- Persistent Storage
    - Docker Volumes ⇒ uses EC2 Instance Storage
    - EBS for tasks
    - EFS
- choose instance types and purchase mode (on demand, savings, spot, etc)

# Research

[https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/storage.html](https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/storage.html)