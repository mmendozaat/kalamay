### Lambda 

Free Tier
- 1M lambda requests
- 400k GB-s of compute time

- up to 3GB RAM

Lambda Language
- Node
- Python
- Java
- C#
- Go
- Ruby
- Custom Runtime API - ex. Rust
- but not docker
  - for ECS/Fargate

  Serverless Cron
  - CloudWatch Events
    - trigger Lambda

Synchronous Invokations
- CLI, SDK, API Gateway, ALB
- Cognito, Step
- `aws lambda invoke --function-name ... --payload ...`

ALB multi-header value
- Client passes query string
  `?name=foo&name=bar`
- converted to json as list
  `queryStringParameters: {name: [foo, bar]}`

Lambda@Edge
- if using CloudFront
  - modify request/response to/from client from/to cloudfront
  - modify request/response to/from origin from/to cloudfront
  - generate response without going to origin

Asynchronous Invocation
- lambda function must be idempotent
  - retries - same result
  - max retries = 2
- `aws lambda invoke --function-name ... --payload ...`
- `aws lambda invoke --function-name ... --payload ... --invocation-type Event`

Event Bridge
- CloudWatch
  - cron/rate eventbridge rule
    - Trigger every hour - lambda
- CodePipeline
  - eventbridge rule
    - trigger on state changes - lambda

Lambda event source mapping
- streams - kinesis/dynamo db
  - by shard
    - max age of message - 7 days
    - can split batch on error
    - records per batch - default 100
    - retry attempts - default 10,000
    - up to 10 concurrent batches per shard
    - all or nothing by batch
    - pause processing until error is resolved
      - if in order processing required
    - define batch window
  - data not deleted after consumed by lambda 
- queues
  - batch size - max 10 messages
  - set queue visibility timeout to 6x of lambda timeout
  - fifo scales up to # of message groups
  - standard - lambda scales up to process all as quickly as possible
    - up to 60 instances per minute to scale up
    - up to 1000 batches of messages processed simultaneously
  - in case of error, batches are returned as individual items

Lambda destinations
- asynchronous invokation
  - on success
  - on failure

Lambda Execution roles
- what can lambda do?
- best practice
  - 1 lambda execution role per function

Lambda Resource policy
- who can invoke/call lambda API?

Lambda environment variables
- can be encrypted

XRay Tracing
- aka Active Tracing
- runs X-Ray daemon
- use X-Ray SDK
- requires
  - AWSXRayDaemonWriteAccess policy
  - env vars
    - _X_AMZN_TRACE_ID
      - tracing header
    - AWS_XRAY_CONTEXT_MISSING
      - LOG_ERROR - default
    - AWS_XRAY_DAEMON_ADDRESS
      - daemon IP:PORT

Lambda in VPC
- requires ENI (Elastic Network Interface) in subnet
  - `AWSLambdaENIManagementAccess`
- requires `AWSLambdaVPCAccessExecutionRole`
- if in public subnet, still no access to internet or public ip
- requires NAT
  - NAT to IGW
- Dynamo DB - not in VPC
  - can use VPC endpoint

Configuration
- RAM
  - 128MB to 3GB, step 64MB
  - vCPU - depends on RAM
  - 1792MB = 1vCPU ~ 1.75GB
  - if CPU-bound, increase RAM
- Timeout
  - default: 3s
  - max: 15 minutes/900s
    - if > 15 -  use EC2, farget or ECS
- Execution Context
  - lambda environment
  - re-used for next invocation
  - ex. db connections
    - call outside of handler
  - has `/tmp` directory
    - part of execution context
    - max 512MB
- Concurrency and concurrency
  - up to 1000 concurrent executions
    - max sum of reserved and unreserved concurrency
  - reserved concurrency
    - exceed -> throttle
  - limit
    - sum of all executions accross functions in account
  - s3 file event
    - automatic retry - message in queue again - up to 6 hours
    - exponential backoff - 1s up to 5 mins
- cold start
  - first invocation is slower (init) than latter
  - fix
    - provisioned concurrency
      - init in advance
      - all invocations will have the same low latency
- dependencies - code + packages zipped together
  - node - npm + node_modules
  - python - `pip --target`
  - java - .jar
  - in s3 if > 50MB, else, straight to lambda
- layers
  - custom runtime
  - or heavy packages - prebuilt by AWS
- versions
  - immutable, except $LATEST
  - publish to create a version
- aliases
  - own ARNs
  - cannot reference other aliases
  - point to lambda versions
  - enable blue/green deployments
    - traffic shift to another version by perentage
    - automate traffic shift using CodeDeploy
      - integrated with SAM
      - types
        - linear 
          - Linear10PercentEvery3Minutes
          - Linear10PercentEvery10Minutes
        - canary - N% then 100%
          - Canary10Percent5minutes
          - Canary10Percent30minutes
        - all-at-once - immediate
      - pre/post traffic hooks
        - as health check

### Lambda Limits

#### Execution
- memory: 128MB - 3GB (step 64MB)
- max execution time: 900s / 15 mins
- env var size: 4KB
- disc capacity (/tmp) - 512MB
- concurrent executions - 1000

#### Deployment
- Deployment size: 50MB
- uncompressed deployment size: 250MB
- use /tmp to cache
- env vars - 4KB

#### Best Practices
- use outside of handler (shared context)
- use env vars (encrypt)
- minimize deployment package
- use layers (load AWS prebuilt runtimes/packages)
- do not use recursive code