
# Components

Application: collection of beanstalk components

Application Version: iteration of application code

Environement: collection of AWS resources running application version

tiers: web server environment, worker environment tier

# Web Server vs. Worker

Web Server


Client connects to ELB which directs traffic to an ASG

Worker


No client accesses the EC2 instance directly. EC2 workers pull messages from SQS queue

  

# Elastic Beanstalk Deployment Modes

Single Instance → DEV

- DNS name maps to Elastic IP
- single AZ


High Availability ⇒ PROD

- DNS maps to ELB DNS name
- spans multiple AZs


Traffic Splitting === Canary Testing

- like blue/green but uses ALB instead of Route 53 and it is automated

# Update options

## All at once

Application is stopped and then the new version is spun up.

- fastest deployment
- application has downtime when it is stopped
- no additional cost


  

## Rolling

Update the application to v2 in batches (buckets). We set the bucket size. No Additional cost.


## Rolling with Additional Batches

Like rolling except the application is always running at full capacity. New buckets are added to update the version before old ones are taken down. Small additional cost because we are adding new instances.


## Immutable

New code is deployed to new instances and a temporary ASG. High cost, double capcity. Quick rollback in case of failures.


## Blue / Green

- not a direct feature of Elastic Beanstalk
- create a new “state” environment and push V2 up ⇒ previous options took place on the same env
- use Route 53 to setup weighted policies to direct a test portion of traffic to the stage env
- use Beanstalk to swap URLs once env test is done

  

# Lifecycle Policy

You can store at most 1000 application versions but if you don’t phase them out, you won’t be able to deploy more ⇒ use a lifecycle policy to phase out old versions

- based on time
- based on space

  

# Beanstalk Extensions

- zip file containing our code must be deployed to EB
- Params set in the UI can also be configured using code
- must be in `.ebextensions/` directory if the source code
- must be YAML / JSON
- `.config`

# Under the Hood

EB uses CloudFormation (used to provision other AWS services) under the hood.

You can define CloudFormation resources in your `.ebextensions` to provision an AWS resource you want

# Migrations

## Load Balancer

- after creating EB env, you cannot change the Type of the ELB (only the config)
- to migrate:
    
    1) create new env with same config except LB
    
    2) deploy application onto new env
    
    3) shift traffic from old env to new env ⇒ CNAME swap or Route 53 update
    

## RDS w/ EB

- RDS can be provisioned w/ Beanstalk ⇒ good for dev / test
    - not good for prod b.c. db lifecycle is tied to beanstalk env lifecycle
- better to create RDS db separately and provide EB app w/ connection string

To decouple RDS

1) create snapshot of RDS db as safeguard

2) enable delete protection in RDS db

3) create new EB env w.o. RDS and point application to existing RDS

4) perform CNAME swap or Route 53 update

5) terminate the old env ⇒ RDS not deleted due to deletion protections

  

# Beanstalk with Docker

Single Docker Container:

- run app as a single docker container
- provide:
    - Docker File ⇒ EB will build and run the Docker Container
    - Dockerrun.aws.json ⇒ describe where the already built Docker image is (ECR, DockerHub, etc.)
- does not use ECS

Multi Docker Container

- helps run multiple containers per EC2 instance
- creates:
    - ECS cluster
    - EC2 instances configured to use ECS cluster
    - Load Balancer
    - Task Definition and execution
- requires Dockerrun.aws.json at root of src
- Dockerrun.aws.json is used to generate the ECS task defitions
- docker images must be pre-built and stored in ECR or DockerHub for example

# Advanced Concepts

HTTPS:

idea = load SSL cert onto the LB

- can be done from:
    - the Elastic Beanstalk console ⇒ load balancer config
    - code: `.ebextensions/sercurelistenere-alb.config`
- SSL certs can be provisioned using ACM or CLI
- must configure security group role to allow incoming port 443

Redirect HTTP to HTTPS:

configure instance to redirect HTTP to HTTPS

configure ALB with a rule to do redirection

- make sure health checks are not redirected
