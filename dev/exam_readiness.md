### Domain 1 - Deployment
- deploy using CI/CD pipelines, processes and patterns
- deploy apps using elastic beanstalk
- prep app package for AWS deployment
- deploy serverless apps

CICD process - AWS CodePipeline
1. Source - AWS CodeCommit
2. Build - AWS CodeBuild
3. Test - 
4. Deploy - AWS CodeDeploy
5. Monitor - AWS X-ray, CloudWatch

CI - up to build
Continuous Delivery - up to deployment (but with manual intervention)
Continuous Deployment - fully automated

Highly Available and Scalable Applications
1. Multi-AZ, Multi-Region
2. Health check and failover mechanism
3. Stateless applications - store session state in cache server or DB

Elastic Beanstalk
- easiest to use
- less control
- simply deploy app, bs takes care of infrastructure, scaling, etc

OpsWorks
- chef
- manage infrastructure in layers

CloudFormation
- define instructure
- like terraform
- yaml/json

#### Elastic Beanstalk

Components
1. Environment
2. Application versions
3. Configuration

Permission Model
1. Service Role
2. Instance Role

#### CloudFormation

- create multiple stacks in multiplie regions from same template
- monitor progress of stack updates
  - in progress
  - completed
  - failed (will rollback)
  
  Elements
  1. intrinsic functions
  2. pseudo-parameters
  3. custom resources
  4. conditional expressions
  
  Template Sections:
  1. AWSTemplateFormatVersion
  2. Description
  3. Metadata
  4. Parameters
  5. Mappings
  6. Conditions
  7. Resources - required
  8. Outputs
  
#### Serverless Platform
1. Compute - Lambda
2. API proxy - API Gateway
3. Storage - S3
4. Database - Dynamo DB
5. Messaging - SNS/SQS
6. Orchestration - Step Functions
7. Analytics - Kinesis, Athena

Serverless Application Model (SAM)
- cloudformation extension
- apache 2.0 open spec
- supports everything cloudformation supports

Lambda
- event sources
  - data stores
    - s3
    - dynamo db
    - kinesis
    - cognito
  - endpoints
    - api gateway
    - iot
    - step functions
    - alexa
  - dev and management tools
    - cloudformation
    - cloudtrail
    - cloudwatch
    - codecommit
  - event/message services
    - sqs
    - sns
    - ses
    - cron
    
### Domain 2 - Security

#### VPC

- SSL/TLS endpoint/data encryption
- ACM - certificate Manager
  - trusted SSl/TLS certificates

Encryption
- Client side encryption
- server side encryption
- key management service 
- cloud HMS

S3 encryption
- AES 256
- SSE-S3
- SSE-KMS
- SSE-C

S3 - encryption is optional
- Glacier - encrypted by default

RDS - optional encryption

Request header in API call to encrypt object by SSE
- x-amz-server-side-encryption

IAM
- User
  - person or service
- group
  - collection of IAM users
- role
  - can assign to users, applications or services
  - no static credential such as access keys, etc
- Policy
  - defines authorization/permissions
  
AWS STS 
- Security Token Service
- temporary credentials to federated users
- web identity
- SAML 2.0 - security assertion markup language
  - MS AD, LDAP, OpenLDAP
  
Flow for SAML
> login > authenticate > get saml assertion > use saml assertion to connect to aws

Flow for Web Federation
> login > authenticate > get token > use token to connect to aws

#### Test Axioms
- Lock down master account
- security groups allow only, nacls explicit deny
- prefer iam roles over access keys

### Domain 3 - Development

- S3
- Dynamo DB
- SNS, SQS, Step Functions
- API Gateway
- CloudFront, ElastiCache
- ECS

#### S3

- bucket
  - global namespace
  - region-based
- path-style URL - bucket name in path - old
- virtual hosted-style URL - bucket name in subdomain
- pre-signed URL
- Cross Origin Resource Sharing (CORS)

#### Dynamo DB
- Fully-managed NoSQL
- 3 replicas per table
- choose eventually consistent or strongly consistent
- runs on SSD

Primary key
- partition key (previously hash key)
- sort key (optional) (previously range key)

- tables
  - items
    - attributes

Secondary Indexes
- attribute projetion
  - KEYS_ONLY
  - INCLUDE <attribute>
  - ALL - includes all attributes
  
LSI 
- local secondary indexes
- partition key + different sort keys

GSI
- global secondary indexes
- partition key +/ sort key different from table
- separate provision

Throughput
- RCU - read capacity units
  - up to 4KB size per item
  - reads per second - strongly consistent
  - 2x reads per second - eventually consistent
- WCU - write capacity units
  - up to 1KB size per item
  - writes per second
- capacity split by partition key

RCU Calculations
- 25 strongly consistent reads/sec of 15kb
soln:
- 15kb / 4kb = ~ 4
- 25 * 4 = 100 RCU

