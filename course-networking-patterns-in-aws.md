
# Overview of Concepts

**Multi-tier Architecture**

- grouping of different software by function into tiers or layers
- commonly use three tiers:
    - Presentation (UI)
    - Application or Logic Tier
    - Data Tier

**Muti-VPC Architecture**

- service taht lets you launch AWS resources into isolated virtual network

# AWS Nework Service Offerings

## VPC Endpoints

- let you privately connect your VPC to AWS services and VPC endpoints
- allows resources inside VPC to communicate with resources outside a VPC
- does not require IGW, VGW, NAT, VPN, or Direct Connect
- instances in VPC do not require a public IP address
    
    ### Gateway VPC Endpoints
    
    - gateway endpoints are destinations reachable from within the VPC through prefix lists
    - uses for traffic destined for DynamoDB or S3
    - below, subnet 1 can reach internet and S3, subnet 2 can only reach S3
        
        
    
    ### Interface Endpoints
    
    - powered by AWS Private Link
    - serves as entry point for traffic destined to AWS Service or a VPC endpoint
    
    ### Gateway Load Balancer Endpoint
    
    - elastic network interface
    - intercepts traffic and routes it to service confugred using Gateway Load Balancer
    - only available for endpoint services configured using Gateway Load Balancer

## VPC Endpoint pricing

- charged per hour endpoint is provisioned and per GB processed through VPC endpoint for Gateway Load Balancer and Interface Endpoint
- no additiona charge for Gateway Endpoint

# VPC Peering

- connection between two VPCs that lets you connect them privately
    
    ## Benefits
    
    - highly availble because it does not rely on physical hardware
    - no bandwidth bottleneck
    - allows you to privately connect resources in different regions - establish peering between VPCs in different Regions
        - without using VPNs, Gateways, or separate network appliances
    
    ## Scenarios for Usage
    
    1) Sharing of resources between all VPCs
    
    
    2) Partial Sharing of Centralized Resources
    
    
    ## Non-Valid Perring Connections
    
    - Overlapping CIDR Blocks → cannot create VPC peering connection between VPCs with matching or overlapping IPV4 or IPV6 CIDR blocks
    - Transitive Peering → Cannot connect a VPC through another VPC, each connection is a 1-1
    - If either VPC in a peering relationship has any of the following connections, the peering relationship cannot be extended to that connection
        - VPN or Direct connect to corporate network
        - internet connection through IGW
        - internet connection in private subnet through NAT
        - a gateway VPC endpoint to an AWS service
    
    ## Pricing
    
    - no charge for setting up or running VPC peering
    - data transferred is charged pr GB for send and receive

# Design Patterns

## Hub and Spoke

From this:


- new departments need to create a new vpn connection
- there is no way to centrally manage traffic - routing and network security groups are handled within each vpc, modifications to traffic controls or deployment of new systems requires an engineer to reconfigure muliple vpcs

To this:


- one transit gateway replaces the VPC peering connections
- routing tables are centralized to the transit gateway
- VPN connections are reduced to 1
- Direct Connect terminates on Transit gatway → therefore we only need 1 Direct Connect connection

## Resilient Hybrid Networks

from this:


- AWS Direct Connect Gateway offer four nines of availability
- there is 1 router at each Partner location → each router is a single point of failure that, if it goes down, the company loses access to AWS

to this:


- another router is added at each partner location
    - each WAN circuit terminates at a different router

  

# Regional High Availability

- Route53 routes users to region with the lowest latency
- VPC Peering keeps the two DBs insync. Incase a user loses connection, they pick up where they left off when they reconnect because the DBs are in sync (Inter-region VPC Peering)


## Transit Gateway Peering

- Transit Gateway supports routing traffic between two or more VPCs within an AWS Region
- Transit Gateway is used to support inter-region peering.
