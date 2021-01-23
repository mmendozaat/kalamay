? instance profiles
? elastic beanstalk

roles
- temporary
- using tokens and time to live
- cannot attach to instance

instance profile
- attaches to role

DNS
- SPF record
- SPF - sender policy framework
  - lists sll authorized host names/ip addresses that are permitted to send email on behalf of your domain

Fan-out
- multiple SQS queues subscribed to SNS topic

Origin Access Identity
- identifier for cloudfront

? VPC Sharing 
- share subnet between accounts

AWS Export/Import
- sending drives to AWS
- no longer exist
- replaced by snowball

Snowball Edge
- transfer PB data to AWS
- physical appliance

Windows instance
- normalization price does not apply

SQS
- pull method
- dead letter queue
  - if message is not processed correctly, not deleted

SNS 
- push method

auto scaling group
- required info for launch configuration
  - name,
  - ami,
  - instance-type
  
DNS record types
- A - domain-IP mapping
- SOA - start of authority - required
- CNAME - canonical name - subdomain mapping 
- MX - mail servers
- PTR - reverse dns lookup
  
Redis
- has sorted sets - ideal for leaderboards

S3
- cross-region replication requires versioning
