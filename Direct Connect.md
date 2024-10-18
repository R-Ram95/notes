---
tags:
  - AWS
  - Networking
---
A private, connection to AWS from your physical facility. Consider bandwidth, connection type, protocol configs, and other network specs when choosing to implement Direct Connect.

# Speed

- supports 1, 10, 100 GBits / s
- LACP (Link Aggregation Control Protocol - multiple dedicated conections can be grouped into link aggregation groups (multiple connections become a single managed connection)
- Specs:
    - connections in LAG must be the same bandwidth
    - max 2 100Gbps connections in a LAG

# Network Requirements

- network is co-located with existing Direct Connect location
- you are wokring with Direct Connect partner
- you are working with independent service provider to connect to direct connect