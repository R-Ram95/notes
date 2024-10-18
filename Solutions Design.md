---
tags:
  - AWS
  - Fundamentals
---
# Migration Stategies

The seven R’s of

**R**ehost

**R**eplatform

**R**elocate

**R**efactor

**R**etire

**R**etain

**R**epurchase

first 4 relate to migrating workloads to AWS

## Rehost - Lift and Shift

![[/Untitled 57.png|Untitled 57.png]]

- Recreate on prem network on AWS
- like for like transfer of application resources from on prem to aws cloud
- automate with **AWS Application Migration Service**

## Relocate - Hypervisor Lift and Shift

- same as Rehost but at Hypervisor Level
- Specific to VMware Cloud ⇒ supports virtual machines

## Replatform - Lift, Tinker, Shift

- retain core architecture
- make cloud optimizations
- e.g. migrating db to RDS

## Refactor - modernize

- reimage how app is architected using cloud native features

  

# Well Architected Framework

Framework to build:

- secure
- high-performing
- resilient
- efficient
- scalable

infrastructure

6 pillars:

- Operational Excellence - running an monitoring systems
- Security - protect info in systems
- Reliability - prevent and recover from failure
- Performance Efficiency - using IT and computing resources efficiently
- Cost Optimization - avoid unneeded costs
- Sustainability - minimize environmental impact of running cloud

# 3-Tier Architecture

![[/Untitled 1 38.png|Untitled 1 38.png]]