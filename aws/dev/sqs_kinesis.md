**applicatin communication**
1. synchronous
app1 -> app2
2. asynchronous
- event based
app1 -> message queue -> app2

**decouple applications**
- SQS - queue model - producer/consumer
- SNS - pub/sub model
- Kinesis - real-time streaming model

### SQS
- oldest AWS offering
- for decoupling applications
- retention
  - default: 4 days
  - max: 14 days
- low latency: < 10ms
- message size: 1kb to 256kb
- producer -> queue -> consumer
- M:M producer:consumer

#### Standard Queue
- best-effort ordering
- at least once delivery
- unlimited throughput
- unlimited messages

**sending message**
- producer -> `SendMessage` API

**consuming message**
- poll for messages (receive up to 10 messages at a time)
- consumers - ec2, on-prem server, lambda, etc
- must delete message - `DeleteMessage` API
- increase througput by horizontal scaling consumers

**auto scaling**
- ec2 instances poll for message
- asg on ec2 instances
- cloudwatch metric 
  - queue length
    - `ApproximateNumberOfMessages`
  - beyond some threshold
    - trigger alarm
      - alarm would trigger ASG to scale
  
**security**
- encryption
  - in-transit: enabled by default
  - at-rest:
    - SSE
    - CMK (KMS)
      - data key reuse period
        - default: 5 minutes
        - 1 minute to 24 hours
- access policies
  - advanced
    - json
      ```
      [{
          Statement: [{
            Sid:
            Effect:
            Principal: {
              AWS:
            }
            Action: []
            Resource:
          }]
      }]
      ```
  - basic
    - send/receive
      - queue owner
      - specified accounts, IAM users or roles
  - IAM policies to access SQS API
- sqs access policies
  - cross-account access to SQS queues
  - policies to allow aws services to access queues

**settings**
1. Message Visibility Timeout
- range: 0s to 12 hours
- default: 30s
  - increase timeout via API - `ChangeMessageVisibility`
- time message is not visible after it is picked up
- if message is processed, must delete
  - if not deleted, message can be processed again
- too low - get duplicates
- too high - cannot reprocess failed messages quickly
2. Delivery Delay
- delay a message after publish
  - consumers won't see it until after delay
  - max: 15 minutes
  - default: 0s
- set using `DelaySeconds` parameter

**dead letter queues**
- also an ordinary SQS queue
  - refer to DLQ from main queue
- set `MaximumReceives` threshold
  - exceed - message goes to dead-letter queues
  - range: 1 to 1000
- debug messages in DLQ
- DLQ retention - 14 days
  - `message retention period`
  - set high enough to allow you to go back and debug

**long polling**
- while polling, wait for message to arrive if queue is empty
- range: 1s to 20s
- preferred over short polling (no wait)
- why
  - decrease API calls
  - decrease message latency
- define at queue level
- or at each API call to poll, use `WaitTimeSeconds`

**SQS Extended Client**
- for processing large messages (> 256kb)
- SQS Extented Client Library (java)
- message in S3, metadata in SQS

**API**
- `CreateQueue` (`MessageRetentionPeriod`)
- `DeleteQueue`
- `PurgeQueue`
- `SendMessage` (`DelaySeconds`)
- `ReceiveMessage`
- `DeleteMessage`
- `ReceiveMessageWaitTimeSeconds`
- `ChangeMessageVisibility`
- batch API calls

#### FIFO Queues
- name ends with `.fifo`
- ordered
- exactly-once processing
- limited throughput
  - 300 msg/s - without batching
  - 3000 msg/s - with batching
  - 10 msg/s per batch
- deduplication
  - message group id
  - message deduplication id
  - deduplication interval (5 minutes)
    - deduplication of messages within interval
    - outside of interval - can be duplicated
  - methods
    - content-based
      - SHA256 of message body
    - ids - deduplication id
