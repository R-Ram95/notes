# Concepts

**Customer Gateway**

- resource in AWS that represents on-prem gateway device
- contains information about the type of routing used by the Site-to-Site VPN, BGP, ASN, etc.

**Customer Gateway Device**

- physical device on your side

**Virtual Private Gateway**

- VPN concentrator on AWS side

**Transit Gateway**

- transit hub that can be used to interconnect your VPCs and on-premises networks

## Limitations

- ipv6 partially supported
- The greatest Maximum Transmission Unit (MTU) available on the inside tunnel interface is 1,399 bytes
- for virtual private gateway only one tunnel can be active (1 - 1.25 Gbps)
- for transit gateway both tunnels can be active (2-2.6 Gbps)
- max 140,000 PPS

  

# **Client VPN**

Client VPN is a managed client-based VPN service that lets you securely access your AWS resources and resources in your on-premises network.
