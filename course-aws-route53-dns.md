
# Domain Name Service (DNS) terminology


# Record Types

A - maps hostname to IPV4

AAAA - maps hostname to IPV6

CNAME - maps hostname to hostname

- target domain name must have A or AAAA record

NS - Name servers for the hosted zone

# CNAME vs Alias

CNAME:

- purpose is to map a host name to another host name (app.domain.com ⇒ blabla.anything.com)
- Only for NON root domain (**something.**mydomain.com)

Alias:

- map a host name to an AWS resource (app.domain.com ⇒ blabl.amazonaws.com)
- works for root domain and non-root domain
- free of charge
- native health check

# Alias Record

- maps hostname to AWS resource
- can be used for top node of DNS namespace (Zone Apex)
- always of type A/AAAA for aws resources
- cant set TTL


### Alias Record Targets

- ELB
- CloudFront distribution
- API gateway
- Elastic Beanstalk
- S3 websites
- VPC interface endpoints
- Global Accelerator Accelerator
- Route 53 record of the same hosted zone
- **CANNOT SET ALIAS RECORD FOR EC2 DNS NAME**
