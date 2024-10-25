
# 1. Summary

1) build a VPC

2) assign VPC ip address range

3) configure Public and Private subnets in VPC

4) Secure subnets with NACL

5) Secure instances using Security Groups

- VPC ⇒ virtual network in the cloud
- Subnet ⇒ range of ip addresses in a VPC
    
    ![[/Untitled 56.png|Untitled 56.png]]
    

# 2. VPC

A Virtual Private Cloud is an isolated network created in the AWS cloud. Specify three things when creating:

1) Name of VPC

2) Region where VPC lives ⇒ they span AZ’s within the same region

3) IP range in CIDR notation

## Default VPC

- automatically creates a public subnet
    - subnet configured with default route for all inbound/outbound traffic
- configured to have one subnet in each AZ of the region
- Subnet IP must be unique to other subnet IP Ranges
- default Amazon VPC IPv4 CIDR: 172.31.0.0/16
- default Amazon VPC is configured with:
    - /16 IPv4 CIDR block, a /20 default subnet for the Regions' Availability Zones
    - internet gateway
    - default route to the internet gateway
    - default security group
    - default network ACL
    - default Dynamic Host Configuration Protocol (DHCP) option set

## Custom VPC

- regional service
- each component must be explicity defined during configuratiojn
    - nothing is allowed in our out without explicit config
- IPV4 and IPV6 support and CIDR block cannot be modified later
- You have control of:
    - IP address ranges supported by defining subnets
    - managing internal traffic flow
    - managing how traffic enters and leave VPC through route tables and network gateways
- choose between dedicated and default tenancy
    - dedicated - hardware that is not shared, costs more, cannot be configured later
    - default - hardware that is shared, lower cost

  

# 3. Subnet

Subnets are within a VPC and are smaller networks within your base network. Specify:

- VPC we want subnet to be in
- AZ for subnet to be in
- IPv4 CIDR block for subnet ⇒ must be a subset of the VPC CIDR block

  

# 4. Direct Connect

Creates a secure connection between on-prem and AWS VPC.

  

# 5. Gateways

## 5.1 Internet Gateway

This gives our VPC access to the internet. Internet gateway is highly available and highly scalable

## 5.2 Virtual Private Gateway

Connects your VPC to another private network. You add a VPG to both the AWS side and customer side ⇒ this creates an encrypted VPN

# 6. Architecting VPC and Subnet

- create atleast two subnets configured in two AZ’s ⇒ redundancy and fault tolerance

# 7. Network Access Control List

NACL is like a virtual firewall at the **subnet level** ⇒ it allows you to control which kind of traffic is allowed to enter and leave

- control access at subnet level
- network traffic is allowed or denied using a NACL

  

![[/Untitled 1 37.png|Untitled 1 37.png]]

# 8. Security Groups

Secures access at the EC2 **Instance level**. By default, only allows outbound traffic. Security Groups are stateful ⇒ they remember if a connection is initiated by the EC2 instance and allows responses without modifying inbound rules

- control access at instance level by Port, Proctol and IP Range

![[/Untitled 2 30.png|Untitled 2 30.png]]

  

# ** Difference Between NACLs and Security Groups

NACLS are Stateless, Security Groups are Stateful

- Security groups remember who they sent outbound traffic to and automatically allow return traffic
- NACLs have inbound and outbound rule sets and both must be evaluated on round trip
