### Lambda
- can reserve concurrency
- up to 10 test events
- automatically scale up to 1000 concurrent executions
- event-driven
- if stream-based, use polling

AWS SAM - serverless application model
- specification

```
$ aws lambda invoke --function-name ... --payload file://...
```

poll-based services
1. dynamo db
2. kinesis
3. sqs

layers
- libraries
- dependencies

- allocate memory for lambda, cpu is allocated based on memory. ratio same as general purpose ec2
- > 1.5GB memory, multi-threading allocated
- default timeout 3s
- max timeout 15minutes

### API Gateway
- for REST and WebSocket API

Endpoint Types
1. edge-optimized
- use cloudfront
2. regional endpoint
- services within region
3. private endpoint
- services within same VPC

? api gateway methods
? throttling
? stages
? certificates
