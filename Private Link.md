---
tags:
  - AWS
  - Networking
---
# What is it?

- provides private connection between VPC and AWS Services
- avoid exposing traffic to public internet
- establishes secure TCP connection between service provider and consumers VPCs
- in the below picture, the EC2 instances in the private subnet are able to communicate with the EC2 instances in the public subnet (which are connected to the internet) through the Private Link Endpoint which has a connection the the NLB - this avoids data travelling through the private internet

![[/Untitled 40.png|Untitled 40.png]]

Before Private Link, services in a VPC were connected to Other VPCs using:

1) Public IP adress using internet gateway of the VPC

2) Private IP using VPC Peering

# Benefits

- provides secure and scalable way to privately connect to AWS hosted services
- it is simple by removing the need to permit public IPs and manage internet connectivty with IGW, NAT Gateways or firewall policies
- gives on prem networks private access to AWS services through Direct Connect

# Considerations

- does not support IPV6
- service provider application will only see traffic from the NLB and will never know the IP address of the customer or service consumer
- can use Procy Protocol V2 in the NLB to send additional connection info such as source and destination
- private DNS of VPC does not resolve outside of hte VPC