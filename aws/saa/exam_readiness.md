### Solutions Architect Associate

#### Design Resilient Architectures

How
1. reliable storage
2. decoupling
3. multi-tier architectures
4. ha/fault-tolerant

**storage**
1. instance store
2. EBS - elastic block store
- multiple volumes can be striped
3. EFS - elastic file system
4. S3 - object store 


EBS types
1. ssd
- random access
- a. general purpose
- b. provisioned
2. hdd
- sequential access
- a. throughput optimized
- b. cold

EFS
- elastic file system
- PB scale
- NFS
- compatible with linux not windows
- mount target within an AZ
- shared by multiple instances

S3
- object storage
- distributed system 
- strong consistency - new objects
- eventual consistency - updates
- IAM policies, bucket policies, ACL
- buckets are regional

Glacier
- vaults - collection of archives
- archives - collection of files
- retrieval
- a. expedited
- b. standard
- c. bulk
- encrypted by default

**decoupling**
1. use a message queue
2. use a load balancer

*fault-tolerance is a higher bar than high availability
*high availability - continued service even with degraded performance
*ha - system can failover
*fault-tolerance - continued service at without degradation of performance
*ft - system conceals its failure, no loss of service
*RTO - recovery time objective - time to recover
*RPO - recovery point objective - data lost

To Study:
- EBS
- S3
- EC2
- SQS
- ELB
- EIP
- Route 53
- Well-architected Framework
- Cloud Formation
- Cloud Watch/Cloud Watch logs
- auto scaling group
- STS
- Security Best Practices whitepaper
- IAM best practices
- VPC
- Bastion Hosts

#### Design Performant Architectures

**Databases**
1. RDS
- read replicas
- multi-az deployment (master-standby)
2. Dynamo DB
- define throughput (read/write per second/capacity unit)
- read capacity unit - max 4k per read
- read capacity unit - 1 strongly consistent reads/s
- read capacity unit - or 2 eventually consistent reads/s
- write capacity unit - max 1k per write
- write capacity unit - 1 write/s
3. Redshift

caching
1. cloudfront (CDN)
- origins - S3, EC2, ELB, HTTP 
- AWS Shield (protect against DDOS) + WAF (firewall) integration
2. ElastiCache
- a. memcached
  - simple
  - multithreaded
- b. redis
  - clustered or not

Auto Scaling
- launch configuration
  - ec2 instance size
  - ami
- auto scaling group (ASG)
  - references launch confi
  - min, max, desired size
  - reference an ELB
  - health check type
- auto scaling policy
  - how much to scale in or out
  - 1 or more attached to ASG
- scale bases on metrics or schedule
  
  ### Secure Applications and Architectures
  
  Guides:
  - shared responsibility model
  - principle of least privilege
  - identities
  
  Identities
  1. IAM users
  2. Roles - temporary
  3. Federation - credentials outside AWS (corporate)
  4. Web Identity Federation - other web identities, use sts
  
  VPC
  - private IP address space
  - components
    - organization: subnets
    - security: security groups/acl
    - network isolation: igw, virtual private gateways - to customer data centers, natgw
    - traffic direction: routes
    
  public subnets
  - route table to igw
  
  private subnets
  - outbound - use nat
  - inbound - proxy or bastion host
 
Security groups
- operate at network interface level
- allow only
- span multiple AZ

network ACL
- operate at subnet level
- allow or deny

SSE - server-side encryption
- SSE-S3
- SSE-KMS
- SSE-C
CSE - client-side encryption
- CSE-KMS
- CSE-C

KMS - key management service

Cloud HSM
- hardware-based key management

Tips
- lock down root user
- prefer IAM roles to access keys
- security groups - allow only
- nacls - allow, deny

instance cost options
- spot block
- hibernate

#### operational excellence

1. AWS Config
- tracks resources - EBS, EC2
- compliance to config rules
2. Cloud Formation
- infra as code
- json/yaml
3. Cloud Trail
- logs api calls
4. VPC Flow logs
- log network traffic
5. AWS Inspector
- checks EC2 for security vulnerabilities
6. AWS Trusted Advisor
- checks for best practices

