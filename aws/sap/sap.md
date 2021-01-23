### organization complexity

1. cross-account access
2. networks
3. multi-account aws environments

delegation
- roles - via sts

federation
- ldap
- active directory
  - simple ad - does not support mfa
  - ad connector - on-premises user
  - aws managed microsoft ad
  
VPN options
  1. aws managed vpn
  2. aws vpn cloudhub
  3. software vpn
  
VPC endpoints
  1. interface endpoints
  2. gateway endpoints
  
multiple accounts
- 1 account each for dev, prod, etc
- consolidate under aws organizations

### solutions design

considerations
1. scaling and reliability
- auto scaling
- elasticache
- sns
- cloudformation
- elb
- sqs
- kinesis
- cloudwatch
2. business continuity
3. performance

cognito
- authentication for web and mobile apps
- user pool vs identity pool

CloudTrail
- log file integrity via hash

deployment mechanisms
1. runtime/container
- ec2
- ecs
- lambda
2. app deployment
- code deploy 
- opsworks
3. code deployment
- code commit
- code pipeline
4. infra deployment
- opsworks
- cloudformation

*elastic beanstalk

*Keys - region specific