- group id
  - 1 consumer per group
  - orders within group

  ### SNS
  - producer -> topic -> N subscribers
  - max: 10M subscriptions per topic
  - max: 100k topics
  - subscribers
    - SQS
    - HTTP/HTTPS (with retries)
    - lambda
    - email
    - SMS
    - mobile notifications
- SNS notifications from AWS services
  - cloudwatch alarms
  - asg
  - s3 bucket events
  - cloud formation state changes
  - etc
- publishing
  - topic publish (using SDK)
    - create topic
    - create subscription/s
    - publish to topic
  - direct publish (using mobile SDK)
    - create platform application
    - create platform endpoint
    - publish to platform endpoint
- encryption
  - in-flight
  - at-rest: KMS
- access controls
  - IAM policies - access to SNS API
- SNS access policies (similar to bucket policies)
  - cross-account access to topics
  - AWS service access to SNS topics

#### Fan-Out pattern
- push once in SNS, receive in all SQS queues that are subscribers
- why
  - data persistence
  - delay processing
  - retry work
- SQS policy for SNS to qrite
- SNS cannot send message to SQS FIFO queue
- use case
  - s3 event
    - only 1 SNS message is generated per event
    - event -> SNS -> fan-out to SQS, other subcribers ok too like lambda

### Kinesis
- alternative to Kafka
- logs, metrics, iot, clickstreams
- automatically replicated to 3 AZs

**Streams**
- low latency streaming ingest at scale
- shards/partitions
  - increase throughput, increase shards
- data retention
  - default: 1 day/24 hours
  - max: 7 days/168 hours
- ability to reprocess/replay data
- multiple apps consume same stream
- data cannot be deleted (immutability)
- billing per shard, unlimited shards possible, 200 account limit - ask to increase
- ordered records per shard
- can batch
- dynamic shards (reshard/merge)
- 1MB/s or 1000 messages/s write/shard
- 2MB/s read/shard 

**Analytics**
- SQL in streams, real-time
- real-time
- auto-scaling
- serverless, managed
- pay for consumption
- can create streams out of real-time queries

**Firehose**
- load streams into S3, redshift, elasticSearch
- 60s latency (near real-time)
- auto-scaling
- convert data in hose (pay for conversion)
- pay for data volume in firehose

#### Kinesis API
- `PutRecord`
- message key + data
  - message/partition key -> hashed -> partition/shard
  - messages get a sequence number
- use a unique partition key
- batching available
- `ProvisionedThroughputExceeded`
  - retries with backoff
  - increase shards/scale
  - ensure partition key is not hot
- API
  - use CLI, SDK
  - use Kinesis CLient Library (java, node, python, ruby, .net)
    - clients use DynamoDB to checkpoint progress
- no free tier

**optional settings**
- shard-level metrics
  - incoming/outgoing bytes/records
  - etc
  - data written at 1-minute periods

```
$ aws kinesis list-streams
$ aws kinesis describe-stream --stream-name ...
$ aws kinesis put-record --stream-name ... --data ... --partition-key ...
return: ShardId, SequenceNumber
$ aws kinesis get-shard-iterator --stream-name ... --shard-id .... --shard-iterator-type ...
returns: string for shard iterator
$ aws kinesis get-records --shard-iterator ...
data is base64 encoded data
```

shard iterator types
- like dynamo db 
- AT_SEQUENCE_NUMBER
- AFTER_SEQUENCE_NUMBER
- AT_TIMESTAMP
- TRIM_HORIZON - cut off messages that are older than retention setting
- LATEST

KCL - kinesis client library
- shard must be read by only 1 KCL instance, KCL instance can read more than 1 shard
- progress is checkpointed in dynamo db
- run KCL in EC2, beanstalk, or on-prem
- records are read in order per shard

VPC endpoints for Kinesis

**Ordering**
- SQS - 
  - order within same group id
  - # of unique group ids == max # of consumers
- Kinesis
  - order within same shard
  - max # of consumers == # of shards