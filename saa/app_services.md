SNS
- push notification
- pub-sub
- redundant storage across AZs
- many to many topic:subscribers
- fan-out to SQS
- 1 message: 1 topic
- up to 10M subscribers/topic
- up to 100k topics
- subscribers
  - lambda
  - SQS
  - http/s webhooks
  - email
  - SMS

Step Functions
- workflow for AWS services
- code or GUI
- build and run state machines
- preferred over SWF
- ASL - amazon state language

SWF
- Simple WOrkflow Service
- support external processes
- human-enabled workflow
- asynchronous
- sequential or parallel workflows
- max completion time of 1 year
- task-oriented api
  - task is assigned once and never duplicated
  - keep track of tasks and events
- worker, decider
- components
  - domains
  - workflows
  - activities
  - task lists
  - workers
  - workflow executions

SQS
- message queue
- store and forward pattern
- distributed/decoupling applications
- buffer between producer and consumer
- auto-scalable
- up to 256KB messages
- retention - 1minute to 14 days
- retention - default 4 days
- at least 1 guarantee
- storage in single region, multi-AZ
- different queues with different priorities ?
- send/receive - IAM policies / authentication
- supports HTTPS and TLS
- PCI DSS level 1 compliant
- HIPAA eligible
- encryption queues - SSE-KMS

SQS Types
1. Standard queue
- best effort ordering
- unlimited throughput
- at least once processing
- max 120k in-flight messages/queue
2. FIFO queue
- FIFO
- up to 300 operations/s !!
- up to 10 messages per operation !!
- exactly once processing
- max 20k in-flight messages/queue

Deduplication for FIFO
- provide MessageDeduplicationId
- dedup interval = 5 minutes
- content-based dediup - deduplication id = SHA256 of message body

Ordering for FIFO
- ordered within MessageGroupId

Dead-Letter Queue
- isolate messages that failed processing
- enable redrive policy
- specify maximum receives before going to dead-letter queue
- message goes to DLQ if receiveCount > maxReceiveCount
- will break order of FIFO queues
- apply to standard of FIFO

Delay Queue
- apply delay seconds - after which message becomes visible
- 0-900 seconds (15 minutes)
- default 0s delay
- does not affect messages already in standard queue
- affects messages already in FIFO queue
- invisible before any consumer picks it up

Visibility Timeout
- invisible to consumers after it is picked up by consumer
- give time to consumer to process it
- consumer must delete the message
- if not, message is visible again
- default - 30s
- maximum - 12 hours

Short Polling
- poll subset of SQS servers
- wait Time seconds = 0
- returns immediately

Long Polling
- wait for waitTimeSeconds to get a response
- fewer api calls
- max 20s
- define waitTimeSeconds at queue or API call level

If message > 256KB
- producer uses SQS Extended Client Library for Java to send message with reference to s3 object
- consumer received message with reference to s3 object
- allow messages up to 2GB

Amazon MQ
- managed message broker
- managed Apache ActiveMQ
- path to migrate from on-prem MQ to cloud
- pay for storage and instance
- creates a cloudformation stack
