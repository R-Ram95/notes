# Auto Scaling


The goal of ASG is to scale in an out EC2 instances for increased and decreased demands, repectively. You set a min, preferred and max number of instances. The ASG automatically reigsterd EC2 instaces to ELB and recreates EC2 instances if they fail health checks. ASG IS FREE.

**Configuring EC2 Auto Scaling Components**

There are three components: Launch Template/Config, ASG, Scaling policies.

**Launch Template and Configuration**

The ASG needs to know the following thing to create EC2 instances on your behalf:

- AMI + Instance type
- EC2 User Data (optional)
- Tenancy (optional)
- Storage (optional)
- EBS
- Security Groups
- SSH key pair
- IAM Roles for EC2
- Network + subnet info
- LB info
- min/max capacity
- scaling policies
- OS
    
    

- Coupled with CloudWatch Alarms to scale in/out
- metrics like Avg CPU are computed for overall ASG instances

Amazon EC2 Auto Scaling groups

Helps you define where EC2 auto scaling deploys resources. You specify the VPC and subnets where the EC2 should be launched in. You can also specify the purchase options for EC2, you can use On-demand or Spot instances.

  

# Scaling Policy

## Dynamic Scaling Policies

Target Tracking Scaling

- e.g. we want avg ASG cpu to be around 40% ⇒ scale in/out to acheive this

Simple / Step Scaling

- when cloud watch alarm is triggered add/ remove units
- e.g. CPU > X% ⇒ add Y Units
- e.g. CPU < X% ⇒ remove Y Units

Scheduled Actions

- anticipate scaling based on known usage patterns
- e.g. Friday at 5pm lots of traffic ⇒ increase the min capacity

## Predictive Scaling


- forecast load and schedule scaling ahead

## Metrics to Scale on

- CPU Utilization: AVG CPU usage across all instances
- RequestCountPerTarget: make sure # of requests per EC2 is stable
    
- Avg Network In
- Custom Metrics

## Scaling Cooldowns

- time after scaling activity (in or out) ⇒ default is 300 seconds
- ASG will not launch or terminate instances during cooldown