- 25 eventually consistent reads/sec of 15kb
soln:
- 15kb / 4kb = ~4
- 25 * 4/2 = 50 RCU

WCU calculation
- 100 writes/sec of 512 bytes
soln:
- 512 bytes < 1kb
- 1kb = 1024 bytes
- 100 * 1 = 100 WCU

Dynamo DB Streams
- aka - change data capture
- stream of item changes
  - insert - new image
  - update - old and new image
  - delete - old image
- 24 hour retention
- millisecond latency
- strictly ordered
- exactly once
- required for global tables

Features
- conditional write operations
- optimistic locking with version number
- batch operations
  - BatchGetItem
  - BatchWriteItem
  
DAX
- dynamo db accelerator
- eventually consistent reads - microseconds - millions requests/s
- in-memory cache
- minimal application changes
- read-through, write-through cache
  - read cache first
  - write db first
- global tables - data replication
  - write to any replica
  - propagated to all replicas in seconds
  - same data in all replicas
  - changes to to streams then to replicas

- milli - 10E-3, micro - 10E-6, nano - 10E-9

Global Tables
- data replication
- read and write on replica
- changes in stream

*Review notes:*
- RCU/WCU calcuations
- scan vs query
  - page size max 1MB
  - use limit parameter to set page size
- cloudwatch mtrics

Operations
- Scan
  - reads a page
  - page size 1MB by default
- query

#### SQS and SNS

Feature | SNS | SQS
--|--|--
persistencce | no | yes
delivery | push (passive) | poll (active)
producer/consumer | publish/subscribe (1:N) | send/receive (1:1)

- processes should be idempotent
  - processing same message 2x should have the same result
  
*Review Notes*
- sqs parameters
  - visibility timeout - time message received is invisibile to other consumers
  - receive message wait time - max time long poll receive call waits for message
  - message retention period - time message is retained in queue before deleted
  - delay seconds - delay message delivery
  
#### Step Functions

- state machine
- workflow for business logic
- build quickly
- scale, recover reliably
- modify quickly
- coordinate distributed applications and microservices
- visualize and track executions
- built-in retry or fallback

Amazon State Language
- define the state machine
- json

#### API Gateway

- API Gateway Cache
  - lambda
  - ec2
  - other http endpoints

#### Caching Soluions

edge side - cache S3 -  cloudfront
server side - cache session state on web tier- dynamo db
server side - cache data between db and app - elasticache

**CloudFront**

origin server
- s3
- ec2
- elb

Cache Behavior
- path patterns
- request headers
- query strings
- cookies

TTL
- long
- short

Security
- signed URLs or signed cookies
- cookies/URLs hashed and signed using private key from key pair

**ElastiCache**

key-value engines
- memcached
- Redis

- cluster
- node
- repliccation group
  - asynchronous
- endpoint

caching strategies
- in app
- lazy loading
  - check cache first
- write through
  - update database always updates the cache

#### Container Services

**ECS**
- elastic container service
  - fargate - serverless containers

**ECR**
- elastic container registry

**EC2**
- elastic compute cloud

**EKS**
- elastic kubernetes service
- Elastic

#### Development Tools and SDKs

- SDKs
- IDEs/Toolkits
  - cloud9 - cloud-based IDE
    - ec2
- command line

#### Test Axioms

1. managed over un-managed
2. api gateway or edge services over direct server/resource access
3. x session state on server
4. decouple infrastructure

### Domain 4 - Refactoring

#### Automate your environment

#### Design Services not servers
#### Use caching
#### Avoid single points of failure
- use db replication
- use caching

Gartners 6 R's of migration
- Rehost - lift and shift
  - manual
    - install
    - config
    - deploy
  - automate
    - use migration tools
- Replatform - lift, tinker and shift
  - determine platform
  - modify infrastructure
- Repurchase - drop and shop
  - buy cots/saas
  - install/setup
- Refactor
  - redesign
  - app code development
  - ALM/SDLC
  - integration
- Retain
- Retire

AWS Application Discovery Service
- discover on-prem applications for migration

#### Test Axioms
- durability != availability
- scalability != elasticity
- instance store is not persistent
- monolith -> microservices -> functions
- go serverless

### Domain 5 - Monitoring and Troubleshooting

- check error codes
  - 400 series - error handling - application error
  - 500 series - retry operation - service error
- instance logs
  - system logs
  - http logs
- monitoring
  - instance health and performance
  - managed services availability
  - network performance
  - utilization
  - unused or underused instances
  
#### CloudTrail
- logs to s3 - encrypted
- logs api calls
  
#### X-Ray
- analyze and debug distributed applications
- tracks requests
- create a `service map`
- dependencies
- bottlenecks
- performance

S3 object size limit - 5TB
 
Dynamo DB provisioned throughput limits - limited by partition key
 
#### Test Axioms
- instances in private vpc requires NAT to access internet
- always check security groups + nacls when troubleshooting 
- igw in route table required for internet access
- ebs volumes are loosely coupled to ec2 instances
